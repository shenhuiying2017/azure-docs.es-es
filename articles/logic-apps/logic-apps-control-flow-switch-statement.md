---
title: "Instrucciones switch: ejecución de pasos en función de valores específicos: Azure Logic Apps | Microsoft Docs"
description: "Ejecutar pasos diferentes en función de los valores de objetos, expresiones o tokens en Logic Apps"
services: logic-apps
keywords: "Instrucción switch"
author: ecfan
manager: anneta
editor: 
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/05/2018
ms.author: estfan; LADocs
ms.openlocfilehash: e1f515189be8a5659af0f6c29b3fac0550abc9f9
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/05/2018
---
# <a name="switch-statements-run-different-steps-based-on-specific-values-in-logic-apps"></a>Instrucciones switch: ejecución de pasos en función de valores específicos en Logic Apps

Para llevar a cabo los diferentes pasos en función de los valores de un objeto, una expresión o un token, utilice una instrucción *switch*. Esta estructura evalúa el objeto, la expresión o el token, elige el caso que coincida con el resultado y ejecuta acciones solo para ese caso. Cuando se ejecuta la instrucción switch, solo un caso debe coincidir con el resultado.

Por ejemplo, imagine que desea una aplicación lógica que lleve a cabo pasos diferentes en función de una opción seleccionada en el correo electrónico. En este ejemplo, la aplicación lógica comprueba la fuente RSS de un sitio web para ver si hay nuevo contenido. Cuando aparece un elemento nuevo en la fuente RSS, la aplicación lógica envía un correo electrónico a un aprobador. Dependiendo de si el aprobador selecciona "Aprobar" o "Rechazar", la aplicación lógica sigue pasos diferentes.

> [!TIP]
> Al igual que todos los lenguajes de programación, la instrucción switch solo admite los operadores de igualdad. Utilice una [instrucción condicional](#conditions) si necesita otros operadores relacionales, como "mayor que".
> Para garantizar el comportamiento de ejecución determinístico, los casos tienen que contener un valor único y estático en lugar de expresiones o tokens dinámicos.

## <a name="prerequisites"></a>requisitos previos

* Una suscripción de Azure. Si aún no tiene una, [regístrese para obtener una cuenta de Azure gratuita](https://azure.microsoft.com/free/).

* Para seguir el ejemplo de este artículo, [cree una aplicación lógica de ejemplo](../logic-apps/quickstart-create-first-logic-app-workflow.md) con una cuenta de Outlook.com o de Office 365 Outlook.

  1. Al agregar la acción para enviar correo electrónico, seleccione **Enviar correo electrónico de aprobación**.

     ![Selección de "Enviar correo electrónico de aprobación"](./media/logic-apps-control-flow-switch-statement/send-approval-email-action.png)

  2. Proporcione los campos necesarios, como la dirección de correo electrónico de la persona que recibe el correo electrónico de aprobación. 
  En **Opciones de usuario**, escriba "Aprobar, Rechazar".

     ![Escriba los detalles del correo electrónico](./media/logic-apps-control-flow-switch-statement/send-approval-email-details.png)

## <a name="add-a-switch-statement"></a>Agregue una instrucción switch

1. Al final del flujo de trabajo de ejemplo, elija **+ Nuevo paso** > **... Más** > **Agregar un caso de conmutador**. 

   ![Agregue una instrucción switch](./media/logic-apps-control-flow-switch-statement/add-switch-statement.png)

   Aparece una instrucción switch con un caso y un caso predeterminado. 
   Una instrucción switch requiere al menos un caso además del caso predeterminado. 

   Si desea agregar una instrucción switch entre los pasos, mueva el puntero sobre la flecha donde desea agregar la instrucción switch. 
   Seleccione el **signo más** (**+**) que aparece y seleccione **Agregar un caso de conmutador**.

4. En el cuadro **En**, seleccione el campo **SelectedOption** cuyo resultado determina la acción que se va a realizar. 
   
   Puede seleccionar el campo en la lista **Agregar contenido dinámico** que aparece.

5. Para controlar los casos donde el aprobador selecciona `Approve` o `Reject`, agregue otro caso entre **Caso** y **Predeterminado**. 
   
6. Agregue estas acciones a los casos correspondientes:

   | Case nº | **SelectedOption** | . |
   |:------ |:-------------------|:------ |
   | Caso 1 | **Aprobar** | Agregar la acción de Outlook **Enviar un correo electrónico** para enviar detalles sobre el elemento RSS únicamente cuando el aprobador ha seleccionado **Aprobar**. |
   | Caso 2 | **Rechazar** | Agregar la acción de Outlook **Enviar un correo electrónico** para notificar a otros aprobadores que el elemento RSS se ha rechazado. |
   | Valor predeterminado | \<Ninguna\> | No es necesaria ninguna acción. En este ejemplo, el caso **Predeterminado** está vacío porque **SelectedOption** solo tiene dos opciones. |
   |         |          |

   ![Instrucción switch](./media/logic-apps-control-flow-switch-statement/switch.png)

7. Guarde la aplicación lógica. 

   Para probar manualmente este ejemplo, elija **Ejecutar** hasta que la aplicación lógica encuentra un nuevo elemento RSS y envía un correo electrónico de aprobación. 
   Seleccione **Aprobar** para observar los resultados.

## <a name="json-definition"></a>Definición JSON

Ahora que ha creado una aplicación lógica mediante una instrucción switch, echemos un vistazo a la definición de código de alto nivel detrás de la instrucción switch.

``` json
"Switch": {
   "type": "Switch",
   "expression": "@body('Send_approval_email')?['SelectedOption']",
   "cases": {
      "Case" : {
         "actions" : {
           "Send_an_email": { }
         },
         "case" : "Approve"
      },
      "Case_2" : {
         "actions" : {
           "Send_an_email_2": { }
         },
         "case" : "Reject"
      }
   },
   "default": {
      "actions": {}
   },
   "runAfter": {
      "Send_approval_email": [
         "Succeeded"
      ]
   }
}
```

| Etiqueta              | DESCRIPCIÓN |
| :----------------- | :---------- |
| `"Switch"`         | El nombre de la instrucción switch, que puede cambiar para mejorar la legibilidad |
| `"type": "Switch"` | Especifica que la acción es una instrucción switch |
| `"expression"`     | En este ejemplo, especifica la opción seleccionada del aprobador que se evalúa con respecto a cada caso declarado más adelante en la definición |
| `"cases"` | Define cualquier número de casos. Para cada caso, `"Case_*"` es el nombre predeterminado del caso que puede cambiar para mejorar la legibilidad |
| `"case"` | Especifica el valor del caso, que tiene que ser un valor único y constante que la instrucción switch usa para comparar. Si no hay casos que coincidan con el resultado de la instrucción switch, se ejecutan las acciones de la sección `"default"`.
|           |         |

## <a name="get-support"></a>Obtención de soporte técnico

* Si tiene alguna duda, visite el [foro de Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para enviar características o sugerencias o votar sobre ellas, visite el [sitio de comentarios de los usuarios de Azure Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Pasos siguientes

* [Ejecución de pasos en función de una condición (instrucciones condicionales)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Ejecución y repetición de pasos (bucles)](../logic-apps/logic-apps-control-flow-loops.md)
* [Ejecución o combinación de pasos en paralelo (ramas)](../logic-apps/logic-apps-control-flow-branches.md)
* [Ejecución de pasos en función del estado de las acciones agrupadas (ámbitos)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)