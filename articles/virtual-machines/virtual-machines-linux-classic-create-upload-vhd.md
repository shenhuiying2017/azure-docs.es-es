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
	ms.date="06/14/2016"
	ms.author="iainfou"/>

# Creación y carga de un disco duro virtual que contiene el sistema operativo Linux

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

En este artículo se muestra cómo puede crear y cargar un disco duro virtual (VHD) que podrá utilizar como imagen propia para crear máquinas virtuales en Azure. Aprenderá a preparar el sistema operativo que podrá utilizar para crear máquinas virtuales basadas en esa imagen.

**Importante**: el contrato de nivel de servicio de la plataforma Azure se aplica a las máquinas virtuales que ejecutan el sistema operativo Linux solo cuando una de las distribuciones aprobadas se use con los detalles de la configuración según se indica en la sección “Versiones admitidas” en [Linux en distribuciones aprobadas por Azure](virtual-machines-linux-endorsed-distros.md). Todas las distribuciones de Linux en la galería de imágenes de Azure son distribuciones aprobadas con la configuración requerida.


## Requisitos previos
En este artículo se supone que tiene los siguientes elementos:

- **Sistema operativo Linux instalado en un archivo .vhd**: ha instalado una [distribución de Linux aprobada por Azure](virtual-machines-linux-endorsed-distros.md) (o consulte la [información para distribuciones no aprobadas](virtual-machines-linux-create-upload-generic.md)) en un disco virtual en el formato VHD. Existen varias herramientas para crear archivos .vhd; por ejemplo, puede utilizar una solución de virtualización como Hyper-V para crear el archivo .vhd e instalar el sistema operativo. Para obtener instrucciones, consulte [Instalación del rol de Hyper-V y configuración de una máquina Virtual](http://technet.microsoft.com/library/hh846766.aspx).

	> [AZURE.NOTE] el reciente formato VHDX no se admite en Azure. Puede convertir el formato VHDX a VHD mediante el Administrador de Hyper-V o el cmdlet `Convert-VHD`. Además, Azure no permite cargar VHD dinámicos, por lo que tendrá que convertir estos discos a VHD estáticos antes de cargarlos. Puede usar herramientas como, por ejemplo, [Azure VHD Utilities for GO](https://github.com/Microsoft/azure-vhd-utils-for-go) para convertir discos dinámicos.

- **Interfaz de la línea de comandos de Azure**: instale la [interfaz de la línea de comandos de Azure](../virtual-machines-command-line-tools.md) más reciente para cargar el archivo VHD.

<a id="prepimage"> </a>
## Paso 1: Preparación de la imagen que se va a cargar

Azure admite varias distribuciones de Linux (consulte [Distribuciones aprobadas](virtual-machines-linux-endorsed-distros.md)). Los artículos siguientes le guiarán en el proceso de preparación de las distintas distribuciones de Linux admitidas en Azure. Tras seguir los pasos de las guías enumeradas a continuación, vuelva aquí. Ya debería tener un archivo VHD listo para cargarse en Azure:

- **[Distribuciones basadas en CentOS](virtual-machines-linux-create-upload-centos.md)**
- **[Debian Linux](virtual-machines-linux-debian-create-upload-vhd.md)**
- **[Oracle Linux](virtual-machines-linux-oracle-create-upload-vhd.md)**
- **[Red Hat Enterprise Linux](virtual-machines-linux-redhat-create-upload-vhd.md)**
- **[SLES y openSUSE](virtual-machines-linux-suse-create-upload-vhd.md)**
- **[Ubuntu](virtual-machines-linux-create-upload-ubuntu.md)**
- **[Otras distribuciones no aprobadas](virtual-machines-linux-create-upload-generic.md)**

Consulte también las **[notas de instalación de Linux](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes)** para más sugerencias generales sobre la preparación de imágenes de Linux para Azure.


<a id="connect"> </a>
## Paso 2: preparación de la conexión a Azure

Asegúrese de estar utilizando la CLI de Azure en el modelo de implementación clásica (`azure config mode asm`) y luego inicie sesión en su cuenta:

```
azure login
```


<a id="upload"> </a>
## Paso 3: cargar la imagen en Azure

Tendrá que cargar el archivo VHD a una cuenta de almacenamiento. Puede elegir una existente o [crear una nueva](../storage/storage-create-storage-account.md).

Use la CLI de Azure para cargar la imagen con el siguiente comando:

		azure vm image create <ImageName> --blob-url <BlobStorageURL>/<YourImagesFolder>/<VHDName> --os Linux <PathToVHDFile>

En el ejemplo anterior:

- **BlobStorageURL** es la dirección URL de la cuenta de almacenamiento que pretende usar.
- **YourImagesFolder** es el contenedor de Almacenamiento de blobs donde desea almacenar las imágenes.
- **VHDName** es la etiqueta que aparece en el portal para identificar el disco duro virtual.
- **PathToVHDFile** es la ruta de acceso completa y el nombre del archivo .vhd de su máquina.

Para obtener más información, consulte [Referencia de la CLI de Azure para el Administrador de servicios de Azure](../virtual-machines-command-line-tools.md).

[Step 1: Prepare the image to be uploaded]: #prepimage
[Step 2: Prepare the connection to Azure]: #connect
[Step 3: Upload the image to Azure]: #upload

<!---HONumber=AcomDC_0629_2016-->