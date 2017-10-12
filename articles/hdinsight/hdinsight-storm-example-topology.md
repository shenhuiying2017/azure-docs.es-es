---
title: "Ejemplo de topologías de Apache Storm en HDInsight | Microsoft Docs"
description: "Una lista de topologías de ejemplo de Storm creada y probada con Apache Storm en HDInsight, incluidas las topologías básicas de C# y Java, y trabajando con Event Hubs."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: f9b1bdff-5928-4705-a76d-52fd200917cb
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/07/2017
ms.author: larryfr
ms.openlocfilehash: 84cd9ff84eafae1498313008081f45c87a2eb817
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="example-storm-topologies-and-components-for-apache-storm-on-hdinsight"></a>Topologías y componentes de ejemplo de Storm para Apache Storm en HDInsight

La siguiente es una lista de ejemplos creada y mantenida por Microsoft para su uso con Apache Storm en HDInsight. Estos ejemplos tratan diversos temas, desde la creación de topologías básicas en C# y Java para trabajar con servicios de Azure como Event Hubs, Cosmos DB, SQL Database, HBase en HDInsight y Azure Storage. Algunos ejemplos también muestran cómo trabajar con tecnologías que no son de Azure, o incluso que no son de Microsoft, como SignalR y Socket.IO.

| Descripción | Muestra | Lenguaje/Marco de trabajo |
|:--- |:--- |:--- |
| [Escritura en el Almacén de Azure Data Lake desde Apache Storm](hdinsight-storm-write-data-lake-store.md) |Escritura en el Almacén de Azure Data Lake |Java |
| [Origen de spout y bolt de Centro de eventos](https://github.com/apache/storm/tree/master/external/storm-eventhubs) |Origen de spout y bolt de Centro de eventos |Java |
| [Desarrollo de topologías basadas en Java para Apache Storm en HDInsight][5797064f] |Maven |Java |
| [Desarrollo de topologías de C# para Apache Storm en HDInsight con Visual Studio][16fce2d1] |Herramientas de HDInsight para Visual Studio |C#, Java |
| [Creación de varios flujos de datos en una topología de Storm en C#][ec5a4064] |Varios flujos |C# |
| [Procesamiento de eventos desde Azure Event Hubs con Storm en HDInsight (C#)][844d1d81] |Event Hubs |C# y Java |
| [Procesamiento de eventos desde Centros de eventos de Azure con Storm en HDInsight (Java)](hdinsight-storm-develop-java-event-hub-topology.md) |Event Hubs |Java |
| [Analyzing sensor data with Storm and HBase in HDInsight][ab894747] [Análisis de los datos de sensor con Storm y HBase en HDInsight] |Event Hubs, HBase, Socket.IO, panel Web |C#, Java, JavaScript, HTML |
| [Procesamiento de los datos de sensor del vehículo desde Event Hubs usando Storm en HDInsight][246ee964] |Event Hubs, Cosmos DB y Azure Storage Blob (WASB) |C#, Java |
| [Extracción, transformación y carga (ETL) desde Azure Event Hubs en HBase usando Storm en HDInsight][b4b68194] |Event Hubs, HBase |C# |
| [Proyecto de topología de Storm en C# para plantillas para trabajar con servicios de Azure desde Storm en HDInsight][ce0c02a2] |Event Hubs, Cosmos DB, SQL Database, HBase y SignalR |C#, Java |
| [Pruebas comparativas de escalabilidad para leer desde Azure Event Hubs usando Storm en HDInsight][d6c540e3] |Rendimiento de mensajes, Event Hubs, SQL Database |C#, Java |
| [Poner en correlación eventos con Storm y HBase en HDInsight](hdinsight-storm-correlation-topology.md) |HBase |C# |
| [Usar Python con Storm en HDInsight](hdinsight-storm-develop-python-topology.md) |Componentes de Python con una topología Flux |Python |
| [Uso de Kafka con Storm en HDInsight](hdinsight-apache-storm-with-kafka.md) | Lectura y escritura de Apache Storm en Apache Kafka | Java |

### <a name="next-steps"></a>Pasos siguientes

* [Introducción a Apache Storm en HDInsight][2b8c3488]
* [Implementación y administración de topologías de Storm con Storm en HDInsight][6eb0d3b8]

[2b8c3488]: hdinsight-apache-storm-tutorial-get-started-linux.md "Aprenda a crear un clúster de Storm en HDInsight y a utilizar el panel de Storm para implementar topologías de ejemplo."
[6eb0d3b8]: hdinsight-storm-deploy-monitor-topology.md "Aprenda a implementar y administrar topologías mediante el panel de Storm basado en web y la interfaz de usuario de Storm o las Herramientas de HDInsight para Visual Studio."
[16fce2d1]: hdinsight-storm-develop-csharp-visual-studio-topology.md "Aprenda a crear topologías de Storm en C# mediante las Herramientas de HDInsight para Visual Studio."
[5797064f]: hdinsight-storm-develop-java-topology.md "Aprenda a crear topologías de Storm en Java mediante Maven creando una topología de recuento de palabras básica."
[ec5a4064]: https://github.com/Blackmist/csharp-storm-example "Muestra una topología básica de Storm que realiza un recuento de palabras, implementada en C#. También muestra cómo crear varios flujos de datos dentro de una topología en C#."
[844d1d81]: hdinsight-storm-develop-csharp-event-hub-topology.md "Aprenda a leer y escribir datos desde Azure Event Hubs con Storm en HDInsight."
[ab894747]: hdinsight-storm-sensor-data-analysis.md "Aprenda a usar Apache Storm en HDInsight para procesar datos de sensor desde Azure Event Hubs, a visualizarlos mediante D3.js y (opcionalmente) a almacenarlos en HBase."
[246ee964]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/IotExample/README.md "Aprenda a usar una topología de Storm para leer mensajes desde Azure Event Hubs, leer documentos de Azure Cosmos DB para hacer referencia a datos y guardar datos en Azure Storage."
[d6c540e3]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/EventCountExample "Varias topologías para mostrar el rendimiento cuando se lee desde Azure Event Hubs y se realiza el almacenamiento en SQL Database mediante Apache Storm en HDInsight."
[b4b68194]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample "Aprenda a leer datos desde Azure Event Hubs, a agregar y transformar los datos y, a continuación, a almacenarlos en HBase en HDInsight."
[ce0c02a2]: https://github.com/hdinsight/hdinsight-storm-examples/tree/master/templates/HDInsightStormExamples "Este proyecto contiene plantillas para spouts, bolts y topologías para interactuar con distintos servicios de Azure como Event Hubs, Cosmos DB y SQL Database."

