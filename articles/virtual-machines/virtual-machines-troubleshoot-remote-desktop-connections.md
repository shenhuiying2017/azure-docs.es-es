<properties
	pageTitle="Solución de problemas de conexión a Escritorio remoto en una máquina virtual de Windows | Microsoft Azure"
	description="Solución de problemas de conexiones del Escritorio remoto o RDP a una máquina virtual de Azure con Windows"
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""
	tags="top-support-issue,azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/16/2015"
	ms.author="dkshir"/>

# Solución de problemas de conexiones del Escritorio remoto a una máquina virtual de Azure con Windows

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


Puede haber varias razones por las que el Escritorio remoto (RDP) no pueda conectarse una máquina virtual de Azure con Windows. Este artículo le ayudará a determinar las causas y corregirlas.

> [AZURE.NOTE]En este artículo solo se aplica a máquinas virtuales de Azure con Windows. Para solucionar los problemas de las conexiones a máquinas virtuales de Azure con Linux, consulte [este artículo](virtual-machines-troubleshoot-ssh-connections.md).

## Contacto con el servicio de atención al cliente de Azure

Si necesita más ayuda en cualquier momento con este artículo, puede ponerse en contacto con los expertos de Azure en [los foros de MSDN Azure o de desbordamiento de pila](http://azure.microsoft.com/support/forums/).

Como alternativa, también puede registrar un incidente de soporte técnico de Azure. Vaya al [sitio de soporte técnico de Azure](http://azure.microsoft.com/support/options/) y haga clic en **Obtener soporte técnico**. Para obtener información sobre el uso del soporte técnico de Azure, lea las [Preguntas más frecuentes de soporte técnico de Microsoft Azure](http://azure.microsoft.com/support/faq/).


## Pasos básicos

Estos pasos básicos pueden ayudar a resolver la mayoría de los errores de conexión de Escritorio remoto:

- Restablecer el servicio de Escritorio remoto desde el [Portal de Azure](https://portal.azure.com). Haga clic en **Examinar todo** > **Máquinas virtuales (clásico)** > su máquina virtual Windows > **Restablecer acceso remoto**.

![Restablecer acceso remoto](./media/virtual-machines-troubleshoot-remote-desktop-connections/Portal-RDP-Reset-Windows.png)

- [Reiniciar la máquina virtual](https://msdn.microsoft.com/library/azure/dn763934.aspx).

- [Cambiar el tamaño de la máquina virtual](https://msdn.microsoft.com/library/dn168976.aspx).


## Ejecución del paquete de diagnóstico de Azure IaaS en Windows

Si va a solucionar problemas en un equipo con Windows 8, Windows 8.1, Windows Server 2012 o Windows Server 2012 R2, puede probar a ejecutar el [paquete de diagnóstico de Azure IaaS (Windows)](http://support.microsoft.com/kb/2976864). Este paquete puede solucionar muchos de los problemas comunes del Escritorio remoto.

1.	Haga clic en **Paquete de diagnóstico de Microsoft Azure IaaS (Windows)** en la [página de diagnóstico de soporte técnico](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864). Haga clic en **Crear** para una nueva sesión de diagnóstico. Puede **compartir** esta sesión con otro equipo de destino o **descargarla** en un equipo local.
2.	**Ejecute** la sesión, **acepte** el contrato de licencia de Microsoft e **inicie** la herramienta de diagnóstico.
3.	Autentique la suscripción de Azure en la ventana emergente y siga las indicaciones.
4.	En la página **¿Cuáles de los siguientes problemas experimenta con su máquina virtual de Azure?**, seleccione el problema **Conectividad de RDP a una máquina virtual de Azure (se requiere reinicio)**.

Si el paquete de diagnóstico de Azure IaaS no se pudo ejecutar o no fue útil, pase a la siguiente sección para solucionar el problema en función del error que se obtiene del cliente de Escritorio remoto.


## Errores comunes de RDP

Éstos son los errores más comunes que pueden surgir al intentar usar Escritorio remoto en una máquina virtual de Azure:

1. [Error de conexión de Escritorio remoto: la sesión remota se desconectó porque no hay servidores de licencias de Escritorio remoto disponibles para proporcionar una licencia](#rdplicense).

2. [Error de conexión de Escritorio remoto: el Escritorio remoto no encuentra el "nombre" del equipo](#rdpname).

3. [Error de conexión de Escritorio remoto: se ha producido un error de autenticación. No se puede conectar con la autoridad de seguridad local](#rdpauth).

4. [Error de Seguridad de Windows: las credenciales no funcionaron](#wincred).

5. [Error de conexión de Escritorio remoto: este equipo no se puede conectar al equipo remoto](#rdpconnect).

<a id="rdplicense"></a>
### Error de conexión de Escritorio remoto: la sesión remota se desconectó porque no hay servidores de licencias de Escritorio remoto disponibles para proporcionar una licencia.

Causa: el período de gracia de licencias de 120 días para el rol de servidor de Escritorio remoto expiró y necesita instalar licencias.

Como solución alternativa, guarde una copia local del archivo RDP desde el Portal de Azure y ejecute este comando en un símbolo del sistema de Windows PowerShell para conectarse.

		mstsc <File name>.RDP /admin

De este modo, se deshabilitará la obtención de licencias solo de esa conexión.

Si no necesita más de dos conexiones simultáneas de Escritorio remoto a la máquina virtual, puede usar el administrador del servidor para quitar el rol de servidor de Escritorio remoto.

Vea también entrada de blog [Se produce el error en la máquina virtual de Azure «No hay servidores de licencias de Escritorio remoto disponibles»](http://blogs.msdn.com/b/wats/archive/2014/01/21/rdp-to-azure-vm-fails-with-quot-no-remote-desktop-license-servers-available-quot.aspx).

<a id="rdpname"></a>
### Error de conexión de Escritorio remoto: el Escritorio remoto no encuentra el "nombre" del equipo.

Causa: el cliente de Escritorio remoto del equipo no pudo resolver el nombre del equipo en la configuración del archivo RDP.

Posibles soluciones:

- Si se encuentra en la intranet de una organización, asegúrese de que el equipo tiene acceso al servidor proxy y puede enviarle tráfico HTTPS.
- Si usa un archivo RDP almacenado localmente, intente usar uno generado por el Portal de Azure. Esto garantizará que tiene el nombre DNS correcto de la máquina virtual o el servicio en la nube y el puerto de extremo de la máquina virtual. A continuación le facilitamos un archivo RDP de ejemplo generado por el Portal de Azure:

		full address:s:tailspin-azdatatier.cloudapp.net:55919
		prompt for credentials:i:1

La parte de la dirección de este archivo RDP tiene el nombre de dominio completo del servicio en la nube que contiene la VM (en este ejemplo, tailspin-azdatatier.cloudapp.net) y el puerto TCP externo del extremo para el tráfico de Escritorio remoto (55919).

<a id="rdpauth"></a>
### Error de conexión de Escritorio remoto: se ha producido un error de autenticación. No se puede conectar con la autoridad de seguridad Local.

Causa: el VM de destino no encuentra la autoridad de seguridad en la parte del nombre de usuario de las credenciales.

Si el nombre de usuario tiene la forma *autoridadDeSeguridad*\*nombreDeUsuario* (ejemplo: CORP\\User1), la parte *autoridadDeSeguridad* es el nombre del equipo de la máquina virtual (para la autoridad de seguridad local) o un nombre de dominio de Active Directory.

Posibles soluciones:

- Si su cuenta de usuario está en la propia VM, compruebe si el nombre de la máquina virtual está escrito correctamente.
- Si la cuenta está en un dominio de Active Directory, compruebe la ortografía del nombre de dominio.
- Si es una cuenta de dominio de Active Directory y el nombre de dominio está escrito correctamente, compruebe que hay algún controlador de dominio disponible en dicho dominio. Esto puede ser un problema común en una red virtual de Azure que contiene controladores de dominio en los que no se ha iniciado un equipo de controlador de dominio. Como solución alternativa, puede usar una cuenta de administrador local, en lugar de una cuenta de dominio.

<a id="wincred"></a>
### Error de Seguridad de Windows: Las credenciales no funcionaron.

Causa: la VM de destino no pudo validar el nombre de la cuenta y la contraseña.

Un equipo con Windows puede validar las credenciales de una cuenta local o de una cuenta de dominio.

- Para las cuentas locales, use la sintaxis *nombreDeEquipo*\*nombreDeUsuario* (ejemplo: SQL1\\Admin4798).
- Para las cuentas de dominio, use la sintaxis *nombreDeDominio*\*nombreDeUsuario* (ejemplo: CONTOSO\\johndoe).

Si promovió su máquina virtual a un controlador de dominio de un nuevo bosque de Active Directory, la cuenta de administrador local con la que inició sesión, también se convierte en una cuenta equivalente con la misma contraseña en el nuevo bosque y dominio. La cuenta de administrador local se elimina. Por ejemplo, si inició sesión con la cuenta de administrador local DC1\\DCAdmin y promovió la máquina virtual como controlador de dominio en un bosque nuevo para el dominio corp.contoso.com, la cuenta local DC1\\DCAdmin se elimina y se crea una nueva cuenta de dominio CORP\\DCAdmin con la misma contraseña.

Asegúrese de que el nombre de la cuenta es un nombre que la máquina virtual pueda comprobar como una cuenta válida y que la contraseña sea correcta.

Si necesita cambiar la contraseña de la cuenta de administrador local, consulte [Cómo restablecer una contraseña o el servicio de Escritorio remoto para máquinas virtuales de Windows](virtual-machines-windows-reset-password.md).

<a id="rdpconnect"></a>
### Error de conexión de Escritorio remoto: este equipo no se puede conectar al equipo remoto.

Causa: la cuenta usada para conectarse no tiene derechos de inicio de sesión de Escritorio remoto.

Todos los equipos de Windows tiene un grupo local Usuarios de Escritorio remoto, que contiene las cuentas y grupos que pueden iniciar sesión en él de forma remota. Los miembros del grupo Administradores local también tienen acceso, aunque esas cuentas no se enumeran en el grupo local Usuarios de Escritorio remoto. Para los equipos unidos a un dominio, el grupo de administradores locales también contiene los administradores de dominio para el dominio.

Asegúrese de que la cuenta que usa para conectarse tiene derechos de inicio de sesión de Escritorio remoto. Como solución alternativa, use una cuenta de dominio o de administrador local para conectarse a través de Escritorio remoto y, seguidamente, use el complemento Administración de equipos (**Herramientas del sistema > Usuarios y grupos locales > Grupos > Usuarios de Escritorio remoto**) para agregar la cuenta deseada al grupo local Usuarios de Escritorio remoto.


## Solución de problemas detallada

Si no se produjeron estos errores y aún no se pudo conectar a la máquina virtual a través de Escritorio remoto, lea [este artículo](virtual-machines-rdp-detailed-troubleshoot.md) para descubrir otras causas.


## Recursos adicionales

[Paquete de diagnóstico de Azure IaaS (Windows)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)

[Cómo restablecer una contraseña o el servicio de Escritorio remoto para máquinas virtuales de Windows](virtual-machines-windows-reset-password.md)

[Instalación y configuración de Azure PowerShell](../install-configure-powershell.md)

[Solución de problemas de conexiones de Secure Shell (SSH) en una máquina virtual de Azure basada en Linux](virtual-machines-troubleshoot-ssh-connections.md)

[Solucionar problemas de acceso a una aplicación que se ejecuta en una máquina virtual de Azure](virtual-machines-troubleshoot-access-application.md)

<!---HONumber=Oct15_HO3-->