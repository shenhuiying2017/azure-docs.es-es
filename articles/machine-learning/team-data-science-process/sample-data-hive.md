---
title: Muestreo de datos en tablas de HDInsight Hive de Azure | Microsoft Docs
description: "Reducción del muestreo de datos en tablas de HDInsight Hive (Hadopop) de Azure"
services: machine-learning,hdinsight
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: f31e8d01-0fd4-4a10-b1a7-35de3c327521
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: bradsev
ms.openlocfilehash: d765c2adc8a3aa77d903490875c7f8ad622ef4d2
ms.sourcegitcommit: 659cc0ace5d3b996e7e8608cfa4991dcac3ea129
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/13/2017
---
# <a name="sample-data-in-azure-hdinsight-hive-tables"></a>Muestreo de datos en tablas de HDInsight Hive de Azure
En este artículo, se describe cómo reducir el tamaño de los datos almacenados en tablas de Azure HDInsight Hive mediante consultas de Hive para reducirlos a un tamaño más manejable para el análisis. En él se tratan tres métodos de muestreo que se utilizan normalmente:

* Muestreo aleatorio uniforme
* Muestreo aleatorio por grupos
* Muestreo estratificado

El siguiente **menú** está vinculado a temas que describen cómo realizar un muestreo de datos desde varios entornos de almacenamiento.

[!INCLUDE [cap-sample-data-selector](../../../includes/cap-sample-data-selector.md)]

**¿Por qué realizar un muestreo de los datos?**
Si el conjunto de datos que pretende analizar es grande, es recomendable reducirlo a un tamaño más pequeño, pero representativo, que sea más manejable. La reducción de tamaño facilita la comprensión y exploración de los datos, y el diseño de características. Su rol en el proceso de ciencia de datos en equipos es permitir la rápida creación de prototipos de las funciones de procesamiento de datos y de los modelos de aprendizaje automático.

Esta tarea de muestreo es un paso en el [proceso de ciencia de datos en equipos (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

## <a name="how-to-submit-hive-queries"></a>Cómo enviar consultas de Hive
Las consultas de Hive se pueden enviar desde la consola de línea de comandos de Hadoop del nodo principal del clúster de Hadoop. Para ello, inicie sesión en el nodo principal del clúster de Hadoop, abra la consola de la línea de comandos de Hadoop y envíe las consultas de Hive desde allí. Para obtener instrucciones sobre el envío de consultas de Hive en la consola de línea de comandos de Hadoop, consulte [Envío de consultas de Hive](move-hive-tables.md#submit).

## <a name="uniform"></a> Muestra aleatoria uniforme
El muestreo aleatorio uniforme implica que cada fila del conjunto de datos tiene la misma probabilidad de muestreo. Se puede implementar agregando un campo adicional de rand() al conjunto de datos en la consulta "select" interna y, en la consulta "select" externa, esa condición en ese campo aleatorio.

Aquí se muestra una consulta de ejemplo:

    SET sampleRate=<sample rate, 0-1>;
    select
        field1, field2, …, fieldN
    from
        (
        select
            field1, field2, …, fieldN, rand() as samplekey
        from <hive table name>
        )a
    where samplekey<='${hiveconf:sampleRate}'

En este caso, `<sample rate, 0-1>` especifica la proporción de registros que los usuarios quieren usar como muestra.

## <a name="group"></a> Muestreo aleatorio por grupos
Cuando se realiza un muestreo de datos de categoría, podría querer incluir o excluir todas las instancias de algún valor de la variable de categoría. Este tipo de muestreo se denomina "muestreo por grupos". Por ejemplo, si tiene una variable de categoría "*Estado*", que tiene valores como NY, MA, CA, NJ y PA, querrá que los registros de cada estado estén juntos, ya estén muestreados o no.

Aquí se muestra una consulta de ejemplo que realiza un muestreo por grupo:

    SET sampleRate=<sample rate, 0-1>;
    select
        b.field1, b.field2, …, b.catfield, …, b.fieldN
    from
        (
        select
            field1, field2, …, catfield, …, fieldN
        from <table name>
        )b
    join
        (
        select
            catfield
        from
            (
            select
                catfield, rand() as samplekey
            from <table name>
            group by catfield
            )a
        where samplekey<='${hiveconf:sampleRate}'
        )c
    on b.catfield=c.catfield

## <a name="stratified"></a>Muestreo estratificado
El muestreo aleatorio se estratifica con respecto a una variable de categoría cuando las muestras obtenidas tienen valores de categoría que están presentes en la misma proporción en que estaban en la población original. En el mismo ejemplo que el anterior, suponga que los datos tienen las siguientes observaciones según los estados: NJ tiene 100 observaciones, NY tiene 60 observaciones y WA tiene 300 observaciones. Si especifica que la tasa de muestreo estratificado sea 0,5, la muestra obtenida debería tener aproximadamente 50, 30 y 150 observaciones de NJ, NY y WA, respectivamente.

Aquí se muestra una consulta de ejemplo:

    SET sampleRate=<sample rate, 0-1>;
    select
        field1, field2, field3, ..., fieldN, state
    from
        (
        select
            field1, field2, field3, ..., fieldN, state,
            count(*) over (partition by state) as state_cnt,
              rank() over (partition by state order by rand()) as state_rank
          from <table name>
        ) a
    where state_rank <= state_cnt*'${hiveconf:sampleRate}'


Para obtener información sobre los métodos de muestreo más avanzados que están disponibles en Hive, consulte [Manual de lenguaje: muestreo](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Sampling).

