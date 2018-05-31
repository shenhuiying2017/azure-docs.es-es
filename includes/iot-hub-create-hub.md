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
ms.openlocfilehash: 9a29406b92f7d2e2ce8171974efb5a264e112d1d
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/20/2018
ms.locfileid: "34371157"
---
1. Inicie sesión en [Azure Portal][lnk-portal].
1. Seleccione **Crear un recurso** > **Internet de las cosas** > **IoT Hub**.
   
    ![Captura de pantalla de la navegación en Azure Portal hacia IoT Hub][1]

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
<!-- Images -->
[1]: ./media/iot-hub-create-hub/create-iot-hub1.png
[2]: ./media/iot-hub-create-hub/create-iot-hub2.png
[3]: ./media/iot-hub-create-hub/create-iot-hub3.png
<!-- Links -->
[lnk-portal]: https://portal.azure.com/
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-resource-groups]: ../articles/azure-resource-manager/resource-group-portal.md