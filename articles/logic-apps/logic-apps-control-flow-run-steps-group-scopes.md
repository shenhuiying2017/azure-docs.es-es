---
title: "Ejecución de pasos según estados de acción agrupados: Azure Logic Apps | Microsoft Docs"
description: "Agrupar acciones en ámbitos y ejecutar pasos según el estado del grupo"
services: logic-apps
keywords: ramas, procesamiento paralelo
documentationcenter: 
author: ecfan
manager: anneta
editor: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/05/2018
ms.author: estfan; LADocs
ms.openlocfilehash: 052af45962f442e96ca28f05ffaa1b9814b2588b
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/05/2018
---
# <a name="scopes-run-steps-based-on-group-status-in-logic-apps"></a>Ámbitos: Ejecución de pasos según el estado de grupo de las aplicaciones lógicas

Para ejecutar pasos solo después de que otro grupo de acciones se haya realizado correctamente o con errores, ponga ese grupo dentro de un *ámbito*. Esta estructura es útil cuando desea organizar acciones como un grupo lógico, evaluar el estado del grupo y realizar acciones basadas en el estado del ámbito. Una vez que todas las acciones de un ámbito acaben de ejecutarse, el ámbito obtiene también su propio estado. Por ejemplo, puede usar ámbitos cuando desea implementar el [control de errores y excepciones](../logic-apps/logic-apps-exception-handling.md#scopes). 

Para comprobar el estado del ámbito, puede usar los mismos criterios que usa para determinar el estado de ejecución de la aplicación lógica, como "Succeeded", "Failed", "Cancelled", etc. De forma predeterminada, cuando las acciones del ámbito se ejecutan correctamente, el estado del ámbito se marca como "Succeeded". Pero si cualquier acción en el ámbito produce error o se cancela, el estado del ámbito se marca como "Failed". Para conocer los límites en los ámbitos, consulte [Límites y configuración](../logic-apps/logic-apps-limits-and-config.md). 

Por ejemplo, aquí hay una aplicación lógica de alto nivel que utiliza un ámbito para ejecutar acciones específicas y una condición para comprobar el estado del ámbito. Si una acción del ámbito genera un error o finaliza inesperadamente, el ámbito se marca como "Failed" o "Aborted" respectivamente, y la aplicación lógica envía un mensaje de "Scope failed" (Error de ámbito). Si todas las acciones del ámbito se realizan correctamente, la aplicación lógica enviará un mensaje de "Scope succeeded" (El ámbito se realizó correctamente).

![Configurar desencadenador "Programación: Periodicidad"](./media/logic-apps-control-flow-run-steps-group-scopes/scope-high-level.png)

## <a name="prerequisites"></a>requisitos previos

Para seguir el ejemplo de este artículo, necesita estos elementos:

* Una suscripción de Azure. Si aún no tiene una, [regístrese para obtener una cuenta de Azure gratuita](https://azure.microsoft.com/free/). 

* Una cuenta de correo electrónico de cualquier proveedor de correo electrónico compatible con Logic Apps. Este ejemplo utiliza Outlook.com. Si utiliza un proveedor diferente, el flujo general sigue siendo el mismo, pero la interfaz de usuario es diferente.

* Una clave de Mapas de Bing. Para obtener esta clave, consulte cómo <a href="https://msdn.microsoft.com/library/ff428642.aspx" target="_blank">obtener una clave de Mapas de Bing</a>.

* Conocimientos básicos acerca de [cómo crear aplicaciones lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="create-sample-logic-app"></a>Creación de una aplicación lógica de ejemplo

En primer lugar, cree esta aplicación lógica de ejemplo para que pueda agregar un ámbito después:

![Creación de una aplicación lógica de ejemplo](./media/logic-apps-control-flow-run-steps-group-scopes/finished-sample-app.png)

* Un desencadenador **Programación: Periodicidad** que comprueba el servicio Mapas de Bing en un intervalo que puede especificar
* Una acción **Bing Maps - Get route** (Mapas de Bing: Obtener ruta) que comprueba el tiempo de viaje entre dos ubicaciones
* Una instrucción condicional que comprueba si el tiempo de viaje supera el tiempo de viaje especificado
* Una acción que le envía un correo electrónico indicando que el tiempo de viaje actual supera el tiempo especificado

Puede guardar la aplicación lógica en cualquier momento, así que guarde su trabajo con frecuencia.

1. Inicie sesión en <a href="https://portal.azure.com" target="_blank">Azure Portal</a> si aún no lo ha hecho. Crear una aplicación lógica en blanco.

2. Agregue el desencadenador **Programación: Periodicidad** con estos valores: **Intervalo** = "1" y **Frecuencia** = "Minuto"

   ![Configurar desencadenador "Programación: Periodicidad"](./media/logic-apps-control-flow-run-steps-group-scopes/recurrence.png)

   > [!TIP]
   > Para simplificar visualmente la vista y ocultar los detalles de cada acción en el diseñador, contraiga la forma de cada acción a medida que recorre estos pasos.

3. Agregue la acción **Bing Maps - Get route** (Mapas de Bing: Obtener ruta). 

   1. Si no tiene una conexión de Mapas de Bing, se le solicitará que cree una.

      | Configuración | Valor | DESCRIPCIÓN |
      | ------- | ----- | ----------- |
      | **Nombre de la conexión** | BingMapsConnection | Proporcione un nombre para la conexión. | 
      | **Clave de API** | <*su-clave-de-Mapas-de-Bing*> | Escriba la clave de Mapas de Bing recibida previamente. | 
      ||||  

   2. Configure la acción **Get route** (Obtener ruta) tal como se muestra en la tabla que aparece a continuación de esta imagen:

      ![Configurar acción "Bing Maps - Get route" (Mapas de Bing: Obtener ruta)](./media/logic-apps-control-flow-run-steps-group-scopes/get-route.png) 

      Para más información acerca de estos parámetros, consulte [Calculate a route](https://msdn.microsoft.com/library/ff701717.aspx) (Cálculo de una ruta).

      | Configuración | Valor | DESCRIPCIÓN |
      | ------- | ----- | ----------- |
      | **Waypoint 1** (Punto de referencia 1) | <*inicio*> | Escriba el origen de la ruta. | 
      | **Waypoint 2** (Punto de referencia 2) | <*fin*> | Escriba el destino de la ruta. | 
      | **Avoid** (Evitar) | None | Escriba los elemento a evitar en la ruta, como autopistas, peajes, etc. Para los valores posibles, consulte [Calculate a route](https://msdn.microsoft.com/library/ff701717.aspx) (Calcular una ruta). | 
      | **Optimize** (Optimizar) | timeWithTraffic | Seleccione un parámetro para optimizar la ruta, como distancia, información sobre el tiempo de trayecto con el tráfico actual, etc. Este ejemplo utiliza este valor: "timeWithTraffic" | 
      | **Distance unit** (Unidad de distancia) | <*su preferencia*> | Especifique la unidad de distancia para calcular la ruta. Este ejemplo utiliza este valor: "Mile" (Millas) | 
      | **Travel mode** (Modo de desplazamiento) | Conducción | Escriba el modo de desplazamiento para la ruta. Este ejemplo utiliza este valor: "Driving" (Conducción) | 
      | **Transit Date-Time** (Fecha y hora de tránsito) | None | Se aplica solo al modo de tránsito. | 
      | **Transit Date-Type Type** (Fecha y tipo de tránsito) | None | Se aplica solo al modo de tránsito. | 
      ||||  

4. Agregue una condición para comprobar si el tiempo de trayecto con el tráfico actual supera el tiempo especificado. En este ejemplo, siga los pasos que aparecen a continuación de esta imagen:

   ![Crear condición](./media/logic-apps-control-flow-run-steps-group-scopes/build-condition.png)

   1. Cambie el nombre de la condición con esta descripción: **If traffic time more than specified time** (Si el tiempo de trayecto es mayor que el tiempo especificado)

   2. En la lista de parámetros, seleccione el campo **Travel Duration Traffic** (Tráfico de duración del desplazamiento), que está en segundos. 

   3. En el operador de comparación, seleccione este operador: **es mayor que**

   4. Como valor de comparación, escriba **600**, que está en segundos y equivale a 10 minutos.

5. En la rama **If true** de la condición, agregue una acción "send email" (enviar correo electrónico) para su proveedor de correo electrónico. Configure esta acción con la información que aparece en la tabla que sigue a esta imagen:

   ![Agregar acción "Send an email" (Enviar un correo electrónico) a la rama "If true"](./media/logic-apps-control-flow-run-steps-group-scopes/send-email.png)

   1. En el campo **Para**, escriba su dirección de correo electrónico con fines de prueba.

   2. En el campo **Asunto**, escriba este texto:

      ```Time to leave: Traffic more than 10 minutes```

   3. En el campo **Cuerpo**, escriba este texto con un espacio final: 

      ```Travel time: ```

      Mientras el cursor aparece en el campo **Cuerpo**, la lista de contenido dinámica permanece abierta para que pueda seleccionar los parámetros que estén disponibles en este momento.

   4. En la lista de contenido dinámico, elija **Expresión**.

   5. Busque y seleccione la función **div( )**.

   6. Mientras el cursor se encuentra entre los paréntesis de la función, elija **Contenido dinámico** para que pueda agregar el parámetro **Travel Duration Traffic** (Tráfico de duración del desplazamiento) a continuación.

   7. En **Get route** (Obtener ruta) en la lista dinámica de parámetros, seleccione el campo **Travel Duration Traffic** (Tráfico de duración del desplazamiento).

      ![Seleccionar "Travel Duration Traffic" (Tráfico de duración del desplazamiento)](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-2.png)

   8. Una vez que el campo se resuelve en formato JSON, agregue una **coma** (```,```) seguida del número ```60``` para que convierta el valor de **Travel Duration Traffic** (Tráfico de duración del desplazamiento) de segundos a minutos. 
   
      ```
      div(body('Get_route')?['travelDurationTraffic'],60)
      ```

      Ahora, la expresión es similar a este ejemplo:

      ![Finalizar expresión](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-3.png)  

   9. Asegúrese de que elige **Aceptar** cuando haya terminado.

  10. Una vez que la expresión se resuelve, agregue este texto con un espacio inicial: ``` minutes```
  
      Ahora, el campo **Cuerpo** es similar a este ejemplo:

      ![Campo "Cuerpo" terminado](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-4.png)

6. Guarde la aplicación lógica.

A continuación, agregue un ámbito para que pueda agrupar acciones específicas y evaluar su estado.

## <a name="add-a-scope"></a>Agregar un ámbito

1. Si aún no lo ha hecho, abra la aplicación lógica en el Diseñador de aplicación lógica. 

2. Agregue un ámbito a la ubicación del flujo de trabajo que quiera. Por ejemplo: 

   * Para agregar un ámbito entre pasos existentes en el flujo de trabajo de la aplicación lógica, mueva el puntero sobre la flecha en la que quiere agregar el ámbito. 
   Elija el **signo más** (**+**) > **Agregar un ámbito**.

     ![Agregar un ámbito](./media/logic-apps-control-flow-run-steps-group-scopes/add-scope.png)

     Si desea agregar un ámbito al final del flujo de trabajo actual, en la parte inferior de la aplicación lógica elija **+ Nuevo paso** > **...Más** > **Agregar un ámbito**.

3. Ahora agregue los pasos o arrastre los pasos existentes que desea ejecutar dentro del ámbito. En este ejemplo, arrastre estas acciones al ámbito:
      
   * **Get route** (Obtener ruta)
   * **If traffic time more than specified time** (Si el tiempo de trayecto es mayor que el tiempo especificado), que incluye las dos ramas: **true** y **false**

   Ahora, la aplicación lógica es similar a este ejemplo:

   ![Ámbito agregado](./media/logic-apps-control-flow-run-steps-group-scopes/scope-added.png)

4. En el ámbito, agregue una condición que compruebe el estado del ámbito. Cambie el nombre de la condición por esta descripción: **If scope failed** (Si el ámbito es erróneo)

   ![Agregar condición para comprobar el estado del ámbito](./media/logic-apps-control-flow-run-steps-group-scopes/add-condition-check-scope-status.png)
  
5. Cree esta expresión que comprueba si el estado del ámbito es igual a `Failed` o `Aborted`.

   ![Agregar expresión que comprueba el estado del ámbito](./media/logic-apps-control-flow-run-steps-group-scopes/build-expression-check-scope-status.png)

   O bien, para escribir esta expresión como texto, elija **Editar en el modo avanzado**.

   ```@equals('@result(''Scope'')[0][''status'']', 'Failed, Aborted')```

6. En las ramas **If true** y **If false**, agregue las acciones que desea realizar, por ejemplo, enviar correo electrónico o un mensaje.

   ![Agregar expresión que comprueba el estado del ámbito](./media/logic-apps-control-flow-run-steps-group-scopes/handle-true-false-branches.png)

7. Guarde la aplicación lógica.

La aplicación lógica terminada es similar ahora a este ejemplo con todas las formas expandidas:

![Aplicación lógica terminada con ámbito](./media/logic-apps-control-flow-run-steps-group-scopes/scopes-overview.png)

## <a name="test-your-work"></a>Comprobación del trabajo

En la barra de herramientas del diseñador, elija **Ejecutar**. Si todas las acciones del ámbito se realizan correctamente, recibirá un mensaje de "Scope succeeded" (El ámbito se realizó correctamente). Si alguna de las acciones del ámbito no se realiza correctamente, recibirá un mensaje de "Scope failed" (El ámbito produjo error). 

<a name="scopes-json"></a>

## <a name="json-definition"></a>Definición JSON

Si está trabajando en la vista de código, puede definir la estructura de un ámbito en la definición JSON de la aplicación lógica en su lugar. Por ejemplo, aquí está la definición JSON para el desencadenador y las acciones de la aplicación lógica anterior:

``` json
"triggers": {
  "Recurrence": {
    "type": "Recurrence",
    "recurrence": {
       "frequency": "Minute",
       "interval": 1
    },
  }
}
```

```json
"actions": {
  "If_scope_failed": {
    "type": "If",
    "actions": {
      "Scope_failed": {
        "type": "ApiConnection",
        "inputs": {
          "body": {
            "Body": "Scope failed",
            "Subject": "Scope failed",
            "To": "<your-email@domain.com>"
          },
          "host": {
            "connection": {
              "name": "@parameters('$connections')['outlook']['connectionId']"
            }
          },
          "method": "post",
          "path": "/Mail"
        },
        "runAfter": {}
      }
    },
    "else": {
      "actions": {
        "Scope_succeded": {
          "type": "ApiConnection",
          "inputs": {
            "body": {
              "Body": "None",
              "Subject": "Scope succeeded",
              "To": "<your-email@domain.com>"
            },
            "host": {
              "connection": {
               "name": "@parameters('$connections')['outlook']['connectionId']"
              }
            },
            "method": "post",
            "path": "/Mail"
          },
          "runAfter": {}
        }
      }
    },
    "expression": "@equals('@result(''Scope'')[0][''status'']', 'Failed, Aborted')",
    "runAfter": {
      "Scope": [
        "Succeeded"
      ]
    }
  },
  "Scope": {
    "type": "Scope",
    "actions": {
      "Get_route": {
        "type": "ApiConnection",
        "inputs": {
          "host": {
            "connection": {
              "name": "@parameters('$connections')['bingmaps']['connectionId']"
            }
          },
          "method": "get",
          "path": "/REST/V1/Routes/Driving",
          "queries": {
            "distanceUnit": "Mile",
            "optimize": "timeWithTraffic",
            "travelMode": "Driving",
            "wp.0": "<start>",
            "wp.1": "<end>"
          }
        },
        "runAfter": {}
      },
      "If_traffic_time_more_than_specified_time": {
        "type": "If",
        "actions": {
          "Send_mail_when_traffic_exceeds_10_minutes": {
            "type": "ApiConnection",
            "inputs": {
              "body": {
                 "Body": "Travel time:@{div(body('Get_route')?['travelDurationTraffic'], 60)} minutes",
                 "Subject": "Time to leave: Traffic more than 10 minutes",
                 "To": "<your-email@domain.com>"
              },
              "host": {
                "connection": {
                   "name": "@parameters('$connections')['outlook']['connectionId']"
                }
              },
              "method": "post",
              "path": "/Mail"
            },
            "runAfter": {}
          }
        },
        "expression": "@greater(body('Get_route')?['travelDurationTraffic'], 600)",
        "runAfter": {
          "Get_route": [
            "Succeeded"
          ]
        }
      }
    },
    "runAfter": {}
  }
}
```

## <a name="get-support"></a>Obtención de soporte técnico

* Si tiene alguna duda, visite el [foro de Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para enviar características y sugerencias o votar las que ya se han enviado, visite el [sitio web de comentarios de los usuarios de Azure Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Pasos siguientes

* [Ejecución de pasos en función de una condición (instrucciones condicionales)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Realización de pasos en función de los diferentes valores (instrucciones switch)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Ejecución y repetición de pasos (bucles)](../logic-apps/logic-apps-control-flow-loops.md)
* [Ejecución o combinación de pasos en paralelo (ramas)](../logic-apps/logic-apps-control-flow-branches.md)