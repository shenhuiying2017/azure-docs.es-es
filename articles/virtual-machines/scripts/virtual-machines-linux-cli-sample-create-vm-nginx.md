---
title: "Ejemplo de script de la CLI de Azure: creación de una máquina virtual Linux con NGINX | Microsoft Docs"
description: "Ejemplo de script de la CLI de Azure: creación de una máquina virtual Linux con NGINX"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/27/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 0a6c33d84f1fab85e6ed2933c47c041ca2e59520
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-vm-with-nginx"></a>Creación de una máquina virtual con NGINX

Este script crea una máquina virtual de Azure y, a continuación, usa la extensión de scripts personalizados para máquinas virtuales de Azure para instalar NGINX. Una vez ejecutado el script, puedo acceder al sitio web de demostración en la dirección IP pública de la máquina virtual.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de ejemplo

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-nginx/create-vm-nginx.sh "Quick Create VM")]

## <a name="custom-script-extension"></a>Extensión Custom Script

La extensión de scripts personalizados copia este script en la máquina virtual. A continuación, se ejecuta el script para instalar y configurar un servidor web NGINX. 

```bash
#!/bin/bash

# update package source
apt-get -y update

# install NGINX
apt-get -y install nginx
```

## <a name="clean-up-deployment"></a>Limpieza de la implementación 

Ejecute el siguiente comando para quitar el grupo de recursos, la máquina virtual y todos los recursos relacionados.

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos para crear un grupo de recursos, una máquina virtual y todos los recursos relacionados. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Comando | Notas |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Crea un grupo de recursos en el que se almacenan todos los recursos. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm#az_vm_create) | Crea la máquina virtual. Este comando también especifica la imagen de máquina virtual que se usará, y las credenciales administrativas.  |
| [az vm open-port](https://docs.microsoft.com/cli/azure/network/nsg/rule#az_network_nsg_rule_create) | Crea una regla de grupo de seguridad de red para permitir el tráfico entrante. En este ejemplo, el puerto 80 está abierto al tráfico HTTP. |
| [azure vm extension set](https://docs.microsoft.com/cli/azure/vm/extension#az_vm_extension_set) | Agrega una extensión de máquina virtual a una máquina virtual y la ejecuta. En este ejemplo, se utiliza la extensión de scripts personalizados para instalar NGINX.|
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az_vm_extension_set) | Elimina un grupo de recursos, incluidos todos los recursos anidados. |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](https://docs.microsoft.com/cli/azure/overview).

Encontrará más ejemplos de scripts de la CLI de máquina virtual en la [documentación sobre máquinas virtuales Linux de Azure](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
