---
title: Desarrollo de trabajos de Python MapReduce con HDInsight | Microsoft Docs
description: Aprenda a crear y ejecutar trabajos de MapReduce para Python en clústeres de HDInsight basado en Linux.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal

ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/11/2016
ms.author: larryfr

---
# <a name="develop-python-streaming-programs-for-hdinsight"></a>Desarrollo de programas de streaming para HDInsight
Hadoop proporciona una API de streaming para MapReduce que le permite escribir mapas y reducir funciones en lenguajes distintos de Java. En este artículo, aprenderá a usar Python para realizar operaciones de MapReduce.

> [!NOTE]
> Mientras el código Python de este documento se puede usar con un clúster de HDInsight basado en Windows, los pasos descritos en este documento son específicos de los clústeres basados en Linux.
> 
> 

Este artículo se basa en información y en los ejemplos publicados por Michael Noll en [Writing an Hadoop MapReduce Program in Python](http://www.michael-noll.com/tutorials/writing-an-hadoop-mapreduce-program-in-python/).

## <a name="prerequisites"></a>Requisitos previos
Para completar los pasos de este artículo, necesitará lo siguiente:

* Un clúster de Hadoop en HDInsight basado en Linux
* Un editor de texto
  
  > [!IMPORTANT]
  > El editor de texto debe usar LF como final de línea. Si usa CRLF, se producirán errores al ejecutar el trabajo MapReduce en clústeres de HDInsight basados en Linux. Si no está seguro, use el paso opcional de la sección [Ejecución de MapReduce](#run-mapreduce) para convertir cualquier CRLF que haya en LF.
  > 
  > 
* Para clientes Windows, PuTTY y PSCP. Estas utilidades están disponibles desde la <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">página de descarga de PuTTY</a>.

## <a name="word-count"></a>Recuento de palabras
Para este ejemplo, implementará un recuento de palabras básico mediante el uso de un asignador y un reductor. El asignador divide las oraciones en palabras individuales y el reductor agrega las palabras y los recuentos para generar la salida.

El siguiente diagrama de flujo ilustra lo que sucede durante las fases de asignación y reducción.

![ilustración de reducción del mapa](./media/hdinsight-hadoop-streaming-python/HDI.WordCountDiagram.png)

## <a name="why-python?"></a>¿Por qué Python?
Python es un lenguaje de programación de uso general y alto nivel que le permite expresar conceptos en menos líneas de código que muchos de los otros lenguajes. Se volvió popular recientemente entre los científicos de datos como un lenguaje para la creación de prototipos porque su naturaleza interpretada, sus tipos dinámicos y su sintaxis elegante lo hacen apto para un desarrollo de aplicaciones rápido.

Python está instalado en todos los clústeres de HDInsight.

## <a name="streaming-mapreduce"></a>Transmisión de MapReduce
Hadoop le permite especificar un archivo que contiene la lógica de asignación y reducción que usa un trabajo. Los requisitos específicos de la lógica de asignación y reducción son:

* **Entrada**: los componentes de asignación y reducción deben leer los datos de entrada desde STDIN.
* **Salida**: los componentes de asignación y reducción deben escribir los datos de salida en STDOUT.
* **Formato de datos**: los datos consumidos y producidos deben ser un par clave-valor, separado por un carácter de tabulación.

Python puede controlar fácilmente estos requisitos si usa el módulo **sys** para leer desde STDIN y **print** para imprimir a STDOUT. La tarea restante consiste simplemente en dar formato a los datos con un carácter de tabulación (`\t`) entre la clave y el valor.

## <a name="create-the-mapper-and-reducer"></a>Creación del asignador y del reductor
El asignador y el reductor son archivos de texto, en este caso **mapper.py** y **reducer.py** (para que quede claro qué hace cada uno). Puede crearlos con el editor que prefiera.

### <a name="mapper.py"></a>Mapper.py
Cree un archivo nuevo llamado **mapper.py** y use el siguiente código como el contenido:

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
        # Process each words returned from read_input
        for words in data:
            # Process each word
            for word in words:
                # Write to STDOUT
                print '%s%s%d' % (word, separator, 1)

    if __name__ == "__main__":
        main()

Dedique un momento para leer el código y comprender lo que hace.

### <a name="reducer.py"></a>reducer.py
Cree un archivo nuevo llamado **reducer.py** y use los siguientes elementos como el contenido:

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

## <a name="upload-the-files"></a>Carga de los archivos
Tanto **mapper.py** como **reducer.py** deben estar en el nodo principal del clúster antes de poder ejecutarlos. La forma más sencilla de cargarlos es usar **scp** (**pscp** si usa un cliente Windows).

Desde el cliente, en el mismo directorio en que se encuentran **mapper.py** y **reducer.py**, use el comando siguiente. Reemplace **nombredeusuario** con un usuario SSH y **nombredelclúster** por el nombre del clúster.

    scp mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:

De esta manera, se copiarán los archivos del sistema local al nodo principal.

> [!NOTE]
> Si usó una contraseña para proteger la cuenta SSH, se le preguntará la contraseña. Si usó una clave SSH, es posible que deba usar el parámetro `-i` y la ruta de acceso a la clave privada, por ejemplo, `scp -i /path/to/private/key mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:`.
> 
> 

## <a name="run-mapreduce"></a>Ejecución de MapReduce
1. Conéctese al clúster mediante SSH:
   
        ssh username@clustername-ssh.azurehdinsight.net
   
   > [!NOTE]
   > Si usó una contraseña para proteger la cuenta SSH, se le preguntará la contraseña. Si usó una clave SSH, es posible que deba usar el parámetro `-i` y la ruta de acceso a la clave privada, por ejemplo, `ssh -i /path/to/private/key username@clustername-ssh.azurehdinsight.net`.
   > 
   > 
2. (Opcional) Si, al crear los archivos mapper.py y reducer.py, usó un editor de texto en el que se emplea LF como final de línea, o bien no sabe qué final de línea usa su editor, use los siguientes comandos para convertir todas las apariciones de CRLF en mapper.py y reducer.py a LF.
   
        perl -pi -e 's/\r\n/\n/g' mappery.py
        perl -pi -e 's/\r\n/\n/g' reducer.py
3. Use el comando siguiente para iniciar el trabajo de MapReduce.
   
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files mapper.py,reducer.py -mapper mapper.py -reducer reducer.py -input wasbs:///example/data/gutenberg/davinci.txt -output wasbs:///example/wordcountout
   
    Este comando cuenta con las siguientes partes:
   
   * **hadoop-streaming.jar**: se usa cuando se realizan operaciones de streaming de MapReduce. Crea una interfaz de Hadoop con el código de MapReduce externo que proporciona.
   * **-files**: indica a Hadoop que los archivos especificados son necesarios en este trabajo de MapReduce y que se deben copiar a todos los nodos de trabajo.
   * **-mapper**: indica a Hadoop qué archivo debe usar como asignador.
   * **-reducer**: indica a Hadoop qué archivo debe usar como reductor.
   * **-input**: el archivo de entrada en el cual debemos contar las palabras.
   * **-output**: el directorio al que se escribirá la salida.
     
     > [!NOTE]
     > El trabajo creará este directorio.
     > 
     > 

Debiera ver gran cantidad de instrucciones **INFO** cuando el trabajo se inicie y, finalmente, debiera ver las operaciones **map** y **reduce** que aparecen como porcentajes.

    15/02/05 19:01:04 INFO mapreduce.Job:  map 0% reduce 0%
    15/02/05 19:01:16 INFO mapreduce.Job:  map 100% reduce 0%
    15/02/05 19:01:27 INFO mapreduce.Job:  map 100% reduce 100%

Recibirá información sobre el estado del trabajo cuando finalice.

## <a name="view-the-output"></a>Visualización de la salida
Una vez que se finalice el trabajo, use el siguiente comando para ver la salida:

    hdfs dfs -text /example/wordcountout/part-00000

Esta acción debiera mostrar una lista de palabras y cuántas veces aparecieron. El siguiente es un ejemplo de los datos de salida:

    wrenching       1
    wretched        6
    wriggling       1
    wrinkled,       1
    wrinkles        2
    wrinkling       2

## <a name="next-steps"></a>Pasos siguientes
Ahora que aprendió a usar los trabajos de transmisión de MapReduce con HDInsight, use los siguientes vínculos para explorar otras formas de trabajar con HDInsight de Azure.

* [Uso de Hive con HDInsight](hdinsight-use-hive.md)
* [Uso de Pig con HDInsight](hdinsight-use-pig.md)
* [Uso de trabajos de MapReduce con HDInsight](hdinsight-use-mapreduce.md)

<!--HONumber=Oct16_HO2-->


