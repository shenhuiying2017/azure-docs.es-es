<properties 
	pageTitle="Fase 2 de la carga de trabajo de la granja de servidores de intranet de SharePoint: Configuración de controladores de dominio" 
	description="En esta segunda fase de la implementación de una granja de servidores solo de intranet de SharePoint 2013 con grupos de disponibilidad AlwaysOn de SQL Server en los servicios de infraestructura de Azure, creará y configurará los dos controladores de dominio de Active Directory." 
	documentationCenter=""
	services="virtual-machines" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/05/2015" 
	ms.author="josephd"/>

# Fase 2 de la carga de trabajo de la granja de servidores de intranet de SharePoint: Configuración de controladores de dominio

En esta fase de la implementación de una granja de SharePoint 2013 de solo intranet con grupos de disponibilidad AlwaysOn de SQL Server en servicios de infraestructura de Azure, configure dos controladores de dominio en la red virtual de Azure para que se puedan autenticar solicitudes web de cliente para los recursos de la granja de servidores de SharePoint en la red virtual de Azure, en lugar de enviar ese tráfico de autenticación a través de la conexión VPN o ExpressRoute a la red local.

Debe completar esta fase antes de pasar a la [fase 3](virtual-machines-workload-intranet-sharepoint-phase3.md). Consulte [Implementación de SharePoint con grupos de disponibilidad AlwaysOn de SQL Server en Azure](virtual-machines-workload-intranet-sharepoint-overview.md) para ver todas las fases.

## Creación de máquinas virtuales de controlador de dominio en Azure

En primer lugar, se debe rellenar la columna **Nombre de máquina Virtual** de la Tabla M y modificar los tamaños de máquina virtual según sea necesario en la columna  **Tamaño mínimo**.

Elemento | Nombre de la máquina virtual | Imagen de la Galería | Tamaño mínimo 
--- | --- | --- | --- 
1. | ______________ (primer controlador de dominio, ejemplo DC1) | Windows Server 2012 R2 Datacenter | A2 (mediano)
2. | ______________ (segundo controlador de dominio, ejemplo DC2) | Windows Server 2012 R2 Datacenter | A2 (mediano)
3. | ______________ (primer equipo de SQL Server, ejemplo SQL1) | Microsoft SQL Server 2014 Enterprise – Windows Server 2012 R2 | 	A7
4. | ______________ (segundo equipo de SQL Server, ejemplo SQL2) | Microsoft SQL Server 2014 Enterprise – Windows Server 2012 R2 | 	A7
5. | ______________ (testigo de nodos de mayoría para el clúster, ejemplo MN1) | Windows Server 2012 R2 Datacenter | A1 (pequeño)
6. | ______________  (primer servidor de aplicaciones de SharePoint, ejemplo APP1) | Versión de evaluación de Microsoft SharePoint Server 2013: Windows Server 2012 R2 | A4 (extragrande)
7. | ______________  (segundo servidor de aplicaciones de SharePoint, ejemplo APP2) | Versión de evaluación de Microsoft SharePoint Server 2013: Windows Server 2012 R2 | A4 (extragrande)
8. | ______________ (primer servidor web de SharePoint, ejemplo WEB1) | Versión de evaluación de Microsoft SharePoint Server 2013: Windows Server 2012 R2 | A4 (extragrande)
9. | ______________ (segundo servidor web de SharePoint, ejemplo WEB2) | Versión de evaluación de Microsoft SharePoint Server 2013: Windows Server 2012 R2 | A4 (extragrande)

**Tabla M: Máquinas virtuales para la granja de servidores de intranet de SharePoint 2013 en Azure**

Para obtener una lista completa de tamaños de máquinas virtuales, consulte [Tamaños de máquinas virtuales y servicios en la nube de Azure](https://msdn.microsoft.com/library/azure/dn197896.aspx).

Utilice el siguiente bloque de comandos de PowerShell para crear las máquinas virtuales para los dos controladores de dominio. Especifique los valores de las variables quitando los caracteres < and >. Tenga en cuenta que este conjunto de comandos de PowerShell usa los valores de las siguientes opciones:

- Tabla M, para las máquinas virtuales
- Tabla V, para la configuración de red virtual
- Tabla S, para la subred
- Tabla A, para los conjuntos de disponibilidad
- Tabla C, para los servicios en la nube

Recuerde que ha definido las Tablas V, S, A y C en [Fase 1: Configuración de Azure](virtual-machines-workload-intranet-sharepoint-phase1.md).

Cuando proporcione todos los valores adecuados, ejecute el bloque resultante en el símbolo del sistema de PowerShell de Azure.

	# Create the first domain controller
	$vmName="<Table M – Item 1 - Virtual machine name column>"
	$vmSize="<Table M – Item 1 - Minimum size column, specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$availSet="<Table A – Item 1 – Availability set name column>"
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -ImageName $image -AvailabilitySetName $availSet
	
	$cred=Get-Credential –Message "Type the name and password of the local administrator account for the first domain controller."
	$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password
	
	$diskSize=<size of the additional data disk in GB>
	$diskLabel="<the label on the disk>"
	$lun=<Logical Unit Number (LUN) of the disk>
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $diskSize -DiskLabel $diskLabel -LUN $lun -HostCaching None
	
	$subnetName="<Table S – Item 1 – Subnet name column>"
	$vm1 | Set-AzureSubnet -SubnetNames $subnetName
	
	$vm1 | Set-AzureStaticVNetIP -IPAddress <Table V – Item 6 – Value column>
	
	$serviceName="<Table C – Item 1 – Cloud service name column>"
	$vnetName="<Table V – Item 1 – Value column>"
	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName $vnetName
	
	# Create the second domain controller
	$vmName="<Table M – Item 2 - Virtual machine name column>"
	$vmSize="<Table M – Item 2 - Minimum size column, specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	
	$vm1=New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -ImageName $image -AvailabilitySetName $availSet
	
	$cred=Get-Credential –Message "Type the name and password of the local administrator account for the second domain controller."
	$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password 
	
	$diskSize=<size of the additional data disk in GB>
	$diskLabel="<the label on the disk>"
	$lun=<Logical Unit Number (LUN) of the disk>
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $diskSize -DiskLabel $diskLabel -LUN $lun -HostCaching None
	
	$vm1 | Set-AzureSubnet -SubnetNames $subnetName
	
	$vm1 | Set-AzureStaticVNetIP -IPAddress <Table V – Item 7 – Value column>
	
	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName $vnetName

## Configuración del primer controlador de dominio

Inicie sesión en el primer equipo de controlador de dominio mediante las credenciales de la cuenta de administrador local.

### <a id="logon"></a>Inicio de sesión en una máquina virtual con una conexión a Escritorio remoto.

1.	En el Portal de administración de Azure, en el panel izquierdo, haga clic en **Máquinas virtuales**.
2.	Para conectarse a una máquina virtual, haga clic en **En ejecución** en la columna **Estado** junto a su nombre.
3.	En la parte inferior de la página, en la barra de comandos, haga clic en **Conectar**.
4.	El Portal de administración le informa de que el archivo .rdp se está recuperando. Haga clic en **Aceptar**.
5.	Aparecerá un cuadro de diálogo de explorador que mostrará el mensaje: "¿Desea abrir o guardar ComputerName.rdp desde manage.windowsazure.com?" Haga clic en **Abrir**.
6.	Seleccione el cuadro de diálogo **Conexión de escritorio remoto** y haga clic en  **Conectar**.
7.	En el cuadro de diálogo **Seguridad de Windows**, haga clic en **Usar otra cuenta**.
8.	En **Nombre de usuario**, escriba el nombre del máquina virtual y nombre de usuario de la cuenta de administrador local que se creó con la máquina virtual (una cuenta de máquina local). Utilice el siguiente formato: 
- *ComputerName*\\*LocalAdministratorAccountName*
9.	En **Contraseña**, escriba la contraseña para la cuenta de administrador local.
10.	Haga clic en **Aceptar**.
11.	En el cuadro de diálogo **Conexión a Escritorio remoto**, haga clic en **Sí**. El escritorio de la nueva máquina aparece en una ventana de sesión de Escritorio remoto.

A continuación, deberá agregar el disco de datos adicionales al primer controlador de dominio.

### <a id="datadisk"></a>Para inicializar un disco vacío

1.	En el panel izquierdo del Administrador de servidores, haga clic en **Servicios de archivos y almacenamiento** y, a continuación, haga clic en **Discos**.
2.	En el panel de contenido, en el grupo **Discos**, haga clic en **disco 2** (con la **partición** establecida en **Desconocida**).
3.	Haga clic en **Tareas** y, a continuación, haga clic en **Nuevo volumen**.
4.	En la página Antes de empezar del Asistente para volumen nuevo, haga clic en **Siguiente**.
5.	En Selección del servidor y del disco, haga clic en **Disco 2** y, a continuación, haga clic en **Siguiente**. Cuando se le solicite, haga clic en Aceptar.
6.	En la página Especificación del tamaño de la página de volumen, haga clic en **Siguiente**.
7.	En la página Asignación a una letra de unidad o carpeta, haga clic en **Siguiente**.
8.	En la página Selección de la configuración del sistema de archivos, haga clic en **Siguiente**.
9.	En la página Confirmación de las selecciones, haga clic en **Crear**.
10.	Cuando haya terminado, haga clic en **Cerrar**.

A continuación, pruebe la conectividad del primer controlador de dominio en ubicaciones de red de su organización.

### <a id="testconn"></a>Para probar la conectividad

1.	En el escritorio, abra un símbolo del sistema de Windows PowerShell.
2.	En el símbolo del sistema de Windows PowerShell, use el comando **ping** para hacer ping  en nombres y direcciones IP de los recursos en la red de la organización.

Este procedimiento garantiza que la resolución de nombres DNS funciona correctamente (que la máquina virtual está configurada correctamente con los servidores DNS locales) y que se pueden enviar paquetes a la red virtual entre locales y desde ella.

A continuación, desde el símbolo de Windows PowerShell en el primer controlador de dominio, ejecute los siguientes comandos:

	$domname="<DNS domain name of the domain for which this computer will be a domain controller>"
	Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
	Install-ADDSDomainController -InstallDns –DomainName $domname  -DatabasePath "F:\NTDS" -SysvolPath "F:\SYSVOL" -LogPath "F:\Logs"

El equipo se reiniciará.

## Configuración del segundo controlador de dominio

Inicie sesión en el segundo equipo de controlador de dominio mediante las credenciales de la cuenta de administrador local. Para obtener instrucciones, consulte el procedimiento [Inicio de sesión en una máquina virtual de conexión a Escritorio remoto](#logon).

A continuación, deberá agregar el disco de datos adicionales al segundo controlador de dominio. Consulte el procedimiento [Para inicializar un disco vacío](#datadisk).

A continuación, pruebe la conectividad en las ubicaciones de la red de la organización desde el segundo controlador de dominio. Consulte el procedimiento [Para probar la conectividad](#testconn). Use este procedimiento para garantizar que la resolución de nombres DNS funciona correctamente (que la máquina virtual está configurada correctamente con los servidores DNS locales) y que se pueden enviar paquetes a la red virtual entre locales y desde ella.

A continuación, desde el símbolo de Windows PowerShell en el segundo controlador de dominio, ejecute los siguientes comandos:

	$domname="<DNS domain name of the domain for which this computer will be a domain controller>"
	Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
	Install-ADDSDomainController -InstallDns –DomainName $domname  -DatabasePath "F:\NTDS" -SysvolPath "F:\SYSVOL" -LogPath "F:\Logs"

El equipo se reiniciará.

## Configuración de permisos y cuentas de la granja de servidores de SharePoint

La granja de servidores de SharePoint tendrá las siguientes cuentas de usuario:

- sp_farm: una cuenta de usuario para la administración de granjas de servidores de SharePoint.
- sp_farm_db: una cuenta de usuario que tenga derechos de sysadmin en instancias de SQL Server.
- sp_install: una cuenta de usuario que tenga derechos de administración de dominio necesarios para instalar roles y características.
- sqlservice: una cuenta de usuario que se puede ejecuta como instancias de SQL.

A continuación, inicie sesión en cualquier equipo con una cuenta de administrador de dominio para el dominio para el que los controladores de dominio son miembros, abra un símbolo de Windows PowerShell de nivel de administrador y ejecute estos comandos *uno por uno*:

	New-ADUser -SamAccountName sp_farm -AccountPassword (read-host "Set user password" -assecurestring) -name "sp_farm" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false
	
	New-ADUser -SamAccountName sp_farm_db -AccountPassword (read-host "Set user password" -assecurestring) -name "sp_farm_db" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false
	
	New-ADUser -SamAccountName sp_install -AccountPassword (read-host "Set user password" -assecurestring) -name "sp_install" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false
	
	New-	ADUser -SamAccountName sqlservice -AccountPassword (read-host "Set user password" -assecurestring) -name "sqlservice" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false

Para cada comando, se le solicitará que escriba una contraseña. Anote estos nombres de cuenta y contraseñas, y guárdelos en una ubicación segura.

A continuación, realice los pasos siguientes para agregar propiedades de cuenta adicionales a las nuevas cuentas de usuario.

1.	En la pantalla de inicio, escriba **Usuarios de Active Directory** y, a continuación, haga clic en **Usuarios y equipos de Active Directory**. 
2.	En el panel de árbol, abra el dominio y, a continuación, haga clic en **Usuarios**. 
3.	En el panel de contenido, haga clic con el botón secundario en **sp_install** y, a continuación, haga clic en **Agregar a un grupo**.
4.	En el cuadro de diálogo **Seleccionar grupos**, escriba **administradores de dominio** y, a continuación, haga clic en **Aceptar** dos veces.
5.	En el cuadro de diálogo, haga clic en **Ver y hacer clic en Características avanzadas**. La opción le permite ver todos los contenedores ocultos y las pestañas ocultas en las ventanas de propiedades para objetos de AD.
6.	Haga clic con el botón secundario en el nombre de dominio y haga clic en **Propiedades**.
7.	En el cuadro de diálogo **Propiedades**, haga clic en la pestaña **Seguridad** y, a continuación, haga clic en el botón **Avanzado**.
8.	En la ventana **Configuración de seguridad avanzada para<YourDomain>**, haga clic en **Agregar**.
9.	En la ventana **Entrada de permiso para <YourDomain>**, haga clic en **   Seleccionar una entidad de seguridad**.
10.	En el cuadro de texto, escriba **<YourDomain>\\sp_install** y, a continuación, haga clic en **Aceptar**.
11.	Seleccione **Permitir** para **Crear objetos de equipo** y, a continuación, haga clic en **Aceptar** tres veces.

A continuación, actualice los servidores DNS de la red virtual de manera que Azure asigne a las máquinas virtuales las direcciones IP de los dos nuevos controladores de dominio que se usarán como sus servidores DNS. Tenga en cuenta que este procedimiento utiliza los valores de Tabla V (para la configuración de red virtual).

1.	En el panel izquierdo del Portal de administración de Azure, haga clic en **Redes** y, a continuación, haga clic en el nombre de la red virtual  (Tabla V – Elemento 1 – Columna Valor).
2.	Haga clic en **Configurar**.
3.	En **Servidores DNS**, quite las entradas correspondientes a los servidores DNS que se encuentran en la red local.
4.	En **Servidores DNS**, agregue dos entradas con nombres descriptivos y las direcciones IP de estos elementos de dos tablas:
- Tabla V – Elemento 6 – Columna Valor
- Tabla V – Elemento 7 – Columna Valor
5.	En la barra de comandos de la parte inferior, haga clic en **Guardar**.
6.	En el panel izquierdo del Portal de administración de Azure, haga clic en **Máquinas virtuales** y, a continuación, haga clic en la columna **Estado** junto al nombre del primer controlador de dominio.
7.	En la barra de comandos, haga clic en **Reiniciar**.
8.	Cuando se inicia el primer controlador de dominio, haga clic en la columna **Estado**  junto al nombre de su segundo controlador de dominio.
9.	En la barra de comandos, haga clic en **Reiniciar**. Espere hasta que se inicie el segundo controlador de dominio.

Tenga en cuenta que reiniciamos los dos controladores de dominio, por lo que no se configuran con los servidores DNS locales como servidores DNS. Porque ambos son por sí mismos servidores DNS, se configuran automáticamente con los servidores DNS locales como reenviadores DNS cuando ascienden a controladores de dominio.

A continuación, necesitamos crear un sitio de replicación de AD para garantizar que los servidores de la red virtual de Azure utilizan los controladores de dominio local. Inicie sesión en el controlador de dominio principal con la cuenta sp_install y ejecute los siguientes comandos desde un símbolo de Windows PowerShell de nivel de administrador:

	$vnet="<Table V – Item 1 – Value column>"
	$vnetSpace="<Table V – Item 5 – Value column>"
	New-ADReplicationSite -Name $vnet 
	New-ADReplicationSubnet –Name $vnetSpace –Site $vnet

Esto muestra la configuración resultante de la realización correcta de esta fase con los nombres de equipo de marcador de posición.

![](./media/virtual-machines-workload-intranet-sharepoint-phase2/workload-spsqlao_02.png)

## Paso siguiente

Para continuar con la configuración de esta carga de trabajo, vaya a [Fase 3: Configuración de la infraestructura de SQL Server](virtual-machines-workload-intranet-sharepoint-phase3.md).

## Recursos adicionales

[Implementación de SharePoint con grupos de disponibilidad AlwaysOn de SQL Server en Azure](virtual-machines-workload-intranet-sharepoint-overview.md)

[Granjas de servidores de SharePoint hospedadas en servicios de infraestructura de Azure](virtual-machines-sharepoint-infrastructure-services.md)

[Infografía de SharePoint con AlwaysOn de SQL Server](http://go.microsoft.com/fwlink/?LinkId=394788)

[Arquitecturas de Microsoft Azure para SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx)

<!--HONumber=54-->