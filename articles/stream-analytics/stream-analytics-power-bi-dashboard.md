---
title: Panel de Power BI en Stream Analytics | Microsoft Docs
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
ms.sourcegitcommit: c60d620d9fc91cfef2340b645e15ed19d3d1f4e1
ms.openlocfilehash: 39b2852c39e8d3eb4687cbcfd9116e1d66fc9d9d


---
# <a name="stream-analytics--power-bi-a-real-time-analytics-dashboard-for-streaming-data"></a>Análisis de transmisiones y Power BI: panel de análisis en tiempo real de flujo de datos
Análisis de transmisiones de Azure permite aprovechar una de las principales herramientas de inteligencia empresarial, Microsoft Power BI. Aprenda a usar Análisis de transmisiones de Azure para analizar grandes volúmenes de datos de streaming y obtener información detallada en un panel de análisis Power BI en tiempo real.

Utilice [Microsoft Power BI](https://powerbi.com/) para crear rápidamente un panel dinámico. [Vea un vídeo que ilustra el escenario](https://www.youtube.com/watch?v=SGUpT-a99MA).

En este artículo, aprenderá a crear sus propias herramientas de inteligencia empresarial personalizadas mediante Power BI como salida para los trabajos de Análisis de transmisiones de Azure y a utilizar un panel en tiempo real.

## <a name="prerequisites"></a>Requisitos previos
* Cuenta de Microsoft Azure
* Cuenta profesional o educativa para Power BI
* Complete el escenario de introducción [Detección de fraudes en tiempo real](stream-analytics-real-time-fraud-detection.md). Este artículo se basa en ese flujo de trabajo y agrega una salida de conjunto de datos de streaming de Power BI.

## <a name="add-power-bi-output"></a>Incorporación del resultado de Power BI
Ahora que existe una entrada para el trabajo, puede definirse una salida a Power BI. Active la casilla en el centro del panel de trabajo **Salidas**. A continuación, haga clic en el conocido botón **+ Agregar** y cree la salida.

![agregar salida](./media/stream-analytics-power-bi-dashboard/create-pbi-output.png)

Proporcione un valor en **Alias de salida**: puede colocar cualquier alias de salida al que le resulte fácil referirse. Este alias de salida es especialmente útil si decide tener varias salidas para su trabajo. En ese caso, debe hacer referencia a esta salida de la consulta. Por ejemplo, vamos a usar el valor de alias de salida "StreamAnalyticsRealTimeFraudPBI".

Haga clic en el botón **Autorizar**.

![agregar autorización](./media/stream-analytics-power-bi-dashboard/pbi-authorize.png)

Se mostrará una ventana para proporcionar las credenciales de Azure (cuenta profesional o educativa) y proporcionará el acceso de trabajo de Azure al área de Power BI.

![autorizar campos](./media/stream-analytics-power-bi-dashboard/authorize-area.png)

La ventana de autorización desaparecerá cuando esté satisfecho y el área Nueva salida tendrá campos para Nombre del conjunto de datos y Nombre de tabla.

![área de trabajo pbi](./media/stream-analytics-power-bi-dashboard/pbi-workspace.png)

Defínalos como se indica a continuación:
* **Área de trabajo de grupo**: seleccione un área de trabajo en el inquilino de Power BI en donde se creará el conjunto de datos.
* **Nombre del conjunto de datos** : proporcione un nombre del conjunto de datos que desea que tenga la salida de Power BI. Por ejemplo, vamos a usar "StreamAnalyticsRealTimeFraudPBI".
* **Nombre de tabla** : proporcione un nombre de tabla en el conjunto de datos de la salida de Power BI. Supongamos que lo llamamos "StreamAnalyticsRealTimeFraudPBI". Actualmente, la salida de Power BI de trabajos de Análisis de transmisiones solo puede tener una tabla en un conjunto de datos.

Haga clic en **Crear** y la configuración de salida se habrá completado.

> [!WARNING]
> Tenga en cuenta que si Power BI ya cuenta con un conjunto de datos y una tabla con el mismo nombre que el proporcionado en este trabajo de Stream Analytics, se sobrescribirán los datos existentes.
> Asimismo, no debería crear explícitamente este conjunto de datos y esta tabla en su cuenta de Power BI. Se crearán automáticamente cuando inicie el trabajo de Stream Analytics y este empiece a enviar salida a Power BI. Si la consulta de trabajo no genera ningún resultado, el conjunto de datos y la tabla no se crearán.
> 
> 

El conjunto de datos se crea con el siguiente conjunto de configuración;
* defaultRetentionPolicy: BasicFIFO (los datos tienen un orden FIFO, 200 000 filas como máximo)
* defaultMode: pushStreaming: admite iconos de streaming y objetos visuales tradicionales basados en informes (también conocidos como inserción)
* En este momento no se admite la creación de conjuntos de datos con otras marcas

Para más información sobre conjuntos de datos de Power BI consulte la referencia de la [API de REST de Power BI](https://msdn.microsoft.com/library/mt203562.aspx).


## <a name="write-query"></a>Escritura de una consulta
Vaya a la pestaña **Consulta** de su trabajo. Escriba la consulta cuya salida desea tener en su Power BI. Por ejemplo, podría ser algo como la siguiente consulta SQL para detectar fraudes en tarjetas SIM en el sector de telecomunicaciones:


```
/* Our criteria for fraud:
 Calls made from the same caller to two phone switches in different locations (e.g. Australia and Europe) within 5 seconds) */

 SELECT System.Timestamp AS WindowEnd, COUNT(*) AS FraudulentCalls
 INTO "StreamAnalyticsRealTimeFraudPBI"
 FROM "StreamAnalyticsRealTimeFraudInput" CS1 TIMESTAMP BY CallRecTime
 JOIN "StreamAnalyticsRealTimeFraudInput" CS2 TIMESTAMP BY CallRecTime
   
/* Where the caller is the same, as indicated by IMSI (International Mobile Subscriber Identity) */
 ON CS1.CallingIMSI = CS2.CallingIMSI

/* ...and date between CS1 and CS2 is between 1 and 5 seconds */
 AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5

/* Where the switch location is different */
 WHERE CS1.SwitchNum != CS2.SwitchNum
 GROUP BY TumblingWindow(Duration(second, 1))
```

## <a name="create-the-dashboard-in-power-bi"></a>Creación del panel en Power BI

Vaya a [Powerbi.com](https://powerbi.com) e inicie sesión con su cuenta profesional o educativa. Si la consulta del trabajo de Análisis de transmisiones genera resultados, verá que ya se ha creado el conjunto de datos:

![conjunto de datos de streaming](./media/stream-analytics-power-bi-dashboard/streaming-dataset.png)

Haga clic en Agregar icono y seleccione los datos de streaming personalizados.

![conjunto de datos de streaming personalizados](./media/stream-analytics-power-bi-dashboard/custom-streaming-data.png)

A continuación, seleccione el conjunto de datos de la lista:

![su conjunto de datos de streaming](./media/stream-analytics-power-bi-dashboard/your-streaming-dataset.png)

Ahora cree una tarjeta de visualización y seleccione el campo fraudulentcalls.

![agregar fraude](./media/stream-analytics-power-bi-dashboard/add-fraud.png)

Ahora tenemos un contador de fraudes.

![contador de fraudes](./media/stream-analytics-power-bi-dashboard/fraud-counter.png)

Recorra el ejercicio de agregar un icono de nuevo, pero esta vez seleccione el gráfico de líneas. Agregue fraudulentcalls como el valor y el eje como windowend. Seleccioné los últimos 10 minutos:

![llamadas fraudulentas](./media/stream-analytics-power-bi-dashboard/fraud-calls.png)


Tenga en cuenta que este tutorial muestra cómo crear un tipo de gráfico para un conjunto de datos. Power BI puede ayudarle a crear otras herramientas de inteligencia empresarial de cliente para su organización. Para obtener otro ejemplo de un panel de Power BI, vea el vídeo [Getting Started with Power BI](https://youtu.be/L-Z_6P56aas?t=1m58s) .

Para más información sobre cómo configurar una salida de Power BI y utilizar grupos de Power BI, revise la [sección Power BI](stream-analytics-define-outputs.md#power-bi) de [Descripción de salidas de Stream Analytics](stream-analytics-define-outputs.md "Descripción de salidas de Stream Analytics"). Otro recurso útil para aprender más sobre la creación de paneles con Power BI es [Paneles de Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/).

## <a name="limitations-and-best-practices"></a>Limitaciones y prácticas recomendadas
Power BI emplea restricciones tanto de simultaneidad como de rendimiento, tal como se describe aquí: [https://powerbi.microsoft.com/pricing](https://powerbi.microsoft.com/pricing "Precios de Power BI")

Actualmente, se puede llamar a Power BI una vez por segundo aproximadamente. Los objetos visuales de streaming admiten paquetes de 15 KB de tamaño. Con un tamaño superior se producirá un error en los objetos visuales de streaming (pero la inserción continuará funcionando).

Gracias a ello Power BI se hace de forma natural con los casos en los que Análisis de transmisiones de Azure realiza una reducción considerable de la carga de datos.
Se recomienda usar TumblingWindow o HoppingWindow para asegurarse de que la inserción de datos sea como máximo de 1 inserción/segundo, y de que la consulta entre dentro de los requisitos de rendimiento. Puede usar la siguiente ecuación para calcular el valor que se debe asignar a la ventana en segundos:

![ecuación&1;](./media/stream-analytics-power-bi-dashboard/equation1.png)  

Por ejemplo: si tiene 1.000 dispositivos enviando datos cada segundo, está en Power BI Pro SKU que admite 1.000.000 de filas/hora, y desea obtener la media de datos por dispositivo en Power BI, puede usar como mucho una inserción cada 4 segundos por dispositivo (como se muestra a continuación):  

![ecuación&2;](./media/stream-analytics-power-bi-dashboard/equation2.png)  

Lo que significa que se cambiaría la consulta original a:

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
Tendrá que volver a autenticar la cuenta de Power BI si la contraseña ha cambiado desde que se creó el trabajo o desde la última autenticación. Si Multi-Factor Authentication (MFA) se configura en el inquilino de Azure Active Directory (AAD), también debe renovar la autorización de Power BI cada 2 semanas. Un síntoma de este problema es la ausencia de salida del trabajo y un "error de autenticación de usuario" en los registros de operaciones.

De forma similar, si un trabajo intenta iniciarse mientras el token está caducado, se producirá un error y no se iniciará. Para resolver este problema, detenga su trabajo en ejecución y vaya a la salida de Power BI.  Haga clic en el vínculo "Renovar autorización" y reinicie el trabajo desde la hora en que se detuvo por última vez para evitar la pérdida de datos. Después de que la autorización se actualice con Power BI, verá una alerta verde en el área de autorización para reflejar que el problema se resolvió.

## <a name="get-help"></a>Obtener ayuda
Para obtener más ayuda, pruebe nuestro [foro de Análisis de transmisiones de Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Pasos siguientes
* [Introducción al Análisis de transmisiones de Azure](stream-analytics-introduction.md)
* [Introducción al uso de Análisis de transmisiones de Azure](stream-analytics-get-started.md)
* [Escalación de trabajos de Análisis de transmisiones de Azure](stream-analytics-scale-jobs.md)
* [Referencia del lenguaje de consulta de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referencia de API de REST de administración de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)




<!--HONumber=Feb17_HO1-->


