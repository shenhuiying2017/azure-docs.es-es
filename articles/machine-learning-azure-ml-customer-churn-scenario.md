<properties title="Analyzing Customer Churn using Microsoft Azure Machine Learning" pageTitle="Análisis del abandono de clientes mediante el Aprendizaje automático de Microsoft | Azure" description="Caso práctico de desarrollo de un modelo integrado para analizar y puntuar la renovación de clientes" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="pamehta" manager="paulettm" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/06/2014" ms.author="barga" />

# Análisis del abandono de clientes mediante el Aprendizaje automático de Microsoft Azure


En este documento se presenta una implementación de referencia de un proyecto de análisis del abandono de clientes creado con el Estudio de aprendizaje automático de Azure, y se analizan los modelos genéricos asociados para la solución del problema de manera holística en el marco de la industria. También medimos la precisión de los modelos creados mediante el Aprendizaje automático de Azure y evaluamos las direcciones hacia una mejora en el desarrollo.  

##El problema del abandono de clientes
Los negocios del mercado de consumidores y de todos los sectores empresariales han de tratar con el abandono de clientes. En ocasiones el abandono es excesivo e influye sobre las decisiones políticas. La solución tradicional pasa por predecir a los clientes con una alta propensión a abandonar y abordar sus necesidades a través de un servicio de asistencia personal, campañas de marketing o mediante la aplicación de exenciones especiales. Estos enfoques pueden variar de un sector a otro o incluso de un grupo de consumidores a otro dentro de un sector (por ejemplo, el de las telecomunicaciones). El factor común es que las empresas necesitan reducir estos esfuerzos especiales de retención de clientes. Por lo tanto, una metodología natural sería puntuar a cada cliente con la probabilidad de abandono y abordar los N principales. Los clientes principales podrían ser los más fiables; en escenarios más sofisticados, una función de beneficios se emplea durante la selección de candidatos a exenciones especiales. Son embargo, estos planteamientos son solo una parte de la estrategia holística de tratar con el abandono. Las empresas también tienen que tener en cuenta el riesgo (y la tolerancia a él asociada), el nivel y el coste de la intervención y la posible segmentación de los clientes.  

##Perspectiva y enfoques de la industria
La gestión del abandono de forma adecuada es signo de madurez industrial. El ejemplo clásico es la industria de las telecomunicaciones, donde se sabe que los abonados cambian con frecuencia de un operador a otro. Este abandono voluntario es realmente preocupante. Además, los operadores han acumulado importantes conocimientos sobre los *factores del abandono*, que son los determinantes que impulsan a los consumidores a cambiar. Por ejemplo, la elección del terminal o dispositivo es un factor conocido de abandono en el negocio de los teléfonos móviles. Como resultado, una política común es subvencionar el precio del terminal para los nuevos abonados mientras se cobra el precio completo a los clientes existentes por una actualización. Tradicionalmente, esa política ha llevado a los clientes a saltar de un operador a otro para obtener un nuevo descuento lo que, a su vez, ha provocado que los operadores refinen sus estrategias. La alta volatilidad en las ofertas de terminales es un factor que invalida de manera muy rápida los modelos de abandono que se basan en los modelos de terminales actuales. Además, los teléfonos móviles no son solo dispositivos de telecomunicaciones, sino también una declaración de moda (como ocurre con el iPhone), y estos indicadores sociales escapan del ámbito de los conjuntos de datos normales de las telecomunicaciones. Como resultado, no se puede idear una buena política eliminando simplemente las razones conocidas del abandono. De hecho, una estrategia continua basada en modelos, incluidos los modelos clásicos que cuantifican variables categóricas (por ejemplo, árboles de decisiones), es **obligatoria**.

Mediante el uso de conjuntos de Big Data en sus clientes, las organizaciones están realizando análisis de Big Data, en especial, la detección de abandono basada en Big Data, como un enfoque eficaz sobre el problema. Puede encontrar más información al respecto en la sección de recomendaciones sobre ETL.  

##Metodología para crear un modelo de abandono de clientes
Un proceso común de solución de problemas para resolver el abandono de clientes se representa en los diagramas 1-3:  

1.	Un modelo de riesgo le permite considerar el modo en que las acciones influyen en la probabilidad y el riesgo.
2.	Un modelo de intervención le permite considerar cómo el nivel de intervención podría afectar a la probabilidad de abandono y la cantidad de valor de tiempo de vida del cliente (CLV).
3.	Este análisis conduce a un análisis cualitativo que se escala a una campaña de marketing proactiva para identificar los segmentos de clientes a los que dirigirles la oferta óptima.  

![][1]

Este enfoque de anticipación es la mejor manera de tratar el abandono, pero conlleva complejidad: tenemos que desarrollar un arquetipo basado en varios modelos y trazar las dependencias entre los modelos. La interacción entre los modelos se puede encapsular como se muestra en el siguiente diagrama:  

![][2]
 
*Ilustración 4: Arquetipo basado en varios modelos unificado*  

Las interacciones entre los diferentes modelos es clave si vamos a proporcionar un enfoque holístico sobre la retención de clientes.  Cada modelo se degrada necesariamente con el paso del tiempo; por lo tanto, la arquitectura es un bucle implícito (similar al arquetipo establecido por el estándar de minería de datos de CRISP-DM, [***3***]).  
 
A pesar de todo, el ciclo general de segmentación/descomposición de marketing de decisión de riesgo es una estructura generalizada, que es aplicable a muchos problemas empresariales. El análisis del abandono es simplemente un representante fuerte de este grupo de problemas, dado que presenta todos los rasgos de un problema empresarial complejo que no permite una solución predictiva simplificada. Los aspectos sociales del enfoque moderno hacia el abandono no se destacan particularmente en el enfoque, pero están encapsulados en el arquetipo de creación de modelos como lo estarían en cualquier otro modelo.  

Un interesante aporte aquí es el análisis de Big Data. Las empresas de telecomunicaciones y los comercios minoristas de hoy en día recopilan datos exhaustivos sobre sus clientes, y podemos prever fácilmente que la necesidad de conectividad entre varios modelos se convertirá en una tendencia común, dadas las tendencias emergentes como Internet de los objetos y los dispositivos multifuncionales, que permiten a las empresas emplear soluciones inteligentes a varios niveles.  

 
#Implementación del arquetipo de creación de modelos en el Estudio de aprendizaje automático de Microsoft Azure 
Dado el problema que acabamos de describir, ¿cómo podemos implementar un enfoque integrado de modelos y puntuación? En esta sección, demostraremos cómo vamos a realizar esto mediante el Estudio de aprendizaje automático en la nube de Microsoft Azure.  

El enfoque de varios modelos es imprescindible a la hora de diseñar un arquetipo global para el abandono. Incluso la parte de puntuación (predictiva) del enfoque debe basarse en varios modelos.  

En el siguiente diagrama se muestra el prototipo que hemos creado, que emplea cuatro algoritmos de puntuación diferentes en el Estudio de aprendizaje automático en la nube para predecir el abandono. El motivo de utilizar un enfoque de varios modelos es no crear únicamente un clasificador de conjunto para aumentar la precisión, sino también protegerse frente al exceso de ajustes y mejorar la selección preceptiva de características.  

![][3]
 
*Ilustración 5: Prototipo de un enfoque de creación de modelos de abandono*  

En las siguientes secciones se proporcionan más detalles sobre el modelo de puntuación del prototipo que hemos implementado con el Estudio de aprendizaje automático en la nube.  

##Selección y preparación de los datos
Los datos usados para crear los modelos y puntuar a los clientes se obtuvieron de una solución vertical de CRM, pero se ocultaron para proteger la privacidad de los clientes. Los datos en sí contienen información sobre 8.000 abonados en los Estados Unidos y combinan tres fuentes: aprovisionamiento de datos (metadatos de abonados), datos de actividad (uso del sistema) y datos del servicio al cliente. Los datos no incluyen ninguna información sobre los clientes relativa a la empresa; por ejemplo, no incluye metadatos de fidelización ni capacidad crediticia.  

Por motivos de simplicidad, los procesos de ETL y de limpieza de datos escapan de nuestro ámbito dado que se supone que la preparación de los datos ya se ha realizado en otra parte.   

La selección de características para la creación de modelos se basa en la puntuación de importancia preliminar del conjunto de indicadores, incluido en el proceso mediante el uso del módulo de bosque aleatorio. Para la implementación en el Estudio de aprendizaje automático en la nube, hemos calculado la media, la mediana y rangos de características representativas. Por ejemplo, hemos agregado las sumas de los datos cualitativos, como los valores máximos y mínimos de la actividad del usuario.    

También hemos capturado información temporal de los últimos 6 meses. Solo hemos usado los seis meses anteriores porque analizamos los datos de un año y establecimos que si había tendencias estadísticamente importantes, el efecto sobre el abandono disminuiría extraordinariamente después de seis meses.  

El aspecto más importante es que el proceso entero, incluidos el ETL, la selección de características y la creación de modelos, se implementó en el Estudio de aprendizaje automático en la nube mediante el uso de fuentes de datos de Microsoft Azure.   

Los siguientes diagramas ilustran los datos usados:  

![][4]
 
*Ilustración 6: Extracto de la fuente de datos (oculta)*  

![][5]

 
*Ilustración 7: Características extraídas de la fuente de datos*
 
##Algoritmos usados en el prototipo

Usamos los siguientes cuatro algoritmos de aprendizaje automático para crear el prototipo (sin personalización):  

1.	Regresión logística (LR)
2.	Árbol de decisiones ampliado (BT) 
3.	Perceptron promediado (AP) 
4.	Máquina de vectores de soporte (SVM)  


El siguiente diagrama ilustra una parte de la superficie de diseño del experimento, que indica la secuencia en la que se crearon los modelos:  

![][6]  

 
*Ilustración 8: Creación de modelos de Aprendizaje automático en el Estudio de aprendizaje automático en la nube*  

##Métodos de puntuación
Puntuamos los cuatro modelos mediante un conjunto de datos de aprendizaje etiquetado.  

También enviamos el conjunto de datos de puntuación a un modelo comparable creado mediante la edición de escritorio de SAS Enterprise Miner 12, y medimos la exactitud del modelo SAS así como la de los cuatro modelos de Aprendizaje automático en la nube de Azure.  

#Resultados 
En esta sección, presentamos nuestros hallazgos sobre la exactitud de los modelos, según el conjunto de datos de puntuación.  

##Exactitud y precisión de la puntuación
En general, la implementación del Aprendizaje automático en la nube de Azure se encuentra por detrás de SAS en cuanto a exactitud, como en un 10-15 % (AUC).  

Sin embargo, la métrica más importante en el abandono es la tasa de errores de clasificación: es decir, de los N clientes principales que abandonan según predice el clasificador, ¿cuáles de ellos **no** abandonaron realmente, pero aún así recibieron un tratamiento especial?  El siguiente diagrama compara esta tasa de errores de clasificación en todos los modelos:  

![][7]

 
*Ilustración 9: Área bajo la curva del prototipo de Passau * 

###Uso de AUC para comparar los resultados
El área bajo la curva (AUC) es una métrica que representa una medida global de *separabilidad* entre las distribuciones de las puntuaciones para poblaciones positivas y negativas. Aunque guarda similitudes con el gráfico ROC (Receiver Operator Characteristic, Característica operativa del receptor), una diferencia importante es que la métrica AUC no requiere que se elija un valor de umbral. En su lugar, lo que hace es resumir los resultados de **todas** las opciones posibles. En contraposición, el gráfico ROC tradicional muestra la tasa de positivos en el eje vertical y la tasa de falsos positivos en el eje horizontal, y el umbral de clasificación varía.   

AUC se usa generalmente como medida de valor en diferentes algoritmos (o diferentes sistemas), dado que permite que se comparen los modelos por medio de sus valores de AUC. Este es un enfoque general en meteorología, ciencias biológicas y muchas otras industrias. Por lo tanto, AUC representa una herramienta generalizada para la evaluación del rendimiento del clasificador.  

###Comparación de las tasas de errores de clasificación
Comparamos las tasas de errores de clasificación del conjunto de datos en cuestión mediante los datos de CRM de 8.000 abonados aproximadamente.  

-	La tasa de errores de clasificación de SAS fue del 10-15 %.
-	La tasa de errores de clasificación del Aprendizaje automático de Azure fue del 15-20 % para los 200-300 clientes principales que abandonan.  

En la industria de telecomunicaciones, es importante dirigirse solo a esos clientes que tienen el mayor riesgo de abandono y ofrecerles un servicio de asistencia personal u otro tratamiento especial. A tal respecto, la implementación de Aprendizaje automático en la nube de Azure logra resultados equivalentes a los del modelo SAS.  

Del mismo modo, la exactitud es más importante que la precisión porque estamos interesados principalmente en clasificar de forma correcta a los posibles clientes que abandonan.  

El siguiente diagrama de Wikipedia representa la relación en un gráfico animado fácil de entender:  

![][8]
 
*Ilustración 10: Balance entre exactitud y precisión*

###Resultados de precisión y exactitud para el modelo de árbol ampliado  

El siguiente gráfico muestra los resultados sin procesar de la puntuación usando el prototipo de Aprendizaje automático en la nube de Azure en el modelo de árbol de decisiones ampliado de dos clases, que pasa a ser el más exacto de los cuatro modelos:  

![][9]

*Ilustración 11: Características del árbol ampliado*

##Comparación del rendimiento 
Comparamos la velocidad a la que se puntúan los datos mediante los modelos de Aprendizaje automático en la nube de Azure y un modelo comparable usando la edición de escritorio de SAS Enterprise Miner 12.1.  

En la siguiente tabla se resume el rendimiento de los algoritmos:  

*Tabla 1. Rendimiento general (exactitud) de los algoritmos* 

**LR**|	**BT**|	**AP**|	**SVM**|
--|--|--|--|
Modelo promedio|	El mejor modelo|	Bajo rendimiento|	Modelo promedio

Los modelos hospedados en el Aprendizaje automático en la nube de Azure superaron a SAS en un 15-25 % en cuanto a velocidad de ejecución, pero la exactitud fue prácticamente igual.  

#Discusión y recomendaciones
En la industria de telecomunicaciones, son varias las prácticas que han surgido para analizar el abandono:  

-	Las métricas se pueden clasificar en cuatro categorías fundamentales:
	-	**Entidad (por ejemplo, abonado)**. Proporcione información básica sobre el abonado y/o cliente que es el sujeto del abandono.
	-	**Actividad**. Obtenga toda la información de uso posible relacionada con la entidad. Por ejemplo, el número de inicios de sesión, etc.
	-	**Servicio al cliente**. Recoja información de los registros de servicio al cliente para indicar si el abonado tuvo problemas o interacciones con el servicio al cliente. 
	-	**Datos competitivos y empresariales**. Obtenga cualquier información posible sobre el cliente (puede que no esté disponible o que sea difícil de rastrear).
-	Importancia de uso para conducir la selección de características. Esto supone que el árbol del bosque aleatorio/ampliado es siempre un enfoque prometedor.  

El uso de las cuatro categorías anteriores crea la ilusión de que un enfoque *determinista* sencillo, basado en índices creados sobre factores razonables por categoría, debería ser suficiente para identificar a los clientes con riesgo de abandono. Por desgracia, aunque esta noción parece plausible, es una concepción errónea. El motivo es que al abandono es un efecto temporal y los factores que contribuyen a él están normalmente en estado transitorio. Lo que lleva a un cliente a plantearse la idea de abandonar hoy puede ser diferente mañana y seguro que será diferente en los próximos seis meses. Por lo tanto, un modelo *probabilístico* es una necesidad.  
 
Esta importante observación con frecuencia se pasa por alto en las empresas, quienes prefieren generalmente un enfoque orientado a BI hacia el análisis, principalmente porque es más comercial y admite una automatización sencilla.  

Sin embargo, la promesa de autoanálisis mediante el uso del Aprendizaje automático en la nube de Azure es que las cuatro categorías de información, clasificadas por división o departamento, se convierten en una fuente valiosa para el aprendizaje automático sobre el abandono.  
 
Otra interesante función introducida en el Aprendizaje automático en la nube de Azure es que existe la posibilidad de agregar un módulo personalizado al repositorio de módulos predefinidos. Esa función crea básicamente una oportunidad para seleccionar bibliotecas y crear plantillas para los mercados verticales. Es un importante diferenciador del Aprendizaje automático en la nube de Azure en el mercado.  

Esperamos tratar este tema en el futuro, en especial en relación con los análisis de Big Data.
  
#Conclusión
En este documento se describe un enfoque sensato hacia un problema común, el abandono del cliente, mediante un marco de trabajo genérico. Hemos considerado un prototipo para los modelos de puntuación y lo hemos implementado mediante el Aprendizaje automático en la nube de Microsoft Azure. Finalmente, hemos evaluado la exactitud y el rendimiento del prototipo con respecto a algoritmos comparables en SAS.  

**Para obtener más información:**  

¿Le ha ayudado este documento? Proporciónenos sus comentarios. Díganos en una escala de 1 (pobre) a 5 (excelente), cómo valoraría este documento y porqué ha dado esta valoración. Por ejemplo:  

-	¿Lo va a puntuar alto porque contiene buenos ejemplos, excelentes capturas de pantalla, redacción clara o por otro motivo? 
-	¿Lo va a puntuar bajo debido a ejemplos pobres, capturas de pantalla borrosas o redacción poco clara?  

Estos comentarios nos ayudarán a mejorar la calidad de los documentos técnicos que publicamos.   

[Enviar comentarios](mailto:sqlfback@microsoft.com).
 
#Referencias
[1] Análisis predictivo: más allá de las predicciones, W. McKnight, Information Management, julio/agosto de 2011, p.18-20.  

[2] http://en.wikipedia.org/wiki/Accuracy_and_precision   
 
[3] http://www.the-modeling-agency.com/crisp-dm.pdf   

[4] Marketing de Big Data  

[5] http://www.amazon.com/Big-Data-Marketing-Customers-Effectively/dp/1118733894/ref=sr_1_12?ie=UTF8&qid=1387541531&sr=8-12&keywords=customer+churn
 
#Anexo

![][10]
 
*Figura 12. Instantánea de una presentación sobre un prototipo de abandono*
  

[1]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-1.png
[2]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-2.png
[3]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-3.png
[4]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-4.png
[5]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-5.png
[6]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-6.png
[7]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-7.png
[8]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-8.png
[9]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-9.png
[10]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-10.png
