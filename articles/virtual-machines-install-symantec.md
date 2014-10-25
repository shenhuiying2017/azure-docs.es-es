<properties title="How to install and configure Symantec Endpoint Protection on an Azure VM" pageTitle="How to install and configure Symantec Endpoint Protection on an Azure VM" description="Describes installing and configuring Symantec Endpoint Protection on a VM in Azure" metaKeywords="" services="virtual machines" solutions="" documentationCenter="" authors="kathydav" videoId="" scriptId="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-multiple" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav"></tags>

# Instalación y configuración de Endpoint Protection en una máquina virtual de Azure

En este artículo se muestra cómo instalar y configurar el cliente Symantec Endpoint Protection en una máquina virtual nueva o existente con Windows Server. Este es el cliente completo, que incluye servicios como protección contra virus y spyware, firewall y prevención de intrusiones.

El cliente se instala como una extensión de seguridad usando el Agente de máquina virtual. En una nueva máquina virtual, instalará el agente junto con el cliente del extremo. En una máquina virtual existente sin el agente, primero necesitará descargar e instalar dicho agente. Este artículo trata ambas situaciones.

Si tiene una suscripción existente de Symantec <for an on-premises solution, you can use it to protect your azure virtual machines> Si todavía no es cliente, puede suscribirse para una prueba. Para obtener más información acerca de esta solución, consulte [Symantec Endpoint Protection en la plataforma de Microsoft Azure][Symantec Endpoint Protection en la plataforma de Microsoft Azure]. Esta página también proporciona vínculos a información de licencia e instrucciones alternativas para instalar el cliente si ya es un cliente de Symantec.

## Instalación de Symantec Endpoint Protection en una nueva máquina virtual

El [Portal de administración de Azure][Portal de administración de Azure] permite instalar el Agente de máquina virtual y la extensión de seguridad de Symantec cuando usa la opción **Desde la galería** para crear la máquina virtual. Este enfoque proporciona una forma sencilla de agregar protección desde Symantec si crea una sola máquina virtual.

La opción **Desde la galería** abre un asistente que le ayuda configurar la máquina virtual. Utilice la última página del asistente para instalar el Agente de máquina virtual y la extensión de seguridad de Symantec.

Para obtener instrucciones generales, consulte [Creación de una máquina virtual que ejecuta Windows Server][Creación de una máquina virtual que ejecuta Windows Server]. Cuando se encuentre en la última página del asistente, realice las acciones siguientes:

1.  En Agente de máquina virtual, active la casilla **Instalar Agente de máquina virtual**.

2.  En Extensiones de seguridad, active la casilla **Symantec Endpoint Protection**.

    ![Install the VM Agent and the Endpoint Protection Client][Install the VM Agent and the Endpoint Protection Client]

3.  Haga clic en la marca de verificación en la parte inferior de la página para crear la máquina virtual.

## Instalación de Symantec Endpoint Protection en una máquina virtual existente

Para instalar el Agente de Deep Security en una máquina virtual existente, necesitará lo siguiente:

-   El módulo de Azure PowerShell, versión 0.8.2 o posterior. Para obtener instrucciones y un vínculo a la versión más reciente, consulte [Instalación y configuración de Azure PowerShell][Instalación y configuración de Azure PowerShell].

-   El Agente de máquina virtual. Para obtener instrucciones y un vínculo a la descarga, consulte la publicación del blog [Agente de máquina virtual y extensiones - Parte 2][Agente de máquina virtual y extensiones - Parte 2].

Abra una sesión de Azure PowerShell y ejecute los comandos siguientes. Asegúrese de sustituir los marcadores de posición por sus propios valores, como por ejemplo MyServiceName.

1.  Obtenga el nombre del servicio en la nube, el nombre de la máquina virtual y la máquina virtual, y almacene toda esa información en las variables de forma que los comandos siguientes puedan usarla:

    `$servicename = MyServiceName`

    `$name = MyVmName`

    `$vm = Get-AzureVM -ServiceName $servicename -Name $name`

    > [WACOM.NOTE] Si no conoce el nombre del servicio en la nube y de la máquina virtual, ejecute Get-AzureVM para mostrar esa información para todas las máquinas virtuales de la suscripción actual.

2.  Agregue el Agente de Symantec Endpoint Protection a la máquina virtual. Para instalar la versión más reciente, ejecute:

    `Set-AzureVMExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection -VM $vm.VM`

    > [WACOM.NOTE] Si desea instalar una versión específica, ejecute el siguiente comando para obtener una lista de versiones disponibles: `Get-AzureVMAvailableExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection`.
    > A continuación, incluya el parámetro Version cuando ejecute Set-AzureVMExtension.

3.  Actualice la máquina virtual, que instala el Agente de Symantec Endpoint Protection:

    `Update-AzureVM -ServiceName $servicename -Name $name -VM $vm.VM`

## Recursos adicionales

[Inicio de sesión en una máquina virtual con Windows Server][Inicio de sesión en una máquina virtual con Windows Server]

[Administración de extensiones][Administración de extensiones]

<!--Link references-->

  [Symantec Endpoint Protection en la plataforma de Microsoft Azure]: http://go.microsoft.com/fwlink/p/?LinkId=403942
  [Portal de administración de Azure]: http://manage.windowsazure.com
  [Creación de una máquina virtual que ejecuta Windows Server]: http://go.microsoft.com/fwlink/p/?LinkId=403943
  [Install the VM Agent and the Endpoint Protection Client]: ./media/virtual-machines-install-symantec/InstallVMAgentandSymantec.png
  [Instalación y configuración de Azure PowerShell]: http://go.microsoft.com/fwlink/p/?LinkId=320552
  [Agente de máquina virtual y extensiones - Parte 2]: http://go.microsoft.com/fwlink/p/?LinkId=403947
  [Inicio de sesión en una máquina virtual con Windows Server]: ../virtual-machines-log-on-windows-server/
  [Administración de extensiones]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409
