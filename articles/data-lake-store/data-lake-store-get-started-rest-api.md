<properties 
   pageTitle="Introducción al Almacén de Data Lake mediante la API de REST| Microsoft Azure" 
   description="Usar las API de REST de WebHDFS para realizar operaciones en el Almacén de Data Lake" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="04/08/2016"
   ms.author="nitinme"/>

# Introducción al Almacén de Azure Data Lake mediante las API de REST

> [AZURE.SELECTOR]
- [Portal](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [.NET SDK](data-lake-store-get-started-net-sdk.md)
- [SDK de Java](data-lake-store-get-started-java-sdk.md)
- [API DE REST](data-lake-store-get-started-rest-api.md)
- [CLI de Azure](data-lake-store-get-started-cli.md)
- [Node.js](data-lake-store-manage-use-nodejs.md)

En este artículo, obtendrá información sobre cómo usar las API de REST de WebHDFS y las API de REST del Almacén de Data Lake para realizar la administración de cuentas así como las operaciones del sistema de archivos del Almacén de Azure Data Lake. El Almacén de Azure Data Lake expone sus propias API de REST para operaciones de administración de cuentas. Sin embargo, como el Almacén de Data Lake es compatible con el ecosistema de HDFS y Hadoop, admite el uso de las API de REST de WebHDFS para las operaciones del sistema de archivos.

>[AZURE.NOTE] Para obtener información detallada sobre la compatibilidad de la API de REST con el Almacén de Data Lake, consulte [Referencia de la API de REST en el Almacén de Azure Data Lake](https://msdn.microsoft.com/library/mt693424.aspx).

## Requisitos previos

- **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).
- **Habilite su suscripción de Azure** para la versión de vista previa pública del Almacén de Data Lake. Consulte las [instrucciones](data-lake-store-get-started-portal.md#signup).
- **Creación de una aplicación de Azure Active Directory**. Consulte [Creación de una aplicación de Active Directory y una entidad de servicio con el portal](../resource-group-create-service-principal-portal.md). Cuando haya creado la aplicación, recupere los siguientes valores relacionados con esta.
	- Obtenga el identificador de cliente e inquilino para la aplicación.
	- Creación de una clave de autenticación
	- Establecimiento de permisos delegados

	En el vínculo que se proporcionó en la sección anterior, puede encontrar instrucciones sobre cómo recuperar estos valores.
- **Asigne la aplicación de Azure Active Directory a un rol**. El rol puede encontrarse al nivel del ámbito en el que quiere conceder el permiso a la aplicación de Azure Active Directory. Por ejemplo, puede asignar la aplicación en el nivel de suscripción o en el nivel de un grupo de recursos. Para obtener instrucciones, consulte [Asignar una aplicación a un rol](../resource-group-create-service-principal-portal.md#assign-application-to-role).
- [cURL](http://curl.haxx.se/). En este artículo se usa cURL para demostrar cómo realizar llamadas de la API de REST en una cuenta de Almacén de Data Lake.

## ¿Cómo se puede autenticar mediante Azure Active Directory?

Puede usar dos enfoques para autenticar con Azure Active Directory.

* **Interactivo**, donde la aplicación solicita al usuario que inicie sesión. Para obtener más información, consulte [Flujo de concesión de códigos de autorización](https://msdn.microsoft.com/library/azure/dn645542.aspx).

* **No interactivo**, en el que la aplicación proporciona sus propias credenciales. Para obtener más información, consulte [Llamadas servicio a servicio mediante credenciales](https://msdn.microsoft.com/library/azure/dn645543.aspx).

En este artículo se usa el enfoque **no interactivo**. Para ello, debe emitir una solicitud POST como la que se muestra a continuación.

	curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
      -F grant_type=client_credentials \
      -F resource=https://management.core.windows.net/ \
      -F client_id=<CLIENT-ID> \
      -F client_secret=<AUTH-KEY>

El resultado de esta solicitud incluirá un token de autorización (indicado por `access-token` en el siguiente resultado) que pasará posteriormente con las llamadas de la API de REST. Guarde este token de autenticación en un archivo de texto; lo necesitará más adelante en este artículo.

	{"token_type":"Bearer","expires_in":"3599","expires_on":"1458245447","not_before":"1458241547","resource":"https://management.core.windows.net/","access_token":"<REDACTED>"}

## Crear una cuenta de Almacén de Data Lake

Esta operación se basa en la llamada de la API de REST que se define [aquí](https://msdn.microsoft.com/library/mt694078.aspx).

Use el siguiente comando cURL. Reemplace **<yourstorename>** por el nombre del Almacén de Data Lake.

	curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -H "Content-Type: application/json" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstorename>?api-version=2015-10-01-preview -d@C:\temp\input.json

En el comando anterior, reemplace <`REDACTED`> por el token de autorización que recuperó anteriormente. La carga útil de la solicitud de este comando se encuentra en el archivo **input.json** que se proporciona para el parámetro `-d` anterior. El contenido del archivo input.json es similar al siguiente:

	{
	"location": "eastus2",
	"tags": {
		"department": "finance"
		},
	"properties": {}
	}	

## Crear carpetas en una cuenta de Almacén de Data Lake

Esta operación se basa en la llamada de la API de REST de WebHDFS que se define [aquí](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Make_a_Directory).

Use el siguiente comando cURL. Reemplace **<yourstorename>** por el nombre del Almacén de Data Lake.

	curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/?op=MKDIRS

En el comando anterior, reemplace <`REDACTED`> por el token de autorización que recuperó anteriormente. Este comando crea un directorio denominado **mytempdir** bajo la carpeta raíz de su cuenta del Almacén de Data Lake.

Si la operación se completa correctamente, verá una respuesta similar a la siguiente:

	{"boolean":true}

## Mostrar carpetas en una cuenta de Almacén de Data Lake

Esta operación se basa en la llamada de la API de REST de WebHDFS que se define [aquí](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#List_a_Directory).

Use el siguiente comando cURL. Reemplace **<yourstorename>** por el nombre del Almacén de Data Lake.

	curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS

En el comando anterior, reemplace <`REDACTED`> por el token de autorización que recuperó anteriormente.

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

## Cargar datos en una cuenta del Almacén de Data Lake

Esta operación se basa en la llamada de la API de REST de WebHDFS que se define [aquí](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Create_and_Write_to_a_File).

Cargar datos con la API de REST de WebHDFS es un proceso de dos pasos, como se explica a continuación.

1. Envíe una solicitud PUT de HTTP sin enviar los datos del archivo que se va a cargar. En el siguiente comando, reemplace **<yourstorename>** por el nombre del Almacén de Data Lake.

		curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/?op=CREATE

	El resultado de este comando contendrá una dirección URL de redireccionamiento temporal, como la que se muestra a continuación.

		HTTP/1.1 100 Continue

		HTTP/1.1 307 Temporary Redirect
		...
		...
		Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/somerandomfile.txt?op=CREATE&write=true
		...
		...

2. Ahora debe enviar otra solicitud PUT de HTTP en la dirección URL que se muestra para la propiedad **Location** en la respuesta. Reemplace **<yourstorename>** por el nombre del Almacén de Data Lake.

		curl -i -X PUT -T myinputfile.txt -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=CREATE&write=true

	El resultado será similar al siguiente:

		HTTP/1.1 100 Continue

		HTTP/1.1 201 Created
		...
		...

## Leer datos de una cuenta del Almacén de Data Lake

Esta operación se basa en la llamada de la API de REST de WebHDFS que se define [aquí](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Open_and_Read_a_File).

La lectura de datos desde una cuenta del Almacén de Data Lake es un proceso de dos pasos.

* Primero, envíe una solicitud GET en el punto de conexión `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN`. Esto devolverá una ubicación a la que se debe enviar la siguiente solicitud GET.
* Después, envíe la solicitud GET en el punto de conexión `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN&read=true`. Se mostrará el contenido del archivo.

Sin embargo, como no existe ninguna diferencia en los parámetros de entrada entre el primer y el segundo paso, puede usar el parámetro `-L` para enviar la primera solicitud. Básicamente, la opción `-L` combina dos solicitudes en una y provocará que cURL vuelva a realizar la solicitud en la nueva ubicación. Por último, se muestra el resultado de todas las llamadas de solicitud, como se muestra a continuación. Reemplace **<yourstorename>** por el nombre del Almacén de Data Lake.

	curl -i -L GET -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN

Debería ver una salida similar a la siguiente:

	HTTP/1.1 307 Temporary Redirect
	...
	Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/somerandomfile.txt?op=OPEN&read=true
	...
	
	HTTP/1.1 200 OK
	...
	
	Hello, Data Lake Store user!

## Cambiar el nombre de un archivo en una cuenta del Almacén de Data Lake

Esta operación se basa en la llamada de la API de REST de WebHDFS que se define [aquí](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Rename_a_FileDirectory).

Para cambiar el nombre de un archivo, use el siguiente comando cURL. Reemplace **<yourstorename>** por el nombre del Almacén de Data Lake.

	curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=RENAME&destination=/mytempdir/myinputfile1.txt

Debería ver una salida similar a la siguiente:

	HTTP/1.1 200 OK
	...
	
	{"boolean":true}

## Eliminar un archivo de una cuenta del Almacén de Data Lake

Esta operación se basa en la llamada de la API de REST de WebHDFS que se define [aquí](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Delete_a_FileDirectory).

Para eliminar un archivo, use el siguiente comando cURL. Reemplace **<yourstorename>** por el nombre del Almacén de Data Lake.

	curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile1.txt?op=DELETE

Debe ver algo parecido a lo siguiente:

	HTTP/1.1 200 OK
	...
	
	{"boolean":true}

## Eliminar una cuenta del Almacén de Data Lake

Esta operación se basa en la llamada de la API de REST que se define [aquí](https://msdn.microsoft.com/library/mt694075.aspx).

Use el siguiente comando cURL para eliminar la cuenta del Almacén de Data Lake. Reemplace **<yourstorename>** por el nombre del Almacén de Data Lake.

	curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstorename>?api-version=2015-10-01-preview

Debe ver algo parecido a lo siguiente:

	HTTP/1.1 200 OK
	...
	...

## Consulte también

- [Abrir aplicaciones Big Data de origen que funcionan con el Almacén de Azure Data Lake](data-lake-store-compatible-oss-other-applications.md)
 

<!---HONumber=AcomDC_0413_2016-->