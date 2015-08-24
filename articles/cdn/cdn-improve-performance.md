<properties 
	pageTitle="Red CDN - Mejora del rendimiento mediante la compresión de archivos" 
	description="Puede mejorar la velocidad de transferencia de archivos y aumentar el rendimiento de carga de página mediante la compresión de archivos." 
	services="cdn" 
	documentationCenter=".NET" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cdn" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/10/2015" 
	ms.author="juliako"/>

#Mejora del rendimiento mediante la compresión de archivos

Este tema trata de cómo mejorar la velocidad de transferencia de archivos y aumentar el rendimiento de carga de página mediante la compresión de archivos.

Hay dos maneras de que la red CDN pueda admitir la compresión:

- Puede habilitar la compresión en el servidor de origen, en cuyo caso la red CDN admitirá la compresión de forma predeterminada y entregará archivos comprimidos a los clientes. 
- Puede habilitar la compresión directamente en los servidores perimetrales de la red CDN, en cuyo caso esta red comprimirá los archivos y los servirá a los usuarios finales.

##Definiciones

- Encabezado de solicitud **Accept-Encoding**: este encabezado indica los métodos de compresión que admite un agente de usuario. Se debe incluir en el encabezado de solicitud. Para obtener más información, consulte [Header Field Definitions](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html) (Definiciones de campos de encabezado).
- **Tipo de contenido**: se debe agregar una lista de tipos de contenido para la compresión. Solo los archivos de texto son válidos para la compresión. Por ejemplo, texto/sin formato, texto/html.
- **Método de compresión**: los métodos de compresión admitidos son gzip/deflate/bzip2; un método admitido debe establecerse en el encabezado de solicitud Accept-Encoding. 
- **Estado de caché**: el estado de la memoria caché identifica si el contenido solicitado ya está almacenado en caché en el POP más cercano al solicitante.  
- **Tamaño de archivo**: de forma predeterminada la compresión solo admite archivos mayores que 1 byte y menores que 1MB.  

##Flujo de trabajo

1. El solicitante envía una solicitud de contenido.
2. Un servidor perimetral comprueba si hay un encabezado **Accept-Encoding**.
	1. Si se incluye, este encabezado identifica el método de compresión solicitado.
	1. Si falta, este tipo de solicitud se servirá en un formato sin comprimir.
3.	El POP perimetral más cercano comprueba el estado de la memoria caché, el método de compresión y si aún tiene un período de vida válido.
	1.	Error de caché: si la versión solicitada no está en la caché, la solicitud se reenvía al origen.
	2.	Acierto de caché con el mismo método de compresión: el servidor perimetral entregará inmediatamente el contenido comprimido al cliente.
	3.	Acierto de caché con un método de compresión diferente: el servidor perimetral transcodificará el recurso para el método de compresión solicitado. 
	4.	Acierto de caché y sin comprimir: si la solicitud inicial provocó que el activo se almacenara en un formato sin comprimir, se realizará una comprobación para ver si es válida la solicitud para la compresión de servidor perimetral (según los criterios de la sección anterior sobre definición y requisitos).
		1.	Si es válida, el servidor perimetral comprimirá el archivo y lo servirá al cliente.
		2.	Si no es válida: el servidor perimetral entregará inmediatamente el contenido sin comprimir al cliente. 

![Compresión de archivos](./media/cdn-file-compression/cdn-compress-files.png)

##Consideraciones 

1. Para los extremos de streaming habilitados para la red CDN de Servicios multimedia, la compresión está habilitada de forma predeterminada para los siguientes tipos de contenido: application/vnd.ms-sstr+xml,application/dash+xml,application/vnd.apple.mpegurl,application/f4m+xml. No se puede habilitar o deshabilitar la compresión de los tipos mencionados mediante el Portal de Azure.  
2. Una sola versión del archivo (comprimido o sin comprimir) se almacenará en caché en el servidor perimetral. Una solicitud de una versión diferente provocará que el servidor perimetral transcodifique el contenido.  

<!---HONumber=August15_HO7-->