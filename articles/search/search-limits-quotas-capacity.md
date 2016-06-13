<properties
	pageTitle="Límites de servicio en Búsqueda de Azure | Microsoft Azure | Servicio de búsqueda hospedado en la nube"
	description="Límites de servicio usados en la planeación de la capacidad y los límites máximos de solicitudes y respuestas de Búsqueda de Azure."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="NA"
	ms.workload="search"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.date="05/25/2016"
	ms.author="heidist"/>

# Límites de servicio en la Búsqueda de Azure

Los límites máximos del almacenamiento, las cargas de trabajo y las cantidades de índices, documentos y otros objetos dependen de si agrega Búsqueda de Azure a un plan de tarifa **Gratis**, **Básico** o **Estándar**.

- El plan **Gratis** es un servicio multiinquilino compartido incluido en su suscripción de Azure. Es una opción sin costo adicional para los suscriptores existentes que permite experimentar con el servicio antes de registrarse para obtener recursos dedicados. 
- El plan **Básico (vista preliminar)** proporciona recursos de proceso dedicados para cargas de trabajo de producción a escala más pequeña. Este nivel está actualmente en versión preliminar y se ofrece una [tarifa reducida del 50 % durante este período](https://azure.microsoft.com/pricing/details/search/).
- El plan **Estándar** se ejecuta en máquinas dedicadas, con más almacenamiento y capacidad de procesamiento en cada nivel, incluida la configuración mínima. El plan Estándar se incluye en dos niveles (S1 y S2). 

Todos los niveles se pueden [aprovisionar en el portal](search-create-service-portal.md), a excepción de S2, que requiere una incidencia de soporte técnico. Póngase en contacto con el servicio de soporte técnico o con azuresearch_contact@microsoft.com para empezar a trabajar con S2.

## Límites de los niveles

[AZURE.INCLUDE [azure-search-limits](../../includes/azure-search-limits-tier.md)]

## Límites de almacenamiento ##

[AZURE.INCLUDE [azure-search-limits](../../includes/azure-search-limits-storage.md)]

## Límites de datos ##

Recurso|Gratuito|Básico (versión preliminar) |S1|S2
---|---|---|---|----
Índice: campos máximos por índice|1000|100 <sup>1</sup>|1000|1000
Índice: perfiles de puntuación máximos por índice|16|16|16|16
Índice: funciones máximas por perfil|8|8|8|8
Tamaño de documento por API de índice<sup>2</sup> en MB|<16|<16|<16|<16
Indexadores: carga máxima de indexación por invocación|10 000 documentos|Limitado solamente por el número máximo de documentos|Limitado solamente por el número máximo de documentos|Limitado solamente por el número máximo de documentos
Indexadores: tiempo de ejecución máximo|3 minutos|24 horas|24 horas|24 horas
Indexador de blobs: tamaño máximo de blob, MB|16|16|128|256
Indexador de blobs: número máximo de caracteres del contenido extraído de un blob|32 000|64 000|4 millones|4 millones

<sup>1</sup> El nivel básico es el único nivel con un límite inferior de 100 campos por índice.

<sup>2</sup> Tamaño máximo del documento cuando se llama a una API de índice. El tamaño del documento es de hecho un límite en el tamaño del cuerpo de la solicitud de la API de índice. Dado que se puede pasar un lote de varios documentos a la API de índice de una vez, el límite de tamaño depende de cuántos documentos estén en el lote. Para un lote con un único documento, el tamaño máximo del documento será de 16 MB de JSON.

Para mantener el tamaño del documento reducido, no olvide excluir los datos no consultables de la solicitud. Las imágenes y otros datos binarios no son consultables directamente y no se deben almacenar en el índice. Para integrar los datos no consultables en los resultados de búsqueda, defina un campo que admita búsqueda que almacene una referencia de dirección URL al recurso.

## Límites de carga de trabajo (consultas por segundo) ##

Recurso|Gratuito|Básico (versión preliminar) |S1|S2
---|---|---|---|----
QPS|N/D|~ 3 por réplica|~ 15 por réplica|~ 60 por réplica

Las consultas por segundo (QPS) son una aproximación basada en heurística, mediante cargas de trabajo de clientes reales y simuladas para obtener valores estimados. El rendimiento QPS exacto dependerá de los datos y de la naturaleza de la consulta.

Aunque se proporcionan cálculos aproximados, las consultas reales por segundo (QPS) son difíciles de determinar, en especial en el servicio compartido gratuito donde el rendimiento se basa en el ancho de banda disponible y en la competición por los recursos del sistema. Los recursos de proceso y almacenamiento que respaldan el servicio compartido se comparten entre varios suscriptores, de modo que el número de QPS de su solución siempre variará según cuántas otras cargas de trabajo se estén ejecutando al mismo tiempo

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

<!---HONumber=AcomDC_0601_2016-->