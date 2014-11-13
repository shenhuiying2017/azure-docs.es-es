<properties title="Puppet y M&aacute;quinas virtuales de Azure" pageTitle="Puppet y M&aacute;quinas virtuales de Azure" description="Describe la instalaci&oacute;n y configuraci&oacute;n de Puppet en una m&aacute;quina virtual de Azure." metaKeywords="" services="virtual machines" solutions="" documentationCenter="" authors="kathydav" manager="timlt" videoId="" scriptId="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="07/29/2014" ms.author="kathydav" />

# Puppet y Máquinas virtuales de Azure

Puppet Enterprise es un software de automatización para crear, implementar y administrar su infraestructura. Puede usarlo para administrar el ciclo de vida de su infraestructura de TI, que incluye: detección, aprovisionamiento, administración de configuración del sistema operativo y las aplicaciones, orquestación e informes.

Puppet es un sistema cliente-servidor. Puppet Master y Puppet Enterprise Agent están disponibles para instalarse a través de Azure:

-   Puppet Master está disponible como una imagen preconfigurada, instalada en un servidor Ubuntu. También puede instalar Puppet Enterprise en un servidor existente, pero usar la imagen es la forma más sencilla de comenzar. Necesitará información acerca del servidor para configurar el agente.
-   Puppet Enterprise Agent está disponible como extensión de máquina virtual que puede instalar cuando crea una máquina virtual o en una máquina virtual existente.

Para obtener instrucciones, descargue la "guía introductoria" disponible en la página de [Microsoft Windows y Azure][Microsoft Windows y Azure].

## Recursos adicionales

[Nuevas integraciones con Microsoft Azure y Visual Studio][Nuevas integraciones con Microsoft Azure y Visual Studio]

[Inicio de sesión en una máquina virtual con Windows Server][Inicio de sesión en una máquina virtual con Windows Server]

[Inicio de sesión en una máquina virtual con Linux][Inicio de sesión en una máquina virtual con Linux]

[Administración de extensiones][Administración de extensiones]

<!--Link references-->

  [Microsoft Windows y Azure]: http://puppetlabs.com/solutions/microsoft
  [Nuevas integraciones con Microsoft Azure y Visual Studio]: http://puppetlabs.com/blog/new-integrations-windows-azure-and-visual-studio
  [Inicio de sesión en una máquina virtual con Windows Server]: ../virtual-machines-log-on-windows-server/
  [Inicio de sesión en una máquina virtual con Linux]: ../virtual-machines-linux-how-to-log-on
  [Administración de extensiones]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409
