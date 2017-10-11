---
title: Conector webhook para Azure Logic Apps | Microsoft Docs
description: "Cómo se usan los desencadenadores y las acciones de webhook para realizar acciones de las aplicaciones lógicas como Filtrar Matriz"
services: logic-apps
author: jeffhollan
manager: anneta
editor: 
documentationcenter: 
tags: connectors
ms.assetid: 71775384-6c3a-482c-a484-6624cbe4fcc7
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/21/2016
ms.author: jehollan; LADocs
ms.openlocfilehash: fbfef291334109c6dcfcde80741874549fb7929f
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="get-started-with-the-webhook-connector"></a>Introducción al conector de webhook

Con el desencadenador y la acción de webhook se pueden iniciar, pausar y reanudar flujos para realizar estas tareas:

* Realizar una desencadenación desde un [Centro de eventos de Azure cuando se reciba un elemento](https://github.com/logicappsio/EventHubAPI)
* Esperar una aprobación antes de continuar con un flujo de trabajo

Más información sobre [cómo crear API personalizadas compatibles con webhook](../logic-apps/logic-apps-create-api-app.md).

## <a name="use-the-webhook-trigger"></a>Uso del desencadenador de webhook

Un [ *desencadenador* ](connectors-overview.md) es un evento que inicia un flujo de trabajo de aplicación lógica. Un desencadenador de webhook se basa en eventos y no depende del sondeo para los nuevos elementos. Al igual que el [desencadenador de solicitud](connectors-native-reqres.md), la aplicación lógica se activa en el instante en el que se produce un evento. El desencadenador de webhook registra una *dirección URL de devolución de llamada* para un servicio y la usa para activar la aplicación lógica según proceda.

Este ejemplo muestra cómo configurar un desencadenador HTTP en el Diseñador de aplicación lógica. En este paso se da por hecho que ya ha implementado una API que sigue [el patrón de suscripción y cancelación de suscripción de webhook que se utiliza en las aplicaciones lógicas](../logic-apps/logic-apps-create-api-app.md#webhook-triggers), o bien que ya ha accedido a una API de este tipo. La llamada de suscripción se realiza cada vez que se guarda una aplicación lógica con un nuevo webhook, o bien cuando se cambia el estado de deshabilitado a habilitado. La llamada de cancelación de suscripción se realiza cada vez que se elimina o guarda un desencadenador de webhook de aplicación lógica, o bien cuando se cambia el estado de habilitado a deshabilitado.

**Para agregar el desencadenador de webhook**

1. Como primer paso, agregue el desencadenador de **Webhook HTTP** en una aplicación lógica.
2. Rellene los parámetros de las llamadas de suscripción y cancelación de suscripción de webhook.

   En este paso se sigue el mismo patrón del formato de la [acción HTTP](connectors-native-http.md).

     ![Desencadenador HTTP](./media/connectors-native-webhook/using-trigger.png)

3. Agregue una acción como mínimo.
4. Haga clic en **Guardar** para publicar la aplicación lógica. En este paso se llamará al punto de conexión de suscripción con la URL de devolución de llamada que se necesita para desencadenar la aplicación lógica.
5. Cada vez que el servicio realiza una solicitud `HTTP POST` en la URL de devolución de llamada, la aplicación lógica se activa (e incluye todos los datos transmitidos en la solicitud).

## <a name="use-the-webhook-action"></a>Uso de la acción de webhook

Una [*acción*](connectors-overview.md) es una operación que se lleva a cabo mediante el flujo de trabajo definido en una aplicación lógica. Las acciones de webhook registran una *URL de devolución de llamadas* en un servicio y esperan hasta que se llame a la URL antes de reanudarse. ["Enviar correo electrónico de aprobación"](connectors-create-api-office365-outlook.md) es un ejemplo de conector que sigue este patrón. Puede extender este patrón a cualquier servicio a través de la acción de webhook. 

Este ejemplo muestra cómo configurar una acción de webhook en el Diseñador de aplicación lógica. En estos pasos se da por hecho que ya ha implementado una API que sigue [el patrón de suscripción y cancelación de suscripción de webhook que se utiliza en las aplicaciones lógicas](../logic-apps/logic-apps-create-api-app.md#webhook-actions), o bien que ya ha accedido a una API de este tipo. La llamada de suscripción se realiza cada vez que una aplicación lógica ejecuta la acción de webhook. La llamada de cancelación de suscripción se realiza cada vez que se cancela una ejecución mientras se espera una respuesta, o bien antes de que se agote el tiempo de espera de la aplicación lógica.

**Para agregar una acción de webhook**

1. Elija **Nuevo paso** > **Agregar una acción**.

2. En el cuadro de búsqueda, escriba "webhook" para buscar la acción **Webhook HTTP**.

    ![Acción de consulta de selección](./media/connectors-native-webhook/using-action-1.png)

3. Rellene los parámetros de las llamadas de suscripción y cancelación de suscripción de webhook.

   En este paso se sigue el mismo patrón del formato de la [acción HTTP](connectors-native-http.md).

     ![Acción de consulta de finalización](./media/connectors-native-webhook/using-action-2.png)
   
   En tiempo de ejecución, la aplicación lógica llama al punto de conexión de suscripción una vez alcanzado este paso.

4. Haga clic en **Guardar** para publicar la aplicación lógica.

## <a name="technical-details"></a>Detalles técnicos

A continuación encontrará más detalles sobre los desencadenadores y las acciones compatibles con webhook.

## <a name="webhook-triggers"></a>Desencadenadores de webhook

| Acción | Descripción |
| --- | --- |
| Webhook HTTP |Suscribe una URL de devolución de llamada a un servicio que puede llamar a la URL para activar la aplicación de lógica según sea necesario. |

### <a name="trigger-details"></a>Detalles del desencadenador

#### <a name="http-webhook"></a>Webhook HTTP

Suscribe una URL de devolución de llamada a un servicio que puede llamar a la URL para activar la aplicación de lógica según sea necesario.
Un asterisco (*) indica un campo obligatorio.

| Display Name (Nombre para mostrar) | Nombre de propiedad | Descripción |
| --- | --- | --- |
| Método de suscripción* |estático |Método HTTP que va a utilizarse para la solicitud de suscripción. |
| URI de suscripción* |uri |URI HTTP que va a utilizarse para la solicitud de suscripción. |
| Método de cancelación de suscripción* |estático |Método HTTP que va a utilizarse para la solicitud de cancelación de suscripción. |
| URI de cancelación de suscripción* |uri |URI HTTP que va a utilizarse para la solicitud de cancelación de suscripción. |
| Cuerpo de suscripción |body |Cuerpo de la solicitud HTTP para realizar la suscripción. |
| Encabezados de suscripción |encabezados |Encabezados de la solicitud HTTP para realizar la suscripción. |
| Autenticación de suscripción |authentication |Autenticación HTTP que se utiliza para realizar la suscripción. Para más información consulte el [conector HTTP](connectors-native-http.md#authentication) |
| Cuerpo de cancelación de suscripción |body |Cuerpo de la solicitud HTTP para cancelar la suscripción. |
| Encabezados de cancelación de suscripción |encabezados |Encabezados de la solicitud HTTP para cancelar la suscripción. |
| Autenticación de cancelación de suscripción |authentication |Autenticación HTTP que se utiliza para cancelar la suscripción. Para más información consulte el [conector HTTP](connectors-native-http.md#authentication) |

**Detalles de salida**

Solicitud de webhook

| Nombre de propiedad | Tipo de datos | Descripción |
| --- | --- | --- |
| encabezados |objeto |Encabezados de la solicitud de webhook |
| body |objeto |Objeto de la solicitud de webhook |
| Código de estado |int |Código de estado de la solicitud de webhook |

## <a name="webhook-actions"></a>Acciones de webhook

| Acción | Descripción |
| --- | --- |
| Webhook HTTP |Suscribe una URL de devolución de llamada a un servicio que puede llamar a la URL para reanudar un paso de flujo de trabajo según sea necesario. |

### <a name="action-details"></a>Detalles de la acción

#### <a name="http-webhook"></a>Webhook HTTP

Suscribe una URL de devolución de llamada a un servicio que puede llamar a la URL para reanudar un paso de flujo de trabajo según sea necesario.
Un asterisco (*) indica un campo obligatorio.

| Display Name (Nombre para mostrar) | Nombre de propiedad | Descripción |
| --- | --- | --- |
| Método de suscripción* |estático |Método HTTP que va a utilizarse para la solicitud de suscripción. |
| URI de suscripción* |uri |URI HTTP que va a utilizarse para la solicitud de suscripción. |
| Método de cancelación de suscripción* |estático |Método HTTP que va a utilizarse para la solicitud de cancelación de suscripción. |
| URI de cancelación de suscripción* |uri |URI HTTP que va a utilizarse para la solicitud de cancelación de suscripción. |
| Cuerpo de suscripción |body |Cuerpo de la solicitud HTTP para realizar la suscripción. |
| Encabezados de suscripción |encabezados |Encabezados de la solicitud HTTP para realizar la suscripción. |
| Autenticación de suscripción |authentication |Autenticación HTTP que se utiliza para realizar la suscripción. Para más información consulte el [conector HTTP](connectors-native-http.md#authentication) |
| Cuerpo de cancelación de suscripción |body |Cuerpo de la solicitud HTTP para cancelar la suscripción. |
| Encabezados de cancelación de suscripción |encabezados |Encabezados de la solicitud HTTP para cancelar la suscripción. |
| Autenticación de cancelación de suscripción |authentication |Autenticación HTTP que se utiliza para cancelar la suscripción. Para más información consulte el [conector HTTP](connectors-native-http.md#authentication) |

**Detalles de salida**

Solicitud de webhook

| Nombre de propiedad | Tipo de datos | Descripción |
| --- | --- | --- |
| encabezados |objeto |Encabezados de la solicitud de webhook |
| body |objeto |Objeto de la solicitud de webhook |
| Código de estado |int |Código de estado de la solicitud de webhook |

## <a name="next-steps"></a>Pasos siguientes

* [Creación de una aplicación lógica](../logic-apps/logic-apps-create-a-logic-app.md)
* [Buscar otros conectores](apis-list.md)