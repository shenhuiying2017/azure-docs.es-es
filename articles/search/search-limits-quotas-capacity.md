<properties
	pageTitle="Límites de servicio en Búsqueda de Azure | Microsoft Azure"
	description="Límites de servicio usados en la planeación de la capacidad y los límites máximos de solicitudes y respuestas de Búsqueda de Azure."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="jhubbard"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="NA"
	ms.workload="search"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.date="08/03/2016"
	ms.author="heidist"/>

# Límites de servicio en la Búsqueda de Azure

Los límites máximos del almacenamiento, las cargas de trabajo y las cantidades de índices, documentos y otros objetos dependen de si agrega Búsqueda de Azure a un plan de tarifa **Gratis**, **Básico** o **Estándar**.

- El plan **Gratis** es un servicio multiinquilino compartido incluido en su suscripción de Azure. Es una opción sin costo adicional para los suscriptores existentes que permite experimentar con el servicio antes de registrarse para obtener recursos dedicados.
- El plan **Básico** proporciona recursos de proceso dedicados para cargas de trabajo de producción a escala más pequeña.
- El plan **Estándar** se ejecuta en máquinas dedicadas, con más almacenamiento y capacidad de procesamiento en cada nivel, incluida la configuración mínima. Standard incluye cuatro niveles: S1, S2, S3 y S3 High Density (S3 HD). S3 and S3 HD están actualmente en versión preliminar y se ofrece una tarifa reducida del 50 % durante este período.

Todos los niveles pueden ser [aprovisionados en el portal](search-create-service-portal.md). A un servicio se le asigna inicialmente una partición y una réplica, pero puede cambiar la asignación de recursos una vez creado el servicio. Consulte [Planeación de la capacidad en Búsqueda de Azure](search-capacity-planning.md) para más información.

## Límites por suscripción

[AZURE.INCLUDE [azure-search-limits-per-subscription](../../includes/azure-search-limits-per-subscription.md)]

## Por límites de servicio ##

[AZURE.INCLUDE [azure-search-limits-per-service](../../includes/azure-search-limits-per-service.md)]

## Por límites de índice ##

Hay una correspondencia uno a uno entre los límites de los índices y los límites de los indexadores. Dado un límite de 200 índices por servicio S2, el número de indexadores máximos y de orígenes de datos de indexador es también 200 para el mismo servicio.

Recurso|Gratuito|Básica |S1|S2|S3 (versión preliminar)|S3 HD (versión preliminar) 
---|---|---|---|---- |---|----
Índice: campos máximos por índice|1000|100 <sup>1</sup>|1000|1000|1000|1000 
Índice: perfiles de puntuación máximos por índice|16|16|16|16|16|16 
Índice: funciones máximas por perfil|8|8|8|8|8|8 
Indexadores: carga máxima de indexación por invocación|10 000 documentos|Limitado solamente por el número máximo de documentos|Limitado solamente por el número máximo de documentos|Limitado solamente por el número máximo de documentos|Limitado solamente por el número máximo de documentos|N/D <sup>2</sup> 
Indexadores: tiempo de ejecución máximo|3 minutos|24 horas|24 horas|24 horas|24 horas|N/D <sup>2</sup> 
Indexador de blobs: tamaño máximo de blob, MB|16|16|128|256|256|N/D <sup>2</sup> 
Indexador de blobs: número máximo de caracteres del contenido extraído de un blob|32 000|64 000|4 millones|4 millones|4 millones|N/D <sup>2</sup> 

<sup>1</sup> El nivel básico es el único SKU con un límite inferior de 100 campos por índice.

<sup>2</sup> S3 HD no admite actualmente indexadores u orígenes de datos de indexador. Póngase en contacto con el soporte técnico de Azure si necesita urgentemente esta funcionalidad.

## Límites de tamaño de documento ##

Recurso|Gratuito|Básica |S1|S2|S3 (versión preliminar)|S3 HD (versión preliminar) 
---|---|---|---|---- |---|----
Tamaño de documento individual por API de índice|<16 MB|<16 MB|<16 MB |<16 MB|<16 MB|<16 MB

Hace referencia al tamaño máximo del documento cuando se llama a una API de índice. El tamaño del documento es de hecho un límite en el tamaño del cuerpo de la solicitud de la API de índice. Dado que se puede pasar un lote de varios documentos a la API de índice de una vez, el límite de tamaño depende de cuántos documentos estén en el lote. Para un lote con un único documento, el tamaño máximo del documento será de 16 MB de JSON.

Para mantener el tamaño del documento reducido, no olvide excluir los datos no consultables de la solicitud. Las imágenes y otros datos binarios no son consultables directamente y no se deben almacenar en el índice. Para integrar los datos no consultables en los resultados de búsqueda, defina un campo que admita búsqueda que almacene una referencia de dirección URL al recurso.

## Límites de carga de trabajo (consultas por segundo) ##

Recurso|Gratuito|Básica|S1|S2|S3 (versión preliminar)|S3 HD (versión preliminar)
---|---|---|---|----|---|----
QPS|N/D|~ 3 por réplica|~ 15 por réplica|~ 60 por réplica|>60 por réplica|>60 por réplica

Las consultas por segundo (QPS) son una aproximación basada en heurística, mediante cargas de trabajo de clientes reales y simuladas para obtener valores estimados. El rendimiento QPS exacto dependerá de los datos y de la naturaleza de la consulta.

Aunque se proporcionan cálculos aproximados, una tasa real es difícil de determinar, en especial en el servicio compartido gratuito donde el rendimiento se basa en el ancho de banda disponible y en la competición por los recursos del sistema. En el nivel Gratis, los recursos de proceso y almacenamiento que respaldan el servicio compartido se comparten entre varios suscriptores, de modo que el número de QPS de su solución siempre variará según cuántas otras cargas de trabajo se estén ejecutando al mismo tiempo.

En el nivel Estándar, puede calcular el número de QPS con más precisión porque tiene control sobre más parámetros. Consulte la sección de procedimientos recomendados en [Administración del servicio de búsqueda en Microsoft Azure](search-manage.md) para obtener instrucciones sobre cómo calcular el valor de QPS para las cargas de trabajo.

## Límites de solicitud de API

- Máximo de 16 MB por solicitud <sup>1</sup>
- Longitud máxima de dirección URL de 8 KB
- Máximo de 1000 documentos por lote del índice de cargas de índices, combinaciones o eliminaciones
- Máximo de 32 campos en cláusula $orderby
- El tamaño máximo del término de búsqueda es de 32 766 bytes (32 KB menos 2 bytes) de texto con codificación UTF-8

<sup>1</sup> En Búsqueda de Azure, el cuerpo de una solicitud está sujeto a un límite superior de 16 megabytes, que impone un límite práctico en el contenido de campos individuales o colecciones que no esté restringido de algún modo por límites teóricos (consulte [Tipos de datos admitidos](https://msdn.microsoft.com/library/azure/dn798938.aspx) para más información sobre composición de campos y restricciones).

## Límites de respuesta de API

- Máximo de 1000 documentos devueltos por página de resultados de búsqueda
- Máximo de 100 sugerencias devueltas por solicitud de Sugerir API

## Límites de clave de API

Las claves de API se usan para la autenticación del servicio. Hay dos tipos. Las claves de administración se especifican en el encabezado de solicitud y conceden acceso completo de lectura y escritura al servicio. Las claves de consulta son de solo lectura, se especifican en la dirección URL y normalmente se distribuyen a las aplicaciones cliente.

- Máximo de 2 claves de administración por servicio
- Máximo de 50 claves de consultas por servicio

<!---HONumber=AcomDC_0914_2016-->