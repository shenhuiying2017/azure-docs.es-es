---
title: "Introducción a Stream Analytics | Microsoft Docs"
description: "Obtenga información sobre Análisis de transmisiones, un servicio administrado que le ayudará a analizar datos de streaming desde Internet de las cosas (IoT) en tiempo real."
keywords: "análisis como servicio, servicios administrados, procesamiento de transmisiones, análisis de transmisiones, ¿qué es análisis de transmisiones"
services: stream-analytics
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 613c9b01-d103-46e0-b0ca-0839fee94ca8
ms.service: stream-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 01/24/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 2b4a10c77ae02ac0e9eeecf6d7d6ade6e4c33115
ms.openlocfilehash: 493a915fb4e3de4cb70324b22beeaae6720a3492


---
# <a name="what-is-stream-analytics"></a>¿Qué es Análisis de transmisiones?
Análisis de transmisiones de Azure es un motor de procesamiento de eventos en tiempo real, totalmente administrado y rentable que ayuda a obtener una visión detallada de los datos. Análisis de transmisiones facilita la configuración de los cálculos de análisis en tiempo real en el flujo de datos procedente de dispositivos, sensores, sitios web, medios sociales, aplicaciones, sistemas de infraestructura y mucho más.

Con unos cuantos clics en el Portal de Azure, puede crear un trabajo de Análisis de transmisiones mediante la especificación del origen de entrada de los datos de streaming, el receptor de salida para los resultados del trabajo y una transformación de los datos expresada en un lenguaje similar a SQL. Puede supervisar y ajustar la escala y la velocidad del trabajo en el Portal de Azure para escalar de unos pocos kilobytes a un gigabyte o más de eventos procesados por segundo.

Análisis de transmisiones aprovecha años de trabajo de Microsoft Research tanto en el desarrollo de motores de streaming altamente optimizados para el procesamiento dependiente del tiempo, como en la integración de lenguajes para las especificaciones intuitivas de estos.

## <a name="what-can-i-use-stream-analytics-for"></a>¿Para qué sirve Análisis de transmisiones?
En la actualidad, grandes cantidades de datos se transmiten a alta velocidad por la red. Las organizaciones que puedan procesar y administrar este flujo de datos en tiempo real, podrán mejorar enormemente su eficacia y diferenciarse en el mercado. Pueden encontrarse escenarios de análisis de streaming en tiempo real en todos los sectores: análisis y alertas personalizados y en tiempo real de los valores bursátiles que ofrecen las empresas de servicios financieros; detección de fraudes en tiempo real; servicios de protección de datos e identidades; ingesta y análisis confiables de los datos generados por sensores y accionadores incrustados en objetos físicos (Internet de las cosas o IoT); análisis clickstream en Internet; y aplicaciones de gestión de relaciones con el cliente (CRM) que emiten alertas cuando la experiencia del cliente en un período de tiempo se ve mermada. Las empresas buscan la manera más flexible, confiable y rentable de realizar tales análisis de datos de streaming de eventos en tiempo real para triunfar en el moderno mundo empresarial tan competitivo.

## <a name="key-capabilities-and-benefits"></a>Ventajas y principales capacidades
* **Facilidad de uso** : Análisis de transmisiones admite un modelo de consulta declarativa simple para describir las transformaciones. Con el fin de mejorar la facilidad de uso, Análisis de transmisiones usa una variante de T-SQL y libera a los clientes de las complejidades técnicas de los sistemas de procesamiento de flujos. Si se utiliza el [lenguaje de consulta de Análisis de transmisiones](https://msdn.microsoft.com/library/azure/dn834998.aspx) en el editor de consultas del explorador, la función de autocompletar de IntelliSense le ayudará a implementar de manera rápida y fácil consultas de series temporales, que incluyen combinaciones de base temporal, agregados de ventanas, filtros temporales y otras operaciones comunes, como combinaciones, agregados, proyecciones y filtros. Además, pruebas de consultas realizadas en el explorador en un archivo de datos de ejemplo para habilitar la implementación rápida e iterativa.  
* **Análisis de transmisiones** es capaz de controlar la capacidad de proceso de alto nivel de eventos de hasta 1 GB por segundo. La integración con [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) y [Centros de IoT de Azure](https://azure.microsoft.com/services/iot-hub/) permite que la solución ingiera millones de eventos por segundo procedentes de dispositivos conectados, secuencias de clics y archivos de registro, entre otros. Para ello, Análisis de transmisiones aprovecha la capacidad de particionamiento de los Centros de eventos, los cuales pueden producir 1 MB/s por partición. Los usuarios pueden particionar el cálculo en varios pasos lógicos dentro de la definición de consulta, con la posibilidad de particionarlos de manera adicional para aumentar la escalabilidad.  
* **Confiabilidad, capacidad de repetición y recuperación rápida** : al ser un servicio administrado en la nube, Análisis de transmisiones ayuda a evitar la pérdida de datos y proporciona continuidad empresarial en el caso de que se produzcan errores, gracias a sus funcionalidades de recuperación integradas. Gracias a la posibilidad de mantener el estado internamente, el servicio proporciona resultados repetibles donde sea posible archivar eventos y volver a aplicar el procesamiento en el futuro, mientras que se obtienen los mismos resultados. Todo esto permite a los clientes retroceder en el tiempo e investigar los cálculos cuando realicen análisis de causas raíz o análisis de hipótesis, entre otros.  
* **Bajo costo** : como servicio en la nube, Análisis de transmisiones está optimizado para que los usuarios pongan en marcha y mantengan sus soluciones de análisis en tiempo real a un costo ínfimo. El servicio se creó para que pague según lo usa, basándose en el uso de la unidad de streaming y la cantidad de datos procesados por el sistema. El uso se calcula en función del volumen de eventos procesados y la cantidad de potencia de proceso aprovisionada dentro del clúster, para gestionar los respectivos trabajos de análisis de streaming.  
* **Datos de referencia** : Análisis de transmisiones ofrece a los usuarios la posibilidad de especificar y usar datos de referencia. Podrían tratarse de datos históricos o simplemente de datos que no son de streaming que cambian con menos frecuencia con el tiempo. El sistema simplifica el uso de datos de referencia para que se consideren como cualquier otro streaming de eventos entrante que se une a otras operaciones de streaming de eventos ingeridos en tiempo real para realizar transformaciones.  
* **Funciones definidas por el usuario:** Análisis de transmisiones tiene integración con Aprendizaje automático de Azure para definir las llamadas de funciones en el servicio Aprendizaje automático como parte de una consulta de Análisis de transmisiones. Esto amplía las funcionalidades de Análisis de transmisiones para sacar provecho de las soluciones existentes de Aprendizaje automático de Azure. Para obtener mayor información al respecto, consulte el [tutorial de integración de Aprendizaje automático](stream-analytics-machine-learning-integration-tutorial.md).
* **Conectividad** : Análisis de transmisiones se conecta con Centros de eventos de Azure y con Centros de IoT de Azure de forma directa para la ingesta de transmisiones y con el servicio BLOB de Azure para los datos históricos. Los resultados se pueden escribir desde Análisis de transmisiones en tablas o blobs de Almacenamiento de Azure, Base de datos SQL de Azure, Azure Data Lake Store, DocumentDB, Centros de eventos, temas o colas de Bus de servicio de Azure y Power BI, donde se podrán visualizar, los podrán procesar los flujos de trabajo, se podrán usar en el análisis por lotes a través de [HDInsight de Azure](https://azure.microsoft.com/services/hdinsight/) o se podrán volver a procesar como una serie de eventos. Cuando se usan los Centros de eventos, es posible integrar varios Análisis de transmisiones con otros orígenes de datos y motores de procesamiento sin perder el carácter de streaming de los cálculos.  

## <a name="get-help"></a>Obtener ayuda
Para obtener más ayuda, pruebe nuestro [foro de Análisis de transmisiones de Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Pasos siguientes
Ya conoce Análisis de transmisiones, un servicio administrado para el análisis del streaming de datos desde Internet de las cosas. Para obtener más información acerca de este servicio, consulte:

* [Introducción al uso de Análisis de transmisiones de Azure](stream-analytics-get-started.md)
* [Escalación de trabajos de Análisis de transmisiones de Azure](stream-analytics-scale-jobs.md)
* [Referencia del lenguaje de consulta de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referencia de API de REST de administración de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)




<!--HONumber=Jan17_HO4-->


