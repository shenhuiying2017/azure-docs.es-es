<properties linkid="virtual-machines-linux-mysql-use-opensuse" urlDisplayName="Install MongoDB" pageTitle="nstall MongoDB on a virtual machine running CentOS Linux in Azure" metaKeywords="Azure, MongoDB" description="Learn how to install Mongo DB on a virtual machine in Azure." metaCanonical="" services="" documentationCenter="" title="Install MongoDB on a virtual machine running CentOS Linux in Azure" authors="timlt" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="timlt"></tags>

# Instalación de MySQL en una máquina virtual que ejecuta OpenSUSE Linux en Azure

[MySQL][] es una conocida base de datos SQL de código abierto. Con la ayuda del [Portal de administración de Azure][], puede crear una máquina virtual con OpenSUSE Linux desde la Galería de imágenes. Después, puede instalar y configurar una base de datos MySQL en la máquina virtual.

En este tutorial, aprenderá a:

-   Utilizar el Portal de administración para crear una máquina virtual de OpenSUSE Linux desde la galería
-   Conectarse a la máquina virtual con SSH o PuTTY
-   Instalar MySQL en la máquina virtual

## Creación de una suscripción de Azure

Si todavía no dispone de una suscripción de Azure, puede registrarse [de forma gratuita][]. Después del registro, siga estos pasos para continuar este tutorial.

[WACOM.INCLUDE [antares-iaas-signup-iaas][]]

## Creación de una máquina virtual que ejecuta OpenSUSE Linux

[WACOM.INCLUDE [create-and-configure-opensuse-vm-in-portal][]]

## Instalación y ejecución de MySQL en la máquina virtual

[WACOM.INCLUDE [install-and-run-mysql-on-opensuse-vm][]]

## Resumen

En este tutorial aprendió a crear una máquina virtual que ejecuta OpenSUSE Linux y a conectarse a ella de manera remota mediante SSH o PuTTY. También, aprendió a instalar y configurar MySQL en la máquina virtual de Linux. Para obtener más información sobre MySQL, consulte [MySQL Documentation][].

  [MySQL]: http://www.mysql.com
  [Portal de administración de Azure]: http://manage.windowsazure.com
  [de forma gratuita]: http://azure.microsoft.com
  [antares-iaas-signup-iaas]: ../includes/antares-iaas-signup-iaas.md
  [create-and-configure-opensuse-vm-in-portal]: ../includes/create-and-configure-opensuse-vm-in-portal.md
  [install-and-run-mysql-on-opensuse-vm]: ../includes/install-and-run-mysql-on-opensuse-vm.md
  [MySQL Documentation]: http://dev.mysql.com/doc/
