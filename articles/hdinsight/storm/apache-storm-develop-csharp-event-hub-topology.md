---
title: Procesamiento de eventos desde Event Hubs con Storm - Azure HDInsight | Microsoft Docs
description: "Aprenda a procesar datos desde Azure Event Hubs con una topología de C# Storm creada en Visual Studio mediante las herramientas de HDInsight para Visual Studio."
services: hdinsight,notification hubs
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 67f9d08c-eea0-401b-952b-db765655dad0
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/27/2017
ms.author: larryfr
ms.openlocfilehash: 9ad160377a8779ae917e6fd2d605ee01b12c3e2a
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/28/2017
---
# <a name="process-events-from-azure-event-hubs-with-storm-on-hdinsight-c"></a>Procesamiento de eventos de Azure Event Hubs con Storm en HDInsight (C#)

Aprenda a trabajar con Azure Event Hubs desde Apache Storm en HDInsight. Este documento usa una topología de C# Storm para leer y escribir datos desde Event Hubs

> [!NOTE]
> Para ver una versión en Java de este proyecto, consulte [Procesamiento de eventos desde Azure Event Hubs con Storm en HDInsight (Java)](https://azure.microsoft.com/resources/samples/hdinsight-java-storm-eventhub/).

## <a name="scpnet"></a>SCP.NET

Los pasos descritos en este documento usan SCP.NET, un paquete NuGet que facilita la creación de topologías y componentes de C# para su uso con Storm en HDInsight.

> [!IMPORTANT]
> Aunque los pasos descritos en este documento se basan en un entorno de desarrollo de Windows con Visual Studio, el proyecto compilado se puede enviar a un clúster de Storm en HDInsight basado en Linux. Solo los clústeres basados en Linux creados después del 28 de octubre de 2016 admiten topologías SCP.NET.

HDInsight 3.4 y versiones superiores usan Mono para ejecutar topologías de C#. El ejemplo usado en este documento funciona con HDInsight 3.6. Si tiene previsto crear sus propias soluciones de .NET para HDInsight, consulte el documento [compatibilidad de Mono](http://www.mono-project.com/docs/about-mono/compatibility/) para detectar posibles incompatibilidades.

> [!WARNING]
> Si tiene problemas al compilar proyectos que usan SCP.NET 1.0.0.x, póngase en contacto con el Soporte técnico de Microsoft para obtener ayuda.

### <a name="cluster-versioning"></a>Control de versiones de clúster

El paquete de NuGet Microsoft.SCP.Net.SDK que se usa en el proyecto debe coincidir con la versión principal de Storm instalada en HDInsight. Las versiones de HDInsight 3.5 y 3.6 usan Storm 1.x, por lo que debe usar la versión 1.0.x.x de SCP.NET con estos clústeres.

> [!IMPORTANT]
> En el ejemplo de este documento se espera un clúster de HDInsight 3.5 o 3.6.
>
> Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las superiores. Consulte la información sobre la [retirada de HDInsight en Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

Las topologías de C# también deben tener como destino .NET 4.5.

## <a name="how-to-work-with-event-hubs"></a>Trabajo con Event Hubs

Microsoft proporciona un conjunto de componentes de Java que se pueden usar para la comunicación con Event Hubs desde una topología de Storm. Puede encontrar el archivo de almacenamiento Java (JAR) que contiene una versión compatible de HDInsight 3.6 de estos componentes en [https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar](https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar).

> [!IMPORTANT]
> Si bien los componentes están escritos en Java, puede usarlos fácilmente desde una topología de C#.

En este ejemplo se usan los componentes siguientes:

* __EventHubSpout__: lee datos de Event Hubs.
* __EventHubBolt__: escribe datos en Event Hubs.
* __EventHubSpoutConfig__: se usa para configurar EventHubSpout.
* __EventHubBoltConfig__: se usa para configurar EventHubBolt.

### <a name="example-spout-usage"></a>Ejemplo de uso de spout

SCP.NET proporciona métodos para agregar un objeto EventHubSpout a la topología. Estos métodos hacen que sea más fácil agregar un spout que usar los métodos genéricos para agregar un componente Java. En el ejemplo siguiente se muestra cómo crear un spout mediante los métodos __SetEventHubSpout__ y **EventHubSpoutConfig** proporcionados por SCP.NET:

```csharp
 topologyBuilder.SetEventHubSpout(
    "EventHubSpout",
    new EventHubSpoutConfig(
        ConfigurationManager.AppSettings["EventHubSharedAccessKeyName"],
        ConfigurationManager.AppSettings["EventHubSharedAccessKey"],
        ConfigurationManager.AppSettings["EventHubNamespace"],
        ConfigurationManager.AppSettings["EventHubEntityPath"],
        eventHubPartitions),
    eventHubPartitions);
```

En el ejemplo anterior se crea un nuevo componente de spout denominado __EventHubSpout__ y se configura para la comunicación con un centro de eventos. La indicación de paralelismo del componente está establecida en el número de particiones del centro de eventos. Esta configuración permite a Storm crear una instancia del componente para cada partición.

### <a name="example-bolt-usage"></a>Ejemplo de uso de bolt

Use el método **JavaComponmentConstructor** para crear una instancia del bolt. En el ejemplo siguiente se muestra cómo crear y configurar una nueva instancia **EventHubBolt**:

```csharp
// Java construcvtor for the Event Hub Bolt
JavaComponentConstructor constructor = JavaComponentConstructor.CreateFromClojureExpr(
    String.Format(@"(org.apache.storm.eventhubs.bolt.EventHubBolt. (org.apache.storm.eventhubs.bolt.EventHubBoltConfig. " +
        @"""{0}"" ""{1}"" ""{2}"" ""{3}"" ""{4}"" {5}))",
        ConfigurationManager.AppSettings["EventHubPolicyName"],
        ConfigurationManager.AppSettings["EventHubPolicyKey"],
        ConfigurationManager.AppSettings["EventHubNamespace"],
        "servicebus.windows.net",
        ConfigurationManager.AppSettings["EventHubName"],
        "true"));

// Set the bolt to subscribe to data from the spout
topologyBuilder.SetJavaBolt(
    "eventhubbolt",
    constructor,
    partitionCount)
        .shuffleGrouping("Spout");
```

> [!NOTE]
> En este ejemplo se usa una expresión Clojure pasada como una cadena en lugar de usar **JavaComponentConstructor** para crear un objeto **EventHubBoltConfig**, como se hizo en el ejemplo con el spout. Cualquiera de estos métodos funciona. Use el método con el que se sienta mejor.

## <a name="download-the-completed-project"></a>Descarga del proyecto finalizado

Puede descargar una versión completa del proyecto creado en este tutorial desde [GitHub](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub). Sin embargo, deberá proporcionar ajustes de configuración siguiendo los pasos de este tutorial.

### <a name="prerequisites"></a>Requisitos previos

* Una [instancia de Apache Storm en un clúster de HDInsight, versión 3.5 o 3.6](apache-storm-tutorial-get-started-linux.md).

    > [!WARNING]
    > El ejemplo usado en este documento requiere Storm en HDInsight versión 3.5 o 3.6. Esto no funciona con versiones anteriores de HDInsight debido a cambios de nombre de clase importantes. Para obtener una versión de este ejemplo que funcione con clústeres anteriores, vea [GitHub](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub/releases).

* Un [centro de eventos de Azure](../../event-hubs/event-hubs-create.md).

* El [SDK de .NET para Azure](http://azure.microsoft.com/downloads/).

* Las [herramientas de HDInsight para Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

* Java JDK 1.8 o posterior en el entorno de desarrollo. Las descargas de JDK están disponibles en [Oracle](http://www.oracle.com/technetwork/java/javase/downloads/index.html).

  * La variable de entorno **JAVA_HOME** debe señalar al directorio que contiene Java.
  * El directorio **%JAVA_HOME%/bin** debe estar en la ruta de acceso.

## <a name="download-the-event-hubs-components"></a>Descarga de los componentes de Event Hubs

Descargue el componente de spout y bolt de Event Hubs desde [https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar](https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar).

Cree un directorio denominado `eventhubspout` y guarde el archivo en él.

## <a name="configure-event-hubs"></a>Configuración de Event Hubs

Event Hubs es el origen de datos para este ejemplo. Use la información de la sección "Creación de un espacio de nombres de Event Hubs y un centro de eventos" de [Introducción al envío de mensajes a Azure Event Hubs en .NET Standard](../../event-hubs/event-hubs-create.md).

1. Después de crear el centro de eventos, vea la hoja **EventHub** de Azure Portal y seleccione **Directivas de acceso compartido**. Haga clic en **+ Agregar** para agregar las siguientes directivas:

   | Nombre | Permisos |
   | --- | --- |
   | escritor |Los métodos Send |
   | lector |Escuchar |

    ![Captura de pantalla de la ventana Directivas de acceso compartido](./media/apache-storm-develop-csharp-event-hub-topology/sas.png)

2. Seleccione las directivas de **lectura** y **escritura**. Copie y guarde el valor de clave principal de ambas directivas, ya que estos valores se usan más adelante.

## <a name="configure-the-eventhubwriter"></a>Configuración de EventHubWriter

1. Si todavía no tiene instalada la versión más reciente de las herramientas de HDInsight para Visual Studio, vea [Get started using HDInsight tools for Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md) (Introducción al uso de las herramientas de HDInsight para Visual Studio).

2. Descargue la solución de [eventhub-storm-hybrid](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub).

3. En el proyecto **EventHubWriter**, abra el archivo **App.config**. Use la información del centro de eventos que configuró anteriormente para rellenar el valor de las claves siguientes:

   | Clave | Valor |
   | --- | --- |
   | EventHubPolicyName |escritura (si ha usado un nombre distinto para la directiva con el permiso *Envío*, úselo en su lugar). |
   | EventHubPolicyKey |Clave de la directiva de escritura. |
   | EventHubNamespace |Espacio de nombres que contiene el centro de eventos. |
   | EventHubName |Nombre del centro de eventos. |
   | EventHubPartitionCount |Número de particiones del centro de eventos. |

4. Guarde y cierre el archivo **App.config**.

## <a name="configure-the-eventhubreader"></a>Configuración de EventHubReader

1. Abra el proyecto **EventHubReader**.

2. Abra el archivo **App.config** para **EventHubReader**. Use la información del centro de eventos que configuró anteriormente para rellenar el valor de las claves siguientes:

   | Clave | Valor |
   | --- | --- |
   | EventHubPolicyName |lectura (si ha usado un nombre distinto para la directiva con el permiso *escucha*, úselo en su lugar). |
   | EventHubPolicyKey |Clave de la directiva de lectura. |
   | EventHubNamespace |Espacio de nombres que contiene el centro de eventos. |
   | EventHubName |Nombre del centro de eventos. |
   | EventHubPartitionCount |Número de particiones del centro de eventos. |

3. Guarde y cierre el archivo **App.config**.

## <a name="deploy-the-topologies"></a>Implementación de las topologías

1. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto **EventHubReader** y seleccione **Enviar a Storm en HDInsight**.

    ![Captura de pantalla del Explorador de soluciones con Enviar a Storm en HDInsight resaltado](./media/apache-storm-develop-csharp-event-hub-topology/submittostorm.png)

2. En el cuadro de diálogo **Enviar topología**, seleccione el **clúster de Storm**. Expanda **Configuraciones adicionales**, seleccione **Rutas de acceso del archivo Java**, elija **...** y luego seleccione el directorio que contiene el archivo JAR que descargó anteriormente. Finalmente, haga clic en **Enviar**.

    ![Captura de pantalla del cuadro de diálogo Enviar topología](./media/apache-storm-develop-csharp-event-hub-topology/submit.png)

3. Cuando se haya enviado la topología, aparecerá el **Visor de topologías de Storm**. Para ver información sobre la topología, seleccione la topología **EventHubReader** en el panel izquierdo.

    ![Captura de pantalla del Visor de topologías de Storm](./media/apache-storm-develop-csharp-event-hub-topology/topologyviewer.png)

4. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto **EventHubWriter** y seleccione **Enviar a Storm en HDInsight**.

5. En el cuadro de diálogo **Enviar topología**, seleccione el **clúster de Storm**. Expanda **Configuraciones adicionales**, seleccione **Rutas de acceso del archivo Java**, elija **...** y seleccione el directorio que contiene el archivo JAR que descargó anteriormente. Finalmente, haga clic en **Enviar**.

6. Cuando se haya enviado la topología, actualice la lista de topologías en el **Visor de topologías de Storm** para comprobar que las dos se estén ejecutando en el clúster.

7. En el **Visor de topologías de Storm**, seleccione la topología **EventHubReader**.

8. Para abrir el resumen de componentes del bolt, haga doble clic en el componente **LogBolt** en el diagrama.

9. En la sección **Executors** (Ejecutores), seleccione uno de los vínculos de la columna **Port** (Puerto). Esto muestra información registrada por el componente. La información registrada es similar al texto siguiente:

        2017-03-02 14:51:29.255 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,255 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1830978598,"deviceId":"8566ccbc-034d-45db-883d-d8a31f34068e"}
        2017-03-02 14:51:29.283 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,283 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1756413275,"deviceId":"647a5eff-823d-482f-a8b4-b95b35ae570b"}
        2017-03-02 14:51:29.313 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,312 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1108478910,"deviceId":"206a68fa-8264-4d61-9100-bfdb68ee8f0a"}

## <a name="stop-the-topologies"></a>Detención de las topologías

Para detener las topologías, seleccione cada una en el **Visor de topologías de Storm** y haga clic en **Eliminar**.

![Captura de pantalla del Visor de topologías de Storm con el botón Eliminar resaltado](./media/apache-storm-develop-csharp-event-hub-topology/killtopology.png)

## <a name="delete-your-cluster"></a>Eliminación del clúster

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Pasos siguientes

En este documento ha aprendido a usar el spout y el bolt de Java Event Hubs desde una topología de C# para trabajar con datos en Azure Event Hubs. Para más información sobre cómo crear topologías de C#, vea lo siguiente:

* [Desarrollo de topologías de C# para Apache Storm en HDInsight con Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md)
* [Guía de programación de SCP](apache-storm-scp-programming-guide.md)
* [Topologías de ejemplo para Storm en HDInsight](apache-storm-example-topology.md)
