<properties
   pageTitle="Procesamiento de eventos desde centros de eventos con Storm en HDInsight con Java | Azure"
   description="Obtenga información sobre cómo procesar los datos de los Centros de eventos con una topología Java de Storm creada con Maven."
   services="hdinsight,notification hubs"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/11/2016"
   ms.author="larryfr"/>


# <a name="process-events-from-azure-event-hubs-with-storm-on-hdinsight-(java)"></a>Procesamiento de eventos desde Centros de eventos de Azure con Storm en HDInsight (Java)

Los Centros de eventos de Azure le permiten procesar grandes volúmenes de datos provenientes de sitios web, aplicaciones y dispositivos. El spout de los Centros de eventos permite que Apache Storm en HDInsight analice fácilmente estos datos en tiempo real. También es posible escribir datos en Centros de eventos desde Storm usando el bolt de Centros de eventos.

En este tutorial, obtendrá información sobre cómo usar el spout y bolt de Centros de eventos para leer y escribir datos en una topología de Storm basada en Java.

## <a name="prerequisites"></a>Requisitos previos

* Un clúster de Apache Storm en HDInsight Use uno de los siguientes artículos de introducción para crear un clúster:

    - Un [clúster de Storm basado en Linux en HDInsight](hdinsight-apache-storm-tutorial-get-started-linux.md): seleccione esta opción si desea usar SSH para trabajar con el clúster desde clientes Windows, Unix, OS X o Linux.

    - Un [clúster de Storm basado en Windows en HDInsight](hdinsight-apache-storm-tutorial-get-started.md): seleccione esta opción si desea usar PowerShell para trabajar con el clúster desde un cliente Windows.

    > [AZURE.NOTE] Los pasos descritos en este documento se basan en el uso de un clúster de Storm en la versión 3.3 o 3.4 de HDInsight. Este tipo de clúster proporciona Storm 0.10.0 y Hadoop 2.7, lo que reduce el número de pasos necesarios para que el ejemplo funcione.
    >
    > Para obtener una versión de este ejemplo que funcione con Storm 0.9.3 en HDInsight 3.2, consulte la rama de [Storm v0.9.3](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub/tree/Storm_v0.9.3) del repositorio de ejemplos.

* Un [Centro de eventos de Azure](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

* [Oracle Java Developer Kit (JDK) versión 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) o equivalente, como [OpenJDK](http://openjdk.java.net/)

* [Maven](https://maven.apache.org/download.cgi): Maven es un sistema de compilación de proyectos para proyectos de Java.

* Un editor de texto o un entorno de desarrollo integrado (IDE) de Java

    > [AZURE.NOTE] El editor o IDE puede tener una funcionalidad específica para trabajar con Maven que no se trata en este documento. Para obtener información sobre las capacidades de su entorno de edición, consulte la documentación del producto que esté utilizando.

 * Un cliente SSH. Para obtener más información sobre el uso de SSH con HDInsight, consulte uno de los siguientes artículos:

    - [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Linux, Unix u OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

    - [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

* Un cliente de SCP. Esto se ofrece con todos los sistemas Linux, Unix y OS X. Para clientes Windows se recomienda PSCP, que está disponible en la [página de descarga de PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

##<a name="understanding-the-example"></a>Descripción del ejemplo

El ejemplo [hdinsight-java-storm-eventhub](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub) contiene dos topologías:

__com.microsoft.example.EventHubWriter__ escribe datos aleatorios en un Centro de eventos de Azure. Los datos los genera un spout y son un identificador de dispositivo y un valor del dispositivo aleatorios. Por lo tanto simula un hardware que emite un identificador de cadena y un valor numérico.

__com.microsoft.example.EventHubReader__ lee los datos desde Centro de eventos (es decir, los datos escritos por EventHubWriter) y los almacena en HDFS (WASB en este caso, ya que esto se escribió y se probó con HDInsight de Azure) en el directorio /devicedata.

Con el formato de los datos como un documento JSON antes de escribir en el concentrador de eventos y cuando los lee el lector se analiza de JSON a de tuplas. El formato de la dirección JSON es el siguiente:

    { "deviceId": "unique identifier", "deviceValue": some value }

La razón para usar un documento JSON para almacenar los datos en Centro de eventos es por lo que sabemos cuál es el formato, en lugar de confiar en los mecanismos internos de formato de Spout y bolt de Centro de eventos.

###<a name="project-configuration"></a>Configuración de proyecto

El archivo **POM.xml** contiene información de configuración para este proyecto Maven. Las partes interesantes son:

####<a name="the-eventhubs-storm-spout-dependency"></a>La dependencia de Spout de EventHubs Storm

    <dependency>
      <groupId>org.apache.storm</groupId>
      <artifactId>storm-eventhubs</artifactId>
      <version>0.10.0</version>
    </dependency>

De este modo, se agrega una dependencia para el paquete storm-eventhubs, que contiene tanto un spout para la lectura de Centros de eventos como un bolt para la escritura en ellos.

> [AZURE.NOTE] Este paquete solo está disponible para la versión de Storm 0.10.0 y las posteriores. Al usar Storm 0.9.3, debe instalar manualmente el paquete de spout que proporciona Microsoft. Para obtener un ejemplo de cómo trabajar con Storm 0.9.3, consulte la rama de [Storm v0.9.3](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub/tree/Storm_v0.9.3) del repositorio de ejemplos.

####<a name="the-hdfsbolt-and-wasb-components"></a>Los componentes HdfsBolt y WASB

El HdfsBolt se usa normalmente para almacenar datos en el sistema de archivos distribuidos Hadoop (HDFS). Sin embargo, los clústeres de HDInsight usan Almacenamiento de Azure (WASB) como almacén de datos predeterminado, por lo que es necesario cargar varios componentes que permiten a HdfsBolt comprender el sistema de archivos WASB.

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
        <version>0.10.0</version>
        </dependency>
    <!--So HdfsBolt knows how to talk to WASB -->
    <dependency>
        <groupId>org.apache.hadoop</groupId>
        <artifactId>hadoop-client</artifactId>
        <version>2.7.1</version>
    </dependency>
    <dependency>
        <groupId>org.apache.hadoop</groupId>
        <artifactId>hadoop-hdfs</artifactId>
        <version>2.7.1</version>
    </dependency>
    <dependency>
        <groupId>org.apache.hadoop</groupId>
        <artifactId>hadoop-azure</artifactId>
        <version>2.7.1</version>
    </dependency>
    <dependency>
        <groupId>org.apache.hadoop</groupId>
        <artifactId>hadoop-common</artifactId>
        <version>2.7.1</version>
        <exclusions>
        <exclusion>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
        </exclusion>
        </exclusions>
    </dependency>

> [AZURE.NOTE] Al trabajar con una versión anterior de HDInsight, como la 3.2, debe registrar manualmente estos componentes. Para obtener un ejemplo de este proceso, junto con los bits personalizados necesarios para clústeres de HDInsight antiguos, consulte la rama de [Storm v0.9.3](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub/tree/Storm_v0.9.3) del repositorio de ejemplos.

####<a name="the-maven-compiler-plugin"></a> El complemento maven-compiler-plugin

    <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-compiler-plugin</artifactId>
      <version>2.3.2</version>
      <configuration>
        <source>1.7</source>
        <target>1.7</target>
      </configuration>
    </plugin>

Le dice a Maven que el proyecto se debe compilar con compatibilidad para Java 7, que es lo que sirve para los clústeres de HDInsight.

####<a name="the-maven-shade-plugin"></a>El complemento maven-shade-plugin

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

Se usa para empaquetar la solución en un uber jar que contiene el código del proyecto y las dependencias requeridas. También se usa para:

* Cambiar el nombre de los archivos de licencia para las dependencias: si no se hace, se puede producir un error en tiempo de ejecución en clústeres de HDInsight basados en Windows.

* Excluir seguridad o firmas: si no se hace, se puede producir un error en tiempo de ejecución en el clúster de HDInsight.

* Asegúrese de combinar varias implementaciones de la misma interfaz en una sola entrada. De lo contrario, se producirán errores debidos a que el bolt Storm HDFS no entiende cómo comunicarse con el sistema de archivos de WASB.

####<a name="the-exec-maven-plugin"></a>El complemento exec-maven-plugin

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

Permite ejecutar fácilmente la topología de forma local en el entorno de desarrollo usando el siguiente comando:

    mvn compile exec:java -Dstorm.topology=<CLASSNAME>

Por ejemplo: `mvn compile exec:java -Dstorm.topology=com.microsoft.example.EventHubWriter`.

####<a name="the-resources-section"></a>La sección de recursos

    <resources>
      <resource>
        <directory>${basedir}/conf</directory>
        <filtering>false</filtering>
        <includes>
          <include>EventHubs.properties</include>
          <include>core-site.xml</include>
        </includes>
      </resource>
    </resources>

Esto define los recursos necesarios para el proyecto:

- **EventHubs.properties**: contiene información que se usa para conectarse a un Centro de eventos de Azure
- **core-site.xml**: contiene información sobre Almacenamiento de Azure que usa el clúster de HDInsight.

Tiene que rellenar ambos con información sobre el clúster de concentrador de eventos y HDInsight.

##<a name="configure-environment-variables"></a>Configuración de las variables de entorno

Pueden establecer las siguientes variables de entorno al instalar Java y el JDK en la estación de trabajo de desarrollo. Sin embargo, debe comprobar que existen y que contienen los valores correctos para su sistema.

* **JAVA_HOME**: debe apuntar al directorio donde está instalado Java Runtime Environment (JRE). Por ejemplo, en una distribución de Unix o Linux, debe tener un valor similar a `/usr/lib/jvm/java-7-oracle`. En Windows, tendría un valor similar a `c:\Program Files (x86)\Java\jre1.7`

* **PATH** : debe contener las rutas de acceso siguientes:

    * **JAVA_HOME** (o la ruta de acceso equivalente).

    * **JAVA_HOME\bin** (o la ruta de acceso equivalente).

    * El directorio donde está instalado Maven

## <a name="configure-event-hub"></a>Configuración del centro de eventos

Centros de eventos es el origen de datos para este ejemplo. Utilice los pasos siguientes para crear un centro de eventos.

1. En el [Portal de Azure clásico](https://manage.windowsazure.com), seleccione **NUEVO** > **Service Bus** > **Centro de eventos** > **Creación personalizada**.

2. En la pantalla **Agregar un concentrador de eventos nuevo**, escriba un **Nombre del centro de eventos**, seleccione la **Región** donde se va a crear el centro y cree un nuevo espacio de nombres o seleccione uno existente. Haga clic en la **Flecha** para continuar.

    ![página 1 del asistente](./media/hdinsight-storm-develop-csharp-event-hub-topology/wiz1.png)

    > [AZURE.NOTE] Debe seleccionar la misma **Ubicación** de su servidor Storm en HDInsight para disminuir la latencia y los costos.

2. En la pantalla **Configurar el concentrador de eventos**, escriba los valores **Recuento de particiones** y **Retención de mensajes**. En este ejemplo, utilice un recuento de particiones de 10 y una retención de mensajes de 1. Anote el recuento de particiones, porque necesitará más adelante este valor.

    ![página 2 del asistente](./media/hdinsight-storm-develop-csharp-event-hub-topology/wiz2.png)

3. Una vez se ha creado el concentrador de eventos, seleccione el espacio de nombres, seleccione **Concentradores de eventos**y, después, seleccione el concentrador de eventos que creó anteriormente.

4. Seleccione **Configurar**y cree dos directivas de acceso con la información siguiente:

    <table>
    <tr><th>Nombre</th><th>Permisos</th></tr>
    <tr><td>Escritor</td><td>Los métodos Send</td></tr>
    <tr><td>Lector</td><td>Escuchar</td></tr>
    </table>

    Después de crear los permisos, seleccione el icono **Guardar** en la parte inferior de la página. Con esto se crean las directivas de acceso compartido que se usarán para enviar (escritor) y escuchar (lector) a este Centro de eventos.

    ![directivas](./media/hdinsight-storm-develop-csharp-event-hub-topology/policy.png)

5. Después de guardar las directivas, use el **generador de claves de acceso compartido** que se encuentra en la parte inferior de la página para recuperar la clave para las directivas de **escritor** y **lector**. Guárdelas, ya que se usarán después.

## <a name="download-and-build-the-project"></a>Descarga y compilación del proyecto.

1. Descargue el proyecto de GitHub: [hdinsight-java-storm-eventhub](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub). Puede descargar el paquete como un archivo zip o usar [git](https://git-scm.com/) para clonar el proyecto de forma local.

2. Para compilar y empaquetar el proyecto, use lo siguiente:

        mvn package

    Esto descargará las dependencias requeridas, compilación y, a continuación, el paquete del proyecto. La salida se almacenará en el directorio __/target__ como __EventHubExample-1.0-SNAPSHOT.jar__.

## <a name="deploy-the-topologies"></a>Implementación de las topologías

El jar creado por este proyecto contiene dos topologías: __com.microsoft.example.EventHubWriter__ y __com.microsoft.example.EventHubReader__. La topología de EventHubWriter debe iniciarse en primer lugar, ya que escribe eventos en Centro de eventos que luego se leen en EventHubReader.

###<a name="if-using-a-linux-based-cluster"></a>Si se usa un clúster basado en Linux

1. Use SCP para copiar el paquete jar en su clúster de HDInsight. Reemplace USERNAME con el usuario SSH para el clúster. Reemplace CLUSTERNAME por el nombre del clúster de HDInsight:

        scp ./target/EventHubExample-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.

    Si usó una contraseña para la cuenta SSH, se le pedirá que escriba la contraseña. Si usó una clave SSH con la cuenta, puede que necesite usar el parámetro `-i` para especificar la ruta de acceso al archivo de clave. Por ejemplo: `scp -i ~/.ssh/id_rsa ./target/EventHubExample-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.`.

    > [AZURE.NOTE] Si el cliente es una estación de trabajo de Windows, puede que no tenga instalado un comando de SCP. Se recomienda PSCP que puede descargarse desde la [página de descarga de PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

    Este comando copiará el archivo en el directorio principal del usuario SSH en el clúster.

1. Cuando termine de cargar el archivo, use SSH para conectarse con el clúster de HDInsight. Reemplace **USERNAME** por el nombre de su inicio de sesión de SSH. Reemplace **CLUSTERNAME** por el nombre del clúster de HDInsight.

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    > [AZURE.NOTE] Si usó una contraseña para la cuenta SSH, se le pedirá que escriba la contraseña. Si usó una clave SSH con la cuenta, puede que necesite usar el parámetro `-i` para especificar la ruta de acceso al archivo de clave. En el ejemplo siguiente se cargará la clave privada desde `~/.ssh/id_rsa`:
    >
    > `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`

    Si usa PuTTY, escriba `CLUSTERNAME-ssh.azurehdinsight.net` en el campo __Host name (or IP address)__ (Nombre de host [o dirección IP]) y haga clic en __Open__ (Abrir) para conectarse. Se le pedirá que escriba el nombre de cuenta de SSH.

    > [AZURE.NOTE] Si usó una contraseña para la cuenta SSH, se le pedirá que escriba la contraseña. Si usó una clave SSH con la cuenta, puede que necesite seguir los pasos a continuación para seleccionar la clave:
    >
    > 1. En **Category** (Categoría), expanda **Connection** (Conexión), **SSH** y seleccione **Auth** (Autenticar).
    > 2. Haga clic en **Browse** (Examinar) y seleccione el archivo .ppk que contiene su clave privada.
    > 3. Haga clic en __Open__ (Abrir) para conectarse.

2. Use el comando siguiente para iniciar las topologías.

        storm jar EventHubExample-1.0-SNAPSHOT.jar com.microsoft.example.EventHubWriter writer
        storm jar EventHubExample-1.0-SNAPSHOT.jar com.microsoft.example.EventHubReader reader

    Esto iniciará las topologías y les dará un nombre descriptivo de "lectura" y "escritura".

3. Espere un minuto o dos para que las topologías escriban y lean los eventos desde Centro de eventos y, a continuación, use el siguiente comando para comprobar que EventHubReader está almacenando los datos en el almacenamiento de HDInsight:

        hadoop fs -ls /devicedata

    Esto debería devolver una lista de archivos similar a la siguiente:

        -rw-r--r--   1 storm supergroup      10283 2015-08-11 19:35 /devicedata/wasbbolt-14-0-1439321744110.txt
        -rw-r--r--   1 storm supergroup      10277 2015-08-11 19:35 /devicedata/wasbbolt-14-1-1439321748237.txt
        -rw-r--r--   1 storm supergroup      10280 2015-08-11 19:36 /devicedata/wasbbolt-14-10-1439321760398.txt
        -rw-r--r--   1 storm supergroup      10267 2015-08-11 19:36 /devicedata/wasbbolt-14-11-1439321761090.txt
        -rw-r--r--   1 storm supergroup      10259 2015-08-11 19:36 /devicedata/wasbbolt-14-12-1439321762679.txt

    > [AZURE.NOTE] Algunos archivos pueden mostrar un tamaño de 0, ya que EventHubReader los creó pero aún no tienen datos almacenados.

    Puede ver el contenido de estos archivo mediante el siguiente comando:

        hadoop fs -text /devicedata/*.txt

    Esto devolverá un valor similar al siguiente:

        3409e622-c85d-4d64-8622-af45e30bf774,848981614
        c3305f7e-6948-4cce-89b0-d9fbc2330c36,-1638780537
        788b9796-e2ab-49c4-91e3-bc5b6af1f07e,-1662107246
        6403df8a-6495-402f-bca0-3244be67f225,275738503
        d7c7f96c-581a-45b1-b66c-e32de6d47fce,543829859
        9a692795-e6aa-4946-98c1-2de381b37593,1857409996
        3c8d199b-0003-4a79-8d03-24e13bde7086,-1271260574

    La primera columna contiene el valor de identificador de dispositivo y la segunda el valor del dispositivo.

4. Use el comando siguiente para detener las topologías.

        storm kill reader
        storm kill writer

###<a name="if-using-a-windows-based-cluster"></a>Si se usa un clúster basado en Windows

1. Abra https://CLUSTERNAME.azurehdinsight.net en el explorador. Cuando se le pida, escriba las credenciales de administrador para el clúster de HDInsight. Llegará al Panel de Storm.

2. Use la lista desplegable __Jar File__ (Archivo Jar) para examinar y seleccionar el archivo EventHubExample-1.0-SNAPSHOT.jar desde el entorno de compilación.

3. Para __Nombre de clase__, escriba `com.mirosoft.example.EventHubWriter`.

4. Para __Parámetros adicionales__, escriba `writer`. Por último, haga clic en __Submit__ (Enviar) para cargar el archivo jar e iniciar la topología de EventHubWriter.

5. Una vez que se inicie la topología, use el formulario para iniciar EventHubReader:

    * __Jar File__(Archivo Jar): seleccione el archivo EventHubExample-1.0-SNAPSHOT.jar que se cargó previamente
    * __Nombre de clase__: escriba `com.microsoft.example.EventHubReader`.
    * __Parámetros adicionales__: escriba `reader`

    Haga clic en Submit (Enviar) para iniciar la topología de EventHubReader.

6. Espere unos minutos para permitir a las topologías generar eventos y almacenarlos Azure Storage; después, seleccione la pestaña __Hadoop Query Console__ (Consola de consultas de Hadoop) situada en la parte superior de la página del __Panel de Storm__.

7. En __Consola de consultas__, seleccione __Hive Editor__ (Editor Hive) y reemplace el valor predeterminado `select * from hivesampletable` por lo siguiente:

        create external table devicedata (deviceid string, devicevalue int) row format delimited fields terminated by ',' stored as textfile location 'wasbs:///devicedata/';
        select * from devicedata limit 10;

    Haga clic en __Select__ (Seleccionar) para ejecutar la consulta. Esto devolverá a través de EventHubReader, 10 filas de los datos escritos en Almacenamiento de Azure (WASB). Una vez terminada la consulta, debería ver datos similares a los siguientes:

        3409e622-c85d-4d64-8622-af45e30bf774,848981614
        c3305f7e-6948-4cce-89b0-d9fbc2330c36,-1638780537
        788b9796-e2ab-49c4-91e3-bc5b6af1f07e,-1662107246
        6403df8a-6495-402f-bca0-3244be67f225,275738503
        d7c7f96c-581a-45b1-b66c-e32de6d47fce,543829859
        9a692795-e6aa-4946-98c1-2de381b37593,1857409996
        3c8d199b-0003-4a79-8d03-24e13bde7086,-1271260574

8. Seleccione el __Panel de Storm__ en la parte superior de la página y, después, __Storm UI__ (IU de Storm). Desde __Storm UI__ (IU de Storm), seleccione el vínculo de la topología de __lector__ y use el botón __Terminar__ para detener la topología. Repita el proceso para la topología de __escritura__ .



### <a name="checkpointing"></a>Puntos de control

El elemento EventHubSpout envía periódicamente puntos de control de su estado al nodo Zookeeper, que almacena el desplazamiento actual de los mensajes leídos de la cola. Esto permite que el componente empiece a recibir mensajes en el desplazamiento almacenado en los escenarios siguientes:

* Se produce un error en la instancia del componente y se reinicia.

* Aumenta o reduce el clúster, mediante la incorporación o eliminación de nodos.

* Se elimina y reinicia la topología **con el mismo nombre**.

####<a name="on-windows-based-hdinsight-clusters"></a>En clústeres de HDInsight basados en Windows

También puede exportar e importar los puntos de control persistentes a WASB (Almacenamiento de Azure que usa su clúster de HDInsight). Los scripts para hacerlo se encuentran en el clúster de HDInsight en Storm, en **c:\apps\dist\storm-0.9.3.2.2.1.0-2340\zkdatatool-1.0\bin**.

>[AZURE.NOTE] El número de versión en la ruta de acceso puede ser diferente, ya que la versión de Storm instalada en el clúster puede cambiar en el futuro.

Los scripts en este directorio son:

* **stormmeta_import.cmd**: importar todos los metadatos de Storm del contenedor de almacenamiento del clúster predeterminado a Zookeeper.

* **stormmeta_export.cmd**: exportar todos los metadatos de Storm de Zookeeper al contenedor de almacenamiento del clúster predeterminado.

* **stormmeta_delete.cmd**: eliminar todos los metadatos de Storm de Zookeeper.

La exportación de una importación permite conservar los datos de puntos de control cuando deba eliminar el clúster, pero quiera reanudar el procesamiento desde el desplazamiento actual en el centro cuando vuelva a poner en línea un nuevo clúster.

> [AZURE.NOTE] Dado que los datos se conservan en el contenedor de almacenamiento predeterminado, el nuevo clúster **debe** utilizar la misma cuenta de almacenamiento y el mismo contenedor que el clúster anterior.

## <a name="delete-your-cluster"></a>Eliminación del clúster

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

##<a name="troubleshooting"></a>Solución de problemas

Si no ve los archivos que se almacenan en la ubicación /devicedata (bien usando el comando `hadoop fs -ls /devicedata` o el comando de Hive en Query Console) use la interfaz de usuario de Storm para buscar los errores devueltos por las topologías.

Para obtener más información sobre el uso de IU de Storm, vea los siguientes temas:

* Si está usando Storm __basado en Linux__ en un clúster de HDInsight, consulte [Implementación y administración de topologías de Apache Storm en HDInsight basado en Linux](hdinsight-storm-deploy-monitor-topology-linux.md)

* Si está usando Storm __basado en Windows__ en un clúster de HDInsight, consulte [Implementación y administración de topologías de Apache Storm en HDInsight basado en Windows](hdinsight-storm-deploy-monitor-topology-linux.md)

##<a name="next-steps"></a>Pasos siguientes

* [Topologías de ejemplo para Storm en HDInsight](hdinsight-storm-example-topology.md)



<!--HONumber=Oct16_HO2-->


