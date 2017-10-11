---
title: "Uso de la instrucción switch para diferentes acciones en Azure Logic Apps | Microsoft Docs"
description: "Elija diferentes acciones para realizarlas en Logic Apps en función de los valores de expresión mediante una instrucción switch."
services: logic-apps
keywords: "Instrucción switch"
author: derek1ee
manager: anneta
editor: 
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/18/2016
ms.author: LADocs; deli
ms.openlocfilehash: 338b6a5b549d7bf81186550295608438ac4aee32
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="perform-different-actions-in-logic-apps-with-a-switch-statement"></a>Diferentes acciones en Logic Apps con una instrucción switch

Al crear un flujo de trabajo, a menudo es necesario realizar diferentes acciones en función del valor de un objeto o una expresión. Por ejemplo, puede que desee que la aplicación lógica se comporte de manera diferente según el código de estado de una solicitud HTTP o una opción seleccionada en un correo electrónico.

Puede utilizar una instrucción switch para implementar estos escenarios. La aplicación lógica puede evaluar un token o una expresión y elegir el caso con el mismo valor para ejecutar las acciones especificadas. Solamente un caso debe cumplir la instrucción switch.

> [!TIP]
> Al igual que todos los lenguajes de programación, la instrucción switch solo admite los operadores de igualdad. Utilice una instrucción de condición si necesita otros operadores relacionales, como "mayor que".
> Para garantizar el comportamiento de ejecución determinístico, los casos deben contener un valor único y estático en lugar de tokens dinámicos o una expresión.

## <a name="prerequisites"></a>Requisitos previos

- Una suscripción de Azure activa. Si no tiene una suscripción de Azure activa, [cree una cuenta gratuita](https://azure.microsoft.com/free/) o pruebe [Logic Apps gratis](https://tryappservice.azure.com/).
- [Conocimientos básicos acerca de Logic Apps](logic-apps-what-are-logic-apps.md)

## <a name="add-a-switch-statement-to-your-workflow"></a>Adición de una instrucción switch al flujo de trabajo

Para mostrar cómo funciona una instrucción switch, este ejemplo crea una aplicación lógica que supervisa los archivos cargados en Dropbox. Cuando se cargan los nuevos archivos, la aplicación lógica envía un mensaje de correo electrónico a un aprobador que decide si se transfieren esos archivos a SharePoint. La aplicación utiliza una instrucción switch que lleva a cabo diferentes acciones en función del valor que selecciona el aprobador.

1. Cree una aplicación lógica y seleccione el desencadenador **Dropbox - Cuando se crea un archivo** (Dropbox - Cuando se crea un archivo).

   ![Uso del desencadenador Dropbox - Cuando se crea un archivo](./media/logic-apps-switch-case/dropbox-trigger.jpg)

2. En el desencadenador, agregue la acción **Outlook.com - Enviar correo electrónico de aprobación**.

   > [!TIP]
   > Logic Apps también admite escenarios de enviar un correo electrónico de aprobación desde una cuenta de Office 365 Outlook.

   - Si no tiene ninguna conexión existente, se le pedirá que cree una.
   - Rellene todos los campos obligatorios. Por ejemplo, en **Para**, especifique la dirección de correo electrónico para enviar el correo electrónico del aprobador.
   - En **Opciones de usuario**, escriba `Approve, Reject`.

   ![Configuración de la conexión](./media/logic-apps-switch-case/send-approval-email-action.jpg)

3. Agregue una instrucción switch.

   - Seleccione **+ Nuevo paso** > **... Más** > **Agregar un caso de conmutador**. 
   - Ahora queremos seleccionar la acción que se va a realizar en función de la salida `SelectedOptions` desde la acción *Enviar correo electrónico de aprobación*. 
   Puede encontrar este campo en el selector **Agregar contenido dinámico**.
   - Use *caso 1* para controlar cuándo el aprobador selecciona `Approve`.
     - Si se aprueba, copie el archivo original en SharePoint Online con la acción [**SharePoint Online - Crear archivo**](../connectors/connectors-create-api-sharepointonline.md).
     - Agregue otra acción dentro del caso para notificar a los usuarios que está disponible un nuevo archivo en SharePoint.
   - Agregue otro caso para controlar cuándo el aprobador selecciona `Reject`.
     - Si se rechaza, envíe un correo electrónico de notificación que informe a otros aprobadores de que el archivo se rechaza y que no es necesario realizar ninguna otra acción.
   - `SelectedOptions`solo proporciona dos opciones, por lo que podemos dejar el caso **Predeterminado** vacío.

   ![Instrucción switch](./media/logic-apps-switch-case/switch.jpg)

   > [!NOTE]
   > Una instrucción switch necesita al menos un caso además del caso predeterminado.

4. Después de la instrucción switch, elimine el archivo original cargado en Dropbox agregando la acción **Dropbox - Delete file** (Dropbox - Eliminar archivo).

5. Guarde la aplicación lógica. Pruebe la aplicación cargando archivos en Dropbox. En breve recibirá un correo electrónico de aprobación. Seleccione una opción y observe el comportamiento.

   > [!TIP]
   > Consulte cómo [supervisar las aplicaciones lógicas](logic-apps-monitor-your-logic-apps.md).

## <a name="understand-the-code-behind-switch-statements"></a>Conocimiento del código detrás de las instrucciones switch

Ahora que ha creado correctamente una aplicación de lógica mediante una instrucción switch, echemos un vistazo a la definición de código en la instrucción switch.

```json
"Switch": {
    "type": "Switch",
    "expression": "@body('Send_approval_email')?['SelectedOption']",
    "cases": {
        "Case 1" : {
            "case" : "Approved",
            "actions" : {}
        },
        "Case 2" : {
            "case" : "Rejected",
            "actions" : {}
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

* `"Switch"` es el nombre de la instrucción switch; puede cambiar su nombre para mejorar la legibilidad. 
* `"type": "Switch"` indica que la acción es una instrucción switch. 
* `"expression"` es la opción seleccionada del aprobador en este ejemplo, y se evalúa con respecto a cada caso declarado más adelante en la definición. 
* `"cases"` puede contener cualquier número de casos. Para cada caso, `"Case *"` es el nombre predeterminado del caso; puede cambiar su nombre para mejorar la legibilidad. 
`"case"` especifica la etiqueta del caso, que la expresión switch usa para la comparación, y debe ser un valor único y constante. Si ninguno de los casos coincide con la expresión switch, se ejecutan las acciones bajo `"default"`.

## <a name="get-help"></a>Obtener ayuda

Para formular preguntas, o responderlas, y ver lo que hacen otros usuarios de Azure Logic Apps, visite el [foro de Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

Para ayudar a mejorar Azure Logic Apps y los conectores, vote o envíe ideas en el [sitio de comentarios de usuario de Azure Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Pasos siguientes

- Aprenda cómo [agregar condiciones](logic-apps-use-logic-app-features.md).
- Aprenda sobre el [control de errores y las excepciones](logic-apps-exception-handling.md).
- Conozca más [funcionalidades del lenguaje de flujo de trabajo](logic-apps-author-definitions.md).