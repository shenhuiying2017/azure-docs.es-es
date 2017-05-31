---
title: "Procesamiento de datos del sensor de vehículo con Apache Storm en HDInsight | Microsoft Docs"
description: "Aprenda a procesar datos del sensor de vehículo de Centros de eventos con Apache Storm en HDInsight. Agregue datos de modelo de Azure Cosmos DB y almacene el resultado en el almacenamiento."
services: hdinsight,documentdb,notification-hubs
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 78980635-8bef-4c33-96c3-fae50e932e31
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/03/2017
ms.author: larryfr
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 8e8ebc724e1c70e8fcd56312adef5da2342373ea
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017


---
# <a name="process-vehicle-sensor-data-from-azure-event-hubs-using-apache-storm-on-hdinsight"></a>Procese datos del sensor de vehículo de Centros de eventos de Azure con Apache Storm en HDInsight

Aprenda a procesar datos del sensor de vehículo de Centros de eventos de Azure con Apache Storm en HDInsight. En este ejemplo se leen datos del sensor de Event Hubs de Azure, que se enriquecen al hacer referencia a los datos almacenados en Azure Cosmos DB. Los datos se almacenan en Azure Storage con el sistema de archivos de Hadoop (HDFS).

![Diagrama de arquitectura de Internet de las cosas (IoT) y HDInsight](./media/hdinsight-storm-iot-eventhub-documentdb/iot.png)

## <a name="overview"></a>Información general

La adición de sensores a vehículos permite predecir problemas con los equipos en función de las tendencias de datos históricos. También permite realizar mejoras en futuras versiones en función del análisis de patrones de uso. Debe ser capaz de cargar los datos de todos los vehículos en Hadoop de forma rápida y eficaz antes de que se lleve a cabo el procesamiento de MapReduce. Además, puede que desee realizar análisis para rutas de error crítico (temperatura del motor, frenos, etc.) en tiempo real.

Event Hubs de Azure se crea para administrar el volumen masivo de datos generados por sensores. Apache Storm puede utilizarse para cargar y procesar los datos antes de almacenarlos en HDFS.

## <a name="solution"></a>Solución

Los datos de telemetría para la temperatura del motor, la temperatura ambiente y la velocidad del vehículo se registran mediante sensores. Los datos se envían a Event Hubs junto con el número de identificación del vehículo (VIN) del coche y una marca de tiempo. Desde allí, una topología de Storm ejecutándose en un clúster de Apache Storm en HDInsight lee los datos, los procesa y los almacena en HDFS.

Durante el procesamiento, el VIN se usa para recuperar información sobre el modelo desde Cosmos DB. Estos datos se agregan al flujo de datos antes de almacenarse.

Los componentes usados en la topología de Storm son:

* **EventHubSpout** : lee los datos de Centros de eventos de Azure.
* **TypeConversionBolt**: convierte la cadena JSON de Event Hubs en una tupla que contiene los siguientes datos del sensor:
    * Engine temperature
    * Temperatura ambiente
    * Velocidad
    * VIN
    * Timestamp
* **DataReferencBolt**: busca el modelo del vehículo desde Cosmos DB mediante el VIN.
* **WasbStoreBolt** : almacena los datos en HDFS (Almacenamiento de Azure).

La siguiente imagen es un diagrama de esta solución:

![topología de storm](./media/hdinsight-storm-iot-eventhub-documentdb/iottopology.png)

## <a name="implementation"></a>Implementación

Se puede encontrar una solución completa y automatizada para este escenario como parte del repositorio [HDInsight-Storm-Examples](https://github.com/hdinsight/hdinsight-storm-examples) en GitHub. Para utilizar este ejemplo, siga los pasos de [IoTExample README.MD](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/IotExample/README.md).

## <a name="next-steps"></a>Pasos siguientes

Para más topologías de ejemplo de Storm, vea [Topologías de ejemplo para Storm en HDInsight](hdinsight-storm-example-topology.md).


