---
title: "Ejecución de ejemplos de MapReduce de Hadoop en HDInsight y Azure | Microsoft Docs"
description: "Introducción al uso de ejemplos de MapReduce en archivos jar incluidos en HDInsight. Use SSH para conectarse al clúster y, a continuación, use el comando de Hadoop para ejecutar trabajos de ejemplo."
keywords: jar de ejemplo de hadoop, jar de ejemplos de hadoop, ejemplos de mapreduce de hadoop, ejemplos de mapreduce
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: e1d2a0b9-1659-4fab-921e-4a8990cbb30a
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: larryfr
ms.openlocfilehash: cb77c61fe0fb649a9098745b99a5c8d62be85971
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="run-the-mapreduce-examples-included-in-hdinsight"></a>Ejecución de los ejemplos de MapReduce incluidos en HDInsight

[!INCLUDE [samples-selector](../../includes/hdinsight-run-samples-selector.md)]

Aprenda a ejecutar los ejemplos de MapReduce incluidos con Hadoop en HDInsight.

## <a name="prerequisites"></a>Requisitos previos

* **Un clúster de HDInsight**: consulte [Introducción al uso de Hadoop con Hive en HDInsight en Linux](hdinsight-hadoop-linux-tutorial-get-started.md)

    > [!IMPORTANT]
    > Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las superiores. Consulte la información sobre la [retirada de HDInsight en Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

* **Un cliente SSH**: para obtener más información, consulte [Uso de SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="the-mapreduce-examples"></a>Ejemplos de MapReduce

**Ubicación**: Los ejemplos se encuentran en el clúster de HDInsight en `/usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar`.

**Contenido**: se incluyen los siguientes ejemplos en este archivo:

* `aggregatewordcount`: programa mapreduce basado en agregación que cuenta las palabras de los archivos de entrada.
* `aggregatewordhist`: programa mapreduce basado en agregación que cuenta el histograma de las palabras de los archivos de entrada.
* `bbp`: programa de mapreduce que usa una fórmula Bailey-Borwein-Plouffe para calcular los dígitos exactos de Pi.
* `dbcount`: trabajo de ejemplo que cuenta los registros de vistas de página almacenados en una base de datos.
* `distbbp`: programa de mapreduce que usa una fórmula de tipo BBP para calcular los bits exactos de Pi.
* `grep`: programa de mapreduce que cuenta las coincidencias de regex en la entrada.
* `join`: trabajo que realiza una unión de conjuntos de datos ordenados con particiones equiparables.
* `multifilewc`: trabajo que cuenta las palabras de varios archivos.
* `pentomino`: programa de mapreduce para la colocación de mosaicos con el fin de encontrar soluciones a problemas de pentominó.
* `pi`: programa de mapreduce que calcula Pi mediante un método cuasi Monte Carlo.
* `randomtextwriter`: programa de mapreduce que escribe 10 GB de datos de texto aleatorios por nodo.
* `randomwriter`: programa de mapreduce que escribe 10 GB de datos aleatorios por nodo.
* `secondarysort`: ejemplo que define una ordenación secundaria para la fase de reducción.
* `sort`: programa de mapreduce que ordena los datos escritos por el escritor aleatorio.
* `sudoku`: solucionador de sudokus.
* `teragen`: genera datos para la ordenación de terabytes (terasort).
* `terasort`: ejecuta la ordenación de terabytes (terasort).
* `teravalidate`: comprueba los resultados de la ordenación de terabytes (terasort).
* `wordcount`: programa de mapreduce que cuenta las palabras de los archivos de entrada.
* `wordmean`: programa de mapreduce que cuenta la longitud media de las palabras de los archivos de entrada.
* `wordmedian`: programa de mapreduce que cuenta la mediana de la longitud de las palabras de los archivos de entrada.
* `wordstandarddeviation`: programa de mapreduce que cuenta la desviación estándar de la longitud de las palabras de los archivos de entrada.

**Código fuente**: el código fuente de estos ejemplos se incluye en el clúster de HDInsight en `/usr/hdp/2.2.4.9-1/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples`.

> [!NOTE]
> El `2.2.4.9-1` en la ruta de acceso es la versión de Hortonworks Data Platform para el clúster de HDInsight y puede que sea diferente para su clúster.

## <a name="run-the-wordcount-example"></a>Ejecución del ejemplo de wordcount

1. Conéctese a HDInsight mediante SSH. Para más información, consulte [Uso SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. En el símbolo `username@#######:~$` , use el siguiente comando para mostrar los ejemplos:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar
    ```

    Este comando genera la lista de ejemplos de la sección anterior de este documento.

3. Use el siguiente comando para obtener ayuda sobre un ejemplo concreto. En este caso, el ejemplo **wordcount** :

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount
    ```

    Recibirá el siguiente mensaje:

        Usage: wordcount <in> [<in>...] <out>

    Este mensaje indica que puede proporcionar varias rutas de entrada para los documentos de origen. La ruta de acceso final es donde se almacena la salida (la cantidad de palabras en los documentos de origen).

4. Use lo siguiente para contar todas las palabras en los cuadernos de Leonardo Da Vinci, que se proporcionan como datos de ejemplo con su clúster:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/davinciwordcount
    ```

    La entrada de este trabajo se lee desde `/example/data/gutenberg/davinci.txt`. La salida de este ejemplo se almacena en `/example/data/davinciwordcount`. Ambas rutas de acceso se encuentran en el almacenamiento predeterminado del clúster, no en el sistema de archivos local.

   > [!NOTE]
   > Como se indica en la Ayuda del ejemplo wordcount, también puede especificar varios archivos de entrada. Por ejemplo, `hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/gutenberg/ulysses.txt /example/data/twowordcount` contaría las palabras de davinci.txt y ulysses.txt.

5. Una vez completado el trabajo, use el siguiente comando para ver la salida:

    ```bash
    hdfs dfs -cat /example/data/davinciwordcount/*
    ```

    Este comando concatena todos los archivos de salida generados por el trabajo. Muestra la salida en la consola. La salida será similar al siguiente texto:

        zum     1
        zur     1
        zwanzig 1
        zweite  1

    Cada línea representa una palabra y el número de veces que aparece en los datos de entrada.

## <a name="the-sudoku-example"></a>Ejemplo de Sudoku

[Sudoku](https://en.wikipedia.org/wiki/Sudoku) es un rompecabezas lógico compuesto por nueve cuadrículas de 3 × 3. Algunas celdas de la cuadrícula tienen números y otras están en blanco; el objetivo es resolver las celdas en blanco. El vínculo anterior contiene más información sobre el rompecabezas, pero el propósito de este ejemplo consiste en resolver las celdas en blanco. Así que nuestra entrada debe ser un archivo en el formato siguiente:

* Nueve filas de nueve columnas;
* Cada columna puede contener un número o `?` (que indica una celda en blanco);
* Las celdas se separan por un espacio.

Existe una forma determinada de construir rompecabezas sudoku para que no se repita un número en una columna o fila. Hay un ejemplo en el clúster de HDInsight que se ha creado correctamente. Se encuentra en `/usr/hdp/*/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta` y contiene el texto siguiente:

    8 5 ? 3 9 ? ? ? ?
    ? ? 2 ? ? ? ? ? ?
    ? ? 6 ? 1 ? ? ? 2
    ? ? 4 ? ? 3 ? 5 9
    ? ? 8 9 ? 1 4 ? ?
    3 2 ? 4 ? ? 8 ? ?
    9 ? ? ? 8 ? 5 ? ?
    ? ? ? ? ? ? 2 ? ?
    ? ? ? ? 4 5 ? 7 8

Para ejecutar este problema con el ejemplo sudoku, use el comando siguiente:

```bash
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar sudoku /usr/hdp/*/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta
```

Los resultados deberían asemejarse a este texto:

    8 5 1 3 9 2 6 4 7
    4 3 2 6 7 8 1 9 5
    7 9 6 5 1 4 3 8 2
    6 1 4 8 2 3 7 5 9
    5 7 8 9 6 1 4 2 3
    3 2 9 4 5 7 8 1 6
    9 4 7 2 8 6 5 3 1
    1 8 5 7 3 9 2 6 4
    2 6 3 1 4 5 9 7 8

## <a name="pi--example"></a>Ejemplo de PI (π)

El ejemplo pi usa un método estadístico (cuasi Monte Carlo) para calcular el valor de pi. Los puntos se colocan de forma aleatoria en un cuadrado unitario. El cuadrado también contiene un círculo. La probabilidad de que los puntos se encuentren dentro del círculo es igual al área del círculo, pi/4. El valor de pi se puede estimar a partir del valor de 4R. R es la proporción de la cantidad de puntos dentro del círculo con respecto al número total de puntos que se encuentran dentro del cuadrado. Mientras más grande sea la muestra de puntos usada, mejor resulta el valor calculado.

Use el siguiente comando para ejecutar este ejemplo. Este comando usa 16 asignaciones con 10 millones de ejemplos cada una para calcular el valor de pi:

```bash
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar pi 16 10000000
```

El valor devuelto debería asemejarse a **3,14159155000000000000**. Como referencia, las primeras 10 posiciones decimales de pi son 3,1415926535.

## <a name="10-gb-greysort-example"></a>Ejemplo de Greysort de 10 GB

GraySort es una ordenación de pruebas comparativas. La métrica es la velocidad de ordenación (TB/minuto) que se logra después de ordenar enormes volúmenes de datos, normalmente 100 TB, como mínimo.

Este ejemplo utiliza solo 10 GB de datos, para así poder ejecutarlo relativamente rápido. Usa las aplicaciones de MapReduce desarrolladas por Owen O'Malley y Arun Murthy. Estas aplicaciones ganaron el estándar de comparación anual de ordenación de terabytes de fin general ("daytona") en 2009 con una velocidad de 0,578 TB/min (100 TB en 173 minutos). Para obtener más información sobre este y otros estándares de comparación de ordenación, consulte el sitio [Sortbenchmark](http://sortbenchmark.org/) .

Este ejemplo utiliza tres conjuntos de programas de MapReduce:

* **TeraGen**: programa de MapReduce que genera filas de datos que se van a ordenar.

* **TeraSort**: toma una muestra de los datos de entrada y usa MapReduce para ordenar los datos de manera absoluta.

    TeraSort es una ordenación MapReduce estándar, salvo por el particionador personalizado. El particionador usa una lista ordenada de N-1 claves de muestra que definen el intervalo de claves para cada reducción. En concreto, todas las claves, como esa muestra[i-1] <= clave < muestra[i] se envían a la reducción i. Esta particionador garantiza que las salidas de la reducción i sean todas menores que la salida de la reducción i+1.

* **TeraValidate**: programa de MapReduce que valida que la salida se ordene de manera global.

    Crea una asignación por archivo en el directorio de salida y cada asignación asegura que cada clave es menor o igual que la anterior. La función de asignación genera registros de la primera y última clave de cada archivo. La función de reducción se asegura de que la primera clave del archivo i es mayor que la última clave del archivo i-1. Los problemas se notifican como una salida de la fase de reducción con las claves que no están en orden.

Utilice los siguientes pasos para generar datos, ordenarlos y, a continuación, validar el resultado:

1. Genere 10 GB de datos que se almacenen en el almacenamiento predeterminado del clúster de HDInsight en `/example/data/10GB-sort-input`:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapred.map.tasks=50 100000000 /example/data/10GB-sort-input
    ```

    `-Dmapred.map.tasks` indica a Hadoop cuántas tareas de asignación se usarán para el trabajo. Los dos parámetros finales indican al trabajo que cree 10 GB de datos y los almacene en `/example/data/10GB-sort-input`.

2. Use el siguiente comando para ordenar los datos:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-input /example/data/10GB-sort-output
    ```

    `-Dmapred.reduce.tasks` indica a Hadoop cuántas tareas de reducción se usarán para el trabajo. Los dos parámetros finales son simplemente las ubicaciones de entrada y salida de los datos.

3. Use lo siguiente para validar los datos generados por la ordenación:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-output /example/data/10GB-sort-validate
    ```

## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha obtenido información acerca de cómo ejecutar los ejemplos incluidos en los clústeres de HDInsight basado en Linux. Para obtener acceso a tutoriales sobre cómo usar Pig, Hive y MapReduce con HDInsight, consulte los siguientes temas:

* [Uso de Pig con Hadoop en HDInsight][hdinsight-use-pig]
* [Uso de Hive con Hadoop en HDInsight][hdinsight-use-hive]
* [Uso de MapReduce con Hadoop en HDInsight][hdinsight-use-mapreduce]

[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-sdk-documentation]: https://msdn.microsoft.com/library/azure/dn479185.aspx

[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-introduction]: hdinsight-hadoop-introduction.md



[hdinsight-samples]: hdinsight-run-samples.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
