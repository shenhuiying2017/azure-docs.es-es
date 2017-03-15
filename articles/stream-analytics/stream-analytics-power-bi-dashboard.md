---
title: Panel de Power BI en Azure Stream Analytics | Microsoft Docs
description: "Utilice un panel de Power BI de streaming en tiempo real para reunir información de inteligencia empresarial y analizar grandes volúmenes de datos procedentes de un trabajo de Análisis de transmisiones."
keywords: "panel de análisis, panel en tiempo real"
services: stream-analytics
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: fe8db732-4397-4e58-9313-fec9537aa2ad
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 02/01/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 3dbc49d35def6d7b12ade529d1dd1156dee9d75b
ms.openlocfilehash: 09c54f8cce119c1cbe6a08e969236612447d9e17
ms.lasthandoff: 02/27/2017


---
# <a name="stream-analytics-and-power-bi-a-real-time-analytics-dashboard-for-streaming-data"></a>Stream Analytics y Power BI: panel de análisis en tiempo real de flujo de datos
Azure Stream Analytics permite aprovechar una de las principales herramientas de inteligencia empresarial, Microsoft Power BI. Aprenda a usar Azure Stream Analytics para analizar grandes volúmenes de datos de streaming y obtener información detallada de un panel de análisis Power BI en tiempo real.

Utilice [Microsoft Power BI](https://powerbi.com/) para crear rápidamente un panel dinámico. [Vea un vídeo que muestra este escenario](https://www.youtube.com/watch?v=SGUpT-a99MA).

En este artículo, aprenderá a crear sus propias herramientas de inteligencia empresarial personalizadas utilizando Power BI como salida para los trabajos de Azure Stream Analytics. También aprenderá a utilizar un panel en tiempo real.

## <a name="prerequisites"></a>Requisitos previos
* Cuenta de Microsoft Azure.
* Cuenta profesional o educativa para Power BI.
* Escenario de [Detección de fraudes en tiempo real](stream-analytics-real-time-fraud-detection.md) completado. El presente artículo se basa en el flujo de trabajo que se describe en [Detección de fraudes en tiempo real](stream-analytics-real-time-fraud-detection.md) y añade una salida de conjunto de datos de streaming de Power BI.

## <a name="add-power-bi-output"></a>Incorporación del resultado de Power BI
Ahora que existe una entrada para el trabajo, puede definirse una salida a Power BI.

1. Active la casilla **Salidas** en el centro del panel de trabajo. A continuación, seleccione **+ Agregar** para crear la salida.

    ![Agregar salida](./media/stream-analytics-power-bi-dashboard/create-pbi-output.png)

2. Proporcione el **Alias de salida**. Puede utilizar cualquier alias de salida al que le resulte fácil referirse. Este alias de salida resulta útil si decide tener varias salidas para su trabajo. En este caso, debe hacer referencia a esta salida en la consulta. Por ejemplo, vamos a usar el valor de alias de salida "StreamAnalyticsRealTimeFraudPBI".

3. Seleccione **Autorizar**.

    ![Agregar autorización](./media/stream-analytics-power-bi-dashboard/pbi-authorize.png)

4. Se abrirá una ventana donde puede proporcionar sus credenciales de Azure (para una cuenta profesional o educativa). También proporciona al trabajo de Azure acceso a su área de Power BI.

    ![Autorizar campos](./media/stream-analytics-power-bi-dashboard/authorize-area.png)

5. La autorización desaparece una vez que haya proporcionado la información necesaria. El área **Nueva salida** tiene campos para el **Nombre del conjunto de datos** y el **Nombre de tabla**.

    ![Área de trabajo de PBI](./media/stream-analytics-power-bi-dashboard/pbi-workspace.png)

6. Defínalos como se indica a continuación:
    * **Área de trabajo de grupo**: seleccione un área de trabajo en el inquilino de Power BI en donde se creará el conjunto de datos.
    * **Nombre del conjunto de datos**: proporcione un nombre del conjunto de datos que desea que tenga la salida de Power BI. Por ejemplo, vamos a usar "StreamAnalyticsRealTimeFraudPBI".
    * **Nombre de tabla**: proporcione un nombre de tabla en el conjunto de datos de la salida de Power BI. Por ejemplo, vamos a usar "StreamAnalyticsRealTimeFraudPBI". Actualmente, la salida de Power BI de trabajos de Stream Analytics solo puede tener una tabla en un conjunto de datos.

7. Seleccione **Crear**. La salida ya está configurada.

> [!WARNING]
> Si Power BI ya cuenta con un conjunto de datos y una tabla con el mismo nombre que el proporcionado en este trabajo de Stream Analytics, se sobrescribirán los datos existentes.
> Asimismo, se recomienda que no cree explícitamente este conjunto de datos y esta tabla en su cuenta de Power BI. Se crearán automáticamente cuando inicie el trabajo de Stream Analytics y este empiece a enviar salida a Power BI. Si la consulta de trabajo no genera ningún resultado, el conjunto de datos y la tabla no se crearán.
>
>

El conjunto de datos se crea con la siguiente configuración:
* **defaultRetentionPolicy: BasicFIFO**: los datos tienen un orden FIFO, con un máximo de 200 000 filas.
* **defaultMode: pushStreaming**: admite iconos de streaming y objetos visuales tradicionales basados en informes (también conocidos como inserción).

Actualmente, no se pueden crear conjuntos de datos con otras marcas.

Para más información sobre conjuntos de datos de Power BI, consulte la referencia de la [API de REST de Power BI](https://msdn.microsoft.com/library/mt203562.aspx).


## <a name="write-query"></a>Escritura de una consulta
Vaya a la pestaña **Consulta** de su trabajo. Escriba la consulta cuya salida desea tener en Power BI. Por ejemplo, podría ser similar a la siguiente consulta SQL para detectar fraudes en tarjetas SIM en el sector de telecomunicaciones:


```
/* Our criteria for fraud:
 Calls made from the same caller to two phone switches in different locations (for example, Australia and Europe) within five seconds */

 SELECT System.Timestamp AS WindowEnd, COUNT(*) AS FraudulentCalls
 INTO "StreamAnalyticsRealTimeFraudPBI"
 FROM "StreamAnalyticsRealTimeFraudInput" CS1 TIMESTAMP BY CallRecTime
 JOIN "StreamAnalyticsRealTimeFraudInput" CS2 TIMESTAMP BY CallRecTime

/* Where the caller is the same, as indicated by IMSI (International Mobile Subscriber Identity) */
 ON CS1.CallingIMSI = CS2.CallingIMSI

/* ...and date between CS1 and CS2 is between one and five seconds */
 AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5

/* Where the switch location is different */
 WHERE CS1.SwitchNum != CS2.SwitchNum
 GROUP BY TumblingWindow(Duration(second, 1))
```

## <a name="create-the-dashboard-in-power-bi"></a>Creación del panel en Power BI

1. Vaya a [Powerbi.com](https://powerbi.com) e inicie sesión con su cuenta profesional o educativa. Si la consulta del trabajo de Stream Analytics genera resultados, verá que ya se ha creado el conjunto de datos, como se muestra en la siguiente ilustración:

    ![Conjunto de datos de streaming](./media/stream-analytics-power-bi-dashboard/streaming-dataset.png)

2. Seleccione **Agregar icono** y seleccione los datos de streaming personalizados.

    ![Conjunto de datos de streaming personalizados](./media/stream-analytics-power-bi-dashboard/custom-streaming-data.png)

3. Seleccione el conjunto de datos de la lista.

    ![Su conjunto de datos de streaming](./media/stream-analytics-power-bi-dashboard/your-streaming-dataset.png)

4. Cree una tarjeta de visualización. A continuación, seleccione el campo **fraudulentcalls**.

    ![Agregar fraude](./media/stream-analytics-power-bi-dashboard/add-fraud.png)

    Ahora tenemos un contador de fraudes.

    ![Contador de fraudes](./media/stream-analytics-power-bi-dashboard/fraud-counter.png)

5. Vuelva a completar paso a paso el ejercicio de agregar un icono. Esta vez seleccione el gráfico de líneas. Agregue **fraudulentcalls** como el valor y **windowend** como el eje. Hemos seleccionado los 10 últimos minutos, como se muestra en la captura de pantalla siguiente:

![Llamadas fraudulentas](./media/stream-analytics-power-bi-dashboard/fraud-calls.png)


Este tutorial solo muestra cómo crear un tipo de gráfico para un conjunto de datos. Power BI puede ayudarle a crear otras herramientas de inteligencia empresarial de cliente para su organización. Para obtener otro ejemplo de un panel de Power BI, vea el vídeo [Getting Started with Power BI](https://youtu.be/L-Z_6P56aas?t=1m58s) .

Para más información sobre cómo configurar una salida de Power BI y utilizar grupos de Power BI, revise la [sección Power BI](stream-analytics-define-outputs.md#power-bi) de [Descripción de salidas de Stream Analytics](stream-analytics-define-outputs.md "Descripción de salidas de Stream Analytics"). Los [Paneles en Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/) son otro recurso útil.

## <a name="learn-about-limitations-and-best-practices"></a>Más información sobre limitaciones y prácticas recomendadas
Power BI emplea restricciones tanto de simultaneidad como de rendimiento, tal como se describe [en esta página sobre Power BI](https://powerbi.microsoft.com/pricing "Precios de Power BI").

Actualmente, se puede llamar a Power BI una vez por segundo aproximadamente. Los objetos visuales de streaming admiten paquetes de 15 KB. Si el tamaño es superior, se producirá un error en los objetos visuales de streaming (pero la inserción continuará funcionando).

Gracias a estas limitaciones, Power BI se presta de forma más natural a los casos en los que Azure Stream Analytics realiza una reducción considerable de la carga de datos.
Se recomienda utilizar una ventana de saltos de tamaño constante o una ventana de salto para asegurarse de que la inserción de datos es a lo sumo de una inserción por segundo y de que la consulta se ajusta a los requisitos de rendimiento.

Puede utilizar la siguiente ecuación para calcular el valor que asignar a la ventana en segundos:

![Ecuación&1;](./media/stream-analytics-power-bi-dashboard/equation1.png)  

Por ejemplo:
- Tiene 1000 dispositivos que envían datos a intervalos de un segundo.
- Está usando Power BI Pro SKU que admite 1 000 000 de filas por hora.
- Desea publicar la cantidad media de datos por dispositivo en Power BI.

En consecuencia, la ecuación se convierte en:

![Ecuación&2;](./media/stream-analytics-power-bi-dashboard/equation2.png)  

De este modo, podemos cambiar la consulta original a la siguiente:

    SELECT
        MAX(hmdt) AS hmdt,
        MAX(temp) AS temp,
        System.TimeStamp AS time,
        dspl
    INTO
        OutPBI
    FROM
        Input TIMESTAMP BY time
    GROUP BY
        TUMBLINGWINDOW(ss,4),
        dspl


### <a name="renew-authorization"></a>Renovar la autorización
Si la contraseña ha cambiado desde que se creó el trabajo o desde la última autenticación, tendrá que volver a autenticar la cuenta de Power BI. Si Azure Multi-Factor Authentication se configura en el inquilino de Azure Active Directory (Azure AD), también debe renovar la autorización de Power BI cada dos semanas. Si no se renueva, podrían aparecer síntomas, como la ausencia de salida del trabajo o un "error de autenticación de usuario" en los registros de operaciones.

De forma similar, si un trabajo intenta iniciarse después de que el token haya caducado, se producirá un error y no se iniciará. Para resolver este problema, detenga el trabajo en ejecución y vaya a la salida de Power BI. A fin de evitar que se pierdan datos, seleccione el vínculo **Renovar autorización** y reinicie el trabajo desde la **Hora de la última detención**.

Después de que la autorización se haya actualizado con Power BI, se mostrará una alerta verde en el área de autorización para indicar que el problema se ha resuelto.

## <a name="get-help"></a>Obtener ayuda
Para obtener ayuda adicional, pruebe nuestro [foro de Análisis de transmisiones de Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Pasos siguientes
* [Introducción al Análisis de transmisiones de Azure](stream-analytics-introduction.md)
* [Introducción al uso de Análisis de transmisiones de Azure](stream-analytics-get-started.md)
* [Escalación de trabajos de Análisis de transmisiones de Azure](stream-analytics-scale-jobs.md)
* [Referencia del lenguaje de consulta de Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referencia de API de REST de administración de Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

