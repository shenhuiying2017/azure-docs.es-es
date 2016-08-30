<properties
	pageTitle="Información general de CDN de Azure | Microsoft Azure"
	description="Obtenga información acerca de la Red de entrega de contenido (CDN) de Azure y de cómo usarla para ofrecer contenido con alto ancho de banda mediante el almacenamiento en caché de blobs y contenidos estáticos."
	services="cdn"
	documentationCenter=""
	authors="camsoper"
	manager="erikre"
	editor=""/>

<tags
	ms.service="cdn"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="08/09/2016"
	ms.author="casoper"/>

# Información general de la red de entrega de contenido (CDN) de Azure

> [AZURE.NOTE] En este documento se describe qué es la red de entrega de contenido (CDN) de Azure, cómo funciona y las características de cada producto CDN de Azure. Si desea omitir esta información y pasar directamente a un tutorial sobre cómo crear un punto de conexión de CDN, consulte [Uso de CDN de Azure](cdn-create-new-endpoint.md). Para ver una lista de ubicaciones actuales de nodos de CDN, consulte [Ubicaciones POP de la Red de entrega de contenido (CDN) de Azure](cdn-pop-locations.md).

La Red de entrega de contenido (CDN) de Azure almacena en caché contenido de web estático en ubicaciones colocadas estratégicamente para proporcionar el máximo rendimiento a la hora de proporcionar contenido a los usuarios. CDN ofrece a los desarrolladores una solución global para entregar contenido de alto ancho de banda almacenando en caché el contenido en nodos físicos en todo el mundo.

Entre las ventajas de utilizar la red CDN para almacenar en memoria caché los recursos de sitios web se incluyen:

- Mejor rendimiento y experiencia del usuario para los usuarios finales sobre todo a la hora de utilizar aplicaciones donde son necesarios varios recorridos de ida y vuelta para cargar el contenido
- Gran escalado para mejorar la administración de cargas instantáneas pesadas, por ejemplo, al comienzo de un evento de lanzamiento de un producto.
- Al distribuir solicitudes de usuarios y ofrecer contenido desde servidores perimetrales, se envía menos tráfico al origen.


## Cómo funciona

![Información general de la red CDN](./media/cdn-overview/cdn-overview.png)

1. Un usuario (Alice) solicita un archivo (también denominado un recurso) mediante una dirección URL con un nombre de dominio especial, como `<endpointname>.azureedge.net`. DNS enruta la solicitud hacia la ubicación del punto de presencia (POP) con mejor rendimiento. Este es normalmente el POP geográficamente más cercano al usuario.

2. Si los servidores perimetrales del POP no tienen el archivo en la memoria caché, el servidor perimetral solicitará el archivo desde el origen. El origen puede ser una aplicación web, servicio en la nube o cuenta de Almacenamiento de Azure, o cualquier servidor web accesible públicamente.

3. El origen devuelve el archivo al servidor perimetral, incluidos los encabezados HTTP opcionales que describen el período de vida (TTL) del archivo.

4. El servidor perimetral almacena el archivo en caché y devuelve el archivo al solicitante original (Alice). El archivo permanece en caché en el servidor perimetral hasta que expire el TTL. Si el origen no ha especificado ningún TTL, el valor predeterminado será de siete días.

5. Otros usuarios adicionales (por ejemplo, Bob) pueden solicitar, a continuación, el mismo archivo mediante esa misma dirección URL y también se les puede dirigir a ese mismo POP.

6. Si no ha expirado el TTL para el archivo, el servidor perimetral devolverá el archivo desde la memoria caché. Esto genera una experiencia de usuario más rápida y una mayor capacidad de respuesta.


## Características de la red CDN de Azure

Hay tres productos de red CDN de Azure: **Red CDN estándar de Azure de Akamai**, **Red CDN estándar de Azure de Verizon** y **Red CDN premium de Azure de Verizon**. En la tabla siguiente se enumeran las características disponibles con cada producto.

| | Estándar de Akamai | Estándar de Verizon | Premium de Verizone |
|-------|-----------------|------------------|-----------------|
| Fácil integración con servicios de Azure como [Almacenamiento](cdn-create-a-storage-account-with-cdn.md), [Servicios en la nube](cdn-cloud-service-with-cdn.md), [Aplicaciones web](../app-service-web/cdn-websites-with-cdn.md) y [Servicios multimedia](../media-services/media-services-manage-origins.md#enable_cdn) | **&#x2713;** | **&#x2713;** | **&#x2713;**|
| Compatibilidad con HTTPS | **&#x2713;** | **&#x2713;** | **&#x2713;** |
| Equilibrio de carga | **&#x2713;** | **&#x2713;** | **&#x2713;** |
| Protección DDOS | **&#x2713;** | **&#x2713;** | **&#x2713;** |
| Pila dual IPv4/IPv6 | **&#x2713;** | **&#x2713;** | **&#x2713;** |
| [HTTP/2](https://msdn.microsoft.com/library/mt762901.aspx) | **&#x2713;** | | |
| [Compatibilidad con nombre de dominio personalizado](cdn-map-content-to-custom-domain.md) | **&#x2713;** | **&#x2713;** | **&#x2713;** |
| [Almacenamiento en caché de cadena de consulta](cdn-query-string.md) | **&#x2713;** | **&#x2713;** | **&#x2713;** |
| [Filtrado por país](cdn-restrict-access-by-country.md) | | **&#x2713;** | **&#x2713;** |
| [Purga rápida](cdn-purge-endpoint.md) | **&#x2713;** | **&#x2713;** | **&#x2713;** |
| [Carga previa de activos](cdn-preload-endpoint.md) | | **&#x2713;** | **&#x2713;** |
| [Análisis esencial](cdn-analyze-usage-patterns.md) | | **&#x2713;** | **&#x2713;** |
| Administración a través de [API de REST](https://msdn.microsoft.com/library/mt634456.aspx), [.NET](./cdn-app-dev-net.md), [Node.js](./cdn-app-dev-node.md) o [PowerShell](./cdn-manage-powershell.md). | **&#x2713;** | **&#x2713;** | **&#x2713;** |
| [Motor de entrega de contenido personalizable, basado en reglas](cdn-rules-engine.md) | | | **&#x2713;** |
| [Informes de HTTP avanzados](cdn-advanced-http-reports.md) | | | **&#x2713;** |
| [Estadísticas en tiempo real](cdn-real-time-stats.md) | | | **&#x2713;** |
| [Alertas en tiempo real](cdn-real-time-alerts.md) | | | **&#x2713;** |

>[AZURE.TIP] ¿Hay una característica que le gustaría ver en CDN de Azure? [Envíenos sus comentarios](https://feedback.azure.com/forums/169397-cdn).

## Pasos siguientes

Para empezar a trabajar con la red CDN, consulte [Uso de CDN de Azure](./cdn-create-new-endpoint.md).

Si es un cliente de red de entrega de contenido existente, ahora puede administrar sus puntos de conexión de red de entrega de contenido a través del [Portal de Microsoft Azure](https://portal.azure.com) o con [PowerShell](cdn-manage-powershell.md).

Para ver la red CDN en acción, consulte el [vídeo sobre nuestra sesión de Build 2016](https://azure.microsoft.com/documentation/videos/build-2016-leveraging-the-new-azure-cdn-apis-to-build-wicked-fast-applications/).

Aprenda a automatizar la red CDN de Azure con [.NET](./cdn-app-dev-net.md) o [Node.js](./cdn-app-dev-node.md).

Para más información sobre los precios, consulte [Precios de Red de entrega de contenido (CDN)](https://azure.microsoft.com/pricing/details/cdn/).

<!---HONumber=AcomDC_0824_2016-->