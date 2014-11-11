<properties  linkid="manage-linux-common-task-mysql-virtual-machine" urlDisplayName="Install MySQL" pageTitle="Install MySQL on a Linux virtual machine in Azure" metaKeywords="Azure vm OpenSUSE, Linux vm" description="Learn how to create an Azure virtual machine with OpenSUSE Linux, and then use SSH or PuTTY to install MySQL." metaCanonical="" services="virtual-machines" documentationCenter="" title="Install MySQL on a virtual machine running OpenSUSE Linux in Azure" authors="" solutions="" manager="" editor="" />

# Instalación de MySQL en una máquina virtual que ejecuta OpenSUSE Linux en Azure

[MySQL][1] es una conocida base de datos SQL de código abierto. Con la
ayuda del [Portal de administración de Azure][2], puede crear una máquina virtual con OpenSUSE Linux desde la Galería de imágenes. Después, puede instalar y configurar una base de datos MySQL en la máquina virtual.

En este tutorial, aprenderá a:

* Utilizar el Portal de administración para crear una máquina virtual de OpenSUSE Linux desde la galería

* Conectarse a la máquina virtual con SSH o PuTTY

* Instalar MySQL en la máquina virtual

## Creación de una máquina virtual que ejecuta OpenSUSE Linux

[WACOM.INCLUDE
[create-and-configure-opensuse-vm-in-portal](../includes/create-and-configure-opensuse-vm-in-portal.md)]

## Instalación y ejecución de MySQL en la máquina virtual

[WACOM.INCLUDE
[install-and-run-mysql-on-opensuse-vm](../includes/install-and-run-mysql-on-opensuse-vm.md)]

## Resumen

En este tutorial aprendió a crear una máquina virtual de OpenSUSE Linux y a conectarse a ella de manera remota mediante SSH o PuTTY. También, aprendió a instalar y configurar MySQL en la máquina virtual de Linux. Para obtener más información sobre MySQL, consulte [MySQL Documentation][3].



[1]: http://www.mysql.com
[2]: http://manage.windowsazure.com
[3]: http://dev.mysql.com/doc/