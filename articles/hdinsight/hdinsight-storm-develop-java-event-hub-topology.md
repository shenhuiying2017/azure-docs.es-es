---
title: Procesamiento de eventos desde cEvent Hubs con Storm en HDInsight con Java | Microsoft Docs
description: "Obtenga información sobre cómo procesar los datos de los Centros de eventos con una topología Java de Storm creada con Maven."
services: hdinsight,notification hubs
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 453fa7b0-c8a6-413e-8747-3ac3b71bed86
ms.service: hdinsight
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/17/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 110f3aa9ce4848c9350ea2e560205aa762decf7a
ms.openlocfilehash: 85f56e223210f14615f7e4e1c87e35111b238aac
ms.lasthandoff: 02/21/2017


---
# <a name="process-events-from-azure-event-hubs-with-storm-on-hdinsight-java"></a>Procesamiento de eventos desde Centros de eventos de Azure con Storm en HDInsight (Java)

Obtenga información sobre cómo usar Azure Event Hubs con Storm en HDInsight. Este ejemplo utiliza componentes basados en Java para leer y escribir datos en Azure Event Hubs.

Los Centros de eventos de Azure le permiten procesar grandes volúmenes de datos provenientes de sitios web, aplicaciones y dispositivos. El spout de centro de eventos permite que Apache Storm en HDInsight analice fácilmente estos datos en tiempo real. También es posible escribir datos en Centros de eventos desde Storm usando el bolt de Centros de eventos.

## <a name="prerequisites"></a>Requisitos previos

* Una instancia de Apache Storm en un clúster de HDInsight, versión 3.5. Para más información, vea [Get started with Storm on HDInsight cluster](hdinsight-apache-storm-tutorial-get-started-linux.md) (Introducción a Storm en clúster de HDInsight).
    
    > [!IMPORTANT]
    > Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las superiores. Para más información, consulte [El contrato de nivel de servicio para las versiones de clúster de HDInsight](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

* Un [Centro de eventos de Azure](../event-hubs/event-hubs-csharp-ephcs-getstarted.md).

* [Oracle Java Developer Kit (JDK) versión 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) o equivalente, como [OpenJDK](http://openjdk.java.net/).

* [Maven](https://maven.apache.org/download.cgi): Maven es un sistema de compilación de proyectos para proyectos de Java.

* Un editor de texto o un entorno de desarrollo integrado (IDE).
  
  > [!NOTE]
  > El editor o IDE puede tener una funcionalidad específica para trabajar con Maven que no se trata en este documento. Para obtener información sobre las capacidades de su entorno de edición, consulte la documentación del producto que esté utilizando.
  
  * Un cliente SSH. Para más información, vea uno de los documentos siguientes:
    
    * [Uso de SSH con Hadoop basado en Linux en HDInsight desde Linux, Unix, OS X y Bash en Windows 10](hdinsight-hadoop-linux-use-ssh-unix.md).

    * [Uso de SSH (PuTTY) con Hadoop en HDInsight basado en Linux desde Windows con PuTTY](hdinsight-hadoop-linux-use-ssh-windows.md).

* Un cliente de SCP. El comando `scp` se proporciona con todos los sistemas Linux, Unix y OS X (incluido Bash en Windows 10). Para los sistemas de Windows que no incluyen el comando `scp`, se recomienda PSCP. PSCP está disponible desde la [página de descarga de PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

## <a name="understanding-the-example"></a>Descripción del ejemplo

El ejemplo [hdinsight-java-storm-eventhub](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub) contiene dos topologías:

**com.microsoft.example.EventHubWriter** escribe datos aleatorios en un Centro de eventos de Azure. Los datos los genera un spout y son un identificador de dispositivo y un valor del dispositivo aleatorios. Por lo tanto simula un hardware que emite un identificador de cadena y un valor numérico.

**com.microsoft.example.EventHubReader** lee los datos del centro de eventos y los guarda en el almacenamiento predeterminado de clústeres en el directorio /devicedata.

Con el formato de los datos como un documento JSON antes de escribir en el concentrador de eventos y cuando los lee el lector se analiza de JSON a de tuplas. El formato de la dirección JSON es el siguiente:

    { "deviceId": "unique identifier", "deviceValue": some value }

### <a name="project-configuration"></a>Configuración de proyecto

El archivo **POM.xml** contiene información de configuración para este proyecto Maven. Las partes interesantes son:

#### <a name="the-eventhubs-storm-spout-dependency"></a>La dependencia de Spout de EventHubs Storm

```xml
<dependency>
    <groupId>com.microsoft</groupId>
    <artifactId>eventhubs</artifactId>
    <version>1.0.2</version>
</dependency>
```

Este xml define una dependencia para el paquete eventhubs, que contiene tanto un spout para la lectura de Event Hubs como un bolt para escribir ahí.

> [!NOTE]
> Este paquete se instala más adelante en este documento.

#### <a name="the-hdfsbolt-and-wasb-components"></a>Los componentes HdfsBolt y WASB

El HdfsBolt se usa normalmente para almacenar datos en el sistema de archivos distribuidos Hadoop (HDFS). Sin embargo, los clústeres de HDInsight usan Almacenamiento de Azure (WASB) como almacén de datos predeterminado, por lo que es necesario cargar varios componentes que permiten a HdfsBolt comprender el sistema de archivos WASB.

```xml
<!--HdfsBolt stuff -->
<dependency>
    <groupId>org.apache.storm</groupId>
    <artifactId>storm-hdfs</artifactId>
    <exclusions>
    <exclusion>
        <groupId>org.apache.hadoop</groupId>
        <artifactId>hadoop-client</artifactId>
    </exclusion>
    <exclusion>
        <groupId>org.apache.hadoop</groupId>
        <artifactId>hadoop-hdfs</artifactId>
    </exclusion>
    </exclusions>
    <version>${storm.version}</version>
</dependency>
<!--So HdfsBolt knows how to talk to WASB -->
<dependency>
    <groupId>org.apache.hadoop</groupId>
    <artifactId>hadoop-client</artifactId>
    <version>${hadoop.version}</version>
</dependency>
<dependency>
    <groupId>org.apache.hadoop</groupId>
    <artifactId>hadoop-hdfs</artifactId>
    <version>${hadoop.version}</version>
</dependency>
<dependency>
    <groupId>org.apache.hadoop</groupId>
    <artifactId>hadoop-azure</artifactId>
    <version>${hadoop.version}</version>
</dependency>
<dependency>
    <groupId>org.apache.hadoop</groupId>
    <artifactId>hadoop-common</artifactId>
    <version>${hadoop.version}</version>
    <exclusions>
    <exclusion>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-log4j12</artifactId>
    </exclusion>
    </exclusions>
</dependency>
```

> [!NOTE]
> Al trabajar con una versión anterior de HDInsight, como la 3.2, debe registrar manualmente estos componentes. Para obtener un ejemplo, vea la rama [Storm v0.9.3](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub/tree/Storm_v0.9.3) del repositorio de ejemplos.

#### <a name="the-maven-compiler-plugin"></a>El complemento maven-compiler-plugin

```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
    <version>2.3.2</version>
    <configuration>
    <source>1.8</source>
    <target>1.8</target>
    </configuration>
</plugin>
```

Configura el proyecto para generar la salida para Java 8, que HDInsight 3.5 utiliza.

#### <a name="the-maven-shade-plugin"></a>El complemento maven-shade-plugin

```xml
<!-- build an uber jar -->
<plugin>
<groupId>org.apache.maven.plugins</groupId>
<artifactId>maven-shade-plugin</artifactId>
<version>2.3</version>
<configuration>
    <transformers>
    <!-- Keep us from getting a can't overwrite file error -->
    <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer"/>
    <!-- Keep us from getting errors when trying to use WASB from the storm-hdfs bolt -->
    <transformer implementation="org.apache.maven.plugins.shade.resource.ServicesResourceTransformer"/>
    </transformers>
    <!-- Keep us from getting a bad signature error -->
    <filters>
    <filter>
        <artifact>*:*</artifact>
        <excludes>
            <exclude>META-INF/*.SF</exclude>
            <exclude>META-INF/*.DSA</exclude>
            <exclude>META-INF/*.RSA</exclude>
        </excludes>
    </filter>
    </filters>
</configuration>
<executions>
    <execution>
    <phase>package</phase>
    <goals>
        <goal>shade</goal>
    </goals>
    </execution>
</executions>
</plugin>
```

Configura la solución para empaquetar la salida en un archivo uber jar. El archivo jar contiene el código del proyecto y necesita dependencias. También se usa para:

* Cambiar el nombre de archivos de licencia para las dependencias.
* Excluir la seguridad y las firmas.
* Asegúrese de combinar varias implementaciones de la misma interfaz en una sola entrada.

Estos valores de configuración evitan errores en tiempo de ejecución.

#### <a name="the-exec-maven-plugin"></a>El complemento exec-maven-plugin

```xml
<plugin>
    <groupId>org.codehaus.mojo</groupId>
    <artifactId>exec-maven-plugin</artifactId>
    <version>1.2.1</version>
    <executions>
    <execution>
    <goals>
        <goal>exec</goal>
    </goals>
    </execution>
    </executions>
    <configuration>
    <executable>java</executable>
    <includeProjectDependencies>true</includeProjectDependencies>
    <includePluginDependencies>false</includePluginDependencies>
    <classpathScope>compile</classpathScope>
    <mainClass>${storm.topology}</mainClass>
    </configuration>
</plugin>
```

Esta configuración facilita la ejecución de la topología de forma local en el entorno de implementación. Puede ejecutar la topología de forma local con la sintaxis siguiente:

    mvn compile exec:java -Dstorm.topology=<CLASSNAME>

Por ejemplo: `mvn compile exec:java -Dstorm.topology=com.microsoft.example.EventHubWriter`.

#### <a name="the-resources-section"></a>La sección de recursos

```xml
<resources>
    <resource>
    <directory>${basedir}/conf</directory>
    <filtering>false</filtering>
    <includes>
        <include>EventHubs.properties</include>
    </includes>
    </resource>
</resources>
```

Esta configuración define recursos que no son de Java que están incluidos en el proyecto. Por ejemplo, el archivo **EventHubs.properties** contiene información que se usa para conectarse a un Centro de eventos de Azure.

## <a name="configure-environment-variables"></a>Configuración de las variables de entorno

Pueden establecer las siguientes variables de entorno al instalar Java y el JDK en la estación de trabajo de desarrollo. Sin embargo, debe comprobar que existen y que contienen los valores correctos para su sistema.

* **JAVA_HOME**: debe apuntar al directorio donde está instalado Java Runtime Environment (JRE). Por ejemplo, en una distribución de Unix o Linux, debe tener un valor similar a `/usr/lib/jvm/java-7-oracle`. En Windows, tendría un valor similar a `c:\Program Files (x86)\Java\jre1.7`
* **PATH** : debe contener las rutas de acceso siguientes:
  
  * **JAVA_HOME** (o la ruta de acceso equivalente).
  * **JAVA_HOME\bin** (o la ruta de acceso equivalente).
  * El directorio donde está instalado Maven

## <a name="download-and-register-the-eventhub-components"></a>Descarga y registro de los componentes de EventHub

1. Descargue `storm-eventhubs-1.0.2-jar-with-dependencies.jar` de [https://000aarperiscus.blob.core.windows.net/certs/storm-eventhubs-1.0.2-jar-with-dependencies.jar](https://000aarperiscus.blob.core.windows.net/certs/storm-eventhubs-1.0.2-jar-with-dependencies.jar). Este archivo contiene un componente spout y bolt para leer y escribir desde EventHubs.

2. Use el siguiente comando para registrar los componentes en el repositorio de Maven local:
    
        mvn install:install-file -Dfile=storm-eventhubs-1.0.2-jar-with-dependencies.jar -DgroupId=com.microsoft -DartifactId=eventhubs -Dversion=1.0.2 -Dpackaging=jar
    
    Modifique el parámetro `-Dfile=` para que apunte a la ubicación del archivo descargado.

    Este comando instala el archivo en el repositorio local de Maven, donde Maven puede encontrarlo en tiempo de compilación.

## <a name="configure-event-hub"></a>Configuración del centro de eventos

Centros de eventos es el origen de datos para este ejemplo. Use los pasos siguientes para crear un centro de eventos.

1. En el [Portal de Azure clásico](https://manage.windowsazure.com), seleccione **NUEVO** > **Service Bus** > **Centro de eventos** > **Creación personalizada**.

2. En la pantalla **Agregar un concentrador de eventos nuevo**, escriba un **Nombre del Centro de eventos**. Seleccione la **Región** en la que crear el centro y, después, cree un espacio de nombres o seleccione uno existente. Por último, haga clic en la **flecha** para continuar.
   
    ![página 1 del asistente](./media/hdinsight-storm-develop-csharp-event-hub-topology/wiz1.png)
   
   > [!NOTE]
   > Seleccione la misma **Ubicación** de su servidor Storm en HDInsight para disminuir la latencia y los costos.

3. En la pantalla **Configurar el concentrador de eventos**, escriba los valores **Recuento de particiones** y **Retención de mensajes**. En este ejemplo, utilice un recuento de particiones de 10 y una retención de mensajes de 1. Anote el recuento de particiones, porque necesita este valor más adelante.
   
    ![página 2 del asistente](./media/hdinsight-storm-develop-csharp-event-hub-topology/wiz2.png)

4. Una vez se ha creado el concentrador de eventos, seleccione el espacio de nombres, seleccione **Concentradores de eventos**y, después, seleccione el concentrador de eventos que creó anteriormente.
5. Seleccione **Configurar** y cree dos directivas de acceso con la información siguiente:
   
    <table>
    <tr><th>Nombre</th><th>Permisos</th></tr>
    <tr><td>Escritor</td><td>Los métodos Send</td></tr>
    <tr><td>Lector</td><td>Escuchar</td></tr>
    </table>
   
    Después de crear los permisos, seleccione el icono **Guardar** en la parte inferior de la página. Estas directivas de acceso compartido se usan para leer y escribir en el Centro de eventos.
   
    ![directivas](./media/hdinsight-storm-develop-csharp-event-hub-topology/policy.png)

6. Después de guardar las directivas, use el **generador de claves de acceso compartido** que se encuentra en la parte inferior de la página para recuperar la clave para las directivas de **escritor** y **lector**. Guarde estas claves.

## <a name="download-and-build-the-project"></a>Descarga y compilación del proyecto.

1. Descargue el proyecto de GitHub: [hdinsight-java-storm-eventhub](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub). Puede descargar el paquete como un archivo zip o usar [git](https://git-scm.com/) para clonar el proyecto de forma local.

2. Para compilar y empaquetar el proyecto, use lo siguiente:
   
        mvn package
   
    Este comando descarga las dependencias requeridas, las compilaciones y, luego, empaqueta el proyecto. La salida se almacena en el directorio **/target** como **EventHubExample-1.0-SNAPSHOT.jar**.

## <a name="deploy-the-topologies"></a>Implementación de las topologías

El jar creado por este proyecto contiene dos topologías: **com.microsoft.example.EventHubWriter** y **com.microsoft.example.EventHubReader**. La topología de EventHubWriter debe iniciarse en primer lugar, ya que escribe eventos en Centro de eventos que luego se leen en EventHubReader.

1. Use SCP para copiar el paquete jar en su clúster de HDInsight. Reemplace USERNAME con el usuario SSH para el clúster. Reemplace CLUSTERNAME por el nombre del clúster de HDInsight:
   
        scp ./target/EventHubExample-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.
   
    Si usó una contraseña para la cuenta SSH, se le pedirá que escriba la contraseña. Si usó una clave SSH con la cuenta, puede que necesite usar el parámetro `-i` para especificar la ruta de acceso al archivo de clave. Por ejemplo: `scp -i ~/.ssh/id_rsa ./target/EventHubExample-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.`.
   
   > [!NOTE]
   > Si el cliente es una estación de trabajo de Windows, puede que no tenga instalado un comando de SCP. Se recomienda PSCP que puede descargarse desde la [página de descarga de PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).
   
    Este comando copia el archivo en el directorio principal del usuario SSH en el clúster.

2. Cuando termine de cargar el archivo, use SSH para conectarse con el clúster de HDInsight. Reemplace **USERNAME** por el nombre de su inicio de sesión de SSH. Reemplace **CLUSTERNAME** por el nombre del clúster de HDInsight.
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
   > [!NOTE]
   > Si usó una contraseña para la cuenta SSH, se le pedirá que escriba la contraseña. Si usó una clave SSH con la cuenta, puede que necesite usar el parámetro `-i` para especificar la ruta de acceso al archivo de clave. En el ejemplo siguiente se cargará la clave privada desde `~/.ssh/id_rsa`:
   > 
   > `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`
   
    Si usa PuTTY, escriba `CLUSTERNAME-ssh.azurehdinsight.net` en el campo **Host name (or IP address)** (Nombre de host [o dirección IP]) y haga clic en **Open** (Abrir) para conectarse. Se le pide que escriba el nombre de cuenta de SSH.
   
   > [!NOTE]
   > Si usó una contraseña para la cuenta SSH, se le pedirá que escriba la contraseña. Si usó una clave SSH con la cuenta, puede que necesite seguir los pasos a continuación para seleccionar la clave:
   > 
   > 1. En **Category** (Categoría), expanda **Connection** (Conexión), **SSH** y seleccione **Auth** (Autenticar).
   > 2. Haga clic en **Browse** (Examinar) y seleccione el archivo .ppk que contiene su clave privada.
   > 3. Haga clic en **Open** (Abrir) para conectarse.

3. Use el comando siguiente para iniciar las topologías.
   
        storm jar EventHubExample-1.0-SNAPSHOT.jar com.microsoft.example.EventHubWriter writer
        storm jar EventHubExample-1.0-SNAPSHOT.jar com.microsoft.example.EventHubReader reader
   
    Estos comandos inician las topologías con los nombres descriptivos "reader" y "writer".

4. Espere un minuto para que las topologías generen datos. Use el siguiente comando para verificar que los datos se escriben en el Almacenamiento para HDInsight:
   
        hadoop fs -ls /devicedata
   
    Este comando devuelve una lista de archivos similar al texto siguiente:
   
        -rw-r--r--   1 storm supergroup      10283 2015-08-11 19:35 /devicedata/wasbbolt-14-0-1439321744110.txt
        -rw-r--r--   1 storm supergroup      10277 2015-08-11 19:35 /devicedata/wasbbolt-14-1-1439321748237.txt
        -rw-r--r--   1 storm supergroup      10280 2015-08-11 19:36 /devicedata/wasbbolt-14-10-1439321760398.txt
        -rw-r--r--   1 storm supergroup      10267 2015-08-11 19:36 /devicedata/wasbbolt-14-11-1439321761090.txt
        -rw-r--r--   1 storm supergroup      10259 2015-08-11 19:36 /devicedata/wasbbolt-14-12-1439321762679.txt
   
   > [!NOTE]
   > Algunos archivos pueden mostrar un tamaño de 0, ya que EventHubReader los creó pero aún no tienen datos almacenados.
   
    Puede ver el contenido de estos archivo mediante el siguiente comando:
   
        hadoop fs -text /devicedata/*.txt
   
    Devuelve datos similares al siguiente texto:
   
        3409e622-c85d-4d64-8622-af45e30bf774,848981614
        c3305f7e-6948-4cce-89b0-d9fbc2330c36,-1638780537
        788b9796-e2ab-49c4-91e3-bc5b6af1f07e,-1662107246
        6403df8a-6495-402f-bca0-3244be67f225,275738503
        d7c7f96c-581a-45b1-b66c-e32de6d47fce,543829859
        9a692795-e6aa-4946-98c1-2de381b37593,1857409996
        3c8d199b-0003-4a79-8d03-24e13bde7086,-1271260574
   
    La primera columna contiene el valor de identificador de dispositivo y la segunda el valor del dispositivo.

5. Use el comando siguiente para detener las topologías.
   
        storm kill reader
        storm kill writer

## <a name="delete-your-cluster"></a>Eliminación del clúster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshooting"></a>Solución de problemas

Si no ve los archivos en el directorio /devicedata, utilice la interfaz de usuario de Storm para buscar los errores devueltos por las topologías.

Para obtener más información sobre el uso de IU de Storm, vea los siguientes temas:

* Si está usando Storm **basado en Linux** en un clúster de HDInsight, consulte [Implementación y administración de topologías de Apache Storm en HDInsight basado en Linux](hdinsight-storm-deploy-monitor-topology-linux.md)

* Si está usando Storm **basado en Windows** en un clúster de HDInsight, consulte [Implementación y administración de topologías de Apache Storm en HDInsight basado en Windows](hdinsight-storm-deploy-monitor-topology-linux.md)

## <a name="next-steps"></a>Pasos siguientes

* [Topologías de ejemplo para Storm en HDInsight](hdinsight-storm-example-topology.md)


