---
title: Uso de repositorios de metadatos externos - Azure HDInsight | Microsoft Docs
description: "Use repositorios de metadatos externos con clústeres de HDInsight."
services: hdinsight
documentationcenter: 
author: mumian
manager: cgronlun
tags: azure-portal
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/16/2018
ms.author: jgao
ms.openlocfilehash: 767a1b8a8213b0139878c82d64639b2ba10b5f4f
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/22/2018
---
# <a name="use-external-metadata-stores-in-azure-hdinsight"></a>Uso de repositorios de metadatos externos en Azure HDInsight

Hive Metastore en HDInsight es una parte fundamental de la arquitectura de Hadoop. Una tienda de metadatos es el repositorio del esquema central que otras herramientas de acceso de macrodatos pueden usar, como Spark, Interactive Query (LLAP), Presto o Pig. HDInsight utiliza una base de datos de Azure SQL Database como Hive Metastore.

![Arquitectura del repositorio de metadatos de Hive en HDInsight](./media/hdinsight-use-external-metadata-stores/metadata-store-architecture.png)

Hay dos formas de configurar una tienda de metadatos para los clústeres de HDInsight:

* [Tienda de metadatos predeterminada](#default-metastore)
* [Tienda de metadatos personalizada](#custom-metastore)

## <a name="default-metastore"></a>Tienda de metadatos predeterminada

De forma predeterminada, HDInsight aprovisiona una tienda de metadatos con cada tipo de clúster. Pero en vez de esto, puede especificar una tienda de metadatos personalizada. La tienda de metadatos predeterminada incluye las siguientes consideraciones:
- No implica costes adicionales. HDInsight aprovisiona una tienda de metadatos con cada tipo de clúster sin ningún coste adicional para el usuario.
- Cada tienda de metadatos predeterminada forma parte del ciclo de vida del clúster. Al eliminar un clúster, también se eliminan esa tienda de metadatos y los metadatos.
- No puede compartir la tienda de metadatos predeterminada con otros clústeres.
- La tienda de metadatos predeterminada utiliza una base de datos Azure SQL DB básica, que tiene un límite de 5 DTU (unidad de transacción de base de datos).
Esta tienda de metadatos predeterminada se utiliza normalmente para las cargas de trabajo relativamente sencillas que no requieren varios clústeres y no necesitan que los metadatos se conserven más allá del ciclo de vida del clúster.


## <a name="custom-metastore"></a>Tienda de metadatos personalizada

HDInsight también admite tiendas de metadatos personalizadas, que se recomiendan para clústeres de producción:
- Como tienda de metadatos, se especifica su propia base de datos de Azure SQL Database.
- El ciclo de vida de la tienda de metadatos no está asociado a un ciclo de vida de los clústeres, por lo que puede crear y eliminar clústeres sin pérdida de metadatos. Los metadatos, como los esquemas de Hive, se conservarán incluso después de eliminar y volver a crear el clúster de HDInsight.
- Una tienda de metadatos personalizada le permite adjuntar varios clústeres y tipos de clústeres a esa tienda de metadatos. Por ejemplo, una tienda de metadatos sencilla puede compartirse a través de clústeres de Interactive Query, Hive y Spark en HDInsight.
- Se paga por el costo de una tienda de metadatos (Azure SQL DB) de acuerdo con el nivel de rendimiento que seleccione.
- Es posible escalar la tienda de metadatos según sea necesario.


![Caso de uso de repositorio de metadatos de Hive en HDInsight](./media/hdinsight-use-external-metadata-stores/metadata-store-use-case.png)

<!-- Image – Typical shared custom Metastore scenario in HDInsight (?) -->



### <a name="select-a-custom-metastore-during-cluster-creation"></a>Selección de una tienda de metadatos personalizada durante la creación del clúster

Puede apuntar el clúster a una instancia de Azure SQL Database generada anteriormente durante la creación del clúster, o puede configurar la instancia de SQL Database una vez creado el clúster. Esta opción se especifica en Almacenamiento > Tienda de metadatos al crear un nuevo clúster de Hadoop, Spark o Hive interactivo desde Azure Portal.

![Repositorio de metadatos de Hive en HDInsight: Azure Portal](./media/hdinsight-use-external-metadata-stores/metadata-store-azure-portal.png)

También puede agregar más clústeres a una tienda de metadatos personalizada desde Azure Portal o desde la configuración de Ambari (Hive > Advanced [Opciones avanzadas]).

![Repositorio de metadatos de Hive en HDInsight: Ambari](./media/hdinsight-use-external-metadata-stores/metadata-store-ambari.png)

## <a name="hive-metastore-best-practices"></a>Procedimientos recomendados de Hive Metastore

Estos son algunos procedimientos recomendados para Hive Metastore en HDInsight:

- Use una tienda de metadatos personalizada siempre que sea posible: esto le ayudará a separar los recursos de proceso (el clúster de ejecución) y los metadatos (almacenados en la tienda de metadatos).
- Comience con un nivel S2, que proporciona 50 DTU y 250 GB de almacenamiento. Si percibe un cuello de botella, puede escalar la base de datos.
- Asegúrese de que la tienda de metadatos creada para una versión del clúster de HDInsight no se comparta entre diferentes versiones del clúster de HDInsight. Diferentes versiones de Hive utilizan diferentes esquemas. Por ejemplo, no se puede compartir una tienda de metadatos con los clústeres de Hive 1.2 y 2.1.
- Realice una copia de la tienda de metadatos personalizada periódicamente.
- Mantenga su tienda de metadatos y el clúster de HDInsight en la misma región.
- Supervise el rendimiento y la disponibilidad de su tienda de metadatos mediante herramientas de supervisión de Azure SQL Database, como Azure Portal o Azure Log Analytics.

## <a name="oozie-metastore"></a>Oozie Metastore

Apache Oozie es un sistema de coordinación de flujos de trabajo que administra trabajos de Hadoop.  Oozie es compatible con los trabajos de Hadoop para Apache MapReduce, Pig, Hive y otros.  Oozie utiliza una tienda de metadatos para almacenar los detalles acerca de los flujos de trabajo actuales y completados. Para aumentar el rendimiento al usar Oozie, puede utilizar una instancia de Azure SQL Database como tienda de metadatos personalizada. La tienda de metadatos también puede proporcionar acceso a datos de trabajo de Oozie después de eliminar el clúster.

Para obtener instrucciones sobre cómo crear una tienda de metadatos de Oozie con Azure SQL Database, consulte este artículo sobre el [uso de Oozie para flujos de trabajo](hdinsight-use-oozie-linux-mac.md).

## <a name="next-steps"></a>pasos siguientes

- [Configuración de clústeres en HDInsight con Hadoop, Spark, Kafka, etc.](./hdinsight-hadoop-provision-linux-clusters.md)
