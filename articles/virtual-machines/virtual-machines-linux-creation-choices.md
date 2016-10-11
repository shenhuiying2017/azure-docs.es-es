<properties
	pageTitle="Diferentes formas de crear una máquina virtual Linux | Microsoft Azure"
	description="Conozca las distintas formas de crear una máquina virtual Linux en Azure, y obtenga vínculos a herramientas y tutoriales para cada método."
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
	ms.date="09/27/2016"
	ms.author="iainfou"/>

# Diferentes formas de crear una máquina virtual Linux en Azure

En Azure, tiene la flexibilidad crear una máquina virtual Linux con las herramientas y los flujos de trabajo que le resulten cómodos. En este artículo se resumen estas diferencias y se ofrecen ejemplos para crear máquinas virtuales Linux.


## Azure CLI 

La CLI de Azure está disponible entre plataformas mediante un paquete de npm, paquetes proporcionados por la distribución o un contenedor de Docker. Puede leer más sobre [cómo instalar y configurar la CLI de Azure](../xplat-cli-install.md). Los siguientes tutoriales proporcionan ejemplos sobre el uso de la CLI de Azure. Lea cada artículo para más información sobre los comandos de inicio rápido de la CLI mostrados:

- [Creación de una máquina virtual con Linux en Azure mediante la CLI](virtual-machines-linux-quick-create-cli.md)
	- En el ejemplo siguiente se crea una máquina virtual de CoreOS con una clave pública llamada `azure_id_rsa.pub`:

	```bash
	azure vm quick-create -ssh-publickey-file ~/.ssh/azure_id_rsa.pub \
		--image-urn CoreOS
	```

- [Creación de una VM de Linux protegida mediante una plantilla de Azure](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
	- En el ejemplo siguiente se crea una máquina virtual mediante una plantilla almacenada en GitHub:

	```bash
	azure group create --name TestRG --location WestUS 
		--template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
	```

- [Creación de un entorno de Linux completo mediante la CLI de Azure](virtual-machines-linux-create-cli-complete.md)
	- Incluye la creación de un equilibrador de carga y varias máquinas virtuales en un conjunto de disponibilidad.

- [Adición de un disco a una máquina virtual de Linux](virtual-machines-linux-add-disk.md)
	- En el ejemplo siguiente se agrega un disco de 5 GB a una máquina virtual existente llamada `TestVM`:

	```bash
	azure vm disk attach-new --resource-group TestRG --vm-name TestVM \
		--size-in-GB 5
	```

## Portal de Azure

[Azure Portal](https://portal.azure.com) le permite crear rápidamente una máquina virtual porque no hay nada que instalar en el sistema. Use el Portal de Azure para crear la máquina virtual:

- [Creación de una máquina virtual Linux en Azure mediante el Portal](virtual-machines-linux-quick-create-portal.md)
- [Conexión de un disco mediante el Portal de Azure](virtual-machines-linux-attach-disk-portal.md)


## Sistema operativo y opciones de imagen
Al crear una máquina virtual, elija una imagen basada en el sistema operativo que desee ejecutar. Azure y sus socios ofrecen muchas imágenes, algunas de las cuales incluyen aplicaciones y herramientas preinstaladas. O bien, cargue una de sus propias imágenes (consulte [la siguiente sección](#use-your-own-image)).

### Imágenes de Azure
Puede usar los comandos de la CLI `azure vm image` para ver lo que hay disponible por publicador, versión de distribución y compilaciones.

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

Consulte [Selección de imágenes de máquinas virtuales con la CLI de Azure](virtual-machines-linux-cli-ps-findimage.md) para ver ejemplos sobre cómo buscar y usar las imágenes disponibles.

Los comandos `azure vm quick-create` y `azure vm create` tienen alias que se pueden usar para acceder rápidamente a las distribuciones más comunes y sus versiones más recientes. Usar alias es más fácil que tener que especificar el publicador, la oferta, el SKU y la versión cada vez que cree una máquina virtual:

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

Si necesita realizar cambios personalizados específicos, use una imagen basada en una máquina virtual de Azure existente; para ello, *capture* esa máquina virtual. También puede cargar una imagen creada de forma local. Para más información sobre las distribuciones compatibles y cómo usar sus propias imágenes, consulte los artículos siguientes:

- [Distribuciones aprobadas por Azure](virtual-machines-linux-endorsed-distros.md)

- [Información para las distribuciones no aprobadas](virtual-machines-linux-create-upload-generic.md)

- [Captura de una máquina virtual Linux para usarla como plantilla de Resource Manager](virtual-machines-linux-capture-image.md).
	- Comandos de ejemplo para capturar una máquina virtual existente:

	```bash
	azure vm deallocate --resource-group TestRG --vm-name TestVM
	azure vm generalize --resource-group TestRG --vm-name TestVM
	azure vm capture --resource-group TestRG --vm-name TestVM --vhd-name-prefix CapturedVM
	```

## Pasos siguientes

- Cree una máquina virtual Linux desde el [portal](virtual-machines-linux-quick-create-portal.md), con la [CLI](virtual-machines-linux-quick-create-cli.md) o mediante una [plantilla](virtual-machines-linux-cli-deploy-templates.md) de Azure Resource Manager.

- Después de crear una máquina virtual Linux, [agregue un disco de datos](virtual-machines-linux-add-disk.md).

- Pasos rápidos para [restablecer una contraseña o las claves SSH y administrar usuarios](virtual-machines-linux-using-vmaccess-extension.md)

<!---HONumber=AcomDC_1005_2016-->