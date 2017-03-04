---
title: Conector de webhook de Logic Apps | Microsoft Docs
description: "En este artículo se muestra información general sobre las acciones y los desencadenadores de webhook para realizar acciones como Filtrar matriz."
services: 
documentationcenter: 
author: jeffhollan
manager: erikre
editor: 
tags: connectors
ms.assetid: 71775384-6c3a-482c-a484-6624cbe4fcc7
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/21/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: c0edbe421050ad46f6d31fd6416df4b344b233ad
ms.openlocfilehash: 2c24699f6253fc7952b331ebcce4b937f1c83603
ms.lasthandoff: 01/20/2017


---
# <a name="get-started-with-the-webhook-connector"></a>Introducción al conector de webhook
Con el desencadenador y la acción de webhook se pueden desencadenar, pausar y reanudar flujos con los siguientes objetivos:

* Realizar una desencadenación desde un [Centro de eventos de Azure en cuanto se reciba un elemento](https://github.com/logicappsio/EventHubAPI)
* Esperar una aprobación antes de continuar con un flujo de trabajo

[En este artículo sobre creación de conectores de aplicaciones lógicas](../logic-apps/logic-apps-create-api-app.md)puede encontrar información sobre cómo crear una API compatible con llamadas de suscripción de webhook.

- - -
## <a name="use-the-webhook-trigger"></a>Uso del desencadenador de webhook
Un desencadenador es un evento que se puede utilizar para iniciar el flujo de trabajo definido en una aplicación lógica. [Más información sobre los desencadenadores](connectors-overview.md).  Los desencadenadores de webhook son muy útiles, ya que no dependen de que se sondeen nuevos elementos (al igual que el [desencadenador de solicitud](connectors-native-reqres.md) , la aplicación lógica se activará en cuanto se produzca un evento).  Para ello, registra una *URL de devolución de llamadas* en un servicio que puede utilizarse para activar la aplicación lógica según sea necesario.

Esta es una secuencia de ejemplo de cómo configurar un desencadenador HTTP en el diseñador de aplicaciones lógicas.  En este artículo se da por hecho que ya ha implementado una API que sigue [el patrón de suscripción y cancelación de suscripción de webhook que se utiliza en Logic Apps](../logic-apps/logic-apps-create-api-app.md#webhook-triggers), o bien que ya ha accedido a una de este tipo.  La llamada de suscripción se realiza cada vez que se guarda una aplicación lógica con un nuevo webhook, o bien cuando se cambia el estado de deshabilitado a habilitado.  La llamada de cancelación de suscripción se realiza cada vez que se elimina o guarda un desencadenador de webhook de aplicación lógica, o bien cuando se cambia el estado de deshabilitado a habilitado.

1. Como primer paso, agregue el desencadenador de **Webhook HTTP** en una aplicación lógica.
2. Rellene los parámetros de las llamadas de suscripción y cancelación de suscripción de webhook.

   * Esto sigue el mismo patrón que el formato de la [acción HTTP](connectors-native-http.md)

     ![Desencadenador HTTP](./media/connectors-native-webhook/using-trigger.png)
3. Agregue una acción como mínimo.
4. Haga clic en Guardar para publicar la aplicación lógica. Esta acción llamará al punto de conexión de suscripción con la URL de devolución de llamada que se necesita para desencadenar esta aplicación lógica.
5. Cada vez que el servicio realiza una solicitud `HTTP POST` en la URL de devolución de llamada, la aplicación lógica se activa (e incluye todos los datos transmitidos en la solicitud).

## <a name="use-the-webhook-action"></a>Uso de la acción de webhook
Una acción es una operación que se lleva a cabo mediante el flujo de trabajo definido en una aplicación lógica. [Más información acerca de las acciones.](connectors-overview.md)  Las acciones de webhook son muy útiles, ya que registran una *URL de devolución de llamadas* en un servicio y esperan hasta que se llame a la URL antes de reanudarse.  ["Enviar correo electrónico de aprobación"](connectors-create-api-office365-outlook.md) es un ejemplo de conector que sigue este patrón.  Puede extender este patrón a cualquier servicio a través de la acción de webhook.  En este artículo se da por hecho que ya ha implementado una API que sigue [el patrón de suscripción y cancelación de suscripción de webhook que se utiliza en Logic Apps](../logic-apps/logic-apps-create-api-app.md#webhook-actions), o bien que ya ha accedido a una de este tipo.  La llamada de suscripción se realiza cada vez que una aplicación lógica ejecuta la acción de webhook.  La llamada de cancelación de suscripción se realiza cada vez que se cancela una ejecución mientras se espera una respuesta, o bien antes de que se agote el tiempo de espera para ejecutar dicha aplicación.

Para agregar una acción de webhook, siga estos pasos:

1. Haga clic en el botón **Nuevo paso** .
2. Elija **Agregar una acción**
3. En el cuadro de búsqueda de acciones, escriba webhook para mostrar la acción **HTTP Webhook** .

    ![Acción de consulta de selección](./media/connectors-native-webhook/using-action-1.png)
4. Rellene los parámetros de las llamadas de suscripción y cancelación de suscripción de webhook.

   * Esto sigue el mismo patrón que el formato de la [acción HTTP](connectors-native-http.md)

     ![Acción de consulta de finalización](./media/connectors-native-webhook/using-action-2.png)
   * La aplicación lógica llamará en tiempo de ejecución al punto de conexión de suscripción cuando llegue al paso.
5. Haga clic en Guardar en la esquina superior izquierda de la barra de herramientas; la aplicación lógica se guardará y se publicará (activará).

- - -
## <a name="technical-details"></a>Detalles técnicos
A continuación, se muestran los detalles de los desencadenadores y las acciones que admite webhook.

## <a name="webhook-triggers"></a>Desencadenadores de webhook
Un desencadenador consiste en una operación para iniciar un flujo de trabajo. [Más información sobre los desencadenadores.](connectors-overview.md) Este conector tiene 1 desencadenador.

| . | Descripción |
| --- | --- |
| Webhook HTTP |Suscribe una URL de devolución de llamada a un servicio que puede llamar a la URL para activar la aplicación de lógica según sea necesario. |

### <a name="trigger-details"></a>Detalles del desencadenador
El conector de webhook incluye 1 desencadenador posible. A continuación, se incluye información sobre la acción, los campos de entrada obligatorios y opcionales, y los detalles de salida correspondientes asociados a su uso.

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

<br>

**Detalles de salida**

Solicitud de webhook

| Nombre de propiedad | Tipo de datos | Descripción |
| --- | --- | --- |
| encabezados |objeto |Encabezados de la solicitud de webhook |
| body |objeto |Objeto de la solicitud de webhook |
| Código de estado |int |Código de estado de la solicitud de webhook |

## <a name="webhook-actions"></a>Acciones de webhook
Una acción es una operación que se lleva a cabo mediante el flujo de trabajo definido en una aplicación lógica. [Más información acerca de las acciones.](connectors-overview.md) El conector tiene 1 acción posible.

| . | Descripción |
| --- | --- |
| Webhook HTTP |Suscribe una URL de devolución de llamada a un servicio que puede llamar a la URL para reanudar un paso de flujo de trabajo según sea necesario. |

### <a name="action-details"></a>Detalles de la acción
El conector de webhook incluye 1 acción posible. A continuación, se incluye información sobre la acción, los campos de entrada obligatorios y opcionales, y los detalles de salida correspondientes asociados a su uso.

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

<br>

**Detalles de salida**

Solicitud de webhook

| Nombre de propiedad | Tipo de datos | Descripción |
| --- | --- | --- |
| encabezados |objeto |Encabezados de la solicitud de webhook |
| body |objeto |Objeto de la solicitud de webhook |
| Código de estado |int |Código de estado de la solicitud de webhook |

- - -
## <a name="next-steps"></a>Pasos siguientes
A continuación se muestran detalles sobre cómo avanzar con aplicaciones lógicas y nuestra comunidad.

## <a name="create-a-logic-app"></a>Creación de una aplicación lógica
Pruebe la plataforma y [cree una aplicación lógica](../logic-apps/logic-apps-create-a-logic-app.md) ahora. Puede explorar los demás conectores disponibles en aplicaciones consultando nuestra [lista de API](apis-list.md).

