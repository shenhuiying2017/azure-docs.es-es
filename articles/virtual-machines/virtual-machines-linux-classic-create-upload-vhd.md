<properties
	pageTitle="Creación y carga de un VHD de Linux | Microsoft Azure"
	description="Cree y cargue un disco duro virtual de Azure (VHD) con el modelo de implementación clásica que contenga el sistema operativo Linux."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="iainfoulds"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/01/2016"
	ms.author="iainfou"/>

# Creación y carga de un disco duro virtual que contiene el sistema operativo Linux

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] También puede [cargar una imagen de disco personalizada mediante Azure Resource Manager](virtual-machines-linux-upload-vhd.md).

En este artículo se muestra cómo puede crear y cargar un disco duro virtual (VHD) que podrá utilizar como imagen propia para crear máquinas virtuales en Azure. Aprenda a preparar el sistema operativo que podrá utilizar para crear máquinas virtuales basadas en esa imagen.

>  [AZURE.NOTE] Si tiene unos momentos, ayúdenos a mejorar la documentación para máquinas virtuales Linux de Azure respondiendo a esta [encuesta rápida](https://aka.ms/linuxdocsurvey) sobre sus experiencias. Cada respuesta nos ayuda a facilitarle el trabajo.

## Requisitos previos
En este artículo se supone que tiene los siguientes elementos:

- **Sistema operativo Linux instalado en un archivo .vhd**: ha instalado una [distribución de Linux aprobada por Azure](virtual-machines-linux-endorsed-distros.md) (o consulte la [información para distribuciones no aprobadas](virtual-machines-linux-create-upload-generic.md)) en un disco virtual en el formato VHD. Existen varias herramientas para crear una máquina virtual y archivos VHD:
	- Instale y configure [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) o [KVM](http://www.linux-kvm.org/page/RunningKVM), teniendo cuidado de usar VHD como formato de imagen. Si es necesario, puede [convertir una imagen](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) con `qemu-img convert`.
	- También puede usar Hyper-V [en Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) o [en Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [AZURE.NOTE] el reciente formato VHDX no se admite en Azure. Al crear una máquina virtual, especifique un VHD como formato. Si es necesario, puede convertir discos VHDX a VHD mediante [`qemu-img convert`](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) o el cmdlet de PowerShell [`Convert-VHD`](https://technet.microsoft.com/library/hh848454.aspx). Además, Azure no permite cargar VHD dinámicos, por lo que tendrá que convertir estos discos a VHD estáticos antes de cargarlos. Puede usar herramientas como, por ejemplo, [Azure VHD Utilities for GO](https://github.com/Microsoft/azure-vhd-utils-for-go) para convertir discos dinámicos durante el proceso de carga en Azure.

- **Interfaz de la línea de comandos de Azure**: instale la [interfaz de la línea de comandos de Azure](../virtual-machines-command-line-tools.md) más reciente para cargar el archivo VHD.

<a id="prepimage"> </a>
## Paso 1: Preparación de la imagen que se va a cargar

Azure admite varias distribuciones Linux (consulte [Distribuciones aprobadas](virtual-machines-linux-endorsed-distros.md)). Los artículos siguientes le guiarán en el proceso de preparación de las distintas distribuciones de Linux admitidas en Azure. Tras completar los pasos en las siguientes guías, vuelva aquí en cuanto tenga un archivo VHD listo para cargarse en Azure:

- **[Distribuciones basadas en CentOS](virtual-machines-linux-create-upload-centos.md)**
- **[Debian Linux](virtual-machines-linux-debian-create-upload-vhd.md)**
- **[Oracle Linux](virtual-machines-linux-oracle-create-upload-vhd.md)**
- **[Red Hat Enterprise Linux](virtual-machines-linux-redhat-create-upload-vhd.md)**
- **[SLES y openSUSE](virtual-machines-linux-suse-create-upload-vhd.md)**
- **[Ubuntu](virtual-machines-linux-create-upload-ubuntu.md)**
- **[Otras distribuciones no aprobadas](virtual-machines-linux-create-upload-generic.md)**

> [AZURE.NOTE] El Acuerdo de Nivel de Servicio de la plataforma Azure se aplica a las máquinas virtuales que ejecutan el sistema operativo Linux solo cuando una de las distribuciones aprobadas se use con los detalles de la configuración según se indica en la sección "Versiones admitidas" en [Linux en distribuciones aprobadas por Azure](virtual-machines-linux-endorsed-distros.md). Todas las distribuciones de Linux en la galería de imágenes de Azure son distribuciones aprobadas con la configuración requerida.

Consulte también las **[notas de instalación de Linux](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes)** para más sugerencias generales sobre la preparación de imágenes de Linux para Azure.


<a id="connect"> </a>
## Paso 2: preparación de la conexión a Azure

Asegúrese de estar utilizando la CLI de Azure en el modelo de implementación clásica (`azure config mode asm`) y luego inicie sesión en su cuenta:

```
azure login
```


<a id="upload"> </a>
## Paso 3: cargar la imagen en Azure

Tiene que cargar el archivo VHD a una cuenta de almacenamiento. Puede seleccionar una cuenta de almacenamiento existente o [crear una nueva](../storage/storage-create-storage-account.md).

Use la CLI de Azure para cargar la imagen con el siguiente comando:

```bash
azure vm image create <ImageName> `
	--blob-url <BlobStorageURL>/<YourImagesFolder>/<VHDName> `
	--os Linux <PathToVHDFile>
```

En el ejemplo anterior:

- **BlobStorageURL** es la dirección URL de la cuenta de almacenamiento que pretende usar
- **SuCarpetaDeImágenes** es el contenedor de Almacenamiento de blobs en el que desea almacenar las imágenes
- **VHDName** es la etiqueta que aparece en el portal para identificar el disco duro virtual.
- **PathToVHDFile** es la ruta de acceso completa y el nombre del archivo .vhd de su máquina.

A continuación se muestra un ejemplo completo:

```bash
azure vm image create UbuntuLTS `
	--blob-url https://teststorage.blob.core.windows.net/vhds/UbuntuLTS.vhd `
	--os Linux /home/ahmet/UbuntuLTS.vhd
```

## Paso 4: Crear una VM desde la imagen
Cree una VM con `azure vm create` de la misma forma que una VM normal. Especifique el nombre que asignó a la imagen en el paso anterior. En el ejemplo siguiente, se utiliza el nombre de imagen **UbuntuLTS** determinado en el paso anterior:

```bash
azure vm create --userName ops --password P@ssw0rd! --vm-size Small --ssh `
	--location "West US" "DeployedUbuntu" UbuntuLTS
```

Para crear sus propias VM, proporcione su propio nombre de usuario + contraseña, la ubicación, el nombre DNS y el nombre de imagen.

## Pasos siguientes

Para obtener más información, consulte [Referencia de la CLI de Azure para el modelo de implementación clásica de Azure](../virtual-machines-command-line-tools.md).

[Step 1: Prepare the image to be uploaded]: #prepimage
[Step 2: Prepare the connection to Azure]: #connect
[Step 3: Upload the image to Azure]: #upload

<!---HONumber=AcomDC_0914_2016-->