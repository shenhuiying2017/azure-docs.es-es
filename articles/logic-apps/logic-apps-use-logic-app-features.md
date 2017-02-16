---
title: "Incorporación de condiciones y desencadenadores para ejecutar flujos de trabajo - Azure Logic Apps | Microsoft Docs"
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
ms.author: stepsic
translationtype: Human Translation
ms.sourcegitcommit: d090ce5a912a2079d2e47d13caf60ca701f0e548
ms.openlocfilehash: 330359583346f7e1944b53ec3fd34104f10e1261


---
# <a name="use-logic-apps-features"></a>Uso de las características de aplicaciones lógicas
En el [tema anterior](../logic-apps/logic-apps-create-a-logic-app.md), creó su primera aplicación lógica. Ahora le mostraremos cómo crear un proceso más completo mediante las aplicaciones lógicas de Servicios de aplicaciones. En este tema se presentan los siguientes conceptos de las nuevas aplicaciones lógicas:

* La lógica condicional, que ejecuta una acción solo cuando se cumple una determinada condición.
* Vista de código para editar una aplicación lógica existente.
* Opciones para iniciar un flujo de trabajo.

Antes de completar este tema, debe completar los pasos de [Creación de una nueva aplicación lógica](../logic-apps/logic-apps-create-a-logic-app.md). En el [Portal de Azure], vaya a su aplicación lógica y haga clic en **Acciones y desencadenadores** en el resumen para editar la definición de la aplicación lógica.

## <a name="reference-material"></a>Material de referencia
Estos documentos pueden serle útiles:

* [API de REST de administración y tiempo de ejecución](https://msdn.microsoft.com/library/azure/mt643787.aspx) : incluyen los métodos para invocar las aplicaciones lógicas directamente.
* [Referencia del lenguaje](https://msdn.microsoft.com/library/azure/mt643789.aspx) : una lista completa de todas las funciones y expresiones compatibles.
* [Tipos de desencadenadores y acciones](https://msdn.microsoft.com/library/azure/mt643939.aspx) : los diferentes tipos de acciones y las entradas que toman.
* [Información general sobre el Servicio de aplicaciones](../app-service/app-service-value-prop-what-is.md) : descripción de los componentes que se deben elegir al crear una solución.

## <a name="adding-conditional-logic"></a>Agregar lógica condicional
Aunque el flujo original funciona, hay algunas áreas que se podrían mejorar.

### <a name="conditional"></a>Condicional
Esta aplicación lógica puede provocar que reciba muchos correos electrónicos. Los siguientes pasos agregan lógica adicional para asegurarse de que solo reciba un correo electrónico cuando el tweet proceda de una persona con un determinado número de seguidores.

1. Haga clic en el signo más y busque la acción *Get User* (Obtener usuario) de Twitter.
2. Pase el campo **Twitteado por** desde el desencadenador para obtener la información sobre el usuario de Twitter.

    ![Get User](media/logic-apps-use-logic-app-features/getuser.png)
3. Haga clic en el signo más de nuevo, pero esta vez seleccione **Agregar condición**
4. En el primer cuadro, haga clic en **...** debajo de **Get User** (Obtener usuario) para buscar el campo **Followers count** (Número de seguidores).
5. En la lista desplegable, seleccione **Superior a**
6. En el segundo cuadro, escriba el número de seguidores que desea que tengan los usuarios.

    ![Condicional](media/logic-apps-use-logic-app-features/conditional.png)
7. Por último, arrastre y coloque el cuadro de correo electrónico en el cuadro **Si procede** . Esto significa que solo obtendrá correos electrónicos cuando se alcance el número de seguidores.

## <a name="repeating-over-a-list-with-foreach"></a>Repetición de una lista con forEach
El bucle forEach especifica una matriz para repetir una acción varias veces. Si no es una matriz se produce un error en el flujo. Por ejemplo, si tiene action1, que genera una matriz de mensajes, y desea enviar cada mensaje, puede incluir esta instrucción forEach en las propiedades de la acción: forEach: "@action('action1').outputs.messages"

## <a name="using-the-code-view-to-edit-a-logic-app"></a>Uso de la vista código para modificar una aplicación lógica
Además del diseñador, puede editar directamente el código que define una aplicación lógica, como sigue.

1. Haga clic en el botón **vista Código** en la barra de comandos.

    Se abrirá un editor completo que muestra la definición que acaba de modificar.

    ![vista Código](media/logic-apps-use-logic-app-features/codeview.png)

    Mediante el editor de texto, puede copiar y pegar cualquier cantidad de acciones dentro de la misma aplicación lógica o entre aplicaciones lógicas. También puede agregar o quitar secciones completas de la definición, así como compartir definiciones con otros.
2. Después de realizar los cambios en la vista Código, simplemente haga clic en **Guardar**.

### <a name="parameters"></a>Parámetros
Hay algunas capacidades de las aplicaciones lógicas que solo puede utilizarse en la vista Código. Un ejemplo son los parámetros. Los parámetros facilitan volver a usar los valores a lo largo de la aplicación lógica. Por ejemplo, si tiene una dirección de correo electrónico que desea utilizar en varias acciones, debe definirla como un parámetro.

Lo siguiente actualiza la aplicación lógica existente para que use parámetros para el término de la consulta.

1. En la vista Código, busque el objeto `parameters : {}` e inserte el siguiente objeto de tema:

        "topic" : {
            "type" : "string",
            "defaultValue" : "MicrosoftAzure"
        }
2. Desplácese hasta la acción `twitterconnector`, busque el valor de la consulta y reemplácelo por `#@{parameters('topic')}`.
    También puede usar la función **concat** para unir dos o más cadenas, por ejemplo:  `@concat('#',parameters('topic'))` es idéntica a la anterior.

Los parámetros son una buena forma de extraer valores que probablemente cambie mucho. Son especialmente útiles cuando necesite reemplazar parámetros en entornos diferentes. Para obtener más información sobre cómo invalidar los parámetros basados en el entorno, consulte nuestra [documentación de API de REST](https://msdn.microsoft.com/library/mt643787.aspx).

Ahora, al hacer clic en **Save** (Guardar), cada hora recibirá todos los nuevos tweets que tengan más de cinco retweets en una carpeta llamada **tweets** de Dropbox.

Para obtener más información acerca de las definiciones de aplicación lógica, consulte [Creación de definiciones de aplicación lógica](../logic-apps/logic-apps-author-definitions.md).

## <a name="starting-a-logic-app-workflow"></a>Inicio de un flujo de trabajo de aplicación lógica
Hay varias opciones para iniciar el flujo de trabajo definido en la aplicación lógica. Un flujo de trabajo siempre se puede iniciar a petición en el [Portal de Azure].

### <a name="recurrence-triggers"></a>Desencadenadores periódicos
Un desencadenador periódico se ejecuta en un intervalo que especifique. Cuando el desencadenador tiene lógica condicional, el desencadenador determina si necesita ejecutar el flujo de trabajo. Un desencadenador indica si se debe ejecutar; para ello, devuelve un código de estado de `200` . Cuando no es necesario ejecutarlo, devuelve un código de estado `202` .

### <a name="callback-using-rest-apis"></a>Devolución de llamada mediante las API de REST
Los servicios pueden llamar a un extremo de aplicación lógica para iniciar un flujo de trabajo. Consulte [Aplicaciones lógicas como puntos de conexión invocables](../logic-apps/logic-apps-http-endpoint.md) para más información. Para iniciar ese tipo de aplicación lógica a petición, haga clic en el botón **Ejecutar ahora** de la barra de comandos. 

<!-- Shared links -->
[Portal de Azure]: https://portal.azure.com



<!--HONumber=Jan17_HO4-->


