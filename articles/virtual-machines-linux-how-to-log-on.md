<properties urlDisplayName="Log on to a VM" pageTitle="Inicio de sesión en una máquina virtual que ejecuta Linux en Azure" metaKeywords="Azure Linux vm, Linux SSH" description="Aprenda a iniciar sesión en una máquina virtual de Azure que ejecuta Linux utilizando un cliente Secure Shell (SSH)." metaCanonical="" services="virtual-machines" documentationCenter="" title="How to Log on to a Virtual Machine Running Linux" authors="kathydav" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="11/12/2014" ms.author="kathydav" />




#Inicio de sesión en una máquina virtual con Linux #

Para una máquina virtual con el sistema operativo Linux, utilice un cliente Secure Shell (SSH) para iniciar sesión.

Debe instalar un cliente de SSH en el equipo que desea usar para iniciar sesión en la máquina virtual. Existen muchos programas cliente de SSH donde elegir. Las siguientes son algunas posibles opciones:

- En un equipo que ejecute un sistema operativo Windows, quizás desee utilizar un cliente de SSH, como PuTTY. Para obtener más información, consulte la [página de descarga de PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).
- En un equipo que ejecute un sistema operativo Linux, quizás desee utilizar un cliente de SSH, como OpenSSH. Para obtener más información, consulte [OpenSSH](http://www.openssh.org/).

>[WACOM.NOTE] Para obtener consejos acerca de los requisitos y la solución de problemas, consulte [Conectar a una máquina virtual de Azure con RDP o SSH](http://go.microsoft.com/fwlink/p/?LinkId=398294). 

Este procedimiento le muestra cómo usar el programa PuTTY para tener acceso a la máquina virtual.

1. Busque el **nombre de host** y la **información de puerto** en el [Portal de administración](http://manage.windowsazure.com). Puede encontrar la información que necesita en el panel de la máquina virtual. Haga clic en el nombre de la máquina virtual y busque los **Detalles de SSH** en la sección **Vista rápida** del panel.

	![Obtain SSH details](./media/virtual-machines-linux-how-to-log-on/sshdetails.png)

2. Abra el programa PuTTY.

3. Escriba el nombre de host y la información de puerto que recopiló del panel y, a continuación, haga clic en **Abrir**.

	![Open PuTTY](./media/virtual-machines-linux-how-to-log-on/putty.png)

4. Inicie sesión en la máquina virtual usando la cuenta que especificó cuando se creó la máquina. De manera predeterminada el nombre de usuario es azureuser.

	![Log on to the virtual machine](./media/virtual-machines-linux-how-to-log-on/sshlogin.png)

>[WACOM.NOTE] La extensión VMAccess le puede ayudar a restablecer la contraseña o clave de SSH si la ha olvidado. Si ha olvidado el nombre de usuario, puede utilizar la extensión para crear otro nuevo con autoridad sudo. Para obtener instrucciones, consulte [Restablecimiento de una contraseña o de SSH para máquinas virtuales de Linux]. 
	
Ahora puede trabajar con la máquina virtual igual que hace con cualquier otro servidor.

<!-- LINKS -->
[Restablecimiento de una contraseña o de SSH para máquinas virtuales de Linux]: http://go.microsoft.com/fwlink/p/?LinkId=512138

<!--HONumber=35.1-->
