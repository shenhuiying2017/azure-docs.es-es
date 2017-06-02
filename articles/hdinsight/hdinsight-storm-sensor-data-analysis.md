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
ms.custom: hdinsightactive
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/21/2017
ms.author: larryfr
ms.translationtype: Human Translation
ms.sourcegitcommit: 8f987d079b8658d591994ce678f4a09239270181
ms.openlocfilehash: 25e0a25a4468acf001f1ce4e6d62ceef1b420955
ms.contentlocale: es-es
ms.lasthandoff: 05/18/2017


---
# <a name="analyze-sensor-data-with-apache-storm-event-hub-and-hbase-in-hdinsight-hadoop"></a>Análisis de datos de sensores con Apache Storm, Centro de eventos y HBase en HDInsight (Hadoop)

Aprenda a usar Apache Storm en HDInsight para procesar datos de sensores desde Centro de eventos de Azure. Los datos se almacenan entonces en Apache HBase en HDInsight y se visualizan mediante D3.js.

La plantilla de Azure Resource Manager utilizada en este documento muestra cómo crear varios recursos de Azure en un grupo de recursos. Esta plantilla crea una red virtual de Azure, dos clústeres de HDInsight (Storm y HBase) y una aplicación web de Azure. Se realiza automáticamente una implementación de node.js de un panel web en tiempo real en la aplicación web.

> [!NOTE]
> La información de este documento y un ejemplo de este documento requieren la versión 3.5 de HDInsight.
>
> Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las superiores. Consulte la información sobre la [retirada de HDInsight en Windows](hdinsight-component-versioning.md#hdi-version-33-nearing-retirement-date).

## <a name="prerequisites"></a>Requisitos previos

* Una suscripción de Azure. Vea [Obtener evaluación gratuita de Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
  
  > [!IMPORTANT]
  > No necesita un clúster existente de HDInsight. En los pasos de este documento se crean los siguientes recursos:
  > 
  > * Una red virtual de Azure
  > * Un clúster de Storm en HDInsight (dos nodos de trabajo basados en Linux)
  > * Un clúster de HBase en HDInsight (dos nodos de trabajo basados en Linux)
  > * Una aplicación web de Azure que hospeda el panel web

* [Node.js](http://nodejs.org/): se utiliza para obtener una vista previa del panel web localmente en su entorno de desarrollo.
* [Java y JDK 1.7](http://www.oracle.com/technetwork/java/javase/downloads/index.html): se utiliza para desarrollar la topología de Storm.
* [Maven](http://maven.apache.org/what-is-maven.html): se usa para crear y compilar el proyecto.
* [Git](http://git-scm.com/): se utiliza para descargar el proyecto de GitHub.
* Un cliente **SSH** : se usa para conectarse a los clústeres de HDInsight basados en Linux. Para más información, consulte [Uso SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).
    
    > [!NOTE]
    > También debe tener acceso al comando `scp`, que se utiliza para copiar archivos entre el entorno de desarrollo local y el clúster de HDInsight mediante SSH.


## <a name="architecture"></a>Arquitectura

![diagrama de arquitectura](./media/hdinsight-storm-sensor-data-analysis/devicesarchitecture.png)

Este ejemplo consta de los siguientes componentes:

* **Centros de eventos de Azure**: contiene datos que se recopilan de los sensores.
* **Storm en HDInsight**: ofrece procesamiento en tiempo real de datos desde el Centro de eventos.
* **HBase en HDInsight**: proporciona un almacén de datos NoSQL persistente para los datos después de haberse procesado en Storm.
* **Servicio de Red virtual de Azure**: habilita las comunicaciones seguras entre el Storm en HDInsight y HBase en clústeres de HDInsight.
  
  > [!NOTE]
  > Se requiere una red virtual cuando se usa la API de cliente Java de HBase. No está expuesta a través de la puerta de enlace pública para los clústeres de HBase. La instalación de clústeres de HBase y Storm en la misma red virtual permite que el clúster de Storm (o cualquier otro sistema de la red virtual) acceda directamente a HBase mediante la API de cliente.

* **Sitio web del panel**: un panel de ejemplo que muestra datos en tiempo real.
  
  * El sitio web se implementa en Node.js, de forma que puede ejecutarse en cualquier sistema operativo cliente para realizar pruebas o se puede implementar en sitios web de Azure.
  * [Socket.io](http://socket.io/) se usa para la comunicación en tiempo real entre la topología Storm y el sitio web.
    
    > [!NOTE]
    > Usar Socket.io para comunicaciones es un detalle de implementación. Puede usar cualquier marco de comunicaciones, como SignalR o WebSockets sin procesar.

  * [D3.js](http://d3js.org/) se usa para representar los datos que se envían al sitio web.

> [!IMPORTANT]
> Como no hay ningún método compatible para crear un clúster de HDInsight para Storm y HBase, se requieren dos clústeres

La topología lee datos del Centro de eventos mediante la clase [org.apache.storm.eventhubs.spout.EventHubSpout](http://storm.apache.org/releases/0.10.1/javadocs/org/apache/storm/eventhubs/spout/class-use/EventHubSpout.html) y escribe los datos en HBase usando la clase [org.apache.storm.hbase.bolt.HBaseBolt](https://storm.apache.org/javadoc/apidocs/org/apache/storm/hbase/bolt/class-use/HBaseBolt.html). La comunicación con el sitio web se logra mediante [client.java socket.io](https://github.com/nkzawa/socket.io-client.java).

En el diagrama siguiente se explica el diseño de la topología:

![diagrama de topología](./media/hdinsight-storm-sensor-data-analysis/sensoranalysis.png)

> [!NOTE]
> Se trata de una vista muy simplificada de la topología. En tiempo de ejecución, se crea una instancia de cada componente por cada partición del Centro de eventos que se está leyendo. Estas instancias se distribuyen entre los nodos del clúster y los datos se enrutan entre ellos como sigue:
> 
> * Se equilibran las cargas de los datos del spout al analizador.
> * Los datos del analizador al panel y HBase se agrupan por identificador de dispositivo, para que los mensajes del mismo dispositivo fluyan siempre al mismo componente.

### <a name="topology-components"></a>Componentes de la topología

* **EventHub Spout**: el spout se proporciona como parte de Apache Storm versión 0.10.0 y versiones posteriores.
  
  > [!NOTE]
  > El spout de Event Hubs utilizado en este ejemplo requiere un clúster de Storm en HDInsight versión 3.3 o 3.4. Para más información sobre cómo usar Centros de eventos de Azure de Storm en HDInsight, consulte [Procesamiento de eventos desde Centros de eventos de Azure con Storm en HDInsight (C#)](hdinsight-storm-develop-java-event-hub-topology.md).

* **ParserBolt.java**: los datos que emite el spout son JSON sin procesar y, en ocasiones, se emite más de un evento a la vez. Este bolt muestra cómo leer los datos que emite el spout, y los emite a una secuencia nueva como una tupla que contiene varios campos.
* **DashboardBolt.java**: este componente muestra cómo usar la biblioteca de cliente de Socket.io para Java para enviar datos en tiempo real al panel web.
* **Temperature.java**: define la topología y carga los datos de configuración desde el archivo **Config.properties**.

## <a name="prepare-your-environment"></a>Preparación del entorno

Antes de usar este ejemplo, debe crear un Centro de eventos de Azure, desde el que lee la topología de Storm.

### <a name="configure-event-hub"></a>Configuración del centro de eventos

Centro de eventos es el origen de datos para este ejemplo. Use los pasos siguientes para crear un centro de eventos.

1. En [Azure Portal](https://portal.azure.com), seleccione **+ Nuevo** -> **Internet de las cosas** -> **Event Hubs**.
2. En la hoja **Crear espacio de nombres** , realice las siguientes tareas:
   
   1. Escriba un valor en **Nombre** para el espacio de nombres.
   2. Seleccione un plan de tarifa. **Básico** es suficiente para este ejemplo.
   3. Seleccione la suscripción de Azure que va a usar en **Suscripción** .
   4. Seleccione un grupo de recursos existente o cree uno.
   5. Seleccione un valor en **Ubicación** para el centro de eventos.
   6. Seleccione **Anclar al panel** y luego haga clic en **Crear**.

3. Cuando se completa el proceso de creación, se muestra la hoja Event Hubs para el espacio de nombres. Desde ahí, seleccione **+ Agregar Centro de eventos**. En la hoja **Crear centro de eventos**, escriba el nombre **sensordata** y seleccione **Crear**. Deje los demás campos con los valores predeterminados.
4. En la hoja Event Hubs para el espacio de nombres, seleccione **Event Hubs**. Seleccione la entrada **sensordata** .
5. En la hoja para el centro de eventos sensordata, seleccione **Directivas de acceso compartido**. Use el vínculo **+ Agregar** para agregar las siguientes directivas:

    | Nombre de la directiva | Notificaciones |
    | ----- | ----- |
    | devices | Enviar |
    | storm | Escuchar |

1. Seleccione ambas directivas y tome nota del valor en **CLAVE PRINCIPAL** . Necesitará el valor de ambas directivas en pasos posteriores.

## <a name="download-and-configure-the-project"></a>Descarga y configuración del proyecto

Use lo siguiente para descargar el proyecto de GitHub.

    git clone https://github.com/Blackmist/hdinsight-eventhub-example

Cuando se haya completado el comando, tendrá la siguiente estructura de directorios:

    hdinsight-eventhub-example/
        TemperatureMonitor/ - this contains the topology
            resources/
                log4j2.xml - set logging to minimal.
                hbase-site.xml - connection information for the HBase cluster.
                Config.properties - configuration information for the topology. How to read from Event Hub and the URI to the dashboard.
            src/ - the Java bolts and topology definition.
        dashboard/nodejs/ - this is the node.js web dashboard.
        SendEvents/ - utilities to send fake sensor data.

> [!NOTE]
> Este documento no profundiza en los detalles sobre el código incluido en esta muestra. Sin embargo, se comenta el código completo.

Abra el archivo **hdinsight-eventhub-example/TemperatureMonitor/resources/Config.properties** y agregue la información del centro de eventos a las siguientes líneas:

    eventhubspout.username = <shared access policy name that can read from Event Hub>
    eventhubspout.password = <shared access policy key>
    eventhubspout.namespace = <namespace of your Event Hub
    eventhubspout.entitypath = <name of your event hub>
    eventhubspout.partitions.count = 2

Guarde el archivo después de agregar esta información.

## <a name="compile-and-test-locally"></a>Compilación y pruebas locales

Antes de las pruebas, debe iniciar el panel para ver el resultado de la topología y generar datos para almacenarlos en el Centro de eventos.

> [!IMPORTANT]
> El componente HBase de esta topología no está activo cuando se prueba localmente. Esto se debe a que no se puede tener acceso a la API de Java para el clúster de HBase desde fuera de la red virtual de Azure que contiene los clústeres.

### <a name="start-the-web-application"></a>Inicio de la aplicación web

1. Abra un nuevo símbolo del sistema o terminal y cambie los directorios a **hdinsight-eventhub-example/dashboard**. Use el siguiente comando para instalar las dependencias necesarias para la aplicación web:
   
        npm install

2. Use el comando siguiente para iniciar la aplicación web:
   
        node server.js
   
    Verá un mensaje similar al texto siguiente:
   
        Server listening at port 3000

3. Abra un explorador web y escriba la dirección **http://localhost:3000/**. Debería ver una página similar a la siguiente imagen:
   
    ![panel web](./media/hdinsight-storm-sensor-data-analysis/emptydashboard.png)
   
    Deje abierto este símbolo del sistema o terminal. Después de las pruebas, presione CTRL+C para detener el servidor web.

### <a name="start-generating-data"></a>Inicio de la generación de datos

> [!NOTE]
> Los pasos de esta sección usan Node.js para que se puedan utilizar en cualquier plataforma. Para obtener ejemplos de otros lenguajes, consulte el directorio **SendEvents** .

1. Abra un nuevo símbolo del sistema, shell o terminal y cambie los directorios a **hdinsight-eventhub-example/SendEvents/nodejs**. Use el comando siguiente para instalar las dependencias que necesita la aplicación:
   
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

3. Use el siguiente comando para insertar entradas nuevas en el Centro de eventos:
   
        node app.js
   
    Verá varias líneas de salida que contienen los datos enviados al Centro de eventos:
   
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
   
        mvn compile exec:java
   
    Este comando inicia la topología en modo local. Los valores contenidos en el archivo **Config.properties** proporcionan la información de conexión para Event Hubs y el sitio web del panel local. Una vez iniciada, la topología lee entradas del centro de eventos y las envía al panel que se ejecuta en la máquina local. Debe ver que aparecen líneas en el panel web, parecidas a las de la imagen siguiente:
   
    ![panel con datos](./media/hdinsight-storm-sensor-data-analysis/datadashboard.png)

2. Mientras se ejecuta el panel, use el comando `node app.js` de los pasos anteriores para enviar datos nuevos a los Centros de eventos. Dado que los valores de temperatura se generan aleatoriamente, el gráfico debe actualizarse para mostrar cambios importantes en la temperatura.
   
   > [!NOTE]
   > Debe estar en el directorio **hdinsight-eventhub-example/SendEvents/Nodejs** cuando use el comando `node app.js`.

3. Después de comprobar que el panel se actualiza, presione Ctrl+C para detener la topología. También puede presionar CTRL+C para detener el servidor web local.

## <a name="create-a-storm-and-hbase-cluster"></a>Creación de un clúster de Storm y HBase

En los pasos descritos en esta sección se usa una [plantilla de Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md) para crear una red virtual de Azure y un clúster de Storm y HBase en la red virtual. La plantilla también crea una aplicación web de Azure e implementa una copia del panel en ella.

> [!NOTE]
> Se utiliza una red virtual para que la topología que se ejecuta en el clúster de Storm pueda comunicarse directamente con el clúster de HBase usando la API Java de HBase.

La plantilla de Resource Manager usada en este documento está ubicada en un contenedor de blobs público en **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-hbase-storm-cluster-in-vnet.json**.

1. Haga clic en el botón siguiente para iniciar sesión en Azure y abrir la plantilla de Resource Manager en Azure Portal.
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hbase-storm-cluster-in-vnet-3.5.json" target="_blank"><img src="./media/hdinsight-storm-sensor-data-analysis/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. En la hoja **Implementación personalizada**, escriba los valores siguientes:
   
    ![Parámetros de HDInsight](./media/hdinsight-storm-sensor-data-analysis/parameters.png)
   
   * **Nombre base del clúster**: este valor se utiliza como nombre base para los clústeres Storm y HBase. Por ejemplo, si escribe **abc**, se creará un clúster de Storm denominado **storm-abc** y un clúster de HBase denominado **hbase-abc**.
   * **Nombre de usuario de inicio de sesión del clúster**: nombre de usuario del administrador de los clústeres Storm y HBase.
   * **Contraseña de inicio de sesión de clúster**: contraseña del usuario administrador para los clústeres Storm y HBase.
   * **Nombre de usuario SSH**: usuario de SSH para crear los clústeres de Storm y HBase.
   * **Contraseña SSH**: contraseña del usuario SSH para los clústeres de Storm y HBase.
   * **Ubicación**: región en la que se crearán los clústeres.
     
     Haga clic en **Aceptar** para guardar los parámetros.

3. Use la sección **Datos básicos** para crear un grupo de recursos o seleccionar uno existente.
4. En el menú desplegable **Ubicación del grupo de recursos**, seleccione la misma ubicación que seleccionó para el parámetro **Ubicación** de la sección **Configuración**.
5. Consulte los términos y condiciones y seleccione **Acepto los términos y condiciones indicados anteriormente**.
6. Por último, active **Anclar al panel** y seleccione **Adquirir**. Se tarda aproximadamente 20 minutos en crear los clústeres.

Una vez creados los recursos, se le redirigirá a una hoja del grupo de recursos que contiene los clústeres y el panel web.

![Hoja de grupo de recursos de la red virtual y clústeres](./media/hdinsight-storm-sensor-data-analysis/groupblade.png)

> [!IMPORTANT]
> Observe que los nombres de los clústeres de HDInsight son **storm-BASENAME** y **hbase-BASENAME**, donde BASENAME es el nombre proporcionado a la plantilla. Estos nombres se utilizarán más adelante al establecer la conexión con los clústeres. Observe también que el nombre del sitio del panel es **basename-dashboard**. Este valor se usa más adelante en este documento.

## <a name="configure-the-dashboard-bolt"></a>Configuración del bolt del panel

Para enviar datos al panel implementado como aplicación web, debe modificar la línea siguiente en el archivo **Config.properties**:

    dashboard.uri: http://localhost:3000

Cambie `http://localhost:3000` por `http://BASENAME-dashboard.azurewebsites.net` y guarde el archivo. Reemplace **BASENAME** por el nombre base proporcionado en el paso anterior. También puede utilizar el grupo de recursos que creó anteriormente para seleccionar el panel y ver la dirección URL.

## <a name="create-the-hbase-table"></a>Creación de la tabla de HBase

Para almacenar datos en HBase, primero hay que crear una tabla. Se crean previamente los recursos en los que Storm necesita escribir, ya que crear recursos desde dentro de una topología de Storm puede dar lugar a que varias instancias intenten crear el mismo recurso. Cree los recursos fuera de la topología y use Storm para leer o escribir y para el análisis.

1. Use SSH para conectarse al clúster de HBase con el usuario y la contraseña de SSH suministrados a la plantilla durante la creación del clúster. Por ejemplo, si se conecta con el comando `ssh` , debe usar la sintaxis siguiente:
   
        ssh USERNAME@hbase-BASENAME-ssh.azurehdinsight.net
   
    En este comando, reemplace **USERNAME** por el nombre de usuario de SSH que proporcionó al crear el clúster y **BASENAME** por el nombre base proporcionado. Cuando se le solicite, escriba la contraseña del usuario de SSH.

2. En la sesión SSH, inicie el shell de HBase.
   
        hbase shell
   
    Una vez cargado el shell, verá un símbolo del sistema de `hbase(main):001:0>`.

3. En el shell de HBase, escriba el comando siguiente para crear una tabla en la que se almacenarán los datos de los sensores:
   
        create 'SensorData', 'cf'

4. Compruebe que se ha creado la tabla con el comando siguiente:
   
        scan 'SensorData'
   
    Esto devuelve información similar al siguiente ejemplo, que indica que hay 0 filas en la tabla.
   
        ROW                   COLUMN+CELL                                       0 row(s) in 0.1900 seconds

5. Escriba `exit` para salir del shell de HBase:

## <a name="configure-the-hbase-bolt"></a>Configuración del bolt de HBase

Para escribir en HBase desde el clúster de Storm, debe proporcionar el bolt de HBase con los detalles de configuración de su clúster de HBase. Este ejemplo utiliza el archivo **hbase-site.xml** del clúster de HBase.

### <a name="download-the-hbase-sitexml"></a>Descarga del archivo hbase-site.xml

Desde un símbolo del sistema, utilice SCP para descargar el archivo **hbase-site.xml** desde el clúster. En el ejemplo siguiente, reemplace **USERNAME** por el usuario de SSH que proporcionó al crear el clúster y **BASENAME** por el nombre base que proporcionó anteriormente. Cuando se le solicite, escriba la contraseña del usuario de SSH. Reemplace `/path/to/TemperatureMonitor/resources/hbase-site.xml` por la ruta de acceso a este archivo en el proyecto TemperatureMonitor.

    scp USERNAME@hbase-BASENAME-ssh.azurehdinsight.net:/etc/hbase/conf/hbase-site.xml /path/to/TemperatureMonitor/resources/hbase-site.xml

Este comando descarga el archivo **hbase-site.xml** en la ruta especificada.

### <a name="enable-the-hbase-bolt"></a>Habilitación del bolt de HBase

Para habilitar el componente bolt de HBase, abra el archivo **TemperatureMonitor/src/main/java/com/microsoft/examples/Temperature.java** y quite la marca de comentario de las líneas siguientes:

    // topologyBuilder.setBolt("HBase", new HBaseBolt("SensorData", mapper).withConfigKey("hbase.conf"), spoutConfig.getPartitionCount())
    //  .fieldsGrouping("Parser", "hbasestream", new Fields("deviceid")).setNumTasks(spoutConfig.getPartitionCount());

Después de quitar la marca de comentario de las líneas, guarde el archivo.

## <a name="build-package-and-deploy-the-solution-to-hdinsight"></a>Compilación, empaquetado e implementación de la solución en HDInsight

En el entorno de desarrollo, siga estos pasos para implementar la topología de Storm en el clúster de storm.

1. Desde el directorio **TemperatureMonitor** , use el comando siguiente para realizar una nueva compilación y crear un paquete JAR a partir del proyecto:
   
        mvn clean compile package
   
    Este comando crea un archivo denominado **TemperatureMonitor-1.0-SNAPSHOT.jar** en el directorio **target** del proyecto.

2. Use scp para cargar el archivo **TemperatureMonitor-1.0-SNAPSHOT.jar** en el clúster de Storm. En el ejemplo siguiente, reemplace **USERNAME** por el usuario de SSH que proporcionó al crear el clúster y **BASENAME** por el nombre base que proporcionó anteriormente. Cuando se le solicite, escriba la contraseña del usuario de SSH.
   
        scp target/TemperatureMonitor-1.0-SNAPSHOT.jar USERNAME@storm-BASENAME-ssh.azurehdinsight.net:TemperatureMonitor-1.0-SNAPSHOT.jar

   > [!NOTE]
   > El archivo puede tardar varios minutos en cargarse.

3. Una vez cargado el archivo, conéctese al clúster mediante SSH.
   
        ssh USERNAME@storm-BASENAME-ssh.azurehdinsight.net

4. Para comenzar la topología, use el comando siguiente desde la sesión SSH:
   
        storm jar TemperatureMonitor-1.0-SNAPSHOT.jar com.microsoft.examples.Temperature temperature

5. Cuando se haya iniciado la topología, abra un explorador con el sitio web publicado en Azure y  use el comando `node app.js` para enviar datos a un Centro de eventos. Debería ver que el panel web se actualiza para mostrar la información.
   
    ![dashboard](./media/hdinsight-storm-sensor-data-analysis/datadashboard.png)

## <a name="view-hbase-data"></a>Visualización de datos de HBase

Utilice los pasos siguientes para conectarse a HBase y compruebe que se hayan escrito los datos en la tabla:

1. Use SSH para conectarse al clúster de HBase.
   
        ssh USERNAME@hbase-BASENAME-ssh.azurehdinsight.net

2. En la sesión SSH, inicie el shell de HBase.
   
        hbase shell
   
    Una vez cargado el shell, verá un símbolo del sistema de `hbase(main):001:0>`.

3. Vea las filas de la tabla:
   
        scan 'SensorData'
   
    Este comando devuelve información similar al siguiente texto, que indica que hay datos en la tabla.
   
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
   > Esta operación de detección solo devuelve un máximo de 10 filas de la tabla.

## <a name="delete-your-clusters"></a>Eliminación de los clústeres
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Para eliminar los clústeres, el almacenamiento y la aplicación web al mismo tiempo, elimine el grupo de recursos que los contiene.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más ejemplos de topologías de Storm con HDInsight, vea [Topologías y componentes de ejemplo de Storm para Apache Storm en HDInsight](hdinsight-storm-example-topology.md).

Para obtener más información sobre Apache Storm, consulte el sitio de [Apache Storm](https://storm.incubator.apache.org/) .

Para obtener más información sobre HBase con HDInsight, consulte [Información general de HBase de HDInsight](hdinsight-hbase-overview.md).

Para obtener más información sobre Socket.io, consulte el sitio de [socket.io](http://socket.io/) .

Para obtener más información sobre D3.js, consulte [D3.js: documentos controlados por datos](http://d3js.org/).

Para obtener información sobre la creación de topologías en Java, consulte [Desarrollo de topologías de Java para Apache Storm en HDInsight](hdinsight-storm-develop-java-topology.md).

Para obtener información sobre la creación de topologías en .NET, consulte [Desarrollo de topologías de C# para Apache Storm en HDInsight mediante Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md).

[azure-portal]: https://portal.azure.com

