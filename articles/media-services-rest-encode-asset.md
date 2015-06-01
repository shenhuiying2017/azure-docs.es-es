<properties 
	pageTitle="Codificación de un recurso mediante el Codificador multimedia de Azure" 
	description="Aprenda a usar el codificador multimedia de Azure para codificar contenido multimedia en Servicios multimedia. Los ejemplos de código usan la API de REST." 
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
	ms.date="02/10/2015" 
	ms.author="juliako"/>


#Codificación de un recurso mediante el Codificador multimedia de Azure

Este artículo forma parte de la serie [Vídeo de Servicios multimedia sobre el flujo de trabajo a petición](media-services-video-on-demand-workflow.md). 

##Información general
Para entregar vídeo digital a través de Internet, debe comprimir los archivos multimedia. Los archivos de vídeo digital son bastante grandes y pueden ser demasiado pesados para entregarlos a través de Internet o para que los dispositivos de sus clientes los muestren correctamente. La codificación es el proceso de compresión de vídeo y audio para que los clientes puedan ver el contenido multimedia.

Los trabajos de codificación son una de las operaciones de procesamiento más habituales en los Servicios multimedia. Los trabajos de codificación se crean para convertir archivos multimedia de una codificación a otra. Al codificar, puede usar el Codificador multimedia integrado de Servicios multimedia. También puede usar un codificador proporcionado por un socio de Servicios multimedia; los codificadores de terceros están disponibles a través de Azure Marketplace. Puede especificar los detalles de las tareas de codificación mediante cadenas preestablecidas definidas para el codificador o mediante archivos de configuración preestablecidos. Para ver los tipos de valores preestablecidos disponibles, vea Valores preestablecidos de tarea para los Servicios multimedia de Azure. Si usó un codificador de terceros, debe [validar los archivos](https://msdn.microsoft.com/library/azure/dn750842.aspx).

Se recomienda codificar siempre los archivos intermedios en un conjunto de archivos MP4 de velocidad de bits adaptable y, a continuación, convertirlo al formato deseado con el [empaquetado dinámico](https://msdn.microsoft.com/library/azure/jj889436.aspx).

Si el recurso de salida tiene el almacenamiento cifrado, asegúrese de configurar la directiva de entrega de recursos. Para más información, vea [Configuración de la directiva de entrega de recursos](media-services-rest-configure-asset-delivery-policy.md).

##Creación de un trabajo con una sola tarea de codificación 

>[AZURE.NOTE] Al trabajar con la API de REST de Servicios multimedia, se aplican las consideraciones siguientes:
>
>Al obtener acceso a las entidades de Servicios multimedia, debe establecer los campos de encabezado específicos y los valores en las solicitudes HTTP. Para obtener más información, consulte [Configuración de desarrollo de la API de REST de Servicios multimedia](media-services-rest-how-to-use.md).

>Después de conectarse correctamente a https://media.windows.net, recibirá una redirección 301 que especifica otro URI de Servicios multimedia. Debe realizar las llamadas posteriores al nuevo URI tal como se describe en [Conexión a Servicios multimedia con la API de REST de Servicios multimedia](media-services-rest-connect_programmatically.md). 


En el ejemplo siguiente se muestra cómo crear y publicar un trabajo con un conjunto de tareas para codificar un vídeo con una resolución y calidad específicas. Al codificar con el Codificador multimedia de Azure, puede usar los valores preestablecidos de configuración de tarea especificados [aquí](https://msdn.microsoft.com/library/azure/dn619389.aspx).
	
Solicitud:

	POST https://media.windows.net/API/Jobs HTTP/1.1
	Content-Type: application/json;odata=verbose
	Accept: application/json;odata=verbose
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=youraccountname&urn%3aSubscriptionId=zf84471d-b1ae-2233-aa09-010f0fc0cf5b&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1336802231&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=gR%2fNaIZgolFLxBOmfSECrp16Mp0Mti3KoePVjBUCzls%3d
	Host: media.windows.net
	Content-Length: 476
	
	{"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aaab7f15b-3136-4ddf-9962-e9ecb28fb9d2')"}}],  "Tasks" : [{"Configuration" : "H264 Broadband 720p", "MediaProcessorId" : "nb:mpid:UUID:70bdc2c3-ebf4-42a9-8542-5afc1e55d217",  "TaskBody" : "<?xml version="1.0" encoding="utf-8"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"}]}

Response:
	
	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 1017
	Content-Type: application/json;odata=verbose;charset=utf-8
	Location: https://media.windows.net/api/Jobs('nb%3Ajid%3AUUID%3A40dc7bef-6bd9-2247-9f3d-d80bc257d715')
	Server: Microsoft-IIS/7.5
	x-ms-request-id: d2052a71-95b1-4a52-9420-ccca1202a5fb
	X-Content-Type-Options: nosniff
	DataServiceVersion: 1.0;
	X-AspNet-Version: 4.0.30319
	X-Powered-By: ASP.NET
	Date: Fri, 11 May 2012 21:32:40 GMT
	
	{"d":{"__metadata":{"id":"https://media.windows.net/api/Jobs('nb%3Ajid%3AUUID%3A40dc7bef-6bd9-2247-9f3d-d80bc257d715')","uri":"https://media.windows.net/api/Jobs('nb%3Ajid%3AUUID%3A40dc7bef-6bd9-2247-9f3d-d80bc257d715')","type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Job"},"Tasks":{"__deferred":{"uri":"https://media.windows.net/api/Jobs('nb%3Ajid%3AUUID%3A40dc7bef-6bd9-2247-9f3d-d80bc257d715')/Tasks"}},"OutputMediaAssets":{"__deferred":{"uri":"https://media.windows.net/api/Jobs('nb%3Ajid%3AUUID%3A40dc7bef-6bd9-2247-9f3d-d80bc257d715')/OutputMediaAssets"}},"InputMediaAssets":{"__deferred":{"uri":"https://media.windows.net/api/Jobs('nb%3Ajid%3AUUID%3A40dc7bef-6bd9-2247-9f3d-d80bc257d715')/InputMediaAssets"}},"Id":"nb:jid:UUID:40dc7bef-6bd9-2247-9f3d-d80bc257d715","Name":"NewTestJob","Created":"/Date(1336771959431)/","LastModified":"/Date(1336771959431)/","EndTime":null,"Priority":0,"RunningDuration":0,"StartTime":null,"State":0,"TemplateId":null}}

##Creación de un trabajo con tareas encadenadas

En muchos escenarios de aplicaciones, los desarrolladores desean crear una serie de tareas de procesamiento. En Servicios multimedia, puede crear una serie de tareas encadenadas. Cada tarea realiza distintos pasos de procesamiento diferentes y puede usar diferentes procesadores multimedia. Las tareas encadenadas pueden entregar un recurso de una tarea a otra, realizando una secuencia lineal de tareas en el recurso. Sin embargo, no es necesario que las tareas realizadas en un trabajo estén en una secuencia. Al crear una tarea encadenada, los objetos **ITask** encadenados se crean en un solo objeto **IJob**.

>[AZURE.NOTE] Actualmente hay un límite de 30 tareas por trabajo. Si necesita encadenar más de 30 tareas, cree más de un trabajo para incluir las tareas.


	POST https://media.windows.net/api/Jobs HTTP/1.1
	Content-Type: application/json;odata=verbose
	Accept: application/json;odata=verbose
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=youraccountname&urn%3aSubscriptionId=zf84471d-b1ae-4e75-2233-010f0fc0cf5b&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1336802231&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=gR%2fNaIZgolFLxBOmfSECrp16Mp0Mti3KoePVjBUCzls%3d

	{  
	   "Name":"NewTestJob",
	   "InputMediaAssets":[  
	      {  
	         "__metadata":{  
	            "uri":"https://testrest.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A910ffdc1-2e25-4b17-8a42-61ffd4b8914c')"
	         }
	      }
	   ],
	   "Tasks":[  
	      {  
	         "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
	         "MediaProcessorId":"nb:mpid:UUID:2e7aa8f3-4961-4e0c-b4db-0e0439e524f5",
	         "TaskBody":"<?xml version="1.0" encoding="utf-8"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"
	      },
	      {  
	         "Configuration":"H264 Smooth Streaming 720p",
	         "MediaProcessorId":"nb:mpid:UUID:2e7aa8f3-4961-4e0c-b4db-0e0439e524f5",
	         "TaskBody":"<?xml version="1.0" encoding="utf-16"?><taskBody><inputAsset>JobOutputAsset(0)</inputAsset><outputAsset>JobOutputAsset(1)</outputAsset></taskBody>"
	      }
	   ]
	}


##Pasos siguientes
Ahora que sabe cómo crear un trabajo para codificar un recurso, vaya al tema [Comprobación del progreso del trabajo con los Servicios multimedia](media-services-rest-check-job-progress.md).

[Azure Marketplace]: https://datamarket.azure.com/
[Valores preestablecidos del codificador]: http://msdn.microsoft.com/library/dn619392.aspx
[Obtención de una instancia de procesador multimedia]:http://go.microsoft.com/fwlink/?LinkId=301732
[Instrucciones acerca de cómo: de un recurso cifrado]:http://go.microsoft.com/fwlink/?LinkId=301733
[Instrucciones acerca de cómo: de un recurso mediante descarga]:http://go.microsoft.com/fwlink/?LinkId=301734
[Comprobación del progreso del trabajo]:http://go.microsoft.com/fwlink/?LinkId=301737
[Valores predefinidos de tarea para Azure Media Packager]:http://msdn.microsoft.com/library/windowsazure/hh973635.aspx


<!--HONumber=52-->