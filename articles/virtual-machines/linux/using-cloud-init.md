---
title: "Uso de cloud-init para personalizar una máquina virtual Linux | Microsoft Docs"
description: "Procedimiento para usar cloud-init con el fin de personalizar una máquina virtual Linux con la CLI de Azure 2.0"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 195c22cd-4629-4582-9ee3-9749493f1d72
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 10/03/2017
ms.author: iainfou
ms.openlocfilehash: 5559f258f5c29b07edb5e61be4755d67173019e0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="use-cloud-init-to-customize-a-linux-vm-in-azure"></a>Uso de cloud-init para personalizar una máquina virtual Linux en Azure
En este artículo se muesta cómo usar [cloud-init](https://cloudinit.readthedocs.io) para establecer el nombre de host, actualizar los paquetes y administrar las cuentas de usuario en una máquina virtual en Azure. Estos scripts cloud-init se ejecutan en el inicio cuando crea una máquina virtual con al CLI de Azure 2.0. Para información más detallada sobre cómo instalar aplicaciones, escribir archivos de configuración e insertar claves desde Key Vault, consulte [este tutorial](tutorial-automate-vm-deployment.md). También puede llevar a cabo estos pasos con la [CLI de Azure 1.0](using-cloud-init-nodejs.md).


## <a name="cloud-init-overview"></a>Introducción a cloud-init
[cloud-init](https://cloudinit.readthedocs.io) es un enfoque ampliamente usado para personalizar una máquina virtual Linux la primera vez que se arranca. Puede usar cloud-init para instalar paquetes y escribir archivos o para configurar los usuarios y la seguridad. Como cloud-init se ejecuta durante el proceso de arranque inicial, no hay pasos adicionales o agentes requeridos que aplicar a la configuración.

cloud-init también funciona entre distribuciones. Por ejemplo, no use **apt-get install** o **yum install** para instalar un paquete. En su lugar, puede definir una lista de paquetes que se van a instalar. Cloud-init usará automáticamente la herramienta de administración de paquetes nativos para la distribución de Linux (distro) que seleccione.

Trabajamos con nuestros asociados para que cloud-init se incluya y funcione en las imágenes que estos proporcionan a Azure. En la tabla siguiente se describe la disponibilidad actual de cloud-init en imágenes de la plataforma de Azure:

| Alias | Publicador | Oferta | SKU | Versión |
|:--- |:--- |:--- |:--- |:--- |:--- |
| UbuntuLTS |Canonical |UbuntuServer |16.04-LTS |más reciente |
| UbuntuLTS |Canonical |UbuntuServer |14.04.5-LTS |más reciente |
| CoreOS |CoreOS |CoreOS |Stable |más reciente |


## <a name="set-the-hostname-with-cloud-init"></a>Establecimiento del nombre de host con cloud-init
Los archivos cloud-init están escritos en [YAML](http://www.yaml.org). Para ejecutar un script cloud-init cuando crea una máquina virtual en Azure con [az vm create](/cli/azure/vm#create), especifique el archivo cloud-init con el modificador `--custom-data`. Veamos algunos ejemplos de lo que puede configurar con un archivo cloud-init. Un escenario común es establecer el nombre de host de una máquina virtual. De manera predeterminada, el nombre de host es el mismo que el de la máquina virtual. 

En primer lugar, cree un grupo de recursos con [az group create](/cli/azure/group#create). En el ejemplo siguiente se crea el grupo de recursos denominado *myResourceGroup* en la ubicación *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

En el shell actual, cree un archivo denominado *cloud_init_hostname.txt* y pegue la configuración siguiente. Por ejemplo, cree el archivo en Cloud Shell, no en la máquina local. Puede utilizar el editor que prefiera. En Cloud Shell, escriba `sensible-editor cloud_init_hostname.txt` para crear el archivo y ver una lista de los editores disponibles. Asegúrese de que todo el archivo cloud-init se copia correctamente, especialmente la primera línea:

```yaml
#cloud-config
hostname: myhostname
```

Ahora, cree una máquina virtual con [az vm create](/cli/azure/vm#create) y especifique el archivo cloud-init con `--custom-data cloud_init_hostname.txt` como se indica a continuación:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVMHostname \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud_init_hostname.txt
```

Una vez que la crea, la CLI de Azure muestra información sobre la máquina virtual. Use `publicIpAddress` para usar un cliente SSH a la máquina virtual. Escriba su propia dirección, como se indica a continuación:

```bash
ssh azureuser@publicIpAddress
```

Para ver el nombre de la máquina virtual, use el comando `hostname` de la siguiente manera:

```bash
hostname
```

La máquina virtual debe informar el nombre de host como el valor establecido en el archivo cloud-init, como se muestra en la salida de ejemplo siguiente:

```bash
myhostname
```

## <a name="update-a-vm-with-cloud-init"></a>Actualización de una máquina virtual con cloud-init
Por motivos de seguridad, es posible que desee configurar una máquina virtual para aplicar las actualizaciones más recientes en el primer arranque. Como cloud-init funciona en distintas distribuciones de Linux, no es necesario especificar `apt` ni `yum` para el administrador de paquetes. En lugar de eso, se define `package_upgrade` para permitir que el proceso cloud-init determine el mecanismo adecuado para la distribución en uso. Este flujo de trabajo permite usar los mismos scripts de cloud-init en distintas distribuciones.

Para ver en acción el proceso de actualización, cree un archivo cloud-init denominado *cloud_init_upgrade.txt* y pegue la configuración siguiente:

```yaml
#cloud-config
package_upgrade: true
```

Ahora, cree una máquina virtual con [az vm create](/cli/azure/vm#create) y especifique el archivo cloud-init con `--custom-data cloud_init_upgrade.txt` como se indica a continuación:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVMUpgrade \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud_init_upgrade.txt
```

SSH a la dirección IP pública de la máquina virtual que se muestra en la salida del comando anterior. Escriba su propia dirección IP pública, como se indica a continuación:

```bash
ssh azureuser@publicIpAddress
```

Ejecute la herramienta de administración de paquetes y compruebe si hay actualizaciones. En el ejemplo siguiente se usa `apt-get` en una máquina virtual Ubuntu:

```bash
sudo apt-get upgrade
```

Como cloud-init comprobó e instaló las actualizaciones en el arranque, no hay ninguna actualización que aplicar, tal como se muestra en la salida de ejemplo siguiente:

```bash
Reading package lists... Done
Building dependency tree
Reading state information... Done
Calculating upgrade... Done
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
```

## <a name="add-a-user-to-a-vm-with-cloud-init"></a>Incorporación de un usuario a una máquina virtual con cloud-init
Una de las primeras tareas en cualquier máquina virtual Linux nueva es agregar un usuario para usted mismo a fin de evitar el uso de *root*. Las claves SSH son un procedimiento recomendado de seguridad y facilidad de uso. Las claves se agregan al archivo *~/.ssh/authorized_keys* con este script cloud-init.

Para agregar un usuario a una máquina virtual Linux, cree un archivo cloud-init denominado *cloud_init_add_user.txt* y pegue la configuración siguiente. Proporcione su clave pública propia (como el contenido de *~/.ssh/id_rsa.pub*) para *ssh-authorized-keys*:

```yaml
#cloud-config
users:
  - name: myadminuser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>
```

Ahora, cree una máquina virtual con [az vm create](/cli/azure/vm#create) y especifique el archivo cloud-init con `--custom-data cloud_init_add_user.txt` como se indica a continuación:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVMUser \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud_init_add_user.txt
```

SSH a la dirección IP pública de la máquina virtual que se muestra en la salida del comando anterior. Escriba su propia dirección IP pública, como se indica a continuación:

```bash
ssh myadminuser@publicIpAddress
```

Para confirmar que se agregó el usuario a la máquina virtual y los grupos especificados, consulte el contenido del archivo */etc/group* como se indica a continuación:

```bash
cat /etc/group
```

En la salida de ejemplo siguiente se muestra que el usuario del archivo *cloud_init_add_user.txt* se agregó a la máquina virtual y al grupo adecuado:

```bash
root:x:0:
<snip />
sudo:x:27:myadminuser
<snip />
myadminuser:x:1000:
```

## <a name="next-steps"></a>Pasos siguientes
Cloud-init es una de las maneras estándar de modificar las máquinas virtuales Linux en el arranque. En Azure, también puede usar extensiones de máquina virtual para modificar la máquina virtual Linux en el arranque o una vez que está en ejecución. Por ejemplo, puede usar la extensión de máquina virtual de Azure para ejecutar un script en una máquina virtual en ejecución y no solo en el primer arranque. Para más información sobre las extensiones de máquina virtual, consulte [Características y extensiones de máquina virtual](extensions-features.md), o bien, si desea ejemplos de cómo usar la extensión, consulte [Administración de usuarios, SSH y comprobar o reparar discos en máquinas virtuales Linux de Azure con la extensión VMAccess](using-vmaccess-extension.md).