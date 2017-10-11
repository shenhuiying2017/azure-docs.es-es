---
title: "Visualización de información en tiempo real de los datos del sensor desde Azure IoT Hub – Power BI | Microsoft Docs"
description: "Use Power BI para visualizar datos de temperatura y humedad que se recopilan desde el sensor y se le envían a Azure IoT Hub."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "visualización de datos en tiempo real, visualización de datos en directo, visualización de datos de sensor"
ms.assetid: e67c9c09-6219-4f0f-ad42-58edaaa74f61
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: xshi
ms.openlocfilehash: b190fea06ffc2406d781c7edad091f097cca9c2d
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="visualize-real-time-sensor-data-from-azure-iot-hub-using-power-bi"></a>Visualización de datos del sensor en tiempo real desde Azure IoT Hub mediante Power BI

![Diagrama integral](media/iot-hub-get-started-e2e-diagram/4.png)


[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>Conocimientos que adquirirá

Aprenderá a visualizar los datos del sensor en tiempo real que recibe el centro de Azure IoT recibe mediante Power BI. Si desea intentar visualizar los datos en IoT Hub con Web Apps, consulte [Use Azure Web Apps to visualize real-time sensor data from Azure IoT Hub](iot-hub-live-data-visualization-in-web-apps.md) (Uso de Azure Web Apps para visualizar datos del sensor en tiempo real desde Azure IoT Hub).

## <a name="what-you-do"></a>Qué debe hacer

- Prepare el IoT Hub para el acceso a datos mediante la adición de un grupo de consumidores.
- Cree, configure y ejecute un trabajo de Stream Analytics para la transferencia de datos desde su IoT Hub a su cuenta de Power BI.
- Cree y publique un informe de Power BI para visualizar los datos.

## <a name="what-you-need"></a>Lo que necesita

- Tutorial [Instalación de su dispositivo](iot-hub-raspberry-pi-kit-node-get-started.md) completado donde se abordan los siguientes requisitos:
  - Una suscripción de Azure activa.
  - Un centro de Azure IoT en su suscripción.
  - Una aplicación cliente que envía mensajes a su centro de Azure IoT.
- Una cuenta de Power BI ([pruebe Power BI de manera gratuita](https://powerbi.microsoft.com/)).

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Creación, configuración y ejecución de un trabajo de Stream Analytics

### <a name="create-a-stream-analytics-job"></a>Creación de un trabajo de Análisis de transmisiones

1. En Azure Portal, haga clic en Nuevo > Internet de las cosas > Trabajo de Stream Analytics.
1. Escriba la siguiente información para el trabajo.

   **Nombre del trabajo**: el nombre del trabajo. El nombre debe ser único globalmente.

   **Grupo de recursos**: use el mismo grupo de recursos que usa el IoT Hub.

   **Ubicación**: use la misma ubicación que el grupo de recursos.

   **Anclar al panel**: active esta opción para facilitar el acceso al IoT Hub desde el panel.

   ![Creación de un trabajo de Stream Analytics en Azure](media/iot-hub-live-data-visualization-in-power-bi/2_create-stream-analytics-job-azure.png)

1. Haga clic en **Crear**.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Adición de una entrada al trabajo de Stream Analytics

1. Abra el trabajo de Stream Analytics.
1. En **Topología de trabajo**, haga clic en **Entradas**.
1. En el panel **Entradas**, haga clic en **Agregar** y, a continuación, escriba la siguiente información:

   **Alias de entrada**: el alias único para la entrada.

   **Origen**: seleccione **IoT Hub**.

   **Grupo de consumidores**: seleccione el grupo de consumidores que acaba de crear.
1. Haga clic en **Crear**.

   ![Adición de una entrada a un trabajo de Stream Analytics en Azure](media/iot-hub-live-data-visualization-in-power-bi/3_add-input-to-stream-analytics-job-azure.png)

### <a name="add-an-output-to-the-stream-analytics-job"></a>Adición de una salida al trabajo de Stream Analytics

1. En **Topología de trabajo**, haga clic en **Salidas**.
1. En el panel **Salidas**, haga clic en **Agregar** y, a continuación, escriba la siguiente información:

   **Alias de salida**: el alias único para la salida.

   **Receptor**: seleccione **Power BI**.
1. Haga clic en **Autorizar** y, a continuación, inicie sesión en su cuenta de Power BI.
1. Una vez autorizado, escriba la siguiente información:

   **Área de trabajo de grupo**: seleccione el área de trabajo de grupo de destino.

   **Nombre del conjunto de datos**: escriba un nombre para el conjunto de datos.

   **Nombre de la tabla**: escriba un nombre para la tabla.
1. Haga clic en **Crear**.

   ![Adición de una salida a un trabajo de Stream Analytics en Azure](media/iot-hub-live-data-visualization-in-power-bi/4_add-output-to-stream-analytics-job-azure.png)

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Configuración de la consulta del trabajo de Stream Analytics

1. En **Topología de trabajo**, haga clic en **Consulta**.
1. Reemplace `[YourInputAlias]` por el alias de entrada del trabajo.
1. Reemplace `[YourOutputAlias]` por el alias de salida del trabajo.
1. Haga clic en **Guardar**.

   ![Adición de una consulta a un trabajo de Stream Analytics en Azure](media/iot-hub-live-data-visualization-in-power-bi/5_add-query-stream-analytics-job-azure.png)

### <a name="run-the-stream-analytics-job"></a>Ejecución del trabajo de Análisis de transmisiones

En el trabajo de Stream Analytics, haga clic en **Iniciar** > **Ahora** > **Iniciar**. Una vez que el trabajo se inicia correctamente, su estado cambia de **Detenido** a **En ejecución**.

![Ejecución de un trabajo de Stream Analytics en Azure](media/iot-hub-live-data-visualization-in-power-bi/6_run-stream-analytics-job-azure.png)

## <a name="create-and-publish-a-power-bi-report-to-visualize-the-data"></a>Creación y publicación de un informe de Power BI para visualizar los datos

1. Asegúrese de que la aplicación de ejemplo se ejecuta en el dispositivo. Si no es así, puede hacer referencia a los tutoriales en [Configurar su dispositivo](https://docs.microsoft.com/azure/iot-hub/iot-hub-raspberry-pi-kit-node-get-started).
1. Inicie sesión en su cuenta de [Power BI](https://powerbi.microsoft.com/en-us/).
1. Vaya al área de trabajo de grupo que estableció en el momento de crear la salida para el trabajo de Stream Analytics.
1. Haga clic en **Conjuntos de datos de streaming**.

   Debería ver en la lista el conjunto de datos que especificó en el momento de crear la salida para el trabajo de Stream Analytics.
1. En **ACCIONES**, haga clic en el primer icono para crear un informe.

   ![Creación de informe de Microsoft Power BI](media/iot-hub-live-data-visualization-in-power-bi/7_create-power-bi-report-microsoft.png)

1. Cree un gráfico de líneas para mostrar la temperatura en tiempo real en un período determinado.
   1. En la página de creación de informes, agregue un gráfico de líneas.
   1. En el panel **Campos**, expanda la tabla que especificó en el momento de crear la salida para el trabajo de Stream Analytics.
   1. Arrastre **EventEnqueuedUtcTime** (Hora UTC de evento en cola) al **Eje** en el panel **Visualizaciones**.
   1. Arrastre **temperature** (temperatura) a **Valores**.

      Ya se ha creado el gráfico de líneas. El eje x muestra la fecha y hora en la zona horaria UTC. El eje y muestra la temperatura del sensor.

      ![Adición de un gráfico de líneas de temperatura a un informe de Microsoft Power BI](media/iot-hub-live-data-visualization-in-power-bi/8_add-line-chart-for-temperature-to-power-bi-report-microsoft.png)

1. Cree otro gráfico de líneas para mostrar la humedad en tiempo real en un período determinado. Para ello, siga los mismos pasos anteriores y coloque **EventEnqueuedUtcTime** (Hora UTC de evento en cola) en el eje x y **humidity** (humedad) en el eje y.

   ![Adición de un gráfico de líneas de humedad a un informe de Microsoft Power BI](media/iot-hub-live-data-visualization-in-power-bi/9_add-line-chart-for-humidity-to-power-bi-report-microsoft.png)

1. Haga clic en **Guardar** para guardar el informe.
1. Haga clic en **Archivo** > **Publicar en Web**.
1. Haga clic en **Crear código para insertar** y, a continuación, haga clic en **Publicar**.

Se le ofrecerá el vínculo del informe, que puede compartir con cualquiera para concederle acceso a este, y un fragmento de código para que pueda integrarlo en su blog o sitio web.

![Publicación de informe de Microsoft Power BI](media/iot-hub-live-data-visualization-in-power-bi/10_publish-power-bi-report-microsoft.png)

Microsoft también ofrece las [aplicaciones móviles de Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-power-bi-apps-for-mobile-devices/) para ver e interactuar con los informes y paneles de Power BI desde su dispositivo móvil.

## <a name="next-steps"></a>Pasos siguientes

Ha utilizado correctamente Power BI para visualizar datos de sensor en tiempo real desde su centro de Azure IoT.
Hay otra manera de visualizar datos desde Azure IoT Hub. Consulte [Use Azure Web Apps to visualize real-time sensor data from Azure IoT Hub](iot-hub-live-data-visualization-in-web-apps.md) (Uso de Azure Web Apps para visualizar datos del sensor en tiempo real desde Azure IoT Hub).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
