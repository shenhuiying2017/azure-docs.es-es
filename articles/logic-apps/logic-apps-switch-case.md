---
title: "Uso de la instrucción switch en Azure Logic Apps | Microsoft Docs"
description: "La instrucción switch permite realizar diferentes acciones fácilmente según el valor de una expresión en Logic Apps"
services: logic-apps
documentationcenter: dev-center-name
manager: erikre
ms.service: logic-apps
ms.devlang: wdl
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/18/2016
ms.author: deli
translationtype: Human Translation
ms.sourcegitcommit: 3a028507f9bbf15c8fd52ccc7c22a5763a9b1b3e
ms.openlocfilehash: 284cfca17b5abf785f7af9569c518c4400fe36fd


---
# <a name="use-switch-statement-in-logic-apps"></a>Uso de la instrucción switch en Logic Apps
Al crear un flujo de trabajo, a menudo necesita realizar diferentes acciones en función del valor de un objeto o una expresión. Por ejemplo, puede que desee que la aplicación lógica se comporte de manera diferente según el código de estado de una solicitud HTTP o la opción seleccionada de un correo electrónico de aprobación.

Estos escenarios se pueden lograr mediante el uso de una instrucción switch: la aplicación lógica evalúa un token o una expresión y elige el caso con el mismo valor para ejecutar acciones dentro. Solamente un caso debe cumplir la instrucción switch.

 > [!TIP]
 > Al igual que todos los lenguajes de programación, la instrucción switch solo admite los operadores de igualdad. Utilice una instrucción condicional si necesita otros operadores relacionales (por ejemplo, mayor que).
 >
 > Para garantizar el comportamiento de ejecución determinístico, los casos deben contener un valor único y estático en lugar de tokens dinámicos o una expresión.

## <a name="prerequisites"></a>Requisitos previos

- Una suscripción de Azure activa.
    - Si no tiene una suscripción de Azure activa, [cree una cuenta gratuita](https://azure.microsoft.com/free/) o pruebe [Logic Apps gratis](https://tryappservice.azure.com/).
- [Conocimientos básicos de Logic Apps](logic-apps-what-are-logic-apps.md).

## <a name="working-with-switch-statement-in-designer"></a>Trabajo con la instrucción switch en el diseñador
Para demostrar el uso de la instrucción switch, vamos a crear una aplicación lógica que supervise los archivos cargados en Dropbox. La aplicación lógica enviará un correo electrónico de aprobación para determinar si se debe transferir a SharePoint. Utilizaremos la instrucción switch para realizar distintas acciones según el aprobador de valores seleccionado.

1. Empiece por crear una aplicación lógica y seleccione el desencadenador **Dropbox - Cuando se crea un archivo**.

 ![Uso del desencadenador Dropbox - Cuando se crea un archivo](./media/logic-apps-switch-case/dropbox-trigger.jpg)

2. Realice un seguimiento del desencadenador con una acción **Outlook.com - Enviar correo electrónico de aprobación**.

 > [!TIP]
 > Logic Apps también admite el escenario de correo electrónico de aprobación desde una cuenta de Office 365 Outlook.

 - Si no tiene una conexión existente, se le pedirá que cree una.
 - Rellene los campos necesarios; el correo electrónico se enviará a approvers@contoso.com.
 - En *Opciones de usuario*, escriba `Approve, Reject`.

 ![Configuración de la conexión](./media/logic-apps-switch-case/send-approval-email-action.jpg)

3. Agregue una instrucción switch.
 - Seleccione **+ Nuevo paso**, **... Más**, **Agregar una instrucción switch**.
 - Queremos seleccionar lo que se debe ejecutar en función de la salida `SelectedOptions` de la acción *Enviar correo electrónico de aprobación*. Puede encontrarlo en el selector **Agregar contenido dinámico**.
 - Use el *Caso 1* para controlar cuando el usuario seleccione `Approve`.
    - Si se aprueba, copie el archivo original a SharePoint Online con la acción **SharePoint Online - Crear archivo**.
    - Agregue otra acción dentro del caso para notificar a los usuarios que está disponible un nuevo archivo en SharePoint.
 - Agregue otro caso para controlar cuando el usuario seleccione `Reject`.
    - Si se rechaza, envíe un correo electrónico de notificación que informe a otros aprobadores de que el archivo se rechaza y que no es necesario realizar ninguna otra acción.
 - Sabemos que `SelectedOptions` solo dispone de dos opciones, el caso *predeterminado* puede dejarse vacío.

 ![Instrucción switch](./media/logic-apps-switch-case/switch.jpg)

 > [!NOTE]
 > La instrucción switch necesita al menos un caso además del caso predeterminado.

4. Después de la instrucción switch, elimine el archivo original cargado en Dropbox con la acción **Dropbox - Eliminar archivo**.

5. Guarde la aplicación lógica y pruébela al cargar archivos en Dropbox. Debería recibir un correo electrónico de aprobación poco después, seleccionar una opción y observar el comportamiento.
 > [!TIP]
 > Consulte cómo [supervisar las aplicaciones lógicas](logic-apps-monitor-your-logic-apps.md).

## <a name="understanding-code-behind"></a>Descripción del código subyacente
Ahora ha creado correctamente una aplicación lógica con la instrucción switch. Echemos un vistazo al código subyacente siguiente.

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

`"Switch"` es el nombre de la instrucción switch; puede cambiarse de nombre para mejorar la legibilidad. `"type": "Switch"` indica que la acción es una instrucción switch. `"expression"`, en este caso, la opción seleccionada del usuario, se evalúa con respecto a cada caso declarado más adelante en la definición. `"cases"` puede contener cualquier número de casos, y si ninguno de los casos cumple la expresión switch, se ejecutan las acciones dentro de `"default"`.

Puede haber cualquier número de casos dentro de `"cases"`. Para cada caso, `"Case 1"` es el nombre del caso; puede cambiarse de nombre para mejorar la legibilidad. `"case"` especifica la etiqueta del caso, con la que se compara la expresión switch, que debe ser un valor único y constante.  

## <a name="next-steps"></a>Pasos siguientes
- Probar otras características de [Logic Apps](logic-apps-use-logic-app-features.md).
- Obtener información sobre el [control de errores y excepciones](logic-apps-exception-handling.md).
- Explorar más [capacidades de lenguaje de flujo de trabajo](logic-apps-author-definitions.md).
- Deje un comentario con sus preguntas o comentarios, o [cuéntenos cómo podemos mejorar Logic Apps](https://feedback.azure.com/forums/287593-logic-apps).


<!--HONumber=Feb17_HO2-->


