---
title: 'Ejemplos de la CLI de Azure 2.0: Instalación de aplicaciones | Microsoft Docs'
description: Ejemplos de la CLI de Azure 2.0
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 22c2f8d811da3dfc565ff32cb602e78b1097ffab
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/05/2018
---
# <a name="install-applications-into-a-virtual-machine-scale-set-with-the-azure-cli-20"></a>Instalación de aplicaciones en un conjunto de escalado de máquinas virtuales con la CLI de Azure 2.0
Este script crea un conjunto de escalado de máquinas virtuales que ejecutan Ubuntu y usa la extensión de script personalizado para instalar una aplicación web básica. Después de ejecutar el script, puede tener acceso a la aplicación web mediante un explorador web.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de ejemplo
[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine-scale-sets/install-apps/install-apps.sh "Install apps into a scale set")]

## <a name="clean-up-deployment"></a>Limpieza de la implementación
Ejecute el siguiente comando para quitar el grupo de recursos, el conjunto de escalado y todos los recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicación del script
Este script usa los siguientes comandos para crear un grupo de recursos, un conjunto de escalado de máquinas virtuales y todos los recursos relacionados. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Get-Help | Notas |
|---|---|
| [az group create](/cli/azure/ad/group#az_ad_group_create) | Crea un grupo de recursos en el que se almacenan todos los recursos. |
| [az vmss create](/cli/azure/vmss#az_vmss_create) | Crea el conjunto de escalado de máquinas virtuales y lo conecta a la red virtual, la subred y el grupo de seguridad de red. También se crea un equilibrador de carga para distribuir el tráfico a varias instancias de máquina virtual. Este comando también especifica la imagen de máquina virtual que se usará y las credenciales administrativas.  |
| [az vmss extension set](/cli/azure/vmss/extension#az_vmss_extension_set) | Instala la extensión de script personalizado de Azure para ejecutar un script que prepare los discos de datos en cada instancia de máquina virtual. |
| [az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create) | Crea una regla de equilibrador de carga para distribuir el tráfico en el puerto TCP 80 entre las instancias de máquina virtual del conjunto de escalado. |
| [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show) | Obtiene información sobre la dirección IP pública asignada que usa el equilibrador de carga. |
| [az group delete](/cli/azure/ad/group#delete) | Elimina un grupo de recursos, incluidos todos los recursos anidados. |

## <a name="next-steps"></a>Pasos siguientes
Para más información acerca de la CLI de Azure 2.0, consulte la [documentación de la CLI de Azure 2.0](https://docs.microsoft.com/cli/azure/overview).

Se pueden encontrar otros ejemplos de scripts de la CLI de Azure 2.0 para conjuntos de escalado de máquinas virtuales en la [documentación de Azure Virtual Machine Scale Set](../cli-samples.md).
