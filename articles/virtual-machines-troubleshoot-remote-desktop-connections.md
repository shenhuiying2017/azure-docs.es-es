<properties 
	pageTitle="Solucionar problemas de conexiones de Escritorio remoto a una máquina virtual de Azure basada en Windows " 
	description="Obtenga información sobre cómo restaurar la conectividad de Escritorio remoto (RDP) a la máquina virtual de Azure realizando el diagnóstico y siguiendo los pasos para identificar la causa del problema."
	services="virtual-machines" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/27/2015" 
	ms.author="josephd"/>

# Solucionar problemas de conexiones de Escritorio remoto a una máquina virtual de Azure basada en Windows 

Este tema describe un enfoque metódico para la corrección y la determinación de las causas principales de los problemas de las conexiones de Escritorio remoto a máquinas virtuales de Azure basadas en Windows.

## Paso 1: Ejecutar el paquete de diagnóstico de Azure IaaS

Para solucionar muchos de los problemas comunes relacionados con la creación de conexiones de Escritorio remoto, Microsoft creó un [Paquete de diagnóstico de Azure IaaS \(Windows\)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864).

1.	Haga clic en **Paquete de diagnóstico de Microsoft Azure IaaS \(Windows\)** que aparece en esta página para crear una sesión de diagnóstico nueva.
2.	En la página **¿Cuáles de los siguientes problemas experimenta con su máquina virtual de Azure?**, seleccione el problema **Conectividad de RDP a una máquina virtual de Azure \(se requiere reinicio\)**. 

Para obtener más información, consulte el [artículo de Knowledge Base Paquete de diagnóstico de Microsoft Azure IaaS \(Windows\)](http://support.microsoft.com/kb/2976864).

Si tras ejecutar el paquete de diagnóstico de Azure IaaS no se corrigió el problema, o si no pudo ejecutar el paquete de diagnóstico, es probable que necesite la solución de problemas más detallada que se describe en los pasos siguientes.

> [AZURE.NOTE]El paquete de diagnóstico de Azure IaaS \(Windows\) se debe ejecutar en un equipo con Windows 8, Windows 8.1, Windows Server 2012 o Windows Server 2012 R2.

## Paso 2: Determinar el mensaje de error en el cliente de Escritorio remoto

Haga uso de estas secciones en función del mensaje de error que obtenga.

### Ventana de mensajes de conexión a Escritorio remoto: La sesión remota se desconectó porque no hay servidores de licencias de Escritorio remoto disponibles para proporcionar una licencia.

Causa: el período de gracia de licencias de 120 días para el rol de servidor de Escritorio remoto expiró y necesita instalar licencias.
 
Como solución temporal, guarde una copia local del archivo RDP desde el Portal de administración de Azure y, a continuación, ejecute este comando en un símbolo del sistema de Windows PowerShell para conectarse.

	mstsc <File name>.RDP /admin

De este modo, se deshabilitará la obtención de licencias solo de esa conexión.

Si no necesita más de dos conexiones simultáneas de Escritorio remoto a la máquina virtual, puede usar el administrador del servidor para quitar el rol de servidor de Escritorio remoto.

Vea también entrada de blog [Se produce el error en la máquina virtual de Azure «No hay servidores de licencias de Escritorio remoto disponibles»](http://blogs.msdn.com/b/wats/archive/2014/01/21/rdp-to-azure-vm-fails-with-quot-no-remote-desktop-license-servers-available-quot.aspx).

### Ventana de mensajes de conexión a Escritorio remoto: El Escritorio remoto no encuentra el «nombre» del equipo.

Causa: el cliente de Escritorio remoto del equipo no puede resolver el nombre del equipo en la configuración del archivo RDP.

A continuación le facilitamos un ejemplo de un archivo RDP generado por el Portal de administración de Azure:

	full address:s:tailspin-azdatatier.cloudapp.net:55919
	prompt for credentials:i:1

La parte de la dirección consiste en el nombre de dominio completo del servicio en la nube que contiene la máquina virtual \(en este ejemplo, tailspin-azdatatier.cloudapp.net\) y el puerto TCP externo del extremo para el tráfico de Escritorio remoto \(55919\).

Posibles soluciones a este problema:

- Si se encuentra en la intranet de una organización, asegúrese de que el equipo tiene acceso al servidor proxy y puede enviarle tráfico HTTPS.
- Si está usando un archivo RDP almacenado localmente, intente usar el archivo generado por el Portal de administración de Azure para asegurarse de que tiene el nombre correcto para el servicio en la nube y el puerto de extremo de la máquina virtual.

### Ventana de mensajes de Seguridad de Windows: Las credenciales no funcionaron.

Causa: la máquina virtual a la que se está conectando no puede validar el nombre de cuenta y la contraseña que envió.

Un equipo basado en Windows puede validar las credenciales de una cuenta local o de una cuenta basada en dominio.

- Para las cuentas locales, use la <computer name>sintaxis \<nombre de cuenta\> \(ejemplo: SQL1\\Admin4798\). 
- Para las cuentas de dominio, use la <domain name>sintaxis \<nombre de cuenta\> \(ejemplo: CONTOSO\\johndoe\).

Para los equipos que promueve a controladores de dominio en un nuevo bosque de AD, la cuenta de administrador local a la que está conectado al realizar la promoción se convierte en una cuenta equivalente con la misma contraseña en el nuevo bosque y dominio. La cuenta de administrador local anterior se elimina. Por ejemplo, si inició sesión con la cuenta de administrador local DC1\\DCAdmin y promovió la máquina virtual como controlador de dominio en un bosque nuevo para el dominio corp.contoso.com, la cuenta local DC1\\DCAdmin se elimina y se crea una nueva cuenta de dominio CORP\\DCAdmin con la misma contraseña.

Vuelva a comprobar el nombre de la cuenta para asegurarse de que es un nombre que la máquina virtual puede comprobar como una cuenta válida. Compruebe de nuevo la contraseña para asegurarse de que es correcta.

Si necesita cambiar la contraseña de la cuenta de administrador local, consulte [Cómo restablecer una contraseña o el servicio de Escritorio remoto para máquinas virtuales de Windows](virtual-machines-windows-reset-password.md).

### Ventana de mensajes de conexión a Escritorio remoto: Este equipo no se puede conectar al equipo remoto.

Causa: la cuenta que está usando para conectarse no tiene derechos de inicio de sesión de Escritorio remoto.

Cada equipo de Windows tiene un grupo local de usuarios de Escritorio remoto, que contiene las cuentas y los grupos que tienen el derecho de iniciar sesión con una conexión a Escritorio remoto. Los miembros del grupo de administradores locales también tienen acceso, aunque esas cuentas no se muestran como miembros del grupo local de usuarios de Escritorio remoto. Para los equipos unidos a un dominio, el grupo de administradores locales también contiene los administradores de dominio para el dominio.

Asegúrese de que la cuenta que está usando para iniciar la conexión tiene derechos de inicio de sesión de Escritorio remoto. Use una cuenta de administrador de dominio o de administrador local como una solución temporal para crear una conexión a Escritorio remoto y agregue la cuenta que desee al grupo local de usuarios de Escritorio remoto mediante el complemento Administración de equipos \(\*\*Herramientas del sistema \> Usuarios y grupos locales \> Grupos \> Usuarios de Escritorio remoto\*\*\).

### Ventana de mensajes de conexión a Escritorio remoto: El Escritorio remoto no se puede conectar al equipo remoto por una de estas razones...

Causa: el cliente de Escritorio remoto no puede ponerse en contacto con el servicio Servicios de Escritorio remoto en la máquina virtual. Este problema puede deberse a muchas causas.

Presentamos a continuación los componentes implicados.

![](./media/virtual-machines-troubleshoot-remote-desktop-connections/tshootrdp_0.png)
 
Antes de iniciar un proceso paso a paso de solución de problemas, es útil repasar mentalmente qué cambió desde que creó las conexiones a Escritorio remoto y tomar ese cambio como base para intentar corregir el problema. Por ejemplo:

- Si pudo crear conexiones a Escritorio remoto y cambió la dirección IP pública del servicio en la nube que contiene la máquina virtual \(también conocida como dirección IP virtual, [VIP]\), la caché del cliente DNS podría tener una entrada para el nombre DNS del servicio en la nube y la *dirección IP antigua*. Vacíe la caché del cliente DNS y vuelva a intentarlo. También puede intentar establecer la conexión usando la VIP nueva.
- Si pasó de usar el Portal de administración de Azure o el Portal de vista previa de Azure a usar una aplicación para administrar las conexiones a Escritorio remoto, asegúrese de que la configuración de la aplicación incluye el puerto TCP determinado al azar para el tráfico de Escritorio remoto. 

Las secciones siguientes indican los pasos para aislar y determinar las distintas causas de este problema y proporcionan soluciones y alternativas.

## Paso 3: Pasos previos antes de la solución de problemas detallada

Siga estos pasos:

- Comprobar el estado de la máquina virtual en el Portal de administración de Azure o el Portal de vista previa de Azure
- [Reiniciar la máquina virtual](https://msdn.microsoft.com/library/azure/dn763934.aspx)
- [Cambiar el tamaño de la máquina virtual](https://msdn.microsoft.com/library/dn168976.aspx)

Después de realizar estos pasos, vuelva a intentar la conexión a Escritorio remoto.

## Paso 4: Solución de problemas detallada 

La incapacidad del cliente de Escritorio remoto para ponerse en contacto con el servicio Servicios de Escritorio remoto en la máquina virtual de Azure puede deberse a los siguientes problemas o errores de configuración:

- Equipo cliente de Escritorio remoto
- Dispositivo perimetral de intranet de la organización
- Extremo de servicio en la nube y lista de control de acceso \(ACL\)
- Grupos de seguridad de red
- Máquina virtual de Azure basada en Windows

### Causa 1: equipo cliente de Escritorio remoto

Para descartar el equipo como causa de los problemas o errores de configuración, compruebe que el equipo puede establecer conexiones a Escritorio remoto con otro equipo local basado en Windows.

![](./media/virtual-machines-troubleshoot-remote-desktop-connections/tshootrdp_1.png)
 
Si no es posible, compruebe si el equipo tiene lo siguiente:

- Una configuración del firewall local que bloquea el tráfico de Escritorio remoto
- Software de proxy de cliente instalado localmente que impide las conexiones a Escritorio remoto
- Software de supervisión de red instalado localmente que impide las conexiones a Escritorio remoto
- Otro tipo de software de seguridad, por ejemplo para supervisar el tráfico o para permitir o impedir ciertos tipos de tráfico, que imposibilita las conexiones a Escritorio remoto

En todos estos casos, intente deshabilitar temporalmente el software y pruebe a establecer una conexión a Escritorio remoto con un equipo local para determinar la causa principal. A continuación, trabaje con el administrador de red para corregir la configuración del software para permitir conexiones a Escritorio remoto.

### Causa 2: dispositivo perimetral de intranet de la organización

Para descartar el dispositivo perimetral de intranet de la organización como causa de los problemas o errores de configuración, compruebe que un equipo conectado directamente a Internet puede establecer conexiones a Escritorio remoto con la máquina virtual de Azure.

![](./media/virtual-machines-troubleshoot-remote-desktop-connections/tshootrdp_2.png)
 
Si no tiene un equipo conectado directamente a Internet, puede crear fácilmente una nueva máquina virtual de Azure en su propio servicio en la nube y usarla. Para obtener más información, vea [Crear una máquina virtual que ejecuta Windows en Azure](virtual-machines-windows-tutorial.md). Cuando acabe de realizar las pruebas, elimine la máquina virtual y el servicio en la nube.

Si puede crear una conexión a Escritorio remoto con un equipo conectado directamente a Internet, compruebe si el dispositivo perimetral de intranet de la organización tiene lo siguiente:

- Un firewall interno que bloquea las conexiones HTTPS a Internet
- Un servidor proxy que impide las conexiones a Escritorio remoto
- Software de detección de intrusiones o supervisión de red en ejecución en los dispositivos de la red perimetral que impide las conexiones a Escritorio remoto

Trabaje con el administrador de red para corregir la configuración del dispositivo perimetral de intranet de la organización para permitir conexiones a Escritorio remoto a Internet basadas en HTTPS.

### Causa 3: extremo de servicio en la nube y ACL

Para descartar el extremo de servicio en la nube y la ACL como causa de los problemas o errores de configuración, compruebe que otra máquina virtual de Azure que se encuentra en el mismo servicio en la nube puede establecer conexiones a Escritorio remoto con su máquina virtual de Azure.

![](./media/virtual-machines-troubleshoot-remote-desktop-connections/tshootrdp_3.png)
 
Si no tiene otra máquina virtual en el mismo servicio en la nube, puede crear una fácilmente. Para obtener más información, vea [Crear una máquina virtual que ejecuta Windows en Azure](virtual-machines-windows-tutorial.md). Cuando acabe de realizar las pruebas, elimine la máquina virtual que creó.

Si se puede establecer una conexión a Escritorio remoto con una máquina virtual en el mismo servicio en la nube, compruebe lo siguiente:

- La configuración del extremo para el tráfico de Escritorio remoto en la máquina virtual de destino. El puerto TCP privado del extremo debe coincidir con el puerto TCP en el que escucha el servicio Servicios de Escritorio remoto en la máquina virtual, que de forma predeterminada es 3389. 
- La ACL del extremo para el tráfico de Escritorio remoto en la máquina virtual de destino. Las ACL permiten especificar el tráfico entrante de Internet que se permite o se deniega en función de la dirección IP de origen. Las ACL mal configuradas pueden impedir el tráfico entrante de Escritorio remoto al extremo. Examine las ACL para asegurarse de que está permitido el tráfico entrante desde las direcciones IP públicas del proxy o de otro servidor perimetral. Para obtener más información, consulte [Acerca de las listas de control de acceso \(ACL\) de red](https://msdn.microsoft.com/library/azure/dn376541.aspx).

Para descartar el extremo como causa del problema, quite el extremo actual y cree un nuevo extremo. Para ello, elija un puerto aleatorio en el intervalo que va entre 49152 y 65535 para el número de puerto externo. Para obtener más información, vea [ Configuración de extremos en una máquina virtual en Azure](virtual-machines-set-up-endpoints.md).

### Causa 4: grupos de seguridad de red

Los grupos de seguridad de red permiten un control pormenorizado del tráfico entrante y saliente permitido. Puede crear reglas que abarquen subredes y servicios en la nube en una red virtual de Azure. Examine las reglas del grupo de seguridad de red para asegurarse de que se permite el tráfico de Escritorio remoto desde Internet.

Para obtener más información, consulte [Información sobre los grupos de seguridad de red](https://msdn.microsoft.com/library/azure/dn848316.aspx).

### Causa 5: máquina virtual de Azure basada en Windows

Por último, los problemas pueden residir en la propia máquina virtual de Azure.

![](./media/virtual-machines-troubleshoot-remote-desktop-connections/tshootrdp_5.png)
 
En primer lugar, si no pudo ejecutar el [paquete de diagnóstico de Azure IaaS \(Windows\)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864) para solucionar el problema **Conectividad de RDP a una máquina virtual de Azure \(se requiere reinicio\)**, siga las instrucciones indicadas en [Cómo restablecer una contraseña o el servicio de Escritorio remoto para máquinas virtuales de Windows](virtual-machines-windows-reset-password.md) para restablecer el servicio Servicios de Escritorio remoto en la máquina virtual. De este modo:

- Se habilitará la regla predeterminada «Escritorio remoto» del firewall de Windows \(puerto TCP 3389\).
- Se habilitarán las conexiones a Escritorio remoto estableciendo en 0 el valor HKLM\\System\\CurrentControlSet\\Control\\Terminal Server\\fDenyTSConnections del Registro.

Pruebe de nuevo la conexión desde su equipo. Si no funciona correctamente, pueden darse algunos de estos problemas:

- El servicio Servicios de Escritorio remoto no se está ejecutando en la máquina virtual de destino.
- El servicio Servicios de Escritorio remoto no está escuchando en el puerto TCP 3389.
- El firewall de Windows u otro firewall local tiene una regla de salida que impide el tráfico de Escritorio remoto.
- El software de detección de intrusiones o supervisión de red que se ejecuta en la máquina virtual de Azure impide las conexiones a Escritorio remoto.

Para corregir estos posibles problemas, puede usar una sesión remota de PowerShell en la máquina virtual de Azure. En primer lugar, debe instalar un certificado para el servicio en la nube de hospedaje de la máquina virtual. Vaya a [Configurar el acceso remoto seguro de PowerShell a máquinas virtuales de Azure](http://gallery.technet.microsoft.com/scriptcenter/Configures-Secure-Remote-b137f2fe) y descargue el archivo de script **InstallWinRMCertAzureVM.ps1** en una carpeta del equipo local.

A continuación, instale Azure PowerShell si todavía no lo ha hecho. Consulte [Instalación y configuración de Azure PowerShell](install-configure-powershell.md).

A continuación, abra un símbolo del sistema de Azure PowerShell y, a continuación, cambie la carpeta actual a la ubicación del archivo de script **InstallWinRMCertAzureVM.ps1**. Para ejecutar un script de PowerShell, debe establecer la directiva de ejecución correcta. Ejecute el comando **Get-ExecutionPolicy** para determinar el nivel de directiva actual. Para obtener información sobre cómo establecer el nivel adecuado, vea [Set-ExecutionPolicy](https://technet.microsoft.com/library/hh849812.aspx).

A continuación, rellene su nombre de suscripción a Azure, el nombre del servicio en la nube y el nombre de la máquina virtual \(quitando los caracteres < and >\) y ejecute estos comandos.

	$subscr="<Name of your Azure subscription>"
	$serviceName="<Name of the cloud service that contains the target virtual machine>"
	$vmName="<Name of the target virtual machine>"
	.\InstallWinRMCertAzureVM.ps1 -SubscriptionName $subscr -ServiceName $serviceName -Name $vmName

Puede obtener el nombre de suscripción correcto en la propiedad SubscriptionName de la pantalla del comando **Get-AzureSubscription**. Puede obtener el nombre del servicio en la nube de la máquina virtual en la columna ServiceName en la pantalla del comando **Get-AzureVM**.

Para demostrar que tiene este certificado nuevo, abra un complemento Certificados centrado en el usuario actual y vaya a la carpeta **Trusted Root Certification Authorities\\Certificates**. Debería ver un certificado con el nombre DNS del servicio en la nube en la columna Emitido para \(ejemplo: cloudservice4testing.cloudapp.net\).

A continuación, inicie una sesión remota de PowerShell con estos comandos.

	$uri = Get-AzureWinRMUri -ServiceName $serviceName -Name $vmName
	$creds = Get-Credential
	Enter-PSSession -ConnectionUri $uri -Credential $creds

Después de escribir las credenciales de administrador válidas, debería ver algo parecido a lo siguiente como símbolo del sistema de PowerShell de Azure:

	[cloudservice4testing.cloudapp.net]: PS C:\Users\User1\Documents>

La primera parte del símbolo del sistema indica que ahora está emitiendo comandos de PowerShell para el servicio en la nube que contiene la máquina virtual de destino. El nombre del servicio en la nube será algo distinto de «cloudservice4testing.cloudapp.net».

Ahora puede emitir comandos de PowerShell para investigar los problemas adicionales que se citaron anteriormente y corregir la configuración.

### Para corregir manualmente el puerto TCP de escucha del servicio Servicios de Escritorio remoto 

Si no pudo ejecutar el [paquete de diagnóstico de Azure IaaS \(Windows\)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864) para solucionar el problema **Conectividad de RDP a una máquina virtual de Azure \(se requiere reinicio\)**, en el símbolo del sistema de la sesión remota de PowerShell, ejecute este comando.

	Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"

La propiedad PortNumber muestra el número de puerto actual. Si es necesario, vuelva a poner el número de puerto del Escritorio remoto en su valor predeterminado \(3389\) con este comando.

	Set-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber" -Value 3389

Compruebe que el puerto cambió a 3389 con este comando.

	Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"

Cierre la sesión remota de PowerShell con este comando.

	Exit-PSSession

Compruebe que el extremo de Escritorio remoto para la máquina virtual de Azure también usa el puerto TCP 3398 como puerto interno. A continuación, reinicie la máquina virtual de Azure y vuelva a probar su conexión a Escritorio remoto.

## Paso 5: Enviar el problema a los foros de soporte técnico de Azure

Para recibir ayuda de expertos de Azure de todo el mundo, puede enviar su problema a los foros de Azure de MSDN o Stack Overflow. Para obtener más información, consulte [Foros de Microsoft Azure](http://azure.microsoft.com/support/forums/).

## Paso 6: Registrar un incidente de soporte técnico de Azure

Si ejecutó el [paquete de diagnóstico de Azure IaaS \(Windows\)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864) para solucionar el problema **Conectividad de RDP a una máquina virtual de Azure \(se requiere reinicio\)** o si siguió los pasos del 2 al 5 de este artículo y envió su problema a los foros de soporte técnico de Azure pero todavía no es capaz de crear una conexión a Escritorio remoto, puede volver a crear la máquina virtual.

Si no puede volver a crear la máquina virtual, es el momento de registrar un incidente de soporte técnico de Azure.

Para registrar un incidente, vaya al [sitio de soporte técnico de Azure](http://azure.microsoft.com/support/options/) y haga clic en **Obtener Ayuda**.

Para obtener información sobre el uso del soporte técnico de Azure, consulte las [Preguntas más frecuentes de soporte técnico de Microsoft Azure](http://azure.microsoft.com/support/faq/).

## Recursos adicionales

[Paquete de diagnóstico de Azure IaaS \(Windows\)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)

[Cómo restablecer una contraseña o el servicio de Escritorio remoto para máquinas virtuales de Windows](virtual-machines-windows-reset-password.md)

[Instalación y configuración de Azure PowerShell](install-configure-powershell.md)

[Documentación sobre las máquinas virtuales](http://azure.microsoft.com/documentation/services/virtual-machines/)

[Preguntas más frecuentes sobre las máquinas virtuales de Azure](http://msdn.microsoft.com/library/azure/dn683781.aspx)


<!--HONumber=54-->