---
title: "Ejemplo de script de la CLI de Azure: enlace de un certificado SSL personalizado a una aplicación web | Microsoft Docs"
description: "Ejemplo de script de la CLI de Azure: enlace de un certificado SSL personalizado a una aplicación web"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: eb95d350-81ea-4145-a1e2-6eea3b7469b2
ms.service: app-service-web
ms.workload: web
ms.devlang: azurecli
ms.tgt_pltfrm: na
ms.topic: sample
ms.date: 06/19/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: ab4e211bc2c3f7e99ab0246e7abba57795318245
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="bind-a-custom-ssl-certificate-to-a-web-app"></a>Enlace de un certificado SSL personalizado a una aplicación web

Este script de ejemplo crea una aplicación web en App Service con sus recursos relacionados y, luego, enlaza el certificado SSL de un nombre de dominio personalizado a ella. Para este ejemplo, necesitará lo siguiente:

* Acceso a la página de configuración DNS de su registrador de dominios.
* Un archivo PFX válido y su contraseña para el certificado SSL que desea cargar y enlazar.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si decide instalar y usar la CLI localmente, para este tema es preciso que ejecute la CLI de Azure versión 2.0 o posterior. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli). 


## <a name="sample-script"></a>Script de ejemplo

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom SSL certificate to a web app")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Comando | Notas |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Crea un grupo de recursos en el que se almacenan todos los recursos. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_create) | Crea un plan de App Service, |
| [az webapp create](https://docs.microsoft.com/cli/azure/webapp#az_webapp_create) | Crea una aplicación web de Azure. |
| [az webapp config hostname add](https://docs.microsoft.com/cli/azure/webapp/config/hostname#az_webapp_config_hostname_add) | Asigna un dominio personalizado a una aplicación web. |
| [az webapp config ssl upload](https://docs.microsoft.com/cli/azure/webapp/config/ssl#az_webapp_config_ssl_upload) | Carga un certificado SSL a una aplicación web. |
| [az webapp config ssl bind](https://docs.microsoft.com/cli/azure/webapp/config/ssl#az_webapp_config_ssl_bind) | Enlaza un certificado SSL cargado a una aplicación web. |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](https://docs.microsoft.com/cli/azure/overview).

Puede encontrar ejemplos de script adicionales de la CLI de App Service en la [documentación de Azure App Service](../app-service-cli-samples.md).
