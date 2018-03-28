---
title: Creación, edición o extensión de JSON para definiciones de aplicaciones lógicas en Azure Logic Apps | Microsoft Docs
description: Creación y personalización de definiciones de aplicaciones lógicas en JSON
author: ecfan
manager: SyntaxC4
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/01/2018
ms.author: estfan; LADocs
ms.openlocfilehash: bde275eb75c97da2a99109484b46b599a5b2f871
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/16/2018
---
# <a name="create-edit-or-customize-json-for-logic-app-definitions"></a>Creación, edición o personalización de JSON para definiciones de aplicaciones lógicas

Al crear soluciones de integración empresarial con flujos de trabajo automatizados en [Azure Logic Apps](../logic-apps/logic-apps-overview.md), las definiciones de aplicaciones lógicas subyacentes usan una notación de objetos JavaScript (JSON) sencilla y declarativa con el [esquema del lenguaje de definición de flujo de trabajo](../logic-apps/logic-apps-workflow-definition-language.md) para su descripción y validación. Estos formatos facilitan la lectura y comprensión de las definiciones de aplicaciones lógicas sin necesidad de saber mucho sobre el código. Si desea automatizar la creación e implementación de aplicaciones lógicas, puede incluir las definiciones de aplicaciones lógicas como [recursos de Azure](../azure-resource-manager/resource-group-overview.md) en las [plantillas de Azure Resource Manager](../azure-resource-manager/resource-group-overview.md#template-deployment). Para crear, administrar e implementar las aplicaciones lógicas, puede usar [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.logicapp), la [CLI de Azure](../azure-resource-manager/resource-group-template-deploy-cli.md) o las [API de REST de Azure Logic Apps](https://docs.microsoft.com/rest/api/logic/).

Para trabajar con definiciones de aplicaciones lógicas en JSON, abra el editor de la vista Código cuando trabaje en Azure Portal o en Visual Studio, o bien copie la definición en cualquier editor que desee. Si es la primera vez que interactúa con las aplicaciones lógicas, consulte el artículo sobre [cómo crear la primera aplicación lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

> [!NOTE]
> Algunas funcionalidades de Azure Logic Apps, como definir parámetros y varios desencadenadores en las definiciones de aplicaciones lógicas, solo están disponibles en JSON, no en el Diseñador de aplicaciones lógicas. Por tanto, para estas tareas, debe trabajar en la vista Código o en otro editor.

## <a name="edit-json---azure-portal"></a>Edición de JSON: Azure Portal

1. Inicie sesión en el <a href="https://portal.azure.com" target="_blank">Azure Portal</a>.

2. En el menú de la izquierda, elija **Todos los servicios**. En el cuadro de búsqueda, busque "Logic Apps" y, a continuación, en los resultados, seleccione la aplicación lógica.

3. En el menú de la aplicación lógica, en **Herramientas de desarrollo**, seleccione **Vista de código de aplicación lógica**.

   El editor de la vista Código se abre y en él se muestra la definición de la aplicación lógica en formato JSON.

## <a name="edit-json---visual-studio"></a>Edición de JSON: Visual Studio

Para poder trabajar en la definición de aplicación lógica en Visual Studio, asegúrese de haber [instalado las herramientas necesarias](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites). Para crear una aplicación lógica con Visual Studio, consulte [Quickstart: Automate tasks and processes with Azure Logic Apps - Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) (Inicio rápido: automatización de tareas y procesos con Azure Logic Apps - Visual Studio).

En Visual Studio, puede abrir las aplicaciones lógicas creadas e implementadas directamente desde Azure Portal o como proyectos de Azure Resource Manager desde Visual Studio.

1. Abra la solución de Visual Studio o el proyecto del [grupo de recursos de Azure](../azure-resource-manager/resource-group-overview.md) que contiene la aplicación lógica.

2. Busque y abra la definición de la aplicación lógica, que aparece de forma predeterminada en una [plantilla de Resource Manager](../azure-resource-manager/resource-group-overview.md#template-deployment) denominada **LogicApp.json**. Puede usar y personalizar esta plantilla para implementarla en diferentes entornos.

3. Abra el menú contextual para la definición de la aplicación lógica o la plantilla. Seleccione **Open with Logic App Designer** (Abrir con diseñador de aplicación lógica).

   ![Abrir la aplicación lógica en una solución de Visual Studio](./media/logic-apps-author-definitions/open-logic-app-designer.png)

4. En la parte inferior del diseñador, seleccione la **vista Código**. 

   El editor de la vista Código se abre y en él se muestra la definición de la aplicación lógica en formato JSON.

5. Para volver a la vista del diseñador, en la parte inferior del editor de la vista Código, elija **Diseño**.

## <a name="parameters"></a>Parámetros

Los parámetros le permite volver a usar los valores en toda la aplicación lógica y son adecuados para reemplazar los valores que podría cambiar con frecuencia. Por ejemplo, si tiene una dirección de correo electrónico que desea usar en varios lugares, debe definirla como un parámetro. 

Los parámetros también resultan útiles cuando es necesario reemplazar los parámetros de varios entornos. Obtenga más información sobre los [parámetros para implementación](#deployment-parameters) y la [documentación de API de REST para Azure Logic Apps](https://docs.microsoft.com/rest/api/logic).

> [!NOTE]
> Los parámetros solo están disponibles en la vista de código.

En la [primera aplicación lógica de ejemplo](../logic-apps/quickstart-create-first-logic-app-workflow.md), creó un flujo de trabajo que envía correos electrónicos cuando aparecen publicaciones nuevas en la fuente RSS de un sitio web. La dirección URL de la fuente está codificada de manera rígida, por lo que este ejemplo muestra cómo reemplazar el valor de la consulta por un parámetro para que sea más sencillo cambiar la dirección URL de la fuente.

1. En la vista de código, busque el objeto `parameters : {}` y agregue un objeto `currentFeedUrl`:

   ``` json
     "currentFeedUrl" : {
      "type" : "string",
            "defaultValue" : "http://rss.cnn.com/rss/cnn_topstories.rss"
   }
   ```

2. En la acción `When_a_feed-item_is_published`, busque la sección `queries` y reemplace el valor de la consulta por `"feedUrl": "#@{parameters('currentFeedUrl')}"`. 

   **Antes**
   ``` json
   }
      "queries": {
          "feedUrl": "https://s.ch9.ms/Feeds/RSS"
       }
   },   
   ```

   **Después**
   ``` json
   }
      "queries": {
          "feedUrl": "#@{parameters('currentFeedUrl')}"
       }
   },   
   ```

   Para combinar dos o más cadenas, también puede usar la función `concat`. 
   Por ejemplo, `"@concat('#',parameters('currentFeedUrl'))"` funciona igual que en el ejemplo anterior.

3.  Cuando termine, seleccione **Guardar**. 

Ahora puede cambiar la fuente RSS del sitio web pasando una dirección URL diferente a través del objeto `currentFeedURL`.

<a name="deployment-parameters"></a>

## <a name="deployment-parameters-for-different-environments"></a>Parámetros de implementación para entornos diferentes

Por lo general, un ciclo de vida de implementación tiene entornos de desarrollo, de ensayo y de producción. Por ejemplo, puede usar la misma definición de aplicación lógica en todos estos entornos, pero usar bases de datos distintas. Del mismo modo, es posible que desee usar la misma definición en muchas regiones diferentes para lograr una alta disponibilidad, pero que cada instancia de aplicación lógica use la base de datos de esa región. 

> [!NOTE] 
> Este escenario no tiene nada que ver con tomar parámetros en *entorno de tiempo de ejecución* donde debería usar la función `trigger()` en su lugar.

Esta es una definición básica:

``` json
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "uri": {
            "type": "string"
        }
    },
    "triggers": {
        "request": {
          "type": "request",
          "kind": "http"
        }
    },
    "actions": {
        "readData": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@parameters('uri')"
            }
        }
    },
    "outputs": {}
}
```
En la solicitud `PUT` real para las aplicaciones lógicas, puede proporcionar el parámetro `uri`. En cada entorno puede proporcionar un valor diferente para el parámetro `connection`. Dado que ya no existe un valor predeterminado, la carga útil de la aplicación lógica requiere este parámetro:

``` json
{
    "properties": {},
        "definition": {
          /// Use the definition from above here
        },
        "parameters": {
            "connection": {
                "value": "https://my.connection.that.is.per.enviornment"
            }
        }
    },
    "location": "westus"
}
``` 

Para más información, consulte la [documentación de API de REST para Azure Logic Apps](https://docs.microsoft.com/rest/api/logic/).

## <a name="process-strings-with-functions"></a>Procesamiento de cadenas con funciones

Logic Apps tiene diversas funciones para trabajar con las cadenas. Por ejemplo, imagine que desea pasar el nombre de una empresa de un pedido a otro sistema. Sin embargo, no está seguro de cómo se debe controlar adecuadamente la codificación de caracteres. Podría llevar a cabo la codificación Base64 en esta cadena, pero para evitar que aparezcan caracteres de escape en la dirección URL, puede reemplazar varios caracteres. Además, solo necesita una subcadena para el nombre de la empresa, porque no se usan los cinco primeros caracteres. 

``` json
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "order": {
      "defaultValue": {
        "quantity": 10,
        "id": "myorder1",
        "companyName": "NAME=Contoso"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "request": {
      "type": "Request",
      "kind": "Http"
    }
  },
  "actions": {
    "order": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').companyName,5,sub(length(parameters('order').companyName), 5) )),'+','-') ,'/' ,'_' )}"
      }
    }
  },
  "outputs": {}
}
```

En estos pasos se describe cómo este ejemplo procesa esta cadena, desde adentro hacia afuera:

``` 
"uri": "http://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').companyName,5,sub(length(parameters('order').companyName), 5) )),'+','-') ,'/' ,'_' )}"
```

1. Obtenga el valor de [`length()`](../logic-apps/logic-apps-workflow-definition-language.md) para el nombre de la empresa, con lo que se obtiene el número total de caracteres.

2. Para obtener una cadena más corta, reste `5`.

3. Ahora obtiene [`substring()`](../logic-apps/logic-apps-workflow-definition-language.md). Comience con el índice `5` y vaya al resto de la cadena.

4. Convierta esta subcadena en una cadena [`base64()`](../logic-apps/logic-apps-workflow-definition-language.md).

5. Ahora [`replace()`](../logic-apps/logic-apps-workflow-definition-language.md) todos los caracteres `+` por caracteres `-`.

6. Por último, [`replace()`](../logic-apps/logic-apps-workflow-definition-language.md) todos los caracteres `/` por caracteres `_`.

## <a name="map-list-items-to-property-values-then-use-maps-as-parameters"></a>Asignación de los elementos de lista a valores de propiedad para luego usar los mapas como parámetros

Para obtener resultados distintos en función del valor de una propiedad, puede crear un mapa que coincida con cada valor de propiedad a un resultado y luego usar ese mapa como parámetro. 

Por ejemplo, este flujo de trabajo define algunas categorías como parámetros y un mapa que coincide con dichas categorías con una dirección URL específica. En primer lugar, el flujo de trabajo obtiene una lista de artículos. Luego, el flujo de trabajo usa el mapa para encontrar la dirección URL que coincide con la categoría de cada artículo.

*   La función [`intersection()`](../logic-apps/logic-apps-workflow-definition-language.md) comprueba si la categoría coincide con una categoría definida conocida.

*   Después de obtener una categoría coincidente, el ejemplo extrae el elemento del mapa a través de corchetes: `parameters[...]`

``` json
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "specialCategories": {
      "defaultValue": [
        "science",
        "google",
        "microsoft",
        "robots",
        "NSA"
      ],
      "type": "Array"
    },
    "destinationMap": {
      "defaultValue": {
        "science": "http://www.nasa.gov",
        "microsoft": "https://www.microsoft.com/en-us/default.aspx",
        "google": "https://www.google.com",
        "robots": "https://en.wikipedia.org/wiki/Robot",
        "NSA": "https://www.nsa.gov/"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "Request": {
      "type": "Request",
      "kind": "http"
    }
  },
  "actions": {
    "getArticles": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "https://ajax.googleapis.com/ajax/services/feed/load?v=1.0&q=http://feeds.wired.com/wired/index"
      }
    },
    "forEachArticle": {
      "type": "foreach",
      "foreach": "@body('getArticles').responseData.feed.entries",
      "actions": {
        "ifGreater": {
          "type": "if",
          "expression": "@greater(length(intersection(item().categories, parameters('specialCategories'))), 0)",
          "actions": {
            "getSpecialPage": {
              "type": "Http",
              "inputs": {
                "method": "GET",
                "uri": "@parameters('destinationMap')[first(intersection(item().categories, parameters('specialCategories')))]"
              }
            }
          }
        }
      },
      "runAfter": {
        "getArticles": [
          "Succeeded"
        ]
      }
    }
  }
}
```

## <a name="get-data-with-date-functions"></a>Obtención de datos con funciones de fecha

Para obtener datos de un origen de datos que no admite *desencadenadores* de manera nativa, en su lugar puede usar las funciones de fecha para trabajar con horas y fecha. Por ejemplo, con esta expresión se sabe cuánto demoran los pasos de este flujo de trabajo, de adentro hacia afuera:

``` json
"expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))",
```

1. En la acción `order`, extraiga el valor de `startTime`. 
2. Obtenga la hora actual con `utcNow()`.
3. Reste un segundo:

   [`addseconds(..., -1)`](../logic-apps/logic-apps-workflow-definition-language.md) 

   Puede usar otras unidades de tiempo, como `minutes` o `hours`. 

3. Ahora puede comparar estos dos valores. 

   Si el primero es inferior al segundo, significa que ha transcurrido más de un segundo desde la primera vez que se realizó el pedido.

Para dar formato a las fechas, puede usar formateadores de cadena. Por ejemplo, para obtener RFC1123, use [`utcnow('r')`](../logic-apps/logic-apps-workflow-definition-language.md). Más información sobre el [formato de fecha](../logic-apps/logic-apps-workflow-definition-language.md).

``` json
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "order": {
      "defaultValue": {
        "quantity": 10,
        "id": "myorder-id"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "Request": {
      "type": "request",
      "kind": "http"
    }
  },
  "actions": {
    "order": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://www.example.com/?id=@{parameters('order').id}"
      }
    },
    "ifTimingWarning": {
      "type": "If",
      "expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))",
      "actions": {
        "timingWarning": {
          "type": "Http",
          "inputs": {
            "method": "GET",
            "uri": "http://www.example.com/?recordLongOrderTime=@{parameters('order').id}&currentTime=@{utcNow('r')}"
          }
        }
      },
      "runAfter": {
        "order": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```


## <a name="next-steps"></a>Pasos siguientes

* [Ejecución de pasos en función de una condición (instrucciones condicionales)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Realización de pasos en función de los diferentes valores (instrucciones switch)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Ejecución y repetición de pasos (bucles)](../logic-apps/logic-apps-control-flow-loops.md)
* [Ejecución o combinación de pasos en paralelo (ramas)](../logic-apps/logic-apps-control-flow-branches.md)
* [Run steps based on grouped action status (scopes)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md) (Ejecución de pasos según un estado de acciones agrupadas [ámbitos])
* Más información sobre el [esquema del lenguaje de definición de flujo de trabajo para Azure Logic Apps](../logic-apps/logic-apps-workflow-definition-language.md)
* Más información [sobre los desencadenadores y las acciones de flujo de trabajo para Logic Apps](../logic-apps/logic-apps-workflow-actions-triggers.md)