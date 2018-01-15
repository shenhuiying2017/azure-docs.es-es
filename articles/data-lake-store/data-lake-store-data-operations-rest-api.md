---
title: 'API de REST: operaciones de sistema de archivos en Azure Data Lake Store | Microsoft Docs'
description: Uso de la API de REST WebHDFS para realizar operaciones de sistema de archivos en Data Lake Store
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/09/2018
ms.author: nitinme
ms.openlocfilehash: a850b3fdff956abe41ac9a4af10a96dc119a75f4
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/10/2018
---
# <a name="filesystem-operations-on-azure-data-lake-store-using-rest-api"></a>Operaciones de sistema de archivos en Azure Data Lake Store con la API de REST
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-data-operations-net-sdk.md)
> * [SDK de Java](data-lake-store-get-started-java-sdk.md)
> * [API de REST](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
> 

En este artículo aprenderá a usar la API de REST WebHDFS y la API de REST de Data Lake Store para realizar operaciones de sistema de archivos en Azure Data Lake Store. Para instrucciones sobre cómo realizar operaciones de administración de cuentas en Data Lake Store con la API de REST, consulte [Operaciones de administración de cuentas en Azure Data Lake Store con la API de REST](data-lake-store-get-started-rest-api.md).

## <a name="prerequisites"></a>Requisitos previos
* **Una suscripción de Azure**. Consulte [Obtención de una versión de evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/).

* **Cuenta del Almacén de Azure Data Lake**. Siga las instrucciones de [Introducción al uso de Azure Portal por parte de Azure Data Lake Store](data-lake-store-get-started-portal.md).

* **[cURL](http://curl.haxx.se/)**. En este artículo se usa cURL para demostrar cómo realizar llamadas de la API de REST en una cuenta de Almacén de Data Lake.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>¿Cómo se puede autenticar mediante Azure Active Directory?
Puede usar dos enfoques para autenticar con Azure Active Directory.

* Para la autenticación del usuario final para la aplicación (interactiva), consulte el artículo sobre la [autenticación del usuario final con Data Lake Store mediante el SDK de .NET](data-lake-store-end-user-authenticate-rest-api.md).
* Para la autenticación entre servicios para la aplicación (no interactiva), consulte el artículo sobre la [autenticación entre servicios con Data Lake Store mediante el SDK de .NET](data-lake-store-service-to-service-authenticate-rest-api.md).


## <a name="create-folders"></a>Crear carpetas
Esta operación se basa en la llamada de la API de REST de WebHDFS que se define [aquí](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Make_a_Directory).

Use el siguiente comando cURL. Reemplace **\<yourstorename>** por el nombre de Data Lake Store.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/?op=MKDIRS'

En el comando anterior, reemplace \<`REDACTED`\> por el token de autorización que recuperó anteriormente. Este comando crea un directorio denominado **mytempdir** bajo la carpeta raíz de su cuenta de Data Lake Store.

Si la operación se completa correctamente, verá una respuesta similar al fragmento de código siguiente:

    {"boolean":true}

## <a name="list-folders"></a>Lista de carpetas
Esta operación se basa en la llamada de la API de REST de WebHDFS que se define [aquí](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#List_a_Directory).

Use el siguiente comando cURL. Reemplace **\<yourstorename>** por el nombre de Data Lake Store.

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS'

En el comando anterior, reemplace \<`REDACTED`\> por el token de autorización que recuperó anteriormente.

Si la operación se completa correctamente, verá una respuesta similar al fragmento de código siguiente:

    {
    "FileStatuses": {
        "FileStatus": [{
            "length": 0,
            "pathSuffix": "mytempdir",
            "type": "DIRECTORY",
            "blockSize": 268435456,
            "accessTime": 1458324719512,
            "modificationTime": 1458324719512,
            "replication": 0,
            "permission": "777",
            "owner": "<GUID>",
            "group": "<GUID>"
        }]
    }
    }

## <a name="upload-data"></a>Carga de datos
Esta operación se basa en la llamada de la API de REST de WebHDFS que se define [aquí](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Create_and_Write_to_a_File).

Use el siguiente comando cURL. Reemplace **\<yourstorename>** por el nombre de Data Lake Store.

    curl -i -X PUT -L -T 'C:\temp\list.txt' -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/list.txt?op=CREATE'

En la sintaxis anterior, el parámetro **-T** es la ubicación del archivo que se va a cargar.

La salida será similar al fragmento de código siguiente:
   
    HTTP/1.1 307 Temporary Redirect
    ...
    Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/list.txt?op=CREATE&write=true
    ...
    Content-Length: 0

    HTTP/1.1 100 Continue

    HTTP/1.1 201 Created
    ...

## <a name="read-data"></a>Lectura de datos
Esta operación se basa en la llamada de la API de REST de WebHDFS que se define [aquí](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Open_and_Read_a_File).

La lectura de datos desde una cuenta del Almacén de Data Lake es un proceso de dos pasos.

* Primero, envíe una solicitud GET en el punto de conexión `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN`. Esta llamada devolverá una ubicación a la que se debe enviar la siguiente solicitud GET.
* Después, envíe la solicitud GET en el punto de conexión `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN&read=true`. Esta llamada muestra el contenido del archivo.

Sin embargo, como no existe ninguna diferencia en los parámetros de entrada entre el primer y el segundo paso, puede usar el parámetro `-L` para enviar la primera solicitud. La opción `-L` combina dos solicitudes en una y hace que cURL vuelva a realizar la solicitud en la nueva ubicación. Por último, se muestra la salida de todas las llamadas de solicitud, como se muestra en el fragmento de código siguiente. Reemplace **\<yourstorename>** por el nombre de Data Lake Store.

    curl -i -L GET -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN'

Debería ver una salida similar al siguiente fragmento de código:

    HTTP/1.1 307 Temporary Redirect
    ...
    Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/somerandomfile.txt?op=OPEN&read=true
    ...

    HTTP/1.1 200 OK
    ...

    Hello, Data Lake Store user!

## <a name="rename-a-file"></a>Cambio del nombre de un archivo
Esta operación se basa en la llamada de la API de REST de WebHDFS que se define [aquí](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Rename_a_FileDirectory).

Para cambiar el nombre de un archivo, use el siguiente comando cURL. Reemplace **\<yourstorename>** por el nombre de Data Lake Store.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=RENAME&destination=/mytempdir/myinputfile1.txt'

Debería ver una salida similar al siguiente fragmento de código:

    HTTP/1.1 200 OK
    ...

    {"boolean":true}

## <a name="delete-a-file"></a>Eliminación de un archivo
Esta operación se basa en la llamada de la API de REST de WebHDFS que se define [aquí](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Delete_a_FileDirectory).

Para eliminar un archivo, use el siguiente comando cURL. Reemplace **\<yourstorename>** por el nombre de Data Lake Store.

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile1.txt?op=DELETE'

Debe ver algo parecido a lo siguiente:

    HTTP/1.1 200 OK
    ...

    {"boolean":true}

## <a name="next-steps"></a>pasos siguientes
* [Operaciones de administración de cuentas en Azure Data Lake Store con la API de REST](data-lake-store-get-started-rest-api.md).

## <a name="see-also"></a>Otras referencias
* [Azure Data Lake Store REST API Reference](https://docs.microsoft.com/rest/api/datalakestore/) (Referencia sobre la API de REST de Azure Data Lake)
* [Abrir aplicaciones Big Data de origen que funcionan con el Almacén de Azure Data Lake](data-lake-store-compatible-oss-other-applications.md)

