---
title: "Cuaderno de estrategias de la plantilla de solución de Cortana Intelligence para la previsión de demanda de energía | Microsoft Docs"
description: "Plantilla de solución con Microsoft Cortana Intelligence que ayuda a prever la demanda de una empresa de suministro de energía."
services: cortana-analytics
documentationcenter: 
author: ilanr9
manager: ilanr9
editor: yijichen
ms.assetid: 8855dbb9-8543-45b9-b4c6-aa743a04d547
ms.service: cortana-analytics
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2016
ms.author: ilanr9;yijichen;garye
ms.openlocfilehash: 275e387878900154660d044b26ff5ac03a17a65a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="cortana-intelligence-solution-template-playbook-for-demand-forecasting-of-energy"></a>Cuaderno de estrategias de la plantilla de soluciones de Cortana Intelligence para la previsión de la demanda de energía
## <a name="executive-summary"></a>Resumen ejecutivo
En los últimos años, Internet de las cosas (IoT), las fuentes de energía alternativas y los macrodatos se han combinado para crear grandes oportunidades en los sectores de los servicios públicos y la energía. Al mismo tiempo, tanto el sector de los servicios públicos como el de la energía han presenciado una reducción del consumo y, al mismo tiempo, la exigencia por parte de los clientes de formas mejores de controlar su uso de la energía. De ahí que tanto las compañías de sectores públicos como las de redes de distribución eléctrica inteligentes necesitan imperiosamente innovar y renovarse. Además, muchas de las redes eléctricas y de servicios públicos se han quedado obsoletas y son muy caras de mantener y administrar. Durante el último año, el equipo ha trabajado en varios contratos con el sector energético. Durante ese tiempo, hemos encontrado muchos casos en los que los servicios públicos o los ISV (fabricantes de software independientes) han evaluado las previsiones de demanda energética en el futuro. Estas previsiones desempeñan un rol importante en su negocio actual y futuro, y se han convertido en la base de varios casos de uso. Aquí se incluye la previsión de carga energética a corto y largo plazo, su comercialización, el equilibrio de carga, la optimización de la red. Los macrodatos y los métodos de análisis avanzado (AA), como Aprendizaje automático (ML), son las principales habilitadores de la generación de previsiones precisas y confiables.  

En este cuaderno de estrategias se combinan las directrices analíticas y empresariales necesarias para desarrollar e implementar correctamente una solución de predicción de la demanda energética. Estas directrices propuestas pueden ayudar a los servicios públicos, científicos de datos e ingenieros de datos a establecer soluciones en la nube de previsión de demanda totalmente operativas. En el caso de las compañías que acaban de empezar su andadura en el análisis avanzado y en los macrodatos, esta solución puede representar el inicio de su estrategia a largo plazo para las redes de distribución eléctrica inteligentes.

> [!TIP]
> Para descargar un diagrama que proporcione una introducción a la arquitectura de esta plantilla, consulte [Arquitectura de la plantilla de solución de Cortana Intelligence para la previsión de la demanda de energía](cortana-analytics-architecture-demand-forecasting-energy.md).  
> 
> 

## <a name="overview"></a>Información general
Este documento trata los negocios, datos y aspectos técnicos del uso de Cortana Intelligence y, en particular, Aprendizaje automático de Azure (AML) para la implementación de soluciones de previsión energética. El documento consta de tres partes principales:  

1. Conocimiento del negocio  
2. Conocimiento de los datos  
3. Implementación técnica

La parte del **conocimiento del negocio** describe el aspecto del negocio que es preciso conocer y tener en cuenta antes de tomar cualquier decisión sobre inversiones. En ella se explica cómo calificar el problema empresarial disponible para asegurarse de que tanto el análisis predictivo como el aprendizaje automático son realmente eficaces y pueden aplicarse. El documento también explica los datos básicos del aprendizaje automático y cómo se utiliza para tratar los problemas de previsión energética. Describe los requisitos previos y los criterios de cualificación de un caso de uso. También se proporcionan varios escenarios de casos de uso y casos de negocio de ejemplo.

Los datos son el ingrediente principal de las soluciones de aprendizaje automático. La parte del **conocimiento de los datos** de este documento trata varios aspectos importantes de los datos. En ella se describe el tipo de datos que se necesitan para la previsión energética, los requisitos de calidad de los datos y los orígenes de datos que suelen existir. También se explica cómo se usan los datos sin procesar para preparar las características de datos que controlan la parte del modelado.

La tercera parte del documento trata del aspecto de la **implementación técnica** de una solución. El modelado y la ingeniería de características son el núcleo del proceso de la ciencia de datos y, por tanto, se tratan de manera detallada. Trata el concepto de servicios web, que son un vehículo importante para la implementación en la nube de soluciones de análisis predictivo. También se describe una arquitectura típica de una solución operativa de un extremo a otro.

Además, el documento incluye material de referencia que permite conocer aún mejor la tecnología y el dominio.

Es importante tener en cuenta que en este documento no se pretende tratar el proceso profundo de la ciencia de datos, ni sus aspectos técnicos y matemáticos. Estos detalles se pueden encontrar en la [documentación de Azure Machine Learning](http://azure.microsoft.com/services/machine-learning/) y estos [blogs](http://blogs.microsoft.com/blog/tag/azure-machine-learning/).

### <a name="target-audience"></a>Audiencia de destino
Este documento va destinado a personal técnico y comercial que desee obtener conocimientos acerca de las soluciones basadas en Aprendizaje automático y de su uso en el entorno de la previsión de la demanda energética.

Los científicos de datos también pueden beneficiarse de la lectura de este documento, ya que conocerán mejor el proceso de alto nivel que controla la implementación de una solución de previsión de la demanda de energía. En este contexto también se puede usar para establecer una buena línea de base y un magnífico punto de partida para obtener información más detallada y avanzada.

### <a name="industry-trends"></a>Tendencias del sector
En los últimos años, IoT, las fuentes de energía alternativas y los macrodatos se han combinado para crear grandes oportunidades en el espacio de los servicios públicos y la energía. Al mismo tiempo, tanto el sector de los servicios públicos como el de la energía han presenciado una reducción del consumo y, al mismo tiempo, la exigencia por parte de los clientes de formas mejores de controlar su uso de la energía.

Muchos servicios públicos y compañías de energía inteligente han liderado el uso de las [redes de distribución eléctrica inteligentes](https://en.wikipedia.org/wiki/Smart_grid) mediante la implementación de varios casos de uso que hacen uso de los datos que generan dichas redes. Muchos casos de uso giran en torno a las características inherentes de la producción de electricidad: no se puede acumular ni almacenar aparte como un inventario. Por lo tanto, se debe usar lo que se produzca. Los servicios públicos que deseen ser más eficientes necesitan prever el consumo eléctrico, simplemente porque eso les proporcionará mayor capacidad de **equilibrar la oferta y la demanda**, lo que evita pérdidas de energía, **reducir las emisiones de gases de efecto invernadero** y controlar los costos.

Cuando se habla de costos, hay otro aspecto importante, el precio. Las nuevas capacidades de intercambiar electricidad entre los sectores públicos aumentan la necesidad de **prever tanto la demanda de electricidad como su precio en el futuro**. Esto puede ayudar a las compañías a determinar sus volúmenes de producción.

Cuando usamos la palabra 'inteligente', en realidad nos referimos a una red de distribución de electricidad que puede "aprender" y, a continuación, realizar predicciones. Puede prever no solo los cambios estacionales en el consumo, sino también **las situaciones de sobrecarga temporal y ajustarse automáticamente en consecuencia**. Mediante la regulación remota del consumo (con la ayuda de estos medidores inteligentes), se pueden controlar las situaciones de sobrecarga localizadas. **Al predecir primero y actuar después**, las redes de distribución de electricidad se hacen más inteligentes con el paso del tiempo.

En el resto de este documento, nos centraremos en una familia concreta de casos de uso que cubre la previsión de la demanda de energía futura, a corto y largo plazo. Hemos trabajado en estas áreas durante varios meses y hemos adquirido conocimientos y aptitudes que nos permitirán generar resultados útiles para el sector. En el documento también se tratarán otros casos de uso en un futuro próximo.

## <a name="business-understanding"></a>Conocimiento del negocio
### <a name="business-goals"></a>Objetivos empresariales
El objetivo de **Demostración de la energía** es mostrar un análisis predictivo típico y una solución de Aprendizaje automático que se pueden implementar en un período muy breve. En concreto, actualmente nos centramos en habilitar soluciones de previsión de demanda de energía, con el fin de que su valor empresarial se pueda producir y aprovechar rápidamente. La información de este cuaderno de estrategias puede ayudar a lograr los siguientes objetivos:

* Poco tiempo para valorar la solución basada en Aprendizaje automático.
* Capacidad para expandir un caso de uso piloto a otros casos de uso o a un ámbito más amplio, según su necesidad de negocio.
* Obtener rápidamente información de los productos del conjunto de aplicaciones Cortana Intelligence.

Teniendo en cuenta estos objetivos, el fin de este cuaderno de estrategias es proporcionar conocimientos empresariales y técnicos que le ayudarán a lograr dichos objetivos.

### <a name="power-load-and-demand-forecasting"></a>Previsión de demanda y carga energéticas
En el sector energético, puede haber muchas maneras en que la previsión de la demanda puede ayudar a resolver problemas empresariales críticos. De hecho, se puede considerar que la previsión de demanda es la base de muchos casos de uso principales del sector. En general, consideramos dos tipos de previsiones de demanda de energía: a corto plazo y a largo plazo. Cada uno de ellos puede servir para un propósito distinto y utilizar un enfoque diferente. La principal diferencia entre ambos es el horizonte de la previsión, es decir, el intervalo de tiempo en el futuro para el que se haría la previsión.

#### <a name="short-term-load-forecasting"></a>Previsión de carga a corto plazo
En el contexto de la demanda de energía, la previsión de carga a corto plazo (STLF) se define como la carga agregada que se prevé en un futuro próximo en distintas partes de la red de distribución de la electricidad (o en la red en conjunto). En este contexto, el corto plazo se define como un horizonte temporal que se encuentra dentro del intervalo entre 1 hora y 24 horas. En algunos casos, también es posible un horizonte de 48 horas. Por lo tanto, STLF es muy común en un caso de uso operativo de la red de distribución de la electricidad. Estos son algunos ejemplos de casos de usos controlador por STLF:

* Equilibrio de suministro y demanda
* Soporte de la comercialización de la energía
* Creación de mercado (establecimiento del precio de la energía)
* Optimización operativa de la red de distribución de electricidad
* [Respuesta a la demanda](https://en.wikipedia.org/wiki/Demand_response)
* Previsión de máxima demanda
* Administración del lado de la demanda
* Equilibrio de carga y prevención de sobrecargas
* Previsión de carga a largo plazo
* Detección de fallos y anomalías
* Reducción o redistribución de los picos 

Los modelos STLF se basan principalmente en datos de consumo recientes (último día o semana) y usan la temperatura prevista como indicador importante. En la actualidad, la obtención de una previsión precisa de la temperatura de la próxima hora y hasta un máximo de 24 horas es una tarea cada vez más sencilla. Estos modelos son menos sensibles a los patrones estacionales o a las tendencias de consumo a largo plazo.

Las soluciones de SLTF también suelen generar un gran volumen de peticiones de predicción (solicitudes de servicio), ya que se invocan cada hora y, en algunos casos, incluso con mayor frecuencia. También es muy habitual ver implantaciones en donde cada subestación o transformador individual se representa como un modelo independiente y, por consiguiente, el volumen de solicitudes de predicción es aún mayor.

#### <a name="long-term-load-forecasting"></a>Previsión de carga a largo plazo
El objetivo de la previsión de carga a largo plazo (LTLF) es prever la demanda de energía con un horizonte temporal que oscila entre una semana a varios meses (incluso en algunos casos puede llegar a varios años). Este intervalo se aplica principalmente a los casos de uso de planeamiento e inversión.

En el caso de los escenarios a largo plazo, es importante disponer de datos de gran calidad que cubran un período de varios años (al menos tres años). Habitualmente, estos modelos extraerán patrones de estacionalidad de los datos y harán uso de predicadores externos, como los patrones del tiempo y del clima.

Es importante dejar claro que cuanto más lejano sea el horizonte del pronóstico, menos preciso será este. Por lo tanto, es importante generar algunos intervalos de confianza junto con la previsión real, ya que ello permitiría factorizar la posible variación en su proceso de planeamiento.

Dado que el escenario de consumo de LTLF es principalmente el planeamiento, cabe esperar unos volúmenes de predicción mucho menores (en comparación con STLF). Habitualmente, estas predicciones se encuentran integradas en herramientas de visualización, como Excel o Power BI, y el usuario las invoca manualmente.

### <a name="short-term-vs-long-term-prediction"></a>Predicción a corto plazo frente a predicción a largo plazo
En la siguiente tabla se comparan los atributos más importantes de STLF y LTLF:

| Atributo | Previsión de carga a corto plazo | Previsión de carga a largo plazo |
| --- | --- | --- |
| Horizonte de pronóstico |De 1 a 48 horas |De 1 a 6 meses o más |
| Granularidad de datos |Cada hora |Cada hora o a diario |
| Casos de uso típicos |<ul><li>Equilibrio entre demanda y suministro</li><li>Elección de la hora de previsión</li><li>Respuesta a la demanda</li></ul> |<ul><li>Planeamiento a largo plazo</li><li>Planeamiento de recursos de cuadrícula</li><li>Planeamiento de recursos</li></ul> |
| Indicadores típicos |<ul><li>Día o semana</li><li>Hora del día</li><li>Temperatura cada hora</li></ul> |<ul><li>Mes del año</li><li>Día del mes</li><li>Temperatura y clima a largo plazo</li></ul> |
| Intervalo de datos históricos |Datos correspondientes a entre dos y tres años |Datos correspondientes entre cinco y diez años |
| Precisión típica |MAPE* del 5 % o inferior |MAPE* del 25 % o inferior |
| Frecuencia de pronóstico |Se realiza cada hora o cada 24 horas |Generado una vez al mes, trimestre o año |

\*[MAPE](https://en.wikipedia.org/wiki/Mean_absolute_percentage_error): error absoluto porcentual de la media

Como se puede ver en esta tabla, es muy importante distinguir entre escenarios de pronóstico a corto y largo plazo, ya que representan diferentes necesidades empresariales y pueden tener patrones de implementación y consumo diferentes.

### <a name="example-use-case-1-esmart-systems--overload-optimization"></a>Caso de uso de ejemplo 1: eSmart Systems (optimización de la sobrecarga)
Un rol importante de una [red de distribución inteligente de electricidad](https://en.wikipedia.org/wiki/Smart_grid) es optimizar y ajustar de forma dinámica y constante los cambiantes patrones de consumo. El consumo de energía puede verse afectado por los cambios a corto plazo que suelen causar principalmente las fluctuaciones de temperatura (*por ejemplo,*, se usa más electricidad para la calefacción o el aire condición). Al mismo tiempo, el consumo eléctrico también se ve influenciado por las tendencias a largo plazo. Dichas tendencias pueden incluir los efectos de las estacionalidad, los días festivos nacionales, el crecimiento del consumo a largo plazo e incluso factores económicos como el índice de consumo, el precio de petróleo y el PIB.

En este caso de uso, [eSmart](http://www.esmartsystems.com/) deseaba implementar una solución basada en la nube que permitiera predecir la tendencia de una situación de sobrecarga en cualquier subestación dada de la red de distribución de electricidad. En concreto, eSmart deseaba identificar las subestaciones que probablemente se sobrecarguen en la próxima hora, con el fin de poderse tomar una acción inmediata para evitar dicha situación, o resolverla.

Una predicción precisa y rápida del funcionamiento requiere la implementación de tres modelos predictivos:

* Un modelo a largo plazo que permite el pronóstico del consumo eléctrico de cada estación durante las próximas semanas o meses.
* Un modelo a corto plazo que permite la predicción de una situación de sobrecarga en una subestación determinada durante la próxima hora.
* Un modelo de temperatura que permite el pronóstico la temperatura futura en varios escenarios.

El objetivo del modelo a largo plazo es clasificar las subestaciones por su tendencia a la sobrecarga (dada su capacidad de transmisión de energía) durante la próxima semana o mes. Esto permite crear una lista corta de subestaciones que serviría como entrada para la predicción a corto plazo. Dado que la temperatura es un indicador importante del modelo a largo plazo, existe la necesidad de crear pronósticos de la temperatura en varios escenarios y usarlos como entrada en el modelo a largo plazo. Luego se invoca el pronóstico a corto plazo para predecir cuál de las estaciones es probable que se sobrecargue en la próxima hora.

Los modelos a corto y a largo plazo se implementan individualmente en cada subestación. Por consiguiente, la ejecución práctica de estos modelos requiere una amplia orquestación. Para que las predicciones a corto plazo sean más precisas, se dedica un modelo más personalizado a cada una de las horas del día. Todos estos modelos se ejecutan cada hora y finalizan la ejecución en pocos minutos, con el fin de que dar suficiente tiempo para responder y tomar las acciones preventivas, en caso de que sea necesario. Esta colección de modelos se actualizan a través del reciclaje periódico mediante los datos más recientes.

[Aquí](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18945)puede encontrar más información acerca de este caso de uso.

#### <a name="use-case-qualification-criteria--prerequisites"></a>Criterios de cualificación de casos de uso: requisitos previos
El principal punto fuerte de Cortana Intelligence radica en su eficaz capacidad para implementar y escalar soluciones que giran en torno al aprendizaje automático. Está diseñado para admitir miles de predicciones que se ejecutan simultáneamente. Se puede escalar automáticamente para seguir un patrón de consumo cambiante. Por tanto el enfoque de la solución se encuentra en la precisión y el rendimiento de cálculo. Por ejemplo, una compañía de servicios está interesada en la producción del pronóstico preciso de la demanda de energía en la próxima hora y en cada hora del día. Por otra parte, nos interesa menos responder la pregunta de por qué la demanda se prevé que sea como es (el propio modelo se encargará de eso).

Por lo tanto, es importante tener en cuenta que no todos los casos de uso y problemas empresariales pueden resolverse de forma eficaz mediante el aprendizaje automático.

Cortana Intelligence y el aprendizaje automático pueden ser muy eficaces para resolver un problema empresarial determinado cuando se cumplen los criterios siguientes:

* El problema empresarial actual es **predictivo** por naturaleza. Un ejemplo de caso de uso predictivo es una empresa de servicios que desea predecir la carga de energía que tendrá un subestación determinada durante la siguiente hora. Por otro lado, el análisis y la clasificación de los potenciadores de demanda histórico serían **descriptivos** por naturaleza y, por tanto, menos aplicables.
* Una vez que la predicción está disponible, las **acciones que se deben** realizar son evidentes. Por ejemplo, si se predice que una subestación va a sufrir una sobrecarga en la próxima hora, se puede desencadenar la acción proactiva de reducir la carga asociada a dicha subestación, con lo que se evita potencialmente una sobrecarga.
* El caso de uso representa una **tipo de problema habitual** que, una vez resuelto, puede preparar el terreno para resolver otros casos de uso similares.
* El cliente puede establecer **objetivos cuantitativos y cualitativos** para demostrar una implementación correcta de una solución. Por ejemplo, un buen objetivo cuantitativo para la previsión de la demanda de energía sería el umbral de precisión necesario (*por ejemplo,*, se permite un error máximo del 5%) o cuando se predice la sobrecarga de la subestación, la precisión (índice de verdaderos positivos) y recuperación (grado de positivos verdaderos) deben estar por encima de un umbral determinado. Estos objetivos se deben derivar de los objetivos de negocio del cliente.
* Hay un claro **escenario de integración** con el flujo de trabajo de negocio de la compañía. Por ejemplo, el pronóstico de la carga de la subestación se puede integrar en el centro de control de la red de distribución de electricidad para permitir las actividades de prevención de sobrecarga.
* El cliente tiene **datos listos para su uso con la calidad suficiente** para dar soporte al caso de uso (en la siguiente sección de este cuaderno de estrategias, **Calidad de los datos**, encontrará más información al respecto).
* El cliente usa una arquitectura de datos centrada en la nube o un **aprendizaje automático basado en la nube**, que incluye Aprendizaje automático de Azure y otros componentes del conjunto de aplicaciones Cortana Intelligence.
* El cliente está dispuesto a establecer **un flujo de datos de un extremo al otro** que facilite el envío de datos a la nube de manera continua y a **poner en funcionamiento** la solución.
* El cliente está listo para **dedicar recursos** que se involucrarán activamente durante la implementación piloto inicial, con el fin de que el conocimiento y la propiedad de la solución pueda transferirse al cliente si dicha implementación se completa de manera correcta.
* El recurso del cliente debe ser un **profesional de datos cualificado**, preferiblemente un científico de datos.

La cualificación de un caso de uso según los criterios anteriores puede mejorar considerablemente su índice de éxito y establecer una buena punta de lanza para la implementación de casos de uso en el futuro.

### <a name="cloud-based-solutions"></a>Soluciones basadas en la nube
El conjunto de aplicaciones Cortana Intelligence en Azure es un entorno integrado que reside en la nube. La implementación de una solución de análisis avanzado en un entorno de nube aporta importantes beneficios a las empresas y, al mismo tiempo, pueden suponer un gran cambio para las empresas que aún utilizan soluciones de TI locales. En el sector energético, hay una tendencia clara hacia la migración gradual de las operaciones a la nube. Esta tendencia va de la mano con el desarrollo de la red de distribución inteligente de electricidad, como se ha explicado anteriormente en **Tendencias del sector**. Dado que este cuaderno de estrategias se centra en una solución basada en la nube del sector energético, es importante explicar las ventajas y otras consideraciones relativas a la implementación de una solución basada en la nube.

Quizás la mayor ventaja de una solución basada en la nube sea el costo. Dado que la solución hace uso de componentes implementados en la nube, no hay costos iniciales ni costos de COGS (costo de bienes vendidos) asociados a ellos. Esto significa que no es preciso invertir en hardware, software y mantenimiento de TI y, por consiguiente, se logra una reducción considerable del riesgo empresarial.

Otra ventaja importante es la estructura de costos de pago por uso de las soluciones basadas en la nube. Los servidores basados en la nube para la realización de cálculo o almacenamiento se pueden implementar y escalar solo cuando sea necesario. Esto representa la ventaja de la eficacia de costo de las soluciones basadas en la nube.

Por último, no es preciso invertir en el mantenimiento de TI ni en el desarrollo de una infraestructura para el futuro, ya que todo esto forma parte de la oferta basada en la nube. En ese contexto, el conjunto de aplicaciones Cortana Intelligence incluye los mejores servicios y su mapa de ruta sigue evolucionando. Constantemente se introducen nuevas características, componentes y capacidades, y evolucionan los existentes.

A las compañías que acaban de iniciar su transición hacia la nube les recomendamos encarecida que adopten un enfoque gradual, para lo que es conveniente implementar un mapa de ruta para la migración a la nube. Creemos que para los servicios y las compañías del sector de la energía, los casos de uso que se tratan en este cuaderno de estrategias representan una excelente oportunidad para realizar una fase piloto de las soluciones de análisis predictivo de la nube.

#### <a name="business-case-justification-considerations"></a>Consideraciones que justifican los casos empresariales
En muchos casos, el cliente puede estar interesado en realizar una justificación comercial en un caso de uso determinado en el que una solución basada en la nube y Aprendizaje automático son componentes importantes. A diferencia de las soluciones locales, en el caso de una solución basada en la nube, el componente de coste inicial es mínimo y la mayoría de los elementos del costo están asociados con el uso real. En lo que se refiere a la implementación de una solución para la previsión de la demanda energética en el conjunto de aplicaciones Cortana Intelligence, se pueden integrar varios servicios en una única estructura de costos comunes. Por ejemplo, las bases de datos (*por ejemplo,*, SQL Azure) pueden utilizarse para almacenar los datos sin procesar y, luego, para los pronósticos reales se utiliza Aprendizaje automático de Azure para hospedar los servicios de previsión. En este ejemplo, la estructura de costos podría incluir componentes transaccionales y de almacenamiento.

Por otra parte, es preciso conocer bien el valor empresarial de la realización de una previsión de la demanda energética (a corto o largo plazo). De hecho, es importante tener en cuenta el valor empresarial de cada operación de previsión. Por ejemplo, un pronóstico preciso de la carga de energía durante las 24 horas siguientes puede evitar un exceso de producción o puede ayudar a evitar sobrecargas en la red de distribución de electricidad, lo que se puede cuantificar en términos de ahorro diario.

Una fórmula básica para calcular los beneficios financieros de la solución de previsión de la demanda sería: ![Fórmula básica para calcular los beneficios financieros de la solución de previsión de la demanda](media/cortana-analytics-playbook-demand-forecasting-energy/financial-benefit-formula.png)

Puesto que el conjunto de aplicaciones Cortana Intelligence proporciona un modelo de precios de pago por uso, no es preciso incluir un componente de costo fijo en esta fórmula. Esta fórmula se puede calcular diaria, mensual o anualmente.

Los planes de precios actuales del conjunto de aplicaciones Cortana Intelligence y del Aprendizaje automático de Azure se pueden encontrar [aquí](http://azure.microsoft.com/pricing/details/machine-learning/).

### <a name="solution-development-process"></a>Proceso de desarrollo de soluciones
El ciclo de desarrollo de una solución de previsión de la demanda de energía normalmente contiene cuatro fases, en las que hacemos uso de las tecnologías y servicios basados en la nube del conjunto de aplicaciones Cortana Intelligence.

Esto se ilustra en el diagrama siguiente:

![Ciclo de red de distribución inteligente de electricidad](media/cortana-analytics-playbook-demand-forecasting-energy/smart-grid-cycle.png)

A continuación se describe este proceso de cuatro pasos:

1. **Recopilación de datos**: todas las soluciones basadas en el análisis avanzado confían en los datos (consulte **Conocimiento de los datos**). En concreto, en lo relativo al análisis predictivo y a la previsión, confiamos en un flujo dinámico continuo de datos. En el caso de la previsión de la demanda energética, estos datos pueden proceder directamente de los medidores inteligentes o estar ya agregados a una base de datos local. También confiamos en otros orígenes externos de datos, como el tiempo y la temperatura. Este flujo continuo de datos debe orquestarse, programarse y almacenarse. [Data Factory de Azure](http://azure.microsoft.com/services/data-factory/) (ADF) es nuestro producto estrella para llevar a cabo esta tarea.
2. **Modelado** : para que las previsiones de la demanda de energía sean precisos y confiables, es preciso desarrollar (entrenar) y mantener un modelo excelente que haga uso de los datos históricos y extraiga los patrones significativos y predictivos en los datos. El área de Aprendizaje automático (ML) ha crecido rápidamente y ahora se desarrollan de manera habitual algoritmos más avanzados. Estudio de aprendizaje automático de Azure proporciona al usuario una excelente experiencia que le ayuda a utilizar los algoritmos de Aprendizaje automático más avanzados en un flujo de trabajo completo. Dicho flujo se trabajo se ilustra en un diagrama de flujo intuitivo e incluye la preparación de los datos, la extracción de características, el modelado y la evaluación de modelos. El usuario puede atraer cientos de modelos que se incluyen en este entorno. Al final de esta fase, los científicos de datos tendrán un modelo de trabajo totalmente evaluado y listo para su implementación.
   
   El siguiente diagrama ilustra un flujo de trabajo típico:
   
   ![Flujo de trabajo de modelado](media/cortana-analytics-playbook-demand-forecasting-energy/modeling-workflow.png)
3. **Implementación** : con un modelo de trabajo a mano, el paso siguiente es la implementación. Aquí, el modelo se convierte en un servicio web que expone una API de RESTful que se puede invocar simultáneamente a través de Internet de distintos clientes de consumo. Aprendizaje automático de Azure proporciona una manera sencilla de implementar un modelo directamente desde Estudio de aprendizaje automático de Azure, con un solo clic de un botón. Todo el proceso de implementación se realiza internamente. Esta solución se puede escalar automáticamente para satisfacer el consumo requerido.
4. **Consumo** : en esta fase es en la que realmente se utiliza el modelo de previsión para generar las previsiones. El consumo se puede controlar desde una aplicación de usuario (*por ejemplo,*, el panel) o directamente desde un sistema operativo como un sistema de equilibrado de demanda/suministro o una solución de optimización de la red de distribución de electricidad. Desde un modelo individual se pueden controlar varios casos de uso.

## <a name="data-understanding"></a>Conocimiento de los datos
Después de cubrir las consideraciones empresariales (consulte **Conocimiento del negocio**) de una solución de previsión de la demanda de energía estamos listos para explicar la parte de los datos. Todas las soluciones de análisis predictivo se basa en datos confiables. Para la previsión de la demanda energética, nos basamos en los datos históricos de consumo con distintos niveles de granularidad. Dichos datos históricos se utilizan como materia prima. Se someterá a un análisis meticuloso en el que los científicos de datos identificarán los indicadores (también denominados funciones) que se pueden colocar en un modelo que finalmente generará los pronósticos necesarios.

En el resto de esta sección, describiremos los distintos pasos y consideraciones necesarios para comprender los datos y cómo pasarlos a un formato utilizable.

### <a name="the-model-development-cycle"></a>El ciclo de desarrollo del modelo
La generación de buenos modelos de previsión requiere una preparación y un planeamiento meticulosos. La división del proceso de modelado en varios pasos y el hecho de no centrarse en más de un paso a la vez pueden mejorar considerablemente el resultado de todo el proceso.

El siguiente diagrama ilustra cómo se puede dividir el proceso de modelado en varios pasos:

![ciclo de desarrollo del modelo](media/cortana-analytics-playbook-demand-forecasting-energy/model-development-cycle.png)

Como se puede apreciar, el ciclo consta de seis pasos:

* Formulación del problema
* Ingesta y exploración de datos
* Preparación de los datos e ingeniería de características
* Modelado
* Evaluación del modelo
* Desarrollo

En el resto de la sección describiremos los pasos individuales y los elementos que se deben considerar en cada paso.

### <a name="problem-formulation"></a>Formulación del problema
La formulación de problema se puede considerar el paso más crítico que es preciso tomar antes de implementar cualquier solución de análisis predictivo. Aquí transformaremos el problema empresarial y lo descompondremos en elementos concretos que pueden resolverse mediante los datos y técnicas de modelado. Se recomienda formular el problema como un conjunto de preguntas y respuestas. Estas son algunas preguntas posibles que pueden aplicarse al ámbito de la previsión de la demanda de energía:

* ¿Cuál es la carga que se espera que tenga un subestación individual en la siguiente hora o día?
* ¿En qué momento del día tendrá una red de distribución de electricidad la máxima demanda?
* ¿Qué probabilidad hay de que una red de distribución de electricidad soporte la carga máxima esperada?
* ¿Cuánta energía debe generar la central eléctrica durante cada hora del día?

La formulación de estas preguntas nos permite centrarnos en obtener los datos correctos e implementar una solución que se ajusta totalmente al problema empresarial en cuestión. Además, a continuación, podemos establecer algunas métricas clave que nos permitan evaluar el rendimiento del modelo. Por ejemplo, ¿cuál es el grado de precisión que debe tener un pronóstico y cuál es el margen de error que aceptaría la empresa?

### <a name="data-sources"></a>Orígenes de datos
La red moderna de distribución inteligente de electricidad recoge datos de varias partes y componentes de ella. Dichos datos representan diversos aspectos de la operación y uso de la red de distribución de electricidad. Dentro del ámbito de la previsión de la demanda energética, limitamos la explicación a los orígenes de datos que reflejan el consumo real de la demanda. Una importante fuente de consumo de energía son los medidores inteligentes. Los servicios públicos de todo el mundo están implementado rápidamente medidores inteligentes en sus clientes. Los medidores inteligentes registran el consumo eléctrico real y retransmiten constantemente estos datos a la compañía de servicio público. Los datos se recopilan y se devuelven a un intervalo fijo, que oscila entre 5 minutos y 1 hora. Los medidores inteligentes más avanzados pueden programarse de forma remota para que controlen y equilibren el consumo real en una casa. Los datos del medidor inteligente son relativamente confiables e incluyen una marca de tiempo. Esto hace que sea una parte importante de la previsión de la demanda. Los datos del medidor se pueden agregar (sumar) a varios niveles de la topología de la red de distribución de electricidad: transformador, subestación, región, *etc*. Luego, podemos elegir el nivel de agregación requerido para crear un modelo de pronóstico para él. Por ejemplo, si la compañía de servicios públicos desea prever la futura carga de cada una las subestaciones de su red de distribución de electricidad, se pueden agregar los datos de todos los medidores de cada subestación individual y utilizarlos como entrada para el modelo de predicción. A los medidores inteligentes los denominamos origen de datos interno.

Una previsión confiable de la demanda de energía también usará otros orígenes de datos externos. Un factor importante que afecta el consumo de energía es el tiempo, o con mayor precisión, la temperatura. Los datos históricos muestran una fuerte correlación entre la temperatura exterior y el consumo de energía. En los días calurosos del verano, los consumidores usan sus aparatos de aire acondicionado, mientras que en invierno encienden la calefacción. Por consiguiente, es clave tener un origen confiable de temperaturas históricas en la ubicación de la red de distribución de electricidad. Además, también confiamos en un pronóstico preciso de la temperatura como indicador de consumo de energía.

Otros orígenes de datos externos también pueden resultar de ayuda durante la creación de modelos de previsión de demanda energética. Entre ellos se pueden incluir los cambios climáticos a largo plazo y los índices económicos (*por ejemplo*, PIB), entre otros. En este documento no se incluirán dichos orígenes de datos.

### <a name="data-structure"></a>Estructura de datos
Después de identificar los orígenes de datos requeridos, deseamos asegurarnos de que los datos sin procesar recopilados incluyen las características de datos correctas. Para generar un modelo confiable de previsión de la demanda, necesitaríamos asegurarnos de que los datos recopilados incluyen elementos de datos que pueden ayudar a predecir la demanda futura. Estos son algunos requisitos básicos que afectan a la estructura de datos (esquema) de los datos sin procesar.

Los datos sin procesar constan de filas y columnas. Cada medida se representa en una sola fila de datos. Cada fila de datos incluye varias columnas (que también se denominan características o campos).

1. **Marca de tiempo** : el campo de marca de tiempo representa la hora real en que se registró la medida. Debe ajustarse a uno de los formatos de fecha y hora comunes. Se deben incluir las partes de fecha y hora. En la mayoría de los casos, no es preciso registrar el tiempo hasta el segundo nivel de granularidad. Es importante especificar la zona horaria en que se registran los datos.
2. **Identificador de medidor** : este campo identifica el medidor o el dispositivo de medición. Es una variable de categoría y puede ser una combinación de caracteres y dígitos.
3. **Valor de consumo** : es el consumo real en una fecha y hora dadas. El consumo se puede medir en kWh (kilovatios-hora) o en cualquier otra unidad que se prefiera. Es importante tener en cuenta que la unidad de medida debe ser la misma en todas las medidas de los datos. En algunos casos, el consumo se puede suministrar en tres fases de alimentación. En ese caso, necesitaríamos recopilar todas las fases de consumo independientes.
4. **Temperatura** : la temperatura normalmente se recopila de un origen independiente. Sin embargo, debe ser compatible con los datos de consumo. Debe incluir una marca de tiempo, tal como se ha descrito anteriormente, que permitirá que se sincronice con los datos de consumo real. El valor de temperatura puede especificarse en grados Celsius o Fahrenheit, pero debe ser coherente en todas las mediciones.
5. **Ubicación:** el campo de ubicación se suele asociar con el lugar en que se han recopilado los datos de temperatura. Se puede representar como un número de código postal o en formato de latitud y longitud (lat. y long.).

Las tablas siguientes muestran ejemplos de un buen formato de datos de consumo y temperatura:

| **Fecha** | **Hora** | **Id. de medidor** | **Fase 1** | **Fase 2** | **Fase 3** |
| --- | --- | --- | --- | --- | --- |
| 7/1/2015 |10:00:00 |ABC1234 |7.0 |2.1 |5.3 |
| 7/1/2015 |10:00:01 |ABC1234 |7.1 |2.2 |4.3 |
| 7/1/2015 |10:00:02 |ABC1234 |6.0 |2.1 |4.0 |

| **Date** | **Hora** | **Ubicación** | **Temperatura** |
| --- | --- | --- | --- |
| 7/1/2015 |10:00:00 |11242 |24,4 |
| 7/1/2015 |10:00:01 |11242 |24,4 |
| 7/1/2015 |10:00:02 |11242 |24,5 |

Como puede verse, este ejemplo incluye tres valores diferentes de consumo, asociados a tres fases de energía. Además, tenga en cuenta que los campos de fecha y hora son independientes, sin embargo también se pueden combinar en una sola columna. En este caso, la columna de ubicación se representa en formato de código postal de cinco dígitos y la temperatura en formato de grados Celsius.

### <a name="data-format"></a>Formato de datos
El conjunto de aplicaciones Cortana Intelligence puede admitir los formatos de datos más comunes, como CSV, TSV, JSON, *etc*. Es importante que el formato de datos sea el mismo durante todo el ciclo de vida del proyecto.

### <a name="data-ingestion"></a>Ingesta de datos
Dado que la previsión de la demanda de energía se realiza de forma constante y con frecuencia, debemos asegurarnos de que los datos sin procesar fluyen por medio de un proceso de ingesta de datos sólido y confiable. El proceso de ingesta debe garantizar que los datos sin procesar están disponibles para el proceso de previsión en el momento requerido. Eso implica que la frecuencia de la ingesta de datos debe ser mayor que la frecuencia de pronóstico.

Por ejemplo: si nuestra solución de previsión de demanda genera una nueva previsión a las 8:00 A.M. a diario, es preciso que nos aseguremos de que todos los datos recopilados en las últimas 24 horas se han introducido completamente hasta ese momento e incluso tiene que incluir la última hora de datos.

Para hacerlo, el conjunto de aplicaciones Cortana Intelligence ofrece varias formas de admitir un proceso de recopilación de datos confiable. Esto se explicará más adelante, en la sección **Implementación** de este documento.

### <a name="data-quality"></a>Calidad de los datos
El origen de los datos sin procesar necesarios para realizar una previsión de la demanda precisa y confiable debe cumplir varios criterios básicos de calidad de los datos. Aunque se pueden usar métodos estadísticos avanzados para compensar los problemas de calidad de los datos, será preciso que nos aseguremos de que atravesamos un umbral básico de calidad de los datos al introducir datos nuevos. Estas son algunas consideraciones relativas a la calidad de los datos sin procesar:

* **Valor que falta** : hace referencia a la situación en que no se recopiló una medida concreta. Aquí, el requisito básico es que el índice de valores que faltan no debe ser superior al 10% durante cualquier período. En caso de que falte un solo valor, debe indicarse mediante un valor predefinido (por ejemplo: '9999'), pero no '0', que podría ser una medida válida.
* **Precisión de la medida** : el valor real del consumo o temperatura debe registrarse con precisión. Unas medidas que no sean exactas generarán previsiones imprecisas. Normalmente, el error de medición debe ser inferior al 1% del valor verdadero.
* **Hora de medida** : se requiere que la marca de tiempo real de los datos recopilados no se desvíe en más de 10 segundos de la hora verdadera de la medida real.
* **Sincronización** : cuando se utilizan varios orígenes de datos (*por ejemplo*, consumo y temperatura) debemos asegurarnos de que no hay problemas de sincronización de hora entre ellos. Esto significa que la diferencia de hora entre la marca de hora recopilada de dos orígenes de datos independientes no debe superar los 10 segundos.
* **Latencia**: como ya se ha explicado, en **Ingesta de datos**, dependemos de un flujo de datos y de un proceso de ingesta confiables. Para controlarlo, debemos asegurarnos de que controlamos la latencia de datos. Dicha latencia se especifica como la diferencia entre la hora en que se tomó la medida real y la hora a la que se cargó en el almacenamiento del conjunto de aplicaciones Cortana Intelligence y está lista para su uso. En el caso de la previsión de carga a corto plazo, la latencia total no debe superar los 30 minutos. En el caso de la previsión de carga a largo plazo, la latencia total no debe ser superior a 1 día.

### <a name="data-preparation-and-feature-engineering"></a>Preparación de los datos e ingeniería de características
Una vez que se han introducido los datos sin procesar (consulte **Ingesta de datos**) y que se han almacenado de forma segura, está listos para ser procesados. Básicamente, la fase de preparación de los datos consiste en tomar los datos sin procesar y convertirlos (transformarlos y volver a darles forma) en un formulario para la fase de modelado. Esta fase puede incluir operaciones sencillas, como el uso de la columna de datos sin procesar tal cual con su valor medido real y valores normalizados, y operaciones más complejas como el [retraso temporal](https://en.wikipedia.org/wiki/Lag_operator), entre otras. Las columnas de datos recién creadas se denominan características de datos y el proceso de generarlas se denomina ingeniería de características. Al final de este proceso, tendríamos un nuevo conjunto de datos derivado de los datos sin procesar que puede utilizarse para el modelado. Además, la fase de preparación de datos debe encargarse de los valores que faltan (consulte **Calidad de los datos**) y compensarlos. En algunos casos, también sería necesario que normalizáramos los datos para asegurarse de que todos los valores se representan en la misma escala.

En esta sección enumeramos algunas de las características de datos comunes que se incluyen en los modelos de previsión de la demanda de energía.

**Características controladas por el tiempo:** estas características se derivan de los datos de fecha y marca de fecha. Dichos datos se extraen y se convierten en características de categoría como:

* Hora del día: es la hora del día y toma los valores 0 a 23
* Día de la semana: representa el día de la semana y toma los valores 1 (domingo) a 7 (sábado)
* Día del mes: representa la fecha real y puede tomar los valores 1 a 31
* Mes del año: representa el mes y toma los valores 1 (enero) a 12 (diciembre)
* Fin de semana: es una característica de valor binario que toma los valores 0 (en el caso de los días laborables) o 1 (en el caso de los fines de semana)
* Festivos: es una característica de valor binario que toma los valores 0 (en el caso de los días normales) o 1 (en el caso de los días festivos)
* Términos de Fourier: los términos de Fourier son ponderaciones que se derivan de la marca de tiempo y que se utilizan para capturar la estacionalidad (ciclos) de los datos. Dado que podemos tener varias estaciones en los datos, es posible que necesitemos varios términos de Fourier. Por ejemplo, los valores de la demanda pueden tener ciclos o estaciones anuales, semanales y diarios, lo que generará tres términos de Fourier.

**Características de medida independientes:** las características independientes incluyen todos los elementos de datos que deseamos a usar como indicadores en nuestro modelo. Se excluye la característica dependiente que necesitaríamos predecir.

* Característica de retardo: son los valores demorados de la demanda real. Por ejemplo, las características de la demora 1 contendrán el valor de la demanda de la hora anterior (asumiendo que son datos de hora) en relación con la marca de tiempo actual. Del mismo modo, podemos agregar la demora 2, la demora 3, *etc*. La combinación real de las características de demora que se usan se determina en la fase de modelado mediante la evaluación de los resultados del modelo.
* Tendencias a largo plazo: esta característica representa el crecimiento lineal de la demanda entre años.

**Característica dependiente:** la característica dependiente es la columna de datos que deseamos que nuestro modelo prediga. Con el [aprendizaje automático supervisado](https://en.wikipedia.org/wiki/Supervised_learning), primero es preciso que entrenemos el modelo mediante las características dependientes (que también se denominan etiquetas). Esto permite que el modelo aprenda los patrones de los datos asociados con la característica dependiente. En previsión de la demanda de energía normalmente deseamos predecir la demanda real y, por tanto, la usaríamos como característica dependiente.

**Tratamiento de los valores que faltan:** durante la fase de preparación de los datos, necesitaríamos determinar la mejor estrategia para tratar los valores que faltan. Esto se realiza principalmente mediante los diversos [métodos de imputación de datos](https://en.wikipedia.org/wiki/Imputation_\(statistics\)) estadísticos. En el caso de una previsión de la demanda de energía, lo habitual es que los valores que faltan se imputen mediante una media móvil de los puntos de datos disponibles anteriores.

**Normalización de datos:** la normalización de datos es otro tipo de transformación que se usa para poner todos los datos numéricos, como la previsión de la demanda, en una escala similar. Esto es algo que suele ayudar a mejorar la precisión del modelo. Para realizar dicha normalización, lo habitual es dividir el valor real entre el intervalo de los datos.
Esto reducirá verticalmente el valor original a un intervalo menor, normalmente entre -1 y 1.

## <a name="modeling"></a>Modelado
El modelado es la fase en que se realiza la conversión de los datos en un modelo. En el núcleo de este proceso hay algoritmos avanzados que examinan los datos históricos (datos de aprendizaje), extraen patrones y generación un modelo. Posteriormente, dicho modelo se puede utilizar para predecir datos nuevos que no se ha utilizado para generar el modelo.

Una vez que tenemos un modelo de trabajo confiable, podemos usarlo para puntuar los nuevos datos estructurados para incluir las características necesarias (X). El proceso de puntuación usará el modelo persistente (objeto de la fase de aprendizaje) y predecirá la variable de destino que se denota mediante Ŷ.

### <a name="demand-forecasting-modeling-techniques"></a>Técnicas de modelado de la previsión de la demanda
En el caso de la previsión de la demanda, hacemos uso de datos históricos, que se ordenan por hora. Por lo general, a los datos que incluyen la dimensión temporal se les denomina [serie temporal](https://en.wikipedia.org/wiki/Time_series). El objetivo del modelado de las series temporales es buscar tendencias relacionadas con el tiempo, estacionalidad, correlación automática (correlación con el paso del tiempo) y formularlas para convertirlas en un modelo.

En los últimos años han desarrollado algoritmos avanzados para acomodar el pronóstico de las series temporales y mejorar la precisión de la previsión. A continuación explicaremos brevemente algunos de ellos.

> [!NOTE]
> Esta sección no pretende utilizarse como una introducción al aprendizaje automático y a la previsión, sino como un breve examen de las técnicas de modelado que se suelen utilizar para la previsión de la demanda. Para más información y material educativo acerca de la predicción de series temporales, se recomienda encarecidamente el Libro en pantalla [Forecasting: principios y prácticas](https://www.otexts.org/book/fpp)(Previsión: principios y práctica).
> 
> 

#### <a name="ma-moving-averagehttpswwwotextsorgfpp62"></a>[**MA (media móvil)**](https://www.otexts.org/fpp/6/2)
La media móvil fue una de las primeras técnicas de análisis que se utilizó para la previsión de una serie temporal y aún hoy sigue siendo de las más usadas. También es la base de técnicas de previsión más avanzadas. Con la media móvil, la previsión del siguiente punto de datos se realiza mediante la obtención del promedio de los K puntos más recientes, donde K denota el orden de la media móvil.

La técnica de la media móvil tiene el efecto de suavizar la previsión y, por tanto, es posible que no controle bien la gran volatilidad de los datos.

#### <a name="ets-exponential-smoothinghttpswwwotextsorgfpp75"></a>[**ETS (suavizado exponencial)**](https://www.otexts.org/fpp/7/5)
El suavizado exponencial (ETS) es una familia de métodos que usan una media ponderada de los puntos de datos recientes para predecir el siguiente punto de datos. La idea es asignar mayor peso a los valores más recientes y reducir gradualmente el peso de los valores medidos anteriores. Hay varios métodos diferentes con esta familia, algunas de ellas incluyen el tratamiento de la estacionalidad de los datos, como el [método de Holt-Winters](https://www.otexts.org/fpp/7/5).

Algunos de estos métodos también tienen en cuenta la estacionalidad de los datos.

#### <a name="arima-auto-regression-integrated-moving-averagehttpswwwotextsorgfpp8"></a>[**ARIMA (modelo autorregresivo integrado de media móvil)**](https://www.otexts.org/fpp/8)
El modelo autorregresivo integrado de media móvil (ARIMA) es otra familia de métodos que habitualmente se utiliza para la previsión de series temporales. A nivel práctico, combina métodos de regresión automática con la media móvil. Los métodos de regresión automática usan los modelos de regresión tomando los valores de series temporales anteriores para calcular el siguiente punto de fecha. Los métodos ARIMA también aplican métodos de diferenciación que incluyen el cálculo de la diferencia entre los puntos de datos y el uso de estos, en lugar del valor medido original. Por último, ARIMA también hace uso de las técnicas de media móvil que se han explicado anteriormente. La combinación de todos estos métodos de distintas maneras es lo que construye la familia de métodos ARIMA.

En la actualidad, ETS y ARIMA se utilizan profusamente para la previsión de la demanda de energía y para muchos otros problemas de predicción. En muchos casos, se combinan para ofrecer resultados muy precisos.

**Regresión múltiple general**. Los modelos de regresión pueden ser el enfoque de modelado más importante del dominio de Machine Learning y de las estadísticas. En el contexto de la serie temporal usamos regresión para predecir los valores futuros (*por ejemplo*, de la demanda). En la regresión, tomamos una combinación lineal de los indicadores y obtenemos información acerca de las ponderaciones (también denominadas coeficientes) de dichos indicadores durante el proceso de aprendizaje. El objetivo es producir una línea de regresión que pronosticará nuestro valor predicho. Los métodos de regresión son adecuadas cuando la variable de destino es numérica y, por tanto, también se ajusta a la previsión de la serie temporal. Hay un gran número de métodos de regresión, entre los que se incluyen modelos de regresión muy sencillos, como la [regresión lineal](https://en.wikipedia.org/wiki/Linear_regression), y otros más avanzados, como los árboles de decisión, las [selvas aleatorias](https://en.wikipedia.org/wiki/Random_forest), las [redes neuronales](https://en.wikipedia.org/wiki/Artificial_neural_network) y los árboles de decisión incrementados.

La creación de una de previsión de la demanda de energía como un problema de regresión nos da mucha flexibilidad al seleccionar las características de datos que se pueden combinar entre los datos de serie temporal de la demanda real y factores externos, como la temperatura. En la sección Ingeniería de características (consulte **Preparación de datos e ingeniería de características**) de este cuaderno de estrategias se puede encontrar más información sobre las características seleccionadas.

Desde nuestra experiencia con la implementación de las previsiones de demanda energéticas piloto, hemos descubierto que los modelos de regresión avanzadas disponibles en Aprendizaje automático de Azure tienden a producir los mejores resultados, por los que son los que usamos.

## <a name="model-evaluation"></a>Evaluación del modelo
La evaluación del modelo tiene un rol crítico en el **ciclo de desarrollo del modelo**. En este paso se examina la validación del modelo y su rendimiento con datos reales. Durante el paso de modelado se usa una parte de los datos disponibles para entrenar el modelo. Durante la fase de evaluación se toma el resto de los datos para probar el modelo. En la práctica significa que introducimos los datos del nuevo modelo que se han reestructurado y contienen las mismas características que el conjunto de datos del entrenamiento. Sin embargo, en el proceso de validación se usa el modelo para predecir la variable de destino, en lugar de proporcionar la variable de destino disponible. A menudo, este proceso se denomina puntuación de modelo. Luego se usarían los valores de destino reales y se compararían con los pronosticados. El objetivo es medir y minimizar los errores de predicción, es decir, la diferencia entre las predicciones y el valor real. La cuantificación de la medida del error es clave, ya que deseamos ajustar el modelo y validar si el error se reduce. El ajuste del modelo se puede realizar bien mediante la modificación de los parámetros del modelo que controlan el proceso de aprendizaje bien mediante la incorporación o eliminación de características de datos (lo que se denomina [barrido de parámetros](https://channel9.msdn.com/Blogs/Azure/Data-Science-Series-Building-an-Optimal-Model-With-Parameter-Sweep)). En la práctica, esto significa existe la posibilidad de que sea preciso iterar entre las fases de ingeniería de características, modelado y evaluación del modelo varias veces hasta que se pueda reducir el error al nivel requerido.

Es importante hacer énfasis en que el error de predicción nunca será cero, ya que no existe ningún modelo que pueda predecir a la perfección todos los resultados. Sin embargo, la empresa puede llegar a aceptar cierto margen de error. En el proceso de validación, deseamos garantizar que nuestro error de predicción del modelo se ajusta al nivel de tolerancia de la empresa, o incluso no llega a él. Por lo tanto, es importante establecer el nivel del error tolerable al principio del ciclo, en la fase de **formulación del problema** .

### <a name="typical-evaluation-techniques"></a>Técnicas de evaluación típicas
Hay varias formas de medir y cuantificar los errores de predicción. Esta sección se centra en las técnicas de evaluación relevantes para la serie temporal y, en concreto, para la previsión de la demanda de energía.

#### <a name="mapehttpsenwikipediaorgwikimeanabsolutepercentageerror"></a>[**MAPE**](https://en.wikipedia.org/wiki/Mean_absolute_percentage_error)
MAPE son las siglas de Mean Absolute Percentage Error (error absoluto porcentual de la media). Con MAPE se calculan las diferencias entre cada punto pronosticado y el valor real del mismo. Después se cuantifica el error por punto, para lo que se calcula la proporción de diferencia con respecto al valor real. En el último paso, se obtiene la media de estos valores. Esta es la fórmula matemática que se usa para MAPE:

![Fórmula para MAPE](media/cortana-analytics-playbook-demand-forecasting-energy/mape-formula.png)
*Donde A<sub>t</sub> es el valor real, <sub>F</sub>t es el valor pronosticado y n es el horizonte de previsión.*

## <a name="deployment"></a>Implementación
Una vez que se ha determinado la fase de modelado y se ha validado el rendimiento, se puede pasar a la fase de implementación. En este contexto, implementación significa permitir al cliente consumir el modelo mediante la ejecución de predicciones reales a gran escala. El concepto de implementación es clave en Aprendizaje automático de Azure, ya que nuestro objetivo principal es invocar constantemente las predicciones, en lugar de obtener la información de los datos. La fase de implementación es la parte en que se habilita el modelo que se va a consumir a gran escala.

En el contexto de la previsión de la demanda energética, nuestro objetivo es invocar previsiones periódicas y continuadas, al mismo que nos aseguramos que hay datos nuevos disponibles para el modelo y que los datos de la previsión se devuelven al cliente que los consume.

### <a name="web-services-deployment"></a>Implementación de servicios web
El principal bloque de creación que se puede implementar en Aprendizaje automático de Azure es el servicio web. Esta es la manera más eficaz de permitir el consumo de un modelo predictivo en la nube. El servicio web encapsula el modelo con una API (interfaz de programación de aplicaciones) de [RESTful](http://www.restapitutorial.com/) API. La API se puede usar como parte de cualquier código de cliente, como se muestra en el diagrama siguiente.

![Implementación y consumo de servicio web](media/cortana-analytics-playbook-demand-forecasting-energy/web-service-deployment-and-consumption.png)

Como se puede ver, el servicio web se implementa en la nube del conjunto de aplicaciones Cortana Intelligence y se puede invocar a través de su punto de conexión de API de REST expuesto. Un tipo diferente de clientes de varios dominios puede invocar el servicio a través de la Web API simultáneamente. El servicio web también puede escalarse para admitir miles de llamadas simultáneas.

### <a name="a-typical-solution-architecture"></a>Arquitectura de una solución típica
Al implementar una solución de previsión de la demanda de energía, nos interesa implementar una solución de un extremo a otro que vaya más allá del servicio web de predicción y que facilite todo el flujo de datos. En el momento en que se invoque una previsión nueva, será preciso asegurarse de que el modelo se alimenta con características de datos actualizadas. Eso implica que los datos sin procesar recopilados recientemente se introducen, procesan y transforman constantemente en el conjunto de características requerido en el que se creó el modelo. Al mismo tiempo, deseamos que los datos de la previsión estén a disposición de los clientes finales que los consumen. En el diagrama siguiente se ilustra un ciclo de flujo de datos (o canalización de datos) de ejemplo:

![Flujo de datos de un extremo a otro de previsión de la demanda de energía](media/cortana-analytics-playbook-demand-forecasting-energy/energy-demand-forecase-end-data-flow.png)

Estos son los pasos que se tienen lugar como parte del ciclo de previsión de demanda de energía:

1. Los millones de medidores de datos implementados generan constantemente datos de consumo de energía en tiempo real.
2. Dichos datos de recopilan y cargan en un repositorio en la nube (*por ejemplo*, Blob de Azure).
3. Antes de procesarse, los datos sin procesar se agregan a una estación o al nivel regional que ha definido la empresa.
4. Luego, se produce el procesamiento de características (consulte **Preparación de datos y procesamiento de características**) y se generan los datos requeridos para el entrenamiento del modelo entrenamiento o puntuación (los datos del conjunto de características se almacenan en una base de datos;*por ejemplo*, SQL Azure).
5. Se invoca el servicio de entrenamiento para que vuelva a entrenar el modelo de pronóstico (la versión actualizada del modelo se conserva para que la puede usar el servicio web de puntuación).
6. El servicio web de puntuación se invoca en una programación que se ajuste a la frecuencia de previsión necesaria.
7. Los datos de las previsiones se almacenan en una base de datos a la que puede acceder el cliente final que los consume.
8. Dicho cliente recupera las previsiones, las vuelve a aplicar a la red de distribución de electricidad y los consume en función del caso de uso requerido.

Es importante tener en cuenta que todo este ciclo está completamente automatizado y se ejecuta según una programación. Toda la orquestación de este ciclo de datos puede realizarse mediante herramientas como [Data Factory de Azure](http://azure.microsoft.com/services/data-factory/).

### <a name="end-to-end-deployment-architecture"></a>Arquitectura de implementación de un extremo a otro
Para implementar de forma práctica una solución de previsión de demanda de energía en Cortana Intelligence, es preciso asegurarse de que los componentes necesarios están establecidos y configurados correctamente.

El siguiente diagrama muestra una arquitectura típica basada en Cortana Intelligence que implementa y orquesta el ciclo de flujo de datos que se ha descrito anteriormente:

![Arquitectura de implementación de un extremo a otro](media/cortana-analytics-playbook-demand-forecasting-energy/architecture.png)

Para más información tanto acerca de cada uno de los componentes como de la arquitectura completa, consulte la plantilla de soluciones energéticas.

