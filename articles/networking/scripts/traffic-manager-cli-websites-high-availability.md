---
title: "Ejemplo de script de la CLI de Azure: enrutamiento del tráfico para la alta disponibilidad de las aplicaciones | Microsoft Docs"
description: "Ejemplo de script de la CLI de Azure: enrutamiento del tráfico para la alta disponibilidad de las aplicaciones"
services: traffic-manager
documentationcenter: traffic-manager
author: KumudD
manager: timlt
editor: tysonn
tags: azure-infrastructure
ms.assetid: 
ms.service: traffic-manager
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: traffic-manager
ms.date: 07/07/2017
ms.author: kumud
ms.openlocfilehash: 5c3754c3c3c96e1d2f0b5b52d8108ecc3903f788
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="route-traffic-for-high-availability-of-applications"></a>Enrutamiento del tráfico para la alta disponibilidad de las aplicaciones

Este script crea un grupo de recursos, dos planes de App Service, dos aplicaciones web, un perfil de Traffic Manager y dos puntos de conexión de Traffic Manager. Traffic Manager dirige el tráfico a la aplicación en una región como la región primaria y a la región secundaria cuando no está disponible la aplicación en la región primaria. Antes de ejecutar el script, debe cambiar los valores de MyWebApp, MyWebAppL1 y MyWebAppL2 a valores únicos en todo Azure. Después de ejecutar el script, puede tener acceso a la aplicación en la región primaria con la dirección URL mywebapp.trafficmanager.net.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de ejemplo

[!code-azurecli-interactive[main](../../../cli_scripts/traffic-manager/direct-traffic-for-increased-application-availability/direct-traffic-for-increased-application-availability.sh "Route traffic for high availability")]


## <a name="clean-up-deployment"></a>Limpieza de la implementación 

Después de ejecutar el script de ejemplo, se puede usar el comando siguiente para quitar el grupo de recursos, la aplicación de App Service y todos los recursos relacionados.

```azurecli
az group delete --name myResourceGroup1 --yes
az group delete --name myResourceGroup2 --yes
```

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos para crear un grupo de recursos, una aplicación web, un perfil de Traffic Manager y todos los recursos relacionados. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Comando | Notas |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Crea un grupo de recursos en el que se almacenan todos los recursos. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_create) | Crea un plan de App Service, que es como una granja de servidores para una aplicación web de Azure. |
| [az appservice web create](https://docs.microsoft.com/cli/azure/appservice/web#az_appservice_web_create) | Crea una aplicación web de Azure en el plan de App Service. |
| [az network traffic-manager profile create](https://docs.microsoft.com/cli/azure/network/traffic-manager/profile#az_network_traffic_manager_profile_create) | Crea un perfil de Azure Traffic Manager. |
| [az network traffic-manager endpoint create](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint#az_network_traffic_manager_endpoint_create) | Añade un punto de conexión a un perfil de Azure Traffic Manager. |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](https://docs.microsoft.com/cli/azure/overview).

Puede encontrar ejemplos de script adicionales de la CLI de App Service en la [documentación de Redes de Azure](../cli-samples.md).
