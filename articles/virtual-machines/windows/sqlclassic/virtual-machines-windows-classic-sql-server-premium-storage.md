---
title: Uso de Azure Premium Storage con SQL Server | Microsoft Docs
description: "En este artículo se usan los recursos creados con el modelo de implementación clásica y se proporcionan instrucciones sobre cómo usar Azure Premium Storage con SQL Server que se ejecuta en Azure Virtual Machines."
services: virtual-machines-windows
documentationcenter: 
author: danielsollondon
manager: jhubbard
editor: monicar
tags: azure-service-management
ms.assetid: 7ccf99d7-7cce-4e3d-bbab-21b751ab0e88
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/01/2017
ms.author: jroth
ms.openlocfilehash: f637e3c744d61f6fda755c162609d7cc9f4619c7
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/19/2018
---
# <a name="use-azure-premium-storage-with-sql-server-on-virtual-machines"></a>Usar Azure Premium Storage con SQL Server en máquinas virtuales
## <a name="overview"></a>Información general
[Azure Premium Storage](../premium-storage.md) es un almacenamiento de última generación que proporciona baja latencia y E/S de alto rendimiento. Funciona mejor para cargas de trabajo intensivas clave de E/S, como [máquinas virtuales](https://azure.microsoft.com/services/virtual-machines/)de SQL Server en IaaS.

> [!IMPORTANT]
> Azure tiene dos modelos de implementación diferentes para crear recursos y trabajar con ellos: [Resource Manager y el clásico](../../../azure-resource-manager/resource-manager-deployment-model.md). En este artículo se trata el modelo de implementación clásico. Microsoft recomienda que las implementaciones más recientes usen el modelo de Resource Manager.

Este artículo proporciona instrucciones de planificación y orientación para migrar una máquina virtual que ejecuta SQL Server de modo que use Premium Storage. Esto incluye pasos relacionados con la infraestructura de Azure (redes, almacenamiento) y la máquina virtual invitada de Windows. En el ejemplo del [Apéndice](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) se muestra una migración de extremo a extremo completa para mover máquinas virtuales mayores con el fin de aprovechar mejor el almacenamiento SSD local mejorado con PowerShell.

Es importante comprender el proceso de extremo a extremo para usar Azure Premium Storage con máquinas virtuales de SQL Server en IAAS. Esto incluye:

* Identificación de los requisitos previos para usar Premium Storage.
* Ejemplos de implementación de SQL Server en IaaS en Premium Storage para nuevas implementaciones.
* Ejemplos de migración de implementaciones existentes, tanto servidores independientes como implementaciones mediante grupos de disponibilidad AlwaysOn de SQL.
* Enfoques de migración posibles.
* Ejemplo completo que muestra los pasos de Azure, Windows y SQL Server para la migración de una implementación AlwaysOn existente.

Para obtener información general sobre SQL Server en máquinas virtuales de Azure, consulte [SQL Server en Azure Virtual Machines](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

**Autor:** Daniel Sol **Revisores técnicos:** Luis Carlos Vargas Herring, Sanjay Mishra, Pravin Mital, Juergen Thomas, Gonzalo Ruiz.

## <a name="prerequisites-for-premium-storage"></a>Requisitos previos para Premium Storage
Hay varios requisitos previos para usar Premium Storage.

### <a name="machine-size"></a>Tamaño de la máquina
Para usar Premium Storage deberá usar máquinas virtuales de la serie DS. Si nunca usó máquinas de la serie DS en el servicio en la nube, debe eliminar la máquina virtual existente, mantener los discos conectados y, a continuación, crear un nuevo servicio en la nube antes de volver a crear la máquina virtual como un tamaño de rol DS*. Para más información sobre los tamaños de las máquinas virtuales, consulte [Tamaños de máquinas virtuales y servicios en la nube de Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="cloud-services"></a>Servicios en la nube
Solo puede usar máquinas virtuales DS* con Premium Storage cuando se crean en un servicio en la nube nuevo. Si usando AlwaysOn de SQL Server en Azure, el agente de escucha de AlwaysOn hará referencia a la dirección IP del equilibrador de carga interno o externo de Azure asociada a un servicio en la nube. Este artículo se centra en cómo migrar manteniendo al mismo tiempo la disponibilidad en este escenario.

> [!NOTE]
> La primera máquina virtual que se implementa en el servicio en la nube nuevo debe ser de la serie DS*.
>
>

### <a name="regional-vnets"></a>Redes virtuales regionales
En el caso de las máquinas virtuales DS*, debe configurar la red virtual (VNET) que hospeda las máquinas virtuales para que sea regional. Esto “amplía” la red virtual de modo que las máquinas virtuales más grandes pueden aprovisionarse en otros clústeres y comunicarse entre sí. En la siguiente captura de pantalla, la ubicación resaltada muestra redes virtuales regionales, mientras que el primer resultado muestra una red virtual “estrecha”.

![RegionalVNET][1]

Puede generar una incidencia de soporte técnico de Microsoft para migrar a una red virtual regional. Microsoft realizará un cambio y, para completar la migración a redes virtuales regionales, debe cambiar la propiedad AffinityGroup en la configuración de red. Primero exporte la configuración de red en PowerShell y, a continuación, reemplace la propiedad **AffinityGroup** del elemento **VirtualNetworkSite** por una propiedad **Location**. Especifique `Location = XXXX` donde `XXXX` es una región de Azure. A continuación, importe la nueva configuración.

Por ejemplo, considere la siguiente configuración de red virtual:

    <VirtualNetworkSite name="danAzureSQLnet" AffinityGroup="AzureSQLNetwork">
    <AddressSpace>
      <AddressPrefix>10.0.0.0/8</AddressPrefix>
      <AddressPrefix>172.16.0.0/12</AddressPrefix>
    </AddressSpace>
    <Subnets>
    ...
    </VirtualNetworkSite>

Para moverlo a una red virtual regional en Europa Occidental, cambie la configuración a la siguiente:

    <VirtualNetworkSite name="danAzureSQLnet" Location="West Europe">
    <AddressSpace>
      <AddressPrefix>10.0.0.0/8</AddressPrefix>
      <AddressPrefix>172.16.0.0/12</AddressPrefix>
    </AddressSpace>
    <Subnets>
    ...
    </VirtualNetworkSite>

### <a name="storage-accounts"></a>Cuentas de almacenamiento
Necesitará crear una cuenta de almacenamiento nueva que esté configurada para Premium Storage. Tenga en cuenta que el uso de Premium Storage se establece en la cuenta de almacenamiento, no en VHD individuales. Sin embargo, cuando se usa una máquina virtual de la serie DS*, se puede adjuntar VHD desde cuentas de Standard Storage y Premium Storage. Puede considerar esta opción si no desea colocar el VHD del sistema operativo en la cuenta de Premium Storage.

El siguiente comando **New-AzureStorageAccountPowerShell** con el tipo **"Premium_LRS"** crea una cuenta Premium Storage:

    $newstorageaccountname = "danpremstor"
    New-AzureStorageAccount -StorageAccountName $newstorageaccountname -Location "West Europe" -Type "Premium_LRS"   

### <a name="vhds-cache-settings"></a>Configuración de la caché de los VHD
La diferencia principal al crear discos que forman parte de una cuenta de Premium Storage es la configuración de la caché de disco. En el caso de discos de volumen de datos de SQL Server, se recomienda usar "**Read Caching**". En el caso de volúmenes de registro de transacciones, la configuración de memoria caché de disco se debe establecer en "**None**". Esto difiere de las recomendaciones para las cuentas de Standard Storage.

Una vez conectados los VHD, no se puede modificar la configuración de caché. Deberá desconectar y volver a conectar el VHD con una configuración de caché actualizada.

### <a name="windows-storage-spaces"></a>Espacios de almacenamiento de Windows
Puede usar los [espacios de almacenamiento de Windows](https://technet.microsoft.com/library/hh831739.aspx) como hizo con la instancia de Standard Storage anterior. Esto le permitirá migrar una máquina virtual que ya usa los espacios de almacenamiento. En el ejemplo del [Apéndice](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) (paso 9 y posteriores) se muestra el código de Powershell para extraer e importar una máquina virtual con varios VHD conectados.

Se usaron grupos de almacenamiento con la cuenta de Azure Storage estándar para mejorar el rendimiento y reducir la latencia. Pueden resultarle útiles las pruebas de los grupos de almacenamiento con Premium Storage para implementaciones nuevas, pero agregan una complejidad adicional con la configuración del almacenamiento.

#### <a name="how-to-find-which-azure-virtual-disks-map-to-storage-pools"></a>Cómo encontrar qué discos virtuales de Azure se asignan a grupos de almacenamiento
Dado que existen recomendaciones de configuración de caché diferentes para los VHD conectados, tal vez decida copiar los VHD en una cuenta de Premium Storage. Sin embargo, al volver a conectarlos a la máquina virtual nueva de la serie DS, podría tener que modificar la configuración de caché. Es más sencillo aplicar la configuración de caché recomendada para Premium Storage cuando se tienen VHD independientes para los archivos de datos y los archivos de registro de SQL (en lugar de un solo VHD que contenga ambos tipos de archivos).

> [!NOTE]
> Si tiene los archivos de datos y los archivos de registro de SQL Server en el mismo volumen, la opción de caché que elija depende de los patrones de acceso de E/S para las cargas de trabajo de la base de datos. Solo las pruebas pueden demostrar qué opción de caché es más adecuada para este escenario.
>
>

Sin embargo, si usa los espacios de almacenamiento de Windows, que se componen de varios VHD, tendrá que examinar los scripts originales para identificar en qué grupo específico se encuentra cada VHD conectado, para poder establecer la configuración de la caché según corresponda para cada disco.

Si no tiene ningún script original que muestre qué VHD se asignan al grupo de almacenamiento, puede seguir los pasos indicados a continuación para determinar la asignación del disco/grupo de almacenamiento.

Para cada disco, siga estos pasos:

1. Obtenga la lista de discos conectados a la máquina virtual con el comando **Get-AzureVM** :

    Get-AzureVM -ServiceName <servicename> -Name <vmname> | Get-AzureDataDisk
2. Anote el nombre del disco y el LUN.

    ![DisknameAndLUN][2]
3. Conéctese mediante Escritorio remoto a la máquina virtual. A continuación, vaya a **Administración de equipos** | **Administrador de dispositivos** | **Unidades de disco**. Examine las propiedades de cada uno de los “Discos virtuales de Microsoft”.

    ![VirtualDiskProperties][3]
4. En este caso, el número LUN es una referencia al número LUN que se especifica al conectar el VHD a la máquina virtual.
5. Para el "Disco virtual de Microsoft", vaya a la pestaña **Detalles** y, en la lista **Propiedad**, vaya a **Clave del controlador**. En **Valor**, anote el valor de **Desplazamiento**, que es 0002 en la captura de pantalla siguiente. El 0002 denota el PhysicalDisk2 al que hace referencia el grupo de almacenamiento.

    ![VirtualDiskPropertyDetails][4]
6. Para cada grupo de almacenamiento, vuelque los discos asociados:

    Get-StoragePool -FriendlyName AMS1pooldata | Get-PhysicalDisk

    ![GetStoragePool][5]

Ahora puede usar esta información para asociar los VHD conectados a discos físicos en los grupos de almacenamiento.

Una vez asignados los VHD a discos físicos en los grupos de almacenamiento, puede desconectarlos y copiarlos en una cuenta de Premium Storage. A continuación, conéctelos con la configuración de la caché correcta. Consulte los pasos del 8 al 12 del ejemplo incluido en el [Apéndice](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage). Estos pasos muestran cómo extraer una configuración de disco VHD conectado a una máquina virtual en un archivo CSV, copiar los VHD, modificar la configuración de la caché del disco y, por último, volver a implementar la máquina virtual como una máquina virtual de la serie DS con todos los discos conectados.

### <a name="vm-storage-bandwidth-and-vhd-storage-throughput"></a>Ancho de banda del almacenamiento de la VM y rendimiento del almacenamiento del VHD
El nivel de rendimiento del almacenamiento depende del tamaño especificado de la máquina virtual DS* y de los tamaños del VHD. Las máquinas virtuales tienen diferentes asignaciones para el número de VHD que se pueden conectar y el ancho de banda máximo que admitirán (MB/s). Para obtener las cifras específicas del ancho de banda, consulte [Tamaños de máquinas virtuales y servicios en la nube de Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

El aumento de IOPS se consigue con tamaños de disco mayores. Debe tenerlo en cuenta al considerar la ruta de acceso de la migración. Para más información, [consulte la tabla de IOPS y tipos de discos](../premium-storage.md#scalability-and-performance-targets).

Por último, tenga en cuenta que las máquinas virtuales admiten diferentes anchos de banda de disco máximos para todos los discos conectados. Con una carga elevada, podría saturar el ancho de banda de disco máximo disponible para ese tamaño de rol de máquina virtual. Por ejemplo, un Standard_DS14 admitirá hasta 512 MB/s; por lo tanto, con tres discos P30 podría saturar el ancho de banda de disco de la máquina virtual. No obstante, en este ejemplo, se puede superar el límite de rendimiento en función de la combinación de E/S de lectura y escritura.

## <a name="new-deployments"></a>Nuevas implementaciones
Las dos secciones siguientes muestran cómo puede implementar máquinas virtuales de SQL Server para Premium Storage. Como se mencionó antes, no debe colocar necesariamente el disco del sistema operativo en el almacenamiento Premium. Puede hacerlo si va a colocar las cargas de trabajo intensivas de E/S en el VHD del sistema operativo.

El primer ejemplo muestra el uso de las imágenes existente en la Galería de Azure. El segundo ejemplo muestra cómo usar una imagen de máquina virtual personalizada que tiene en una cuenta de almacenamiento estándar existente.

> [!NOTE]
> En estos ejemplos se supone que ya ha creado una red virtual regional.
>
>

### <a name="create-a-new-vm-with-premium-storage-with-gallery-image"></a>Crear una nueva máquina virtual con Premium Storage con una imagen de la Galería
El ejemplo siguiente muestra cómo colocar el VHD del sistema operativo en Premium Storage y cómo adjuntar los VHD de Premium Storage. Sin embargo, también puede colocar el disco del sistema operativo en una cuenta de Standard Storage y, a continuación, conectar los VHD que residen en una cuenta de Premium Storage. Se muestran ambos escenarios.

    $mysubscription = "DansSubscription"
    $location = "West Europe"

    #Set up subscription
    Set-AzureSubscription -SubscriptionName $mysubscription
    Select-AzureSubscription -SubscriptionName $mysubscription -Current  

#### <a name="step-1-create-a-premium-storage-account"></a>Paso 1: Crear una cuenta de Premium Storage
    #Create Premium Storage account, note Type
    $newxiostorageaccountname = "danspremsams"
    New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  


#### <a name="step-2-create-a-new-cloud-service"></a>Paso 2: Crear un nuevo servicio en la nube
    $destcloudsvc = "danNewSvcAms"
    New-AzureService $destcloudsvc -Location $location


#### <a name="step-3-reserve-a-cloud-service-vip-optional"></a>Paso 3: Reservar una VIP de servicio en la nube (opcional)
    #check exisitng reserved VIP
    Get-AzureReservedIP

    $reservedVIPName = “sqlcloudVIP”
    New-AzureReservedIP –ReservedIPName $reservedVIPName –Label $reservedVIPName –Location $location

#### <a name="step-4-create-a-vm-container"></a>Paso 4: Crear un contenedor de máquinas virtuales
    #Generate storage keys for later
    $xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

    ##Generate storage acc contexts
    $xioContext = New-AzureStorageContext –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary   

    #Create container
    $containerName = 'vhds'
    New-AzureStorageContainer -Name $containerName -Context $xioContext

#### <a name="step-5-placing-os-vhd-on-standard-or-premium-storage"></a>Paso 5: Colocar el VHD del sistema operativo en Standard o Premium Storage
    #NOTE: Set up subscription and default storage account which will be used to place the OS VHD in

    #If you want to place the OS VHD Premium Storage Account
    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount  $newxiostorageaccountname  

    #If you wanted to place the OS VHD Standard Storage Account but attach Premium Storage VHDs then you would run this instead:
    $standardstorageaccountname = "danstdams"

    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount  $standardstorageaccountname

#### <a name="step-6-create-vm"></a>Paso 6: Crear una máquina virtual
    #Get list of available SQL Server Images from the Azure Image Gallery.
    $galleryImage = Get-AzureVMImage | where-object {$_.ImageName -like "*SQL*2014*Enterprise*"}
    $image = $galleryImage.ImageName

    #Set up Machine Specific Information
    $vmName = "dsDan1"
    $vnet = "dansvnetwesteur"
    $subnet = "SQL"
    $ipaddr = "192.168.0.8"

    #Remember to change to DS series VM
    $newInstanceSize = "Standard_DS1"

    #create new Avaiability Set
    $availabilitySet = "cloudmigAVAMS"

    #Machine User Credentials
    $userName = "myadmin"
    $pass = "mycomplexpwd4*"

    #Create VM Config
    $vmConfigsl = New-AzureVMConfig -Name $vmName -InstanceSize $newInstanceSize -ImageName $image  -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` -AdminUserName $userName -Password $pass | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

    #Add Data and Log Disks to VM Config
    #Note the size specified ‘-DiskSizeInGB 1023’, this will attach 2 x P30 Premium Storage Disk Type
    #Utilising the Premium Storage enabled Storage account

    $vmConfigsl | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 0 -HostCaching "ReadOnly"  -DiskLabel "DataDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-data1.vhd"
    $vmConfigsl | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 1 -HostCaching "None"  -DiskLabel "logDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-log1.vhd"

    #Create VM
    $vmConfigsl  | New-AzureVM –ServiceName $destcloudsvc -VNetName $vnet ## Optional (-ReservedIPName $reservedVIPName)  

    #Add RDP Endpoint
    $EndpointNameRDPInt = "3389"
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmName | Add-AzureEndpoint -Name "EndpointNameRDP" -Protocol "TCP" -PublicPort "53385" -LocalPort $EndpointNameRDPInt  | Update-AzureVM

    #Check VHD storage account, these should be in $newxiostorageaccountname
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmName | Get-AzureDataDisk
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmName |Get-AzureOSDisk


### <a name="create-a-new-vm-to-use-premium-storage-with-a-custom-image"></a>Crear una nueva VM para usar Premium Storage con una imagen personalizada
Este escenario muestra dónde tiene imágenes personalizadas que residen en una cuenta de Standard Storage. Como ya se mencionó, si desea colocar el VHD del sistema operativo en Premium Storage necesitará copiar la imagen que existe en la cuenta de Standard Storage y transferirlo a Premium Storage antes de poder usarlo. Si tiene una imagen local, también puede usar este método para copiarla directamente a la cuenta Premium Storage.

#### <a name="step-1-create-storage-account"></a>Paso 1: Crear una cuenta de almacenamiento
    $mysubscription = "DansSubscription"
    $location = "West Europe"

    #Create Premium Storage account
    $newxiostorageaccountname = "danspremsams"
    New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  

    #Standard Storage account
    $origstorageaccountname = "danstdams"

#### <a name="step-2-create-cloud-service"></a>Paso 2: Crear un servicio en la nube
    $destcloudsvc = "danNewSvcAms"
    New-AzureService $destcloudsvc -Location $location


#### <a name="step-3-use-existing-image"></a>Paso 3: Usar una imagen existente
Puede usar una imagen existente. También puede [captar la imagen de una máquina existente](../classic/capture-image-classic.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Tenga en cuenta que la máquina de la que se crea una imagen no tiene que ser DS \*. Ahora que ya ha creado la imagen, los pasos siguientes muestran cómo copiarla en la cuenta de Premium Storage con el commandlet de PowerShell **Start-AzureStorageBlobCopy**.

    #Get storage account keys:
    #Standard Storage account
    $originalstorage =  Get-AzureStorageKey -StorageAccountName $origstorageaccountname
    #Premium Storage account
    $xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

    #Set up contexts for the storage accounts:
    $origContext = New-AzureStorageContext  –StorageAccountName $origstorageaccountname -StorageAccountKey $originalstorage.Primary
    $destContext = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary  

#### <a name="step-4-copy-blob-between-storage-accounts"></a>Paso 4: Copiar un blob entre cuentas de almacenamiento
    #Get Image VHD
    $myImageVHD = "dansoldonorsql2k14-os-2015-04-15.vhd"
    $containerName = 'vhds'

    #Copy Blob between accounts
    $blob = Start-AzureStorageBlobCopy -SrcBlob $myImageVHD -SrcContainer $containerName `
    -DestContainer vhds -Destblob "prem-$myImageVHD" `
    -Context $origContext -DestContext $destContext  

#### <a name="step-5-regularly-check-copy-status"></a>Paso 5: Comprobar periódicamente el estado de copia
    $blob | Get-AzureStorageBlobCopyState

#### <a name="step-6-add-image-disk-to-azure-disk-repository-in-subscription"></a>Paso 6: Agregar un disco de imagen al repositorio de discos de Azure en la suscripción
    $imageMediaLocation = $destContext.BlobEndPoint+"/"+$myImageVHD
    $newimageName = "prem"+"dansoldonorsql2k14"

    Add-AzureVMImage -ImageName $newimageName -MediaLocation $imageMediaLocation

> [!NOTE]
> Es posible que, aunque los informes notifiquen un estado correcto, se produzca un error de concesión de disco. En este caso, espere unos 10 minutos.
>
>

#### <a name="step-7--build-the-vm"></a>Paso 7: Crear la máquina virtual
Aquí va a crear la máquina virtual a partir de la imagen y a adjuntar dos VHD de Premium Storage:

    $newimageName = "prem"+"dansoldonorsql2k14"
    #Set up Machine Specific Information
    $vmName = "dansolchild"
    $vnet = "westeur"
    $subnet = "Clients"
    $ipaddr = "192.168.0.41"

    #This will need to be a new cloud service
    $destcloudsvc = "danregsvcamsxio2"

    #Use to DS Series VM
    $newInstanceSize = "Standard_DS1"

    #create new Avaiability Set
    $availabilitySet = "cloudmigAVAMS3"

    #Machine User Credentials
    $userName = "myadmin"
    $pass = "theM)stC0mplexP@ssw0rd!”


    #Create VM Config
    $vmConfigsl2 = New-AzureVMConfig -Name $vmName -InstanceSize $newInstanceSize -ImageName $newimageName  -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` -AdminUserName $userName -Password $pass | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

    $vmConfigsl2 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 0 -HostCaching "ReadOnly"  -DiskLabel "DataDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-Datadisk-1.vhd"
    $vmConfigsl2 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 1 -HostCaching "None"  -DiskLabel "LogDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-logdisk-1.vhd"



    $vmConfigsl2 | New-AzureVM –ServiceName $destcloudsvc -VNetName $vnet

## <a name="existing-deployments-that-do-not-use-always-on-availability-groups"></a>Implementaciones existentes que no usan grupos de disponibilidad AlwaysOn
> [!NOTE]
> Para las implementaciones existentes, vea primero la sección [Requisitos previos](#prerequisites-for-premium-storage) de este tema.
>
>

Existen diferentes consideraciones para las implementaciones de SQL Server que no usan grupos de disponibilidad AlwaysOn y para las que los usan. Si no usa AlwaysOn y tiene un servidor SQL Server independiente, puede realizar la actualización a Premium Storage mediante un servicio en la nube y una cuenta de almacenamiento nuevos. Considere las opciones siguientes:

* **Crear una nueva máquina virtual de SQL Server**. Puede crear una nueva máquina virtual de SQL Server que use una cuenta de Premium Storage, como se documenta en Nuevas implementaciones. A continuación, haga una copia de seguridad y restaure las bases de datos de usuario y la configuración de SQL Server. La aplicación deberá actualizarse para que haga referencia al nuevo servidor SQL Server si se tiene acceso a ella interna o externamente. Deberá copiar todos los objetos “fuera de la base de datos” como si estuviera llevando a cabo una migración de SQL Server en paralelo (SxS). Esto incluye objetos tales como inicios de sesión, certificados y servidores vinculados.
* **Migrar una máquina virtual de SQL Server existente**. Esto requerirá desconectar la máquina virtual de SQL Server y, a continuación, transferirla a un nuevo servicio en la nube, lo que incluye copiar todos los VHD conectados en la cuenta de Premium Storage. Cuando la máquina virtual se conecte, la aplicación hará referencia al nombre de host del servidor, igual que antes. Tenga en cuenta que el tamaño del disco existente afectará a las características de rendimiento. Por ejemplo, un disco de 400 GB se redondea hacia arriba a P20. Si sabe que no necesita ese rendimiento de disco, podría volver a crear la máquina virtual como una máquina virtual de la serie DS y conectar los VHD de Premium Storage con la especificación de tamaño o rendimiento que necesite. A continuación, puede desconectar y volver a conectar los archivos de base de datos de SQL.

> [!NOTE]
> Al copiar los discos VHD debe tener en cuenta el tamaño, ya que en función de este se decidirá en qué tipo de disco de Premium Storage se engloban, lo que determina la especificación de rendimiento del disco. Azure redondeará hacia arriba al tamaño de disco más cercano, por lo que si tiene un disco de 400 GB, redondeará a P20. En función de los requisitos de E/S existentes del VHD del sistema operativo, podría no tener que migrarlo a una cuenta de Premium Storage.
>
>

Si se tiene acceso al servidor SQL Server externamente, la VIP del servicio en la nube cambiará. También tendrá que actualizar los puntos de conexión, las ACL y la configuración DNS.

## <a name="existing-deployments-that-use-always-on-availability-groups"></a>Implementaciones existentes que usan grupos de disponibilidad AlwaysOn
> [!NOTE]
> Para las implementaciones existentes, vea primero la sección [Requisitos previos](#prerequisites-for-premium-storage) de este tema.
>
>

En esta sección, examinaremos, en primer lugar, la forma en que AlwaysOn interactúa con las redes de Azure. A continuación, desglosaremos las migraciones en dos escenarios: migraciones donde se puede tolerar un tiempo de inactividad y migraciones donde se debe alcanzar el tiempo de inactividad mínimo.

Los grupos de disponibilidad AlwaysOn de SQL Server locales usan un agente de escucha local que registra un nombre DNS virtual junto con una dirección IP que comparten uno o varios servidores SQL Server. Cuando los clientes se conectan, se enrutan a través de la dirección IP de escucha al servidor principal de SQL Server. Este es el servidor que posee el recurso IP de AlwaysOn en ese momento.

![DeploymentsUseAlways On][6]

En Microsoft Azure, solo puede tener una dirección IP asignada a una NIC en la máquina virtual, así que para lograr el mismo nivel de abstracción que en local, Azure emplea la dirección IP que se asigna a los equilibradores de carga internos y externos (ILB/ELB). El recurso IP que se comparte entre los servidores se establece en la misma IP que el ILB/ELB. Esto se publica en el DNS y el tráfico de cliente se pasa a través del ILB/ELB a la réplica del servidor principal de SQL Server. El ILB/ELB sabe qué servidor de SQL Server es el principal, ya que usa sondeos para sondear el recurso IP de AlwaysOn. En el ejemplo anterior, sondea cada nodo que tiene un extremo al que hace referencia el ELB/ILB, y el que responde es el servidor principal de SQL Server.

> [!NOTE]
> El ILB y ELB se asignan a un servicio en la nube de Azure determinado, por lo tanto lo más probable es que cualquier migración a la nube en Azure implique un cambio en la dirección IP del equilibrador de carga.
>
>

### <a name="migrating-always-on-deployments-that-can-allow-some-downtime"></a>Migración de implementaciones de AlwaysOn que permiten cierto tiempo de inactividad
Hay dos estrategias para migrar implementaciones de AlwaysOn que permiten cierto tiempo de inactividad:

1. **Agregar más réplicas secundarias a un clúster de AlwaysOn existente**
2. **Migrar a un clúster de AlwaysOn nuevo**

#### <a name="1-add-more-secondary-replicas-to-an-existing-always-on-cluster"></a>1. Agregar más réplicas secundarias a un clúster de AlwaysOn existente
Una estrategia consiste en agregar más elementos secundarios al grupo de disponibilidad AlwaysOn. Deberá agregarlos a un servicio en la nube nuevo y actualizar el agente de escucha con la dirección IP nueva del equilibrador de carga.

##### <a name="points-of-downtime"></a>Puntos de tiempo de inactividad:
* Validación de clústeres.
* Pruebas de las conmutaciones por error de AlwaysOn en los elementos secundarios nuevos.

Si usa grupos de almacenamiento de Windows en la máquina virtual para un mayor rendimiento de E/S, se desconectarán durante una validación completa del clúster. La prueba de validación es necesaria al agregar nodos al clúster. El tiempo necesario para ejecutar la prueba puede variar, por lo que debe probarlo en un entorno de prueba representativo para obtener una aproximación del tiempo que tardará.

Debe aprovisionar tiempo donde pueda realizar una conmutación por error manual y pruebas de caos en los nodos recién agregados para asegurarse de que la alta disponibilidad de AlwaysOn funciona tal como se espera de ella.

![DeploymentUseAlways On2][7]

> [!NOTE]
> Debe detener todas las instancias del servidor SQL Server donde se usen los grupos de almacenamiento antes de que se ejecute la validación.
>
> ##### <a name="high-level-steps"></a>Pasos de alto nivel
>

1. Cree dos nuevos servidores SQL Server en el nuevo servicio en la nube con Premium Storage conectado.
2. Copie las de copias de seguridad completas y restáurelas con **NORECOVERY**.
3. Copie los objetos dependientes “fuera de la base de datos de usuario”, como los inicios de sesión, etc.
4. Cree un equilibrador de carga interno nuevo (ILB) o use un equilibrador de carga externo (ELB) y, a continuación, configure los extremos de carga equilibrada en ambos nodos nuevos.

   > [!NOTE]
   > Compruebe que todos los nodos tienen la configuración de extremo correcta antes de continuar
   >
   >
5. Detenga el acceso de usuario/aplicación al servidor SQL Server (si usa grupos de almacenamiento).
6. Detenga los servicios del motor de SQL Server en todos los nodos (si usa grupos de almacenamiento).
7. Agregue nuevos nodos al clúster y ejecute la validación completa.
8. Una vez que la validación sea correcta, inicie todos los servicios de SQL Server.
9. Haga una copia de seguridad de los registros de transacciones y restaure las bases de datos de usuario.
10. Agregue nuevos nodos al grupo de disponibilidad AlwaysOn y coloque la replicación en **Sincrónica**.
11. Agregue el recurso de dirección IP del ILB/ELB del servicio en la nube nuevo a través de PowerShell para AlwaysOn basándose en el ejemplo de sitios múltiples del [Apéndice](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage). En los clústeres de Windows, establezca **Posibles propietarios** del recurso **Dirección IP** en los nuevos nodos antiguos. Consulte la sección “Agregar recurso de dirección IP en la misma subred” del [Apéndice](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage).
12. Conmute por error a uno de los nodos nuevos.
13. Convierta los nuevos nodos en asociados de conmutación por error automática y pruebe las conmutaciones por error.
14. Quite los nodos originales del grupo de disponibilidad.

##### <a name="advantages"></a>Ventajas
* Los nuevos servidores SQL Server (SQL Server y aplicación) se pueden probar antes de agregarlos a AlwaysOn.
* Puede cambiar el tamaño de la máquina virtual y personalizar el almacenamiento en función de sus requisitos. Sin embargo, sería beneficioso mantener las rutas de acceso de archivos de SQL.
* Puede controlar cuándo se inicia la transferencia de las copias de seguridad de la base de datos a las réplicas secundarias. Esto difiere del uso del commandlet **Start-AzureStorageBlobCopy** de Azure para copiar VHD, ya que es una copia asincrónica.

##### <a name="disadvantages"></a>Desventajas
* Al usar grupos de almacenamiento de Windows, hay un tiempo de inactividad del clúster durante la validación del clúster completa para los nuevos nodos adicionales.
* Según la versión de SQL Server y del número de réplicas secundarias existentes, podría ser imposible agregar más réplicas secundarias sin quitar réplicas secundarias existentes.
* El tiempo de transferencia de datos de SQL podría ser largo cuando se configuran los elementos secundarios.
* Hay costos adicionales durante la migración cuando se tienen máquinas nuevas que se ejecutan en paralelo.

#### <a name="2-migrate-to-a-new-always-on-cluster"></a>2. Migrar a un clúster de AlwaysOn nuevo
Otra estrategia consiste en crear un clúster de AlwaysOn nuevo con nodos completamente nuevos en el servicio en la nube nuevo y, después, redirigir a los clientes para que lo usen.

##### <a name="points-of-downtime"></a>Puntos de tiempo de inactividad
Cuando se transfieren aplicaciones y usuarios al nuevo agente de escucha de AlwaysOn se produce un tiempo de inactividad. El tiempo de inactividad depende de lo siguiente:

* El tiempo necesario para restaurar las copias de seguridad del registro de transacciones finales a las bases de datos de los servidores nuevos.
* El tiempo necesario para actualizar las aplicaciones cliente para que usen el nuevo agente de escucha de AlwaysOn.

##### <a name="advantages"></a>Ventajas
* Puede probar el entorno de producción real, SQL Server y los cambios de compilación del sistema operativo.
* Tiene la opción de personalizar el almacenamiento y posiblemente reducir el tamaño de la máquina virtual. Esto podría reducir los costos.
* Puede actualizar su compilación o versión de SQL Server durante este proceso. También puede actualizar el sistema operativo.
* El clúster de AlwaysOn anterior puede actuar como destino de reversión sólido.

##### <a name="disadvantages"></a>Desventajas
* Si desea que los dos clústeres de AlwaysOn se ejecuten simultáneamente, debe cambiar el nombre DNS del agente de escucha. Esto agrega sobrecarga de administración durante la migración, ya que las cadenas de la aplicación cliente deben reflejar el nuevo nombre del agente de escucha.
* Debe implementar un mecanismo de sincronización entre los dos entornos para mantenerlos lo más cerca posible, con el fin de minimizar los requisitos de sincronización final antes de la migración.
* Existe un coste añadido durante la migración mientras se ejecuta el nuevo entorno.

### <a name="migrating-always-on-deployments-for-minimal-downtime"></a>Migración de las implementaciones de AlwaysOn para reducir el tiempo de inactividad al mínimo
Hay dos estrategias para migrar las implementaciones de AlwaysOn de tal modo que el tiempo de inactividad sea mínimo:

1. **Usar un elemento secundario existente: sitio único**
2. **Usar una réplica de un elemento secundario existente: multisitio**

#### <a name="1-utilize-an-existing-secondary-single-site"></a>1. Usar un elemento secundario existente: sitio único
Una estrategia para el tiempo de inactividad mínimo consiste en tomar un elemento secundario existente en la nube y quitarlo del servicio en la nube actual. A continuación, copie los VHD a la nueva cuenta de Premium Storage y cree la máquina virtual en el nuevo servicio en la nube. A continuación, actualice el agente de escucha en la agrupación en clústeres y la conmutación por error.

##### <a name="points-of-downtime"></a>Puntos de tiempo de inactividad
* Hay tiempo de inactividad cuando se actualiza el nodo final con el punto de conexión de carga equilibrada.
* La reconexión del cliente podría retrasarse en función de la configuración de cliente/DNS.
* Si elige desconectar el grupo de clústeres de AlwaysOn para intercambiar las direcciones IP, habrá un tiempo de inactividad adicional. Para evitarlo, use una dependencia OR y los posibles propietarios del recurso de dirección IP adicional. Consulte la sección “Agregar recurso de dirección IP en la misma subred” del [Apéndice](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage).

> [!NOTE]
> Si desea que el nodo agregado participe como asociado de conmutación por error de AlwaysOn, deberá agregar un punto de conexión de Azure con una referencia al conjunto de carga equilibrada. Al ejecutar el comando **Add-AzureEndpoint** para hacerlo, las conexiones actuales permanecen abiertas, pero las nuevas conexiones con el agente de escucha no podrán establecerse hasta que se actualice el equilibrador de carga. En las pruebas se vio que duraba 90-120 segundos. En necesario comprobarlo.
>
>

##### <a name="advantages"></a>Ventajas
* No se incurre en más gastos durante la migración.
* Migración uno a uno.
* Menor complejidad.
* Admite una mayor IOPS en los SKU de Premium Storage. Cuando los discos se desconectan de la máquina virtual y se copian en el nuevo servicio en la nube, puede usarse una herramienta de terceros para aumentar el tamaño del VHD, lo que proporciona un mayor rendimiento. Para aumentar el tamaño del VHD, consulte este [debate de foro](https://social.msdn.microsoft.com/Forums/azure/4a9bcc9e-e5bf-4125-9994-7c154c9b0d52/resizing-azure-data-disk?forum=WAVirtualMachinesforWindows).

##### <a name="disadvantages"></a>Desventajas
* Hay una pérdida temporal de alta disponibilidad y recuperación ante desastres durante la migración.
* Como se trata de una migración de 1:1, tendrá que usar un tamaño mínimo de máquina virtual que sea compatible con el número de VHD, por lo que podría no ser capaz de reducir el tamaño de las máquinas virtuales.
* Este escenario usa el commandlet **Start-AzureStorageBlobCopy** , que es asincrónico. No hay ningún contrato de nivel de servicio cuando finaliza la copia. El tiempo de realización de las copias varía; aunque depende de la espera en cola, también depende de la cantidad de datos que se van a transferir. El tiempo de copia aumenta si la transferencia va a otro centro de datos de Azure que admite Premium Storage en otra región. Si solo tiene dos nodos, considere una posible mitigación en caso de que la copia tarde más que en las pruebas. Esto puede incluir las siguientes ideas.
  * Agregue un tercer nodo temporal de SQL Server para alta disponibilidad antes de la migración con el tiempo de inactividad acordado.
  * Ejecute la migración fuera del mantenimiento programado de Azure.
  * Asegúrese de que ha configurado correctamente el cuórum de clúster.  

##### <a name="high-level-steps"></a>Pasos de alto nivel
Este documento no muestra un ejemplo completo de un extremo a otro; sin embargo, el [Apéndice](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) proporciona información detallada que puede aprovecharse para realizar esta operación.

![MinimalDowntime][8]

* Recopile la configuración del disco y quite el nodo (no elimine los VHD conectados).
* Cree una cuenta de Premium Storage y copie los VHD de la cuenta de Standard Storage.
* Cree un nuevo servicio en la nube y vuelva a implementar la máquina virtual SQL2 en ese servicio en la nube. Cree la máquina virtual mediante el VHD del sistema operativo original copiado y adjunte los VHD copiados.
* Configure ILB/ELB y agregue extremos.
* Actualice el agente de escucha de una de las siguientes maneras:
  * Desconecte el grupo de AlwaysOn y actualice el agente de escucha de AlwaysOn con la nueva dirección IP del ILB/ELB.
  * Agregue el recurso de dirección IP del ILB/ELB nuevo del servicio en la nube a través de PowerShell en los clústeres de Windows. A continuación, establezca los posibles propietarios del recurso de dirección IP en el nodo migrado, SQL2, y establézcalo como dependencia OR en el nombre de red. Consulte la sección “Agregar recurso de dirección IP en la misma subred” del [Apéndice](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage).
* Compruebe la configuración/propagación de DNS en los clientes.
* Migre la máquina virtual SQL1 y siga los pasos del 2 al 4.
* Si sigue los pasos 5ii, agregue SQL1 como posible propietario para el recurso de dirección IP agregada.
* Pruebe las conmutaciones por error.

#### <a name="2-utilize-existing-secondary-replicas-multi-site"></a>2. Usar una réplica de un elemento secundario existente: multisitio
Si tiene nodos en más de un centro de datos (DC) de Azure o si tiene un entorno híbrido, puede usar una configuración de AlwaysOn en este entorno para minimizar el tiempo de inactividad.

El enfoque consiste en cambiar la sincronización de AlwaysOn a sincrónica para el centro de datos de Azure local o secundario y, después, realizar la conmutación por error a ese servidor SQL Server. Posteriormente, copie los VHD en una cuenta de Premium Storage y vuelva a implementar la máquina en un nuevo servicio en la nube. Actualice el agente de escucha y, a continuación, conmute por recuperación.

##### <a name="points-of-downtime"></a>Puntos de tiempo de inactividad
El tiempo de inactividad es el tiempo que se tarda en conmutar por error al centro de datos alternativo y conmutar por recuperación. También depende de la configuración de cliente/DNS, y la reconexión del cliente podría retrasarse.
Considere el siguiente ejemplo de una configuración de AlwaysOn híbrida:

![MultiSite1][9]

##### <a name="advantages"></a>Ventajas
* Puede usar la infraestructura existente.
* Tiene la opción de actualizar previamente el almacenamiento de Azure en primer lugar en el centro de datos de recuperación ante desastres de Azure.
* Puede volver a configurar el almacenamiento del centro de datos de recuperación ante desastres de Azure.
* Se produce un mínimo de dos conmutaciones por error durante la migración, excluidas las conmutaciones por error de prueba.
* No es necesario mover los datos de SQL Server con copia de seguridad y restauración.

##### <a name="disadvantages"></a>Desventajas
* En función del acceso del cliente a SQL Server, podría haber una mayor latencia cuando SQL Server se ejecuta en un centro de datos alternativo de la aplicación.
* El tiempo de copia de los VHD en el almacenamiento Premium podría ser largo. Esto podría afectar a la decisión sobre si se debe conservar el nodo en el grupo de disponibilidad. Tenga en cuenta esto si se requiere el registro cuando se ejecutan cargas de trabajo intensivas durante la migración, ya que el nodo principal tendrá que mantener las transacciones sin replicar en su registro de transacciones. En consecuencia, este podría aumentar de forma significativa.
* Este escenario usa el commandlet **Start-AzureStorageBlobCopy** , que es asincrónico. No hay ningún contrato de nivel de servicio al finalizar. El tiempo de realización de las copias varía; aunque depende de la espera en cola, también depende de la cantidad de datos que se van a transferir. Por lo tanto, solo tiene un nodo en el segundo centro de datos y debe seguir los pasos de mitigación en caso de que la copia tarde más que en las pruebas. Esto puede incluir las siguientes ideas.
  * Agregue un segundo nodo temporal de SQL para alta disponibilidad antes de la migración con el tiempo de inactividad acordado.
  * Ejecute la migración fuera del mantenimiento programado de Azure.
  * Asegúrese de que ha configurado correctamente el cuórum de clúster.

Este escenario da por supuesto que se ha documentado sobre su instalación y sabe cómo se asigna el almacenamiento para realizar los cambios que permitan una configuración óptima de la caché de disco.

##### <a name="high-level-steps"></a>Pasos de alto nivel
![Multisite2][10]

* Convierta el centro de datos local/alternativo de Azure en el servidor principal de SQL Server y en el otro asociado de conmutación por error automática (AFP).
* Recopile información sobre la configuración del disco de SQL2 y quite el nodo (no elimine los VHD conectados).
* Cree una cuenta de Premium Storage y copie los VHD de la cuenta de Standard Storage.
* Cree un nuevo servicio en la nube y cree la máquina virtual SQL2 con sus discos de Premium Storage conectados.
* Configure ILB/ELB y agregue extremos.
* Actualice el agente de escucha de AlwaysOn con la nueva dirección IP del ILB/ELB y pruebe la conmutación por error.
* Compruebe la configuración de DNS.
* Cambie el AFP a SQL2 y, a continuación, migre SQL1 y siga los pasos del 2 al 5.
* Pruebe las conmutaciones por error.
* Cambie el AFP de nuevo a SQL1 y SQL2.

## <a name="appendix-migrating-a-multisite-always-on-cluster-to-premium-storage"></a>Apéndice: Migración de un clúster de AlwaysOn de sitios múltiples al Premium Storage
En el resto de este tema, encontrará un ejemplo detallado de cómo convertir un clúster de AlwaysOn de sitios múltiples al Almacenamiento premium. Asimismo, convierte el agente de escucha de modo que pase de usar un equilibrador de carga externo (ELB) a un equilibrador de carga interno (ILB).

### <a name="environment"></a>Environment
* Windows 2k12 / SQL 2k12
* 1 archivo de base de datos en SP
* 2 grupos de almacenamiento por nodo

![Appendix1][11]

### <a name="vm"></a>Máquina virtual
En este ejemplo, vamos a demostrar cómo pasar de ELB a ILB. ELB estaba disponible antes de ILB, por lo que aquí se muestra cómo cambiarlo durante la migración.

![Appendix2][12]

### <a name="pre-steps-connect-to-subscription"></a>Pasos previos: Conectarse con la suscripción
    Add-AzureAccount

    #Set up subscription
    Get-AzureSubscription

#### <a name="step-1-create-new-storage-account-and-cloud-service"></a>Paso 1: Crear una cuenta de almacenamiento y un servicio en la nube nuevos
    $mysubscription = "DansSubscription"
    $location = "West Europe"

    #Storage accounts
    #current storage account where the vm to migrate resides
    $origstorageaccountname = "danstdams"

    #Create Premium Storage account
    $newxiostorageaccountname = "danspremsams"
    New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  

    #Generate storage keys for later
    $originalstorage =  Get-AzureStorageKey -StorageAccountName $origstorageaccountname
    $xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

    #Generate storage acc contexts
    $origContext = New-AzureStorageContext  –StorageAccountName $origstorageaccountname -StorageAccountKey $originalstorage.Primary
    $xioContext = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary  

    #Set up subscription and default storage account
    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $origstorageaccountname
    Select-AzureSubscription -SubscriptionName $mysubscription -Current

    #CREATE NEW CLOUD SVC
    $vnet = "dansvnetwesteur"

    ##Existing cloud service
    $sourceSvc="dansolSrcAms"

    ##Create new cloud service
    $destcloudsvc = "danNewSvcAms"
    New-AzureService $destcloudsvc -Location $location

#### <a name="step-2-increase-the-permitted-failures-on-resources-optional"></a>Paso 2: Aumentar los errores permitidos en los recursos <Optional>
En ciertos recursos que pertenecen al grupo de disponibilidad AlwaysOn hay límites en el número de errores que pueden producirse en un período, en el que el servicio de clúster intentará reiniciar el grupo de recursos. Se recomienda aumentar este número al realizar este procedimiento, ya que, si no lo conmuta por error manualmente y desencadena conmutaciones por error al apagar los equipos, puede acercarse a este límite.

También se recomienda duplicar el número de errores permitidos. Para hacerlo en el Administrador de clústeres de conmutación por error, vaya a las propiedades del grupo de recursos de AlwaysOn:

![Appendix3][13]

Cambie el número máximo de errores a 6.

#### <a name="step-3-addition-ip-address-resource-for-cluster-group-optional"></a>Paso 3: Agregar el recurso de dirección IP para el grupo de clústeres <Optional>
Si tiene una sola dirección IP para el grupo de clústeres y está alineada con la subred en la nube, tenga en cuenta que, si desconecta accidentalmente todos los nodos del clúster en la nube de esa red, el recurso de IP del clúster y el nombre de red del clúster no podrán conectarse. En caso de que esto suceda, impedirá las actualizaciones en otros recursos de clúster.

#### <a name="step-4-dns-configuration"></a>Paso 4: Configuración de DNS
La implementación de una transición fluida depende de cómo se usa y se actualiza DNS.
Cuando se instala AlwaysOn, se crea un grupo de recursos de clúster de Windows. Si se abre el Administrador de clústeres de conmutación por error verá que, como mínimo, tendrá tres recursos. Los dos a los que hace referencia el documento son:

* Nombre de red virtual (VNN): es el nombre DNS al que se conecta el cliente cuando desea conectarse a servidores SQL Server a través de AlwaysOn.
* Recurso de dirección IP: es la dirección IP asociada con el VNN. Puede tener más de una, y en una configuración multisitio tendrá una dirección IP por subred/sitio.

Al conectarse a SQL Server, el controlador del cliente de SQL Server recuperará los registros DNS asociados al agente de escucha e intentará conectarse a cada dirección IP asociada de AlwaysOn. A continuación, se explicarán algunos de los factores que pueden influir en esto.

El número de registros DNS simultáneos asociados al nombre del agente de escucha depende no solo del número de direcciones IP asociadas, sino también de la configuración “RegisterAllIpProviders” de los clústeres de conmutación por error del recurso VNN de AlwaysOn.

Al implementar AlwaysOn en Azure, es preciso seguir varios pasos para crear el agente de escucha y las direcciones IP. En “RegisterAllIpProviders”, debe seleccionarse manualmente 1. Este procedimiento es distinto de una implementación local de AlwaysOn, donde este valor ya está establecido en 1.

Si “RegisterAllIpProviders” es 0, solo verá un registro DNS en el DNS asociado al agente de escucha:

![Appendix4][14]

Si “RegisterAllIpProviders” es 1:

![Appendix5][15]

El código siguiente volcará la configuración del VNN y la establecerá automáticamente. Tenga en cuenta que, para que el cambio surta efecto, deberá desconectar el VNN y volver a conectarlo, para lo que deberá desconectar el agente de escucha que produce interrupciones de conectividad de cliente.

    ##Always On Listener Name
    $ListenerName = "Mylistener"
    ##Get AlwaysOn Network Name Settings
    Get-ClusterResource $ListenerName| Get-ClusterParameter
    ##Set RegisterAllProvidersIP
    Get-ClusterResource $ListenerName| Set-ClusterParameter RegisterAllProvidersIP  1

En un paso posterior de la migración, será preciso que actualice el agente de escucha de AlwaysOn con una dirección IP actualizada que haga referencia a un equilibrador de carga, lo que implicará la eliminación y posterior adición de un recurso de dirección IP. Después de la actualización de la IP, deberá asegurarse de que la nueva dirección IP se actualizó en la zona DNS y que los clientes actualizan su caché de DNS local.

Si los clientes residen en un segmento de red diferente y hacen referencia a un servidor DNS diferente, necesitará tener en cuenta lo que sucede con la transferencia de zona DNS durante la migración, ya que el tiempo de reconexión de la aplicación se verá restringido como mínimo por el tiempo de transferencia de zona de las direcciones IP nuevas del agente de escucha. Si está sometido a una restricción de tiempo, debe analizar y probar la opción de forzar una transferencia de zona incremental con sus equipos de Windows, y también colocar el registro de host DNS en un menor período de vida (TTL), para que los clientes se actualicen. Para más información, consulte las [transferencias de zona incrementales](https://technet.microsoft.com/library/cc958973.aspx) y [Start-DnsServerZoneTransfer](https://technet.microsoft.com/library/jj649917.aspx).

De manera predeterminada, el TTL del registro DNS asociado al agente de escucha de AlwaysOn en Azure es de 1200 segundos. Puede que desee reducir este valor si está sometido a una restricción de tiempo durante la migración para garantizar que los clientes actualicen sus DNS con la dirección IP actualizada para el agente de escucha. Puede ver y modificar la configuración volcando la configuración del VNN:

    $AGName = "myProductionAG"
    $ListenerName = "Mylistener"
    #Look at HostRecordTTL
    Get-ClusterResource $ListenerName| Get-ClusterParameter

    #Set HostRecordTTL Examples
    Get-ClusterResource $ListenerName| Set-ClusterParameter -Name "HostRecordTTL" 120

Tenga en cuenta que cuanto menor sea “HostRecordTTL”, mayor tráfico DNS se producirá.

##### <a name="client-application-settings"></a>Configuración de la aplicación cliente
Si la aplicación cliente de SQL admite .Net 4.5 SQLClient, puede usar la palabra clave “MULTISUBNETFAILOVER=TRUE”. Se recomienda que se aplique, ya que permite una conexión más rápida con el grupo de disponibilidad AlwaysOn de SQL durante la conmutación por error. Enumera todas las direcciones IP asociadas al agente de escucha de AlwaysOn en paralelo y tiene una velocidad de reintento de conexión TCP más agresiva durante una conmutación por error.

Para obtener más información sobre la configuración anterior, consulte [Palabra clave MultiSubnetFailover y características asociadas](https://msdn.microsoft.com/library/hh213080.aspx#MultiSubnetFailover). Consulte también [Compatibilidad de SqlClient para la alta disponibilidad y la recuperación ante desastres](https://msdn.microsoft.com/library/hh205662\(v=vs.110\).aspx).

#### <a name="step-5-cluster-quorum-settings"></a>Paso 5: Configuración del cuórum de clúster
Dado que va a quitar como mínimo un servidor SQL Server de cada vez, debe modificar la configuración del cuórum de clúster. Si usa el testigo del recurso compartido de archivos (FSW) con dos nodos, debe establecer el cuórum para permitir la mayoría de nodo y usar el voto dinámico, con el fin de permitir que se conserve un único nodo.

    Set-ClusterQuorum -NodeMajority  

Para obtener más información sobre la administración y la configuración del cuórum de clúster, consulte [Configurar y administrar el cuórum en un clúster de conmutación por error de Windows Server 2012](https://technet.microsoft.com/library/jj612870.aspx).

#### <a name="step-6-extract-existing-endpoints-and-acls"></a>Paso 6: Extraer los extremos y las ACL existentes
    #GET Endpoint info
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmNameToMigrate | Get-AzureEndpoint
    #GET ACL Rules for Each EP, this example is for the Always On Endpoint
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmNameToMigrate | Get-AzureAclConfig -EndpointName "myAOEndPoint-LB"  

Guárdelos en un archivo de texto.

#### <a name="step-7-change-failover-partners-and-replication-modes"></a>Paso 7: Cambiar los asociados de conmutación por error y los modos de replicación
Si tiene más de 2 servidores SQL Server, debe cambiar la conmutación por error de otro elemento secundario en otro centro de datos o en local a “Sincrónico” y convertirlo en un asociado de conmutación por error automática (AFP), con el fin de mantener la alta disponibilidad mientras realiza los cambios. Puede hacerlo a través de TSQL o modificarlo mediante SSMS:

![Appendix6][16]

#### <a name="step-8-remove-secondary-vm-from-cloud-service"></a>Paso 8: Quitar la VM secundaria del servicio en la nube
Debe planear la migración de un nodo secundario en la nube en primer lugar. Si actualmente es el principal, debe iniciar una conmutación por error manual.

    $vmNameToMigrate="dansqlams2"

    #Check machine status
    Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate

    #Shutdown secondary VM
    Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | stop-AzureVM


    #Extract disk configuration

    ##Building Existing Data Disk Configuration
    $file = "C:\Azure Storage Testing\mydiskconfig_$vmNameToMigrate.csv"
    $datadisks = @(Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureDataDisk )
    Add-Content $file “lun, vhdname, hostcaching, disklabel, diskName”
    foreach ($disk in $datadisks)
    {
      $vhdname = $disk.MediaLink.AbsolutePath -creplace  "/vhds/"
      $disk.Lun, , $disk.HostCaching, $vhdname, $disk.DiskLabel,$disks.DiskName
    # Write-Host "copying disk $disk"
    $adddisk = "{0},{1},{2},{3},{4}" -f $disk.Lun,$vhdname, $disk.HostCaching, $disk.DiskLabel, $disk.DiskName
    $adddisk | add-content -path $file
    }

    #Get OS Disk
    $osdisks = Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureOSDisk ## | select -ExpandProperty MediaLink
    $osvhdname = $osdisks.MediaLink.AbsolutePath -creplace  "/vhds/"
    $osdisks.OS, $osdisks.HostCaching, $osvhdname, $osdisks.DiskLabel, $osdisks.DiskName
    $addosdisk = "{0},{1},{2},{3},{4}" -f $osdisks.OS,$osvhdname, $osdisks.HostCaching, $osdisks.Disklabel , $osdisks.DiskName
    $addosdisk | add-content -path $file

    #Import disk config
    $diskobjects  = Import-CSV $file

    #Check disk config, make sure below returns the disks associated with the VM
    $diskobjects

    #Identify OS Disk
    $osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
    $osdiskforbuild = $osdiskimport.diskName

    #Check machibe is off
    Get-AzureVM -ServiceName $sourceSvc -Name  $vmNameToMigrate

    #Drop machine and rebuild to new cls
    Remove-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate

#### <a name="step-9-change-disk-caching-settings-in-csv-file-and-save"></a>Paso 9: Cambiar la configuración de la caché de disco en el archivo CSV y guardarla
Para los volúmenes de datos, debe establecerse en READONLY.

Para los volúmenes TLOG, debe establecerse en NONE.

![Appendix7][17]

#### <a name="step-10-copy-vhds"></a>Paso 10: Copiar los VHD
    #Ensure you have created the container for these:
    $containerName = 'vhds'

    #Create container
    New-AzureStorageContainer -Name $containerName -Context $xioContext

    ####DISK COPYING####
    #Get disks from csv, get settings for each VHDs and copy to Premium Storage accoun
    ForEach ($disk in $diskobjects)
       {
       $lun = $disk.Lun
       $vhdname = $disk.vhdname
       $cacheoption = $disk.HostCaching
       $disklabel = $disk.DiskLabel
       $diskName = $disk.DiskName
       Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname has cache setting : $cacheoption"

       #Start async copy
       Start-AzureStorageBlobCopy -srcUri "https://$origstorageaccountname.blob.core.windows.net/vhds/$vhdname" `
    -SrcContext $origContext `
    -DestContainer $containerName `
    -DestBlob $vhdname `
    -DestContext $xioContext
       }



Puede comprobar el estado de la copia de los VHD en la cuenta de Premium Storage:

    ForEach ($disk in $diskobjects)
       {
       $lun = $disk.Lun
       $vhdname = $disk.vhdname
       $cacheoption = $disk.HostCaching
       $disklabel = $disk.DiskLabel
       $diskName = $disk.DiskName

       $copystate = Get-AzureStorageBlobCopyState -Blob $vhdname -Container $containerName -Context $xioContext
    Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname, STATUS = " $copystate.Status
       }

![Appendix8][18]

Espere hasta que todos se registren como correctos.

Para obtener información sobre blobs individuales:

    Get-AzureStorageBlobCopyState -Blob "blobname.vhd" -Container $containerName -Context $xioContext

#### <a name="step-11-register-os-disk"></a>Paso 11: Registrar el disco del sistema operativo
    #Change storage account
    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $newxiostorageaccountname
    Select-AzureSubscription -SubscriptionName $mysubscription -Current

    #Register OS disk
    $osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
    $osvhd = $osdiskimport.vhdname
    $osdiskforbuild = $osdiskimport.diskName

    #Registering OS disk, but as XIO disk
    $xioDiskName = $osdiskforbuild + "xio"
    Add-AzureDisk -DiskName $xioDiskName -MediaLocation  "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$osvhd"  -Label "BootDisk" -OS "Windows"

#### <a name="step-12-import-secondary-into-new-cloud-service"></a>Paso 12: Importar el elemento secundario en el nuevo servicio en la nube
El código siguiente también usa la opción adicional. Puede importar la máquina y usar la VIP conservable.

    #Build VM Config
    $ipaddr = "192.168.0.5"
    #Remember to change to XIO
    $newInstanceSize = "Standard_DS13"
    $subnet = "SQL"

    #Create new Avaiability Set
    $availabilitySet = "cloudmigAVAMS"

    #build machine config into object
    $vmConfig = New-AzureVMConfig -Name $vmNameToMigrate -InstanceSize $newInstanceSize -DiskName $xioDiskName -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

    #Reload disk config
    $diskobjects  = Import-CSV $file
    $datadiskimport = $diskobjects | where {$_.lun -ne "Windows"}

    ForEach ( $attachdatadisk in $datadiskimport)
       {
    $label = $attachdatadisk.disklabel
    $lunNo = $attachdatadisk.lun
    $hostcach = $attachdatadisk.hostcaching
    $datadiskforbuild = $attachdatadisk.diskName
    $vhdname = $attachdatadisk.vhdname

    ###Attaching disks to a VM during a deploy to a new cloud service and new storage account is different from just attaching VHDs to just a redeploy in a new cloud service
    $vmConfig | Add-AzureDataDisk -ImportFrom -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vhdname" -LUN $lunNo -HostCaching $hostcach -DiskLabel $label

    }

    #Create VM
    $vmConfig  | New-AzureVM –ServiceName $destcloudsvc –Location $location -VNetName $vnet ## Optional (-ReservedIPName $reservedVIPName)

#### <a name="step-13-create-ilb-on-new-cloud-svc-add-load-balanced-endpoints-and-acls"></a>Paso 13: Crear ILB en SVC en la nube nuevo y agregar extremos de carga equilibrada y ACL
    #Check for existing ILB
    GET-AzureInternalLoadBalancer -ServiceName $destcloudsvc

    $ilb="sqlIntIlbDest"
    $subnet = "SQL"
    $IP="192.168.0.25"
    Add-AzureInternalLoadBalancer -ServiceName $destcloudsvc -InternalLoadBalancerName $ilb –SubnetName $subnet –StaticVNetIPAddress $IP

    #Endpoints
    $epname="sqlIntEP"
    $prot="tcp"
    $locport=1433
    $pubport=1433
    Get-AzureVM –ServiceName $destcloudsvc –Name $vmNameToMigrate  | Add-AzureEndpoint -Name $epname -Protocol $prot -LocalPort $locport -PublicPort $pubport -ProbePort 59999 -ProbeIntervalInSeconds 5 -ProbeTimeoutInSeconds 11  -ProbeProtocol "TCP" -InternalLoadBalancerName $ilb -LBSetName $ilb -DirectServerReturn $true | Update-AzureVM

    #SET Azure ACLs or Network Security Groups & Windows FWs

    #http://msdn.microsoft.com/library/azure/dn495192.aspx

    ####WAIT FOR FULL AlwaysOn RESYNCRONISATION!!!!!!!!!#####

#### <a name="step-14-update-always-on"></a>Paso 14: Actualizar AlwaysOn
    #Code to be executed on a Cluster Node
    $ClusterNetworkNameAmsterdam = "Cluster Network 2" # the azure cluster subnet network name
    $newCloudServiceIPAmsterdam = "192.168.0.25" # IP address of your cloud service

    $AGName = "myProductionAG"
    $ListenerName = "Mylistener"


    Add-ClusterResource "IP Address $newCloudServiceIPAmsterdam" -ResourceType "IP Address" -Group $AGName -ErrorAction Stop |  Set-ClusterParameter -Multiple @{"Address"="$newCloudServiceIPAmsterdam";"ProbePort"="59999";SubnetMask="255.255.255.255";"Network"=$ClusterNetworkNameAmsterdam;"OverrideAddressMatch"=1;"EnableDhcp"=0} -ErrorAction Stop

    #set dependancy and NETBIOS, then remove old IP address

    #set NETBIOS, then remove old IP address
    Get-ClusterGroup $AGName | Get-ClusterResource -Name "IP Address $newCloudServiceIPAmsterdam" | Set-ClusterParameter -Name EnableNetBIOS -Value 0

    #set dependency to Listener (OR Dependency) and delete previous IP Address resource that references:

    #Make sure no static records in DNS

![Appendix9][19]

Ahora quite la dirección IP antigua del servicio en la nube.

![Appendix10][20]

#### <a name="step-15-dns-update-check"></a>Paso 15: Comprobar actualizaciones de DNS
Ahora debe comprobar los servidores DNS en sus redes de cliente de SQL Server y asegurarse de que los clústeres agregaron el registro de host adicional para la dirección IP agregada. Si los servidores DNS no se han actualizado, considere la posibilidad de forzar una transferencia de zona DNS y asegúrese de que los clientes de la subred pueden realizar la resolución en ambas direcciones IP de AlwaysOn, con el fin de que no tenga que esperar a la replicación automática de DNS.

#### <a name="step-16-reconfigure-always-on"></a>Paso 16: Reconfigurar AlwaysOn
En este momento, espere a que el elemento secundario al que se migró el nodo se vuelva a sincronizar totalmente con el nodo local, y cambie al nodo de replicación sincrónica y conviértalo en el AFP.  

#### <a name="step-17-migrate-second-node"></a>Paso 17: Migrar el nodo secundario
    $vmNameToMigrate="dansqlams1"

    Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate

    #Get endpoint information
    $endpoint = Get-AzureVM -ServiceName $sourceSvc  -Name $vmNameToMigrate | Get-AzureEndpoint

    #Shutdown VM
    Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | stop-AzureVM

    #Get disk config

    #Building Existing Data Disk Configuration
    $file = "C:\Azure Storage Testing\mydiskconfig_$vmNameToMigrate.csv"
    $datadisks = @(Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureDataDisk )
    Add-Content $file “lun, vhdname, hostcaching, disklabel, diskName”
    foreach ($disk in $datadisks)
    {
      $vhdname = $disk.MediaLink.AbsolutePath -creplace  "/vhds/"
      $disk.Lun, , $disk.HostCaching, $vhdname, $disk.DiskLabel,$disks.DiskName
    # Write-Host "copying disk $disk"
    $adddisk = "{0},{1},{2},{3},{4}" -f $disk.Lun,$vhdname, $disk.HostCaching, $disk.DiskLabel, $disk.DiskName
    $adddisk | add-content -path $file
    }

    #Get OS Disk
    $osdisks = Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureOSDisk ## | select -ExpandProperty MediaLink
    $osvhdname = $osdisks.MediaLink.AbsolutePath -creplace  "/vhds/"
    $osdisks.OS, $osdisks.HostCaching, $osvhdname, $osdisks.DiskLabel, $osdisks.DiskName
    $addosdisk = "{0},{1},{2},{3},{4}" -f $osdisks.OS,$osvhdname, $osdisks.HostCaching, $osdisks.Disklabel , $osdisks.DiskName
    $addosdisk | add-content -path $file

    #Import disk config
    $diskobjects  = Import-CSV $file

    #Check disk configuration
    $diskobjects

    #Identify OS Disk
    $osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
    $osdiskforbuild = $osdiskimport.diskName

    #Check machine is off
    Get-AzureVM -ServiceName $sourceSvc -Name  $vmNameToMigrate

    #Drop machine and rebuild to new cls
    Remove-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate

#### <a name="step-18-change-disk-caching-settings-in-csv-file-and-save"></a>Paso 18: Cambiar la configuración de la caché de disco en el archivo CSV y guardarla
Para los volúmenes de datos, debe establecerse en READONLY.

Para los volúmenes TLOG, debe establecerse en NONE.

![Appendix11][21]

#### <a name="step-19-create-new-independent-storage-account-for-secondary-node"></a>Paso 19: Crear una nueva cuenta de almacenamiento independiente para el nodo secundario
    $newxiostorageaccountnamenode2 = "danspremsams2"
    New-AzureStorageAccount -StorageAccountName $newxiostorageaccountnamenode2 -Location $location -Type "Premium_LRS"  

    #Reset the storage account src if node 1 in a different storage account
    $origstorageaccountname2nd = "danstdams2"

    #Generate storage keys for later
    $xiostoragenode2 = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountnamenode2

    #Generate storage acc contexts
    $xioContextnode2 = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountnamenode2 -StorageAccountKey $xiostoragenode2.Primary  

    #Set up subscription and default storage account
    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $newxiostorageaccountnamenode2
    Select-AzureSubscription -SubscriptionName $mysubscription -Current

#### <a name="step-20-copy-vhds"></a>Paso 20: Copiar los VHD
    #Ensure you have created the container for these:
    $containerName = 'vhds'

    #Create container
    New-AzureStorageContainer -Name $containerName -Context $xioContextnode2  

    ####DISK COPYING####
    ##get disks from csv, get settings for each VHDs and copy to Premium Storage accoun
    ForEach ($disk in $diskobjects)
       {
       $lun = $disk.Lun
       $vhdname = $disk.vhdname
       $cacheoption = $disk.HostCaching
       $disklabel = $disk.DiskLabel
       $diskName = $disk.DiskName
       Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname has cache setting : $cacheoption"

       #Start async copy
       Start-AzureStorageBlobCopy -srcUri "https://$origstorageaccountname2nd.blob.core.windows.net/vhds/$vhdname" `
        -SrcContext $origContext `
        -DestContainer $containerName `
        -DestBlob $vhdname `
        -DestContext $xioContextnode2
       }

    #Check for copy progress

    #check induvidual blob status
    Get-AzureStorageBlobCopyState -Blob "danRegSvcAms-dansqlams1-2014-07-03.vhd" -Container $containerName -Context $xioContext


Puede comprobar el estado de la copia del VHD para todos los VHD: ForEach ($disk in $diskobjects) { $lun = $disk.Lun $vhdname = $disk.vhdname $cacheoption = $disk.HostCaching $disklabel = $disk.DiskLabel $diskName = $disk.DiskName

       $copystate = Get-AzureStorageBlobCopyState -Blob $vhdname -Container $containerName -Context $xioContextnode2
    Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname, STATUS = " $copystate.Status
       }

![Appendix12][22]

Espere hasta que todos se registren como correctos.

Para obtener información sobre blobs individuales:

    #Check induvidual blob status
    Get-AzureStorageBlobCopyState -Blob "danRegSvcAms-dansqlams1-2014-07-03.vhd" -Container $containerName -Context $xioContextnode2

#### <a name="step-21-register-os-disk"></a>Paso 21: Registrar el disco del sistema operativo
    #change storage account to the new XIO storage account
    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $newxiostorageaccountnamenode2
    Select-AzureSubscription -SubscriptionName $mysubscription -Current

    #Register OS disk
    $osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
    $osvhd = $osdiskimport.vhdname
    $osdiskforbuild = $osdiskimport.diskName

    #Registering OS disk, but as XIO disk
    $xioDiskName = $osdiskforbuild + "xio"
    Add-AzureDisk -DiskName $xioDiskName -MediaLocation  "https://$newxiostorageaccountnamenode2.blob.core.windows.net/vhds/$osvhd"  -Label "BootDisk" -OS "Windows"

    #Build VM Config
    $ipaddr = "192.168.0.4"
    $newInstanceSize = "Standard_DS13"

    #Join to existing Avaiability Set

    #Build machine config into object
    $vmConfig = New-AzureVMConfig -Name $vmNameToMigrate -InstanceSize $newInstanceSize -DiskName $xioDiskName -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

    #Reload disk config
    $diskobjects  = Import-CSV $file
    $datadiskimport = $diskobjects | where {$_.lun -ne "Windows"}

    ForEach ( $attachdatadisk in $datadiskimport)
       {
    $label = $attachdatadisk.disklabel
    $lunNo = $attachdatadisk.lun
    $hostcach = $attachdatadisk.hostcaching
    $datadiskforbuild = $attachdatadisk.diskName
    $vhdname = $attachdatadisk.vhdname

    ###This is different to just a straight cloud service change
    #note if you do not have a disk label the command below will fail, populate as required.
    $vmConfig | Add-AzureDataDisk -ImportFrom -MediaLocation "https://$newxiostorageaccountnamenode2.blob.core.windows.net/vhds/$vhdname" -LUN $lunNo -HostCaching $hostcach -DiskLabel $label

    }

    #Create VM
    $vmConfig  | New-AzureVM –ServiceName $destcloudsvc –Location $location -VNetName $vnet -Verbose

#### <a name="step-22-add-load-balanced-endpoints-and-acls"></a>Paso 22: Agregar extremos de carga equilibrada y ACL
    #Endpoints
    $epname="sqlIntEP"
    $prot="tcp"
    $locport=1433
    $pubport=1433
    Get-AzureVM –ServiceName $destcloudsvc –Name $vmNameToMigrate  | Add-AzureEndpoint -Name $epname -Protocol $prot -LocalPort $locport -PublicPort $pubport -ProbePort 59999 -ProbeIntervalInSeconds 5 -ProbeTimeoutInSeconds 11  -ProbeProtocol "TCP" -InternalLoadBalancerName $ilb -LBSetName $ilb -DirectServerReturn $true | Update-AzureVM


    #STOP!!! CHECK in the Azure portal or Machine Endpoints through PowerShell that these Endpoints are created!

    #SET ACLs or Azure Network Security Groups & Windows FWs

    #http://msdn.microsoft.com/library/azure/dn495192.aspx

#### <a name="step-23-test-failover"></a>Paso 23: Probar la conmutación por error
Ahora debe permitir que el nodo migrado se sincronice con el nodo de AlwaysOn local, colocarlo en modo de replicación sincrónica y esperar hasta que se sincronice. A continuación, conmute por error de local al primer nodo migrado, que es el AFP. En cuanto lo consiga, cambie el último nodo migrado al AFP.

Debe probar las conmutaciones por error entre todos los nodos y ejecutar pruebas de caos para garantizar que las conmutaciones por error funcionan como se esperaba y de manera puntual.

#### <a name="step-24-put-back-cluster-quorum-settings--dns-ttl--failover-pntrs--sync-settings"></a>Paso 24: Reponer la configuración del cuórum de clúster / TTL de DNS / asociados de conmutación por error / configuración de sincronización
##### <a name="adding-ip-address-resource-on-same-subnet"></a>Agregar un recurso de dirección IP en la misma subred
Si solo tiene 2 servidores SQL Server y desea migrarlos a un nuevo servicio en la nube, pero desea mantenerlos en la misma subred, puede evitar desconectar el agente de escucha para eliminar la dirección IP original de AlwaysOn y agregar la nueva dirección IP. Si va a migrar las máquinas virtuales a otra subred no será necesario que lo haga, ya que habrá una red de clúster adicional que haga referencia a esa subred.

Una vez que haya arrancado el elemento secundario migrado y haya agregado el nuevo recurso de dirección IP para el nuevo servicio en la nube antes de conmutar por error al elemento principal existente, debe seguir estos pasos en el Administrador de clústeres de conmutación por error:

Para agregar la dirección IP, consulte el paso 14 del [Apéndice](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage).

1. Para el recurso de dirección IP actual, cambie el posible propietario a “Servidor principal existente de SQL Server”, en el ejemplo siguiente, “dansqlams4”:

    ![Appendix13][23]
2. Para el recurso de dirección IP nuevo, cambie el posible propietario a “Servidor secundario migrado de SQL Server”, en el ejemplo siguiente, “dansqlams5”:

    ![Appendix14][24]
3. Después de establecerlo puede conmutar por error, y cuando el último nodo haya migrado debe editar los posibles propietarios para que ese nodo se agregue como posible propietario:

    ![Appendix15][25]

## <a name="additional-resources"></a>Recursos adicionales
* [Azure Premium Storage](../premium-storage.md)
* [Máquinas virtuales](https://azure.microsoft.com/services/virtual-machines/)
* [SQL Server en Azure Virtual Machines](../sql/virtual-machines-windows-sql-server-iaas-overview.md)

<!-- IMAGES -->
[1]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/1_VNET_Portal.png
[2]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/2_Diskname_Lun.png
[3]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/3_Virtual_Disk_Properties.png
[4]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/4_Virtual_Disk_Properties_Details.png
[5]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/5_Get_Storage_Pool.png
[6]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/6_Deployments_Always_On.png
[7]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/7_Add_More_Secondaries.png
[8]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/8_Use_Existing_Secondary_Single_Site.png
[9]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/9_Use_Existing_Secondary_Multi_Site.png
[10]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/9_Use_Existing_Secondary_Multi_Site_b.png
[11]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_01.png
[12]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_02.png
[13]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_03.png
[14]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_04.png
[15]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_05.png
[16]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_06.png
[17]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_07.png
[18]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_08.png
[19]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_09.png
[20]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_10.png
[21]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_11.png
[22]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_12.png
[23]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_13.png
[24]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_14.png
[25]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_15.png
