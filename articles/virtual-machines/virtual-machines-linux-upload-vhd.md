<properties
	pageTitle="Creación y carga de un VHD de Linux | Microsoft Azure"
	description="Cree y cargue en Azure, un disco duro virtual (VHD) mediante el modelo de implementación de Resource Manager."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="iainfoulds"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/07/2016"
	ms.author="iainfou"/>

# Carga de un disco duro virtual

En este artículo se muestra cómo cargar un disco duro virtual (VHD) mediante el modelo de implementación de Resource Manager. Esta funcionalidad le permite instalar y configurar una distribución de Linux según sus requisitos y después utilizar ese disco duro virtual para crear rápidamente máquinas virtuales de Azure.

## Comandos rápidos
Asegúrese de que ha iniciado sesión en la [CLI de Azure](../xplat-cli-install.md) y que usa el modo de Resource Manager (`azure config mode arm`).

En primer lugar, crea un grupo de recursos:

```bash
azure group create TestRG --location "WestUS"
```

Cree una cuenta de almacenamiento para contener los discos virtuales:

```bash
azure storage account create testuploadedstorage --resource-group TestRG \
	--location "WestUS" --kind Storage --sku-name LRS
```

Enumere las claves de almacenamiento de la cuenta de almacenamiento que acaba de crear y tome nota de `key1`:

```bash
azure storage account keys list testuploadedstorage --resource-group TestRG
```

La salida debe ser similar a:

```
info:    Executing command storage account keys list
+ Getting storage account keys
data:    Name  Key                                                                                       Permissions
data:    ----  ----------------------------------------------------------------------------------------  -----------
data:    key1  d4XAvZzlGAgWdvhlWfkZ9q4k9bYZkXkuPCJ15NTsQOeDeowCDAdB80r9zA/tUINApdSGQ94H9zkszYyxpe8erw==  Full
data:    key2  Ww0T7g4UyYLaBnLYcxIOTVziGAAHvU+wpwuPvK4ZG0CDFwu/mAxS/YYvAQGHocq1w7/3HcalbnfxtFdqoXOw8g==  Full
info:    storage account keys list command OK
```

Cree un contenedor dentro de su cuenta de almacenamiento mediante la clave de almacenamiento obtenida:

```bash
azure storage container create --account-name testuploadedstorage \
	--account-key <key1> --container vm-images
```

Por último, cargue el VHD en el contenedor que acaba de crear:

```bash
azure storage blob upload --blobtype page --account-name testuploadedstorage \
	--account-key <key1> --container vm-images /path/to/disk/yourdisk.vhd
```

Ahora puede crear una máquina virtual desde el disco virtual cargado [mediante una plantilla de Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd) o a través de la CLI especificando el identificador URI en el disco de la forma siguiente:

```bash
azure vm create TestVM -l "WestUS" --resource-group TestRG \
	-Q https://testuploadedstorage.blob.core.windows.net/vm-images/yourdisk.vhd
```

Tenga en cuenta que todavía necesitará todos los parámetros adicionales requeridos por el comando `azure vm create`, como la red virtual, la dirección IP pública, el nombre de usuario y las claves SSH, entre otros. Puede leer más sobre los [parámetros disponibles de Resource Manager de la CLI](azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines).

## Requisitos
Para poder completar los pasos anteriores, necesitará:

- **Sistema operativo Linux instalado en un archivo .vhd**: instale una [distribución de Linux aprobada por Azure](virtual-machines-linux-endorsed-distros.md) (o consulte la [información para distribuciones no aprobadas](virtual-machines-linux-create-upload-generic.md)) en un disco virtual en el formato VHD. Existen varias herramientas para crear una máquina virtual y archivos VHD:
	- Instale y configure [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) o [KVM](http://www.linux-kvm.org/page/RunningKVM), teniendo cuidado de usar VHD como formato de imagen. También puede [convertir una imagen](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) con `qemu-img convert` si es necesario.
	- También puede usar Hyper-V [en Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) o [en Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [AZURE.NOTE] el reciente formato VHDX no se admite en Azure. Cuando crea una máquina virtual, especifique el formato VHD original. Si es necesario, puede convertir el formato VHDX a VHD mediante [`qemu-img convert`](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) o el cmdlet de PowerShell [`Convert-VHD`](https://technet.microsoft.com/library/hh848454.aspx). Además, Azure no permite cargar VHD dinámicos, por lo que tendrá que convertir estos discos a VHD estáticos antes de cargarlos. Puede usar herramientas como, por ejemplo, [Azure VHD Utilities for GO](https://github.com/Microsoft/azure-vhd-utils-for-go) para convertir discos dinámicos durante el proceso de carga en Azure.


<a id="prepimage"> </a>
## Preparar la imagen que se va a cargar

Azure admite varias distribuciones de Linux (consulte [Distribuciones aprobadas](virtual-machines-linux-endorsed-distros.md)). Los artículos siguientes le guiarán en el proceso de preparación de las distintas distribuciones de Linux admitidas en Azure. Asegúrese de preparar adecuadamente el disco virtual antes de cargarlo:

- **[Distribuciones basadas en CentOS](virtual-machines-linux-create-upload-centos.md)**
- **[Debian Linux](virtual-machines-linux-debian-create-upload-vhd.md)**
- **[Oracle Linux](virtual-machines-linux-oracle-create-upload-vhd.md)**
- **[Red Hat Enterprise Linux](virtual-machines-linux-redhat-create-upload-vhd.md)**
- **[SLES y openSUSE](virtual-machines-linux-suse-create-upload-vhd.md)**
- **[Ubuntu](virtual-machines-linux-create-upload-ubuntu.md)**
- **[Otras distribuciones no aprobadas](virtual-machines-linux-create-upload-generic.md)**

Consulte también las **[notas de instalación de Linux](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes)** para más sugerencias generales sobre la preparación de imágenes de Linux para Azure.

> [AZURE.NOTE] El [Acuerdo de Nivel de Servicio de la plataforma Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) se aplica a las máquinas virtuales que ejecutan Linux solo cuando una de las distribuciones aprobadas se use con los detalles de la configuración según se indica en la sección “Versiones admitidas” en [Linux en distribuciones aprobadas por Azure](virtual-machines-linux-endorsed-distros.md).

## Pasos siguientes
Después de haber preparado y cargado el disco virtual personalizado, puede leer más sobre [el uso de Resource Manager y plantillas](../resource-group-overview.md). También puede [agregar un disco de datos](virtual-machines-linux-add-disk.md) a las máquinas virtuales nuevas. Si tiene aplicaciones que se ejecutan en las máquinas virtuales a las que necesite acceder, asegúrese de [abrir puertos y puntos de conexión](virtual-machines-linux-nsg-quickstart.md).

<!---HONumber=AcomDC_0713_2016-->