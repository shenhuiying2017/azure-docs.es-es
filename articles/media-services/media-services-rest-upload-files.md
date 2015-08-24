<properties 
	pageTitle="Carga de archivos en una cuenta de Servicios multimedia mediante API de REST" 
	description="Aprenda a obtener contenido multimedia en Servicios multimedia mediante la creación y carga de recursos." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/11/2015"
	ms.author="juliako"/>


#Carga de archivos en una cuenta de Servicios multimedia mediante API de REST

[AZURE.INCLUDE [media-services-selector-upload-files](../../includes/media-services-selector-upload-files.md)]
 

En Servicios multimedia, cargue los archivos digitales en un recurso. La entidad [Recurso](https://msdn.microsoft.com/library/azure/hh974277.aspx) puede contener archivos de vídeo, audio, imágenes, colecciones de miniaturas, pistas de texto y subtítulos (y los metadatos acerca de estos archivos). Una vez cargados los archivos en el recurso, el contenido se almacena de forma segura en la nube para un posterior procesamiento y streaming.


>[AZURE.NOTE]Los Servicios multimedia usan el valor de la propiedad IAssetFile.Name al generar direcciones URL para el contenido de streaming (por ejemplo, http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.). Por este motivo, no se permite la codificación porcentual. El valor de la propiedad **Name** no puede tener ninguno de los siguientes [caracteres reservados para la codificación porcentual](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters): !*'();:@&=+$,/?%#". Además, solo puede haber un '.' para la extensión del nombre de archivo.

El flujo de trabajo básico para la ingesta de recursos se divide en las secciones siguientes:

- Creación de un recurso
- Cifrado de un recurso (opcional)
- Carga de un archivo al almacenamiento de blobs


##Creación de un recurso

>[AZURE.NOTE]Al trabajar con la API de REST de Servicios multimedia, se aplican las consideraciones siguientes:
>
>Al obtener acceso a las entidades de Servicios multimedia, debe establecer los campos de encabezado específicos y los valores en las solicitudes HTTP. Para obtener más información, consulte [Configuración del desarrollo de la API de REST de Servicios multimedia](media-services-rest-how-to-use.md).

>Después de conectarse correctamente a https://media.windows.net, recibirá una redirección 301 especificando otro URI de Servicios multimedia. Debe realizar las llamadas subsiguientes al nuevo URI como se describe en [Conexión a Servicios multimedia con la API de REST](media-services-rest-connect_programmatically.md).
 
Un recurso es un contenedor para varios tipos o conjuntos de objetos en Servicios multimedia, como vídeo, audio, imágenes, colecciones de miniaturas, pistas de texto y archivos de subtítulos. En la API de REST, crear un recurso requiere el envío de una solicitud POST a Servicios multimedia y colocar la información sobre cualquier propiedad del recurso en el cuerpo de solicitud.

Una de las propiedades que se pueden especificar al crear un recurso es **Options**. **Options** es un valor de enumeración que describe las opciones de cifrado con las que se puede crear un recurso. Un valor válido es uno de los valores de la lista siguiente, no una combinación de valores.

- **None** = **0**: No se usará cifrado. Este es el valor predeterminado. Tenga en cuenta que al utilizar esta opción el contenido no está protegido en tránsito o en reposo en el almacenamiento. Si tiene previsto entregar un MP4 mediante una descarga progresiva, utilice esta opción. 

- **StorageEncrypted** = **1**: Especifique si desea que los archivos se cifren con cifrado AES de 256 bits para la carga y el almacenamiento.

	Si el recurso tiene el almacenamiento cifrado, asegúrese de configurar la directiva de entrega de recursos. Para más información, consulte [Configuración de la directiva de entrega de recursos](media-services-rest-configure-asset-delivery-policy.md).

- **CommonEncryptionProtected** = **2**: Especifique si va a cargar archivos protegidos con un método de cifrado común (por ejemplo, PlayReady).

- **EnvelopeEncryptionProtected** = **4**: Especifique si va a cargar HLS cifrado con archivos AES. Tenga en cuenta que los archivos deben haberse codificado y cifrado con Transform Manager.

>[AZURE.NOTE]Si el recurso usa cifrado, debe crear una **ContentKey** y vincularla al recurso, tal como se describe en el siguiente tema:[Creación de ContentKey](media-services-rest-create-contentkey.md). Tenga en cuenta que después de cargar los archivos en el recurso, debe actualizar las propiedades de cifrado en la entidad **AssetFile** con los valores que obtuvo durante el cifrado del **recurso**. Para ello, use la solicitud HTTP **MERGE**.


En el ejemplo siguiente se muestra cómo crear un recurso.

**Solicitud HTTP**

	POST https://media.windows.net/api/Assets HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-2233-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
	x-ms-version: 2.11
	Host: media.windows.net
	
	{"Name":"BigBuckBunny.mp4"}
	

**Respuesta HTTP**

Si se realiza correctamente, se devuelve lo siguiente:
	
	HTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 452
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')
	Server: Microsoft-IIS/8.5
	x-ms-client-request-id: c59de965-bc89-4295-9a57-75d897e5221e
	request-id: e98be122-ae09-473a-8072-0ccd234a0657
	x-ms-request-id: e98be122-ae09-473a-8072-0ccd234a0657
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Sun, 18 Jan 2015 22:06:40 GMT
	{  
	   "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets/@Element",
	   "Id":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
	   "State":0,
	   "Created":"2015-01-18T22:06:40.6010903Z",
	   "LastModified":"2015-01-18T22:06:40.6010903Z",
	   "AlternateId":null,
	   "Name":"BigBuckBunny.mp4",
	   "Options":0,
	   "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
	   "StorageAccountName":"storagetestaccount001"
	}
	
##Creación de AssetFile

La entidad [AssetFile](http://msdn.microsoft.com/library/azure/hh974275.aspx) representa un archivo de audio o vídeo que se almacena en un contenedor de blobs. Un archivo de recursos siempre está asociado a un recurso y un recurso puede contener uno o varios archivos de recursos. La tarea de Servicios multimedia produce un error si un objeto de archivo de recursos no está asociado a un archivo digital de un contenedor de blobs.

Tenga en cuenta que la instancia de **AssetFile** y el archivo multimedia real son dos objetos distintos. La instancia de AssetFile contiene metadatos sobre el archivo multimedia, mientras que el archivo multimedia contiene el contenido multimedia real.

Después de cargar el archivo multimedia digital en un contenedor de blobs, usará la solicitud HTTP **MERGE** para actualizar la entidad AssetFile con información sobre el archivo multimedia (tal como se muestra más adelante en este tema).

**Solicitud HTTP**

	POST https://media.windows.net/api/Files HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-4ca2-2233-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
	x-ms-version: 2.11
	Host: media.windows.net
	Content-Length: 164
	
	{  
	   "IsEncrypted":"false",
	   "IsPrimary":"false",
	   "MimeType":"video/mp4",
	   "Name":"BigBuckBunny.mp4",
	   "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
	}


**Respuesta HTTP**

	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 535
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5')
	Server: Microsoft-IIS/8.5
	request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
	x-ms-request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Mon, 19 Jan 2015 00:34:07 GMT
	
	{  
	   "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Files/@Element",
	   "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
	   "Name":"BigBuckBunny.mp4",
	   "ContentFileSize":"0",
	   "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
	   "EncryptionVersion":null,
	   "EncryptionScheme":null,
	   "IsEncrypted":false,
	   "EncryptionKeyId":null,
	   "InitializationVector":null,
	   "IsPrimary":false,
	   "LastModified":"2015-01-19T00:34:08.1934137Z",
	   "Created":"2015-01-19T00:34:08.1934137Z",
	   "MimeType":"video/mp4",
	   "ContentChecksum":null
	}


## Creación de AccessPolicy con permiso de escritura. 

Antes de cargar los archivos en el almacenamiento de blobs, establezca los derechos de la directiva de acceso para escribir en un recurso. Para ello, envíe una solicitud HTTP al conjunto de entidades AccessPolicies. Defina un valor DurationInMinutes durante la creación o recibirá un mensaje de error de servidor interno 500 como respuesta. Para obtener más información sobre AccessPolicies, consulte [AccessPolicy](http://msdn.microsoft.com/library/azure/hh974297.aspx).

En el ejemplo siguiente se muestra cómo crear una entidad AccessPolicy:
		
**Solicitud HTTP**

	POST https://media.windows.net/api/AccessPolicies HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-2233-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
	x-ms-version: 2.11
	Host: media.windows.net
	
	{"Name":"NewUploadPolicy", "DurationInMinutes":"440", "Permissions":"2"} 

**Solicitud HTTP**

	If successful, the following response is returned:
	
	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 312
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Location: https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies('nb%3Apid%3AUUID%3Abe0ac48d-af7d-4877-9d60-1805d68bffae')
	Server: Microsoft-IIS/8.5
	request-id: 74c74545-7e0a-4cd6-a440-c1c48074a970
	x-ms-request-id: 74c74545-7e0a-4cd6-a440-c1c48074a970
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Sun, 18 Jan 2015 22:18:06 GMT

	{  
	   "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#AccessPolicies/@Element",
	   "Id":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
	   "Created":"2015-01-18T22:18:06.6370575Z",
	   "LastModified":"2015-01-18T22:18:06.6370575Z",
	   "Name":"NewUploadPolicy",
	   "DurationInMinutes":440.0,
	   "Permissions":2
	}

##Obtención de la dirección URL de carga

Para recibir la dirección URL de carga real, cree un localizador de SAS. Los localizadores definen la hora de inicio y el tipo de extremo de conexión para los clientes que desean tener acceso a archivos de un recurso. Puede crear varias entidades Locator para un par AccessPolicy y Asset determinado a fin de controlar las distintas solicitudes y necesidades del cliente. Cada uno de estos localizadores usa el valor StartTime más el valor DurationInMinutes de AccessPolicy para determinar la cantidad de tiempo que se puede usar una dirección URL. Para obtener más información, consulte [Localizador](http://msdn.microsoft.com/library/azure/hh974308.aspx).


Una dirección URL de SAS tiene el formato siguiente:

	{https://myaccount.blob.core.windows.net}/{asset name}/{video file name}?{SAS signature}

Se aplican algunas consideraciones:

- No puede tener más de cinco localizadores únicos asociados a un recurso determinado a la vez. Para obtener más información, consulte Localizador.
- Si necesita cargar los archivos inmediatamente, debe establecer el valor StartTime cinco minutos antes de la hora actual. Esto se debe a que puede haber un desplazamiento de reloj entre el equipo cliente y Servicios multimedia. Además, el valor de StartTime debe tener el siguiente formato: YYYY-MM-DDTHH:mm:ssZ (por ejemplo, "2014-05-23T17:53:50Z").	
- Puede haber un retraso de 30 a 40 segundos desde el momento en que se crea un localizador hasta que este está disponible para su uso. Este problema afecta a la dirección URL de SAS y a localizadores de origen.

En el ejemplo siguiente se muestra cómo crear un localizador de dirección URL de SAS, tal como se define en la propiedad Type del cuerpo de solicitud ("1" para un localizador de SAS y "2" para un localizador de origen bajo demanda). La propiedad **Path** devuelta contiene la dirección URL que debe usar para cargar el archivo.
	
**Solicitud HTTP**
	
	POST https://media.windows.net/api/Locators HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-4ca2-2233-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
	x-ms-version: 2.11
	Host: media.windows.net
	{  
	   "AccessPolicyId":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
	   "AssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
	   "StartTime":"2015-02-18T16:45:53",
	   "Type":1
	}


**Respuesta HTTP**

Si se realiza correctamente, se devuelve la respuesta siguiente:
		
	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 949
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Locators('nb%3Alid%3AUUID%3Aaf57bdd8-6751-4e84-b403-f3c140444b54')
	Server: Microsoft-IIS/8.5
	request-id: 2adeb1f8-89c5-4cc8-aa4f-08cdfef33ae0
	x-ms-request-id: 2adeb1f8-89c5-4cc8-aa4f-08cdfef33ae0
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Mon, 19 Jan 2015 03:01:29 GMT
	
	{  
	   "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Locators/@Element",
	   "Id":"nb:lid:UUID:af57bdd8-6751-4e84-b403-f3c140444b54",
	   "ExpirationDateTime":"2015-02-19T00:05:53",
	   "Type":1,
	   "Path":"https://storagetestaccount001.blob.core.windows.net/asset-f438649c-313c-46e2-8d68-7d2550288247?sv=2012-02-12&sr=c&si=af57bdd8-6751-4e84-b403-f3c140444b54&sig=fE4btwEfZtVQFeC0Wh3Kwks2OFPQfzl5qTMW5YytiuY%3D&st=2015-02-18T16%3A45%3A53Z&se=2015-02-19T00%3A05%3A53Z",
	   "BaseUri":"https://storagetestaccount001.blob.core.windows.net/asset-f438649c-313c-46e2-8d68-7d2550288247",
	   "ContentAccessComponent":"?sv=2012-02-12&sr=c&si=af57bdd8-6751-4e84-b403-f3c140444b54&sig=fE4btwEfZtVQFeC0Wh3Kwks2OFPQfzl5qTMW5YytiuY%3D&st=2015-02-18T16%3A45%3A53Z&se=2015-02-19T00%3A05%3A53Z",
	   "AccessPolicyId":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
	   "AssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
	   "StartTime":"2015-02-18T16:45:53",
	   "Name":null
	}

## Carga de un archivo en un contenedor de almacenamiento de blobs
	
Una vez establecidas AccessPolicy y Locator, el archivo real se carga en un contenedor de almacenamiento de blobs de Azure mediante las API de REST de almacenamiento de Azure. Puede cargar en la página o blobs en bloques.

>[AZURE.NOTE]Debe agregar el nombre de archivo para el archivo que desea cargar en el valor **Path** del localizador recibido en la sección anterior. Por ejemplo, https://storagetestaccount001.blob.core.windows.net/asset-e7b02da4-5a69-40e7-a8db-e8f4f697aac0/BigBuckBunny.mp4?.

Para obtener más información sobre cómo trabajar con blobs de Almacenamiento de Azure, consulte [API de REST del servicio Blob](http://msdn.microsoft.com/library/azure/dd135733.aspx).


## Actualización de AssetFile 

Ahora que ha cargado el archivo, actualice la información de tamaño de FileAsset (y otro tipo de información). Por ejemplo:
	
	MERGE https://media.windows.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5') HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-4ca2-2233-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421662918&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=utmoXXbm9Q7j4tW1yJuMVA3egRiQy5FPygwadkmPeaY%3d
	x-ms-version: 2.11
	Host: media.windows.net
	
	{  
	   "ContentFileSize":"1186540",
	   "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
	   "MimeType":"video/mp4",
	   "Name":"BigBuckBunny.mp4",
	   "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
	}


**Respuesta HTTP**

Si se realiza correctamente, se devuelve lo siguiente: HTTP/1.1 204 No Content

## Eliminación de Locator y AccessPolicy 

**Solicitud HTTP**


	DELETE https://media.windows.net/api/Locators('nb%3Alid%3AUUID%3Aaf57bdd8-6751-4e84-b403-f3c140444b54') HTTP/1.1
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-2233-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421662918&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=utmoXXbm9Q7j4tW1yJuMVA3egRiQy5FPygwadkmPeaY%3d
	x-ms-version: 2.11
	Host: media.windows.net

	
**Respuesta HTTP**

Si se realiza correctamente, se devuelve lo siguiente:

	HTTP/1.1 204 No Content 
	...

**Solicitud HTTP**

	DELETE https://media.windows.net/api/AccessPolicies('nb%3Apid%3AUUID%3Abe0ac48d-af7d-4877-9d60-1805d68bffae') HTTP/1.1
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-2233-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421662918&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=utmoXXbm9Q7j4tW1yJuMVA3egRiQy5FPygwadkmPeaY%3d
	x-ms-version: 2.11
	Host: media.windows.net

**Respuesta HTTP**

Si se realiza correctamente, se devuelve lo siguiente:

	HTTP/1.1 204 No Content 
	...

 
[How to Get a Media Processor]: media-services-get-media-processor.md
 

<!---HONumber=August15_HO7-->