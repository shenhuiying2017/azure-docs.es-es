<properties urlDisplayName="Get Started" pageTitle="Configuración y consulta de tablas de HBase mediante Hive en HDInsight | Azure" metaKeywords="" description="Get started using HBase with Hadoop in HDInsight. Learn how to create HBase tables and query them using Hive." metaCanonical="" services="hdinsight" documentationCenter="" title="Set up HBase clusters and query them using Hive on Hadoop in HDInsight" authors="bradsev" solutions="big-data" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/9/2014" ms.author="bradsev" />



# Configure clústeres de HBase y realice consultas en ellos mediante Hive en Hadoop en HDInsight

Aprenda a crear y a consultar tablas de HBase mediante Hive en Hadoop en HDInsight. 

##En este artículo

* [¿Qué es HBase?](#hbaseintro)
* [Requisitos previos](#prerequisites)
* [Aprovisionamiento de clústeres de HBase con el Portal de administración de Azure](#create-hbase-cluster)
* [Administración de tablas de HBase mediante el shell de HBase](#create-sample-table)
* [Uso de HiveQL para consultar tablas de HBase](#hive-query)
* [Uso de la biblioteca de cliente de REST de HBase de Microsoft para administrar tablas de HBase](#hbase-powershell)
* [Consulte también](#seealso)

##<a name="hbaseintro"></a>¿Qué es HBase?

HBase es una base de datos NoSQL de baja latencia que permite el procesamiento de transacciones en línea de Big Data. HBase se ofrece como un clúster administrado integrado en el entorno de Azure. Los clústeres están configurados para almacenar datos directamente en el almacenamiento de blobs de Azure, que proporciona una latencia baja y una mayor elasticidad de las opciones de rendimiento o coste. Esto permite que los clientes creen sitios web interactivos que trabajen con grandes conjuntos de datos, creen servicios que almacenen datos del sensor y telemetría desde millones de extremos y analicen estos datos con trabajos de Hadoop. Para obtener más información sobre HBase y los escenarios en que se puede usar, consulte [Información general de HBase de HDInsight](http://azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-overview/).

> [WACOM.NOTE] HBase (versión 0.98.0) solo está disponible para usarlo con clústeres de HDInsight 3.1 en HDInsight (basado en Apache Hadoop y YARN 2.4.0). Para obtener información de la versión, consulte [Novedades en las versiones de clústeres proporcionadas por HDInsight][hdinsight-versions]

##<a name="prerequisites"></a>Requisitos previos

Antes de empezar este tutorial, debe contar con lo siguiente:

- **Una suscripción de Azure** Para obtener más información sobre cómo obtener una suscripción, consulte [Opciones de compra][azure-purchase-options], [Ofertas para miembros][azure-member-offers] o [Evaluación gratuita][azure-free-trial].
- **Una cuenta de almacenamiento de Azure** Para obtener instrucciones, consulte [Cómo crear una cuenta de almacenamiento][azure-create-storageaccount].
- **Una estación de trabajo** con Visual Studio 2013 instalado. Para obtener instrucciones, consulte [Instalación de Visual Studio](http://msdn.microsoft.com/en-us/library/e2h7fzkw.aspx).
- Descarga de la [biblioteca de cliente de REST de HBase de Microsoft para .NET](https://github.com/hdinsight/hbase-sdk-for-net). 


##<a name="create-hbase-cluster"></a>Aprovisionamiento de un clúster de HBase en el Portal de Azure

En esta sección se describe cómo aprovisionar un clúster de HBase mediante el Portal de Azure.


[WACOM.INCLUDE [provisioningnote](../includes/hdinsight-provisioning.md)]

**Para aprovisionar un clúster de HBase en el Portal de Azure** 


1. Inicie sesión en el [Portal de administración de Azure][azure-management-portal]. 

2. Haga clic en **HDInsight** a la izquierda para mostrar el estado de los clústeres de su cuenta y luego en el icono **+NUEVO** en la parte inferior izquierda. 

	![Creating an HBase cluster in HDInsight in the Azure portal.](http://i.imgur.com/PmGynKZ.jpg)

3. Haga clic en el icono de HDInsight en la segunda columna de la izquierda y luego en la opción HBase de la siguiente columna. Especifique los valores para NOMBRE DEL CLÚSTER y TAMAÑO DEL CLÚSTER, el nombre de la cuenta de almacenamiento y una contraseña para el nuevo clúster de HBase.
 
	![Choosing and HBase cluster type and entering cluster login credentials.](http://i.imgur.com/ecxbB9K.jpg)

4. Haga clic en el icono de verificación en la parte inferior izquierda para crear el clúster de HBase.


##<a name="create-sample-table"></a>Creación de una tabla de ejemplo de HBase a partir del shell de HBase
En esta sección se describe cómo habilitar y usar el Protocolo de escritorio remoto (RDP) para acceder al shell de HBase y usarlo luego para crear una tabla de ejemplo de HBase, agregar filas y mostrar a continuación las filas en la tabla.

Se supone que ha completado el procedimiento descrito en la primera sección y que, por tanto, ha creado correctamente un clúster de HBase.

**Habilitación de la conexión RDP al clúster de HBase**

1. Para habilitar una Conexión a Escritorio remoto al clúster de HDInsight, seleccione el clúster de HBase que ha creado y haga clic en la pestaña **CONFIGURACIÓN**. Haga clic en el botón **HABILITAR DE FORMA REMOTA** en la parte inferior de la página para habilitar la conexión RDP al clúster.
2. Proporcione las credenciales y la fecha de caducidad en el asistente **CONFIGURAR ESCRITORIO REMOTO** y haga clic en el círculo marcado en la parte inferior derecha. (La operación puede tardar unos minutos en completarse).
3. Para conectarse al clúster de HDInsight, haga clic en el botón **CONECTAR** en la parte inferior de la pestaña **CONFIGURACIÓN**.

 
**Abra el shell de HBase.**

1. En la sesión de RDP, haga clic en el acceso directo del **símbolo del sistema de Hadoop** situado en el escritorio.

2. Cambie la carpeta al directorio principal de HBase:
		
		cd %HBASE_HOME%\bin

3. Abra del shell de HBase:

		hbase shell


**Creación de una tabla de ejemplo y adición y recuperación de los datos**

1. Cree una tabla de ejemplo:

		create 'sampletable', 'cf1'

2. Agregue una fila a la tabla de ejemplo:

		put 'sampletable', 'row1', 'cf1:col1', 'value1'

3. Muestre las filas en la tabla de ejemplo:
	
		scan 'sampletable'

##<a name="hive-query"></a>Uso de Hive para consultar una tabla de HBase

Ahora que tiene un clúster de HBase aprovisionado y que ha creado una tabla, puede consultarla mediante Hive. En esta sección se crea una tabla de Hive que se asigna a la tabla de HBase y se usa para consultar los datos en la tabla de HBase.

**Para abrir el panel del clúster, siga estos pasos:**

1. Inicie sesión en el [Portal de administración de Azure][azure-management-portal]. 
2. Haga clic en **HDINSIGHT** en el panel izquierdo. Verá una lista de los clústeres creados, junto con el que acaba de crear en la última sección.
3. Haga clic en el nombre del clúster donde desea ejecutar el trabajo de Hive.
4. Haga clic en **ADMINISTRAR CLÚSTER** en la parte inferior de la página para abrir el panel del clúster. Se abre una página web en una pestaña de explorador diferente.   
5. Escriba el nombre de usuario y la contraseña de la cuenta de usuario de Hadoop.  El nombre de usuario predeterminado es **admin** y la contraseña es la que ha escrito durante el proceso de aprovisionamiento.  El panel tiene este aspecto:

	![HDInsight cluster dashboard.](http://i.imgur.com/tMwXlj9.jpg)


**Para ejecutar una consulta de Hive, siga estos pasos:**

1. Para crear una tabla de Hive con una asignación a la tabla de HBase, escriba a continuación el script de Hive en la ventana de la consola de Hive y haga clic en el botón **ENVIAR**. Asegúrese de que ha creado la tabla de ejemplo a la que se hace referencia aquí en HBase mediante el shell de HBase antes de ejecutar esta instrucción.
 
    	CREATE EXTERNAL TABLE hbasesampletable(rowkey STRING, col1 STRING, col2 STRING)
    	STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
    	WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,cf1:col1,cf1:col2')
    	TBLPROPERTIES ('hbase.table.name' = 'sampletable');

 
2. Para ejecutar una consulta de Hive sobre los datos de HBase, escriba a continuación el script de HiveQL en la ventana de la consola de Hive y haga clic en el botón **ENVIAR**.

     	SELECT count(*) FROM hbasesampletable;
 
4. Para recuperar los resultados de la consulta de Hive, haga clic en el vínculo **Ver detalles** en la ventana **Sesión de trabajo** cuando el trabajo termine de ejecutarse.


Nota: el vínculo del shell de HBase cambia la pestaña por el **shell de HBase**.



**Para examinar el archivo de salida, siga estos pasos:**

1. En el panel del clúster, haga clic en **Archivos** en la parte superior.
2. Haga clic en **Templeton-Job-Status**.
3. Haga clic en el número de GUID que tiene la hora de última modificación un poco después que la hora de inicio del trabajo que anotara anteriormente. Tome nota de este GUID.  Lo necesitará en la próxima sesión.
4. El **stdout** archivo tiene los datos que necesita en la próxima sección. Puede hacer clic en **stdout** para descargar una copia del archivo de datos que desea.

	
##<a name="hbase-powershell"></a>Uso de la biblioteca de cliente de REST de HBase para API de C# para .NET para crear una tabla de HBase y recuperar los datos de la tabla

La biblioteca de cliente de REST de HBase de Microsoft para proyectos de .NET se debe descargar de GitHub y el proyecto compilado para usar el SDK de .NET para HBase. El siguiente procedimiento incluye las instrucciones para esta tarea.

1. Descargue la [biblioteca de cliente de REST de HBase de Microsoft para .NET](https://github.com/hdinsight/hbase-sdk-for-net) si aún no ha satisfecho este requisito previo.

2. Abra Marlin.sln en Visual Studio (**Archivo** -> **Abrir** -> **Proyecto/Solución ...**) de la ubicación en la que se ha descargado. Seleccione **Ver** -> **Explorador de soluciones** para ver la solución 'Marlin' y su proyecto Microsoft.HBase.Client. Compile el proyecto **Marlin**; para ello, haga clic con el botón secundario en él en el **Explorador de soluciones** y seleccione **Compilar solución**. 

4. Cree una aplicación de consola nueva de Visual C#: Recupere el archivo Microsoft.HBase.Client.dll resultante y el archivo protobuf.dll compilado (desde el directorio ...\bin\debug\Microsoft.HBase.Client) y agréguelos a su proyecto de C#: Haga clic con el botón secundario en **Referencias**, seleccione **Agregar referencias...**, vaya a los dos ensamblados y cárguelos. [protobuf-net](http://code.google.com/p/protobuf-net/) es una implementación .NET del serializador de archivos binarios de búferes de protocolo de Google usado para la comunicación de datos.

5. Agregue las siguientes instrucciones using en la parte superior del archivo:
		
		using Microsoft.HBase.Client;
		using org.apache.hadoop.hbase.rest.protobuf.generated;

6. Cree una nueva instancia de un cliente de HBase mediante las credenciales del clúster y recupere la versión del clúster:

		// Create a new instance of an HBase client.
		var creds = new ClusterCredentials(new Uri("https://myclustername.azurehdinsight.net"), "myusername", "mypassword");
		var client = new HBaseClient(creds);

		// Retrieve the cluster version
		var version = client.GetVersion();
		Console.WriteLine(version);

7. Para crear una nueva tabla de HBase, use este código:

		// Create a new HBase table.
		var testTableSchema = new TableSchema();
		testTableSchema.name = "mytablename";
		testTableSchema.columns.Add(new ColumnSchema() { name = "d" });
		testTableSchema.columns.Add(new ColumnSchema() { name = "f" });
		client.CreateTable(testTableSchema);

8. Inserte datos en una tabla con este código:

		// Insert data into a table.
		var tableName = "mytablename";
		var testKey = "content";
		var testValue = "the force is strong in this column";
		var set = new CellSet();
		var row = new CellSet.Row { key = Encoding.UTF8.GetBytes(testKey) };
		set.rows.Add(row);

		var value = new Cell { column = Encoding.UTF8.GetBytes("d:starwars"), data = Encoding.UTF8.GetBytes(testValue) };
		row.values.Add(value);
		client.StoreCells(tableName, set);

9. Para recuperar una celda con su clave, use este código. 

		// Retrieve a cell with its key.
		var testKey = "content";
		var tableName = "mytablename";

		var cells = client.GetCells(tableName, testKey);
		// get the first value from the row.
		.WriteLine(Encoding.UTF8.GetString(cells.rows[0].values[0].data));
		// with the previous insert, it should yield: "the force is strong in this column"

10. Explore sobre las filas de una tabla con el siguiente código:

		//Scan over rows in a table.
		var creds = new ClusterCredentials(new Uri("https://myclustername.azurehdinsight.net"), "myusername", "mypassword");
		var client = new HBaseClient(creds);

		var tableName = "mytablename";

		// assume the table has integer keys and we want data between keys 25 and 35
		var scanSettings = new Scanner()
		{
    		batch = 10,
    		startRow = BitConverter.GetBytes(25),
    		endRow = BitConverter.GetBytes(35)
		};

		var scannerInfo = client.CreateScanner(tableName, scanSettings);
		CellSet next = null;
		while ((next = client.ScannerGetNext(scannerInfo)) != null)
		{
    		foreach (var row in next.rows)
    		{
        		// ... read the rows
    		}
		}



##<a name="summary"></a>Resumen
En este tutorial, ha aprendido a aprovisionar un clúster de HBase, a crear tablas y a ver los datos de esas tablas desde el shell de HBase. También ha aprendido a usar Hive para consultar los datos de las tablas de HBasey a usar las API de C# para HBase para crear una tabla de HBase y recuperar los datos de la tabla.

##<a name="next"></a> Pasos siguientes

[Información general de HBase de HDInsight][hdinsight-hbase-overview]:
HBase es una base de datos NoSQL de código abierto Apache basada en Hadoop que proporciona un acceso aleatorio y una coherencia fuerte para grandes cantidades de datos no estructurados y semiestructurados.

[Aprovisionamiento de clústeres de HBase en Red virtual de Azure][hdinsight-hbase-provision-vnet]:
Con la integración de red virtual, los clústeres de HBase se pueden implementar en la misma red virtual como sus aplicaciones para que estas puedan comunicarse directamente con HBase.

[Análisis de opiniones de Twitter con HBase en HDInsight][hbase-twitter-sentiment]:
Aprenda realizar [análisis de opiniones en tiempo real](http://en.wikipedia.org/wiki/Sentiment_analysis) de Big Data usando HBase en un clúster de Hadoop en HDInsight.

[hdinsight-hbase-overview]: ../hdinsight-hbase-overview/
[hdinsight-hbase-provision-vnet]: ../hdinsight-hbase-provision-vnet
[hdinsight-versions]: ../hdinsight-component-versioning/

[hdinsight-get-started-30]: ../hdinsight-get-started-30/

[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/

[hbase-twitter-sentiment]: ../hdinsight-hbase-analyze-twitter-sentiment/

[hdinsight-use-hive]: ../hdinsight-use-hive/

[hdinsight-storage]: ../hdinsight-use-blob-storage/



[azure-purchase-options]: http://azure.microsoft.com/en-us/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/en-us/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/en-us/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: http://azure.microsoft.com/en-us/documentation/articles/storage-create-storage-account/ 

[apache-hadoop]: http://hadoop.apache.org/

[powershell-download]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[powershell-install-configure]: ../install-configure-powershell/
[powershell-open]: ../install-configure-powershell/#Install


[img-hdi-dashboard]: ./media/hdinsight-get-started/HDI.dashboard.png
[img-hdi-dashboard-query-select]: ./media/hdinsight-get-started/HDI.dashboard.query.select.png
[img-hdi-dashboard-query-select-result]: ./media/hdinsight-get-started/HDI.dashboard.query.select.result.png







<!--HONumber=35_1-->
