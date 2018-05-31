---
title: Control de mensajes grandes en Azure Logic Apps | Microsoft Docs
description: Aprenda a controlar tamaños de mensaje grandes con la fragmentación de Logic Apps.
services: logic-apps
documentationcenter: ''
author: shae-hurst
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: logic-apps
ms.workload: logic-apps
ms.devlang: ''
ms.tgt_pltfrm: ''
ms.topic: article
ms.date: 4/27/2018
ms.author: shhurst
ms.openlocfilehash: a99fbdd7c9beb32f640d5ca623f8bcda3cb9aba4
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/14/2018
ms.locfileid: "34166155"
---
# <a name="handle-large-messages-with-chunking-in-logic-apps"></a>Control de mensajes grandes con la fragmentación de Logic Apps

Cuando se administran los mensajes, Logic Apps limita el contenido del mensaje a un tamaño máximo. Este límite ayuda a reducir la sobrecarga creada al almacenar y procesar mensajes de gran tamaño. Para controlar mensajes que superan este límite, Logic Apps puede *fragmentar* un mensaje grande en mensajes más pequeños. De este modo, puede seguir transfiriendo archivos grandes con Logic Apps en condiciones determinadas. Cuando se comunica con otros servicios mediante conectores o HTTP, Logic Apps puede consumir mensajes grandes pero *solo* en fragmentos. Esta condición significa que los conectores deben admitir también la fragmentación, o que en el intercambio de mensajes HTTP subyacente entre Logic Apps y estos servicios y aplicaciones se debe usar la fragmentación.

En este artículo se muestra cómo puede configurar la compatibilidad con la fragmentación en mensajes que superan el límite.

## <a name="what-makes-messages-large"></a>¿Qué hace que los mensajes sean "grandes"?

Los mensajes son "grandes" en función del servicio que los administra. El límite de tamaño exacto en mensajes grandes varía entre Logic Apps y los conectores. Tanto Logic Apps como los conectores no pueden consumir directamente mensajes grandes, así que es necesario fragmentarlos. Para conocer el límite de tamaño de los mensajes de Logic Apps, consulte [Límites y configuración de Logic Apps](../logic-apps/logic-apps-limits-and-config.md).
Para información sobre el límite de tamaño de los mensajes de cada conector, consulte los [detalles técnicos específicos del conector](../connectors/apis-list.md).

### <a name="chunked-message-handling-for-logic-apps"></a>Control de mensajes fragmentados en Logic Apps

Logic Apps no puede usar directamente las salidas de los mensajes fragmentados con un tamaño superior al límite. Solo las acciones que admiten la fragmentación pueden acceder al contenido de mensaje en estas salidas. Por lo tanto, una acción que controla mensajes grandes debe satisfacer *cualquiera* de estos criterios:

* Admitir la fragmentación de forma nativa cuando esa acción pertenece a un conector 
* Tener habilitada la compatibilidad con la fragmentación en la configuración de tiempo de ejecución de esa acción 

De lo contrario, al intentar acceder a una salida con contenido de gran tamaño, recibirá un error. Para habilitar la fragmentación, consulte [Set up chunking support](#set-up-chunking) (Configuración de la compatibilidad con la fragmentación).

### <a name="chunked-message-handling-for-connectors"></a>Control de mensajes fragmentados de conectores

Los servicios que se comunican con Logic Apps pueden tener sus propios límites de tamaño de mensaje. Estos límites suelen ser menores que el límite de Logic Apps. Por ejemplo, suponiendo que admita la fragmentación, un conector podría considerar que un mensaje de 30 MB es grande, mientras que Logic Apps no. Para cumplir con el límite de este conector, Logic Apps divide los mensajes de más de 30 MB en fragmentos más pequeños.

Para los conectores que admiten la fragmentación, el protocolo de fragmentación subyacente es invisible para los usuarios finales. Sin embargo, no todos los conectores admiten la fragmentación, por lo que estos conectores generan errores en tiempo de ejecución cuando los mensajes entrantes superan los límites de tamaño de los conectores.

<a name="set-up-chunking"></a>

## <a name="set-up-chunking-over-http"></a>Configuración de la fragmentación mediante HTTP

En escenarios de HTTP genéricos, puede dividir las cargas y descargas de contenido de gran tamaño mediante HTTP, de forma que la aplicación lógica y un punto de conexión pueden intercambiar mensajes grandes. Sin embargo, debe fragmentar los mensajes de la forma esperada por Logic Apps. 

Si un punto de conexión ha habilitado la fragmentación para cargas o descargas, las acciones HTTP de la aplicación lógica fragmentan automáticamente los mensajes grandes. En caso contrario, debe configurar la compatibilidad con la fragmentación en el punto de conexión. Si no posee ni controla el punto de conexión o el conector, posiblemente no tenga la opción de configurar la fragmentación.

Además, si una acción HTTP ya no permite la fragmentación, también debe configurarla en la propiedad `runTimeConfiguration` de la acción. Puede establecer esta propiedad dentro de la acción, ya sea directamente en el editor de vista de código, tal y como se describe más adelante, o en el Diseñador de aplicaciones lógicas, como se describe aquí:

1. En la esquina superior derecha de la acción HTTP, elija el botón de puntos suspensivos (**...** ) y, a continuación, elija **Configuración**.

   ![En la acción, abra el menú Configuración.](./media/logic-apps-handle-large-messages/http-settings.png)

2. En **Transferencia de contenido**, establezca **Permitir fragmentación** en **Activado**.

   ![Activar la fragmentación](./media/logic-apps-handle-large-messages/set-up-chunking.png)

3. Para seguir con la configuración de la fragmentación para cargas y descargas, continúe con las siguientes secciones.

<a name="download-chunks"></a>

## <a name="download-content-in-chunks"></a>Descarga del contenido en fragmentos

Muchos puntos de conexión envían automáticamente mensajes grandes en fragmentos cuando se descargan mediante una solicitud HTTP GET. Para descargar mensajes fragmentados desde un punto de conexión mediante HTTP, el punto de conexión debe admitir las solicitudes de contenido parciales, o *Descargas fragmentadas*. Cuando la aplicación lógica envía una solicitud HTTP GET a un punto de conexión para la descarga de contenido y el punto de conexión responde con un código de estado "206", la respuesta contiene contenido fragmentado. Logic Apps no puede controlar si un punto de conexión admite solicitudes parciales. Sin embargo, cuando la aplicación lógica obtiene primero la respuesta "206", envía automáticamente varias solicitudes para descargar todo el contenido.

Para comprobar si un punto de conexión puede admitir contenido parcial, envíe una solicitud HEAD. Esta solicitud le ayuda a determinar si la respuesta contiene el encabezado `Accept-Ranges`. De este modo, si el punto de conexión admite descargas fragmentadas pero no envía contenido fragmentado, puede *sugerir* esta opción si establece el encabezado `Range` de la solicitud HTTP GET. 

Estos pasos describen el proceso detallado que usa Logic Apps para descargar contenido fragmentado desde un punto de conexión hasta la aplicación lógica:

1. La aplicación lógica envía una solicitud HTTP GET al punto de conexión.

   El encabezado de solicitud puede incluir opcionalmente un campo `Range` que describe un intervalo de bytes de fragmentos de contenido solicitados.

2. El punto de conexión responde con el código de estado "206" y un cuerpo del mensaje HTTP.

    Los detalles sobre el contenido de este fragmento aparecen en el encabezado `Content-Range` de la respuesta, junto con información que ayuda a Logic Apps a determinar el inicio y la finalización del fragmento, más el tamaño total de todo el contenido antes de la fragmentación.

3. La aplicación lógica envía automáticamente solicitudes HTTP GET de seguimiento.

    La aplicación lógica envía solicitudes GET de seguimiento hasta que se recupera todo el contenido.

Por ejemplo, la definición de esta acción muestra una solicitud HTTP GET que establece el encabezado `Range`. El encabezado *sugiere* que el punto de conexión debe responder con contenido fragmentado:

```json
"getAction": {
    "inputs": {
        "headers": {
            "Range": "bytes=0-1023"
        },
       "method": "GET",
       "uri": "http://myAPIendpoint/api/downloadContent"
    },
    "runAfter": {},
    "type": "Http"
}
```

La solicitud GET establece el encabezado "Range" en "bytes = 0-1023", que es el intervalo de bytes. Si el punto de conexión admite solicitudes de contenido parcial, responde con un fragmento de contenido del intervalo solicitado. En función del punto de conexión, el formato exacto del campo de encabezado "Range" puede variar.

<a name="upload-chunks"></a>

## <a name="upload-content-in-chunks"></a>Carga de contenido en fragmentos

Para cargar contenido fragmentado desde una acción HTTP, la acción debe tener habilitada la compatibilidad con la fragmentación mediante la propiedad `runtimeConfiguration` de la acción. Esta configuración permite que la acción inicie el protocolo de fragmentación. La aplicación lógica, a continuación, puede enviar un mensaje POST o PUT inicial al punto de conexión de destino. Una vez que el punto de conexión responde con un tamaño de fragmento sugerido, la aplicación lógica realiza el seguimiento mediante el envío de solicitudes HTTP PATCH que contienen los fragmentos de contenido.

Estos pasos describen el proceso detallado que usa Logic Apps para cargar contenido fragmentado desde la aplicación lógica hasta un punto de conexión:

1. La aplicación lógica envía una solicitud HTTP POST o PUT inicial con un cuerpo de mensaje vacío. El encabezado de solicitud incluye esta información sobre el contenido que la aplicación lógica desea cargar en fragmentos:

   | Campo de encabezado de solicitud de Logic Apps | Valor | Escriba | DESCRIPCIÓN |
   |---------------------------------|-------|------|-------------|
   | **x-ms-transfer-mode** | chunked | string | Indica que el contenido se carga en fragmentos |
   | **x-ms-content-length** | <*content-length*> | Entero | El tamaño del contenido completo en bytes antes de la fragmentación |
   ||||

2. El punto de conexión responde con el código de estado correcto "200" y esta información opcional:

   | Campo de encabezado de respuesta del punto de conexión | Escriba | Obligatorio | DESCRIPCIÓN |
   |--------------------------------|------|----------|-------------|
   | **x-ms-chunk-size** | Entero | Sin  | El tamaño de fragmento sugerido en bytes |
   | **Ubicación** | string | Sin  | La ubicación de la dirección URL a la que enviar los mensajes HTTP PATCH |
   ||||

3. La aplicación lógica crea y envía mensajes HTTP PATCH de seguimiento, cada uno con esta información:

   * Un fragmento de código basado en **x-ms-chunk-size** o en algún tamaño calculado interno hasta que todo el contenido que suma **x-ms-content-length** se carga de manera secuencial

   * Estos detalles de encabezado sobre el fragmento de contenido enviado en cada mensaje PATCH:

     | Campo de encabezado de solicitud de Logic Apps | Valor | Escriba | DESCRIPCIÓN |
     |---------------------------------|-------|------|-------------|
     | **Content-Range** | <*range*> | string | El intervalo de bytes del fragmento de contenido actual, que incluye el valor inicial, el valor final y el tamaño total del contenido, por ejemplo: "bytes = 0-1023/10100" |
     | **Content-Type** | <*content-type*> | string | El tipo de contenido fragmentado |
     | **Content-Length** | <*content-length*> | string | La longitud de tamaño en bytes del fragmento actual |
     |||||

4. Después de cada solicitud PATCH, el punto de conexión responde con el código de estado "200" para confirmar la recepción de cada fragmento.

Por ejemplo, la definición de esta acción muestra una solicitud HTTP POST para cargar contenido fragmentado en un punto de conexión. En la propiedad `runTimeConfiguration` de la acción, la propiedad `contentTransfer` establece `transferMode` en `chunked`:

```json
"postAction": {
    "runtimeConfiguration": {
        "contentTransfer": {
            "transferMode": "chunked"
        }
    },
    "inputs": {
        "method": "POST",
        "uri": "http://myAPIendpoint/api/action",
        "body": "@body('getAction')"
    },
    "runAfter": {
    "getAction": ["Succeeded"]
    },
    "type": "Http"
}
```