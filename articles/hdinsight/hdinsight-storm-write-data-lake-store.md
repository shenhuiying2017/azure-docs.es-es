---
title: "Uso del Almacén de Azure Data Lake con Apache Storm en HDInsight de Azure"
description: "Aprenda a escribir datos en Almacén de Azure Data Lake desde una topología de Apache Storm en HDInsight. Este documento y el ejemplo asociado muestran cómo se puede usar el componente HdfsBolt para escribir en Almacén de Data Lake."
services: hdinsight
documentationcenter: na
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 1df98653-a6c8-4662-a8c6-5d288fc4f3a6
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/03/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: 376415d34592d18de00513ee9142512eb716e426
ms.lasthandoff: 03/04/2017


---
# <a name="use-azure-data-lake-store-with-apache-storm-with-hdinsight-java"></a>Uso de Azure Data Lake Store con Apache Storm con HDInsight (Java)

Almacén de Azure Data Lake es un servicio de almacenamiento en la nube compatible con HDFS que ofrece alto rendimiento, disponibilidad, durabilidad y confiabilidad de los datos. En este documento, aprenderá a utilizar una topología de Storm basada en Java para escribir datos en Azure Data Lake Store. Los pasos descritos en este documento usan el componente [HdfsBolt](http://storm.apache.org/releases/1.0.2/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html), que se proporciona como parte de Apache Storm.

> [!IMPORTANT]
> La topología de ejemplo usada en este documento se basa en componentes que se incluyen con clústeres de Storm en HDInsight y pueden que requiera modificaciones para que funcione con Azure Data Lake Store cuando se usa con otros clústeres de Apache Storm.

## <a name="how-to-work-with-azure-data-lake-store"></a>Trabajo con Azure Data Lake Store

Data Lake Store aparece en HDInsight como un sistema de archivos compatible con HDFS, por lo que puede usar el bolt Storm-HDFS para escribir en él. Cuando trabaja con Azure Data Lake en HDInsight, puede utilizar un esquema de archivos de `adl://`.

* Si Data Lake Storage es el almacenamiento principal para el clúster, use `adl:///`. Esta es la raíz del almacenamiento de clúster en Azure Data Lake. Esto puede traducirse en una ruta de acceso de /clústeres/NOMBREDECLÚSTER en su cuenta de Data Lake Storage.
* Si Data Lake Storage es el almacenamiento secundario para el clúster, use `adl://DATALAKEACCOUNT.azuredatalakestore.net/`. Este URI especifica la cuenta de Data Lake Storage en la que se escriben los datos. Los datos se escriben a partir de la raíz de Data Lake Store.

    > [!NOTE]
    > También puede usar este formato URI para guardar los datos en la cuenta de Data Lake Store que contiene el almacenamiento principal para su clúster. Esto le permite guardar los datos fuera de la ruta del directorio que contiene HDInsight.

El código Java siguiente muestra cómo puede utilizar el bolt Storm-HDFS para escribir datos en un directorio denominado `/stormdata` en una cuenta de Data Lake Store denominada `MYDATALAKE`.

```java
// 1. Create sync and rotation policies to control when data is synched
//    (written) to the file system and when to roll over into a new file.
SyncPolicy syncPolicy = new CountSyncPolicy(1000);
FileRotationPolicy rotationPolicy = new FileSizeRotationPolicy(0.5f, Units.KB);
// 2. Set the format. In this case, comma delimited
RecordFormat recordFormat = new DelimitedRecordFormat().withFieldDelimiter(",");
// 3. Set the directory name. In this case, '/stormdata/'
FileNameFormat fileNameFormat = new DefaultFileNameFormat().withPath("/stormdata/");
// 4. Create the bolt using the previously created settings,
//    and also tell it the base URL to your Data Lake Store.
// NOTE! Replace 'MYDATALAKE' below with the name of your data lake store.
HdfsBolt adlsBolt = new HdfsBolt()
    .withFsUrl("adl://MYDATALAKE.azuredatalakestore.net/")
        .withRecordFormat(recordFormat)
        .withFileNameFormat(fileNameFormat)
        .withRotationPolicy(rotationPolicy)
        .withSyncPolicy(syncPolicy);
// 4. Give it a name and wire it up to the bolt it accepts data
//    from. NOTE: The name used here is also used as part of the
//    file name for the files written to Data Lake Store.
builder.setBolt("ADLStoreBolt", adlsBolt, 1)
    .globalGrouping("finalcount");
```

El siguiente YAML muestra cómo utilizar el bolt Storm-HDFS desde el marco de trabajo de Flux:

```yaml
components:
  - id: "syncPolicy"
    className: "org.apache.storm.hdfs.bolt.sync.CountSyncPolicy"
    constructorArgs:
      - 1000
  - id: "rotationPolicy"
    className: "org.apache.storm.hdfs.bolt.rotation.FileSizeRotationPolicy"
    constructorArgs:
      - 5
      - KB

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

  - id: "rotationAction"
    className: "org.apache.storm.hdfs.common.rotation.MoveFileAction"
    configMethods:
      - name: "toDestination"
        args: ["${hdfs.dest.dir}"]

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
    parallelism: 1
```

> [!NOTE]
> Los ejemplos de este documento usan el marco de trabajo de Flux.

## <a name="prerequisites"></a>Requisitos previos

* [Java JDK 1.8](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) o versiones posteriores. HDInsight 3.5 requiere Java 8.

* [Maven 3.x](https://maven.apache.org/download.cgi)

* La versión 3.5 de un clúster de Storm en HDInsight. Para crear un nuevo clúster de Storm en HDInsight, siga los pasos descritos en el documento [Uso de HDInsight con Data Lake Store mediante Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

### <a name="configure-environment-variables"></a>Configuración de las variables de entorno

Pueden establecer las siguientes variables de entorno al instalar Java y el JDK en la estación de trabajo de desarrollo. Sin embargo, debe comprobar que existen y que contienen los valores correctos para su sistema.

* **JAVA_HOME**: debe apuntar al directorio donde está instalado Java Runtime Environment (JRE). Por ejemplo, en una distribución de Unix o Linux, debe tener un valor similar a `/usr/lib/jvm/java-8-oracle`. En Windows, tendría un valor similar a `c:\Program Files (x86)\Java\jre1.8`.
* **PATH** : debe contener las rutas de acceso siguientes:
  
  * **JAVA\_HOME** (o la ruta de acceso equivalente)
  * **JAVA\_HOME\bin** (o la ruta de acceso equivalente)
  * El directorio donde está instalado Maven

## <a name="topology-implementation"></a>Implementación de la topología

El ejemplo usado en este documento está escrito en Java y utiliza los siguientes componentes:

* **TickSpout**: genera los datos que usan otros componentes de la topología.
* **PartialCount**: cuenta los eventos generados mediante TickSpout.
* **FinalCount**: agrega datos de recuento de PartialCount.
* **ADLStoreBolt**: escribe datos en Azure Data Lake Store mediante el componente [HdfsBolt](http://storm.apache.org/releases/1.0.2/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html).

El proyecto que contiene esta topología está disponible como descarga en [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store).

## <a name="build-and-package-the-topology"></a>Compilación y empaquetado de la topología

1. Descargue el proyecto de ejemplo de [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store ](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store) en su entorno de desarrollo.

2. Abra el archivo `StormToDataLake\src\main\java\com\microsoft\example\StormToDataLakeStore.java` en un editor y busque la línea que contiene `.withFsUrl("adl://MYDATALAKE.azuredatalakestore.net/")`. Cambie **MYDATALAKE** por el nombre de Azure Data Lake Store que usó al crear el servidor de HDInsight.

3. Desde un símbolo del sistema, un terminal o la sesión del shell, cambie los directorios a la raíz del proyecto descargado y ejecute los siguientes comandos para compilar y empaquetar la topología.
   
        mvn compile
        mvn package
   
    Al finalizar la compilación y el empaquetado, habrá un nuevo directorio denominado "`target`", que contiene un archivo denominado "`StormToDataLakeStore-1.0-SNAPSHOT.jar`". Contiene la topología compilada.

## <a name="deploy-and-run-the-topology"></a>Implementación y ejecución de la topología

1. Use el siguiente comando para copiar la topología en el clúster de HDInsight. Reemplace **USER** por el nombre de usuario SSH que usó al crear el clúster. Reemplace **CLUSTERNAME** por el nombre del clúster.
   
        scp target\StormToDataLakeStore-1.0-SNAPSHOT.jar USER@CLUSTERNAME-ssh.azurehdinsight.net:StormToDataLakeStore-1.0-SNAPSHOT.jar
   
    Cuando se le solicite, escriba la contraseña que se usó al crear el usuario SSH del clúster. Si usó una clave pública en lugar de una contraseña, tal vez tenga que usar el parámetro `-i` para especificar la ruta de acceso a la correspondiente clave privada.
   
   > [!NOTE]
   > Si usa un cliente Windows para el desarrollo, puede que no tenga un comando `scp`. En ese caso, puede usar `pscp`, que está disponible en [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

2. Una vez finalizada la carga, use lo siguiente para conectarse al clúster de HDInsight con SSH. Reemplace **USER** por el nombre de usuario SSH que usó al crear el clúster. Reemplace **CLUSTERNAME** por el nombre del clúster.
   
        ssh USER@CLUSTERNAME-ssh.azurehdinsight.net
   
    Cuando se le solicite, escriba la contraseña que se usó al crear el usuario SSH del clúster. Si usó una clave pública en lugar de una contraseña, tal vez tenga que usar el parámetro `-i` para especificar la ruta de acceso a la correspondiente clave privada.
   
   > [!NOTE]
   > Si usa un cliente Windows para el desarrollo, consulte [Conexión con HDInsight basado en Linux con SSH desde Windows](hdinsight-hadoop-linux-use-ssh-windows.md) para obtener información sobre el uso de un cliente PuTTY para conectarse al clúster.

3. Una vez conectado, use el comando siguiente para crear un archivo denominado `dev.properties`:

        nano dev.properties

4. Use el texto siguiente como contenido del archivo `dev.properties`:

        hdfs.write.dir: /stormdata
        hdfs.url: adl:///
    
    Para guardar el archivo, use __Ctrl+X__, después __Y__ y, finalmente, presione __Intro__. Los valores de este archivo establecen la URL de Data Lake Store y el nombre del directorio en el que se escriben los datos.

3. Use el comando siguiente para iniciar la topología:
   
        storm jar StormToDataLakeStore-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /datalakewriter.yaml --filter dev.properties

    Este comando inicia la topología mediante el marco de trabajo de Flux. La topología la define el archivo `datalakewriter.yaml` incluido en el jar. El archivo `dev.properties` se pasa como un filtro y la topología lee los valores contenidos en el archivo.

## <a name="view-output-data"></a>Visualización de datos de salida

Para ver los datos, use el siguiente comando:

    hdfs dfs -ls /stormdata/

Esto muestra una lista de archivos creados por la topología.

Si Data Lake Store no es el almacenamiento predeterminado para el clúster, use el comando siguiente para ver los datos:

    hdfs dfs -ls adl://MYDATALAKE.azuredatalakestore.net/stormdata/

En el comando anterior, reemplace __MYDATALAKE__ con la cuenta de Data Lake Store.

La lista siguiente es un ejemplo de los datos devueltos por los comandos anteriores:

    Found 30 items
    -rw-r-----+  1 larryfr larryfr       5120 2017-03-03 19:13 /stormdata/hdfs-bolt-3-0-1488568403092.txt
    -rw-r-----+  1 larryfr larryfr       5120 2017-03-03 19:13 /stormdata/hdfs-bolt-3-1-1488568404567.txt
    -rw-r-----+  1 larryfr larryfr       5120 2017-03-03 19:13 /stormdata/hdfs-bolt-3-10-1488568408678.txt
    -rw-r-----+  1 larryfr larryfr       5120 2017-03-03 19:13 /stormdata/hdfs-bolt-3-11-1488568411636.txt
    -rw-r-----+  1 larryfr larryfr       5120 2017-03-03 19:13 /stormdata/hdfs-bolt-3-12-1488568411884.txt
    -rw-r-----+  1 larryfr larryfr       5120 2017-03-03 19:13 /stormdata/hdfs-bolt-3-13-1488568412603.txt
    -rw-r-----+  1 larryfr larryfr       5120 2017-03-03 19:13 /stormdata/hdfs-bolt-3-14-1488568415055.txt

## <a name="stop-the-topology"></a>Detención de la topología

Las topologías de Storm se ejecutarán hasta que se detengan o se elimine el clúster. Use la información siguiente para detener las topologías.

**Para HDInsight basado en Linux**:

Desde una sesión de SSH en el clúster, use el siguiente comando:

    storm kill datalakewriter

## <a name="delete-your-cluster"></a>Eliminación del clúster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido a usar Storm para escribir en el Almacén de Azure Data Lake, descubra otros [ejemplos de Storm para HDInsight](hdinsight-storm-example-topology.md).


