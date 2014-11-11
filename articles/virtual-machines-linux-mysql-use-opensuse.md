<properties linkid="virtual-machines-linux-mysql-use-opensuse" urlDisplayName="Install MongoDB" pageTitle="nstall MongoDB on a virtual machine running CentOS Linux in Azure" metaKeywords="Azure, MongoDB" description="Learn how to install Mongo DB on a virtual machine in Azure." metaCanonical="" services="" documentationCenter="" title="Install MongoDB on a virtual machine running CentOS Linux in Azure" authors="" solutions="" manager="" editor="" />

Instalación de MySQL en una máquina virtual que ejecuta OpenSUSE Linux en Azure
===============================================================================

[MySQL](http://www.mysql.com) es una conocida base de datos SQL de código abierto. Con la ayuda del [Portal de administración de Azure](http://manage.windowsazure.com), puede crear una máquina virtual con OpenSUSE Linux desde la Galería de imágenes. Después, puede instalar y configurar una base de datos MySQL en la máquina virtual.

En este tutorial, aprenderá a:

-   Utilizar el Portal de administración para crear una máquina virtual de OpenSUSE Linux desde la galería
-   Conectarse a la máquina virtual con SSH o PuTTY
-   Instalar MySQL en la máquina virtual

Registrarse para la característica de vista previa de Máquinas virtuales
------------------------------------------------------------------------

Necesitará registrarse para la característica de vista previa de Máquinas virtuales de Azure a fin de crear una máquina virtual. También puede registrarse para obtener una cuenta gratuita si no tiene una cuenta de Azure.

[WACOM.INCLUDE [antares-iaas-signup-iaas](../includes/antares-iaas-signup-iaas.md)]

Creación de una máquina virtual que ejecuta OpenSUSE Linux
----------------------------------------------------------

[WACOM.INCLUDE [create-and-configure-opensuse-vm-in-portal](../includes/create-and-configure-opensuse-vm-in-portal.md)]

Instalación y ejecución de MySQL en la máquina virtual
------------------------------------------------------

[WACOM.INCLUDE [install-and-run-mysql-on-opensuse-vm](../includes/install-and-run-mysql-on-opensuse-vm.md)]

Resumen
-------

En este tutorial aprendió a crear una máquina virtual que ejecuta OpenSUSE Linux y a conectarse a ella de manera remota mediante SSH o PuTTY. También, aprendió a instalar y configurar MySQL en la máquina virtual de Linux. Para obtener más información sobre MySQL, consulte [MySQL Documentation](http://dev.mysql.com/doc/).

