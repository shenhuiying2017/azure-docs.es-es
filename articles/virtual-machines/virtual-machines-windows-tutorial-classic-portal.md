<properties
	pageTitle="Creación de una máquina virtual que ejecuta Windows en Azure"
	description="Aprenda a crear una máquina virtual Windows (VM) en el Portal de administración de Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/05/2015"
	ms.author="kathydav"/>

# Creación de una máquina virtual con Windows en el Portal de administración de Azure

> [AZURE.SELECTOR]
- [Azure Preview Portal](virtual-machines-windows-tutorial.md)
- [Azure Portal](virtual-machines-windows-tutorial-classic-portal.md)
- [PowerShell - Resource Management](virtual-machines-deploy-rmtemplates-powershell.md)
- [PowerShell - Service Management](virtual-machines-ps-create-preconfigure-windows-vms.md)


Este tutorial muestra lo fácil que resulta crear una máquina virtual (VM) de Azure en el Portal de administración de Azure. Vamos a usar una imagen de Windows Server como ejemplo, pero es solo una de las muchas imágenes que Azure ofrece. Tenga en cuenta que las opciones de imagen dependen de su suscripción. Por ejemplo, las imágenes de escritorio pueden estar disponibles para los suscriptores MSDN.

También se pueden crear máquinas virtuales con [imágenes propias](virtual-machines-create-upload-vhd-windows-server.md). Para obtener información sobre este y otros métodos, consulte [Diferentes formas de crear una máquina virtual de Windows](virtual-machines-windows-choices-create-vm.md).

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

## <a id="createvirtualmachine"> </a>Creación de la máquina virtual

En esta sección se muestra cómo utilizar la opción **De la galería** en el Portal de administración de Azure para crear la máquina virtual. Esta opción proporciona más opciones de configuración que la opción **Creación rápida**. Por ejemplo, si desea conectar una máquina virtual a una red virtual, necesitará usar la opción **De la galería**.

> [AZURE.NOTE]También puede probar el [Portal de vista previa de Azure](https://portal.azure.com), más completo y personalizable, para crear una máquina virtual, automatizar la implementación de plantillas de aplicación para varias máquinas virtuales, usar características mejoradas de supervisión y diagnóstico de máquinas virtuales y mucho más. Las opciones de configuración de máquinas virtuales disponibles en los dos portales son muy similares pero no idénticas.

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-WindowsVM.md)]

## Pasos siguientes

- Inicie sesión en la máquina virtual. Para obtener instrucciones, consulte [Inicio de sesión en una máquina virtual con Windows Server](virtual-machines-log-on-windows-server.md).

- Acople un disco para almacenar los datos. Puede acoplar tanto discos vacíos como discos que contienen datos. Para obtener instrucciones, consulte el [tutorial Acoplamiento de un disco de datos a una máquina virtual de Windows](storage-windows-attach-disk.md).

## Recursos adicionales

Para obtener más información acerca de lo que se puede configurar para una máquina virtual y cuando se puede hacer, consulte [Acerca de los valores de configuración de la VM de Azure](http://msdn.microsoft.com/library/azure/dn763935.aspx).
 

<!---HONumber=July15_HO4-->