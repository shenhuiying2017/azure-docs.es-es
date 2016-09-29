<properties
   pageTitle="Copia de datos desde blobs de almacenamiento de Azure a Almacén de Data Lake| Microsoft Azure"
   description="Use la herramienta AdlCopy para copiar datos desde los blobs de almacenamiento de Azure a Almacén de Data Lake."
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/19/2016"
   ms.author="nitinme"/>

# Copia de datos de los blobs de almacenamiento de Azure en el Almacén de Data Lake

> [AZURE.SELECTOR]
- [Uso de DistCp](data-lake-store-copy-data-wasb-distcp.md)
- [Uso de AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)

Azure Data Lake Store proporciona una herramienta de línea de comandos, [AdlCopy](http://aka.ms/downloadadlcopy), para copiar datos desde los siguientes orígenes:

* Desde los blobs de Almacenamiento de Azure en Data Lake Store. No puede utilizar AdlCopy para copiar datos del Almacén de Data Lake a los blobs de Almacenamiento de Azure.

* Entre dos cuentas de Azure Data Lake Store.

Además, puede usar la herramienta AdlCopy en dos modos diferentes:

* **Independiente**, donde la herramienta usa recursos de Almacén de Data Lake para realizar la tarea.
* **Con una cuenta de Análisis de Data Lake**, donde las unidades asignadas a la cuenta de Análisis de Data Lake se usan para realizar la operación de copia. Puede que desee usar esta opción cuando quiera realizar las tareas de copia de forma predecible.

##Requisitos previos

Antes de empezar este artículo, debe tener lo siguiente:

- **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).
- **Habilitación de la suscripción de Azure** para la vista previa pública del Almacén de Data Lake. Consulte las [instrucciones](data-lake-store-get-started-portal.md#signup).
- Contenedor de **blobs de almacenamiento de Azure** con algunos datos.
- **Cuenta de Análisis de Azure Data Lake (opcional)**. Consulte [Introducción a Análisis de Azure Data Lake](../data-lake-analytics/data-lake-analytics-get-started-portal.md) para obtener instrucciones sobre cómo crear una cuenta de Almacén de Data Lake.
- **Herramienta AdlCopy**. Instale la herramienta AdlCopy desde [http://aka.ms/downloadadlcopy](http://aka.ms/downloadadlcopy).

## Sintaxis de la herramienta AdlCopy

Use la siguiente sintaxis para trabajar con la herramienta AdlCopy.

	AdlCopy /Source <Blob or Data Lake Store source> /Dest <Data Lake Store destination> /SourceKey <Key for Blob account> /Account <Data Lake Analytics account> /Unit <Number of Analytics units> /Pattern 

A continuación, se describen los parámetros de la sintaxis:

| Opción | Description |
|-----------|------------|
| Origen | Especifica la ubicación de los datos de origen en el blob de almacenamiento de Azure. El origen puede ser un contenedor de blobs, un blob u otra cuenta de Data Lake Store. |
| Dest | Especifica el destino de Almacén de Data Lake al que copiar. |
| SourceKey | Especifica la clave de acceso de almacenamiento para el origen de blobs de almacenamiento de Azure. Esto solo es necesario si el origen es un contenedor de blobs o un blob. |
| Cuenta | **Opcional**. Utilícelo si desea usar una cuenta de Análisis de Azure Data Lake para ejecutar el trabajo de copia. Si usa la opción /Account en la sintaxis, pero no especifica una cuenta de Análisis de Data Lake, AdlCopy usa una cuenta predeterminada para ejecutar el trabajo. Además, si usa esta opción, debe agregar el origen (blob de almacenamiento de Azure) y el destino (Almacén de Azure Data Lake) como orígenes de datos para la cuenta de Análisis de Data Lake. |
| Unidades | Especifica la cantidad de unidades de Análisis de Data Lake que se usará para el trabajo de copia. Esta opción es obligatoria si usa la opción **/Account** para especificar la cuenta de Análisis de Data Lake.
| Patrón | Especifica un patrón Regex que indica qué blobs o archivos se van a copiar. AdlCopy usa una coincidencia que distingue mayúsculas de minúsculas. El patrón predeterminado que se usa cuando no se especifica ninguno es copiar todos los elementos. No se admite la especificación de varios patrones de archivos.                                                                                                                                                                                                                                                                                                                                               



## Uso de AdlCopy (en modo independiente) para copiar datos desde un blob de Almacenamiento de Azure

1. Abra un símbolo del sistema y vaya al directorio donde está instalada la herramienta AdlCopy, normalmente `%HOMEPATH%\Documents\adlcopy`.

2. Ejecute el siguiente comando para copiar un blob específico desde el contenedor de origen a un Almacén de Data Lake:

		AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>

	Por ejemplo:

		AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log /dest swebhdfs://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

	
	>[AZURE.NOTE] La sintaxis anterior especifica el archivo que se va a copiar en una carpeta de la cuenta de Data Lake Store. La herramienta AdlCopy crea una carpeta si el nombre de carpeta especificado no existe.

	Se le pedirá que escriba las credenciales de la suscripción a Azure en la que tiene la cuenta de Azure Data Lake Store. Verá un resultado similar al siguiente:

		Initializing Copy.
		Copy Started.
		100% data copied.
		Finishing Copy.
		Copy Completed. 1 file copied.

3. También puede copiar todos los blobs desde un contenedor a la cuenta de Almacén de Data Lake con el siguiente comando:

		AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/ /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>		

	Por ejemplo:

		AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest adl://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

## Uso de AdlCopy (en modo independiente) para copiar datos desde otra cuenta de Data Lake Store

También puede usar AdlCopy para copiar datos entre dos cuentas de Data Lake Store.

1. Abra un símbolo del sistema y vaya al directorio donde está instalada la herramienta AdlCopy, normalmente `%HOMEPATH%\Documents\adlcopy`.

2. Ejecute el siguiente comando para copiar un archivo específico de una cuenta de Data Lake Store a otra.

		AdlCopy /Source adl://<source_adls_account>.azuredatalakestore.net/<path_to_file> /dest adl://<dest_adls_account>.azuredatalakestore.net/<path>/

	Por ejemplo:

		AdlCopy /Source adl://mydatastore.azuredatalakestore.net/mynewfolder/909f2b.log /dest adl://mynewdatalakestore.azuredatalakestore.net/mynewfolder/

	>[AZURE.NOTE] La sintaxis anterior especifica el archivo que se va a copiar en una carpeta de la cuenta de Data Lake Store de destino. La herramienta AdlCopy crea una carpeta si el nombre de carpeta especificado no existe.

	Se le pedirá que escriba las credenciales de la suscripción a Azure en la que tiene la cuenta de Azure Data Lake Store. Verá un resultado similar al siguiente:

		Initializing Copy.
		Copy Started.|
		100% data copied.
		Finishing Copy.
		Copy Completed. 1 file copied.

3. El siguiente comando copia todos los archivos de una carpeta específica de la cuenta de Data Lake Store de origen en una carpeta de la cuenta de Data Lake Store de destino.

		AdlCopy /Source adl://mydatastore.azuredatalakestore.net/mynewfolder/ /dest adl://mynewdatalakestore.azuredatalakestore.net/mynewfolder/

## Uso de AdlCopy (con una cuenta de Data Lake Analytics) para copiar datos

También puede usar la cuenta de Análisis de Data Lake para ejecutar el trabajo AdlCopy para copiar datos desde los blobs de almacenamiento de Azure al Almacén de Data Lake. Con frecuencia, usaría esta opción cuando los datos que se van a migrar se encuentran en el rango de gigabytes y terabytes y desea obtener un mejor rendimiento predecible.

Para usar la cuenta de Data Lake Analytics con AdlCopy para copiar desde un blob de Almacenamiento de Azure, se debe agregar el origen (blob de Almacenamiento de Azure) como origen de datos para la cuenta de Data Lake Analytics. Para obtener instrucciones sobre cómo agregar orígenes de datos adicionales a la cuenta de Análisis de Data Lake, consulte [Administración de orígenes de datos de la cuenta de Análisis de Data Lake](..//data-lake-analytics/data-lake-analytics-manage-use-portal.md#manage-account-data-sources).

>[AZURE.NOTE] Si va a copiar desde una cuenta de Azure Data Lake Store como origen mediante una cuenta de Data Lake Analytics, no es necesario asociar la cuenta de Data Lake Store con la cuenta de Data Lake Analytics. El requisito para asociar el almacén de origen a la cuenta de Data Lake Analytics solo se aplica cuando el origen es una cuenta de Almacenamiento de Azure.

Ejecute el siguiente comando para copiar de un blob de Almacenamiento de Azure a una cuenta de Data Lake Store mediante la cuenta de Data Lake Analytics:

	AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Account <data_lake_analytics_account> /Unit <number_of_data_lake_analytics_units_to_be_used>

Por ejemplo:

	AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest swebhdfs://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Account mydatalakeanalyticaccount /Units 2


De forma similar, ejecute el siguiente comando para copiar de un blob de Almacenamiento de Azure a una cuenta de Data Lake Store mediante la cuenta de Data Lake Analytics:

	AdlCopy /Source adl://mysourcedatalakestore.azuredatalakestore.net/mynewfolder/ /dest adl://mydestdatastore.azuredatalakestore.net/mynewfolder/ /Account mydatalakeanalyticaccount /Units 2

## Uso de AdlCopy para copiar datos mediante la coincidencia de patrones

En esta sección, aprenderá a usar AdlCopy para copiar datos de un origen (en el siguiente ejemplo, se usan blobs de Almacenamiento de Azure) a una cuenta de Data Lake Store de destino mediante la coincidencia de patrones. Por ejemplo, puede usar los pasos siguientes para copiar todos los archivos con la extensión .csv del blob de origen al de destino.

1. Abra un símbolo del sistema y vaya al directorio donde está instalada la herramienta AdlCopy, normalmente `%HOMEPATH%\Documents\adlcopy`.

2. Ejecute el siguiente comando para copiar todos los archivos con la extensión *.csv de un blob específico del contenedor de origen a una cuenta de Data Lake Store:

		AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Pattern *.csv

	Por ejemplo:

		AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/FoodInspectionData/ /dest adl://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Pattern *.csv

	
## Facturación

* Si usa la herramienta AdlCopy de forma independiente, se le facturarán los costos de salida por migrar datos, si la cuenta de Almacenamiento de Azure de origen no está en la misma región que el Almacén de Data Lake.

* Si usa la herramienta AdlCopy con la cuenta de Análisis de Data Lake, se aplicarán las [tarifas de facturación de Análisis de Data Lake](https://azure.microsoft.com/pricing/details/data-lake-analytics/) estándar.

## Consideraciones para usar AdlCopy

* AdlCopy (para la versión 1.0.5) admite la copia de datos desde orígenes que en conjunto tienen más de miles de archivos y carpetas. Sin embargo, si tiene problemas al copiar un conjunto de datos grande, puede distribuir los archivos o carpetas en subcarpetas diferentes y usar la ruta de acceso a esas subcarpetas como origen en su lugar.

## Pasos siguientes

- [Protección de los datos en el Almacén de Data Lake](data-lake-store-secure-data.md)
- [Uso de Análisis de Azure Data Lake con el Almacén de Data Lake](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Uso de HDInsight de Azure con el Almacén de Data Lake](data-lake-store-hdinsight-hadoop-use-portal.md)

<!---HONumber=AcomDC_0914_2016-->