<properties 
	pageTitle="Crear y cargar datos en tablas de Hive desde el almacenamiento de blobs de Azure | Microsoft Azure" 
	description="Crear tablas de subárbol y cargar datos de blob en tablas de subárbol" 
	services="machine-learning,storage" 
	documentationCenter="" 
	authors="hangzh-msft" 
	manager="jacob.spoelstra" 
	editor="cgronlun"  />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/12/2015" 
	ms.author="hangzh;bradsev" />

 
#Crear y cargar datos en tablas de subárbol desde el almacenamiento de blobs de Azure

Este **menú** vincula a temas en los que se describe cómo introducir datos en los entornos de destino en que se pueden almacenar y procesar los datos durante el proceso de análisis de Cortana (CAPS).

[AZURE.INCLUDE [cap-ingest-data-selector](../../includes/cap-ingest-data-selector.md)]

 
## Introducción
En **este documento**, se presentan consultas genéricas de Hive que crean tablas de Hive y cargan datos desde el almacenamiento de blobs de Azure. También se ofrecen algunas instrucciones acerca de las particiones de tablas de subárbol y de cómo utilizar el formato ORC para mejorar el rendimiento de las consultas.

## Requisitos previos
En este artículo se supone que ha:
 
* Creado una cuenta de almacenamiento de Azure. Si necesita instrucciones, consulte [Creación de una cuenta de almacenamiento de Azure](../hdinsight-get-started.md#storage). 
* Aprovisionado un clúster de Hadoop personalizado con el servicio HDInsight. Si necesita instrucciones, vea [Personalización de clústeres Hadoop de HDInsight de Azure para análisis avanzado](machine-learning-data-science-customize-hadoop-cluster.md).
* Habilitado el acceso remoto para el clúster, ha iniciado sesión y ha abierto la consola de la línea de comandos de Hadoop. Si necesita instrucciones, consulte [Acceso al nodo principal del clúster Hadoop](machine-learning-data-science-customize-hadoop-cluster.md#headnode). 

## Carga de datos en el almacenamiento de blobs de Azure
Si creó una máquina virtual de Azure siguiendo las instrucciones dadas en [Configuración de una máquina virtual de Azure para análisis avanzado](machine-learning-data-science-setup-virtual-machine.md), este archivo de script debió descargarse en el directorio *C:\\Users\\<nombre de usuario>\\Documents\\Data Science Scripts* de la máquina virtual. Estas consultas de subárbol solo requieren que conecte sus propios esquemas de datos y la configuración de almacenamiento de blobs de Azure en los campos adecuados para estar preparado para su envío.

Se supone que los datos de las tablas de Hive están en un formato tabular **sin comprimir** y que se han cargado los datos en el contenedor predeterminado (o en otro adicional) de la cuenta de almacenamiento que usa el clúster Hadoop.

Si desea practicar sobre _NYC Taxi Trip Data_, necesita descargar primero los 24 archivos <a href="http://www.andresmh.com/nyctaxitrips/" target="_blank">NYC Taxi Trip Data</a> (12 archivos de carreras y 12 de tarifas), **descomprimir** todos los archivos en archivos .csv y cargarlos en el contenedor predeterminado (o el contenedor adecuado) de la cuenta de almacenamiento de Azure que creó el procedimiento descrito en el tema [Personalización de clústeres Hadoop de HDInsight de Azure para tecnología y procesos de análisis avanzados](machine-learning-data-science-customize-hadoop-cluster.md). En esta [página](machine-learning-data-science-process-hive-walkthrough/#upload) encontrará el proceso de cargar los archivos .csv en el contenedor predeterminado de la cuenta de almacenamiento.

## Envío de una consulta de Hive
Las consultas de subárbol se pueden enviar desde la consola de línea de comandos de Hadoop del nodo principal del clúster de Hadoop. Para ello, inicie sesión en el nodo principal del clúster de Hadoop, abra la consola de la línea de comandos de Hadoop y envíe las consultas de Hive desde allí. Para obtener instrucciones sobre cómo hacerlo, vea [Envío de consultas de Hive a clústeres Hadoop de HDInsight en el proceso de análisis avanzado](machine-learning-data-science-process-hive-tables.md).

Los usuarios también pueden usar la consola de consultas (editor de subárbol) escribiendo la dirección URL

<nombre de clúster de https://&#60;Hadoop>.azurehdinsight.net/Home/HiveEditor

en un explorador web. Tenga en cuenta que se le pedirá que indique las credenciales de clúster de Hadoop para iniciar sesión, por lo que deberá tener estas credenciales a mano.

Además, puede [enviar trabajos de Hive mediante PowerShell](../hdinsight/hdinsight-submit-hadoop-jobs-programmatically.md#hive-powershell).


## <a name="create-tables"></a> Creación de tablas y base de datos de Hive

Las consultas de Hive se comparten en el [repositorio de Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_db_tbls_load_data_generic.hql) y se pueden descargar desde allí.

Esta es la consulta de subárbol que crea una tabla de subárbol.

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

- **<nombre de base de datos>**: nombre de la base de datos que los usuarios desean crear. Si los usuarios solo desean usar la base de datos predeterminada, se puede omitir la consulta *crear base de datos...* 
- **<nombre de tabla>**: nombre de la tabla que los usuarios quieren crear en la base de datos especificada. Si los usuarios quieren usar la base de datos predeterminada, se puede hacer referencia directamente a la tabla con *<nombre de tabla>* sin <nombre de base de datos>.
- **<separador de campos>**: separador que delimita los campos del archivo de datos que se cargará en la tabla de Hive. 
- **<separador de líneas>**: separador que delimita las líneas del archivo de datos. 
- **<ubicación de almacenamiento>**: ubicación de almacenamiento de Azure para guardar los datos de tablas de Hive. Si los usuarios no especifican *LOCATION <ubicación de almacenamiento>*, la base de datos y las tablas se almacenan de forma predeterminada en el directorio *hive/warehouse/* del contenedor predeterminado del clúster Hive. Si un usuario desea especificar la ubicación de almacenamiento, la ubicación de almacenamiento debe estar dentro del contenedor predeterminado para la base de datos y las tablas. A esta ubicación tiene que hacerse referencia como ubicación relativa al contenedor predeterminado del clúster en el formato de *'wasb:///<directorio 1>/'* o *'wasb:///<directorio 1>/<directorio 2>/'*, etc. Después de ejecutar la consulta, se crearán los directorios relativos dentro del contenedor predeterminado. 
- **TBLPROPERTIES("skip.header.line.count"="1")**: si el archivo de datos tiene una línea de encabezado, los usuarios deben agregar esta propiedad **al final** de la consulta *create table*. De lo contrario, se cargará la línea de encabezado como registro en la tabla. Si el archivo de datos no tiene una línea de encabezado, se puede omitir esta configuración en la consulta. 

## <a name="load-data"></a>Carga de datos en tablas de Hive
Esta es la consulta de subárbol que carga datos en una tabla de subárbol.

    LOAD DATA INPATH '<path to blob data>' INTO TABLE <database name>.<table name>;

- **<ruta de acceso a datos de blob>**: si el archivo blob que se va a cargar en la tabla de Hive se encuentra en el contenedor predeterminado del clúster Hadoop de HDInsight, *<ruta de acceso a datos de blob>* debe tener el formato *"wasb:///<directorio de este contenedor>/<nombre del archivo de blob>"*. El archivo blob también puede estar en un contenedor adicional del clúster de Hadoop de HDInsight. En este caso, *<ruta de acceso a datos de blob>* debe tener el formato *"wasb://<nombre del contenedor>@<nombre de cuenta de almacenamiento>.blob.core.windows.net/<nombre de archivo de blob>"*.

	>[AZURE.NOTE]Los datos blob que se van a cargar en la tabla de subárbol tienen que estar en el contenedor adicional o predeterminado de la cuenta de almacenamiento para el clúster de Hadoop. De lo contrario, la consulta *LOAD DATA* generará un error indicando que no puede acceder a los datos.


## <a name="partition-orc"></a>Temas avanzados: tabla con particiones y datos de Hive de almacén en formato ORC

Si los datos son grandes, crear particiones de la tabla será beneficioso para las consultas que solo necesitan examinar algunas particiones de la tabla. Por ejemplo, es razonable crear particiones de los datos de registro de un sitio web por fechas.

Además de crear particiones de tablas de subárbol, también es beneficioso almacenar los datos de subárbol en el formato ORC. Para obtener más información sobre la aplicación de formato ORC, vea <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC#LanguageManualORC-ORCFiles" target="_blank">El uso de archivos ORC mejora el rendimiento cuando Hive está leyendo, escribiendo y procesando datos</a>.

### Tabla con particiones
Esta es la consulta de subárbol que crea una tabla con particiones y carga datos en ella.

    CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<table name>
	(field1 string,
	...
	fieldN string
	)
    PARTITIONED BY (<partitionfieldname> vartype) ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>'
		 lines terminated by '<line separator>' TBLPROPERTIES("skip.header.line.count"="1");
	LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<partitioned table name> 
		PARTITION (<partitionfieldname>=<partitionfieldvalue>);

Al consultar tablas con particiones, se recomienda agregar la condición de partición al **comienzo** de la cláusula `where` ya que esto mejora la eficacia de la búsqueda de manera significativa.

    select 
		field1, field2, ..., fieldN
	from <database name>.<partitioned table name> 
	where <partitionfieldname>=<partitionfieldvalue> and ...;

### <a name="orc"></a>Almacenamiento de datos de Hive en formato ORC

Los usuarios no pueden cargar datos directamente desde el almacenamiento de blobs en tablas de subárbol que se almacenan en el formato ORC. Estos son los pasos que los usuarios deben llevar a cabo para cargar datos desde blobs de Azure en tablas de subárbol almacenadas en formato ORC.

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

2. Cree una tabla interna con el mismo esquema que la tabla externa del paso 1, con el mismo delimitador de campo, y almacene los datos de subárbol en el formato ORC.

		CREATE TABLE IF NOT EXISTS <database name>.<ORC table name> 
		(
			field1 string,
			field2 int,
			...
			fieldN date
		) 
		ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' STORED AS ORC;

3. Seleccionar datos desde la tabla externa del paso 1 e insertarlos en la tabla de ORC

		INSERT OVERWRITE TABLE <database name>.<ORC table name>
            SELECT * FROM <database name>.<external textfile table name>;

	>[AZURE.NOTE]Si la tabla TEXTFILE *<nombre de base de datos>.<nombre de la tabla de archivo de texto externo>* tiene particiones, en el PASO 3, el comando `SELECT * FROM <database name>.<external textfile table name>` seleccionará la variable de partición como un campo en el conjunto de datos devuelto. Si se inserta en *<nombre de base de datos>.<nombre de la tabla ORC>* se producirá un error ya que *<nombre de base de datos>.<nombre de la tabla ORC>* no tiene la variable de partición como un campo en el esquema de tabla. En este caso, los usuarios deben seleccionar específicamente los campos que se van a insertar en *<nombre de base de datos>.<nombre de la tabla ORC>* de la manera siguiente:

		INSERT OVERWRITE TABLE <database name>.<ORC table name> PARTITION (<partition variable>=<partition value>)
		   SELECT field1, field2, ..., fieldN
		   FROM <database name>.<external textfile table name> 
		   WHERE <partition variable>=<partition value>;

4. Es seguro quitar *<nombre de la tabla de archivo de texto externo>* cuando use la consulta siguiente una vez insertados todos los datos en *<nombre de base de datos>.<nombre de la tabla ORC>*:

		DROP TABLE IF EXISTS <database name>.<external textfile table name>;

Después de seguir este procedimiento, debe tener una tabla con datos en el formato ORC lista para su uso.

<!---HONumber=Oct15_HO3-->