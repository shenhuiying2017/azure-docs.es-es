---
title: "Configuración de grupos de disponibilidad AlwaysOn en máquinas virtuales de Azure con PowerShell | Microsoft Docs"
description: "En este tutorial se usan recursos que se crearon con el modelo de implementación clásica. Se usa PowerShell para crear un grupo de disponibilidad AlwaysOn en Azure."
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: a4e2f175-fe56-4218-86c7-a43fb916cc64
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/17/2017
ms.author: mikeray
ms.openlocfilehash: b99cf767fb931d3f7fe14fcbe7990126244613ed
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="configure-the-always-on-availability-group-on-an-azure-vm-with-powershell"></a>Configuración de grupos de disponibilidad AlwaysOn en máquinas virtuales de Azure con PowerShell
> [!div class="op_single_selector"]
> * [Portal de Azure clásico: interfaz de usuario](../classic/portal-sql-alwayson-availability-groups.md)
> * [Portal de Azure clásico: PowerShell](../classic/ps-sql-alwayson-availability-groups.md)
<br/>

Antes de comenzar, considere que ahora puede completar esta tarea en un modelo de Azure Resource Manager. Se recomienda el modelo de Azure Resource Manager para las implementaciones nuevas. Consulte [Grupos de disponibilidad de SQL Server AlwaysOn en máquinas virtuales de Azure](../sql/virtual-machines-windows-portal-sql-availability-group-overview.md).

> [!IMPORTANT]
> Se recomienda que las implementaciones más recientes usen el modelo Resource Manager. Azure tiene dos modelos de implementación diferentes para crear y trabajar con recursos: [el Administrador de recursos y el clásico](../../../azure-resource-manager/resource-manager-deployment-model.md). Este artículo trata del modelo de implementación clásico.

Las máquinas virtuales (VM) de Azure pueden ayudar a los administradores de bases de datos a disminuir el costo de un sistema de alta disponibilidad de SQL Server. En este tutorial se muestra cómo implementar un grupo de disponibilidad mediante SQL Server AlwaysOn de extremo a extremo dentro de un entorno de Azure. Al final del tutorial, la solución SQL Server AlwaysOn en Azure constará de los siguientes elementos:

* Una red virtual que contiene varias subredes, incluida una subred front-end y back-end.
* Un controlador de dominio con un dominio de Active Directory.
* Dos máquinas virtuales de SQL Server que están implementadas en la subred back-end y unidas al dominio de Active Directory.
* Un clúster de conmutación por error Windows de tres nodos con el modelo de cuórum Mayoría de nodo.
* Un grupo de disponibilidad con dos réplicas de confirmación sincrónica de una base de datos de disponibilidad.

Este escenario es una buena opción debido a su simplicidad en Azure, no por su rentabilidad ni otros factores. Por ejemplo, puede minimizar el número de máquinas virtuales para un grupo de disponibilidad de dos réplicas para ahorrar horas de proceso en Azure con el controlador de dominio como el testigo de recurso compartido de archivos de cuórum en un clúster de conmutación por error de dos nodos. Este método reduce el recuento de máquinas virtuales en uno respecto a la configuración anterior.

Este tutorial está concebido para mostrarle los pasos necesarios para configurar la solución descrita anteriormente sin profundizar en los detalles de cada paso. Por tanto, en lugar de proporcionar los pasos de configuración de la interfaz gráfica de usuario (GUI), usa el script de PowerShell para llevarle rápidamente a través de cada paso. En este tutorial se da por hecho lo siguiente:

* Ya tiene una cuenta de Azure con la suscripción de la máquina virtual.
* Tiene instalados los [cmdlets de Azure PowerShell](/powershell/azure/overview).
* Ya tiene un conocimiento sólido de grupos de disponibilidad AlwaysOn para soluciones locales. Para obtener más información, consulte [Grupos de disponibilidad AlwaysOn (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx).

## <a name="connect-to-your-azure-subscription-and-create-the-virtual-network"></a>Conexión a la suscripción de Azure y creación de la red virtual
1. En una ventana de PowerShell del equipo local, importe el módulo de Azure, descargue un archivo de configuración de publicación en la máquina y conecte la sesión de PowerShell a su suscripción de Azure importando la configuración de publicación descargada.

        Import-Module "C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\Azure\Azure.psd1"
        Get-AzurePublishSettingsFile
        Import-AzurePublishSettingsFile <publishsettingsfilepath>

    El comando **Get-AzurePublishSettingsFile** genera automáticamente un certificado de administración con las descargas de Azure en la máquina. Se abre automáticamente un explorador y se le solicitará que escriba las credenciales de la cuenta de Microsoft para su suscripción de Azure. El archivo descargado **.publishsettings** contiene toda la información que necesita para administrar la suscripción de Azure. Después de guardar este archivo en un directorio local, impórtelo mediante el comando **Import-AzurePublishSettingsFile**.

   > [!NOTE]
   > El archivo .publishsettings contiene sus credenciales (sin codificar) que se usan para administrar sus suscripciones y servicios de Azure. El procedimiento recomendado para este archivo consiste en almacenarlo temporalmente fuera de los directorios de origen (por ejemplo en la carpeta Bibliotecas\Documentos) y, a continuación, eliminarlo una vez que la importación haya finalizado. Un usuario malintencionado que obtuviera acceso al archivo .publishsettings podría modificar, crear y eliminar sus servicios de Azure.

2. Defina una serie de variables que usará para crear la infraestructura de TI en la nube.

        $location = "West US"
        $affinityGroupName = "ContosoAG"
        $affinityGroupDescription = "Contoso SQL HADR Affinity Group"
        $affinityGroupLabel = "IaaS BI Affinity Group"
        $networkConfigPath = "C:\scripts\Network.netcfg"
        $virtualNetworkName = "ContosoNET"
        $storageAccountName = "<uniquestorageaccountname>"
        $storageAccountLabel = "Contoso SQL HADR Storage Account"
        $storageAccountContainer = "https://" + $storageAccountName + ".blob.core.windows.net/vhds/"
        $winImageName = (Get-AzureVMImage | where {$_.Label -like "Windows Server 2008 R2 SP1*"} | sort PublishedDate -Descending)[0].ImageName
        $sqlImageName = (Get-AzureVMImage | where {$_.Label -like "SQL Server 2012 SP1 Enterprise*"} | sort PublishedDate -Descending)[0].ImageName
        $dcServerName = "ContosoDC"
        $dcServiceName = "<uniqueservicename>"
        $availabilitySetName = "SQLHADR"
        $vmAdminUser = "AzureAdmin"
        $vmAdminPassword = "Contoso!000"
        $workingDir = "c:\scripts\"

    Preste atención a lo siguiente para asegurarse de que los comandos se ejecutarán correctamente después:

   * Las variables **$storageAccountName** y **$dcServiceName** tienen que ser únicas porque se usan para identificar la cuenta de almacenamiento en la nube y el servidor en la nube, respectivamente, en Internet.
   * Los nombres que especifica para las variables **$affinityGroupName** y **$virtualNetworkName** se configuran en el documento de configuración de red virtual que usará más adelante.
   * **$sqlImageName** especifica el nuevo nombre de la imagen de máquina virtual que contiene SQL Server 2012 Service Pack 1 Enterprise Edition.
   * Para mayor simplicidad, **Contoso!000** es la misma contraseña que se usa en todo el tutorial.

3. Cree un grupo de afinidad.

        New-AzureAffinityGroup `
            -Name $affinityGroupName `
            -Location $location `
            -Description $affinityGroupDescription `
            -Label $affinityGroupLabel

4. Cree una red virtual importando un archivo de configuración.

        Set-AzureVNetConfig `
            -ConfigurationPath $networkConfigPath

    El archivo de configuración contiene el siguiente documento XML. En resumen, especifica una red virtual denominada **ContosoNET** en el grupo de afinidad denominado **ContosoAG**. Tiene el espacio de direcciones **10.10.0.0/16** y dos subredes, **10.10.1.0/24** y **10.10.2.0/24**, que son la subred front-end y back-end, respectivamente. La subred front-end es donde puede colocar las aplicaciones cliente como Microsoft SharePoint. La subred back-end es donde colocará las VM con SQL Server. Si cambia las variables **$affinityGroupName** y **$virtualNetworkName** anteriores, también tiene que cambiar los nombres correspondientes a continuación.

        <NetworkConfiguration xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
          <VirtualNetworkConfiguration>
            <Dns />
            <VirtualNetworkSites>
              <VirtualNetworkSite name="ContosoNET" AffinityGroup="ContosoAG">
                <AddressSpace>
                  <AddressPrefix>10.10.0.0/16</AddressPrefix>
                </AddressSpace>
                <Subnets>
                  <Subnet name="Front">
                    <AddressPrefix>10.10.1.0/24</AddressPrefix>
                  </Subnet>
                  <Subnet name="Back">
                    <AddressPrefix>10.10.2.0/24</AddressPrefix>
                  </Subnet>
                </Subnets>
              </VirtualNetworkSite>
            </VirtualNetworkSites>
          </VirtualNetworkConfiguration>
        </NetworkConfiguration>

5. Cree una cuenta de almacenamiento asociada con el grupo de afinidad que creó y establézcala como la cuenta de almacenamiento actual en su suscripción.

        New-AzureStorageAccount `
            -StorageAccountName $storageAccountName `
            -Label $storageAccountLabel `
            -AffinityGroup $affinityGroupName
        Set-AzureSubscription `
            -SubscriptionName (Get-AzureSubscription).SubscriptionName `
            -CurrentStorageAccount $storageAccountName

6. Cree el servidor de controlador de dominio en el nuevo servicio en la nube y conjunto de disponibilidad.

        New-AzureVMConfig `
            -Name $dcServerName `
            -InstanceSize Medium `
            -ImageName $winImageName `
            -MediaLocation "$storageAccountContainer$dcServerName.vhd" `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -Windows `
                -DisableAutomaticUpdates `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword |
                New-AzureVM `
                    -ServiceName $dcServiceName `
                    –AffinityGroup $affinityGroupName `
                    -VNetName $virtualNetworkName

    Estos comandos canalizados hacen lo siguiente:

   * **New-AzureVMConfig** crea una configuración de máquina virtual.
   * **Add-AzureProvisioningConfig** proporciona los parámetros de configuración de un servidor de Windows independiente.
   * **Add-AzureDataDisk** agrega el disco de datos que usará para almacenar datos de Active Directory, con la opción establecida en None.
   * **New-AzureVM** crea un nuevo servicio en la nube y una nueva máquina virtual de Azure en el nuevo servicio en la nube.

7. Espere a que la nueva máquina virtual esté completamente aprovisionada y descargue el archivo de escritorio remoto en el directorio de trabajo. Como la nueva máquina virtual de Azure tarda mucho tiempo en aprovisionarse, el bucle `while` sigue sondeando la nueva máquina virtual hasta que esté lista para su uso.

        $VMStatus = Get-AzureVM -ServiceName $dcServiceName -Name $dcServerName

        While ($VMStatus.InstanceStatus -ne "ReadyRole")
        {
            write-host "Waiting for " $VMStatus.Name "... Current Status = " $VMStatus.InstanceStatus
            Start-Sleep -Seconds 15
            $VMStatus = Get-AzureVM -ServiceName $dcServiceName -Name $dcServerName
        }

        Get-AzureRemoteDesktopFile `
            -ServiceName $dcServiceName `
            -Name $dcServerName `
            -LocalPath "$workingDir$dcServerName.rdp"

El servidor de controlador de dominio ya está aprovisionado correctamente. A continuación, configurará el dominio de Active Directory en este servidor de controlador de dominio. Deje abierta la ventana de PowerShell en el equipo local. Se usará más adelante para crear las dos VM con SQL Server.

## <a name="configure-the-domain-controller"></a>Configuración del controlador de dominio
1. Conéctese al servidor de controlador de dominio iniciando el archivo de escritorio remoto. Use el nombre de usuario AzureAdmin y la contraseña **Contoso!000** del administrador del equipo, que especificó al crear la nueva máquina virtual.
2. Abra una ventana de Azure PowerShell en modo de administrador.
3. Ejecute el siguiente comando **DCPROMO. EXE** para configurar el dominio **corp.contoso.com** con los directorios de datos en la unidad M.

        dcpromo.exe `
            /unattend `
            /ReplicaOrNewDomain:Domain `
            /NewDomain:Forest `
            /NewDomainDNSName:corp.contoso.com `
            /ForestLevel:4 `
            /DomainNetbiosName:CORP `
            /DomainLevel:4 `
            /InstallDNS:Yes `
            /ConfirmGc:Yes `
            /CreateDNSDelegation:No `
            /DatabasePath:"C:\Windows\NTDS" `
            /LogPath:"C:\Windows\NTDS" `
            /SYSVOLPath:"C:\Windows\SYSVOL" `
            /SafeModeAdminPassword:"Contoso!000"

    Una vez que finalice el comando, la máquina virtual se reinicia automáticamente.

4. Conéctese de nuevo al servidor de controlador de dominio iniciando el archivo de escritorio remoto. Esta vez, inicie sesión como **CORP\Administrador**.
5. Abra una ventana de PowerShell en modo de administrador e importe el módulo Active Directory PowerShell mediante el comando siguiente:

        Import-Module ActiveDirectory

6. Ejecute los comandos siguientes para agregar tres usuarios al dominio.

        $pwd = ConvertTo-SecureString "Contoso!000" -AsPlainText -Force
        New-ADUser `
            -Name 'Install' `
            -AccountPassword  $pwd `
            -PasswordNeverExpires $true `
            -ChangePasswordAtLogon $false `
            -Enabled $true
        New-ADUser `
            -Name 'SQLSvc1' `
            -AccountPassword  $pwd `
            -PasswordNeverExpires $true `
            -ChangePasswordAtLogon $false `
            -Enabled $true
        New-ADUser `
            -Name 'SQLSvc2' `
            -AccountPassword  $pwd `
            -PasswordNeverExpires $true `
            -ChangePasswordAtLogon $false `
            -Enabled $true

    **CORP\Install** se usa para configurar todo lo relacionado con las instancias de servicio de SQL Server, el clúster de conmutación por error y el grupo de disponibilidad. **CORP\SQLSvc1** y **CORP\SQLSvc2** se usan como cuentas de servicio de SQL Server para las dos máquinas virtuales de SQL Server.
7. A continuación, ejecute los comandos siguientes para proporcionar **CORP\Install** los permisos para crear objetos de equipo en el dominio.

        Cd ad:
        $sid = new-object System.Security.Principal.SecurityIdentifier (Get-ADUser "Install").SID
        $guid = new-object Guid bf967a86-0de6-11d0-a285-00aa003049e2
        $ace1 = new-object System.DirectoryServices.ActiveDirectoryAccessRule $sid,"CreateChild","Allow",$guid,"All"
        $corp = Get-ADObject -Identity "DC=corp,DC=contoso,DC=com"
        $acl = Get-Acl $corp
        $acl.AddAccessRule($ace1)
        Set-Acl -Path "DC=corp,DC=contoso,DC=com" -AclObject $acl

    El GUID especificado anteriormente es el GUID para el tipo de objeto del equipo. La cuenta **CORP\Install** necesita los permisos **Leer todas las propiedades** y **Crear objetos de equipo** para crear los objetos de Active Directory para el clúster de conmutación por error. El permiso **Leer todas las propiedades** ya se concede a CORP\Install de forma predeterminada, por lo que no es necesario concederlo explícitamente. Para más información sobre los permisos necesarios para crear el clúster de conmutación por error, consulte [Guía paso a paso de clústeres de conmutación por error: configurar cuentas en Active Directory](https://technet.microsoft.com/library/cc731002%28v=WS.10%29.aspx).

    Ahora que terminó de configurar Active Directory y los objetos de usuario, creará dos máquinas virtuales de SQL Server y las unirá a este dominio.

## <a name="create-the-sql-server-vms"></a>Creación de las máquinas virtuales de SQL Server
1. Siga usando la ventana de PowerShell que está abierta en el equipo local. Defina las variables adicionales siguientes:

        $domainName= "corp"
        $FQDN = "corp.contoso.com"
        $subnetName = "Back"
        $sqlServiceName = "<uniqueservicename>"
        $quorumServerName = "ContosoQuorum"
        $sql1ServerName = "ContosoSQL1"
        $sql2ServerName = "ContosoSQL2"
        $availabilitySetName = "SQLHADR"
        $dataDiskSize = 100
        $dnsSettings = New-AzureDns -Name "ContosoBackDNS" -IPAddress "10.10.0.4"

    La dirección IP **10.10.0.4** normalmente se asigna a la primera máquina virtual que se crea en la subred **10.10.0.0/16** de la red virtual de Azure. Debe comprobar que esta es la dirección del servidor controlador de dominio mediante la ejecución de **IPCONFIG**.
2. Ejecute los siguientes comandos canalizados para crear la primera máquina virtual del clúster de conmutación por error, denominado **ContosoQuorum**:

        New-AzureVMConfig `
            -Name $quorumServerName `
            -InstanceSize Medium `
            -ImageName $winImageName `
            -MediaLocation "$storageAccountContainer$quorumServerName.vhd" `
            -AvailabilitySetName $availabilitySetName `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -WindowsDomain `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword `
                -DisableAutomaticUpdates `
                -Domain $domainName `
                -JoinDomain $FQDN `
                -DomainUserName $vmAdminUser `
                -DomainPassword $vmAdminPassword |
                Set-AzureSubnet `
                    -SubnetNames $subnetName |
                    New-AzureVM `
                        -ServiceName $sqlServiceName `
                        –AffinityGroup $affinityGroupName `
                        -VNetName $virtualNetworkName `
                        -DnsSettings $dnsSettings

    Tenga en cuenta lo siguiente con respecto al comando anterior:

   * **New-AzureVMConfig** crea una configuración de máquina virtual con el nombre del conjunto de disponibilidad deseado. Las máquinas virtuales subsiguientes se crean con el mismo nombre de conjunto de disponibilidad para que se unan al mismo conjunto de disponibilidad.
   * **Add-AzureProvisioningConfig** une la máquina virtual al dominio de Active Directory local que creó.
   * **Set-AzureSubnet** coloca la máquina virtual en la subred back-end.
   * **New-AzureVM** crea un nuevo servicio en la nube y una nueva máquina virtual de Azure en el nuevo servicio en la nube. El parámetro **DnsSettings** especifica que el servidor DNS para los servidores en el nuevo servicio en la nube tiene la dirección IP **10.10.0.4**. Esta es la dirección IP del servidor de controlador de dominio. Este parámetro es necesario para que las nuevas máquinas virtuales en el servicio en la nube se unan al dominio de Active Directory correctamente. Sin este parámetro, debe establecer manualmente los valores de IPv4 en la máquina virtual para que use el servidor de controlador de dominio como servidor DNS principal después de aprovisionar la máquina virtual y, a continuación, unir la máquina virtual al dominio de Active Directory.
3. Ejecute los siguientes comandos canalizados para crear las máquinas virtuales de SQL Server, denominadas **ContosoSQL1** y **ContosoSQL2**.

        # Create ContosoSQL1...
        New-AzureVMConfig `
            -Name $sql1ServerName `
            -InstanceSize Large `
            -ImageName $sqlImageName `
            -MediaLocation "$storageAccountContainer$sql1ServerName.vhd" `
            -AvailabilitySetName $availabilitySetName `
            -HostCaching "ReadOnly" `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -WindowsDomain `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword `
                -DisableAutomaticUpdates `
                -Domain $domainName `
                -JoinDomain $FQDN `
                -DomainUserName $vmAdminUser `
                -DomainPassword $vmAdminPassword |
                Set-AzureSubnet `
                    -SubnetNames $subnetName |
                    Add-AzureEndpoint `
                        -Name "SQL" `
                        -Protocol "tcp" `
                        -PublicPort 1 `
                        -LocalPort 1433 |
                        New-AzureVM `
                            -ServiceName $sqlServiceName

        # Create ContosoSQL2...
        New-AzureVMConfig `
            -Name $sql2ServerName `
            -InstanceSize Large `
            -ImageName $sqlImageName `
            -MediaLocation "$storageAccountContainer$sql2ServerName.vhd" `
            -AvailabilitySetName $availabilitySetName `
            -HostCaching "ReadOnly" `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -WindowsDomain `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword `
                -DisableAutomaticUpdates `
                -Domain $domainName `
                -JoinDomain $FQDN `
                -DomainUserName $vmAdminUser `
                -DomainPassword $vmAdminPassword |
                Set-AzureSubnet `
                    -SubnetNames $subnetName |
                    Add-AzureEndpoint `
                        -Name "SQL" `
                        -Protocol "tcp" `
                        -PublicPort 2 `
                        -LocalPort 1433 |
                        New-AzureVM `
                            -ServiceName $sqlServiceName

    Tenga en cuenta lo siguiente con respecto a los comandos anteriores:

   * **New-AzureVMConfig** usa el mismo nombre de conjunto de disponibilidad que el servidor de controlador de dominio y usa la imagen de SQL Server 2012 Service Pack 1 Enterprise Edition en la galería de máquina virtual. También establece el disco del sistema operativo en solo Caching de lectura (sin Caching de escritura). Se recomienda migrar los archivos de base de datos a un disco de datos independiente que adjunte a la máquina virtual y configure sin caché de lectura o de escritura. Sin embargo, lo mejor es quitar el servicio de caché de escritura en el disco del sistema operativo, ya que no se puede quitar la caché de lectura en el disco del sistema operativo.
   * **Add-AzureProvisioningConfig** une la máquina virtual al dominio de Active Directory local que creó.
   * **Set-AzureSubnet** coloca la máquina virtual en la subred back-end.
   * **Add-AzureEndpoint** agrega extremos de acceso para que las aplicaciones cliente puedan tener acceso a estas instancias de servicios de SQL Server en Internet. Se proporcionan puertos diferentes para ContosoSQL1 y ContosoSQL2.
   * **New-AzureVM** crea la nueva máquina virtual de SQL Server en el mismo servicio en la nube que ContosoQuorum. Si desea que las máquinas virtuales estén en el mismo conjunto de disponibilidad, tiene que colocarlas en el mismo servicio en la nube.
4. Espere a que cada máquina virtual esté completamente aprovisionada y, para cada máquina virtual, descargue su archivo de escritorio remoto en el directorio de trabajo. El bucle `for` recorre las tres nuevas máquinas virtuales y ejecuta los comandos dentro de las llaves de nivel superior para cada uno de ellos.

        Foreach ($VM in $VMs = Get-AzureVM -ServiceName $sqlServiceName)
        {
            write-host "Waiting for " $VM.Name "..."

            # Loop until the VM status is "ReadyRole"
            While ($VM.InstanceStatus -ne "ReadyRole")
            {
                write-host "  Current Status = " $VM.InstanceStatus
                Start-Sleep -Seconds 15
                $VM = Get-AzureVM -ServiceName $VM.ServiceName -Name $VM.InstanceName
            }

            write-host "  Current Status = " $VM.InstanceStatus

            # Download remote desktop file
            Get-AzureRemoteDesktopFile -ServiceName $VM.ServiceName -Name $VM.InstanceName -LocalPath "$workingDir$($VM.InstanceName).rdp"
        }

    Las máquinas virtuales de SQL Server están aprovisionadas y en ejecución, pero se instalan con SQL Server con las opciones predeterminadas.

## <a name="initialize-the-failover-cluster-vms"></a>Inicialización de las máquinas virtuales de clúster de conmutación por error
En esta sección, deberá modificar los tres servidores que usará en el clúster de conmutación por error y la instalación de SQL Server. Concretamente:

* Todos los servidores: tiene que instalar la característica **Clústeres de conmutación por error**.
* Todos los servidores: tiene que agregar **CORP\Install** como **administrador** de la máquina.
* ContosoSQL1 y ContosoSQL2 solamente: tendrá que agregar **CORP\Install** como un rol de **sysadmin** en la base de datos predeterminada.
* ContosoSQL1 y ContosoSQL2 solamente: tendrá que agregar **NT AUTHORITY\System** como inicio de sesión con los permisos siguientes:

  * Modificar cualquier grupo de disponibilidad
  * Conectar SQL
  * Ver estado del servidor
* ContosoSQL1 y ContosoSQL2 solamente: el protocolo **TCP** ya está habilitado en la VM con SQL Server. Sin embargo, tendrá que abrir el firewall para el acceso remoto de SQL Server.

De este modo, estará listo para comenzar. A partir de **ContosoQuorum**, siga los pasos que se indican a continuación:

1. Conéctese a **ContosoQuorum** iniciando los archivos de escritorio remoto. Use el nombre de usuario **AzureAdmin** y la contraseña **Contoso!000** del administrador de la máquina, que especificó al crear las máquinas virtuales.
2. Compruebe que los equipos se han unido correctamente a **corp.contoso.com**.
3. Espere a que la instalación de SQL Server termine de ejecutar las tareas automatizadas de inicialización antes de continuar .
4. Abra una ventana de Azure PowerShell en modo de administrador.
5. Instale la característica de Windows Clúster de conmutación por error.

        Import-Module ServerManager
        Add-WindowsFeature Failover-Clustering
6. Agregue **CORP\Install** como administrador local.

        net localgroup administrators "CORP\Install" /Add
7. Cierre sesión en ContosoQuorum. Ya está listo con este servidor.

        logoff.exe

A continuación, inicialice **ContosoSQL1** y **ContosoSQL2**. Siga los pasos a continuación, que son idénticos para ambas máquinas virtuales con SQL Server.

1. Conéctese a las dos máquinas virtuales de SQL Server a través del inicio de los archivos de escritorio remoto. Use el nombre de usuario **AzureAdmin** y la contraseña **Contoso!000** del administrador de la máquina, que especificó al crear las máquinas virtuales.
2. Compruebe que los equipos se han unido correctamente a **corp.contoso.com**.
3. Espere a que la instalación de SQL Server termine de ejecutar las tareas automatizadas de inicialización antes de continuar .
4. Abra una ventana de Azure PowerShell en modo de administrador.
5. Instale la característica de Windows Clúster de conmutación por error.

        Import-Module ServerManager
        Add-WindowsFeature Failover-Clustering
6. Agregue **CORP\Install** como administrador local.

        net localgroup administrators "CORP\Install" /Add
7. Importe el proveedor de PowerShell SQL Server.

        Set-ExecutionPolicy -Execution RemoteSigned -Force
        Import-Module -Name "sqlps" -DisableNameChecking
8. Agregue **CORP\Install** como el rol sysadmin para la instancia predeterminada de SQL Server.

        net localgroup administrators "CORP\Install" /Add
        Invoke-SqlCmd -Query "EXEC sp_addsrvrolemember 'CORP\Install', 'sysadmin'" -ServerInstance "."
9. Agregue **NT AUTHORITY\System** como inicio de sesión con los tres permisos anteriormente descritos.

        Invoke-SqlCmd -Query "CREATE LOGIN [NT AUTHORITY\SYSTEM] FROM WINDOWS" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT ALTER ANY AVAILABILITY GROUP TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT CONNECT SQL TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT VIEW SERVER STATE TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
10. Abra el firewall para el acceso remoto de SQL Server.

         netsh advfirewall firewall add rule name='SQL Server (TCP-In)' program='C:\Program Files\Microsoft SQL Server\MSSQL11.MSSQLSERVER\MSSQL\Binn\sqlservr.exe' dir=in action=allow protocol=TCP
11. Cierre sesión en ambas máquinas virtuales.

         logoff.exe

Finalmente, está listo para configurar el grupo de disponibilidad. Se usará el proveedor de SQL Server PowerShell para realizar todo el trabajo en **ContosoSQL1**.

## <a name="configure-the-availability-group"></a>Configuración del grupo de disponibilidad
1. Conéctese de nuevo a **ContosoSQL1** iniciando los archivos de escritorio remoto. En lugar de iniciar sesión con la cuenta de la máquina, inicie sesión con **CORP\Install**.
2. Abra una ventana de Azure PowerShell en modo de administrador.
3. Defina las siguientes variables:

        $server1 = "ContosoSQL1"
        $server2 = "ContosoSQL2"
        $serverQuorum = "ContosoQuorum"
        $acct1 = "CORP\SQLSvc1"
        $acct2 = "CORP\SQLSvc2"
        $password = "Contoso!000"
        $clusterName = "Cluster1"
        $timeout = New-Object System.TimeSpan -ArgumentList 0, 0, 30
        $db = "MyDB1"
        $backupShare = "\\$server1\backup"
        $quorumShare = "\\$server1\quorum"
        $ag = "AG1"
4. Importe el proveedor de PowerShell SQL Server.

        Set-ExecutionPolicy RemoteSigned -Force
        Import-Module "sqlps" -DisableNameChecking
5. Cambie la cuenta de servicio de SQL Server para ContosoSQL1 a CORP\SQLSvc1.

        $wmi1 = new-object ("Microsoft.SqlServer.Management.Smo.Wmi.ManagedComputer") $server1
        $wmi1.services | where {$_.Type -eq 'SqlServer'} | foreach{$_.SetServiceAccount($acct1,$password)}
        $svc1 = Get-Service -ComputerName $server1 -Name 'MSSQLSERVER'
        $svc1.Stop()
        $svc1.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc1.Start();
        $svc1.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)
6. Cambie la cuenta de servicio de SQL Server para ContosoSQL2 a CORP\SQLSvc2.

        $wmi2 = new-object ("Microsoft.SqlServer.Management.Smo.Wmi.ManagedComputer") $server2
        $wmi2.services | where {$_.Type -eq 'SqlServer'} | foreach{$_.SetServiceAccount($acct2,$password)}
        $svc2 = Get-Service -ComputerName $server2 -Name 'MSSQLSERVER'
        $svc2.Stop()
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc2.Start();
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)
7. Descargue **CreateAzureFailoverCluster.ps1** de [Create Failover Cluster for AlwaysOn Availability Groups in Windows Azure VM](http://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a) (Creación del clúster de conmutación por error para grupos de disponibilidad AlwaysOn en la máquina virtual de Azure) en el directorio de trabajo local. Usará este script para ayudarle a crear un clúster funcional de conmutación por error. Para información sobre cómo Clústeres de conmutación por error de Windows interactúa con la red de Azure, consulte [Alta disponibilidad y recuperación ante desastres para SQL Server en Azure Virtual Machines](../sql/virtual-machines-windows-sql-high-availability-dr.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json).
8. Cambie al directorio de trabajo y cree el clúster de conmutación por error con el script descargado.

        Set-ExecutionPolicy Unrestricted -Force
        .\CreateAzureFailoverCluster.ps1 -ClusterName "$clusterName" -ClusterNode "$server1","$server2","$serverQuorum"
9. Habilite los grupos de disponibilidad AlwaysOn para las instancias predeterminadas de SQL Server en **ContosoSQL1** y **ContosoSQL2**.

        Enable-SqlAlwaysOn `
            -Path SQLSERVER:\SQL\$server1\Default `
            -Force
        Enable-SqlAlwaysOn `
            -Path SQLSERVER:\SQL\$server2\Default `
            -NoServiceRestart
        $svc2.Stop()
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc2.Start();
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)
10. Cree un directorio de copia de seguridad y conceda permisos a las cuentas de servicio de SQL Server. Usará este directorio para preparar la base de datos de disponibilidad en la réplica secundaria.

         $backup = "C:\backup"
         New-Item $backup -ItemType directory
         net share backup=$backup "/grant:$acct1,FULL" "/grant:$acct2,FULL"
         icacls.exe "$backup" /grant:r ("$acct1" + ":(OI)(CI)F") ("$acct2" + ":(OI)(CI)F")
11. Cree una base de datos en **ContosoSQL1** denominada **MyDB1**, realice una copia de seguridad completa y una copia de seguridad de registros y restáurelas en **ContosoSQL2** con la opción **WITH NORECOVERY**.

         Invoke-SqlCmd -Query "CREATE database $db"
         Backup-SqlDatabase -Database $db -BackupFile "$backupShare\db.bak" -ServerInstance $server1
         Backup-SqlDatabase -Database $db -BackupFile "$backupShare\db.log" -ServerInstance $server1 -BackupAction Log
         Restore-SqlDatabase -Database $db -BackupFile "$backupShare\db.bak" -ServerInstance $server2 -NoRecovery
         Restore-SqlDatabase -Database $db -BackupFile "$backupShare\db.log" -ServerInstance $server2 -RestoreAction Log -NoRecovery
12. Cree los extremos del grupo de disponibilidad en las máquinas virtuales de SQL Server y establezca los permisos adecuados en los extremos.

         $endpoint =
             New-SqlHadrEndpoint MyMirroringEndpoint `
             -Port 5022 `
             -Path "SQLSERVER:\SQL\$server1\Default"
         Set-SqlHadrEndpoint `
             -InputObject $endpoint `
             -State "Started"
         $endpoint =
             New-SqlHadrEndpoint MyMirroringEndpoint `
             -Port 5022 `
             -Path "SQLSERVER:\SQL\$server2\Default"
         Set-SqlHadrEndpoint `
             -InputObject $endpoint `
             -State "Started"

         Invoke-SqlCmd -Query "CREATE LOGIN [$acct2] FROM WINDOWS" -ServerInstance $server1
         Invoke-SqlCmd -Query "GRANT CONNECT ON ENDPOINT::[MyMirroringEndpoint] TO [$acct2]" -ServerInstance $server1
         Invoke-SqlCmd -Query "CREATE LOGIN [$acct1] FROM WINDOWS" -ServerInstance $server2
         Invoke-SqlCmd -Query "GRANT CONNECT ON ENDPOINT::[MyMirroringEndpoint] TO [$acct1]" -ServerInstance $server2
13. Cree las réplicas de disponibilidad.

         $primaryReplica =
             New-SqlAvailabilityReplica `
             -Name $server1 `
             -EndpointURL "TCP://$server1.corp.contoso.com:5022" `
             -AvailabilityMode "SynchronousCommit" `
             -FailoverMode "Automatic" `
             -Version 11 `
             -AsTemplate
         $secondaryReplica =
             New-SqlAvailabilityReplica `
             -Name $server2 `
             -EndpointURL "TCP://$server2.corp.contoso.com:5022" `
             -AvailabilityMode "SynchronousCommit" `
             -FailoverMode "Automatic" `
             -Version 11 `
             -AsTemplate
14. Por último, cree el grupo de disponibilidad y una la réplica secundaria al grupo de disponibilidad.

         New-SqlAvailabilityGroup `
             -Name $ag `
             -Path "SQLSERVER:\SQL\$server1\Default" `
             -AvailabilityReplica @($primaryReplica,$secondaryReplica) `
             -Database $db
         Join-SqlAvailabilityGroup `
             -Path "SQLSERVER:\SQL\$server2\Default" `
             -Name $ag
         Add-SqlAvailabilityDatabase `
             -Path "SQLSERVER:\SQL\$server2\Default\AvailabilityGroups\$ag" `
             -Database $db

## <a name="next-steps"></a>Pasos siguientes
Ha implementado correctamente SQL Server AlwaysOn mediante la creación de un grupo de disponibilidad en Azure. Para configurar un agente de escucha para este grupo de disponibilidad, consulte [Configuración de un agente de escucha con ILB para grupos de disponibilidad AlwaysOn en Azure](../classic/ps-sql-int-listener.md).

Para más información sobre el uso de SQL Server en Azure, consulte [SQL Server en Azure Virtual Machines](../sql/virtual-machines-windows-sql-server-iaas-overview.md).
