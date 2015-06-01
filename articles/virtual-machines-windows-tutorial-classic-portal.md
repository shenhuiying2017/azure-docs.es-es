<properties
	pageTitle="Creación de una máquina virtual que ejecuta Windows en Azure"
	description="Aprenda a crear una máquina virtual Windows (VM) en el portal de Azure clásico."
	services="virtual-machines"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor="tysonn"
	tags="azure-classic-portal"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/27/2015"
	ms.author="kathydav"/>



# Crear una máquina virtual que ejecute Windows

> [AZURE.SELECTOR]
- [Azure portal](virtual-machines-windows-tutorial.md)
- [Azure classic portal](virtual-machines-windows-tutorial-classic-portal.md)
- [PowerShell](virtual-machines-ps-create-preconfigure-windows-vms.md)

Este tutorial muestra lo fácil que es crear una máquina virtual (VM) de Azure. En este tutorial se usa una imagen de Windows Server, pero es solo una de las muchas imágenes disponibles a través de Azure. Aquí se incluyen los sistemas operativos Windows, los sistemas operativos basados en Linux y las imágenes con aplicaciones instaladas. Las imágenes entre las que puede elegir dependen del tipo de suscripción. Por ejemplo, las imágenes de escritorio pueden estar disponibles para los suscriptores de MSDN.

También se pueden crear máquinas virtuales Windows con [imágenes propias](virtual-machines-create-upload-vhd-windows-server-classic-portal.md). Para obtener más información acerca de las máquinas virtuales de Azure, consulte [Información general sobre Máquinas virtuales de Azure](http://msdn.microsoft.com/library/azure/jj156143.aspx).

[AZURE.INCLUDE [free-trial-note](../includes/free-trial-note.md)]

## <a id="createvirtualmachine"> </a>Creación de la máquina virtual

En esta sección se muestra cómo utilizar la opción **De la galería** del portal clásico de Azure para crear una máquina virtual. Esta opción proporciona más opciones de configuración que **Creación rápida**. Por ejemplo, si desea unir una máquina virtual a una red virtual, necesitará utilizar la opción **De la galería**.

> [AZURE.NOTE]También puede probar el [Portal de Azure](https://portal.azure.com), que tiene más opciones y es personalizable, para crear una máquina virtual, automatizar la implementación de plantillas de aplicación de varias máquinas virtuales, usar las características mejoradas de supervisión y diagnóstico de máquinas virtuales, etc. Las opciones de configuración de máquina virtual disponibles en los dos portales se solapan sustancialmente, pero no son idénticas.

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../includes/virtual-machines-create-WindowsVM.md)]

## Pasos siguientes

- Inicie sesión en la máquina virtual. Para obtener instrucciones, consulte [Inicio de sesión en una máquina virtual con Windows Server](virtual-machines-log-on-windows-server.md).

- Acople un disco para almacenar los datos. Puede acoplar tanto discos vacíos como discos que contienen datos. Para obtener instrucciones, consulte el [tutorial Acoplamiento de un disco de datos a una máquina virtual de Windows](storage-windows-attach-disk.md).

## Recursos adicionales

Para obtener más información acerca de lo que se puede configurar para una máquina virtual y cuando se puede hacer, consulte [Acerca de los valores de configuración de la VM de Azure](http://msdn.microsoft.com/library/azure/dn763935.aspx).

<!--HONumber=52-->
