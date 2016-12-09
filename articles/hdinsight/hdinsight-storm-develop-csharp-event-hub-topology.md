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
ms.date: 10/27/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 77d0dfe7e09baab9b923b3c49af0cb7c28cd8625


---
# <a name="process-events-from-azure-event-hubs-with-storm-on-hdinsight-c"></a>Procesamiento de eventos desde Centros de eventos de Azure con Storm en HDInsight (C#)
Los Centros de eventos de Azure le permiten procesar grandes volúmenes de datos provenientes de sitios web, aplicaciones y dispositivos. El spout de los Centros de eventos permite que Apache Storm en HDInsight analice fácilmente estos datos en tiempo real. También es posible escribir datos en Centros de eventos desde Storm usando el bolt de Centros de eventos.

En este tutorial, aprenderá a utilizar las plantillas de Visual Studio que se instalan junto con las herramientas de HDInsight para Visual Studio para crear dos topologías que funcionen con los Centros de eventos de Azure.

* **EventHubWriter**: genera datos de manera aleatoria y los escribe en los Centros de eventos.
* **EventHubReader**: lee datos de Event Hubs y los registra en los registros de Storm.

> [!NOTE]
> Aunque los pasos descritos en este documento se basan en un entorno de desarrollo de Windows con Visual Studio, el proyecto compilado se puede enviar a un clúster de HDInsight basado en Windows o Linux. Solo los clústeres basados en Linux creados con posterioridad al 28/10/2016 admiten topologías SCP.NET.
> 
> Para usar una topología de C# con un clúster basado en Linux, debe actualizar el paquete de NuGet Microsoft.SCP.Net.SDK usado en el proyecto a la versión 0.10.0.6 u otra superior. La versión del paquete también debe coincidir con la versión principal de Storm instalada en HDInsight. Por ejemplo, en las versiones de HDInsight 3.3 y 3.4 se usa Storm 0.10.x, mientras que en HDInsight 3.5 se usa Storm 1.0.x.
> 
> Las topologías de C# en clústeres basados en Linux deben usar .NET 4.5, y emplear Mono para ejecutarse en el clúster de HDInsight. La mayoría de las cosas funcionará; no obstante, debe comprobar el documento de [compatibilidad de Mono](http://www.mono-project.com/docs/about-mono/compatibility/) para ver las posibles incompatibilidades.
> 
> Para obtener una versión de Java de este proyecto, que funcionará también en un clúster basado en Windows o en Linux, consulte [Procesamiento de eventos desde Azure Event Hubs con Storm en HDInsight (Java)](hdinsight-storm-develop-java-event-hub-topology.md).
> 
> 

## <a name="prerequisites"></a>Requisitos previos
* Un [clúster de Apache Storm en HDInsight](hdinsight-apache-storm-tutorial-get-started.md)
* Un [Centro de eventos de Azure](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
* El [SDK .NET de Azure](http://azure.microsoft.com/downloads/)
* Las [Herramientas de HDInsight para Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md)

## <a name="completed-project"></a>Proyecto completado
Puede descargar una versión completa del proyecto creado en este tutorial desde GitHub: [eventhub-storm-hybrid](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub). Sin embargo, deberá proporcionar ajustes de configuración siguiendo los pasos de este tutorial.

## <a name="event-hubs-spout-and-bolt"></a>Spout y bolt de Centros de eventos
El spout y bolt de Centros de eventos son componentes de Java que le permiten trabajar con facilidad con los Centros de eventos desde Apache Storm. A pesar de que estos componentes están escritos en Java, las herramientas de HDInsight para Visual Studio le permiten crear topologías híbridas que combinan componentes de Java y C#.

El spout y el bolt se distribuyen como un único archivo Java (.jar) llamado **eventhubs-storm-spout-#.#-jar-with-dependencies.jar**, donde #.# es la versión del archivo.

### <a name="download-the-jar-file"></a>Descarga del archivo .jar
La versión más reciente del archivo jar se incluye en el proyecto [HDInsight Storm examples](https://github.com/hdinsight/hdinsight-storm-examples) en la carpeta **lib/eventhubs**. Para descargar el archivo, use uno de los siguientes métodos.

> [!NOTE]
> El spout y bolt se enviaron para su inclusión en el proyecto de Apache Storm. Para más información, consulte [STORM-583: Initial check-in for storm-event hubs](https://github.com/apache/storm/pull/336/files) en GitHub.
> 
> 

* **Descargue un archivo ZIP**: desde el sitio [HDInsight Storm examples](https://github.com/hdinsight/hdinsight-storm-examples), seleccione **Download ZIP** (Descargar ZIP) en el panel derecho para descargar un archivo .zip que contiene el proyecto.
  
    ![botón download zip](./media/hdinsight-storm-develop-csharp-event-hub-topology/download.png)
  
    Cuando se haya descargado, puede extraer el archivo, que se encontrará en el directorio **lib** .
* **Clone el proyecto**: si tiene instalado [Git](http://git-scm.com/), use el siguiente comando para clonar de manera local el repositorio y, después, encuentre el archivo en el directorio **lib**.
  
        git clone https://github.com/hdinsight/hdinsight-storm-examples

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
2. En la pantalla **Enviar topología**, seleccione su **clúster de Storm**. Expanda **Configuraciones adicionales**, seleccione **Rutas de acceso del archivo Java**, elija **...** y, después, seleccione el directorio que contiene el archivo **eventhubs-storm-spout-0.9-jar-with-dependencies.jar** que descargó anteriormente. Finalmente, haga clic en **Enviar**.
   
    ![Imagen del cuadro de diálogo de envío](./media/hdinsight-storm-develop-csharp-event-hub-topology/submit.png)
3. Cuando se haya enviado la topología, aparecerá el **Visor de topologías de Storm** . Seleccione la topología **EventHubReader** en el panel de la izquierda para ver estadísticas de la topología. Actualmente, no debería ocurrir nada, puesto que todavía no se han escrito eventos en Centros de eventos.
   
    ![vista de almacenamiento de ejemplo](./media/hdinsight-storm-develop-csharp-event-hub-topology/topologyviewer.png)
4. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto **EventHubWriter** y seleccione **Enviar a Storm en HDInsight**.
5. En la pantalla **Enviar topología**, seleccione su **clúster de Storm**. Expanda **Configuraciones adicionales**, seleccione **Rutas de acceso del archivo Java**, **...** y, después, seleccione el directorio que contiene el archivo **eventhubs-storm-spout-0.9-jar-with-dependencies.jar** que descargó anteriormente. Finalmente, haga clic en **Enviar**.
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

## <a name="notes"></a>Notas
### <a name="checkpointing"></a>Puntos de control
El elemento EventHubSpout envía periódicamente puntos de control de su estado al nodo Zookeeper, que almacena el desplazamiento actual de los mensajes leídos de la cola. Esto permite que el componente empiece a recibir mensajes en el desplazamiento almacenado en los escenarios siguientes:

* Se produce un error en la instancia del componente y se reinicia.
* Aumenta o reduce el clúster, mediante la incorporación o eliminación de nodos.
* Se elimina y reinicia la topología **con el mismo nombre**.

También puede exportar e importar los puntos de control persistentes a WASB (el almacenamiento de Azure que usa su clúster de HDInsight). Los scripts para hacerlo se encuentran en el clúster de HDInsight en Storm, en **c:\apps\dist\storm-0.9.3.2.2.1.0-2340\zkdatatool-1.0\bin**.

> [!NOTE]
> El número de versión en la ruta de acceso puede ser diferente, ya que la versión de Storm instalada en el clúster puede cambiar en el futuro.
> 
> 

Los scripts en este directorio son:

* **stormmeta_import.cmd**: importar todos los metadatos de Storm del contenedor de almacenamiento del clúster predeterminado a Zookeeper.
* **stormmeta_export.cmd**: exportar todos los metadatos de Storm de Zookeeper al contenedor de almacenamiento del clúster predeterminado.
* **stormmeta_delete.cmd**: eliminar todos los metadatos de Storm de Zookeeper.

La exportación de una importación permite conservar los datos de puntos de control cuando deba eliminar el clúster, pero quiera reanudar el procesamiento desde el desplazamiento actual en el centro cuando vuelva a poner en línea un nuevo clúster.

> [!NOTE]
> Dado que los datos se conservan en el contenedor de almacenamiento predeterminado, el nuevo clúster **debe** utilizar la misma cuenta de almacenamiento y el mismo contenedor que el clúster anterior.
> 
> 

## <a name="next-steps"></a>Pasos siguientes
En este documento, ha aprendido a usar el spout y bolt de los Centros de eventos de Java desde una topología de C# para trabajar con datos en el Centro de eventos de Azure. Para obtener más información acerca de cómo crear topologías de C#, consulte lo siguiente

* [Desarrollo de topologías de C# para Apache Storm en HDInsight con Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md)
* [Guía de programación de SCP](hdinsight-storm-scp-programming-guide.md)
* [Topologías de ejemplo para Storm en HDInsight](hdinsight-storm-example-topology.md)




<!--HONumber=Nov16_HO3-->


