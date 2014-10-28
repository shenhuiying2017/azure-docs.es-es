<properties linkid="store-mongodb-virtual-machines-linux-centos" urlDisplayName="Install MongoDB" pageTitle="nstall MongoDB on a virtual machine running CentOS Linux in Azure" metaKeywords="Azure, MongoDB" description="Learn how to install Mongo DB on a virtual machine in Azure." metaCanonical="" services="" documentationCenter="" title="Install MongoDB on a virtual machine running CentOS Linux in Azure" authors="bbenz, MSOpenTech" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang ms.topic="article" ms.date="01/01/1900" ms.author="bbenz, MSOpenTech"></tags>

# Instalación de MongoDB en una máquina virtual con CentOS Linux en Azure

[MongoDB][] es una conocida base de datos NoSQL de alto rendimiento de código abierto. En el [Portal de administración de Azure][], puede crear una máquina virtual con CentOS Linux desde la Galería de imágenes. A continuación, puede instalar y configurar una base de datos MongoDB en la máquina virtual.

Aprenderá a:

-   Utilizar el Portal de administración para crear una máquina virtual con CentOS Linux desde la galería
-   Conectarse a la máquina virtual con SSH o PuTTY
-   Instalar MongoDB en la máquina virtual

## Creación de una máquina virtual que ejecuta CentOS Linux

[WACOM.INCLUDE [create-and-configure-centos-vm-in-portal][]]

## Instalación y ejecución de MongoDB en la máquina virtual

[WACOM.INCLUDE [install-and-run-mongo-on-centos-vm][]]

## Resumen

En este tutorial aprendió a crear una máquina virtual de Linux y a conectarse a ella de manera remota mediante SSH o PuTTY. También, aprendió a instalar y configurar MongoDB en la máquina virtual de Linux. Para obtener más información acerca de MongoDB, consulte la [documentación sobre MongoDB][] (en inglés).

  [MongoDB]: http://www.mongodb.org/
  [Portal de administración de Azure]: http://manage.windowsazure.com
  [create-and-configure-centos-vm-in-portal]: ../includes/create-and-configure-centos-vm-in-portal.md
  [install-and-run-mongo-on-centos-vm]: ../includes/install-and-run-mongo-on-centos-vm.md
  [documentación sobre MongoDB]: http://www.mongodb.org/display/DOCS/Home
