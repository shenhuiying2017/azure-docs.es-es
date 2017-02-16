---
title: Uso de DataFu con Pig en HDInsight
description: "DataFu es un conjunto de bibliotecas para su uso con Hadoop. Aprenda cómo usar DataFu con Pig en su clúster de HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 0016721a-82be-4773-88ad-91e6b2c21cbb
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/08/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 8c07f0da21eab0c90ad9608dfaeb29dd4a01a6b7
ms.openlocfilehash: 4ebe010ab1f3d9df8bef1a6fc2ac5c24a7d1f5ee


---
# <a name="use-datafu-with-pig-on-hdinsight"></a>Uso de DataFu con Pig en HDInsight

DataFu es un conjunto de bibliotecas de código abierto para su uso con Hadoop. En este documento, obtendrá información sobre cómo usar DataFu en su clúster de HDInsight y cómo usar funciones definida por usuario (UDF) de DataFu con Pig.

## <a name="prerequisites"></a>Requisitos previos

* Una suscripción de Azure.
* Clúster de HDInsight de Azure (basado en Linux o Windows)

  > [!IMPORTANT]
  > Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las superiores. Para más información, consulte [El contrato de nivel de servicio para las versiones de clúster de HDInsight](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

* Cierta familiaridad básica con el [uso de Pig en HDInsight](hdinsight-use-pig.md)

## <a name="install-datafu-on-linux-based-hdinsight"></a>Instalación de DataFu en HDInsight basado en Linux

> [!NOTE]
> DataFu se instala en clústeres basados en Linux 3.3 y versiones posteriores, así como en clústeres basados en Windows. No se instala en clústeres basados en Linux anteriores a la versión 3.3.
> 
> Si usa un clúster basado en Linux versión 3.3 o superior, o bien un clúster basado en Windows, puede omitir esta sección.

DataFu puede descargarse e instalarse desde el repositorio de Maven. Use los pasos siguientes para agregar DataFu a su clúster de HDInsight:

1. Conéctese al clúster de HDInsight basado en Linux mediante SSH. Para obtener más información sobre el uso de SSH con HDInsight, consulta uno de los siguientes documentos:
   
    * [Uso de SSH con Hadoop en HDInsight basado en Linux desde Linux, OS X y Unix](hdinsight-hadoop-linux-use-ssh-unix.md)
    * [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Windows](hdinsight-hadoop-linux-use-ssh-unix.md)

2. Use el comando siguiente para descargar el archivo jar de DataFu mediante la utilidad wget, o copie y pegue el vínculo en el explorador para iniciar la descarga.
   
        wget http://central.maven.org/maven2/com/linkedin/datafu/datafu/1.2.0/datafu-1.2.0.jar

3. Después, cargue el archivo en el almacenamiento predeterminado para el clúster de HDInsight. Esto hace que el archivo esté disponible para todos los nodos del clúster y que el archivo permanezca en el almacenamiento aunque se elimine y se vuelva a crear el clúster.
   
        hdfs dfs -put datafu-1.2.0.jar /example/jars
   
    > [!NOTE]
    > En el ejemplo anterior se almacena el archivo jar en `wasbs:///example/jars` ya que este directorio ya existe en el almacenamiento del clúster. Puede usar la ubicación que desee en el almacenamiento del clúster de HDInsight.

## <a name="use-datafu-with-pig"></a>Uso de DataFu con Pig

Los pasos descritos en esta sección asumen que está familiarizado con el uso de Pig en HDInsight y solo se proporcionan las instrucciones de Pig Latin, no los pasos necesarios para usarlos con el clúster. Para obtener más información acerca del uso de Pig con HDInsight, consulte [Uso de Pig con HDInsight](hdinsight-use-pig.md).

> [!IMPORTANT]
> Al usar DataFu desde Pig en un clúster de HDInsight basado en Linux, primero debe registrar el archivo jar mediante la siguiente instrucción de Pig Latin:
> 
> ```register wasbs:///example/jars/datafu-1.2.0.jar```
> 
> DataFu se registra de forma predeterminada en los clústeres de HDInsight basados en Windows.

Normalmente, definirá un alias para las funciones de DataFu. Por ejemplo:

    DEFINE SHA datafu.pig.hash.SHA();

Se define un alias denominado `SHA` para la función hash de SHA. Después puede usarlo en un script de Pig Latin para generar un valor hash para los datos de entrada. Por ejemplo, lo siguiente reemplaza los nombres de los datos de entrada con un valor hash:

    raw = LOAD '/data/raw/' USING PigStorage(',') AS  
        (name:chararray, 
        int1:int, 
        int2:int,
        int3:int); 
    mask = FOREACH raw GENERATE SHA(name), int1, int2, int3; 
    DUMP mask;

Si se usa con los siguientes datos de entrada:

    Lana Zemljaric,5,9,1
    Qiong Zhong,9,3,6
    Sandor Harsanyi,0,7,3
    Roko Petkovic,2,6,2
    Tibor Rozsa,8,0,0
    Lea Hrastovsek,6,3,6
    Regina Toth,2,1,2
    Eva Makay,8,9,2
    Shi Liao,4,6,0
    Tjasa Zemljaric,0,2,5

El resultado será el siguiente:

    (c1a743b0f34d349cfc2ce00ef98369bdc3dba1565fec92b4159a9cd5de186347,5,9,1)
    (713d030d621ab69aa3737c8ea37a2c7c724a01cd0657a370e103d8cdecac6f99,9,3,6)
    (7a5f5abdd281f68168199319d98a1a662535f988d1443b3a3c497010937bac89,0,7,3)
    (a94818e93807e12079c4b35f8f3c8c8ef8e8acd1954e7f0476bc1a3a86fc96a9,2,6,2)
    (894ead4f48af91df7e088241218a23157bede7c52115272e417e95c046d48902,8,0,0)
    (6f99f163af3448fda672087db306f363e27a98a9e49c1f274a0860e303f8aec4,6,3,6)
    (a03de92a28be3c6a984c7a153fa9ed81c0413f76a9401955b5f7e04a5dd0ab9f,2,1,2)
    (6ceab977c8fb48d9ad0dc413e6bc646cabd89f22e7ab97a6b0133f3d225c6013,8,9,2)
    (fa9c436469096ff1bd297e182831f460501b826272ae97e921f5f6e3f54747e8,4,6,0)
    (bc22db7c238b86c37af79a62c78f61a304b35143f6087eb99c34040325865654,0,2,5)

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre DataFu o Pig, consulte los documentos siguientes:

* [Guía de Pig de Apache DataFu](http://datafu.incubator.apache.org/docs/datafu/guide.html).
* [Uso de Pig con HDInsight](hdinsight-use-pig.md)




<!--HONumber=Jan17_HO3-->


