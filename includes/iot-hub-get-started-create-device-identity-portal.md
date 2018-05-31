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
ms.openlocfilehash: 1df3e188b71b8fa2d5223bad8bc5914513e26286
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/20/2018
ms.locfileid: "34371208"
---
## <a name="create-a-device-identity"></a>Creación de una identidad de dispositivo

En esta sección, usará [Azure Portal][lnk-azure-portal] para crear una identidad de dispositivo en el registro de identidades de su instancia de IoT Hub. No se puede conectar un dispositivo a IoT Hub a menos que tenga una entrada en el registro de identidades. Para más información, consulte la sección sobre el registro de la identidad de la [guía para desarrolladores de IoT Hub][lnk-devguide-identity]. Use el panel **Dispositivos IoT** del portal para generar un identificador de dispositivo único y una clave para que la use el dispositivo para identificarse en IoT Hub. Los identificadores de dispositivos distinguen mayúsculas de minúsculas.

1. Inicie sesión en [Azure Portal][lnk-azure-portal].

1. Seleccione **Todos los recursos** y busque su recurso de IoT Hub.

1. Cuando se abra el recurso de IoT Hub, haga clic en la herramienta **Dispositivos IoT** y, a continuación, haga clic en **Agregar** en la parte superior. 

    ![Creación de la identidad del dispositivo en el portal][img-add-device]

1. Proporcione un nombre para el dispositivo nuevo, como **myDeviceId**, y haga clic en **Guardar**. Esta acción crea una nueva identidad de dispositivo para su instancia de IoT Hub.

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

   ![Agregar un nuevo dispositivo][img-create-device]

1. En la lista de dispositivos, haga clic en el dispositivo recién creado y copie el valor de **Cadena de conexión: clave principal** para usarlo más adelante.

    ![Cadena de conexión de dispositivo][img-connection-string]

> [!NOTE]
> El registro de identidades de IoT Hub solo almacena identidades de dispositivos para permitir el acceso seguro al centro de IoT. Almacena las claves y los identificadores de dispositivo para usarlos como credenciales de seguridad, y un indicador de habilitado o deshabilitado que permite deshabilitar el acceso a un dispositivo individual. Si la aplicación necesita almacenar otros metadatos específicos del dispositivo, debe usar un almacén específico de la aplicación. Consulte la [guía para desarrolladores de IoT Hub][lnk-devguide-identity] para más información.

<!-- Images. -->
[img-find-iothub]: ./media/iot-hub-get-started-create-device-identity-portal/find-iothub.png
[img-add-device]: ./media/iot-hub-get-started-create-device-identity-portal/create-identity-portal.png
[img-connection-string]: ./media/iot-hub-get-started-create-device-identity-portal/device-connection-string.png
[img-create-device]:./media/iot-hub-get-started-create-device-identity-portal/add-device.png

<!-- Links -->
[lnk-azure-portal]: https://portal.azure.com
[lnk-devguide-identity]: ../articles/iot-hub/iot-hub-devguide-identity-registry.md

