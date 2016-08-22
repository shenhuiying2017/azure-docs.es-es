<properties
	pageTitle="Creación y carga de una imagen de Linux personalizada | Microsoft Azure"
	description="Cree y cargue en Azure un disco duro virtual (VHD) con una imagen de Linux personalizada mediante el modelo de implementación de Resource Manager."
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
	ms.date="07/15/2016"
	ms.author="iainfou"/>

# Carga y creación de una máquina virtual desde una imagen de disco personalizada

En este artículo se muestra cómo cargar un disco duro virtual (VHD) mediante el modelo de implementación de Resource Manager y crear máquinas virtuales desde esta imagen personalizada. Esta funcionalidad le permite instalar y configurar una distribución de Linux según sus requisitos y después utilizar ese disco duro virtual para crear rápidamente máquinas virtuales de Azure.

## Comandos rápidos
Asegúrese de que ha iniciado sesión en la [CLI de Azure](../xplat-cli-install.md) y que usa el modo de Resource Manager (`azure config mode arm`).

En primer lugar, crea un grupo de recursos:

```bash
azure group create TestRG --location "WestUS"
```

Cree una cuenta de almacenamiento para contener los discos virtuales:

```bash
azure storage account create testuploadedstorage --resource-group TestRG \
	--location "WestUS" --kind Storage --sku-name PLRS
```

Enumere las claves de acceso de la cuenta de almacenamiento que acaba de crear y tome nota de `key1`:

```bash
azure storage account keys list testuploadedstorage --resource-group TestRG
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

Tenga en cuenta que la cuenta de almacenamiento de destino debe ser la misma cuenta donde cargó su disco virtual. También debe especificar o responder a avisos para todos los parámetros adicionales requeridos por el comando `azure vm create`, como la red virtual, la dirección IP pública, el nombre de usuario y las claves SSH, entre otros. Puede leer más sobre los [parámetros disponibles de Resource Manager de la CLI](azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines).


## Pasos detallados
Hay una serie de pasos implicados en la preparación de su imagen de Linux personalizada y su carga en Azure. El resto de este artículo proporciona información más detallada sobre cada uno de los pasos que se indican en el conjunto de comandos rápidos anterior.


## Requisitos
Para poder completar los pasos anteriores, necesitará:

- **Sistema operativo Linux instalado en un archivo .vhd**: instale una [distribución de Linux aprobada por Azure](virtual-machines-linux-endorsed-distros.md) (o consulte la [información para distribuciones no aprobadas](virtual-machines-linux-create-upload-generic.md)) en un disco virtual en el formato VHD. Existen varias herramientas para crear una máquina virtual y archivos VHD:
	- Instale y configure [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) o [KVM](http://www.linux-kvm.org/page/RunningKVM), teniendo cuidado de usar VHD como formato de imagen. También puede [convertir una imagen](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) con `qemu-img convert` si es necesario.
	- También puede usar Hyper-V [en Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) o [en Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [AZURE.NOTE] el reciente formato VHDX no se admite en Azure. Al crear una máquina virtual, especifique un VHD como formato. Si es necesario, puede convertir discos VHDX a VHD mediante [`qemu-img convert`](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) o el cmdlet de PowerShell [`Convert-VHD`](https://technet.microsoft.com/library/hh848454.aspx). Además, Azure no permite cargar VHD dinámicos, por lo que tendrá que convertir estos discos a VHD estáticos antes de cargarlos. Puede usar herramientas como, por ejemplo, [Azure VHD Utilities for GO](https://github.com/Microsoft/azure-vhd-utils-for-go) para convertir discos dinámicos durante el proceso de carga en Azure.

- Las máquinas virtuales creadas desde su imagen personalizada deben residir en la misma cuenta de almacenamiento que la propia imagen.
	- Cree una cuenta de almacenamiento y un contenedor para almacenar su imagen personalizada y las máquinas virtuales creadas.
	- Una vez que haya creado todas las máquinas virtuales, puede eliminar su imagen de forma segura.


<a id="prepimage"> </a>
## Preparar la imagen que se va a cargar

Azure admite varias distribuciones de Linux (consulte [Distribuciones aprobadas](virtual-machines-linux-endorsed-distros.md)). Los artículos siguientes le guiarán en el proceso de preparación de las distintas distribuciones de Linux admitidas en Azure:

- **[Distribuciones basadas en CentOS](virtual-machines-linux-create-upload-centos.md)**
- **[Debian Linux](virtual-machines-linux-debian-create-upload-vhd.md)**
- **[Oracle Linux](virtual-machines-linux-oracle-create-upload-vhd.md)**
- **[Red Hat Enterprise Linux](virtual-machines-linux-redhat-create-upload-vhd.md)**
- **[SLES y openSUSE](virtual-machines-linux-suse-create-upload-vhd.md)**
- **[Ubuntu](virtual-machines-linux-create-upload-ubuntu.md)**
- **[Otras distribuciones no aprobadas](virtual-machines-linux-create-upload-generic.md)**

Consulte también las **[notas de instalación de Linux](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes)** para más sugerencias generales sobre la preparación de imágenes de Linux para Azure.

> [AZURE.NOTE] El [Acuerdo de Nivel de Servicio de la plataforma Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) se aplica a las máquinas virtuales que ejecutan Linux solo cuando una de las distribuciones aprobadas se use con los detalles de la configuración según se indica en la sección “Versiones admitidas” en [Linux en distribuciones aprobadas por Azure](virtual-machines-linux-endorsed-distros.md).


## Crear un grupo de recursos
Los grupos de recursos reúnen de forma lógica todos los recursos de Azure que admiten sus máquinas virtuales, como el almacenamiento y las redes virtuales. Lea más sobre [grupos de recursos de Azure aquí](../resource-group-overview.md). Antes de cargar la imagen de disco personalizada y crear máquinas virtuales, primero debe crear un grupo de recursos:

```bash
azure group create TestRG --location "WestUS"
```

## Crear una cuenta de almacenamiento
Las máquinas virtuales se almacenan como blobs en páginas dentro de una cuenta de almacenamiento. Lea más sobre [almacenamiento de blobs de Azure aquí](../storage/storage-introduction.md#blob-storage). Debe crear una cuenta de almacenamiento para la imagen de disco personalizada y las máquinas virtuales. Todas las máquinas virtuales que cree desde la imagen de disco personalizada deben estar en la misma cuenta de almacenamiento que esa imagen.

Cree una cuenta de almacenamiento dentro del grupo de recursos que acaba de crear:

```bash
azure storage account create testuploadedstorage --resource-group TestRG \
	--location "WestUS" --kind Storage --sku-name PLRS
```

## Enumerar claves de cuentas de almacenamiento
Azure genera dos claves de acceso de 512 bits para cada cuenta de almacenamiento. Estas claves de acceso se usan al autenticar en la cuenta de almacenamiento, como la realización de operaciones de escritura. Lea más sobre [administración del acceso al almacenamiento aquí](../storage/storage-create-storage-account.md#manage-your-storage-account). Puede ver claves de acceso con el comando `azure storage account keys list`.

Consulte las claves de acceso de la cuenta de almacenamiento que acaba de crear:

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
Tome nota de `key1`, pues el objetivo de su uso será interactuar con su cuenta de almacenamiento en los siguientes pasos.

## Creación de un contenedor de almacenamiento
De la misma manera que crea directorios distintos para organizar de forma lógica el sistema de archivos local, crea contenedores dentro de una cuenta de almacenamiento para organizar las imágenes de disco y los discos virtuales. Una cuenta de almacenamiento puede contener un número cualquiera de contenedores.

Cree un nuevo contenedor, especificando la clave de acceso obtenida en el paso anterior:

```bash
azure storage container create --account-name testuploadedstorage \
	--account-key <key1> --container vm-images
```

## Carga de VHD
Ahora, en realidad, puede cargar la imagen de disco personalizada. Al igual que con todos los discos virtuales usados por las máquinas virtuales, cargará y almacenará la imagen de disco personalizada como blob en páginas.

Deberá especificar su clave de acceso, el contenedor que creó en el paso anterior y, a continuación, la ruta de acceso a la imagen de disco personalizada del equipo local:

```bash
azure storage blob upload --blobtype page --account-name testuploadedstorage \
	--account-key <key1> --container vm-images /path/to/disk/yourdisk.vhd
```

## Creación de una máquina virtual a partir de una imagen personalizada
Al crear máquinas virtuales desde la imagen de disco personalizada, debe especificar el URI a la imagen de disco y asegurarse de que la cuenta de almacenamiento de destino coincide con la ubicación donde se almacena la imagen de disco personalizada. Puede crear la máquina virtual mediante la CLI de Azure o la plantilla JSON de Resource Manager.


### Creación de una máquina virtual con la CLI de Azure
Especifique el parámetro `--image-urn` (o simplemente `-Q`) con el comando `azure vm create` para que apunte a la imagen de disco personalizada. Asegúrese de que `--storage-account-name` (o `-o`) coincide con la cuenta de almacenamiento donde se almacena la imagen de disco personalizada. No es necesario usar el mismo contenedor que la imagen de disco personalizada para almacenar las máquinas virtuales. Basta con que se asegure de crear cualquier contenedor adicional de la misma manera que los pasos anteriores antes de cargar las imágenes de disco personalizadas.

Creación de una máquina virtual desde la imagen de disco personalizada:

```bash
azure vm create TestVM -l "WestUS" --resource-group TestRG \
	-Q https://testuploadedstorage.blob.core.windows.net/vm-images/yourdisk.vhd
	-o testuploadedstorage
```

Tenga en cuenta que aún deberá especificar o responder a avisos para todos los parámetros adicionales requeridos por el comando `azure vm create`, como la red virtual, la dirección IP pública, el nombre de usuario y las claves SSH, entre otros. Lea más sobre los [parámetros disponibles de Resource Manager de la CLI](azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines).

### Creación de una máquina virtual con una plantilla JSON
Las plantillas de Azure Resource Manager son archivos de Notación de objetos JavaScript (JSON) que definen el entorno que desea crear. Las plantillas se desglosan en distintos proveedores de recursos, tales como proceso o red. Puede usar las plantillas existentes o escribir las suyas propias. Lea más sobre el [uso de Resource Manager y plantillas](../resource-group-overview.md).

Dentro del proveedor `Microsoft.Compute/virtualMachines` de la plantilla, tendrá un nodo `storageProfile` que contiene los detalles de configuración para la máquina virtual. Los dos parámetros principales para modificar son los URI `image` y `vhd` que apuntan a la imagen de disco personalizada y el disco virtual de la nueva máquina virtual. A continuación se muestra un ejemplo de JSON para el uso de una imagen de disco personalizado:

```bash
"storageProfile": {
          "osDisk": {
            "name": "TestVM",
            "osType": "Linux",
            "caching": "ReadWrite",
			"createOption": "FromImage",
            "image": {
              "uri": "https://testuploadedstorage.blob.core.windows.net/vm-images/yourdisk.vhd"
            },
            "vhd": {
              "uri": "https://testuploadedstorage.blob.core.windows.net/vhds/newvmname.vhd"
            }
          }
```

Puede usar [esta plantilla existente para crear una máquina virtual desde una imagen personalizada](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image) o leer más sobre la [creación de sus propias plantillas de Azure Resource Manager](../resource-group-authoring-templates.md).

Una vez que tenga configurada una plantilla, cree las máquinas virtuales con el comando `azure group deployment create`. Especifique el URI de la plantilla JSON con el parámetro `--template-uri`:

```bash
azure group deployment create --resource-group TestTemplateRG
	--template-uri https://uri.to.template/yourtemplate.json
```

Si tiene un archivo JSON almacenado localmente en el equipo, puede usar el parámetro `--template-file` en su lugar:

```bash
azure group deployment create --resource-group TestTemplateRG
	--template-file /path/to/yourtemplate.json
```


## Pasos siguientes
Después de haber preparado y cargado el disco virtual personalizado, puede leer más sobre el [uso de Resource Manager y plantillas](../resource-group-overview.md). También es posible que quiera [agregar un disco de datos](virtual-machines-linux-add-disk.md) a las nuevas máquinas virtuales. Si tiene aplicaciones que se ejecutan en las máquinas virtuales a las que necesite tener acceso, asegúrese de [abrir puertos y puntos de conexión](virtual-machines-linux-nsg-quickstart.md).

<!---HONumber=AcomDC_0810_2016-->