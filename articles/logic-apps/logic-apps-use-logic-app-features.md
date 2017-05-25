---
title: "Incorporación de lógica condicional e inicio de flujos de trabajo - Azure Logic Apps | Microsoft Docs"
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
ms.translationtype: Human Translation
ms.sourcegitcommit: 9f7d623ec213de6d46f59547aff9d4417ac95ede
ms.openlocfilehash: 41aafe94d24f0e22fe2256ab213c7668b670764c
ms.contentlocale: es-es
ms.lasthandoff: 02/15/2017


---
# <a name="use-logic-apps-features"></a>Uso de las características de aplicaciones lógicas
En el [tema anterior](../logic-apps/logic-apps-create-a-logic-app.md), creó su primera aplicación lógica. Ahora puede crear un proceso más completo con Azure Logic Apps. En este tema se presentan los siguientes conceptos nuevos sobre Azure Logic Apps:

* La lógica condicional, que ejecuta una acción solo cuando se cumple una determinada condición.
* Vista de código para editar una aplicación lógica existente.
* Opciones para iniciar un flujo de trabajo.

Antes de completar este tema, debe completar los pasos de [Creación de una nueva aplicación lógica](../logic-apps/logic-apps-create-a-logic-app.md). En el [Azure Portal], vaya a su aplicación lógica y haga clic en **Acciones y desencadenadores** en el resumen para editar la definición de la aplicación lógica.

## <a name="reference-material"></a>Material de referencia
Estos documentos pueden serle útiles:

* [API de REST de administración y tiempo de ejecución](https://msdn.microsoft.com/library/azure/mt643787.aspx) : incluyen los métodos para invocar las aplicaciones lógicas directamente.
* [Referencia del lenguaje](https://msdn.microsoft.com/library/azure/mt643789.aspx) : una lista completa de todas las funciones y expresiones compatibles.
* [Tipos de desencadenadores y acciones](https://msdn.microsoft.com/library/azure/mt643939.aspx) : los diferentes tipos de acciones y las entradas que toman.
* [Información general sobre el Servicio de aplicaciones](../app-service/app-service-value-prop-what-is.md) : descripción de los componentes que se deben elegir al crear una solución.

## <a name="add-conditional-logic-to-your-logic-app"></a>Agregar lógica condicional a la aplicación lógica

Aunque el flujo original de la aplicación lógica funciona, se podrían mejorar algunas áreas.

### <a name="conditional"></a>Condicional

La primera aplicación lógica podría dar lugar a que reciba demasiados correos electrónicos. Los siguientes pasos agregan lógica condicional para que reciba correo electrónico solo cuando el tweet proceda de una persona con un determinado número de seguidores.

0. En el Diseñador de aplicación lógica, seleccione **Nuevo paso** (+) > **Agrega una acción**.
0.    Busque y agregue la acción **Get User** (Obtener usuario) para Twitter.
0. Para obtener información sobre el usuario de Twitter, busque y agregue el campo **Tweeted by** (Twitteado por) desde el desencadenador.

    ![Get User](media/logic-apps-use-logic-app-features/getuser.png)

0. Elija **Nuevo paso** (+) > **Agrega una condición**.
0. Para filtrar según el número de seguidores de que disponen los usuarios, en **Nombre de objeto**, elija **Agregar contenido dinámico**. 
0.    En el cuadro de búsqueda, busque y agregue el campo **Followers count** (Recuento seguidores).
0. En **Relationship** (Relación), seleccione **is greater than** (es mayor que).
0. En el cuadro **Value** (Valor), escriba el número de seguidores que desea que tengan los usuarios.

    ![Condicional](media/logic-apps-use-logic-app-features/conditional.png)

0. Por último, arrastre el cuadro **Send email** (Enviar correo electrónico) al cuadro **If Yes** (Si procede). 

Ahora recibirá los correos electrónicos solo cuando el recuento de seguidores cumpla la condición.

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

### <a name="parameters"></a>Parámetros

Algunas funcionalidades de Logic Apps solo están disponibles en la vista de código, como, por ejemplo, los parámetros. Los parámetros facilitan volver a usar los valores en toda la aplicación lógica. Por ejemplo, si tiene una dirección de correo electrónico que desea utilizar en varias acciones, debe definirla como un parámetro.

Los parámetros son una buena forma de extraer valores que probablemente cambie mucho. Son especialmente útiles cuando necesite reemplazar parámetros en entornos diferentes. Para obtener información sobre cómo invalidar los parámetros basados en el entorno, vea la [documentación de API de REST](https://docs.microsoft.com/rest/api/logic).

En este ejemplo se muestra cómo actualizar la aplicación lógica existente, para que pueda usar los parámetros para el término de consulta.

1. En la vista de código, busque el objeto `parameters : {}` y agregue un objeto de tema:

        "topic" : {
            "type" : "string",
            "defaultValue" : "MicrosoftAzure"
        }

2. Vaya a la acción `twitterconnector`, busque el valor de consulta y reemplace ese valor por `#@{parameters('topic')}`. 

    Para combinar dos o más cadenas, también puede usar la función `concat`. 
    Por ejemplo, `@concat('#',parameters('topic'))` funciona igual que en el caso anterior.

3.    Cuando termine, seleccione **Guardar**. 

    Ahora, cada hora recibirá todos los nuevos tweets que tengan más de cinco retweets en una carpeta llamada **tweets** de Dropbox.

Para obtener más información acerca de las definiciones de aplicación lógica, consulte [Creación de definiciones de aplicación lógica](../logic-apps/logic-apps-author-definitions.md).

## <a name="start-logic-app-workflows"></a>Inicio de flujos de trabajo de aplicación lógica

Hay diferentes opciones para iniciar el flujo de trabajo definido en la aplicación lógica. Un flujo de trabajo siempre se puede iniciar a petición en [Azure Portal].

### <a name="recurrence-triggers"></a>Desencadenadores periódicos

Un desencadenador periódico se ejecuta en un intervalo que especifique. Cuando el desencadenador tiene lógica condicional, este determina si necesita ejecutar el flujo de trabajo. Un desencadenador indica si se debe ejecutar el flujo de trabajo, para lo que devuelve un código de estado `200`. Cuando no es necesario ejecutar el flujo de trabajo, el desencadenador devuelve un código de estado `202`.

### <a name="callback-using-rest-apis"></a>Devolución de llamada mediante las API de REST

Para iniciar un flujo de trabajo, los servicios pueden llamar a un punto de conexión de aplicación lógica. Para iniciar ese tipo de aplicación lógica a petición, seleccione **Ejecutar ahora** en la barra de comandos. Vea [Start workflows by calling logic app endpoints as triggers](../logic-apps/logic-apps-http-endpoint.md) (Inicio de flujos de trabajo mediante llamadas a puntos de conexión de aplicación lógica como desencadenadores). 

<!-- Shared links -->
[Azure Portal]: https://portal.azure.com

