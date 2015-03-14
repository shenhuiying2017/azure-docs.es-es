<properties 
	pageTitle="Configuración y consulta de tablas de HBase mediante Hive en HDInsight | Azure" 
	description="Introducción al uso de HBase con Hadoop en HDInsight. Obtenga información acerca de cómo crear tablas de HBase y consultarlas con Hive." 
	services="hdinsight" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="1/7/2015" 
	ms.author="bradsev"/>



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

HBase es una base de datos NoSQL de baja latencia que permite el procesamiento de transacciones en línea de Big Data. HBase se ofrece como un clúster administrado integrado en el entorno de Azure. Los clústeres están configurados para almacenar datos directamente en el almacenamiento de blobs de Azure, que proporciona una latencia baja y una mayor elasticidad de las opciones de rendimiento o coste. Esto permite que los clientes creen sitios web interactivos que trabajen con grandes conjuntos de datos, creen servicios que almacenen datos del sensor y telemetría desde millones de extremos y analicen estos datos con trabajos de Hadoop. Para obtener más información sobre HBase y los escenarios en los que se puede usar, vea [Información general de HBase de HDInsight][hdinsight-hbase-overview].

> [AZURE.NOTE] HBase (versión 0.98.0) solo está disponible para usarlo con clústeres de HDInsight 3.1 en HDInsight (basado en Apache Hadoop y YARN 2.4.0). Para obtener información de la versión, consulte [Novedades en las versiones de clústeres de Hadoop proporcionadas por HDInsight][hdinsight-versions]

##<a name="prerequisites"></a>Requisitos previos

Antes de empezar este tutorial, debe contar con lo siguiente:

- **Una suscripción de Azure** Para obtener más información sobre cómo obtener una suscripción, consulte [Opciones de compra][azure-purchase-options], [Ofertas para miembros][azure-member-offers] o [Prueba gratuita][azure-free-trial].
- **Una cuenta de almacenamiento de Azure** Para obtener instrucciones, consulte [Creación de una cuenta de almacenamiento][azure-create-storageaccount].
- **Una estación de trabajo** con Visual Studio 2013 instalado. Para obtener instrucciones, consulte [Instalación de Visual Studio](http://msdn.microsoft.com/library/e2h7fzkw.aspx).

##<a name="create-hbase-cluster"></a>Aprovisionamiento de un clúster de HBase en el Portal de Azure

En esta sección se describe cómo aprovisionar un clúster de HBase mediante el Portal de administración de Azure.


[AZURE.INCLUDE [provisioningnote](../includes/hdinsight-provisioning.md)]

**Para aprovisionar un clúster de HBase en el Portal de administración de Azure, siga estos pasos:** 


1. Inicie sesión en el [Portal de administración de Azure][azure-management-portal]. 
2. Haga clic en **NUEVO** en la parte inferior izquierda y, a continuación, haga clic en **SERVICIOS DE DATOS**, **HDINSIGHT** y **HBASE**. 
3. Escriba **NOMBRE DEL CLÚSTER**, **TAMAÑO DEL CLÚSTER**, CONTRASEÑA DE USUARIO DEL CLÚSTER y **CUENTA DE ALMACENAMIENTO**.
 
	![Choosing and HBase cluster type and entering cluster login credentials.][img-hdinsight-hbase-cluster-quick-create]

4. Haga clic en el icono de verificación en la parte inferior izquierda para crear el clúster de HBase.


##<a name="create-sample-table"></a>Creación de una tabla de ejemplo de HBase a partir del shell de HBase
En esta sección se describe cómo habilitar y usar el Protocolo de escritorio remoto (RDP) para acceder al shell de HBase y usarlo luego para crear una tabla de ejemplo de HBase, agregar filas y mostrar a continuación las filas en la tabla.

Se supone que ha completado el procedimiento descrito en la primera sección y que, por tanto, ha creado correctamente un clúster de HBase.

**Para habilitar la conexión RDP al clúster de HBase, siga estos pasos:**

1. En el Portal de administración, haga clic en **HDINSIGHT** a la izquierda para ver la lista de los clústeres existentes.
2. Haga clic en el clúster de HDInsight en el que desea abrir el shell de HBase.
3. Haga clic en **CONFIGURACIÓN** en la parte superior.
4. En la parte inferior, haga clic en **HABILITAR DE FORMA REMOTA**.
5. Escriba el nombre de usuario y la contraseña de RDP.  El nombre de usuario debe ser distinto al que se utiliza al aprovisionar el clúster. Los datos detallados en **CADUCA EL** pueden ser de hasta siete días a partir del actual.
6. Haga clic en la marca de verificación de la parte inferior derecha para habilitar Escritorio remoto.
7. Una vez habilitado RPD, haga clic en **CONECTAR** en la parte inferior de la pestaña **CONFIGURACIÓN** y siga las instrucciones.

 
**Para abrir el shell de HBase, siga estos pasos:**

1. En la sesión de RDP, haga clic en el acceso directo de la **línea de comandos de Hadoop** situada en el escritorio.

2. Cambie la carpeta al directorio principal de HBase:
		
		cd %HBASE_HOME%\bin

3. Abra del shell de HBase:

		hbase shell


**Para crear una tabla de ejemplo, agregar datos y recuperarlos, siga estos pasos:**

1. Cree una tabla de ejemplo:

		create 'sampletable', 'cf1'

2. Agregue una fila a la tabla de ejemplo:

		put 'sampletable', 'row1', 'cf1:col1', 'value1'

3. Muestre las filas en la tabla de ejemplo:
	
		scan 'sampletable'

**Comprobar el estado del clúster en la interfaz de usuario web de HBase**
	
HBase también incluye una interfaz de usuario web que ayuda a supervisar el clúster, por ejemplo, proporcionando estadísticas de solicitudes o información acerca de las regiones. En el clúster de HBase puede encontrar la interfaz de usuario web en la dirección de zookeepernode.


	http://zookeepernode:60010/master-status
	
En un clúster HighAvailability (HA), encontrará un vínculo al nodo maestro de HBase actualmente activo que hospeda la interfaz de usuario web.

**Carga masiva de una tabla de ejemplo**

1. Cree un archivo samplefile1.txt que contenga los datos siguientes y cárguelo en /tmp/samplefile1.txt en el almacenamiento de blobs de Azure:

		row1	c1	c2
		row2	c1	c2
		row3	c1	c2
		row4	c1	c2
		row5	c1	c2
		row6	c1	c2
		row7	c1	c2
		row8	c1	c2
		row9	c1	c2
		row10    c1	c2

2. Cambie la carpeta al directorio principal de HBase:
		
		cd %HBASE_HOME%\bin

3. Ejecute ImportTsv:

		hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,a:b,a:c" -Dimporttsv.bulk.output=/tmpOutput sampletable2 /tmp/samplefile1.txt

4. Cargue los resultados del comando anterior en HBase:

		hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /tmpOutput sampletable2



##<a name="hive-query"></a>Uso de Hive para consultar una tabla de HBase

Ahora que tiene un clúster de HBase aprovisionado y que ha creado una tabla de HBase, puede consultarla mediante Hive. En esta sección se crea una tabla de Hive que se asigna a la tabla de HBase y se usa para consultar los datos en la tabla de HBase.

**Para abrir el panel del clúster., siga estos pasos:**

1. Inicie sesión en el [Portal de administración de Azure][azure-management-portal]. 
2. Haga clic en **HDINSIGHT** en el panel izquierdo. Verá una lista de los clústeres creados, junto con el que acaba de crear en la última sección.
3. Haga clic en el nombre del clúster donde desea ejecutar el trabajo de Hive.
4. Haga clic en **CONSOLA DE CONSULTAS** en la parte inferior de la página para abrir el panel del clúster. Se abre una página web en una pestaña de explorador diferente.   
5. Escriba el nombre de usuario y la contraseña de la cuenta de usuario de Hadoop.  El nombre de usuario predeterminado es **admin** y la contraseña es la que ha escrito durante el proceso de aprovisionamiento. Se abre una nueva pestaña del explorador. 
6. Haga clic en **Editor de Hive** en la parte superior. El Editor de Hive tiene el siguiente aspecto:

	![HDInsight cluster dashboard.][img-hdinsight-hbase-hive-editor]





























**Para ejecutar consultas de Hive, siga estos pasos:**

1. Escriba el siguiente script de HiveQL en el Editor de Hive y haga clic en **ENVIAR** para crear una asignación de tabla de Hive a la tabla HBase. Antes de ejecutar esta instrucción, asegúrese de haber creado la tabla sampletable a la que se hace referencia aquí en HBase mediante el shell de HBase.
 
    	CREATE EXTERNAL TABLE hbasesampletable(rowkey STRING, col1 STRING, col2 STRING)
    	STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
    	WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,cf1:col1,cf1:col2')
    	TBLPROPERTIES ('hbase.table.name' = 'sampletable');

	Espere hasta que el **Estado** se actualice a **Completado**.
 
2. Escriba el siguiente script de HiveQL en el Editor de Hive y haga clic en el botón **ENVIAR**. La consulta de Hive consulta los datos de la tabla HBase:

     	SELECT count(*) FROM hbasesampletable;
 
4. Para recuperar los resultados de la consulta de Hive, haga clic en el vínculo **Ver detalles** de la ventana **Sesión de trabajo** cuando el trabajo termine de ejecutarse. El resultado del trabajo debe ser 1 porque solo ha incluido un registro en la tabla HBase.




**Para examinar el archivo de salida, siga estos pasos:**

1. En la Consola de consultas, haga clic en **Explorador de archivos** en la parte superior.
2. Haga clic en la cuenta de almacenamiento de Azure usada como sistema de archivos predeterminado para el clúster de HDInsight.
3. Haga clic en el nombre del clúster de HBase. El contenedor de la cuenta de almacenamiento de Azure predeterminado usa el nombre del clúster.
4. Haga clic en **user**.
5. Haga clic en **admin**. Este es el nombre de usuario de Hadoop.
6. Haga clic en el nombre del trabajo cuya hora de **Última modificación** coincida con la hora a la que se ejecutó la consulta SELECT de Hive.
4. Haga clic en **stdout**. Guarde el archivo y ábralo con el Bloc de notas. El resultado debería ser 1.

	![HDInsight HBase Hive Editor File Browser][img-hdinsight-hbase-file-browser]
	
##<a name="hbase-powershell"></a>Uso de la biblioteca de cliente de REST de HBase para API de C# para .NET para crear una tabla de HBase y recuperar los datos de la tabla

La biblioteca de cliente de REST de HBase de Microsoft para proyectos de .NET se debe descargar de GitHub y el proyecto compilado para usar el SDK de .NET para HBase. El siguiente procedimiento incluye las instrucciones para esta tarea.

1. Cree una nueva aplicación C# de la consola de Escritorio de Windows de Visual Studio.
2. Abra la Consola del Administrador de paquetes NuGet. Para ello haga clic en el menú **HERRAMIENTAS**, **Administrador de paquetes de NuGet**, **Consola del Administrador de paquetes**.
3. Ejecute el siguiente comando NuGet en la consola:

	Install-Package Microsoft.HBase.Client

5. Agregue las siguientes instrucciones using en la parte superior del archivo:
		
		using Microsoft.HBase.Client;
		using org.apache.hadoop.hbase.rest.protobuf.generated;

6. Reemplace la función Main por el siguiente contenido:

        static void Main(string[] args)
        {
            string clusterURL = "https://<yourHBaseClusterName>.azurehdinsight.net";
            string hadoopUsername= "<yourHadoopUsername>";
            string hadoopUserPassword = "<yourHadoopUserPassword>";

            string hbaseTableName = "sampleHbaseTable";

            // Create a new instance of an HBase client.
            ClusterCredentials creds = new ClusterCredentials(new Uri(clusterURL), hadoopUsername, hadoopUserPassword);
            HBaseClient hbaseClient = new HBaseClient(creds);

            // Retrieve the cluster version
            var version = hbaseClient.GetVersion();
            Console.WriteLine("The HBase cluster version is " + version);

            // Create a new HBase table.
            TableSchema testTableSchema = new TableSchema();
            testTableSchema.name = hbaseTableName;
            testTableSchema.columns.Add(new ColumnSchema() { name = "d" });
            testTableSchema.columns.Add(new ColumnSchema() { name = "f" });
            hbaseClient.CreateTable(testTableSchema);

            // Insert data into the HBase table.
            string testKey = "content";
            string testValue = "the force is strong in this column";
            CellSet cellSet = new CellSet();
            CellSet.Row cellSetRow = new CellSet.Row { key = Encoding.UTF8.GetBytes(testKey) };
            cellSet.rows.Add(cellSetRow);

            Cell value = new Cell { column = Encoding.UTF8.GetBytes("d:starwars"), data = Encoding.UTF8.GetBytes(testValue) };
            cellSetRow.values.Add(value);
            hbaseClient.StoreCells(hbaseTableName, cellSet);

            // Retrieve a cell by its key.
            cellSet = hbaseClient.GetCells(hbaseTableName, testKey);
            Console.WriteLine("The data with the key '" + testKey + "' is: " + Encoding.UTF8.GetString(cellSet.rows[0].values[0].data));
            // with the previous insert, it should yield: "the force is strong in this column"

		    //Scan over rows in a table. Assume the table has integer keys and you want data between keys 25 and 35. 
		    Scanner scanSettings = new Scanner()
		    {
    		    batch = 10,
    		    startRow = BitConverter.GetBytes(25),
    		    endRow = BitConverter.GetBytes(35)
		    };

		    ScannerInformation scannerInfo = hbaseClient.CreateScanner(hbaseTableName, scanSettings);
		    CellSet next = null;
            Console.WriteLine("Scan results");

            while ((next = hbaseClient.ScannerGetNext(scannerInfo)) != null)
		    {
    		    foreach (CellSet.Row row in next.rows)
    		    {
                    Console.WriteLine(row.key + " : " + Encoding.UTF8.GetString(row.values[0].data));
    		    }
		    }

            Console.WriteLine("Press ENTER to continue ...");
            Console.ReadLine();
        }

7. Establezca las tres primeras variables en la función Main.
8. Presione **F5** para ejecutar la aplicación.



##<a name="next"></a> Pasos siguientes
En este tutorial, ha aprendido a aprovisionar un clúster de HBase, a crear tablas y a ver los datos de esas tablas desde el shell de HBase. También ha aprendido a usar Hive para consultar los datos de las tablas de HBasey a usar las API de C# para HBase para crear una tabla de HBase y recuperar los datos de la tabla. 

Para obtener más información, consulte:

- [Información general de HBase de HDInsight][hdinsight-hbase-overview]:
HBase es una base de datos NoSQL de código abierto Apache basada en Hadoop que proporciona un acceso aleatorio y una coherencia fuerte para grandes cantidades de datos no estructurados y semiestructurados.
- [Aprovisionamiento de clústeres de HBase en Red virtual de Azure][hdinsight-hbase-provision-vnet]:
Con la integración de red virtual, los clústeres de HBase se pueden implementar en la misma red virtual como sus aplicaciones para que estas puedan comunicarse directamente con HBase.
- [Análisis de opiniones de Twitter con HBase en HDInsight][hbase-twitter-sentiment]:
Descubra cómo realizar [análisis de opinión](http://en.wikipedia.org/wiki/Sentiment_analysis) en tiempo real de grandes volúmenes de datos con HBase en un clúster de Hadoop en HDInsight.

[hdinsight-hbase-overview]: ../hdinsight-hbase-overview/
[hdinsight-hbase-provision-vnet]: ../hdinsight-hbase-provision-vnet
[hdinsight-versions]: ../hdinsight-component-versioning/
[hbase-twitter-sentiment]: ../hdinsight-hbase-analyze-twitter-sentiment/
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/ 

[img-hdinsight-hbase-cluster-quick-create]: ./media/hdinsight-hbase-get-started/hdinsight-hbase-quick-create.png
[img-hdinsight-hbase-hive-editor]: ./media/hdinsight-hbase-get-started/hdinsight-hbase-hive-editor.png
[img-hdinsight-hbase-file-browser]: ./media/hdinsight-hbase-get-started/hdinsight-hbase-file-browser.png








<!--HONumber=42-->
