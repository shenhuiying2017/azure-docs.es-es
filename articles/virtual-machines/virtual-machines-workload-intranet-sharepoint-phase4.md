<properties
	pageTitle="Fase 4 de la granja de SharePoint Server 2013 | Microsoft Azure"
	description="Cree las máquinas virtuales del servidor de SharePoint y una nueva granja de SharePoint en la fase 4 de la granja de SharePoint Server 2013 en Azure."
	documentationCenter=""
	services="virtual-machines"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="Windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/22/2015"
	ms.author="josephd"/>

# Fase 4 de la carga de trabajo de la granja de servidores de intranet de SharePoint: Configuración de servidores de SharePoint

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]En este artículo se trata la creación de recursos con el modelo de implementación clásica.

En esta fase de la implementación de una granja de servidores de SharePoint 2013 de solo intranet con grupos de disponibilidad AlwaysOn de SQL Server en servicios de infraestructura de Azure, creará los niveles web y de aplicaciones de la granja de servidores de SharePoint y creará la granja de servidores con el Asistente para configuración de SharePoint.

Debe completar esta fase antes de pasar a la [fase 5](virtual-machines-workload-intranet-sharepoint-phase5.md). Consulte [Implementación de SharePoint con grupos de disponibilidad AlwaysOn de SQL Server en Azure](virtual-machines-workload-intranet-sharepoint-overview.md) para ver todas las fases.

## Creación de máquinas virtuales de servidor de SharePoint en Azure

Existen cuatro máquinas virtuales de servidor de SharePoint. Dos máquinas virtuales de servidor de SharePoint son para los servidores web front-end y dos son para la administración y el hospedaje de aplicaciones de SharePoint. Dos servidores de SharePoint en cada nivel de proporcionan una alta disponibilidad.

Use el siguiente bloque de comandos de Azure PowerShell para crear las máquinas virtuales para los cuatro servidores de SharePoint. Especifique los valores de las variables quitando los caracteres < and >. Tenga en cuenta que este conjunto de comandos de Azure PowerShell usa los valores de las siguientes tablas:

- Tabla M, para las máquinas virtuales
- Tabla V, para la configuración de red virtual
- Tabla S, para la subred
- Tabla A, para los conjuntos de disponibilidad
- Tabla C, para los servicios en la nube

Recuerde que definió la Tabla M en [Fase 2: Configuración de controladores de dominio](virtual-machines-workload-intranet-sharepoint-phase2.md) y las Tablas V, S, A y C en [Fase 1: Configuración de Azure](virtual-machines-workload-intranet-sharepoint-phase1.md).

Cuando proporcione todos los valores adecuados, ejecute el bloque resultante en el símbolo del sistema de PowerShell de Azure.

	# Create the first SharePoint application server
	$vmName="<Table M – Item 6 - Virtual machine name column>"
	$vmSize="<Table M – Item 6 - Minimum size column, specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$availSet="<Table A – Item 3 – Availability set name column>"
	$image= Get-AzureVMImage | where { $_.Label -eq "SharePoint Server 2013 Trial" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -ImageName $image -AvailabilitySetName $availSet

	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for the first SharePoint application server."
	$cred2=Get-Credential –Message "Now type the name and password of an account that has permissions to add this virtual machine to the domain."
	$ADDomainName="<name of the AD domain that the server is joining (example CORP)>"
	$domainDNS="<FQDN of the AD domain that the server is joining (example corp.contoso.com)>"
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain $ADDomainName -DomainUserName $cred2.GetNetworkCredential().Username -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain $domainDNS

	$subnetName="<Table 6 – Item 1 – Subnet name column>"
	$vm1 | Set-AzureSubnet -SubnetNames $subnetName

	$serviceName="<Table C – Item 3 – Cloud service name column>"
	$vnetName="<Table V – Item 1 – Value column>"
	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName $vnetName

	# Create the second SharePoint application server
	$vmName="<Table M – Item 7 - Virtual machine name column>"
	$vmSize="<Table M – Item 7 - Minimum size column, specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$vm1=New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -ImageName $image -AvailabilitySetName $availSet

	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for the second SharePoint application server."
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain $ADDomainName -DomainUserName $cred2.GetNetworkCredential().Username -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain $domainDNS

	$vm1 | Set-AzureSubnet -SubnetNames $subnetName

	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName $vnetName

	# Create the first SharePoint web server
	$vmName="<Table M – Item 8 - Virtual machine name column>"
	$vmSize="<Table M – Item 8 - Minimum size column, specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$availSet="<Table A – Item 4 – Availability set name column>"
	$vm1=New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -ImageName $image -AvailabilitySetName $availSet

	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for the first SharePoint web server."
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain $ADDomainName -DomainUserName $cred2.GetNetworkCredential().Username -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain $domainDNS

	$vm1 | Set-AzureSubnet -SubnetNames $subnetName

	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName $vnetName

	# Create the second SharePoint web server
	$vmName="<Table M – Item 9 - Virtual machine name column>"
	$vmSize="<Table M – Item 9 - Minimum size column, specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$vm1=New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -ImageName $image -AvailabilitySetName $availSet

	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for the second SharePoint web server."
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain $ADDomainName -DomainUserName $cred2.GetNetworkCredential().Username -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain $domainDNS

	$vm1 | Set-AzureSubnet -SubnetNames $subnetName

	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName $vnetName

Use el procedimiento [Inicio de sesión en una máquina virtual con una conexión a Escritorio remoto](virtual-machines-workload-intranet-sharepoint-phase2.md#logon) cuatro veces, una vez para cada servidor de SharePoint, para iniciar sesión con las credenciales de cuenta [Dominio]\\sp\_farm\_db. Ha creado estas credenciales en [Fase 2: configuración de controladores de dominio](virtual-machines-workload-intranet-sharepoint-phase2.md).

Use el procedimiento [Para probar la conectividad](virtual-machines-workload-intranet-sharepoint-phase2.md#testconn) cuatro veces, uno para cada servidor de SharePoint, para probar la conectividad en las ubicaciones de la red de la organización.

> [AZURE.NOTE]Los servidores de SharePoint se crean a partir de la imagen de la versión de evaluación de SharePoint Server 2013. Necesita convertir la instalación para que utilice una clave de licencia de minorista o de licencia por volumen para las ediciones Standard o Enterprise de SharePoint Server 2013.

## Configuración de la granja de servidores de SharePoint

Siga estos pasos para configurar el primer servidor de SharePoint en la granja de servidores:

1.	Desde el escritorio del primer servidor de aplicaciones de SharePoint, haga doble clic en **Asistente para configuración de productos de SharePoint 2013**. Cuando se le pida que permita al programa realizar cambios en el equipo, haga clic en **Sí**.
2.	En la página **Productos de SharePoint**, haga clic en **Siguiente**.
3.	Aparece un cuadro de diálogo **Asistente para configuración de productos de SharePoint** que le advierte que se reiniciarán o restablecerán los servicios (como IIS). Haga clic en **Sí**.
4.	En la página **Conectar a una granja de servidores**, seleccione **Crear una nueva granja de servidores** y después haga clic en **Siguiente**.
5.	En la página **Especificar las opciones de la base de datos de configuración**:
 - En **Servidor de base de datos**, escriba el nombre del servidor de la base de datos principal.
 - En **Nombre de usuario**, escriba [dominio]** \\sp\_farm\_db** (creado en [Fase 2: Configuración de controladores de dominio](virtual-machines-workload-intranet-sharepoint-phase2.md)). Recuerde que la cuenta de sp\_farm\_db tiene privilegios de sysadmin en el servidor de la base de datos.
 - En **Contraseña**, escriba la contraseña de la cuenta de sp\_farm\_db.
6.	Haga clic en **Siguiente**.
7.	En la página **Especificar configuración de seguridad del conjunto de servidores**, escriba una frase de contraseña dos veces. Registre la frase de contraseña y almacénela en una ubicación segura para futuras referencias. Haga clic en **Siguiente**.
8.	En la página **Configurar la aplicación web de Administración central de SharePoint**, haga clic en **Siguiente**.
9.	Aparecerá la página **Finalizando el Asistente para configuración de Productos de SharePoint**. Haga clic en **Siguiente**.
10.	Aparecerá la página **Configurando Productos de SharePoint**. Espere hasta que se complete el proceso de configuración, aproximadamente 8 minutos.
11.	Después de configurar correctamente la granja de servidores, haga clic en **Finalizar**. Se iniciará el nuevo sitio web de administración.
12.	Para empezar a configurar la granja de servidores de SharePoint, haga clic en **Iniciar el Asistente**.

Realice el procedimiento siguiente en el segundo servidor de aplicaciones de SharePoint y los dos servidores web front-end:

1.	En el escritorio, haga doble clic en ** Asistente para la configuración de productos de SharePoint 2013**. Cuando se le pida que permita al programa realizar cambios en el equipo, haga clic en **Sí**.
2.	En la página **Productos de SharePoint**, haga clic en **Siguiente**.
3.	Aparece un cuadro de diálogo **Asistente para configuración de productos de SharePoint** que le advierte que se reiniciarán o restablecerán los servicios (como IIS). Haga clic en **Sí**.
4.	En la página **Conectar a una granja de servidores**, haga clic en **Conectar con un conjunto de servidores existente** y después haga clic en **Siguiente**.
5.	En la página **Especificar las opciones de la base de datos de configuración**, escriba el nombre del servidor de base de datos principal en **Servidor de base de datos** y después haga clic en **Recuperar nombres de base de datos**.
6.	Haga clic en **SharePoint\_Config** en la lista de nombres de la base de datos y después haga clic en **Siguiente**.
7.	En la página **Especificar configuración de seguridad del conjunto de servidores**, escriba la frase de contraseña del procedimiento anterior. Haga clic en **Siguiente**.
8.	Aparecerá la página **Finalizando el Asistente para configuración de Productos de SharePoint**. Haga clic en **Siguiente**.
9.	En la página **Configuración realizada correctamente**, haga clic en **Finalizar**.

Cuando SharePoint crea la granja de servidores, configura un conjunto de inicios de sesión de servidor en la máquina virtual principal de SQL Server. SQL Server 2012 introduce el concepto de usuarios con contraseñas para bases de datos independientes. La base de datos almacena todos los metadatos de la base de datos y la información de usuario, y un usuario que se define en esta base de datos no necesita tener un inicio de sesión correspondiente. La información de esta base de datos se replica mediante el grupo de disponibilidad y está disponible después de una conmutación por error. Para obtener más información, consulte [Bases de datos independientes](http://go.microsoft.com/fwlink/p/?LinkId=262794).

Sin embargo, las bases de datos de SharePoint no son bases de datos independientes de forma predeterminada. Por lo tanto, deberá configurar manualmente el servidor de base de datos secundario para que tenga el mismo conjunto de inicios de sesión para las cuentas de granja de servidores de SharePoint que el servidor de base de datos principal. Puede realizar esta sincronización desde SQL Server Management Studio conectándose a ambos servidores al mismo tiempo.

Tras concluir la instalación inicial, existen más opciones de configuración para las capacidades de la granja de servidores de SharePoint. Para obtener más información, consulte [Planificación para SharePoint 2013 en los servicios de infraestructura de Azure](http://msdn.microsoft.com/library/dn275958.aspx).

## Configuración del equilibrio de carga interno

Configure el equilibrio de carga interno para que el tráfico de cliente a la granja de SharePoint se distribuya por igual a los dos servidores web front-end. Esto requiere que especifique una instancia de equilibrio de cargo que consta de un nombre y su propia dirección IP, que se asignan a partir del espacio de direcciones de subred. Para determinar si una dirección IP que elige para el equilibrador de carga interno está disponible, utilice los siguientes comandos de PowerShell de Azure:

	$vnet="<Table V – Item 1 – Value column>"
	$testIP="<a chosen IP address from the subnet address space, Table S - Item 1 – Subnet address space column>"
	Test-AzureStaticVNetIP –VNetName $vnet –IPAddress $testIP

Si el campo **IsAvailable** en la visualización del comando **Test-AzureStaticVNetIP** es **True**, puede utilizar la dirección IP.

Ejecute el siguiente conjunto de comandos en el símbolo del sistema de Azure PowerShell en el equipo local:

	$serviceName="<Table C – Item 3 – Cloud service name column>"
	$ilb="<name of your internal load balancer instance>"
	$subnet="<Table S – Item 1 – Subnet name column>"
	$IP="<an available IP address for your ILB instance>"
	Add-AzureInternalLoadBalancer –ServiceName $serviceName -InternalLoadBalancerName $ilb –SubnetName $subnet –StaticVNetIPAddress $IP

	$prot="tcp"
	$locport=80
	$pubport=80
	# This example assumes unsecured HTTP traffic to the SharePoint farm.

	$epname="SPWeb1"
	$vmname="<Table M – Item 8 – Virtual machine name column>"
	Get-AzureVM –ServiceName $serviceName –Name $vmname | Add-AzureEndpoint -Name $epname -LBSetName $ilb -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM

	$epname="SPWeb2"
	$vmname="<Table M – Item 9 – Virtual machine name column>"
	Get-AzureVM –ServiceName $serviceName –Name $vmname | Add-AzureEndpoint -Name $epname -LBSetName $ilb -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM

A continuación, agregue un registro de dirección DNS para la infraestructura DNS de su organización que resuelva el nombre de dominio completo de la granja de servidores de SharePoint (por ejemplo, sp.corp.contoso.com) a la dirección IP asignada a la instancia de equilibrador de carga interno (el valor de **$IP** en el bloque de comandos de PowerShell de Azure anterior).

Esta es la configuración resultante de la realización correcta de esta fase:

![](./media/virtual-machines-workload-intranet-sharepoint-phase4/workload-spsqlao_04.png)

## Paso siguiente

Para continuar con la configuración de esta carga de trabajo, vaya a [Fase 5: Creación del grupo de disponibilidad y adición de las bases de datos de SharePoint](virtual-machines-workload-intranet-sharepoint-phase5.md).

## Recursos adicionales

[Implementación de SharePoint con grupos de disponibilidad AlwaysOn de SQL Server en Azure](virtual-machines-workload-intranet-sharepoint-overview.md)

[Granjas de servidores de SharePoint hospedadas en servicios de infraestructura de Azure](virtual-machines-sharepoint-infrastructure-services.md)

[Infografía de SharePoint con AlwaysOn de SQL Server](http://go.microsoft.com/fwlink/?LinkId=394788)

[Arquitecturas de Microsoft Azure para SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx)

[Instrucciones de implementación de los servicios de infraestructura de Azure](virtual-machines-infrastructure-services-implementation-guidelines.md)

[Carga de trabajo de servicios de infraestructura de Azure: aplicación de línea de negocio de alta disponibilidad](virtual-machines-workload-high-availability-lob-application.md)

<!---HONumber=Oct15_HO2-->