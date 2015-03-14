<properties 
	pageTitle="Uso de los perfiles de puntuación en la Búsqueda de Azure" 
	description="Introducción a los perfiles de puntuación en la Búsqueda de Azure" 
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
	ms.date="02/10/2015" 
	ms.author="heidist"/>

# Introducción a los perfiles de puntuación en la Búsqueda de Azure

Los perfiles de puntuación son una característica de la Búsqueda de Microsoft Azure que personalizan el cálculo de las puntuaciones de búsqueda, que influyen en cómo se clasifican los elementos en una lista de resultados de búsqueda. Puede pensar en los perfiles de puntuación como en una forma de modelar la relevancia, impulsando los elementos que cumplen los criterios predefinidos. Por ejemplo, suponga que la aplicación es un sitio para reservas de hoteles en línea. Al impulsar el campo "ubicación", las búsquedas que incluyen un término como Madrid darán como resultado las puntuaciones más altas para los elementos que tienen Madrid en el campo "ubicación". Tenga en cuenta que puede tener más de un perfil de puntuación, o ninguno, si la puntuación predeterminada es suficiente para la aplicación.

Para ayudarle a experimentar con perfiles de puntuación, puede descargar una aplicación de ejemplo que utiliza perfiles de puntuación para cambiar el orden de clasificación de los resultados de búsqueda. El ejemplo es una aplicación de consola, quizás no muy realista para el desarrollo de aplicaciones reales, pero útil como una herramienta de aprendizaje. 

La aplicación de ejemplo demuestra los comportamientos de puntuación con la utilización de datos ficticios, lo que se denomina como "musicstoreindex". La sencillez de la aplicación de ejemplo facilita la modificación de perfiles de puntuación y consultas para ver a continuación los efectos inmediatos en el orden de clasificación cuando se ejecuta el programa.

<h2 id="sub-1">Requisitos previos</h2>

La aplicación de ejemplo está escrita en C# con Visual Studio 2013. Pruebe la edición gratuita de [Visual Studio 2013 Express](http://www.visualstudio.com/es-es/products/visual-studio-express-vs.aspx) si aún no tiene una copia de Visual Studio.

Necesitará una suscripción de Azure y un servicio de Búsqueda de Azure para completar el tutorial. Consulte [Introducción a Búsqueda de Azure](../search-get-started/) para obtener ayuda con la configuración del servicio.

[WACOM.INCLUDE [Para completar este tutorial, deberá tener una cuenta de Azure:](../includes/free-trial-note.md)]

<h2 id="sub-2">Descarga de la aplicación de ejemplo</h2>
Vaya a [Demostración de perfiles de puntuación de la Búsqueda de Azure](https://azuresearchscoringprofiles.codeplex.com/) de Codeplex para descargar la aplicación de ejemplo descrita en este tutorial.

En la pestaña Código de origen, haga clic en **Descargar** para obtener un archivo zip de la solución. 

 ![][12]

<h2 id="sub-3">Edición de app.config</h2>

1. Después de extraer los archivos, abra la solución en Visual Studio para editar el archivo de configuración.
1. En el Explorador de soluciones, haga doble clic en **app.config**. Este archivo especifica el extremo de servicio y una "clave de API" usada para autenticar la solicitud. Puede obtener estos valores en el Portal de administración.
1. Inicie sesión en el [Portal de vista previa de Azure](https://portal.azure.com).
1. Vaya al panel de servicio de Búsqueda de Azure.
1. Haga clic en el icono **Propiedades** para copiar la dirección URL del servicio.
1. Haga clic en el icono **Claves** para copiar la "clave de API".

Cuando haya terminado de agregar la dirección URL y la "clave de API" a app.config, la configuración de la aplicación debe tener este aspecto:

   ![][11]


<h2 id="sub-4">Exploración de la aplicación</h2>

Está casi listo para compilar y ejecutar la aplicación, pero antes de hacerlo, examine los archivos JSON utilizados para crear y rellenar el índice.

**Schema.json** define el índice, incluidos los perfiles de puntuación que se destacan en esta demostración. Observe que el esquema define todos los campos utilizados en el índice, incluidos los campos que no se pueden buscar, como "margen", que puede usar en un perfil de puntuación. La sintaxis del perfil de puntuación se documenta en [Incorporación de un perfil de puntuación a un índice de Búsqueda de Azure](http://msdn.microsoft.com/library/azure/dn798928.aspx).

**Data1-3.json** proporciona los datos, 246 álbumes de un conjunto de géneros. Los datos son una combinación del álbum real y la información del intérprete, con campos ficticios como "precio" y "margen" usados para ilustrar las operaciones de búsqueda. Los archivos de datos se ajustan al índice y se cargan en el servicio de Búsqueda de Azure. Después de que los datos se cargan e indizan, puede realizar consultas de dichos datos.

**Program.cs** realiza las siguientes operaciones:

- Abre una ventana de consola.

- Se conecta a la Búsqueda de Azure con la dirección URL y la "clave de API" del servicio.

- Elimina el elemento "musicstoreindex" si existe.

- Crea un campo nuevo "musicstoreindex" con el archivo schema.json.

- Rellena el índice usando los archivos de datos.

- Consulta el índice con cuatro consultas. Observe que los perfiles de puntuación se especifican como un parámetro de consulta. Todas las consultas buscan el mismo término, "mejor". La primera consulta muestra la puntuación predeterminada. Las tres consultas restantes utilizan un perfil de puntuación.

<h2 id="sub-5">Compilación y ejecución de la aplicación</h2>

Para descartar problemas de referencia de ensamblado o conectividad, compile y ejecute la aplicación para asegurarse de que no existen problemas para trabajar por primera vez. Debería ver una aplicación de consola abierta en segundo plano. Las cuatro consultas se ejecutan en secuencia sin detenerse. En muchos sistemas, todo el programa se ejecuta en menos de 15 segundos. Si la aplicación de consola incluye un mensaje que indica "Completado. Presione Entrar para continuar", significa que el programa ha finalizado correctamente. 

Para comparar las ejecuciones de las consultas, puede marcar, copiar y pegar los resultados de las consultas desde la consola y pegarlos en un archivo Excel. 

En la ilustración siguiente se muestran los resultados de las primera tres consultas en paralelo. Todas las consultas usan el mismo término de búsqueda, "mejor", que aparece en muchos títulos de álbumes.

   ![][10]

La primera consulta usa la puntuación predeterminada. Habida cuenta de que el término de búsqueda aparece solo en títulos de álbumes y no se especifica ningún otro criterio, se devuelven artículos que tengan "mejor" en el título del álbum en el orden en que los encuentra el servicio de búsqueda. 

La segunda consulta utiliza un perfil de puntuación, pero tenga en cuenta que el perfil no tiene ningún efecto. Los resultados son idénticos a los de la primera consulta. Esto es porque el perfil de puntuación impulsa un campo ("género") que no es relevante para la consulta. El término de búsqueda "mejor" no existe en ningún campo "género" de ningún documento. Cuando un perfil de puntuación no tiene ningún efecto, los resultados son los mismos que los de la puntuación predeterminada.  

La tercera consulta es la primera evidencia del impacto del perfil de puntuación. El término de búsqueda sigue siendo "mejor", por lo que estamos trabajando con el mismo conjunto de álbumes pero, como el perfil de puntuación proporciona criterios adicionales que impulsan los criterios "puntuación" y "fecha de la última actualización", algunos elementos se colocan en la parte superior de la lista.

En la ilustración siguiente se muestra la cuarta y última consulta, impulsadas por el criterio "margen". El campo "margen" no permite búsquedas y no se pueden devolver resultados de búsqueda. El valor "margen" se ha agregado manualmente a la hoja de cálculo para ayudar a ilustrar el punto de que los elementos con mayores márgenes aparecen más arriba en la lista de resultados de búsqueda. 

   ![][9]

Ahora que experimentó con perfiles de puntuación, pruebe a cambiar el programa para que use sintaxis de consulta diferentes, perfiles de puntuación o datos enriquecidos. Los vínculos de la sección siguiente proporcionan más información.

<h2 id="next-steps">Pasos siguientes</h2>

Obtenga más información sobre los perfiles de puntuación. Consulte [Incorporación de un perfil de puntuación a un índice de Búsqueda de Azure](http://msdn.microsoft.com/library/azure/dn798928.aspx) para obtener información detallada.

Obtenga más información sobre la sintaxis de búsqueda y los parámetros de consulta. Consulte [Búsqueda en documentos (API de REST de Búsqueda de Azure)](http://msdn.microsoft.com/library/azure/dn798927.aspx) para obtener información detallada.

¿Necesita volver atrás para obtener información sobre la creación de índices? [Vea este vídeo](http://channel9.msdn.com/Shows/Cloud+Cover/Cloud-Cover-152-Azure-Search-with-Liam-Cavanagh) para comprender los conceptos básicos.

<!--Anchors-->
[Requisitos previos]: #sub-1
[Descarga de la aplicación de ejemplo]: #sub-2
[Edición de app.config]: #sub-3
[Exploración de la aplicación]: #sub-4
[Compilación y ejecución de la aplicación]: #sub-5
[Pasos siguientes]: #next-steps

<!--Image references-->
[12]: ./media/search-get-started-scoring-profiles/AzureSearch_CodeplexDownload.PNG
[11]: ./media/search-get-started-scoring-profiles/AzureSearch_Scoring_AppConfig.PNG
[10]: ./media/search-get-started-scoring-profiles/AzureSearch_XLSX1.PNG
[9]: ./media/search-get-started-scoring-profiles/AzureSearch_XLSX2.PNG

<!--HONumber=46--> 
