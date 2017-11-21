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
ms.date: 10/19/2017
ms.author: larryfr
ms.openlocfilehash: 87c2aece68c5de06d683abf971b6c7ccf7f67a54
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2017
---
# <a name="analyze-sensor-data-with-apache-storm-event-hub-and-hbase-in-hdinsight-hadoop"></a>Análisis de datos de sensores con Apache Storm, Centro de eventos y HBase en HDInsight (Hadoop)

Aprenda a usar Apache Storm en HDInsight para procesar datos de sensores desde Centro de eventos de Azure. Los datos se almacenan entonces en Apache HBase en HDInsight y se visualizan mediante D3.js.

La plantilla de Azure Resource Manager utilizada en este documento muestra cómo crear varios recursos de Azure en un grupo de recursos. La plantilla crea instancia de Azure Virtual Network, dos clústeres de HDInsight (Storm y HBase) y una instancia de Azure Web Apps. Se realiza automáticamente una implementación de node.js de un panel web en tiempo real en la aplicación web.

> [!NOTE]
> La información y el ejemplo de este documento requieren la versión 3.6 de HDInsight.
>
> Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las superiores. Consulte la información sobre la [retirada de HDInsight en Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="architecture"></a>Arquitectura

![diagrama de arquitectura](./media/apache-storm-sensor-data-analysis/devicesarchitecture.png)

Este ejemplo consta de los siguientes componentes:

* **Azure Event Hubs**: contiene datos que se recopilan de los sensores.
* **Storm en HDInsight**: ofrece procesamiento en tiempo real de datos desde el Centro de eventos.
* **HBase en HDInsight**: proporciona un almacén de datos NoSQL persistente para los datos después de haberse procesado en Storm.
* **Servicio Azure Virtual Network**: habilita las comunicaciones seguras entre el Storm en HDInsight y HBase en clústeres de HDInsight.
  
  > [!NOTE]
  > Se requiere una red virtual cuando se usa la API de cliente Java de HBase. No está expuesta a través de la puerta de enlace pública para los clústeres de HBase. La instalación de clústeres de HBase y Storm en la misma red virtual permite que el clúster de Storm (o cualquier otro sistema de la red virtual) acceda directamente a HBase mediante la API de cliente.

* **Sitio web del panel**: un panel de ejemplo que muestra datos en tiempo real.
  
  * El sitio web se implementa en Node.js.
  * [Socket.io](http://socket.io/) se usa para la comunicación en tiempo real entre la topología Storm y el sitio web.
    
    > [!NOTE]
    > Usar Socket.io para comunicaciones es un detalle de implementación. Puede usar cualquier marco de comunicaciones, como SignalR o WebSockets sin procesar.

  * [D3.js](http://d3js.org/) se usa para representar los datos que se envían al sitio web.

> [!IMPORTANT]
> Como no hay ningún método compatible para crear un clúster de HDInsight para Storm y HBase, se requieren dos clústeres

La topología lee datos desde la instancia de Event Hubs mediante la clase `org.apache.storm.eventhubs.spout.EventHubSpout` y escribe los datos en HBase con la clase `org.apache.storm.hbase.bolt.HBaseBolt`. La comunicación con el sitio web se logra mediante [client.java socket.io](https://github.com/nkzawa/socket.io-client.java).

En el diagrama siguiente se explica el diseño de la topología:

![diagrama de topología](./media/apache-storm-sensor-data-analysis/sensoranalysis.png)

> [!NOTE]
> Este diagrama es una vista simplificada de la topología. Por cada partición del Centro de eventos se crea una instancia de cada componente. Estas instancias se distribuyen entre los nodos del clúster y los datos se enrutan entre ellos como sigue:
> 
> * Se equilibran las cargas de los datos del spout al analizador.
> * Los datos del analizador al panel y HBase se agrupan por identificador de dispositivo, para que los mensajes del mismo dispositivo fluyan siempre al mismo componente.

### <a name="topology-components"></a>Componentes de la topología

* **Event Hubs Spout**: el spout se proporciona como parte de Apache Storm versión 0.10.0 y posteriores.
  
  > [!NOTE]
  > El spout de Event Hubs utilizado en este ejemplo requiere un clúster de Storm en HDInsight versión 3.5 o 3.6.

* **ParserBolt.java**: los datos que emite el spout son JSON sin procesar y, en ocasiones, se emite más de un evento a la vez. Este bolt lee los datos que emite el spout y analiza el mensaje JSON. A continuación, el bolt envía los datos como tupla con varios campos.
* **DashboardBolt.java**: este componente muestra cómo usar la biblioteca de cliente de Socket.io para Java para enviar datos en tiempo real al panel web.
* **no hbase.yaml**: la definición de la topología utilizada con la ejecución en modo local. No usa componentes de HBase.
* **with-hbase.yaml**: la definición de la topología utilizada con la ejecución de la topología en el clúster. Usa componentes de HBase.
* **dev.properties**: la información de configuración del spout de Event Hubs, el bolt de HBase y los componentes del panel.

## <a name="prepare-your-environment"></a>Preparación del entorno

Antes de utilizar este ejemplo, debe preparar el entorno de desarrollo. También debe crear una instancia de Azure Event Hubs para que se use en este ejemplo.

Necesita los siguientes elementos instalados en el entorno de desarrollo:

* [Node.js](http://nodejs.org/): se utiliza para obtener una vista previa del panel web en el entorno de desarrollo local.
* [Java y JDK 1.7](http://www.oracle.com/technetwork/java/javase/downloads/index.html): se utiliza para desarrollar la topología de Storm.
* [Maven](http://maven.apache.org/what-is-maven.html): se usa para crear y compilar el proyecto.
* [Git](http://git-scm.com/): se utiliza para descargar el proyecto de GitHub.
* Un cliente **SSH** : se usa para conectarse a los clústeres de HDInsight basados en Linux. Para más información, consulte [Uso SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

Para crear un centro de eventos, siga los pasos que se indican en el documento [Creación de un centro de eventos](../../event-hubs/event-hubs-create.md).

> [!IMPORTANT]
> Guarde el nombre, el espacio de nombres y la clave del centro de eventos para RootManageSharedAccessKey. Esta información se usará para configurar la topología de Storm.

No necesita un clúster de HDInsight. Los pasos descritos en este documento proporcionan una plantilla de Azure Resource Manager que crea los recursos que este ejemplo necesita. Esta plantilla crea los siguientes recursos:
 
* Una instancia de Azure Virtual Network
* Un clúster de Storm en HDInsight (dos nodos de trabajo basados en Linux)
* Un clúster de HBase en HDInsight (dos nodos de trabajo basados en Linux)
* Una aplicación web de Azure que hospeda el panel web

## <a name="download-and-configure-the-project"></a>Descarga y configuración del proyecto

Use lo siguiente para descargar el proyecto de GitHub.

    git clone https://github.com/Blackmist/hdinsight-eventhub-example

Cuando se haya completado el comando, tendrá la siguiente estructura de directorios:

    hdinsight-eventhub-example/
        TemperatureMonitor/ - this contains the topology
            resources/
                log4j2.xml - set logging to minimal.
                no-hbase.yaml - topology definition without hbase components.
                with-hbase.yaml - topology definition with hbase components.
            src/main/java/com/microsoft/examples/bolts/
                ParserBolt.java - parses JSON data into tuples
                DashboardBolt.java - sends data over Socket.IO to the web dashboard.
        dashboard/nodejs/ - this is the node.js web dashboard.
        SendEvents/ - utilities to send fake sensor data.

> [!NOTE]
> Este documento no profundiza en los detalles sobre el código incluido en este ejemplo. Sin embargo, se comenta el código completo.

Para configurar el proyecto para leer desde Event Hubs, abra el archivo `hdinsight-eventhub-example/TemperatureMonitor/dev.properties` y agregue la información de la instancia de Event Hubs a las líneas siguientes:

```bash
eventhub.policy.key: the_key_for_RootManageSharedAccessKey
eventhub.namespace: your_namespace_here
eventhub.name: your_event_hub_name
eventhub.partitions: 2
```

## <a name="compile-and-test-locally"></a>Compilación y pruebas locales

> [!IMPORTANT]
> Para el uso local de la topología se necesita un entorno de desarrollo de Storm en funcionamiento. Para más información, consulte [Setting up a development environment](http://storm.apache.org/releases/1.1.0/Setting-up-development-environment.html) (Configuración de un entorno de desarrollo) en Apache.org.

Antes de las pruebas, debe iniciar el panel para ver el resultado de la topología y generar datos para almacenarlos en el Centro de eventos.

> [!IMPORTANT]
> El componente HBase de esta topología no está activo cuando se prueba localmente. No se puede acceder a la API de Java para el clúster de HBase desde fuera de Azure Virtual Network con los clústeres.

### <a name="start-the-web-application"></a>Inicio de la aplicación web

1. Abra un símbolo del sistema y cambie los directorios a `hdinsight-eventhub-example/dashboard`. Use el siguiente comando para instalar las dependencias necesarias para la aplicación web:
   
    ```bash
    npm install
    ```

2. Use el comando siguiente para iniciar la aplicación web:
   
    ```bash
    node server.js
    ```
   
    Verá un mensaje similar al texto siguiente:
   
        Server listening at port 3000

3. Abra un explorador web y escriba `http://localhost:3000/` como dirección. Aparece una página similar a la siguiente imagen:
   
    ![panel web](./media/apache-storm-sensor-data-analysis/emptydashboard.png)
   
    Deje abierto este símbolo del sistema. Después de las pruebas, presione CTRL+C para detener el servidor web.

### <a name="generate-data"></a>Generación de datos

> [!NOTE]
> Los pasos de esta sección usan Node.js para que se puedan utilizar en cualquier plataforma. Para ejemplos de otros lenguajes, consulte el directorio `SendEvents`.

1. Abra un nuevo símbolo del sistema, shell o terminal y cambie los directorios a `hdinsight-eventhub-example/SendEvents/nodejs`. Use el comando siguiente para instalar las dependencias que necesita la aplicación:

    ```bash
    npm install
    ```

2. Abra el archivo `app.js` en un editor de texto y agregue la información de la instancia de Event Hubs que obtuvo anteriormente:
   
    ```javascript
    // ServiceBus Namespace
    var namespace = 'Your-eventhub-namespace';
    // Event Hub Name
    var hubname ='Your-eventhub-name';
    // Shared access Policy name and key (from Event Hub configuration)
    var my_key_name = 'RootManageSharedAccessKey';
    var my_key = 'Your-Key';
    ```
   
   > [!NOTE]
   > En este ejemplo se da por supuesto que usa `RootManageSharedAccessKey` para acceder al centro de eventos.

3. Use el siguiente comando para insertar entradas nuevas en el Centro de eventos:
   
    ```bash
    node app.js
    ```
   
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

### <a name="build-and-start-the-topology"></a>Compilación e inicio de la topología

1. Abra un símbolo del sistema nuevo y cambie los directorios a `hdinsight-eventhub-example/TemperatureMonitor`. Para compilar y empaquetar la topología, use el siguiente comando: 

    ```bash
    mvn clean package
    ```

2. Para iniciar la topología en modo local, use el comando siguiente:

    ```bash
    storm jar target/TemperatureMonitor-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local --filter dev.properties resources/no-hbase.yaml
    ```

    * `--local` inicia la topología en modo local.
    * `--filter`usa el archivo `dev.properties` para rellenar los parámetros de la definición de topología.
    * `resources/no-hbase.yaml` usa la definición de la topología `no-hbase.yaml`.
 
   Una vez iniciada, la topología lee entradas del centro de eventos y las envía al panel que se ejecuta en la máquina local. Debe ver que aparecen líneas en el panel web, parecidas a las de la imagen siguiente:
   
    ![panel con datos](./media/apache-storm-sensor-data-analysis/datadashboard.png)

2. Mientras se ejecuta el panel, use el comando `node app.js` de los pasos anteriores para enviar datos nuevos a Event Hubs. Dado que los valores de temperatura se generan aleatoriamente, el gráfico debe actualizarse para mostrar cambios importantes en la temperatura.
   
   > [!NOTE]
   > Debe estar en el directorio **hdinsight-eventhub-example/SendEvents/Nodejs** cuando use el comando `node app.js`.

3. Después de comprobar que el panel se actualiza, presione Ctrl+C para detener la topología. También puede presionar CTRL+C para detener el servidor web local.

## <a name="create-a-storm-and-hbase-cluster"></a>Creación de un clúster de Storm y HBase

En los pasos descritos en esta sección se usa una [plantilla de Azure Resource Manager](../../azure-resource-manager/resource-group-template-deploy.md) para crear una instancia de Azure Virtual Network y, en ella, un clúster de Storm y HBase. La plantilla también crea una aplicación web de Azure e implementa una copia del panel en ella.

> [!NOTE]
> Se utiliza una red virtual para que la topología que se ejecuta en el clúster de Storm pueda comunicarse directamente con el clúster de HBase usando la API Java de HBase.

La plantilla de Resource Manager que se usa en este documento se encuentra en un contenedor de blobs público de **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-hbase-storm-cluster-in-vnet-3.6.json**.

1. Haga clic en el botón siguiente para iniciar sesión en Azure y abrir la plantilla de Resource Manager en Azure Portal.
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hbase-storm-cluster-in-vnet-3.6.json" target="_blank"><img src="./media/apache-storm-sensor-data-analysis/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. En la sección **Custom deployment** (Implementación personalizada), escriba los valores siguientes:
   
    ![Parámetros de HDInsight](./media/apache-storm-sensor-data-analysis/parameters.png)
   
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

Cuando se hayan creado los recursos, se muestra la información sobre el grupo de recursos.

![Grupo de recursos de la red virtual y clústeres](./media/apache-storm-sensor-data-analysis/groupblade.png)

> [!IMPORTANT]
> Observe que los nombres de los clústeres de HDInsight son **storm-BASENAME** y **hbase-BASENAME**, donde BASENAME es el nombre proporcionado a la plantilla. Estos nombres se utilizarán más adelante al establecer la conexión con los clústeres. Observe también que el nombre del sitio del panel es **basename-dashboard**. Este valor se usa más adelante en este documento.

## <a name="configure-the-dashboard-bolt"></a>Configuración del bolt del panel

Para enviar datos al panel implementado como aplicación web, debe modificar la línea siguiente en el archivo `dev.properties`:

```yaml
dashboard.uri: http://localhost:3000
```

Cambie `http://localhost:3000` por `http://BASENAME-dashboard.azurewebsites.net` y guarde el archivo. Reemplace **BASENAME** por el nombre base proporcionado en el paso anterior. También puede utilizar el grupo de recursos que creó anteriormente para seleccionar el panel y ver la dirección URL.

## <a name="create-the-hbase-table"></a>Creación de la tabla de HBase

Para almacenar datos en HBase, primero hay que crear una tabla. Se crean previamente los recursos en los que Storm necesita escribir, ya que crear recursos desde dentro de una topología de Storm puede dar lugar a que varias instancias intenten crear el mismo recurso. Cree los recursos fuera de la topología y use Storm para leer o escribir y para el análisis.

1. Use SSH para conectarse al clúster de HBase con el usuario y la contraseña de SSH suministrados a la plantilla durante la creación del clúster. Por ejemplo, si se conecta con el comando `ssh` , debe usar la sintaxis siguiente:
   
    ```bash
    ssh sshuser@clustername-ssh.azurehdinsight.net
    ```
   
    Reemplace `sshuser` por el nombre de usuario SSH que proporcionó al crear el clúster. Reemplace `clustername` por el nombre del clúster de HBase.

2. En la sesión SSH, inicie el shell de HBase.
   
    ```bash
    hbase shell
    ```
   
    Una vez cargado el shell, verá un símbolo del sistema de `hbase(main):001:0>`.

3. En el shell de HBase, escriba el comando siguiente para crear una tabla en la que se almacenarán los datos de los sensores:
   
    ```hbase
    create 'SensorData', 'cf'
    ```

4. Compruebe que se ha creado la tabla con el comando siguiente:
   
    ```hbase
    scan 'SensorData'
    ```
   
    Esto devuelve información similar al siguiente ejemplo, que indica que hay 0 filas en la tabla.
   
        ROW                   COLUMN+CELL                                       0 row(s) in 0.1900 seconds

5. Escriba `exit` para salir del shell de HBase:

## <a name="configure-the-hbase-bolt"></a>Configuración del bolt de HBase

Para escribir en HBase desde el clúster de Storm, debe proporcionar el bolt de HBase con los detalles de configuración de su clúster de HBase.

1. Utilice uno de los ejemplos siguientes para recuperar el cuórum de Zookeeper para el clúster de HBase:

    ```bash
    CLUSTERNAME='your_HDInsight_cluster_name'
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/HBASE/components/HBASE_MASTER" | jq '.metrics.hbase.master.ZookeeperQuorum'
    ```

    > [!NOTE]
    > Reemplace `your_HDInsight_cluster_name` por el nombre del clúster de HDInsight. Para más información acerca de cómo instalar la utilidad `jq`, consulte [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/).
    >
    > Cuando se le solicite, escriba la contraseña de inicio de sesión del administrador de HDInsight.

    ```powershell
    $clusterName = 'your_HDInsight_cluster_name'
    $creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HBASE/components/HBASE_MASTER" -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.metrics.hbase.master.ZookeeperQuorum
    ```

    > [!NOTE]
    > Reemplace `your_HDInsight_cluster_name por el nombre del clúster de HDInsight. Cuando se le solicite, escriba la contraseña de inicio de sesión del administrador de HDInsight.
    >
    > Este ejemplo requiere Azure PowerShell. Para información acerca de cómo usar Azure PowerShell, consulte el artículo de [introducción a Azure PowerShell](https://docs.microsoft.com/en-us/powershell/scripting/Getting-Started-with-Windows-PowerShell?view=powershell-6).

    La información que se devuelve de estos ejemplos es similar al texto siguiente:

    `zk2-hbase.mf0yeg255m4ubit1auvj1tutvh.ex.internal.cloudapp.net:2181,zk0-hbase.mf0yeg255m4ubit1auvj1tutvh.ex.internal.cloudapp.net:2181,zk3-hbase.mf0yeg255m4ubit1auvj1tutvh.ex.internal.cloudapp.net:2181`

    Esta información la usa Storm para comunicarse con el clúster de HBase.

2. Modifique el archivo `dev.properties` y agregue la información de cuórum de Zookeeper a la línea siguiente:

    ```yaml
    hbase.zookeeper.quorum: your_hbase_quorum
    ```

## <a name="build-package-and-deploy-the-solution-to-hdinsight"></a>Compilación, empaquetado e implementación de la solución en HDInsight

En el entorno de desarrollo, siga estos pasos para implementar la topología de Storm en el clúster de storm.

1. Desde el directorio `TemperatureMonitor`, use el comando siguiente para realizar una nueva compilación y crear un paquete JAR a partir del proyecto:
   
        mvn clean package
   
    Este comando crea un archivo denominado `TemperatureMonitor-1.0-SNAPSHOT.jar in the ` en el directorio de destino del proyecto.

2. Use scp para cargar los archivos `TemperatureMonitor-1.0-SNAPSHOT.jar` y `dev.properties` en el clúster de Storm. En el ejemplo siguiente, reemplace `sshuser` por el usuario de SSH que proporcionó al crear el clúster y `clustername` por el nombre del clúster de Storm. Cuando se le solicite, escriba la contraseña del usuario de SSH.
   
    ```bash
    scp target/TemperatureMonitor-1.0-SNAPSHOT.jar dev.properties sshuser@clustername-ssh.azurehdinsight.net:
    ```

   > [!NOTE]
   > Los archivos pueden tardar unos minutos en cargarse.

    Para más información sobre cómo usar los comandos `scp` y `ssh` con HDInsight, consulte el artículo sobre el [uso de SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

3. Una vez cargado el archivo, conéctese al clúster de Storm mediante SSH.
   
    ```bash
    ssh sshuser@clustername-ssh.azurehdinsight.net
    ```

    Reemplace `sshuser` por el nombre de usuario de SSH. Reemplace `clustername` por el nombre de usuario de Storm.

4. Para comenzar la topología, use el comando siguiente desde la sesión SSH:
   
    ```bash
    storm jar TemperatureMonitor-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote --filter dev.properties -R /with-hbase.yaml
    ```

    * `--remote` envía la topología al servicio Nimbus, que la distribuye en los nodos de supervisión del clúster.
    * `--filter`usa el archivo `dev.properties` para rellenar los parámetros de la definición de topología.
    * `-R /with-hbase.yaml` usa la topología `with-hbase.yaml` que se incluye en el paquete.

5. Cuando se haya iniciado la topología, abra un explorador con el sitio web publicado en Azure y  use el comando `node app.js` para enviar datos a un Centro de eventos. Debería ver que el panel web se actualiza para mostrar la información.
   
    ![dashboard](./media/apache-storm-sensor-data-analysis/datadashboard.png)

## <a name="view-hbase-data"></a>Visualización de datos de HBase

Utilice los pasos siguientes para conectarse a HBase y compruebe que se hayan escrito los datos en la tabla:

1. Use SSH para conectarse al clúster de HBase.
   
    ```bash
    ssh sshuser@clustername-ssh.azurehdinsight.net
    ```

    Reemplace `sshuser` por el nombre de usuario de SSH. Reemplace `clustername` por el nombre del clúster de HBase.

2. En la sesión SSH, inicie el shell de HBase.
   
    ```bash
    hbase shell
    ```
   
    Una vez cargado el shell, verá un símbolo del sistema de `hbase(main):001:0>`.

3. Vea las filas de la tabla:
   
    ```hbase
    scan 'SensorData'
    ```
   
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

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

Para eliminar los clústeres, el almacenamiento y la aplicación web al mismo tiempo, elimine el grupo de recursos que los contiene.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más ejemplos de topologías de Storm con HDInsight, vea [Topologías y componentes de ejemplo de Storm para Apache Storm en HDInsight](apache-storm-example-topology.md).

Para obtener más información sobre Apache Storm, consulte el sitio de [Apache Storm](https://storm.incubator.apache.org/) .

Para obtener más información sobre HBase con HDInsight, consulte [Información general de HBase de HDInsight](../hbase/apache-hbase-overview.md).

Para obtener más información sobre Socket.io, consulte el sitio de [socket.io](http://socket.io/) .

Para obtener más información sobre D3.js, consulte [D3.js: documentos controlados por datos](http://d3js.org/).

Para obtener información sobre la creación de topologías en Java, consulte [Desarrollo de topologías de Java para Apache Storm en HDInsight](apache-storm-develop-java-topology.md).

Para obtener información sobre la creación de topologías en .NET, consulte [Desarrollo de topologías de C# para Apache Storm en HDInsight mediante Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md).

[azure-portal]: https://portal.azure.com
