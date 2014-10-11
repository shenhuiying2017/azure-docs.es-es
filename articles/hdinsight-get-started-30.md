<properties linkid="manage-services-hdinsight-get-started-hdinsight-hadoop-3.0" urlDisplayName="Get Started" pageTitle="Get started using Hadoop in HDInsight | Azure" metaKeywords="" description="Get started using Hadoop in HDInsight, a big data solution. Learn how to provision clusters, run hive jobs, and output data to Excel for analysis." metaCanonical="" services="hdinsight" documentationCenter="" title="Get started using Hadoop in HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao"></tags>

# Introducción al uso de Hadoop 2.2 en HDInsight

<div class="dev-center-tutorial-selector sublanding">
<a href="../hdinsight-get-started" title="Introducci&oacute;n al uso de Hadoop 2.4 en HDInsight">Hadoop 2.4</a>
<a href="../hdinsight-get-started-30" title="Introducci&oacute;n al uso de Hadoop 2.2 en HDInsight" class="current">Hadoop 2.2</a>
<!--a href="../hdinsight-get-started-21" title="Get started using Hadoop 1.2 in HDInsight">Hadoop 1.2</a-->
</div>

Gracias a HDInsight, Apache Hadoop, el marco de software de MapReduce está disponible en un entorno de Azure más sencillo, escalable y rentable. HDInsight también ofrece una solución rentable para la administración y el almacenamiento de datos mediante el almacenamiento de blobs de Azure.

> [WACOM.NOTE] Si no ha trabajado antes con Hadoop y Big Data, puede que le interese obtener más información sobre los términos [Apache Hadoop][], [MapReduce][], [HDFS][] y [Hive][].

Además de poner HDInsight de Azure a disposición de los usuarios, Microsoft también proporciona el emulador de HDInsight para Azure, anteriormente conocido como *Microsoft HDInsight Developer Preview*. El emulador está destinado a los desarrolladores y solo admite implementaciones de un solo nodo. Para usar el emulador de HDInsight, consulte [Introducción al emulador de HDInsight][].

> [WACOM.NOTE] Para obtener instrucciones sobre cómo aprovisionar un clúster de HBase, consulte [Aprovisionamiento de un clúster de HBase en HDInsight][]. Consulte [¿Cuál es la diferencia entre Hadoop y HBase?][] para comprender por qué debería elegir uno en lugar del otro.

## ¿Cuál es el objetivo de este tutorial?

Supongamos que tiene un gran conjunto de datos no estructurados y desea ejecutar consultas en él para extraer alguna información significativa. Eso es exactamente lo que vamos a hacer en este tutorial. Así es como lo lograremos:

![HDI.GetStartedFlow][]

También puede ver un vídeo demostrativo de este tutorial:

<center>
<iframe width="560" height="315" src="http://www.youtube.com/embed/v=Y4aNjnoeaHA?list=PLDrz-Fkcb9WWdY-Yp6D4fTC1ll_3lU-QS" frameborder="0" allowfullscreen>
</iframe>
</center>
<!--center><a href="https://www.youtube.com/watch?v=Y4aNjnoeaHA&list=PLDrz-Fkcb9WWdY-Yp6D4fTC1ll_3lU-QS" target = "_blank">![HDI.getstarted.video][img-hdi-getstarted-video]</a></center-->

**Requisitos previos:**

Antes de empezar este tutorial, debe contar con lo siguiente:

-   Una suscripción de Azure. Para obtener más información acerca de cómo obtener una suscripción, consulte [Opciones de compra][], [Ofertas para miembros][] o [Evaluación gratuita][].
-   Un equipo con Office Professional Plus 2013, Office 365 Pro Plus, Excel 2013 Standalone u Office Professional Plus 2010.

**Duración aproximada:** 30 minutos

## Apartados de este tutorial

-   [Creación de una cuenta de almacenamiento de Azure][]
-   [Aprovisionamiento de un clúster de HDInsight][]
-   [Ejecución de un trabajo de Hive][]
-   [Conexión a las herramientas de inteligencia empresarial de Microsoft][]
-   [Pasos siguientes][]

## <a name="storage"></a>Creación de una cuenta de Almacenamiento de Azure

HDInsight usa el almacenamiento de blobs de Azure para almacenar datos. Se llama *WASB* o *Almacenamiento de Azure - Blob*. WASB es la implementación del sistema de archivos distribuido de Hadoop (HDFS) de Microsoft en el almacenamiento de blobs de Azure. Para obtener más información, consulte [Uso del almacenamiento de blobs de Azure con HDInsight][].

Cuando aprovisiona un clúster de HDInsight, especifica una cuenta de Almacenamiento de Azure Storage. Un contenedor de almacenamiento de blobs de esa cuenta se designa como el sistema de archivos predeterminado, justo como HDFS. De forma predeterminada, el clúster de HDInsight se aprovisiona en el mismo centro de datos que la cuenta de almacenamiento especificada.

Además de esta cuenta de almacenamiento, puede agregar cuentas de almacenamiento adicionales cuando configura de forma personalizada un clúster de HDInsight. Esta cuenta de almacenamiento adicional puede ser de la misma suscripción de Azure o de suscripciones de Azure diferentes. Para obtener instrucciones, consulte [Aprovisionamiento de clústeres de HDInsight usando opciones personalizadas][].

Para simplificar este tutorial, solamente se usan el contenedor de blobs y la cuenta de almacenamiento predeterminados. En la práctica, los archivos de datos normalmente se almacenan en una cuenta de almacenamiento designada.

**Para crear una cuenta de Almacenamiento de Azure**

1.  Inicie sesión en el [Portal de administración de Azure][].
2.  Haga clic en **NEW** en la esquina inferior izquierda, seleccione **SERVICIOS DE DATOS**, **ALMACENAMIENTO** y, a continuación, haga clic en **CREACIÓN RÁPIDA**.

    ![HDI.StorageAccount.QuickCreate][]

3.  Escriba los detalles de **URL**, **UBICACIÓN** y **REPLICACIÓN** y, a continuación, haga clic en **CREAR CUENTA DE ALMACENAMIENTO**. No se admiten grupos de afinidad. La nueva cuenta de almacenamiento aparecerá en la lista de almacenamiento.

    > [WACOM.NOTE] Un clúster de HDInsight y la cuenta de almacenamiento de Azure asociada deben estar en el mismo centro de datos. Por tanto, asegúrese de que crea la cuenta de almacenamiento en las ubicaciones admitidas para el clúster, que son: **Asia oriental**, **Sudeste de Asia**, **Norte de Europa**, **Oeste de Europa**, **Este de EE. UU.**, **Oeste de EE. UU.**, **Centro y norte de EE. UU.**, **Centro y sur de EE. UU.**.

4.  Espere hasta que la característica **STATUS** de la nueva cuenta de almacenamiento cambie a **Online**.
5.  Seleccione la nueva cuenta de almacenamiento en la lista y haga clic en **ADMINISTRAR CLAVES DE ACCESO** en la parte inferior de la página.
6.  Tome nota de los valores de los campos **NOMBRE DE CUENTA DE ALMACENAMIENTO** y **CLAVE DE ACCESO PRIMARIA** (o **CLAVE DE ACCESO SECUNDARIA**. Cualquiera de las claves funciona). Los necesitará más adelante en el tutorial.

Para obtener más información, consulte
[Creación de una cuenta de almacenamiento][] y [Uso del almacenamiento de blobs de Azure con HDInsight][].

## <a name="provision"></a>Aprovisionamiento de un clúster de HDInsight

Cuando aprovisiona un clúster de Azure, aprovisiona cursos de proceso de Azure que contienen aplicaciones de Hadoop y aplicaciones relacionadas. En esta sección aprovisiona un clúster de HDInsight de la versión 3.0, que se basa en la versión 2.2. de Hadoop. Si desea aprovisionar un clúster de HDInsight con la versión 2.4 de Hadoop, haga clic en la pestaña de versión específica al principio de este artículo. También puede crear clústeres de Hadoop para otras versiones usando cmdlets de HDInsight PowerShell o el SDK .NET de HDInsight. Para obtener instrucciones, consulte [Aprovisionamiento de clústeres de HDInsight usando opciones personalizadas][]. Para obtener información acerca de las diferentes versiones de HDInsight y sus contratos de nivel de servicio, consulte la página [Control de versiones de componentes de HDInsight][].

**Para aprovisionar un clúster de HDInsight**

1.  Inicie sesión en el [Portal de administración de Azure][].

2.  Haga clic en la opción **HDInsight**, que aparece a la izquierda, para ver el estado de los clústeres en la cuenta. En la captura de pantalla siguiente no hay ningún clúster de HDInsight existente.

    ![HDI.ClusterStatus][]

3.  Haga clic en **NUEVO** en la esquina inferior izquierda y después en **Servicios de datos**, **HDInsight** y **Creación rápida**.

    ![ClústerCreaciónPersonalizada.HDI][]

    Escriba o seleccione los valores mostrados en la imagen anterior y después haga clic en la flecha derecha.

4.  En la pestaña **Configurar clúster**, escriba o seleccione los valores siguientes:

	<table border="1">
	<tr><th>Nombre</th><th>Valor</th></tr>
	<tr><td>Nodos de datos</td><td>Número de nodos de datos que desea implementar. Para propósitos de prueba, cree un clúster de un solo nodo. <br />El límite del tamaño del clúster varía según las suscripciones a Azure. Póngase en contacto con el servicio de soporte relacionado con la facturación de Azure para aumentar el límite.</td></tr>
	<tr><td>Región/Red virtual</td><td>Seleccione la misma región que la cuenta de almacenamiento que creó en el último procedimiento. HDInsight requiere que la cuenta de almacenamiento se encuentre en la misma región. Posteriormente, en la configuración, puede seleccionar solo una cuenta de almacenamiento que se encuentre en la misma región que especificó aquí. Las regiones disponibles son las siguientes: <strong>Asia oriental</strong>, <strong>Sudeste de Asia</strong>, <strong>Norte de Europa</strong>, <strong>Oeste de Europa</strong>, <strong>Este de EE. UU.</strong>, <strong>Oeste de EE. UU.</strong>, <strong>Centro y norte de EE. UU.</strong> y <strong>Centro y sur de EE. UU.</strong>.</td></tr>
	</table>

    Haga clic en la flecha derecha.

5.  En la página **Configurar usuario de clúster**, proporcione los siguientes valores:

    ![HDI.CustomCreateCluster.ClusterUser][]

	<table border="1">
	<tr><th>Nombre</th><th>Valor</th></tr>
	<tr><td>Nodos de datos</td><td>Número de nodos de datos que desea implementar. Para propósitos de prueba, cree un clúster de un solo nodo. <br />El límite del tamaño del clúster varía según las suscripciones a Azure. Póngase en contacto con el servicio de soporte relacionado con la facturación de Azure para aumentar el límite.</td></tr>
	<tr><td>Región/Red virtual</td><td>Seleccione la misma región que la cuenta de almacenamiento que creó en el último procedimiento. HDInsight requiere que la cuenta de almacenamiento se encuentre en la misma región. Posteriormente, en la configuración, puede seleccionar solo una cuenta de almacenamiento que se encuentre en la misma región que especificó aquí. Las regiones disponibles son las siguientes: <strong>Asia oriental</strong>, <strong>Sudeste de Asia</strong>, <strong>Norte de Europa</strong>, <strong>Oeste de Europa</strong>, <strong>Este de EE. UU.</strong>, <strong>Oeste de EE. UU.</strong>, <strong>Centro y norte de EE. UU.</strong> y <strong>Centro y sur de EE. UU.</strong>.</td></tr>
	</table>

    > [WACOM.NOTE] La base de datos SQL de Azure usada para la tienda de metadatos debe permitir conectividad con otros servicios de Azure, incluido HDInsight de Azure. En el panel de base de datos SQL de Azure, en el lado derecho, haga clic en el nombre de servidor. Este es el servidor en el que se ejecuta la instancia de base de datos SQL. Cuando se encuentre en la vista de servidor, haga clic en **Configurar** y después para **Servicios de Windows Azure**, haga clic en **Sí** y después en **Guardar**.

    Haga clic en la flecha derecha.

6.  Proporcione el siguiente valor en la página **Cuenta de almacenamiento**:

    ![HDI.CustomCreateCluster.StorageAccount][]

	<table border='1'>
		<tr><th>Propiedad</th><th>Valor</th></tr>
		<tr><td>Cuenta de almacenamiento</td>
			<td>Especifique la cuenta de almacenamiento de Azure que se usará como sistema de archivos predeterminado para el clúster de HDInsight. Puede elegir una de las tres opciones siguientes:
			<ul>
				<li>Usar almacenamiento existente</li>
				<li>Crear nuevo almacenamiento</li>
				<li>Utilizar almacenamiento de otra suscripción</li>
			</ul></td></tr>
		<tr><td>Nombre de cuenta</td>
			<td><ul>
				<li>Si optó por usar el almacenamiento existente, seleccione una cuenta de almacenamiento existente para <strong>Nombre de cuenta</strong>. En la lista desplegable solamente aparecen las cuentas de almacenamiento ubicadas en el mismo centro de datos en el que eligió aprovisionar el clúster.</li>
				<li>Si eligió <strong>Crear nuevo almacenamiento</strong> o <strong>Usar almacenamiento de otra suscripción</strong>, debe proporcionar el nombre de cuenta de almacenamiento.</li>
			</ul></td></tr>
		<tr><td>Clave de cuenta</td>
			<td>Si eligió la opción <strong>Usar almacenamiento de otra suscripción</strong>, especifique la clave de cuenta para esa cuenta de almacenamiento.</td></tr>
		<tr><td>Contenedor predeterminado</td>
			<td><p>Especifica el contenedor predeterminado de la cuenta de almacenamiento que se usará como sistema de archivos predeterminado para el clúster de HDInsight. Si eligió <strong>Usar almacenamiento existente</strong> para el campo <strong>Cuenta de almacenamiento</strong> y no existen contenedores en esa cuenta, el contenedor se creará de forma predeterminada con el mismo nombre que el del clúster. Si ya existe un contenedor con el nombre del clúster, se anexará un número de secuencia al nombre del contenedor. Por ejemplo, mycontainer1, mycontainer2 y así sucesivamente. Sin embargo, si la cuenta de almacenamiento existente tiene un contenedor con un nombre diferente al del clúster especificado, también puede usar ese contenedor.</p>
        </td></tr>
		<tr><td>Cuentas de almacenamiento adicionales</td>
			<td>HDInsight admite varias cuentas de almacenamiento. No hay límite en el número de cuentas de almacenamiento adicionales que un clúster puede usar. No obstante, si crea un clúster mediante el Portal de administración, tendrá un límite de siete debido a las restricciones de la interfaz de usuario. Por cada cuenta de almacenamiento adicional que especifique, se agregará una página Cuenta de almacenamiento adicional al asistente donde podrá especificar la información de la cuenta. Por ejemplo, en la captura de pantalla anterior, se selecciona una cuenta de almacenamiento adicional y, por tanto, se agrega la página 5 al diálogo.</td></tr>
	</table>

    Si optó por cuentas de almacenamiento adicionales, haga clic en la fecha derecha. De lo contrario, haga clic en la marca de verificación para iniciar el aprovisionamiento del clúster. Cuando el aprovisionamiento se complete, la columna de estado mostrará **En ejecución**.

7.  En la página **Cuenta de almacenamiento**, escriba la información de la cuenta de almacenamiento adicional si optó por ella:

    ![HDI.CustomCreateCluster.AddOnStorage][]

    De nuevo aquí, tiene la opción de elegir entre almacenamiento existente, crear nuevo almacenamiento o usar almacenamiento de otra suscripción de Azure. El procedimiento para proporcionar los valores es similar al paso anterior.

    Haga clic en la marca de verificación para iniciar el aprovisionamiento del clúster. Cuando el aprovisionamiento se complete, la columna de estado mostrará **En ejecución**.

## <a name="sample"></a>Ejecución de un trabajo de Hive

Ahora que ha aprovisionado un clúster de HDInsight, el paso siguiente es ejecutar un trabajo de Hive para consultar una tabla de ejemplo de Hive, *hivesampletable*, que se incluye con los clústeres de HDInsight. La tabla contiene datos sobre el fabricante de dispositivos, plataformas y modelos. Haremos consultas en esta tabla para recuperar datos de dispositivos móviles por un fabricante especificado.

**Para ejecutar un trabajo de Hive desde el panel de clúster**

1.  Inicie sesión en el [Portal de administración de Azure][].
2.  Haga clic en **HDINSIGHT** en el panel izquierdo. Verá una lista de los clústeres creados, junto con el que acaba de crear en la última sección.
3.  Haga clic en el nombre del clúster donde desea ejecutar el trabajo de Hive y elija **ADMINISTRAR CLÚSTER** en la parte inferior de la página.
4.  Se abre una página web en una pestaña de explorador diferente. Escriba la cuenta de usuario y la contraseña de Hadoop. El nombre de usuario predeterminado es **admin** y la contraseña es la que ha escrito durante el proceso de aprovisionamiento del clúster. El panel tiene este aspecto:

    ![hdi.dashboard][]

    Hay varias pestañas en la parte superior. La pestaña predeterminada es **Editor de Hive** y el resto de pestañas son **Historial de trabajos** y **Explorador de archivos**. Mediante el panel, puede enviar consultas de Hive, comprobar registros de trabajo de Hive y examinar archivos WASB.

    > [WACOM.NOTE] Tenga en cuenta que la dirección URL de la página web tres *\<Nombre\_De\_Cluster\>.azurehdinsight.net*. Por tanto, en lugar de abrir el panel desde el Portal de administración, también puede abrirlo desde el explorador web mediante la dirección URL.

5.  En la pestaña **Editor de Hive**, para **Nombre de la consulta**, escriba **HTC20**. El nombre de la consulta es el título del trabajo.

6.  En el panel de consultas, escriba la siguiente consulta:

        SELECT * FROM hivesampletable
            WHERE devicemake LIKE "HTC%"
            LIMIT 20;

    ![hdi.dashboard.query.select][]

7.  Haga clic en **Enviar**. Los resultados tardan unos segundos en aparecer. La pantalla se actualiza cada 30 segundos. También puede hacer clic en **Actualizar** para renovar la pantalla.

    Una vez completado el proceso, la pantalla tendrá el siguiente aspecto:

    ![hdi.dashboard.query.select.result][]

8.  Haga clic en el nombre de la consulta en la pantalla para ver la salida. Anote el valor de **Hora de inicio del trabajo (UTC)**. Lo necesitará más adelante.

    ![hdi.dashboard.query.select.result.output][]

    La página también muestra los campos **Salida del trabajo** y **Registro de trabajo**. También tiene la posibilidad de descargar el archivo de salida (\_stdout) y el archivo de registro (\_stderr).

    > [WACOM.NOTE] La tabla **Sesión del trabajo** de la pestaña **Editor de Hive** muestra trabajos completados o en ejecución mientras permanezca en esa pestaña. La tabla no enumera ningún trabajo si sale de la página. La pestaña **Historial de trabajos** mantiene una lista de todos los trabajos, tanto de los completados como de los que se encuentran en ejecución.

**Para examinar el archivo de salida**

1.  En el panel del clúster, haga clic en **Explorador de archivos** en la parte superior.
2.  Haga clic en el nombre de cuenta de almacenamiento, haga clic en el nombre del contenedor (que coincide con el del clúster) y después haga clic en **usuario**.
3.  Haga clic en admin y después en el número GUID que cuya hora de la última modificación es ligeramente posterior a la hora de inicio del trabajo que anotó anteriormente. Tome nota de este GUID. Lo necesitará en la próxima sesión.

    ![hdi.dashboard.query.browse.output][]

## <a name="powerquery"></a>Conexión a las herramientas de inteligencia empresarial de Microsoft

Puede usar el complemento Power Query para Microsoft Excel para importar la salida del trabajo desde HDInsight en Excel, donde se pueden usar las herramientas de inteligencia empresarial (BI) de Microsoft para analizar más en profundidad los resultados.

Para completar esta parte del tutorial, debe tener instalado Excel 2010 o 2013.

**Para descargar Microsoft Power Query para Excel**

-   Descargue Microsoft Power Query para Microsoft Excel en el [Centro de descarga de Microsoft][] e instálelo.

**Para importar datos de HDInsight**

1.  Abra Excel y cree un libro en blanco.
2.  Haga clic en el menú **Power Query**, en **Desde otros orígenes** y, a continuación, en **De HDInsight de Azure**.

    ![HDI.GettingStarted.PowerQuery.ImportData][]

3.  En **Nombre de cuenta**, escriba el nombre de la cuenta de almacenamiento de blobs de Azure asociada con su clúster y, a continuación, haga clic en **Aceptar**. Esta es la cuenta de almacenamiento creada previamente en el tutorial.
4.  En **Clave de cuenta**, escriba la clave de la cuenta de almacenamiento de blobs de Azure y, a continuación, haga clic en **Guardar**.
5.  En el panel de navegación de la derecha, haga doble clic en el nombre del contenedor de almacenamiento de blobs. De forma predeterminada, el nombre del contenedor es el mismo que el del clúster.

6.  Busque **stdout** en la columna **Nombre**. Compruebe que el GUID de la columna Ruta de acceso de la carpeta correspondiente coincide con el GUID que anotó anteriormente. Haga clic en **Binario** a la izquierda de **stdout**.

    ![HDI.GettingStarted.PowerQuery.ImportData2][]

7.  Haga clic en **Cerrar y cargar** en la esquina superior izquierda para importar la salida del trabajo de Hive en Excel.

## <a name="nextsteps"></a>Pasos siguientes

En este tutorial ha aprendido cómo aprovisionar un clúster con HDInsight, cómo ejecutar un trabajo de MapReduce en este y cómo importar los resultados en Excel para procesarlos mejor y representarlos gráficamente mediante las herramientas de BI. Para obtener más información, consulte los artículos siguientes:

-   [Introducción al emulador de HDInsight][]
-   [Uso del almacenamiento de blobs de Azure con HDInsight][]
-   [Administración de HDInsight con PowerShell][]
-   [Carga de datos en HDInsight][]
-   [Uso de MapReduce con HDInsight][]
-   [Uso de Hive con HDInsight][]
-   [Uso de Pig con HDInsight][]
-   [Uso de Oozie con HDInsight][]
-   [Desarrollo de programas de streaming de Hadoop C# para HDInsight][]
-   [Desarrollo de programas MapReduce de Java para HDInsight][]

  [Hadoop 2.4]: ../hdinsight-get-started "Introducción al uso de Hadoop 2.4 en HDInsight"
  [Hadoop 2.2]: ../hdinsight-get-started-30 "Introducción al uso de Hadoop 2.2 en HDInsight"
  [Apache Hadoop]: http://go.microsoft.com/fwlink/?LinkId=510084
  [MapReduce]: http://go.microsoft.com/fwlink/?LinkId=510086
  [HDFS]: http://go.microsoft.com/fwlink/?LinkId=510087
  [Hive]: http://go.microsoft.com/fwlink/?LinkId=510085
  [Introducción al emulador de HDInsight]: ../hdinsight-get-started-emulator/
  [Aprovisionamiento de un clúster de HBase en HDInsight]: http://azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-get-started/
  [¿Cuál es la diferencia entre Hadoop y HBase?]: http://go.microsoft.com/fwlink/?LinkId=510237
  [HDI.GetStartedFlow]: ./media/hdinsight-get-started/HDI.GetStartedFlow.png
  [Opciones de compra]: http://azure.microsoft.com/en-us/pricing/purchase-options/
  [Ofertas para miembros]: http://azure.microsoft.com/en-us/pricing/member-offers/
  [Evaluación gratuita]: http://azure.microsoft.com/en-us/pricing/free-trial/
  [Creación de una cuenta de almacenamiento de Azure]: #storage
  [Aprovisionamiento de un clúster de HDInsight]: #provision
  [Ejecución de un trabajo de Hive]: #sample
  [Conexión a las herramientas de inteligencia empresarial de Microsoft]: #powerquery
  [Pasos siguientes]: #nextsteps
  [Uso del almacenamiento de blobs de Azure con HDInsight]: ../hdinsight-use-blob-storage/
  [Aprovisionamiento de clústeres de HDInsight usando opciones personalizadas]: ../hdinsight-provision-clusters/
  [Portal de administración de Azure]: https://manage.windowsazure.com/
  [HDI.StorageAccount.QuickCreate]: ./media/hdinsight-get-started/HDI.StorageAccount.QuickCreate.png
  [Creación de una cuenta de almacenamiento]: ../storage-create-storage-account/
  [Control de versiones de componentes de HDInsight]: http://azure.microsoft.com/en-us/documentation/articles/hdinsight-component-versioning/
  [HDI.ClusterStatus]: ./media/hdinsight-get-started/HDI.ClusterStatus.png
  [ClústerCreaciónPersonalizada.HDI]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.png
  [HDI.CustomCreateCluster.ClusterUser]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.ClusterUser.png
  [HDI.CustomCreateCluster.StorageAccount]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.StorageAccount.png
  [HDI.CustomCreateCluster.AddOnStorage]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.AddOnStorage.png
  [hdi.dashboard]: ./media/hdinsight-get-started/HDI.dashboard.png
  [hdi.dashboard.query.select]: ./media/hdinsight-get-started/HDI.dashboard.query.select.png
  [hdi.dashboard.query.select.result]: ./media/hdinsight-get-started/HDI.dashboard.query.select.result.png
  [hdi.dashboard.query.select.result.output]: ./media/hdinsight-get-started/HDI.dashboard.query.select.result.output.png
  [hdi.dashboard.query.browse.output]: ./media/hdinsight-get-started/HDI.dashboard.query.browse.output.png
  [Centro de descarga de Microsoft]: http://www.microsoft.com/en-us/download/details.aspx?id=39379
  [HDI.GettingStarted.PowerQuery.ImportData]: ./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData.png
  [HDI.GettingStarted.PowerQuery.ImportData2]: ./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData2.png
  [Administración de HDInsight con PowerShell]: ../hdinsight-administer-use-powershell/
  [Carga de datos en HDInsight]: ../hdinsight-upload-data/
  [Uso de MapReduce con HDInsight]: ../hdinsight-use-mapreduce
  [Uso de Hive con HDInsight]: ../hdinsight-use-hive/
  [Uso de Pig con HDInsight]: ../hdinsight-use-pig/
  [Uso de Oozie con HDInsight]: ../hdinsight-use-oozie/
  [Desarrollo de programas de streaming de Hadoop C# para HDInsight]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
  [Desarrollo de programas MapReduce de Java para HDInsight]: ../hdinsight-develop-deploy-java-mapreduce/
