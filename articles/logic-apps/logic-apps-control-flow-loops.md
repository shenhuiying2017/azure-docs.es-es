---
title: "Bucles: matrices de procesos o repetición de acciones en Azure Logic Apps | Microsoft Docs"
description: "Matrices de procesos con bucles \"for each\" o repetición de acciones hasta que se cumplen determinadas condiciones en Logic Apps"
services: logic-apps
keywords: bucles for each
documentationcenter: 
author: ecfan
manager: anneta
editor: 
ms.assetid: 75b52eeb-23a7-47dd-a42f-1351c6dfebdc
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/05/2018
ms.author: estfan; LADocs
ms.openlocfilehash: f634b1004fef2eb65c6b8134088ceead47c91890
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/05/2018
---
# <a name="loops-process-arrays-or-repeat-actions-until-a-condition-is-met"></a>Bucles: Matrices de procesos o repetición de acciones hasta que se cumple una condición

Para recorrer en iteración las matrices de la aplicación lógica, puede usar un [bucle "Foreach"](#foreach-loop) o un [bucle secuencial "Foreach"](#sequential-foreach-loop). Los ciclos de un bucle "Foreach" estándar se ejecutan en paralelo, mientras que los ciclos de un bucle "Foreach" secuencial se ejecutan de uno en uno. Para conocer el número máximo de elementos de matriz que los bucles "Foreach" pueden procesar en una única ejecución de aplicación lógica, consulte [Límites y configuración](../logic-apps/logic-apps-limits-and-config.md). 

> [!TIP] 
> Si tiene un desencadenador que recibe una matriz y desea ejecutar un flujo de trabajo para cada elemento de matriz, puede *desagrupar* esa matriz con la propiedad de desencadenador [**SplitOn**](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch). 
  
Para repetir acciones hasta que se cumpla una condición o cambie algún estado, use un [bucle "Until"](#until-loop). La aplicación lógica realiza primero todas las acciones dentro del bucle y, después, comprueba la condición como último paso. Si se cumple la condición, se detiene el bucle. En caso contrario, se repite el bucle. Para conocer el número máximo de bucles "Until" en una única ejecución de aplicación lógica, consulte [Límites y configuración](../logic-apps/logic-apps-limits-and-config.md). 

## <a name="prerequisites"></a>requisitos previos

* Una suscripción de Azure. Si aún no tiene una, [regístrese para obtener una cuenta de Azure gratuita](https://azure.microsoft.com/free/). 

* Conocimientos básicos acerca de [cómo crear aplicaciones lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="foreach-loop"></a>

## <a name="foreach-loop"></a>Bucle "Foreach"

Para repetir acciones para cada elemento de una matriz, use un bucle "Foreach" en el flujo de trabajo de la aplicación lógica. Puede incluir varias acciones en un bucle "Foreach" y se pueden anidar bucles "Foreach" dentro de otros. De forma predeterminada, los ciclos de un bucle "Foreach" estándar se ejecutan en paralelo. Para ver el número máximo de ciclos paralelos que los bucles "Foreach" pueden ejecutar, consulte [Límites y configuración](../logic-apps/logic-apps-limits-and-config.md).

> [!NOTE] 
> Un bucle "Foreach" solo funciona con una matriz y las acciones del bucle emplean la referencia `@item()` para procesar cada elemento de la matriz. Si especifica datos que no están en una matriz, se producirá un error en el flujo de trabajo de la aplicación lógica. 

Por ejemplo, esta aplicación lógica le envía un resumen diario de la fuente RSS de un sitio web. La aplicación utiliza un bucle "Foreach" que envía un correo electrónico para cada nuevo elemento encontrado.

1. [Cree una aplicación lógica de ejemplo](../logic-apps/quickstart-create-first-logic-app-workflow.md) con una cuenta de Outlook.com o de Office 365 Outlook.

2. Entre el desencadenador RSS y la acción para enviar un correo electrónico, agregue un bucle "Foreach". 

   Para agregar un bucle entre pasos, mueva el puntero sobre la flecha en la que desee agregar el bucle. 
   Haga clic en el **signo más** (**+**) que aparece y seleccione **Add a for each** (Agregar un bucle "Foreach").

   ![Agregar un bucle "Foreach" entre pasos](media/logic-apps-control-flow-loops/add-for-each-loop.png)

3. Ahora, compile el bucle. En **Select an output from previous steps** (Seleccionar una salida de los pasos anteriores) después de que aparezca la lista **Agregar contenido dinámico**, seleccione la matriz **Vínculos de fuente**, que es la salida del desencadenador RSS. 

   ![Seleccionar de la lista de contenido dinámico](media/logic-apps-control-flow-loops/for-each-loop-dynamic-content-list.png)

   > [!NOTE] 
   > *Solo* puede seleccionar salidas de matriz del paso anterior.

   La matriz seleccionada ahora aparece aquí:

   ![Seleccionar matriz](media/logic-apps-control-flow-loops/for-each-loop-select-array.png)

4. Para realizar una acción en cada elemento de matriz, arrastre la acción **Enviar un correo electrónico** al bucle **Foreach**. 

   La aplicación lógica podría ser similar a la de este ejemplo:

   ![Agregar pasos a un bucle "Foreach"](media/logic-apps-control-flow-loops/for-each-loop-with-step.png)

5. Guarde la aplicación lógica. Para probar manualmente la aplicación lógica, en la barra de herramientas del diseñador, elija **Ejecutar**.

<a name="for-each-json"></a>

## <a name="foreach-loop-definition-json"></a>Definición de bucle "Foreach" (JSON)

Si está trabajando en la vista de código de la aplicación lógica, puede definir el bucle `Foreach` en la definición JSON de la aplicación lógica en su lugar, por ejemplo:

``` json
"actions": {
    "myForEachLoopName": {
        "type": "Foreach",
        "actions": {
            "Send_an_email": {
                "type": "ApiConnection",
                "inputs": {
                    "body": {
                        "Body": "@{item()}",
                        "Subject": "New CNN post @{triggerBody()?['publishDate']}",
                        "To": "me@contoso.com"
                    },
                    "host": {
                        "api": {
                            "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/office365"
                        },
                        "connection": {
                            "name": "@parameters('$connections')['office365']['connectionId']"
                        }
                    },
                    "method": "post",
                    "path": "/Mail"
                },
                "runAfter": {}
            }
        },
        "foreach": "@triggerBody()?['links']",
        "runAfter": {},
    }
},
```

<a name="sequential-foreach-loop"></a>

## <a name="foreach-loop-sequential"></a>Bucle "Foreach": secuencial

De forma predeterminada, cada ciclo de un bucle "Foreach" se ejecuta en paralelo para cada elemento de la matriz. Para ejecutar cada ciclo de forma secuencial, establezca la opción **Secuencial** en el bucle "Foreach".

1. En la esquina superior derecha del bucle, elija **puntos suspensivos** (**...**) > **Configuración**.

   ![En bucle "Foreach", elija "..." > "Configuración"](media/logic-apps-control-flow-loops/for-each-loop-settings.png)

2. Active la opción **Secuencial** y, a continuación, elija **Listo**.

   ![Activar opción Secuencial del bucle "Foreach"](media/logic-apps-control-flow-loops/for-each-loop-sequential-setting.png)

También puede establecer el parámetro **operationOptions** en `Sequential` en la definición JSON de la aplicación lógica. Por ejemplo: 

``` json
"actions": {
    "myForEachLoopName": {
        "type": "Foreach",
        "actions": {
            "Send_an_email": {               
            }
        },
        "foreach": "@triggerBody()?['links']",
        "runAfter": {},
        "operationOptions": "Sequential"
    }
},
```

<a name="until-loop"></a>

## <a name="until-loop"></a>Bucle "Until"
  
Para repetir acciones hasta que se cumpla una condición o cambie algún estado, use un bucle "Until" en el flujo de trabajo de la aplicación lógica. Estos son algunos casos de uso habituales en los que puede utilizar un bucle "Until":

* Llamada a un punto de conexión hasta que obtiene la respuesta que desea.
* Creación de un registro en una base de datos y espera hasta que se aprueba un campo específico de ese recurso y continúa el procesamiento. 

> [!NOTE]
> Los bucles "Until" no pueden incluir bucles "Foreach" ni otros bucles "Until".

Por ejemplo, a las 8:00 a. m. cada día, esta aplicación lógica incrementa una variable hasta que el valor de la misma es igual a 10. Después, la aplicación lógica envía un correo electrónico que confirma el valor actual. Aunque en este ejemplo se usa Office 365 Outlook, puede usar cualquier proveedor de correo electrónico compatible con Logic Apps ([revise la lista de conectores aquí](https://docs.microsoft.com/connectors/)). Si usa otra cuenta de correo electrónico, los pasos generales serán los mismos, pero su interfaz de usuario puede ser ligeramente distinta. 

1. Crear una aplicación lógica en blanco. En el Diseñador de aplicación lógica, busque "periodicidad" y seleccione este desencadenador: **Programación: Periodicidad** 

   ![Agregar desencadenador "Programación: Periodicidad"](./media/logic-apps-control-flow-loops/do-until-loop-add-trigger.png)

2. Especifique cuando se debe activar el desencadenador estableciendo el intervalo, la frecuencia y la hora del día. Para establecer la hora, seleccione **Mostrar opciones avanzadas**.

   ![Agregar desencadenador "Programación: Periodicidad"](./media/logic-apps-control-flow-loops/do-until-loop-set-trigger-properties.png)

   | Propiedad | Valor |
   | -------- | ----- |
   | **Intervalo** | 1 | 
   | **Frecuencia** | Día |
   | **A estas horas** | 8 |
   ||| 

3. En el desencadenador, elija **Nuevo paso** > **Agregar una acción**. Busque "variables" y seleccione esta acción: **Variables: Inicializar variable**.

   ![Agregar la acción "Variables: Inicializar variable"](./media/logic-apps-control-flow-loops/do-until-loop-add-variable.png)

4. Configure la variable con estos valores:

   ![Establecer propiedades de las variables](./media/logic-apps-control-flow-loops/do-until-loop-set-variable-properties.png)

   | Propiedad | Valor | DESCRIPCIÓN |
   | -------- | ----- | ----------- |
   | **Name** | Límite | El nombre de la variable | 
   | **Tipo** | Entero | El tipo de datos de la variable | 
   | **Valor** | 0 | El valor de inicio de la variable | 
   |||| 

5. En la acción **Inicializar variable**, elija **Nuevo paso** > **Más**. Seleccione este bucle: **Add a do until** (Agregar un bucle "do until")

   ![Agregar un bucle "do until"](./media/logic-apps-control-flow-loops/do-until-loop-add-until-loop.png)

6. Cree la condición de salida del bucle seleccionando la variable **Limit** y el operador **is equal** (es igual a). Escriba **10** como valor de comparación.

   ![Crear condición de salida para detener el bucle](./media/logic-apps-control-flow-loops/do-until-loop-settings.png)

7. Dentro del bucle, elija **Agregar una acción**. Busque "variables" y agregue esta acción: **Variables: Incrementar variable**.

   ![Agregar acción para incrementar la variable](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable.png)

8. En **Nombre**, seleccione la variable **Limit**. En **Valor**, escriba "1". 

   ![Aumentar "Límite" en 1](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable-settings.png)

9. Debajo del bucle, pero fuera de este, agregue una acción que envíe un correo electrónico. Si se le pide, inicie sesión en la cuenta de correo electrónico.

   ![Agregar una acción que envía correo electrónico](media/logic-apps-control-flow-loops/do-until-loop-send-email.png)

10. Establezca las propiedades del correo electrónico. Agregue la variable **Limit** al asunto. De este modo, se puede confirmar que el valor actual de la variable cumple la condición especificada, por ejemplo:

    ![Configurar las propiedades del correo electrónico](./media/logic-apps-control-flow-loops/do-until-loop-send-email-settings.png)

    | Propiedad | Valor | DESCRIPCIÓN |
    | -------- | ----- | ----------- | 
    | **To** | *<email-address@domain>* | La dirección de correo electrónico del destinatario. Para las pruebas, use su propia dirección de correo electrónico. | 
    | **Asunto** | El valor actual de "Límite" es **Limit** | Especifique el asunto del correo electrónico. En este ejemplo, asegúrese de que incluye la variable **Limit**. | 
    | **Cuerpo** | <*email-content*> | Especifique el contenido del mensaje de correo electrónico que desea enviar. En este ejemplo, escriba cualquier texto que desee. | 
    |||| 

11. Guarde la aplicación lógica. Para probar manualmente la aplicación lógica, en la barra de herramientas del diseñador, elija **Ejecutar**.

    Después de que la lógica empiece a ejecutarse, recibirá un correo electrónico con el contenido que haya especificado:

    ![Correo electrónico recibido](./media/logic-apps-control-flow-loops/do-until-loop-sent-email.png)

## <a name="prevent-endless-loops"></a>Impedir bucles sin fin

Un bucle "Until" tiene límites predeterminados que detienen la ejecución si se produce alguna de estas condiciones:

| Propiedad | Valor predeterminado | DESCRIPCIÓN | 
| -------- | ------------- | ----------- | 
| **Recuento** | 60 | El número máximo de bucles que se ejecutan antes de salir del bucle. El valor predeterminado es de 60 ciclos. | 
| **Tiempo de espera** | PT1H | La cantidad máxima de tiempo para ejecutar un bucle antes de salir de este. El valor predeterminado es una hora y se especifica en formato ISO 8601. <p>El valor de tiempo de espera se evalúa para cada ciclo del bucle. Si una acción del bucle tarda más que el límite de tiempo de espera, el ciclo actual no se detiene, pero no se iniciará el siguiente ciclo porque no se cumple la condición del límite. | 
|||| 

Para cambiar estos límites predeterminados, elija **Mostrar opciones avanzadas** en la forma de acción del bucle.

<a name="until-json"></a>

## <a name="until-definition-json"></a>Definición "Until" (JSON)

Si está trabajando en la vista de código de la aplicación lógica, puede definir un bucle `Until` en la definición JSON de la aplicación lógica en su lugar, por ejemplo:

``` json
"actions": {
    "Initialize_variable": {
        // Definition for initialize variable action
    },
    "Send_an_email": {
        // Definition for send email action
    },
    "Until": {
        "type": "Until",
        "actions": {
            "Increment_variable": {
                "type": "IncrementVariable",
                "inputs": {
                    "name": "Limit",
                    "value": 1
                },
                "runAfter": {}
            }
        },
        "expression": "@equals(variables('Limit'), 10)",
        // To prevent endless loops, an "Until" loop 
        // includes these default limits that stop the loop. 
        "limit": { 
            "count": 60,
            "timeout": "PT1H"
        },
        "runAfter": {
            "Initialize_variable": [
                "Succeeded"
            ]
        },
    }
},
```

En otro ejemplo, este bucle "Until" llama a un punto de conexión HTTP que crea un recurso y se detiene cuando el cuerpo de respuesta HTTP devuelve un estado "Completado". Para impedir bucles sin fin, el bucle también se detendrá si se produce alguna de estas condiciones:

* El bucle se ha ejecutado 10 veces según lo especificado por el atributo `count`. El valor predeterminado es 60 veces. 
* El bucle ha intentado ejecutarse durante dos horas según lo especificado por el atributo `timeout` en formato ISO 8601. El valor predeterminado es de una hora.
  
``` json
"actions": {
    "myUntilLoopName": {
        "type": "Until",
        "actions": {
            "Create_new_resource": {
                "type": "Http",
                "inputs": {
                    "body": {
                        "resourceId": "@triggerBody()"
                    },
                    "url": "https://domain.com/provisionResource/create-resource",
                    "body": {
                        "resourceId": "@triggerBody()"
                    }
                },
                "runAfter": {},
                "type": "ApiConnection"
            }
        },
        "expression": "@equals(triggerBody(), 'Completed')",
        "limit": {
            "count": 10,
            "timeout": "PT2H"
        },
        "runAfter": {}
    }
},
```

## <a name="get-support"></a>Obtención de soporte técnico

* Si tiene alguna duda, visite el [foro de Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para enviar características y sugerencias o votar las que ya se han enviado, visite el [sitio web de comentarios de los usuarios de Azure Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Pasos siguientes

* [Ejecución de pasos en función de una condición (instrucciones condicionales)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Realización de pasos en función de los diferentes valores (instrucciones switch)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Ejecución o combinación de pasos en paralelo (ramas)](../logic-apps/logic-apps-control-flow-branches.md)
* [Ejecución de pasos en función del estado de las acciones agrupadas (ámbitos)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)