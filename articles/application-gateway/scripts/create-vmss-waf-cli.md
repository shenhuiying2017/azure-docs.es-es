---
title: 'Ejemplo de script de la CLI de Azure: restringir el tráfico web | Microsoft Docs'
description: 'Ejemplo de script de la CLI de Azure: cree una puerta de enlace de aplicaciones con un firewall de aplicaciones web y un conjunto de escalado de máquinas virtuales que use reglas OWASP para restringir el tráfico.'
services: application-gateway
documentationcenter: networking
author: vhorne
manager: jpconnock
editor: tysonn
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/29/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 5864b77976db2ba953304a17c4622fdb6d4b7cdc
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2018
---
# <a name="restrict-web-traffic-using-the-azure-cli"></a>Restringir el tráfico web mediante la CLI de Azure

Este script crea una puerta de enlace de aplicaciones con un firewall de aplicaciones web que usa un conjunto de escalado de máquinas virtuales para los servidores back-end. El firewall de aplicaciones web restringe el tráfico web basado en reglas OWASP. Después de ejecutar el script, se puede probar la puerta de enlace de aplicaciones mediante la dirección IP pública.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de ejemplo

[!code-azurecli-interactive[main](../../../cli_scripts/application-gateway/create-vmss/create-vmss-waf.sh "Create application gateway")]

## <a name="clean-up-deployment"></a>Limpieza de la implementación 

Ejecute el siguiente comando para quitar el grupo de recursos, la puerta de enlace de aplicaciones y todos los recursos relacionados.

```azurecli-interactive 
az group delete --name myResourceGroupAG --yes
```

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos para crear la implementación. Cada elemento de la tabla incluye vínculos a la documentación específica del comando.

| Get-Help | Notas |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Crea un grupo de recursos en el que se almacenan todos los recursos. |
| [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet#az_net) | Crea una red virtual. |
| [az network vnet subnet create](https://docs.microsoft.com/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) | Crea una subred en una red virtual. |
| [az network public-ip create](https://docs.microsoft.com/cli/azure/public-ip#az_network_public_ip_create) | Crea la dirección IP pública de la puerta de enlace de aplicaciones. |
| [az network application-gateway create](https://docs.microsoft.com/cli/azure/application-gateway#az_application_gateway_create) | Crear una puerta de enlace de aplicaciones. |
| [az vmss create](https://docs.microsoft.com/cli/azure/vmss#az_vmss_create) | Crea un conjunto de escalado de máquinas virtuales. |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account#az_storage_account_create) | Crea una cuenta de almacenamiento. |
| [az monitor diagnostic-settings create](https://docs.microsoft.com/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create) | Crea una cuenta de almacenamiento. |
| [az network public-ip show](https://docs.microsoft.com/cli/azure/network/public-ip#az_network_public_ip_show) | Obtiene la dirección IP pública de la puerta de enlace de aplicaciones. |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](https://docs.microsoft.com/cli/azure/overview).

Se pueden encontrar más ejemplos de script de la CLI de la puerta de enlace de aplicaciones en la [documentación de Azure Application Gateway](../cli-samples.md).
