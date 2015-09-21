<properties
	pageTitle="Qué es Búsqueda de Azure"
	description="Información técnica y resumen de características de Búsqueda de Azure."
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
	ms.date="07/08/2015"
	ms.author="heidist"/>

# ¿Qué es Búsqueda de Azure?

El servicio Búsqueda de Azure es un servicio en la nube totalmente administrado que permite a los desarrolladores crear eficaces aplicaciones de búsqueda mediante un SDK de .NET o las API de REST. Incluye búsquedas de texto completo que abarcan todo el contenido, además de comportamientos de búsqueda avanzada similares a los que presentan los motores de búsqueda web comerciales, por ejemplo, sugerencias de escritura anticipada de consultas, basadas en la entrada parcial de una palabra, resaltado de los aciertos encontrados y navegación por facetas. La compatibilidad con el lenguaje natural está integrada y se utilizan las reglas lingüísticas que corresponden al idioma especificado.

Es posible escalar el servicio en función de las necesidades de aumento de la capacidad de búsqueda o de almacenamiento. Por ejemplo, los minoristas pueden aumentar la capacidad para satisfacer los volúmenes adicionales que acompañan a los eventos de promoción o de compras de temporada.

El servicio Búsqueda de Azure, basado en API, está destinado a desarrolladores e integradores de sistemas que saben trabajar con servicios web y HTTP. El servicio Búsqueda de Azure elimina la complejidad de administrar un servicio de búsqueda en la nube y simplifica la creación de aplicaciones web y móviles basadas en búsquedas.

##Cómo funciona

Búsqueda de Azure es un [servicio PaaS](https://wikipedia.org/wiki/Platform_as_a_service) que delega la administración de los servidores y la infraestructura a Microsoft, pero deja un servicio listo para usar que se rellena con los datos de búsqueda y al que se tiene acceso posteriormente desde la aplicación. Dependiendo de cómo se configure, se usará el servicio gratuito que se comparte con otros suscriptores de Búsqueda de Azure o se aplicará el nivel de precios Estándar que ofrece recursos dedicados en exclusividad. La búsqueda estándar es escalable, con opciones que satisfacen el aumento de la demanda de almacenamiento o de cargas de consultas.

Búsqueda de Azure almacena los datos en un índice donde se pueden llevar a cabo consultas de texto completo. Es posible crear el esquema de estos índices en el Portal de Azure o mediante programación, utilizando las API de REST o la biblioteca de cliente. Una vez que se ha definido el esquema, se pueden cargar los datos en el servicio Búsqueda de Azure, donde se indizan seguidamente.

Se pueden emplear modelos de inserción o de extracción para la carga de los datos en el índice. El modelo de extracción se proporciona a través de indizadores que se pueden configurar para actualizaciones bajo demanda o programadas (consulte [Operaciones del indizador (API de REST del servicio Búsqueda de Azure)](https://msdn.microsoft.com/library/azure/dn946891.aspx)), lo que permite introducir fácilmente datos y cambios de datos de DocumentDB de Azure, Base de datos SQL de Azure o SQL Server hospedado en una máquina virtual de Azure. El modelo de inserción se proporciona a través del SDK o las API de REST usadas para enviar documentos actualizados a un índice. Se puede insertar datos desde cualquier conjunto de datos prácticamente, siempre y cuando tengan formato JSON. Consulte [Adición, actualización o eliminación de documentos](https://msdn.microsoft.com/library/azure/dn798930.aspx) o [Uso del SDK de .NET)](search-howto-dotnet-sdk.md) para obtener instrucciones sobre la carga de datos.

Algunos desarrolladores elegirán un indizador por la comodidad que proporciona. Para otros, vale la pena hacer un poco más de trabajo y emplear el modelo de inserción. Los motivos para elegir el modelo de inserción son dos. Primero, se evita la carga adicional que conllevan los mecanismos estilo rastreador en los servidores de datos. En segundo lugar, se evita la latencia inherente a la indización programada. En días de campañas de ventas, por ejemplo, se necesitan búsquedas que reflejen al segundo el estado de disponibilidad en el inventario. El modelo de inserción ofrece este grado de precisión.

##Qué se puede crear y almacenar

Un flujo de trabajo típico consiste en generar el esquema y los documentos en un entorno de desarrollo local y, a continuación, usar el SDK de .NET o la API de REST para cargar, procesar y, en última instancia, consultar los datos. Todos los datos indizados se conservan en el servicio Búsqueda de Azure para mejorar el rendimiento y garantizar la coherencia entre operaciones de búsqueda.

Se puede utilizar el editor integrado en el portal para crear el esquema de índice y los perfiles de puntuación, lo que es ideal para la creación de prototipos. Los desarrolladores que necesiten un enfoque automatizado y repetible tal vez prefieran crear el índice en el código. Primero se agregan las características más recientes a la API, de forma que según los requisitos de la aplicación, un enfoque de programación puede ser la única opción.

Cuando se genera el esquema, se definen los campos y sus atributos admitidos en la aplicación de búsqueda. Los campos contienen datos en los que se puede buscar, por ejemplo, nombres de productos, descripciones, comentarios de los clientes, marcas, precios, anuncios promocionales, etcétera. Los atributos informan sobre el tipo de operaciones que pueden llevarse a cabo. Entre los atributos que más se utilizan se encuentra si un campo admite búsqueda de texto completo (searchable=true), filtros (filterable=true) o facetas (facetable=true)

Los campos también contienen datos que no admiten búsquedas relevantes para la aplicación de búsqueda, por ejemplo, valores que se usan internamente en filtros y perfiles de puntuación, o quizá direcciones URL que dirigen a contenido almacenado en otras plataformas de almacenamiento (por ejemplo, archivos de imágenes o vídeos guardados en almacenamiento de blobs). Un ejemplo citado a menudo como campo solo para uso interno es un margen de beneficio o cualquier otro valor que indique el potencial de ingresos. Tal vez la aplicación de búsqueda debe promover aquellos elementos específicos que aportan más beneficio financiero a la compañía. El esquema puede incluir atributos de campo que permitan estos tipos de comportamientos de búsqueda.

Los documentos son los datos detallados que devuelve el motor de búsqueda en los resultados de búsqueda. Por ejemplo, si la aplicación de búsqueda es un catálogo en línea, habrá un documento por cada SKU y la página de los resultados de búsqueda se creará utilizando los valores de los documentos devueltos que coinciden con los términos de búsqueda.

##Véalo en acción

Vea nuestros vídeos para conocer distintos escenarios y capacidades. Visite [Búsqueda de Azure: ejemplos, tutoriales y demostraciones en vídeo](search-video-demo-tutorial-list.md) para obtener vínculos a contenido de vídeo.

##Exploración en profundidad de las características

El servicio Búsqueda de Azure ofrece valor añadido en varias categorías, como aprovisionamiento y escala, programación, acceso y control, y en las características que se elijan implementar en la aplicación de búsqueda personalizada.

La siguiente lista de comprobación de características ayuda a evaluar Búsqueda de Azure según las necesidades de una aplicación de búsqueda. Encontrará anuncios sobre nuevas características en Actualizaciones recientes de Búsqueda de Azure. También puede consultar la página de solicitud de características de Búsqueda de Azure para comprobar el estado de aquellas características que aún no se han implementado.

¿Alguna pregunta acerca de una característica específica? Acuda al [foro de Búsqueda de Azure en MSDN](https://social.msdn.microsoft.com/forums/azure/home?forum=azuresearch). También puede revisar la [página de solicitud de características de Búsqueda de Azure](http://feedback.azure.com/forums/263029-azure-search) para comprobar el estado de las características que aún no se han implementado.

###Características de capacidad y escala, y restricciones

El servicio puede ejecutarse como una implementación estándar o compartida. La búsqueda estándar admite recursos dedicados que se pueden escalar en función de las cargas de trabajo. El servicio compartido es gratuito y está pensado para probar la funcionalidad de los servicios, ya que no hay ninguna garantía de rendimiento.

Es **escalable** en incrementos de almacenamiento y recuentos de documentos (particiones) o en la carga de consultas (réplicas). Cada réplica ejecuta una copia de un índice. Una alta disponibilidad requiere 2 réplicas para cargas de trabajo de consulta y 3 réplicas para cargas de trabajo de lectura y escritura (consulta e indización). Para obtener más información acerca de cómo planear la capacidad, consulte Límites y restricciones (Búsqueda de Azure).

El servicio Búsqueda de Azure extiende automáticamente los índices y los documentos entre las particiones que se han asignado a este servicio. Esto significa que no se pueden vincular índices y documentos a un conjunto de particiones y réplicas.

Las particiones y las réplicas son recursos de todo el servicio, y todos los índices se ejecutan en todas las réplicas. Si se necesita aislamiento de índice o si existen requisitos de dispersión geográfica de servicios y recursos en distintos centros de datos, se puede crear un segundo servicio.

Hay límites en el almacenamiento y el número de índices y documentos que se cargan en el servicio. El límite real será lo que suceda primero: se agota el almacenamiento físico o se alcanza el límite máximo de los recuentos de documentos e índices. Vea [Límites y restricciones (Búsqueda de Azure)](search-limits-quotas-capacity.md) para obtener detalles.

###Capacidad de programación

La API de REST se compone de solicitudes y respuestas HTTP con contenido en formato JSON. Hay una API para el acceso al servicio y otra para administrarlo. Consulte [API de REST del servicio Búsqueda de Azure](https://msdn.microsoft.com/library/azure/dn798935.aspx) y [API de REST de administración de Búsqueda de Azure](https://msdn.microsoft.com/library/azure/dn832684.aspx) para obtener detalles.

El SDK de .NET incluye clases que facilitan el uso del servicio Búsqueda de Azure sin necesidad de trabajar directamente con HTTP y JSON. Para obtener más información, consulte [Uso del SDK de .NET de Búsqueda de Azure](search-howto-dotnet-sdk.md).

###Acceso y control

La búsqueda solo es accesible a través de HTTPS.

La autenticación desde la aplicación host en Búsqueda de Azure se realiza mediante una clave de API de administración en un encabezado HTTP. No hay ningún modelo de autenticación o autorización por usuario. Sin embargo, se puede usar $filter para restringir el acceso por identidad de usuario. También se pueden usar varias claves de API de consulta que se asignan a distintas aplicaciones de cliente. Consulte [Administración del servicio de búsqueda en Microsoft Azure](search-manage.md) para obtener más información sobre administración de claves. Consulte [Búsqueda de documentos (API de REST del servicio Búsqueda de Azure)](https://msdn.microsoft.com/library/azure/dn798927.aspx) para obtener más información sobre $filter.

###Índices y documentos

Es posible tener varios índices (vea [Límites y restricciones (Búsqueda de Azure)](search-limits-quotas-capacity.md) para obtener información sobre los límites basados en niveles de precios). Tenga en cuenta que ya no se ofrece compatibilidad para la combinación de índices. Una solicitud de búsqueda puede especificar un índice.

Los **documentos** contienen campos y atributos asociados. Los campos incluyen texto donde se puede buscar, valores que se utilizan principalmente (o incluso exclusivamente) en filtros y perfiles de puntuación, y muy probablemente: direcciones URL o punteros a contenido, por ejemplo, imágenes, de otros almacenamientos de datos. Muchas aplicaciones de búsqueda usan varias formas de almacenamiento. Se pueden almacenar imágenes o vídeos de manera más económica en otros medios de almacenamiento, como el Almacenamiento de blobs de Azure.

Se pueden usar los **indizadores** para programar actualizaciones del índice de datos modificados en Base de datos SQL de Azure, SQL Server en máquinas virtuales de Azure o DocumentDB de Azure. Consulte [operaciones del indizador (API de REST del servicio Búsqueda de Azure)]((https://msdn.microsoft.com/library/azure/dn946891.aspx) para obtener más información.

Se pueden formular **consultas** utilizando la sintaxis de OData para filtros booleanos y la sintaxis de consulta simple para la búsqueda de texto completo. Consulte [Sintaxis de expresiones de OData para Búsqueda de Azure](https://msdn.microsoft.com/library/azure/dn798921.aspx) y [Sintaxis de consulta simple en Búsqueda de Azure](https://msdn.microsoft.com/library/azure/dn798920.aspx) para obtener más información.

Se pueden buscar documentos específicos basándose en su identificador y recuperar rápidamente elementos específicos. Esto resulta útil cuando un usuario hace clic en un resultado concreto y se quieren buscar detalles específicos acerca de ese documento. Consulte [Búsqueda de documento (API de REST del servicio Búsqueda de Azure)](https://msdn.microsoft.com/library/azure/dn798929.aspx) para obtener más información.

##Características de las aplicaciones de búsqueda

La **búsqueda de texto completo** (habilitada de forma predeterminada para campos de texto) está habilitada para cualquier campo con el atributo de que se puede buscar. La búsqueda de texto completo se basa en la coincidencia de los prefijos, es decir, las coincidencias se basan en la primera parte de un término de búsqueda, no en el medio ni en el final de la palabra. Consulte [Creación de índice (API de REST de servicio de Búsqueda de Azure)](https://msdn.microsoft.com/library/azure/dn798941.aspx) o [Creación de un índice de Búsqueda de Azure en el portal](search-create-index-portal.md) para conocer los pasos para definir un índice.

Los **perfiles de puntuación** se utilizan para generar modelos de clasificación que optimizan la búsqueda basada en objetivos de negocio. Por ejemplo, tal vez se desea que los productos más recientes o con descuento aparezcan arriba en los resultados de búsqueda. También se pueden crear perfiles de puntuación mediante etiquetas para puntuaciones personalizadas, según las preferencias de búsqueda de los clientes de las que se ha hecho seguimiento y se han almacenado por separado. Consulte [Adición de perfiles de puntuación a un índice de búsqueda (API de REST de servicio Búsqueda de Azure)](https://msdn.microsoft.com/library/azure/dn798928.aspx) para obtener más información.

La **compatibilidad de idioma** está integrada para 50 idiomas, con varias opciones de pilas de procesamiento de lenguaje natural, incluidos los conocidos analizadores de Lucene y los analizadores propios de Microsoft que alimentan Microsoft Office y Bing (solo en la versión preview). Consulte [Compatibilidad de idioma (API de REST de servicio de Búsqueda de Azure)](https://msdn.microsoft.com/library/azure/dn879793.aspx) para Lucene y [API de REST del servicio Búsqueda de Azure versión 2015-02-28-Preview](search-api-2015-02-28-Preview.md) para los procesadores de lenguaje natural.

El término **navegación por facetas** hace referencia a un árbol de clasificación utilizado para la búsqueda autodirigida, a menudo basada en marcas, modelos, tamaños o cualquier categoría que tenga sentido para los datos. La navegación por facetas se implementa mediante atributos en el índice, junto con un campo de facetas que se proporciona en una consulta. Consulte [Navegación por facetas](search-faceted-navigation.md) para obtener más información.

Las **sugerencias** para escritura anticipada o autocompletar consultas se admiten a través de atributos en el índice. El servicio Búsqueda de Azure admite tanto las coincidencias aproximadas como de infijo (coincidencia con cualquier parte del contenido del campo). Se pueden hacer ambas, no son mutuamente excluyentes. Consulte [Creación de índice (API de REST del servicio Búsqueda de Azure)](https://msdn.microsoft.com/library/azure/dn798941.aspx) para obtener información acerca de cómo habilitar las sugerencias y [Sugerencias (API de REST de servicio de Búsqueda de Azure)](https://msdn.microsoft.com/library/azure/dn798936.aspx) para su uso.

Se pueden usar los **filtros** para implementar la navegación por facetas, usada en la construcción de las consultas, o globalmente para restringir las operaciones de búsqueda y filtrar según los criterios que se establecen en el código. Los filtros están habilitados para campos específicos mediante el esquema de índice y a través del parámetro de búsqueda $Filter. Consulte [Creación de índice (API de REST de servicio de Búsqueda de Azure)](https://msdn.microsoft.com/library/azure/dn798941.aspx) y [Búsqueda de documentos (API de REST del servicio de Búsqueda de Azure)](https://msdn.microsoft.com/library/azure/dn798927.aspx) para obtener más información.

También la función **Ordenar** está habilitada para campos específicos a través del esquema de índice e implementada después como un parámetro de búsqueda a través del parámetro $orderBy. Una vez más, los detalles se encuentran en [Creación de índice (API de REST del servicio Búsqueda de Azure)](https://msdn.microsoft.com/library/azure/dn798941.aspx) y [Búsqueda de documentos (API de REST del servicio Búsqueda de Azure)](https://msdn.microsoft.com/library/azure/dn798927.aspx).

El **recuento** de las coincidencias de búsqueda devueltas en una consulta y la capacidad de limitar la cantidad de coincidencias que se devuelven de una vez se implementan a través de $top y $skip. Consulte [Búsqueda de documentos (API de REST del servicio Búsqueda de Azure)](https://msdn.microsoft.com/library/azure/dn798927.aspx) para obtener más información.

El **resaltado de los aciertos** se especifica mediante el parámetro de resaltado de consulta y permite mostrar un fragmento de texto con el resaltado de las palabras clave encontradas a partir de los términos de búsqueda especificados por el usuario. Consulte [Búsqueda de documentos (API de REST del servicio Búsqueda de Azure)](https://msdn.microsoft.com/library/azure/dn798927.aspx) para obtener más información acerca de los parámetros de consulta.

##Informes y análisis

El uso de los recursos se muestra en el panel del servicio, de forma que se puede obtener rápidamente una idea de cómo se utiliza el almacenamiento.

El número de índices, el recuento de documentos y el consumo de almacenamiento están disponibles en el portal. También se puede utilizar la API. Consulte [Obtención de estadísticas de índice (API de REST del servicio Búsqueda de Azure)](https://msdn.microsoft.com/library/azure/dn798942.aspx) para obtener más información.

No hay ningún mecanismo integrado para medir el rendimiento de las consultas u otras operaciones del servicio. Además, actualmente no hay ninguna compatibilidad para el registro o el análisis de los resultados de la búsqueda (por ejemplo, recuperar una lista de términos de búsqueda que no produjo ningún resultado o informar sobre el origen de las solicitudes de búsqueda).

##Prueba

Visite [Creación de un servicio de Búsqueda de Azure](search-create-service-portal.md) para configurar el servicio o use [Probar el Servicio de aplicaciones de Azure](http://go.microsoft.com/fwlink/p/?LinkId=618214) para obtener una sesión de una hora gratis sin necesidad de instalación ni suscripción.

También puede consultar estos tutoriales:

[Uso de Búsqueda de Azure en .NET](search-howto-dotnet-sdk.md) [Introducción a Búsqueda de Azure](search-get-started-dotnet.md) [Búsqueda de Azure: ejemplos, tutoriales y demostraciones en vídeo](search-video-demo-tutorial-list.md)

<!---HONumber=Sept15_HO2-->