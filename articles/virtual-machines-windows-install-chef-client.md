<properties 
	pageTitle="Chef y Máquinas virtuales de Azure" 
	description="Describe la instalación y configuración de Chef en una máquina virtual de Azure." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/29/2015" 
	ms.author="kathydav"/>

#Chef y Máquinas virtuales de Azure

Chef proporciona un sistema de automatización para crear, implementar y administrar su infraestructura. Los recursos se administran utilizando recetas, que son definiciones reutilizables que proporcionan instrucciones para tareas como, por ejemplo, configurar un servidor web.

Chef es un sistema cliente-servidor. Para conocer las opciones que tiene a su disposición para utilizar un servidor Chef, consulte [Elección de su instalación](http://www.getchef.com/chef/choose-your-version/). Necesitará información acerca del servidor Chef para configurar el cliente. 

Para instalar el cliente de Chef en una máquina virtual de Azure, puede seguir una de las opciones siguientes:

- Use el Portal de administración de Azure para instalar el cliente Chef cuando cree una máquina virtual que ejecute Windows Server 2012 o Windows Server 2012 R2. Para obtener instrucciones, vea [Microsoft Azure Portal](https://docs.chef.io/azure_portal.html).
- Use Azure PowerShell para instalar el cliente de Chef en una máquina virtual existente. En Github tiene un [script](https://gist.github.com/kaustubh-d/cea1aa75baebd3615609) de ejemplo a su disposición.
- Use un complemento de Chef, [knife-azure](http://docs.getchef.com/plugin_knife_azure.html), para crear una instancia de máquina virtual e instalar el cliente Chef.   


##Recursos adicionales
[Chef y Microsoft Azure]

[Inicio de sesión en una máquina virtual con Windows Server]

[Inicio de sesión en una máquina virtual con Linux]

[Administración de extensiones]

<!--Link references-->
[Chef y Microsoft Azure]: http://www.getchef.com/solutions/azure/
[Inicio de sesión en una máquina virtual con Windows Server]: ../virtual-machines-log-on-windows-server/
[Inicio de sesión en una máquina virtual con Linux]: ../virtual-machines-linux-how-to-log-on
[Administración de extensiones]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409






<!--HONumber=42-->
