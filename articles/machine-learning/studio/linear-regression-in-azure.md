---
title: "Uso de la regresión lineal en Machine Learning | Microsoft Docs"
description: "Una comparación de los modelos de regresión lineal en Excel y en Estudio de aprendizaje automático de Azure"
metakeywords: 
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 417ae6ab-de4f-4bdd-957a-d96133234656
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: kbaroni;garye
ms.openlocfilehash: 218f2b141e3551180a2152570f99fdb427980dd7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="using-linear-regression-in-azure-machine-learning"></a>Uso de regresión lineal en Aprendizaje automático de Azure
> *Kate Baroni* y *Ben Boatman* son arquitectos de soluciones para empresas del Centro de Excelencia de Perspectivas sobre los datos de Microsoft. En este artículo, se describe su experiencia al migrar un conjunto existente de análisis de regresión a una solución basada en la nube mediante Aprendizaje automático (ML) de Azure. 
> 
> 

&nbsp; 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="goal"></a>Objetivo
Nuestro proyecto se inició con dos objetivos en mente: 

1. Usar el análisis predictivo para mejorar la exactitud de las proyecciones de ingresos mensuales de la organización. 
2. Usar Azure Machine Learning para confirmar, optimizar, aumentar la velocidad y escalar los resultados. 

Como muchas empresas, nuestra organización pasa por un proceso de previsión de ingresos mensuales. Nuestro pequeño equipo de analistas de negocios se encarga de usar Azure Machine Learning para respaldar el proceso y mejorar la precisión de la previsión. El equipo dedicó varios meses a recopilar los datos de varios orígenes y ejecutar los atributos de datos a través de análisis estadísticos a fin de identificar los atributos claves relevantes para la previsión de ventas de servicios. El paso siguiente consistía en comenzar el prototipo de los modelos de regresión estadística de los datos en Excel. En unas semanas, tuvimos un modelo de regresión de Excel que superaba los procesos actuales de previsión de finanzas y de campo. Esto se convirtió en el resultado de la predicción de la línea de base. 

A continuación, el siguiente paso consistió en pasar nuestro análisis predictivo a Azure Machine Learning para averiguar cómo se podría mejorar el rendimiento de las predicciones con Machine Learning.

## <a name="achieving-predictive-performance-parity"></a>Consecución de la paridad en el rendimiento predictivo
Nuestra prioridad era lograr la paridad entre los modelos de regresión de Excel y de Machine Learning. Teniendo en cuenta los mismos datos y la misma división de datos de prueba (test) y entrenamiento (train), queríamos conseguir la paridad de rendimiento predictivo entre Excel y Machine Learning. Al principio no lo conseguimos. El modelo de Excel superaba al de Machine Learning. El error se debía a una falta de conocimiento de la configuración de la herramienta base en Machine Learning. Después de una sincronización con el equipo de producto de Machine Learning, se consiguió una mejor comprensión de la configuración base necesaria para nuestros conjuntos de datos, y se logró la paridad entre los dos modelos. 

### <a name="create-regression-model-in-excel"></a>Creación de un modelo de regresión en Excel
La regresión de Excel utilizaba el modelo de regresión lineal estándar de Excel Analysis ToolPak. 

Calculamos el *porcentaje de error medio absoluto* y se utilizó como medida de rendimiento para el modelo. Tardamos 3 meses en conseguir un modelo operativo con Excel. Aplicamos gran parte de lo aprendido al experimento con Machine Learning Studio, lo que, en última instancia, era una ventaja a la hora de conocer los requisitos.

### <a name="create-comparable-experiment-in-azure-machine-learning"></a>Creación de un experimento comparable en Aprendizaje automático de Azure
Seguimos estos pasos para crear nuestro experimento en Machine Learning Studio: 

1. Cargamos el conjunto de datos como un archivo csv en Machine Learning Studio (archivo muy pequeño).
2. Hemos creado un nuevo experimento y hemos usado el módulo [Seleccionar columnas de conjunto de datos][select-columns] para seleccionar las mismas características de datos que se usaron en Excel. 
3. Usamos el módulo [Dividir datos][split] (con el modo *Expresión relativa*) para dividir los datos en los mismos conjuntos de datos de entrenamiento, tal y como se habría realizado en Excel. 
4. Realizamos experimentos con el módulo [Regresión lineal][linear-regression] (solo opciones predeterminadas), documentamos el proceso y comparamos los resultados con el modelo de regresión de Excel.

### <a name="review-initial-results"></a>Revisión de los resultados iniciales
Al principio, el modelo de Excel superaba claramente al de Machine Learning Studio: 

|  | Excel | Estudio |
| --- |:---:|:---:|
| Rendimiento | | |
| <ul style="list-style-type: none;"><li>R cuadrado ajustado</li></ul> |0,96 |N/D |
| <ul style="list-style-type: none;"><li>Coeficiente de <br />Determinación</li></ul> |N/D |0,78<br />(baja precisión) |
| Error medio absoluto |9,5 millones de $ |19,4 millones de $ |
| Error medio absoluto (%) |6,03 % |12,2 % |

Cuando ejecutamos el proceso y los resultados para los desarrolladores y científicos de datos del equipo de Machine Learning, rápidamente nos proporcionaron algunas sugerencias útiles. 

* Cuando se usa el módulo [Regresión lineal][linear-regression] en Machine Learning Studio, se proporcionan dos métodos:
  * Descenso de gradiente en línea: pueden resultar más adecuado para los problemas a mayor escala.
  * Ordinaria de mínimos cuadrados: este es el método en el que se suele pensar cuando se habla de la regresión lineal. Para los conjuntos de datos más pequeños, la regresión ordinaria de mínimos cuadrados puede ser una opción más adecuada.
* Considere la posibilidad de ajustar el parámetro Peso de regularización L2 para mejorar el rendimiento. Está establecido en 0,001 de forma predeterminada, pero para nuestro pequeño conjunto de datos, lo establecimos en 0,005 para mejorar el rendimiento. 

### <a name="mystery-solved"></a>¡Misterio resuelto!
Al aplicar las recomendaciones, logramos el mismo rendimiento de línea de base en Machine Learning Studio que con Excel: 

|  | Excel | Studio (inicial) | Studio con mínimos cuadrados |
| --- |:---:|:---:|:---:|
| Valor etiquetado |Valores reales (numéricos) |same |same |
| Objetivo del aprendizaje |Excel -> Análisis de datos -> Regresión |Regresión lineal |Regresión lineal |
| Opciones del objetivo del aprendizaje |N/D |Valores predeterminados |ordinaria de mínimos cuadrados<br />L2 = 0,005 |
| Conjunto de datos |26 filas, 3 características, 1 etiqueta. Todas numéricas. |same |same |
| División: aprendizaje |Excel entrenado en las primeras 18 filas; probado en las últimas 8 filas. |same |same |
| División: prueba |Fórmula de regresión de Excel aplicada a las últimas 8 filas. |same |same |
| **Rendimiento** | | | |
| R cuadrado ajustado |0,96 |N/D | |
| Coeficiente de determinación |N/D |0,78 |0,952049 |
| Error medio absoluto |9,5 millones de $ |19,4 millones de $ |9,5 millones de $ |
| Error medio absoluto (%) |<span style="background-color: 00FF00;"> 6,03 %</span> |12,2 % |<span style="background-color: 00FF00;"> 6,03 %</span> |

Además, los coeficientes de Excel son muy similares a los pesos de la característica en el modelo de entrenamiento de Azure:

|  | Coeficientes de Excel | Ponderaciones de la característica de Azure |
| --- |:---:|:---:|
| Intersección/sesgo |19 470 209,88 |19 328 500 |
| Característica A |0,832653063 |0,834156 |
| Característica B |11 071 967,08 |11 007 300 |
| Característica C |25 383 318,09 |25 140 800 |

## <a name="next-steps"></a>Pasos siguientes
Queríamos consumir el servicio web Machine Learning en Excel. Nuestros analistas de negocios se basan en Excel y necesitábamos una manera de llamar al servicio web Machine Learning con una fila de datos de Excel y obtener el valor esperado para Excel. 

También queríamos optimizar nuestro modelo utilizando las opciones y los algoritmos disponibles en Machine Learning Studio.

### <a name="integration-with-excel"></a>Integración con Excel
Nuestra solución fue instrumentar nuestro modelo de regresión de Machine Learning mediante la creación de un servicio web desde el modelo entrenado. En unos minutos, se creó el servicio web y pudimos llamarlo directamente desde Excel para obtener el valor de ingresos previstos. 

La sección *Panel de servicios web* incluye un libro de Excel descargable. El libro contiene información predefinida sobre el esquema y la API de servicio web incrustada. Al hacer clic en *Descargar el libro de Excel*, el libro se abre y puede guardarlo en el equipo local. 

![][1]

Con el libro abierto, copie los parámetros predefinidos en la sección de parámetros de color azul, como se muestra a continuación. Una vez que se especifican los parámetros, Excel llama al servicio web Machine Learning y las etiquetas puntuadas previstas se mostrarán en la sección de valores de predicción de color verde. El libro continuará creando predicciones para los parámetros basándose en el modelo entrenado para todos los elementos de fila especificados en los parámetros. Para obtener más información sobre cómo usar esta característica, consulte [Consumo de un servicio web de Aprendizaje automático de Azure de Excel](consuming-from-excel.md). 

![][2]

### <a name="optimization-and-further-experiments"></a>Optimización y otros experimentos
Ahora que teníamos una línea de base con nuestro modelo de Excel, dimos un paso más para optimizar nuestro modelo de regresión lineal de Machine Learning. Usamos el módulo [Selección de características basada en filtros][filter-based-feature-selection] para mejorar nuestra selección de datos iniciales de elementos. Ello nos ayudó a lograr una mejora del rendimiento del 4,6 % en el error medio absoluto. Para proyectos futuros, utilizaremos esta característica que nos permitirá ahorrar semanas de iteración en los atributos de los datos para buscar el conjunto correcto de características que se utilizará para el modelado. 

A continuación, tenemos previsto incluir algoritmos adicionales como [bayesianos][bayesian-linear-regression] o [árboles de decisiones incrementados][boosted-decision-tree-regression] en nuestro experimento para comparar el rendimiento. 

Si desea experimentar con regresión, un buen conjunto de datos para probar es el conjunto de datos de ejemplo de Energy Efficiency Regression, que tiene muchos atributos numéricos. El conjunto de datos se proporciona como parte de los conjuntos de datos de muestra en Machine Learning Studio. Puede usar diversos módulos de entrenamiento para predecir la carga de calefacción o refrigeración. En el gráfico siguiente, se muestra una comparación de distintos entrenamientos de regresión efectuados con el conjunto de datos Energy Efficiency para predecir una variable de destino sobre la carga de refrigeración: 

| Modelo | Error medio absoluto | Error cuadrático medio | Error absoluto relativo | Error cuadrático relativo | Coeficiente de determinación |
| --- | --- | --- | --- | --- | --- |
| Árbol de decisiones incrementados |0,930113 |1,4239 |0,106647 |0,021662 |0,978338 |
| Regresión lineal (descenso de gradiente) |2,035693 |2,98006 |0,233414 |0,094881 |0,905119 |
| Regresión de red neuronal |1,548195 |2,114617 |0,177517 |0,047774 |0,952226 |
| Regresión lineal (ordinaria de mínimos cuadrados) |1,428273 |1,984461 |0,163767 |0,042074 |0,957926 |

## <a name="key-takeaways"></a>Puntos clave
Hemos aprendido mucho al ejecutar experimentos de regresión en Excel y en Aprendizaje automático de Azure de forma paralela. El hecho de crear un modelo de línea de base en Excel y compararlo con modelos usando la [regresión lineal][linear-regression] de Machine Learning nos permitió aprender sobre Azure Machine Learning. Además, descubrimos oportunidades para mejorar la selección de datos y el modelo de rendimiento. 

También descubrimos que es aconsejable utilizar la [Selección de características basada en filtros][filter-based-feature-selection] para acelerar los proyectos futuros de predicción. Al aplicar la selección de características a los datos, se puede crear un modelo mejorado en Machine Learning con un mejor rendimiento general. 

La capacidad de transferir sistemáticamente la predicción analítica de previsión desde Machine Learning hasta Excel permite aumentar significativamente la capacidad de proporcionar resultados correctos a una extensa audiencia de usuarios empresariales. 

## <a name="resources"></a>Recursos
A continuación, encontrará algunos recursos que le ayudarán a trabajar con la regresión: 

* Regresión en Excel. Si nunca ha intentado realizar la regresión en Excel, este tutorial le facilitará el trabajo: [http://www.excel-easy.com/examples/regression.html](http://www.excel-easy.com/examples/regression.html)
* Regresión frente a previsión. Tyler Chessman escribió un artículo de blog que explica cómo realizar una serie de previsiones de tiempo en Excel. [http://sqlmag.com/sql-server-analysis-services/understanding-time-series-forecasting-concepts](http://sqlmag.com/sql-server-analysis-services/understanding-time-series-forecasting-concepts) 
* Regresión lineal ordinaria con mínimos cuadrados: errores, problemas y riesgos. Introducción y análisis sobre la regresión: [http://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/ ](http://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/)

[1]: ./media/linear-regression-in-azure/machine-learning-linear-regression-in-azure-1.png
[2]: ./media/linear-regression-in-azure/machine-learning-linear-regression-in-azure-2.png


<!-- Module References -->
[bayesian-linear-regression]: https://msdn.microsoft.com/library/azure/ee12de50-2b34-4145-aec0-23e0485da308/
[boosted-decision-tree-regression]: https://msdn.microsoft.com/library/azure/0207d252-6c41-4c77-84c3-73bdf1ac5960/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/

