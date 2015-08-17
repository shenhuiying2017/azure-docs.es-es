<properties 
	pageTitle="Crear y cargar datos en tablas de Hive desde el almacenamiento de blobs de Azure | Microsoft Azure" 
	description="Crear tablas de subárbol y cargar datos de blob en tablas de subárbol" 
	services="machine-learning" 
	documentationCenter="" 
	authors="hangzh-msft" 
	manager="paulettm" 
	editor="cgronlun"  />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/02/2015" 
	ms.author="hangzh;bradsev" />

 
#Crear y cargar datos en tablas de subárbol desde el almacenamiento de blobs de Azure
 
En este documento, se presentan las consultas de subárbol genéricas que crean tablas de subárbol y cargan datos desde el almacenamiento de blobs de Azure. Estas consultas de subárbol se comparten en el repositorio de GitHub. [Repositorio de GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_db_tbls_load_data_generic.hql).


Si crea una máquina virtual de Azure siguiendo las instrucciones de "Configurar una máquina virtual de Azure con IPython Notebook Server", este archivo de script se ha descargado en el directorio `C:\Users<user name>\Documents\Data Science Scripts` de la máquina virtual. Deberá conectar su propio esquema de datos y configuración de almacenamiento de blobs de Azure en los campos correspondientes de estas consultas y estas consultas de subárbol deberán estar listas para su envío.

Se asume que los datos de las tablas de Hive están en formato tabular **sin comprimir** y que los datos se han cargado en el contenedor adicional o predeterminado de la cuenta de almacenamiento utilizada por el clúster de Hadoop. Si los usuarios desean practicar sobre _NYC Taxi Trip Data_, primero necesitan [descargar los 24 archivos](http://www.andresmh.com/nyctaxitrips/) (12 archivos de carreras y 12 archivos de tarifas), **descomprimir** todos los archivos en archivos .csv y cargarlos en el contenedor adicional o predeterminado de la cuenta de almacenamiento de Azure que se usan cuando [se personaliza el clúster de Hadoop de HDInsight de Azure](machine-learning-data-science-customize-hadoop-cluster.html).

Las consultas de subárbol se pueden enviar en la línea de comandos de Hadoop en el nodo principal del clúster de Hadoop. Necesita:

1. [Habilitar el acceso remoto al nodo principal del clúster de Hadoop e iniciar sesión en el nodo principal](machine-learning-data-science-customize-hadoop-cluster.md).
2. [Enviar las consultas de Hive en la línea de comandos de Hadoop en el nodo principal](machine-learning-data-science-hive-queries.md).

Los usuarios también pueden usar la [consola de consultas (editor de Hive)] escribiendo la URL en un explorador web `https://<Hadoop cluster name>.azurehdinsight.net/Home/HiveEditor (se le pedirá que introduzca las credenciales de clúster de Hadoop para iniciar sesión) o puede [enviar trabajos de Hive +mediante PowerShell](hdinsight-submit-hadoop-jobs-programmatically.md).

- [Paso 1: Crear tablas y base de datos de Hive](#create-tables)
- [Paso 2: Cargar datos en tablas de Hive](#load-data)
- [Temas avanzados: tabla con particiones y datos de Hive de almacén en formato ORC](#partition-orc)

## <a name="create-tables"></a> Creación de tablas y base de datos de Hive

    create database if not exists <database name>;
	CREATE EXTERNAL TABLE if not exists <database name>.<table name>
	(
		field1 string, 
		field2 int, 
		field3 float, 
		field4 double, 
		...,
		fieldN string
	) 
	ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' lines terminated by '<line separator>' 
	STORED AS TEXTFILE LOCATION '<storage location>' TBLPROPERTIES("skip.header.line.count"="1");

Estas son las descripciones de los campos que los usuarios necesitan para conectar y otras configuraciones:

- `<database name>`: el nombre de la base de datos que los usuarios desean crear. Si los usuarios solo desean usar la base de datos predeterminada, se puede omitir la consulta `create database...`. 
- `<table name>`: el nombre de la tabla que los usuarios desean crear dentro de la base de datos especificada. Si los usuarios desean usar la base de datos predeterminada, `<table name>` puede hacer referencia directamente a la tabla sin `<database name>.`.
- `<field separator>`: el separador que separa los campos del archivo de datos que se cargará en la tabla de subárbol. 
- `<line separator>`: el separador que separa líneas del archivo de datos. 
- `<storage location>`: la ubicación de almacenamiento de Azure para guardar los datos de tablas de subárbol. Si los usuarios no especifican `LOCATION '<storage location>'`, de forma predeterminada la base de datos y las tablas se almacenan en el directorio `hive/warehouse/` del contenedor predeterminado del clúster de Hive. Si un usuario desea especificar la ubicación de almacenamiento, la ubicación de almacenamiento debe estar dentro del contenedor predeterminado para la base de datos y las tablas. Esta ubicación tiene que referirse como ubicación relativa para el contenedor predeterminado del clúster en el formato de `'wasb:///<directory 1>/'` o `'wasb:///<directory 1>/<directory 2>/'`, etc. Después de ejecutar la consulta, se crearán los directorios relativos dentro del contenedor predeterminado. 
- `TBLPROPERTIES("skip.header.line.count"="1")`: si el archivo de datos tiene una línea de encabezado, los usuarios deben agregar esta propiedad al **final** de la consulta `create table`. De lo contrario, se cargará la línea de encabezado como registro en la tabla. Si el archivo de datos no tiene una línea de encabezado, se puede omitir esta configuración en la consulta. 

## <a name="load-data"></a>Carga de datos en tablas de Hive

    LOAD DATA INPATH '<path to blob data>' INTO TABLE <database name>.<table name>;

- `<path to blob data>`: Si el archivo blob que se va a cargar en la tabla de Hive se encuentra en el contenedor predeterminado del clúster de Hadoop de HDInsight, la `<path to blob data>` debe tener el formato `'wasb:///<directory in this container>/<blob file name>'`. El archivo de blob también debe estar en el contenedor adicional del clúster de Hadoop de HDInsight. En este caso, `<path to blob data>` debe tener el formato `'wasb://<container name>@<storage account name>.blob.windows.core.net/<blob file name>'`.

> [AZURE.NOTE]Los datos blob que se van a cargar en la tabla de subárbol tienen que estar en el contenedor adicional o predeterminado de la cuenta de almacenamiento para el clúster de Hadoop. De lo contrario, la consulta `LOAD DATA` generará un error indicando que no puede tener acceso a los datos.


## <a name="partition-orc"></a>Temas avanzados: tabla con particiones y datos de Hive de almacén en formato ORC

Si los datos son grandes, la partición de la tabla será beneficiosa para las consultas que solo necesitan examinar algunas particiones de la tabla. Por ejemplo, es razonable crear particiones de los datos de registro de un sitio web por fechas.

Además de crear particiones de la tabla, también es beneficioso almacenar los datos de subárbol en formato ORC. ORC significa "Columnas de filas optimizadas". Consulte _"[El uso de archivos ORC mejora el rendimiento cuando Hive lee, escribe y procesa datos](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC#LanguageManualORC-ORCFiles)."_ para obtener más detalles.

### Tabla con particiones

Las consultas de crear una tabla con particiones y cargar datos en ella son las siguientes:

    CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<table name>
	(field1 string,
	...
	fieldN string
	)
    PARTITIONED BY (<partitionfieldname> vartype) ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>'
		 lines terminated by '<line separator>' TBLPROPERTIES("skip.header.line.count"="1");
	LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<partitioned table name> 
		PARTITION (<partitionfieldname>=<partitionfieldvalue>);

Al consultar tablas con particiones, se recomienda agregar la condición de partición al **principio** de la cláusula `where` para que se pueda mejorar significativamente la eficacia de la búsqueda.

    select 
		field1, field2, ..., fieldN
	from <database name>.<partitioned table name> 
	where <partitionfieldname>=<partitionfieldvalue> and ...;

### <a name="orc"></a>Almacenamiento de datos de Hive en formato ORC

Los usuarios no pueden cargar datos directamente de blob en tablas de subárbol en formato de almacenamiento ORC. Estos son los pasos que los usuarios deben llevar a cabo para cargar datos desde blobs de Azure en tablas de subárbol almacenadas en formato ORC.

1. Cree una tabla externa **STORED AS TEXTFILE** y cargue datos del almacenamiento de blobs en la tabla.

		CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<external textfile table name>
		(
			field1 string,
			field2 int,
			...
			fieldN date
		)
		ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' 
			lines terminated by '<line separator>' STORED AS TEXTFILE 
			LOCATION 'wasb:///<directory in Azure blob>' TBLPROPERTIES("skip.header.line.count"="1");

		LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<table name>;

2. Cree una tabla interna con el mismo esquema que la tabla externa del paso 1 y el mismo delimitador de campo. Y almacene los datos de subárbol en el formato de ORC

		CREATE TABLE IF NOT EXISTS <database name>.<ORC table name> 
		(
			field1 string,
			field2 int,
			...
			fieldN date
		) 
		ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' STORED AS ORC;

3. Seleccionar datos desde la tabla externa del paso 1 e insertarlos en la tabla de ORC

		INSERT OVERWRITE TABLE <database name>.<ORC table name> SELECT * FROM <database name>.<external textfile table name>;

> [AZURE.NOTE]Si la tabla TEXTFILE `<database name>.<external textfile table name>` tiene particiones, en el PASO 3, `SELECT * FROM <database name>.<external textfile table name>` seleccionará la variable de partición como un campo en el conjunto de datos devuelto. Al insertarla en el `<database name>.<ORC table name>` se generará un error puesto que `<database name>.<ORC table name>` no tiene la variable de partición como campo en el esquema de tabla. En este caso, los usuarios deban seleccionar de manera específica los campos que se van a insertar en `<database name>.<ORC table name>` de la siguiente manera:

		INSERT OVERWRITE TABLE <database name>.<ORC table name> PARTITION (<partition variable>=<partition value>)
		      SELECT field1, field2, ..., fieldN
		      FROM <database name>.<external textfile table name> 
		      WHERE <partition variable>=<partition value>;

4. Es seguro quitar el `<external textfile table name>` mediante la siguiente consulta cuando todos los datos se han insertado en `<database name>.<ORC table name>`:

		DROP TABLE IF EXISTS <database name>.<external textfile table name>;

Ahora tenemos una tabla con datos en el formato ORC lista para su uso.
 

<!---HONumber=August15_HO6-->