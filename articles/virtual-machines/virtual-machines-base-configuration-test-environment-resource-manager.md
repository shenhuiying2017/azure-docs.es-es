<properties
	pageTitle="Entorno de prueba de la configuración base con Administrador de recursos de Azure"
	description="Obtenga información acerca de cómo crear un entorno de desarrollo o prueba sencillo que simule una intranet simplificada en Microsoft Azure con Administrador de recursos."
	documentationCenter=""
	services="virtual-machines"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/23/2015"
	ms.author="josephd"/>

# Entorno de prueba de la configuración base con Administrador de recursos de Azure

En este artículo se proporcionan instrucciones paso a paso para crear el entorno de prueba de la configuración básica en una red virtual de Microsoft Azure mediante las máquinas virtuales creadas en Administrador de recursos.

Puede utilizar el entorno de prueba resultante:

- Para el desarrollo de aplicaciones y pruebas.
- Como configuración inicial de un entorno de prueba extendido de diseño propio que incluya servicios de Azure y máquinas virtuales adicionales.

El entorno de prueba de la configuración base consta de la subred de la red corporativa en una red virtual solo en la nube denominada TestLab que simula una intranet privada simplificada conectada a Internet.

![](./media/virtual-machines-base-configuration-test-environment-resource-manager/BC_TLG04.png)

Contiene:

- Una máquina virtual de Azure con Windows Server 2012 R2 denominada DC1 que está configurada como controlador de dominio de la intranet y un servidor DNS (sistema de nombres de dominio).
- Una máquina virtual de Azure con Windows Server 2012 R2 denominada APP1 que está configurado como servidor web y de aplicaciones general.
- Una máquina virtual de Azure con Windows Server 2012 R2 denominado CLIENT1 que actúa como cliente de la intranet.

Esta configuración permite a DC1, APP1, CLIENT1 y otros equipos de subred de la red corporativa:

- Conectarse a Internet para instalar las actualizaciones, obtener acceso a recursos en Internet en tiempo real y participar en las tecnologías de nube pública, como Microsoft Office 365 y otros servicios de Azure.
- Administrarse de forma remota con Conexiones de escritorio remoto desde el equipo que está conectado a Internet o la red de su organización.

Hay cuatro fases de configuración de la subred de la red corporativa del entorno de pruebas de la configuración base de Windows Server 2012 R2 en Azure.

1.	Creación de la red virtual.
2.	Configuración de DC1.
3.	Configuración de APP1.
4.	Configuración de CLIENT1.

Si no dispone de ninguna cuenta de Azure, puede registrarse para una prueba gratuita en [Probar Azure](http://azure.microsoft.com/pricing/free-trial/). Si tiene una suscripción a MSDN, consulte [Beneficio de Azure para los suscriptores de MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

> [AZURE.NOTE]Las máquinas virtuales en Azure suponen en un costo económico constante cuando se están ejecutando. Este costo se factura en su prueba gratuita, la suscripción de MSDN o la suscripción de pago. Para obtener más información acerca de los costos de ejecutar máquinas virtuales de Azure, consulte [Detalles de precios de máquinas virtuales](http://azure.microsoft.com/pricing/details/virtual-machines/) y [Calculadora de precios de Azure](http://azure.microsoft.com/pricing/calculator/). Para reducir los costos, consulte [Reducción del costo de las máquinas virtuales del entorno de prueba en Azure](#costs).

[AZURE.INCLUDE [resource-manager-pointer-to-service-management](../../includes/resource-manager-pointer-to-service-management.md)]

- [Entorno de prueba de configuración base](virtual-machines-base-configuration-test-environment.md)


## Fase 1: Creación de la red virtual

En primer lugar, si es preciso, siga las instrucciones en [Cómo instalar y configurar Azure PowerShell](../install-configure-powershell.md) para instalar Azure PowerShell en el equipo local. Abra un símbolo del sistema de Azure PowerShell.

A continuación, seleccione la suscripción de Azure correcta con estos comandos. Reemplace todo el contenido dentro de las comillas, incluidos los caracteres < and >, por el nombre correcto.

	$subscr="<Subscription name>"
	Select-AzureSubscription -SubscriptionName $subscr –Current

El nombre de la suscripción se puede obtener de la propiedad SubscriptionName de la visualización del comando **Get-AzureSubscription**.

A continuación, cambie Azure PowerShell al modo Administrador de recursos.

	Switch-AzureMode AzureResourceManager 

Seguidamente, cree un nuevo grupo de recursos para el laboratorio de pruebas de la configuración base. Para determinar el nombre único del grupo de recursos, utilice este comando para enumerar los grupos de recursos existente.

	Get-AzureResourceGroup | Sort ResourceGroupName | Select ResourceGroupName

Para obtener una lista de las ubicaciones de Azure donde puede crear máquinas virtuales basadas en Administrador de recursos, utilice estos comandos.

	$loc=Get-AzureLocation | where { $_.Name –eq "Microsoft.Compute/virtualMachines" }
	$loc.Locations

Cree un nuevo grupo de recursos con estos comandos. Reemplace todo el contenido dentro de las comillas, incluidos los caracteres < and >, por los nombres correctos.

	$rgName="<resource group name>"
	$locName="<location name, such as West US>"
	New-AzureResourceGroup -Name $rgName -Location $locName

Las máquinas virtuales basadas en Administrador de recursos requieren una cuenta de almacenamiento basada en Administrador de recursos. Debe elegir un nombre único global para la cuenta de almacenamiento que contenga solo letras minúsculas y números. Puede usar este comando para enumerar las cuentas de almacenamiento existentes.

	Get-AzureStorageAccount | Sort Name | Select Name

Para probar si la cuenta de almacenamiento elegida tiene un nombre único global, necesitará ejecutar el comando **Test-AzureName** en el modo de administración de servicios de Azure de PowerShell. Use estos comandos.

	Switch-AzureMode AzureServiceManagement
	Test-AzureName -Storage <Proposed storage account name>

Si el comando Test-AzureName muestra **False**, el nombre propuesto es único. Cuando haya determinado un nombre único, vuelva a cambiar Azure PowerShell al modo Administrador de recursos con este comando.

	Switch-AzureMode AzureResourceManager 

Cree una nueva cuenta de almacenamiento para el nuevo entorno de prueba con estos comandos.

	$rgName="<your new resource group name>"
	$locName="<the location of your new resource group>"
	$saName="<storage account name>"
	New-AzureStorageAccount -Name $saName -ResourceGroupName $rgName –Type Standard_LRS -Location $locName

A continuación, cree la red virtual de Azure TestLab que va a hospedar la subred de la red corporativa de la configuración base.

	$rgName="<name of your new resource group>"
	$locName="<Azure location name, such as West US>"
	$corpnetSubnet=New-AzureVirtualNetworkSubnetConfig -Name Corpnet -AddressPrefix 10.0.0.0/24
	New-AzurevirtualNetwork -Name TestLab -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/8 -Subnet $corpnetSubnet –DNSServer 10.0.0.4

Esta es su configuración actual.

![](./media/virtual-machines-base-configuration-test-environment-resource-manager/BC_TLG01.png)

## Fase 2: Configuración de DC1

DC1 es un controlador de dominio para el dominio de servicios de dominio de Active Directory (AD DS) corp.contoso.com y un servidor DNS para las máquinas virtuales de la red virtual TestLab.

En primer lugar, proporcione el nombre del grupo de recursos, la ubicación de Azure y el nombre de la cuenta de almacenamiento, y ejecute estos comandos en el símbolo del sistema de Azure PowerShell en el equipo local para crear una máquina virtual de Azure para DC1.

	$rgName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$saName="<storage account name>"
	$vnet=Get-AzurevirtualNetwork -Name TestLab -ResourceGroupName $rgName
	$pip = New-AzurePublicIpAddress -Name DC1-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic = New-AzureNetworkInterface -Name DC1-NIC -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -PrivateIpAddress 10.0.0.4
	$vm=New-AzureVMConfig -VMName DC1 -VMSize Standard_A1
	$storageAcc=Get-AzureStorageAccount -ResourceGroupName $rgName -Name $saName
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/DC1-TestLab-ADDSDisk.vhd"
	Add-AzureVMDataDisk -VM $vm -Name ADDS-Data -DiskSizeInGB 20 -VhdUri $vhdURI  -CreateOption empty
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for DC1." 
	$vm=Set-AzureVMOperatingSystem -VM $vm -Windows -ComputerName DC1 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureVMNetworkInterface -VM $vm -Id $nic.Id
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/DC1-TestLab-OSDisk.vhd"
	$vm=Set-AzureVMOSDisk -VM $vm -Name DC1-TestLab-OSDisk -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureVM -ResourceGroupName $rgName -Location $locName -VM $vm

A continuación, conéctese a la máquina virtual DC1.

1.	En el Portal de vista previa de Azure, haga clic en **Examinar todo** en el panel izquierdo, en **Máquinas virtuales** en la lista **Examinar** y, a continuación, en la máquina virtual **DC1**.  
2.	En el panel **DC1**, haga clic en **Conectar**.
3.	Cuando se le pida, abra el archivo DC1.rdp descargado.
4.	Cuando aparezca un cuadro de mensaje de conexión a Escritorio remoto, haga clic en **Conectar**.
5.	Cuando se le pidan las credenciales, utilice las siguientes:
- Nombre: **DC1\**[nombre de la cuenta de administrador local]
- Contraseña: [Contraseña de la cuenta de administrador local]
6.	Cuando aparezca un cuadro de mensaje de conexión a Escritorio remoto referido a certificados, haga clic en **Sí**.

A continuación, agregue un disco de datos adicional como un nuevo volumen con la letra de unidad F:.

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

A continuación, configure DC1 como un controlador de dominio y servidor DNS para el dominio corp.contoso.com. Ejecute estos comandos en un símbolo del sistema de Windows PowerShell con nivel de administrador.

	Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
	Install-ADDSForest -DomainName corp.contoso.com -DatabasePath "F:\NTDS" -SysvolPath "F:\SYSVOL" -LogPath "F:\Logs"

Una vez reiniciado DC1, vuelva a conectar la máquina virtual de DC1.

1.	En el Portal de vista previa de Azure, haga clic en Examinar en el panel izquierdo, en Máquinas virtuales en la lista Examinar y, a continuación, en la máquina virtual DC1.
2.	En el panel DC1, haga clic en Conectar.
3.	Cuando se le pida que abra DC1.rdp, haga clic en **Abrir**.
4.	Cuando aparezca un cuadro de mensaje de conexión a Escritorio remoto, haga clic en **Conectar**.
5.	Cuando se le pidan las credenciales, utilice las siguientes:
- Nombre: **CORP\**[nombre de la cuenta de administrador local]
- Contraseña: [Contraseña de la cuenta de administrador local]
6.	Cuando se lo solicite un cuadro de mensaje de conexión a Escritorio remoto que haga referencia a certificados, haga clic en **Sí**.

A continuación, cree una cuenta de usuario en Active Directory que se utilizará al iniciar sesión en equipos miembros del dominio corporativo. Ejecute estos comandos uno a uno en un símbolo del sistema de Windows PowerShell con nivel de administrador.

	New-ADUser -SamAccountName User1 -AccountPassword (read-host "Set user password" -assecurestring) -name "User1" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false
	Add-ADPrincipalGroupMembership -Identity "CN=User1,CN=Users,DC=corp,DC=contoso,DC=com" -MemberOf "CN=Enterprise Admins,CN=Users,DC=corp,DC=contoso,DC=com","CN=Domain Admins,CN=Users,DC=corp,DC=contoso,DC=com"

Tenga en cuenta que el primer comando genera un símbolo del sistema para proporcionar la contraseña de la cuenta User1. Puesto que esta cuenta se utilizará para las conexiones a escritorio remotos para todos los equipos de miembros de dominio CORP, elija una contraseña segura. Para comprobar su robustez, consulte [Comprobador de contraseñas: uso de contraseñas seguras](https://www.microsoft.com/security/pc-security/password-checker.aspx). Registre la contraseña de la cuenta User1 y almacénela en una ubicación segura.

Cierre la sesión de Escritorio remoto con DC1 y, a continuación, vuelva a conectar con la cuenta CORP\\User1.

A continuación, para permitir el tráfico de la herramienta Ping, ejecute este comando en un símbolo de Windows PowerShell con nivel de administrador.

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True

Se trata de la configuración actual.

![](./media/virtual-machines-base-configuration-test-environment-resource-manager/BC_TLG02.png)

## Fase 3: Configuración de APP1

App1 proporciona servicios de uso compartido de archivos y web.

En primer lugar, proporcione el nombre del grupo de recursos, la ubicación de Azure y el nombre de la cuenta de almacenamiento, y ejecute estos comandos en el símbolo del sistema de Azure PowerShell en el equipo local para crear una máquina virtual de Azure para APP1.

	$rgName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$saName="<storage account name>"
	$vnet=Get-AzurevirtualNetwork -Name TestLab -ResourceGroupName $rgName
	$pip = New-AzurePublicIpAddress -Name APP1-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic = New-AzureNetworkInterface -Name APP1-NIC -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
	$vm=New-AzureVMConfig -VMName APP1 -VMSize Standard_A1
	$storageAcc=Get-AzureStorageAccount -ResourceGroupName $rgName -Name $saName
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for APP1." 
	$vm=Set-AzureVMOperatingSystem -VM $vm -Windows -ComputerName APP1 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureVMNetworkInterface -VM $vm -Id $nic.Id
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/APP1-TestLab-OSDisk.vhd"
	$vm=Set-AzureVMOSDisk -VM $vm -Name APP1-TestLab-OSDisk -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureVM -ResourceGroupName $rgName -Location $locName -VM $vm

A continuación, conéctese a la máquina virtual APP1 con el nombre y la contraseña de la cuenta de administrador local APP1 y abra un símbolo de sistema de Windows PowerShell de nivel de administrador.

Para comprobar la comunicación de red y la resolución de nombres entre APP1 y DC1, ejecute el comando **ping dc1.corp.contoso.com** y compruebe que haya cuatro respuestas.

A continuación, una la máquina virtual APP1 al dominio CORP con estos comandos en el símbolo del sistema de Windows PowerShell.

	Add-Computer -DomainName corp.contoso.com
	Restart-Computer

Tenga en cuenta que después de especificar el comando Add-On debe indicar sus credenciales de cuenta del dominio CORP\\User1

Después de que la máquina virtual APP1 se reinicie, conéctese a ella con el nombre y la contraseña de la cuenta CORP\\User1 y abra un símbolo de sistema de Windows PowerShell de nivel de administrador.

A continuación, convierta APP1 un en servidor web con este comando en el símbolo del sistema de Windows PowerShell.

	Install-WindowsFeature Web-WebServer –IncludeManagementTools

A continuación, cree una carpeta compartida y un archivo de texto dentro de la carpeta en APP1 con estos comandos.

	New-Item -path c:\files -type directory
	Write-Output "This is a shared file." | out-file c:\files\example.txt
	New-SmbShare -name files -path c:\files -changeaccess CORP\User1

Se trata de la configuración actual.

![](./media/virtual-machines-base-configuration-test-environment-resource-manager/BC_TLG03.png)

## Fase 4: Configuración de CLIENT1

Client1 actúa como un equipo portátil, tableta o de escritorio en la intranet de Contoso.

En primer lugar, proporcione el nombre del grupo de recursos, la ubicación de Azure y el nombre de la cuenta de almacenamiento, y ejecute estos comandos en el símbolo del sistema de Azure PowerShell en el equipo local para crear una máquina virtual de Azure para CLIENT1.

	$rgName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$saName="<storage account name>"
	$vnet=Get-AzurevirtualNetwork -Name TestLab -ResourceGroupName $rgName
	$pip = New-AzurePublicIpAddress -Name CLIENT1-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic = New-AzureNetworkInterface -Name CLIENT1-NIC -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
	$vm=New-AzureVMConfig -VMName CLIENT1 -VMSize Standard_A1
	$storageAcc=Get-AzureStorageAccount -ResourceGroupName $rgName -Name $saName
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for CLIENT1." 
	$vm=Set-AzureVMOperatingSystem -VM $vm -Windows -ComputerName CLIENT1 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureVMNetworkInterface -VM $vm -Id $nic.Id	
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/CLIENT1-TestLab-OSDisk.vhd"
	$vm=Set-AzureVMOSDisk -VM $vm -Name CLIENT1-TestLab-OSDisk -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureVM -ResourceGroupName $rgName -Location $locName -VM $vm

A continuación, conéctese a la máquina virtual CLIENT1 con el nombre y la contraseña de la cuenta de administrador local CLIENT1 y abra un símbolo de sistema de Windows PowerShell de nivel de administrador.

Para comprobar la comunicación de la red y la resolución de nombres entre CLIENT1 y DC1, ejecute el comando **ping dc1.corp.contoso.com** en un símbolo del sistema de Windows PowerShell y compruebe que hay cuatro respuestas.

A continuación, una la máquina virtual de CLIENT1 al dominio CORP con estos comandos en el símbolo del sistema de Windows PowerShell.

	Add-Computer -DomainName corp.contoso.com
	Restart-Computer

Tenga en cuenta que después de especificar el comando Add-On debe indicar sus credenciales de cuenta del dominio CORP\\User1

Después de que la máquina virtual CLIENT1 se reinicie, conéctese a ella con el nombre y la contraseña de la cuenta CORP\\User1 y abra un símbolo de sistema de Windows PowerShell de nivel de administrador.

A continuación, compruebe que puede tener acceso a recursos compartidos de archivos y web en APP1 de CLIENT1.

1.	En el Administrador de servidores, en el panel de árbol, haga clic en **Servidor Local**.
2.	En **Propiedades de CLIENT1**, haga clic en **Activo** al lado de **Configuración de seguridad mejorada de IE**.
3.	En **Configuración de seguridad mejorada de IE**, haga clic en **Desactivar** para **Administradores** y **Usuarios** y, a continuación, haga clic en **Aceptar**.
4.	En la pantalla Inicio, haga clic en **Internet Explorer** y, a continuación, en **Aceptar**.
5.	En la barra de direcciones, escriba ****http://app1.corp.contoso.com/** y después presione ENTRAR. Debe ver la página web de Internet Information Services de forma predeterminada para APP1.
6.	En la barra de tareas del escritorio, haga clic en el icono Explorador de archivos.
7.	En la barra de direcciones, escriba **\\\\app1\\Files** y, a continuación, presione ENTRAR.
8.	Debería ver una ventana de carpeta con el contenido de la carpeta compartida Archivos.
9.	En la ventana de carpeta compartida **Archivos** , haga doble clic en el archivo **Example.txt**. Debería ver el contenido del archivo Example.txt.
10.	Cierre las ventanas de carpetas compartidas **Example.tx - Bloc de notas** y **Archivos**.

Se trata de la configuración final.

![](./media/virtual-machines-base-configuration-test-environment-resource-manager/BC_TLG04.png)

La configuración base de Azure ya está lista para entornos de pruebas y desarrollo de aplicaciones o para entornos de prueba adicionales.

## Recursos adicionales

[Entornos de prueba de nube híbrida](../virtual-network/virtual-networks-setup-hybrid-cloud-environment-testing.md)

[Entorno de prueba de configuración base](virtual-machines-base-configuration-test-environment.md)


## <a id="costs"></a>Reducción del costo de las máquinas virtuales del entorno de prueba en Azure

Para minimizar el costo de ejecutar las máquinas virtuales de entorno de prueba, puede realizar una de las acciones siguientes:

- Crear el entorno de prueba y realizar las pruebas y demostraciones necesarias lo más rápido posible. Cuando haya finalizado, elimine las máquinas virtuales del entorno de prueba.
- Apagar sus máquinas virtuales del entorno de prueba en un estado desasignado.

Para apagar las máquinas virtuales con Azure PowerShell, escriba el nombre del grupo de recursos y ejecute estos comandos.

	$rgName="<your resource group name>"
	Stop-AzureVM -ResourceGroupName $rgName -Name "CLIENT1"
	Stop-AzureVM -ResourceGroupName $rgName -Name "APP1"
	Stop-AzureVM -ResourceGroupName $rgName -Name "DC1" -Force -StayProvisioned

Para asegurarse de que las máquinas virtuales funcionan correctamente al iniciar todos ellos desde el estado Detenido (desasignado), debe iniciarlos en el orden siguiente:

1.	DC1
2.	APP1
3.	CLIENT1

Para iniciar las máquinas virtuales en orden con Azure PowerShell, escriba el nombre del grupo de recursos y ejecute estos comandos.

	$rgName="<your resource group name>"
	Start-AzureVM -ResourceGroupName $rgName -Name "DC1"
	Start-AzureVM -ResourceGroupName $rgName -Name "APP1"
	Start-AzureVM -ResourceGroupName $rgName -Name "CLIENT1"

<!---HONumber=August15_HO6-->