<properties
	pageTitle="Tutorial de Apache Storm: Introducción a Storm | Microsoft Azure"
	description="Introducción al análisis de grandes cantidades de datos con Apache Storm y muestras de inicio de Storm en HDInsight. Aprenda a usar Storm para procesar datos en tiempo real."
	keywords="apache storm,apache storm tutorial,big data analytics,storm starter"
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/06/2015"
   ms.author="larryfr"/>


# Tutorial de Apache Storm: introducción a las muestras de Inicio de Storm para análisis de grandes cantidades de datos en HDInsight

Apache Storm es un sistema de cálculo distribuido, escalable, con tolerancia a errores y en tiempo real para el procesamiento de secuencias de datos. Con Storm en HDInsight de Azure, puede crear un clúster de Storm basado en la nube que realice análisis en tiempo real de grandes cantidades de datos en tiempo real.

## Antes de empezar

Debe cumplir los siguientes requisitos previos para poder completar correctamente este tutorial sobre Apache Storm.

- **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## Crear una cuenta de Almacenamiento de Azure

Storm en HDInsight usa el almacenamiento de blobs de Azure para almacenar archivos de registro y topologías enviadas al clúster. Siga estos pasos para crear una cuenta de almacenamiento de Azure para usarla con el clúster:

1. Inicie sesión en el [Portal de Azure](http://manage.windowsazure.com/).

2. Haga clic en **NUEVO** en la esquina inferior izquierda, seleccione **SERVICIOS DE DATOS**, **ALMACENAMIENTO** y haga clic en **CREACIÓN RÁPIDA**.

	![El Portal de Azure, donde puede usar la función de creación rápida para configurar una nueva cuenta de almacenamiento.](./media/hdinsight-apache-storm-tutorial-get-started/HDI.StorageAccount.QuickCreate.png)

3. Escriba información sobre la **DIRECCIÓN URL**, **UBICACIÓN** y **REPLICACIÓN** y, después, haga clic en **CREAR CUENTA DE ALMACENAMIENTO**. No seleccione un grupo de afinidad al crear almacenamiento para HDInsight. La nueva cuenta de almacenamiento aparecerá en la lista de almacenamiento.

	>[AZURE.NOTE]La opción de creación rápida para aprovisionar un clúster de HDInsight, como el que se usa en este tutorial, no solicita una ubicación cuando se aprovisiona el clúster. En su lugar, coloca de forma predeterminada el clúster en el mismo centro de datos en que se encuentra la cuenta de almacenamiento. Por lo tanto, asegúrese de crear la cuenta de almacenamiento en las ubicaciones que el clúster admite, que son: **Este de Asia**, **Sudeste Asiático**, **Norte de Europa**, **Oeste de Europa**, **Este de EE. UU.**, **Oeste de EE. UU.**, **Centro y norte de EE. UU.**, **Centro y sur de EE. UU**.

4. Espere hasta que la característica **ESTADO** de la nueva cuenta de almacenamiento cambie a **En línea**.

Para obtener información sobre la creación de cuentas de almacenamiento, consulte [Creación de una cuenta de almacenamiento](../storage/storage-create-storage-account.md).

##Aprovisionamiento de un clúster de Storm en el portal de Azure

Cuando aprovisiona un clúster de HDInsight, aprovisiona recursos de proceso de Azure que contienen aplicaciones de Apache Storm y otras relacionadas. También puede crear clústeres de Hadoop para otras versiones mediante el portal de Azure, los cmdlets de PowerShell de Azure o el SDK .NET de HDInsight. Para obtener instrucciones, consulte [Aprovisionamiento de clústeres de HDInsight usando opciones personalizadas][hdinsight-provision]. Para obtener información sobre las diferentes versiones de HDInsight y sus contratos de nivel de servicio (SLA), consulte la página [Control de versiones de componentes de HDInsight](hdinsight-component-versioning.md).

[AZURE.INCLUDE [provisioningnote](../../includes/hdinsight-provisioning.md)]

1. Inicie sesión en el [Portal de Azure][azureportal].

2. Haga clic en **HDInsight** a la izquierda y, después, en **+NUEVO** en la esquina inferior izquierda de la página.

3. Haga clic en el icono de HDInsight en la segunda columna y seleccione **STORM**.

	![Creación rápida](./media/hdinsight-apache-storm-tutorial-get-started/quickcreate.png)

4. Escriba un nombre de clúster único y escriba una contraseña única para la cuenta de administrador. En **CUENTA DE ALMACENAMIENTO**, seleccione la cuenta de almacenamiento creada anteriormente.

	En **TAMAÑO DE CLÚSTER**, seleccione un tamaño de **1 nodo de datos** que se usará para este clúster. Es así para minimizar el costo asociado con el clúster. Para su uso en producción, debe crear un clúster de mayor tamaño.

	> [AZURE.NOTE]La cuenta de administrador del clúster se denomina **admin**. La contraseña que escriba es la contraseña para esta cuenta. Necesitará esta información para realizar acciones con el clúster, como enviar o administrar topologías de Storm.

5. Por último, seleccione la marca de verificación junto a **CREAR CLÚSTER DE HDINSIGHT** para crear el clúster.

> [AZURE.NOTE]El aprovisionamiento del clúster tarda algo de tiempo (normalmente menos de 15 minutos) en crear el clúster, configurar el software e instalar las topologías y los datos de muestra.

##Ejecución de una muestra de inicio de Storm en HDInsight

Este tutorial de Apache Storm le sirve como introducción al análisis de grandes cantidades de datos con las muestras de inicio de Storm en GitHub.

Cada clúster de Storm en HDInsight incluye el panel Storm, que puede usarse para cargar y ejecutar topologías de Storm en el clúster. Cada clúster incluye también las topologías de muestra que se pueden ejecutar directamente desde el panel Storm.

###<a id="connect"></a>Conexión con el panel

El panel se encuentra en **https://&lt;clustername>.azurehdinsight.net//**, donde **clustername** es el nombre del clúster. También encontrará un vínculo al panel en la parte inferior de la página del portal de Azure del clúster.

![El portal de Azure con el vínculo al panel Storm](./media/hdinsight-apache-storm-tutorial-get-started/dashboard-link.png)

> [AZURE.NOTE]Al conectarse con el panel, se le pedirá que escriba un nombre de usuario y una contraseña. Se trata del nombre del administrador (**admin**) y la contraseña que usó al crear el clúster.

Cuando se haya cargado el panel Storm, verá el formulario **Enviar topología**.

![Envíe su topología de inicio de Storm con el panel de Storm.](./media/hdinsight-apache-storm-tutorial-get-started/submit.png)

El formulario **Enviar topología** puede usarse para cargar y ejecutar archivos .jar que contienen topologías de Storm. También incluye varias muestras básicas que se ofrecen con el clúster.

###<a id="run"></a>Ejecución de la muestra de recuento de palabras desde el proyecto de inicio de Storm en GitHub

Las muestras que se facilitan con el clúster incluyen ciertas variaciones de una topología de recuento de palabras. Estos ejemplos incluyen un **spout** que emite oraciones de forma aleatoria y **bolts** que dividen una frase en palabras individuales para, a continuación, contar cuántas veces se ha encontrado cada palabra. Estos ejemplos proceden de los [Ejemplos de inicio de Storm](https://github.com/apache/storm/tree/master/examples/storm-starter), que forman parte de Apache Storm.

Siga estos pasos para ejecutar una muestra de inicio de Storm:

1. Seleccione **StormStarter - WordCount** en la lista desplegable **Archivo Jar**. Esto rellenará los campos **Nombre de la clase** y **Parámetros adicionales** con los parámetros de esta muestra.

	![WordCount de inicio de Storm seleccionada en el panel de Storm.](./media/hdinsight-apache-storm-tutorial-get-started/submit.png)

	* **Nombre de la clase**: la clase en el archivo .jar que envía la topología.
	* **Parámetros adicionales**: los parámetros que requiere la topología. En este ejemplo, el campo se usa para proporcionar un nombre descriptivo para la topología enviada.

2. Haga clic en el botón **Enviar**. Tras unos instantes, el campo **Resultado** mostrará el comando usado para enviar el trabajo, así como los resultados del comando. El campo **Error** mostrará los errores que se producen en el envío de la topología.

	![Botón Enviar y resultados de WordCount de inicio de Storm.](./media/hdinsight-apache-storm-tutorial-get-started/submit-results.png)

	> [AZURE.NOTE]Los resultados no indican que haya finalizado la topología: **una topología Storm, una vez iniciada, se ejecuta hasta que la detenga.** La topología de recuento de palabras generará frases aleatorias y mantendrá un recuento del número de veces que encuentra cada palabra hasta que la detenga.

###<a id="monitor"></a>Supervisión de la topología

La interfaz de usuario de Storm se puede usar para supervisar la topología.

1. Seleccione **IU de Storm** en la parte superior del panel de Storm. Se mostrará información de resumen del clúster y de las topologías en ejecución.

	![Panel de Storm en el que se muestra el resumen de topología de WordCount de inicio de Starter.](./media/hdinsight-apache-storm-tutorial-get-started/stormui.png)

	En la página anterior, puede ver el tiempo que ha estado activa la topología, así como el número de trabajos, ejecutores y tareas que se usan.

	> [AZURE.NOTE]La columna **Nombre** contiene el nombre descriptivo que se proporcionó anteriormente mediante el campo **Parámetros adicionales**.

4. En el **resumen de la topología**, seleccione la entrada **wordcount** de la columna **Nombre**. Se mostrará más información sobre la topología.

	![Panel de Storm con la información de topología de WordCount de inicio de Starter.](./media/hdinsight-apache-storm-tutorial-get-started/topology-summary.png)

	Esta página ofrece la siguiente información:

	* **Estadísticas de topología**: información básica sobre el rendimiento de la topología, organizada en ventanas de tiempo.

		> [AZURE.NOTE]Al seleccionar una ventana de tiempo específica, se cambia la ventana de tiempo de la información que aparece en otras secciones de la página.

	* **Spouts**: información básica sobre spouts, entre la que se incluye el último error que ha devuelto cada spout.

	* **Bolts**: información básica sobre bolts.

	* **Configuración de la topología**: información detallada sobre la configuración de la topología.

	Esta página también ofrece acciones que se pueden realizar en la topología:

	* **Activar**: reanuda el procesamiento de una topología desactivada.

	* **Desactivar**: pausa una topología en ejecución.

	* **Reequilibrar**: ajusta el paralelismo de la topología. Debe volver a equilibrar las topologías en ejecución después de haber cambiado el número de nodos del clúster. Esto permite que la topología ajuste el paralelismo para compensar el mayor o menor número de nodos del clúster. Para obtener más información, consulte [Descripción del paralelismo de una topología de Storm](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).

	* **Eliminar**: finaliza una topología de Storm tras el tiempo de espera especificado.

5. En esta página, seleccione una entrada en la sección **Spouts** o **Bolts**. Se mostrará información sobre el componente seleccionado.

	![Panel de Storm con información acerca de los componentes seleccionados.](./media/hdinsight-apache-storm-tutorial-get-started/component-summary.png)

	En esta página se muestra la siguiente información:

	* **Estadísticas de spouts y bolts**: información básica sobre el rendimiento de los componentes, organizada en ventanas de tiempo.

		> [AZURE.NOTE]Al seleccionar una ventana de tiempo específica, se cambia la ventana de tiempo de la información que aparece en otras secciones de la página.

	* **Estadísticas de entrada** (solo bolt): información sobre los componentes que generan los datos que consume el bolt.

	* **Estadísticas de salida**: información sobre los datos que emite este bolt.

	* **Ejecutores**: información sobre las instancias de este componente.

	* **Errores**: errores que ha generado este componente.

5. Al visualizar los detalles de un spout o bolt, seleccione una entrada de la columna **Puerto** en la sección **Ejecutores** para ver los detalles de una instancia específica del componente.

		2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["with"]
		2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["nature"]
		2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [snow]
		2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [snow, 747293]
		2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [white]
		2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [white, 747293]
		2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [seven]
		2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [seven, 1493957]

	A partir de estos datos, puede ver que la palabra **seven** se ha repetido 1493957 veces. Ese es el número de veces que se ha encontrado desde que se inició esta topología.

###Detención de la topología

Vuelva a la página **Resumen de la topología** de la topología de recuento de palabras y, después, seleccione el botón **Eliminar** de la sección **Acciones de topología**. Cuando se le solicite, escriba 10 como los segundos de espera antes de detener la topología. Tras el período de tiempo de espera, ya no aparecerá la topología cuando visite la sección **IU de Storm** del panel.

##Resumen

En este tutorial de Apache Storm, usó el inicio de Storm para aprender a crear un clúster de Storm en HDInsight y usar el panel de Storm para implementar, supervisar y administrar topologías de Storm.

##<a id="next"></a>Pasos siguientes

* **Herramientas de HDInsight para Visual Studio**: las herramientas de HDInsight le permiten usar Visual Studio para enviar, supervisar y administrar topologías Storm de forma similar al panel Storm mencionado anteriormente. Las herramientas de HDInsight también ofrecen la posibilidad de crear topologías de Storm en C# e incluyen topologías de muestra que puede implementar y ejecutar en el clúster.

	Para obtener más información, consulte [Introducción al uso de las herramientas de Hadoop de HDInsight para Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md).

* **Archivos de muestra**: el clúster de Storm en HDInsight ofrece varios ejemplos en el directorio **%STORM\_HOME%\\contrib**. Cada ejemplo debe contener lo siguiente:

	* El código fuente, por ejemplo, storm-starter-0.9.1.2.1.5.0-2057-sources.jar

	* Los documentos de Java, por ejemplo, storm-starter-0.9.1.2.1.5.0-2057-javadoc.jar

	* El ejemplo, por ejemplo, storm-starter-0.9.1.2.1.5.0-2057-jar-with-dependencies.jar

	Use el comando 'jar' para extraer el código fuente o los documentos de Java. Por ejemplo, 'jar -xvf storm-starter-0.9.1.2.1.5.0.2057-javadoc.jar'.

	> [AZURE.NOTE]Los documentos de Java constan de páginas web. Una vez extraídos, use un explorador para ver el archivo **index.html**.

	Para obtener acceso a estas muestras, debe habilitar Escritorio remoto para el clúster de Storm en HDInsight y, después, copiar los archivos desde **%STORM\_HOME%\\contrib**.

* El documento siguiente contiene una lista de otros ejemplos que se pueden usar con Storm en HDInsight:

	* [Topologías de ejemplo para Storm en HDInsight](hdinsight-storm-example-topology.md)

[apachestorm]: https://storm.incubator.apache.org
[stormdocs]: http://storm.incubator.apache.org/documentation/Documentation.html
[stormstarter]: https://github.com/apache/storm/tree/master/examples/storm-starter
[stormjavadocs]: https://storm.incubator.apache.org/apidocs/
[azureportal]: https://manage.windowsazure.com/
[hdinsight-provision]: hdinsight-provision-clusters.md

<!---HONumber=August15_HO6-->