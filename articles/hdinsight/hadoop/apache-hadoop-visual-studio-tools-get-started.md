---
title: "Conexión a Azure HDInsight mediante Herramientas de Data Lake para Visual Studio | Microsoft Docs"
description: "Aprenda a instalar y utilizar Herramientas de Data Lake para Visual Studio para conectarse a clústeres de Hadoop en Azure HDInsight y ejecutar consultas de Hive."
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
ms.date: 11/27/2017
ms.author: jgao
ms.openlocfilehash: 1e9d5ca475424c99b30c62252f4b0abc9bd09078
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/28/2017
---
# <a name="connect-to-azure-hdinsight-and-run-hive-queries-using-data-lake-tools-for-visual-studio"></a>Conectarse a Azure HDInsight y ejecutar consultas de Hive con Herramientas de Data Lake para Visual Studio

Aprenda a usar Data Lake Tools para Visual Studio (también conocido como Azure Data Lake y Herramientas de Stream Analytics) para conectarse a clústeres de Hadoop en [Azure HDInsight](../hdinsight-hadoop-introduction.md) y enviar consultas de Hive. Para más información sobre el uso de HDInsight, consulte [Introducción a HDInsight](../hdinsight-hadoop-introduction.md) y [Primeros pasos con HDInsight](apache-hadoop-linux-tutorial-get-started.md). Para más información acerca de cómo conectarse a un clúster de Storm, consulte [Desarrollo de topologías de C# para Apache Storm en HDInsight con Visual Studio](../storm/apache-storm-develop-csharp-visual-studio-topology.md).

Data Lake Tools para Visual Studio se puede utilizar para acceder a Data Lake Analytics y a HDInsight.  Para más información sobre Data Lake Tools, consulte [Tutorial: Desarrollo de scripts U-SQL mediante Data Lake Tools para Visual Studio](../../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md).

**Requisitos previos**

Para completar este tutorial y usar Data Lake Tools en Visual Studio, necesita los elementos siguientes:

* Un clúster de Azure HDInsight: para crear uno, consulte [Introducción al uso de Hadoop en Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md). Para aprender a ejecutar consultas de Hive interactivas, necesita un clúster de [HDInsight Interactive Query](../interactive-query/apache-interactive-query-get-started.md).
* Una estación de trabajo con Visual Studio 2013/2015/2017.
    
    > [!NOTE]
    > Actualmente, las herramientas de Data Lake Tools para Visual Studio solo se incluyen en la versión en inglés.
    > 
    > 

## <a name="install-and-upgrade-data-lake-tools-for-visual-studio"></a>Instalación y actualización de Data Lake Tools para Visual Studio

Data Lake Tools está instalado de forma predeterminada para Visual Studio de 2017. Con versiones anteriores de Visual Studio, puede instalarlo mediante el [Instalador de plataforma web](https://www.microsoft.com/web/downloads/). Debe elegir el que coincida con su versión de Visual Studio. Si no tiene instalado Visual Studio, puede instalar el SDK de Azure y la instancia de Visual Studio Community más recientes mediante el [Instalador de plataforma web](https://www.microsoft.com/web/downloads/):

![Herramientas de Data Lake para Visual Studio: instalador de plataforma web.](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.wpi.png "Uso del instalador de plataforma web para instalar Herramientas de Data Lake para Visual Studio")

**Para actualizar las herramientas**
1. Abra Visual Studio.
2. En el menú **Herramientas**, haga clic en **Extensiones y actualizaciones**.
3. Expanda **Actualizaciones**, actualice **Azure Data Lake y Herramientas de Stream Analytics**, si hay alguno de estos componentes.

> [!NOTE]
>
> Solo la versión 2.3.0.0 o posterior admite la conexión a clústeres de Interactive Query y la ejecución de consultas de Hive interactivas.

## <a name="connect-to-azure-subscriptions"></a>Conexión a suscripciones de Azure
Data Lake Tools para Visual Studio permite conectarse a los clústeres de HDInsight, realizar algunas operaciones básicas de administración y ejecutar consultas de Hive.

> [!NOTE]
> Para más información acerca de cómo conectarse a un clúster de Hadoop genérico, consulte [How to write and submit Hive queries using Visual Studio](http://blogs.msdn.com/b/xiaoyong/archive/2015/05/04/how-to-write-and-submit-hive-queries-using-visual-studio.aspx)(Escritura y envío de consultas de Hive usando Visual Studio).
> 
> 

**Para conectarse a su suscripción de Azure**

1. Abra Visual Studio.
2. Desde el menú **Ver**, haga clic en **Explorador de servidores** para abrir la ventana del Explorador de servidores.
3. Expanda **Azure** y, después, haga lo mismo con **HDInsight**.
   
   > [!NOTE]
   > Debe abrirse la ventana **Lista de tareas de HDInsight** . Si no la ve, haga clic en **Otras ventanas** en el menú **Ver** y luego en **Ventana Lista de tareas de HDInsight**.  
   > 
   > 
4. Escriba sus credenciales de suscripción de Azure y, a continuación, haga clic en **Iniciar sesión**. La autenticación solo es necesaria si nunca se ha conectado a la suscripción de Azure desde Visual Studio en esta estación de trabajo.
5. En el Explorador de servidores, se mostrará una lista de los clústeres de HDInsight existentes. Si no tiene ningún clúster, puede crear uno mediante Azure Portal, Azure PowerShell o el SDK de HDInsight. Para más información, consulte [Creación de clústeres de Hadoop en HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).
   
   ![Explorador de servidores de Herramientas de Data Lake para Visual Studio: lista de clústeres](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.server.explorer.png "Explorador de servidores de Herramientas de Data Lake para Visual Studio")
6. Expanda un clúster de HDInsight. Verá las **bases de datos de Hive**, una cuenta de almacenamiento predeterminada, las cuentas de almacenamiento vinculadas y el **registro del servicio Hadoop**. Puede expandir las entidades.

Una vez conectado a su suscripción de Azure, podrá realizar las siguientes tareas:

**Para conectarse al Portal de Azure desde Visual Studio, siga estos pasos:**

* En el Explorador de servidores, expanda **Azure** > **HDInsight**, haga clic con el botón derecho en un clúster de HDInsight y, después, haga clic en **Administrar el clúster en Azure Portal**.

**Para hacer preguntas y proporcionar comentarios desde Visual Studio, siga estos pasos:**

* En el menú **Herramientas**, haga clic en **HDInsight** y, después, haga clic en **Foro de MSDN** para realizar preguntas o **Enviar comentarios**.

## <a name="navigate-the-linked-resources"></a>Navegación por los recursos vinculados
Desde el Explorador de servidores, podrá ver la cuenta de almacenamiento predeterminada y las cuentas de almacenamiento vinculadas. Expanda la cuenta de almacenamiento predeterminada para ver los contenedores en la cuenta de almacenamiento. Se marcarán la cuenta de almacenamiento predeterminada y el contenedor predeterminado. También puede hacer clic con el botón derecho en cualquiera de los contenedores para ver el contenido.

![Explorador de servidores de Herramientas de Data Lake para Visual Studio: lista de recursos vinculados](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.linked.resources.png "lista de recursos vinculados")

Después de abrir un contenedor, puede usar los siguientes botones para cargar, eliminar y descargar blobs:

![Explorador de servidores de Herramientas de Data Lake para Visual Studio: operaciones de blob](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.blob.operations.png "cargar, eliminar y descargar blobs")

## <a name="run-interactive-hive-queries"></a>Ejecución de consultas de Hive interactivas
[Apache Hive](http://hive.apache.org) es una infraestructura de almacenamiento de datos basada en Hadoop para proporcionar resúmenes de datos, consultas y análisis. Data Lake Tools para Visual Studio admite la ejecución de consultas de Hive desde Visual Studio. Para más información sobre Hive, consulte [Uso de Hive con HDInsight](hdinsight-use-hive.md).

[Interactive Query](../interactive-query/apache-interactive-query-get-started.md) aprovecha [Hive en LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) en Apache Hive 2.1 para llevar la interactividad a las consultas complejas de estilo almacén de datos en grandes conjuntos de datos almacenados. La ejecución de consultas de Hive en Interactive Query es mucho más rápida en comparación con los trabajos por lotes tradicionales de Hive.  Para más información sobre cómo ejecutar trabajos por lotes de Hive, consulte [Ejecución de trabajos por lotes de Hive](#run-hive-batch-jobs).

> [!note]
>
> Solo se admite la ejecución de consultas de Hive interactivas cuando se conecta a un clúster de [HDInsight Interactive Query](../interactive-query/apache-interactive-query-get-started.md).

Data Lake Tools para Visual Studio también permite a los usuarios ver lo que hay dentro del trabajo de Hive al recopilar y exponer los registros de Yarn de determinados trabajos de Hive.

### <a name="view-the-hivesampletable"></a>Visualización de la tabla **hivesampletable Hive**
Todos los clústeres de HDInsight incluyen una tabla Hive de ejemplo denominada *hivesampletable*. Esta tabla de Hive se usa para mostrar cómo enumerar las tablas de Hive, ver los esquemas de tabla y mostrar las filas de la tabla de Hive.

**Para mostrar las tablas de Hive y ver el esquema de la tabla de Hive, siga estos pasos:**

1. En el **Explorador de servidores**, expanda **Azure** > **HDInsight** > el clúster de su elección > **Bases de datos de Hive** > **Predeterminada** > **hivesampletable** para ver el esquema de la tabla.
2. Haga clic con el botón derecho en **hivesampletable** y, después, haga clic en **Ver las primeras 100 filas** para mostrar las filas. Esta operación equivale a ejecutar la siguiente consulta de Hive usando el controlador ODBC de Hive:
   
     SELECT * FROM hivesampletable LIMIT 100
   
   Puede personalizar el recuento de filas.
   
   ![Herramientas de Data Lake para Visual Studio en HDInsight: consulta del esquema de Hive](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.hive.schema.png "Resultados de la consulta de Hive")

### <a name="create-hive-tables"></a>Crear tablas de Hive
Puede usar la interfaz gráfica de usuario para crear una tabla de Hive o usar las consultas de Hive. Para obtener información acerca del uso de consultas de Hive, consulte [Ejecución de consultas de Hive](#run.queries).

**Para crear una tabla de Hive, siga estos pasos:**

1. En el **Explorador de servidores**, expanda **Azure** > **Clústeres de HDInsight** un clúster de HDInsight > **Bases de datos de Hive**, haga clic con el botón derecho en **Predeterminada** y, finalmente, haga clic en **Crear tabla**.
2. Configure la tabla.
3. Haga clic en **Crear tabla** para enviar el trabajo de creación de la nueva tabla de Hive.
   
    ![Herramientas de Data Lake para Visual Studio en HDInsight: creación de tablas de Hive](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.create.hive.table.png "Creación de tablas de Hive")

### <a name="run.queries"></a>Validación y ejecución de consultas de Hive
Existen dos formas de crear y ejecutar consultas de Hive:

* Creación de consultas ad hoc
* Crear una aplicación de Hive

**Para crear, validar y ejecutar consultas ad hoc, siga estos pasos:**

1. En el **Explorador de servidores**, expanda **Azure** y luego expanda **Clústeres de HDInsight**.
2. Haga clic con el botón derecho en el clúster donde desea ejecutar la consulta y, a continuación, haga clic en **Escribir una consulta de Hive**.
3. Escriba las consultas de Hive. Observe que el editor de Hive es compatible con Intellisense. Data Lake Tools para Visual Studio es compatible con la carga de metadatos remotos cuando se edita un script de Hive. Por ejemplo, al escribir "SELECT * FROM", IntelliSense mostrará todos los nombres de tabla sugeridos. Cuando se especifica un nombre de tabla, IntelliSense muestra los nombres de columna. La herramienta admite casi todas las instrucciones DML Hive, subconsultas y las UDF integradas.
   
    ![Herramientas de Data Lake para Visual Studio en HDInsight: IntelliSense](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.intellisense.table.names.png "IntelliSense en U-SQL")
   
    ![Herramientas de Data Lake para Visual Studio en HDInsight: IntelliSense](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.intellisense.column.names.png "IntelliSense en U-SQL")
   
   > [!NOTE]
   > Solo se sugieren los metadatos del clúster que se selecciona en la barra de herramientas de HDInsight.
   > 
   > 
4. (Opcional) Haga clic en **Validar script** para comprobar los errores de sintaxis del script.
   
    ![Herramientas de Data Lake para Visual Studio: validación local](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.validate.hive.script.png "Validación de scripts")
5. Haga clic en **Enviar** o en **Enviar (avanzado)**. Con la opción de envío avanzado, configurará el **nombre del trabajo**, los **argumentos**, las **configuraciones adicionales** y el **estado de directorio** del script:
   
    ![Consulta de Hive para Hadoop en HDInsight](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.submit.jobs.advanced.png "Envío de consultas")
   
    Una vez enviado el trabajo, se mostrará la ventana **Resumen del trabajo de Hive** .
   
    ![Resumen de una consulta de Hive para Hadoop en HDInsight](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.run.hive.job.summary.png "Resumen de un trabajo de Hive")
6. Utilice el botón **Actualizar** para actualizar el estado hasta que el estado del trabajo cambie a **Completado**.
7. Haga clic en los vínculos de la parte inferior para ver la **consulta del trabajo**, la **salida del trabajo**, el **registro de trabajo** o el **registro Yarn**.

**Para crear y ejecutar una solución de Hive, siga estos pasos:**

1. En el menú **ARCHIVO**, haga clic en **Nuevo** y en **Proyecto**.
2. Seleccione **HDInsight** en el panel izquierdo, seleccione **Aplicación de Hive** en el panel central, especifique las propiedades y, finalmente, haga clic en **Aceptar**.
   
    ![Herramientas de Data Lake para Visual Studio en HDInsight: nuevo proyecto de Hive](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.new.hive.project.png "Creación de aplicaciones de Hive desde Visual Studio")
3. En el **Explorador de soluciones**, haga doble clic en **Script.hql** para abrirlo.
4. Para validar el script de Hive, haga clic en el botón **Validar script** o haga clic con el botón derecho en el script del editor de Hive y, finalmente, haga clic en **Validar script** en el menú contextual.

### <a name="view-hive-jobs"></a>Ver trabajos de Hive
Puede ver consultas de trabajo, salidas de trabajo, registros de trabajo y registros Yarn para trabajos de Hive. Para obtener más información, consulte la captura de pantalla anterior.

La versión más reciente de la herramienta permite ver el contenido de los trabajos de Hive al recopilar y exponer los registros Yarn. El registro Yarn puede ayudarle a investigar problemas de rendimiento. Para más información acerca de cómo HDInsight recopila registros YARN, consulte [Acceso a registros de aplicación de HDInsight mediante programación](../hdinsight-hadoop-access-yarn-app-logs.md).

**Para ver los trabajos de Hive, siga estos pasos:**

1. En el **Explorador de servidores**, expanda **Azure** y, después, expanda **HDInsight**.
2. Haga clic con el botón derecho en un clúster de HDInsight y luego haga clic en **Ver trabajos**. Se mostrará una lista de los trabajos de Hive que se ejecutaron en el clúster.
3. Haga clic en un trabajo de la lista de trabajos para seleccionarlo y después utilice la ventana **Resumen de trabajo de Hive** para abrir la **consulta del trabajo**, la **salida del trabajo**, el **registro de trabajo** o el **registro Yarn**.
   
    ![Herramientas de Data Lake para Visual Studio en HDInsight: visualización de trabajos de Hive](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.view.hive.jobs.png "Visualización de trabajos de Hive")

### <a name="faster-path-hive-execution-via-hiveserver2"></a>Ejecución de Hive más rápida mediante HiveServer2
> [!NOTE]
> Este tutorial solo funciona en la versión 3.2 y posteriores del clúster de HDInsight.
> 
> 

Data Lake Tools se usan para enviar trabajos de Hive mediante [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (denominado también Templeton). Tardó mucho tiempo en devolver los detalles del trabajo y la información de error.
Para resolver este problema de rendimiento, Data Lake Tools ejecuta trabajos de Hive directamente en el clúster mediante HiveServer2, de modo que omite RDP o SSH.
Además de mejorar el rendimiento, los usuarios también pueden ver Hive en gráficos Tez y los detalles de la tarea.

En el caso del clúster de HDInsight versión 3.2 o posterior, puede ver el botón **Ejecutar mediante HiveServer2**:

![Herramientas de Data Lake para Visual Studio: ejecución con hiveserver2](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.execute.via.hiveserver2.png "Ejecución de consultas de Hive con HiveServer2")

Y puede ver los registros transmitidos de vuelta en tiempo real y ver los gráficos del trabajo si se ejecuta la consulta de Hive en Tez.

![Herramientas de Data Lake para Visual Studio en HDInsight: ejecución de Hive de ruta de acceso rápida](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.fast.path.hive.execution.png "Visualización de gráficos de trabajo")

**Diferencia entre la ejecución de consultas a través de HiveServer2 y el envío de consultas a través de WebHCat**

Aunque la ejecución de consultas a través de HiveServer2 ofrece muchas ventajas de rendimiento, tiene varias limitaciones. Algunas de las limitaciones no son adecuadas para el uso de producción. En la tabla siguiente se muestran las diferencias:

|  | Ejecución a través de HiveServer2 | Envío a través de WebHCat |
| --- | --- | --- |
| Ejecución de consultas |Elimina la sobrecarga de WebHCat (que inicia un trabajo MapReduce con el nombre "TempletonControllerJob"). |Siempre que una consulta se ejecute a través de WebHCat, dicha aplicación inicia un trabajo de MapReduce que introduce latencia adicional. |
| Registros inversos |Casi en tiempo real. |Los registros de ejecución de trabajo solo están disponibles cuando finalice el trabajo. |
| Visualización del historial de trabajos |Si una consulta se ejecuta a través de HiveServer2, no se conserva su historial de trabajos (registro de trabajo, salida de trabajo). La aplicación puede verse en la interfaz de usuario de YARN con información limitada. |Si una consulta se ejecuta mediante WebHCat, su historial de trabajos (registro de trabajo, salida de trabajo) se conserva y se puede ver con Visual Studio, el SDK de HDInsight o PowerShell. |
| Cierre de ventana |La ejecución a través de HiveServer2 se realiza de forma "sincrónica" por lo que deben mantenerse las ventanas abiertas; si se cierran las ventanas, se cancela la ejecución de consultas. |El envío a través de WebHCat se realiza de forma "asincrónica" por lo que puede enviar la consulta a través de WebHCat y cerrar Visual Studio. Puede regresar y ver los resultados en cualquier momento. |

### <a name="tez-hive-job-performance-graph"></a>Gráfico de rendimiento de trabajos de Hive de Tez
Data Lake Tools admite que se muestren gráficos de rendimiento de los trabajos de Hive que ejecuta el motor de ejecución de Tez. Para más información sobre cómo habilitar Tez, consulte [Uso de Hive en HDInsight](hdinsight-use-hive.md). Después de enviar un trabajo de Hive a Visual Studio, Visual Studio mostrará el gráfico de dicho trabajo cuando se complete.  Es posible que tenga que hacer clic en el botón **Actualizar** para ver el estado del trabajo más reciente.

> [!NOTE]
> Esta característica solo está disponible en las versiones del clúster de HDInsight posteriores a la 3.2.4.593 y solo puede funcionar con trabajos completados (si ha enviado el trabajo a través de WebHCat, este gráfico se muestra al ejecutar una consulta a través de HiveServer2). 
> 
> 

![Gráfico de rendimiento de Tez de Hive para Hadoop](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.hive.tez.performance.graph.png "Estado del trabajo")

Para ayudarle a entender mejor la consulta de Hive, la herramienta agrega la vista de operadores de Hive en esta versión. Basta con que haga doble clic en los vértices del gráfico de trabajo para ver todos los operadores dentro de un vértice. También puede mantener el puntero sobre un operador determinado, para ver más detalles del operador.

### <a name="task-execution-view-for-hive-on-tez-jobs"></a>Vista de ejecución de tareas de Hive en trabajos Tez
La vista de ejecución de tareas de Hive en trabajos Tez puede usarse para obtener información estructurada y visualizada de los trabajos de Hive y obtener más detalles del trabajo. Cuando hay problemas de rendimiento, puede usar la vista para obtener más información. Por ejemplo, cómo funciona cada tarea y la información detallada sobre cada tarea (lectura y escritura de datos, programación y hora de inicio y fin, etc.), por lo que puede ajustar configuraciones de trabajo o la arquitectura del sistema según la información mostrada.

![Herramientas de Data Lake para Visual Studio: vista de ejecución](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.task.execution.view.png "Vista de ejecución de tareas")

## <a name="run-hive-batch-jobs"></a>Ejecución de trabajos por lotes de Hive
[Apache Hive](http://hive.apache.org) es una infraestructura de almacenamiento de datos basada en Hadoop para proporcionar resúmenes de datos, consultas y análisis. Data Lake Tools para Visual Studio admite la ejecución de consultas de Hive desde Visual Studio. Para más información sobre Hive, consulte [Uso de Hive con HDInsight](hdinsight-use-hive.md).

Se tarda mucho en probar el script de Hive contra un clúster de HDInsight, a excepción del clúster Interactive Query. Puede tardar varios minutos o más tiempo. Data Lake Tools para Visual Studio puede validar el script de Hive localmente sin conectarse a un clúster activo. Para más información sobre la ejecución de consultas interactivas, consulte [Ejecución de consultas de Hive interactivas](#run-interactive-hive-queries).

Data Lake Tools para Visual Studio también permite a los usuarios ver lo que hay dentro del trabajo de Hive al recopilar y exponer los registros de Yarn de determinados trabajos de Hive.

Para más información sobre cómo ejecutar trabajos por lotes de Hive, consulte la sección [Ejecución de trabajos por lotes de Hive](#run-interactive-hive-queries). La información de esa sección se aplica a la ejecución de trabajos por lotes de Hive con un tiempo más largo.

## <a name="run-pig-scripts"></a>Ejecución de scripts de Pig
Data Lake Tools para Visual Studio admite la creación y el envío de scripts de Pig a clústeres de HDInsight. Los usuarios pueden crear un proyecto de Pig desde una plantilla y, a continuación, enviar el scripts a los clústeres de HDInsight.

## <a name="feedbacks--known-issues"></a>Comentarios y problemas conocidos
* Actualmente, los resultados de HiveServer2 se muestran en forma de texto puro, un formato que no es conveniente. Microsoft está trabajando para corregirlo.
* Si los resultados comienzan con valores NULL, no se muestran. Este problema se ha corregido, así que si no consigue solucionarlo, póngase en contacto con el soporte técnico.
* La codificación del script de HQL creado con Visual Studio depende de la configuración regional del usuario. Si el usuario carga el script en el clúster como binario, es posible que no se ejecute correctamente.

## <a name="next-steps"></a>Pasos siguientes
En este artículo, ha aprendido a conectarse a clústeres de HDInsight desde Visual Studio mediante el paquete de herramientas de Data Lake (HDInsight) y a ejecutar una consulta de Hive. Para más información, consulte:

* [Uso de Hive para Hadoop en HDInsight](hdinsight-use-hive.md)
* [Introducción al uso de Hadoop en HDInsight](apache-hadoop-linux-tutorial-get-started.md)
* [Envío de trabajos de Hadoop en HDInsight](submit-apache-hadoop-jobs-programmatically.md)
* [Análisis de datos de Twitter con Hadoop en HDInsight](../hdinsight-analyze-twitter-data.md)

