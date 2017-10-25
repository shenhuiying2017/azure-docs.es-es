---
title: "Cómo dirigir las versiones en tiempo de ejecución de Azure Functions | Microsoft Docs"
description: "Las funciones admiten varias versiones en tiempo de ejecución. Obtenga información acerca de cómo especificar la versión en tiempo de ejecución de una aplicación de función hospedada de Azure."
services: functions
documentationcenter: 
author: ggailey777
manager: cfowler
editor: 
ms.service: functions
ms.workload: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2017
ms.author: glenga
ms.openlocfilehash: 26d4276a0a550d78a9c7657c464bd3320c956fb0
ms.sourcegitcommit: 54fd091c82a71fbc663b2220b27bc0b691a39b5b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/12/2017
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>Cómo seleccionar un destino para versiones en tiempo de ejecución de Azure Functions

El tiempo de ejecución de Azure Functions implementa la ejecución del código en Azure sin servidor. Este tiempo de ejecución se encuentra en varios entornos distintos de los que se hospedan en Azure. [Azure Functions Core Tools](functions-run-local.md) implementa este tiempo de ejecución en el equipo de desarrollo. El [tiempo de ejecución de Azure Functions](functions-runtime-overview.md) permite usar funciones en entornos locales. 

Functions admiten varias versiones principales del runtime. Una actualización de la versión principal puede introducir cambios importantes. En este tema se describe cómo puede dirigir sus aplicaciones de función a una versión en tiempo de ejecución determinada cuando se hospeda en Azure. 

Functions permite hacer que el destino sea una versión principal específica del runtime mediante el uso de la configuración de aplicación `FUNCTIONS_EXTENSION_VERSION` en la aplicación de función. Esto se aplica a tanto a la versión pública como a la versión preliminar. La aplicación de función se mantiene en la versión principal del runtime especificada hasta que elija explícitamente moverla a una nueva versión. La aplicación de función se actualiza a nuevas versiones secundarias del runtime cuando están disponibles. Las actualizaciones de las versiones secundarias no introducen cambios importantes.  

Cuando una nueva versión principal esté disponible públicamente, se le brindará la oportunidad de instalarla al ver la aplicación de función en el portal. Después de pasar a una nueva versión, siempre puede usar la configuración de la aplicación `FUNCTIONS_EXTENSION_VERSION` para volver a una versión anterior del runtime.

Cada cambio en la versión del runtime hace que la aplicación de función se reinicie. Las notas de la versión de todas las versiones del runtime (principales y secundarias) se publican en el [repositorio de GitHub](https://github.com/Azure/azure-webjobs-sdk-script/releases).   
## <a name="view-the-current-runtime-version"></a>Visualización de la versión actual del runtime

Utilice el siguiente procedimiento para ver la versión específica del runtime que utiliza la aplicación de función. 

1. En el [Azure Portal](https://portal.azure.com), navegue hasta la aplicación de función y, en **Características configuradas**, elija **Configuración de Function App**. 

    ![Seleccionar Configuración de Function App](./media/functions-versions/add-update-app-setting.png)

2. En la pestaña **Configuración de Function App**, busque la **versión del runtime**. Observe la versión específica del runtime y la versión principal solicitada. En el ejemplo siguiente, la versión principal se establece en `~1.0`.
 
   ![Seleccionar Configuración de Function App](./media/functions-versions/function-app-view-version.png)

## <a name="target-the-functions-version-20-runtime"></a>Destino del runtime de la versión 2.0 de Functions

>[!IMPORTANT]   
> El entorno en tiempo de ejecución 2.0 de Azure Functions está en versión preliminar y actualmente no se admiten todas las características de Azure Functions. Para más información, consulte los [problemas conocidos del entorno en tiempo de ejecución 2.0 de Azure Functions](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Azure-Functions-runtime-2.0-known-issues).  

<!-- Add a table comparing the 1.x and 2.x runtime features-->

[!INCLUDE [functions-set-runtime-version](../../includes/functions-set-runtime-version.md)]

En Azure Portal puede mover la aplicación de función a la versión preliminar de la versión 2.0 del runtime. En la pestaña **Configuración de Function App**, elija **beta** en **Versión del runtime**.  

   ![Seleccionar Configuración de Function App](./media/functions-versions/function-app-view-version.png)

Esta opción equivale a establecer la configuración de la aplicación `FUNCTIONS_EXTENSION_VERSION` en `beta`. Haga clic en el botón **~ 1** botón para volver a la versión principal actual admitida públicamente. También puede utilizar la CLI de Azure para actualizar esta configuración de la aplicación. 

## <a name="target-a-specific-runtime-version-from-the-portal"></a>Selección del destino de una versión específica del runtime desde el portal

Cuando tenga que seleccionar el destino de una versión principal que no sea la versión principal actual versión o la versión 2.0, debe establecer la configuración de la aplicación `FUNCTIONS_EXTENSION_VERSION`.

1. En el [Azure Portal](https://portal.azure.com), navegue hasta la aplicación de función y, en **Características configuradas**, elija **Configuración de la aplicación**.

    ![Seleccionar Configuración de Function App](./media/functions-versions/add-update-app-setting1a.png)

2. En la pestaña **Configuración de la aplicación**, busque el valor `FUNCTIONS_EXTENSION_VERSION` y cambie el valor a una versión principal válida del runtime 1.x o a `beta` en el caso de la versión 2.0. 

    ![Establecer el valor de la aplicación de función](./media/functions-versions/add-update-app-setting2.png)

3. Haga clic en **Guardar** para guardar la actualización de la configuración de la aplicación. 

## <a name="target-a-specific-version-using-azure-cli"></a>Selección del destino de una versión concreta mediante la CLI de Azure

 `FUNCTIONS_EXTENSION_VERSION` también se puede establecer desde la CLI de Azure. Mediante la CLI de Azure, actualice la configuración de la aplicación en la aplicación de función con el comando [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#set).

```azurecli-interactive
az functionapp config appsettings set --name <function_app> \
--resource-group <my_resource_group> \
--settings FUNCTIONS_EXTENSION_VERSION=<version>
```
En este código, reemplace `<function_app>` por el nombre de la aplicación de función. Reemplace también `<my_resource_group>` por el nombre del grupo de recursos para la aplicación de función. Reemplace `<version>` por una versión principal válida del runtime 1.x o `beta` en el caso de la versión 2.0. 

Este comando se puede ejecutar desde [Azure Cloud Shell](../cloud-shell/overview.md), para lo que es preciso hacer clic en **Pruébelo** en el código de ejemplo anterior. También puede usar la [CLI de Azure localmente](/cli/azure/install-azure-cli) para ejecutar este comando después de ejecutar [az login](/cli/azure#az_login) para iniciar sesión.
