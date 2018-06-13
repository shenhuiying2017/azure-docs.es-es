---
title: archivo de inclusión
description: archivo de inclusión
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 05/17/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: d586ca18953b12045fbbaa4a656d78a7192eb88e
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/08/2018
ms.locfileid: "34371242"
---
## <a name="create-an-iot-hub"></a>Crear un centro de IoT
Cree un Centro de IoT al que se pueda conectar la aplicación de dispositivo simulado. En los siguientes pasos se muestra cómo completar esta tarea mediante el Portal de Azure.

1. Inicie sesión en [Azure Portal][lnk-portal].

1. Seleccione **Crear un recurso** > **Internet de las cosas** > **IoT Hub**.
   
    ![Barra de accesos del Portal de Azure][1]

1. En el panel **Centro de IoT**, escriba la información necesaria para su centro de IoT:

   * **Suscripción**: elija la suscripción que quiere usar para crear este IoT Hub.

   * **Grupo de recursos**: cree un grupo de recursos para hospedar el centro de IoT o use uno existente. Para obtener más información, consulte [Uso de grupos de recursos para administrar recursos de Azure][lnk-resource-groups].

   * **Región**: seleccione la ubicación más cercana a usted.

   * **Nombre**: cree un nombre para su centro de IoT. Si el nombre que escribe está disponible, aparece una marca de verificación verde.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   ![Ventana Datos básicos de IoT Hub][2]

2. Seleccione **Next: Size and scale** (Siguiente: tamaño y escala) para seguir creando su IoT Hub. 

3. Seleccione su **Nivel de precios y de escala**. En este artículo, seleccione el nivel **F1 - Free** (F1: gratis) si aún está disponible en la suscripción. Para más información, consulte [Nivel de precios y de escala][lnk-pricing].

   ![Ventana Tamaño y escala de IoT Hub][3]

4. Seleccione **Revisar + crear**.

1. Revise la información de IoT Hub y, a continuación, haga clic en **Crear**. El centro de IoT puede tardar varios minutos en crearse. Puede ver el progreso en el panel **Notificaciones**.

1. Cuando la nueva instancia de IoT Hub esté lista, haga clic en su icono en Azure Portal para abrir su ventana de propiedades. Ahora que ha creado un centro de IoT, busque la información importante que usa para conectar a él dispositivos y aplicaciones. Haga clic en **Directivas de acceso compartido**.
   
1. En **Directivas de acceso compartido**, seleccione la directiva **iothubowner**. Copie el valor del campo **Connection string---primary key** (Cadena de conexión: clave principal) del centro de IoT para usarlo más adelante. Consulte [Control de acceso][lnk-access-control] en la Guía del desarrollador de Azure IoT Hub para más información.
   
    ![Directivas de acceso compartido][5]

<!-- Images. -->
[1]: ./media/iot-hub-get-started-create-hub/create-iot-hub1.png
[2]: ./media/iot-hub-get-started-create-hub/create-iot-hub2.png
[3]: ./media/iot-hub-get-started-create-hub/create-iot-hub3.png
[4]: ./media/iot-hub-get-started-create-hub/create-iot-hub4.png
[5]: ./media/iot-hub-get-started-create-hub/create-iot-hub5.png

<!-- Links -->
[lnk-access-control]: ../articles/iot-hub/iot-hub-devguide-security.md
[lnk-portal]: https://portal.azure.com/
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-resource-groups]: ../articles/azure-resource-manager/resource-group-portal.md
