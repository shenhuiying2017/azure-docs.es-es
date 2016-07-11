<properties
	pageTitle="Instalación de MongoDB en una máquina virtual de Windows | Microsoft Azure"
	description="Obtenga información acerca de cómo instalar MongoDB en una máquina virtual de Azure creada con el modelo clásico de implementación con Windows Server."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="iainfoulds"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/07/2016"
	ms.author="iainfou"/>

#Instalación de MongoDB en una máquina virtual de Windows

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

[MongoDB][MongoDB] es una conocida base de datos NoSQL de alto rendimiento de código abierto. Este artículo le guía en la creación de una nueva máquina virtual (VM) de Windows Server mediante el [Portal de Azure clásico][AzurePortal], la creación y conexión de un disco de datos a la máquina virtual y la instalación y configuración de MongoDB. Si tiene una máquina virtual existente en Azure que le gustaría utilizar, puede ir directamente a la [instalación y configuración de MongoDB](#install-and-run-mongo-on-win2k8-vm).


## Creación de una máquina virtual que ejecuta Windows Server

Para crear una máquina virtual siga estas instrucciones.

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-windowsvm.md)]

> [AZURE.NOTE] Al crear la máquina virtual, puede agregar un extremo para MongoDB y configurarlo como se indica a continuación: asígnele el nombre **Mongo**, use el protocolo **TCP** y establezca los puertos públicos y privados en **27017**.

## Acoplamiento de un disco de datos
Para proporcionar almacenamiento para la máquina virtual, acople un disco de datos e inicialícelo para que Windows pueda usarlo. Puede acoplar un disco existente si ya tiene datos que desea usar o acoplar un disco vacío.

[AZURE.INCLUDE [howto-attach-disk-windows-linux](../../includes/howto-attach-disk-windows-linux.md)]

Para obtener instrucciones sobre la inicialización del disco, consulte "Inicialización de un nuevo disco de datos en Windows Server" en [Acoplamiento de un disco de datos a una máquina virtual de Windows](virtual-machines-windows-classic-attach-disk.md).

## Instalación y ejecución de MongoDB en la máquina virtual

[AZURE.INCLUDE [install-and-run-mongo-on-win2k8-vm](../../includes/install-and-run-mongo-on-win2k8-vm.md)]

## Resumen
En este tutorial se ha descrito cómo crear una máquina virtual con Windows Server, conectarse a ella de forma remota y acoplar un disco de datos. También se ha descrito cómo instalar y configurar MongoDB en la máquina virtual basada en Windows. Ahora es posible acceder a MongoDB en la máquina virtual basada en Windows. Para ello, hay que seguir los temas avanzados de la [documentación de MongoDB][MongoDocs].

[MongoDocs]: http://docs.mongodb.org/manual/
[MongoDB]: http://www.mongodb.org/
[AzurePortal]: http://manage.windowsazure.com

<!---HONumber=AcomDC_0629_2016-->