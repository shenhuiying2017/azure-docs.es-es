<properties 
	pageTitle="Administración de extremos de streaming en una cuenta de Servicios multimedia" 
	description="En este tema se muestra cómo administrar los extremos de streaming mediante el Portal de administración de Azure." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	writer="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/15/2015" 
	ms.author="juliako"/>


# <a id="managemediaservicesorigins"></a>Administración de extremos de streaming en una cuenta de Servicios multimedia

Este artículo forma parte de la serie [Flujo de trabajo de vídeo bajo demanda de Servicios multimedia](../media-services-video-on-demand-workflow) y [Flujo de trabajo de streaming en vivo de Servicios multimedia](../media-services-live-streaming-workflow).  


En Servicios multimedia, un extremo de streaming representa un servicio de streaming que puede entregar contenido directamente a una aplicación de reproducción de cliente o a una red de entrega de contenido (CDN) para la distribución posterior. Actualmente, los Servicios multimedia de Microsoft Azure no ofrecen una integración perfecta de CDN, pero puede usar uno de los proveedores de CDN en el mercado (red CDN de Azure o Akamai). La secuencia de salida del servicio de extremo de streaming puede ser streaming en vivo o un recurso de vídeo bajo demanda en la cuenta de Servicios multimedia. 

Además, puede controlar la capacidad del servicio de extremo de streaming para administrar las necesidades crecientes de ancho de banda mediante el ajuste de unidades de escalado (también conocidas como unidades de streaming). Se recomienda asignar una o más unidades de escalado para las aplicaciones en el entorno de producción. Las unidades de escalado proporcionan capacidad de salida dedicada que puede adquirirse en incrementos de 200 Mbps y funcionalidad adicional que actualmente incluye el uso de empaquetado dinámico. 

En este tema se muestra cómo administrar los extremos de streaming mediante el Portal de administración de Azure.


## Adición y eliminación de extremos de streaming 

1. En el [Portal de administración](https://manage.windowsazure.com/), haga clic en **Servicios multimedia**. A continuación, haga clic en el nombre del servicio multimedia.
2. Seleccione la página **EXTREMOS DE STREAMING**. 
3. Haga clic en el botón ADD o DELETE en la parte inferior de la página. Tenga en cuenta que no es posible eliminar el extremo de streaming predeterminado. 
4. Haga clic en el botón INICIAR para iniciar el extremo de streaming. 
5. Haga clic en el nombre del extremo de streaming para configurarlo.   

	![Origin page][origin-page]

## <a id="scale_streaming_endpoints"></a>Escalado del extremo de streaming

Las unidades de streaming proporcionan capacidad de salida dedicada que puede adquirirse en incrementos de 200 Mbps y una funcionalidad adicional que actualmente incluye [capacidades de empaquetado dinámico](http://go.microsoft.com/fwlink/?LinkId=276874). De forma predeterminada, el streaming se configura en un modelo de instancia compartida para el que los recursos del servidor (por ejemplo, cálculo, capacidad de salida, etc.) se comparten con los demás usuarios. Para mejorar el rendimiento de streaming, se recomienda adquirir unidades de streaming. 

Para cambiar el número de unidades de streaming, haga lo siguiente:

1. Para especificar el número de unidades de streaming, seleccione la pestaña ESCALA y desplace el control deslizante de **capacidad reservada**.

	![Scale page](./media/media-services-how-to-scale/media-services-origin-scale.png)

4. Presione el botón SAVE para guardar los cambios.

	La asignación de cualquier nueva unidad de streaming puede tardar unos 20 minutos en finalizarse. 

	 
	>[AZURE.NOTE] Actualmente, pasar de cualquier valor positivo de unidades de streaming a ninguno puede deshabilitar el streaming a petición hasta una hora.

	>[AZURE.NOTE] Se utiliza el número más elevado de unidades especificadas durante el período de 24 horas al calcular el coste. Para obtener más información acerca del precio, consulte la página sobre [información del precio de Servicios multimedia](http://go.microsoft.com/fwlink/?LinkId=275107).
	
## Configuración del extremo de streaming

La pestaña CONFIGURAR permite realizar configuraciones, tal como se muestra en esta imagen. A continuación se muestra la descripción de los campos.

>[AZURE.NOTE] La configuración que se describe en esta página solo se aplica a los extremos de streaming que tienen al menos una unidad reservada. Para reservar las unidades reservadas de streaming.

![Configure origin][configure-origin]
  

1. Definir el período máximo de almacenamiento en caché que se especificará en el encabezado de control de caché de las respuestas HTTP. Este valor no sobrescribirá el valor máximo de caché que se definió explícitamente en el contenido de blobs.

2. Especificar las direcciones IP que podrán conectarse al extremo de streaming publicado. Si no se especificaron direcciones IP, cualquier dirección IP se podrá conectar.

3. Especifique la configuración para la autenticación del encabezado de firma de Akamai.

4. Puede especificar una directiva de acceso entre dominios para los clientes de Adobe Flash (para obtener más información, consulte [Especificación de archivos de directiva entre dominios](http://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html). Así como la directiva de acceso de cliente para los clientes de Microsoft Silverlight (para obtener más información, consulte [Disponibilidad de un servicio entre límites del dominio](https://msdn.microsoft.com/es-es/library/cc197955(v=vs.95).aspx),  

5. también puede configurar los nombres de host personalizado, al hacer clic en el botón **Configurar**. Para obtener más información, consulte la propiedad **CustomHostNames** en el tema [StreamingEndpoint](https://msdn.microsoft.com/es-es/library/dn783468.aspx).  



[origin-page]: ./media/media-services-manage-origins/media-services-origins-page.png
[configure-origin]: ./media/media-services-manage-origins/media-services-origins-configure.png
[configure-origin-configure-custom-host-names]: ./media/media-services-manage-origins/media-services-configure-custom-host-names.png

<!--HONumber=45--> 
