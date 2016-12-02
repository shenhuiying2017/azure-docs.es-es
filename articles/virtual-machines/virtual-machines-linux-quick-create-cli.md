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
ms.date: 09/26/2016
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: 2bd363e3c22f4cf4daf2e0fa352fd4a131d1675f
ms.openlocfilehash: 89db2c9f388b8a5496a306ba0a152ab57481ea50


---

# <a name="create-a-linux-vm-using-the-azure-cli-20-preview"></a>Creación de una máquina virtual Linux con la CLI de Azure 2.0 (versión preliminar)
En este artículo se muestra cómo implementar rápidamente una máquina virtual Linux en Azure mediante el comando [az vm create](/cli/azure/vm#create) con la CLI de Azure 2.0 (versión preliminar). 

> [!NOTE] 
> La versión preliminar de la CLI de Azure 2.0 es la CLI multiplataforma de próxima generación. Pruébela y cuéntenos su opinión en la [página del proyecto de GitHub](https://github.com/Azure/azure-cli).
>
> El resto de nuestro documentos usa la CLI de Azure existente. Para crear una máquina virtual mediante la CLI de Azure y no con la CLI 2.0 (versión preliminar), consulte [Create a VM with the Azure CLI](virtual-machines-linux-quick-create-cli-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Creación de una máquina virtual con la CLI de Azure).

Para crear una máquina virtual necesitará: 

* Una cuenta de Azure ([obtenga aquí una prueba gratuita](https://azure.microsoft.com/pricing/free-trial/)).
* La [CLI de Azure v. 2.0 (versión preliminar)](https://github.com/Azure/azure-cli#installation) instalada
* para iniciar sesión en la cuenta de Azure (escriba [az login](/cli/azure/#login))

(también puede implementar rápidamente una máquina virtual Linux mediante [Azure Portal](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)).

En el ejemplo siguiente se muestra cómo implementar una máquina virtual Debian y asociar su clave de Shell seguro (SSH) (sus argumentos pueden ser diferentes; si desea una imagen distinta, puede [buscar una](virtual-machines-linux-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)).

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

En primer lugar, escriba [az resource group create](/cli/azure/resource/group#create) para crear el grupo de recursos que contiene todos los recursos implementados:

```azurecli
az resource group create -n myResourceGroup -l westus
```

El resultado es similar al siguiente (puede elegir otra opción `--output` diferente si lo desea):

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup",
  "location": "westus",
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-your-vm-using-the-latest-debian-image"></a>Creación de una máquina virtual con la imagen de Debian más reciente

Ya puede crear una máquina virtual y su entorno. No olvide reemplazar el valor `----public-ip-address-dns-name` por un valor único; el que aparece a continuación puede estar ya en uso.

```azurecli
az vm create \
--image credativ:Debian:8:latest \
--admin-username ops \
--ssh-key-value ~/.ssh/id_rsa.pub \
--public-ip-address-dns-name mydns \
--resource-group myResourceGroup \
--location westus \
--name myVM
```


El resultado tendrá un aspecto similar al siguiente. Observe el valor `publicIpAddress` o `fqdn` en **ssh** en la máquina virtual.


```json
{
  "fqdn": "mydns.westus.cloudapp.azure.com",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "macAddress": "00-0D-3A-32-05-07",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.112.217.29",
  "resourceGroup": "myResourceGroup"
}
```

Inicie sesión en la máquina virtual con la dirección IP pública indicada en la salida. También puede utilizar el nombre de dominio completo que aparece.

```bash
ssh ops@mydns.westus.cloudapp.azure.com
```

Debería ver algo parecido a lo siguiente, en función de la distribución elegida:

```
The authenticity of host 'mydns.westus.cloudapp.azure.com (40.112.217.29)' can't be established.
RSA key fingerprint is SHA256:xbVC//lciRvKild64lvup2qIRimr/GB8C43j0tSHWnY.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'mydns.westus.cloudapp.azure.com,40.112.217.29' (RSA) to the list of known hosts.

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
ops@mynewvm:~$ ls /
bin  boot  dev  etc  home  initrd.img  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var  vmlinuz
```

## <a name="next-steps"></a>Pasos siguientes
El comando `az vm create` es una forma rápida de implementar una máquina virtual para poder iniciar sesión en un shell de Bash y empezar a trabajar. Sin embargo, el uso de `az vm create` no ofrece un control amplio ni permite crear un entorno más complejo.  Para implementar una máquina virtual Linux personalizada para su infraestructura, puede seguir cualquiera de estos artículos.

* [Uso de una plantilla de Azure Resource Manager para crear una implementación específica](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Creación de un entorno personalizado para una máquina virtual Linux mediante el uso de comandos de la CLI de Azure directamente](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Creación de una máquina virtual Linux protegida con SSH en Azure mediante plantillas](virtual-machines-linux-create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

También puede [utilizar el controlador de Azure `docker-machine` con diversos comandos para crear rápidamente una máquina virtual Linux como host de Docker](virtual-machines-linux-docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Si está utilizando Java, pruebe con el método [create()](/java/api/com.microsoft.azure.management.compute._virtual_machine).




<!--HONumber=Nov16_HO4-->


