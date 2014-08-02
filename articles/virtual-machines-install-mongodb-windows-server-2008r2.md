<properties linkid="manage-windows-common-task-mongodb-vm" urlDisplayName="Install MongoDB" pageTitle="Install MongoDB on a Windows Server virtual machine" metaKeywords="Azure vm, Azure MongoDB, Azure remote desktop" description="Learn how to create an Azure virtual machine with Windows Server 2008 R2, and then use Remote Desktop to install MongoDB." metaCanonical="" services="virtual-machines" documentationCenter="" title="Install MongoDB on a virtual machine running Windows Server in Azure" authors="kathydav" solutions="" manager="jeffreyg" editor="tysonn" />




Instalación de MongoDB en una máquina virtual con Windows Server en Azure
=========================================================================

[MongoDB](http://www.mongodb.org/) es una conocida base de datos NoSQL de alto rendimiento de código abierto. En el [Portal de administración de Azure](http://manage.windowsazure.com) puede crear una máquina virtual con Windows Server desde la Galería de imágenes. A continuación, puede instalar y configurar una base de datos MongoDB en la máquina virtual.

En este tutorial, aprenderá a:

-   Utilizar el Portal de administración para crear una máquina virtual con Windows Server desde la galería
-   Conectarse a la máquina virtual con Escritorio remoto
-   Instalar MongoDB en la máquina virtual

Creación de una máquina virtual con Windows Server 2008 R2
----------------------------------------------------------

[WACOM.INCLUDE [create-and-configure-windows-server-2008-vm-in-portal](../includes/create-and-configure-windows-server-2008-vm-in-portal.md)]

Acoplamiento de un disco de datos
---------------------------------

[WACOM.INCLUDE [attach-data-disk-windows-server-2008-vm-in-portal](../includes/attach-data-disk-windows-server-2008-vm-in-portal.md)]

Instalación y ejecución de MongoDB en la máquina virtual
--------------------------------------------------------

[WACOM.INCLUDE [install-and-run-mongo-on-win2k8-vm](../includes/install-and-run-mongo-on-win2k8-vm.md)]

Resumen
-------

En este tutorial se ha facilitado información acerca de cómo crear una máquina virtual con Windows Server y conectarse a ella de forma remota. También se ha descrito cómo instalar y configurar MongoDB en la máquina virtual con Windows. Para obtener más información acerca de MongoDB, consulte la [documentación sobre MongoDB](http://www.mongodb.org/display/DOCS/Home) (en inglés).

