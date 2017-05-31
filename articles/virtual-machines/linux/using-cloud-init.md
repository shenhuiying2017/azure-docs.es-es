---
title: "Uso de cloud-init para personalizar una máquina virtual Linux | Microsoft Docs"
description: "Procedimiento para usar cloud-init con el fin de personalizar una máquina virtual Linux con la CLI de Azure 2.0"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 195c22cd-4629-4582-9ee3-9749493f1d72
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 05/11/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: a7a6daad34525683579e25b9591ed28f2bf29c04
ms.contentlocale: es-es
ms.lasthandoff: 05/11/2017


---
# <a name="use-cloud-init-to-customize-a-linux-vm-during-creation"></a>Uso de cloud-init para personalizar una máquina virtual Linux durante la creación
En este artículo se muestra cómo crear un script de cloud-init para establecer el nombre de host, actualizar los paquetes instalados y administrar cuentas de usuario con la CLI de Azure 2.0. Los scripts de cloud-init se llaman durante la creación de una máquina virtual desde la CLI de Azure. Para obtener información más detallada acerca de la instalación de aplicaciones, la escritura de archivos de configuración y la inserción de claves desde Key Vault, consulte [este tutorial](tutorial-automate-vm-deployment.md). También puede llevar a cabo estos pasos con la [CLI de Azure 1.0](using-cloud-init-nodejs.md).

## <a name="quick-commands"></a>Comandos rápidos
Cree un script cloud-init.txt que establezca el nombre de host, actualice todos los paquetes y agregue un usuario de sudo a Linux.

```yaml
#cloud-config
hostname: myVMhostname
apt_upgrade: true
users:
  - name: myNewAdminUser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>==myAdminUser@myVM
```

Cree un grupo de recursos para iniciar máquinas virtuales con [az group create](/cli/azure/group#create). En el ejemplo siguiente se crea el grupo de recursos denominado *myResourceGroup*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Cree una máquina virtual Linux con [az vm create](/cli/azure/vm#create) mediante cloud-init para configurarla durante el inicio con el parámetro `--custom-data`.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
```

## <a name="detailed-walkthrough"></a>Tutorial detallado
Cuando inicia una nueva VM de Linux, obtiene una VM Linux estándar con nada personalizado o preparada para sus necesidades. [Cloud-init](https://cloudinit.readthedocs.org) es una forma estándar de insertar un script u opciones de configuración en esa VM Linux cuando arranca por primera vez.

En Azure hay algunas formas distintas de realizar cambios en una máquina virtual Linux cuando se está implementando o arrancando.

* Inserte scripts con cloud-init.
* Inserte scripts con la [extensión VMAccess](using-vmaccess-extension.md)de Azure.
* Una plantilla de Azure con cloud-init.
* Una plantilla de Azure con [CustomScriptExtention](extensions-customscript.md).

Para insertar scripts en cualquier momento después del arranque:

* SSH para ejecutar directamente los comandos.
* Inserte scripts con la [extensión VMAccess](using-vmaccess-extension.md)de Azure, ya sea de manera imperativa como en una plantilla de Azure.
* Herramientas de administración de la configuración, como Ansible, Salt, Chef y Puppet.

> [!NOTE]
> La extensión VMAccess ejecuta un script como raíz del mismo modo que podría hacerlo con SSH. Sin embargo, con la extensión de VM se habilitan varias características que Azure ofrece y que pueden ser útiles según el escenario.

## <a name="cloud-init-availability-on-azure-vm-quick-create-image-aliases"></a>Disponibilidad de cloud-init en los alias de imagen de creación rápida de VM de Azure:
| Alias | Publicador | Oferta | SKU | Versión | Cloud-init |
|:--- |:--- |:--- |:--- |:--- |:--- |
| CentOS |OpenLogic |CentOS |7,2 |más reciente |no |
| CoreOS |CoreOS |CoreOS |Stable |más reciente |yes |
| Debian |credativ |Debian |8 |más reciente |no |
| openSUSE |SUSE |openSUSE |13.2 |más reciente |no |
| RHEL |Redhat |RHEL |7,2 |más reciente |no |
| UbuntuLTS |Canonical |UbuntuServer |14.04.4-LTS |más reciente |yes |

Trabajamos con nuestros asociados para que cloud-init se incluya y funcione en las imágenes que estos proporcionan a Azure.

## <a name="add-a-cloud-init-script-to-the-vm-creation-with-the-azure-cli"></a>Incorporación de un script de cloud-init al proceso de creación de máquinas virtuales con la CLI de Azure
Para iniciar un script de cloud-init al crear una VM en Azure, especifique el archivo de cloud-init mediante el modificador `--custom-data` de la CLI de Azure.

Cree un grupo de recursos para iniciar máquinas virtuales con [az group create](/cli/azure/group#create). En el ejemplo siguiente se crea el grupo de recursos denominado *myResourceGroup*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Cree una máquina virtual Linux con [az vm create](/cli/azure/vm#create) mediante cloud-init para configurarla durante el inicio.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
```

## <a name="create-a-cloud-init-script-to-set-the-hostname-of-a-linux-vm"></a>Creación de un script de cloud-init para establecer el nombre de host de una máquina virtual Linux
Una de las configuraciones más sencillas y más importantes para cualquier VM de Linux sería el nombre de host. Podemos establecer esto fácilmente mediante cloud-init con este script.  

### <a name="example-cloud-init-script-named-cloudconfighostnametxt"></a>Script de cloud-init de ejemplo denominado `cloud_config_hostname.txt`.
```yaml
#cloud-config
hostname: myservername
```

Durante el primer inicio de la máquina virtual, este script de cloud-init establece el nombre de host en *myservername*. Cree una máquina virtual Linux con [az vm create](/cli/azure/vm#create) mediante cloud-init para configurarla durante el inicio.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
```

Inicie sesión y compruebe el nombre de host de la nueva VM.

```bash
ssh myVM
hostname
myservername
```

## <a name="create-a-cloud-init-script"></a>Creación de un script de cloud-init
Por seguridad, desea que la VM de Ubuntu se actualice en el primer arranque. Mediante cloud-init podemos hacerlo con el siguiente script, dependiendo de la distribución de Linux que se use.

### <a name="example-cloud-init-script-cloudconfigaptupgradetxt-for-the-debian-family"></a>Script de cloud-init de ejemplo `cloud_config_apt_upgrade.txt` para la familia Debian
```yaml
#cloud-config
apt_upgrade: true
```

Después de iniciarse Linux, se actualizan todos los paquetes instalados a través de **apt-get**. Cree una máquina virtual Linux con [az vm create](/cli/azure/vm#create) mediante cloud-init para configurarla durante el inicio.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud_config_apt_upgrade.txt
```

Inicie sesión y compruebe que todos los paquetes se han actualizado.

```bash
ssh myUbuntuVM
sudo apt-get upgrade
Reading package lists... Done
Building dependency tree
Reading state information... Done
Calculating upgrade... Done
The following packages have been kept back:
  linux-generic linux-headers-generic linux-image-generic
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
```

## <a name="create-a-cloud-init-script-to-add-a-user-to-linux"></a>Creación de un script de cloud-init para agregar un usuario a Linux
Una de las primeras tareas en cualquier máquina virtual Linux nueva consiste en agregar un usuario para usted mismo o para evitar el uso de *root*. Las claves SSH son el procedimiento recomendado para la seguridad y la facilidad de uso; además, se agregan al archivo *~/.ssh/authorized_keys* con este script de cloud-init.

### <a name="example-cloud-init-script-cloudconfigadduserstxt-for-debian-family"></a>Script de cloud-init de ejemplo `cloud_config_add_users.txt` para la familia Debian
```yaml
#cloud-config
users:
  - name: myCloudInitAddedAdminUser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>==myAdminUser@myUbuntuVM
```

Una vez que se inicia Linux, todos los usuarios de la lista se crean y se agregan al grupo de sudo. Cree una máquina virtual Linux con [az vm create](/cli/azure/vm#create) mediante cloud-init para configurarla durante el inicio.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud_config_add_users.txt
```

Inicie sesión y compruebe el usuario recién creado.

```bash
ssh myVM
cat /etc/group
```

Salida

```bash
root:x:0:
<snip />
sudo:x:27:myCloudInitAddedAdminUser
<snip />
myCloudInitAddedAdminUser:x:1000:
```

## <a name="next-steps"></a>Pasos siguientes
Cloud-init se convierte en la manera estándar de modificar la VM Linux en el arranque. Azure también tiene extensiones de VM, que le permiten modificar su VM Linux en el arranque o mientras está en ejecución. Por ejemplo, puede usar la extensión VMAccessExtension de Azure para restablecer SSH o la información del usuario mientras la VM está en ejecución. Con cloud-init, necesitaría reiniciar para restablecer la contraseña.

[Acerca de las características y extensiones de las máquinas virtuales](extensions-features.md)

[Administración de usuarios, SSH y comprobar o reparar discos en máquinas virtuales de Linux de Azure con la extensión VMAccess](using-vmaccess-extension.md)
