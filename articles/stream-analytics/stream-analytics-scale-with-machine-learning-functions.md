<properties
	pageTitle="Escalado del trabajo de Análisis de transmisiones con funciones de Aprendizaje automático de Azure | Microsoft Azure"
	description="Aprenda a escalar correctamente trabajos de Análisis de transmisiones (creación de particiones, cantidad de SU y más) cuando se usan funciones de Aprendizaje automático de Azure."
	keywords=""
	documentationCenter=""
	services="stream-analytics"
	authors="jeffstokes72"
	manager="paulettm"
	editor="cgronlun"
/>

<tags
	ms.service="stream-analytics"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="data-services"
	ms.date="03/18/2016"
	ms.author="jeffstok"
/>

# Escalado del trabajo de Análisis de transmisiones con funciones de Aprendizaje automático de Azure

A menudo resulta bastante fácil configurar un trabajo de Análisis de transmisiones y ejecutar algunos datos de ejemplo mediante él. ¿Qué debemos hacer cuando tengamos que ejecutar el mismo trabajo con un volumen de datos más alto? Será necesario comprender cómo configurar el trabajo de Análisis de transmisiones para escalarlo. En este documento, nos centraremos en los aspectos especiales de escalar trabajos de Análisis de transmisiones con funciones de Aprendizaje automático. Para más información sobre cómo escalar trabajos de Análisis de transmisiones en general, consulte el artículo [Escalado de trabajos de Análisis de transmisiones de Azure para incrementar el rendimiento de procesamiento de flujo de datos](stream-analytics-scale-jobs.md).

## ¿Qué es una función de Aprendizaje automático de Azure en Análisis de transmisiones?

Una función de Aprendizaje automático en Análisis de transmisiones puede utilizarse como una llamada de función normal en el lenguaje de consulta de Análisis de transmisiones. Sin embargo, en segundo plano, las llamadas de función son en realidad solicitudes de servicio web de Aprendizaje automático de Azure. Los servicios web de Aprendizaje automático admiten el "procesamiento por lotes" de varias filas, lo que se conoce como mini lote, en la misma llamada a la API de servicio web, con el fin de mejorar el rendimiento en general. Consulte los siguientes artículos para más detalles: [Azure ML Now Available as a Function in Azure Stream Analytics](https://blogs.technet.microsoft.com/machinelearning/2015/12/10/azure-ml-now-available-as-a-function-in-azure-stream-analytics/) (Aprendizaje automático de Azure ahora disponible como función en Análisis de transmisiones de Azure) y [Cómo consumir un servicio web de Aprendizaje automático de Azure implementado en un experimento de Aprendizaje automático](machine-learning/machine-learning-consume-web-services.md#request-response-service-rrs).

## Configuración de un trabajo de Análisis de transmisiones con funciones de Aprendizaje automático

Al configurar una función de Aprendizaje automático para el trabajo de Análisis de transmisiones, hay dos parámetros a tener en cuenta: el tamaño de lote de las llamadas de función de Aprendizaje automático y las unidades de streaming (SU) aprovisionadas para el trabajo de Análisis de transmisiones. Para determinar los valores adecuados de estos parámetros, será necesario en primer lugar decidir entre latencia y rendimiento, es decir, entre la latencia del trabajo de Análisis de transmisiones y el rendimiento de cada SU. Siempre se pueden agregar SU a un trabajo para aumentar el rendimiento de una consulta de Análisis de transmisiones bien particionada, aunque las SU adicionales aumentarán el costo de ejecución del trabajo.

Por lo tanto, es importante determinar la *tolerancia* de la latencia en la ejecución de un trabajo de Análisis de transmisiones. La latencia adicional derivada de ejecutar solicitudes de servicio de Aprendizaje automático de Azure aumentará naturalmente con el tamaño del lote, lo que agravará la latencia del trabajo de Análisis de transmisiones. Por otro lado, al aumentar el tamaño del lote, el trabajo de Análisis de transmisiones puede procesar *más eventos con el *mismo número* de solicitudes de servicio web de Aprendizaje automático. Con frecuencia el aumento de la latencia del servicio web de Aprendizaje automático es sublineal con respecto al aumento del tamaño del lote, así que es importante tener en cuenta el tamaño de lote más rentable para un servicio web de Aprendizaje automático en cualquier situación dada. El tamaño de lote predeterminado para las solicitudes de servicio web es de 1000 y se puede modificar mediante la [API de REST de Análisis de transmisiones](https://msdn.microsoft.com/library/mt653706.aspx "API de REST de Análisis de transmisiones") o el [cliente de PowerShell para Análisis de transmisiones](stream-analytics-monitor-and-manage-jobs-use-powershell.md "Cliente de PowerShell para Análisis de transmisiones").

Una vez determinado el tamaño de lote, se puede determinar la cantidad de unidades de streaming (SU), según el número de eventos que la función tenga que procesar por segundo. Para más información sobre las unidades de streaming, consulte el artículo [Escalado de trabajos de Análisis de transmisiones de Azure para incrementar el rendimiento de procesamiento de flujo de datos](stream-analytics-scale-jobs.md#configuring-streaming-units).

En general, hay 20 conexiones simultáneas al servicio web de Aprendizaje automático por cada 6 SU, salvo que los trabajos de 1 y 3 SU obtendrán también 20 conexiones simultáneas. Por ejemplo, si la tasa de datos de entrada es de 200 000 eventos por segundo y el tamaño de lote se deja en el valor predeterminado de 1000, la latencia de servicio web resultante con un minilote de 1000 eventos será de 200 ms. Esto significa que cada conexión puede hacer 5 solicitudes al servicio web de Aprendizaje automático en un segundo. Con 20 conexiones, el trabajo de Análisis de transmisiones puede procesar 20 000 eventos en 200 ms y, por lo tanto, 100 000 eventos en un segundo. Así que para procesar 200 000 eventos por segundo, el trabajo de Análisis de transmisiones necesita 40 conexiones simultáneas, lo que viene a ser 12 SU. En el diagrama siguiente se muestran las solicitudes desde el trabajo de Análisis de transmisiones hasta el punto de conexión de servicio web de Aprendizaje automático: cada 6 SU tienen 20 conexiones simultáneas al servicio web de Aprendizaje automático como máximo.

![Escalado de Análisis de transmisiones con funciones de Aprendizaje automático 2: ejemplo de trabajo](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-00.png "Escalado de Análisis de transmisiones con funciones de Aprendizaje automático 2: ejemplo de trabajo")

En general, si ***B*** es el tamaño de lote y ***L*** es la latencia del servicio web con el tamaño de lote B en milisegundos, el rendimiento de un trabajo de Análisis de transmisiones con ***N*** SU es:

![Escalado de Análisis de transmisiones con funciones de Aprendizaje automático: fórmula](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-02.png "Escalado de Análisis de transmisiones con funciones de Aprendizaje automático: fórmula")

Un aspecto adicional a tener en cuenta pueden ser las 'llamadas simultáneas máximas' en el lado del servicio web de Aprendizaje automático; se recomienda que estas llamadas se establezcan en el valor máximo (actualmente 200).

Para más información sobre esta configuración, consulte [Escalado del servicio web](../machine-learning/machine-learning-scaling-webservice.md).

## Ejemplo: Análisis de opiniones

En el ejemplo siguiente se incluye un trabajo de Análisis de transmisiones con la función de Aprendizaje automático de análisis de opiniones, como se describe en el [tutorial de integración de Aprendizaje automático de Análisis de transmisiones](stream-analytics-machine-learning-integration-tutorial.md).

La consulta es una consulta sencilla completamente particionada seguida de la función **sentiment**, tal como se muestra a continuación:

    WITH subquery AS (
        SELECT text, sentiment(text) as result from input
    )
    
    Select text, result.[Score]
    Into output
    From subquery

Considere el escenario siguiente: se debe crear un trabajo de Análisis de transmisiones con un rendimiento de 10 000 tweets por segundo para realizar análisis de opiniones de los tweets (eventos). ¿Podría administrar este trabajo de Análisis de transmisiones el tráfico con 1 SU? Con el tamaño de lote predeterminado de 1000, el trabajo debería poder mantenerse al nivel de la entrada. Es más, la función de Aprendizaje automático agregada no debería generar más de un segundo de latencia, que es la latencia general predeterminada del servicio web de Aprendizaje automático de análisis de opiniones (con un tamaño de lote predeterminado de 1000). La latencia **general** o total del trabajo de Análisis de transmisiones sería normalmente de unos segundos. Examine con más detalle este trabajo de Análisis de transmisiones, *especialmente* las llamadas de función de Aprendizaje automático. Si el tamaño de lote es 1000, un rendimiento de 10 000 eventos supondrá unas 10 solicitudes al servicio web. Incluso con 1 SU, hay suficientes conexiones simultáneas para dar cabida a este tráfico de entrada.

Pero ¿qué sucede si la tasa de eventos de entrada aumenta en 100 x y ahora el trabajo de Análisis de transmisiones necesita procesar 1 000 000 de tweets por segundo? Hay dos opciones:

1.  Aumentar el tamaño de lote
2.  Particionar el flujo de entrada para procesar los eventos en paralelo.

Con la primera opción, la **latencia** del trabajo aumentará.

Con la segunda opción, será necesario aprovisionar más SU y, por lo tanto, generar más solicitudes de servicio web de Aprendizaje automático simultáneas. Esto significa que el **costo** del trabajo aumentará.


Suponga que la latencia del servicio web de Aprendizaje automático de análisis de opiniones es de 200 ms para 1000 lotes de eventos o menos, 250 ms para 5000 lotes de eventos, 300 ms para 10 000 lotes de eventos o 500 ms para 25 000 lotes de eventos.

1. Con la primera opción (**sin** aprovisionar más SU), el tamaño de lote se podría aumentar a **25 000**. Esto, a su vez, permitiría que el trabajo procesara 1 000 000 eventos con 20 conexiones simultáneas al servicio web de Aprendizaje automático (con una latencia de 500 ms por llamada). De modo que, la latencia adicional del trabajo de Análisis de transmisiones debido a las solicitudes de la función de análisis de opiniones contra las solicitudes de servicio web de Aprendizaje automático aumentaría de **200 ms** a **500 ms**. Sin embargo, tenga en cuenta que el tamaño de lote **no** se puede aumentar de manera indefinida dado que el servicio web de Aprendizaje automático necesita que el tamaño de la carga de una solicitud sea de 4 MB o un tiempo de espera más pequeño de las solicitudes de servicio web tras 100 segundos de funcionamiento.
2. Con la segunda opción, el tamaño de lote se deja en 1000; con una latencia del servicio web de 200 ms, con cada 20 conexiones simultáneas al servicio web se podrían procesar 1000 * 20 * 5 eventos = 100 000 por segundo. Por tanto, para procesar 1 000 000 eventos por segundo, el trabajo necesitaría 60 SU. En comparación con la primera opción, el trabajo de Análisis de transmisiones haría más solicitudes de lote de servicio web, lo que a su vez generaría un mayor costo.

A continuación se muestra una tabla del rendimiento del trabajo de Análisis de transmisiones para diferentes SU y tamaños de lote (en número de eventos por segundo).

| SU | | | | Tamaño del lote (latencia de Aprendizaje automático) | |
|--------|-------------------------|---------------|---------------|----------------|----------------|
| | | | | | |
| | 500 (200 ms) | 1000 (200 ms) | 5000 (250 ms) | 10 000 (300 ms) | 25 000 (500 ms) |
| 1 unidad de búsqueda | 2500 | 5\.000 | 20\.000 | 30 000 | 50\.000 |
| 3 SU | 2500 | 5\.000 | 20\.000 | 30 000 | 50\.000 |
| 6 SU | 2500 | 5\.000 | 20\.000 | 30 000 | 50\.000 |
| 12 SU | 5\.000 | 10\.000 | 40\.000 | 60 000 | 100 000 |
| 18 SU | 7500 | 15 000 | 60 000 | 90 000 | 150 000 |
| 24 SU | 10\.000 | 20\.000 | 80 000 | 120 000 | 200 000 |
| … | … | … | … | … | … |
| 60 SU | 25 000 | 50\.000 | 200 000 | 300 000 | 500\.000 |

En este momento, ya debe saber cómo funcionan las funciones de Aprendizaje automático en Análisis de transmisiones. Probablemente también sepa que los trabajos de Análisis de transmisiones "extraen" datos de los orígenes de datos y que cada "extracción" devuelve un lote de eventos que procesa el trabajo de Análisis de transmisiones. ¿Cómo afecta este modelo de extracción a las solicitudes de servicio web de Aprendizaje automático?

Normalmente, el tamaño de lote que establecemos para las funciones de Aprendizaje automático no será divisible exactamente por el número de eventos devueltos por cada "extracción" del trabajo de Análisis de transmisiones. Cuando esto suceda, se llamará al servicio web de Aprendizaje automático con lotes "parciales". Esto se hace para no generar sobrecarga de latencia adicional del trabajo en la fusión de eventos de una extracción a otra.

## Nuevas métricas de supervisión relacionadas con la función

En el área de supervisión de un trabajo de Análisis de transmisiones, se han agregado tres métricas adicionales relacionadas con las funciones. Y son: SOLICITUDES DE FUNCIONES, EVENTOS DE FUNCIONES y SOLICITUDES DE FUNCIONES CON ERRORES, como se muestra en el siguiente gráfico.

![Escalado de Análisis de transmisiones con funciones de Aprendizaje automático: métricas](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-01.png "Escalado de Análisis de transmisiones con funciones de Aprendizaje automático: métricas")

Se definen de la manera siguiente:

**SOLICITUDES DE FUNCIONES**: el número de solicitudes de función.

**EVENTOS DE FUNCIONES**: el número de eventos en las solicitudes de función.

**SOLICITUDES DE FUNCIONES CON ERRORES**: el número de solicitudes de funciones con errores.

## Puntos clave  

Para resumir los puntos principales, para escalar un trabajo de Análisis de transmisiones con funciones de Aprendizaje automático, debe tener en cuenta los siguientes elementos:

1.  La tasa de eventos de entrada.
2.  La latencia permitida para el trabajo de Análisis de transmisiones en ejecución (y, por tanto, el tamaño de lote de las solicitudes de servicio web de Aprendizaje automático).
3.  Las SU de Análisis de transmisiones aprovisionadas y el número de solicitudes de servicio web de Aprendizaje automático (los costos relacionados con funciones adicionales).

Como ejemplo se ha utilizado una consulta de Análisis de transmisiones totalmente particionada. Si se necesita una consulta más compleja, el [foro de Análisis de transmisiones de Azure](https://social.msdn.microsoft.com/Forums/es-ES/home?forum=AzureStreamAnalytics) es un excelente recurso para obtener ayuda adicional del equipo de Análisis de transmisiones.

## Pasos siguientes

Para obtener más información sobre Análisis de transmisiones, vea:

- [Introducción al uso de Análisis de transmisiones de Azure](stream-analytics-get-started.md)
- [Escalación de trabajos de Análisis de transmisiones de Azure](stream-analytics-scale-jobs.md)
- [Referencia del lenguaje de consulta de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referencia de API de REST de administración de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=AcomDC_0323_2016-->