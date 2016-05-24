<properties
	pageTitle="Diferentes formas de crear una máquina virtual Linux | Microsoft Azure"
	description="Enumera las distintas formas de crear una máquina virtual Linux en Azure y proporciona vínculos a instrucciones adicionales."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="iainfoulds"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure-services"
	ms.date="04/12/2016"
	ms.author="iainfou"/>

# Diferentes formas de crear una máquina virtual de Linux con Resource Manager

Azure ofrece varias formas de crear una máquina virtual mediante el modelo de implementación de Resource Manager, que se adapte a distintos usuarios y objetivos. En este artículo se resumen estas diferencias y las opciones que tiene para crear las máquinas virtuales (VM) de Linux.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementación clásica.

## Opciones de herramienta

### Shell de comandos: CLI de Azure 

Desde la CLI, utilice la interfaz de la línea de comandos de Azure. Puede obtener más información sobre [cómo instalar la CLI de Azure](../xplat-cli-install.md) mediante npm, un contenedor de Docker o un script de instalación. Los siguientes tutoriales proporcionan ejemplos sobre el uso de la CLI de Azure:

* [Creación de una máquina virtual con Linux en Azure mediante la CLI](virtual-machines-linux-quick-create-cli.md) 

* [Creación de una VM de Linux protegida mediante una plantilla de Azure](virtual-machines-linux-create-ssh-secured-vm-from-template.md)

* [Creación de una máquina virtual con Linux desde cero con la CLI de Azure](virtual-machines-linux-create-cli-complete.md)

### GUI: Portal de Azure

La interfaz gráfica de usuario del [Portal de Azure](https://portal.azure.com) es una manera fácil de probar una máquina virtual, especialmente si no tiene experiencia con Azure, ya que no necesita instalar nada en el sistema. Use el Portal de Azure para crear la máquina virtual:

* [Creación de una máquina virtual con Linux en el Portal de Azure](virtual-machines-linux-portal-create.md) 

## Sistema operativo y opciones de imagen

En ambos métodos, puede elegir la imagen basada en el sistema operativo que desee ejecutar. Azure y sus socios ofrecen muchas imágenes, algunas de las cuales incluyen aplicaciones y herramientas preinstaladas. O bien, puede cargar una de sus propias imágenes.

### Imágenes de Azure

En todos los artículos anteriores, se puede usar fácilmente una imagen de Azure existente para crear una máquina virtual y personalizarla para la red, el equilibrio de carga y mucho más. El portal proporciona Azure Marketplace con imágenes suministradas por Azure. Puede obtener listas similares usando la línea de comandos. Por ejemplo, en la CLI de Azure, ejecute `azure vm image list` para ver una lista de todas las imágenes disponibles, por ubicación y publicador. Consulte [Navegación y selección de las imágenes de máquina virtual Linux en Azure con CLI o Powershell](virtual-machines-linux-cli-ps-findimage.md) para obtener ejemplos sobre la búsqueda y el uso de las imágenes disponibles.

### Uso de su propia imagen

Si necesita realizar cambios personalizados específicos, use una imagen basada en una máquina virtual de Azure existente *capturando* esa máquina virtual, o cargue una imagen suya almacenada en un disco duro virtual (VHD). Para más información sobre las distribuciones compatibles y cómo usar sus propias imágenes, consulte los artículos siguientes:

* [Distribuciones aprobadas por Azure](virtual-machines-linux-endorsed-distros.md)

* [Información para las distribuciones no aprobadas](virtual-machines-linux-create-upload-generic.md)

* [Captura de una máquina virtual de Linux para usarla como plantilla de Resource Manager](virtual-machines-linux-capture-image.md).

## Pasos siguientes

* Pruebe uno de los tutoriales para crear una máquina virtual de Linux desde el [Portal](virtual-machines-linux-portal-create.md), con la [CLI](virtual-machines-linux-quick-create-cli.md) o mediante una [plantilla](virtual-machines-linux-cli-deploy-templates.md) de Azure Resource Manager.

* Después de crear una máquina virtual de Linux, puede [agregar un disco de datos](virtual-machines-linux-add-disk.md) fácilmente.

* Pasos rápidos para [restablecer una contraseña o las claves SSH y administrar los usuarios](virtual-machines-linux-using-vmaccess-extension.md)

<!---HONumber=AcomDC_0518_2016-->