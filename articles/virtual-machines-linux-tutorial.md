<properties 
	pageTitle="Creación de una máquina virtual que ejecuta Linux en Azure" 
	description="Aprenda a crear una máquina virtual de Azure que ejecuta Linux utilizando una imagen de Azure." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="1/19/2015" 
	ms.author="kathydav"/>

#Creación de una máquina virtual que ejecuta Linux 

Es fácil crear una máquina virtual que ejecute Linux utilizando la Galería de imágenes en el Portal de administración de Azure. Esta guía le muestra cómo hacerlo y en este proceso se supone que no tiene ninguna experiencia previa con Azure.

> [AZURE.NOTE] Aunque no es necesario que tenga experiencia con máquinas virtuales de Azure para completar este tutorial, sí necesita una cuenta de Azure. Puede crear una cuenta de evaluación gratuita en pocos minutos. Para obtener más información, consulte [Creación de una cuenta de Azure](http://azure.microsoft.com/develop/php/tutorials/create-a-windows-azure-account/). 

Esta tutorial abarca lo siguiente:

- [Acerca de las máquinas virtuales en Azure] []
- [Creación de una máquina virtual] []
- [Inicio de sesión en una máquina virtual después de su creación] []
- [Acoplamiento de un disco de datos a la nueva máquina virtual] []

**Importante**: este tutorial crea una máquina virtual que no está conectada a una red virtual. Si desea que su máquina virtual use una red virtual, debe especificar la red virtual cuando cree la máquina virtual. Para obtener más información sobre las redes virtuales, consulte [Información general sobre redes virtuales de Azure](http://go.microsoft.com/fwlink/p/?LinkID=294063).

## <a id="virtualmachine"> </a>Acerca de las máquinas virtuales en Azure ##

Una máquina virtual en Azure es un servidor en la nube que puede controlar y administrar. Después de crear una máquina virtual en Azure, tiene la opción de eliminarla y volver a crearla cada vez que lo necesite, además de tener acceso a ella igual que hace con un servidor de su oficina. Para crear una máquina virtual, se usan archivos de un disco duro virtual (VHD). Se usan los siguientes tipos de discos duros virtuales para una máquina virtual:

- **Imagen** - Un VHD que se usa como plantilla para crear una máquina virtual nueva. Una imagen es una plantilla porque no tiene una configuración específica como una máquina virtual en ejecución, tal como el nombre del equipo y la configuración de la cuenta de usuario. Si crea una máquina virtual usando una imagen, se crea un disco de sistema operativo automáticamente para la máquina virtual nueva.
- **Disco** - Un disco es un VHD que puede arrancar y montar como una versión en ejecución de un sistema operativo. Después de que se proporciona una imagen, se convierte en un disco. Siempre se crea un disco al usar una imagen para crear una máquina virtual. Un disco es todo VHD que se adjunta a hardware virtualizado y que se ejecuta como parte de un servicio.

Para crear una máquina virtual con el uso de imágenes tiene disponibles las opciones siguientes:

- Cree una máquina virtual usando una imagen que se proporciona en la Galería de imágenes del Portal de administración de Azure.
- Cree y cargue un archivo .vhd que contenga una imagen de Azure y luego cree una máquina virtual usando la imagen. Para obtener más información sobre la creación y la carga de una imagen personalizada, consulte [Creación y carga de un disco duro virtual que contiene el sistema operativo Linux] (/es-es/manage/linux/common-tasks/upload-a-vhd/).

Cada máquina virtual reside en un servicio en la nube, ya sea sola o agrupada con otras máquinas virtuales. Puede poner máquinas virtuales en el mismo servicio en la nube para que las máquinas virtuales puedan comunicarse entre sí, equilibrar la carga de tráfico de red entre las máquinas virtuales y mantener la alta disponibilidad de las máquinas. Para obtener más información sobre los servicios de nube y las máquinas virtuales, consulte la sección "Modelos de ejecución" en [Introducción a Azure](http://go.microsoft.com/fwlink/p/?LinkId=311926).

## <a id="custommachine"></a>Creación de una máquina virtual ##

Este tutorial utiliza el método **De la galería** para crear una máquina virtual porque le ofrece más opciones que el método **Creación rápida**. Puede seleccionar recursos conectados, el nombre DNS y la conectividad de red si fuera necesario.

[AZURE.INCLUDE [virtual-machines-create-LinuxVM](../includes/virtual-machines-create-LinuxVM.md)]

[AZURE.INCLUDE [virtual-machines-Linux-tutorial-log-on-attach-disk](../includes/virtual-machines-Linux-tutorial-log-on-attach-disk.md)]

> [AZURE.NOTE] También se puede conectar a la máquina virtual de Linux mediante una clave SSH para la identificación. Para obtener más información acerca de cómo configurar y conectar con una clave SSH, consulte [Uso de SSH con Linux en Azure](virtual-machines-linux-use-ssh-key).

##Pasos siguientes 

Para obtener más información sobre Linux en Azure, consulte los siguientes artículos:

- [Introducción a Linux en Azure](http://azure.microsoft.com/documentation/articles/introduction-linux/)

- [Uso de las herramientas de línea de comandos de Azure para Mac y Linux](http://azure.microsoft.com/documentation/articles/xplat-cli/)

- [Acerca de los ajustes de configuración de máquinas virtuales en Azure](http://msdn.microsoft.com/library/azure/dn763935.aspx)

- [Extensión de máquina virtual Docker para Linux en Azure](virtual-machines-docker-vm-extension.md)


[Pasos siguientes]: #next
[Acerca de las máquinas virtuales en Azure]: #virtualmachine
[Creación de una máquina virtual]: #custommachine
[Inicio de sesión en una máquina virtual después de su creación]: #logon
[Acoplamiento de un disco de datos a la nueva máquina virtual]: #attachdisk





<!--HONumber=42-->
