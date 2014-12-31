<properties linkid="manage-services-hdinsight-get-started-hdinsight-hadoop" urlDisplayName="Get Started" pageTitle="Introducción al uso de Hadoop con Hive en HDInsight | Azure" metaKeywords="" description="Get started using Hadoop in HDInsight, a big data solution in the cloud. Learn how to provision clusters, query data with Hive, and output to Excel for analysis." metaCanonical="" services="hdinsight" documentationCenter="" title="Get started using Hadoop with Hive in HDInsight to analyze mobile handset use" authors="nitinme" solutions="big-data" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/14/2014" ms.author="nitinme" />


# Introducción al uso de Hadoop con Hive en HDInsight para analizar el uso de datos móviles

<!--div class="dev-center-tutorial-selector sublanding">
<a href="../hdinsight-get-started" title="Get started using Hadoop 2.4 in HDInsight" class="current">Hadoop 2.4</a>
<a href="../hdinsight-get-started-30" title="Get started using Hadoop 2.2 in HDInsight">Hadoop 2.2</a>
<!--a href="../hdinsight-get-started-21" title="Get started using Hadoop 1.2 in HDInsight">Hadoop 1.2</a>
</div-->

Para comenzar rápidamente a utilizar HDInsight, este tutorial muestra cómo ejecutar una consulta Hive para extraer información significativa desde datos no estructurados en un clúster de Hadoop. A continuación, se analizarán los resultados en Microsoft Excel.


> [WACOM.NOTE] Si todavía no conoce a Hadoop y los grandes datos, puede obtener más información sobre los términos [Apache Hadoop][apache-hadoop], [MapReduce][apache-mapreduce], [HDFS][apache-hdfs] y [Hive][apache-hive]. Para comprender el modo en que HDInsight habilita Hadoop en Azure, consulte [Introducción a Hadoop en HDInsight][hadoop-hdinsight-intro].

Además de poner HDInsight de Azure a disposición de los usuarios, Microsoft también proporciona el emulador de HDInsight para Azure, anteriormente conocido como *Microsoft HDInsight Developer Preview*. El emulador está destinado a los desarrolladores y solo admite implementaciones de un solo nodo. Para usar el emulador de HDInsight, consulte [Introducción al emulador de HDInsight][hdinsight-emulator].

> [WACOM.NOTE] Para obtener instrucciones sobre cómo aprovisionar un clúster de HBase, consulte [Aprovisionamiento de un clúster de HBase en HDInsight][hdinsight-hbase-custom-provision]. Consulte <a href="http://go.microsoft.com/fwlink/?LinkId=510237">¿Cuál es la diferencia entre Hadoop y HBase?</a> para comprender por qué se elige uno u otro.   

## ¿Cuál es el objetivo de este tutorial? ##

Supongamos que tiene un gran conjunto de datos no estructurados y desea ejecutar consultas en él para extraer alguna información significativa. Eso es exactamente lo que vamos a hacer en este tutorial. Así es como lo lograremos:

   !["Get started using Hadoop with Hive in HDInsight" tutorial steps illustrated: create an account; provision a cluster; query data; and analyze in Excel.][image-hdi-getstarted-flow]

También puede ver un vídeo demostrativo de este tutorial:

<center><iframe width="560" height="315" src="http://www.youtube.com/embed/v=Y4aNjnoeaHA?list=PLDrz-Fkcb9WWdY-Yp6D4fTC1ll_3lU-QS" frameborder="0" allowfullscreen></iframe></center>

<!--center><a href="https://www.youtube.com/watch?v=Y4aNjnoeaHA&list=PLDrz-Fkcb9WWdY-Yp6D4fTC1ll_3lU-QS" target="_blank">![HDI.getstarted.video][img-hdi-getstarted-video]</a></center-->



**Requisitos previos:

Antes de empezar este tutorial, debe contar con lo siguiente:


- Una suscripción de Azure. Para obtener más información acerca de cómo obtener una suscripción, consulte [Opciones de compra][azure-purchase-options], [Ofertas para miembros][azure-member-offers] o [Evaluación gratuita][azure-free-trial].
- Un equipo con Office Professional Plus 2013, Office 365 Pro Plus, Excel 2013 Standalone u Office Professional Plus 2010.

**Duración aproximada:** 30 minutos

##Apartados de este tutorial

* [Creación de una cuenta de almacenamiento de Azure](#storage)
* [Aprovisionamiento de un clúster de HDInsight](#provision)
* [Ejecución de muestras en el portal](#sample)
* [Ejecución de un trabajo de HIVE](#hivequery)
* [Pasos siguientes](#nextsteps)

##<a name="storage"></a>Creación de una cuenta de almacenamiento de Azure

HDInsight usa el almacenamiento de blobs de Azure para almacenar datos. Se llama *WASB* o *Almacenamiento de Azure - Blob*. WASB es la implementación del sistema de archivos distribuido de Hadoop (HDFS) de Microsoft en el almacenamiento de blobs de Azure. Para obtener más información, consulte [Uso del almacenamiento de blobs de Azure con HDInsight][hdinsight-storage].

Cuando aprovisiona un clúster de HDInsight, especifica una cuenta de Almacenamiento de Azure Storage. Un contenedor de almacenamiento de blobs de esa cuenta se designa como el sistema de archivos predeterminado, justo como HDFS. El clúster de HDInsight se aprovisiona de forma predeterminada en el mismo centro de datos que la cuenta de almacenamiento que especifique.

Además de esta cuenta de almacenamiento, puede agregar cuentas de almacenamiento adicionales cuando configura de forma personalizada un clúster de HDInsight. Esta cuenta de almacenamiento adicional puede ser de la misma suscripción de Azure o de suscripciones de Azure diferentes. Para obtener instrucciones, consulte [Aprovisionamiento de clústeres de HDInsight usando opciones personalizadas][hdinsight-provision]. 

Para simplificar este tutorial, solamente se usan el contenedor de blobs y la cuenta de almacenamiento predeterminados. En la práctica, los archivos de datos normalmente se almacenan en una cuenta de almacenamiento designada.

**Para crear una cuenta de almacenamiento de Azure**

1. Inicie sesión en el [Portal de administración de Azure][azure-management-portal].
2. Haga clic en **NUEVO** en la esquina inferior izquierda de la página, seleccione **SERVICIOS DE DATOS**, seleccione **ALMACENAMIENTO** y después haga clic en **CREACIÓN RÁPIDA**.

	![Azure portal where you can use Quick Create to set up a new storage account.][image-hdi-storageaccount-quickcreate]

3. Introduzca la **URL**, **UBICACIÓN** y **REPLICACIÓN** y después haga clic en **CREAR CUENTA DE ALMACENAMIENTO**. No se admiten grupos de afinidad. La nueva cuenta de almacenamiento aparecerá en la lista de almacenamiento.

	>[WACOM.NOTE]  La opción de creación rápida para aprovisionar un clúster de HDInsight, como el que se usa en este tutorial, no solicita una ubicación cuando se aprovisiona el clúster. De forma predeterminada, coloca el clúster en el mismo centro de datos que la cuenta de almacenamiento. Por tanto, asegúrese de que crea la cuenta de almacenamiento en las ubicaciones admitidas para el clúster, que son:  **Asia oriental**, **Sudeste asiático**, **Norte de Europa**, **Oeste de Europa**, **Este de EE. UU.**, **Oeste de EE. UU.**, **Centro-Norte de EE. UU.**, **Centro y sur de EE. UU.**.

4. Espere hasta que la característica **ESTADO** de la nueva cuenta de almacenamiento cambie a **En línea**.
5. Seleccione la nueva cuenta de almacenamiento en la lista y haga clic en **ADMINISTRAR CLAVES DE ACCESO** en la parte inferior de la página.
7. Tome nota de los valores de los campos **NOMBRE DE CUENTA DE ALMACENAMIENTO** y **CLAVE DE ACCESO PRIMARIA** (o **CLAVE DE ACCESO SECUNDARIA**. Cualquiera de las claves funciona).  Los necesitará más adelante en el tutorial.


Para obtener más información, consulte
[Creación de una cuenta de almacenamiento][azure-create-storageaccount] y [Uso del almacenamiento de blobs de Azure con HDInsight][hdinsight-storage].
	
##<a name="provision"></a>Aprovisionamiento de un clúster de HDInsight

Cuando aprovisiona un clúster de HDInsight, aprovisiona recursos de proceso de Azure que contienen aplicaciones de Hadoop y aplicaciones relacionadas. En esta sección aprovisiona un clúster de HDInsight de la versión 3.1, que se basa en la versión 2.4. de Hadoop. También puede crear clústeres de Hadoop para otras versiones mediante el portal de Azure, cmdlets de HDInsight PowerShell o el SDK .NET de HDInsight. Para obtener instrucciones, consulte [Aprovisionamiento de clústeres de HDInsight usando opciones personalizadas][hdinsight-provision]. Para obtener información acerca de las diferentes versiones de HDInsight y sus contratos de nivel de servicio, consulte la página [Control de versiones de componentes de HDInsight](http://azure.microsoft.com/en-us/documentation/articles/hdinsight-component-versioning/) .

[WACOM.INCLUDE [provisioningnote](../includes/hdinsight-provisioning.md)]


**Para aprovisionar un clúster de HDInsight** 

1. Inicie sesión en el [Portal de administración de Azure][azure-management-portal]. 

2. Haga clic en la opción **HDInsight**, que aparece a la izquierda, para ver el estado de los clústeres en la cuenta. En la captura de pantalla siguiente no hay ningún clúster de HDInsight existente.

	![Status of HDInsight clusters in the Azure portal.][image-hdi-clusterstatus]

3. Haga clic en **NUEVO** en la parte inferior izquierda, haga clic en **Servicios de datos**, haga clic en **HDInsight** y después haga clic en **Hadoop**.

	![Creation of a Hadoop cluster in HDInsight.][image-hdi-quickcreatecluster]

4. Escriba o seleccione los siguientes valores:

	<table border="1">
	<tr><th>Nombre</th><th>Valor</th></tr>
	<tr><td>Cluster Name</td><td>Nombre del clúster.</td></tr>
	<tr><td>Cluster Size</td><td>Número de nodos de datos que desea implementar. El valor predeterminado es 4. Pero también están disponibles las opciones para usar 1 o 2 nodos de datos en la lista desplegable. Con la opción Creación personalizada puede especificarse mediante la opción <strong>Creación personalizada</strong> . Hay disponible información sobre las tarifas de facturación para varios tamaños de clúster. Haga clic en el símbolo <strong>?</strong> justo encima del cuadro desplegable y siga el vínculo del elemento emergente.</td></tr>
	<tr><td>Password</td><td>La contraseña de la cuenta <i>admin</i> cuenta. Se especifica el nombre de usuario del clúster "admin" cuando no se está usando la opción <strong>Creación personalizada</strong> . Tenga en cuenta que esta NO es la cuenta de administrador de Windows para las máquinas virtuales donde se aprovisionan los clústeres. El nombre de cuenta solo se puede cambiar con el asistente <strong>Creación personalizada</strong> asistente.</td></tr>
	<tr><td>Cuenta de almacenamiento</td><td>Seleccione la cuenta de almacenamiento que ha creado en el cuadro desplegable. <br/>

	Una vez elegida una cuenta de almacenamiento, no se puede cambiar. En caso de quitarla, el clúster dejará de estar disponible para su uso.

	El clúster de HDInsight se coloca en el mismo centro de datos que la cuenta de almacenamiento. 
	</td></tr>
	</table>

	Guarde una copia del nombre del clúster.  La necesitará más adelante en el tutorial.

	
5. Haga clic en **Crear clúster de HDInsight**. Cuando el aprovisionamiento se complete, la columna de estado mostrará** En ejecución**.

	>[WACOM.NOTE] El procedimiento anterior crea un clúster con HDInsight versión 3.1. Para crear otras versiones de clúster, utilice el método de creación personalizada del Portal de administración, o bien use Azure PowerShell. Para obtener información sobre las diferencias entre las distintas versiones de clúster, consulte [Novedades en las versiones de clúster proporcionadas por HDInsight][hdinsight-versions] Para obtener información sobre el uso de la opción **CREACIÓN PERSONALIZADA**, consulte [Aprovisionamiento de clústeres de HDInsight usando opciones personalizadas][hdinsight-provision].


##<a name="sample"></a>Ejecución de muestras en el portal

Un clúster de HDInsight aprovisionado correctamente ofrece una consola de consultas para ejecutar muestras directamente en el portal. Puede usar las muestras para aprender a trabajar con HDInsight explorando algunos escenarios básicos. Estas muestras vienen con todos los componentes necesarios como, por ejemplo, los datos objeto del análisis y las consultas que se ejecutan en los datos. 

**Para ejecutar la muestra**, en el Portal de administración de Azure, haga clic en el nombre del clúster en el que desea ejecutar la muestra y, a continuación, en **Consola de consultas** en la parte inferior de la página. En la página web que se abre, haga clic en la pestaña **Galería de introducción** y en la categoría **Muestras**, haga clic en la muestra que desea ejecutar. Siga las instrucciones de la página web para finalizar la muestra. Para obtener más información sobre lo que hace cada muestra, haga clic en los vínculos que aparecen a continuación.

Muestra | ¿Para qué sirve?
------ | ---------------
[Análisis de datos de sensor][hdinsight-sensor-data-sample] | Aprenda el uso de HDInsight para procesar datos históricos producidos por sistemas de aire acondicionado, ventilación y calefacción (HVAC) para identificar sistemas que no pueden mantener de manera confiable una temperatura establecida
[Análisis de registros de sitios web][hdinsight-weblogs-sample] | Aprenda el uso de HDInsight para analizar los archivos de registro del sitio web con objeto de conocer la frecuencia de las visitas diarias al mismo desde sitios web externos y obtener un resumen de los errores del sitio web que los usuarios experimentan


##<a name="hivequery"></a>Ejecución de una consulta de HIVE en el portal
Ahora que ha aprovisionado un clúster de HDInsight, el paso siguiente es ejecutar un trabajo de Hive para consultar una tabla de ejemplo de Hive, *hivesampletable*, que se incluye con los clústeres de HDInsight. La tabla contiene datos sobre el fabricante de dispositivos, plataformas y modelos. Haremos consultas en esta tabla para recuperar datos de dispositivos móviles por un fabricante especificado.

> [WACOM.NOTE] Las herramientas de HDInsight para Visual Studio se incluyen con el SDK de Azure para .NET versión 2.5 o posterior.  Con las herramientas de Visual Studio, puede conectarse al clúster de HDInsight, crear tablas de Hive y ejecutar consultas de Hive.  Para obtener más información, consulte [Introducción al uso de las herramientas de Hadoop de HDInsight para Visual Studio][1].

**Para ejecutar un trabajo de Hive desde el panel de clúster**

1. Inicie sesión en el [Portal de administración de Azure][azure-management-portal]. 
2. Haga clic en **HDINSIGHT** en el panel izquierdo. Verá una lista de los clústeres creados, junto con el que acaba de crear en la última sección.
3. Haga clic en el nombre del clúster donde desea ejecutar el trabajo de Hive y elija **CONSOLA DE CONSULTAS** en la parte inferior de la página. 
4. Se abre una página web en una pestaña de explorador diferente. Escriba la cuenta de usuario y la contraseña de Hadoop.  El nombre de usuario predeterminado es **admin** y la contraseña es la que ha escrito durante el proceso de aprovisionamiento del clúster.  El panel tiene este aspecto:

	![Hive Editor tab in the HDInsight cluster dashboard.][img-hdi-dashboard]

	Hay varias pestañas en la parte superior.  La pestaña predeterminada es **Editor de Hive** y el resto de pestañas son **Historial de trabajos** y **Explorador de archivos**.  Mediante el panel, puede enviar consultas de Hive, comprobar registros de trabajo de Hive y examinar archivos WASB.

	> [WACOM.NOTE] Tenga en cuenta que la dirección URL de la página web tres *&lt;NombreClúster&gt;.azurehdinsight.net*. Por tanto, en lugar de abrir el panel desde el Portal de administración, también puede abrirlo desde el explorador web mediante la dirección URL.

6. En la pestaña **Editor de Hive**, para **Nombre de la consulta**, escriba **HTC20**.  El nombre de la consulta es el título del trabajo.

7. En el panel de consultas, escriba la siguiente consulta: 

		SELECT * FROM hivesampletable
			WHERE devicemake LIKE "HTC%"
			LIMIT 20;

	![Query entered in the query pane of the Hive Editor.][img-hdi-dashboard-query-select]

4. Haga clic en **Enviar**. Los resultados tardan unos segundos en aparecer. La pantalla se actualiza cada 30 segundos. También puede hacer clic en **Actualizar** para renovar la pantalla.

    Una vez completado el proceso, la pantalla tendrá el siguiente aspecto:

	![Results from a Hive query in listed at the bottom of the cluster dashboard.][img-hdi-dashboard-query-select-result]

5. Haga clic en el nombre de la consulta en la pantalla para ver la salida. Anote el valor de **Hora de inicio del trabajo (UTC)**. Lo necesitará más adelante. 

    ![Job Start Time listed in the Job History tab of the HDInsight cluster dashboard.][img-hdi-dashboard-query-select-result-output]

    La página también muestra los campos **Salida del trabajo** y **Registro de trabajo**. También tiene la posibilidad de descargar el archivo de salida (\_stdout) y el archivo de registro \(_stderr).


	> [WACOM.NOTE] La tabla **Sesión del trabajo** de la pestaña **Editor de Hive** muestra trabajos completados o en ejecución mientras permanezca en esa pestaña. La tabla no enumera ningún trabajo si sale de la página. La pestaña **Historial de trabajos** mantiene una lista de todos los trabajos, tanto de los completados como de los que se encuentran en ejecución.
 

**Para examinar el archivo de salida**

1. En el panel del clúster, haga clic en **Explorador de archivos** en la parte superior. 
2. Haga clic en el nombre de cuenta de almacenamiento, haga clic en el nombre del contenedor (que coincide con el del clúster) y después haga clic en **usuario**.
3. Haga clic en **admin** y después en el GUID cuya hora de la última modificación es ligeramente posterior a la hora de inicio del trabajo que anotó anteriormente. Tome nota de este GUID. Lo necesitará en la próxima sesión.


   	![The output file GUID listed in the File Browser tab.][img-hdi-dashboard-query-browse-output]


###<a name="powerquery"></a>Conexión a las herramientas de inteligencia empresarial de Microsoft 

Puede usar el complemento Power Query para Microsoft Excel para importar la salida del trabajo desde HDInsight en Excel, donde se pueden usar las herramientas de inteligencia empresarial (BI) de Microsoft para analizar más en profundidad los resultados. 

Para completar esta parte del tutorial, debe tener instalado Excel 2010 o 2013. 

**Para descargar Microsoft Power Query para Excel**

- Descargue Microsoft Power Query para Microsoft Excel en el [Centro de descarga de Microsoft](http://www.microsoft.com/en-us/download/details.aspx?id=39379) e instálelo.

**Para importar datos de HDInsight**

1. Abra Excel y cree un libro en blanco.
3. Haga clic en el menú **Power Query**, en **Desde otros orígenes** y, a continuación, en **De HDInsight de Azure**.

	![Excel PowerQuery Import menu open for Azure HDInsight.][image-hdi-gettingstarted-powerquery-importdata]

3. En **Nombre de cuenta**, escriba el nombre de la cuenta de almacenamiento de blobs de Azure asociada con su clúster y, a continuación, haga clic en **Aceptar**. Esta es la cuenta de almacenamiento creada previamente en el tutorial.
4. En **Clave de cuenta**, escriba la clave de la cuenta de almacenamiento de blobs de Azure y, a continuación, haga clic en** Guardar**. 
5. En el panel de navegación de la derecha, haga doble clic en el nombre del contenedor de almacenamiento de blobs. De forma predeterminada, el nombre del contenedor es el mismo que el del clúster. 

6. Busque **stdout** en la columna **Nombre**. Compruebe que el GUID de la columna **Ruta de acceso **de la carpeta correspondiente coincide con el GUID que anotó anteriormente. Una coincidencia sugiere que los datos de salida corresponden al trabajo que envió. Haga clic en **Binario** a la izquierda de **stdout**.

	![Finding the data output by GUID in the list of content.][image-hdi-gettingstarted-powerquery-importdata2]

9. Haga clic en **Cerrar y cargar** en la esquina superior izquierda para importar la salida del trabajo de Hive en Excel.


##<a name="nextsteps"></a>Pasos siguientes
En este tutorial ha aprendido cómo aprovisionar un clúster con HDInsight, cómo ejecutar un trabajo de MapReduce en este y cómo importar los resultados en Excel para procesarlos mejor y representarlos gráficamente mediante las herramientas de BI. Para obtener más información, consulte los artículos siguientes:

- [Introducción al uso de las herramientas de Hadoop de HDInsight para Visual Studio][1].
- [Introducción al emulador de HDInsight][hdinsight-emulator]
- [Uso del almacenamiento de blobs de Azure con HDInsight][hdinsight-storage]
- [Administración de HDInsight con PowerShell][hdinsight-admin-powershell]
- [Carga de datos en HDInsight][hdinsight-upload-data]
- [Uso de MapReduce con HDInsight][hdinsight-use-mapreduce]
- [Uso de Hive con HDInsight][hdinsight-use-hive]
- [Uso de Pig con HDInsight][hdinsight-use-pig]
- [Uso de Oozie con HDInsight][hdinsight-use-oozie]
- [Desarrollo de programas de streaming de Hadoop C# para HDInsight][hdinsight-develop-streaming]
- [Desarrollo de programas MapReduce de Java para HDInsight][hdinsight-develop-mapreduce]


[1]: ../hdinsight-hadoop-visual-studio-tools-get-started/

[hdinsight-versions]: ../hdinsight-component-versioning/

[hdinsight-get-started-30]: ../hdinsight-get-started-30/
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-use-mapreduce]: ../hdinsight-use-mapreduce
[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/
[hdinsight-use-oozie]: ../hdinsight-use-oozie/
[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-emulator]: ../hdinsight-get-started-emulator/
[hdinsight-develop-streaming]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
[hdinsight-develop-mapreduce]: ../hdinsight-develop-deploy-java-mapreduce/
[hadoop-hdinsight-intro]: ../hdinsight-hadoop-introduction/
[hdinsight-weblogs-sample]: ../hdinsight-hive-analyze-website-log/
[hdinsight-sensor-data-sample]: ../hdinsight-hive-analyze-sensor-data/

[azure-purchase-options]: http://azure.microsoft.com/en-us/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/en-us/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/en-us/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: ../storage-create-storage-account/ 

[apache-hadoop]: http://go.microsoft.com/fwlink/?LinkId=510084
[apache-hive]: http://go.microsoft.com/fwlink/?LinkId=510085
[apache-mapreduce]: http://go.microsoft.com/fwlink/?LinkId=510086
[apache-hdfs]: http://go.microsoft.com/fwlink/?LinkId=510087
[hdinsight-hbase-custom-provision]: http://azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-get-started/


[powershell-download]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[powershell-install-configure]: ../install-configure-powershell/
[powershell-open]: ../install-configure-powershell/#Install


[img-hdi-dashboard]: ./media/hdinsight-get-started/HDI.dashboard.png
[img-hdi-dashboard-query-select]: ./media/hdinsight-get-started/HDI.dashboard.query.select.png
[img-hdi-dashboard-query-select-result]: ./media/hdinsight-get-started/HDI.dashboard.query.select.result.png
[img-hdi-dashboard-query-select-result-output]: ./media/hdinsight-get-started/HDI.dashboard.query.select.result.output.png
[img-hdi-dashboard-query-browse-output]: ./media/hdinsight-get-started/HDI.dashboard.query.browse.output.png

[img-hdi-getstarted-video]: ./media/hdinsight-get-started/HDI.GetStarted.Video.png


[image-hdi-storageaccount-quickcreate]: ./media/hdinsight-get-started/HDI.StorageAccount.QuickCreate.png
[image-hdi-clusterstatus]: ./media/hdinsight-get-started/HDI.ClusterStatus.png
[image-hdi-quickcreatecluster]: ./media/hdinsight-get-started/HDI.QuickCreateCluster.png
[image-hdi-getstarted-flow]: ./media/hdinsight-get-started/HDI.GetStartedFlow.png

[image-hdi-gettingstarted-powerquery-importdata]: ./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData.png
[image-hdi-gettingstarted-powerquery-importdata2]: ./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData2.png

<!--HONumber=35_1-->
