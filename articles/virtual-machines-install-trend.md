<properties 
	pageTitle="Instalación y configuración de Trend Micro Deep Security como servicio en una máquina virtual de Azure" 
	description="Describe la instalación y configuración de Trend Micro Security en una máquina virtual de Azure" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-multiple" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/24/2014" 
	ms.author="kathydav"/>

#Instalación y configuración de Trend Micro Deep Security como servicio en una máquina virtual de Azure

<p> En este artículo se muestra cómo instalar y configurar Trend Micro Deep Security como servicio en una máquina virtual nueva o existente con Windows Server. La protección que Deep Security como servicio proporciona protección antimalware, firewall, sistema de prevención contra intrusiones y supervisión de integridad. 

<p>El cliente se instala como una extensión de seguridad usando el Agente de máquina virtual. En una nueva máquina virtual, instalará el agente de máquina virtual junto con el Agente de Deep Security. En una máquina virtual que no tenga el Agente de máquina virtual, primero necesitará descargarlo e instalarlo. Este artículo trata ambas situaciones.

<p> Si tiene una suscripción existente de Trend para una solución local, puede usarla para proteger sus máquinas virtuales de Azure. Si todavía no es cliente, puede suscribirse para una prueba. Para obtener más información acerca de esta solución, consulte la publicación del blog [Extensión del Agente de máquina virtual de Microsoft Azure para Deep Security](http://go.microsoft.com/fwlink/p/?LinkId=403945).

## Instalación del Agente de Deep Security en una nueva máquina virtual

El [Portal de administración de Azure](http://manage.windowsazure.com) permite instalar el Agente de máquina virtual y la extensión de seguridad de Trend cuando usa la opción **Desde la galería** para crear la máquina virtual. Este enfoque proporciona una forma sencilla de agregar protección desde Trend si crea una sola máquina virtual.

La opción **Desde la galería** abre un asistente que le ayuda configurar la máquina virtual. Utilice la última página del asistente para instalar el Agente de máquina virtual y la extensión de seguridad de Trend. Para obtener instrucciones generales, consulte [Creación de una máquina virtual que ejecuta Windows Server](http://go.microsoft.com/fwlink/p/?LinkId=403943). Cuando se encuentre en la última página del asistente, realice las acciones siguientes:

1.	En Agente de máquina virtual, active la casilla **Instalar Agente de máquina virtual**.

2.	En Extensiones de seguridad, active **Agente de Trend Micro Deep Security**.

3.	Haga clic en la marca de verificación para crear la máquina virtual.

	![Install the VM Agent and the Deep Security Agent](./media/virtual-machines-install-trend/InstallVMAgentandTrend.png)

## Instalación del Agente de Deep Security en una máquina virtual existente

Para llevar a cabo esta tarea, necesitará lo siguiente:

- El módulo de Azure PowerShell, versión 0.8.2 o posterior. Para obtener instrucciones y un vínculo a la versión más reciente, consulte [Instalación y configuración de Azure PowerShell](http://go.microsoft.com/fwlink/p/?LinkId=320552).  

- El Agente de máquina virtual. Para obtener instrucciones y un vínculo a la descarga, consulte la entrada de blog [Agente de máquina virtual y extensiones - Parte 2](http://go.microsoft.com/fwlink/p/?LinkId=403947).

Abra una sesión de Azure PowerShell y ejecute los comandos siguientes. Asegúrese de sustituir los marcadores de posición por sus propios valores, como por ejemplo MyServiceName.

1.	Obtenga el nombre del servicio en la nube, el nombre de la máquina virtual y la máquina virtual, y almacene toda esa información en las variables de forma que los comandos siguientes puedan usarla:
	<p>`$servicename = MyServiceName`
	<p>`$name = MyVmName`
	<p>`$vm = Get-AzureVM -ServiceName $servicename -Name $name`

	> [AZURE.NOTE] Si no conoce el nombre del servicio en la nube y de la máquina virtual, ejecute Get-AzureVM para mostrar esa información para todas las máquinas virtuales de la suscripción actual.

2.	Agregue el agente de Deep Security a la máquina virtual:
<p> `Set-AzureVMExtension -Publisher TrendMicro.DeepSecurity -ExtensionName TrendMicroDSA -VM $vm.VM`

	> [AZURE.NOTE] Si desea instalar una versión específica, ejecute el siguiente comando para obtener una lista de versiones disponibles:  `Get-AzureVMAvailableExtension TrendMicro.DeepSecurity -ExtensionName TrendMicroDSA`. A continuación, incluya el parámetro Version cuando ejecute Set-AzureVMExtension.

3.	Actualice la máquina virtual, que instala el Agente de Deep Security:
<p> `Update-AzureVM -ServiceName $servicename -Name $name -VM $vm.VM`


## Pasos siguientes
Después de instalar el agente, tardará unos minutos en comenzar a ejecutarse. Después de ello, necesitará activar Deep Security en la máquina virtual de forma que pueda ser administrado por Deep Security Manager. Consulte lo siguiente:

- Artículo de Trend sobre esta solución, [Seguridad en la nube instantánea para Microsoft Azure](http://go.microsoft.com/fwlink/?LinkId=404101).
- [Script de Windows PowerShell de ejemplo](http://go.microsoft.com/fwlink/?LinkId=404100) para configurar la máquina virtual.
- [Instrucciones](http://go.microsoft.com/fwlink/?LinkId=404099) para el ejemplo.




##Recursos adicionales
[Inicio de sesión en una máquina virtual con Windows Server]

[Administración de extensiones]


<!--Link references-->
[Inicio de sesión en una máquina virtual con Windows Server]: ../virtual-machines-log-on-windows-server/
[Administración de extensiones]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409






<!--HONumber=42-->
