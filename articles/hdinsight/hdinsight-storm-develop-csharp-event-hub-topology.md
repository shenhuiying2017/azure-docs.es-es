---
title: Procesamiento de eventos desde Event Hubs con Storm en HDInsight | Microsoft Docs
description: "Aprenda a procesar datos de Centros de eventos con una topología de Storm de C# creada en Visual Studio con las herramientas de HDInsight para Visual Studio."
services: hdinsight,notification hubs
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 67f9d08c-eea0-401b-952b-db765655dad0
ms.service: hdinsight
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/12/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: c9a5091973395dd888939432292fbd06dcbf0680
ms.openlocfilehash: c0349b5890a75c6ffaa6b7eca93baa3101912cf6


---
# <a name="process-events-from-azure-event-hubs-with-storm-on-hdinsight-c"></a>Procesamiento de eventos desde Centros de eventos de Azure con Storm en HDInsight (C#)

Los Centros de eventos de Azure le permiten procesar grandes volúmenes de datos provenientes de sitios web, aplicaciones y dispositivos. El spout de los Centros de eventos permite que Apache Storm en HDInsight analice fácilmente estos datos en tiempo real. También es posible escribir datos en Centros de eventos desde Storm usando el bolt de Centros de eventos.

En este tutorial, aprenderá a utilizar las plantillas de Visual Studio que se instalan junto con las herramientas de HDInsight para Visual Studio para crear dos topologías que funcionen con los Centros de eventos de Azure.

* **EventHubWriter**: genera datos de manera aleatoria y los escribe en los Centros de eventos.
* **EventHubReader**: lee datos de Event Hubs y los registra en los registros de Storm.

> [!NOTE] 
> Para ver una versión en Java de este proyecto, consulte [Procesamiento de eventos desde Azure Event Hubs con Storm en HDInsight (Java)](hdinsight-storm-develop-java-event-hub-topology.md).

## <a name="scpnet"></a>SCP.NET

Estos proyectos usan SCP.NET, un paquete de NuGet que facilita la creación de topologías y componentes de C# para su uso con Storm en HDInsight.

> [!IMPORTANT]
> Aunque los pasos descritos en este documento se basan en un entorno de desarrollo de Windows con Visual Studio, el proyecto compilado se puede enviar a un clúster de Storm en HDInsight basado en Linux. __Solo los clústeres basados en Linux creados con posterioridad al 28/10/2016 admiten topologías SCP.NET.__

### <a name="cluster-versioning"></a>Control de versiones de clúster

El paquete de NuGet Microsoft.SCP.Net.SDK que se usa en el proyecto debe coincidir con la versión principal de Storm instalada en HDInsight. Las versiones de Storm en HDInsight 3.3 y 3.4 usan la versión Storm 0.10.x, por lo que debe usar la versión de SCP.NET 0.10.x.x con estos clústeres. HDInsight 3.5 usa Storm 1.0.x., así que debe usar la versión de SCP.NET 1.0.x.x con esta versión del clúster.

> [!IMPORTANT]
> Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las superiores. Para más información, consulte [El contrato de nivel de servicio para las versiones de clúster de HDInsight](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

HDInsight 3.4 y versiones superiores usan Mono para ejecutar topologías de C#. La mayoría de las cosas funcionarán con Mono; no obstante, debe comprobar el documento de [compatibilidad de Mono](http://www.mono-project.com/docs/about-mono/compatibility/) para ver las posibles incompatibilidades.

Las topologías de C# también deben tener como destino .NET 4.5.

## <a name="how-to-work-with-event-hubs"></a>Trabajo con Event Hubs

Microsoft proporciona un conjunto de componentes de Java que se pueden usar para la comunicación con Azure Event Hubs desde una topología de Storm. Puede encontrar el archivo jar que contiene la versión más reciente de estos componentes en [https://github.com/hdinsight/hdinsight-storm-examples/blob/master/lib/eventhubs/](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/lib/eventhubs/).

> [!IMPORTANT]
> Si bien los componentes están escritos en Java, puede usarlos fácilmente desde una topología de C#.

Los componentes con los que trabajará principalmente son:

* __EventHubSpout__: lee datos de Event Hubs.
* __EventHubBolt__: escribe datos en Event Hubs.
* __EventHubSpoutConfig__: se usa para configurar EventHubSpout.
* __EventHubBoltConfig__: se usa para configurar EventHubBolt.
* __UnicodeEventDataScheme__: se usa para configurar el spout para utilizar la codificación UTF-8 al leer Event Hubs. Si no se usa, el spout adopta como predeterminada la codificación String.

### <a name="example-spout-usage"></a>Ejemplo de uso de spout

SCP.NET proporciona métodos para agregar específicamente un objeto EventHubSpout a su topología. Estos métodos hacen que sea más fácil agregar un spout que usar los métodos genéricos para agregar un componente Java. En el ejemplo siguiente se muestra cómo crear un nuevo spout mediante los métodos __SetEventHubSpout__ y EventHubSpoutConfig proporcionados por SCP.NET:

```csharp
topologyBuilder.SetEventHubSpout(
    "EventHubSpout",
    new EventHubSpoutConfig(
        // the shared access signature name and key used to read the data
        ConfigurationManager.AppSettings["EventHubSharedAccessKeyName"],
        ConfigurationManager.AppSettings["EventHubSharedAccessKey"],
        // The namespace that contains the Event Hub to read from
        ConfigurationManager.AppSettings["EventHubNamespace"],
        // The Event Hub name to read from
        ConfigurationManager.AppSettings["EventHubEntityPath"],
        // The number of partitions in the Event Hub
        eventHubPartitions),
    // Parallelism hint for this component. Should be set to the partition count.
    eventHubPartitions);
```

En el ejemplo anterior se crea un nuevo componente de spout llamado __EventHubSpout__ y se configura para la comunicación con un centro de eventos. Observe que la indicación de paralelismo del componente está establecida en el número de particiones en el centro de eventos. Esto permite a Storm crear una instancia del componente para cada partición.

> [!WARNING]
> A partir del 1 de enero de 2017, al usar los métodos SetEventHubSpout y EventHubSpoutConfig se crea un spout que emplea codificación String al leer datos de Event Hubs. Si necesita usar codificación UTF-8, consulte el ejemplo siguiente.

También puede usar el método genérico JavaCompoentConstructor al crear un spout. En el ejemplo siguiente se muestra cómo crear un nuevo spout mediante el método JavaComponentConstructor. También se demuestra cómo configurar el spout para leer datos mediante una codificación UTF-8 en lugar de String:

```csharp
// Create an instance of UnicodeEventDataScheme
var schemeConstructor = new JavaComponentConstructor("com.microsoft.eventhubs.spout.UnicodeEventDataScheme");
// Create an instance of EventHubSpoutConfig
var eventHubSpoutConfig = new JavaComponentConstructor(
    "com.microsoft.eventhubs.spout.EventHubSpoutConfig",
    new List<Tuple<string, object>>()
    {
        // the shared access signature name and key used to read the data
        Tuple.Create<string, object>(JavaComponentConstructor.JAVA_LANG_STRING, ConfigurationManager.AppSettings["EventHubSharedAccessKeyName"]),
        Tuple.Create<string, object>(JavaComponentConstructor.JAVA_LANG_STRING, ConfigurationManager.AppSettings["EventHubSharedAccessKey"]),
        // The namespace that contains the Event Hub to read from
        Tuple.Create<string, object>(JavaComponentConstructor.JAVA_LANG_STRING, ConfigurationManager.AppSettings["EventHubNamespace"]),
        // The Event Hub name to read from
        Tuple.Create<string, object>(JavaComponentConstructor.JAVA_LANG_STRING, ConfigurationManager.AppSettings["EventHubEntityPath"]),
        // The number of partitions in the Event Hub
        Tuple.Create<string, object>("int", eventHubPartitions),
        // The encoding scheme to use when reading
        Tuple.Create<string, object>("com.microsoft.eventhubs.spout.IEventDataScheme", schemeConstructor)
    }
    );
// Create an instance of the spout
var eventHubSpout = new JavaComponentConstructor(
    "com.microsoft.eventhubs.spout.EventHubSpout",
    new List<Tuple<string, object>>()
    {
        Tuple.Create<string, object>("com.microsoft.eventhubs.spout.EventHubSpoutConfig", eventHubSpoutConfig)
    }
    );
// Set the spout in the topology
topologyBuilder.SetJavaSpout("EventHubSpout", eventHubSpout, eventHubPartitions);
```

> [!IMPORTANT]
> UnicodeEventDataScheme solo está disponible en la versión 9.5 de los componentes de Event Hubs, que está disponible en [https://github.com/hdinsight/hdinsight-storm-examples/blob/master/lib/eventhubs/](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/lib/eventhubs/).

### <a name="example-bolt-usage"></a>Ejemplo de uso de bolt

Se debe usar el método JavaComponmentConstructor para crear una instancia del bolt. En el ejemplo siguiente se muestra cómo crear y configurar una nueva instancia del objeto EventHubBolt:

```csharp
//Create constructor for the Java bolt
JavaComponentConstructor constructor =
    // Use a Clojure expression to create the EventHubBoltCOnfig
    JavaComponentConstructor.CreateFromClojureExpr(
        String.Format(@"(org.apache.storm.eventhubs.bolt.EventHubBolt. (org.apache.storm.eventhubs.bolt.EventHubBoltConfig. " +
        @"""{0}"" ""{1}"" ""{2}"" ""{3}"" ""{4}"" {5}))",
    // The policy name and key used to read from Event Hubs
    ConfigurationManager.AppSettings["EventHubPolicyName"],
    ConfigurationManager.AppSettings["EventHubPolicyKey"],
    // The namespace that contains the Event Hub
    ConfigurationManager.AppSettings["EventHubNamespace"],
    "servicebus.windows.net", //suffix for the namespace fqdn
    // The Evetn Hub Name)
    ConfigurationManager.AppSettings["EventHubName"],
    "true"));

//Set the bolt
topologyBuilder.SetJavaBolt(
        "EventHubBolt",
        constructor,
        partitionCount). //Parallelism hint uses partition count
    shuffleGrouping("Spout"); //Consume data from spout
```

> [!NOTE]
> En este ejemplo de usa una expresión Clojure que se pasa a una cadena, en lugar de usar JavaComponentConstructor para crear un objeto EventHubBoltConfig independiente como se hizo en el ejemplo con el spout. Cualquiera de estos métodos funciona; elija el que mejor se adapte en su caso.

## <a name="download-the-completed-project"></a>Descarga del proyecto finalizado

Puede descargar una versión completa del proyecto creado en este tutorial desde GitHub: [eventhub-storm-hybrid](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub). Sin embargo, deberá proporcionar ajustes de configuración siguiendo los pasos de este tutorial.

### <a name="prerequisites"></a>Requisitos previos

* Una instancia de [Apache Storm en un clúster de HDInsight, versión 3.5](hdinsight-apache-storm-tutorial-get-started.md)

    > [!WARNING]
    > El ejemplo usado en este documento requiere Storm en HDInsight versión 3.5. Hay cambios en los valores className utilizados para componentes básicos de Storm entre las versiones en los clústeres anteriores y la versión de Storm incluida con HDInsight 3.5. Para obtener una versión de este ejemplo que funcione con clústeres anteriores, consulte [https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub/releases](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub/releases).

* Un [Centro de eventos de Azure](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

* El [SDK .NET de Azure](http://azure.microsoft.com/downloads/)

* Las [Herramientas de HDInsight para Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md)

* Java JDK 1.7 o una versión superior en el entorno de desarrollo. Las descargas de JDK están disponibles en [http://www.oracle.com/technetwork/java/javase/downloads/index.html](http://www.oracle.com/technetwork/java/javase/downloads/index.html).

  * La variable de entorno **JAVA_HOME** debe señalar al directorio que contiene Java.
  * El directorio **%JAVA_HOME%/bin** debe estar en la ruta de acceso.

## <a name="download-the-event-hub-components"></a>Descarga de los componentes del centro de eventos

El spout y el bolt se distribuyen como un único archivo Java (.jar) llamado **eventhubs-storm-spout-#.#-jar-with-dependencies.jar**, donde #.# es la versión del archivo.

Se puede descargar una versión del archivo jar que funciona con la versión 3.5 de Storm en HDInsight desde [https://github.com/hdinsight/hdinsight-storm-examples/blob/master/lib/eventhubs/](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/lib/eventhubs/).

Cree un nuevo directorio denominado `eventhubspout` y guarde el archivo en el directorio.

## <a name="configure-event-hubs"></a>Configuración de Centros de eventos

Centros de eventos es el origen de datos para este ejemplo. Use la información de la sección **Creación de un Centro de eventos** del documento [Introducción a Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md).

1. Después de crear el centro de eventos, vaya a la hoja EventHub de Azure Portal y seleccione **Directivas de acceso compartido**. Use el vínculo **+ Agregar** para agregar las siguientes directivas:
   
   | Nombre | Permisos |
   | --- | --- |
   | escritor |Los métodos Send |
   | lector |Escuchar |
   
    ![directivas](./media/hdinsight-storm-develop-csharp-event-hub-topology/sas.png)

2. Seleccione las directivas de **lectura** y **escritura**. Copie y guarde el valor de **CLAVE PRINCIPAL** para ambas directivas, ya que estas se usarán más adelante.

## <a name="configure-the-eventhubwriter"></a>Configuración de EventHubWriter

1. Si todavía no tiene instalada la versión más reciente de las herramientas de HDInsight para Visual Studio, consulte [Introducción al uso de las herramientas de HDInsight para Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md).

2. Descargue la solución de [eventhub-storm-hybrid](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub). Abra la solución y examine el código del proyecto **EventHubWriter**.

3. En el proyecto **EventHubWriter**, abra el archivo **App.config**. Use la información del Centro de eventos que configuró anteriormente para rellenar el valor de las claves siguientes:
   
   | Clave | Valor |
   | --- | --- |
   | EventHubPolicyName |escritura (si ha usado un nombre distinto para la directiva con el permiso *Envío*, úselo en su lugar). |
   | EventHubPolicyKey |La clave de la directiva de escritura |
   | EventHubNamespace |El espacio de nombres que contiene el Centro de eventos |
   | EventHubName |Nombre del Centro de eventos |
   | EventHubPartitionCount |Número de particiones del Centro de eventos |

4. Guarde y cierre el archivo **App.config**.

## <a name="configure-the-eventhubreader"></a>Configuración de EventHubReader

1. Abra el proyecto **EventHubReader** y examine el código.

2. Abra el archivo **App.config** para **EventHubWriter**. Use la información del Centro de eventos que configuró anteriormente para rellenar el valor de las claves siguientes:
   
   | Clave | Valor |
   | --- | --- |
   | EventHubPolicyName |lectura (si ha usado un nombre distinto para la directiva con el permiso *escucha*, úselo en su lugar). |
   | EventHubPolicyKey |La clave de la directiva de lectura |
   | EventHubNamespace |El espacio de nombres que contiene el Centro de eventos |
   | EventHubName |Nombre del Centro de eventos |
   | EventHubPartitionCount |Número de particiones del Centro de eventos |

3. Guarde y cierre el archivo **App.config**.

## <a name="deploy-the-topologies"></a>Implementación de las topologías

1. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto **EventHubReader** y seleccione **Enviar a Storm en HDInsight**.
   
    ![enviar a storm](./media/hdinsight-storm-develop-csharp-event-hub-topology/submittostorm.png)

2. En la pantalla **Enviar topología**, seleccione su **clúster de Storm**. Expanda **Configuraciones adicionales**, seleccione **Rutas de acceso del archivo Java**, elija **...** y, después, seleccione el directorio que contiene el archivo jar que descargó anteriormente. Finalmente, haga clic en **Enviar**.
   
    ![Imagen del cuadro de diálogo de envío](./media/hdinsight-storm-develop-csharp-event-hub-topology/submit.png)

3. Cuando se haya enviado la topología, aparecerá el **Visor de topologías de Storm** . Seleccione la topología **EventHubReader** en el panel de la izquierda para ver estadísticas de la topología. Actualmente, no debería ocurrir nada, puesto que todavía no se han escrito eventos en Centros de eventos.
   
    ![vista de almacenamiento de ejemplo](./media/hdinsight-storm-develop-csharp-event-hub-topology/topologyviewer.png)

4. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto **EventHubWriter** y seleccione **Enviar a Storm en HDInsight**.

5. En la pantalla **Enviar topología**, seleccione su **clúster de Storm**. Expanda **Configuraciones adicionales**, seleccione **Rutas de acceso del archivo Java**, elija **...** y, después, seleccione el directorio que contiene el archivo jar que descargó anteriormente. Finalmente, haga clic en **Enviar**.

6. Cuando se haya enviado la topología, actualice la lista de topologías en el **Visor de topologías de Storm** para comprobar que las dos se estén ejecutando en el clúster.

7. En el **Visor de topologías de Storm**, seleccione la topología **EventHubReader**.

8. En la vista gráfica, haga doble clic en el componente **LogBolt**. Se abrirá la página **Component Summary** (Resumen de componente) correspondiente al bolt.

9. En la sección **Executors** (Ejecutores), seleccione uno de los vínculos de la columna **Port** (Puerto). Se muestra información registrada por el componente. La información registrada es similar a la siguiente:
   
        2016-10-20 13:26:44.186 m.s.s.b.ScpNetBolt [INFO] Processing tuple: source: com.microsoft.eventhubs.spout.EventHubSpout:7, stream: default, id: {5769732396213255808=520853934697489134}, [{"deviceId":3,"deviceValue":1379915540}]
        2016-10-20 13:26:44.234 m.s.s.b.ScpNetBolt [INFO] Processing tuple: source: com.microsoft.eventhubs.spout.EventHubSpout:7, stream: default, id: {7154038361491319965=4543766486572976404}, [{"deviceId":3,"deviceValue":459399321}]
        2016-10-20 13:26:44.335 m.s.s.b.ScpNetBolt [INFO] Processing tuple: source: com.microsoft.eventhubs.spout.EventHubSpout:6, stream: default, id: {513308780877039680=-7571211415704099042}, [{"deviceId":5,"deviceValue":845561159}]
        2016-10-20 13:26:44.445 m.s.s.b.ScpNetBolt [INFO] Processing tuple: source: com.microsoft.eventhubs.spout.EventHubSpout:7, stream: default, id: {-2409895457033895206=5479027861202203517}, [{"deviceId":8,"deviceValue":2105860655}]

## <a name="stop-the-topologies"></a>Detención de las topologías

Para detener las topologías, seleccione cada una en el **Visor de topologías de Storm** y haga clic en **Eliminar**.

![imagen de eliminación de una topología](./media/hdinsight-storm-develop-csharp-event-hub-topology/killtopology.png)

## <a name="delete-your-cluster"></a>Eliminación del clúster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Pasos siguientes

En este documento, ha aprendido a usar el spout y bolt de los Centros de eventos de Java desde una topología de C# para trabajar con datos en el Centro de eventos de Azure. Para obtener más información acerca de cómo crear topologías de C#, consulte lo siguiente

* [Desarrollo de topologías de C# para Apache Storm en HDInsight con Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md)
* [Guía de programación de SCP](hdinsight-storm-scp-programming-guide.md)
* [Topologías de ejemplo para Storm en HDInsight](hdinsight-storm-example-topology.md)




<!--HONumber=Jan17_HO3-->


