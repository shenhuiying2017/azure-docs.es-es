---
title: Apache Storm con componentes de Python - Azure HDInsight | Microsoft Docs
description: "Aprenda a crear una topología de Apache Storm que use componentes de Python."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
keywords: apache storm python
ms.assetid: edd0ec4f-664d-4266-910c-6ecc94172ad8
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/04/2017
ms.author: larryfr
ms.openlocfilehash: 305c4060ad81458b254e66a4bad6dfd7bf69b28d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="develop-apache-storm-topologies-using-python-on-hdinsight"></a>Desarrollo de topologías Apache Storm con Python en HDInsight

Aprenda a crear una topología de Apache Storm que use componentes de Python. Apache Storm admite varios lenguajes, e incluso le permite combinar componentes de varios lenguajes en una topología. El marco de trabajo de Flux (introducido con Storm 0.10.0) permite crear fácilmente soluciones que usan componentes de Python.

> [!IMPORTANT]
> La información de este documento se probó con Storm en HDInsight 3.6. Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las superiores. Consulte la información sobre la [retirada de HDInsight en Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

El código de este proyecto está disponible en [https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount).

## <a name="prerequisites"></a>Requisitos previos

* Python 2.7 o versiones posteriores

* Java JDK 1.8 o versiones posteriores

* Maven 3

* (Opcional) Un entorno de desarrollo de Storm local. Un entorno de Storm local solo es necesario si desea ejecutar localmente la topología. Para más información, consulte [Setting up a development environment](http://storm.apache.org/releases/1.0.1/Setting-up-development-environment.html) (Configuración de un entorno de desarrollo).

## <a name="storm-multi-language-support"></a>Compatibilidad con varios lenguajes de Storm

Apache Storm se ha diseñado para funcionar con componentes escritos con cualquier lenguaje de programación. Los componentes deben aprender a trabajar con la [definición Thrift para Storm](https://github.com/apache/storm/blob/master/storm-core/src/storm.thrift). Para Python, se proporciona un módulo como parte del proyecto de Apache Storm que le permite interactuar fácilmente con Storm. Puede encontrar este módulo en [https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py](https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py).

Storm es un proceso de Java que se ejecuta en Máquina virtual Java (JVM). Los componentes escritos en otros lenguajes se ejecutan como subprocesos. Storm se comunica con estos subprocesos mediante mensajes JSON enviados a través de stdin y stdout. Se puede encontrar más detalles sobre la comunicación entre los componentes en la documentación de [Multi-lang protocolo](https://storm.apache.org/documentation/Multilang-protocol.html) (Protocolo de varios lenguajes).

## <a name="python-with-the-flux-framework"></a>Python con el marco de trabajo de Flux

El marco de trabajo de Flux le permite definir las topologías de Storm de forma independiente con respecto a los componentes. El marco de trabajo de Flux usa YAML para definir la topología de Storm. El siguiente texto es un ejemplo de cómo se hace referencia a un componente de Python en el documento de YAML:

```yaml
# Spout definitions
spouts:
  - id: "sentence-spout"
    className: "org.apache.storm.flux.wrappers.spouts.FluxShellSpout"
    constructorArgs:
      # Command line
      - ["python", "sentencespout.py"]
      # Output field(s)
      - ["sentence"]
    # parallelism hint
    parallelism: 1
```

La clase `FluxShellSpout` se usa para iniciar el script `sentencespout.py` que implementa el spout.

Flux espera que los scripts de Python estén en el directorio `/resources` dentro del archivo jar que contiene la topología. Por ello, este ejemplo almacena los scripts de Python en el directorio `/multilang/resources`. El archivo `pom.xml` incluye este archivo mediante el siguiente código XML:

```xml
<!-- include the Python components -->
<resource>
    <directory>${basedir}/multilang</directory>
    <filtering>false</filtering>
</resource>
```

Como se mencionó anteriormente, hay un archivo `storm.py` que implementa la definición de Thrift para Storm. El marco de trabajo de Flux incluye `storm.py` automáticamente cuando se compila el proyecto, por lo que no tiene que preocuparse de incluirlo.

## <a name="build-the-project"></a>Compilación del proyecto

Desde la raíz del proyecto, use el comando siguiente:

```bash
mvn clean compile package
```

Este comando crea un archivo `target/WordCount-1.0-SNAPSHOT.jar` que contiene la topología compilada.

## <a name="run-the-topology-locally"></a>Ejecución de la topología de manera local

Para ejecutar la topología de manera local, use el siguiente comando:

```bash
storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -l -R /topology.yaml
```

> [!NOTE]
> Este comando requiere un entorno de desarrollo de Storm local. Para más información, consulte [Setting up a development environment](http://storm.apache.org/releases/1.0.1/Setting-up-development-environment.html) (Configuración de un entorno de desarrollo).

Una vez que se inicia la topología, esta emite información en la consola local que se parece al siguiente texto:


    24302 [Thread-25-sentence-spout-executor[4 4]] INFO  o.a.s.s.ShellSpout - ShellLog pid:2436, name:sentence-spout Emiting the cow jumped over the moon
    24302 [Thread-30] INFO  o.a.s.t.ShellBolt - ShellLog pid:2438, name:splitter-bolt Emitting the
    24302 [Thread-28] INFO  o.a.s.t.ShellBolt - ShellLog pid:2437, name:counter-bolt Emitting years:160
    24302 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=the, count=599}
    24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=seven, count=302}
    24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=dwarfs, count=143}
    24303 [Thread-25-sentence-spout-executor[4 4]] INFO  o.a.s.s.ShellSpout - ShellLog pid:2436, name:sentence-spout Emiting the cow jumped over the moon
    24303 [Thread-30] INFO  o.a.s.t.ShellBolt - ShellLog pid:2438, name:splitter-bolt Emitting cow
    24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=four, count=160}


Para detener la topología, use __Ctrl+C__.

## <a name="run-the-storm-topology-on-hdinsight"></a>Ejecutar la topología de Storm en HDInsight

1. Use el siguiente comando para copiar el archivo `WordCount-1.0-SNAPSHOT.jar` en el clúster de Storm en HDInsight:

    ```bash
    scp target\WordCount-1.0-SNAPSHOT.jar sshuser@mycluster-ssh.azurehdinsight.net
    ```

    Reemplace `sshuser` con el usuario SSH para el clúster. Reemplace `mycluster` por el nombre del clúster. Puede que se le solicite que escriba la contraseña del usuario de SSH.

    Para más información sobre cómo usar SSH y SCP, consulte [Uso de SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Una vez cargado el archivo, conéctese al clúster mediante SSH:

    ```bash
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

3. En la sesión SSH, use el siguiente comando para iniciar la topología en el clúster:

    ```bash
    storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -r -R /topology.yaml
    ```

3. Puede usar la interfaz de usuario de Storm para ver la topología en el clúster. Esta interfaz de usuario web se encuentra en https://mycluster.azurehdinsight.net/stormui. Reemplace `mycluster` por el nombre del clúster.

> [!NOTE]
> Cuando se ha iniciado una topología Storm, esta se ejecuta hasta que se detiene. Para detener la topología, use uno de los siguientes métodos:
>
> * El comando `storm kill TOPOLOGYNAME` desde la línea de comandos.
> * El botón **Terminar** en la interfaz de usuario de Storm.


## <a name="next-steps"></a>Pasos siguientes

Consulte los siguientes documentos para ver otras maneras de usar Python con HDInsight.

* [Uso de Python para la transmisión de trabajos de MapReduce](hdinsight-hadoop-streaming-python.md)
* [Uso de funciones definidas por el usuario (UDF) de Python en Pig y Hive](hdinsight-python.md)
