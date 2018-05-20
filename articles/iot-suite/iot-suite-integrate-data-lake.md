---
title: Integración de la solución de supervisión remota con Azure Data Lake Store | Microsoft Docs
description: Obtenga información sobre cómo integrar la solución de supervisión remota con Azure Data Lake Store con un trabajo de Azure Stream Analytics.
+services: ''
+suite: iot-suite
+author: philmea
+manager: timlt
+ms.author: philmea
+ms.date: 04/029/2018
+ms.topic: article
+ms.service: iot-suite
ms.openlocfilehash: 701dd51d13ec1880f23c48cc5e8b368b15d0deca
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="integrate-the-remote-monitoring-solution-with-azure-data-lake-store"></a>Integración de la solución de supervisión remota con Azure Data Lake Store

Puede haber requisitos de análisis avanzados aparte de los que se ofrecen en la solución de supervisión remota. Azure Data Lake Store es ideal para esta aplicación, ya que puede almacenar los datos de conjuntos de datos masivos y diversos, así como integrarse con Azure Data Lake Analytics para proporcionar análisis a petición.

En este tema de procedimientos, usará un trabajo de Azure Stream Analytics para transmitir datos desde el centro de IoT de la solución de supervisión remota a una instancia de Azure Data Lake Store.

## <a name="prerequisites"></a>requisitos previos

Para completar este tema de procedimientos, necesitará lo siguiente:

* [Implementación de la solución preconfigurada de supervisión remota](/iot-suite/iot-suite-remote-monitoring-deploy).
  * La solución de supervisión remota implementará el centro de IoT y el trabajo de Azure Stream Analytics utilizados en este artículo en su suscripción de Azure.
* [Implementación de una instancia de Azure Data Lake Store](/data-lake-store/data-lake-store-get-started-portal)
  * La instancia de Data Lake Store debe implementarse en la misma región que la solución de supervisión remota.
  * [Cree una carpeta](/data-lake-store/data-lake-store-get-started-portal#createfolder) denominada "streaming" en su cuenta.

## <a name="create-a-consumer-group"></a>Creación de un grupo de consumidores

Cree un grupo de consumidores dedicado en el centro de IoT de la solución de supervisión remota. El trabajo de Stream Analytics lo usará para el streaming de datos a la instancia de Data Lake Store.

> [!NOTE]
> Las aplicaciones usan grupos de consumidores para extraer datos de Azure IoT Hub. Debe crear un grupo de consumidores para cada cinco consumidores de salida. Puede crear hasta treinta y dos grupos de consumidores.

1. Inicie sesión en el Portal de Azure.

1. En Azure Portal, haga clic en el botón **Cloud Shell**.

    ![Icono de inicio del portal](media/iot-suite-integrate-data-lake/portal-launch-icon.png)

1. Ejecute este comando para crear un grupo de consumidores:

```azurecli-interactive
az iot hub consumer-group create --hub-name contoso-rm30263 --name streamanalyticsjob --resource-group contoso-rm
```

> [!NOTE]
> Use los nombres del centro de IoT y del grupo de recursos de la solución de supervisión remota.

## <a name="create-stream-analytics-job"></a>Creación de un trabajo de Stream Analytics

Cree un trabajo de Azure Stream Analytics para transmitir los datos desde el centro de IoT a la instancia de Azure Data Lake Store.

1. Haga clic en **Crear un recurso**, seleccione Internet de las cosas en Marketplace y haga clic en **Trabajo de Stream Analytics**.

    ![Nuevo trabajo de Stream Analytics](media/iot-suite-integrate-data-lake/new-stream-analytics-job.png)

1. Escriba un nombre de trabajo y seleccione la suscripción y el grupo de recursos apropiados.

1. Seleccione una ubicación en o casi en la misma región en que se encuentra la instancia de Data Lake Store. Aquí usamos Este de EE. UU.

1. Asegúrese de dejar el entorno de hospedaje como valor predeterminado para la **nube**.

1. Haga clic en **Create**(Crear).

    ![Creación de un trabajo de Stream Analytics](media/iot-suite-integrate-data-lake/create-stream-analytics-job.png)

## <a name="configure-the-stream-analytics-job"></a>Configuración del trabajo de Stream Analytics

1. Vaya al **trabajo de Stream Analytics** del grupo de recursos de la solución de supervisión remota.

1. En la página de información general, haga clic en **Entradas**.

    ![Página de información general](media/iot-suite-integrate-data-lake/stream-analytics-overview.png)

1. Haga clic en **Agregar entrada de flujo** y seleccione **IoT Hub** en la lista desplegable.

    ![Adición de entradas](media/iot-suite-integrate-data-lake/stream-analytics-add-input.png)

1. En la pestaña Nueva entrada, escriba un alias de entrada de **IoTHub**.

1. En la lista desplegable Grupo de consumidores, seleccione el grupo de consumidores creado anteriormente. En este caso, usamos **streamanalyticsjob**.

    ![Selección de entradas](media/iot-suite-integrate-data-lake/stream-analytics-new-input.png)

1. Haga clic en **Save**(Guardar).

1. En la página de información general, haga clic en **Salidas**.

    ![Adición de instancias de Data Lake Store](media/iot-suite-integrate-data-lake/stream-analytics-overview-2.png)

1. Haga clic en **Agregar** y seleccione **Data Lake Store** en la lista desplegable.

    ![Adición de salidas](media/iot-suite-integrate-data-lake/stream-analytics-output.png)

1. En la pestaña Nueva salida, escriba un alias de salida de **DataLakeStore**.

1. Seleccione la cuenta de Data Lake Store creada en los pasos anteriores y proporcione la estructura de carpetas para transmitir datos al almacén.

1. En el campo de formato de fecha, escriba **/streaming/{fecha}/{hora}**. Deje el formato de fecha predeterminado de AAAA/MM/DD y el formato de hora hh.

    ![Definición de la estructura de carpetas](media/iot-suite-integrate-data-lake/stream-analytics-new-output.png)

1. Haga clic en **Autorizar**.

    Tendrá que autorizar a Data Lake Store para conceder al trabajo de Stream Analytics acceso de escritura al sistema de archivos.

    ![Autorización de Stream Analytics para Data Lake Store](media/iot-suite-integrate-data-lake/stream-analytics-out-authorize.png)

    Verá un menú emergente y, una vez que este se cierre, el botón Autorizar se atenúa tras haberse completado la autorización.

    > [!NOTE]
    > Si ve un error en la ventana emergente, abra una nueva ventana del explorador en modo de incógnito y vuelva a intentarlo.

1. Haga clic en **Save**(Guardar).

## <a name="edit-the-stream-analytics-query"></a>Edición de consultas de Stream Analytics

Azure Stream Analytics usa un lenguaje de consulta similar a SQL para especificar un origen de entrada que transmite los datos, los transforma según sea necesario y genera su salida en distintos destinos de almacenamiento o procesamiento.

1. En la pestaña de información general, haga clic en **Editar consulta**.

    ![Editar consulta](media/iot-suite-integrate-data-lake/stream-analytics-edit-query.png)

1. En el editor de consultas, reemplace los marcadores de posición [YourOutputAlias] y [YourInputAlias] por los valores definidos anteriormente.

    ```sql
    SELECT
        *, System.Timestamp as time
    INTO
        DataLakeStore
    FROM
        IoTHub
    ```

    ![Consulta de Stream Analytics](media/iot-suite-integrate-data-lake/stream-analytics-query.png)

1. Haga clic en **Save**(Guardar).
1. Haga clic en **Sí** para aceptar los cambios.

## <a name="start-the-stream-analytics-job"></a>Inicio del trabajo de Stream Analytics

1. En la pestaña de información general, haga clic en **Iniciar**.

    ![Inicia el trabajo de Stream Analytics](media/iot-suite-integrate-data-lake/stream-analytics-start.png)

1. En la pestaña Iniciar trabajo, haga clic en **Personalizar**.

1. Determine el tiempo personalizado para volver atrás algunas horas, a fin de seleccionar datos desde cuando el dispositivo comenzó el streaming.

1. Haga clic en **Iniciar**.

    ![Selección de fecha personalizada](media/iot-suite-integrate-data-lake/stream-analytics-start-custom.png)

    Espere hasta que el trabajo entre en el estado de ejecución, si ve errores que podrían derivar de su consulta, asegúrese de verificar que la sintaxis sea correcta.

    ![Trabajo en ejecución](media/iot-suite-integrate-data-lake/stream-analytics-running.png)

    El trabajo de streaming empezará a leer datos de IoT Hub y los almacenará en Data Lake Store. Los datos pueden tardar varios minutos en aparecer en Data Lake Store.

## <a name="explore-the-streaming-data"></a>Exploración de los datos de streaming

1. Vaya a la instancia de Data Lake Store.

1. En la pestaña de información general, haga clic en **Explorador de datos**.

1. En el Explorador de datos, explore en profundidad la carpeta **/streaming**. Verá las carpetas creadas con el formato AAAA/MM/DD/HH.

    ![Exploración de los datos de streaming](media/iot-suite-integrate-data-lake/data-lake-store-data-explorer.png)

    Verá los archivos JSON con un archivo por hora.

    ![Exploración de los datos de streaming](media/iot-suite-integrate-data-lake/data-lake-store-file-preview.png)

## <a name="next-steps"></a>Pasos siguientes

Se puede usar Azure Data Lake Analytics para realizar análisis de macrodatos de los conjuntos de datos de Data Lake Store. Obtenga más información sobre la [documentación de Data Lake Analytics](https://docs.microsoft.com/en-us/azure/data-lake-analytics).
