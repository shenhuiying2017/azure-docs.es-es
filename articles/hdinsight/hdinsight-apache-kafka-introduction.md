---
title: "Introducción a Apache kafka en HDInsight | Microsoft Docs"
description: "Información acerca de Apache Kafka en HDInsight. Qué es, qué hace y dónde encontrar ejemplos y obtener una introducción."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: f284b6e3-5f3b-4a50-b455-917e77588069
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/09/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 8c4e33a63f39d22c336efd9d77def098bd4fa0df
ms.openlocfilehash: a3ceca6cd0f470a5cd6849c345867f094b870a85
ms.lasthandoff: 04/20/2017

---
# <a name="introducing-apache-kafka-on-hdinsight-preview"></a>Introducción a Apache Kafka en HDInsight (versión preliminar)

[Apache Kafka](https://kafka.apache.org) es una plataforma de streaming distribuido de código abierto que se puede utilizar para compilar canalizaciones de datos de streaming en tiempo real y aplicaciones. Kafka también proporciona funcionalidad de agente de mensajería similar a una cola de mensajes, donde puede publicar y suscribirse a las transmisiones de datos con nombre. Kafka en HDInsight proporciona un servicio administrado, altamente escalable y de alta disponibilidad en la nube de Microsoft Azure.

## <a name="why-use-kafka-on-hdinsight"></a>¿Por qué usar Kafka en HDInsight?

Kafka ofrece las siguientes características:

* Patrón de mensajería de publicación y suscripción: Kafka proporciona una API de productor para publicar registros a un tema de Kafka. Al suscribirse a un tema, se utiliza la API de consumidor.

* Procesamiento de transmisiones: Kafka se suele utilizar con Apache Storm o Spark para el procesamiento de transmisiones en tiempo real. Kafka 0.10.0.0 (HDInsight versión 3.5) introdujo una API de streaming que permite generar soluciones de streaming sin necesidad de Storm ni Spark.

* Escala horizontal: Kafka particiona las transmisiones en los nodos del clúster de HDInsight. Los procesos del consumidor se pueden asociar con las particiones individuales para proporcionar equilibrio de carga al consumir los registros.

* Entrega en orden: dentro de cada partición, los registros se almacenan en la transmisión en el orden en que se recibieron. Al asociar un proceso de consumidor por partición, puede garantizar que los registros se procesan en orden.

* Tolerancia a errores: las particiones pueden replicarse entre los nodos para proporcionar tolerancia a errores.

## <a name="use-cases"></a>Casos de uso

* **Mensajería**: como admite el patrón de publicación de mensajes y suscripción, Kafka a menudo se utiliza como agente de mensajería.

* **Seguimiento de la actividad**: Kafka proporciona un registro en orden, por lo que se puede utilizar para realizar un seguimiento y volver a crear actividades. Por ejemplo, las acciones del usuario en un sitio web o dentro de una aplicación.

* **Agregación**: mediante el procesamiento de las transmisiones, puede agregar información de distintas transmisiones para combinar y centralizar la información en datos operativos.

* **Transformación**: mediante el procesamiento de las transmisiones, puede combinar y enriquecer los datos de varios temas de entrada en uno o más temas de salida.

## <a name="where-do-i-start"></a>¿Por dónde empiezo?

Consulte el artículo [Introducción a Kafka en HDInsight](hdinsight-apache-kafka-get-started.md) para conocer los pasos para crear un clúster Kafka y usar Kafka, ejemplos basados en Java del uso del productor, consumidor y la API de streaming incluidos.

## <a name="next-steps"></a>Pasos siguientes

Use los vínculos siguientes para aprender a usar a Apache Kafka en HDInsight:

* [Introducción a Kafka en HDInsight](hdinsight-apache-kafka-get-started.md)

* [Uso de MirrorMaker para crear una réplica de Kafka en HDInsight](hdinsight-apache-kafka-mirroring.md)

* [Uso de Apache Kafka con Storm en HDInsight](hdinsight-apache-storm-with-kafka.md)

* [Uso de Apache Spark con Kafka en HDInsight](hdinsight-apache-spark-with-kafka.md)

* [Conexión a Kafka a través de una red virtual de Azure](hdinsight-apache-kafka-connect-vpn-gateway.md)
