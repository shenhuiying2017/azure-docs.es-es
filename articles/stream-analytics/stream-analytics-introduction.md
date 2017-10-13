---
title: "Introducción a Stream Analytics | Microsoft Docs"
description: "Obtenga información sobre Stream Analytics, un servicio administrado que le ayudará a analizar datos de streaming desde Internet de las cosas (IoT) en tiempo real."
keywords: "análisis como servicio, servicios administrados, procesamiento de transmisiones, análisis de transmisiones, qué es stream analytics"
services: stream-analytics
documentationcenter: 
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 613c9b01-d103-46e0-b0ca-0839fee94ca8
ms.service: stream-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 08/08/2017
ms.author: samacha
ms.openlocfilehash: 6bb70ba7588b8c6e9261fca097403e7fccb041eb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-stream-analytics"></a>¿Qué es Stream Analytics?

Azure Stream Analytics es un motor de procesamiento de eventos totalmente administrado que le permite configurar cálculos analíticos en tiempo real sobre datos en streaming. Los datos pueden proceder de dispositivos, sensores, sitios web, fuentes de redes sociales, aplicaciones, sistemas de infraestructura, etc. 

## <a name="what-can-i-do-with-stream-analytics"></a>¿Qué se puede hacer con Stream Analytics?

Use Stream Analytics para examinar grandes volúmenes de datos que fluyen desde dispositivos o procesos, extraer información del flujo de datos y buscar relaciones, tendencias y patrones. En función de lo que contengan los datos, después puede realizar tareas de aplicación. Por ejemplo, puede generar alertas, iniciar flujos de trabajo de automatización, proporcionar información a una herramienta de informes como Power BI o almacenar datos para analizarlos más adelante. 

Ejemplos:

* Análisis y alertas personalizados y en tiempo real de valores bursátiles que ofrecen las empresas de servicios financieros.
* Detección de fraudes en tiempo real basada en el examen de datos de transacciones. 
* Servicios de protección de datos e identidades.
* Análisis de datos generados por sensores y accionadores insertados en objetos físicos (Internet de las cosas o IoT).
* Análisis clickstream de Internet.
* Aplicaciones de administración de las relaciones con el cliente (CRM) que emiten alertas cuando la experiencia del cliente en un período de tiempo se ve mermada.

## <a name="how-does-stream-analytics-work"></a>¿Cómo funciona Stream Analytics?

En el diagrama se muestra la canalización de Stream Analytics y cómo se ingieren datos, se analizan y después se envían para presentarlos o para tomar una acción. 

![Canalización de Stream Analytics](./media/stream-analytics-introduction/stream_analytics_intro_pipeline.png)

Stream Analytics comienza con un origen de datos en streaming. Los datos se pueden ingerir en Azure desde un dispositivo mediante Azure Event Hubs o IoT Hub. También se pueden extraer los datos de un almacén de datos como Azure Blob Storage. 

Para examinar el flujo de datos, se crea un *trabajo* de Stream Analytics que especifica de dónde provienen los datos. Además, el trabajo especifica una *transformación*&mdash;cómo buscar datos, patrones o relaciones. Para esta tarea, Stream Analytics admite un lenguaje de consulta similar a SQL que le permite filtrar, ordenar, agregar y combinar datos en streaming durante un período de tiempo.

Por último, el trabajo especifica una salida a la que enviar datos transformados. Esto le permite controlar qué hacer en respuesta a la información que se ha analizado. Por ejemplo, en respuesta al análisis, puede:

* Enviar un comando para cambiar la configuración de un dispositivo. 
* Enviar datos a una cola supervisada por un proceso que realiza una acción en función de lo que encuentre. 
* Enviar datos a un panel de Power BI para crear informes.
* Enviar datos a un almacenamiento como Data Lake Store, una base de datos de SQL Server o Azure Blob o Table Storage.

Puede supervisar este trabajo y ajustar la cantidad de eventos que procesa por segundo mientras se ejecuta. También puede hacer que los trabajos generen registros de diagnóstico para solucionar problemas.

## <a name="key-capabilities-and-benefits"></a>Ventajas y principales capacidades

Stream Analytics se ha diseñado para ser fácil de usar, flexible, escalable a cualquier tamaño de trabajo y económico.

### <a name="connectivity-to-many-inputs-and-outputs"></a>Conectividad con muchas entradas y salidas

Stream Analytics se conecta directamente con [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) y con [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) para la ingesta de flujo de datos, y con el [servicio Azure Blob Storage](https://docs.microsoft.com/azure/storage/storage-introduction#blob-storage-accounts) para la ingesta de datos históricos. Si obtiene datos de Event Hubs, puede combinar Stream Analytics con otros orígenes de datos y motores de procesamiento.

La entrada de trabajo también puede incluir datos de referencia (datos estáticos o que cambian con lentitud). Puede combinar datos en streaming con estos datos de referencia para realizar operaciones de búsqueda de la misma manera que lo haría con las consultas de base de datos.

Redirija la salida del trabajo de Stream Analytics en muchas direcciones. Puede escribir en el almacenamiento, como blobs o tablas de Azure Storage, Azure SQL DB, instancias de Azure Data Lake Store o Azure Cosmos DB. Desde allí, los datos pueden pasar por el análisis por lotes mediante Azure HDInsight. Podría enviar la salida a otro servicio para que la consuma otro proceso, como Event Hubs o temas o colas de Azure Service Bus. Podría enviar la salida a Power BI para obtener una visualización.

### <a name="ease-of-use"></a>Facilidad de uso

Para definir transformaciones, use un [lenguaje de consulta de Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx) declarativo simple que le permite crear análisis sofisticados sin emplear programación. El lenguaje de consulta toma datos en streaming como entrada. Después, puede filtrar y ordenar los datos, agregar valores, realizar cálculos, combinar datos (dentro de un flujo o con datos de referencia) y usar funciones geoespaciales. Puede editar consultas en el portal, con IntelliSense y comprobación de sintaxis, y puede probar consultas usando datos de ejemplo que extraiga del flujo de datos en directo.

### <a name="extensible-query-language"></a>Lenguaje de consulta extensible

Puede ampliar la funcionalidad del lenguaje de consulta si define e invoca funciones adicionales. Puede definir llamadas a funciones en el servicio Azure Machine Learning para aprovechar las ventajas de las soluciones de Azure Machine Learning. También puede integrar funciones definidas por el usuario (UDF) de JavaScript para realizar cálculos complejos como parte de una consulta de Stream Analytics.

### <a name="scalability"></a>Escalabilidad

Stream Analytics puede controlar hasta 1 GB de datos entrantes por segundo. La integración con [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) y [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) permite que los trabajos ingieran millones de eventos por segundo procedentes de dispositivos conectados, secuencias de clic y archivos de registro, entre otros. Mediante la característica de partición de Event Hubs, puede dividir los cálculos en pasos lógicos, cada uno con la capacidad de ser subdividido aún más para aumentar la escalabilidad.

### <a name="low-cost"></a>Bajo costo

Como servicio en la nube, Stream Analytics se optimiza para que pueda empezar a usarlo a bajo costo. Se paga según se usa, basándose en la utilización de unidades de streaming y la cantidad de datos procesados por el sistema. La utilización se calcula en función del volumen de eventos procesados y la cantidad de potencia de proceso aprovisionada dentro del clúster para administrar trabajos de Stream Analytics.

### <a name="reliability-quick-recovery-and-repeatability"></a>Confiabilidad, recuperación rápida y capacidad de repetición

Como servicio administrado en la nube, Stream Analytics ayuda a evitar la pérdida de datos y proporciona continuidad empresarial. Si se producen errores, el servicio proporciona funcionalidades de recuperación integradas. Gracias a la posibilidad de mantener el estado internamente, el servicio proporciona resultados repetibles donde sea posible archivar eventos y volver a aplicar el procesamiento en el futuro, mientras que se obtienen los mismos resultados. Todo esto le permite retroceder en el tiempo e investigar los cálculos cuando realice análisis de causas raíz o análisis de hipótesis, entre otros.

## <a name="next-steps"></a>Pasos siguientes

* Empiece [experimentando con entradas y consultas desde dispositivos de IoT](stream-analytics-get-started-with-azure-stream-analytics-to-process-data-from-iot-devices.md).
* Cree una [solución de Stream Analytics de principio a fin](stream-analytics-real-time-fraud-detection.md) que examine metadatos de teléfono para buscar llamadas fraudulentas.
* Aprenda sobre el lenguaje de consulta similar a SQL para Stream Analytics y sobre conceptos únicos como [funciones de ventana](stream-analytics-window-functions.md).
* Aprenda a [escalar trabajos de Stream Analytics](stream-analytics-scale-jobs.md). 
* Aprenda a [integrar Stream Analytics y Azure Machine Learning](stream-analytics-machine-learning-integration-tutorial.md).
* Encuentre respuestas a sus preguntas sobre Stream Analytics en el [foro de Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

