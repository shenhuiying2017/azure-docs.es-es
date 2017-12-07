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
ms.date: 11/21/2017
ms.author: glenga
ms.openlocfilehash: 588437af80ecf60b7c4b24dbf6bccc67fc33da7a
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/29/2017
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>Cómo seleccionar un destino para versiones en tiempo de ejecución de Azure Functions

Una aplicación de función se ejecuta en una versión específica del runtime de Azure Functions. Hay dos versiones principales: 1.x y 2.x. En este artículo se explica cómo elegir la versión principal que se va a utilizar y cómo configurar una aplicación de función en Azure para que se ejecute en la versión que elija. Para obtener información sobre cómo configurar un entorno de desarrollo local para una versión específica, consulte [Codificación y comprobación de las funciones de Azure Functions en un entorno local](functions-run-local.md).

## <a name="differences-between-runtime-1x-and-2x"></a>Diferencias entre las versiones 1.x y 2.x del runtime

> [!IMPORTANT] 
> La versión 1.x del runtime es la única versión aprobada para su uso en producción.

| Tiempo de ejecución | Estado |
|---------|---------|
|1.x|Disponible con carácter general|
|2.x|Vista previa|

En las siguientes secciones explican las diferencias de lenguajes, enlaces y compatibilidad con el desarrollo multiplataforma.

### <a name="languages"></a>Idiomas

En la tabla siguiente se indican qué lenguajes de programación se admiten en cada versión del runtime.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

Para más información, consulte [Lenguajes admitidos](supported-languages.md).

### <a name="bindings"></a>Enlaces 

Runtime 2.x permite crear [extensiones de enlace](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview) personalizadas. Los enlaces integrados que utilizan este modelo de extensibilidad solo están disponibles en la versión 2.x; entre la primera se encuentran los [enlaces de Microsoft Graph](functions-bindings-microsoft-graph.md).

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

Para información acerca de la compatibilidad con los enlaces y otros espacios funcionales en la versión 2.x, consulte los [problemas conocidos del Runtime 2.0](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Azure-Functions-runtime-2.0-known-issues).

### <a name="cross-platform-development"></a>Desarrollo multiplataforma

Runtime 1.x admite el desarrollo de funciones solo en el portal o en Windows; con la versión 2.x puede desarrollar y ejecutar instancias de Azure Functions en Linux o mac OS.

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

## <a name="target-the-version-20-runtime"></a>Selección de destino de la versión 2.0 del runtime

>[!IMPORTANT]   
> El entorno en tiempo de ejecución 2.0 de Azure Functions está en versión preliminar y actualmente no se admiten todas las características de Azure Functions. Para más información, consulte [Diferencias entre las versiones 1.x y 2.x del runtime.](#differences-between-runtime-1x-and-2x) anteriormente en este artículo.

En Azure Portal puede mover la aplicación de función a la versión preliminar de la versión 2.0 del runtime. En la pestaña **Configuración de Function App**, elija **beta** en **Versión del runtime**.  

![Seleccionar Configuración de Function App](./media/functions-versions/function-app-view-version.png)

Esta opción equivale a establecer la configuración de la aplicación `FUNCTIONS_EXTENSION_VERSION` en `beta`. Haga clic en el botón **~ 1** botón para volver a la versión principal actual admitida públicamente. También puede utilizar la CLI de Azure para actualizar esta configuración de la aplicación. 

## <a name="target-a-version-using-the-portal"></a>Selección de destino de una versión mediante el portal

Cuando tenga que seleccionar el destino de una versión que no sea la versión principal actual versión o la versión 2.0, debe establecer la configuración de la aplicación `FUNCTIONS_EXTENSION_VERSION`.

1. En el [Azure Portal](https://portal.azure.com), navegue hasta la aplicación de función y, en **Características configuradas**, elija **Configuración de la aplicación**.

    ![Seleccionar Configuración de Function App](./media/functions-versions/add-update-app-setting1a.png)

2. En la pestaña **Configuración de la aplicación**, busque el valor `FUNCTIONS_EXTENSION_VERSION` y cambie el valor a una versión válida del runtime 1.x o a `beta` en el caso de la versión 2.0. 

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

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Selección del destino de la versión 2.0 del runtime en el entorno de desarrollo local](functions-run-local.md)

> [!div class="nextstepaction"]
> [Vea las notas de versión para las versiones del runtime](https://github.com/Azure/azure-webjobs-sdk-script/releases)