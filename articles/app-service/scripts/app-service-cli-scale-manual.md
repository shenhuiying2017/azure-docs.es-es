---
title: "Ejemplo de script de la CLI de Azure: escalado manual de una aplicación web mediante la CLI de Azure 2.0 | Microsoft Docs"
description: "Ejemplo de script de la CLI de Azure: escalado manual de una aplicación web mediante la CLI de Azure 2.0"
services: appservice
documentationcenter: appservice
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 251d9074-8fff-4121-ad16-9eca9556ac96
ms.service: app-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 06/19/2017
ms.author: cfowler
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 190ca4b228434a7d1b30348011c39a979c22edbd
ms.openlocfilehash: a8e9dc2d7767459fafe80a4986f5ac87e95e6e34
ms.contentlocale: es-es
ms.lasthandoff: 09/09/2017

---

# <a name="scale-a-web-app-manually"></a>Escalado manual de una aplicación web

En este escenario, aprenderá a crear un grupo de recursos, un plan de App Service y una aplicación web. A continuación, escalará el plan de App Service desde una sola instancia a varias instancias.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si decide instalar y usar la CLI localmente, para este tema es preciso que ejecute la CLI de Azure versión 2.0 o posterior. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Script de ejemplo

[!code-azurecli-interactive[principal](../../../cli_scripts/app-service/scale-manual/scale-manual.sh "Escalado manual")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos para crear un grupo de recursos, una aplicación web y todos los recursos relacionados. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Comando | Notas |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Crea un grupo de recursos en el que se almacenan todos los recursos. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_create) | Crea un plan de App Service, que es como una granja de servidores para una aplicación web de Azure. |
| [az webapp create](https://docs.microsoft.com/cli/azure/webapp#az_webapp_create) | Crea una aplicación web de Azure. |
| [az appservice plan update](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_update) | Actualiza las propiedades del plan de App Service. |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](https://docs.microsoft.com/cli/azure/overview).

Puede encontrar ejemplos de script adicionales de la CLI de App Service en la [documentación de Azure App Service](../app-service-cli-samples.md).

