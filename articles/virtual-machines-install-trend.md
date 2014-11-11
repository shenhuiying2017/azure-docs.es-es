<properties title="How to install and configure Trend on an Azure VM" pageTitle="How to install and configure Trend Micro Deep Security as a Service on an Azure VM" description="Describes installing and configuring Trend Micro security on a VM in Azure" metaKeywords="" services="virtual machines" solutions="" documentationCenter="" authors="kathydav" videoId="" scriptId="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-multiple" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav" />

# Instalación y configuración de Trend Micro Deep Security como servicio en una máquina virtual de Azure

En este artículo se muestra cómo instalar y configurar Trend Micro Deep Security como servicio en una máquina virtual nueva o existente con Windows Server. La protección que Deep Security como servicio proporciona protección antimalware, firewall, sistema de prevención contra intrusiones y supervisión de integridad.

El cliente se instala como una extensión de seguridad usando el Agente de máquina virtual. En una nueva máquina virtual, instalará el agente de máquina virtual junto con el Agente de Deep Security. En una máquina virtual que no tenga el Agente de máquina virtual, primero necesitará descargarlo e instalarlo. Este artículo trata ambas situaciones.

Si tiene una suscripción existente de Trend para una solución local, puede usarla para proteger sus máquinas virtuales de Azure. Si todavía no es cliente, puede suscribirse para una prueba. Para obtener más información acerca de esta solución, consulte la publicación del blog [Extensión del Agente de máquina virtual de Microsoft Azure para Deep Security][Extensión del Agente de máquina virtual de Microsoft Azure para Deep Security].

## Instalación del Agente de Deep Security en una nueva máquina virtual

El [Portal de administración de Azure][Portal de administración de Azure] permite instalar el Agente de máquina virtual y la extensión de seguridad de Trend cuando usa la opción **Desde la galería** para crear la máquina virtual. Este enfoque proporciona una forma sencilla de agregar protección desde Trend si crea una sola máquina virtual.

La opción **Desde la galería** abre un asistente que le ayuda configurar la máquina virtual. Utilice la última página del asistente para instalar el Agente de máquina virtual y la extensión de seguridad de Trend. Para obtener instrucciones generales, consulte [Creación de una máquina virtual que ejecuta Windows Server][Creación de una máquina virtual que ejecuta Windows Server]. Cuando se encuentre en la última página del asistente, realice las acciones siguientes:

1.  En Agente de máquina virtual, active la casilla **Instalar Agente de máquina virtual**.

2.  En Extensiones de seguridad, active **Agente de Trend Micro Deep Security**.

3.  Haga clic en la marca de verificación para crear la máquina virtual.

    ![Install the VM Agent and the Deep Security Agent][Install the VM Agent and the Deep Security Agent]

## Instalación del Agente de Deep Security en una máquina virtual existente

Para llevar a cabo esta tarea, necesitará lo siguiente:

-   El módulo de Azure PowerShell, versión 0.8.2 o posterior. Para obtener instrucciones y un vínculo a la versión más reciente, consulte [Instalación y configuración de Azure PowerShell][Instalación y configuración de Azure PowerShell].

-   El Agente de máquina virtual. Para obtener instrucciones y un vínculo a la descarga, consulte la publicación del blog [Agente de máquina virtual y extensiones - Parte 2][Agente de máquina virtual y extensiones - Parte 2].

Abra una sesión de Azure PowerShell y ejecute los comandos siguientes. Asegúrese de sustituir los marcadores de posición por sus propios valores, como por ejemplo MyServiceName.

1.  Obtenga el nombre del servicio en la nube, el nombre de la máquina virtual y la máquina virtual, y almacene toda esa información en las variables de forma que los comandos siguientes puedan usarla:

    `$servicename = MyServiceName`

    `$name = MyVmName`

    `$vm = Get-AzureVM -ServiceName $servicename -Name $name`

    > [WACOM.NOTE] Si no conoce el nombre del servicio en la nube y de la máquina virtual, ejecute Get-AzureVM para mostrar esa información para todas las máquinas virtuales de la suscripción actual.

2.  Agregue el agente de Deep Security a la máquina virtual:

    `Set-AzureVMExtension -Publisher TrendMicro.DeepSecurity -ExtensionName TrendMicroDSA -VM $vm.VM`

    > [WACOM.NOTE] Si desea instalar una versión específica, ejecute el siguiente comando para obtener una lista de versiones disponibles: `Get-AzureVMAvailableExtension TrendMicro.DeepSecurity -ExtensionName TrendMicroDSA`. A continuación, incluya el parámetro Version cuando ejecute Set-AzureVMExtension.

3.  Actualice la máquina virtual, que instala el Agente de Deep Security:

    `Update-AzureVM -ServiceName $servicename -Name $name -VM $vm.VM`

## Pasos siguientes

Después de instalar el agente, tardará unos minutos en comenzar a ejecutarse. Después de ello, necesitará activar Deep Security en la máquina virtual de forma que pueda ser administrado por Deep Security Manager. Consulte lo siguiente:

-   Artículo de Trend sobre esta solución [Seguridad en la nube instantánea para Microsoft Azure][Seguridad en la nube instantánea para Microsoft Azure].
-   [Script de Windows PowerShell de ejemplo][Script de Windows PowerShell de ejemplo] para configurar la máquina virtual.
-   [Instrucciones][Instrucciones] para el ejemplo.

## Recursos adicionales

[Inicio de sesión en una máquina virtual con Windows Server][Inicio de sesión en una máquina virtual con Windows Server]

[Administración de extensiones][Administración de extensiones]

<!--Link references-->

  [Extensión del Agente de máquina virtual de Microsoft Azure para Deep Security]: http://go.microsoft.com/fwlink/p/?LinkId=403945
  [Portal de administración de Azure]: http://manage.windowsazure.com
  [Creación de una máquina virtual que ejecuta Windows Server]: http://go.microsoft.com/fwlink/p/?LinkId=403943
  [Install the VM Agent and the Deep Security Agent]: ./media/virtual-machines-install-trend/InstallVMAgentandTrend.png
  [Instalación y configuración de Azure PowerShell]: http://go.microsoft.com/fwlink/p/?LinkId=320552
  [Agente de máquina virtual y extensiones - Parte 2]: http://go.microsoft.com/fwlink/p/?LinkId=403947
  [Seguridad en la nube instantánea para Microsoft Azure]: http://go.microsoft.com/fwlink/?LinkId=404101
  [Script de Windows PowerShell de ejemplo]: http://go.microsoft.com/fwlink/?LinkId=404100
  [Instrucciones]: http://go.microsoft.com/fwlink/?LinkId=404099
  [Inicio de sesión en una máquina virtual con Windows Server]: ../virtual-machines-log-on-windows-server/
  [Administración de extensiones]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409
