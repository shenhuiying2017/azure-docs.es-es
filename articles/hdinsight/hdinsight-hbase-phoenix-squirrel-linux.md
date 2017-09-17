---
title: Uso de Apache Phoenix y SQuirreL con HBase - Azure HDInsight | Microsoft Docs
description: "Aprenda a usar Apache Phoenix en HDInsight y cómo instalar y configurar SQuirreL en su estación de trabajo para conectarse a un clúster de HBase en HDInsight."
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: cda0f33b-a2e8-494c-972f-ae0bb482b818
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/06/2017
ms.author: jgao
ms.translationtype: HT
ms.sourcegitcommit: eeed445631885093a8e1799a8a5e1bcc69214fe6
ms.openlocfilehash: 342e29b009f51f7d1e9c3134a69cab6e30726bcd
ms.contentlocale: es-es
ms.lasthandoff: 09/07/2017

---
# <a name="use-apache-phoenix-with-linux-based-hbase-clusters-in-hdinsight"></a>Uso de Apache Phoenix con clústeres de HBase basados en Linux en HDInsight
Aprenda a utilizar [Apache Phoenix](http://phoenix.apache.org/) en HDInsight y SQLLine. Para obtener más información acerca de Phoenix, consulte [Phoenix en 15 minutos o menos](http://phoenix.apache.org/Phoenix-in-15-minutes-or-less.html). Para la gramática de Phoenix, vea [Gramática de Phoenix](http://phoenix.apache.org/language/index.html).

> [!NOTE]
> Para obtener información de la versión de Phoenix en HDInsight, consulte [Novedades en las versiones de clústeres de Hadoop proporcionadas por HDInsight](hdinsight-component-versioning.md).
>
>

## <a name="use-sqlline"></a>Uso de SQLLine
[SQLLine](http://sqlline.sourceforge.net/) es una utilidad de línea de comandos para ejecutar SQL.

### <a name="prerequisites"></a>Requisitos previos
Antes de poder usar SQLLine, debe disponer de los siguientes elementos:

* **Un clúster de HBase en HDInsight**. Para obtener información sobre el aprovisionamiento del clúster de HBase, consulte [Introducción a HBase Apache en HDInsight][hdinsight-hbase-get-started].
* **Conexión al clúster de HBase a través del protocolo de escritorio remoto**. Para obtener instrucciones, vea [Administración de clústeres de Hadoop en HDInsight mediante el Portal de Azure][hdinsight-manage-portal].

Cuando se conecte a un clúster de HBase, deberá conectarse a uno de los Zookeepers. Cada clúster de HDInsight tiene tres Zookeepers.

**Para averiguar el nombre de host de ZooKeeper**

1. Abra Ambari; para ello, vaya a **https://<ClusterName>.azurehdinsight.net**.
2. Escriba el nombre de usuario y la contraseña de HTTP (clúster) para iniciar sesión.
3. En el menú izquierdo, haga clic en **ZooKeeper** . Verá una lista con los tres **servidores de ZooKeeper**.
4. Haga clic en uno de los **servidores de ZooKeeper** que aparecen. En el panel Resumen, busque el **Nombre de host**. Es similar a *zk1-jdolehb.3lnng4rcvp5uzokyktxs4a5dhd.bx.internal.cloudapp.net*.

**Para usar SQLLine**

1. Conéctese al clúster con SSH. Para más información, consulte [Uso SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Desde SSH, introduzca los siguientes comandos para ejecutar SQLLine:

        cd /usr/hdp/2.2.9.1-7/phoenix/bin
        ./sqlline.py <ClusterName>:2181:/hbase-unsecure
3. Ejecute los siguientes comandos para crear una tabla de HBase e insertar algunos datos:

        CREATE TABLE Company (COMPANY_ID INTEGER PRIMARY KEY, NAME VARCHAR(225));

        !tables

        UPSERT INTO Company VALUES(1, 'Microsoft');

        SELECT * FROM Company;

        !quit

Para más información, consulte el [manual de SQLLine](http://sqlline.sourceforge.net/#manual) y la [gramática de Phoenix](http://phoenix.apache.org/language/index.html).

## <a name="next-steps"></a>Pasos siguientes
En este artículo, ha aprendido cómo utilizar Phoenix Apache en HDInsight.  Para obtener más información, consulte:

* [Información general de HBase de HDInsight][hdinsight-hbase-overview]: HBase es una base de datos NoSQL de código abierto Apache basada en Hadoop que proporciona acceso aleatorio y una coherencia sólida para grandes cantidades de datos no estructurados y semiestructurados.
* [Aprovisionamiento de clústeres de HBase en Azure Virtual Network][hdinsight-hbase-provision-vnet]: con la integración de redes virtuales, los clústeres de HBase se pueden implementar en la misma red virtual que sus aplicaciones para que estas puedan comunicarse directamente con HBase.
* [Configuración de la replicación de HBase en HDInsight](hdinsight-hbase-replication.md): aprenda a configurar la replicación de HBase entre dos centros de datos de Azure.
* [Análisis de sentimiento de Twitter con HBase en HDInsight][hbase-twitter-sentiment]: descubra cómo realizar [análisis de sentimiento](http://en.wikipedia.org/wiki/Sentiment_analysis) en tiempo real de macrodatos con HBase en un clúster de Hadoop en HDInsight.

[azure-portal]: https://portal.azure.com
[vnet-point-to-site-connectivity]: https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNETPT

[hdinsight-hbase-get-started]: hdinsight-hbase-tutorial-get-started.md
[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md
[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md

[hdinsight-hbase-phoenix-sqlline]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-phoenix-sqlline.png
[img-certificate]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-vpn-certificate.png
[img-vnet-diagram]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-vnet-point-to-site.png
[img-squirrel-driver]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-driver.png
[img-squirrel-alias]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-alias.png
[img-squirrel]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel.png
[img-squirrel-sql]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-sql.png

