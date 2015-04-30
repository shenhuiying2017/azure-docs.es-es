<properties 
	pageTitle="Administración de extremos de streaming en una cuenta de Servicios multimedia" 
	description="En este tema se muestra cómo administrar los extremos de streaming mediante el Portal de administración de Azure." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	writer="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/10/2015" 
	ms.author="juliako"/>


#<a id="managemediaservicesorigins"></a>Administración de extremos de streaming en una cuenta de Servicios multimedia

Este artículo forma parte de la serie [Flujo de trabajo de vídeo bajo demanda de Servicios multimedia](media-services-video-on-demand-workflow.md) y [Flujo de trabajo de streaming en directo de Servicios multimedia](media-services-live-streaming-workflow.md).  


En Servicios multimedia de Microsoft Azure, un **extremo de streaming** representa un servicio de streaming que puede entregar contenido directamente a una aplicación de reproducción de cliente o a una red de entrega de contenido (CDN) para la distribución posterior. Servicios multimedia también proporciona integración sin problemas de CDN de Azure. La secuencia de salida de un servicio de StreamingEndpoint puede ser una secuencia en vivo o un recurso de vídeo bajo demanda en su cuenta de Servicios multimedia.  

Además, puede controlar la capacidad del servicio de extremo de streaming para administrar las necesidades crecientes de ancho de banda mediante el ajuste de unidades de escalado (también conocidas como unidades de streaming). Se recomienda asignar una o más unidades de escalado para las aplicaciones en el entorno de producción. Las unidades de escalado proporcionan capacidad de salida dedicada que puede adquirirse en incrementos de 200 Mbps y funcionalidad adicional cuya funcionalidad incorpora: [empaquetado dinámico](https://msdn.microsoft.com/library/azure/jj889436.aspx), integración de CDN y configuración avanzada.

Tenga en cuenta que solo se le cobrará cuando StreamingEndpoint esté en estado en ejecución. 

Este tema proporciona información general de las principales funcionalidades proporcionadas por los extremos de streaming. El tema también muestra cómo usar el Portal de administración de Azure para administrar extremos de streaming.


##Adición y eliminación de extremos de streaming 

Puede agregar o quitar extremos de streaming con .NET SDK, la API de REST o el Portal de administración de Azure.

Para agregar/quitar el extremo de streaming mediante el Portal, haga lo siguiente:

1. En el [Portal de administración](https://manage.windowsazure.com/), haga clic en **Servicios multimedia**. A continuación, haga clic en el nombre del servicio multimedia.
2. Seleccione la página **EXTREMOS DE STREAMING**. 
3. Haga clic en el botón ADD o DELETE en la parte inferior de la página. Tenga en cuenta que no es posible eliminar el extremo de streaming predeterminado. 
4. Haga clic en el botón INICIAR para iniciar el extremo de streaming. 
5. Haga clic en el nombre del extremo de streaming para configurarlo.   

	![Página de extremo de streaming][streaming-endpoint]


De forma predeterminada puede tener hasta dos extremos de streaming. Si necesita solicitar más, consulte [Cuotas y limitaciones](media-services-quotas-and-limitations/).

##<a id="scale_streaming_endpoints"></a>Escalado del extremo de streaming

Las unidades de streaming proporcionan capacidad de salida dedicada que puede adquirirse en incrementos de 200 Mbps y una funcionalidad adicional que actualmente incluye [capacidades de empaquetado dinámico](http://go.microsoft.com/fwlink/?LinkId=276874). De forma predeterminada, el streaming se configura en un modelo de instancia compartida para el que los recursos del servidor (por ejemplo, cálculo, capacidad de salida, etc.) se comparten con los demás usuarios. Para mejorar el rendimiento de streaming, se recomienda adquirir unidades de streaming. 

Puede escalar con el SDK de .NET , la API de REST o el Portal de administración de Azure.

Para cambiar el número de unidades de streaming mediante el Portal, haga lo siguiente:

1. Para especificar el número de unidades de streaming, seleccione la pestaña ESCALA y desplace el control deslizante de **capacidad reservada**.

	![Página de escala](./media/media-services-how-to-scale/media-services-origin-scale.png)

4. Presione el botón SAVE para guardar los cambios.

	La asignación de cualquier nueva unidad de streaming puede tardar unos 20 minutos en finalizarse. 

	 
	>[AZURE.NOTE] Actualmente, pasar de cualquier valor positivo de unidades de streaming a ninguno puede deshabilitar el streaming a petición hasta una hora.

	>[AZURE.NOTE] Se utiliza el número más elevado de unidades especificadas durante el período de 24 horas al calcular el coste. Para obtener más información acerca del precio, consulte la página sobre [información del precio de Servicios multimedia](http://go.microsoft.com/fwlink/?LinkId=275107).
	
##<a id="configure_streaming_endpoints"></a>Configuración del extremo de streaming

Extremo de streaming le permite configurar las siguientes propiedades cuando tenga al menos una unidad de escala: 

- Control de acceso
- Nombres de host personalizados
- Control de caché
- Directivas de acceso en varios sitios

Para obtener información detallada acerca de estas propiedades, consulte [StreamingEndpoint](https://msdn.microsoft.com/library/azure/dn783468.aspx).

Puede configurar estas propiedades utilizando el SDK de .NET, la API de REST o el Portal de administración de Azure.

Para cambiar el número de unidades de streaming mediante el Portal, haga lo siguiente:

1. Seleccione el extremo de streaming que desee configurar.
1. Seleccione la pestaña CONFIGURAR.
  
Aparecerá una breve descripción de los campos.

![Configure origin][configure-origin]
  

1. Definir el período máximo de almacenamiento en caché que se especificará en el encabezado de control de caché de las respuestas HTTP. Este valor no sobrescribirá el valor máximo de caché que se definió explícitamente en el contenido de blobs.

2. Especificar las direcciones IP que podrán conectarse al extremo de streaming publicado. Si no se especificaron direcciones IP, cualquier dirección IP se podrá conectar.

3. Especifique la configuración para la autenticación del encabezado de firma de Akamai.

4. Puede especificar una directiva de acceso entre dominios para los clientes de Adobe Flash (para obtener más información, consulte [Especificación de archivos de directiva entre dominios](http://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html). Así como la directiva de acceso de cliente para los clientes de Microsoft Silverlight (para obtener más información, consulte [Disponibilidad de un servicio entre límites del dominio](https://msdn.microsoft.com/library/cc197955(v=vs.95).aspx),  

5. también puede configurar los nombres de host personalizado, al hacer clic en el botón **Configurar**. Para obtener más información, consulte la propiedad **CustomHostNames** en el tema [StreamingEndpoint](https://msdn.microsoft.com/library/dn783468.aspx).  


##<a id="enable_cdn"></a>Habilitación de la integración de CDN de Azure

Puede especificar habilitar la integración de CDN de Azure para un extremo de streaming(está deshabilitada de forma predeterminada).

Para configurar la integración de CDN de Azure en true:

- El extremo de streaming debe tener al menos una unidad (escala) de streaming. Si más adelante desea establecer unidades de escala en 0, primero debe deshabilitar la integración de CDN. 

- El extremo de streaming debe estar en un estado detenido. Cuando se habilita CDN, puede iniciar el extremo de streaming. 

Es posible que la integración de CDN de Azure tarde en habilitarse hasta 90 minutos.   

Una vez habilitada, se deshabilitan las siguientes configuraciones: **Nombres de host personalizados** y **Control de acceso**.

No todos los centros de datos admiten la integración de CDN de Azure. Si utiliza el Portal de administración de Azure, verá que la opción **Habilitar CDN** si está disponible en el centro de datos. 

![Streaming Endpoint Enable CDN][streaming-endpoint-enable-cdn]

Si utiliza el SDK de .NET o la API de REST, deberá comprobar si su centro de datos tiene la integración de CDN de Azure disponible. 

Para comprobarlo, haga lo siguiente:

1. Intente establecer CdnEnabled en true.
1. Compruebe el resultado devuelto de un código de Error HTTP 412 (PreconditionFailed) con un mensaje del tipo "La propiedad CdnEnabled del extremo de streaming no puede establecerse en true porque  la capacidad de CDN no está disponible en la región actual". 

	Si obtiene este error, el centro de datos no lo admite. Pruebe con otro centro de datos.


[streaming-endpoint-enable-cdn]: ./media/media-services-manage-origins/media-services-origins-enable-cdn.png
[streaming-endpoint]: ./media/media-services-manage-origins/media-services-origins-page.png
[configure-origin]: ./media/media-services-manage-origins/media-services-origins-configure.png
[configure-origin-configure-custom-host-names]: ./media/media-services-manage-origins/media-services-configure-custom-host-names.png

<!--HONumber=52-->