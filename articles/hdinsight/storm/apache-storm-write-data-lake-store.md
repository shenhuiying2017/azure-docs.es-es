---
title: Escritura de Apache Storm en Storage o Data Lake Store - Azure HDInsight | Microsoft Docs
description: "Obtenga más información sobre cómo usar Apache Storm para escribir datos en almacenamiento compatible con HDFS para HDInsight. Azure Storage o Azure Data Lake Store proporcionan almacenamiento compatible con HDFS para HDInsight. En este documento y en el ejemplo relacionado se muestra cómo se puede usar el componente HfdfsBolt para escribir datos en el almacenamiento predeterminado de un clúster de HDInsight en Storm."
services: hdinsight
documentationcenter: na
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 1df98653-a6c8-4662-a8c6-5d288fc4f3a6
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/04/2018
ms.author: larryfr
ms.openlocfilehash: 5550dc2ffc53c6ccd30ecb4901ec98c4d38e366b
ms.sourcegitcommit: d6984ef8cc057423ff81efb4645af9d0b902f843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/05/2018
---
# <a name="write-to-hdfs-from-apache-storm-on-hdinsight"></a>Escritura de datos en HDFS desde Apache Storm en HDInsight

Obtenga más información sobre cómo usar Storm para escribir datos en el almacenamiento compatible con HDFS que usa Apache Storm en HDInsight. HDInsight puede usar tanto Azure Storage como Azure Data Lake como almacenamiento compatible con HDFS. Storm proporciona un componente [HdfsBolt](http://storm.apache.org/releases/1.1.0/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) que permite escribir datos en HDFS. En este documento se proporciona información sobre cómo escribir datos en ambos tipos de almacenamiento desde HdfsBolt. 

> [!IMPORTANT]
> La topología de ejemplo que se usa en este documento depende de componentes que se incluyen con Storm en HDInsight. Puede que sea necesario realizar alguna modificación para que funcione con Azure Data Lake Store al usarlo con otros clústeres de Apache Storm.

## <a name="get-the-code"></a>Obtención del código

El proyecto que contiene esta topología está disponible como descarga en [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store).

Para compilar este proyecto, necesitará la siguiente configuración para su entorno de desarrollo:

* [Java JDK 1.8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) o versiones posteriores. HDInsight 3.5 y versiones posteriores requieren Java 8.

* [Maven 3.x](https://maven.apache.org/download.cgi)

Pueden establecer las siguientes variables de entorno al instalar Java y el JDK en la estación de trabajo de desarrollo. Sin embargo, debe comprobar que existen y que contienen los valores correctos para su sistema.

* `JAVA_HOME`: debe apuntar al directorio en el que esté instalado JDK.
* `PATH`: debe contener las rutas de acceso siguientes:
  
    * `JAVA_HOME` (o la ruta de acceso equivalente).
    * `JAVA_HOME\bin` (o la ruta de acceso equivalente).
    * Directorio en el que esté instalado Maven.

## <a name="how-to-use-the-hdfsbolt-with-hdinsight"></a>Cómo usar HdfsBolt con HDInsight

> [!IMPORTANT]
> Antes de usar HdfsBolt con Storm en HDInsight, debe usar una acción de script para copiar los archivos .jar necesarios en `extpath` para Storm. Para obtener más información, consulte la sección [Configuración del clúster](#configure).

HdfsBolt usa el esquema de archivo que usted proporcione para comprender cómo escribir datos en HDFS. Con HDInsight, use uno de los siguientes esquemas:

* `wasb://`: se usa con una cuenta de Azure Storage.
* `adl://`: se usa con Azure Data Lake Store.

En la siguiente tabla se proporcionan ejemplos de cómo usar el esquema de archivos en diferentes casos:

| Esquema | Notas |
| ----- | ----- |
| `wasb:///` | La cuenta de almacenamiento predeterminada es un contenedor de blobs de una cuenta de Azure Storage. |
| `adl:///` | La cuenta de almacenamiento predeterminada es un directorio de Azure Data Lake Store. Durante la creación del clúster, especifique el directorio de Data Lake Store que sea la raíz del HDFS del clúster. Por ejemplo, el directorio `/clusters/myclustername/`. |
| `wasb://CONTAINER@ACCOUNT.blob.core.windows.net/` | Cuenta de Azure Storage no predeterminada (adicional) asociada al clúster. |
| `adl://STORENAME/` | Raíz de Data Lake Store que usa el clúster. El uso de este esquema permite acceder a datos que se encuentren fuera del directorio que contiene el sistema de archivos del clúster. |

Para obtener más información, consulte la referencia de [HdfsBolt](http://storm.apache.org/releases/1.1.0/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) en Apache.org.

### <a name="example-configuration"></a>Configuración de ejemplo

El siguiente YAML es un fragmento del archivo `resources/writetohdfs.yaml` incluido en el ejemplo. Este archivo define la topología de Storm mediante el marco [Flux](https://storm.apache.org/releases/1.1.0/flux.html) para Apache Storm.

```yaml
components:
  - id: "syncPolicy"
    className: "org.apache.storm.hdfs.bolt.sync.CountSyncPolicy"
    constructorArgs:
      - 1000

  # Rotate files when they hit 5 MB
  - id: "rotationPolicy"
    className: "org.apache.storm.hdfs.bolt.rotation.FileSizeRotationPolicy"
    constructorArgs:
      - 5
      - "MB"

  - id: "fileNameFormat"
    className: "org.apache.storm.hdfs.bolt.format.DefaultFileNameFormat"
    configMethods:
      - name: "withPath"
        args: ["${hdfs.write.dir}"]
      - name: "withExtension"
        args: [".txt"]

  - id: "recordFormat"
    className: "org.apache.storm.hdfs.bolt.format.DelimitedRecordFormat"
    configMethods:
      - name: "withFieldDelimiter"
        args: ["|"]

# spout definitions
spouts:
  - id: "tick-spout"
    className: "com.microsoft.example.TickSpout"
    parallelism: 1


# bolt definitions
bolts:
  - id: "hdfs-bolt"
    className: "org.apache.storm.hdfs.bolt.HdfsBolt"
    configMethods:
      - name: "withConfigKey"
        args: ["hdfs.config"]
      - name: "withFsUrl"
        args: ["${hdfs.url}"]
      - name: "withFileNameFormat"
        args: [ref: "fileNameFormat"]
      - name: "withRecordFormat"
        args: [ref: "recordFormat"]
      - name: "withRotationPolicy"
        args: [ref: "rotationPolicy"]
      - name: "withSyncPolicy"
        args: [ref: "syncPolicy"]
```

En este YAML se definen los siguientes elementos:

* `syncPolicy`: define cuándo se sincronizan o vacían los archivos en el sistema de archivos. En este ejemplo, cada 1000 tuplas.
* `fileNameFormat`: define la ruta de acceso y el patrón del nombre de archivo que se usarán al escribir archivos. En este ejemplo, la ruta de acceso se proporciona durante el tiempo de ejecución con un filtro, y la extensión de archivo es `.txt`.
* `recordFormat`: define el formato interno de los archivos escritos. En este ejemplo, los campos están delimitados por el carácter `|`.
* `rotationPolicy`: define cuándo se giran los archivos. En este ejemplo no se realiza ningún giro.
* `hdfs-bolt`: usa los componentes anteriores como parámetros de configuración para la clase `HdfsBolt`.

Para obtener más información sobre el marco Flux, consulte [https://storm.apache.org/releases/1.1.0/flux.html](https://storm.apache.org/releases/1.1.0/flux.html).

## <a name="configure-the-cluster"></a>Configuración del clúster

De manera predeterminada, Storm en HDInsight no incluye los componentes que usa HdfsBolt para la comunicación con Azure Storage o Data Lake Store en el parámetro classpath de Storm. Use la siguiente acción de script para agregar dichos componentes al directorio `extlib` de Storm en su clúster:

* Identificador URI de script: `https://hdiconfigactions.blob.core.windows.net/linuxstormextlibv01/stormextlib.sh`
* Nodos a aplicar: Nimbus, Supervisor
* Parámetros: None

Para obtener más información sobre cómo usar este script con su clúster, consulte el documento [Personalización de clústeres de HDInsight mediante la acción de scripts](./../hdinsight-hadoop-customize-cluster-linux.md).

## <a name="build-and-package-the-topology"></a>Compilación y empaquetado de la topología

1. Descargue el proyecto de ejemplo de [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store ](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store) en su entorno de desarrollo.

2. Desde un símbolo del sistema, un terminal o una sesión de shell, cambie los directorios a la raíz del proyecto descargado. Para compilar y empaquetar la topología, use el siguiente comando:
   
        mvn compile package
   
    Al finalizar la compilación y el empaquetado, habrá un nuevo directorio denominado `target` con el archivo `StormToHdfs-1.0-SNAPSHOT.jar`. Este archivo contiene la topología compilada.

## <a name="deploy-and-run-the-topology"></a>Implementación y ejecución de la topología

1. Use el siguiente comando para copiar la topología en el clúster de HDInsight. Reemplace **USER** por el nombre de usuario SSH que usó al crear el clúster. Reemplace **CLUSTERNAME** por el nombre del clúster.
   
        scp target\StormToHdfs-1.0-SNAPSHOT.jar USER@CLUSTERNAME-ssh.azurehdinsight.net:StormToHdfs1.0-SNAPSHOT.jar
   
    Cuando se le solicite, escriba la contraseña que se usó al crear el usuario SSH del clúster. Si usó una clave pública en lugar de una contraseña, tal vez tenga que usar el parámetro `-i` para especificar la ruta de acceso a la correspondiente clave privada.
   
   > [!NOTE]
   > Para obtener más información sobre cómo usar `scp` con HDInsight, consulte [Conexión a través de SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Una vez finalizada la carga, use lo siguiente para conectarse al clúster de HDInsight con SSH. Reemplace **USER** por el nombre de usuario SSH que usó al crear el clúster. Reemplace **CLUSTERNAME** por el nombre del clúster.
   
        ssh USER@CLUSTERNAME-ssh.azurehdinsight.net
   
    Cuando se le solicite, escriba la contraseña que se usó al crear el usuario SSH del clúster. Si usó una clave pública en lugar de una contraseña, tal vez tenga que usar el parámetro `-i` para especificar la ruta de acceso a la correspondiente clave privada.
   
   Para más información, consulte [Uso SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

3. Una vez conectado, use el comando siguiente para crear un archivo denominado `dev.properties`:

        nano dev.properties

4. Use el texto siguiente como contenido del archivo `dev.properties`:

        hdfs.write.dir: /stormdata/
        hdfs.url: wasb:///

    > [!IMPORTANT]
    > En este ejemplo se da por supuesto que su clúster usa una cuenta de Azure Storage como almacenamiento predeterminado. Si el clúster usa Azure Data Lake Store, use `hdfs.url: adl:///`.
    
    Para guardar el archivo, use __Ctrl+X__, después __Y__ y, finalmente, presione __Intro__. Los valores de este archivo establecen la URL de Data Lake Store y el nombre del directorio en el que se escriben los datos.

3. Use el comando siguiente para iniciar la topología:
   
        storm jar StormToHdfs-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writetohdfs.yaml --filter dev.properties

    Este comando inicia la topología con el marco de Flux enviándolo al nodo Nimbus del clúster. La topología la define el archivo `writetohdfs.yaml` incluido en el jar. El archivo `dev.properties` se pasa como un filtro y la topología lee los valores contenidos en el archivo.

## <a name="view-output-data"></a>Visualización de datos de salida

Para ver los datos, use el siguiente comando:

    hdfs dfs -ls /stormdata/

Se muestra una lista de los archivos que ha creado la topología.

La lista siguiente es un ejemplo de los datos devueltos por los comandos anteriores:

    Found 30 items
    -rw-r-----+  1 sshuser sshuser       488000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-0-1488568403092.txt
    -rw-r-----+  1 sshuser sshuser       444000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-1-1488568404567.txt
    -rw-r-----+  1 sshuser sshuser       502000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-10-1488568408678.txt
    -rw-r-----+  1 sshuser sshuser       582000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-11-1488568411636.txt
    -rw-r-----+  1 sshuser sshuser       464000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-12-1488568411884.txt

## <a name="stop-the-topology"></a>Detención de la topología

Las topologías de Storm se ejecutarán hasta que se detengan o se elimine el clúster. Para detener la topología, use el siguiente comando:

    storm kill hdfswriter

## <a name="delete-your-cluster"></a>Eliminación del clúster

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>pasos siguientes

Ahora que ya sabe usar Storm para escribir datos en Azure Storage y Azure Data Lake Store, descubra otros [ejemplos de Storm para HDInsight](apache-storm-example-topology.md).

