---
title: "Ejemplo de script de la CLI de Azure: supervisión de una aplicación web con registros de servidor web | Microsoft Docs"
description: "Ejemplo de script de la CLI de Azure: supervisión de una aplicación web con registros de servidor web"
services: appservice
documentationcenter: appservice
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 0887656f-611c-4627-8247-b5cded7cef60
ms.service: app-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 03/20/2017
ms.author: cfowler
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: ef75aa1ea579d4a3804e641ef9c848ccde8b9d7a
ms.contentlocale: es-es
ms.lasthandoff: 05/15/2017

---

# <a name="monitor-a-web-app-with-web-server-logs"></a>Supervisión de una aplicación web con registros de servidor web

En este escenario, creará un grupo de recursos, un plan de App Service y una aplicación web, además de configurar la aplicación web para habilitar los registros del servidor web. A continuación, descargará los archivos de registro para revisarlos.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Script de ejemplo

[!code-azurecli-interactive[principal](../../../cli_scripts/app-service/monitor-with-logs/monitor-with-logs.sh "Registros de Monitor")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos para crear un grupo de recursos, una aplicación web y todos los recursos relacionados. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Comando | Notas |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Crea un grupo de recursos en el que se almacenan todos los recursos. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | Crea un plan de App Service, que es como una granja de servidores para una aplicación web de Azure. |
| [az appservice web create](https://docs.microsoft.com/cli/azure/appservice/web#create) | Crea una aplicación web de Azure en el plan de App Service. |
| [az appservice web log config](https://docs.microsoft.com/cli/azure/appservice/web/log#config) | Configura los registros que conservará una aplicación web de Azure. |
| [az appservice web log download](https://docs.microsoft.com/cli/azure/appservice/web/log#download) | Descarga los registros de una aplicación web de Azure en el equipo local. |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](https://docs.microsoft.com/cli/azure/overview).

Puede encontrar ejemplos de script adicionales de la CLI de App Service en la [documentación de Azure App Service](../app-service-cli-samples.md).

