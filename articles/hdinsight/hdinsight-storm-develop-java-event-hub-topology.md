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
ms.custom: hdinsightactive
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/13/2017
ms.author: larryfr
ms.translationtype: HT
ms.sourcegitcommit: 2c6cf0eff812b12ad852e1434e7adf42c5eb7422
ms.openlocfilehash: db278f2ecd025257a969e3a9f05f5269a659999d
ms.contentlocale: es-es
ms.lasthandoff: 09/13/2017

---
# <a name="process-events-from-azure-event-hubs-with-storm-on-hdinsight-java"></a>Procesamiento de eventos desde Centros de eventos de Azure con Storm en HDInsight (Java)

Obtenga información sobre cómo usar Azure Event Hubs con Storm en HDInsight. Este ejemplo utiliza componentes basados en Java para leer y escribir datos en Azure Event Hubs.

Los Centros de eventos de Azure le permiten procesar grandes volúmenes de datos provenientes de sitios web, aplicaciones y dispositivos. El spout de centro de eventos permite que Apache Storm en HDInsight analice fácilmente estos datos en tiempo real. También es posible escribir datos en Centros de eventos desde Storm usando el bolt de Centros de eventos.

## <a name="prerequisites"></a>Requisitos previos

* Una instancia de Apache Storm en un clúster de HDInsight, versión 3.6. Para más información, vea [Get started with Storm on HDInsight cluster](hdinsight-apache-storm-tutorial-get-started-linux.md) (Introducción a Storm en clúster de HDInsight).

    > [!IMPORTANT]
    > Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las superiores. Consulte la información sobre la [retirada de HDInsight en Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

* Un [Centro de eventos de Azure](../event-hubs/event-hubs-csharp-ephcs-getstarted.md).

* [Oracle Java Developer Kit (JDK) versión 8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) o equivalente, como [OpenJDK](http://openjdk.java.net/).

* [Maven](https://maven.apache.org/download.cgi): Maven es un sistema de compilación de proyectos para proyectos de Java.

* Un editor de texto o un entorno de desarrollo integrado (IDE).

    > [!NOTE]
    > El editor o IDE puede tener una funcionalidad específica para trabajar con Maven que no se trata en este documento. Para obtener información sobre las capacidades de su entorno de edición, consulte la documentación del producto que esté utilizando.

    * Un cliente SSH. Para más información, consulte [Uso SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

* Los comandos `ssh` y `scp`, que sirven para copiar archivos en el clúster de HDInsight. En Windows, se obtienen a través de Bash en Windows 10.

## <a name="understanding-the-example"></a>Descripción del ejemplo

El ejemplo [hdinsight-java-storm-eventhub](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub) contiene dos topologías:

La topología `resources/writer.yaml` escribe datos aleatorios en un centro de eventos de Azure. Los datos los genera el componente `DeviceSpout` y son un identificador de dispositivo y un valor de dispositivo aleatorios. Por lo tanto simula un hardware que emite un identificador de cadena y un valor numérico.

La topología `resources/reader.yaml` lee los datos del centro de eventos (es decir, los datos que escribe EventHubWriter,) analiza los datos JSON y registra los datos `deviceId` y `deviceValue`.

Con el formato de los datos como un documento JSON antes de escribir en el concentrador de eventos y cuando los lee el lector se analiza de JSON a de tuplas. El formato de la dirección JSON es el siguiente:

    { "deviceId": "unique identifier", "deviceValue": some value }

### <a name="project-configuration"></a>Configuración de proyecto

El archivo `POM.xml` contiene información de configuración para este proyecto de Maven. Las partes interesantes son:

#### <a name="event-hub-components"></a>Componentes del centro de eventos

El componente que lee y escribe en Azure Event Hubs se encuentra en el [repositorio de HDInsight](https://github.com/hdinsight/mvn-repo). En las siguientes secciones del archivo `POM.xml` se cargan los componentes de este repositorio

```xml
<repositories>
    <repository>
        <id>hdinsight-examples</id>
        <url>http://raw.github.com/hdinsight/mvn-repo/master</url>
    </repository>
</repositories>
```

#### <a name="the-eventhubs-storm-spout-dependency"></a>La dependencia de Spout de EventHubs Storm

```xml
<dependency>
    <groupId>com.microsoft</groupId>
    <artifactId>eventhubs</artifactId>
    <version>${storm.eventhub.version}</version>
</dependency>
```

Este xml define una dependencia para el paquete eventhubs, que contiene tanto un spout para la lectura de Event Hubs como un bolt para escribir ahí.

```xml
</source>
    <target>1.8</target>
    </configuration>
</plugin>
```

Este xml configura el proyecto para generar la salida para Java 8, versión mínima que utiliza HDInsight 3.5.

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

Este xml configura la solución para empaquetar la salida en un archivo uber jar. El archivo jar contiene el código del proyecto y necesita dependencias. También se usa para:

* Cambiar el nombre de archivos de licencia para las dependencias.
* Excluir la seguridad y las firmas.
* Asegúrese de combinar varias implementaciones de la misma interfaz en una sola entrada.

Estos valores de configuración evitan errores en tiempo de ejecución.

#### <a name="topology-definitions"></a>Definiciones de topología

En este ejemplo se utiliza el marco [Flux](https://storm.apache.org/releases/1.1.0/flux.html). El marco usa YAML para definir las topologías. La principal ventaja es que no se codifica la topología de forma rígida en el código de Java. Dado que la definición es YAML, se puede cambiar antes de enviar la topología, sin tener que volver a compilar todo el contenido.

__writer.yaml__:

```yaml
---
# Topology that reads from Event Hubs
name: "eventhubwriter"

components:
  # Configure the Event Hub spout
  - id: "eventhubbolt-config"
    className: "org.apache.storm.eventhubs.bolt.EventHubBoltConfig"
    constructorArgs:
      # These are populated from the .properties file when the topology is started
      - "${eventhub.write.policy.name}"
      - "${eventhub.write.policy.key}"
      - "${eventhub.namespace}"
      - "servicebus.windows.net"
      - "${eventhub.name}"

spouts:
  - id: "device-emulator-spout"
    className: "com.microsoft.example.DeviceSpout"
    parallelism: ${eventhub.partitions}

bolts:
  - id: "eventhub-bolt"
    className: "org.apache.storm.eventhubs.bolt.EventHubBolt"
    constructorArgs:
      - ref: "eventhubbolt-config" # config declared in components section
    # parallelism hint. This should be the same as the number of partitions for your Event Hub, so we read it from the dev.properties file passed at run time.
    parallelism: ${eventhub.partitions}

  # Log information
  - id: "log-bolt"
    className: "org.apache.storm.flux.wrappers.bolts.LogInfoBolt"
    parallelism: 1

# How data flows through the components
streams:
  - name: "spout -> eventhub" # just a string used for logging
    from: "device-emulator-spout"
    to: "eventhub-bolt"
    grouping:
        type: SHUFFLE

  - name: "spout -> logger"
    from: "device-emulator-spout"
    to: "log-bolt"
    grouping:
        type: SHUFFLE
```

__reader.yaml__:

```yaml
---
# Topology that reads from Event Hubs
name: "eventhubreader"

components:
  # Configure the Event Hub spout
  - id: "eventhubspout-config"
    className: "org.apache.storm.eventhubs.spout.EventHubSpoutConfig"
    constructorArgs:
      # These are populated from the .properties file when the topology is started
      - "${eventhub.read.policy.name}"
      - "${eventhub.read.policy.key}"
      - "${eventhub.namespace}"
      - "${eventhub.name}"
      - ${eventhub.partitions}

spouts:
  - id: "eventhub-spout"
    className: "org.apache.storm.eventhubs.spout.EventHubSpout"
    constructorArgs:
      - ref: "eventhubspout-config" # config declared in components section
    # parallelism hint. This should be the same as the number of partitions for your Event Hub, so we read it from the dev.properties file passed at run time.
    parallelism: ${eventhub.partitions}

bolts:
  # Log information
  - id: "log-bolt"
    className: "org.apache.storm.flux.wrappers.bolts.LogInfoBolt"
    parallelism: 1

  # Parses from JSON into tuples
  - id: "parser-bolt"
    className: "com.microsoft.example.ParserBolt"
    parallelism: ${eventhub.partitions}

# How data flows through the components
streams:
  - name: "spout -> parser" # just a string used for logging
    from: "eventhub-spout"
    to: "parser-bolt"
    grouping:
        type: SHUFFLE

  - name: "parser -> log-bolt"
    from: "parser-bolt"
    to: "log-bolt"
    grouping:
        type: SHUFFLE
```

#### <a name="tell-the-topology-about-event-hub"></a>Indicación de la topología al centro de eventos

En tiempo de ejecución, el archivo `dev.properties` sirve para pasar la configuración del centro de eventos a la topología. El ejemplo siguiente es el contenido predeterminado del archivo:

```yaml
eventhub.write.policy.name: writer
eventhub.write.policy.key: your_key_here
eventhub.read.policy.name: reader
eventhub.read.policy.key: your_key_here
eventhub.namespace: your_namespace_here
eventhub.name: storm
eventhub.partitions: 2
```

## <a name="configure-environment-variables"></a>Configuración de las variables de entorno

Pueden establecer las siguientes variables de entorno al instalar Java y el JDK en la estación de trabajo de desarrollo. Sin embargo, debe comprobar que existen y que contienen los valores correctos para su sistema.

* **JAVA_HOME**: debe apuntar al directorio donde está instalado Java Runtime Environment (JRE). Por ejemplo, en una distribución de Unix o Linux, debe tener un valor similar a `/usr/lib/jvm/java-7-oracle`. En Windows, tendría un valor similar a `c:\Program Files (x86)\Java\jre1.7`
* **PATH** : debe contener las rutas de acceso siguientes:

  * **JAVA_HOME** (o la ruta de acceso equivalente).
  * **JAVA_HOME\bin** (o la ruta de acceso equivalente).
  * El directorio donde está instalado Maven

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

2. Modifique el archivo `dev.properties` con la configuración del centro de eventos.

3. Para compilar y empaquetar el proyecto, use lo siguiente:

        mvn package

    Este comando descarga las dependencias requeridas, las compilaciones y, luego, empaqueta el proyecto. La salida se almacena en el directorio **/target** como **EventHubExample-1.0-SNAPSHOT.jar**.

## <a name="test-locally"></a>Prueba local

Dado que estas topologías solo leen y escriben en los centros de eventos, puede realizar una prueba local si tiene un [entorno de desarrollo de Storm](http://storm.apache.org/releases/current/Setting-up-development-environment.html). Para la ejecución local en el entorno de desarrollo, utilice los pasos siguientes:

1. Ejecute la escritura:

        storm jar EventHubExample-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local -R /writer.yaml --filter dev.properties

2. Ejecute la lectura:

        storm jar EventHubExample-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local -R /reader.yaml --filter dev.properties

> [!TIP]
> * `--local`: ejecuta la topología en modo local (no distribuida).
> * `-R /writer.yaml`: carga la definición de la topología del `resources` empaquetado en el archivo jar. Si la topología es un archivo del sistema de archivos local, especifique su ruta de acceso como último parámetro.
> * `--filter dev.properties`: usa el contenido de `dev.properties` para rellenar los valores de las definiciones de las topologías. Por ejemplo: `${eventhub.read.policy.name}`.

La salida se registra en la consola cuando se ejecuta localmente. Para detener la topología, use __Ctrl + C__.

## <a name="deploy-the-topologies"></a>Implementación de las topologías

1. Use SCP para copiar el paquete jar en su clúster de HDInsight. Reemplace USERNAME con el usuario SSH para el clúster. Reemplace CLUSTERNAME por el nombre del clúster de HDInsight:

        scp ./target/EventHubExample-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.

    Si usó una contraseña para la cuenta SSH, se le pedirá que escriba la contraseña. Si usó una clave SSH con la cuenta, puede que necesite usar el parámetro `-i` para especificar la ruta de acceso al archivo de clave. Por ejemplo: `scp -i ~/.ssh/id_rsa ./target/EventHubExample-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.`

    Este comando copia el archivo en el directorio principal del usuario SSH en el clúster.

2. Cuando termine de cargar el archivo, use SSH para conectarse con el clúster de HDInsight. Reemplace **USERNAME** por el nombre de su inicio de sesión de SSH. Reemplace **CLUSTERNAME** por el nombre del clúster de HDInsight.

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    > [!NOTE]
    > Si usó una contraseña para la cuenta SSH, se le pedirá que escriba la contraseña. Si usó una clave SSH con la cuenta, puede que necesite usar el parámetro `-i` para especificar la ruta de acceso al archivo de clave. En el ejemplo siguiente se cargará la clave privada desde `~/.ssh/id_rsa`:
    >
    > `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`

3. Use el comando siguiente para iniciar las topologías.

        storm jar EventHubExample-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writer.yaml --filter dev.properties
        storm jar EventHubExample-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /reader.yaml --filter dev.properties

    > [!TIP]
    > * `--remote`: envía la topología al servicio Nimbus, que la inicia en los nodos de trabajo del clúster.

4. Para ver los datos registrados, vaya a https://CLUSTERNAME.azurehdinsight.net/stormui, donde __CLUSTERNAME__ es el nombre del clúster de HDInsight. Seleccione las topologías y profundice hasta los componentes. Seleccione la entrada del __puerto__ para que la instancia de un componente vea la información registrada.

5. Use el comando siguiente para detener las topologías.

        storm kill reader
        storm kill writer

## <a name="delete-your-cluster"></a>Eliminación del clúster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Pasos siguientes

* [Topologías de ejemplo para Storm en HDInsight](hdinsight-storm-example-topology.md)

