---
title: "Carga de un disco de Linux personalizado con la CLI de Azure 2.0 (versión preliminar)| Microsoft Docs"
description: "Creación y carga en Azure de un disco duro virtual (VHD) mediante el modelo de implementación de Resource Manager y la CLI de Azure 2.0 (versión preliminar)"
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
ms.date: 02/02/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 25ca54a6514b281417ac71a89dac167c94137b18
ms.openlocfilehash: bbb9a2cd2123a29507f21c11b536ae81368cf8a7


---
# <a name="upload-and-create-a-linux-vm-from-custom-disk-by-using-the-azure-cli-20-preview"></a>Carga y creación de una máquina virtual Linux a partir de un disco personalizado mediante la CLI de Azure 2.0 (versión preliminar)
En este artículo se muestra cómo cargar un disco duro virtual (VHD) en Azure mediante el modelo de implementación de Resource Manager y cómo crear máquinas virtuales Linux desde este disco personalizado. Esta funcionalidad le permite instalar y configurar una distribución de Linux según sus requisitos y después utilizar ese disco duro virtual para crear rápidamente máquinas virtuales de Azure.


## <a name="cli-versions-to-complete-the-task"></a>Versiones de la CLI para completar la tarea
Puede completar la tarea mediante una de las siguientes versiones de la CLI:

- [CLI de Azure 1.0](virtual-machines-linux-upload-vhd-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json): la CLI para los modelos de implementación clásico y de Resource Manager
- [CLI de Azure 2.0 (versión preliminar)](#quick-commands): la CLI de última generación para el modelo de implementación de administración de recursos (este artículo)


## <a name="quick-commands"></a>Comandos rápidos
Si necesita realizar rápidamente la tarea, en la siguiente sección se detallan los comandos base para cargar un disco duro virtual en Azure. Se puede encontrar información más detallada y contexto para cada paso en el resto del documento, [comenzando aquí](#requirements).

Asegúrese de que ha instalado la última [CLI de Azure 2.0 (versión preliminar)](/cli/azure/install-az-cli2) y de que ha iniciado sesión en una cuenta de Azure con [az login](/cli/azure/#login).

En los ejemplos siguientes, reemplace los nombres de parámetros de ejemplo por los suyos propios. Nombres de parámetros de ejemplo incluidos `myResourceGroup`, `mystorageaccount` y `mydisks`.

En primer lugar, cree un grupo de recursos con [az group create](/cli/azure/group#create). En el ejemplo siguiente se crea un grupo de recursos denominado `myResourceGroup` en la ubicación `WestUs`:

```azurecli
az group create --name myResourceGroup --location westus
```

Cree una cuenta de almacenamiento que contenga los discos virtuales con [az storage account create](/cli/azure/storage/account#create). Incluso si desea usar [Introducción a Azure Managed Disks](../storage/storage-managed-disks-overview.md), debe crear una cuenta de almacenamiento en la que pueda cargar el disco duro virtual antes de realizar la conversión a un disco administrado. En el ejemplo siguiente se crea una cuenta de almacenamiento denominada `mystorageaccount`:

```azurecli
az storage account create --resource-group myResourceGroup --location westus \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

Cree una lista de las claves de acceso de la cuenta de almacenamiento con [az storage account keys list](/cli/azure/storage/account/keys#list). Tome nota de `key1`:

```azurecli
az storage account keys list --resource-group myResourceGroup --account-name mystorageaccount
```

Cree un contenedor en su cuenta de almacenamiento mediante la clave de almacenamiento que obtuvo con [az storage container create](/cli/azure/storage/container#create). En el ejemplo siguiente se crea un contenedor denominado `mydisks` con el valor de clave de almacenamiento de `key1`:

```azurecli
az storage container create --account-name mystorageaccount \
    --account-key key1 --name mydisks
```

Por último, cargue el disco duro virtual en el contenedor que creó con [az storage blob upload](/cli/azure/storage/blob#upload). Especifique la ruta de acceso local para el disco duro virtual en `/path/to/disk/mydisk.vhd`:

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 --container-name mydisks --type page \
    --file /path/to/disk/mydisk.vhd --name myDisk.vhd
```

### <a name="azure-managed-disks"></a>Azure Managed Disks
Puede crear una máquina virtual mediante Azure Managed Disks o mediante discos no administrados. Los discos administrados se controlan mediante la plataforma de Azure y no requieren preparativos ni ubicación para el almacenamiento. Para más información acerca de Azure Managed Disks, consulte [Introducción a los discos administrados de Azure](../storage/storage-managed-disks-overview.md). Para crear una máquina virtual desde el disco duro virtual, debe convertir primero el disco duro virtual en un disco administrado con [az disk create](/cli/azure/disk/create):

```azurecli
az disk create --resource-group myResourceGroup --name myManagedDisk \
  --source https://mystorageaccount.blob.core.windows.net/mydisks/myDisk.vhd
```

Obtenga el identificador URI del disco administrado que ha creado con [az disk list](/cli/azure/disk/list):

```azurecli
az disk list --resource-group myResourceGroup \
  --query '[].{Name:name,URI:creationData.sourceUri}' --output table
```

La salida es similar a la del ejemplo siguiente:

```azurecli
Name               URI
-----------------  ----------------------------------------------------------------------------------------------------
myUMDiskFromVHD    https://vhdstoragezw9.blob.core.windows.net/system/Microsoft.Compute/Images/vhds/my_image-osDisk.vhd
```

A continuación, cree la máquina virtual con [az vm create](/cli/azure/vm#create) y especifique el identificador URI del disco administrado (`--image`). En el ejemplo siguiente se crea una máquina virtual denominada `myVM` mediante el disco administrado creado a partir del disco duro virtual cargado:

```azurecli
az vm create --resource-group myResourceGroup --location westus \
    --name myVM --storage-account mystorageaccount --os-type linux \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --image https://vhdstoragezw9.blob.core.windows.net/system/Microsoft.Compute/Images/vhds/my_image-osDisk.vhd
```

### <a name="unmanaged-disks"></a>Discos no administrados
Para crear una máquina virtual con discos no administrados, especifique el URI en el disco (`--image`) con [az vm create](/cli/azure/vm#create). En el ejemplo siguiente se crea una máquina virtual denominada `myVM` con el disco virtual cargado anteriormente:

```azurecli
az vm create --resource-group myResourceGroup --location westus \
    --name myVM --storage-account mystorageaccount --os-type linux \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --image https://mystorageaccount.blob.core.windows.net/mydisk/myDisks.vhd
```

La cuenta de almacenamiento de destino debe ser la misma cuenta donde cargó su disco virtual. También debe especificar todos los parámetros adicionales requeridos, o responder a sus avisos, por el comando **az vm create**, como la red virtual, la dirección IP pública, el nombre de usuario y las claves SSH. Puede leer más sobre los [parámetros disponibles de Resource Manager de la CLI](azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines).

## <a name="requirements"></a>Requisitos
Para completar los pasos siguientes, necesita:

* **Sistema operativo Linux instalado en un archivo .vhd**: instale una [distribución de Linux aprobada por Azure](virtual-machines-linux-endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (o consulte la [información para distribuciones no aprobadas](virtual-machines-linux-create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)) en un disco virtual en el formato VHD. Existen varias herramientas para crear una máquina virtual y archivos VHD:
  * Instale y configure [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) o [KVM](http://www.linux-kvm.org/page/RunningKVM), teniendo cuidado de usar VHD como formato de imagen. Si es necesario, puede [convertir una imagen](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) con `qemu-img convert`.
  * También puede usar Hyper-V [en Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) o [en Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> el reciente formato VHDX no se admite en Azure. Al crear una máquina virtual, especifique un VHD como formato. Si es necesario, puede convertir discos VHDX a VHD mediante [`qemu-img convert`](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) o el cmdlet de PowerShell [`Convert-VHD`](https://technet.microsoft.com/library/hh848454.aspx). Además, Azure no permite cargar VHD dinámicos, por lo que tendrá que convertir estos discos a VHD estáticos antes de cargarlos. Puede usar herramientas como, por ejemplo, [Azure VHD Utilities for GO](https://github.com/Microsoft/azure-vhd-utils-for-go) para convertir discos dinámicos durante el proceso de carga en Azure.
> 
> 

* Las máquinas virtuales creadas desde el disco personalizado deben residir en la misma cuenta de almacenamiento que el propio disco.
  * Cree una cuenta de almacenamiento y un contenedor para almacenar el disco personalizado y las máquinas virtuales creadas.
  * Una vez que haya creado todas las máquinas virtuales, puede eliminar el disco de forma segura.

Asegúrese de que ha instalado la última [CLI de Azure 2.0 (versión preliminar)](/cli/azure/install-az-cli2) y de que ha iniciado sesión en una cuenta de Azure con [az login](/cli/azure/#login).

En los ejemplos siguientes, reemplace los nombres de parámetros de ejemplo por los suyos propios. Nombres de parámetros de ejemplo incluidos `myResourceGroup`, `mystorageaccount` y `mydisks`.

<a id="prepimage"> </a>

## <a name="prepare-the-disk-to-be-uploaded"></a>Preparación del disco que se va a cargar
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
Los grupos de recursos reúnen de forma lógica todos los recursos de Azure que admiten sus máquinas virtuales, como el almacenamiento y las redes virtuales. Para más información acerca de los grupos de recursos, consulte [Información general de Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). Antes de cargar el disco personalizado y crear máquinas virtuales, es preciso crear un grupo de recursos con [az group create](/cli/azure/group#create).

En el ejemplo siguiente se crea un grupo de recursos denominado `myResourceGroup` en la ubicación `westus`: [Introducción a Azure Managed Disks](../storage/storage-managed-disks-overview.md)
```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-a-storage-account"></a>Crear una cuenta de almacenamiento
Cuando vaya a crear una máquina virtual, puede hacerlo mediante Azure Managed Disks o mediante discos no administrados. Los discos administrados se controlan mediante la plataforma de Azure y no requieren preparativos ni ubicación para el almacenamiento. Los discos no administrados se almacenan como blobs en páginas dentro de una cuenta de almacenamiento. Para más información, consulte [Introducción a Azure Managed Disks](../storage/storage-managed-disks-overview.md) o [Azure Blob Storage aquí](../storage/storage-introduction.md#blob-storage). Incluso si desea usar Azure Managed Disks, debe crear una cuenta de almacenamiento en la que pueda cargar el disco duro virtual antes de realizar la conversión a un disco administrado.

Cree una cuenta de almacenamiento para el disco personalizado y las máquinas virtuales con [az storage account create](/cli/azure/storage/account#create). Todas las máquinas virtuales que cree con discos no administrados deben estar en la misma cuenta de almacenamiento que ese disco. Con los discos administrados, puede crear máquinas virtuales en cualquier grupo de recursos dentro de la suscripción.

En el ejemplo siguiente se crea una cuenta de almacenamiento denominada `mystorageaccount` en el grupo de recursos que creó anteriormente:

```azurecli
az storage account create --resource-group myResourceGroup --location westus \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

## <a name="list-storage-account-keys"></a>Enumerar claves de cuentas de almacenamiento
Azure genera dos claves de acceso de 512 bits para cada cuenta de almacenamiento. Estas claves de acceso se usan al autenticar en la cuenta de almacenamiento, como la realización de operaciones de escritura. Lea más sobre [administración del acceso al almacenamiento aquí](../storage/storage-create-storage-account.md#manage-your-storage-account). Vea las teclas de acceso con [az storage account keys list](/cli/azure/storage/account/keys#list).

Consulte las claves de acceso de la cuenta de almacenamiento que ha creado:

```azurecli
az storage account keys list --resource-group myResourceGroup --account-name mystorageaccount
```

La salida es parecida a esta:

```azurecli
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
De la misma manera que crea directorios distintos para organizar de forma lógica el sistema de archivos local, crea contenedores dentro de una cuenta de almacenamiento para organizar los discos. Una cuenta de almacenamiento puede contener un número cualquiera de contenedores. Cree un contenedor con [az storage container create](/cli/azure/storage/container#create).

En el ejemplo siguiente se crea un contenedor denominado `mydisks`, especificando la clave de acceso obtenida en el paso anterior (`key1`):

```azurecli
az storage container create --account-name mystorageaccount \
    --account-key key1 --name mydisks
```

## <a name="upload-vhd"></a>Carga de VHD
Ahora puede cargar su disco personalizado con [az storage blob upload](/cli/azure/storage/blob#upload). Cargue y almacene el disco personalizado como un blob en páginas.

Especifique la clave de acceso, el contenedor que creó en el paso anterior y, a continuación, la ruta de acceso al disco personalizado del equipo local:

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 --container-name mydisks --type page \
    --file /path/to/disk/mydisk.vhd --name myDisk.vhd
```

## <a name="create-vm-from-custom-disk"></a>Creación de una máquina virtual a partir de un disco personalizado
De nuevo, puede crear una máquina virtual mediante Azure Managed Disks o mediante discos no administrados. Para ambos tipos, especifique el identificador URI en el disco administrado o no administrado cuando cree una máquina virtual. En el caso de los discos no administrados, asegúrese de que la cuenta de almacenamiento de destino coincida con el lugar donde está almacenado el disco personalizado. Puede crear la máquina virtual mediante Azure 2.0 (versión preliminar) o la plantilla JSON de Resource Manager.

### <a name="azure-cli-20-preview---azure-managed-disks"></a>Azure CLI 2.0 (versión preliminar) - Azure Managed Disks
Para crear una máquina virtual desde el disco duro virtual, debe convertir primero el disco duro virtual en un disco administrado con [az disk create](/cli/azure/disk/create). En el ejemplo siguiente se crea un disco administrado llamado `myManagedDisk` a partir del disco duro virtual que cargó en su cuenta de almacenamiento y contenedor denominados:

```azurecli
az disk create --resource-group myResourceGroup --name myManagedDisk \
  --source https://mystorageaccount.blob.core.windows.net/mydisks/myDisk.vhd
```

Obtenga el identificador URI del disco administrado que ha creado con [az disk list](/cli/azure/disk/list):

```azurecli
az disk list --resource-group myResourceGroup \
  --query '[].{Name:name,URI:creationData.sourceUri}' --output table
```

La salida es similar a la del ejemplo siguiente:

```azurecli
Name               URI
-----------------  ----------------------------------------------------------------------------------------------------
myUMDiskFromVHD    https://vhdstoragezw9.blob.core.windows.net/system/Microsoft.Compute/Images/vhds/my_image-osDisk.vhd
```

A continuación, cree la máquina virtual con [az vm create](/cli/azure/vm#create) y especifique el identificador URI del disco administrado (`--image`). En el ejemplo siguiente se crea una máquina virtual denominada `myVM` mediante el disco administrado creado a partir del disco duro virtual cargado:

```azurecli
az vm create --resource-group myResourceGroup --location westus \
    --name myVM --storage-account mystorageaccount --os-type linux \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --image https://vhdstoragezw9.blob.core.windows.net/system/Microsoft.Compute/Images/vhds/my_image-osDisk.vhd
```

### <a name="azure-20-preview---unmanaged-disks"></a>Azure 2.0 (versión preliminar): discos no administrados
Para crear una máquina virtual con discos no administrados, especifique el URI en el disco (`--image`) con [az vm create](/cli/azure/vm#create). En el ejemplo siguiente se crea una máquina virtual denominada `myVM` con el disco virtual cargado anteriormente:

Especifique el parámetro `--image` con [az vm create](/cli/azure/vm#create) para apuntar al disco personalizado. Asegúrese de que `--storage-account` coincide con la cuenta de almacenamiento donde se almacena el disco personalizado. No es necesario utilizar el mismo contenedor que disco personalizado para almacenar las máquinas virtuales. Asegúrese de crear contenedores adicionales siguiendo los pasos anteriores antes de cargar el disco personalizado.

En el ejemplo siguiente se crea una máquina virtual denominada `myVM` a partir del disco personalizado:

```azurecli
az vm create --resource-group myResourceGroup --location westus \
    --name myVM --storage-account mystorageaccount --os-type linux \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --image https://mystorageaccount.blob.core.windows.net/mydisks/myDisk.vhd
```

También debe especificar todos los parámetros adicionales que requiere el comando **az vm create**, como el nombre de usuario y las claves SSH, así como responder a sus indicaciones.


### <a name="resource-manager-template---unmanaged-disks"></a>Plantilla de Azure Resource Manager: discos no administrados
Las plantillas de Azure Resource Manager son archivos de Notación de objetos JavaScript (JSON) que definen el entorno que desea crear. Las plantillas se desglosan en distintos proveedores de recursos, tales como proceso o red. Puede usar las plantillas existentes o escribir las suyas propias. Lea más sobre el [uso de Resource Manager y las plantillas](../azure-resource-manager/resource-group-overview.md).

Dentro del proveedor `Microsoft.Compute/virtualMachines` de la plantilla, tendrá un nodo `storageProfile` que contiene los detalles de configuración de la máquina virtual. Los dos parámetros principales para modificar son los URI `image` y `vhd` que apuntan al disco personalizado y el disco virtual de la nueva máquina virtual. A continuación se muestra un ejemplo de JSON para el uso de un disco personalizado:

```json
"storageProfile": {
          "osDisk": {
            "name": "myVM",
            "osType": "Linux",
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "image": {
              "uri": "https://mystorageaccount.blob.core.windows.net/mydisks/myDisk.vhd"
            },
            "vhd": {
              "uri": "https://mystorageaccount.blob.core.windows.net/vhds/newvmname.vhd"
            }
          }
```

Puede usar [esta plantilla existente para crear una máquina virtual desde una imagen personalizada](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image) o leer más sobre la [creación de sus propias plantillas de Azure Resource Manager](../resource-group-authoring-templates.md). 

Una vez que tenga una plantilla configurada, utilice [az group deployment create](/cli/azure/group/deployment#create) para crear las máquinas virtuales. Especifique el URI de la plantilla JSON con el parámetro `--template-uri` :

```azurecli
az group deployment create --resource-group myNewResourceGroup \
  --template-uri https://uri.to.template/mytemplate.json
```

Si tiene un archivo JSON almacenado localmente en el equipo, puede usar el parámetro `--template-file` en su lugar:

```azurecli
az group deployment create --resource-group myNewResourceGroup \
  --template-file /path/to/mytemplate.json
```


## <a name="next-steps"></a>Pasos siguientes
Después de haber preparado y cargado el disco virtual personalizado, puede leer más sobre el [uso de Resource Manager y las plantillas](../azure-resource-manager/resource-group-overview.md). También es posible que quiera [agregar un disco de datos](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) a las nuevas máquinas virtuales. Si tiene aplicaciones que se ejecutan en las máquinas virtuales a las que necesite tener acceso, asegúrese de [abrir puertos y puntos de conexión](virtual-machines-linux-nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).




<!--HONumber=Feb17_HO2-->


