<properties urlDisplayName="Install MongoDB" pageTitle="Instalaci&oacute;n de MongoDB en una m&aacute;quina virtual de Linux en Azure" metaKeywords="Azure vm CentOS, Azure vm Linux, Linux vm, Linux MongoDB" description="Aprenda a crear una m&aacute;quina virtual de Azure con CentOS Linux y luego utilizar SSH o PuTTY para instalar MongoDB." metaCanonical="" services="virtual-machines" documentationCenter="" title="Instalaci&oacute;n de MongoDB en una m&aacute;quina virtual con CentOS Linux en Azure" authors="kathydav" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav" />

# Instalación de MongoDB en una máquina virtual con CentOS Linux en Azure

[MongoDB][MongoDB] es una conocida base de datos NoSQL de alto rendimiento de código abierto. En el Portal de administración de Azure, puede crear una máquina virtual con CentOS Linux desde la Galería de imágenes. A continuación, puede instalar y configurar una base de datos MongoDB en la máquina virtual.

Aprenderá a:

-   Utilizar el Portal de administración para seleccionar e instalar una máquina virtual Linux con CentOS Linux desde la galería.

-   Conectarse a la máquina virtual con SSH o PuTTY
-   Instalar MongoDB en la máquina virtual

## Creación de una máquina virtual que ejecuta CentOS Linux

[WACOM.INCLUDE [create-and-configure-centos-vm-in-portal](../includes/create-and-configure-centos-vm-in-portal.md)]

## Acoplamiento de un disco de datos

[WACOM.INCLUDE [attach-data-disk-centos-vm-in-portal](../includes/attach-data-disk-centos-vm-in-portal.md)]

## Instalación y ejecución de MongoDB en la máquina virtual

[WACOM.INCLUDE [install-and-run-mongo-on-centos-vm](../includes/install-and-run-mongo-on-centos-vm.md)]

## Resumen

En este tutorial aprendió a crear una máquina virtual de Linux y a conectarse a ella de manera remota mediante SSH o PuTTY. También, aprendió a instalar y configurar MongoDB en la máquina virtual de Linux. Para obtener más información acerca de MongoDB, consulte la [documentación sobre MongoDB][documentación sobre MongoDB] (en inglés).

  [MongoDB]: http://www.mongodb.org/
  [documentación sobre MongoDB]: http://www.mongodb.org/display/DOCS/Home
