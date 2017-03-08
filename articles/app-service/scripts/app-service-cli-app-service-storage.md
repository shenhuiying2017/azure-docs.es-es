---
title: "Ejemplo de script de la CLI de Azure: conexión de una aplicación web a una cuenta de almacenamiento | Microsoft Docs"
description: "Ejemplo de script de la CLI de Azure: conexión de una aplicación web a una cuenta de almacenamiento"
services: appservice
documentationcenter: appservice
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: bc8345b2-8487-40c6-a91f-77414e8688e6
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 02/21/2017
ms.author: cfowler
translationtype: Human Translation
ms.sourcegitcommit: c23bab16ee8bf771f4af44abc94e958f512d5390
ms.openlocfilehash: 6ea4c0f82bf29b5683a851da9e6c1134fe0394af
ms.lasthandoff: 02/27/2017

---

# <a name="connect-a-web-app-to-a-storage-account"></a>Conexión de una aplicación web a una cuenta de almacenamiento

En este escenario aprenderá a crear una cuenta de Azure Storage y una aplicación web de Azure. Y, después, vinculará la cuenta de almacenamiento a la aplicación web mediante la configuración de la aplicación.

Antes de ejecutar este script, asegúrese de que se haya creado una conexión con Azure mediante el comando `az login`.

Este ejemplo funciona en un shell de Bash. Para ver las opciones de ejecución de scripts de la CLI de Azure en un cliente Windows, consulte [Using the Azure CLI on Windows](../../virtual-machines/virtual-machines-windows-cli-options.md) (Uso de la CLI de Azure en Windows).

## <a name="sample-script"></a>Script de ejemplo

[!code-azurecli[principal](../../../cli_scripts/app-service/connect-to-storage/connect-to-storage.sh "Azure Storage")]

## <a name="clean-up-deployment"></a>Limpieza de la implementación 

Después de ejecutar el script de ejemplo, se puede usar el comando siguiente para quitar el grupo de recursos, la aplicación App Service y todos los recursos relacionados.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos para crear un grupo de recursos, una aplicación web, una cuenta de almacenamiento y todos los recursos relacionados. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Comando | Notas |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Crea un grupo de recursos en el que se almacenan todos los recursos. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | Crea un plan de App Service, que es como una granja de servidores para una aplicación web de Azure. |
| [az appservice web create](https://docs.microsoft.com/cli/azure/appservice/web#create) | Crea una aplicación web de Azure en el plan de App Service. |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account#create) | Crea una cuenta de almacenamiento. Aquí es donde se almacenarán los recursos estáticos. |
| [az storage account show-connection-string](https://docs.microsoft.com/cli/azure/storage/account#show-connection-string) | |
| [az appservice web config appsetings update](https://docs.microsoft.com/cli/azure/appservice/web/config/appsettings#update) | Crea o actualiza una configuración de aplicación para una aplicación web de Azure. La configuración de la aplicación se expone como variables de entorno para la aplicación. |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](https://docs.microsoft.com/cli/azure/overview).

Puede encontrar ejemplos de script adicionales de la CLI de App Service en la [documentación de Azure App Service](../app-service-cli-samples.md).
