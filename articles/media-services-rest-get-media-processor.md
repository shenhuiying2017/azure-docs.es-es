<properties 
	pageTitle="Creación de un procesador de multimedia en Azure" 
	description="Aprenda a crear un componente de procesador de multimedia para codificar, cifrar, descifrar o convertir el formato de contenido multimedia para Servicios multimedia de Azure." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
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


# Obtención de una instancia de procesador multimedia

Este artículo forma parte de la serie [Flujo de trabajo de vídeo bajo demanda de Servicios multimedia](media-services-video-on-demand-workflow.md) . 

## Información general

En los Servicios multimedia, un procesador multimedia es un componente que controla una tarea de procesamiento específica, como codificación, conversión de formato, cifrado o descifrado de contenido multimedia. Normalmente crea un procesador multimedia cuando crea una tarea para codificar, cifrar o convertir el formato de contenido multimedia.

La siguiente tabla proporciona el nombre y la descripción de cada procesador multimedia disponible.

<table border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">
  <thead>
    <tr>
       <th>Nombre de procesador multimedia</th>
       <th>Descripción</th>
	<th>Más información</th>
    </tr>
  </thead>
  <tbody>
    <tr>
       <td>Codificador multimedia de Azure</td>
       <td>Le permite ejecutar tareas de codificación con el Codificador multimedia.</td>
       <td><a href="http://msdn.microsoft.com/library/jj129582.aspx"> Cadenas preestablecidas de tarea para el Codificador multimedia de Azure</a></td>
    </tr>
    <tr>
        <td>Windows Azure Media Packager</td>
        <td>Le permite convertir recursos multimedia de .mp4 a un formato de Smooth Streaming. Además, le permite convertir recursos multimedia de Smooth Streaming al formato Apple HTTP Live Streaming (HLS).</td>
		<td><a href="http://msdn.microsoft.com/library/hh973635.aspx">Cadenas preestablecidas de tarea para el Empaquetador multimedia de Azure</a></td>
    </tr>
    <tr>
        <td>Windows Azure Media Encryptor</td>
        <td>Le permite cifrar recursos multimedia con la protección PlayReady.</td>
        <td><a href="http://msdn.microsoft.com/library/hh973610.aspx">Cadenas preestablecidas de tarea para el Empaquetador multimedia de Azure</a></td>
    </tr>
    <tr>
        <td>Azure Media Indexer</td>
        <td>Le permite crear archivos multimedia y contenido que se puede buscar, así como generar pistas y palabras clave de subtítulos (CC).</td>
		<td>N/D</td>
    </tr>
    <tr>
        <td>Storage Decryption</td>
        <td>Le permite descifrar recursos multimedia que se cifraron con el cifrado de almacenamiento.</td>
		<td>N/D</td>
    </tr>  </tbody>
</table>

<br />

## Obtener MediaProcessor

>[AZURE.NOTE] Al trabajar con la API de REST de Servicios multimedia, se aplican las consideraciones siguientes:
>
>Al obtener acceso a las entidades de Servicios multimedia, debe establecer los campos de encabezado específicos y los valores en las solicitudes HTTP. Para obtener más información, consulte [Configuración de desarrollo de la API de REST de Servicios multimedia](media-services-rest-how-to-use.md).

>Después de conectarse correctamente a https://media.windows.net, recibirá una redirección 301 que especifica otro URI de Servicios multimedia. Debe realizar las llamadas posteriores al nuevo URI tal como se describe en [Conexión a Servicios multimedia con la API de REST](media-services-rest-connect_programmatically.md). 



La siguiente llamada REST muestra cómo obtener una instancia de procesador multimedia por nombre (en este caso, **Codificador de Servicios multimedia**). 

	
Solicitud:

	GET https://media.windows.net/api/MediaProcessors()?$filter=Name%20eq%20'Azure%20Media%20Encoder' HTTP/1.1
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	User-Agent: Microsoft ADO.NET Data Services
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=juliakoams1&urn%3aSubscriptionId=bbbef702-e769-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423635565&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=6zwXEn7YJzVJbVCNpqDUjBLuE5iUwsdJbWvJNvpY3%2b8%3d
	x-ms-version: 2.8
	Host: media.windows.net
	
Respuesta:
	
	HTTP/1.1 200 OK
	Cache-Control: no-cache
	Content-Length: 273
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Server: Microsoft-IIS/8.5
	x-ms-client-request-id: 8a291764-4ed7-405d-aa6e-d3ebabb0b3f6
	request-id: dceeb559-48b5-48e1-81d3-d324b6203d51
	x-ms-request-id: dceeb559-48b5-48e1-81d3-d324b6203d51
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Wed, 11 Feb 2015 00:19:56 GMT
	
	{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#MediaProcessors","value":[{"Id":"nb:mpid:UUID:1b1da727-93ae-4e46-a8a1-268828765609","Description":"Azure Media Encoder","Name":"Azure Media Encoder","Sku":"","Vendor":"Microsoft","Version":"4.4"}]}


## Pasos siguientes
Ahora que sabe cómo obtener una instancia de procesador multimedia, consulte el tema [Codificación de un recurso][], que le mostrará cómo utilizar el Codificador multimedia de Azure para codificar un recurso.

[Codificación de un recurso]: ../media-services-rest-encode-asset/
[Cadenas preestablecidas de tarea para el Codificador multimedia de Azure]: http://msdn.microsoft.com/library/jj129582.aspx
[Conexión con los Servicios multimedia mediante programación]: ../media-services-rest-connect_programmatically/
<!--HONumber=47-->
