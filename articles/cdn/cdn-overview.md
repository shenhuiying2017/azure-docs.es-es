<properties
	pageTitle="Información general de la red CDN de Azure"
	description="Obtenga información acerca de la Red de entrega de contenido (CDN) de Azure y de cómo usarla para ofrecer contenido con alto ancho de banda mediante el almacenamiento en caché de blobs y contenidos estáticos."
	services="cdn"
	documentationCenter=".NET"
	authors="camsoper"
	manager="erikre"
	editor=""/>

<tags
	ms.service="cdn"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="04/15/2016" 
	ms.author="casoper"/>

# Información general de la red de entrega de contenido (CDN) de Azure

La Red de entrega de contenido (CDN) de Azure almacena en caché blobs de Azure y contenido estático usado por sitios web en ubicaciones colocadas estratégicamente para proporcionar el máximo rendimiento a la hora de proporcionar contenido a los usuarios. CDN ofrece a los desarrolladores una solución global para entregar contenido de alto ancho de banda almacenando en caché el contenido en nodos físicos en todo el mundo. Para obtener una lista actualizada de las ubicaciones de nodos de la red CDN, consulte [Ubicaciones POP de la Red de entrega de contenido (CDN) de Azure](cdn-pop-locations.md).

Entre las ventajas de utilizar la red CDN para almacenar en memoria caché los datos de Azure se incluyen:

- Mejor rendimiento y experiencia del usuario para los usuarios finales sobre todo a la hora de utilizar aplicaciones donde son necesarios varios recorridos de ida y vuelta para cargar el contenido
- Gran escalado para mejorar la administración de cargas instantáneas pesadas, por ejemplo, al comienzo de un evento de lanzamiento de un producto.
- Al distribuir solicitudes de usuarios y ofrecer contenido desde servidores perimetrales, se envía menos tráfico al origen.


## Cómo funciona

![Información general de la red CDN](./media/cdn-overview/cdn-overview.png)

1. Un usuario (Alice) solicita un archivo (también denominado un recurso) mediante una dirección URL con un nombre de dominio especial, como `<endpointname>.azureedge.net`. DNS enruta la solicitud hacia la ubicación del punto de presencia (POP) con mejor rendimiento. Este es normalmente el POP geográficamente más cercano al usuario.

2. Si los servidores perimetrales del POP no tienen el archivo en la memoria caché, el servidor perimetral solicitará el archivo desde el origen. El origen puede ser una aplicación web, servicio en la nube o cuenta de Almacenamiento de Azure, o cualquier servidor web accesible públicamente.

3. El origen devuelve el archivo al servidor perimetral, incluidos los encabezados HTTP opcionales que describen el período de vida (TTL) del archivo.

4. El servidor perimetral almacena el archivo en caché y devuelve el archivo al solicitante original (Alice). El archivo permanecerá en caché en el servidor perimetral hasta que expire el TTL. Si el origen no ha especificado un TTL, el valor predeterminado será de 7 días.

5. Otros usuarios adicionales (por ejemplo, Bob) pueden solicitar, a continuación, el mismo archivo mediante esa misma dirección URL y también se les puede dirigir a ese mismo POP.

6. Si no ha expirado el TTL para el archivo, el servidor perimetral devolverá el archivo desde la memoria caché. Esto genera una experiencia de usuario más rápida y una mayor capacidad de respuesta.


## Características estándar

El nivel de red CDN estándar incluye las siguientes funciones:

- Fácil integración con servicios de Azure como [Almacenamiento](cdn-create-a-storage-account-with-cdn.md), [Servicios en la nube](cdn-cloud-service-with-cdn.md), [Aplicaciones web](../app-service-web/cdn-websites-with-cdn.md) y [Servicios multimedia](../media-services/media-services-manage-origins.md#enable_cdn)
- Compatibilidad con HTTPS
- Equilibrio de carga
- Protección DDOS
- [Almacenamiento en caché de cadena de consulta](cdn-query-string.md)
- [Compatibilidad con nombre de dominio personalizado](cdn-map-content-to-custom-domain.md)
- [Filtrado por país](cdn-restrict-access-by-country.md)
- [Análisis esencial](cdn-analyze-usage-patterns.md)
- [Purga rápida](cdn-purge-endpoint.md)
- [Carga previa de activos](cdn-preload-endpoint.md)
- [Administración mediante API de REST](https://msdn.microsoft.com/library/mt634456.aspx)


## Características de la edición Premium

El nivel premium de la red de entrega de contenido incluye todas las características del nivel estándar, más estas características adicionales:

- [Motor de entrega de contenido personalizable, basado en reglas](cdn-rules-engine.md)
- [Informes de HTTP avanzados](cdn-advanced-http-reports.md)
- [Estadísticas en tiempo real](cdn-real-time-stats.md)

## Pasos siguientes

Para empezar a trabajar con la red CDN, consulte [Uso de CDN de Azure](./cdn-create-new-endpoint.md).

Si es un cliente de red de entrega de contenido existente, ahora puede administrar sus puntos de conexión de red de entrega de contenido a través del [Portal de Microsoft Azure](https://portal.azure.com).

Para ver la red CDN en acción, consulte el [vídeo sobre nuestra sesión de Build 2016](https://azure.microsoft.com/documentation/videos/build-2016-leveraging-the-new-azure-cdn-apis-to-build-wicked-fast-applications/).

<!---HONumber=AcomDC_0504_2016-->