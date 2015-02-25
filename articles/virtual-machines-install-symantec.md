<properties pageTitle="Instalación y configuración de Endpoint Protection en una máquina virtual de Azure" description="Describe la instalación y configuración de Symantec Endpoint Protection en una máquina virtual de Azure" services="virtual-machines" documentationCenter="" authors="KBDAzure" manager="timlt" editor=""/>

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-multiple" ms.devlang="na" ms.topic="article" ms.date="1/26/2015" ms.author="kathydav"/>

#Instalación y configuración de Endpoint Protection en una máquina virtual de Azure

En este artículo se muestra cómo instalar y configurar el cliente Symantec Endpoint Protection en una máquina virtual nueva o existente con Windows Server. Este es el cliente completo, que incluye servicios como protección contra virus y spyware, firewall y prevención de intrusiones. 

El cliente se instala como una extensión de seguridad usando el Agente de máquina virtual. En una nueva máquina virtual, instalará el agente junto con el cliente del extremo. En una máquina virtual existente sin el agente, primero necesitará descargar e instalar dicho agente. Este artículo trata ambas situaciones.

Si tiene una suscripción existente de Symantec para una solución local, puede usarla para proteger sus máquinas virtuales de Azure. Si todavía no es cliente, puede suscribirse para una prueba. Para obtener más información acerca de esta solución, consulte [Symantec Endpoint Protection en la plataforma de Microsoft Azure](http://go.microsoft.com/fwlink/p/?LinkId=403942). Esta página también proporciona vínculos a información de licencia e instrucciones alternativas para instalar el cliente si ya es un cliente de Symantec.

##Instalación de Symantec Endpoint Protection en una nueva máquina virtual

El [Portal de administración de Azure](http://manage.windowsazure.com) permite instalar el Agente de máquina virtual y la extensión de seguridad de Symantec cuando usa la opción **Desde la galería** para crear la máquina virtual. Este enfoque proporciona una forma sencilla de agregar protección desde Symantec si crea una sola máquina virtual. 

La opción **Desde la galería** abre un asistente que le ayuda configurar la máquina virtual. Utilice la última página del asistente para instalar el Agente de máquina virtual y la extensión de seguridad de Symantec. 

Para obtener instrucciones generales, consulte [Creación de una máquina virtual que ejecuta Windows Server](http://go.microsoft.com/fwlink/p/?LinkId=403943). Cuando llegue a la última página del asistente:

1.	En el agente de VM, la opción **Instalar agente de VM** ya debe estar activada.

2.	En Extensiones de seguridad, active la casilla **Symantec Endpoint Protection**.


	![Install the VM Agent and the Endpoint Protection Client](./media/virtual-machines-install-symantec/InstallVMAgentandSymantec.png)

3.	Haga clic en la marca de verificación en la parte inferior de la página para crear la máquina virtual.

## Instalación de Symantec Endpoint Protection en una máquina virtual existente

Antes de comenzar, necesitará lo siguiente:

- El módulo de Azure PowerShell, versión 0.8.2 o posterior. Para obtener instrucciones y un vínculo a la versión más reciente, consulte [Instalación y configuración de Azure PowerShell](http://go.microsoft.com/fwlink/p/?LinkId=320552).  

- El Agente de máquina virtual. Para obtener instrucciones y un vínculo a la descarga, consulte la entrada de blog [Agente de máquina virtual y extensiones - Parte 2](http://go.microsoft.com/fwlink/p/?LinkId=403947).

Para instalar la extensión de seguridad de Symantec en una máquina virtual existente:

1.	Obtenga el nombre de servicio de la nube y el nombre de la máquina virtual. Si no los conoce, utilice el comando **Get-AzureVM** para mostrar esa información para todas las máquinas virtuales en la suscripción actual. A continuación, reemplace todo el contenido dentro de las comillas, incluyendo los caracteres < y >, y ejecute estos comandos:

	<p>`$servicename = "<YourServiceName>"`
<p>`$name = "<YourVmName>"`
<p>`$vm = Get-AzureVM -ServiceName $servicename -Name $name`
<p>`Get-AzureVMAvailableExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection`

2.	Desde la pantalla del comando Get-AzureVMAvailableExtension, anote el número de versión de la propiedad Version y, a continuación, ejecute estos comandos:

	<p>`$ver=<version number from the Version property>`
<p>`Set-AzureVMExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection -Version $ver -VM $vm.VM`
<p>`Update-AzureVM -ServiceName $servicename -Name $name -VM $vm.VM`

Para comprobar que la extensión de seguridad de Symantec se ha instalado y está actualizada:

1.	Iniciar sesión en la nueva máquina virtual.
2.	En Windows Server 2008 R2, haga clic en **Inicio > Todos los programas > Symantec Endpoint Protection**. En Windows Server 2012, desde la pantalla de inicio, escriba **Symantec** y, a continuación, haga clic en **Symantec Endpoint Protection**.
3.	En la ventana de estado, aplique las actualizaciones si es necesario.

## Recursos adicionales
[Inicio de sesión en una máquina virtual con Windows Server]

[Administración de extensiones]

<!--Link references-->
[Inicio de sesión en una máquina virtual con Windows Server]: ../virtual-machines-log-on-windows-server/

[Administración de extensiones]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409





<!--HONumber=42-->
