---
title: "Uso compartido de vistas personalizadas de Azure Time Series Insights con direcciones URL con parámetros | Microsoft Docs"
description: "En este artículo se describe cómo desarrollar direcciones URL con parámetros en Azure Time Series Insights, de forma que una vista de cliente pueda compartirse fácilmente."
services: time-series-insights
ms.service: time-series-insights
author: MarkMcGeeAtAquent
ms.author: MarkMcGeeAtAquent
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: rest-api
ms.topic: get-started-article
ms.workload: big-data
ms.date: 11/21/2017
ms.openlocfilehash: b7c58697323ec12ac08575916cb3ac5b38cc39c1
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="share-a-custom-view-using-a-parameterized-url"></a>Uso compartido de una vista personalizada mediante una dirección URL con parámetros

Para compartir una vista personalizada en el explorador de Time Series Insights, puede crear una dirección URL con parámetros de la vista personalizada mediante programación.

El explorador de Time Series Insights admite parámetros de consulta de dirección URL para especificar vistas para compartir directamente desde la dirección URL.  Por ejemplo, con solo la dirección URL, puede especificar un entorno de destino, un predicado de búsqueda y un intervalo de tiempo deseado. Cuando un usuario hace clic en la dirección URL personalizada, la interfaz proporciona un vínculo directo a dicho recurso en el portal de Time Series Insights.  Se aplican directivas de acceso a datos. 

## <a name="environment-id"></a>Identificador de entorno

El parámetro `environmentId=<guid>` especifica el identificador de entorno de destino.  Se trata de un componente del FQDN de acceso a datos y se puede encontrar en la esquina superior derecha de la información general del entorno de Azure Portal.  Es todo lo que va delante de `env.timeseries.azure.com`. Un ejemplo de un parámetro de identificador de entorno es `?environmentId=10000000-0000-0000-0000-100000000108`.

## <a name="time"></a>Hora

Puede especificar valores de tiempos absolutos o relativos con una dirección URL con parámetros.

### <a name="absolute-time-values"></a>Valores de tiempo absolutos

Con los valores de tiempo absolutos, use los parámetros `from=<integer>` y `to=<integer>`. 

`from=<integer>` es un valor de JavaScript en milisegundos de la hora de inicio del intervalo de búsqueda.

`to=<integer>` es un valor de JavaScript en milisegundos de la hora de finalización del intervalo de búsqueda. 

Para identificar los milisegundos de JavaScript para una fecha, consulte [Epoch & Unix Timestamp Converter](https://www.freeformatter.com/epoch-timestamp-to-date-converter.html) (Convertidor de marca de tiempo de Epoch y Unix).

### <a name="relative-time-values"></a>Valores de tiempo relativos

Con valores de tiempo relativos, use `relativeMillis=<value>`, donde *value* son los milisegundos de JavaScript a partir de los datos más recientes en el back-end.

Por ejemplo, `&relativeMillis=3600000` muestra los 60 minutos de datos más recientes.

Los valores aceptados corresponden al menú de **tiempo rápido** del explorador de Time Series Insights, e incluyen los siguientes:

- 1800000 (últimos 30 minutos)
- 3600000 (últimos 60 minutos)
- 10800000 (últimas 3 horas)
- 21600000 (últimas 6 horas)
- 43200000 (últimas 12 horas)
- 86400000 (últimas 24 horas)
- 604800000 (últimos 7 días)
- 2592000000 (últimas 30 horas)

### <a name="optional-parameters"></a>Parámetros opcionales

El parámetro `timeSeriesDefinitions=<collection of term objects>` especifica los términos de una vista de Time Series Insights, donde:

- `name=<string>`
  - El nombre del *término*.
- `splitBy=<string>`
  - El nombre de columna para *dividido por*.
- `measureName=<string>`
  - El nombre de columna de *medida*.
- `predicate=<string>`
  - La cláusula *where* para el filtrado en el lado del servidor.

El parámetro multiChartStack = <true/false>' permite el apilamiento en el gráfico, mientras que el parámetro 'multiChartSameScale = <true/false>' permite la misma escala del eje Y en los términos de un parámetro opcional.  

- 'multiChartStack=false'
  - 'True' está habilitado de forma predeterminada, así que pase pasa 'false' a la pila.
- 'multiChartStack=false&multiChartSameScale=true' 
  - Para usar la misma escala del eje Y en todos los términos es preciso habilitar el apilamiento.  De forma predeterminada es 'false', por lo que si se pasa 'true' se habilita esta funcionalidad.  
  
'timeBucketUnit =<Unit>&timeBucketSize =<integer>' permite ajustar el control deslizante de intervalos para proporcionar una vista más pormenorizada y agregada del gráfico.  
- 'timeBucketUnit=<Unit>&timeBucketSize=<integer>'
  - Unidades = días, horas, minutos, segundos y milisegundos.  La unidad siempre debe escribirse en mayúsculas.
  - Para definir el número de unidades, pase el entero que desea a timeBucketSize.  Tenga en cuenta que puede agregar hasta de 7 días.  
  
El parámetro 'timezoneOffset =<integer>' permite establecer la zona horaria del gráfico que se va a ver como un desplazamiento a la hora UTC.  
  - 'timezoneOffset=-<integer>'
    - El entero siempre está en milisegundos.  
    - Tenga en cuenta que esta funcionalidad no es exactamente igual que la que se habilita en el explorador TSI, donde se le permiten elegir entre local (hora del explorador) y UTC.  
 
### <a name="examples"></a>Ejemplos

Por ejemplo, para agregar definiciones de series temporales como un parámetro de dirección URL, puede usar lo siguiente:

```https
&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},
{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

Con estos ejemplos de definiciones de series temporales para 

- el identificador de entorno
- los últimos 60 minutos de datos
- los términos (F1PressureID, F2TempStation y F3VibrationPL) que componen los parámetros opcionales
 
puede construir la siguiente dirección URL con parámetros para una vista:

```https
https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

Si hubiera usado el explorador de Time Series Insights para crear la vista descrita por la dirección URL anterior, tendría este aspecto:

![Términos del explorador de Time Series Insights](media/parameterized-url/url1.png)

La vista completa (incluido el gráfico) tendría este aspecto:

![Vista de diagrama](media/parameterized-url/url2.png)

## <a name="next-steps"></a>Pasos siguientes
[Consulta de datos mediante C#](time-series-insights-query-data-csharp.md)
