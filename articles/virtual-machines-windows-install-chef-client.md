<properties title="About Chef and Azure Virtual Machines" pageTitle="About Chef and Azure Virtual Machines" description="Describes installing and configuring Chef on a VM in Azure" metaKeywords="" services="virtual machines" solutions="" documentationCenter="" authors="kathydav" videoId="" scriptId="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav"></tags>

# Chef y Máquinas virtuales de Azure

Chef proporciona un sistema de automatización para crear, implementar y administrar su infraestructura. Los recursos se administran utilizando recetas, que son definiciones reutilizables que proporcionan instrucciones para tareas como, por ejemplo, configurar un servidor web.

Chef es un sistema cliente-servidor. Para conocer las opciones que tiene a su disposición para utilizar un servidor Chef, consulte [Elección de su instalación][Elección de su instalación]. Necesitará información acerca del servidor Chef para configurar el cliente. Para instalar el cliente Chef en una máquina virtual de Azure, dispone de las siguientes opciones:

-   Use el Portal de administración para instalar el cliente Chef cuando cree una máquina virtual que ejecute Windows Server 2012 o Windows Server 2012 R2. Para obtener instrucciones, consulte el [Portal de Microsoft Azure][Portal de Microsoft Azure].
-   Use Windows PowerShell para instalar el cliente Chef en una máquina virtual existente. En Github tiene un [script][script] de ejemplo a su disposición.
-   Use un complemento de Chef, [knife-azure][knife-azure], para crear una instancia de máquina virtual e instalar el cliente Chef.

## Recursos adicionales

[Chef y Microsoft Azure][Chef y Microsoft Azure]

[Inicio de sesión en una máquina virtual con Windows Server][Inicio de sesión en una máquina virtual con Windows Server]

[Inicio de sesión en una máquina virtual con Linux][Inicio de sesión en una máquina virtual con Linux]

[Administración de extensiones][Administración de extensiones]

<!--Link references-->

  [Elección de su instalación]: http://www.getchef.com/chef/choose-your-version/
  [Portal de Microsoft Azure]: http://docs.opscode.com/azure_portal.html
  [script]: https://gist.github.com/kaustubh-d/cea1aa75baebd3615609
  [knife-azure]: http://docs.getchef.com/plugin_knife_azure.html
  [Chef y Microsoft Azure]: http://www.getchef.com/solutions/azure/
  [Inicio de sesión en una máquina virtual con Windows Server]: ../virtual-machines-log-on-windows-server/
  [Inicio de sesión en una máquina virtual con Linux]: ../virtual-machines-linux-how-to-log-on
  [Administración de extensiones]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409
