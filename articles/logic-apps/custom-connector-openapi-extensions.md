---
title: 'Extensiones de OpenAPI para conectores personalizados: Azure Logic Apps | Microsoft Docs'
description: Ampliar OpenAPI del conector personalizado con funcionalidad avanzada
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: LADocs; estfan
ms.openlocfilehash: e8565019bd4631043485224c73c2fe60e633951c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="extend-your-custom-connectors-openapi-with-advanced-functionality"></a>Ampliar OpenAPI del conector personalizado con funcionalidad avanzada

Para crear conectores personalizados para Azure Logic Apps, Microsoft Flow o Microsoft PowerApps, debe proporcionar un archivo de definición de OpenAPI, que es un documento que se puede leer en una máquina y que es independiente del lenguaje, que describe las operaciones y los parámetros de la API. Junto con la funcionalidad estándar de OpenAPI, también puede incluir estas extensiones de OpenAPI al crear conectores personalizados para Logic Apps y Flow:

* [`summary`](#summary)
* [`x-ms-summary`](#x-ms-summary)
* [`description`](#description)
* [`x-ms-visibility`](#x-ms-visibility)
* [`x-ms-dynamic-values`](#x-ms-dynamic-values)
* [`x-ms-dynamic-schema`](#x-ms-dynamic-schema)

Aquí encontrará más detalles acerca de estas extensiones:

<a name="summary"></a>

## <a name="summary"></a>summary

Especifica el título de la acción (operación). </br>
Se aplica a: operaciones </br>
Recomendación: use el *tipo oración* para `summary`. </br>
Ejemplo: "Cuando se agrega un evento al calendario" o "Enviar un correo electrónico"

!["summary" de cada operación](./media/custom-connector-openapi-extensions/summary.png)

``` json
"actions" {
  "Send_an_email": {
    /// Other action properties here...
    "summary": "Send an email",
    /// Other action properties here...
  }
},
```

<a name="x-ms-summary"></a>

## <a name="x-ms-summary"></a>x-ms-summary

Especifica el título de una entidad. </br>
Se aplica a: parámetros, esquema de respuestas </br>
Recomendación: use el *tipo título* para `x-ms-summary`. </br>
Ejemplo: "Id. de calendario", "Asunto", "Descripción del evento", etc.

!["x-ms-summary" para cada entidad](./media/custom-connector-openapi-extensions/x-ms-summary.png)

``` json
"actions" {
  "Send_an_email": {
    /// Other action properties here...
    "parameters": [ 
      {
        /// Other parameters here...
        "x-ms-summary": "Subject",
        /// Other parameters here...
      }
    ]
  }
},
```

<a name="description"></a>

## <a name="description"></a>description

Especifica una explicación detallada acerca de la funcionalidad de la operación o el formato y función de una entidad. </br>
Se aplica a: operaciones, parámetros, esquema de respuestas </br>
Recomendación: use el *tipo oración* para `description`. </br>
Ejemplo: "Esta operación se desencadena cuando se agrega un nuevo evento al calendario", "Especificar el asunto del mensaje de correo", etc.

!["description" para cada operación o entidad](./media/custom-connector-openapi-extensions/description.png)

``` json
"actions" {
  "Send_an_email": {
     "description": "Specify the subject of the mail",
     /// Other action properties here...
  }
},
```

<a name="visibility"></a>

## <a name="x-ms-visibility"></a>x-ms-visibility

Especifica la visibilidad de una entidad de cara al usuario. </br>
Los valores posibles son `important`, `advanced` y `internal` </br>
Se aplica a: operaciones, parámetros, esquemas

* Las operaciones y parámetros `important` son los primeros que se muestran al usuario.
* Las operaciones y parámetros `advanced` se ocultan en un menú adicional.
* Las operaciones y parámetros `internal` se ocultan al usuario.

> [!NOTE] 
> Para los parámetros `internal` y `required`, **debe** proporcionar valores predeterminados.

Ejemplo: el menú **Ver más** y **Mostrar opciones avanzadas** ocultan las operaciones y parámetros `advanced`.

!["x-ms-visibility" para mostrar u ocultar las operaciones y parámetros](./media/custom-connector-openapi-extensions/x-ms-visibility.png)

``` json
"actions" {
  "Send_an_email": {
     /// Other action properties here...
     "parameters": [
         {
           "name": "Subject",
           "type": "string",
           "description": "Specify the subject of the mail",
           "x-ms-summary": "Subject",
           "x-ms-visibility": "important",
           /// Other parameter properties here...
         }
     ]
     /// Other action properties here...
  }
},
```

<a name="x-ms-dynamic-values"></a>

## <a name="x-ms-dynamic-values"></a>x-ms-dynamic-values

Muestra una lista rellena para el usuario, para que este pueda seleccionar parámetros de entrada para una operación. </br>
Se aplica a: parámetros </br>
Uso: agregue el objeto `x-ms-dynamic-values` a la definición del parámetro. Por ejemplo, consulte este [ejemplo de OpenAPI](https://procsi.blob.core.windows.net/blog-images/sampleDynamicSwagger.json).

!["x-ms-dynamic-values" para mostrar listas](./media/custom-connector-openapi-extensions/x-ms-dynamic-values.png)

### <a name="properties-for-x-ms-dynamic-values"></a>Propiedades de x-ms-dynamic-values

| Nombre | Obligatorio u opcional | Descripción | 
| ---- | -------------------- | ----------- | 
| **operationID** | Obligatorio | La operación que se va a llamar para rellenar la lista | 
| **value-path** | Obligatorio | Una cadena de ruta de acceso en el objeto dentro de `value-collection` que hace referencia al valor del parámetro. </br>Si no se especifica `value-collection`, la respuesta se evalúa como una matriz. | 
| **value-title** | Opcional | Una cadena de ruta de acceso en el objeto dentro de `value-collection` que hace referencia a la descripción del valor. </br>Si no se especifica `value-collection`, la respuesta se evalúa como una matriz. | 
| **value-collection** | Opcional | Una cadena de ruta de acceso que se evalúa en una matriz de objetos en la carga útil de respuesta | 
| **parameters** | Opcional | Un objeto cuyas propiedades especifican los parámetros de entrada necesarios para invocar una operación de dynamic-values | 
|||| 

Este es un ejemplo en el que se muestra la propiedad en `x-ms-dynamic-values`:

``` json
"x-ms-dynamic-values": {
  "operationId": "PopulateDropdown",
  "value-path": "name",
  "value-title": "properties/displayName",
  "value-collection": "value",
  "parameters": {
     "staticParameter": "<value>",
     "dynamicParameter": {
        "parameter": "<value-to-pass-to-dynamicParameter>"
     }
  }
}
```

## <a name="example-all-the-openapi-extensions-up-to-this-point"></a>Ejemplo: todas las extensiones de OpenAPI hasta este momento

``` json
"/api/lists/{listID-dynamic}": {
    "get": {
        "description": "Get items from a single list - uses dynamic values and outputs dynamic schema",
        "summary": "Gets items from the selected list",
        "operationID": "GetListItems",
        "parameters": [
           {
             "name": "listID-dynamic",
             "type": "string",
             "in": "path",
             "description": "Select the list from where you want outputs",
             "required": true,
             "x-ms-summary": "Select List",
             "x-ms-dynamic-values": {
                "operationID": "GetLists",
                "value-path": "id",
                "value-title": "name"
             }
           }
        ]
    }
}
```

<a name="x-ms-dynamic schema"></a>

## <a name="x-ms-dynamic-schema"></a>x-ms-dynamic-schema

Indica que el esquema del parámetro actual o la respuesta son dinámicos. Este objeto puede invocar una operación que esté definida por el valor de este campo, detectar dinámicamente el esquema y mostrar la interfaz de usuario adecuada para recopilar entradas de usuario o para mostrar los campos disponibles. 

Se aplica a: parámetros, respuestas

Uso: agregue el objeto `x-ms-dynamic-schema` a la definición de un parámetro de solicitud o cuerpo de respuesta. Por ejemplo, consulte este [ejemplo de OpenAPI](https://procsi.blob.core.windows.net/blog-images/sampleDynamicSwagger.json).

Este ejemplo muestra cómo cambia el formulario de entrada, según el elemento que el usuario seleccione en la lista desplegable:

!["x-ms-dynamic-schema" para parámetros dinámicos](./media/custom-connector-openapi-extensions/x-ms-dynamic-schema.png)

Y este otro ejemplo muestra cómo cambian las salidas, según el elemento que el usuario seleccione en la lista desplegable. En esta versión, el usuario selecciona "Automóviles":

!["x-ms-dynamic-schema-response" para el elemento seleccionado "Automóviles"](./media/custom-connector-openapi-extensions/x-ms-dynamic-schema-output1.png)

En esta versión, el usuario selecciona "Comida":

!["x-ms-dynamic-schema-response" para el elemento seleccionado "Comida"](./media/custom-connector-openapi-extensions/x-ms-dynamic-schema-output2.png)

### <a name="properties-for-x-ms-dynamic-schema"></a>Propiedades de x-ms-dynamic-schema

| Nombre | Obligatorio u opcional | Descripción | 
| ---- | -------------------- | ----------- | 
| **operationID** | Obligatorio | La operación que se va a llamar para recuperar los cambios del esquema | 
| **parameters** | Obligatorio | Un objeto cuyas propiedades especifican los parámetros de entrada necesarios para invocar una operación de dynamic-schema | 
| **value-path** |Opcional | Una cadena de ruta de acceso que hace referencia a la propiedad que tiene el esquema. </br>Si no se especifica, se supone que la respuesta contiene el esquema en las propiedades del objeto raíz. | 
|||| 

Este es un ejemplo de un parámetro dinámico:

``` json
{
  "name": "dynamicListSchema",
  "in": "body",
  "description": "Dynamic schema for items in the selected list",
  "schema": {
    "type": "object",
    "x-ms-dynamic-schema": {
        "operationID": "GetListSchema",
        "parameters": {
          "listID": {
            "parameter": "listID-dynamic"
          }
        },
        "value-path": "items"
    }
  }
}
```

Este es un ejemplo de una respuesta dinámica:

``` json
"DynamicResponseGetListSchema": {
   "type": "object",
   "x-ms-dynamic-schema": {
      "operationID": "GetListSchema",
      "parameters": {
         "listID": {
            "parameter": "listID-dynamic"
         }
      },
      "value-path": "items"
    }
}
```

## <a name="next-steps"></a>Pasos siguientes

* [Describir los conectores y las API personalizadas](../logic-apps/custom-connector-api-postman-collection.md)
* [Logic Apps: Registrar el conector](../logic-apps/logic-apps-custom-connector-register.md)
* [Flow: Registro del conector personalizado](https://ms.flow.microsoft.com/documentation/register-custom-api/#register-your-custom-connector)