---
title: "Cómo seleccionar un destino para versiones en tiempo de ejecución de Azure Functions"
description: "Azure Functions admite varias versiones del runtime. Obtenga información sobre cómo especificar la versión en tiempo de ejecución de una aplicación de función hospedada en Azure."
services: functions
documentationcenter: 
author: ggailey777
manager: cfowler
editor: 
ms.service: functions
ms.workload: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2018
ms.author: glenga
ms.openlocfilehash: c87455f43c6e580c52b3f772a1cbe0c1ac9c9f5b
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/01/2018
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>Cómo seleccionar un destino para versiones en tiempo de ejecución de Azure Functions

Una aplicación de función se ejecuta en una versión específica del runtime de Azure Functions. Hay dos versiones principales: [1.x y 2.x](functions-versions.md). En este artículo se explica cómo configurar una aplicación de función en Azure para que se ejecute en la versión que elija. Para obtener información sobre cómo configurar un entorno de desarrollo local para una versión específica, consulte [Codificación y comprobación de las funciones de Azure Functions en un entorno local](functions-run-local.md).

>[!IMPORTANT]   
> El entorno en tiempo de ejecución 2.0 de Azure Functions está en versión preliminar y actualmente no se admiten todas las características de Azure Functions. Para más información, consulte [Selección de un destino para versiones de runtime de Azure Functions](functions-versions.md).

## <a name="automatic-and-manual-version-updates"></a>Actualizaciones de versiones automáticas y manuales

Functions permite hacer que el destino sea una versión específica del runtime mediante el uso de la configuración de aplicación `FUNCTIONS_EXTENSION_VERSION` en la aplicación de función. La aplicación de función se mantiene en la versión principal especificada hasta que elija explícitamente moverla a una nueva versión.

Si especifica solo la versión principal ("~ 1" para 1.x) o "beta" 2.x, la aplicación de la función se actualiza automáticamente a las nuevas versiones secundarias del runtime cuando están disponibles. Las nuevas versiones secundarias no introducen cambios importantes. Si especifica una versión secundaria (por ejemplo, "1.0.11360"), la aplicación de función se mantiene en esa versión hasta que la modifique explícitamente. 

Cuando una nueva versión está disponible públicamente, un aviso en el portal le da la oportunidad de pasar a esa versión. Después de pasar a una nueva versión, siempre puede usar la configuración de la aplicación `FUNCTIONS_EXTENSION_VERSION` para volver a una versión anterior.

Un cambio en la versión del runtime hace que la aplicación de función se reinicie.

Los valores que puede establecer en la configuración de la aplicación `FUNCTIONS_EXTENSION_VERSION` para habilitar las actualizaciones automáticas son actualmente "~1" para el runtime 1.x y "beta" para 2.x.

## <a name="view-the-current-runtime-version"></a>Visualización de la versión actual del runtime

Utilice el siguiente procedimiento para ver la versión del runtime que utiliza una aplicación de función. 

1. En el [Azure Portal](https://portal.azure.com), navegue hasta la aplicación de función y, en **Características configuradas**, elija **Configuración de Function App**. 

    ![Seleccionar Configuración de Function App](./media/functions-versions/add-update-app-setting.png)

2. En la pestaña **Configuración de Function App**, busque la **versión del runtime**. Observe la versión específica del runtime y la versión principal solicitada. En el ejemplo siguiente la configuración de la aplicación FUNCIONES\_EXTENSIÓN\_VERSIÓN se establece en `~1`.
 
   ![Seleccionar Configuración de Function App](./media/functions-versions/function-app-view-version.png)

## <a name="target-a-version-using-the-portal"></a>Selección de destino de una versión mediante el portal

Cuando tenga que seleccionar el destino de una versión que no sea la versión principal actual o la versión 2.0, establezca la configuración de la aplicación `FUNCTIONS_EXTENSION_VERSION`.

1. En el [Azure Portal](https://portal.azure.com), navegue hasta la aplicación de función y, en **Características configuradas**, elija **Configuración de la aplicación**.

    ![Seleccionar Configuración de Function App](./media/functions-versions/add-update-app-setting1a.png)

2. En la pestaña **Configuración de la aplicación**, busque el valor `FUNCTIONS_EXTENSION_VERSION` y cambie el valor a una versión válida del runtime 1.x o a `beta` en el caso de la versión 2.0. Una tilde con la versión principal significa utilizar la versión más reciente de esa versión principal (por ejemplo, "~ 1"). 

    ![Establecer el valor de la aplicación de función](./media/functions-versions/add-update-app-setting2.png)

3. Haga clic en **Guardar** para guardar la actualización de la configuración de la aplicación. 

## <a name="target-a-version-using-azure-cli"></a>Selección del destino de una versión mediante la CLI de Azure

 `FUNCTIONS_EXTENSION_VERSION` también se puede establecer desde la CLI de Azure. Mediante la CLI de Azure, actualice la configuración de la aplicación en la aplicación de función con el comando [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#set).

```azurecli-interactive
az functionapp config appsettings set --name <function_app> \
--resource-group <my_resource_group> \
--settings FUNCTIONS_EXTENSION_VERSION=<version>
```
En este código, reemplace `<function_app>` por el nombre de la aplicación de función. Reemplace también `<my_resource_group>` por el nombre del grupo de recursos para la aplicación de función. Reemplace `<version>` por una versión válida del runtime 1.x o `beta` en el caso de la versión 2.0. 

Este comando se puede ejecutar desde [Azure Cloud Shell](../cloud-shell/overview.md), para lo que es preciso hacer clic en **Pruébelo** en el código de ejemplo anterior. También puede usar la [CLI de Azure localmente](/cli/azure/install-azure-cli) para ejecutar este comando después de ejecutar [az login](/cli/azure#az_login) para iniciar sesión.

## <a name="next-steps"></a>pasos siguientes

> [!div class="nextstepaction"]
> [Selección del destino de la versión 2.0 del runtime en el entorno de desarrollo local](functions-run-local.md)

> [!div class="nextstepaction"]
> [Vea las notas de versión para las versiones del runtime](https://github.com/Azure/azure-webjobs-sdk-script/releases)