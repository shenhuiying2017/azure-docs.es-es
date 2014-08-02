<properties linkid="store-mongodb-virtual-machines-linux-centos" urlDisplayName="Install MongoDB" pageTitle="nstall MongoDB on a virtual machine running CentOS Linux in Azure" metaKeywords="Azure, MongoDB" description="Learn how to install Mongo DB on a virtual machine in Azure." metaCanonical="" services="" documentationCenter="" title="Install MongoDB on a virtual machine running CentOS Linux in Azure" authors="" solutions="" manager="" editor="" />

Instalación de MongoDB en una máquina virtual con CentOS Linux en Azure
=======================================================================

[MongoDB](http://www.mongodb.org/) es una conocida base de datos NoSQL de alto rendimiento de código abierto. En el [Portal de administración de Azure](http://manage.windowsazure.com), puede crear una máquina virtual con CentOS Linux desde la Galería de imágenes. A continuación, puede instalar y configurar una base de datos MongoDB en la máquina virtual.

Aprenderá a:

-   Utilizar el Portal de administración para crear una máquina virtual con CentOS Linux desde la galería
-   Conectarse a la máquina virtual con SSH o PuTTY
-   Instalar MongoDB en la máquina virtual

Registrarse para la característica de vista previa de Máquinas virtuales
------------------------------------------------------------------------

Necesitará registrarse para la característica de vista previa de Máquinas virtuales de Azure a fin de crear una máquina virtual. También puede registrarse para obtener una cuenta de evaluación gratuita si no tiene una cuenta de Azure.

[WACOM.INCLUDE [antares-iaas-signup-iaas](../includes/antares-iaas-signup-iaas.md)]

Creación de una máquina virtual que ejecuta CentOS Linux
--------------------------------------------------------

[WACOM.INCLUDE [create-and-configure-centos-vm-in-portal](../includes/create-and-configure-centos-vm-in-portal.md)]

Instalación y ejecución de MongoDB en la máquina virtual
--------------------------------------------------------

[WACOM.INCLUDE [install-and-run-mongo-on-centos-vm](../includes/install-and-run-mongo-on-centos-vm.md)]

Resumen
-------

En este tutorial aprendió a crear una máquina virtual de Linux y a conectarse a ella de manera remota mediante SSH o PuTTY. También, aprendió a instalar y configurar MongoDB en la máquina virtual de Linux. Para obtener más información acerca de MongoDB, consulte la [documentación sobre MongoDB](http://www.mongodb.org/display/DOCS/Home) (en inglés).

