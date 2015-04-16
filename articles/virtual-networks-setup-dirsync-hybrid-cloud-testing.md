<properties 
	pageTitle="Configuración de la sincronización de directorios (DirSync) de Office 365 en una nube híbrida para pruebas" 
	description="Aprenda a configurar un servidor de sincronización de directorios (DirSync) de Office 365 en una nube híbrida de profesionales de TI o de pruebas de desarrollo." 
	services="virtual-network" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-network" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/05/2015" 
	ms.author="josephd"/>

#Configuración de la sincronización de directorios (DirSync) de Office 365 en una nube híbrida para pruebas

En este tema se le guiará en el proceso de creación de un entorno de nube híbrida para probar la sincronización de directorios (DirSync) de Office 365 con la sincronización de contraseña hospedada en Microsoft Azure. Aquí está la configuración resultante.

![](./media/virtual-networks-set-up-DirSync-hybrid-cloud-for-testing/CreateDirSyncHybridCloud_3.png)
 
Esta configuración simula un servidor DirSync en el entorno de producción de Azure desde su ubicación en Internet. Consta de:

- Una red local simplificada (la subred de red corporativa).
- Una red virtual entre locales hospedada en Azure (TestVNET).
- Una conexión VPN de sitio a sitio.
- Una suscripción de prueba de Office 365 FastTrack.
- Un servidor DirSync y un controlador de dominio secundario en la red virtual TestVNET.

Esta configuración proporciona una base y un punto de partida común desde el que puede:

- Desarrollar y probar aplicaciones para Office 365 basadas en la sincronización con un dominio de Active Directory local mediante la sincronización de contraseñas.
- Realizar pruebas de esta carga de trabajo de TI basada en la nube.

Hay tres fases principales para configurar este entorno de prueba de nube híbrida:

1.	Configuración del entorno de nube híbrida para pruebas.
2.	Configuración de la versión de prueba de Office 365 FastTrack.
3.	Configuración del servidor DirSync (DS1).

Si todavía no dispone de una suscripción a Azure, puede registrarse para una prueba gratuita en [Probar Azure](http://azure.microsoft.com/pricing/free-trial/). Si tiene una suscripción a MSDN, consulte [Beneficio de Azure para los suscriptores de MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

##Fase 1: Configuración del entorno de nube híbrida

Utilice las instrucciones del tema [Configuración de un entorno de nube híbrida para pruebas](virtual-networks-setup-hybrid-cloud-environment-testing.md) . Dado que este entorno de prueba no requiere la presencia del servidor APP1 en la subred de la red corporativa, no dude en cerrarlo por ahora.

Esta es su configuración actual.

![](./media/virtual-networks-set-up-DirSync-hybrid-cloud-for-testing/CreateDirSyncHybridCloud_1.png)

##Fase 2: Configuración de la versión de prueba de Office 365 FastTrack

Para iniciar la versión de prueba de Office 365 FastTrack, necesita un nombre de compañía ficticio y una cuenta de Microsoft. Se recomienda que utilice una variante del nombre de compañía Contoso como nombre de su compañía, que es una compañía ficticia que se usa en el contenido de ejemplo de Microsoft, pero no es obligatorio.

A continuación, regístrese para obtener una nueva cuenta de Microsoft. Vaya a **http://outlook.com** y cree una cuenta con una dirección de correo electrónico, como usuario123@outlook.com. Regístrese para una prueba de Office 365 FastTrack con esa cuenta.

A continuación, regístrese para obtener una nueva prueba de Office 365 FastTrack.

1.	Inicie sesión en CLIENT1 con las credenciales de cuenta CORP\User1.
2.	Abra Internet Explorer y vaya a **http://fasttrack.office.com**.
3.	Haga clic en **Introducción a FastTrack**.
4.	En la página Introducción a FastTrack, en **Primero, regístrese para obtener una prueba de Office 365**, haga clic en **Para empresas, regístrese aquí**.
5.	En la página Paso 1, rellene la página, especifique la nueva cuenta de Microsoft en **Dirección de correo electrónico de trabajo** y haga clic en **Siguiente**.
6.	En la página Paso 2, escriba el nombre de una cuenta de Office 365 inicial en el primer campo, el nombre de la compañía ficticia y una contraseña. Anote la dirección de correo electrónico resultante (por ejemplo, usuario123@contoso123.onmicrosoft.com) y la contraseña en una ubicación segura. Necesitará esta información para completar el asistente de configuración de la herramienta de sincronización de Active Directory en la fase 3. Haga clic en **Next**.
7.	En la página Paso 3, escriba el número de teléfono de su smartphone o teléfono móvil o con función de mensajes de texto y, a continuación, haga clic en **Enviar mensaje**.
8.	Cuando reciba el mensaje de texto en el teléfono, escriba el código de verificación y haga clic en **Crear mi cuenta**. 
9.	Cuando Office 365 haya finalizado la creación de la cuenta, haga clic en **Ya está listo**.
10.	Ahora debería ver la página principal del portal de Office 365. En la cinta de opciones superior, haga clic en **Admin**y, después, haga clic en **Office 365**. Aparecerá la página del centro de administración de Office 365. Mantenga esta página abierta en CLIENT1.

Esta es su configuración actual.

![](./media/virtual-networks-set-up-DirSync-hybrid-cloud-for-testing/CreateDirSyncHybridCloud_2.png)

##Fase 3: Configuración del servidor DirSync (DS1)

En primer lugar, cree una máquina virtual de Azure de DS1 con estos comandos en el símbolo del sistema de Azure PowerShell en el equipo local. Antes de ejecutar estos comandos, introduzca los valores de las variables y quite los caracteres < y >.

	$ServiceName="<The cloud service name for your TestVNET virtual network>"	
	$LocalAdminName="<A local administrator account name>" 
	$LocalAdminPW="<The password for the local administrator account>"
	$User1Password="<The password for the CORP\User1 account>"
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name DS1 -InstanceSize Medium -ImageName $image
	$vm1 | Add-AzureProvisioningConfig -AdminUserName $LocalAdminName -Password $LocalAdminPW -WindowsDomain -Domain "CORP" -DomainUserName "User1" -DomainPassword $User1Password -JoinDomain "corp.contoso.com"
	$vm1 | Set-AzureSubnet -SubnetNames TestSubnet
	New-AzureVM -ServiceName $ServiceName -VMs $vm1 -VNetName TestVNET

A continuación, conéctese a la máquina virtual de DS1.

1.	En la página de máquinas virtuales del Portal de administración de Azure, haga clic en **En ejecución** en la columna de estado de la máquina virtual de DS1.
2.	En la barra de tareas, haga clic en **Conectar**. 
3.	Cuando se le pida que abra DS1.rdp, haga clic en **Abrir**.
4.	Cuando aparezca un cuadro de mensaje de conexión a Escritorio remoto, haga clic en **Conectar**.
5.	Cuando se le pidan credenciales, utilice estas:
	- Nombre: **CORP\User1**
	- Contraseña: [Contraseña de la cuenta User1]
6.	Cuando aparezca un cuadro de mensaje de conexión a Escritorio remoto que haga referencia a certificados, haga clic en **Sí**.

A continuación, configure una regla del Firewall de Windows para permitir el tráfico para probar la conectividad básica. Desde un símbolo del sistema de Windows PowerShell con nivel de administrador en DS1, ejecute estos comandos.

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc1.corp.contoso.com

El comando ping debería producir cuatro respuestas correctas desde la dirección IP 10.0.0.1.

A continuación, instale .NET 3.5 en DS1 con este comando en el símbolo del sistema de Windows PowerShell.

	Add-WindowsFeature NET-Framework-Core

A continuación, instale la sincronización de directorios en DS1.

1.	Ejecute Internet Explorer, escriba **http://go.microsoft.com/fwlink/?LinkID=278924** en la barra de direcciones y, a continuación, presione ENTRAR. Cuando se le solicite ejecutar dirsync.exe, haga clic en la flecha junto a **Guardar**, haga clic en **Guardar como**y, a continuación, haga clic en **Guardar** para guardar el archivo en la carpeta Descargas. Para obtener más información acerca de cómo instalar la herramienta, consulte [Instalación o actualización de la herramienta de sincronización de directorios](http://technet.microsoft.com/library/jj151800).
2.	Abra la carpeta **Descargas**, haga clic en el archivo **dirsync** y, a continuación, haga clic en **Ejecutar como administrador**.
3.	En la página de bienvenida del Asistente de configuración de sincronización de Active Directory, haga clic en **Siguiente**. 
4.	En la página Términos de licencia, haga clic en **Acepto**y, después, haga clic en **Siguiente**.
5.	En la página Seleccionar instalación de carpeta, haga clic en **Siguiente**. La instalación puede tardar varios minutos en completarse.
6.	En la página final, desactive **Iniciar el Asistente de configuración ahora**y haga clic en **Finalizar**.
7.	Desde la pantalla de inicio, haga clic en **user1**y, después, haga clic en **Cerrar sesión**.

A continuación, habilite la sincronización de directorios de la versión de prueba de Office 365 FastTrack.

1.	En CLIENT1, en la página **Centro de administración de Office 365**, en el panel izquierdo, haga clic en **Usuarios**y, a continuación, haga clic en **Usuarios activos**.
2.	Para la **Sincronización de Active Directory**, haga clic en **Configurar**.
3.	En la página de configuración y administración de la sincronización de Active Directory, en el paso 3, haga clic en **Activar**.
4.	Cuando se le pregunte **¿Desea activar la sincronización de Active Directory?**, haga clic en **Activar**. Después de hacer esto, aparece **se ha activado la sincronización de Active Directory** en el paso 3.
5.	Deje la página **Configuración y administración de la sincronización de Active Directory** abierta en CLIENT1.

A continuación, inicie sesión en DC1 con la cuenta CORP\User1 y abra un símbolo del sistema de Windows PowerShell con nivel de administrador. Ejecute estos comandos uno por uno para crear una nueva unidad organizativa denominada contoso_users y agregue dos cuentas de usuario nuevas para Marci Kaufman y Lynda Meyer.

	New-ADOrganizationalUnit -Name contoso_users -Path "DC=corp,DC=contoso,DC=com"
	New-ADUser -SamAccountName marcik -AccountPassword (Read-Host "Set user password" -AsSecureString) -name "Marci Kaufman" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false -Path "OU=contoso_users,DC=corp,DC=contoso,DC=com"
	New-ADUser -SamAccountName lyndam -AccountPassword (Read-Host "Set user password" -AsSecureString) -name "Lynda Meyer" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false -Path "OU=contoso_users,DC=corp,DC=contoso,DC=com"

Cuando ejecute cada comando de Windows PowerShell, se le solicitará la nueva contraseña del usuario. Anote estas contraseñas y guárdelas en una ubicación segura. Las necesitará más adelante.

A continuación, configure la sincronización de directorios en DS1.

1.	Inicie una sesión con la cuenta de CORP\User1 DS1.
2.	En la pantalla **Inicio**, escriba **Directory Sync**.
3.	Haga clic con el botón secundario en **Configuración de la sincronización de directorios**y, a continuación, haga clic en **Ejecutar como administrador**. Así se inicia al Asistente de configuración.
4.	En la página de bienvenida, haga clic en **Siguiente**.
5.	En la página de credenciales de Active Directory de Microsoft Azure, escriba la dirección de correo electrónico y la contraseña de la cuenta inicial que creó al configurar la versión de prueba de Office 365 FastTrack en la fase 2. Haga clic en Next. 
6.	En la página Credenciales de Active Directory, escriba **CORP\User1** en **Nombre de usuario** y la contraseña de la cuenta User1 en **Contraseña**. Haga clic en **Next**.
7.	En la página Implementación híbrida, seleccione **Habilitar implementación híbrida**y haga clic en **Siguiente**.
8.	En la página de sincronización de contraseñas, seleccione **Habilitar sincronización de contraseñas**y, a continuación, haga clic en **Siguiente**.
9.	Se muestra la página Configuración. Cuando se complete la configuración, haga clic en **Siguiente**.
10.	En la página final, haga clic en **Finalizar**. Cuando se le solicite, haga clic en **Aceptar**.

A continuación, compruebe que las cuentas de usuario en el dominio CORP están sincronizadas con Office 365. Tenga en cuenta que pueden pasar unas horas antes de que se produzca la sincronización.

En CLIENT1, en la página **Establecimiento y administración de la sincronización de Active Directory**, haga clic en el vínculo **usuarios** en el paso 6 de esta página. Si la sincronización de directorios se ha realizado correctamente, debería ver algo parecido a esto.

![](./media/virtual-networks-set-up-DirSync-hybrid-cloud-for-testing/CreateDirSyncHybridCloud_4.png)

La columna **Estado** indica que la cuenta se ha obtenido mediante la sincronización con un dominio de Active Directory.

A continuación, se muestra la sincronización de contraseñas de Office 365 con la cuenta de Active Directory de Lynda Myer.  

1.	En CLIENT1, en la página **Usuarios activos**, seleccione la cuenta de **Lynda Meyer**.
2.	En las propiedades de la cuenta de Lynda Meyer, en **Licencia asignada**, haga clic en **Editar**.
3.	En la pestaña **Asignar licencias** , seleccione una ubicación en **Establecer la ubicación del usuario** (por ejemplo, Estados Unidos).
4.	Seleccione **Microsoft Office 365 Plan E3**y haga clic en **Guardar**.
5.	Cierre Internet Explorer.
6.	Ejecute Internet Explorer y vaya a **http://portal.microsoftonline.com**. 
7.	Inicie sesión con las credenciales de Office 365 de Lynda Meyer. Su nombre de usuario será lyndam@<*Su nombre ficticio*>.onmicrosoft.com. La contraseña es la de la cuenta de usuario de Active Directory de Lynda Meyer.
8.	Tras el inicio de sesión correcto, verá la página principal del portal de Office 365 con **Marquemos hoy la diferencia**.

Esta es su configuración actual.

![](./media/virtual-networks-set-up-DirSync-hybrid-cloud-for-testing/CreateDirSyncHybridCloud_3.png)
 
Este entorno ya está preparado para realizar pruebas de aplicaciones de Office 365 que se basen en la funcionalidad de DirSync de Office 365 o para probar la funcionalidad de DirSync y el rendimiento desde DS1.

##Recursos adicionales

[Implementación de la sincronización de directorios de Office 365 (DirSync) en Microsoft Azure](http://technet.microsoft.com/library/dn635310.aspx)

[Soluciones que usan los servidores de Office y la nube](http://technet.microsoft.com/library/dn262744.aspx)

[Configuración de un entorno de nube híbrida para pruebas](virtual-networks-setup-hybrid-cloud-environment-testing.md)

[Configurar una granja de servidores de intranet de SharePoint en una nube híbrida para pruebas](virtual-networks-setup-sharepoint-hybrid-cloud-testing.md)

[Configuración de una aplicación de LOB basada en web en una nube híbrida para pruebas](virtual-networks-setup-lobapp-hybrid-cloud-testing.md)

[Configuración de un entorno de nube híbrida simulado para pruebas](virtual-networks-setup-simulated-hybrid-cloud-environment-testing.md)

<!--HONumber=47-->
