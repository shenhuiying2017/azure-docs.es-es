---
title: "Análisis de los datos de los sensores con Apache Storm y HBase | Microsoft Docs"
description: "Obtenga información acerca de cómo conectarse a Apache Storm con una red virtual. Utilice Storm con HBase para procesar los datos de sensores de un centro de eventos y visualizarlos con D3.js."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: a9a1ac8e-5708-4833-b965-e453815e671f
ms.service: hdinsight
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/12/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 279990a67ae260b09d056fd84a12160150eb4539
ms.openlocfilehash: 0d2743f10d828aaf5ef401ac5378c94384e0a46b


---
# <a name="analyze-sensor-data-with-apache-storm-event-hub-and-hbase-in-hdinsight-hadoop"></a>Análisis de datos de sensores con Apache Storm, Centro de eventos y HBase en HDInsight (Hadoop)
Aprenda a usar Apache Storm en HDInsight para procesar datos de sensor desde el Centro de eventos de Azure, almacenarlos en Apache HBase en HDInsight y visualizarlos mediante D3.js que se ejecuta como una aplicación web de Azure.

La plantilla de Azure Resource Manager utilizada en este documento muestra cómo crear varios recursos de Azure en un grupo de recursos. Concretamente, crea una Red virtual de Azure, dos clústeres de HDInsight (Storm y HBase) y una aplicación web de Azure. Se realiza automáticamente una implementación de node.js de un panel web en tiempo real en la aplicación web.

> [!NOTE]
> La información de este documento, así como el ejemplo proporcionado, se han probado con las versiones 3.3 y 3.4 de clúster de HDInsight basado en Linux.
>
> Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las superiores. Para más información, consulte [El contrato de nivel de servicio para las versiones de clúster de HDInsight](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

## <a name="prerequisites"></a>Requisitos previos
* Una suscripción de Azure. Consulte [How to get Azure Free trial for testing Hadoop in HDInsight (Obtención de una versión de prueba gratuita de Azure para probar Hadoop en HDInsight)](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
  
  > [!IMPORTANT]
  > No necesita un clúster de HDInsight existente; los pasos descritos en este documento crearán los recursos siguientes:
  > 
  > * Una red virtual de Azure
  > * Un clúster de Storm en HDInsight (dos nodos de trabajo basados en Linux)
  > * Un clúster de HBase en HDInsight (dos nodos de trabajo basados en Linux)
  > * Una aplicación web de Azure que hospeda el panel web
  > 
  > 
* [Node.js](http://nodejs.org/): se utiliza para obtener una vista previa del panel web localmente en su entorno de desarrollo.
* [Java y JDK 1.7](http://www.oracle.com/technetwork/java/javase/downloads/index.html): se utiliza para desarrollar la topología de Storm.
* [Maven](http://maven.apache.org/what-is-maven.html): se usa para crear y compilar el proyecto.
* [Git](http://git-scm.com/): se utiliza para descargar el proyecto de GitHub.
* Un cliente **SSH** : se usa para conectarse a los clústeres de HDInsight basados en Linux. Para más información sobre el uso de SSH con HDInsight, consulte los documentos siguientes.
  
  * [Utilización de SSH con HDInsight desde un cliente Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
  * [Utilización de SSH con HDInsight desde un cliente Linux, Unix o Mac](hdinsight-hadoop-linux-use-ssh-unix.md)
    
    > [!NOTE]
    > También debe tener acceso al comando `scp`, que se utiliza para copiar archivos entre el entorno de desarrollo local y el clúster de HDInsight mediante SSH.
    > 
    > 

## <a name="architecture"></a>Arquitectura
![diagrama de arquitectura](./media/hdinsight-storm-sensor-data-analysis/devicesarchitecture.png)

Este ejemplo consta de los siguientes componentes:

* **Centros de eventos de Azure**: contiene datos que se recopilan de los sensores. En este ejemplo, se ofrece una aplicación que genera los datos.
* **Storm en HDInsight**: ofrece procesamiento en tiempo real de datos desde el Centro de eventos.
* **HBase en HDInsight**: proporciona un almacén de datos NoSQL persistente para los datos después de haberse procesado en Storm.
* **Servicio de Red virtual de Azure**: habilita las comunicaciones seguras entre el Storm en HDInsight y HBase en clústeres de HDInsight.
  
  > [!NOTE]
  > Se requiere una red virtual para usar la API de cliente HBase de Java, ya no se expone a través de la puerta de enlace pública para clústeres de HBase. La instalación de clústeres de HBase y Storm en la misma red virtual permite que el clúster de Storm (o cualquier otro sistema de la red virtual) acceda directamente a HBase mediante la API de cliente.
  > 
  > 
* **Sitio web del panel**: un panel de ejemplo que muestra datos en tiempo real.
  
  * El sitio web se implementa en Node.js, de forma que puede ejecutarse en cualquier sistema operativo cliente para realizar pruebas o se puede implementar en sitios web de Azure.
  * [Socket.io](http://socket.io/) se usa para la comunicación en tiempo real entre la topología Storm y el sitio web.
    
    > [!NOTE]
    > Se trata de un detalle de implementación. Puede usar cualquier marco de comunicaciones, como SignalR o WebSockets sin procesar.
    > 
    > 
  * [D3.js](http://d3js.org/) se usa para representar los datos que se envían al sitio web.

> [!IMPORTANT]
> Como no hay ningún método compatible para crear un clúster de HDInsight para Storm y HBase, se requieren dos clústeres
> 
> 

La topología lee datos del Centro de eventos mediante la clase [org.apache.storm.eventhubs.spout.EventHubSpout](http://storm.apache.org/releases/0.10.1/javadocs/org/apache/storm/eventhubs/spout/class-use/EventHubSpout.html) y escribe los datos en HBase usando la clase [org.apache.storm.hbase.bolt.HBaseBolt](https://storm.apache.org/javadoc/apidocs/org/apache/storm/hbase/bolt/class-use/HBaseBolt.html). La comunicación con el sitio web se logra mediante [client.java socket.io](https://github.com/nkzawa/socket.io-client.java).

A continuación, se muestra un diagrama de la topología.

![diagrama de topología](./media/hdinsight-storm-sensor-data-analysis/sensoranalysis.png)

> [!NOTE]
> Se trata de una vista muy simplificada de la topología. En tiempo de ejecución, se crea una instancia de cada componente por cada partición del Centro de eventos que se está leyendo. Estas instancias se distribuyen entre los nodos del clúster y los datos se enrutan entre ellos como sigue:
> 
> * Se equilibran las cargas de los datos del spout al analizador.
> * Los datos del analizador al panel y HBase se agrupan por identificador de dispositivo, para que los mensajes del mismo dispositivo fluyan siempre al mismo componente.
> 
> 

### <a name="topology-components"></a>Componentes de la topología
* **EventHub Spout**: el spout se proporciona como parte de Apache Storm versión 0.10.0 y versiones posteriores.
  
  > [!NOTE]
  > El spout de Event Hubs utilizado en este ejemplo requiere un clúster de Storm en HDInsight versión 3.3 o 3.4. Para más información sobre cómo usar Centros de eventos de Azure de Storm en HDInsight, consulte [Procesamiento de eventos desde Centros de eventos de Azure con Storm en HDInsight (C#)](hdinsight-storm-develop-java-event-hub-topology.md).
  > 
  > 
* **ParserBolt.java**: los datos que emite el spout son JSON sin procesar y, en ocasiones, se emite más de un evento a la vez. Este bolt muestra cómo leer los datos que emite el spout, y los emite a una secuencia nueva como una tupla que contiene varios campos.
* **DashboardBolt.java**: esto muestra cómo usar la biblioteca de cliente de Socket.io para Java para enviar datos en tiempo real al panel web.

En este ejemplo se usa el marco [Flux](https://storm.apache.org/releases/0.10.0/flux.html) , por lo que la definición de topología se encuentra en archivos YAML. Hay dos:

* **no-hbase.yaml** : use este archivo cuando pruebe la topología en el entorno de desarrollo. No utiliza componentes de HBase, ya que no se puede acceder a la API de Java de HBase desde fuera de la red virtual donde se encuentra el clúster.
* **with-hbase.yaml** : use este archivo cuando implemente la topología en el clúster de Storm. Usa componentes de HBase, ya que se ejecuta en la misma red virtual que el clúster de HBase.

## <a name="prepare-your-environment"></a>Preparación del entorno
Antes de usar este ejemplo, debe crear un Centro de eventos de Azure, desde el que lee la topología de Storm.

### <a name="configure-event-hub"></a>Configuración del centro de eventos
Centro de eventos es el origen de datos para este ejemplo. Utilice los pasos siguientes para crear un centro de eventos.

1. En [Azure Portal](https://portal.azure.com), seleccione **+ Nuevo** -> **Internet de las cosas** -> **Event Hubs**.
2. En la hoja **Crear espacio de nombres** , realice las siguientes tareas:
   
   1. Escriba un valor en **Nombre** para el espacio de nombres.
   2. Seleccione un plan de tarifa. **Básico** es suficiente para este ejemplo.
   3. Seleccione la suscripción de Azure que va a usar en **Suscripción** .
   4. Seleccione un grupo de recursos existente o cree uno.
   5. Seleccione un valor en **Ubicación** para el centro de eventos.
   6. Seleccione **Anclar al panel** y luego haga clic en **Crear**.
3. Cuando se completa el proceso de creación, se muestra la hoja Event Hubs para el espacio de nombres. Desde ahí, seleccione **+ Agregar Centro de eventos**. En la hoja **Create Event Hub** (Crear centro de eventos), escriba el nombre **sensordata** y seleccione **Crear**. Deje los demás campos con los valores predeterminados.
4. En la hoja Event Hubs para el espacio de nombres, seleccione **Event Hubs**. Seleccione la entrada **sensordata** .
5. En la hoja para el centro de eventos sensordata, seleccione **Directivas de acceso compartido**. Use el vínculo **+ Agregar** para agregar las siguientes directivas:

    | Nombre de la directiva | Notificaciones |
    | ----- | ----- |
    | devices | Enviar |
    | storm | Escuchar |

1. Seleccione ambas directivas y tome nota del valor en **CLAVE PRINCIPAL** . Necesitará el valor para ambas directivas en pasos posteriores.

## <a name="download-and-configure-the-project"></a>Descarga y configuración del proyecto
Use lo siguiente para descargar el proyecto de GitHub.

    git clone https://github.com/Blackmist/hdinsight-eventhub-example

Cuando se haya completado el comando, tendrá la siguiente estructura de directorios:

    hdinsight-eventhub-example/
        TemperatureMonitor/ - this contains the topology
            resources/
                log4j2.xml - set logging to minimal
                no-hbase.yaml - topology definition for local testing
                with-hbase.yaml - topology definition that uses HBase in a virutal network
            src/ - the Java bolts
            dev.properties - contains configuration values for your environment
        dashboard/nodejs/ - this is the node.js web dashboard
        SendEvents/ - utilities to send fake sensor data

> [!NOTE]
> Este documento no profundiza en los detalles sobre el código incluido en este ejemplo; sin embargo, el código completo está comentado.
> 
> 

Abra el archivo **hdinsight-eventhub-example/TemperatureMonitor/dev.properties** y agregue la información del centro de eventos a las siguientes líneas:

    eventhub.read.policy.name: storm
    eventhub.read.policy.key: KeyForTheStormPolicy
    eventhub.namespace: YourNamespace
    eventhub.name: sensordata

> [!NOTE]
> En este ejemplo se da por supuesto que usó **storm** como nombre de la directiva que tiene una notificación **Escuchar** y que el nombre de su centro de eventos es **sensordata**.
> 
> 

 Guarde el archivo después de agregar esta información.

## <a name="compile-and-test-locally"></a>Compilación y pruebas locales
Antes de las pruebas, debe iniciar el panel para ver el resultado de la topología y generar datos para almacenarlos en el Centro de eventos.

> [!IMPORTANT]
> El componente de HBase de esta topología no está activo cuando se prueba localmente, ya que no se puede acceder a la API de Java para el clúster de HBase desde fuera de la Red virtual de Azure que contiene los clústeres.
> 
> 

### <a name="start-the-web-application"></a>Inicio de la aplicación web
1. Abra un nuevo símbolo de sistema o terminal, cambie los directorios a **hdinsight-eventhub-example/dashboard**y, después, use el siguiente comando para instalar las dependencias necesarias para la aplicación web:
   
        npm install
2. Use el comando siguiente para iniciar la aplicación web:
   
        node server.js
   
    Debe ver un mensaje similar al siguiente:
   
        Server listening at port 3000
3. Abra un explorador web y escriba la dirección **http://localhost:3000/**. Debería ver una página similar a la siguiente:
   
    ![panel web](./media/hdinsight-storm-sensor-data-analysis/emptydashboard.png)
   
    Deje abierto este símbolo del sistema o terminal. Después de las pruebas, presione CTRL+C para detener el servidor web.

### <a name="start-generating-data"></a>Inicio de la generación de datos
> [!NOTE]
> Los pasos de esta sección usan Node.js para que se puedan utilizar en cualquier plataforma. Para obtener ejemplos de otros lenguajes, consulte el directorio **SendEvents** .
> 
> 

1. Abra un nuevo símbolo del sistema, shell o terminal, cambie de directorio a **hdinsight-eventhub-example/SendEvents/nodejs**y use el siguiente comando para instalar las dependencias necesarias para la aplicación:
   
        npm install
2. Abra el archivo **app.js** en un editor de texto y agregue la información del Centro de eventos que obtuvo anteriormente:
   
        // ServiceBus Namespace
        var namespace = 'YourNamespace';
        // Event Hub Name
        var hubname ='sensordata';
        // Shared access Policy name and key (from Event Hub configuration)
        var my_key_name = 'devices';
        var my_key = 'YourKey';
   
   > [!NOTE]
   > En este ejemplo se da por supuesto que ha usado **sensordata** como nombre del centro de eventos y **devices** como nombre de la directiva que tiene una notificación **Enviar**.
   > 
   > 
3. Use el siguiente comando para insertar entradas nuevas en el Centro de eventos:
   
        node app.js
   
    Debe ver varias líneas de salida que contienen los datos enviados al Centro de eventos. Tendrán un aspecto similar al siguiente:
   
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"0","Temperature":7}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"1","Temperature":39}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"2","Temperature":86}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"3","Temperature":29}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"4","Temperature":30}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"5","Temperature":5}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"6","Temperature":24}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"7","Temperature":40}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"8","Temperature":43}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"9","Temperature":84}

### <a name="start-the-topology"></a>Inicio de la topología
1. Abra un nuevo símbolo del sistema, shell o terminal, cambie de directorio a **hdinsight-eventhub-example/TemperatureMonitor**y use el siguiente comando para iniciar la topología:
   
        mvn compile exec:java -Dexec.args="--local -R /no-hbase.yaml --filter dev.properties"
   
    Si usa PowerShell, utilice en su lugar lo siguiente:
   
        mvn compile exec:java "-Dexec.args=--local -R /no-hbase.yaml --filter dev.properties"
   
   > [!NOTE]
   > Si está en un sistema Linux, Unix u OS X y tiene [instalado Storm en el entorno de desarrollo](http://storm.apache.org/releases/0.10.0/Setting-up-development-environment.html), puede usar en su lugar los siguientes comandos:
   > 
   > `mvn compile package`
   > `storm jar target/WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local -R /no-hbase.yaml`
   > 
   > 
   
    Esto inicia la topología que se define en el archivo **no-hbase.yaml** en modo local. Los valores contenidos en el archivo **dev.properties** proporcionan la información de conexión para Event Hubs. Una vez iniciada, la topología lee entradas del centro de eventos y las envía al panel que se ejecuta en la máquina local. Debe ver que aparecen líneas en el panel web, parecidas a las siguientes.
   
    ![panel con datos](./media/hdinsight-storm-sensor-data-analysis/datadashboard.png)
2. Mientras se ejecuta el panel, use el comando `node app.js` de los pasos anteriores para enviar datos nuevos a los Centros de eventos. Dado que los valores de temperatura se generan aleatoriamente, el gráfico debe actualizarse para mostrar cambios importantes en la temperatura.
   
   > [!NOTE]
   > Debe estar en el directorio **hdinsight-eventhub-example/SendEvents/Nodejs** cuando use el comando `node app.js`.
   > 
   > 
3. Después de comprobar que esto funciona, presione Ctrl +C para detener la topología. También puede presionar CTRL+C para detener el servidor web local.

## <a name="create-a-storm-and-hbase-cluster"></a>Creación de un clúster de Storm y HBase
Para ejecutar la topología en HDInsight y para habilitar el bolt de HBase, debe crear un nuevo clúster de Storm y un clúster de HBase. En los pasos descritos en esta sección se usa una [plantilla de Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md) para crear una nueva Red virtual de Azure y un clúster de Storm y HBase en la red virtual. La plantilla también crea una aplicación web de Azure e implementa una copia del panel en ella.

> [!NOTE]
> Se utiliza una red virtual para que la topología que se ejecuta en el clúster de Storm pueda comunicarse directamente con el clúster de HBase usando la API Java de HBase.
> 
> 

La plantilla de Resource Manager usada en este documento está ubicada en un contenedor de blobs público en **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-hbase-storm-cluster-in-vnet.json**.

1. Haga clic en el botón siguiente para iniciar sesión en Azure y abrir la plantilla de Resource Manager en Azure Portal.
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hbase-storm-cluster-in-vnet.json" target="_blank"><img src="./media/hdinsight-storm-sensor-data-analysis/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. En la hoja **Parámetros** , escriba lo siguiente:
   
    ![Parámetros de HDInsight](./media/hdinsight-storm-sensor-data-analysis/parameters.png)
   
   * **BASECLUSTERNAME**: este valor se utilizará como nombre base para los clústeres de Storm y de HBase. Por ejemplo, si escribe **hdi**, se creará un clúster de Storm denominado **storm-hdi** y un clúster de HBase denominado **hbase-hdi**.
   * **CLUSTERLOGINUSERNAME**: el nombre de usuario administrador para los clústeres de Storm y HBase.
   * **CLUSTERLOGINPASSWORD**: la contraseña de usuario administrador para los clústeres de Storm y HBase.
   * **SSHUSERNAME**: el usuario de SSH para crear los clústeres de Storm y HBase.
   * **SSHPASSWORD**: la contraseña del usuario SSH para los clústeres de Storm y HBase.
   * **Ubicación**: la región en la que se crearán los clústeres.
     
     Haga clic en **Aceptar** para guardar los parámetros.
3. Use la sección **Grupo de recursos** para crear un grupo de recursos o seleccione uno existente.
4. En el menú desplegable **Ubicación del grupo de recursos**, seleccione la misma ubicación que seleccionó para el parámetro **LOCATION**.
5. Seleccione **Términos legales** y, después, **Crear**.
6. Por último, active **Anclar al panel** y, después, seleccione **Crear**. Se tardará aproximadamente unos 20 minutos en crear los clústeres.

Una vez creados los recursos, se le redirigirá a una hoja del grupo de recursos que contiene los clústeres y el panel web.

![Hoja de grupo de recursos de la red virtual y clústeres](./media/hdinsight-storm-sensor-data-analysis/groupblade.png)

> [!IMPORTANT]
> Observe que los nombres de los clústeres de HDInsight son **storm-BASENAME** y **hbase-BASENAME**, donde BASENAME es el nombre proporcionado a la plantilla. Utilizará estos nombres en pasos posteriores al conectarse a los clústeres. Observe también que el nombre del sitio del panel es **basename-dashboard**. Lo utilizará después cuando vea el panel.
> 
> 

## <a name="configure-the-dashboard-bolt"></a>Configuración del bolt del panel
Para enviar datos al panel implementado como aplicación web, debe modificar la línea siguiente en el archivo **dev.properties** :

    dashboard.uri: http://localhost:3000

Cambie `http://localhost:3000` por `http://BASENAME-dashboard.azurewebsites.net` y guarde el archivo. Reemplace **BASENAME** por el nombre base proporcionado en el paso anterior. También puede utilizar el grupo de recursos que creó anteriormente para seleccionar el panel y ver la dirección URL.

## <a name="create-the-hbase-table"></a>Creación de la tabla de HBase
Para almacenar datos en HBase, primero hay que crear una tabla. Por lo general se crean previamente los recursos que necesita Storm para escribir, como intentar crear recursos desde dentro de una topología de Storm pueden dar lugar a varias copias distribuidas del código que intenta crear el mismo recurso. Cree los recursos fuera de la topología y solo use Storm para leer o escribir y para el análisis.

1. Use SSH para conectarse al clúster de HBase con el usuario y la contraseña de SSH suministrados a la plantilla durante la creación del clúster. Por ejemplo, si se conecta con el comando `ssh` , debe usar la sintaxis siguiente:
   
        ssh USERNAME@hbase-BASENAME-ssh.azurehdinsight.net
   
    En este comando, reemplace **USERNAME** por el nombre de usuario de SSH que proporcionó al crear el clúster y **BASENAME** por el nombre base proporcionado. Cuando se le solicite, escriba la contraseña del usuario de SSH.
2. En la sesión SSH, inicie el shell de HBase.
   
        hbase shell
   
    Una vez cargado el shell, verá un símbolo del sistema de `hbase(main):001:0>` .
3. En el shell de HBase, escriba el comando siguiente para crear una tabla en la que se almacenarán los datos de los sensores:
   
        create 'SensorData', 'cf'
4. Compruebe que se ha creado la tabla con el comando siguiente:
   
        scan 'SensorData'
   
    Esto devuelve información similar al siguiente ejemplo, que indica que hay 0 filas en la tabla.
   
        ROW                   COLUMN+CELL                                       0 row(s) in 0.1900 seconds
5. Escriba lo siguiente para salir del shell de HBase:
   
        exit

## <a name="configure-the-hbase-bolt"></a>Configuración del bolt de HBase
Para escribir en HBase desde el clúster de Storm, debe proporcionar el bolt de HBase con los detalles de configuración de su clúster de HBase. La manera más fácil de hacerlo es descargar el archivo **hbase-site.xml** del clúster e incluirlo en el proyecto. También debe quitar los comentarios de varias dependencias en el archivo **pom.xml** , que carga el componente storm-hbase y las dependencias necesarias.

> [!IMPORTANT]
> También debe descargar el archivo storm-hbase.jar proporcionado en el clúster 3.3 o 3.4 de Storm en HDInsight; esta versión está compilada para funcionar con HBase 1.1.x, que se utiliza para HBase en los clústeres 3.3 y 3.4 de HDInsight. Si utiliza un componente de storm-hbase procedente de otra parte, se puede compilar con una versión anterior de HBase.
> 
> 

### <a name="download-the-hbase-sitexml"></a>Descarga del archivo hbase-site.xml
Desde un símbolo del sistema, utilice SCP para descargar el archivo **hbase-site.xml** desde el clúster. En el ejemplo siguiente, reemplace **USERNAME** por el usuario de SSH que proporcionó al crear el clúster y **BASENAME** por el nombre base que proporcionó anteriormente. Cuando se le solicite, escriba la contraseña del usuario de SSH. Reemplace `/path/to/TemperatureMonitor/resources/hbase-site.xml` por la ruta de acceso a este archivo en el proyecto TemperatureMonitor.

    scp USERNAME@hbase-BASENAME-ssh.azurehdinsight.net:/etc/hbase/conf/hbase-site.xml /path/to/TemperatureMonitor/resources/hbase-site.xml

Esto descargará el archivo **hbase-site.xml** en la ruta especificada.

### <a name="download-and-install-the-storm-hbase-component"></a>Descarga e instalación del componente de storm-hbase
1. Desde un símbolo del sistema, utilice SCP para descargar el archivo **storm-hbase.jar** desde el clúster de Storm. En el ejemplo siguiente, reemplace **USERNAME** por el usuario de SSH que proporcionó al crear el clúster y **BASENAME** por el nombre base que proporcionó anteriormente. Cuando se le solicite, escriba la contraseña del usuario de SSH.
   
        scp USERNAME@storm-BASENAME-ssh.azurehdinsight.net:/usr/hdp/current/storm-client/contrib/storm-hbase/storm-hbase*.jar .
   
    Se descargará un archivo denominado `storm-hbase-####.jar`, donde #### es el número de versión de Storm para este clúster. Tome nota de este número, ya que lo utilizará más adelante.
2. Use el comando siguiente para instalar este componente en el repositorio de Maven local del entorno de desarrollo. Esto permite a Maven encontrar el paquete al compilar el proyecto. Reemplace **####** por el número de versión que se incluye en el nombre de archivo.
   
        mvn install:install-file -Dfile=storm-hbase-####.jar -DgroupId=org.apache.storm -DartifactId=storm-hbase -Dversion=#### -Dpackaging=jar
   
    Si usa PowerShell, utilice el comando siguiente:
   
        mvn install:install-file "-Dfile=storm-hbase-####.jar" "-DgroupId=org.apache.storm" "-DartifactId=storm-hbase" "-Dversion=####" "-Dpackaging=jar"

### <a name="enable-the-storm-hbase-component-in-the-project"></a>Habilitación del componente de storm-hbase en el proyecto
1. Abra el archivo **TemperatureMonitor/pom.xml** y elimine las líneas siguientes:
   
        <!-- uncomment this section to enable the hbase-bolt
        end comment for hbase-bolt section -->
   
   > [!IMPORTANT]
   > Elimine solo estas dos líneas; no elimine ninguna de las líneas entre ellas.
   > 
   > 
   
    Esto habilita varios componentes que son necesarios al comunicarse con HBase mediante el bolt de hbase.
2. Busque las líneas siguientes y reemplace **####** por el número de versión del archivo storm-hbase que descargó anteriormente.
   
        <dependency>
            <groupId>org.apache.storm</groupId>
            <artifactId>storm-hbase</artifactId>
            <version>####</version>
        </dependency>
   
   > [!IMPORTANT]
   > El número de versión debe coincidir con la versión utilizada al instalar el componente en el repositorio de Maven local, ya que Maven usa esta información para cargar el componente al compilar el proyecto.
   > 
   > 
3. Guarde el archivo **pom.xml** .

## <a name="build-package-and-deploy-the-solution-to-hdinsight"></a>Compilación, empaquetado e implementación de la solución en HDInsight
En el entorno de desarrollo, siga estos pasos para implementar la topología de Storm en el clúster de storm.

1. Desde el directorio **TemperatureMonitor** , use el comando siguiente para realizar una nueva compilación y crear un paquete JAR a partir del proyecto:
   
        mvn clean compile package
   
    Se creará un archivo denominado **TemperatureMonitor-1.0-SNAPSHOT.jar** in the **target** del proyecto.
2. Use scp para cargar el archivo **TemperatureMonitor-1.0-SNAPSHOT.jar** en el clúster de Storm. En el ejemplo siguiente, reemplace **USERNAME** por el usuario de SSH que proporcionó al crear el clúster y **BASENAME** por el nombre base que proporcionó anteriormente. Cuando se le solicite, escriba la contraseña del usuario de SSH.
   
        scp target\TemperatureMonitor-1.0-SNAPSHOT.jar USERNAME@storm-BASENAME-ssh.azurehdinsight.net:TemperatureMonitor-1.0-SNAPSHOT.jar
   
   > [!NOTE]
   > Puede tardar varios minutos en cargar el archivo, ya que tendrá varios megabytes.
   > 
   > 
   
    Use scp para cargar el archivo **dev.properties** , ya que contiene la información que se usa para conectarse a Event Hubs y al panel.
   
        scp dev.properties USERNAME@storm-BASENAME-ssh.azurehdinsight.net:dev.properties
3. Una vez cargados los archivos, conéctese al clúster mediante SSH.
   
        ssh USERNAME@storm-BASENAME-ssh.azurehdinsight.net
4. En la sesión SSH, use el siguiente comando para iniciar la topología.
   
        storm jar TemperatureMonitor-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /with-hbase.yaml --filter dev.properties
   
    Esto inicia la topología con la definición de topología del archivo **with-hbase.yaml** y los valores de configuración del archivo **dev.properties**.
5. Cuando se haya iniciado la topología, abra un explorador con el sitio web publicado en Azure y  use el comando `node app.js` para enviar datos a un Centro de eventos. Debería ver que el panel web se actualiza para mostrar la información.
   
    ![dashboard](./media/hdinsight-storm-sensor-data-analysis/datadashboard.png)

## <a name="view-hbase-data"></a>Visualización de datos de HBase
Una vez enviados los datos a la topología mediante `node app.js`, siga estos pasos para conectarse a HBase y comprobar que los datos se han escrito en la tabla que creó anteriormente.

1. Use SSH para conectarse al clúster de HBase.
   
        ssh USERNAME@hbase-BASENAME-ssh.azurehdinsight.net
2. En la sesión SSH, inicie el shell de HBase.
   
        hbase shell
   
    Una vez cargado el shell, verá un símbolo del sistema de `hbase(main):001:0>` .
3. Vea las filas de la tabla:
   
        scan 'SensorData'
   
    Esto debe devolver una información similar a la siguiente, que indica que hay 0 filas en la tabla.
   
        hbase(main):002:0> scan 'SensorData'
        ROW                             COLUMN+CELL
        \x00\x00\x00\x00               column=cf:temperature, timestamp=1467290788277, value=\x00\x00\x00\x04
        \x00\x00\x00\x00               column=cf:timestamp, timestamp=1467290788277, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x01               column=cf:temperature, timestamp=1467290788348, value=\x00\x00\x00M
        \x00\x00\x00\x01               column=cf:timestamp, timestamp=1467290788348, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x02               column=cf:temperature, timestamp=1467290788268, value=\x00\x00\x00R
        \x00\x00\x00\x02               column=cf:timestamp, timestamp=1467290788268, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x03               column=cf:temperature, timestamp=1467290788269, value=\x00\x00\x00#
        \x00\x00\x00\x03               column=cf:timestamp, timestamp=1467290788269, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x04               column=cf:temperature, timestamp=1467290788356, value=\x00\x00\x00>
        \x00\x00\x00\x04               column=cf:timestamp, timestamp=1467290788356, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x05               column=cf:temperature, timestamp=1467290788326, value=\x00\x00\x00\x0D
        \x00\x00\x00\x05               column=cf:timestamp, timestamp=1467290788326, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x06               column=cf:temperature, timestamp=1467290788253, value=\x00\x00\x009
        \x00\x00\x00\x06               column=cf:timestamp, timestamp=1467290788253, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x07               column=cf:temperature, timestamp=1467290788229, value=\x00\x00\x00\x12
        \x00\x00\x00\x07               column=cf:timestamp, timestamp=1467290788229, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x08               column=cf:temperature, timestamp=1467290788336, value=\x00\x00\x00\x16
        \x00\x00\x00\x08               column=cf:timestamp, timestamp=1467290788336, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x09               column=cf:temperature, timestamp=1467290788246, value=\x00\x00\x001
        \x00\x00\x00\x09               column=cf:timestamp, timestamp=1467290788246, value=2015-02-10T14:43.05.00320Z
        10 row(s) in 0.1800 seconds
   
   > [!NOTE]
   > Esta operación de análisis solo devolverá un máximo de 10 filas de la tabla.
   > 
   > 

## <a name="delete-your-clusters"></a>Eliminación de los clústeres
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Para eliminar los clústeres, el almacenamiento y la aplicación web al mismo tiempo, elimine el grupo de recursos que los contiene.

## <a name="next-steps"></a>Pasos siguientes
Ahora ha aprendido a usar Storm para leer los datos de Event Hubs, almacenarlos en HBase y visualizar la información en un panel externo mediante Socket.io y D3.js.

* Para más ejemplos de topologías de Storm con HDInsight, consulte:
  
  * [Topologías de ejemplo para Storm en HDInsight](hdinsight-storm-example-topology.md)
* Para obtener más información sobre Apache Storm, consulte el sitio de [Apache Storm](https://storm.incubator.apache.org/) .
* Para obtener más información sobre HBase con HDInsight, consulte [Información general de HBase de HDInsight](hdinsight-hbase-overview.md).
* Para obtener más información sobre Socket.io, consulte el sitio de [socket.io](http://socket.io/) .
* Para obtener más información sobre D3.js, consulte [D3.js: documentos controlados por datos](http://d3js.org/).
* Para obtener información sobre la creación de topologías en Java, consulte [Desarrollo de topologías de Java para Apache Storm en HDInsight](hdinsight-storm-develop-java-topology.md).
* Para obtener información sobre la creación de topologías en .NET, consulte [Desarrollo de topologías de C# para Apache Storm en HDInsight mediante Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md).

[azure-portal]: https://portal.azure.com



<!--HONumber=Jan17_HO3-->


