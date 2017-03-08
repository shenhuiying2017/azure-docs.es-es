---
title: "Tutorial de Apache Storm: Introducción a Storm | Microsoft Docs"
description: "Introducción al análisis de grandes cantidades de datos con Apache Storm y muestras de inicio de Storm en HDInsight. Aprenda a usar Storm para procesar datos en tiempo real."
keywords: "Storm de Apache, tutorial de Storm de Apache, análisis de macrodatos, inicio de Storm"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 6f93f7ff-0736-4708-80ef-4289dae532a9
ms.service: hdinsight
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/28/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: cfaade8249a643b77f3d7fdf466eb5ba38143f18
ms.openlocfilehash: e505d02895abd011661b3e4f66c7f4f7ea042358
ms.lasthandoff: 03/01/2017

---
# <a name="get-started-with-the-storm-starter-samples-for-big-data-analytics-on-hdinsight"></a>Introducción a los ejemplos de Storm Starter para analizar macrodatos en HDInsight

Apache Storm es un sistema de cálculo distribuido, escalable, con tolerancia a errores y en tiempo real para el procesamiento de secuencias de datos. Con Storm en HDInsight de Microsoft Azure, puede crear un clúster de Storm basado en la nube que realice análisis de macrodatos en tiempo real. 

> [!IMPORTANT]
> En los pasos de este artículo se crea un clúster de HDInsight basado en Windows. HDInsight solo está disponible en Windows para versiones inferiores a HDInsight 3.4. Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las superiores. Para más información, consulte [El contrato de nivel de servicio para las versiones de clúster de HDInsight](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).
>
> Para conocer los pasos para crear un clúster Storm basado en Linux en HDInsight, consulte [Tutorial de Apache Storm: Introducción al ejemplo de inicio de Storm mediante el análisis de datos en HDInsight](hdinsight-apache-storm-tutorial-get-started-linux.md)

## <a name="prerequisites"></a>Requisitos previos
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Debe cumplir los siguientes requisitos previos para poder completar correctamente este tutorial sobre Apache Storm.

* **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

### <a name="access-control-requirements"></a>Requisitos de control de acceso
[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-a-storm-cluster"></a>Creación de un clúster de Storm

Siga estos pasos para crear un clúster de Storm en HDInsight:

1. En [Azure Portal](https://portal.azure.com), seleccione **+ NUEVOE**, **Inteligencia y análisis** y luego seleccione **HDInsight**.
   
    ![Creación de un clúster de HDInsight](./media/hdinsight-apache-storm-tutorial-get-started/create-hdinsight.png)

2. Introduzca la siguiente información de la hoja **Básico**:

    * **Nombre del clúster**: nombre del clúster de HDInsight.
    * **Suscripción**: seleccione la suscripción que vaya a usar.
    * **Nombre de usuario de inicio de sesión del clúster** y **contraseña de inicio de sesión de clúster**: inicio de sesión de acceso al clúster a través de HTTPS. Use estas credenciales para acceder a servicios como la interfaz de usuario de Ambari Web o la API de REST.
    * **Nombre de usuario de Secure Shell (SSH)**: deje el valor predeterminado de estos campos. No se usan para los clústeres de HDInsight basados en Windows.
    * **Grupo de recursos**: en el que se va a crear el clúster.
    * **Ubicación**: región de Azure donde se va crear el clúster.
   
    ![Seleccione la suscripción.](./media/hdinsight-apache-storm-tutorial-get-started/hdinsight-basic-configuration.png)

3. Seleccione **Tipo de clúster** y establezca los siguientes valores en la hoja **Configuración de clúster**:
   
    * **Tipo de clúster**: Storm

    * **Sistema operativo**: Windows

    * **Versión**: Storm 0.10.0 (HDI 3.3)

        > [!NOTE]
        > La versión 3.4 de HDInsight y posteriores solo están disponibles con el sistema operativo Linux.

    * **Nivel de clúster**: estándar
     
    Por último, use el botón **Seleccionar** para guardar la configuración.
     
    ![Seleccionar el tipo de clúster](./media/hdinsight-apache-storm-tutorial-get-started/set-hdinsight-cluster-type.png)

4. Después de seleccionar el tipo de clúster, use el botón __Seleccionar__ para establecer el tipo de clúster. A continuación, use el botón __Siguiente__ para finalizar la configuración básica.

5. Desde la hoja **Storage**, seleccione o cree una cuenta de Storage. Para seguir los pasos de este documento, deje los demás campos de esta hoja con los valores predeterminados. Use el botón __Siguiente__ para guardar la configuración de almacenamiento.

    ![Configuración de la cuenta de almacenamiento de HDInsight](./media/hdinsight-apache-storm-tutorial-get-started/set-hdinsight-storage-account.png)

6. En la hoja **Resumen**, revise la configuración para el clúster. Use los vínculos __Edit__ (Editar) para cambiar cualquier configuración incorrecta. Por último, use el botón __Crear__ para crear el clúster.
   
    ![Resumen de configuración del clúster](./media/hdinsight-apache-storm-tutorial-get-started/hdinsight-configuration-summary.png)
   
    > [!NOTE]
    > Un clúster puede tardar hasta 20 minutos en crearse.

## <a name="run-a-storm-starter-sample-on-hdinsight"></a>Ejecución de una muestra de inicio de Storm en HDInsight
Este tutorial de Apache Storm le sirve como introducción al análisis de grandes cantidades de datos con las muestras de inicio de Storm en GitHub.

Cada clúster de Storm en HDInsight incluye el panel Storm, que puede usarse para cargar y ejecutar topologías de Storm en el clúster. Cada clúster incluye también las topologías de muestra que se pueden ejecutar directamente desde el panel Storm.

### <a id="connect"></a>Conexión con el panel
El panel se encuentra **https://&lt;clustername>.azurehdinsight.net//**, donde **clustername** es el nombre del clúster. Si lo desea, también puede utilizar un vínculo al panel. Para ello, seleccione el clúster en el Panel de inicio y haga clic en el vínculo **Panel** que encontrará en la parte superior de la hoja.

![El portal de Azure con el vínculo al panel Storm](./media/hdinsight-apache-storm-tutorial-get-started/dashboard.png)

> [!NOTE]
> Al conectarse con el panel, se le pide que escriba un nombre de usuario y una contraseña. Se trata del nombre del administrador (**admin**) y la contraseña que usó al crear el clúster.
> 
> 

Cuando se haya cargado el panel Storm, verá el formulario **Enviar topología** .

![Envíe su topología de inicio de Storm con el panel de Storm.](./media/hdinsight-apache-storm-tutorial-get-started/submit.png)

El formulario **Enviar topología** sirve para cargar y ejecutar archivos .jar que contienen topologías de Storm. También incluye varias muestras básicas que se ofrecen con el clúster.

### <a id="run"></a>Ejecución de la muestra de recuento de palabras desde el proyecto de inicio de Storm en GitHub
Las muestras que se facilitan con el clúster incluyen ciertas variaciones de una topología de recuento de palabras. En estos ejemplos se incluye un **spout** que emite oraciones de forma aleatoria, así como **bolts** que dividen cada frase en palabras y después cuentan el número de veces que aparece cada palabra. Estos ejemplos proceden de los [Ejemplos de inicio de Storm](https://github.com/apache/storm/tree/master/examples/storm-starter), que forman parte de Apache Storm.

Siga estos pasos para ejecutar una muestra de inicio de Storm:

1. Seleccione **StormStarter - WordCount** en la lista desplegable **Archivo del producto**. De este modo, los campos **Nombre de clase** y **Parámetros adicionales** se rellenan con los parámetros del ejemplo.
   
    ![WordCount de inicio de Storm seleccionada en el panel de Storm.](./media/hdinsight-apache-storm-tutorial-get-started/submit.png)
   
   * **Nombre de la clase** : la clase en el archivo .jar que envía la topología.
   * **Parámetros adicionales** : los parámetros que requiere la topología. En este ejemplo, el campo se usa para proporcionar un nombre descriptivo para la topología enviada.
2. Haga clic en **Enviar**. Tras unos instantes, el campo **Resultado** muestra el comando usado para enviar el trabajo, así como los resultados del comando. El campo **Error** muestra los errores que se producen en el envío de la topología.
   
    ![Botón Enviar y resultados de WordCount de inicio de Storm.](./media/hdinsight-apache-storm-tutorial-get-started/submit-results.png)
   
   > [!NOTE]
   > Los resultados no indican que haya finalizado la topología: **una topología Storm, una vez iniciada, se ejecuta hasta que la detenga.** La topología de recuento de palabras genera frases aleatorias y mantiene un recuento del número de veces que encuentra cada palabra hasta que la detenga.
   > 
   > 

### <a id="monitor"></a>Supervisión de la topología
La interfaz de usuario de Storm se puede usar para supervisar la topología.

1. Seleccione **IU de Storm** en la parte superior del panel de Storm. Se muestra información de resumen del clúster y de las topologías en ejecución.
   
    ![Panel de Storm en el que se muestra el resumen de topología de WordCount de inicio de Starter.](./media/hdinsight-apache-storm-tutorial-get-started/stormui.png)
   
    En la página anterior, puede ver el tiempo que ha estado activa la topología, así como el número de trabajos, ejecutores y tareas que se usan.
   
   > [!NOTE]
   > La columna **Nombre** contiene el nombre descriptivo que se proporcionó anteriormente mediante el campo **Parámetros adicionales**.
   > 
   > 
2. En el **resumen de la topología**, seleccione la entrada **wordcount** de la columna **Nombre**. Se muestra más información sobre la topología.
   
    ![Panel de Storm con la información de topología de WordCount de inicio de Starter.](./media/hdinsight-apache-storm-tutorial-get-started/topology-summary.png)
   
    Esta página ofrece la siguiente información:
   
   * **Estadísticas de topología** : información básica sobre el rendimiento de la topología, organizada en ventanas de tiempo.
     
     > [!NOTE]
     > Al seleccionar una ventana de tiempo específica, se cambia la ventana de tiempo de la información que aparece en otras secciones de la página.
     > 
     > 
   * **Spouts** : información básica sobre spouts, entre la que se incluye el último error que ha devuelto cada spout.
   * **Bolts** : información básica sobre bolts.
   * **Configuración de la topología** : información detallada sobre la configuración de la topología.
     
     Esta página también ofrece acciones que se pueden realizar en la topología:
   * **Activar** : reanuda el procesamiento de una topología desactivada.
   * **Desactivar** : pausa una topología en ejecución.
   * **Reequilibrar** : ajusta el paralelismo de la topología. Debe volver a equilibrar las topologías en ejecución después de haber cambiado el número de nodos del clúster. Esto permite que la topología ajuste el paralelismo para compensar el mayor o menor número de nodos del clúster. Para más información, consulte la entrada de blog [Understanding the parallelism of a Storm topology](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).
   * **Eliminar** : finaliza una topología de Storm tras el tiempo de espera especificado.
3. En esta página, seleccione una entrada en la sección **Spouts** o **Bolts**. Se muestra información sobre el componente seleccionado.
   
    ![Panel de Storm con información acerca de los componentes seleccionados.](./media/hdinsight-apache-storm-tutorial-get-started/component-summary.png)
   
    En esta página se muestra la siguiente información:
   
   * **Estadísticas de spouts y bolts** : información básica sobre el rendimiento de los componentes, organizada en ventanas de tiempo.
     
     > [!NOTE]
     > Al seleccionar una ventana de tiempo específica, se cambia la ventana de tiempo de la información que aparece en otras secciones de la página.
     > 
     > 
   * **Estadísticas de entrada** (solo bolt): información sobre los componentes que generan los datos que consume el bolt.
   * **Estadísticas de salida** : información sobre los datos que emite este bolt.
   * **Ejecutores** : información sobre las instancias de este componente.
   * **Errores** : errores que ha generado este componente.
4. Al ver los detalles de un spout o bolt, seleccione una entrada de la columna **Puerto** en la sección **Ejecutores** para ver los detalles de una instancia específica del componente.
   
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["with"]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["nature"]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [snow]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [snow, 747293]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [white]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [white, 747293]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [seven]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [seven, 1493957]
   
    A partir de estos datos, puede ver que la palabra **seven** se ha repetido 1 493 957 veces. Ese es el número de veces que se ha encontrado desde que se inició esta topología.

### <a name="stop-the-topology"></a>Detención de la topología
Vuelva a la página **Topology summary** (Resumen de la topología) de la topología de recuento de palabras y seleccione **Kill** (Terminar) en la sección **Topology actions** (Acciones de topología). Cuando se le solicite, escriba 10 como los segundos de espera antes de detener la topología. Tras el período de tiempo de espera, ya no aparece la topología cuando visita la sección **IU de Storm** del panel.

## <a name="delete-the-cluster"></a>Eliminación del clúster
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="summary"></a>Resumen
En este tutorial de Apache Storm, usó el inicio de Storm para aprender a crear un clúster de Storm en HDInsight y usar el panel de Storm para implementar, supervisar y administrar topologías de Storm.

## <a id="next"></a>Pasos siguientes
* **Herramientas de HDInsight para Visual Studio** : las herramientas de HDInsight le permiten usar Visual Studio para enviar, supervisar y administrar topologías Storm de forma similar al panel Storm mencionado anteriormente. Las herramientas de HDInsight también ofrecen la posibilidad de crear topologías de Storm en C# e incluyen topologías de muestra que puede implementar y ejecutar en el clúster.
  
    Para obtener más información, consulte [Introducción al uso de las herramientas de HDInsight para Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md).
* **Archivos de muestra**: el clúster Storm de HDInsight dispone de varios ejemplos en el directorio **%STORM_HOME%\contrib**. Cada ejemplo debe contener lo siguiente:
  
  * El código fuente, por ejemplo, storm-starter-0.9.1.2.1.5.0-2057-sources.jar
  * Los documentos de Java, por ejemplo, storm-starter-0.9.1.2.1.5.0-2057-javadoc.jar
  * El ejemplo, por ejemplo, storm-starter-0.9.1.2.1.5.0-2057-jar-with-dependencies.jar
    
    Use el comando 'jar' para extraer el código fuente o los documentos de Java. Por ejemplo, 'jar -xvf storm-starter-0.9.1.2.1.5.0.2057-javadoc.jar'.
    
    > [!NOTE]
    > Los documentos de Java constan de páginas web. Una vez extraídos, use un explorador para ver el archivo **index.html** .
    > 
    > 
    
    Para obtener acceso a estos ejemplos, debe habilitar Escritorio remoto en la instancia de Storm del clúster de HDInsight y copiar los archivos desde **%STORM_HOME%\contrib**.
* El documento siguiente contiene una lista de otros ejemplos que se pueden usar con Storm en HDInsight:
  
  * [Topologías de ejemplo para Storm en HDInsight](hdinsight-storm-example-topology.md)

[apachestorm]: https://storm.incubator.apache.org
[stormdocs]: http://storm.incubator.apache.org/documentation/Documentation.html
[stormstarter]: https://github.com/apache/storm/tree/master/examples/storm-starter
[stormjavadocs]: https://storm.incubator.apache.org/apidocs/
[azureportal]: https://manage.windowsazure.com/
[hdinsight-provision]: hdinsight-provision-clusters.md
[preview-portal]: https://portal.azure.com/

