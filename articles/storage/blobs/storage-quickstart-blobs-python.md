---
title: "Inicio rápido de Azure: transferencia de objetos a y desde Azure Blob Storage mediante Python | Microsoft Docs"
description: "Aprendizaje rápido para transferir objetos a y desde Azure Blob Storage mediante Python"
services: storage
documentationcenter: storage
author: ruthogunnnaike
manager: cwatson
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 10/12/2017
ms.author: v-ruogun
ms.openlocfilehash: 44ec416a814ff6a5fef79ef21e2f54ce4ce4da17
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/17/2017
---
#  <a name="transfer-objects-tofrom-azure-blob-storage-using-python"></a>Transferencia de objetos a y desde Azure Blob Storage mediante Python
En este tutorial de inicio rápido, aprenderá a usar Python para cargar, descargar y enumerar blobs en bloques en un contenedor en Azure Blob Storage. 

## <a name="prerequisites"></a>Requisitos previos

Para completar esta guía de inicio rápido: 
* Instalación de [Python](https://www.python.org/downloads/)
* Descargue e instale el [SDK de Azure Storage para Python](storage-python-how-to-use-blob-storage.md#download-and-install-azure-storage-sdk-for-python). 

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="create-a-storage-account-using-the-azure-portal"></a>Creación de una cuenta de almacenamiento mediante Azure Portal

En primer lugar, cree una nueva cuenta de almacenamiento de uso general que se utilizará para este tutorial de inicio rápido. 

1. Vaya a [Azure Portal](https://portal.azure.com) e inicie sesión con su cuenta de Azure. 
2. En el menú de concentrador, seleccione **Nuevo** > **Almacenamiento** > **Cuenta de almacenamiento: blob, archivo, tabla, cola**. 
3. Escriba un nombre para la cuenta de almacenamiento. El nombre deben tener entre tres y 24 caracteres, y solo pueden contener números y letras minúsculas. También debe ser único.
4. Establezca `Deployment model` en **Administrador de recursos**.
5. Establezca `Account kind` en **Uso general**.
6. Establezca `Performance` en **Estándar**. 
7. Establezca `Replication` en **Almacenamiento con redundancia local (LRS)**.
8. Establezca `Storage service encryption` en **Deshabilitado**.
9. Establezca `Secure transfer required` en **Deshabilitado**.
10. Seleccione su suscripción. 
11. Para `resource group`, cree uno nuevo y asígnele un nombre único. 
12. Seleccione el valor de `Location` que desee usar para la cuenta de almacenamiento.
13. Active **Anclar al panel** y haga clic en **Crear** para crear la cuenta de almacenamiento. 

Después de crear la cuenta de almacenamiento, se ancla al panel. Haga clic en ella para abrirla. En **CONFIGURACIÓN**, haga clic en **Claves de acceso**. Seleccione una clave, copie el nombre de la cuenta de almacenamiento en el Portapapeles y péguelo en el Bloc de notas para utilizarlo posteriormente.

## <a name="download-the-sample-application"></a>Descarga de la aplicación de ejemplo
La [aplicación de ejemplo](https://github.com/Azure-Samples/storage-blobs-python-quickstart.git) utilizada en este tutorial de inicio rápido es una aplicación con Python básica.  

Use [git](https://git-scm.com/) para descargar una copia de la aplicación en su entorno de desarrollo. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-python-quickstart.git 
```

Este comando clona el repositorio en la carpeta git local. Para abrir el programa de Python, busque la carpeta storage-blobs-python-quickstart y el archivo example.py.  

## <a name="configure-your-storage-connection-string"></a>Configuración de la cadena de conexión de almacenamiento.
En la aplicación, debe proporcionar el nombre y la clave de la cuenta de almacenamiento para crear un objeto `BlockBlobService`. Abra el archivo `example.py` en el Explorador de soluciones en el IDE. Reemplace los valores **accountname** y **accountkey** con el nombre de cuenta y la clave. 

```python 
block_blob_service = BlockBlobService(account_name='accountname', account_key='accountkey') 
```

## <a name="run-the-sample"></a>Ejecución del ejemplo
En este ejemplo se crea un archivo de prueba en la carpeta "Documents". El programa de ejemplo carga el archivo de prueba en Blob Storage, enumera los blobs en el contenedor y descarga el archivo con un nuevo nombre. 

Ejecute el ejemplo. La salida siguiente es un ejemplo de la salida devuelta al ejecutar la aplicación:
  
```
Temp file = C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

Uploading to Blob storage as blobQuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

List blobs in the container
         Blob name: QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

Downloading blob to C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078_DOWNLOADED.txt
```
Al presionar cualquier tecla para continuar, el programa de muestra elimina el contenedor de almacenamiento y los archivos. Antes de continuar, compruebe la carpeta "Documents" de los dos archivos. Puede abrirlos y ver que son idénticas.

También puede usar una herramienta como [Explorador de Azure Storage](http://storageexplorer.com) para ver los archivos de Blob Storage. El Explorador de Azure Storage es una herramienta gratuita multiplataforma que permite acceder a la información de la cuenta de almacenamiento. 

Después de haber comprobado los archivos, presione cualquier tecla para finalizar la demostración y eliminar los archivos de prueba. Ahora que sabe lo que hace el ejemplo, abra el archivo example.py para examinar el código. 

## <a name="get-references-to-the-storage-objects"></a>Obtención de referencias a los objetos de almacenamiento
Lo primero que hay que hacer es crear las referencias a los objetos usados para acceder a Blob Storage y administrarlo. Estos objetos dependen unos de otros, y cada uno es utilizado por el siguiente de la lista.

* Cree una instancia del objeto **BlockBlobService**, que apunta a Blob service en la cuenta de almacenamiento. 

* Cree una instancia del objeto **CloudBlobContainer**, que representa el contenedor al que está accediendo. Los contenedores se usan para organizar los blobs al igual que se usan las carpetas en el equipo para organizar los archivos.

Una vez que tenga CloudBlobContainer, puede crear una instancia del objeto **CloudBlockBlob** que apunte al blob específico en el que está interesado y realizar operaciones como carga, descarga y copia.

En esta sección, se crean instancias de los objetos, se crea un contenedor y luego se establecen permisos en el contenedor para que los blobs sean públicos. El contenedor se denomina **quickstartblobs**. 


```python 
# Create the BlockBlockService that is used to call the Blob service for the storage account
block_blob_service = BlockBlobService(account_name='accountname', account_key='accountkey') 
 
# Create a container called 'quickstartblobs'.
container_name ='quickstartblobs'
block_blob_service.create_container(container_name) 

# Set the permission so the blobs are public.
block_blob_service.set_container_acl(container_name, public_access=PublicAccess.Container)
```
## <a name="upload-blobs-to-the-container"></a>Carga de blobs al contenedor

Blob Storage admite blobs en bloques, blobs en anexos y blobs en páginas. Los blobs en bloques son los que se usan con más frecuencia y serán los que utilicemos en este tutorial de inicio rápido.  

Para cargar un archivo en un blob, obtenga la ruta de acceso completa del archivo combinando el nombre del directorio y el nombre de archivo en la unidad local. A continuación, puede cargar el archivo en la ruta de acceso especificada con el método **create\_blob\_from\_path**. 

El código de ejemplo crea un archivo local que se usará para la carga y descarga, almacenando el archivo que se va a cargar como **file\_path\_to\_file** y el nombre del blob como **local\_file\_name**. En el ejemplo siguiente se carga el archivo al contenedor denominado **quickstartblobs**.

```python
# Create a file in Documents to test the upload and download.
local_path=os.path.expanduser("~\Documents")
local_file_name ="QuickStart_" + str(uuid.uuid4()) + ".txt"
full_path_to_file =os.path.join(local_path, local_file_name)

# Write text to the file.
file = open(full_path_to_file,  'w')
file.write("Hello, World!")
file.close()

print("Temp file = " + full_path_to_file)
print("\nUploading to Blob storage as blob" + local_file_name)

# Upload the created file, use local_file_name for the blob name
block_blob_service.create_blob_from_path(container_name, local_file_name, full_path_to_file)
```

Existen varios métodos de carga que puede usar con Blob Storage. Por ejemplo, si tiene una secuencia de memoria, puede utilizar el método **create\_blob\_from\_stream** en lugar de **create\_blob\_from\_path**. 

Los blobs en bloques pueden tener un tamaño de hasta 4,7 TB y pueden ser cualquier cosa, desde hojas de cálculo de Excel a archivos de vídeo grandes. Los blobs en páginas se utilizan principalmente para los archivos VHD usados para respaldar VM IaaS. Los blobs en anexos se utilizan para el registro, por ejemplo, cuando desea escribir en un archivo y luego sigue agregando más información. La mayoría de los objetos almacenados en Blob Storage son blobs en bloques.

## <a name="list-the-blobs-in-a-container"></a>Enumerar los blobs de un contenedor

Obtenga una lista de archivos del contenedor con el método **list_blobs**. Este método devuelve un generador. El código siguiente recupera la lista de blobs, luego los recorre y se muestran los nombres de los blobs encontrados en un contenedor.  

```python
# List the blobs in the container
print("\nList blobs in the container")
    generator = block_blob_service.list_blobs(container_name)
    for blob in generator:
        print("\t Blob name: " + blob.name)
```

## <a name="download-the-blobs"></a>Descarga de los blobs

Descargue blobs en el disco local con el método **get\_blob\_to\_path**. El código siguiente descarga el blob cargado en una sección anterior. "_DOWNLOADED" se agrega como un sufijo al nombre de blob para que pueda ver ambos archivos en el disco local. 

```python
# Download the blob(s).
# Add '_DOWNLOADED' as prefix to '.txt' so you can see both files in Documents.
full_path_to_file2 = os.path.join(local_path, string.replace(local_file_name ,'.txt', '_DOWNLOADED.txt'))
print("\nDownloading blob to " + full_path_to_file2)
block_blob_service.get_blob_to_path(container_name, local_file_name, full_path_to_file2)
```

## <a name="clean-up-resources"></a>Limpieza de recursos
Si ya no necesita los blobs cargados en este tutorial de inicio rápido, puede eliminar todo el contenedor mediante **delete\_container**. Si ya no necesita los archivos creados, use el método **delete\_blob** para eliminarlos.

```python
# Clean up resources. This includes the container and the temp files
block_blob_service.delete_container(container_name)
os.remove(full_path_to_file)
os.remove(full_path_to_file2)
```

## <a name="next-steps"></a>Pasos siguientes
 
En este tutorial de inicio rápido aprendió a transferir archivos entre un disco local y Azure Blob Storage mediante Python. Para más información sobre cómo trabajar con Blob Storage, continúe con los procedimientos de Blob Storage.

> [!div class="nextstepaction"]
> [Procedimientos de las operaciones de Blob Storage](./storage-python-how-to-use-blob-storage.md)
 

Para más información sobre el Explorador de Storage y los blobs, consulte [Administración de recursos de Azure Blob Storage con el Explorador de Storage (versión preliminar)](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
