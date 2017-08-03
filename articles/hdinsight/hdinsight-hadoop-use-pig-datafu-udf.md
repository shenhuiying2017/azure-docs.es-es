---
title: Uso de DataFu con Pig en HDInsight - Azure | Microsoft Docs
description: "DataFu es un conjunto de bibliotecas para su uso con Hadoop. Aprenda cómo usar DataFu con Pig en su clúster de HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 0016721a-82be-4773-88ad-91e6b2c21cbb
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/04/2017
ms.author: larryfr
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 272159d5017e62d1f606e69d6bdcde8ecbc0e3bf
ms.contentlocale: es-es
ms.lasthandoff: 07/08/2017


---
# <a name="use-datafu-with-pig-on-hdinsight"></a>Uso de DataFu con Pig en HDInsight

Aprenda a usar DataFu con HDInsight. DataFu es un conjunto de bibliotecas de código abierto que se usan con Pig en Hadoop.

## <a name="prerequisites"></a>Requisitos previos

* Una suscripción de Azure.

* Clúster de HDInsight de Azure (basado en Linux o Windows)

  > [!IMPORTANT]
  > Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las superiores. Consulte la información sobre la [retirada de HDInsight en Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

* Cierta familiaridad básica con el [uso de Pig en HDInsight](hdinsight-use-pig.md)

## <a name="install-datafu-on-linux-based-hdinsight"></a>Instalación de DataFu en HDInsight basado en Linux

> [!NOTE]
> DataFu se instala en clústeres basados en Linux 3.3 y versiones posteriores, así como en clústeres basados en Windows. No se instala en clústeres basados en Linux anteriores a la versión 3.3.
>
> Si usa un clúster basado en Linux versión 3.3 o superior, o bien un clúster basado en Windows, puede omitir esta sección.

DataFu puede descargarse e instalarse desde el repositorio de Maven. Use los pasos siguientes para agregar DataFu a su clúster de HDInsight:

1. Conéctese al clúster de HDInsight basado en Linux mediante SSH. Para más información, consulte [Uso SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Use el comando siguiente para descargar el archivo jar de DataFu mediante la utilidad wget, o copie y pegue el vínculo en el explorador para iniciar la descarga.

    ```
    wget http://central.maven.org/maven2/com/linkedin/datafu/datafu/1.2.0/datafu-1.2.0.jar
    ```

3. Después, cargue el archivo en el almacenamiento predeterminado para el clúster de HDInsight. La colocación del archivo en el almacenamiento predeterminado permite que esté disponible para todos los nodos del clúster.

    ```
    hdfs dfs -put datafu-1.2.0.jar /example/jars
    ```

    > [!NOTE]
    > El comando anterior almacena el archivo jar en `/example/jars` dado que este directorio ya existe en el almacenamiento de clúster. Puede usar la ubicación que desee en el almacenamiento del clúster de HDInsight.

## <a name="use-datafu-with-pig"></a>Uso de DataFu con Pig

En los pasos de esta sección se supone que está familiarizado con el uso de Pig en HDInsight. Para obtener más información acerca del uso de Pig con HDInsight, consulte [Uso de Pig con HDInsight](hdinsight-use-pig.md).

> [!IMPORTANT]
> Al usar DataFu desde Pig en un clúster de HDInsight basado en Linux, primero debe registrar el archivo .jar.
>
> Si el clúster usa Azure Storage, utilice una ruta `wasb://`. Por ejemplo: `register wasb:///example/jars/datafu-1.2.0.jar`.
>
> Si el clúster usa Azure Data Lake Store, utilice una ruta `adl://`. Por ejemplo: `register adl://home/example/jars/datafu-1.2.0.jar`.
>
> DataFu se registra de forma predeterminada en los clústeres de HDInsight basados en Windows.

Normalmente, definirá un alias para las funciones de DataFu.Normalmente, definirá un alias para las funciones de DataFu. Por ejemplo:

    DEFINE SHA datafu.pig.hash.SHA();

Esta instrucción define un alias llamado `SHA` para la función de hash de SHA. Después puede usarlo en un script de Pig Latin para generar un valor hash para los datos de entrada. Por ejemplo, el siguiente código reemplaza los nombres de los datos de entrada por un valor hash:

```piglatin
raw = LOAD '/data/raw/' USING PigStorage(',') AS  
    (name:chararray,
    int1:int,
    int2:int,
    int3:int);
mask = FOREACH raw GENERATE SHA(name), int1, int2, int3;
DUMP mask;
```

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

Se genera lo siguiente:

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

