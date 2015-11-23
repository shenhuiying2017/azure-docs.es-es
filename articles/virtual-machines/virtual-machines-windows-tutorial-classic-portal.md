<properties
	pageTitle="Creación de una máquina virtual que ejecuta Windows en Azure"
	description="Cree una máquina virtual de Windows en el Portal de Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/11/2015"
	ms.author="cynthn"/>

# Creación de una máquina virtual que ejecuta Windows en el Portal de Azure

> [AZURE.SELECTOR]
- [Azure preview portal](virtual-machines-windows-tutorial.md)
- [Azure portal](virtual-machines-windows-tutorial-classic-portal.md)
- [PowerShell: Resource Manager deployment](virtual-machines-deploy-rmtemplates-powershell.md)
- [PowerShell: Classic deployment](virtual-machines-ps-create-preconfigure-windows-vms.md)

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager deployment model](virtual-machines-windows-tutorial.md).

Este tutorial muestra lo fácil que resulta crear una máquina virtual (VM) de Azure en el Portal de Azure. Vamos a usar una imagen de Windows Server como ejemplo, pero es solo una de las muchas imágenes que Azure ofrece. Tenga en cuenta que las opciones de imagen dependen de su suscripción. Por ejemplo, las imágenes de escritorio pueden estar disponibles para los suscriptores MSDN.

También puede crear máquinas virtuales con sus [propias imágenes](virtual-machines-create-upload-vhd-windows-server.md). Para obtener más información sobre este y otros métodos, consulte [Diferentes formas de crear una máquina virtual de Windows](virtual-machines-windows-choices-create-vm.md).

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

## Tutorial en vídeo

Se trata del tutorial en formato de vídeo.

[AZURE.VIDEO creating-a-windows-vm-on-microsoft-azure-classic-portal]

## <a id="createvirtualmachine"> </a>Creación de la máquina virtual

En esta sección le mostraremos cómo usar la opción **Desde la galería** del Portal de Azure, para crear la máquina virtual. Esta opción proporciona más opciones de configuración que la opción **Creación rápida**. Por ejemplo, si desea conectar una máquina virtual a una red virtual, necesitará usar la opción **De la galería**.

> [AZURE.NOTE]Asimismo, también puede probar el Portal de vista previa de Azure, mucho más completo y personalizable, para crear una máquina virtual, usar características mejoradas de supervisión y diagnóstico, usar el almacenamiento Premium y mucho más. Las opciones disponibles para configurar máquinas virtuales en los dos portales se solapan considerablemente, pero no son idénticas. Por ejemplo, use el Portal de vista previa de Azure para configurar una máquina virtual con almacenamiento Premium.

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-windowsvm.md)]

## Pasos siguientes

- Iniciar sesión en la nueva máquina virtual. Para obtener instrucciones, consulte [Inicio de sesión en una máquina virtual que ejecuta Windows Server](virtual-machines-log-on-windows-server.md).

- Acople un disco para almacenar los datos. Puede acoplar tanto discos vacíos como discos que contienen datos. Para obtener instrucciones, consulte el [tutorial de acoplamiento de un disco de datos](storage-windows-attach-disk.md).

<!---HONumber=Nov15_HO3-->