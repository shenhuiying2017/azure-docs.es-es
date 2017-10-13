---
title: Panel de Power BI en Azure Stream Analytics | Microsoft Docs
description: "Utilice un panel de Power BI de streaming en tiempo real para reunir información de inteligencia empresarial y analizar grandes volúmenes de datos procedentes de un trabajo de Análisis de transmisiones."
keywords: "panel de análisis, panel en tiempo real"
services: stream-analytics
documentationcenter: 
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: fe8db732-4397-4e58-9313-fec9537aa2ad
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 06/27/2017
ms.author: samacha
ms.openlocfilehash: b446e2296f2747012849936b994c4a4a2044869e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="stream-analytics-and-power-bi-a-real-time-analytics-dashboard-for-streaming-data"></a>Stream Analytics y Power BI: panel de análisis en tiempo real de flujo de datos
Azure Stream Analytics permite aprovechar una de las principales herramientas de inteligencia empresarial, [Microsoft Power BI](https://powerbi.com/). En este artículo, aprenderá a crear herramientas de inteligencia empresarial personalizadas utilizando Power BI como salida para los trabajos de Azure Stream Analytics. También aprenderá a crear y usar un panel en tiempo real.

Este artículo continúa a partir del tutorial [Detección de fraudes en tiempo real](stream-analytics-real-time-fraud-detection.md) de Stream Analytics. Se basa en el flujo de trabajo creado en ese tutorial y agrega una salida de Power BI para que se puedan visualizar llamadas telefónicas fraudulentas detectadas por un trabajo de Streaming Analytics. 

Puede ver un [vídeo](https://www.youtube.com/watch?v=SGUpT-a99MA) que muestra este escenario.


## <a name="prerequisites"></a>Requisitos previos

Antes de empezar, asegúrese de que dispone de lo siguiente:

* Una cuenta de Azure.
* Una cuenta de Power BI. Puede usar una cuenta profesional o una cuenta educativa.
* Versión completa del tutorial [Detección de fraudes en tiempo real](stream-analytics-real-time-fraud-detection.md). El tutorial incluye una aplicación que genera metadatos de llamada telefónica ficticia. En el tutorial, se crea un centro de eventos y se envían los datos de llamada telefónica de streaming al centro de eventos. Escriba una consulta que detecte llamadas fraudulentas (llamadas del mismo número a la vez en distintas ubicaciones). 


## <a name="add-power-bi-output"></a>Incorporación del resultado de Power BI
En el tutorial de detección de fraudes en tiempo real, la salida se envía a Azure Blob Storage. En esta sección se agrega una salida que envía información a Power BI.

1. En Azure Portal, abra la cuenta de Stream Analytics que creó antes. Si usó el nombre sugerido, el trabajo se llama `sa_frauddetection_job_demo`.

2. Active la casilla **Salidas** en el centro del panel de trabajo y seleccione **+ Agregar**.

3. Como **Alias de salida**, escriba `CallStream-PowerBI`. Puede usar otro nombre. Si lo hace, tome nota del mismo, porque más adelante se necesita el nombre. 

4. En **Receptor**, seleccione **Power BI**.

   ![Creación de una salida para Power BI](./media/stream-analytics-power-bi-dashboard/create-pbi-ouptut.png)

5. Haga clic en **Autorizar**.

    Se abrirá una ventana donde puede especificar sus credenciales de Azure para una cuenta profesional o educativa. 

    ![Especificación de credenciales de acceso a Power BI](./media/stream-analytics-power-bi-dashboard/authorize-area.png)

6. Escriba sus credenciales. Luego, cuando escriba sus credenciales, tenga en cuenta que también le da permiso al trabajo de Stream Analytics para acceder a su área de Power BI.

7. Cuando vuelva a la hoja **Nueva salida**, escriba la siguiente información:

    * **Área de trabajo de grupo**: seleccione el área de trabajo en el inquilino de Power BI en donde quiere crear el conjunto de datos.
    * **Nombre del conjunto de datos**: escriba `sa-dataset`. Puede usar otro nombre. Si lo hace, apúntelo para recordarlo más tarde.
    * **Nombre de la tabla**: escriba `fraudulent-calls`. Actualmente, la salida de Power BI de trabajos de Stream Analytics solo puede tener una tabla en un conjunto de datos.

    ![Área de trabajo de PBI](./media/stream-analytics-power-bi-dashboard/create-pbi-ouptut-with-dataset-table.png)

    > [!WARNING]
    > Si Power BI tiene un conjunto de datos y una tabla con los mismos nombres que especifica en el trabajo de Stream Analytics, se sobrescribirán los existentes.
    > Se recomienda no crear explícitamente este conjunto de datos y la tabla en la cuenta de Power BI. Se crearán automáticamente cuando inicie el trabajo de Stream Analytics y este empiece a enviar salida a Power BI. Si la consulta de trabajo no genera ningún resultado, el conjunto de datos y la tabla no se crean.
    >

8. Haga clic en **Crear**.

El conjunto de datos se crea con la siguiente configuración:

* **defaultRetentionPolicy: BasicFIFO**: los datos tienen un orden FIFO, con un máximo de 200 000 filas.
* **defaultMode: pushStreaming**: el conjunto de datos admite iconos de streaming y objetos visuales tradicionales basados en informes (esto también se denomina inserción). inserción).

Actualmente, no se pueden crear conjuntos de datos con otras marcas.

Para más información sobre conjuntos de datos de Power BI, consulte la referencia de la [API de REST de Power BI](https://msdn.microsoft.com/library/mt203562.aspx).


## <a name="write-the-query"></a>Escritura de la consulta

1. Cierre la hoja **Salidas** y vuelva a la hoja del trabajo.

2. Haga clic en el cuadro **Consulta**. 

3. Escriba la siguiente consulta. Esta consulta es similar a la consulta de autocombinación que creó en el tutorial de detección de fraudes. La diferencia es que esta consulta envía los resultados a la nueva salida generada (`CallStream-PowerBI`). 

    >[!NOTE]
    >Si no asignó el nombre `CallStream` a la entrada en el tutorial de detección de fraudes, sustituya el nombre por `CallStream` en las cláusulas **FROM** y **JOIN** de la consulta.

        /* Our criteria for fraud:
        Calls made from the same caller to two phone switches in different locations (for example, Australia and Europe) within five seconds */

        SELECT System.Timestamp AS WindowEnd, COUNT(*) AS FraudulentCalls
        INTO "CallStream-PowerBI"
        FROM "CallStream" CS1 TIMESTAMP BY CallRecTime
        JOIN "CallStream" CS2 TIMESTAMP BY CallRecTime

        /* Where the caller is the same, as indicated by IMSI (International Mobile Subscriber Identity) */
        ON CS1.CallingIMSI = CS2.CallingIMSI

        /* ...and date between CS1 and CS2 is between one and five seconds */
        AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5

        /* Where the switch location is different */
        WHERE CS1.SwitchNum != CS2.SwitchNum
        GROUP BY TumblingWindow(Duration(second, 1))

4. Haga clic en **Guardar**.


## <a name="test-the-query"></a>Prueba de la consulta
Esta sección es opcional pero conveniente. 

1. Si la aplicación TelcoStreaming no se ejecuta en este momento, siga estos pasos para iniciarla:

    * Abra el símbolo del sistema.
    * Vaya a la carpeta donde se encuentran los archivos telcogenerator.exe y telcodatagen.exe.config modificado.
    * Ejecute el siguiente comando:

            telcodatagen.exe 1000 .2 2

2. En la hoja **Consulta**, haga clic en los puntos que aparecen junto a la entrada `CallStream` y seleccione **Datos de ejemplo de la entrada**.

3. Especifique que quiere datos correspondientes a tres minutos y haga clic en **OK**. Espere a que se le notifique que se ha realizado un muestreo de los datos.

4. Haga clic en **Probar** y asegúrese de que obtiene resultados.


## <a name="run-the-job"></a>Ejecución del trabajo

1. Asegúrese de que la aplicación TelcoStreaming se ejecuta.

2. Cierre la hoja **Consulta**.

3. En la hoja del trabajo, haga clic en **Iniciar**.

    ![Inicio del trabajo de Análisis de transmisiones](./media/stream-analytics-power-bi-dashboard/stream-analytics-sa-job-start-output.png)

El trabajo de Stream Analytics empieza a buscar llamadas fraudulentas en el flujo entrante. También crea el conjunto de datos y la tabla en Power BI y empieza a enviarles datos sobre las llamadas fraudulentas.


## <a name="create-the-dashboard-in-power-bi"></a>Creación del panel en Power BI

1. Vaya a [Powerbi.com](https://powerbi.com) e inicie sesión con su cuenta profesional o educativa. Si la consulta del trabajo de Stream Analytics genera resultados, verá que el conjunto de datos ya se ha creado:

    ![Conjunto de datos de streaming en Power BI](./media/stream-analytics-power-bi-dashboard/streaming-dataset.png)

2. En el área de trabajo, haga clic en **+&nbsp;Crear**.

    ![Botón Crear en el área de trabajo de Power BI](./media/stream-analytics-power-bi-dashboard/pbi-create-dashboard.png)

3. Cree otro panel y asígnele el nombre `Fraudulent Calls`.

    ![Creación de un panel y asignación de un nombre en el área de trabajo de Power BI](./media/stream-analytics-power-bi-dashboard/pbi-create-dashboard-name.png)

4. En la parte superior de la ventana, haga clic en **Agregar icono**, seleccione **DATOS DE TRANSMISIÓN PERSONALIZADOS** y luego haga clic en **Siguiente**.

    ![Conjunto de datos de streaming personalizados](./media/stream-analytics-power-bi-dashboard/custom-streaming-data.png)

5. En **YOUR DATSETS** (SUS CONJUNTOS DE DATOS), seleccione el conjunto de datos y haga clic en **Siguiente**.

    ![Su conjunto de datos de streaming](./media/stream-analytics-power-bi-dashboard/your-streaming-dataset.png)

6. En **Tipo de visualización**, seleccione **Tarjeta** y luego, en la lista **Campos**, seleccione **fraudulentcalls**.

    ![Detalles de visualización del nuevo icono](./media/stream-analytics-power-bi-dashboard/add-fraud.png)

7. Haga clic en **Siguiente**.

8. Rellene detalles del icono, como el título y el subtítulo.

    ![Título y subtítulo del nuevo icono](./media/stream-analytics-power-bi-dashboard/pbi-new-tile-details.png)

9. Haga clic en **Apply**.

    Ahora tiene un contador de fraudes.

    ![Contador de fraudes](./media/stream-analytics-power-bi-dashboard/fraud-counter.png)

8. Siga de nuevo los pasos para agregar un icono (a partir del paso 4). Esta vez, haga lo siguiente:

    * Cuando obtenga el **Tipo de visualización**, seleccione **Gráfico de líneas**. 
    * Agregue un eje y seleccione **windowend**. 
    * Agregue un valor y seleccione **fraudulentcalls**.
    * En **Período de tiempo para mostrar**, seleccione los últimos 10 minutos.

    ![Creación de un icono de gráfico de líneas](./media/stream-analytics-power-bi-dashboard/pbi-create-tile-line-chart.png)

9. Haga clic en **Siguiente**, agregue el título y el subtítulo y haga clic en **Aplicar**.

    El panel de Power BI le ofrece ahora dos vistas de datos sobre las llamadas fraudulentas detectadas en los datos de streaming.

    ![Panel de Power BI finalizado que muestra dos iconos de llamadas fraudulentas](./media/stream-analytics-power-bi-dashboard/pbi-dashboard-fraudulent-calls-finished.png)


## <a name="learn-more-about-power-bi"></a>Más información sobre Power BI

Este tutorial solo muestra cómo crear algunos tipos de visualizaciones para un conjunto de datos. Power BI puede ayudarle a crear otras herramientas de inteligencia empresarial de cliente para su organización. Para obtener otras ideas, vea los siguientes recursos:

* Para obtener otro ejemplo de un panel de Power BI, vea el vídeo [Getting Started with Power BI](https://youtu.be/L-Z_6P56aas?t=1m58s) .
* Para más información sobre cómo configurar una salida de trabajo de Stream Analytics en Power BI y utilizar grupos de Power BI, revise la sección [Power BI](stream-analytics-define-outputs.md#power-bi) del artículo [Salidas de Stream Analytics](stream-analytics-define-outputs.md). 
* Para información sobre el uso en general de Power BI, vea [Paneles de Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/).


## <a name="learn-about-limitations-and-best-practices"></a>Más información sobre limitaciones y prácticas recomendadas
Actualmente, se puede llamar a Power BI una vez por segundo aproximadamente. Los objetos visuales de streaming admiten paquetes de 15 KB. Si el tamaño es superior, se producirá un error en los objetos visuales de streaming (pero la inserción continuará funcionando). Gracias a estas limitaciones, Power BI se presta de forma más natural a los casos en los que Azure Stream Analytics realiza una reducción considerable de la carga de datos. Se recomienda utilizar una ventana de saltos de tamaño constante o una ventana de salto para asegurarse de que la inserción de datos es a lo sumo de una inserción por segundo y de que la consulta se ajusta a los requisitos de capacidad de procesamiento.

Puede utilizar la siguiente ecuación para calcular el valor que asignar a la ventana en segundos:

![Ecuación 1](./media/stream-analytics-power-bi-dashboard/equation1.png)  

Por ejemplo:

* Tiene 1000 dispositivos que envían datos a intervalos de un segundo.
* Está usando Power BI Pro SKU que admite 1 000 000 de filas por hora.
* Desea publicar la cantidad media de datos por dispositivo en Power BI.

En consecuencia, la ecuación se convierte en:

![Ecuación 2](./media/stream-analytics-power-bi-dashboard/equation2.png)  

Con esta configuración, se puede cambiar la consulta original a la siguiente:

    SELECT
        MAX(hmdt) AS hmdt,
        MAX(temp) AS temp,
        System.TimeStamp AS time,
        dspl
    INTO "CallStream-PowerBI"
    FROM
        Input TIMESTAMP BY time
    GROUP BY
        TUMBLINGWINDOW(ss,4),
        dspl


### <a name="renew-authorization"></a>Renovar la autorización
Si la contraseña ha cambiado desde que se creó o autenticó por última vez el trabajo, tendrá que volver a autenticar la cuenta de Power BI. Si Azure Multi-Factor Authentication se configura en el inquilino de Azure Active Directory (Azure AD), también debe renovar la autorización de Power BI cada dos semanas. Si no se renueva, podrían aparecer síntomas, como la ausencia de salida del trabajo o un `Authenticate user error` en los registros de operaciones.

De forma similar, si un trabajo intenta iniciarse después de que el token haya caducado, se producirá un error y no se iniciará. Para resolver este problema, detenga el trabajo en ejecución y vaya a la salida de Power BI. A fin de evitar que se pierdan datos, seleccione el vínculo **Renovar autorización** y reinicie el trabajo desde la **Hora de la última detención**.

Después de que la autorización se haya actualizado con Power BI, se mostrará una alerta verde en el área de autorización para indicar que el problema se ha resuelto.

## <a name="get-help"></a>Obtener ayuda
Para obtener ayuda adicional, pruebe nuestro [foro de Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Pasos siguientes
* [Introducción al Análisis de transmisiones de Azure](stream-analytics-introduction.md)
* [Introducción al uso de Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Escalación de trabajos de Análisis de transmisiones de Azure](stream-analytics-scale-jobs.md)
* [Referencia del lenguaje de consulta de Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referencia de API de REST de administración de Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
