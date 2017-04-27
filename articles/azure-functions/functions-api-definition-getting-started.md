---
title: "Introducción a OpenAPI Metadata en Azure Functions | Microsoft Docs"
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
translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: ad82c1a552d9d77259c44b938975eebc44933b86
ms.lasthandoff: 04/26/2017


---
# <a name="creating-openapi-20-swagger-metadata-for-a-function-app-preview"></a>Creación de OpenAPI 2.0 (Swagger) Metadata para una Function App (versión preliminar)

Este documento lo guía a través del proceso paso a paso de creación de una definición de OpenAPI para una API sencilla que se hospedan en Azure Functions.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

### <a name="what-is-openapi-swagger"></a>¿Qué es OpenAPI (Swagger)?
[Swagger Metadata](http://swagger.io/) es un archivo que define la funcionalidad y los modos de funcionamiento de la API, y permite hospedar una API de REST que puede consumir una gran variedad de software. Los productos de Microsoft, como PowerApps y [API Apps](https://docs.microsoft.com/azure/app-service-api/app-service-api-dotnet-get-started#a-idcodegena-generate-client-code-for-the-data-tier), así como herramientas para desarrolladores de terceros, como [Postman](https://www.getpostman.com/docs/importing_swagger), y [muchos otros paquetes](http://swagger.io/tools/) permiten que su API se consuma con una definición de Swagger.

## <a name="prepare-function"></a>Creación de una instancia de Function con una API sencilla
  Para crear una definición de OpenAPI, primero se debe crear una instancia de Function con una API sencilla. Si ya tiene una API que se hospeda en una Function App, puede pasar directamente a la sección siguiente.
1. Cree una nueva instancia de Function App.
  1. [Azure Portal](https://portal.azure.com)  >  `+ New` > Busque "Function App"
1. Cree una nueva función de desencadenador HTTP dentro de la nueva instancia de Function App.
  1. La función se rellena previamente con el código que define una API de REST muy sencilla.
  1. Cualquier cadena que se pasa a la instancia de Function como un parámetro de consulta o en el cuerpo se devuelve como "Hello {entrada}"
1. Vaya a la pestaña `Integrate` de la nueva función de desencadenador HTTP.
  1. Cambie de `Allowed HTTP methods` a `Selected methods`.
  1. En `Selected HTTP methods` desactive todos los verbos excepto POST.
    ![Métodos HTTP seleccionados](./media/functions-api-definition-getting-started/selectedHTTPmethods.png)
  1. Este paso simplificará la definición de la API más adelante.

## <a name="enable"></a>Habilitación de compatibilidad de definición de la API
1. Vaya a `your function name` > `API Definition (preview)`.
1. Establezca `API Definition Source` en `Function`.
  1. Este paso habilita un conjunto de opciones de OpenAPI para instancia de Function App, incluido un punto de conexión para hospedar un archivo de OpenAPI de su dominio de Function App, una copia en línea de [OpenAPI Editor](http://editor.swagger.io)y un generador de definiciones de inicio rápido.
![Definición habilitada](./media/functions-api-definition-getting-started/enabledefinition.png)

## <a name="create-definition"></a>Creación de la definición de la API desde una plantilla
1. Haga clic en `Generate API Definition template`
  1. Este paso examina la instancia de Function App en busca de las funciones de desencadenador HTTP y usa la información de functions.json para generar un documento de OpenAPI.
2. Agregue una operación a `paths: /api/yourfunctionroute post:`.
  1. El documento de OpenAPI de inicio rápido es un esquema de un documento de OpenAPI completo. Requiere más metadatos para ser una definición de OpenAPI completa, como objetos de operación y plantillas de respuesta.
  1. El objeto de la operación de ejemplo a continuación tiene una sección "produces/consumes", un objeto de parámetro y un objeto de respuesta ya rellenados.
  
  ```yaml
      post:
        operationId: /api/yourfunctionroute/post
        consumes: [application/json]
        produces: [application/json]
        parameters:
          - name: name
            in: body
            description: Your name
            x-ms-summary: Your name
            required: true
            schema:
              type: object
              properties:
                name:
                  type: string
        description: >-
          Replace with Operation Object
          #http://swagger.io/specification/#operationObject
        responses:
          '200':
            description: A Greeting
            x-ms-summary: Your name
            schema:
              type: string
        security:
          - apikeyQuery: []
  ```

> [!NOTE]
>  x-ms-resumen proporciona un nombre para mostrar en Logic Apps, Flow y PowerApps.
>
> Consulte [Personalizar la definición de Swagger para PowerApps](https://powerapps.microsoft.com/tutorials/customapi-how-to-swagger/) para obtener más información.

3. Haga clic en `save` para guardar los cambios ![Adición de la definición de plantilla](./media/functions-api-definition-getting-started/addingtemplate.png)

## <a name="use-definition"></a>Uso de su definición de API
1. Copie la `API definition URL` y péguela en una nueva pestaña del explorador para ver el documento de OpenAPI sin formato.
1. Puede importar el documento de OpenAPI a cualquier herramientas para pruebas e integración usando esa dirección URL.
  1. Muchos recursos de Azure pueden importar automáticamente el documento de OpenAPI utilizando la dirección URL de la definición de API que se guarda en la configuración de la aplicación Function. Como parte del origen de definición de API de `Function`, actualizamos esa dirección URL en su nombre.


## <a name="test-definition"></a>Uso de la interfaz de usuario de Swagger para probar la definición de API
Son herramientas de pruebas integradas en la vista de la interfaz de usuario del editor de definiciones de API incrustado. Agregue su clave de API y, luego, use el botón `Try this operation` de cada método. La herramienta usará la definición de la API para dar formato a las solicitudes y respuestas correctas indicarán que la definición es correcta.

### <a name="steps"></a>Pasos:

1. Copie la clave de API de la función.
  1. La clave de API puede encontrarse en la instancia de Function de desencadenador HTTP en `function name`  >  `Keys`  >  `Function Keys` 
   ![Tecla de Function](./media/functions-api-definition-getting-started/functionkey.png)
1. Vaya a la página `API Definition (preview)`.
  1. Haga clic en `Authenticate` y agregue la clave de API de la instancia de Function para el objeto de seguridad en la parte superior.
  ![Clave de OpenAPI](./media/functions-api-definition-getting-started/definitionTest auth.png)
1. Seleccione `/api/yourfunctionroute` > `POST`.
1. Haga clic en `Try it out` y escriba el nombre que quiere probar.
1. Debería ver un resultado SUCCESS en `Pretty` 
 ![prueba de definición de la API](./media/functions-api-definition-getting-started/definitionTest.png)

## <a name="next-steps"></a>Pasos siguientes
* [Definición de OpenAPI en información general de Functions](functions-api-definition.md)
  * Lea la documentación completa para obtener más información sobre la compatibilidad con OpenAPI.
* [Referencia para desarrolladores de Funciones de Azure](functions-reference.md)  
  * contiene las referencias del programador para codificar funciones y definir desencadenadores y enlaces.
* [Repositorio de GitHub de Azure Functions](https://github.com/Azure/Azure-Functions/)
  * Visite GitHub de Functions para proporcionarnos sus comentarios sobre la versión preliminar de la compatibilidad con la definición de API. Cree una entrada en GitHub para lo que le gustaría ver actualizado.

