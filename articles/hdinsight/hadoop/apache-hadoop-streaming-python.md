---
title: 'Desarrollo de trabajos de MapReduce de streaming de Python con HDInsight: Azure | Microsoft Docs'
description: Aprenda a usar Python en trabajos de MapReduce de streaming. Hadoop proporciona una API de streaming para MapReduce para escribir en lenguajes diferentes de Java.
services: hdinsight
keyword: mapreduce python,python map reduce,python mapreduce
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 7631d8d9-98ae-42ec-b9ec-ee3cf7e57fb3
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/17/2018
ms.author: larryfr
ms.openlocfilehash: 2563927684720a0be1144fa51aea7b9dae4abe7e
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/18/2018
---
# <a name="develop-python-streaming-mapreduce-programs-for-hdinsight"></a>Desarrollo de programas de MapReduce de streaming de Python para HDInsight

Aprenda a usar Python en operaciones de MapReduce de streaming. Hadoop proporciona una API de streaming para MapReduce que le permite escribir mapas y reducir funciones en lenguajes distintos de Java. Los pasos descritos en este documento implementan los componentes de asignación y reducción de Python.

## <a name="prerequisites"></a>requisitos previos

* Un clúster de Hadoop en HDInsight basado en Linux

  > [!IMPORTANT]
  > Los pasos descritos en este documento requieren un clúster de HDInsight que use Linux. Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las superiores. Consulte la información sobre la [retirada de HDInsight en Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* Un editor de texto

  > [!IMPORTANT]
  > El editor de texto debe usar LF como final de línea. El uso de un final de línea de CRLF provoca errores al ejecutar el trabajo de MapReduce en clústeres de HDInsight basados en Linux.

* Los comandos `ssh` y `scp` o [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-3.8.0)

## <a name="word-count"></a>Recuento de palabras

Este ejemplo es un recuento de palabras básico implementado en un asignador y reductor de Python. El asignador divide las oraciones en palabras individuales y el reductor agrega las palabras y los recuentos para generar la salida.

El siguiente diagrama de flujo ilustra lo que sucede durante las fases de asignación y reducción.

![Ilustración del proceso de MapReduce](./media/apache-hadoop-streaming-python/HDI.WordCountDiagram.png)

## <a name="streaming-mapreduce"></a>Transmisión de MapReduce

Hadoop le permite especificar un archivo que contiene la lógica de asignación y reducción que usa un trabajo. Los requisitos específicos de la lógica de asignación y reducción son:

* **Entrada**: los componentes de asignación y reducción deben leer los datos de entrada desde STDIN.
* **Salida**: los componentes de asignación y reducción deben escribir los datos de salida en STDOUT.
* **Formato de datos**: los datos consumidos y producidos deben ser un par clave-valor, separado por un carácter de tabulación.

Con Python se pueden controlar fácilmente estos requisitos mediante el uso del módulo `sys` para leer desde STDIN y `print` para imprimir en STDOUT. La tarea restante consiste simplemente en dar formato a los datos con un carácter de tabulación (`\t`) entre la clave y el valor.

## <a name="create-the-mapper-and-reducer"></a>Creación del asignador y del reductor

1. Cree un archivo llamado `mapper.py` y use el siguiente código como contenido:

   ```python
   #!/usr/bin/env python

   # Use the sys module
   import sys

   # 'file' in this case is STDIN
   def read_input(file):
       # Split each line into words
       for line in file:
           yield line.split()

   def main(separator='\t'):
       # Read the data using read_input
       data = read_input(sys.stdin)
       # Process each word returned from read_input
       for words in data:
           # Process each word
           for word in words:
               # Write to STDOUT
               print '%s%s%d' % (word, separator, 1)

   if __name__ == "__main__":
       main()
   ```

2. Cree un archivo nuevo llamado **reducer.py** y use el siguiente código como contenido:

   ```python
   #!/usr/bin/env python

   # import modules
   from itertools import groupby
   from operator import itemgetter
   import sys

   # 'file' in this case is STDIN
   def read_mapper_output(file, separator='\t'):
       # Go through each line
       for line in file:
           # Strip out the separator character
           yield line.rstrip().split(separator, 1)

   def main(separator='\t'):
       # Read the data using read_mapper_output
       data = read_mapper_output(sys.stdin, separator=separator)
       # Group words and counts into 'group'
       #   Since MapReduce is a distributed process, each word
       #   may have multiple counts. 'group' will have all counts
       #   which can be retrieved using the word as the key.
       for current_word, group in groupby(data, itemgetter(0)):
           try:
               # For each word, pull the count(s) for the word
               #   from 'group' and create a total count
               total_count = sum(int(count) for current_word, count in group)
               # Write to stdout
               print "%s%s%d" % (current_word, separator, total_count)
           except ValueError:
               # Count was not a number, so do nothing
               pass

   if __name__ == "__main__":
       main()
   ```

## <a name="run-using-powershell"></a>Ejecución con PowerShell

Para asegurarse de que los archivos tengan los finales de línea correctos, use el siguiente script de PowerShell:

[!code-powershell[main](../../../powershell_scripts/hdinsight/streaming-python/streaming-python.ps1?range=138-140)]

Use el siguiente script de PowerShell para cargar los archivos, ejecutar el trabajo y ver la salida:

[!code-powershell[main](../../../powershell_scripts/hdinsight/streaming-python/streaming-python.ps1?range=5-134)]

## <a name="run-from-an-ssh-session"></a>Ejecución desde una sesión de SSH

1. En el entorno de desarrollo, en el mismo directorio que `mapper.py` y `reducer.py`, use el comando siguiente:

    ```bash
    scp mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:
    ```

    Reemplace `username` por el nombre de usuario de SSH del clúster y `clustername` o el nombre de su clúster.

    Este comando copia los archivos del sistema local al nodo principal.

    > [!NOTE]
    > Si usó una contraseña para proteger su cuenta SSH, se le preguntará la contraseña. Si usó una clave SSH, es posible que deba usar el parámetro `-i` y la ruta de acceso a la clave privada. Por ejemplo, `scp -i /path/to/private/key mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:`.

2. Conéctese al clúster mediante SSH:

    ```bash
    ssh username@clustername-ssh.azurehdinsight.net`
    ```

    Para más información, consulte [Uso de SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

3. Para asegurarse de que mapper.py y reducer.py tengan los finales de línea correctos, use los siguientes comandos:

    ```bash
    perl -pi -e 's/\r\n/\n/g' mapper.py
    perl -pi -e 's/\r\n/\n/g' reducer.py
    ```

4. Use el comando siguiente para iniciar el trabajo de MapReduce.

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files mapper.py,reducer.py -mapper mapper.py -reducer reducer.py -input /example/data/gutenberg/davinci.txt -output /example/wordcountout
    ```

    Este comando cuenta con las siguientes partes:

   * **hadoop-streaming.jar**: se usa cuando se realizan operaciones de streaming de MapReduce. Crea una interfaz de Hadoop con el código de MapReduce externo que proporciona.

   * **-files**: agrega los archivos especificados al trabajo de MapReduce.

   * **-mapper**: indica a Hadoop qué archivo debe usar como asignador.

   * **-reducer**: indica a Hadoop qué archivo debe usar como reductor.

   * **-input**: el archivo de entrada en el cual debemos contar las palabras.

   * **-output**: el directorio en el que se escribe la salida.

    Cuando el trabajo de MapReduce funciona, el proceso se muestra como porcentajes.

        15/02/05 19:01:04 INFO mapreduce.Job:  map 0% reduce 0%    15/02/05 19:01:16 INFO mapreduce.Job:  map 100% reduce 0%    15/02/05 19:01:27 INFO mapreduce.Job:  map 100% reduce 100%


5. Para ver la salida, use el comando siguiente:

    ```bash
    hdfs dfs -text /example/wordcountout/part-00000
    ```

    Este comando muestra una lista de palabras y el número de veces que aparecieron.

## <a name="next-steps"></a>pasos siguientes

Ahora que aprendió a usar los trabajos de transmisión de MapReduce con HDInsight, use los siguientes vínculos para explorar otras formas de trabajar con HDInsight de Azure.

* [Uso de Hive con HDInsight](hdinsight-use-hive.md)
* [Uso de Pig con HDInsight](hdinsight-use-pig.md)
* [Uso de trabajos de MapReduce con HDInsight](hdinsight-use-mapreduce.md)
