<properties 
	pageTitle="Uso de la red CDN - Guía de características de Azure" 
	description="Obtenga información acerca del uso de la Red de entrega de contenido (CDN) de Azure para ofrecer contenido con alto ancho de banda mediante el almacenamiento en caché de blobs y contenidos estáticos." 
	services="cdn" 
	documentationCenter=".net" 
	authors="zhangmanling" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cdn" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="05/19/2015" 
	ms.author="mazha"/>


# Uso de la red CDN en Azure

La Red de entrega de contenido (CDN) de Azure es el bloque de creación fundamental para escalar cualquier aplicación HTTP en Azure. Ofrece a los clientes de Azure una solución global de almacenamiento en caché y entrega de contenido a los usuarios finales. Como resultado, en lugar de acudir al origen cada vez, las solicitudes de usuario se enrutan de manera inteligente al POP perimetral de la red CDN de mejor rendimiento. Esto aumenta considerablemente el rendimiento y experiencia del usuario. Para obtener una lista actualizada de las ubicaciones de nodos de la red CDN, consulte [Ubicaciones del nodo red CDN de Azure].

Entre las ventajas de utilizar la red CDN para almacenar en memoria caché los datos de Azure se incluyen:

-   Mejor rendimiento y experiencia de usuario para aquellos usuarios finales que están lejos de un origen de contenido y utilicen aplicaciones donde son necesarias muchas conexiones a Internet para cargar el contenido.
-   Gran distribución para mejorar la administración de cargas instantáneas pesadas, por ejemplo, al comienzo de un acontecimiento como el lanzamiento de un producto.
-   Al distribuir las solicitudes de usuarios y al prestar servicio de contenido desde POP perimetrales globales, se envía menos tráfico al origen, causando la descarga del origen.

Los clientes existentes de la red CDN ahora pueden usar la red CDN de Azure a través del [Portal de administración de Azure]. La red CDN es una característica complementaria a su suscripción y tiene un [plan de facturación] independiente.

##Paso 1: Creación de un origen de red CDN en Azure

El origen de la red CDN es la ubicación desde la cual la red CDN recupera el contenido y la memoria caché en los POP perimetrales. El origen de Azure integrado incluye Aplicaciones de Azure, Servicios en la nube, Almacenamiento y Servicios multimedia.

## Paso 2: Creación de un extremo de red CDN que señala a su origen de Azure

Una vez configurado el origen, estará disponible en la lista de orígenes cuando [cree un nuevo extremo de la red CDN](cdn-create-new-endpoint.md).

> [AZURE.NOTE]La configuración que ha establecido para el extremo no estará disponible de forma inmediata; el registro puede tardar hasta 60 minutos en propagarse a través de la red CDN. Es posible que los usuarios que intenten usar el nombre de dominio de la red CDN de forma inmediata reciban el código de estado 400 (solicitud incorrecta) hasta que el contenido esté disponible a través de la red CDN.

## Paso 3: Establecimiento de la configuración de la red CDN 

Puede habilitar a una serie de características para el extremo de la red CDN, como almacenamiento en caché de directivas, almacenamiento en caché de la cadena de consulta, etc.

## Paso 4: Acceso a su contenido de la red CDN

Para obtener acceso al contenido almacenado en la memoria caché de la red CDN, use la URL de la red CDN que se le ha proporcionado en el portal. Por ejemplo, la dirección de un blob en caché será similar a la siguiente: http://<*EspacioDeNombresCDN*>.vo.msecnd.net/<*miContenedorPúblico*>/<*nombreBlob*>



## Consulte también

[Información general de la red de entrega de contenido (CDN) de Azure](cdn-overview.md)
 

<!---HONumber=August15_HO6-->