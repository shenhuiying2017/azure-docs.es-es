---
title: "Ejemplo de script de la CLI de Azure: asignación de un dominio personalizado a una aplicación web | Microsoft Docs"
description: "Ejemplo de script de la CLI de Azure: asignación de un dominio personalizado a una aplicación web"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 5ac4a680-cc73-4578-bcd6-8668c08802c2
ms.service: app-service-web
ms.workload: web
ms.devlang: azurecli
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/09/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 757d6f778774e4439f2c290ef78cbffd2c5cf35e
ms.openlocfilehash: 480c18285e4708ce2771f10a848e4a55acf0c817
ms.lasthandoff: 04/10/2017

---
# <a name="map-a-custom-domain-to-a-web-app"></a>Asignación de un dominio personalizado a una aplicación web

Este script de ejemplo crea una aplicación web en App Service con sus recursos relacionados y, después, le asigna `www.<yourdomain>`.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Script de ejemplo

[!code-azurecli[main](../../../cli_scripts/app-service/configure-custom-domain/configure-custom-domain.sh?highlight=3 "Asignación de un dominio personalizado a una aplicación web")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Comando | Notas |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Crea un grupo de recursos en el que se almacenan todos los recursos. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | Crea un plan de App Service, |
| [az appservice web create](https://docs.microsoft.com/cli/azure/appservice/web#delete) | Crea una aplicación web. |
| [az appservice web config hostname add](https://docs.microsoft.com/cli/azure/appservice/web/config/hostname#add) | Asigna un dominio personalizado a una aplicación web. |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](https://docs.microsoft.com/cli/azure/overview).

Puede encontrar ejemplos de script adicionales de la CLI de App Service en la [documentación de Azure App Service](../app-service-cli-samples.md).

