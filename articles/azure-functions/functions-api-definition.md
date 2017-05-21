---
title: OpenAPI Metadata en Azure Functions | Microsoft Docs
description: "Introducción a la compatibilidad con OpenAPI en Azure Functions"
services: functions
documentationcenter: 
author: alexkarcher-msft
manager: erikre
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 03/23/2017
ms.author: alkarche
ms.translationtype: Human Translation
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: aac362f5123038cc39e0d2b32c10f7747a702cfe
ms.contentlocale: es-es
ms.lasthandoff: 05/16/2017


---
# <a name="openapi-20-metadata-support-in-azure-functions-preview"></a>Compatibilidad con OpenAPI 2.0 Metadata en Azure Functions (versión preliminar)
Esta función de versión preliminar le permite escribir una definición de OpenAPI 2.0 (anteriormente Swagger) dentro de una Function App y hospeda ese archivo mediante Function App.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

### <a name="what-is-openapi-metadata"></a>¿Qué es OpenAPI Metadata?
[OpenAPI Metadata](http://swagger.io/) permite que una función que hospede una API de REST que puede consumir una gran variedad de software. Desde ofertas propias, como PowerApps y [API Apps](https://docs.microsoft.com/azure/app-service-api/app-service-api-dotnet-get-started#a-idcodegena-generate-client-code-for-the-data-tier), hasta herramientas de terceros, como [Postman](https://www.getpostman.com/docs/importing_swagger) y [muchos otros paquetes](http://swagger.io/tools/).

>[!TIP]
>Se recomienda empezar por el [tutorial de introducción](./functions-api-definition-getting-started.md) y, luego, volver a este documento para obtener más información sobre características específicas.

## <a name="enable"></a>Habilitación de compatibilidad de definición de OpenAPI
* Todas las configuraciones de OpenAPI pueden configurarse en la página `API Definition (preview)` en la configuración de Function App.
* Establezca `API definition source` a `Function` para habilitar una definición de OpenAPI hospedada y la generación de definiciones de inicio rápido.
  * `External URL` permite que su Function use una definición de OpenAPI que está hospedada en otro lugar.

## <a name="generate-definition"></a>Generación de un esqueleto de Swagger a partir de sus metadatos de la instancia de Function
Una plantilla es una forma fantástica de empezar a escribir la primera definición de OpenAPI. La función de plantilla de definición crea una definición de OpenAPI dispersa con todos los metadatos de function.json para cada una de las funciones de desencadenador HTTP. **Necesitará rellenar más información acerca de la API con la [especificación de OpenAPI](http://swagger.io/specification/), como las plantillas de solicitud y respuesta.**

[Consulte este tutorial de inicio para obtener instrucciones paso a paso.](./functions-api-definition-getting-started.md)

### <a name="templates"></a>Plantillas disponibles

|Nombre| Descripción |
|:-----|:-----|
|Definición generada|Una definición de OpenAPI con la cantidad máxima de información que se pueden inferir a partir de los metadatos existentes de la función.|

### <a name="quickstart-details"></a>Metadatos incluidos en la definición generada

En la tabla siguiente se representa la configuración del portal y los datos correspondientes en function.json, tal y como se asignan al esqueleto de Swagger generado.

|Swagger.json|IU del portal|Function.json|
|:----|:-----|:-----|
|[Host](http://swagger.io/specification/#fixed-fields-15)|`Function app settings` > `Go to App Service Settings` > `Overview` > `URL`|*no presente*
|[Paths](http://swagger.io/specification/#paths-object-29)|`Integrate` > `Selected HTTP methods`|Bindings: Route
|[Path Item](http://swagger.io/specification/#path-item-object-32)|`Integrate` > `Route template`|Bindings: Methods
|[Seguridad](http://swagger.io/specification/#security-scheme-object-112)|simétricas|*no presente*|
|operationID*|Ruta + verbos permitidos|Ruta + verbos permitidos|

\*operationID solo es necesario para la integración con PowerApps + Flow
> [!NOTE]
>  x-ms-resumen proporciona un nombre para mostrar en Logic Apps, Flow y PowerApps.
>
> Consulte [Personalizar la definición de Swagger para PowerApps](https://powerapps.microsoft.com/tutorials/customapi-how-to-swagger/) para obtener más información.

## <a name="CICD"></a>Uso de CI/CD para establecer una definición de API

 Debe habilitar el hospedaje de la definición de la API en el portal antes de habilitar el control de código fuente para modificar la definición de la API desde el control de código fuente. Siga las instrucciones que se describen a continuación.

1. Vaya a `API Definition (preview)` en la configuración de Function App.
  1. Establezca `API definition source` en `Function`.
  1. Haga clic en `Generate API definition template` y, luego, en `Save` para crear una definición de plantilla que podrá modificar más adelante.
  1. Anote su `API definition URL` y `key`.
1. [Configure CI/CD](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment#continuous-deployment-requirements)
2. Modifique su `swagger.json` en el control de código fuente en `\site\wwwroot\.azurefunctions\swagger\swagger.json`.

Ahora los cambios de `swagger.json` en el repositorio se hospedan en su Function App en `API definition URL` y `key` que se anotaron en el paso 1.3.

## <a name="next-steps"></a>Pasos siguientes
* [Tutorial de inicio](functions-api-definition-getting-started.md)
  * Pruebe nuestro tutorial para ver una definición de OpenAPI en acción.
* [Repositorio de GitHub de Azure Functions](https://github.com/Azure/Azure-Functions/)
  * Visite GitHub de Functions para proporcionarnos sus comentarios sobre la versión preliminar de la compatibilidad con la definición de API. Cree una entrada en GitHub para lo que le gustaría ver actualizado.
* [Referencia para desarrolladores de Funciones de Azure](functions-reference.md)  
  * contiene las referencias del programador para codificar funciones y definir desencadenadores y enlaces.

