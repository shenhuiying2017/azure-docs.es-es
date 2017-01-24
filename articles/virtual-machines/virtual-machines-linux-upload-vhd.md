---
title: "Creación y carga de una imagen de Linux personalizada | Microsoft Docs"
description: "Cree y cargue en Azure un disco duro virtual (VHD) con una imagen de Linux personalizada mediante el modelo de implementación de Resource Manager."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: a8c7818f-eb65-409e-aa91-ce5ae975c564
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 9f944edcafb55634c9338bc8dd518a5f032f56e0
ms.openlocfilehash: edab3b3058a2e28ce74e62434b85b1643f267493


---
# <a name="upload-and-create-a-linux-vm-from-custom-disk-image"></a>Carga y creación de una máquina virtual Linux desde una imagen de disco personalizada
En este artículo se muestra cómo cargar un disco duro virtual (VHD) en Azure mediante el modelo de implementación de Resource Manager y crear máquinas virtuales Linux desde esta imagen personalizada. Esta funcionalidad le permite instalar y configurar una distribución de Linux según sus requisitos y después utilizar ese disco duro virtual para crear rápidamente máquinas virtuales de Azure.

## <a name="quick-commands"></a>Comandos rápidos
Si necesita realizar rápidamente la tarea, en la siguiente sección se detallan los comandos base para cargar una máquina virtual en Azure. Se puede encontrar información más detallada y contexto para cada paso en el resto del documento, [comenzando aquí](#requirements).

Asegúrese de haber iniciado sesión en la [CLI de Azure](../xplat-cli-install.md) y que usa el modo de Resource Manager:

```bash
azure config mode arm
```

En los ejemplos siguientes, reemplace los nombres de parámetros de ejemplo por los suyos propios. Nombres de parámetros de ejemplo incluidos `myResourceGroup`, `mystorageaccount` y `myimages`.

En primer lugar, cree un grupo de recursos. En el ejemplo siguiente se crea un grupo de recursos denominado `myResourceGroup` en la ubicación `WestUs`:

```bash
azure group create myResourceGroup --location "WestUS"
```

Cree una cuenta de almacenamiento para contener los discos virtuales. En el ejemplo siguiente se crea una cuenta de almacenamiento denominada `mystorageaccount`:

```bash
azure storage account create mystorageaccount --resource-group myResourceGroup \
    --location "WestUS" --kind Storage --sku-name PLRS
```

Enumere las claves de acceso de la cuenta de almacenamiento. Tome nota de `key1`:

```bash
azure storage account keys list mystorageaccount --resource-group myResourceGroup
```

Cree un contenedor dentro de su cuenta de almacenamiento mediante la clave de almacenamiento obtenida. En el ejemplo siguiente se crea un contenedor denominado `myimages` con el valor de clave de almacenamiento de `key1`:

```bash
azure storage container create --account-name mystorageaccount \
    --account-key key1 --container myimages
```

Por último, cargue el disco duro virtual en el contenedor que ha creado: Especifique la ruta de acceso local para el disco duro virtual en `/path/to/disk/mydisk.vhd`:

```bash
azure storage blob upload --blobtype page --account-name mystorageaccount \
    --account-key key1 --container myimages /path/to/disk/mydisk.vhd
```

Ahora puede crear una máquina virtual desde el disco virtual cargado [mediante plantillas de Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd). También puede usar la CLI especificando el URI para el disco (`--image-urn`: En el ejemplo siguiente se crea una máquina virtual denominada `myVM` con el disco virtual cargado anteriormente:

```bash
azure vm create myVM -l "WestUS" --resource-group myResourceGroup \
    --image-urn https://mystorageaccount.blob.core.windows.net/myimages/mydisk.vhd
```

La cuenta de almacenamiento de destino debe ser la misma cuenta donde cargó su disco virtual. También debe especificar o responder a avisos para todos los parámetros adicionales requeridos por el comando `azure vm create` , como la red virtual, la dirección IP pública, el nombre de usuario y las claves SSH, entre otros. Puede leer más sobre los [parámetros disponibles de Resource Manager de la CLI](azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines).

## <a name="requirements"></a>Requisitos
Para completar los pasos siguientes, necesita:

* **Sistema operativo Linux instalado en un archivo .vhd**: instale una [distribución de Linux aprobada por Azure](virtual-machines-linux-endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (o consulte la [información para distribuciones no aprobadas](virtual-machines-linux-create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)) en un disco virtual en el formato VHD. Existen varias herramientas para crear una máquina virtual y archivos VHD:
  * Instale y configure [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) o [KVM](http://www.linux-kvm.org/page/RunningKVM), teniendo cuidado de usar VHD como formato de imagen. Si es necesario, puede [convertir una imagen](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) con `qemu-img convert`.
  * También puede usar Hyper-V [en Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) o [en Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> el reciente formato VHDX no se admite en Azure. Al crear una máquina virtual, especifique un VHD como formato. Si es necesario, puede convertir discos VHDX a VHD mediante [`qemu-img convert`](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) o el cmdlet de PowerShell [`Convert-VHD`](https://technet.microsoft.com/library/hh848454.aspx). Además, Azure no permite cargar VHD dinámicos, por lo que tendrá que convertir estos discos a VHD estáticos antes de cargarlos. Puede usar herramientas como, por ejemplo, [Azure VHD Utilities for GO](https://github.com/Microsoft/azure-vhd-utils-for-go) para convertir discos dinámicos durante el proceso de carga en Azure.
> 
> 

* Las máquinas virtuales creadas desde su imagen personalizada deben residir en la misma cuenta de almacenamiento que la propia imagen.
  * Cree una cuenta de almacenamiento y un contenedor para almacenar su imagen personalizada y las máquinas virtuales creadas.
  * Una vez que haya creado todas las máquinas virtuales, puede eliminar su imagen de forma segura.

Asegúrese de haber iniciado sesión en la [CLI de Azure](../xplat-cli-install.md) y que usa el modo de Resource Manager:

```bash
azure config mode arm
```

En los ejemplos siguientes, reemplace los nombres de parámetros de ejemplo por los suyos propios. Nombres de parámetros de ejemplo incluidos `myResourceGroup`, `mystorageaccount` y `myimages`.

<a id="prepimage"> </a>

## <a name="prepare-the-image-to-be-uploaded"></a>Preparar la imagen que se va a cargar
Azure admite varias distribuciones Linux (consulte [Distribuciones aprobadas](virtual-machines-linux-endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). Los artículos siguientes le guiarán en el proceso de preparación de las distintas distribuciones de Linux admitidas en Azure:

* **[Distribuciones basadas en CentOS](virtual-machines-linux-create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Debian Linux](virtual-machines-linux-debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Oracle Linux](virtual-machines-linux-oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Red Hat Enterprise Linux](virtual-machines-linux-redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[SLES y openSUSE](virtual-machines-linux-suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ubuntu](virtual-machines-linux-create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Otras distribuciones no aprobadas](virtual-machines-linux-create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

Consulte también las **[notas de instalación de Linux](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes)** para más sugerencias generales sobre la preparación de imágenes de Linux para Azure.

> [!NOTE]
> El [Acuerdo de Nivel de Servicio de la plataforma Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) se aplica a las máquinas virtuales que ejecutan Linux solo cuando una de las distribuciones aprobadas se use con los detalles de la configuración según se indica en la sección "Versiones admitidas" en [Linux en distribuciones aprobadas por Azure](virtual-machines-linux-endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

## <a name="create-a-resource-group"></a>Crear un grupo de recursos
Los grupos de recursos reúnen de forma lógica todos los recursos de Azure que admiten sus máquinas virtuales, como el almacenamiento y las redes virtuales. Lea más sobre [grupos de recursos de Azure aquí](../azure-resource-manager/resource-group-overview.md). Antes de cargar la imagen de disco personalizada y crear máquinas virtuales, primero debe crear un grupo de recursos: 

En el ejemplo siguiente se crea un grupo de recursos denominado `myResourceGroup` en la ubicación `WestUS`:

```bash
azure group create myResourceGroup --location "WestUS"
```

## <a name="create-a-storage-account"></a>Crear una cuenta de almacenamiento
Las máquinas virtuales se almacenan como blobs en páginas dentro de una cuenta de almacenamiento. Lea más sobre [almacenamiento de blobs de Azure aquí](../storage/storage-introduction.md#blob-storage). Creará una cuenta de almacenamiento para la imagen de disco personalizada y las máquinas virtuales. Todas las máquinas virtuales que cree desde la imagen de disco personalizada deben estar en la misma cuenta de almacenamiento que esa imagen.

En el ejemplo siguiente se crea una cuenta de almacenamiento denominada `mystorageaccount` en el grupo de recursos que creó anteriormente:

```bash
azure storage account create mystorageaccount --resource-group myResourceGroup \
    --location "WestUS" --kind Storage --sku-name PLRS
```

## <a name="list-storage-account-keys"></a>Enumerar claves de cuentas de almacenamiento
Azure genera dos claves de acceso de 512 bits para cada cuenta de almacenamiento. Estas claves de acceso se usan al autenticar en la cuenta de almacenamiento, como la realización de operaciones de escritura. Lea más sobre [administración del acceso al almacenamiento aquí](../storage/storage-create-storage-account.md#manage-your-storage-account). Puede ver claves de acceso con el comando `azure storage account keys list` .

Consulte las claves de acceso de la cuenta de almacenamiento que ha creado:

```bash
azure storage account keys list mystorageaccount --resource-group myResourceGroup
```

La salida es parecida a esta:

```
info:    Executing command storage account keys list
+ Getting storage account keys
data:    Name  Key                                                                                       Permissions
data:    ----  ----------------------------------------------------------------------------------------  -----------
data:    key1  d4XAvZzlGAgWdvhlWfkZ9q4k9bYZkXkuPCJ15NTsQOeDeowCDAdB80r9zA/tUINApdSGQ94H9zkszYyxpe8erw==  Full
data:    key2  Ww0T7g4UyYLaBnLYcxIOTVziGAAHvU+wpwuPvK4ZG0CDFwu/mAxS/YYvAQGHocq1w7/3HcalbnfxtFdqoXOw8g==  Full
info:    storage account keys list command OK

```
Tome nota de `key1` , pues el objetivo de su uso será interactuar con su cuenta de almacenamiento en los siguientes pasos.

## <a name="create-a-storage-container"></a>Creación de un contenedor de almacenamiento
De la misma manera que crea directorios distintos para organizar de forma lógica el sistema de archivos local, crea contenedores dentro de una cuenta de almacenamiento para organizar los las imágenes y los discos virtuales. Una cuenta de almacenamiento puede contener un número cualquiera de contenedores. 

En el ejemplo siguiente se crea un contenedor denominado `myimages`, especificando la clave de acceso obtenida en el paso anterior (`key1`):

```bash
azure storage container create --account-name mystorageaccount \
    --account-key key1 --container myimages
```

## <a name="upload-vhd"></a>Carga de VHD
Ahora, en realidad, puede cargar la imagen de disco personalizada. Al igual que con todos los discos virtuales usados por las máquinas virtuales, cargará y almacenará la imagen de disco personalizada como un blob en páginas.

Especifique la clave de acceso, el contenedor que creó en el paso anterior y, a continuación, la ruta de acceso a la imagen de disco personalizada del equipo local:

```bash
azure storage blob upload --blobtype page --account-name mystorageaccount \
    --account-key key1 --container myimages /path/to/disk/mydisk.vhd
```

## <a name="create-vm-from-custom-image"></a>Creación de una máquina virtual a partir de una imagen personalizada
Al crear máquinas virtuales a partir de la imagen de disco personalizada, especifique el identificador URI de la imagen de disco. Asegúrese de que la cuenta de almacenamiento de destino coincida con el lugar donde está almacenada la imagen de disco personalizada. Puede crear la máquina virtual mediante la CLI de Azure o la plantilla JSON de Resource Manager.

### <a name="create-a-vm-using-the-azure-cli"></a>Creación de una máquina virtual con la CLI de Azure
Especifique el parámetro `--image-urn` con el comando `azure vm create` para apuntar a la imagen de disco personalizada. Asegúrese de que `--storage-account-name` coincide con la cuenta de almacenamiento donde se almacena la imagen de disco personalizada. No es necesario utilizar el mismo contenedor que la imagen de disco personalizada para almacenar las máquinas virtuales. Asegúrese de crear contenedores adicionales siguiendo los pasos anteriores antes de cargar las imágenes de disco personalizadas.

En el ejemplo siguiente se crea una máquina virtual denominada `myVM` a partir de la imagen de disco personalizada:

```bash
azure vm create myVM -l "WestUS" --resource-group myResourceGroup \
    --image-urn https://mystorageaccount.blob.core.windows.net/myimages/mydisk.vhd
    --storage-account-name mystorageaccount
```

También debe especificar o responder a los mensajes de todos los parámetros adicionales requeridos por el comando `azure vm create` , como la red virtual, la dirección IP pública, el nombre de usuario y las claves SSH, entre otros. Lea más sobre los [parámetros disponibles de Resource Manager de la CLI](azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines).

### <a name="create-a-vm-using-a-json-template"></a>Creación de una máquina virtual con una plantilla JSON
Las plantillas de Azure Resource Manager son archivos de Notación de objetos JavaScript (JSON) que definen el entorno que desea crear. Las plantillas se desglosan en distintos proveedores de recursos, tales como proceso o red. Puede usar las plantillas existentes o escribir las suyas propias. Lea más sobre el [uso de Resource Manager y las plantillas](../azure-resource-manager/resource-group-overview.md).

Dentro del proveedor `Microsoft.Compute/virtualMachines` de la plantilla, tendrá un nodo `storageProfile` que contiene los detalles de configuración de la máquina virtual. Los dos parámetros principales para modificar son los URI `image` y `vhd` que apuntan a la imagen de disco personalizada y el disco virtual de la nueva máquina virtual. A continuación se muestra un ejemplo de JSON para el uso de una imagen de disco personalizado:

```bash
"storageProfile": {
          "osDisk": {
            "name": "myVM",
            "osType": "Linux",
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "image": {
              "uri": "https://mystorageaccount.blob.core.windows.net/myimages/mydisk.vhd"
            },
            "vhd": {
              "uri": "https://mystorageaccount.blob.core.windows.net/vhds/newvmname.vhd"
            }
          }
```

Puede usar [esta plantilla existente para crear una máquina virtual desde una imagen personalizada](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image) o leer más sobre la [creación de sus propias plantillas de Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md). 

Una vez que tenga configurada una plantilla, cree las máquinas virtuales con el comando `azure group deployment create` . Especifique el URI de la plantilla JSON con el parámetro `--template-uri` :

```bash
azure group deployment create --resource-group myResourceGroup
    --template-uri https://uri.to.template/mytemplate.json
```

Si tiene un archivo JSON almacenado localmente en el equipo, puede usar el parámetro `--template-file` en su lugar:

```bash
azure group deployment create --resource-group myResourceGroup
    --template-file /path/to/mytemplate.json
```


## <a name="next-steps"></a>Pasos siguientes
Después de haber preparado y cargado el disco virtual personalizado, puede leer más sobre el [uso de Resource Manager y las plantillas](../azure-resource-manager/resource-group-overview.md). También es posible que quiera [agregar un disco de datos](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) a las nuevas máquinas virtuales. Si tiene aplicaciones que se ejecutan en las máquinas virtuales a las que necesite tener acceso, asegúrese de [abrir puertos y puntos de conexión](virtual-machines-linux-nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).




<!--HONumber=Jan17_HO1-->


