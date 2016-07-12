<properties
	pageTitle="Diferentes formas de crear una máquina virtual Linux | Microsoft Azure"
	description="Muestra las distintas formas de crear una máquina virtual con Linux en Azure, así como vínculos a herramientas y tutoriales para cada método."
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
	ms.date="07/06/2016"
	ms.author="iainfou"/>

# Diferentes formas de crear una máquina virtual de Linux con Resource Manager

Azure ofrece varias formas de crear una máquina virtual mediante el modelo de implementación de Resource Manager, que se adapte a distintos usuarios y objetivos. En este artículo se resumen estas diferencias y las opciones que tiene para crear las máquinas virtuales (VM) de Linux.

## CLI de Azure 

La CLI de Azure está disponible entre plataformas mediante un paquete de npm, paquetes proporcionados por la distribución o un contenedor de Docker. Puede leer más sobre [cómo instalar y configurar la CLI de Azure](../xplat-cli-install.md). Los siguientes tutoriales proporcionan ejemplos sobre el uso de la CLI de Azure. Lea cada artículo para más información sobre los comandos de inicio rápido de la CLI mostrados:

* [Creación de una máquina virtual con Linux en Azure mediante la CLI](virtual-machines-linux-quick-create-cli.md)

	```bash
	azure vm quick-create -M ~/.ssh/azure_id_rsa.pub -Q CoreOS
	```

* [Creación de una VM de Linux protegida mediante una plantilla de Azure](virtual-machines-linux-create-ssh-secured-vm-from-template.md)

	```bash
	azure group create --name TestRG --location WestUS 
		--template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
	```

* [Creación de una máquina virtual con Linux desde cero con la CLI de Azure](virtual-machines-linux-create-cli-complete.md)

* [Adición de un disco a una máquina virtual de Linux](virtual-machines-linux-add-disk.md)

	```bash
	azure vm disk attach-new --resource-group TestRG --vm-name TestVM <size-in-GB>
	```

## Portal de Azure

La interfaz gráfica de usuario del [Portal de Azure](https://portal.azure.com) es una manera fácil de probar una máquina virtual, especialmente si no tiene experiencia con Azure, ya que no necesita instalar nada en el sistema. Use el Portal de Azure para crear la máquina virtual:

* [Creación de una máquina virtual Linux en Azure mediante el Portal](virtual-machines-linux-quick-create-portal.md)
* [Conexión de un disco mediante el Portal de Azure](virtual-machines-linux-attach-disk-portal.md)

## Sistema operativo y opciones de imagen
Al crear una máquina virtual, elija una imagen basada en el sistema operativo que desee ejecutar. Azure y sus socios ofrecen muchas imágenes, algunas de las cuales incluyen aplicaciones y herramientas preinstaladas. Si lo prefiere, puede cargar una de sus propias imágenes (consulte a continuación).

### Imágenes de Azure
Puede usar los comandos de la CLI `azure vm image` para ver lo que está disponible por publicador, versión de distribución y compilaciones.

Lista de publicadores disponibles:

```bash
azure vm image list-publishers --location WestUS
```

Lista de productos disponibles (ofertas) de un publicador determinado:

```bash
azure vm image list-offers --location WestUS --publisher Canonical
```

Lista de SKU disponibles (versiones de distribución) de una oferta determinada:

```bash
azure vm image list-skus --location WestUS --publisher Canonical --offer UbuntuServer
```

Lista de todas las imágenes disponibles para una versión determinada:

```bash
azure vm image list --location WestUS --publisher Canonical --offer UbuntuServer --sku 16.04.0-LTS
```

Consulte [Navegación y selección de las imágenes de máquina virtual Linux en Azure con CLI o Powershell](virtual-machines-linux-cli-ps-findimage.md) para ver ejemplos sobre la búsqueda y el uso de las imágenes disponibles.

Los comandos `azure vm quick-create` y `azure vm create` también tienen algunos alias que se pueden utilizar para acceder rápidamente a las distribuciones más comunes y sus versiones más recientes. Esto es más fácil que tener especificar el publicador, la oferta, el SKU y la versión cada vez que cree una máquina virtual:

| Alias | Publicador | Oferta | SKU | Versión |
|:----------|:----------|:-------------|:------------|:--------|
| CentOS | OpenLogic | CentOS | 7,2 | más reciente |
| CoreOS | CoreOS | CoreOS | Stable | más reciente |
| Debian | credativ | Debian | 8 | más reciente |
| openSUSE | SUSE | openSUSE | 13\.2 | más reciente |
| RHEL | Redhat | RHEL | 7,2 | más reciente |
| SLES | SLES | SLES | 12-SP1 | más reciente |
| UbuntuLTS | Canonical | UbuntuServer | 14\.04.4-LTS | más reciente |

### Uso de su propia imagen

Si necesita realizar cambios personalizados específicos, use una imagen basada en una máquina virtual de Azure existente; para ello, *capture* esa máquina virtual o cargue una imagen suya almacenada en un disco duro virtual (VHD). Para más información sobre las distribuciones compatibles y cómo usar sus propias imágenes, consulte los artículos siguientes:

* [Distribuciones aprobadas por Azure](virtual-machines-linux-endorsed-distros.md)

* [Información para las distribuciones no aprobadas](virtual-machines-linux-create-upload-generic.md)

* [Captura de una máquina virtual Linux para usarla como plantilla de Resource Manager](virtual-machines-linux-capture-image.md). Comandos de inicio rápido:

	```bash
	azure vm deallocate --resource-group TestRG --vm-name TestVM
	azure vm generalize --resource-group TestRG --vm-name TestVM
	azure vm capture --resource-group TestRG --vm-name TestVM --vhd-name-prefix CapturedVM
	```

## Pasos siguientes

* Pruebe uno de los tutoriales para crear una máquina virtual Linux desde el [portal](virtual-machines-linux-quick-create-portal.md), con la [CLI](virtual-machines-linux-quick-create-cli.md) o mediante una [plantilla](virtual-machines-linux-cli-deploy-templates.md) de Azure Resource Manager.

* Después de crear una máquina virtual Linux, puede [agregar un disco de datos](virtual-machines-linux-add-disk.md) fácilmente.

* Pasos rápidos para [restablecer una contraseña o las claves SSH y administrar usuarios](virtual-machines-linux-using-vmaccess-extension.md)

<!---HONumber=AcomDC_0706_2016-->