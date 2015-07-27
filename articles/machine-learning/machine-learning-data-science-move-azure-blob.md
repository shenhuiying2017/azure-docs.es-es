<properties 
	pageTitle="Mover datos hacia y desde el almacenamiento de blobs de Azure | Microsoft Azure" 
	description="Mover datos hacia y desde el almacenamiento de blobs de Azure" 
	services="machine-learning,storage" 
	documentationCenter="" 
	authors="msolhab" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/29/2015" 
	ms.author="sunliangms;sachouks;mohabib;bradsev" />

# Mover datos hacia y desde el almacenamiento de blobs de Azure

El artículo [Escenarios para la Tecnología y procesos de análisis avanzado (ADAPT) en Aprendizaje automático de Azure](../machine-learning-data-science-plan-sample-scenarios.md) le ayudará a determinar los recursos que necesita para una variedad de flujos de trabajo de ciencia de datos utilizados en el proceso de análisis avanzado. Si necesita mover datos hacia el almacenamiento de blobs de Azure y desde él en función de su escenario, utilice uno de los métodos siguientes:

- [Uso del Explorador de almacenamiento de Azure](#explorer)
- [Uso de la utilidad de línea de comandos AzCopy](#AzCopy)
- [Uso el SDK de Azure en Python](#PythonSDK)


> [AZURE.TIP]Como alternativa, puede usar [Factoría de datos de Azure](https://azure.microsoft.com/es-es/services/data-factory/) para crear y programar una canalización que descargará datos desde el almacenamiento de blobs de Azure, los pasará a un servicio web publicado de Aprendizaje automático de Azure, recibirá los resultados de análisis predictivos y cargará los resultados en el almacenamiento. Consulte [Creación de canalizaciones predictivas mediante Factoría de datos de Azure y Aprendizaje automático de Azure](../data-factory/data-factory-create-predictive-pipelines.md) para obtener más información.

<para></para>

> [AZURE.NOTE]Para ver una introducción completa al almacenamiento de blobs de Azure, consulte [Aspectos básicos del blob de Azure](../storage-dotnet-how-to-use-blobs.md) y [Servicio BLOB de Azure](https://msdn.microsoft.com/library/azure/dd179376.aspx).

Antes de cargar o descargar datos, debe conocer su nombre de cuenta de almacenamiento de Azure y la clave de cuenta. Para ver instrucciones sobre cómo obtener esta información, consulte la sección "Visualización, copia y regeneración de claves de acceso de almacenamiento" [Administración de cuentas de almacenamiento](../storage-create-storage-account.md). En este documento se supone que tiene una cuenta de almacenamiento de Azure y las claves de almacenamiento correspondientes.


<a id="explorer"></a>
## Usar el Explorador de almacenamiento de Azure 

El Explorador de almacenamiento de Azure es una herramienta gratuita basada en Windows para inspeccionar y modificar datos de una cuenta de almacenamiento de Azure. Se puede descargar en el [Explorador de almacenamiento de Azure](http://azurestorageexplorer.codeplex.com/). En los pasos siguientes se describe cómo cargar y descargar datos mediante el Explorador de almacenamiento de Azure.

1.  Iniciar el Explorador de almacenamiento de Azure 
2.  Si la cuenta de almacenamiento a la que desea acceder no se ha agregado al Explorador de almacenamiento de Azure, haga clic en el botón "Agregar cuenta" para agregar la cuenta. Si ya se ha agregado, seleccione la cuenta en la lista desplegable "Seleccione una cuenta de almacenamiento". ![Creación del espacio de trabajo][1] <br>
3. Escriba el nombre de la cuenta de almacenamiento y la clave de la cuenta de almacenamiento y, a continuación, haga clic en Agregar cuenta de almacenamiento. Puede agregar varias cuentas de almacenamiento y cada cuenta se mostrará en una pestaña. Los contenedores de esta cuenta de almacenamiento se muestran en el panel izquierdo. Seleccione un contenedor para ver los blobs del contenedor en el panel derecho. ![Creación del espacio de trabajo][2] <br> ![Creación del espacio de trabajo][3] <br>
4. Cargue datos haciendo clic en el botón "Cargar". Seleccione uno o varios archivos para cargarlos desde el sistema de archivos y haga clic en "Abrir" para empezar a cargarlos.
5. Para descargar datos seleccione el blob en el contenedor correspondiente y haga clic en el botón "Descargar".

<a id="AzCopy"></a>
## Uso de AzCopy

AzCopy es una utilidad de línea de comandos para cargar y descargar datos.

**Advertencia** Si está usando una máquina diferente a la máquina virtual que se configuró anteriormente en el proceso de análisis avanzado, instale AzCopy mediante las siguientes instrucciones de instalación: [Descarga e instalación de AzCopy](../storage-use-azcopy.md#install).

###Ejemplos de carga/descarga de archivos a/desde blobs:

	# Uploading from local file system
	AzCopy /Source:<your_local_directory> /Dest: https://<your_account_name>.blob.core.windows.net/<your_container_name> /DestKey:<your_account_key> /S 

	# Downloading blobs to local file system
	AzCopy /Source:https://<your_account_name>.blob.core.windows.net/<your_container_name>/<your_sub_directory_at_blob>  /Dest:<your_local_directory> /SourceKey:<your_account_key> /Pattern:<file_pattern> /S

	# Transferring blobs between Azure containers
	AzCopy /Source:https://<your_account_name1>.blob.core.windows.net/<your_container_name1>/<your_sub_directory_at_blob1> /Dest:https://<your_account_name2>.blob.core.windows.net/<your_container_name2>/<your_sub_directory_at_blob2> /SourceKey:<your_account_key1> /DestKey:<your_account_key2> /Pattern:<file_pattern> /S
	
	<your_account_name>: your storage account name
	<your_account_key>: your storage account key
	<your_container_name>: your container name
	<your_sub_directory_at_blob>: the sub directory in the container 
	<your_local_directory>: directory of local file system where files to be uploaded from or the directory of local file system files to be downloaded to
	<file_pattern>: pattern of file names to be transferred. The standard wildcards are supported

> [AZURE.TIP]1. Al cargar archivos, /S cargará los archivos de forma recursiva. Sin este parámetro, no se cargarán los archivos del subdirectorio. 2. Al descargar el archivo, /S buscará el contenedor de manera recursiva hasta que se descarguen todos los archivos del directorio especificado y sus subdirectorios, o todos los archivos que coincidan con el patrón especificado en el directorio especificado y sus subdirectorios. 3. No se puede especificar un archivo de blob específico para descargar mediante el parámetro /Source. Para descargar un archivo específico, especifique el nombre del archivo blob que se va a descargar mediante el parámetro /Pattern. El parámetro /S se puede usar para que AzCopy busque de forma recursiva un patrón de nombre de archivo. Sin el parámetro de patrón, AzCopy descargará todos los archivos en ese directorio.

Para un uso detallado de AzCopy, consulte [Introducción a la utilidad de línea de comandos AzCopy](../storage-use-azcopy.md#install).


<a id="PythonSDK"></a>
## Usar Python

Con la API de Python proporcionada en el SDK de Azure, puede

- Crear un contenedor
- Cargar un blob en un contenedor
- Descargar blobs
- Enumerar los blobs de un contenedor
- Eliminar un blob

En esta sección se describe cómo enumerar, cargar y descargar blobs. Para obtener más detalles del uso de la API de Python, consulte [Uso del servicio de almacenamiento de blobs desde Python](../storage-python-how-to-use-blob-storage.md).

> [AZURE.NOTE]Si está utilizando una máquina diferente de la máquina virtual que se configuró anteriormente en el proceso de análisis avanzado, deberá instalar el [SDK de Azure para Python](../python-how-to-install.md) para usar el código de ejemplo siguiente.

### Cargar datos en blob

Agregue el siguiente fragmento de código cerca de la parte superior de cualquier código de Python en el que desee obtener acceso mediante programación al almacenamiento de Azure:

	from azure.storage import BlobService

El objeto **BlobService** permite trabajar con contenedores y blobs. El código siguiente crea un objeto BlobService utilizando el nombre de la cuenta de almacenamiento y la clave de la cuenta: Reemplace el nombre de la cuenta y la clave de cuenta por la cuenta real y la clave.
	
	blob_service = BlobService(account_name="<your_account_name>", account_key="<your_account_key>")

Use los siguientes métodos para cargar datos en un blob:
 
1. put_block_blob_from_path (carga el contenido de un archivo desde la ruta especificada)
2. put_block_blob_from_file (carga el contenido de una secuencia o archivo ya abierto)
3. put_block_blob_from_bytes (carga una matriz de bytes)
4. put_block_blob_from_text (carga el valor de texto especificado con la codificación especificada)
 
El siguiente código de ejemplo carga un archivo local en un contenedor:
	
	blob_service.put_block_blob_from_path("<your_container_name>", "<your_blob_name>", "<your_local_file_name>")

El siguiente código de ejemplo carga todos los archivos (excepto los directorios) en un directorio local para el almacenamiento de blobs:

	from azure.storage import BlobService
	from os import listdir
	from os.path import isfile, join
	
	# Set parameters here
	ACCOUNT_NAME = "<your_account_name>"
	ACCOUNT_KEY = "<your_account_key>"
	CONTAINER_NAME = "<your_container_name>"
	LOCAL_DIRECT = "<your_local_directory>"		
	
	blob_service = BlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)
	# find all files in the LOCAL_DIRECT (excluding directory)
	local_file_list = [f for f in listdir(LOCAL_DIRECT) if isfile(join(LOCAL_DIRECT, f))]
	
	file_num = len(local_file_list)
	for i in range(file_num):
	    local_file = join(LOCAL_DIRECT, local_file_list[i])
	    blob_name = local_file_list[i]
	    try:
	        blob_service.put_block_blob_from_path(CONTAINER_NAME, blob_name, local_file)
	    except:
	        print "something wrong happened when uploading the data %s"%blob_name

### Descargar datos del blob

Utilice los siguientes métodos para descargar datos de un blob: 1. get_blob_to_path 2. get_blob_to_file 3. get_blob_to_bytes 4. get_blob_to_text

Estos métodos que realizan la fragmentación necesaria cuando el tamaño de los datos supera los 64 MB.

El código de ejemplo siguiente descarga el contenido de un blob de un contenedor en un archivo local:

	blob_service.get_blob_to_path("<your_container_name>", "<your_blob_name>", "<your_local_file_name>")

El siguiente código de ejemplo descarga todos los blobs de un contenedor. Usa list_blobs para obtener la lista de blobs disponibles del contenedor y los descarga en un directorio local.

	from azure.storage import BlobService
	from os.path import join
	
	# Set parameters here
	ACCOUNT_NAME = "<your_account_name>"
	ACCOUNT_KEY = "<your_account_key>"
	CONTAINER_NAME = "<your_container_name>"
	LOCAL_DIRECT = "<your_local_directory>"		
	
	blob_service = BlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)
	
	# List all blobs and download them one by one
	blobs = blob_service.list_blobs(CONTAINER_NAME)
	for blob in blobs:
	    local_file = join(LOCAL_DIRECT, blob.name)
	    try:
	        blob_service.get_blob_to_path(CONTAINER_NAME, blob.name, local_file)
	    except:
	        print "something wrong happened when downloading the data %s"%blob.name

<!-- Images -->

[1]: ./media/machine-learning-data-science-move-azure-blob/data-science-process-uploading-data-to-blob-storage-img1.png
[2]: ./media/machine-learning-data-science-move-azure-blob/data-science-process-uploading-data-to-blob-storage-img2.png
[3]: ./media/machine-learning-data-science-move-azure-blob/data-science-process-uploading-data-to-blob-storage-img3.png
 

<!---HONumber=July15_HO3-->