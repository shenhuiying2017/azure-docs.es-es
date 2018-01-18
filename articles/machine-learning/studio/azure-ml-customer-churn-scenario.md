---
title: "Análisis del abandono de clientes mediante Machine Learning | Microsoft Docs"
description: "Caso práctico de desarrollo de un modelo integrado para analizar y puntuar el abandono de clientes"
services: machine-learning
documentationcenter: 
author: jeannt
manager: jhubbard
editor: cgronlun
ms.assetid: 1333ffe2-59b8-4f40-9be7-3bf1173fc38d
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/18/2017
ms.author: jeannt
ms.openlocfilehash: e0b82fe8e8c8bc4ac9c45370d90fa9330d749878
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/08/2018
---
# <a name="analyzing-customer-churn-by-using-azure-machine-learning"></a>Análisis del el abandono de clientes mediante Azure Machine Learning
## <a name="overview"></a>Información general
En este artículo se presenta una implementación de referencia de un proyecto de análisis de pérdida de clientes creado mediante Azure Machine Learning. Se describen los modelos asociados genéricos para solucionar holísticamente el problema de pérdida de clientes industrial. También medimos la precisión de los modelos generados mediante Machine Learning y evaluamos instrucciones para su posterior desarrollo.  

### <a name="acknowledgements"></a>Agradecimientos
Este experimento lo desarrolló y probó Serge Berger, principal científico de datos de Microsoft y Roger Barga, anterior director de productos para Microsoft Azure Machine Learning. El equipo de documentación de Azure quiere expresar su agradecimiento por los conocimientos aportados y por compartir estas notas del producto.

> [!NOTE]
> Los datos utilizados para este experimento no están disponibles públicamente. Para ver un ejemplo de cómo crear un modelo de Machine Learning de análisis de abandono de clientes, consulte [Retail churn model template](https://gallery.cortanaintelligence.com/Collection/Retail-Customer-Churn-Prediction-Template-1) (Plantilla de modelo de abandono de clientes minoristas) de [Azure AI Gallery](http://gallery.cortanaintelligence.com/).
> 
> 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="the-problem-of-customer-churn"></a>El problema del abandono de clientes
Los negocios del mercado de consumidores y de todos los sectores empresariales han de tratar con el abandono de clientes. En ocasiones el abandono es excesivo e influye sobre las decisiones políticas. La solución tradicional pasa por predecir a los clientes con una alta propensión a abandonar y abordar sus necesidades a través de un servicio de asistencia personal, campañas de marketing o mediante la aplicación de exenciones especiales. Estos enfoques pueden variar de un sector a otro. Pueden variar de un clúster determinado de consumidor a otro dentro de un sector (por ejemplo, el de telecomunicaciones).

El factor común es que las empresas necesitan reducir estos esfuerzos especiales de retención de clientes. Por lo tanto, una metodología natural sería puntuar a cada cliente con la probabilidad de abandono y abordar los N principales. Los clientes principales podrían ser los más rentables. Por ejemplo, en escenarios más sofisticados, una función de beneficios se emplea durante la selección de candidatos a exenciones especiales. Sin embargo, estos planteamientos son solo una parte de la estrategia completa de tratar con el abandono. Las empresas también tienen que tener en cuenta el riesgo (y la tolerancia a él asociada), el nivel y el coste de la intervención y la posible segmentación de los clientes.  

## <a name="industry-outlook-and-approaches"></a>Perspectiva y enfoques de la industria
La gestión del abandono de forma adecuada es signo de madurez industrial. El ejemplo clásico es la industria de las telecomunicaciones, donde se sabe que los abonados cambian con frecuencia de un operador a otro. Este abandono voluntario es realmente preocupante. Además, los operadores han acumulado importantes conocimientos sobre los *factores del abandono*, que son los determinantes que impulsan a los consumidores a cambiar.

Por ejemplo, la elección del terminal o dispositivo es un factor conocido de abandono en el negocio de los teléfonos móviles. Como resultado, una directiva popular es subvencionar el precio de un auricular para los nuevos suscriptores y cobrarlo a precio normal a los clientes ya existentes para obtener una actualización. Tradicionalmente, esa política ha llevado a los clientes a saltar de un operador a otro para obtener un nuevo descuento. Esto, a su vez, ha provocado que los operadores refinen sus estrategias.

La alta volatilidad en las ofertas de terminales es un factor que invalida de manera rápida los modelos de abandono que se basan en los modelos de terminales actuales. Además, los teléfonos móviles no son solo dispositivos de telecomunicaciones, también son artículos de moda (por ejemplo, el iPhone). Estas predicciones sociales están fuera del ámbito de los conjuntos de datos de telecomunicaciones normales.

Como resultado, no se puede idear una buena política eliminando simplemente las razones conocidas del abandono. De hecho, es **obligatorio**establecer una estrategia de modelado continua, que incluye modelos clásicos que cuantifican variables de categorías (por ejemplo, árboles de decisión).

Mediante el uso de conjuntos de Big Data en sus clientes, las organizaciones están realizando análisis de Big Data, en especial, la detección de abandono basada en Big Data, como un enfoque eficaz sobre el problema. Puede encontrar más información sobre el enfoque de datos de gran tamaño al problema del abandono en la sección Recomendaciones sobre ETL.  

## <a name="methodology-to-model-customer-churn"></a>Metodología para modelar el abandono de clientes
En las figuras 1-3 se describe un proceso de solución de problemas común para resolver el tema del abandono de clientes:  

1. Un modelo de riesgo le permite considerar el modo en que las acciones influyen en la probabilidad y el riesgo.
2. Un modelo de intervención le permite considerar cómo el nivel de intervención podría afectar a la probabilidad de abandono y la cantidad de valor de tiempo de vida del cliente (CLV).
3. Este análisis se presta a un análisis cualitativo que se remite a una campaña de marketing proactiva destinada a segmentos de clientes para ofrecer la oferta óptima.  

![][1]

Este enfoque de previsión es la mejor manera de tratar el abandono, pero resulta complejo: tenemos que desarrollar un arquetipo de varios modelos y realizar un seguimiento de las dependencias entre los modelos. La interacción entre los modelos se puede encapsular como se muestra en el siguiente diagrama:  

![][2]

*Ilustración 4: Arquetipo basado en varios modelos unificado*  

La interacción entre los modelos es clave si vamos a proporcionar un enfoque holístico sobre la retención de clientes. Cada modelo se degrada necesariamente con el paso del tiempo; por lo tanto, la arquitectura es un bucle implícito (similar al arquetipo establecido por el estándar de minería de datos de CRISP-DM, [***3***]).  

A pesar de todo, el ciclo general de segmentación/descomposición de marketing de decisión de riesgo es una estructura generalizada, que es aplicable a muchos problemas empresariales. El análisis del abandono es simplemente un representante fuerte de este grupo de problemas, dado que presenta todos los rasgos de un problema empresarial complejo que no permite una solución predictiva simplificada. Los aspectos sociales del enfoque moderno hacia el abandono no se destacan particularmente en el enfoque, pero están encapsulados en el arquetipo de creación de modelos como lo estarían en cualquier modelo.  

Un interesante aporte aquí es el análisis de Big Data. Las empresas de telecomunicaciones y los comercios minoristas de hoy en día recopilan datos exhaustivos sobre sus clientes, y podemos prever fácilmente que la necesidad de conectividad entre varios modelos se convertirá en una tendencia común, dadas las tendencias emergentes como Internet de los objetos y los dispositivos multifuncionales, que permiten a las empresas emplear soluciones inteligentes a varios niveles.  

 

## <a name="implementing-the-modeling-archetype-in-machine-learning-studio"></a>Implementación del arquetipo de modelado en Machine Learning Studio
Dado el problema que acabamos de describir, ¿cuál es la mejor forma de implementar un enfoque integrado de modelos y puntuación? En esta sección, demostraremos cómo lo hemos conseguido mediante Azure Machine Learning Studio.  

El enfoque de varios modelos es imprescindible a la hora de diseñar un arquetipo global para el abandono. Incluso la parte de puntuación (predictiva) del enfoque debe basarse en varios modelos.  

El siguiente diagrama muestra el prototipo que creamos, que emplea cuatro algoritmos puntuación en Machine Learning Studio para predecir el abandono. El motivo de utilizar un enfoque de varios modelos es no crear únicamente un clasificador de conjunto para aumentar la precisión, sino también protegerse frente al exceso de ajustes y mejorar la selección preceptiva de características.  

![][3]

*Ilustración 5: Prototipo de un enfoque de creación de modelos de abandono*  

Las secciones siguientes proporcionan más detalles sobre el prototipo de modelo de puntuación que hemos implementado mediante Machine Learning Studio.  

### <a name="data-selection-and-preparation"></a>Selección y preparación de los datos
Los datos usados para crear los modelos y puntuar a los clientes se obtuvieron de una solución vertical de CRM, pero se ocultaron para proteger la privacidad de los clientes. Los datos contienen información acerca de las 8000 suscripciones en Estados Unidos y combina tres orígenes: datos de aprovisionamiento (metadatos de suscripción), datos de actividad (uso del sistema) y datos de soporte al cliente. Los datos no incluyen ninguna información sobre los clientes relativa a la empresa; por ejemplo, no incluye metadatos de fidelización ni capacidad crediticia.  

Por motivos de simplicidad, los procesos de ETL y de limpieza de datos escapan de nuestro ámbito dado que se supone que la preparación de los datos ya se ha realizado en otra parte.   

La selección de características para la creación de modelos se basa en la puntuación de importancia preliminar del conjunto de indicadores, incluido en el proceso mediante el uso del módulo de bosque aleatorio. Para la implementación en Machine Learning Studio, hemos calculado el promedio, la media y rangos de funciones representativas. Por ejemplo, hemos agregado las sumas de los datos cualitativos, como los valores máximos y mínimos de la actividad del usuario.    

Hemos capturado también información de tiempo correspondiente a los últimos seis meses. Hemos analizado los datos durante un año y hemos establecido que incluso si hubiera tendencias estadísticamente significativas, el efecto en el abandono disminuye considerablemente después de seis meses.  

El aspecto más importante es que el proceso entero, incluidos el ETL, la selección de funciones y la creación de modelos se implementó en Machine Learning Studio mediante el uso de fuentes de datos de Microsoft Azure.   

Los siguientes diagramas ilustran los datos usados.  

![][4]

*Ilustración 6: Extracto de la fuente de datos (oculta)*  

![][5]

*Ilustración 7: Características extraídas de la fuente de datos*
 

> Tenga en cuenta que estos datos son privados y, por tanto, no se pueden compartir el modelo y los datos.
> Pero, para obtener un modelo similar utilizando los datos disponibles públicamente, consulte este experimento de ejemplo en [Azure AI Gallery](http://gallery.cortanaintelligence.com/): [Telco Customer Churn](http://gallery.cortanaintelligence.com/Experiment/31c19425ee874f628c847f7e2d93e383) (Abandono de clientes en las empresas de telecomunicaciones).
> 
> Para obtener más información sobre cómo se puede implementar un modelo de análisis de pérdida de clientes con Cortana Intelligence Suite, también se recomienda ver [este vídeo](https://info.microsoft.com/Webinar-Harness-Predictive-Customer-Churn-Model.html) del director de programas Wee Hyong Tok. 
> 
> 

### <a name="algorithms-used-in-the-prototype"></a>Algoritmos usados en el prototipo
Usamos los siguientes cuatro algoritmos de aprendizaje automático para crear el prototipo (sin personalización):  

1. Regresión logística (LR)
2. Árbol de decisiones ampliado (BT)
3. Perceptron promediado (AP)
4. Máquina de vectores de soporte (SVM)  

El siguiente diagrama ilustra una parte de la superficie de diseño del experimento, que indica la secuencia en la que se crearon los modelos:  

![][6]  

*Ilustración 8: Creación de modelos en Machine Learning Studio*  

### <a name="scoring-methods"></a>Métodos de puntuación
Puntuamos a los cuatro modelos mediante el uso de un conjunto de datos de entrenamiento etiquetado.  

También enviamos el conjunto de datos de puntuaciones a un modelo comparable creado mediante la edición de escritorio de SAS Enterprise Miner 12. Hemos medido la exactitud del modelo SAS y de los cuatro modelos de Machine Learning Studio.  

## <a name="results"></a>Results
En esta sección, presentamos nuestros hallazgos sobre la exactitud de los modelos, según el conjunto de datos de puntuación.  

### <a name="accuracy-and-precision-of-scoring"></a>Exactitud y precisión de la puntuación
Por lo general, la implementación de Azure Machine Learning está, aproximadamente, entre un 10 y un 15 % por debajo de SAS en cuanto a precisión (área bajo la curva o AUC).  

Sin embargo, la estadística más importante en el abandono es el índice de clasificaciones incorrectas: es decir, de los N primeros usuarios que el clasificador predijo que abandonarían, ¿cuáles de ellos **no** han abandonado realmente y, a pesar de todo, han recibido un tratamiento especial? En el diagrama siguiente se compara este índice de clasificaciones incorrectas para todos los modelos:  

![][7]

*Ilustración 9: Área del prototipo de Passau situada bajo la curva*

### <a name="using-auc-to-compare-results"></a>Uso de AUC para comparar los resultados
El área bajo la curva (AUC) es una métrica que representa una medida global de *separabilidad* entre las distribuciones de las puntuaciones para poblaciones positivas y negativas. Aunque guarda similitudes con el gráfico ROC (Receiver Operator Characteristic, Característica operativa del receptor), una diferencia importante es que la métrica AUC no requiere que se elija un valor de umbral. En su lugar, lo que hace es resumir los resultados de **todas** las opciones posibles. En contraposición, el gráfico ROC tradicional muestra la tasa de positivos en el eje vertical y la tasa de falsos positivos en el eje horizontal, y el umbral de clasificación varía.   

AUC se usa generalmente como medida de valor en diferentes algoritmos (o diferentes sistemas), dado que permite que se comparen los modelos por medio de sus valores de AUC. Se trata de un enfoque popular en sectores como la meteorología y las biociencias. Por lo tanto, AUC representa una herramienta generalizada para la evaluación del rendimiento del clasificador.  

### <a name="comparing-misclassification-rates"></a>Comparación de las tasas de clasificaciones incorrectas
Hemos comparado las tasas de errores de clasificación del conjunto de datos en cuestión mediante los datos de CRM de 8000 abonados aproximadamente.  

* La tasa de errores de clasificación de SAS fue del 10-15 %.
* La tasa de clasificaciones incorrectas de Machine Learning Studio estaba comprendida entre el 15 y el 20% para los primeros 200 a 300 clientes que abandonan.  

En el sector de las telecomunicaciones, es importante dirigirse solo a esos clientes con mayor riesgo de abandono y ofrecerles un servicio de asistencia personal u otro tratamiento especial. En ese sentido, la implementación de Machine Learning Studio obtiene resultados a la par del modelo SAS.  

Del mismo modo, la exactitud es más importante que la precisión porque estamos interesados principalmente en clasificar de forma correcta a los posibles clientes que abandonan.  

El siguiente diagrama de Wikipedia representa la relación en un gráfico animado fácil de entender:  

![][8]

*Ilustración 10: Balance entre exactitud y precisión*

### <a name="accuracy-and-precision-results-for-boosted-decision-tree-model"></a>Resultados de precisión del modelo de árbol de decisiones ampliado
El siguiente gráfico muestra los resultados sin formato desde la puntuación utilizando el prototipo de Machine Learning para el modelo de árbol de decisión ampliado, que resulta ser el más preciso entre los cuatro modelos de puntuación:  

![][9]

*Ilustración 11: Características del modelo de árbol de decisión ampliado*

## <a name="performance-comparison"></a>Comparación del rendimiento
Hemos comparado la velocidad a la que se han calificado los datos mediante Machine Learning Studio y un modelo comparable creado con la edición de escritorio de SAS Enterprise Miner 12.1.  

En la siguiente tabla se resume el rendimiento de los algoritmos:  

*Tabla 1. Rendimiento general (precisión) de los algoritmos*

| LR | BT | AP | SVM |
| --- | --- | --- | --- |
| Modelo promedio |El mejor modelo |Déficit de rendimiento |Modelo promedio |

Los modelos hospedados en Machine Learning Studio superaron en rendimiento a SAS en un 15-25% en cuanto a velocidad de ejecución, pero en cuanto a precisión, estaban a la par.  

## <a name="discussion-and-recommendations"></a>Debate y recomendaciones
En el sector de las telecomunicaciones, han surgido varias prácticas para analizar el abandono, entre las que se incluyen:  

* Las métricas se pueden clasificar en cuatro categorías fundamentales:
  * **Entidad (por ejemplo, una suscripción)**. Proporcione información básica sobre el abonado y/o cliente que es el sujeto del abandono.
  * **Actividad**. Obtenga toda la información de uso posible relacionada con la entidad, por ejemplo, el número de inicios de sesión.
  * **Servicio al cliente**. Recoja información de los registros de servicio al cliente para indicar si el abonado tuvo problemas o interacciones con el servicio al cliente.
  * **Datos competitivos y empresariales**. Obtenga cualquier información posible acerca del cliente (por ejemplo, puede no estar disponible o ser difícil de seguir).
* Importancia de uso para conducir la selección de características. Esto implica que el modelo de árbol de decisión ampliado es siempre es un enfoque prometedor.  

El uso de estas cuatro categorías crea la ilusión de que un enfoque *determinista* sencillo, basado en índices creados sobre factores razonables por categoría, debería ser suficiente para identificar a los clientes con riesgo de abandono. Por desgracia, aunque esta noción parece plausible, es una concepción errónea. El motivo es que al abandono es un efecto temporal y los factores que contribuyen a él están normalmente en estado transitorio. Lo que lleva a un cliente a plantearse la idea de abandonar hoy puede ser diferente mañana y seguro que será diferente en los próximos seis meses. Por lo tanto, un modelo *probabilístico* es una necesidad.  

Esta importante observación con frecuencia se pasa por alto en las empresas, quienes prefieren generalmente un enfoque del análisis orientado a la inteligencia empresarial, principalmente porque es más comercial y admite una automatización sencilla.  

Sin embargo, la promesa del análisis de autoservicio mediante Machine Learning Studio es que las cuatro categorías de información, clasificadas por división o departamento, se convierten en una valiosa fuente de aprendizaje automático sobre el abandono.  

Otra capacidad interesante disponible en Azure Machine Learning es la capacidad de agregar un módulo personalizado al repositorio de módulos predefinidos que ya están disponibles. Esa función crea básicamente una oportunidad para seleccionar bibliotecas y crear plantillas para los mercados verticales. Es un elemento diferenciador importante de Azure Machine Learning en el mercado.  

Esperamos seguir con este tema en el futuro, especialmente en lo relacionado con el análisis de macrodatos.
  

## <a name="conclusion"></a>Conclusión
En este documento se describe un enfoque sensato para abordar el problema común del abandono de clientes mediante el uso de un marco genérico. Hemos considerado un prototipo para puntuar modelos y lo hemos implementado mediante Azure Machine Learning. Finalmente, hemos evaluado la exactitud y el rendimiento del prototipo con respecto a algoritmos comparables en SAS.  

 

## <a name="references"></a>Referencias
[1] Predictive Analytics: Beyond the Predictions (Análisis predictivo, más allá de las predicciones), W. McKnight, Information Management, julio/agosto de 2011, pp. 18-20.  

[2] Artículo de Wikipedia: [Precisión y exactitud](http://en.wikipedia.org/wiki/Accuracy_and_precision)

[3] [CRISP-DM 1.0: Step-by-Step Data Mining Guide](http://www.the-modeling-agency.com/crisp-dm.pdf) (Metodología CRISP-DM 1.0: Guía paso a paso de minería de datos)   

[4] [[Big Data Marketing: Engage Your Customers More Effectively and Drive Value]](http://www.amazon.com/Big-Data-Marketing-Customers-Effectively/dp/1118733894/ref=sr_1_12?ie=UTF8&qid=1387541531&sr=8-12&keywords=customer+churn) (Marketing de macrodatos: atraer más eficazmente a los clientes e impulsar el valor)

[5] [Telco churn model template](http://gallery.cortanaintelligence.com/Experiment/Telco-Customer-Churn-5) (Plantilla de modelo de abandono de clientes de telecomunicaciones) en [Azure AI Gallery](http://gallery.cortanaintelligence.com/) 
 

## <a name="appendix"></a>Anexo
![][10]

*Ilustración 12. Instantánea de una presentación sobre un prototipo de abandono*

[1]: ./media/azure-ml-customer-churn-scenario/churn-1.png
[2]: ./media/azure-ml-customer-churn-scenario/churn-2.png
[3]: ./media/azure-ml-customer-churn-scenario/churn-3.png
[4]: ./media/azure-ml-customer-churn-scenario/churn-4.png
[5]: ./media/azure-ml-customer-churn-scenario/churn-5.png
[6]: ./media/azure-ml-customer-churn-scenario/churn-6.png
[7]: ./media/azure-ml-customer-churn-scenario/churn-7.png
[8]: ./media/azure-ml-customer-churn-scenario/churn-8.png
[9]: ./media/azure-ml-customer-churn-scenario/churn-9.png
[10]: ./media/azure-ml-customer-churn-scenario/churn-10.png
