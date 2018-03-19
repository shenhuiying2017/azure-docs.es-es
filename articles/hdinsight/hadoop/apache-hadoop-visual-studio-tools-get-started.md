---
title: "Conexión a Azure HDInsight mediante Herramientas de Data Lake para Visual Studio | Microsoft Docs"
description: "Aprenda a instalar y utilizar Data Lake Tools para Visual Studio para conectarse a clústeres de Hadoop en Azure HDInsight y luego a ejecutar consultas de Hive."
keywords: herramientas hadoop, consulta hive, visual studio, visual studio hadoop
services: HDInsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: ce9c572a-1e98-46bf-9581-13a9767f1fa5
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/22/2018
ms.author: jgao
ms.openlocfilehash: afd40d75bb9c5fd3170a4da215925244994d7749
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/12/2018
---
# <a name="use-data-lake-tools-for-visual-studio-to-connect-to-azure-hdinsight-and-run-hive-queries"></a>Conectarse a Azure HDInsight y ejecutar consultas de Hive con Herramientas de Data Lake para Visual Studio

Aprenda a usar Data Lake Tools para Visual Studio (también conocido como Herramientas de Azure Data Lake y Stream Analytics para Visual Studio) para conectarse a clústeres de Hadoop en [Azure HDInsight](../hdinsight-hadoop-introduction.md) y enviar consultas de Hive. 

Para más información sobre el uso de HDInsight, consulte [Introducción a HDInsight](../hdinsight-hadoop-introduction.md) y [Primeros pasos con HDInsight](apache-hadoop-linux-tutorial-get-started.md). 

Para más información acerca de cómo conectarse a un clúster de Storm, consulte [Desarrollo de topologías de C# para Apache Storm con Azure Data Lake Tools para Visual Studio](../storm/apache-storm-develop-csharp-visual-studio-topology.md).

Data Lake Tools para Visual Studio se puede utilizar para acceder a Azure Data Lake Analytics y a HDInsight. Para más información acerca de Data Lake Tools, consulte [Desarrollo de scripts U-SQL mediante Data Lake Tools para Visual Studio](../../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md).

## <a name="prerequisites"></a>requisitos previos

Para completar este tutorial y usar Data Lake Tools para Visual Studio, se necesitan los siguientes elementos:

* Un clúster de HDInsight de Azure. Para crear un clúster de HDInsight, consulte [Tutorial de Hadoop: introducción al uso de Hadoop en HDInsight](apache-hadoop-linux-tutorial-get-started.md). Para ejecutar consultas de Hive interactivas, necesita un clúster de [Interactive Query de HDInsight](../interactive-query/apache-interactive-query-get-started.md).
* Un equipo que tenga Visual Studio 2017, 2015 o 2013 instalado.
    
    > [!NOTE]
    > Actualmente, solo está disponible la versión en inglés de Data Lake Tools para Visual Studio.
    > 
    > 

## <a name="install-or-update-data-lake-tools-for-visual-studio"></a>Instalación o actualización de Data Lake Tools para Visual Studio

### <a name="install-data-lake-tools"></a>Instalación de Data Lake Tools

Data Lake Tools está instalado de forma predeterminada para Visual Studio de 2017. Para las versiones anteriores de Visual Studio, Data Lake Tools se puede instalar mediante el [Instalador de plataforma web](https://www.microsoft.com/web/downloads/platform.aspx). Elija la versión de Data Lake Tools que coincida con su versión de Visual Studio. 

### <a name="install-visual-studio"></a>Instalación de Visual Studio

Si no tiene instalado Visual Studio, use el [Instalador de plataforma web](https://www.microsoft.com/web/downloads/platform.aspx) para instalar las versiones más recientes de Visual Studio Community y el SDK de Azure:

![Captura de pantalla del Instalador de plataforma web de Data Lake Tools para Visual Studio: instalador de plataforma web.](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.wpi.png "Uso del Instalador de plataforma web para instalar Data Lake Tools para Visual Studio")

### <a name="update-the-tools"></a>Actualización de las herramientas

1. Abra Visual Studio.
2. En el menú **Herramientas**, seleccione **Extensiones y actualizaciones**.
3. Expanda **Actualizaciones** y seleccione **Herramientas de Azure Data Lake y Stream Analytics** (si está instalado).

> [!NOTE]
>
> Puede utilizar solo Data Lake Tools, versión 2.3.0.0 o posterior, para conectarse a clústeres de Interactive Query y ejecutar consultas de Hive interactivas.

## <a name="connect-to-azure-subscriptions"></a>Conexión a suscripciones de Azure
Data Lake Tools para Visual Studio se puede utilizar para conectarse a clústeres de HDInsight, realizar algunas operaciones básicas de administración y ejecutar consultas de Hive.

> [!NOTE]
> Para más información acerca de cómo conectarse a un clúster de Hadoop genérico, consulte [How to write and submit Hive queries using Visual Studio](http://blogs.msdn.com/b/xiaoyong/archive/2015/05/04/how-to-write-and-submit-hive-queries-using-visual-studio.aspx) (Escritura y envío de consultas de Hive mediante Visual Studio).
> 
> 

Para conectarse a su suscripción de Azure:

1. Abra Visual Studio.
2. En el menú **Ver**, seleccione **Explorador de servidores**.
3. En el Explorador de servidores, expanda **Azure** y, después, expanda **HDInsight**.
   
   > [!NOTE]
   > Debe abrirse la ventana de **lista de tareas de HDInsight**. Si no ve la ventana, en el menú **Ver**, seleccione **Otras ventanas**y, después, seleccione **Ventana de lista de tareas de HDInsight**.  
   > 
   > 
4. Escriba sus credenciales de suscripción de Azure y, después, seleccione **Iniciar sesión**. La autenticación solo se requiere si nunca se ha conectado a la suscripción de Azure desde Visual Studio en este equipo.
5. En el Explorador de servidores, aparece una lista de los clústeres de HDInsight existentes. Si no tiene ningún clúster, puede crear uno mediante Azure Portal, Azure PowerShell o el SDK de HDInsight. Para más información, consulte [Creación de clústeres de Hadoop en HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).
   
   ![Captura de pantalla de la lista de clústeres de Data Lake Tools para Visual Studio en el Explorador de servidores](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.server.explorer.png "Lista de clústeres de Data Lake Tools para Visual Studio en el Explorador de servidores")
6. Expanda un clúster de HDInsight. **Bases de datos de Hive**, aparecerán una cuenta de almacenamiento predeterminada, cuentas de almacenamiento vinculadas y un **registro del servicio Hadoop**. Puede expandir las entidades.

Una vez conectado a su suscripción de Azure, puede realizar las siguientes tareas.

Para conectarse a Azure Portal desde Visual Studio:

1. En el Explorador de servidores, seleccione **Azure** > **HDInsight**.
2. Haga clic con el botón derecho en un clúster de HDInsight y, seleccione **Manage Cluster in Azure portal** (Administrar clúster in Azure Portal).

Para hacer preguntas y realizar comentarios desde Visual Studio:

1. En el menú **Herramientas**, seleccione **HDInsight**.
2. Para formular preguntas, seleccione **Foro de MSDN**. Para enviar comentarios, seleccione **Enviar comentarios**.

## <a name="explore-linked-resources"></a>Exploración de recursos vinculados
En el Explorador de servidores, puede ver la cuenta de almacenamiento predeterminada y las cuentas de almacenamiento vinculadas. Expanda la cuenta de almacenamiento predeterminada para ver los contenedores en la cuenta de almacenamiento. Se marcarán la cuenta de almacenamiento predeterminada y el contenedor predeterminado. Haga clic con el botón derecho en cualquiera de los contenedores para ver su contenido.

![Captura de pantalla de recursos vinculados de lista de Data Lake Tools para Visual Studio en el Explorador de servidores](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.linked.resources.png "Recursos vinculados de lista")

Después de abrir un contenedor, puede usar los siguientes botones para cargar, eliminar y descargar blobs:

![Captura de pantalla de operaciones de blob de Data Lake Tools para Visual Studio en el Explorador de servidores](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.blob.operations.png "Carga, eliminación y descarga de blobs en el Explorador de servidores")

## <a name="run-interactive-hive-queries"></a>Ejecución de consultas de Hive interactivas
[Apache Hive](http://hive.apache.org) es una infraestructura de almacenamiento de datos que se basa en Hadoop. Hive se utiliza para el análisis, las consultas y resumen de los datos. Data Lake Tools para Visual Studio se puede usar para ejecutar consultas de Hive desde Visual Studio. Para más información sobre Hive, consulte [Uso de Hive con HDInsight](hdinsight-use-hive.md).

[Interactive Query](../interactive-query/apache-interactive-query-get-started.md) usa [Hive en LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) en Apache Hive 2.1. Interactive Query aporta la interactividad a las complejas consultas de estilo de almacenamiento de datos en grandes conjuntos de datos almacenados. La ejecución de consultas de Hive en Interactive Query es mucho más rápida en comparación con los trabajos por lotes tradicionales de Hive. Para más información, consulte [Ejecución de trabajos por lotes de Hive](#run-hive-batch-jobs).

> [!NOTE]
>
> Solo se pueden ejecutar consultas de Hive interactivas al conectarse a un clúster de [HDInsight Interactive Query](../interactive-query/apache-interactive-query-get-started.md).

También se puede utilizar Data Lake Tools para Visual Studio para ver el contenido de un trabajo de Hive. Data Lake Tools para Visual Studio recopila y muestra los registros Yarn de determinados trabajos de Hive.

### <a name="view-hivesampletable"></a>Visualización **hivesampletable**
Todos los clústeres de HDInsight tienen una tabla de Hive de ejemplo denominada hivesampletable. La tabla de Hive define cómo enumerar las tablas de Hive, ver esquemas de tabla y enumerar las filas de la tabla de Hive.

Para enumerar las tablas de Hive y ver el esquema de la tabla de Hive:

1. Para ver el esquema de la tabla, en el **Explorador de servidores**, seleccione **Azure** > **HDInsight**. Seleccione el clúster y, a continuación, seleccione **Bases de datos de Hive** > **Predeterminado** > **hivesampletable**.
2. Haga clic con el botón derecho en **hivesampletable** y, después, haga clic en **Ver las primeras 100 filas** para mostrar las filas. Esto equivale a ejecutar la siguiente consulta de Hive mediante el uso del controlador ODBC de Hive:
   
     `SELECT * FROM hivesampletable LIMIT 100`
   
   Puede personalizar el recuento de filas.
   
   ![Captura de pantalla de una consulta de Hive de HDInsight para Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.hive.schema.png "Resultados de la consulta de Hive")

### <a name="create-hive-tables"></a>Crear tablas de Hive
Para crear una tabla de Hive, puede usar la GUI o las consultas de Hive. Para obtener información acerca del uso de consultas de Hive, consulte [Ejecución de consultas de Hive](#run.queries).

Para crear una tabla de Hive:

1. En el **Explorador de servidores**, seleccione **Azure** > **Clústeres de HDInsight**. Seleccione un clúster de HDInsight y, después, seleccione **Bases de datos de Hive**.
2. Haga clic con el botón derecho en **predeterminado**y, después, seleccione **Crear tabla**.
3. Configure la tabla.  
4. Seleccione **Crear tabla** para enviar el trabajo para crear la nueva tabla de Hive.
   
    ![Captura de la ventana Crear tabla de Visual Studio Tools en HDInsight](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.create.hive.table.png "Creación de tablas de Hive")

### <a name="run.queries"></a>Validación y ejecución de consultas de Hive
Para crear y ejecutar consultas de Hive tiene dos opciones:

* Crear consultas ad hoc
* Crear una aplicación de Hive

Para crear, validar y ejecutar consultas ad hoc:

1. En el **Explorador de servidores**, seleccione **Azure** > **Clústeres de HDInsight**.
2. Haga clic con el botón derecho en el clúster en que desee ejecutar la consulta y, después, seleccione **Escribir una consulta de Hive**.  
3. Escriba las consultas de Hive. 

    El editor de Hive es compatible con IntelliSense. Data Lake Tools para Visual Studio es compatible con la carga de metadatos remotos cuando se edita un script de Hive. Por ejemplo, si escribe **SELECT * FROM**, IntelliSense enumera todos los nombres de tabla sugeridos. Cuando se especifica un nombre de tabla, IntelliSense enumera los nombres de columna. Las herramientas admiten casi todas las instrucciones DML de Hive, subconsultas y UDF integradas.
   
    ![Captura de pantalla del ejemplo 1 de Visual Studio Tools en HDInsight: IntelliSense](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.intellisense.table.names.png "IntelliSense en U-SQL")
   
    ![Captura de pantalla del ejemplo 2 de Visual Studio Tools en HDInsight: IntelliSense](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.intellisense.column.names.png "IntelliSense en U-SQL")
   
   > [!NOTE]
   > IntelliSense solo sugiere los metadatos del clúster que se selecciona en la barra de herramientas de HDInsight.
   > 
   
4. (Opcional) Para comprobar los errores de sintaxis del script, seleccione **Validar script**.
   
    ![Captura de pantalla de la validación local de Data Lake Tools para Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.validate.hive.script.png "Validar script")
5. Seleccione **Enviar** o en **Enviar (avanzado)**. Si selecciona la opción de envío avanzado, configure las opciones **Nombre del trabajo**, **argumentos**, **Configuraciones adicionales** y **Estado de directorio** del script:
   
    ![Captura de pantalla de consulta de Hive para Hadoop en HDInsight](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.submit.jobs.advanced.png "Envío de consultas")
   
    Una vez enviado el trabajo, aparece la ventana **Resumen del trabajo de Hive**.
   
    ![Captura de pantalla de una consulta de Hive para Hadoop en HDInsight](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.run.hive.job.summary.png "Resumen de un trabajo de Hive")
6. Utilice el botón **Actualizar** para actualizar el estado hasta que el estado del trabajo cambie a **Completado**.
7. Seleccione los vínculos de la parte inferior para ver **Consulta de trabajo**, **Salida de trabajo**, **Registro de trabajo** o **Registro Yarn**.

Para crear y ejecutar una solución de Hive:

1. En el menú **Archivo**, seleccione **Nuevo** y, a continuación, seleccione **Proyecto**.
2. En el panel izquierdo, seleccione **HDInsight**. En el panel del medio, seleccione **Aplicación Hive**. Escriba las propiedades y seleccione **Aceptar**.
   
    ![Captura de un proyecto de Hive nuevo de Visual Studio Tools en HDInsight](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.new.hive.project.png "Creación de aplicaciones de Hive desde Visual Studio")
3. En el **Explorador de soluciones**, haga doble clic en **Script.hql** para abrir el script.
4. Para validar el script de Hive, haga clic en el botón **Validar script**. O bien, puede haga clic con el botón derecho en el script en el editor de Hive y, después, seleccione **Validar script** en el menú contextual.

### <a name="view-hive-jobs"></a>Ver trabajos de Hive
Puede ver consultas de trabajo, salidas de trabajo, registros de trabajo y registros Yarn para trabajos de Hive. Para más información, vea la captura de pantalla anterior.

En la versión más reciente de la herramienta puede ver el contenido de los trabajos de Hive al recopilar y exponer los registros Yarn. Un registro Yarn puede ayudarle a investigar problemas de rendimiento. Para más información acerca de la forma en que HDInsight recopila registros Yarn, consulte [Acceso a registros de aplicación de YARN en HDInsight basado en Windows](../hdinsight-hadoop-access-yarn-app-logs.md).

Para ver trabajos de Hive:

1. En el **Explorador de servidores**, expanda **Azure** y, después, expanda **HDInsight**.
2. Haga clic con el botón derecho en un clúster de HDInsight y, luego, seleccione **Ver trabajos**. Aparece una lista de los trabajos de Hive que se ejecutaron en el clúster.  
3. Seleccione un trabajo. En la ventana **Resumen de trabajos de Hive**, seleccione una de las siguientes opciones:
    - **Consulta de trabajo**
    - **Salida de trabajo**
    - **Registro de trabajo**  
    - **Registro Yarn**
   
    ![Captura de pantalla de la ventana Ver trabajos de Hive de Visual Studio Tools en HDInsight](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.view.hive.jobs.png "Ver trabajos de Hive")

### <a name="faster-path-hive-execution-via-hiveserver2"></a>Ejecución de Hive con una ruta de acceso más rápida mediante HiveServer2
> [!NOTE]
> Esta característica solo funciona en un clúster de la versión 3.2 de HDInsight, o cualquier versión posterior.
 
Data Lake Tools para Visual Studio se usa para enviar trabajos de Hive mediante [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (denominado también Templeton). Este método de envío de trabajos de Hive tardó mucho tiempo en devolver los detalles del trabajo y la información de error.

Para solucionar este problema de rendimiento, Data Lake Tools para Visual Studio puede omitir RDP/SSH y ejecutar trabajos de Hive directamente en el clúster a través de HiveServer2.

Además de mejorar el rendimiento, con este método también pueden ver Hive en gráficos de Apache Tez y en los detalles de la tarea.

En un clúster de HDInsight, versión 3.2 o posterior, aparece el botón **Ejecutar mediante HiveServer2**:

![Captura de pantalla de la opción Ejecutar mediante HiveServer2 de Data Lake Tools para Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.execute.via.hiveserver2.png "Ejecución de consultas de Hive mediante HiveServer2")

También puede ver que se vuelve a hacer streaming de los registros en tiempo real. Si la consulta de Hive se ejecuta en Tez, también puede ver los gráficos del trabajo.

![Captura de pantalla de la característica Ejecución de Hive con una ruta de acceso más rápida mediante HiveServer2 de Data Lakes Tools para Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.fast.path.hive.execution.png "Ver gráficos de trabajo")

### <a name="execute-queries-via-hiveserver2-vs-submit-queries-via-webhcat"></a>Ejecución de consultas mediante HiveServer2 frente al envío de consultas a través de WebHCat

Aunque puede obtener muchas ventajas de rendimiento al ejecutar consultas mediante HiveServer2, este método tiene varias limitaciones. Algunas de las limitaciones hacen que este método no sea adecuado para usarlo en producción. 

La siguiente tabla muestra las diferencias entre la ejecución de consultas a través de HiveServer2 y el envío de consultas a través de WebHCat:

|  | Ejecutar mediante HiveServer2 | Enviar a través de WebHCat |
| --- | --- | --- |
| Ejecución de consultas |Elimina la sobrecarga de WebHCat (que inicia un trabajo MapReduce con el nombre TempletonControllerJob). |Si una consulta se ejecuta a través de WebHCat, dicha aplicación inicia un trabajo de MapReduce, lo que introduce mayor latencia. |
| Registros inversos |Casi en tiempo real. |Los registros de ejecución de trabajo solo están disponibles cuando finalice el trabajo. |
| Visualización del historial de trabajos |Si una consulta se ejecuta a través de HiveServer2, no se conserva su historial de trabajos (registro de trabajos, salida de trabajos). La aplicación se puede ver en la interfaz de usuario de Yarn con información limitada. |Si una consulta se ejecuta a través de WebHCat, su historial de trabajos (registro de trabajo, salida de trabajo) se conserva. El historial de trabajos se puede ver mediante Visual Studio, el SDK de HDInsight o PowerShell. |
| Cierre de ventana |La ejecución a través de HiveServer2 es *sincrónica*. Si se cierran las ventanas, se cancela la ejecución de la consulta. |El envío a través de WebHCat es *asincrónico*. Puede enviar la consulta a través de WebHCat y, después, cerrar Visual Studio. Puede regresar y ver los resultados en cualquier momento. |

### <a name="tez-hive-job-performance-graph"></a>Gráfico de rendimiento de trabajos de Hive de Tez
En Data Lake Tools para Visual Studio, puede ver los gráficos de rendimiento de los trabajos de Hive que ejecuta el motor de ejecución de Tez. Para obtener información acerca de la habilitación de Tez, consulte [¿Qué son Apache Hive y HiveQL en Azure HDInsight?](hdinsight-use-hive.md). 

Después de enviar un trabajo de Hive a Visual Studio, este mostrará el gráfico de dicho trabajo cuando se complete. Es posible que tenga que hacer clic en el botón **Actualizar** para ver el estado del trabajo más reciente.

> [!NOTE]
> Esta característica solo está disponible para un clúster de la versión 3.2.4.593 de HDInsight, o cualquier versión posterior. La característica solo funciona en trabajos completados. Para usar esta característica también debe enviar trabajo a través de WebHCat. La siguiente imagen aparece cuando se ejecuta una consulta a través de HiveServer2: 
> 
> ![Captura de pantalla del gráfico de rendimiento de Tez de Hive para Hadoop](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.hive.tez.performance.graph.png "Estado del trabajo")

Para ayudarle a entender mejor su consulta de Hive, en esta versión se ha agregado la vista de operador de Hive. Para ver todos los operadores que hay en el vértice, haga doble clic en los vértices del gráfico del trabajo. También puede señalar a un operador concreto para ver más detalles de él.

### <a name="task-execution-view-for-hive-on-tez-jobs"></a>Vista de ejecución de tareas de Hive en trabajos de Tez
La vista de ejecución de tareas de Hive en trabajos de Tez se puede usar para obtener información estructurada y visualizada de los trabajos de Hive. También puede obtener más información del trabajo. Si se produce algún problema de rendimiento, puede usar la vista para obtener más información sobre el mismo. Por ejemplo, puede obtener información acerca de cómo funciona cada tarea e información detallada acerca de cada tarea (lectura y escritura de datos, hora de programación/inicio/fin, etc.). Esta información se puede usar para ajustar las configuraciones de trabajo o la arquitectura del sistema en función de la información visualizada.

![Captura de pantalla de la ventana Vista de ejecución de tareas de Data Lake Tools para Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.task.execution.view.png "Vista de ejecución de tareas")

## <a name="run-hive-batch-jobs"></a>Ejecución de trabajos por lotes de Hive
Se puede tardar mucho en probar un script de Hive en un clúster de HDInsight (a excepción del clúster Interactive Query). Este proceso puede tardar varios minutos. Data Lake Tools para Visual Studio puede validar el script de Hive localmente sin conectarse a un clúster activo. Para más información sobre la ejecución de consultas interactivas, consulte [Ejecución de consultas de Hive interactivas](#run-interactive-hive-queries).

Data Lake Tools para Visual Studio puede usarse para ver el contenido del trabajo de Hive, para lo que recopila y expone los registros Yarn de determinados trabajos de Hive.

Para más información sobre cómo ejecutar trabajos por lotes de Hive, consulte [Ejecución de consultas de Hive interactivas](#run-interactive-hive-queries). La información de esa sección se aplica a la ejecución de trabajos por lotes de Hive que tardan más en ejecutarse.

## <a name="run-pig-scripts"></a>Ejecución de scripts de Pig
Data Lake Tools para Visual Studio se puede usar para crear y enviar scripts de Pig a clústeres de HDInsight. En primer lugar, cree un proyecto de Pig desde una plantilla. Luego, envíe el script a los clústeres de HDInsight.

## <a name="feedback-and-known-issues"></a>Comentarios y problemas conocidos
* Actualmente, los resultados de HiveServer2 se muestran en forma de texto sin formato, algo que está lejos de ser lo ideal. Microsoft está trabajando para solucionar este problema.
* Se ha corregido un problema en el que no se muestran los resultados que comienzan por valores null. Si está bloqueado debido a este problema, póngase en contacto con el equipo de soporte técnico.
* La codificación del script HQL que crea Visual Studio depende de la configuración regional del usuario. El script no se ejecuta correctamente si se carga en un clúster como un archivo binario.

## <a name="next-steps"></a>Pasos siguientes
En este artículo, ha aprendido a usar el paquete Data Lake Tools para Visual Studio para establecer conexión a los clústeres de HDInsight desde Visual Studio. También ha aprendido a ejecutar una consulta de Hive. Para obtener más información, consulte estos artículos:

* [Uso de Hive para Hadoop en HDInsight](hdinsight-use-hive.md)
* [Introducción al uso de Hadoop en HDInsight](apache-hadoop-linux-tutorial-get-started.md)
* [Envío de trabajos de Hadoop en HDInsight](submit-apache-hadoop-jobs-programmatically.md)
* [Análisis de datos de Twitter con Hadoop en HDInsight](../hdinsight-analyze-twitter-data.md)

