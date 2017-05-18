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
ms.date: 04/10/2017
ms.author: cephalin
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: 27da647a10a52af40333a962c75c73cab93861d7
ms.contentlocale: es-es
ms.lasthandoff: 05/15/2017

---

# <a name="bind-a-custom-ssl-certificate-to-a-web-app"></a>Enlace de un certificado SSL personalizado a una aplicación web

Este script de ejemplo crea una aplicación web en App Service con sus recursos relacionados y, luego, enlaza el certificado SSL de un nombre de dominio personalizado a ella. Para este ejemplo, necesitará lo siguiente:

* Acceso a la página de configuración DNS de su registrador de dominios.
* Un archivo PFX válido y su contraseña para el certificado SSL que desea cargar y enlazar.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Script de ejemplo

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Enlace de un certificado SSL personalizado a una aplicación web")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Comando | Notas |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Crea un grupo de recursos en el que se almacenan todos los recursos. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | Crea un plan de App Service, |
| [az appservice web create](https://docs.microsoft.com/cli/azure/appservice/web#delete) | Crea una aplicación web de Azure. |
| [az appservice web config hostname add](https://docs.microsoft.com/cli/azure/appservice/web/config/hostname#add) | Asigna un dominio personalizado a una aplicación web. |
| [az appservice web config ssl upload](https://docs.microsoft.com/cli/azure/appservice/web/config/ssl#upload) | Carga un certificado SSL a una aplicación web. |
| [az appservice web config ssl bind](https://docs.microsoft.com/en-us/cli/azure/appservice/web/config/ssl#bind) | Enlaza un certificado SSL cargado a una aplicación web. |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](https://docs.microsoft.com/cli/azure/overview).

Puede encontrar ejemplos de script adicionales de la CLI de App Service en la [documentación de Azure App Service](../app-service-cli-samples.md).

