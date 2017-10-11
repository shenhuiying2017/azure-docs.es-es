---
title: "Pronóstico meteorológico mediante la utilización de Azure Machine Learning con datos de IoT Hub | Microsoft Docs"
description: "Use Azure Machine Learning para predecir la posibilidad de lluvia en función de los datos de temperatura y humedad que IoT Hub recopila de un sensor."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "Previsión meteorológica con Machine Learning"
ms.assetid: 8ba7d9e7-699c-4448-b353-0f3e1429d198
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/25/2017
ms.author: xshi
ms.openlocfilehash: 50ae54b9476c49b80236e295c0bf244df8236cff
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="weather-forecast-using-the-sensor-data-from-your-iot-hub-in-azure-machine-learning"></a>Pronóstico meteorológico con los datos del sensor de IoT Hub en Azure Machine Learning

![Diagrama integral](media/iot-hub-get-started-e2e-diagram/6.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

El aprendizaje automático es una técnica de ciencia de datos que ayuda a los equipos a aprender de los datos existentes para prever tendencias, resultados y comportamientos futuros. Azure Machine Learning es un servicio de análisis predictivo en la nube que permite crear e implementar rápidamente modelos predictivos como soluciones de análisis.

## <a name="what-you-learn"></a>Conocimientos que adquirirá

Obtenga información sobre cómo usar Azure Machine Learning para realizar pronósticos meteorológicos, como la posibilidad de lluvia, con los datos de temperatura y humedad de Azure IoT Hub. La posibilidad de lluvia es el resultado de un modelo de pronóstico meteorológico preparado. El modelo se basa en datos históricos para predecir la posibilidad de lluvia en función de la temperatura y la humedad.

## <a name="what-you-do"></a>Qué debe hacer

- Implementar el modelo de pronóstico meteorológico como un servicio web.
- Preparar el IoT Hub para el acceso a datos mediante la adición de un grupo de consumidores.
- Crear un trabajo de Stream Analytics y configurar el trabajo para:
  - Leer los datos de temperatura y humedad de IoT Hub.
  - Llamar al servicio web para saber la posibilidad de lluvia.
  - Guardar el resultado en Azure Blob Storage.
- Usar el Explorador de Microsoft Azure Storage para consultar el pronóstico meteorológico.

## <a name="what-you-need"></a>Lo que necesita

- Tutorial [Instalación de su dispositivo](iot-hub-raspberry-pi-kit-node-get-started.md) completado donde se abordan los siguientes requisitos:
  - Una suscripción de Azure activa.
  - Un centro de Azure IoT en su suscripción.
  - Una aplicación cliente que envía mensajes a su centro de Azure IoT.
- Una cuenta de Azure Machine Learning Studio. ([Pruebe Machine Learning Studio gratis](https://studio.azureml.net/)).

## <a name="deploy-the-weather-prediction-model-as-a-web-service"></a>Implementación del modelo de pronóstico meteorológico como un servicio web

1. Vaya a la [página del modelo de pronóstico meteorológico](https://gallery.cortanaintelligence.com/Experiment/Weather-prediction-model-1).
1. Haga clic en **Abrir en Studio** en Microsoft Azure Machine Learning Studio.
   ![Abrir la página del modelo de pronóstico meteorológico en la Galería de Cortana Intelligence](media/iot-hub-weather-forecast-machine-learning/2_weather-prediction-model-in-cortana-intelligence-gallery.png)
1. Haga clic en **Ejecutar** para validar los pasos del modelo. Este paso puede tardar 2 minutos en completarse.
   ![Abrir el modelo de pronóstico meteorológico en Azure Machine Learning Studio](media/iot-hub-weather-forecast-machine-learning/3_open-weather-prediction-model-in-azure-machine-learning-studio.png)
1. Haga clic en **CONFIGURAR SERVICIO WEB** > **Servicio web predictivo**.
   ![Implementar el modelo de pronóstico meteorológico en Azure Machine Learning Studio](media/iot-hub-weather-forecast-machine-learning/4-deploy-weather-prediction-model-in-azure-machine-learning-studio.png)
1. En el diagrama, arrastre el módulo **Entrada de servicio web** a algún lugar cerca del módulo **Puntuar modelo**.
1. Conecte el módulo **Entrada de servicio web** con el módulo **Puntuar modelo**.
   ![Conectar dos módulos en Azure Machine Learning Studio](media/iot-hub-weather-forecast-machine-learning/13_connect-modules-azure-machine-learning-studio.png)
1. Haga clic en **EJECUTAR** para validar los pasos del modelo.
1. Haga clic en **IMPLEMENTAR SERVICIO WEB** para implementar el modelo como un servicio web.
1. En el panel del modelo, descargue **Excel 2010 o el libro anterior** para **SOLICITUD/RESPUESTA**.

   > [!Note]
   > Asegúrese de descargar **Excel 2010 o el libro anterior** aunque ejecute la última versión de Excel en el equipo.

   ![Descargar Excel para el punto de conexión SOLICITUD/RESPUESTA](media/iot-hub-weather-forecast-machine-learning/5_download-endpoint-app-excel-for-request-response.png)

1. Abra el libro de Excel, tome nota de la **DIRECCIÓN URL DEL SERVICIO WEB** y de la **CLAVE DE ACCESO**.

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Creación, configuración y ejecución de un trabajo de Stream Analytics

### <a name="create-a-stream-analytics-job"></a>Creación de un trabajo de Stream Analytics

1. En [Azure Portal](https://ms.portal.azure.com/), haga clic en **Nuev** > **Internet de las cosas** > **Trabajo de Stream Analytics**.
1. Escriba la siguiente información para el trabajo.

   **Nombre del trabajo**: el nombre del trabajo. El nombre debe ser único globalmente.

   **Grupo de recursos**: use el mismo grupo de recursos que usa el IoT Hub.

   **Ubicación**: use la misma ubicación que el grupo de recursos.

   **Anclar al panel**: active esta opción para facilitar el acceso al IoT Hub desde el panel.

   ![Creación de un trabajo de Stream Analytics en Azure](media/iot-hub-weather-forecast-machine-learning/7_create-stream-analytics-job-azure.png)

1. Haga clic en **Crear**.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Adición de una entrada al trabajo de Stream Analytics

1. Abra el trabajo de Stream Analytics.
1. En **Topología de trabajo**, haga clic en **Entradas**.
1. En el panel **Entradas**, haga clic en **Agregar** y, a continuación, escriba la siguiente información:

   **Alias de entrada**: el alias único para la entrada.

   **Origen**: seleccione **IoT Hub**.

   **Grupo de consumidores**: seleccione el grupo de consumidores que ha creado.

   ![Adición de una entrada al trabajo de Stream Analytics en Azure](media/iot-hub-weather-forecast-machine-learning/8_add-input-stream-analytics-job-azure.png)

1. Haga clic en **Crear**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Adición de una salida al trabajo de Stream Analytics

1. En **Topología de trabajo**, haga clic en **Salidas**.
1. En el panel **Salidas**, haga clic en **Agregar** y, a continuación, escriba la siguiente información:

   **Alias de salida**: el alias único para la salida.

   **Receptor**: seleccione **Blob Storage**.

   **Cuenta de almacenamiento**: la cuenta de almacenamiento para Blob Storage. Puede crear una cuenta de almacenamiento o usar una existente.

   **Contenedor**: el contenedor donde se guarda el blob. Puede crear un contenedor o usar uno existente.

   **Formato de serialización de eventos**: seleccione **CSV**.

   ![Adición de una salida al trabajo de Stream Analytics en Azure](media/iot-hub-weather-forecast-machine-learning/9_add-output-stream-analytics-job-azure.png)

1. Haga clic en **Crear**.

### <a name="add-a-function-to-the-stream-analytics-job-to-call-the-web-service-you-deployed"></a>Adición de una función al trabajo de Stream Analytics para llamar al servicio web implementado

1. En **Topología de trabajo**, haga clic en **Funciones** > **Agregar**.
1. Escriba la siguiente información:

   **Alias de función**: escriba `machinelearning`.

   **Tipo de función**: seleccione **Azure ML**.

   **Opción de importación**: seleccione **Importar de una suscripción distinta**.

   **Dirección URL**: escriba la DIRECCIÓN URL DEL SERVICIO WEB que anotó del libro de Excel.

   **Clave**: escriba la CLAVE DE ACCESO que anotó del libro de Excel.

   ![Adición de una función al trabajo de Stream Analytics en Azure](media/iot-hub-weather-forecast-machine-learning/10_add-function-stream-analytics-job-azure.png)

1. Haga clic en **Crear**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Configuración de la consulta del trabajo de Stream Analytics

1. En **Topología de trabajo**, haga clic en **Consulta**.
1. Reemplace el código existente por el código siguiente:

   ```sql
   WITH machinelearning AS (
      SELECT EventEnqueuedUtcTime, temperature, humidity, machinelearning(temperature, humidity) as result from [YourInputAlias]
   )
   Select System.Timestamp time, CAST (result.[temperature] AS FLOAT) AS temperature, CAST (result.[humidity] AS FLOAT) AS humidity, CAST (result.[Scored Probabilities] AS FLOAT ) AS 'probabalities of rain'
   Into [YourOutputAlias]
   From machinelearning
   ```

   Reemplace `[YourInputAlias]` por el alias de entrada del trabajo.

   Reemplace `[YourOutputAlias]` por el alias de salida del trabajo.

1. Haga clic en **Guardar**.

### <a name="run-the-stream-analytics-job"></a>Ejecución del trabajo de Stream Analytics

En el trabajo de Stream Analytics, haga clic en **Iniciar** > **Ahora** > **Iniciar**. Una vez que el trabajo se inicia correctamente, su estado cambia de **Detenido** a **En ejecución**.

![Ejecución del trabajo de Stream Analytics](media/iot-hub-weather-forecast-machine-learning/11_run-stream-analytics-job-azure.png)

## <a name="use-microsoft-azure-storage-explorer-to-view-the-weather-forecast"></a>Uso del Explorador de Microsoft Azure Storage para consultar el pronóstico meteorológico

Ejecute la aplicación cliente para empezar a recopilar y enviar datos de temperatura y humedad a IoT Hub. Por cada mensaje que IoT Hub recibe, el trabajo de Stream Analytics llama al servicio web de pronóstico meteorológico para producir la posibilidad de lluvia. El resultado se guarda luego en Azure Blob Storage. El Explorador de Azure Storage es una herramienta que puede usar para consultar el resultado.

1. [Descargue e instale el Explorador de Microsoft Azure Storage](http://storageexplorer.com/).
1. Abra el Explorador de Azure Storage.
1. Inicie sesión en la cuenta de Azure.
1. Seleccione su suscripción.
1. Haga clic en la suscripción > **Cuentas de almacenamiento** > su cuenta de almacenamiento > **Contenedores de blob** > su contenedor.
1. Abra un archivo .csv para ver el resultado. La última columna registra la posibilidad de lluvia.

   ![Obtención del resultado del pronóstico meteorológico con Azure Machine Learning](media/iot-hub-weather-forecast-machine-learning/12_get-weather-forecast-result-azure-machine-learning.png)

## <a name="summary"></a>Resumen

Ha usado Azure Machine Learning correctamente para predecir la posibilidad de lluvia en función de los datos de temperatura y humedad que IoT Hub recibe.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]