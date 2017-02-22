---
title: "Creación de una máquina virtual Linux con la CLI de Azure 2.0 (versión preliminar) | Microsoft Azure"
description: "Creación de una máquina virtual Linux con la CLI de Azure 2.0 (versión preliminar)."
services: virtual-machines-linux
documentationcenter: 
author: squillace
manager: timlt
editor: 
ms.assetid: 82005a05-053d-4f52-b0c2-9ae2e51f7a7e
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/13/2016
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: 42ee74ac250e6594616652157fe85a9088f4021a
ms.openlocfilehash: 0fd7aa8f941adaeb9961fd0e4724161b9fe2eeee


---

# <a name="create-a-linux-vm-using-the-azure-cli-20-preview-azpy"></a>Creación de una máquina virtual Linux con la versión preliminar de la CLI de Azure 2.0 (az.py)
En este artículo se muestra cómo implementar rápidamente una máquina virtual Linux en Azure mediante el comando [az vm create](/cli/azure/vm#create) con la CLI de Azure 2.0 (versión preliminar) y discos tanto administrados como de cuentas de almacenamiento nativas.

> [!NOTE] 
> La versión preliminar de la CLI de Azure 2.0 es la CLI multiplataforma de próxima generación. [Pruébelo.](https://docs.microsoft.com/cli/azure/install-az-cli2)
>
> Para crear una máquina virtual mediante la CLI de Azure 1.0 y no con la versión preliminar de la CLI de Azure 2.0, consulte [Creación de una máquina virtual Linux desde cero con la CLI de Azure](virtual-machines-linux-quick-create-cli-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Para crear una máquina virtual necesitará: 

* Una cuenta de Azure ([obtenga aquí una prueba gratuita](https://azure.microsoft.com/pricing/free-trial/)).
* La [CLI de Azure v. 2.0 (versión preliminar)](/cli/azure/install-az-cli2) instalada
* para iniciar sesión en la cuenta de Azure (escriba [az login](/cli/azure/#login))

(También puede implementar rápidamente una máquina virtual Linux mediante [Azure Portal](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)).

En el ejemplo siguiente se muestra cómo implementar una máquina virtual con Debian y conectarse a ella mediante la clave de Shell seguro (SSH). Los argumentos puede variar; si desea otra imagen, [puede buscar una](virtual-machines-linux-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="using-managed-disks"></a>Uso de discos administrados

Para usar Azure Managed Disks, necesita una región compatible. En primer lugar, escriba [az group create](/cli/azure/group#create) para crear el grupo de recursos que contiene todos los recursos implementados:

```azurecli
 az group create -n myResourceGroup -l westus
```

El resultado es similar al siguiente (puede especificar otra opción `--output` diferente si quiere ver otro formato):

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup",
  "location": "westus",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```
### <a name="create-your-vm"></a>Creación de su máquina virtual 
Ya puede crear una máquina virtual y su entorno. No olvide reemplazar el valor `--public-ip-address-dns-name` por un valor único; el que aparece a continuación puede estar ya en uso.

```azurecli
az vm create \
--image credativ:Debian:8:latest \
--admin-username azureuser \
--ssh-key-value ~/.ssh/id_rsa.pub \
--public-ip-address-dns-name manageddisks \
--resource-group myResourceGroup \
--location westus \
--name myVM
```


El resultado tendrá un aspecto similar al siguiente. Observe el valor `publicIpAddress` o `fqdn` en **ssh** en la máquina virtual.


```json
{
  "fqdn": "manageddisks.westus.cloudapp.azure.com",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "macAddress": "00-0D-3A-32-E9-41",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "104.42.127.53",
  "resourceGroup": "myResourceGroup"
}
```

Inicie sesión en la máquina virtual, ya sea con la dirección IP pública o con el nombre de dominio completo (FQDN) que se indica en la salida.

```bash
ssh ops@manageddisks.westus.cloudapp.azure.com
```

Debería ver algo parecido a lo siguiente, en función de la distribución elegida:

```bash
The authenticity of host 'manageddisks.westus.cloudapp.azure.com (134.42.127.53)' can't be established.
RSA key fingerprint is c9:93:f5:21:9e:33:78:d0:15:5c:b2:1a:23:fa:85:ba.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'manageddisks.westus.cloudapp.azure.com' (RSA) to the list of known hosts.
Enter passphrase for key '/home/ops/.ssh/id_rsa':

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Fri Jan 13 14:44:21 2017 from net-37-117-240-123.cust.vodafonedsl.it
ops@myVM:~$ 
```

Consulte [Pasos siguientes](#next-steps) para conocer otras cosas puede hacer con la nueva máquina virtual y discos administrados.

## <a name="using-unmanaged-disks"></a>Uso de discos no administrados 

Las máquinas virtuales que usan discos de almacenamiento no administrados tienen cuentas de almacenamiento no administradas. En primer lugar, escriba [az group create](/cli/azure/group#create) para crear el grupo de recursos donde se alojarán todos los recursos implementados:

```azurecli
az group create --name nativedisks --location westus
```

El resultado es similar al siguiente (puede elegir otra opción `--output` diferente si lo desea):

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/nativedisks",
  "location": "westus",
  "managedBy": null,
  "name": "nativedisks",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

### <a name="create-your-vm"></a>Creación de su máquina virtual 

Ya puede crear una máquina virtual y su entorno. No olvide reemplazar el valor `--public-ip-address-dns-name` por un valor único; el que aparece a continuación puede estar ya en uso.

```azurecli
az vm create \
--image credativ:Debian:8:latest \
--admin-username azureuser \
--ssh-key-value ~/.ssh/id_rsa.pub \
--public-ip-address-dns-name nativedisks \
--resource-group nativedisks \
--location westus \
--name myVM \
--use-native-disk
```

El resultado tendrá un aspecto similar al siguiente. Observe el valor `publicIpAddress` o `fqdn` en **ssh** en la máquina virtual.

```json
{
  "fqdn": "nativedisks.westus.cloudapp.azure.com",
  "id": "/subscriptions/<guid>/resourceGroups/nativedisks/providers/Microsoft.Compute/virtualMachines/myVM",
  "macAddress": "00-0D-3A-33-24-3C",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.91.91.195",
  "resourceGroup": "nativedisks"
}
```

Inicie sesión en la máquina virtual con la dirección IP pública o el nombre de dominio completo (FQDN), ambos se indican en la salida anterior.

```bash
ssh ops@nativedisks.westus.cloudapp.azure.com
```

Debería ver algo parecido a lo siguiente, en función de la distribución elegida:

```
The authenticity of host 'nativedisks.westus.cloudapp.azure.com (13.91.93.195)' can't be established.
RSA key fingerprint is 3f:65:22:b9:07:c9:ef:7f:8c:1b:be:65:1e:86:94:a2.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'nativedisks.westus.cloudapp.azure.com,13.91.93.195' (RSA) to the list of known hosts.
Enter passphrase for key '/home/ops/.ssh/id_rsa':

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
ops@myVM:~$ ls /
bin  boot  dev  etc  home  initrd.img  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var  vmlinuz
```

## <a name="next-steps"></a>Pasos siguientes
El comando `az vm create` es una forma rápida de implementar una máquina virtual para poder iniciar sesión en un shell de Bash y empezar a trabajar. Sin embargo, el uso de `az vm create` no ofrece un control amplio ni permite crear un entorno más complejo.  Para implementar una máquina virtual Linux personalizada para su infraestructura, puede seguir cualquiera de estos artículos.

* [Uso de una plantilla de Azure Resource Manager para crear una implementación específica](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Creación de un entorno personalizado para una máquina virtual Linux mediante el uso de comandos de la CLI de Azure directamente](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Creación de una máquina virtual Linux protegida con SSH en Azure mediante plantillas](virtual-machines-linux-create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

También puede [utilizar el controlador de Azure `docker-machine` con diversos comandos para crear rápidamente una máquina virtual Linux como host de Docker](virtual-machines-linux-docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Si está utilizando Java, pruebe con el método [create()](/java/api/com.microsoft.azure.management.compute._virtual_machine).




<!--HONumber=Feb17_HO2-->


