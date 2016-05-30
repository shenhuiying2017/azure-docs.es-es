<properties 
	pageTitle="Entorno de prueba de aplicación LOB | Microsoft Azure" 
	description="Aprenda a crear una aplicación de línea de negocio basada en web en un entorno de nube híbrida para profesionales de TI o pruebas de desarrollo." 
	services="virtual-machines-windows" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-resource-manager"/>

<tags 
	ms.service="virtual-machines-windows" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/01/2016" 
	ms.author="josephd"/>

# Configuración de una aplicación de LOB basada en web en una nube híbrida para pruebas

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementación clásica.

En este tema se le guiará en el proceso de creación de un entorno de nube híbrida para probar una aplicación de línea de negocio (LOB) basada en web Microsoft Azure. Aquí está la configuración resultante.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-lob/virtual-machines-windows-ps-hybrid-cloud-test-env-lob-ph3.png)

Para obtener un ejemplo de una aplicación de producción de LOB hospedada en Azure, consulte el plano técnico de la arquitectura de las **aplicaciones de línea de negocios** en [Diagramas y planos técnicos de la arquitectura de software de Microsoft](http://msdn.microsoft.com/dn630664).

Esta configuración simula una aplicación de LOB en un entorno de producción de Azure desde su ubicación en Internet. Consta de:

- Una red local simplificada (la subred de red corporativa).
- Una red virtual entre locales hospedada en Azure (TestVNET).
- Una conexión VPN de sitio a sitio.
- Un servidor de línea de negocio, SQL server y un controlador de dominio secundario en la red virtual TestVNET.

Esta configuración proporciona una base y un punto de partida común desde el que puede:

- Desarrollar y probar aplicaciones de LOB hospedadas en Internet Information Services (IIS) con un back-end de base de datos de SQL Server 2014 en Azure.
- Realizar pruebas de esta carga de trabajo de TI basada en la nube híbrida.

Hay tres fases principales para configurar este entorno de prueba de nube híbrida:

1.	Configuración del entorno de nube híbrida para pruebas.
2.	Configuración del equipo con SQL server (SQL1).
3.	Configuración del servidor de línea de negocio (LOB1).

Esta carga de trabajo requiere una suscripción de Azure. Si tiene una suscripción de MSDN o de Visual Studio, consulte [Crédito mensual de Azure para suscriptores de Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

## Fase 1: configuración del entorno de nube híbrida

Utilice las instrucciones del tema [Configuración de un entorno de nube híbrida para hacer pruebas](virtual-machines-windows-ps-hybrid-cloud-test-env-base.md). Dado que este entorno de prueba no requiere la presencia del servidor APP1 en la subred de la red corporativa, no dude en cerrarlo por ahora.

Esta es su configuración actual.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-lob/virtual-machines-windows-ps-hybrid-cloud-test-env-lob-ph1.png)

> [AZURE.NOTE] Asimismo, puede configurar el [entorno de prueba de nube híbrida simulada para la fase 1](virtual-machines-windows-ps-hybrid-cloud-test-env-sim.md).
 
## Fase 2: configuración del equipo con SQL Server (SQL1)

Desde el Portal de Azure, inicie el equipo de DC2 si es necesario.

A continuación, cree una máquina virtual de Azure para SQL1 con estos comandos en un símbolo del sistema de Azure PowerShell en el equipo local. Antes de ejecutar estos comandos, introduzca los valores de las variables y quite los caracteres < and >.

	$rgName="<your resource group name>"
	$locName="<the Azure location of your resource group>"
	$saName="<your storage account name>"
	
	$vnet=Get-AzureRMVirtualNetwork -Name "TestVNET" -ResourceGroupName $rgName
	$subnet=Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name "TestSubnet"
	$pip=New-AzureRMPublicIpAddress -Name SQL1-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic=New-AzureRMNetworkInterface -Name SQL1-NIC -ResourceGroupName $rgName -Location $locName -Subnet $subnet -PublicIpAddress $pip
	$vm=New-AzureRMVMConfig -VMName SQL1 -VMSize Standard_A4
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/SQL1-SQLDataDisk.vhd"
	Add-AzureRMVMDataDisk -VM $vm -Name "Data" -DiskSizeInGB 100 -VhdUri $vhdURI  -CreateOption empty
	
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the SQL Server computer." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName SQL1 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftSQLServer -Offer SQL2014-WS2012R2 -Skus Standard -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/SQL1-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

Use el Portal de Azure para conectarse a SQL1 con la cuenta de administrador local.

1.	En el panel izquierdo del Portal de administración de Azure, haga clic en **Máquinas virtuales** y, a continuación, en **En ejecución** en la columna Estado de SQL1.
2.	En la barra de tareas, haga clic en **Conectar**. 
3.	Cuando se le pida que abra SQL1.rdp, haga clic en **Abrir**.
4.	Cuando aparezca un cuadro de mensaje de conexión a Escritorio remoto, haga clic en **Conectar**.
5.	Cuando se le pidan credenciales, utilice estas:
	- Nombre: **SQL1\**[Nombre de la cuenta de administrador local]
	- Contraseña: [Contraseña de la cuenta de administrador local]
6.	Cuando aparezca un cuadro de mensaje de conexión a Escritorio remoto referido a certificados, haga clic en **Sí**.

A continuación, configure reglas del Firewall de Windows para permitir el tráfico y probar la conectividad básica y SQL Server. Desde un símbolo del sistema de Windows PowerShell con nivel de administrador en SQL1, ejecute estos comandos.

	New-NetFirewallRule -DisplayName "SQL Server" -Direction Inbound -Protocol TCP -LocalPort 1433,1434,5022 -Action allow 
	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc2.corp.contoso.com

El comando ping debería devolver cuatro respuestas correctas desde la dirección IP 192.168.0.4.

A continuación, agregue el disco de datos adicional como un nuevo volumen con la letra de unidad F:.

1.	En el panel izquierdo del Administrador de servidores, haga clic en **Servicios de archivos y almacenamiento** y, a continuación, haga clic en **Discos**.
2.	En el panel de contenido, en el grupo **Discos**, haga clic en **disco 2** (con la **partición** establecida en **Desconocida**).
3.	Haga clic en **Tareas** y, a continuación, haga clic en **Nuevo volumen**.
4.	En la página Antes de empezar del Asistente para volumen nuevo, haga clic en **Siguiente**.
5.	En la página Selección del servidor y del disco, haga clic en **Disco 2** y, a continuación, haga clic en **Siguiente**. Cuando se le solicite, haga clic en **Aceptar**.
6.	En la página Especificación del tamaño de la página de volumen, haga clic en **Siguiente**.
7.	En la página Asignación a una letra de unidad o carpeta, haga clic en **Siguiente**.
8.	En la página Selección de la configuración del sistema de archivos, haga clic en **Siguiente**.
9.	En la página Confirmación de las selecciones, haga clic en **Crear**.
10.	Cuando haya terminado, haga clic en **Cerrar**.

Ejecute estos comandos en el símbolo del sistema de Windows PowerShell en SQL1:

	md f:\Data
	md f:\Log
	md f:\Backup

Después, una SQL1 al dominio de Active Directory CORP con estos comandos en el símbolo del sistema de Windows PowerShell.

	Add-Computer -DomainName corp.contoso.com
	Restart-Computer

Utilice la cuenta CORP\\User1 cuando se le pida que proporcione las credenciales de cuenta de dominio para el comando **Add-Computer**.

Después de reiniciar, use el Portal de Azure para conectarse a SQL1 *con la cuenta de administrador local*.

A continuación, configure SQL Server 2014 para usar la unidad F: para nuevas bases de datos y para los permisos de cuenta de usuario.

1.	Desde la pantalla Inicio, escriba **SQL Server Management** y haga clic en **SQL Server 2014 Management Studio**.
2.	En **Conectar con el servidor**, haga clic en **Conectar**.
3.	En el panel de árbol del Explorador de objetos, haga clic con el botón derecho en **SQL1** y, a continuación, haga clic en **Propiedades**.
4.	En la ventana **Propiedades del servidor**, haga clic en **Configuración de base de datos**.
5.	Busque las **Ubicaciones predeterminadas de la base de datos** y establezca estos valores: 
	- Para **Datos**, escriba la ruta de acceso **f:\\Data**.
	- Para **Registro**, escriba la ruta de acceso **f:\\Log**.
	- Para **Copia de seguridad**, escriba la ruta de acceso **f:\\Backup**.
	- Nota: Solo las bases de datos nuevas utilizan estas ubicaciones.
6.	Haga clic en **Aceptar** para cerrar la ventana.
7.	En el panel de árbol del **Explorador de objetos**, abra **Seguridad**.
8.	Haga clic con el botón derecho en **Inicios de sesión** y, a continuación, haga clic en **Nuevo inicio de sesión**.
9.	En **Nombre de inicio de sesión**, escriba **CORP\\User1**.
10.	En la página **Roles de servidor**, haga clic en **sysadmin** y, a continuación, haga clic en **Aceptar**.
11.	Cierre Microsoft SQL Server Management Studio.

Esta es su configuración actual.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-lob/virtual-machines-windows-ps-hybrid-cloud-test-env-lob-ph2.png)
 
## Fase 3: configuración del servidor de línea de negocio (LOB1)

En primer lugar, cree una máquina virtual de Azure de LOB1 con estos comandos en el símbolo del sistema de Azure PowerShell en el equipo local.

	$rgName="<your resource group name>"
	$locName="<your Azure location, such as West US>"
	$saName="<your storage account name>"
	
	$vnet=Get-AzureRMVirtualNetwork -Name "TestVNET" -ResourceGroupName $rgName
	$subnet=Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name "TestSubnet"
	$pip=New-AzureRMPublicIpAddress -Name LOB1-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic=New-AzureRMNetworkInterface -Name LOB1-NIC -ResourceGroupName $rgName -Location $locName -Subnet $subnet -PublicIpAddress $pip
	$vm=New-AzureRMVMConfig -VMName LOB1 -VMSize Standard_A2
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for LOB1."
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName LOB1 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/LOB1-TestLab-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name LOB1-TestVNET-OSDisk -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

Luego, use el Portal de Azure para conectarse a LOB1 con las credenciales de la cuenta de administrador local.

A continuación, configure una regla del Firewall de Windows para permitir el tráfico para probar la conectividad básica. Desde un símbolo del sistema de Windows PowerShell con nivel de administrador en LOB1, ejecute estos comandos.

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc2.corp.contoso.com

El comando ping debería devolver cuatro respuestas correctas desde la dirección IP 192.168.0.4.

Después, una LOB1 al dominio de Active Directory CORP con estos comandos en el símbolo del sistema de Windows PowerShell.

	Add-Computer -DomainName corp.contoso.com
	Restart-Computer

Utilice la cuenta CORP\\User1 cuando se le pida que proporcione las credenciales de cuenta de dominio para el comando **Add-Computer**.

Después de reiniciar, utilice el Portal de Azure para conectarse a LOB1 con la cuenta y la contraseña de CORP\\User1.

A continuación, configure LOB1 para IIS y pruebe el acceso desde CLIENT1.

1.	Ejecute el Administrador del servidor y haga clic en **Agregar roles y características**.
2.	En la página Antes de comenzar, haga clic en **Siguiente**.
3.	En la página Seleccionar tipo de instalación, haga clic en **Siguiente**.
4.	En la página Seleccionar servidor de destino, haga clic en **Siguiente**.
5.	En la página Roles de servidor, haga clic en **Servidor web (IIS)** en la lista de **Roles**.
6.	Cuando se le solicite, haga clic en **Agregar características** y después en **Siguiente**.
7.	En la página Selección de características, haga clic en **Siguiente**.
8.	En la página Servidor web (IIS), haga clic en **Siguiente**.
9.	En la página Selección de los servicios de rol, seleccione o desactive las casillas de verificación de los servicios que necesita para probar la aplicación del servidor de línea de negocio y, a continuación, haga clic en **Siguiente**.
10.	En la página Confirmación de las selecciones de instalación, haga clic en **Instalar**.
11.	Espere hasta que se haya completado la instalación de los componentes y haga clic en **Cerrar**.
12.	Inicie sesión en el equipo CLIENT1 con las credenciales de la cuenta de CORP\\User1 y, a continuación, inicie Internet Explorer.
13.	En la barra de direcciones, escriba **http://lob1/** y, a continuación, presione ENTRAR. Debería ver la página web predeterminada de IIS 8.

Se trata de la configuración actual.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-lob/virtual-machines-windows-ps-hybrid-cloud-test-env-lob-ph3.png)
 
Este entorno ya está preparado para implementar su aplicación basada en web en LOB1 y probar la funcionalidad y el rendimiento de la subred de la red corporativa.

## Paso siguiente

- Implementar el [entorno de producción](virtual-machines-windows-lob-overview.md)

<!---HONumber=AcomDC_0518_2016-->