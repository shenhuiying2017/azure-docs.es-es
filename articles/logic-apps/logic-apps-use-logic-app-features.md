---
title: "Incorporación de condiciones e inicio de flujos de trabajo: Azure Logic Apps | Microsoft Docs"
description: "Controle la ejecución de flujos de trabajo en Azure Logic Apps mediante la incorporación de lógica condicional, desencadenadores, acciones y parámetros."
author: stepsic-microsoft-com
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: e4e24de4-049a-4b3a-a14c-3bf3163287a8
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/28/2017
ms.author: LADocs; stepsic
ms.openlocfilehash: e632c48ed31e82536db55a9c54438bece0c38fd4
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="use-logic-apps-features"></a>Uso de las características de aplicaciones lógicas

En un [tema anterior](../logic-apps/logic-apps-create-a-logic-app.md), creó su primera aplicación lógica. Para controlar el flujo de trabajo de la aplicación lógica, puede especificar distintas rutas de acceso para que la aplicación lógica se ejecute y determinar cómo se procesan los datos de matrices, colecciones y lotes. Puede incluir estos elementos en el flujo de trabajo de la aplicación lógica:

* Las condiciones y las instrucciones [switch](../logic-apps/logic-apps-switch-case.md) permiten que la aplicación lógica ejecute distintas acciones en función de que se cumplan determinadas condiciones.

* Los [bucles](../logic-apps/logic-apps-loops-and-scopes.md) permiten que la aplicación lógica ejecute pasos varias veces. Por ejemplo, puede repetir acciones en una matriz si usa un bucle **For_each**. También puede repetir acciones hasta que se cumpla una condición si usa un bucle **Until**.

* Los [ámbitos](../logic-apps/logic-apps-loops-and-scopes.md) permiten agrupar una serie de acciones juntas, por ejemplo, para implementar el control de excepciones.

* La [desagrupación](../logic-apps/logic-apps-loops-and-scopes.md) permite que la aplicación lógica inicie flujos de trabajo independientes para elementos de una matriz si usa el comando **SplitOn**.

En este tema se presentan otros conceptos de creación de la aplicación lógica:

* Vista de código para editar una aplicación lógica existente
* Opciones para iniciar un flujo de trabajo

## <a name="conditions-run-steps-only-after-meeting-a-condition"></a>Condiciones: ejecutan pasos solo después de que se cumpla una condición

Para que la aplicación lógica ejecute pasos solo cuando los datos cumplen determinados criterios, puede agregar una condición que compare los datos del flujo de trabajo con campos o valores concretos.

Por ejemplo, suponga que tiene una aplicación lógica que le envía demasiados correos electrónicos para entradas en la fuente RSS de un sitio web. Puede agregar una condición para que la aplicación lógica solo envíe un correo electrónico cuando la nueva entrada pertenezca a una categoría específica.

1. En [Azure Portal](https://portal.azure.com), busque y abra la aplicación lógica en el Diseñador de aplicaciones lógicas.

2. Agregue una condición a la ubicación del flujo de trabajo que quiera. 

   Para agregar la condición entre pasos existentes en el flujo de trabajo de la aplicación lógica, mueva el puntero sobre la flecha en la que quiere agregar la condición. 
   Elija el **signo más** (**+**) y luego seleccione **Agregar una condición**. Por ejemplo:

   ![Incorporación de una condición a la aplicación lógica](./media/logic-apps-use-logic-app-features/add-condition.png)

   > [!NOTE]
   > Si quiere agregar una condición al final del flujo de trabajo actual, vaya a la parte inferior de la aplicación lógica y elija **+ Nuevo paso**.

3. Defina la condición. Especifique el campo de origen que quiere evaluar, la operación que va a realizar y el valor o campo de destino. Para agregar campos existentes, elija en la lista **Agregar contenido dinámico**.

   Por ejemplo:

   ![Edición de una condición en modo básico](./media/logic-apps-use-logic-app-features/edit-condition-basic-mode.png)

   Esta es la condición completa:

   ![Condición completa](./media/logic-apps-use-logic-app-features/edit-condition-basic-mode-2.png)

   > [!TIP]
   > Para definir la condición en el código, elija **Editar en modo avanzado**. Por ejemplo:
   > 
   > ![Edición de la condición en el código](./media/logic-apps-use-logic-app-features/edit-condition-advanced-mode.png)

4. En **EN CASO AFIRMATIVO** y **EN CASO NEGATIVO**, agregue los pasos que se van a realizar en función de que la condición se cumpla.

   Por ejemplo:

   ![Condición con las trayectoria en caso de AFIRMATIVO y NEGATIVO](./media/logic-apps-use-logic-app-features/condition-yes-no-path.png)

   > [!TIP]
   > Puede arrastrar acciones existentes a las trayectorias **EN CASO AFIRMATIVO** y **EN CASO NEGATIVO**.

5. Cuando haya terminado, guarde la aplicación lógica.

Ahora solo recibirá correos electrónicos cuando las entradas cumplan la condición.

## <a name="repeat-actions-over-a-list-with-foreach"></a>Repetición de acciones de una lista con forEach

El bucle forEach especifica una matriz para repetir una acción varias veces. Si no es una matriz se produce un error en el flujo. Por ejemplo, si tiene action1, que genera una matriz de mensajes, y desea enviar cada mensaje, puede incluir esta instrucción forEach en las propiedades de la acción: `forEach : "@action('action1').outputs.messages"`

## <a name="edit-the-code-definition-for-a-logic-app"></a>Edición de la definición de código para una aplicación lógica

Aunque cuenta con el Diseñador de aplicación lógica, puede editar directamente el código que define una aplicación lógica.

1. En la barra de comandos, haga clic en **Vista Código**.

    Se abre un editor completo que muestra la definición que ha modificado.

    ![vista Código](media/logic-apps-use-logic-app-features/codeview.png)

    En el editor de texto, puede copiar y pegar cualquier cantidad de acciones dentro de la misma aplicación lógica o entre aplicaciones lógicas. 
    También puede agregar o quitar secciones completas de la definición, así como compartir definiciones con otros.

2. Para guardar los cambios, elija **Guardar**.

## <a name="parameters"></a>Parámetros

Algunas funcionalidades de Logic Apps solo están disponibles en la vista de código, como, por ejemplo, los parámetros. Los parámetros facilitan volver a usar los valores en toda la aplicación lógica. Por ejemplo, si tiene una dirección de correo electrónico que desea utilizar en varias acciones, debe definirla como un parámetro.

Los parámetros son una buena forma de extraer valores que probablemente cambie mucho. Son especialmente útiles cuando necesite reemplazar parámetros en entornos diferentes. Para obtener información sobre cómo invalidar los parámetros basados en el entorno, vea [Creación de definiciones de aplicación lógica](../logic-apps/logic-apps-author-definitions.md) y [Documentación de la API de REST](https://docs.microsoft.com/rest/api/logic).

En este ejemplo se muestra cómo actualizar la aplicación lógica existente, para que pueda usar los parámetros para el término de consulta.

1. En la vista de código, busque el objeto `parameters : {}` y agregue un objeto `currentFeedUrl`:

        "currentFeedUrl" : {
            "type" : "string",
            "defaultValue" : "http://rss.cnn.com/rss/cnn_topstories.rss"
        }

2. Vaya a la acción `When_a_feed-item_is_published`, busque la sección `queries` y reemplace el valor de consulta por: `"feedUrl": "#@{parameters('currentFeedUrl')}"`. 

    Para combinar dos o más cadenas, también puede usar la función `concat`. 
    Por ejemplo, `"@concat('#',parameters('currentFeedUrl'))"` funciona igual que el anterior.

3.  Cuando termine, seleccione **Guardar**. 

    Ahora puede cambiar la fuente RSS del sitio web pasando una dirección URL diferente a través del objeto `currentFeedURL`.

Obtenga más información sobre la [creación de definiciones de aplicación lógica](../logic-apps/logic-apps-author-definitions.md).

## <a name="start-logic-app-workflows"></a>Inicio de flujos de trabajo de aplicación lógica

Hay diferentes opciones para iniciar el flujo de trabajo definido en la aplicación lógica. Un flujo de trabajo siempre se puede iniciar a petición en [Azure Portal].

### <a name="recurrence-triggers"></a>Desencadenadores periódicos

Un desencadenador periódico se ejecuta en un intervalo que especifique. Cuando el desencadenador tiene lógica condicional, este determina si necesita ejecutar el flujo de trabajo. Un desencadenador indica si se debe ejecutar el flujo de trabajo, para lo que devuelve un código de estado `200`. Cuando no es necesario ejecutar el flujo de trabajo, el desencadenador devuelve un código de estado `202`.

### <a name="callback-using-rest-apis"></a>Devolución de llamada mediante las API de REST

Para iniciar un flujo de trabajo, los servicios pueden llamar a un punto de conexión de aplicación lógica. Para iniciar ese tipo de aplicación lógica a petición, seleccione **Ejecutar ahora** en la barra de comandos. Vea [Start workflows by calling logic app endpoints as triggers](../logic-apps/logic-apps-http-endpoint.md) (Inicio de flujos de trabajo mediante llamadas a puntos de conexión de aplicación lógica como desencadenadores). 

<!-- Shared links -->
[Azure Portal]: https://portal.azure.com

## <a name="next-steps"></a>Pasos siguientes

* [Instrucciones switch](../logic-apps/logic-apps-switch-case.md) 
* [Bucles, ámbitos y desagrupación](../logic-apps/logic-apps-loops-and-scopes.md)
* [Creación de definiciones de aplicación lógica](../logic-apps/logic-apps-author-definitions.md)