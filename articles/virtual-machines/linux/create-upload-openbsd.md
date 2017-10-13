---
title: "Creación y carga de una imagen de máquina virtual de OpenBSD en Azure | Microsoft Docs"
description: "Aprenda a crear y cargar un disco duro virtual (VHD) que contenga el sistema operativo OpenBSD para crear una máquina virtual de Azure mediante la CLI de Azure."
services: virtual-machines-linux
documentationcenter: 
author: thomas1206
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 1ef30f32-61c1-4ba8-9542-801d7b18e9bf
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/24/2017
ms.author: huishao
ms.openlocfilehash: 9b4163471f3dc8483993b9ac762694af4e926aa0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-upload-an-openbsd-disk-image-to-azure"></a>Creación y carga de una imagen de máquina virtual de OpenBSD en Azure
En este artículo se muestra cómo crear y cargar un disco duro virtual (VHD) que contenga el sistema operativo OpenBSD. Después de cargarlo, puede utilizarlo como su propia imagen para crear una máquina virtual (VM) en Azure a través de la CLI de Azure.


## <a name="prerequisites"></a>Requisitos previos
En este artículo se supone que tiene los siguientes elementos:

* **Una suscripción de Azure**: si no tiene una cuenta, puede crear una en un par de minutos. Si tiene una suscripción a MSDN, consulte [Crédito mensual de Azure para suscriptores de Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). De lo contrario, obtenga información sobre cómo [crear una cuenta de prueba gratuita](https://azure.microsoft.com/pricing/free-trial/).  
* **Azure CLI 2.0**: asegúrese de que ha instalado la última versión de la [CLI de Azure 2.0](/cli/azure/install-azure-cli) y de que ha iniciado sesión en una cuenta de Azure con [az login](/cli/azure/#login).
* **Sistema operativo OpenBSD instalado en un archivo .vhd**: se debe instalar un sistema operativo OpenBSD compatible (versión 6.1) en un disco duro virtual. Existen varias herramientas para crear archivos .vhd. Por ejemplo, puede utilizar una solución de virtualización como Hyper-V para crear el archivo .vhd e instalar el sistema operativo. Para obtener instrucciones sobre cómo instalar y utilizar Hyper-V, consulte [Instalar Hyper-V y crear una máquina virtual](http://technet.microsoft.com/library/hh846766.aspx).


## <a name="prepare-openbsd-image-for-azure"></a>Preparación de la imagen de OpenBSD para Azure
En la máquina virtual donde se instaló el sistema operativo OpenBSD 6.1, que agrega compatibilidad con Hyper-V, complete los procedimientos siguientes:

1. Si DHCP no está habilitado durante la instalación, habilite el servicio como se indica a continuación:

    ```sh    
    echo dhcp > /etc/hostname.hvn0
    ```

2. Configure una consola de serie como sigue:

    ```sh
    echo "stty com0 115200" >> /etc/boot.conf
    echo "set tty com0" >> /etc/boot.conf
    ```

3. Configure la instalación del paquete como sigue:

    ```sh
    echo "https://ftp.openbsd.org/pub/OpenBSD" > /etc/installurl
    ```
   
4. De forma predeterminada, el usuario `root` está deshabilitado en las máquinas virtuales en Azure. Los usuarios pueden ejecutar comandos con privilegios elevados mediante el comando `doas` en la máquina virtual OpenBSD. El comando doas está habilitado de manera predeterminada. Para más información, consulte [doas.conf](http://man.openbsd.org/doas.conf.5). 

5. Instale y configure los requisitos previos para el agente de Azure como sigue:

    ```sh
    pkg_add py-setuptools openssl git
    ln -sf /usr/local/bin/python2.7 /usr/local/bin/python
    ln -sf /usr/local/bin/python2.7-2to3 /usr/local/bin/2to3
    ln -sf /usr/local/bin/python2.7-config /usr/local/bin/python-config
    ln -sf /usr/local/bin/pydoc2.7  /usr/local/bin/pydoc
    ```

6. Siempre puede encontrar la versión más reciente del agente de Azure en [Github](https://github.com/Azure/WALinuxAgent/releases). Instale el agente de la forma siguiente:

    ```sh
    git clone https://github.com/Azure/WALinuxAgent 
    cd WALinuxAgent
    python setup.py install
    waagent -register-service
    ```

    > [!IMPORTANT]
    > Después de instalar el agente de Azure, es recomendable comprobar que está ejecutándose:
    >
    > ```bash
    > ps auxw | grep waagent
    > root     79309  0.0  1.5  9184 15356 p1  S      4:11PM    0:00.46 python /usr/local/sbin/waagent -daemon (python2.7)
    > cat /var/log/waagent.log
    > ```

7. Desaprovisione el sistema para limpiarlo y dejarlo adecuado para el reaprovisionamiento. El comando siguiente también elimina la última cuenta de usuario aprovisionada y los datos asociados:

    ```sh
    waagent -deprovision+user -force
    ```

Ahora ya puede apagar la máquina virtual.


## <a name="prepare-the-vhd"></a>Preparación de VHD
El formato VHDX no se admite en Azure, solo el **VHD fijo**. Puede convertir el disco al formato VHD fijo con el Administrador de Hyper-V o el cmdlet [convert-vhd](https://technet.microsoft.com/itpro/powershell/windows/hyper-v/convert-vhd) de Powershell. A continuación se muestra un ejemplo.

```powershell
Convert-VHD OpenBSD61.vhdx OpenBSD61.vhd -VHDType Fixed
```

## <a name="create-storage-resources-and-upload"></a>Creación de recursos de almacenamiento y de carga
En primer lugar, cree un grupo de recursos con [az group create](/cli/azure/group#create). En el ejemplo siguiente, se crea un grupo de recursos denominado *myResourceGroup* en la ubicación *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Para cargar el VHD, cree una cuenta de almacenamiento con el comando [az storage account create](/cli/azure/storage/account#create). Los nombres de la cuenta de almacenamiento deben ser únicos, así que indique su propio nombre. En el ejemplo siguiente se crea una cuenta de almacenamiento denominada *mystorageaccount*:

```azurecli
az storage account create --resource-group myResourceGroup \
    --name mystorageaccount \
    --location eastus \
    --sku Premium_LRS
```

Para controlar el acceso a la cuenta de almacenamiento, obtenga la clave de almacenamiento mediante el comando [az storage account keys list](/cli/azure/storage/account/keys#list), como se indica a continuación:

```azurecli
STORAGE_KEY=$(az storage account keys list \
    --resource-group myResourceGroup \
    --account-name mystorageaccount \
    --query "[?keyName=='key1']  | [0].value" -o tsv)
```

Para separar lógicamente los discos duros virtuales que ha cargado, cree un contenedor dentro de la cuenta de almacenamiento con el comando [az storage container create](/cli/azure/storage/container#create):

```azurecli
az storage container create \
    --name vhds \
    --account-name mystorageaccount \
    --account-key ${STORAGE_KEY}
```

Por último, cargue el VHD con el comando [az storage blob upload](/cli/azure/storage/blob#upload) como se indica a continuación:

```azurecli
az storage blob upload \
    --container-name vhds \
    --file ./OpenBSD61.vhd \
    --name OpenBSD61.vhd \
    --account-name mystorageaccount \
    --account-key ${STORAGE_KEY}
```


## <a name="create-vm-from-your-vhd"></a>Creación de una máquina virtual desde el disco duro virtual
Puede crear una máquina virtual con un [script de ejemplo](../scripts/virtual-machines-linux-cli-sample-create-vm-vhd.md) o directamente con [az vm create](/cli/azure/vm#create). Para especificar el VHD de OpenBSD cargado, utilice el parámetro `--image` como sigue:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myOpenBSD61 \
    --image "https://mystorageaccount.blob.core.windows.net/vhds/OpenBSD61.vhd" \
    --os-type linux \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

Obtenga la dirección IP de la máquina virtual de OpenBSD con el comando [az vm list-ip-addresses](/cli/azure/vm#list-ip-addresses) como se indica a continuación:

```azurecli
az vm list-ip-addresses --resource-group myResourceGroup --name myOpenBSD61
```

Ahora puede enviar un SSH a la máquina virtual de OpenBSD de la forma habitual:
        
```bash
ssh azureuser@<ip address>
```


## <a name="next-steps"></a>Pasos siguientes
Si desea obtener más información sobre la compatibilidad de Hyper-V en OpenBSD6.1, consulte [OpenBSD 6.1](https://www.openbsd.org/61.html) e [hyperv.4](http://man.openbsd.org/hyperv.4).

Si quiere crear una máquina virtual desde el disco administrado, consulte [az disk](/cli/azure/disk). 
