---
title: 'Ramas paralelas: Azure Logic Apps | Microsoft Docs'
description: "Creación o unión de ramas paralelas en Logic Apps"
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
ms.openlocfilehash: 9a836b707a576b9a938f43397ef35c00aeb476bf
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/05/2018
---
# <a name="create-or-join-parallel-branches-in-your-logic-app"></a>Creación o unión de ramas paralelas en una aplicación lógica

De forma predeterminada, las acciones en una aplicación lógica se ejecutan secuencialmente. Para llevar a cabo acciones independientes al mismo tiempo, puede crear [ramas paralelas](#parallel-branches)y luego [unir esas ramas](#join-branches) más adelante en el flujo. 

> [!TIP] 
> Si tiene un desencadenador que recibe una matriz y desea ejecutar un flujo de trabajo para cada elemento de matriz, puede *desagrupar* esa matriz con la propiedad de desencadenador [**SplitOn**](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch).

## <a name="prerequisites"></a>requisitos previos

* Una suscripción de Azure. Si aún no tiene una, [regístrese para obtener una cuenta de Azure gratuita](https://azure.microsoft.com/free/). 

* Conocimientos básicos acerca de [cómo crear aplicaciones lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="parallel-branches"></a>

## <a name="add-a-parallel-branch"></a>Incorporación de una rama paralela

Para ejecutar pasos independientes al mismo tiempo, puede agregar ramas paralelas junto a un paso existente. 

![Ejecución de pasos en paralelo](media/logic-apps-control-flow-branches/parallel.png)

La aplicación lógica espera a que todas las ramas finalicen antes de continuar con el flujo de trabajo.
Las ramas paralelas se ejecutan solo cuando sus valores de propiedad `runAfter` coinciden con estado del paso primario terminado. Por ejemplo, `branchAction1` y `branchAction2` están configuradas para ejecutarse solo cuando `parentAction` se completa con el estado `Succeded`.

> [!NOTE]
> Antes de empezar, la aplicación lógica tiene que tener un paso al que se puedan agregar ramas paralelas.

1. En <a href="https://portal.azure.com" target="_blank">Azure Portal</a>, abra la aplicación lógica en Diseñador de aplicación lógica.

2. Mueva el mouse sobre la flecha situada encima del paso donde desea agregar ramas paralelas.

3. Elija el signo **más** (**+**), luego **Agregar una rama paralela** y seleccione el elemento que desea agregar.

   ![Incorporación de una rama paralela](media/logic-apps-control-flow-branches/add-parallel-branch.png)

   El elemento seleccionado aparece ahora en una rama paralela.

4. Para cada rama paralela, agregue los pasos que desee. Para agregar una acción secuencial a una rama paralela, mueva el mouse en la acción en la que desea agregar la acción secuencial. Elija el signo **más** (**+**) y el paso que desea agregar.

   ![Incorporación de un paso secuencial a una rama paralela](media/logic-apps-control-flow-branches/add-sequential-action-parallel-branch.png)

5. Para volver a combinar ramas [una las ramas paralelas](#join-branches). 

<a name="parallel-json"></a>

## <a name="parallel-branch-definition-json"></a>Definición de la rama paralela (JSON)

Si está trabajando en la vista de código, puede definir la estructura paralela en la definición JSON de la aplicación lógica en su lugar, por ejemplo:

``` json
{
  "triggers": {
    "myTrigger": { }
  },
  "actions": {
    "parentAction": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {}
    },
    "branchAction1": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {
        "parentAction": [
          "Succeeded"
        ]
      }
    },
    "branchAction2": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {
        "parentAction": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```

<a name="join-branches"></a>

## <a name="join-parallel-branches"></a>Unión de ramas paralelas

Para combinar ramas paralelas, simplemente agregue un paso en la parte inferior debajo de todas las ramas. Este paso se ejecuta después de que todas las ramas paralelas terminen de ejecutarse.

![Unión de ramas paralelas](media/logic-apps-control-flow-branches/join.png)

1. En [Azure Portal](https://portal.azure.com), busque y abra la aplicación lógica en el Diseñador de aplicaciones lógicas. 

2. En las ramas paralelas que se desea unir, agregue el paso que desea realizar.

   ![Incorporación de un paso que une ramas paralelas](media/logic-apps-control-flow-branches/join-steps.png)

   Sus ramas paralelas están ahora combinadas.

<a name="join-json"></a>

## <a name="join-definition-json"></a>Definición de la unión (JSON)

Si está trabajando en la vista de código, puede definir la estructura de la unión en la definición JSON de la aplicación lógica en su lugar, por ejemplo:

``` json
{
  "triggers": {
    "myTrigger": { }
  },
  "actions": {
    "parentAction": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {}
    },
    "branchAction1": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {
        "parentAction": [
          "Succeeded"
        ]
      }
    },
    "branchAction2": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {
        "parentAction": [
          "Succeeded"
        ]
      }
    },
    "joinAction": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {
        "branchAction1": [
          "Succeeded"
        ],
        "branchAction2": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```

## <a name="get-support"></a>Obtención de soporte técnico

* Si tiene alguna duda, visite el [foro de Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para enviar características y sugerencias o votar las que ya se han enviado, visite el [sitio web de comentarios de los usuarios de Azure Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Pasos siguientes

* [Ejecución de pasos en función de una condición (instrucciones condicionales)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Realización de pasos en función de los diferentes valores (instrucciones switch)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Ejecución y repetición de pasos (bucles)](../logic-apps/logic-apps-control-flow-loops.md)
* [Ejecución de pasos en función del estado de las acciones agrupadas (ámbitos)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)