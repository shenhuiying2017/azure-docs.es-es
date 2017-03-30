---
title: "Uso de componentes de Python en una topología Storm en HDinsight | Microsoft Docs"
description: "Obtenga información acerca de cómo puede usar los componentes de Python con Apache Storm en HDInsight de Azure. Aprenderá a usar los componentes de Python desde una topología Storm basada en Clojure y una topología basada en Java."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: edd0ec4f-664d-4266-910c-6ecc94172ad8
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/12/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 8b32aa77e1dbe18076d73e10914b59be107c3588
ms.lasthandoff: 03/25/2017


---
# <a name="develop-apache-storm-topologies-using-python-on-hdinsight"></a>Desarrollo de topologías Apache Storm con Python en HDInsight

Apache Storm admite varios lenguajes, e incluso le permite combinar componentes de varios lenguajes en una topología. En este documento, obtendrá información sobre cómo usar componentes de Python en las topologías Java y Storm basado en Clojure en HDInsight.

> [!IMPORTANT]
> Este documento proporciona pasos sobre el uso de clústeres de HDInsight basados en Linux y Windows. Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las superiores. Para más información, consulte [El contrato de nivel de servicio para las versiones de clúster de HDInsight](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

## <a name="prerequisites"></a>Requisitos previos

* Python 2.7 o versiones posteriores
* Java JDK 1.7 o superior
* [Leiningen](http://leiningen.org/)

## <a name="storm-multi-language-support"></a>Compatibilidad con varios lenguajes de Storm

Storm se diseñó para funcionar con componentes escritos con cualquier lenguaje de programación; sin embargo, para ello es necesario que los componentes comprendan cómo trabajar con la [definición de Thrift para Storm](https://github.com/apache/storm/blob/master/storm-core/src/storm.thrift). Para Python, se proporciona un módulo como parte del proyecto de Apache Storm que le permite interactuar fácilmente con Storm. Puede encontrar este módulo en [https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py](https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py).

Como Apache Storm es un proceso de Java que se ejecuta en Java Virtual Machine (JVM), los componentes escritos en otros lenguajes se ejecutan como subprocesos. Los bits de Storm que se ejecutan en JVM se comunican con estos subprocesos mediante mensajes JSON enviados a través de stdin y stdout. Se puede encontrar más detalles sobre la comunicación entre los componentes en la documentación de [Multi-lang protocolo](https://storm.apache.org/documentation/Multilang-protocol.html) (Protocolo de varios lenguajes).

### <a name="the-storm-module"></a>La interfaz de usuario de Storm
El módulo de Storm (https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py,) proporciona los bits necesarios para crear componentes de Python que funcionen con Storm.

Esto proporciona cosas como `storm.emit` para emitir tuplas, y `storm.logInfo` para escribir en los registros. Resulta conveniente echar un vistazo a este archivo y comprender lo que proporciona.

## <a name="challenges"></a>Desafíos
Con el módulo **storm.py** , puede crear spouts de Python que consumen datos y bolts que procesan datos; sin embargo, la definición general de topología Storm que permite la comunicación entre los componentes se sigue escribiendo con Java o Clojure. Además, si usa Java, también debe crear componentes de Java que actúen como interfaz para los componentes de Python.

También, como los clústeres de Storm se ejecutan de forma distribuida, debe asegurarse de que los módulos que necesiten los componentes de Python estén disponibles en todos los nodos de trabajo del clúster. Storm no proporciona ninguna manera fácil de realizar esto con recursos de varios lenguajes, así que o bien deberá incluir todas las dependencias como parte del archivo jar en la topología, o instalar manualmente dependencias en cada nodo de trabajo del cluster.

### <a name="java-vs-clojure-topology-definition"></a>Definición de la topología Java frente a Clojure
De los dos métodos de la definición de una topología, Clojure es, sin duda, el más fácil y limpio dado que se puede hacer referencia directa a componentes de Python en la definición de la topología. En las definiciones de topologías basadas en Java, también debe definir componentes de Java que administren cosas como declarar los campos de las tuplas devueltas por los componentes de Python.

Ambos métodos se describen en este documento, junto con proyectos de ejemplo.

## <a name="python-components-with-a-java-topology"></a>Componentes de Python con una topología Java
> [!NOTE]
> Este ejemplo está disponible en [https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount) en el directorio de **JavaTopology** . Se trata de un proyecto basado en Maven. Si no está familiarizado con Maven,consulte [Desarrollo de topologías basadas en Java con Apache Storm en HDInsight](hdinsight-storm-develop-java-topology.md) para obtener más información sobre cómo crear un proyecto de Maven en una topología Storm.
> 
> 

Una topología basada en Java que usa Python (u otros componentes de lenguaje JVM) parece inicialmente que usa componentes de Java; pero si mira cada uno de los spouts o bolts de Java, verá código similar al siguiente:

    public SplitBolt() {
        super("python", "countbolt.py");
    }

Aquí es donde Java invoca a Python y ejecuta el script que contiene la lógica real del bolt. Los bolts y spouts de Java (en este ejemplo), simplemente declaran los campos de la tupla que emitirá el componente subyacente de Python.

Los archivos de Python reales se almacenan en el directorio `/multilang/resources` en este ejemplo. Se hace referencia al directorio `/multilang` en el archivo **pom.xml**:

<resources>
    <resource>
        <!-- Where the Python bits are kept -->
        <directory>${basedir}/multilang</directory>
    </resource>
</resources>

Esto incluye todos los archivos de la carpeta `/multilang` del archivo jar que se generarán a partir de este proyecto.

> [!IMPORTANT]
> Tenga en cuenta que solo se especifica el directorio `/multilang` y no `/multilang/resources`. Storm no espera recursos de JVM en un directorio `resources` , por lo que se buscan ya internamente. La colocación de componentes en esta carpeta le permite simplemente hacer referencia por nombre en el código Java. Por ejemplo: `super("python", "countbolt.py");`. Otra manera de verlo es que Storm considera el directorio `resources` como la raíz (/) cuando se accede a recursos de varios lenguajes.
> 
> En este proyecto de ejemplo, el `storm.py` módulo se incluye en el directorio `/multilang/resources`.
> 
> 

### <a name="build-and-run-the-project"></a>Compilación y ejecución del proyecto
Para ejecutar este proyecto localmente, use el siguiente comando de Maven para compilar y ejecutar en modo local:

    mvn compile exec:java -Dstorm.topology=com.microsoft.example.WordCount

Use ctrl + c para terminar el proceso.

Para implementar el proyecto en un clúster de HDInsight mediante Apache Storm, siga estos pasos:

1. Cree un archivo uber-jar:
   
        mvn package
   
    Se creará un archivo denominado **WordCount--1.0 SNAPSHOT.jar** en el directorio `/target` para este proyecto.
2. Cargue el archivo jar en el clúster de Hadoop mediante uno de los métodos siguientes:
   
   * En clústeres de HDInsight **basados en Linux**: use `scp WordCount-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:WordCount-1.0-SNAPSHOT.jar` para copiar el archivo jar en el clúster, y sustituya USERNAME por el nombre de usuario de SSH y CLUSTERNAME por el nombre del clúster de HDInsight.
     
       Después de que el archivo ha terminado de cargarse, conéctese al clúster mediante SSH e inicie la topología con `storm jar WordCount-1.0-SNAPSHOT.jar com.microsoft.example.WordCount wordcount`
   * En clústeres de HDInsight **basados en Windows**: conéctese al panel de Storm; para ello, vaya a HTTPS://CLUSTERNAME.azurehdinsight.net/ desde el explorador. Sustituya CLUSTERNAME por el nombre del clúster de HDInsight y proporcione el nombre y la contraseña de administrador cuando se le solicite.
     
       Mediante el formulario, realice las acciones siguientes:
     
     * **Archivo del producto**: seleccione **Examinar** y luego, el archivo **WordCount-1.0-SNAPSHOT.jar**.
     * **Nombre de clase**: escriba `com.microsoft.example.WordCount`.
     * **Parámetros adicionales**: escriba un nombre descriptivo como `wordcount` para identificar la topología.
       
       Por último, seleccione **Submit** (Enviar) para iniciar la topología.

> [!NOTE]
> Cuando se ha iniciado una topología Storm, esta se ejecuta hasta que se detiene (termina). Para detener la topología, use el comando `storm kill TOPOLOGYNAME` desde la línea de comandos (por ejemplo, una sesión de SSH en un clúster de Linux), o bien, desde la interfaz de usuario de Storm, seleccione la topología y después, el botón **Terminar**.
> 
> 

## <a name="python-components-with-a-clojure-topology"></a>Componentes de Python con una topología Clojute
> [!NOTE]
> Este ejemplo está disponible en [https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount) en el directorio de **ClojureTopology** .
> 
> 

Esta topología se creó mediante [Leiningen](http://leiningen.org) para [crear un nuevo proyecto de Clojure](https://github.com/technomancy/leiningen/blob/stable/doc/TUTORIAL.md#creating-a-project). Después de eso, se realizaron las siguientes modificaciones en el proyecto de  scaffolding:

* **project.clj**: se agregaron dependencias para Storm y exclusiones para los elementos que pueden ocasionar problemas cuando se implementan en el servidor de HDInsight.
* **resources/resources**: Leiningen crea un directorio `resources` predeterminado, sin embargo, parece que los archivos que se almacenan aquí se agregan a la raíz del archivo jar creado con este proyecto y Storm espera archivos en un subdirectorio llamado `resources`. Así que se agregó un subdirectorio y los archivos de Python se almacenan en `resources/resources`. En tiempo de ejecución, esto se considerará la raíz (/) para obtener acceso a los componentes de Python.
* **src/wordcount/core.clj**: este archivo contiene la definición de la topología y se hace referencia a él desde el archivo **project.clj**. Para obtener más información sobre el uso de Clojure para definir una topología Storm, consulte [Clojure DSL](https://storm.apache.org/documentation/Clojure-DSL.html).

### <a name="build-and-run-the-project"></a>Compilación y ejecución del proyecto
**Para compilar y ejecutar el proyecto localmente**, use el siguiente comando:

    lein clean, run

Para detener la topología, use **Ctrl + C**.

**Para compilar un archivo uberjar e implementarlo en HDInsight**, siga estos pasos:

1. Cree un archivo uberjar que contenga la topología y las dependencias necesarias:
   
        lein uberjar
   
    Se creará un nuevo archivo llamado `wordcount-1.0-SNAPSHOT.jar` en el directorio de `target\uberjar+uberjar` .
2. Use uno de los siguientes métodos para implementar y ejecutar la topología en un cluster de HDInsight:
   
   * **HDInsight basado en Linux**
     
     1. Copie el archivo en el nodo principal del clúster de HDInsight con `scp`. Por ejemplo:
        
             scp wordcount-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:wordcount-1.0-SNAPSHOT.jar
        
         Reemplace USERNAME por un usuario de SSH de su clúster y CLUSTERNAME por el nombre del clúster de HDInsight.
     2. Después de que se ha copiado el archivo en el clúster, use SSH para conectarse al clúster y enviar el trabajo. Para más información, consulte [Uso SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).
     
     3. Después de que se ha conectado, use lo siguiente para iniciar la topología:
        
             storm jar wordcount-1.0-SNAPSHOT.jar wordcount.core wordcount
   * **HDInsight basado en Windows**
     
     1. Conéctese al panel de Storm; para ello, vaya a HTTPS://CLUSTERNAME.azurehdinsight.net/ desde el explorador. Sustituya CLUSTERNAME por el nombre del clúster de HDInsight y proporcione el nombre y la contraseña de administrador cuando se le solicite.
     2. Mediante el formulario, realice las acciones siguientes:
        
        * **Archivo del producto**: seleccione **Examinar** y luego, el archivo **WordCount-1.0-SNAPSHOT.jar**.
        * **Nombre de clase**: escriba `wordcount.core`.
        * **Parámetros adicionales**: escriba un nombre descriptivo como `wordcount` para identificar la topología.
          
          Por último, seleccione **Submit** (Enviar) para iniciar la topología.

> [!NOTE]
> Cuando se ha iniciado una topología Storm, esta se ejecuta hasta que se detiene (termina). Para detener la topología, use el comando `storm kill TOPOLOGYNAME` desde la línea de comandos (una sesión de SSH en un clúster de Linux), o bien, desde la interfaz de usuario de Storm, seleccione la topología y después, el botón **Terminar**.
> 
> 

## <a name="next-steps"></a>Pasos siguientes
En este documento, aprendió a usar los componentes de Python en una topología Storm. Consulte los siguientes documentos para ver otras maneras de usar Python con HDInsight.

* [Uso de Python para la transmisión de trabajos de MapReduce](hdinsight-hadoop-streaming-python.md)
* [Uso de funciones definidas por el usuario (UDF) de Python en Pig y Hive](hdinsight-python.md)


