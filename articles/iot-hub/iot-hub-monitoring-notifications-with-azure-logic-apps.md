---
title: "Supervisión remota y notificaciones de IoT con Azure Logic Apps | Microsoft Docs"
description: "Use Azure Logic Apps para la supervisión de temperatura de IoT en IoT Hub y el envío automático de notificaciones de correo electrónico al buzón de correo cada vez que se detecten anomalías."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "supervisión de iot, notificaciones de iot, supervisión de temperatura de iot"
ms.assetid: 43043067-2e1f-42c9-953d-e2dce8fd86df
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/25/2017
ms.author: xshi
ms.openlocfilehash: 7a611912ae55eb22103539dbba9f1a06aaa543b7
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="iot-remote-monitoring-and-notifications-with-azure-logic-apps-connecting-your-iot-hub-and-mailbox"></a>Supervisión remota y notificaciones de IoT con Azure Logic Apps conectando IoT Hub y el buzón de correo

![Diagrama integral](media/iot-hub-get-started-e2e-diagram/7.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

Azure Logic Apps proporciona una manera de automatizar los procesos como una serie de pasos. Una aplicación lógica puede conectar diversos servicios y protocolos. Comienza con un desencadenador como "Cuando se agregue una cuenta" y continúa con una combinación de acciones, una como "enviando una notificación de inserción". Esta característica convierte a Logic Apps en una solución de IoT perfecta para la supervisión de IoT, por ejemplo, para mantenerse alerta en busca de anomalías, entre otros escenarios de uso.

## <a name="what-you-learn"></a>Conocimientos que adquirirá

Aprenda a crear una aplicación lógica que conecte IoT Hub y el buzón de correo para la supervisión de temperatura y las notificaciones. Cuando la temperatura es superior a 30 °C, la aplicación cliente marca `temperatureAlert = "true"` en el mensaje que envía a IoT Hub. El mensaje desencadena la aplicación lógica para enviar una notificación de correo electrónico.

## <a name="what-you-do"></a>Qué debe hacer

* Crear un espacio de nombres de Service Bus y agregarle una cola.
* Agregar un punto de conexión y una regla de enrutamiento a IoT Hub.
* Crear, configurar y probar una aplicación lógica.

## <a name="what-you-need"></a>Lo que necesita

* Tutorial [Instalación de su dispositivo](iot-hub-raspberry-pi-kit-node-get-started.md) completado donde se abordan los siguientes requisitos:
  * Una suscripción de Azure activa.
  * Un centro de Azure IoT en su suscripción.
  * Una aplicación cliente que envía mensajes a su centro de Azure IoT.

## <a name="create-service-bus-namespace-and-add-a-queue-to-it"></a>Crear un espacio de nombres de Service Bus y agregarle una cola

### <a name="create-a-service-bus-namespace"></a>Crear un espacio de nombres de Service Bus

1. En [Azure Portal](https://portal.azure.com/), haga clic en **Nuevo** > **Enterprise Integration** > **Service Bus**.
1. Proporcione la siguiente información:

   **Nombre**: nombre de Service Bus.

   **Nivel de precios**: haga clic en **Básico** > **Seleccionar**. El nivel Básico es suficiente para este tutorial.

   **Grupo de recursos**: use el mismo grupo de recursos que usa el IoT Hub.

   **Ubicación**: use la misma ubicación que emplea IoT Hub.
1. Haga clic en **Crear**.

   ![Crear un espacio de nombres de Service Bus en Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/1_create-service-bus-namespace-azure-portal.png)

### <a name="add-a-service-bus-queue"></a>Agregar una cola de Service Bus

1. Abra el espacio de nombres de Service Bus y haga clic en **+ Cola**.
1. Escriba un nombre para la cola y haga clic en **Crear**.
1. Abra la cola de Service Bus y haga clic en **Directivas de acceso compartido** > **+ Agregar**.
1. Escriba un nombre para la directiva, active **Administrar** y luego haga clic en **Crear**.

   ![Agregar una cola de Service Bus en Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/2_add-service-bus-queue-azure-portal.png)

## <a name="add-an-endpoint-and-a-routing-rule-to-your-iot-hub"></a>Agregar un punto de conexión y una regla de enrutamiento a IoT Hub

### <a name="add-an-endpoint"></a>Agregación de un extremo

1. Abra IoT Hub, haga clic en Puntos de conexión > + Agregar.
1. Escriba la siguiente información:

   **Nombre**: nombre del punto de conexión.

   **Tipo de punto de conexión**: seleccione **Cola de Service Bus**.

   **Espacio de nombres de Service Bus**: seleccione el espacio de nombres que ha creado.

   **Cola de Service Bus**: seleccione la cola que ha creado.
1. Haga clic en **Aceptar**.

   ![Agregar un punto de conexión a IoT Hub en Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/3_add-iot-hub-endpoint-azure-portal.png)

### <a name="add-a-routing-rule"></a>Agregar una regla de enrutamiento

1. En IoT Hub, haga clic en **Rutas** > **+ Agregar**.
1. Escriba la siguiente información:

   **Nombre**: nombre de la regla de enrutamiento.

   **Origen de datos**: seleccione **DeviceMessages**.

   **Punto de conexión**: seleccione el punto de conexión que ha creado.

   **Cadena de consulta**: escriba `temperatureAlert = "true"`.
1. Haga clic en **Guardar**.

   ![Agregar una regla de enrutamiento en Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/4_add-routing-rule-azure-portal.png)

## <a name="create-and-configure-a-logic-app"></a>Crear y configurar una aplicación lógica

### <a name="create-a-logic-app"></a>Creación de una aplicación lógica

1. En [Azure Portal](https://portal.azure.com/), haga clic en **Nuevo** > **Enterprise Integration** > **Aplicación lógica**.
1. Escriba la siguiente información:

   **Nombre**: nombre de la aplicación lógica.

   **Grupo de recursos**: use el mismo grupo de recursos que usa el IoT Hub.

   **Ubicación**: use la misma ubicación que emplea IoT Hub.
1. Haga clic en **Crear**.

### <a name="configure-the-logic-app"></a>Configurar la aplicación lógica

1. Abra la aplicación lógica que se abre en el Diseñador de aplicaciones lógicas.
1. En el Diseñador de aplicaciones lógicas, haga clic en **Aplicación lógica en blanco**.

   ![Comenzar con una aplicación lógica en blanco en Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/5_start-with-blank-logic-app-azure-portal.png)

1. Haga clic en **Service Bus**.

   ![Seleccionar Service Bus para empezar a crear la aplicación lógica en Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/6_select-service-bus-when-creating-blank-logic-app-azure-portal.png)

1. Haga clic en **Service Bus: Cuando llegan uno o más mensajes a una cola (autocompletar)**.
1. Cree una conexión de Service Bus.
   1. Escriba un nombre de conexión.
   1. Haga clic en el espacio de nombres de Service Bus > directiva de Service Bus > **Crear**.

      ![Crear una conexión de Service Bus para la aplicación lógica en Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/7_create-service-bus-connection-in-logic-app-azure-portal.png)

   1. Haga clic en **Continuar** después de crear la conexión de Service Bus.
   1. Seleccione la cola que ha creado y escriba `175` en **Recuento máximo de mensajes**.

      ![Especificar el recuento máximo de mensajes de la conexión de Service Bus en la aplicación lógica](media/iot-hub-monitoring-notifications-with-azure-logic-apps/8_specify-maximum-message-count-for-service-bus-connection-logic-app-azure-portal.png)
   1. Haga clic en el botón "Guardar" para guardar los cambios.

1. Cree una conexión de servicio SMTP.
   1. Haga clic en **Nuevo paso** > **Agregar una acción**.
   1. Escriba `SMTP`, haga clic en el servicio **SMTP** en el resultado de la búsqueda y luego haga clic en **SMTP: Enviar correo electrónico**.

      ![Crear una conexión SMTP en la aplicación lógica en Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/9_create-smtp-connection-logic-app-azure-portal.png)

   1. Escriba la información SMTP del buzón de correo y luego haga clic en **Crear**.

      ![Escribir la información de la conexión SMTP en la aplicación lógica en Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/10_enter-smtp-connection-info-logic-app-azure-portal.png)

      Obtenga la información SMTP de [Hotmail/Outlook.com](https://support.office.com/en-us/article/Add-your-Outlook-com-account-to-another-mail-app-73f3b178-0009-41ae-aab1-87b80fa94970), [Gmail](https://support.google.com/a/answer/176600?hl=en) y [Yahoo Mail](https://help.yahoo.com/kb/SLN4075.html).
   1. Escriba la dirección de correo electrónico en **De** y **Para** y `High temperature detected` en **Asunto** y **Cuerpo**.
   1. Haga clic en **Guardar**.

La aplicación lógica está funcionando al guardarse.

## <a name="test-the-logic-app"></a>Probar la aplicación lógica

1. Inicie la aplicación cliente que se implementa en el dispositivo en [Connect ESP8266 to Azure IoT Hub (Conectar ESP8266 a Azure IoT Hub)](iot-hub-arduino-huzzah-esp8266-get-started.md).
1. Aumente la temperatura ambiental en torno al SensorTag para que sea superior a 30 °C. Por ejemplo, encienda una vela al lado del SensorTag.
1. Debería recibir una notificación de correo electrónico enviada por la aplicación lógica.

   > [!NOTE]
   > Es posible que el proveedor de servicios de correo electrónico necesite comprobar la identidad del remitente para asegurarse de que es usted quien envía el mensaje.

## <a name="next-steps"></a>Pasos siguientes

Ha creado correctamente una aplicación lógica que conecta IoT Hub y el buzón de correo para la supervisión de temperatura y las notificaciones.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
