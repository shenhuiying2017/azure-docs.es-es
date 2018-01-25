---
title: "Creación de una definición de OpenAPI para una función | Microsoft Docs"
description: "Cree una definición de OpenAPI que permita que otras aplicaciones y servicios llamen a la función en Azure."
services: functions
keywords: OpenAPI, Swagger, aplicaciones de nube, servicios en la nube,
documentationcenter: 
author: mgblythe
manager: cfowler
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/15/2017
ms.author: mblythe; glenga
ms.custom: mvc
ms.openlocfilehash: 29e78bbb8e3d4d4feb3f7d32cf0a5ef1b02a6268
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2018
---
# <a name="create-an-openapi-definition-for-a-function"></a>Creación de una definición de OpenAPI para una función
Las API de REST a menudo se describen con una definición de OpenAPI (anteriormente conocida como el archivo [Swagger](http://swagger.io/)). Esta definición contiene información sobre las operaciones que están disponibles en una API y cómo se deben estructurar los datos de la solicitud y la respuesta para la API.

En este tutorial se crea una función que determina si la reparación de emergencia de una turbina eólica es rentable. Luego se crea una definición de OpenAPI para la aplicación de función de manera de poder llamar la función desde otras aplicaciones y servicios.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Crear una función en Azure
> * Generar una definición de OpenAPI con las herramientas de OpenAPI
> * Modificar la definición para brindar metadatos adicionales
> * Probar la definición mediante una llamada a la función

## <a name="create-a-function-app"></a>Creación de una aplicación de función

Debe tener una Function App para hospedar la ejecución de las funciones. Una aplicación de función permite agrupar funciones como una unidad lógica para facilitar la administración, la implementación, el escalado y el uso compartido de recursos. 

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)]


## <a name="create-the-function"></a>Creación de la función

En este tutorial se usa una función desencadenada por HTTP que toma dos parámetros: el tiempo estimado para reparar una turbina (en horas) y la capacidad de la turbina (en kilovatios). La función luego calcula cuánto costará la reparación y cuántos ingresos podría generar la turbina en un período de 24 horas.

1. Expanda la instancia de Function App y haga clic en el botón **+**, que se encuentra junto a **Funciones**. Si se trata de la primera función de Function App, seleccione **Función personalizada**. Se muestra el conjunto completo de plantillas de funciones. 

    ![Página de inicio rápido de Functions en Azure Portal](media/functions-openapi-definition/add-first-function.png)

2. En el campo de búsqueda, escriba `http` y, a continuación, elija **C#** para la plantilla del desencadenador HTTP. 
 
    ![Elija el desencadenador HTTP](./media/functions-openapi-definition/select-http-trigger-portal.png)

3. Escriba `TurbineRepair` en el campo **Nombre** de la función, elija `Function` en **[Nivel de autenticación](functions-bindings-http-webhook.md#http-auth)** y, a continuación, seleccione **Crear**.  

    ![Crear la función desencadenada mediante HTTP](./media/functions-openapi-definition/select-http-trigger-portal-2.png)

1. Reemplace el contenido del archivo run.csx por el código siguiente y haga clic en **Guardar**:

    ```csharp
    using System.Net;

    const double revenuePerkW = 0.12; 
    const double technicianCost = 250; 
    const double turbineCost = 100;

    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {   

        //Get request body
        dynamic data = await req.Content.ReadAsAsync<object>();
        int hours = data.hours;
        int capacity = data.capacity;

        //Formulas to calculate revenue and cost
        double revenueOpportunity = capacity * revenuePerkW * 24;  
        double costToFix = (hours * technicianCost) +  turbineCost;
        string repairTurbine;

        if (revenueOpportunity > costToFix){
            repairTurbine = "Yes";
        }
        else {
            repairTurbine = "No";
        }

        return req.CreateResponse(HttpStatusCode.OK, new{
            message = repairTurbine,
            revenueOpportunity = "$"+ revenueOpportunity,
            costToFix = "$"+ costToFix         
        }); 
    }
    ```
    Este código de función devuelve un mensaje de `Yes` o `No` para indicar si una reparación de emergencia es rentable, así como la oportunidad de ingresos que representa la turbina, además del costo de repararla. 

1. Para probar la función, haga clic en **Probar** a la derecha para expandir la pestaña de pruebas. Escriba el valor siguiente en el **Cuerpo de la solicitud**, y, luego, haga clic en **Ejecutar**.

    ```json
    {
    "hours": "6",
    "capacity": "2500"
    }
    ```

    ![Prueba de la función en Azure Portal](media/functions-openapi-definition/test-function.png)

    El valor siguiente se devuelve en el cuerpo de la respuesta.

    ```json
    {"message":"Yes","revenueOpportunity":"$7200","costToFix":"$1600"}
    ```

Ahora ya existe una función que determina la rentabilidad de las reparaciones de emergencia. A continuación, se genera y modifica una definición de OpenAPI para la aplicación de función.

## <a name="generate-the-openapi-definition"></a>Generación de la definición de OpenAPI

Ahora el usuario está listo para generar la definición de OpenAPI. Esta definición la pueden usar otras tecnologías Microsoft, como API Apps, [PowerApps](functions-powerapps-scenario.md) y [Microsoft Flow](../azure-functions/app-service-export-api-to-powerapps-and-flow.md), así como herramientas de desarrollador de terceros como [Postman](https://www.getpostman.com/docs/importing_swagger)y [muchos paquetes más](http://swagger.io/tools/).

1. Seleccione solo los *verbos* compatibles con la API (en este caso, POST). Esto permite que la definición de la API generada sea más limpia.

    1. En la pestaña **Integrar** de la nueva función de Desencadenador HTTP, cambie **Métodos HTTP permitidos** a **Métodos seleccionados**

    1. En **Métodos HTTP seleccionados**, borre todas las opciones excepto **POST** y, a continuación, haga clic en **Guardar**.

        ![Métodos HTTP seleccionados](media/functions-openapi-definition/selected-http-methods.png)
        
1. Haga clic en el nombre de la aplicación de función (como **function-demo-energy**) > **Características de la plataforma** > **Definición de la API**.

    ![Definición de API](media/functions-openapi-definition/api-definition.png)

1. En la pestaña **Definición de la API**, haga clic en **Función**.

    ![Origen de definición de la API](media/functions-openapi-definition/api-definition-source.png)

    Este paso habilita un conjunto de opciones de OpenAPI de la aplicación de función, incluido un punto de conexión para hospedar un archivo de OpenAPI del dominio de la aplicación de función, una copia alineada de [OpenAPI Editor](http://editor.swagger.io) y un generador de plantillas de definición de API.

1. Haga clic en **Generar plantilla de definición de API** > **Guardar**.

    ![Generar plantilla de definición de API](media/functions-openapi-definition/generate-template.png)

    Azure examina la aplicación de función en busca de las funciones de desencadenador HTTP y usa la información de functions.json para generar una definición de OpenAPI. A continuación se muestra la definición que se genera:

    ```yaml
    swagger: '2.0'
    info:
    title: function-demo-energy.azurewebsites.net
    version: 1.0.0
    host: function-demo-energy.azurewebsites.net
    basePath: /
    schemes:
    - https
    - http
    paths:
    /api/TurbineRepair:
        post:
        operationId: /api/TurbineRepair/post
        produces: []
        consumes: []
        parameters: []
        description: >-
            Replace with Operation Object
            #http://swagger.io/specification/#operationObject
        responses:
            '200':
            description: Success operation
        security:
            - apikeyQuery: []
    definitions: {}
    securityDefinitions:
    apikeyQuery:
        type: apiKey
        name: code
        in: query
    ```

    Esta definición se describe como una _plantilla_ porque requiere más metadatos para ser una definición de OpenAPI completa. La definición se modificará en el paso siguiente.

## <a name="modify-the-openapi-definition"></a>Modificación de la definición de OpenAPI
Ahora que se tiene una definición de plantilla, se puede modificar para proporcionar metadatos adicionales sobre las estructuras de datos y las operaciones de la API. En **Definición de la API**, elimine la definición generada desde `post` hasta la parte inferior de la definición, pegue el contenido siguiente y haga clic en **Guardar**.

```yaml
    post:
      operationId: CalculateCosts
      description: Determines if a technician should be sent for repair
      summary: Calculates costs
      x-ms-summary: Calculates costs
      x-ms-visibility: important
      produces:
        - application/json
      consumes:
        - application/json
      parameters:
        - name: body
          in: body
          description: Hours and capacity used to calculate costs
          x-ms-summary: Hours and capacity
          x-ms-visibility: important
          required: true
          schema:
            type: object
            properties:
              hours:
                description: The amount of effort in hours required to conduct repair
                type: number
                x-ms-summary: Hours
                x-ms-visibility: important
              capacity:
                description: The max output of a turbine in kilowatts
                type: number
                x-ms-summary: Capacity
                x-ms-visibility: important
      responses:
        200:
          description: Message with cost and revenue numbers
          x-ms-summary: Message
          schema:
           type: object
           properties:
            message:
              type: string
              description: Returns Yes or No depending on calculations
              x-ms-summary: Message 
            revenueOpportunity:
              type: string
              description: The revenue opportunity cost
              x-ms-summary: RevenueOpportunity 
            costToFix:
              type: string
              description: The cost in $ to fix the turbine
              x-ms-summary: CostToFix
      security:
        - apikeyQuery: []
definitions: {}
securityDefinitions:
  apikeyQuery:
    type: apiKey
    name: code
    in: query
```

En este caso, también podría, simplemente, pegar los metadatos actualizados, pero es importante comprender los tipos de modificaciones que se realizaron en la plantilla predeterminada:

+ Se especificó que la API genera y consume datos en formato JSON.

+ Se especificaron los parámetros requeridos con sus nombres y tipos de datos.

+ Se especificaron los valores devueltos para una respuesta correcta, con sus nombres y tipos de datos.

+ Se proporcionaron descripciones y resúmenes descriptivos para la API y sus operaciones y parámetros. Esto resulta importante para quienes usarán esta función.

+ Se agregó x-ms-summary y x-ms-visibility, que se usan en la interfaz de usuario para Microsoft Flow y Logic Apps. Para más información, consulte el artículo sobre las [extensiones de OpenAPI para las API personalizadas en Microsoft Flow](https://preview.flow.microsoft.com/documentation/customapi-how-to-swagger/).

> [!NOTE]
> La definición de seguridad se dejó con el método de autenticación predeterminado de la clave de API. Esta sección de la definición se podría cambiar si se usara otro tipo de autenticación.

Para más información sobre cómo definir las operaciones de API, consulte la [especificación de OpenAPI](https://swagger.io/specification/#operationObject).

## <a name="test-the-openapi-definition"></a>Prueba de la definición de OpenAPI
Antes de usar la definición de la API, se aconseja probarla en la interfaz de usuario de Azure Functions.

1. En la pestaña **Administrar** de la función, en **Claves de host**, copie la clave **predeterminada**.

    ![Copia de la clave de API](media/functions-openapi-definition/copy-api-key.png)

    > [!NOTE]
    >Esta clave se usa para pruebas y también cuando se llama a la API desde una aplicación o servicio.

1. Vuelva a la definición de la API: **function-demo-energy** > **Características de la plataforma** > **Definición de la API**.

1. En el panel de la derecha, haga clic en **Autenticar**, escriba la clave de API que copió y haga clic en **Autenticar**.

    ![Autenticación con la clave de API](media/functions-openapi-definition/authenticate-api-key.png)

1. Desplácese hacia abajo y haga clic en **Try this operation** (Intentar esta operación).

    ![Intentar esta operación](media/functions-openapi-definition/try-operation.png)

1. Escriba los valores de **horas** y **capacidad**.

    ![Escribir parámetros](media/functions-openapi-definition/parameters.png)

    Observe cómo la interfaz de usuario usa las descripciones de la definición de la API.

1. Haga clic en **Enviar solicitud** y, a continuación, haga clic en la pestaña **Pretty** (Descripción) para ver el resultado.

    ![Enviar una solicitud](media/functions-openapi-definition/send-request.png)

## <a name="next-steps"></a>pasos siguientes

En este tutorial aprendió lo siguiente:

> [!div class="checklist"]
> * Crear una función en Azure
> * Generar una definición de OpenAPI con las herramientas de OpenAPI
> * Modificar la definición para brindar metadatos adicionales
> * Probar la definición mediante una llamada a la función

Pase al tema siguiente para obtener información sobre cómo crear una aplicación de PowerApps que use la definición de OpenAPI que acaba de crear.
> [!div class="nextstepaction"]
> [Llamada a una función desde PowerApps](functions-powerapps-scenario.md)
