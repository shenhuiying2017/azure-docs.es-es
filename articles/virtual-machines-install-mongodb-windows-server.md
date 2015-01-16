<properties urlDisplayName="Install MongoDB" pageTitle="Instalación de MongoDB en una máquina virtual con Windows Server" metaKeywords="Azure vm, Azure MongoDB, Azure remote desktop" description="Aprenda a instalar MongoDB en una máquina virtual de Azure con Windows Server." metaCanonical="" services="virtual-machines" documentationCenter="" title="Install MongoDB on a virtual machine running Windows Server in Azure" authors="kathydav" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="11/24/2014" ms.author="kathydav" />

#Instalación de MongoDB en una máquina virtual con Windows Server

[MongoDB][MongoDB] es una conocida base de datos NoSQL de alto rendimiento de código abierto.  En el [Portal de administración de Azure][AzureManagementPortal], puede crear una máquina virtual con Windows Server desde la Galería de imágenes.  A continuación, puede instalar y configurar una base de datos MongoDB en la máquina virtual.

En este artículo se muestra cómo:

- Utilizar el Portal de administración para crear una máquina virtual con Windows Server desde la galería
- Conectarse a la máquina virtual a través del Escritorio remoto de Windows.
- Acoplar un disco de datos a la máquina virtual
- Instalar MongoDB en la máquina virtual

## Creación de una máquina virtual que ejecuta Windows Server

A continuación se muestran instrucciones generales; puede modificarlas creando un extremo para permitir el acceso remoto a MongoDB. (Puede crearlo más tarde, tal y como se describe después de las instrucciones para instalar MongoDB.) En la última página del asistente, agregue un extremo y configúrelo de esta manera:

- Asígnele el nombre **Mongo**
- Use **TCP** como protocolo
- Establezca los puertos públicos y privados en **27017**.
 
[WACOM.INCLUDE [virtual-machines-create-WindowsVM](../includes/virtual-machines-create-WindowsVM.md)]

## Acoplamiento de un disco de datos
Para proporcionar un almacenamiento para la máquina virtual, acople un disco de datos e inicialícelo para que Windows pueda usarlo. Puede acoplar un disco existente si ya tiene datos que desea usar o acoplar un disco vacío.

[WACOM.INCLUDE [howto-attach-disk-windows-linux](../includes/howto-attach-disk-windows-linux.md)]

Para obtener instrucciones acerca de cómo conseguir esta información, consulte la sección "Inicializar un disco de datos nuevo en Windows Server" en [Acoplamiento de un disco de datos a una máquina virtual de Windows](http://azure.microsoft.com/en-us/documentation/articles/storage-windows-attach-disk/).

## Instalación y ejecución de MongoDB en la máquina virtual 

[WACOM.INCLUDE [install-and-run-mongo-on-win2k8-vm](../includes/install-and-run-mongo-on-win2k8-vm.md)]

##Resumen
En este tutorial se ha facilitado información acerca de cómo crear una máquina virtual con Windows Server, conectarse a ella de forma remota y acoplar un disco de datos.  También se ha descrito cómo instalar y configurar MongoDB en la máquina virtual con Windows. Para obtener más información acerca de MongoDB, consulte la [documentación sobre MongoDB][MongoDocs].

[MongoDocs]: http://www.mongodb.org/display/DOCS/Home
[MongoDB]: http://www.mongodb.org/
[AzureManagementPortal]: http://manage.windowsazure.com

<!--HONumber=35.1-->
