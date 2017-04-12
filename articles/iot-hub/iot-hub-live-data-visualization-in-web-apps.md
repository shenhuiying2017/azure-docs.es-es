---
title: "Visualización de datos del sensor en tiempo real desde Azure IoT Hub (Web Apps) | Microsoft Docs"
description: "Use Azure Web Apps para visualizar datos de temperatura y humedad que se recopilan desde el sensor y se envían a su centro de IoT de Azure."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "visualización de datos en tiempo real, visualización de datos en directo, visualización de datos de sensor"
ms.assetid: e42b07a8-ddd4-476e-9bfb-903d6b033e91
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/29/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 1d1eec1457917baef1acab27f7c60bdb048837a9
ms.lasthandoff: 04/12/2017


---
# <a name="visualize-real-time-sensor-data-from-azure-iot-hub-using-azure-web-apps"></a>Visualización de datos del sensor en tiempo real desde Azure IoT Hub mediante Azure Web Apps

![Conexión entre el sensor, el dispositivo de IoT, IoT Hub y la aplicación web de Azure](media/iot-hub-live-data-visualization-in-web-apps/1_sensor-iot-device-azure-iot-hub-web-app-connection.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>Conocimientos que adquirirá

En esta lección, aprenderá a visualizar datos del sensor en tiempo real que recibe su centro de IoT de Azure mediante la ejecución de una aplicación web hospedada en una aplicación web de Azure. Si quiere intentar visualizar los datos en su centro de IoT con Power BI, consulte [Uso de Power BI para visualizar datos del sensor en tiempo real desde Azure IoT Hub](iot-hub-live-data-visualization-in-power-bi.md).

## <a name="what-you-do"></a>Qué debe hacer

- Crear una aplicación web en Azure Portal.
- Preparar el centro de IoT para el acceso a datos mediante la adición de un grupo de consumidores.
- Configurar la aplicación web para leer datos del sensor desde su centro de IoT.
- Cargar una aplicación web para que se hospede en la aplicación web.
- Abrir la aplicación web para ver datos de temperatura y humedad en tiempo real desde su centro de IoT.

## <a name="what-you-need"></a>Lo que necesita

- Tiene que haber completado el tutorial para [conectar ESP8266 a Azure IoT Hub](iot-hub-arduino-huzzah-esp8266-get-started.md), que engloba los siguientes requisitos:
  - Una suscripción de Azure activa.
  - Un centro de Azure IoT en su suscripción.
  - Una aplicación cliente que envía mensajes a su centro de Azure IoT.
- Git. ([Descargar Git](https://www.git-scm.com/downloads)).

## <a name="create-an-azure-web-app"></a>Creación de una aplicación web de Azure

1. En el [portal de Azure](https://ms.portal.azure.com/), haga clic en **Nuevo** > **Web y móvil** > **Aplicación web**.
1. Escriba un nombre de trabajo único, compruebe la suscripción, especifique un grupo de recursos y una ubicación, seleccione **Anclar al panel** y luego haga clic en **Crear**.

   Se recomienda seleccionar la misma ubicación donde se encuentra el grupo de recursos. Esto ayuda a acelerar el procesamiento y a reducir los costos de la transferencia de datos.

   ![Creación de una aplicación web de Azure](media/iot-hub-live-data-visualization-in-web-apps/2_create-web-app-azure.png)

## <a name="add-a-consumer-group-to-your-iot-hub"></a>Adición de un grupo de consumidores a IoT Hub

Las aplicaciones usan grupos de consumidores para extraer datos de Azure IoT Hub. En esta lección, creará un grupo de consumidores que se usará en una aplicación web para leer datos de su centro de IoT.

Para agregar un grupo de consumidores a su centro de IoT, siga estos pasos:

1. En el [portal de Azure](https://ms.portal.azure.com/), abra su centro de IoT.
1. Haga clic en **Puntos de conexión** en el panel izquierdo, seleccione **Eventos** en el panel central, escriba un nombre en **Grupos de consumidores** en el panel derecho y, a continuación, haga clic en **Guardar**.

   ![Creación de un grupo de consumidores en Azure IoT Hub](media/iot-hub-live-data-visualization-in-web-apps/3_add-consumer-group-iot-hub-azure.png)

## <a name="configure-the-web-app-to-read-data-from-your-iot-hub"></a>Configuración de la aplicación web para leer datos del centro de IoT

1. Abra la aplicación web que acaba de aprovisionar.
1. Haga clic en **Application settings** (Configuración de la aplicación) y luego agregue los siguientes pares clave-valor en **App settings** (Configuración de la aplicación):

   | Clave                                   | Valor                                                        |
   |---------------------------------------|--------------------------------------------------------------|
   | Azure.IoT.IoTHub.ConnectionString     | Obtenido del Explorador de IoT Hub.                               |
   | Azure.IoT.IoTHub.DeviceId             | Obtenido del Explorador de IoT Hub.                               |
   | Azure.IoT.IoTHub.ConsumerGroup        | El nombre del grupo de consumidores que agrega a su centro de IoT.  |

   ![Agregar la configuración a una aplicación web de Azure con pares clave-valor](media/iot-hub-live-data-visualization-in-web-apps/4_web-app-settings-key-value-azure.png)

## <a name="upload-a-web-application-to-be-hosted-by-the-web-app"></a>Carga de una aplicación web para que se hospede en la aplicación web

Hemos facilitado el acceso a una aplicación web en GitHub que muestra datos del sensor en tiempo real desde su centro de IoT. Todo lo que tiene que hacer es configurar la aplicación web para que funcione con un repositorio de Git, descargarla de GitHub y cargarla en Azure para que la hospede la aplicación web.

1. En la aplicación web, haga clic en **Opciones de implementación** > **Elegir origen** > **Repositorio de Git local**.

   ![Configuración de la implementación de aplicaciones web de Azure para usar el repositorio de Git local](media/iot-hub-live-data-visualization-in-web-apps/5_configure-web-app-deployment-local-git-repository-azure.png)

1. Haga clic en **Configurar conexión**, cree un nombre de usuario y una contraseña que se usarán para conectarse al repositorio de Git de Azure y luego haga clic en **Aceptar**.

   ![Configuración del nombre de usuario y la contraseña del repositorio de Git en Azure para la aplicación web](media/iot-hub-live-data-visualization-in-web-apps/6_web-app-set-user-password-git-repo-azure.png)

1. Haga clic en **Aceptar** para finalizar la configuración.
1. Haga clic en **Overview** (Información general) y anote el valor de la **dirección URL de clonación de Git**.

   ![Obtener la dirección URL de clonación de Git de la aplicación web de Azure](media/iot-hub-live-data-visualization-in-web-apps/7_web-app-git-clone-url-azure.png)

1. Abra una ventana de comando o de terminal en el equipo local.
1. Descargue la aplicación web de GitHub y cárguela en Azure para que se hospede en la aplicación web. Para ello, ejecute los siguientes comandos:

   ```bash
   git clone https://github.com/Azure-Samples/web-apps-node-iot-hub-data-visualization.git
   git remote add webapp <Git clone URL>
   git push webapp master:master
   ```

   > [!Note]
   > La \<dirección URL de clonación de GIT\> es la dirección URL del repositorio de Git que se encuentra en la página **Overview** (Información general) de la aplicación web.

## <a name="open-the-web-app-to-see-real-time-temperature-and-humidity-data-from-your-iot-hub"></a>Abra la aplicación web para ver los datos de temperatura y humedad en tiempo real desde su centro de IoT.

En la página **Overview** (Información general) de la aplicación web, haga clic en la dirección URL para abrir la aplicación web.

![Obtención de la dirección URL de la aplicación web de Azure](media/iot-hub-live-data-visualization-in-web-apps/8_web-app-url-azure.png)

Verá los datos de temperatura y humedad en tiempo real desde su centro de IoT.

![Página de aplicación web de Azure que muestra la temperatura y humedad en tiempo real](media/iot-hub-live-data-visualization-in-web-apps/9_web-app-page-show-real-time-temperature-humidity-azure.png)

## <a name="next-steps"></a>Pasos siguientes
Ha usado correctamente una aplicación web de Azure para visualizar datos del sensor en tiempo real desde su centro de Azure IoT.

Hay otra manera de visualizar datos desde Azure IoT Hub. Consulte [Use Power BI to visualize real-time sensor data from Azure IoT Hub](iot-hub-live-data-visualization-in-power-bi.md) (Uso de Power BI para visualizar datos de sensor en tiempo real desde Azure IoT Hub).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
