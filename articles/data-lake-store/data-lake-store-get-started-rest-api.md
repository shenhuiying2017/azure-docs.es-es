---
title: "Uso del SDK de Python como introducción a Azure Data Lake Store | Microsoft Docs"
description: "Usar las API de REST de WebHDFS para realizar operaciones en el Almacén de Data Lake"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 57ac6501-cb71-4f75-82c2-acc07c562889
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/21/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: de04bf367f9f9f92756202cf6c1571f811a0f1f7
ms.lasthandoff: 04/22/2017


---
# <a name="get-started-with-azure-data-lake-store-using-rest-apis"></a>Introducción al Almacén de Azure Data Lake mediante las API de REST
> [!div class="op_single_selector"]
> * [Portal](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [SDK de Java](data-lake-store-get-started-java-sdk.md)
> * [API DE REST](data-lake-store-get-started-rest-api.md)
> * [CLI de Azure](data-lake-store-get-started-cli.md)
> * [CLI de Azure 2.0](data-lake-store-get-started-cli-2.0.md)
> * [Node.js](data-lake-store-manage-use-nodejs.md)
> * [Python](data-lake-store-get-started-python.md)
>
> 

En este artículo, obtendrá información sobre cómo usar las API de REST de WebHDFS y las API de REST del Almacén de Data Lake para realizar la administración de cuentas así como las operaciones del sistema de archivos del Almacén de Azure Data Lake. El Almacén de Azure Data Lake expone sus propias API de REST para operaciones de administración de cuentas. Sin embargo, como el Almacén de Data Lake es compatible con el ecosistema de HDFS y Hadoop, admite el uso de las API de REST de WebHDFS para las operaciones del sistema de archivos.

> [!NOTE]
> Para obtener información detallada sobre la compatibilidad de la API de REST con el Almacén de Data Lake, consulte [Referencia de la API de REST en el Almacén de Azure Data Lake](https://msdn.microsoft.com/library/mt693424.aspx).
> 
> 

## <a name="prerequisites"></a>Requisitos previos
* **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Cree una aplicación de Azure Active Directory**. Utilice la aplicación Azure AD para autenticar la aplicación Data Lake Store con Azure AD. Existen diferentes enfoques para realizar la autenticación con Azure AD, que son la **autenticación de usuario final** o la **autenticación de servicio a servicio**. Para instrucciones y más información acerca de cómo realizar la autenticación, consulte [Authenticate with Data Lake Store using Azure Active Directory](data-lake-store-authenticate-using-active-directory.md)(Autenticación con Data Lake Store mediante Azure Active Directory).
* [cURL](http://curl.haxx.se/). En este artículo se usa cURL para demostrar cómo realizar llamadas de la API de REST en una cuenta de Almacén de Data Lake.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>¿Cómo se puede autenticar mediante Azure Active Directory?
Puede usar dos enfoques para autenticar con Azure Active Directory.

### <a name="end-user-authentication-interactive"></a>Autenticación de usuario final (interactiva)
En este escenario, la aplicación pide al usuario que inicie sesión y todas las operaciones se realizan en el contexto del usuario. Realice los pasos siguientes para realizar la autenticación interactiva.

1. A través de la aplicación, redirija al usuario a la siguiente dirección URL:
   
        https://login.microsoftonline.com/<TENANT-ID>/oauth2/authorize?client_id=<APPLICATION-ID>&response_type=code&redirect_uri=<REDIRECT-URI>
   
   > [!NOTE]
   > \<REDIRECT-URI> debe codificarse para utilizarse en una dirección URL. Por lo tanto, para https://localhost, use `https%3A%2F%2Flocalhost`).
   > 
   > 
   
    Para este tutorial, puede sustituir los valores de marcador de posición de la dirección URL anterior y pegarlos en la barra de direcciones del explorador web. Se le redirigirá a una página autenticarse con sus datos de inicio de sesión de Azure. Una vez que haya iniciado sesión correctamente, la respuesta se muestra en la barra de direcciones del explorador. La respuesta estará en el formato siguiente:
   
        http://localhost/?code=<AUTHORIZATION-CODE>&session_state=<GUID>
2. Obtenga el código de autorización de la respuesta. Para este tutorial, puede copiar el código de autorización de la barra de direcciones del explorador web y pasarlo en la solicitud POST al punto de conexión de token, tal y como se muestra a continuación:
   
        curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token \
        -F redirect_uri=<REDIRECT-URI> \
        -F grant_type=authorization_code \
        -F resource=https://management.core.windows.net/ \
        -F client_id=<APPLICATION-ID> \
        -F code=<AUTHORIZATION-CODE>
   
   > [!NOTE]
   > En este caso, no se necesita codificar \<REDIRECT-URI>.
   > 
   > 
3. La respuesta es un objeto JSON que contiene un token de acceso (por ejemplo, `"access_token": "<ACCESS_TOKEN>"`) y uno de actualización (por ejemplo, `"refresh_token": "<REFRESH_TOKEN>"`). La aplicación usa el token de acceso al acceder al Almacén de Azure Data Lake y el token de actualización para obtener otro token de acceso cuando expira un token de acceso.
   
        {"token_type":"Bearer","scope":"user_impersonation","expires_in":"3599","expires_on":"1461865782","not_before":    "1461861882","resource":"https://management.core.windows.net/","access_token":"<REDACTED>","refresh_token":"<REDACTED>","id_token":"<REDACTED>"}
4. Cuando expira el token de acceso, puede solicitar uno nuevo con el token de actualización, tal y como se muestra a continuación:
   
        curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
             -F grant_type=refresh_token \
             -F resource=https://management.core.windows.net/ \
             -F client_id=<APPLICATION-ID> \
             -F refresh_token=<REFRESH-TOKEN>

Para más información sobre la autenticación interactiva de usuarios, consulte el [flujo de concesión de un código de autorización](https://msdn.microsoft.com/library/azure/dn645542.aspx).

### <a name="service-to-service-authentication-non-interactive"></a>Autenticación de servicio a servicio (no interactiva)
En este escenario, la aplicación proporciona sus propias credenciales para realizar las operaciones. Para ello, debe emitir una solicitud POST como la que se muestra a continuación. 

    curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
      -F grant_type=client_credentials \
      -F resource=https://management.core.windows.net/ \
      -F client_id=<CLIENT-ID> \
      -F client_secret=<AUTH-KEY>

El resultado de esta solicitud incluirá un token de autorización (que se indica mediante `access-token` en el siguiente resultado) que pasará posteriormente con las llamadas de la API de REST. Guarde este token de autenticación en un archivo de texto; lo necesitará más adelante en este artículo.

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1458245447","not_before":"1458241547","resource":"https://management.core.windows.net/","access_token":"<REDACTED>"}

En este artículo se usa el enfoque **no interactivo** . Para más información sobre el enfoque no interactivo (llamadas de servicio a servicio), consulte el tema sobre [llamadas de servicio a servicio utilizando las credenciales del cliente](https://msdn.microsoft.com/library/azure/dn645543.aspx).

## <a name="create-a-data-lake-store-account"></a>Crear una cuenta de Almacén de Data Lake
Esta operación se basa en la llamada de la API de REST que se define [aquí](https://msdn.microsoft.com/library/mt694078.aspx).

Use el siguiente comando cURL. Reemplace **\<yourstorename>** por el nombre de Data Lake Store.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -H "Content-Type: application/json" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstorename>?api-version=2015-10-01-preview -d@"C:\temp\input.json"

En el comando anterior, reemplace \<`REDACTED`\> por el token de autorización que recuperó anteriormente. La carga útil de la solicitud de este comando se encuentra en el archivo **input.json** que se proporciona para el parámetro `-d` anterior. El contenido del archivo input.json es similar al siguiente:

    {
    "location": "eastus2",
    "tags": {
        "department": "finance"
        },
    "properties": {}
    }    

## <a name="create-folders-in-a-data-lake-store-account"></a>Crear carpetas en una cuenta de Almacén de Data Lake
Esta operación se basa en la llamada de la API de REST de WebHDFS que se define [aquí](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Make_a_Directory).

Use el siguiente comando cURL. Reemplace **\<yourstorename>** por el nombre de Data Lake Store.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/?op=MKDIRS'

En el comando anterior, reemplace \<`REDACTED`\> por el token de autorización que recuperó anteriormente. Este comando crea un directorio denominado **mytempdir** bajo la carpeta raíz de su cuenta de Data Lake Store.

Si la operación se completa correctamente, verá una respuesta similar a la siguiente:

    {"boolean":true}

## <a name="list-folders-in-a-data-lake-store-account"></a>Mostrar carpetas en una cuenta de Almacén de Data Lake
Esta operación se basa en la llamada de la API de REST de WebHDFS que se define [aquí](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#List_a_Directory).

Use el siguiente comando cURL. Reemplace **\<yourstorename>** por el nombre de Data Lake Store.

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS'

En el comando anterior, reemplace \<`REDACTED`\> por el token de autorización que recuperó anteriormente.

Si la operación se completa correctamente, verá una respuesta similar a la siguiente:

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
            "owner": "NotSupportYet",
            "group": "NotSupportYet"
        }]
    }
    }

## <a name="upload-data-into-a-data-lake-store-account"></a>Cargar datos en una cuenta del Almacén de Data Lake
Esta operación se basa en la llamada de la API de REST de WebHDFS que se define [aquí](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Create_and_Write_to_a_File).

Use el siguiente comando cURL. Reemplace **\<yourstorename>** por el nombre de Data Lake Store.

    curl -i -X PUT -L -T 'C:\temp\list.txt' -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/list.txt?op=CREATE'

En la sintaxis anterior el parámetro **-T** es la ubicación del archivo que se va a cargar.

La salida será similar a la siguiente:
   
    HTTP/1.1 307 Temporary Redirect
    ...
    Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/list.txt?op=CREATE&write=true
    ...
    Content-Length: 0

    HTTP/1.1 100 Continue

    HTTP/1.1 201 Created
    ...

## <a name="read-data-from-a-data-lake-store-account"></a>Leer datos de una cuenta del Almacén de Data Lake
Esta operación se basa en la llamada de la API de REST de WebHDFS que se define [aquí](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Open_and_Read_a_File).

La lectura de datos desde una cuenta del Almacén de Data Lake es un proceso de dos pasos.

* Primero, envíe una solicitud GET en el punto de conexión `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN`. Esto devolverá una ubicación a la que se debe enviar la siguiente solicitud GET.
* Después, envíe la solicitud GET en el punto de conexión `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN&read=true`. Se mostrará el contenido del archivo.

Sin embargo, como no existe ninguna diferencia en los parámetros de entrada entre el primer y el segundo paso, puede usar el parámetro `-L` para enviar la primera solicitud. `-L` combina dos solicitudes en una y provocará que cURL vuelva a realizar la solicitud en la nueva ubicación. Por último, se muestra el resultado de todas las llamadas de solicitud, como se muestra a continuación. Reemplace **\<yourstorename>** por el nombre de Data Lake Store.

    curl -i -L GET -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN'

Debería ver una salida similar a la siguiente:

    HTTP/1.1 307 Temporary Redirect
    ...
    Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/somerandomfile.txt?op=OPEN&read=true
    ...

    HTTP/1.1 200 OK
    ...

    Hello, Data Lake Store user!

## <a name="rename-a-file-in-a-data-lake-store-account"></a>Cambiar el nombre de un archivo en una cuenta del Almacén de Data Lake
Esta operación se basa en la llamada de la API de REST de WebHDFS que se define [aquí](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Rename_a_FileDirectory).

Para cambiar el nombre de un archivo, use el siguiente comando cURL. Reemplace **\<yourstorename>** por el nombre de Data Lake Store.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=RENAME&destination=/mytempdir/myinputfile1.txt'

Debería ver una salida similar a la siguiente:

    HTTP/1.1 200 OK
    ...

    {"boolean":true}

## <a name="delete-a-file-from-a-data-lake-store-account"></a>Eliminar un archivo de una cuenta del Almacén de Data Lake
Esta operación se basa en la llamada de la API de REST de WebHDFS que se define [aquí](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Delete_a_FileDirectory).

Para eliminar un archivo, use el siguiente comando cURL. Reemplace **\<yourstorename>** por el nombre de Data Lake Store.

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile1.txt?op=DELETE'

Debe ver algo parecido a lo siguiente:

    HTTP/1.1 200 OK
    ...

    {"boolean":true}

## <a name="delete-a-data-lake-store-account"></a>Eliminar una cuenta del Almacén de Data Lake
Esta operación se basa en la llamada de la API de REST que se define [aquí](https://msdn.microsoft.com/library/mt694075.aspx).

Use el siguiente comando cURL para eliminar la cuenta del Almacén de Data Lake. Reemplace **\<yourstorename>** por el nombre de Data Lake Store.

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstorename>?api-version=2015-10-01-preview

Debe ver algo parecido a lo siguiente:

    HTTP/1.1 200 OK
    ...
    ...

## <a name="see-also"></a>Otras referencias
* [Abrir aplicaciones Big Data de origen que funcionan con el Almacén de Azure Data Lake](data-lake-store-compatible-oss-other-applications.md)


