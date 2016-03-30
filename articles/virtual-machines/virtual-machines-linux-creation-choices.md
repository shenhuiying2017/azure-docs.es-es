<properties
	pageTitle="Diferentes formas de crear una máquina virtual Linux | Microsoft Azure"
	description="Enumera las distintas formas de crear una máquina virtual Linux en Azure y proporciona vínculos a instrucciones adicionales."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure-services"
	ms.date="03/07/2016"
	ms.author="dkshir"/>

# Diferentes formas de crear una máquina virtual de Linux con Resource Manager

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementación clásica.

Azure ofrece varias formas de crear una VM que se adapte a distintos usuarios y objetivos. En este artículo se resumen estas diferencias y las opciones que tiene para crear las máquinas virtuales de Linux.

Recientemente se han incluido plantillas del Administrador de recursos de Azure como una manera de crear y administrar una máquina virtual y sus distintos recursos como una sola unidad de implementación lógica. Las instrucciones de este enfoque se incluyen a continuación, cuando sea posible. Para obtener más información acerca del Administrador de recursos de Azure y cómo administrar los recursos como una sola unidad, consulte la [Información general](../resource-group-overview.md).

## Opciones de herramienta

### GUI: Portal de Azure

La interfaz gráfica de usuario del [Portal de Azure](https://portal.azure.com) es una manera fácil de probar una máquina virtual, especialmente si no tiene experiencia con Azure. Use el Portal de Azure para crear la máquina virtual:

* [Creación de una máquina virtual con Linux en el Portal de Azure](virtual-machines-linux-portal-create.md) 

### Shell de comandos: CLI de Azure 

Si prefiere trabajar en un shell de comandos, use la interfaz de línea de comandos (CLI) de Azure para los usuarios de Mac, Linux y Windows.

Para la CLI de Azure, consulte los siguientes tutoriales:

* [Create a Linux VM from the CLI for dev and test](virtual-machines-linux-quick-create-cli.md) (Creación de una máquina virtual de Linux desde la CLI para desarrollo y pruebas) 

* [Creación de una VM de Linux protegida mediante una plantilla de Azure](virtual-machines-linux-create-ssh-secured-vm-from-template.md)

## Sistema operativo y opciones de imagen

Elija una imagen basada en el sistema operativo que desea ejecutar. Azure y sus socios ofrecen muchas imágenes, algunas de los cuales incluyen aplicaciones y herramientas. O bien, use una de sus propias imágenes.

### Imágenes de Azure

En todos los artículos anteriores, se puede usar fácilmente una imagen de Azure existente para crear una máquina virtual y personalizarla para la red, el equilibrio de carga y mucho más. El portal proporciona Azure Marketplace con imágenes suministradas por Azure. Puede obtener listas similares usando la línea de comandos. Por ejemplo, en la CLI de Azure, ejecute `azure vm image list` para obtener una lista de todas las imágenes disponibles, por ubicación y publicador. Consulte [Navigate and select Azure virtual machine images with Windows PowerShell and the Azure CLI](virtual-machines-linux-cli-ps-findimage.md) (Seleccione y navegue por imágenes de máquina virtual de Azure con PowerShell y la CLI de Azure).

### Uso de su propia imagen

Use una imagen basada en una máquina virtual de Azure existente *capturando* esa máquina virtual o cargue una imagen suya, almacenada en un disco duro virtual (VHD). Para más información, consulte:

* [Distribuciones aprobadas por Azure](virtual-machines-linux-endorsed-distros.md)

* [Información para las distribuciones no aprobadas](virtual-machines-linux-create-upload-generic.md)

* [Captura de una máquina virtual clásica con Linux para usarla como imagen](virtual-machines-linux-capture-image.md).

## Pasos siguientes

* Pruebe uno de los tutoriales para crear una máquina virtual de Linux desde el [portal](virtual-machines-linux-portal-create.md), con la [CLI](virtual-machines-linux-quick-create-cli.md) o mediante una [plantilla](virtual-machines-linux-cli-deploy-templates.md) de Azure Resource Manager.

* Después de crear una máquina virtual de Linux, puede [agregar un disco de datos](virtual-machines-linux-add-disk.md) fácilmente.

<!---HONumber=AcomDC_0323_2016-->