<properties
	pageTitle="Solución de problemas con la conexión del Escritorio remoto a una máquina virtual de Azure | Microsoft Azure"
	description="Solución de problemas con la conexión del Escritorio remoto de una máquina virtual de Windows. Obtenga pasos rápidos de mitigación, ayuda por cada mensaje de error y soluciones detalladas de problemas de red."
	keywords="Error de escritorio remoto, error de conexión del escritorio remoto, no se puede conectar a la máquina virtual, solución de problemas con el escritorio remoto"
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
	ms.date="10/27/2015"
	ms.author="dkshir"/>

# Solución de problemas de conexiones del Escritorio remoto a una máquina virtual de Azure con Windows

Pueden crearse varios errores en el Escritorio remoto (RDP) si intenta conectarse a la Máquina virtual de Azure basada en Windows. El problema puede estar en el software RDP de la máquina virtual, el equipo host subyacente, la conexión de red o en el lado de cliente desde donde conectarse. Este artículo le ayudará a determinar las causas y corregirlas.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

En este artículo solo se aplica a máquinas virtuales de Azure con Windows. En cuanto a las máquinas virtuales que ejecutan Linux, consulte [Solucionar problemas con la conexión SSH a una Máquina virtual de Azure](virtual-machines-troubleshoot-ssh-connections.md).

Si necesita más ayuda en cualquier momento con este artículo, puede ponerse en contacto con los expertos de Azure en [los foros de MSDN Azure o de desbordamiento de pila](http://azure.microsoft.com/support/forums/). Como alternativa, también puede registrar un incidente de soporte técnico de Azure. Vaya al [sitio de soporte técnico de Azure](http://azure.microsoft.com/support/options/) y haga clic en **Obtener soporte técnico**.

En la primera sección "Pasos básicos" se enumeran los pasos para tratar problemas de conexión comunes, en la segunda sección se ofrecen pasos de resolución por mensaje de error específico y en la última sección se ayuda a realizar la solución de problemas detallada de cada componente de red.

## Pasos para corregir errores comunes del Escritorio remoto en el modelo de implementación clásica

Estos pasos básicos pueden ayudarlo a solucionar la mayoría de los errores comunes en la conexión a Escritorio remoto que se producen en las máquinas virtuales creadas con el modelo de implementación clásica. Después de realizar cada paso, intente volver a conectarse a la máquina virtual.

- Restablezca el servicio de escritorio remoto desde el [Portal de vista previa de Azure](https://portal.azure.com) para solucionar problemas de inicio con el servidor RDP.<br> Haga clic en Examinar todo > Máquinas virtuales (clásico) > su máquina virtual Windows > **Restablecer acceso remoto**.

    ![Captura de pantalla que muestra el proceso de restablecimiento de la configuración de RDP](./media/virtual-machines-troubleshoot-remote-desktop-connections/Portal-RDP-Reset-Windows.png)

- Reinicie la máquina Virtual para tratar otros problemas de inicio.<br>
	Haga clic en Examinar todo > Máquinas virtuales (clásico) > su máquina virtual Windows > **Reiniciar**.

- Cambie el tamaño de la máquina virtual para corregir cualquier problema de host.<br>
	Haga clic en Examinar todo > Máquinas virtuales (clásico) > su máquina virtual Windows > Configuración > **Tamaño**. Para obtener información detallada, vea [Cambiar el tamaño de la máquina virtual](https://msdn.microsoft.com/library/dn168976.aspx).

- Revise la captura de pantalla o el registro de la consola de la máquina virtual para corregir problemas de arranque. Haga clic en Examinar todo > Máquinas virtuales (clásico) > su máquina virtual Windows > **Diagnóstico de arranque**.

- Compruebe el estado de los recursos de máquina virtual para ver si hay algún problema en la plataforma. Haga clic en Examinar todo > Máquinas virtuales (clásico) > su máquina virtual Windows > **Comprobar estado**

## Pasos para corregir errores comunes del Escritorio remoto en el modelo de implementación del Administrador de recursos

Estos pasos básicos pueden ayudarlo a solucionar la mayoría de los errores comunes en la conexión a Escritorio remoto que se producen en las máquinas virtuales creadas con el modelo de implementación del Administrador de recursos. Después de realizar cada paso, intente volver a conectarse a la máquina virtual.

- Restablezca el acceso remoto con PowerShell<br> a. Si todavía no lo tiene, [instale Azure PowerShell y conéctese a su suscripción de Azure](../powershell-install-configure.md) mediante el método de Azure AD.

	b. Cambie al modo Administrador de recursos.

	```
	Switch-AzureMode -Name AzureResourceManager
	```
	c. Ejecute el comando Set-AzureVMAccessExtension para restablecer la conexión RDP, tal como se muestra en el ejemplo siguiente.

	```
	Set-AzureVMExtension -ResourceGroupName "myRG" -VMName "myVM" -Name "myVMAccessExtension" -ExtensionType "VMAccessAgent" -Publisher "Microsoft.Compute" -typeHandlerVersion "2.0" -Location Westus
	```

- Reinicie la máquina virtual para tratar otros problemas de inicio.<br> Haga clic en Examinar todo > Máquinas virtuales > su máquina virtual Windows > **Reiniciar**.

- Cambie el tamaño de la máquina virtual para corregir cualquier problema de host.<br> Haga clic en Examinar todo > Máquinas virtuales > su máquina virtual Windows > Configuración > **Tamaño**.

- Revise la captura de pantalla o el registro de la consola de la máquina virtual para corregir problemas de arranque. Haga clic en Examinar todo > Máquinas virtuales > su máquina virtual Windows > **Diagnóstico de arranque**.


## Solucionar problemas específicos con la conexión del Escritorio remoto

Éstos son los errores más comunes que pueden surgir al intentar usar Escritorio remoto en una máquina virtual de Azure:

1. [Error de conexión de Escritorio remoto: la sesión remota se desconectó porque no hay servidores de licencias de Escritorio remoto disponibles para proporcionar una licencia](#rdplicense).

2. [Error de conexión de Escritorio remoto: el Escritorio remoto no encuentra el "nombre" del equipo](#rdpname).

3. [Error de conexión de Escritorio remoto: se ha producido un error de autenticación. No se puede conectar con la autoridad de seguridad local](#rdpauth).

4. [Error de Seguridad de Windows: las credenciales no funcionaron](#wincred).

5. [Error de conexión de Escritorio remoto: este equipo no se puede conectar al equipo remoto](#rdpconnect).

<a id="rdplicense"></a>
### Error de conexión de Escritorio remoto: la sesión remota se desconectó porque no hay servidores de licencias de Escritorio remoto disponibles para proporcionar una licencia.

Causa: el período de gracia de licencias de 120 días para el rol de servidor de Escritorio remoto expiró y necesita instalar licencias.

Como solución alternativa, guarde una copia local del archivo RDP desde el portal y ejecute este comando en un símbolo del sistema de Windows PowerShell para conectarse.

		mstsc <File name>.RDP /admin

De este modo, se deshabilitará la obtención de licencias solo de esa conexión.

Si no necesita más de dos conexiones simultáneas de Escritorio remoto a la máquina virtual, puede usar el administrador del servidor para quitar el rol de servidor de Escritorio remoto.

Vea también entrada de blog [Se produce el error en la máquina virtual de Azure «No hay servidores de licencias de Escritorio remoto disponibles»](http://blogs.msdn.com/b/wats/archive/2014/01/21/rdp-to-azure-vm-fails-with-quot-no-remote-desktop-license-servers-available-quot.aspx).

<a id="rdpname"></a>
### Error de conexión de Escritorio remoto: el Escritorio remoto no encuentra el "nombre" del equipo.

Causa: el cliente de Escritorio remoto del equipo no pudo resolver el nombre del equipo en la configuración del archivo RDP.

Posibles soluciones:

- Si se encuentra en la intranet de una organización, asegúrese de que el equipo tiene acceso al servidor proxy y puede enviarle tráfico HTTPS.
- Si usa un archivo RDP almacenado localmente, intente usar uno generado por el portal. Esto garantizará que tiene el nombre DNS correcto de la máquina virtual o el servicio en la nube y el puerto de extremo de la máquina virtual. A continuación le facilitamos un archivo RDP de ejemplo generado por el portal:

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

## Soluciones detalladas a problemas con el Escritorio remoto

Si no se produjeron estos errores y aún no se pudo conectar a la máquina virtual a través de Escritorio remoto, lea [este artículo](virtual-machines-rdp-detailed-troubleshoot.md) para descubrir otras causas.


## Recursos adicionales

[Paquete de diagnóstico de Azure IaaS (Windows)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)

[Cómo restablecer una contraseña o el servicio de Escritorio remoto para máquinas virtuales de Windows](virtual-machines-windows-reset-password.md)

[Instalación y configuración de Azure PowerShell](../install-configure-powershell.md)

[Solución de problemas de conexiones de Secure Shell (SSH) en una máquina virtual de Azure basada en Linux](virtual-machines-troubleshoot-ssh-connections.md)

[Solucionar problemas de acceso a una aplicación que se ejecuta en una máquina virtual de Azure](virtual-machines-troubleshoot-access-application.md)

<!---HONumber=AcomDC_1125_2015-->