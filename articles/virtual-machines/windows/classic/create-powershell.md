---
title: "Creación de una máquina virtual Windows con PowerShell | Microsoft Docs"
description: "Creación de máquinas virtuales Windows utilizando Azure PowerShell y el modelo de implementación clásico"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 42c0d4be-573c-45d1-b9b0-9327537702f7
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: cynthn
ms.openlocfilehash: af672a873b33cbd0b6151fd564e84c96f0b6e1e3
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="create-a-windows-virtual-machine-with-powershell-and-the-classic-deployment-model"></a>Creación de una máquina virtual de Windows con PowerShell y el modelo de implementación clásica
> [!div class="op_single_selector"]
> * [Azure Portal - Windows](tutorial.md)
> * [PowerShell - Windows](create-powershell.md)
> 
> 

<br>

> [!IMPORTANT] 
> Azure tiene dos modelos de implementación diferentes para crear recursos y trabajar con ellos: [Resource Manager y el clásico](../../../resource-manager-deployment-model.md). En este artículo se trata el modelo de implementación clásico. Microsoft recomienda que las implementaciones más recientes usen el modelo del Administrador de recursos. Obtenga información sobre cómo [realizar estos pasos con el modelo de Resource Manager](../../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

En estos pasos se muestra cómo personalizar un conjunto de comandos de Azure PowerShell que creen y preconfiguren una máquina virtual de Azure basada en Windows mediante el uso de un enfoque de bloque de creación. Puede utilizar este proceso para crear un conjunto de comandos para una nueva máquina virtual basada en Windows rápidamente y expandir una implementación existente, o para crear varios conjuntos de comandos que creen rápidamente un entorno de profesionales de TI, o de desarrollo o pruebas.

En estos pasos se sigue un enfoque consistente en atar cabos para crear conjuntos de comandos de Azure PowerShell. Este enfoque puede ser útil si está familiarizado con PowerShell o desea conocer los valores que debe especificar para una configuración correcta. Los usuarios avanzados de PowerShell pueden tomar los comandos y sustituir sus propios valores de las variables (las líneas que comienzan con "$").

Si aún no lo ha hecho, siga las instrucciones de [Instalación y configuración de Azure PowerShell](/powershell/azure/overview) para instalar Azure PowerShell en un equipo local. Después, abra el símbolo del sistema de Windows PowerShell.

## <a name="step-1-add-your-account"></a>Paso 1: agregar la cuenta
1. En el símbolo del sistema de Powershell, escriba **Add-AzureAccount** y haga clic en **Entrar**. 
2. Escriba la dirección de correo electrónico asociada a su suscripción de Azure y haga clic en **Continuar**. 
3. Escriba la contraseña de su cuenta. 
4. Haga clic en **Iniciar sesión**.

## <a name="step-2-set-your-subscription-and-storage-account"></a>Paso 2: establecimiento de la cuenta de suscripción y almacenamiento
Para establecer su cuenta de suscripción y almacenamiento de Azure, ejecute estos comandos en el símbolo del sistema de Windows PowerShell. Reemplace todo el contenido dentro de las comillas, incluidos los caracteres < y >, por los nombres correctos.

    $subscr="<subscription name>"
    $staccount="<storage account name>"
    Select-AzureSubscription -SubscriptionName $subscr –Current
    Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

El nombre de suscripción correcto se puede obtener de la propiedad SubscriptionName de la salida del comando **Get-AzureSubscription** . Puede obtener el nombre de cuenta de almacenamiento correcto de la propiedad Label de la salida del comando **Get-AzureStorageAccount**, una vez haya ejecutado el comando **Select-AzureSubscription**.

## <a name="step-3-determine-the-imagefamily"></a>Paso 3: Determinación de ImageFamily
A continuación, deberá determinar el valor de ImageFamily o Label para la imagen concreta que corresponda a la máquina virtual de Azure que desea crear. Puede obtener la lista de los valores de ImageFamily disponibles con este comando.

    Get-AzureVMImage | select ImageFamily -Unique

A continuación, se muestran algunos ejemplos de valores de ImageFamily para equipos basados en Windows:

* Windows Server 2012 R2 Datacenter
* Windows Server 2008 R2 SP1
* Windows Server 2016 Technical Preview 4
* SQL Server 2012 SP1 Enterprise en Windows Server 2012

Si encuentra la imagen que está buscando, abra una nueva instancia del editor de texto que prefiera o el entorno de scripting integrado de PowerShell (ISE). Copie lo siguiente en el nuevo archivo de texto o PowerShell ISE, sustituyendo el valor de ImageFamily.

    $family="<ImageFamily value>"
    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

En algunos casos, el nombre de la imagen se encuentra en la propiedad Label en lugar del valor de ImageFamily. Si no encuentra la imagen que desea para el uso de la propiedad ImageFamily, obtenga un listado de las imágenes según su propiedad Label con este comando.

    Get-AzureVMImage | select Label -Unique

Si encuentra la imagen correcta con este comando, abra una nueva instancia del editor de texto de su elección o PowerShell ISE. Copie lo siguiente en el nuevo archivo de texto o PowerShell ISE, sustituyendo el valor de Label.

    $label="<Label value>"
    $image = Get-AzureVMImage | where { $_.Label -eq $label } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

## <a name="step-4-build-your-command-set"></a>Paso 4: Creación del conjunto de comandos
Cree el resto del conjunto de comandos copiando el conjunto de bloques adecuado en el nuevo archivo de texto o ISE y, a continuación, rellene los valores de las variables y quite los caracteres < y >. Consulte los dos [ejemplos](#examples) al final de este artículo para obtener una idea del resultado final.

Inicie el conjunto de comandos eligiendo uno de estos dos bloques de comandos (obligatorio).

Opción 1: Especificación de un nombre de máquina virtual y un tamaño.

    $vmname="<machine name>"
    $vmsize="<Specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

Opción 2: Especificación de un nombre, un tamaño y un nombre del conjunto de disponibilidad.

    $vmname="<machine name>"
    $vmsize="<Specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
    $availset="<set name>"
    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image -AvailabilitySetName $availset

Para los valores de InstanceSize de las máquinas virtuales de las series D, DS o G, consulte [Máquina virtual y tamaños de servicios en la nube de Azure](https://msdn.microsoft.com/library/azure/dn197896.aspx).

> [!NOTE]
> Si tiene un contrato Enterprise con Software Assurance y desea sacar el máximo provecho de la [ventaja para uso híbrido](https://azure.microsoft.com/pricing/hybrid-use-benefit/) de Windows Server, agregue el parámetro **-LicenseType** al cmdlet **New-AzureVMConfig**, pasando el valor **Windows_Server** para el típico caso de uso.  Asegúrese de que está utilizando una imagen que ha cargado; no puede utilizar una imagen estándar de la galería con la ventaja para uso híbrido.
> 
> 

De forma opcional, en un equipo de Windows independiente, especifique la cuenta y la contraseña del administrador local.

    $cred=Get-Credential -Message "Type the name and password of the local administrator account."
    $vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.Username -Password $cred.GetNetworkCredential().Password

Elija una contraseña segura. Para comprobar su robustez, consulte [Comprobador de contraseñas: uso de contraseñas seguras](https://www.microsoft.com/security/pc-security/password-checker.aspx).

Opcionalmente, para agregar el equipo de Windows a un dominio de Active Directory existente, especifique la cuenta del administrador local y la contraseña, el dominio, y el nombre y contraseña de una cuenta de dominio.

    $cred1=Get-Credential –Message "Type the name and password of the local administrator account."
    $cred2=Get-Credential –Message "Now type the name (not including the domain) and password of an account that has permission to add the machine to the domain."
    $domaindns="<FQDN of the domain that the machine is joining>"
    $domacctdomain="<domain of the account that has permission to add the machine to the domain>"
    $vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain $domacctdomain -DomainUserName $cred2.Username -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain $domaindns

Para ver opciones adicionales de configuración previa de máquinas virtuales basadas en Linux, consulte la sintaxis del conjunto de parámetros **Windows** y **WindowsDomain** en [Add-AzureProvisioningConfig](/powershell/module/azure/add-azureprovisioningconfig).

Si lo desea, asígnele una dirección IP concreta, conocida como una DIP estática.

    $vm1 | Set-AzureStaticVNetIP -IPAddress <IP address>

Puede comprobar que una dirección IP concreta está disponible con:

    Test-AzureStaticVNetIP –VNetName <VNet name> –IPAddress <IP address>

De forma opcional, asigne la máquina virtual a una subred concreta en una red virtual de Azure.

    $vm1 | Set-AzureSubnet -SubnetNames "<name of the subnet>"

Opcionalmente, agregue un único disco de datos a la máquina virtual.

    $disksize=<size of the disk in GB>
    $disklabel="<the label on the disk>"
    $lun=<Logical Unit Number (LUN) of the disk>
    $hcaching="<Specify one: ReadOnly, ReadWrite, None>"
    $vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

Para un controlador de dominio de Active Directory, establezca $hcaching en "None".

Si lo desea, puede agregar la máquina virtual a un conjunto de equilibrio de carga existente para el tráfico externo.

    $protocol="<Specify one: tcp, udp>"
    $localport=<port number of the internal port>
    $pubport=<port number of the external port>
    $endpointname="<name of the endpoint>"
    $lbsetname="<name of the existing load-balanced set>"
    $probeprotocol="<Specify one: tcp, http>"
    $probeport=<TCP or HTTP port number of probe traffic>
    $probepath="<URL path for probe traffic>"
    $vm1 | Add-AzureEndpoint -Name $endpointname -Protocol $protocol -LocalPort $localport -PublicPort $pubport -LBSetName $lbsetname -ProbeProtocol $probeprotocol -ProbePort $probeport -ProbePath $probepath

Por último, elija uno de estos bloques de comandos necesarios para crear la máquina virtual.

Opción 1: Creación de la máquina virtual en un servicio en la nube existente.

    New-AzureVM –ServiceName "<short name of the cloud service>" -VMs $vm1

El nombre corto del servicio en la nube es el nombre que aparece en la lista de Cloud Services en Azure Portal o en la lista de grupos de recursos en Azure Portal.

Opción 2: Creación de la máquina virtual en un servicio en la nube y la red virtual.

    $svcname="<short name of the cloud service>"
    $vnetname="<name of the virtual network>"
    New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname

## <a name="step-5-run-your-command-set"></a>Paso 5: Ejecución del conjunto de comandos
Revise el conjunto de comandos de Azure PowerShell creado en el editor de texto o PowerShell ISE, que consta de varios bloques de comandos del paso 4. Asegúrese de que ha especificado todas las variables necesarias y de que tengan los valores correctos. También, asegúrese de que ha quitado todos los caracteres < y >.

Si está utilizando un editor de texto, copie el conjunto de comandos en el Portapapeles y, a continuación, haga clic con el botón derecho en el símbolo del sistema de Windows PowerShell que esté abierto. Así, se emitirá el conjunto de comandos como una serie de comandos de PowerShell y se creará la máquina virtual de Azure. Como alternativa, ejecute el conjunto de comando en PowerShell ISE.

Si va a crear esta máquina virtual de nuevo o una similar, puede:

* Guardar este conjunto de comandos como archivo de script de PowerShell (*.ps1).
* Guarde este conjunto de comandos como un Runbook de Azure Automation en la sección **Cuentas de Automation** de Azure Portal.

## <a id="examples"></a>Ejemplos
Estos son dos ejemplos del uso de los pasos anteriores para crear conjuntos de comandos de Azure PowerShell que creen máquinas virtuales de Azure basadas en Windows.

### <a name="example-1"></a>Ejemplo 1
Se necesita un conjunto de comandos de PowerShell que cree la máquina virtual inicial de un controlador de dominio de Active Directory que:

* Utilice la imagen de Windows Server 2012 R2 Datacenter.
* Tenga el nombre AZDC1.
* Sea un equipo independiente.
* Tenga un disco de datos adicional de 20 GB.
* Tenga la dirección IP estática 192.168.244.4.
* Se encuentre en la subred BackEnd de la red virtual AZDatacenter.
* Se encuentre en el servicio en la nube Azure-TailspinToys.

Este es el comando de Azure PowerShell correspondiente para crear esta máquina virtual, con líneas en blanco entre cada bloque para mejorar la legibilidad.

    $family="Windows Server 2012 R2 Datacenter"
    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
    $vmname="AZDC1"
    $vmsize="Medium"
    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

    $cred=Get-Credential -Message "Type the name and password of the local administrator account."
    $vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.Username -Password $cred.GetNetworkCredential().Password

    $vm1 | Set-AzureSubnet -SubnetNames "BackEnd"

    $vm1 | Set-AzureStaticVNetIP -IPAddress 192.168.244.4

    $disksize=20
    $disklabel="DCData"
    $lun=0
    $hcaching="None"
    $vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

    $svcname="Azure-TailspinToys"
    $vnetname="AZDatacenter"
    New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname

### <a name="example-2"></a>Ejemplo 2
Se necesita un conjunto de comandos de PowerShell que cree una máquina virtual para un servidor de línea de negocio que:

* Utilice la imagen de Windows Server 2012 R2 Datacenter.
* Tenga el nombre LOB1.
* Sea un miembro del dominio corp.contoso.com.
* Tenga un disco de datos adicional de 200 GB.
* Se encuentre en la subred FrontEnd de la red virtual AZDatacenter.
* Se encuentre en el servicio en la nube Azure-TailspinToys.

Este es el comando de Azure PowerShell correspondiente para crear esta máquina virtual.

    $family="Windows Server 2012 R2 Datacenter"
    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
    $vmname="LOB1"
    $vmsize="Large"
    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

    $cred1=Get-Credential –Message "Type the name and password of the local administrator account."
    $cred2=Get-Credential –Message "Now type the name (not including the domain) and password of an account that has permission to add the machine to the domain."
    $domaindns="corp.contoso.com"
    $domacctdomain="CORP"
    $vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain $domacctdomain -DomainUserName $cred2.Username -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain $domaindns

    $vm1 | Set-AzureSubnet -SubnetNames "FrontEnd"

    $disksize=200
    $disklabel="LOBData"
    $lun=0
    $hcaching="ReadWrite"
    $vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

    $svcname="Azure-TailspinToys"
    $vnetname="AZDatacenter"
    New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname


## <a name="next-steps"></a>Pasos siguientes
Si necesita un disco de sistema operativo con un tamaño superior a 127 GB, puede [ampliar la unidad del SO](../../virtual-machines-windows-expand-os-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

