---
title: "API de detección de anomalías de Azure Machine Learning | Microsoft Docs"
description: "La API de detección de anomalías es un ejemplo integrado en Microsoft Azure Machine Learning que detecta anomalías en los datos de serie temporales con valores numéricos espaciados de manera uniforme en el tiempo."
services: machine-learning
documentationcenter: 
author: alokkirpal
manager: jhubbard
editor: cgronlun
ms.assetid: 52fafe1f-e93d-47df-a8ac-9a9a53b60824
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/05/2017
ms.author: alok;rotimpe
ms.openlocfilehash: e2adfffa00a726fe2c452c25dd777ef054319b04
ms.sourcegitcommit: 0e1c4b925c778de4924c4985504a1791b8330c71
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/06/2018
---
# <a name="machine-learning-anomaly-detection-api"></a>API de detección de anomalías de Machine Learning
## <a name="overview"></a>Información general
La [API de detección de anomalías](https://gallery.cortanaintelligence.com/MachineLearningAPI/Anomaly-Detection-2) es un ejemplo integrado en Azure Machine Learning que detecta anomalías en los datos de serie temporales con valores numéricos espaciados de manera uniforme en el tiempo.

Estas API pueden detectar los siguientes tipos de patrones anómalos en datos de series temporales:

* **Tendencias positivas y negativas**: por ejemplo, al supervisar el uso de memoria en informática, una tendencia al alza puede resultar interesante porque puede ser indicativa de una pérdida de memoria.
* **Cambios en el intervalo dinámico de valores**: por ejemplo, al supervisar las excepciones producidas por un servicio en la nube, cualquier cambio en el intervalo dinámico de valores podría indicar inestabilidad en el estado del servicio.
* **Cambios abruptos e interrupciones**: por ejemplo, al supervisar el número de errores de inicio de sesión en un servicio o el número de finalizaciones de compras en un sitio de comercio electrónico, los cambios abruptos y las interrupciones podrían indicar un comportamiento anormal.

Estos detectores de aprendizaje automático realizan un seguimiento de dichos cambios en los valores con el paso del tiempo e informan de los cambios continuados en sus valores con puntuaciones de anomalías. No requieren el ajuste del umbral ad hoc y sus puntuaciones pueden utilizarse para controlar el índice de falsos positivos. La API de detección de anomalías es útil en varios escenarios, por ejemplo, la supervisión de servicios mediante el seguimiento de KPI en el tiempo, la supervisión del uso mediante métricas como el número de búsquedas o el número de clics y la supervisión del rendimiento mediante contadores como la memoria, la CPU, las lecturas de archivos, etc., en el tiempo.

La oferta de detección de anomalías incluye herramientas útiles para comenzar.

* La [aplicación web](http://anomalydetection-aml.azurewebsites.net/) le ayuda a evaluar y visualizar los resultados de las API de detección de anomalías en los datos.

> [!NOTE]
> Pruebe **IT Anomaly Insights solution** de [esta API](https://gallery.cortanaintelligence.com/MachineLearningAPI/Anomaly-Detection-2).
> 
> Para que esta solución se implemente de extremo a extremo en su suscripción de Azure, <a href="https://gallery.cortanaintelligence.com/Solution/Anomaly-Detection-Pre-Configured-Solution-1" target="_blank">**comience por esta página>**</a>.
> 
>

## <a name="api-deployment"></a>Implementación de la API
Para poder usar la API, debe implementarla en su suscripción de Azure, donde se hospedará como un servicio web Machine Learning.  Puede hacerlo desde la [Galería de Azure AI](https://gallery.cortanaintelligence.com/MachineLearningAPI/Anomaly-Detection-2).  Con esta acción se implementarán dos servicios web Machine Learning (y sus recursos relacionados) en su suscripción de Azure: uno para la detección de anomalías con detección de estacionalidad y otro sin detección de estacionalidad.  Una vez que la implementación haya finalizado, podrá administrar las API desde la página [Azure Machine Learning Web Services](https://services.azureml.net/webservices/) (Servicios web Machine Learning de Azure).  En esta página podrá buscar sus ubicaciones de punto de conexión y las claves de API, así como código de ejemplo para llamar a la API.  Puede encontrar instrucciones más detalladas [aquí](https://docs.microsoft.com/azure/machine-learning/machine-learning-manage-new-webservice).

## <a name="scaling-the-api"></a>Escalado de la aplicación
De forma predeterminada, la implementación tendrá un plan de facturación de desarrollo y pruebas gratuito que incluye 1000 transacciones al mes y 2 horas de proceso al mes.  Puede actualizar a otro plan según sus necesidades.  La información de precios de los distintos planes está disponibles [aquí](https://azure.microsoft.com/en-us/pricing/details/machine-learning/) en "Precios de API web de producción".

## <a name="managing-aml-plans"></a>Administración de los planes de AML 
Puede administrar el plan de facturación [aquí](https://services.azureml.net/plans/).  El nombre del plan tomará como base el nombre del grupo de recursos que eligió al implementar la API, a lo que se añadirá una cadena única para la suscripción.  Las instrucciones sobre cómo actualizar el plan están disponibles [aquí](https://docs.microsoft.com/azure/machine-learning/machine-learning-manage-new-webservice) en la sección "Administración de planes de facturación".

## <a name="api-definition"></a>Definición de la API
El servicio web proporciona API basadas en REST a través de HTTPS que se pueden consumir de varias formas, como una aplicación web o móvil, R, Python, Excel, etc.  Los datos de la serie temporal se envían a este servicio mediante una llamada a la API de REST, y se ejecuta una combinación de los tres tipos de anomalías descritos a continuación.

## <a name="calling-the-api"></a>Llamada a la API
Para poder llamar a la API, debe conocer la ubicación del punto de conexión y la clave de API.  Estos dos elementos, junto con el código de ejemplo para llamar a la API, están disponibles en la página [Azure Machine Learning Web Services](https://services.azureml.net/webservices/) (Servicios web Machine Learning de Azure).  Navegue a la API deseada y, a continuación, haga clic en la pestaña "Consume" (Usar) para buscarlos.  Tenga en cuenta que se puede llamar a la API como una API de Swagger (es decir, con el parámetro de dirección URL `format=swagger`) o como una API no Swagger (es decir, sin el parámetro de dirección URL `format`).  El código de ejemplo utiliza el formato de Swagger.  A continuación se muestra una solicitud de ejemplo y una respuesta en formato no Swagger.  Estos ejemplos son para el punto de conexión de estacionalidad.  El punto de conexión de no estacionalidad es similar.

### <a name="sample-request-body"></a>Cuerpo de solicitud de ejemplo
La solicitud contiene dos objetos: `Inputs` y `GlobalParameters`.  En la solicitud de ejemplo siguiente, algunos parámetros se envían explícitamente mientras que otros no (desplácese hacia abajo para obtener una lista completa de parámetros para cada punto de conexión).  Los parámetros que no se envían explícitamente en la solicitud utilizarán los valores predeterminados que se indican a continuación.

    {
                "Inputs": {
                        "input1": {
                                "ColumnNames": ["Time", "Data"],
                                "Values": [
                                        ["5/30/2010 18:07:00", "1"],
                                        ["5/30/2010 18:08:00", "1.4"],
                                        ["5/30/2010 18:09:00", "1.1"]
                                ]
                        }
                },
        "GlobalParameters": {
            "tspikedetector.sensitivity": "3",
            "zspikedetector.sensitivity": "3",
            "bileveldetector.sensitivity": "3.25",
            "detectors.spikesdips": "Both"
        }
    }

### <a name="sample-response"></a>Respuesta de ejemplo
Tenga en cuenta que, para ver el campo `ColumnNames`, debe incluir `details=true` como un parámetro de dirección URL en la solicitud.  Consulte las tablas siguientes para conocer el significado de cada uno de estos campos.

    {
        "Results": {
            "output1": {
                "type": "table",
                "value": {
                    "Values": [
                        ["5/30/2010 6:07:00 PM", "1", "1", "0", "0", "-0.687952590518378", "0", "-0.687952590518378", "0", "-0.687952590518378", "0"],
                        ["5/30/2010 6:08:00 PM", "1.4", "1.4", "0", "0", "-1.07030497733224", "0", "-0.884548154298423", "0", "-1.07030497733224", "0"],
                        ["5/30/2010 6:09:00 PM", "1.1", "1.1", "0", "0", "-1.30229513613974", "0", "-1.173800281031", "0", "-1.30229513613974", "0"]
                    ],
                    "ColumnNames": ["Time", "OriginalData", "ProcessedData", "TSpike", "ZSpike", "BiLevelChangeScore", "BiLevelChangeAlert", "PosTrendScore", "PosTrendAlert", "NegTrendScore", "NegTrendAlert"],
                    "ColumnTypes": ["DateTime", "Double", "Double", "Double", "Double", "Double", "Int32", "Double", "Int32", "Double", "Int32"]
                }
            }
        }
    }


## <a name="score-api"></a>API de puntuación
La API de puntuación se utiliza para ejecutar la detección de anomalías en datos de series temporales no estacionales. La API ejecuta una serie de detectores de anomalías en los datos y devuelve sus puntuaciones de anomalías. En la siguiente ilustración se muestra un ejemplo de anomalías que puede detectar la API de puntuación. Esta serie temporal tiene 2 cambios de nivel distintos y 3 picos. Los puntos rojos muestran la hora en que se detecta el cambio de nivel, mientras que los puntos negros muestran los picos detectados.
![API de puntuación][1]

### <a name="detectors"></a>Detectores
La API de detección de anomalías admite detectores en tres categorías generales. En la siguiente tabla se pueden encontrar detalles sobre parámetros de entrada específicos y salidas para cada detector.

| Categoría del detector | Detector | DESCRIPCIÓN | Parámetros de entrada | Salidas |
| --- | --- | --- | --- | --- |
| Detectores de pico |Detector de TSpike |Detecta picos y DIP según lo lejos que estén los valores del primer y el tercer cuartil. |*tspikedetector.sensitivity:* toma el valor entero en el intervalo 1-10 (predeterminado: 3); los valores más altos capturarán más valores extremos; por tanto, habrá menos sensibilidad. |TSpike: valores binarios: '1' si se detecta un pico o una interrupción, '0' en caso contrario |
| Detectores de pico | Detector de ZSpike |Detecta picos y DIP en función de lo lejos que estén los puntos de datos de la media. |*zspikedetector.sensitivity:* toma el valor entero en el intervalo 1-10 (predeterminado: 3); los valores más altos capturarán más valores extremos; por tanto, habrá menos sensibilidad. |ZSpike: valores binarios: '1' si se detecta un pico o una interrupción, si no '0' | |
| Detector de tendencia lenta |Detector de tendencia lenta |Detección de tendencia positiva lenta según la sensibilidad establecida |*trenddetector.sensitivity:* umbral en la puntuación del detector (valor predeterminado: 3,25, 3,25 - 5 es un intervalo razonable del que seleccionar este valor; cuanto más alto, menos sensible) |tscore: número flotante que representa la puntuación de anomalías en la tendencia |
| Detectores de cambio de nivel | Detector de cambio de nivel bidireccional |Detección de cambio de nivel ascendente y descendente según la sensibilidad establecida |*bileveldetector.sensitivity* : umbral en la puntuación del detector (valor predeterminado: 3,25, 3,25 - 5 es un intervalo razonable del que seleccionar este valor; cuanto más alto, menos sensible) |rpscore: número flotante que representa la puntuación de anomalía en el cambio de nivel ascendente y descendente | |

### <a name="parameters"></a>Parámetros
En la siguiente tabla se muestra información más detallada sobre estos parámetros de entrada:

| Parámetros de entrada | DESCRIPCIÓN | Configuración predeterminada | type | Intervalo válido | Intervalo sugerido |
| --- | --- | --- | --- | --- | --- |
| detectors.historyWindow |Historial (en número de puntos de datos) utilizado para el cálculo de la puntuación de anomalía |500 |integer |10-2000 |Dependiente de la serie temporal |
| detectors.spikesdips | Si se deben detectar solo subidas, solo bajadas, o ambos |Ambos |enumerated |Ambos, subidas, bajadas |Ambos |
| bileveldetector.sensitivity |Sensibilidad del detector de cambio de nivel bidireccional. |3.25 |double |None |3.25-5 (cuanto menores sean los valores, mayor es la sensibilidad) |
| trenddetector.sensitivity |Sensibilidad del detector de tendencia positiva. |3.25 |double |None |3.25-5 (cuanto menores sean los valores, mayor es la sensibilidad) |
| tspikedetector.sensitivity |Sensibilidad del detector TSpike |3 |integer |1-10 |3-5 (cuanto menores sean los valores, mayor es la sensibilidad) |
| zspikedetector.sensitivity |Sensibilidad del detector ZSpike |3 |integer |1-10 |3-5 (cuanto menores sean los valores, mayor es la sensibilidad) |
| postprocess.tailRows |Número de los puntos de datos más recientes que se van a mantener en los resultados de salida |0 |integer |0 (se mantienen todos los puntos de datos), o especifique el número de puntos para mantener en los resultados |N/D |

### <a name="output"></a>Salida
La API ejecuta todos los detectores en los datos de la serie temporal y devuelve puntuaciones de anomalías e indicadores de picos binarios para cada punto en el tiempo. En la tabla siguiente se muestran los resultados de la API. 

| Salidas | DESCRIPCIÓN |
| --- | --- |
| Hora |Marcas de tiempo de datos sin procesar, o datos agregados (o) atribuidos si se aplica la agregación (o) atribución de los datos que faltan |
| Datos |Valores de datos sin procesar, o datos agregados (o) atribuidos si se aplica la agregación (o) atribución de los datos que faltan |
| TSpike |Indicador binario para indicar si el detector TSpike ha detectado un pico |
| ZSpike |Indicador binario para indicar si el detector ZSpike ha detectado un pico |
| rpscore |Número flotante que representa la puntuación de anomalía en el cambio de nivel bidireccional |
| rpalert |Valor 1/0 que indica que hay una anomalía de cambio de nivel bidireccional basada en la sensibilidad de entrada |
| tscore |Número flotante que representa la puntuación de anomalía en tendencia positiva |
| talert |Valor 1/0 que indica que hay una anomalía de tendencia positiva basada en la sensibilidad de entrada |

## <a name="scorewithseasonality-api"></a>La API ScoreWithSeasonality
La API ScoreWithSeasonality se utiliza para ejecutar la detección de anomalías en series temporales que tienen patrones estacionales. Esta API es útil para detectar desviaciones en los patrones estacionales.  
En la siguiente ilustración se muestra un ejemplo de anomalías detectadas en una serie temporal estacional. La serie temporal tiene un pico (el primer punto negro), dos interrupciones (el segundo punto negro y otro al final) y un cambio de nivel (punto rojo). Tenga en cuenta que tanto la interrupción en el medio de la serie temporal como el cambio de nivel son solo apreciables después de que los componentes estacionales se eliminan de la serie.
![API de estacionalidad][2]

### <a name="detectors"></a>Detectores
Los detectores en el punto de conexión de estacionalidad son similares a los del punto de conexión de no estacionalidad, pero con nombres de parámetro ligeramente diferentes (incluidos más abajo).

### <a name="parameters"></a>Parámetros

En la siguiente tabla se muestra información más detallada sobre estos parámetros de entrada:

| Parámetros de entrada | DESCRIPCIÓN | Configuración predeterminada | type | Intervalo válido | Intervalo sugerido |
| --- | --- | --- | --- | --- | --- |
| preprocess.aggregationInterval |Intervalo de agregación en segundos para agregar series temporales de entrada |0 (no se realiza ninguna agregación) |integer |0: omitir agregación, de lo contrario, > 0 |De 5 minutos a 1 día, dependiente de la serie temporal |
| preprocess.aggregationFunc |Función que se usa para agregar datos al objeto AggregationInterval especificado |mean |enumerated |mean, sum, length |N/D |
| preprocess.replaceMissing |Valores que se utiliza para atribuir los datos que faltan |lkv (último valor conocido) |enumerated |zero, lkv, mean |N/D |
| detectors.historyWindow |Historial (en número de puntos de datos) utilizado para el cálculo de la puntuación de anomalía |500 |integer |10-2000 |Dependiente de la serie temporal |
| detectors.spikesdips | Si se deben detectar solo subidas, solo bajadas, o ambos |Ambos |enumerated |Ambos, subidas, bajadas |Ambos |
| bileveldetector.sensitivity |Sensibilidad del detector de cambio de nivel bidireccional. |3.25 |double |None |3.25-5 (cuanto menores sean los valores, mayor es la sensibilidad) |
| postrenddetector.sensitivity |Sensibilidad del detector de tendencia positiva. |3.25 |double |None |3.25-5 (cuanto menores sean los valores, mayor es la sensibilidad) |
| negtrenddetector.sensitivity |Sensibilidad del detector de tendencia negativa. |3.25 |double |None |3.25-5 (cuanto menores sean los valores, mayor es la sensibilidad) |
| tspikedetector.sensitivity |Sensibilidad del detector TSpike |3 |integer |1-10 |3-5 (cuanto menores sean los valores, mayor es la sensibilidad) |
| zspikedetector.sensitivity |Sensibilidad del detector ZSpike |3 |integer |1-10 |3-5 (cuanto menores sean los valores, mayor es la sensibilidad) |
| seasonality.enable |Si se realizará el análisis de estacionalidad |true |boolean |true, false |Dependiente de la serie temporal |
| seasonality.numSeasonality |Número máximo de ciclos periódicos para detectar |1 |integer |1, 2 |1-2 |
| seasonality.transform |Si los componentes de tendencia (y) estacionales se eliminarán antes de aplicar la detección de anomalías |deseason |enumerated |none, deseason, deseasontrend |N/D |
| postprocess.tailRows |Número de los puntos de datos más recientes que se van a mantener en los resultados de salida |0 |integer |0 (se mantienen todos los puntos de datos), o especifique el número de puntos para mantener en los resultados |N/D |

### <a name="output"></a>Salida
La API ejecuta todos los detectores en los datos de la serie temporal y devuelve puntuaciones de anomalías e indicadores de picos binarios para cada punto en el tiempo. En la tabla siguiente se muestran los resultados de la API. 

| Salidas | DESCRIPCIÓN |
| --- | --- |
| Hora |Marcas de tiempo de datos sin procesar, o datos agregados (o) atribuidos si se aplica la agregación (o) atribución de los datos que faltan |
| OriginalData |Valores de datos sin procesar, o datos agregados (o) atribuidos si se aplica la agregación (o) atribución de los datos que faltan |
| ProcessedData |Cualquiera de las siguientes:  <ul><li>Serie temporal ajustada estacionalmente si se ha detectado una estacionalidad importante y se ha seleccionado la opción deseason.</li><li>Serie temporal ajustada estacionalmente y con anulación de tendencia (detrended) si se ha detectado una estacionalidad importante y se ha seleccionado la opción deseasontrend;</li><li>de lo contrario, es igual a OriginalData.</li> |
| TSpike |Indicador binario para indicar si el detector TSpike ha detectado un pico |
| ZSpike |Indicador binario para indicar si el detector ZSpike ha detectado un pico |
| BiLevelChangeScore |Número flotante que representa la puntuación de anomalía en el cambio de nivel |
| BiLevelChangeAlert |Valor 1/0 que indica que hay una anomalía de cambio de nivel basada en la sensibilidad de entrada |
| PosTrendScore |Número flotante que representa la puntuación de anomalía en tendencia positiva |
| PosTrendAlert |Valor 1/0 que indica que hay una anomalía de tendencia positiva basada en la sensibilidad de entrada |
| NegTrendScore |Número flotante que representa la puntuación de anomalía en tendencia negativa |
| NegTrendAlert |Valor 1/0 que indica que hay una anomalía de tendencia negativa basada en la sensibilidad de entrada |

[1]: ./media/apps-anomaly-detection-api/anomaly-detection-score.png
[2]: ./media/apps-anomaly-detection-api/anomaly-detection-seasonal.png

