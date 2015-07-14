<properties
	pageTitle="Configuración y consulta de tablas de HBase mediante Hive en HDInsight | Azure"
	description="Introducción al uso de HBase con Hadoop en HDInsight. Obtenga información acerca de cómo crear tablas de HBase y consultarlas con Hive."
	services="hdinsight"
	documentationCenter=""
	authors="mumian"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/09/2015"
	ms.author="jgao"/>



# Introducción a Apache HBase en HDInsight

Aprenda a crear y a consultar tablas de HBase mediante Hive en Hadoop en HDInsight.

HBase es una base de datos NoSQL de baja latencia que permite el procesamiento de transacciones en línea de Big Data. HBase se ofrece como un clúster administrado que se integra en el entorno de Azure. Los clústeres están configurados para almacenar datos directamente en el almacenamiento de blobs de Azure, el cual proporciona una baja latencia y una mayor elasticidad en las opciones de coste y rendimiento. Esto permite que los clientes creen sitios web interactivos que trabajen con grandes conjuntos de datos, creen servicios que almacenen datos del sensor y telemetría desde millones de extremos y analicen estos datos con trabajos de Hadoop. Para obtener más información sobre HBase y los escenarios en los que se puede usar, vea [Información general de HBase de HDInsight][hdinsight-hbase-overview].

> [AZURE.NOTE]HBase (versión 0.98.0) solo está disponible para usarlo con clústeres de HDInsight 3.1 en HDInsight (basado en Apache Hadoop y YARN 2.4.0). Para obtener información de la versión, consulte [Novedades en las versiones de clústeres de Hadoop proporcionadas por HDInsight][hdinsight-versions].

## Requisitos previos

Antes de empezar este tutorial, debe contar con lo siguiente:

- **Una suscripción de Azure**: para obtener más información sobre cómo obtener una suscripción, consulte [Opciones de compra][azure-purchase-options], [Ofertas para miembros][azure-member-offers] o [Prueba gratuita][azure-free-trial].
- **Una cuenta de almacenamiento de Azure**: para obtener instrucciones, consulte [Creación de una cuenta de almacenamiento][azure-create-storageaccount].
- **Una estación de trabajo** con Visual Studio 2013 instalado: para obtener instrucciones, consulte [Instalación de Visual Studio](http://msdn.microsoft.com/library/e2h7fzkw.aspx).

## Aprovisionamiento de un clúster de HBase

[AZURE.INCLUDE [provisioningnote](../includes/hdinsight-provisioning.md)]

**Para aprovisionar un clúster de HBase usando el Portal de Azure, siga estos pasos:**


1. Inicie sesión en el [Portal de Azure][azure-management-portal].
2. Haga clic en **NUEVO** en la parte inferior izquierda y, a continuación, haga clic en **SERVICIOS DE DATOS** > **HDINSIGHT** > **HBASE**.

	También puede utilizar la opción CREACIÓN PERSONALIZADA.
3. Escriba **NOMBRE DEL CLÚSTER**, **TAMAÑO DEL CLÚSTER**, CONTRASEÑA DE USUARIO DEL CLÚSTER y **CUENTA DE ALMACENAMIENTO**.

	![Selección de un tipo de clúster de HBase y especificación de las credenciales de inicio de sesión del clúster.][img-hdinsight-hbase-cluster-quick-create]

	El NOMBRE DE USUARIO DE HADOOP predeterminado es admin. Puede personalizar el nombre mediante la opción CREACIÓN PERSONALIZADA.

	> [AZURE.WARNING]Para lograr alta disponibilidad de servicios de HBase, debe aprovisionar un clúster que contenga al menos **tres** nodos. Esto garantiza que, si un nodo deja de funcionar, las regiones de datos de HBase están disponibles en otros nodos.

4. Haga clic en el icono de verificación en la parte inferior derecha para crear el clúster de HBase.

>[AZURE.NOTE]Después de elimina un clúster de HBase, puede crear otro clúster de HBase mediante el mismo blob de forma predeterminada. El nuevo clúster seleccionará las tablas de HBase que creó en el clúster original.

## Creación de una tabla de ejemplo de HBase a partir del shell de HBase
En esta sección se describe cómo usar el shell de HBase para crear tablas de HBase, agregar filas y mostrar las filas. Para obtener acceso al shell de HBase, debe habilitar primero el Protocolo de escritorio remoto (RDP) y, a continuación, realizar una conexión RDP al clúster de HBase. Para obtener las instrucciones, consulte [Administración de clústeres de Hadoop en HDInsight mediante el Portal de Azure][hdinsight-manage-portal].


**Para usar el shell de HBase, siga estos pasos:**

1. En la sesión de RDP, haga clic en el acceso directo de la **línea de comandos de Hadoop** situada en el escritorio.
2. Cambie la carpeta al directorio principal de HBase:

		cd %HBASE_HOME%\bin
3. Abra del shell de HBase:

		hbase shell

4. Cree una HBase con una familia de columnas e inserte una fila:

		create 'sampletable', 'cf1'
		put 'sampletable', 'row1', 'cf1:col1', 'value1'
		scan 'sampletable'

	Para obtener más información acerca del esquema de la tabla de Hbase, consulte [Introducción al diseño de esquema de HBase][hbase-schema]. Para ver más comandos de HBase, consulte [Guía de referencia de Apache HBase][hbase-quick-start].
5. Enumere las tablas de HBase:

		list

**Comprobación del estado del clúster en la interfaz de usuario web de HBase**

HBase también incluye una interfaz de usuario web que puede usar como ayuda para supervisar el clúster. Por ejemplo, podría solicitar estadísticas o información acerca de las regiones. En el clúster de HBase puede encontrar la interfaz de usuario web en la dirección de zookeepernode:


	http://zookeepernode:60010/master-status

En un clúster de alta disponibilidad, encontrará un vínculo al nodo maestro de HBase actualmente activo que hospeda la interfaz de usuario web.

**Carga masiva de una tabla de ejemplo**

1. Desde el shell de HBase, cree una tabla de HBase con dos familias de columnas:

		create 'Contacts', 'Personal', 'Office'


3. Cree una lista de contactos que contenga los siguientes datos y cárguelo en una carpeta llamada /tmp/contacts.txt en el almacenamiento de blobs de Azure. Para obtener instrucciones, consulte [Carga de datos para trabajos de Hadoop en HDInsight][hdinsight-upload-data].

		8396	Calvin Raji	230-555-0191	5415 San Gabriel Dr.
		16600	Karen Wu	646-555-0113	9265 La Paz
		4324	Karl Xie	508-555-0163	4912 La Vuelta
		16891	Jonathan Jackson	674-555-0110	40 Ellis St.
		3273	Miguel Miller	397-555-0155	6696 Anchor Drive
		3588	Osarumwense Agbonile	592-555-0152	1873 Lion Circle
		10272	Julia Lee	870-555-0110	3148 Rose Street
		4868	Jose Hayes	599-555-0171	793 Crawford Street
		4761	Caleb Alexander	670-555-0141	4775 Kentucky Dr.
		16443	Terry Chander	998-555-0171	771 Northridge Drive

2. En una línea de comandos de Hadoop, cambie la carpeta al directorio principal de HBase:

		cd %HBASE_HOME%\bin

3. Ejecute ImportTsv. ImportTsv es una herramienta que carga datos en formato TSV en HBase. Tiene dos usos distintos: cargar datos de formato TSV del sistema de archivos distribuido de Hadoop (HDFS) en HBase y preparar los archivos que se van a cargar. Para obtener más información, consulte [Guía de referencia de Apache HBase][hbase-reference].

		hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name, Personal:HomePhone, Office:Address" -Dimporttsv.bulk.output=/tmpOutput Contacts /tmp/contacts.txt

4. Cargue los resultados del comando anterior en HBase:

		hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /tmpOutput Contacts

## Uso de Hive para consultar una tabla de HBase

Ahora que ha aprovisionado un clúster de HBase y ha creado una tabla de HBase, puede consultar los datos de la tabla mediante el uso de Hive. En esta sección se crea una tabla de Hive que se asigna a la tabla de HBase y se usa para consultar los datos en la tabla de HBase.

**Para abrir el panel de clúster, siga estos pasos:**

1. Inicie sesión en el [Portal de Azure][azure-management-portal].
2. Haga clic en **HDINSIGHT** en el panel izquierdo. Verá una lista de los clústeres creados, junto con el que acaba de crear en la última sección.
3. Haga clic en el nombre del clúster donde desea ejecutar el trabajo de Hive.
4. Haga clic en **CONSOLA DE CONSULTAS** en la parte inferior de la página para abrir el panel del clúster. Se abre una página web en una pestaña de explorador diferente.
5. Escriba el nombre de usuario y la contraseña de la cuenta de usuario de Hadoop. El nombre de usuario predeterminado es **admin** y la contraseña es la que ha escrito durante el proceso de aprovisionamiento. Se abre una nueva pestaña del explorador.
6. Haga clic en **Editor de Hive** en la parte superior de la página. El Editor de Hive tiene el siguiente aspecto:

	![Panel de clúster de HDInsight.][img-hdinsight-hbase-hive-editor]





























**Para ejecutar consultas de Hive, siga estos pasos:**

1. Escriba el siguiente script de HiveQL en el Editor de Hive y haga clic en **ENVIAR** para crear una tabla de Hive que se asigne a la tabla de HBase. Antes de ejecutar esta instrucción, asegúrese de haber creado la tabla de ejemplo a la que se hace referencia aquí en HBase mediante el shell de HBase.

		CREATE EXTERNAL TABLE hbasecontactstable(rowkey STRING, name STRING, homephone STRING, office STRING)
		STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
		WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:HomePhone,Office:Address')
		TBLPROPERTIES ('hbase.table.name' = 'Contacts');

	Espere hasta que el **Estado** se actualice a **Completado**.

2. Escriba el siguiente script de HiveQL en el Editor de Hive y haga clic en el botón **ENVIAR**. La consulta de Hive consulta los datos de la tabla HBase:

     	SELECT count(*) FROM hbasecontactstable;

4. Para recuperar los resultados de la consulta de Hive, haga clic en el vínculo **Ver detalles** de la ventana **Sesión de trabajo** cuando el trabajo termine de ejecutarse. Habrá un único archivo de salida de trabajo porque se coloca un registro en la tabla de HBase.




**Para examinar el archivo de salida, siga estos pasos:**

1. En la Consola de consultas, haga clic en **Explorador de archivos**.
2. Haga clic en la cuenta de almacenamiento de Azure usada como sistema de archivos predeterminado para el clúster de HBase.
3. Haga clic en el nombre del clúster de HBase. El contenedor de la cuenta de almacenamiento de Azure predeterminado usa el nombre del clúster.
4. Haga clic en **Usuario**, y, a continuación, haga clic en **Admin**. Este es el nombre de usuario de Hadoop.
6. Haga clic en el nombre del trabajo cuya hora de **Última modificación** coincida con la hora a la que se ejecutó la consulta SELECT de Hive.
4. Haga clic en **stdout**. Guarde el archivo y ábralo con el Bloc de notas. Habrá un archivo de salida.

	![Explorador de archivos de Editor de Hive de HBase para HDInsight][img-hdinsight-hbase-file-browser]

## Uso de la biblioteca de cliente de la API de REST de HBase para C# de .NET para crear una tabla de HBase y recuperar los datos de la tabla

Debe descargar la biblioteca de cliente de la API de REST de HBase para .NET desde GitHub y compilar el proyecto para que pueda usar el SDK para .NET de HBase. El siguiente procedimiento incluye las instrucciones para esta tarea.

1. Cree una nueva aplicación C# de la consola de Escritorio de Windows de Visual Studio.
2. Abra la Consola del Administrador de paquetes de NuGet. Para ello, haga clic en el menú **HERRAMIENTAS** > **Administrador de paquetes de NuGet** > **Consola del Administrador de paquetes**.
3. Ejecute el siguiente comando NuGet en la consola:

		Install-Package Microsoft.HBase.Client

5. Agregue las siguientes instrucciones **using** en la parte superior del archivo:

		using Microsoft.HBase.Client;
		using org.apache.hadoop.hbase.rest.protobuf.generated;

6. Reemplace la función **Main** por el siguiente contenido:

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

7. Establezca las tres primeras variables en la función **Main**.
8. Presione **F5** para ejecutar la aplicación.



## Pasos siguientes
En este tutorial, ha aprendido a aprovisionar un clúster de HBase, a crear tablas y a ver los datos de esas tablas desde el shell de HBase. También ha aprendido a usar Hive para consultar los datos de las tablas de HBase y a usar las API de REST de C# para HBase para crear una tabla de HBase y recuperar los datos de la tabla.

Para obtener más información, consulte:

- [Información general de HBase de HDInsight][hdinsight-hbase-overview]\: HBase es una base de datos NoSQL de código abierto Apache basada en Hadoop que proporciona acceso aleatorio y una coherencia sólida para grandes cantidades de datos no estructurados y semiestructurados.
- [Aprovisionamiento de clústeres de HBase en Red virtual de Azure][hdinsight-hbase-provision-vnet]\: con la integración de redes virtuales, los clústeres de HBase se pueden implementar en la misma red virtual que sus aplicaciones para que estas puedan comunicarse directamente con HBase.
- [Configuración de la replicación de HBase en HDInsight](hdinsight-hbase-geo-replication.md): aprenda a configurar la replicación de HBase entre dos centros de datos de Azure. 
- [Análisis de opiniones de Twitter con HBase en HDInsight][hbase-twitter-sentiment]\: descubra cómo realizar [análisis de opinión](http://en.wikipedia.org/wiki/Sentiment_analysis) en tiempo real de grandes volúmenes de datos con HBase en un clúster de Hadoop en HDInsight.

[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hbase-reference]: http://hbase.apache.org/book.html#importtsv
[hbase-schema]: http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf
[hbase-quick-start]: http://hbase.apache.org/book.html#quickstart





[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md
[hdinsight-versions]: hdinsight-component-versioning.md
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/

[img-hdinsight-hbase-cluster-quick-create]: ./media/hdinsight-hbase-get-started/hdinsight-hbase-quick-create.png
[img-hdinsight-hbase-hive-editor]: ./media/hdinsight-hbase-get-started/hdinsight-hbase-hive-editor.png
[img-hdinsight-hbase-file-browser]: ./media/hdinsight-hbase-get-started/hdinsight-hbase-file-browser.png

<!--HONumber=54-->