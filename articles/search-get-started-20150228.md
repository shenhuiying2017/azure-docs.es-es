<properties 
	pageTitle="Crear la primera aplicación de Búsqueda de Azure en .NET" 
	description="Tutorial para crear una solución que usa la biblioteca de cliente de .NET con el SDK de .NET para Búsqueda de Azure." 
	services="search" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="mblythe" 
	editor=""/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="03/05/2015" 
	ms.author="heidist"/>

#Crear la primera aplicación de Búsqueda de Azure en .NET#

En este tutorial se crea una aplicación de búsqueda web personalizada en Visual Studio 2013 o posterior que usa Búsqueda de Azure para la experiencia de búsqueda. En el tutorial se usa el [SDK de .NET para Búsqueda de Azure](https://msdn.microsoft.com/library/azure/dn951165.aspx) para crear las clases de los objetos y las operaciones que se usan en el ejemplo.

Puede descargar el ejemplo de codeplex en la [demostración de Búsqueda de Azure con datos de USGS](https://azsearchdemos.codeplex.com/SourceControl/latest) para seguir los pasos de este tutorial. La aplicación de ejemplo usa los datos del [Servicio geológico de Estados Unidos \(USGS\)](http://geonames.usgs.gov/domestic/download_data.htm), filtrados por el estado de Washington. Vamos a usar estos datos para crear una aplicación de búsqueda basada en los datos sobre edificios de referencia, como hospitales y escuelas, además de características geológicas como ríos, lagos y montes.

Para ejecutar este ejemplo, debe tener un servicio Búsqueda de Azure, para el que puede registrarse en el [portal de Azure](https://portal.azure.com).

Puede empezar con [Crear un servicio en el portal](../search-create-service-portal/) si necesita ayuda para aprovisionar y comprobar la disponibilidad del servicio. En el artículo también se explica cómo encontrar el nombre del servicio y las claves de administración que se usan en cada tutorial y solución que incluyen Búsqueda de Azure.

> [AZURE.TIP]Se recomienda actualizar los paquetes de NuGet antes de compilar los proyectos para evitar errores de compilación. Haga clic con el botón secundario en la solución y elija **Administrar paquetes de NuGet**. Actualice todos los paquetes que se usan en esta solución.

##Crear el índice##

1. Copie el nombre del servicio y la clave de administración desde el [portal de Azure](https://portal.azure.com) y péguelos en **DataIndexer** \| **App.config**.
1. Haga clic con el botón secundario en el proyecto **DataIndexer** para establecerlo como proyecto de inicio.
1. Compile y ejecute el proyecto.

Verá una ventana de consola con estos mensajes.

![][1]

En el portal, verá un nuevo índice de características con xx y xx. Como el portal puede tardar varios minutos en ponerse al día, espere unos minutos antes de actualizar la pantalla para ver los resultados.

![][2]

##Compilar la aplicación##


1. Copie el nombre del servicio y la clave de administración desde el [portal de Azure](https://portal.azure.com) y péguelos en **SimpleSearchMVCApp** \| **Web.config**.
1. Haga clic con el botón secundario en el proyecto **SimpleSearchMVCApp** para establecerlo como proyecto de inicio.
1. Compile y ejecute el proyecto.

En el explorador predeterminado, verá una página web que proporciona un cuadro de búsqueda para acceder a los datos de USGS almacenados en el índice en el servicio Búsqueda de Azure.

![][3]

##Buscar en los datos de USGS##

El conjunto de datos de USGS incluye registros que son relevantes para el estado de Washington. Si hace clic en **Search** en un cuadro de búsqueda vacío, obtendrá las 50 primeras entradas \(es el valor predeterminado\).

Escriba un término de búsqueda para que el motor de búsqueda tenga con qué trabajar. Pruebe a escribir un nombre regional. "Snoqualmie" es una ciudad de Washington. También es el nombre de un río, una cascada, un paso de montaña y parques del estado.

![][4]

También puede probar con alguno de estos términos:

- Seattle
- Rainier
- Seattle y Rainier
- Seattle + Rainier + Mount \(obtiene resultados de los puntos de referencia en Rainier Avenue, o el club Rainier, todo dentro de los límites de la ciudad de Seattle\).

##Explorar el código##

Para obtener información sobre los conceptos básicos del SDK de .NET, eche un vistazo a [Cómo usar Búsqueda de Azure en .NET](../search-howto-dotnet-sdk/) para obtener una explicación de las clases más usadas habitualmente en la biblioteca de cliente.

En el resto de esta sección se tratan algunos puntos sobre cada proyecto. En su caso, se le remitirá a algunos enfoques alternativos que usan características más avanzadas.

**Proyecto DataIndexer**

Para simplificar las cosas, los datos se insertan dentro de la solución, en un archivo de texto generado a partir de los datos del [sitio web del Servicio geológico de Estados Unidos \(USGS\)](http://geonames.usgs.gov/domestic/download_data.htm).

Algunos métodos alternativos a la inserción de los datos son los [indizadores de DocumentDB](documentdb-search-indexer.md) o los [indizadores de Base de datos SQL de Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md). Los indizadores extraen los datos en el índice de Búsqueda de Azure, lo que puede simplificar mucho el código que hay que escribir y mantener.

También puede cargar los datos de una base de datos local de SQL Server. [En este tutorial](http://azure.microsoft.com/blog/2014/11/10/how-to-sync-sql-server-data-with-azure-search/) se muestra cómo hacerlo.

El programa **DataIndexer** incluye un método **SearchDocuments** para buscar y filtrar datos. Este método existe como un paso de comprobación y permite la salida de los mensajes de estado a la ventana de consola, concretamente los que muestran los resultados de búsqueda y los comportamientos de los filtros. Lo más probable es que no necesite un método como este en el código que escriba para crear y cargar un índice.

**Proyecto SimpleSearchMVCApp**

El proyecto de MVC usa una vista y un controlador para enrutar las entradas y salidas a la capa de presentación. **Index.cshtml** proporciona el código HTML que se usa para representar los resultados de búsqueda. Actualmente, es una tabla sencilla que organiza los datos del conjunto de datos. Aunque es útil para crear prototipos y realizar pruebas, puede mejorar fácilmente la presentación. Para obtener sugerencias acerca de cómo procesar resultados por lotes y poner contadores en una página, consulte [Paginar los resultados y paginación en Búsqueda de Azure](search-pagination-page-layout.md).

Las conexiones a Búsqueda de Azure y la ejecución de una solicitud de consulta se definen en el archivo **FeatureSearch.cs**.

Para concluir, si aún no está convencido del valor y la sencillez del SDK de .NET, compare los archivos de origen de este ejemplo con este basado en la API de REST: [Demostración de Búsqueda de Azure con Adventure Works](https://azuresearchadventureworksdemo.codeplex.com/). La versión del SDK de .NET que se describe en este tutorial es mucho más sencilla, con menos líneas de código.

##Pasos siguientes##

Este es el primer tutorial de Búsqueda de Azure basado en el conjunto de datos de USGS. Con el tiempo, ampliaremos este tutorial y crearemos otros nuevos para mostrar las características de búsqueda que podría interesarle incluir en sus soluciones personalizadas.

Si ya tiene conocimientos sobre Búsqueda de Azure, puede usar este ejemplo como punto de partida para probar proveedores de sugerencias \(escritura automática o autocompletar consultas\), filtros y navegación por facetas. También puede mejorar la página de resultados de búsqueda si agrega recuentos y procesamiento por lotes de documentos para que los usuarios puedan navegar por las páginas de resultados.

¿Es la primera vez que usa Búsqueda de Azure? Le recomendamos que pruebe otros tutoriales para comprender mejor lo que puede crear. Visite nuestra [página de documentación](http://azure.microsoft.com/documentation/services/search/) para encontrar más recursos. También puede ver los vínculos en nuestra [lista de vídeos y tutoriales](https://msdn.microsoft.com/library/azure/dn798933.aspx) para obtener acceso a más información.

<!--Image references-->
[1]: ./media/search-get-started-20150228/consolemessages.png
[2]: ./media/search-get-started-20150228/portalindexstatus.png
[3]: ./media/search-get-started-20150228/usgssearchbox.png
[4]: ./media/search-get-started-20150228/snoqualmie.png
<!--HONumber=54-->