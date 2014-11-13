<properties title="Creaci&oacute;n de la primera soluci&oacute;n de b&uacute;squeda con B&uacute;squeda de Azure" pageTitle="Creaci&oacute;n de la primera soluci&oacute;n de b&uacute;squeda con B&uacute;squeda de Azure" description="Creaci&oacute;n de la primera soluci&oacute;n de b&uacute;squeda con B&uacute;squeda de Azure" metaKeywords="" services="" solutions="" documentationCenter="" authors="Heidist" manager="mblythe" videoId="" scriptId="" />

<tags ms.service="azure-search" ms.devlang="" ms.workload="search" ms.topic="article" ms.tgt_pltfrm="" ms.date="09/23/2014" ms.author="heidist" />

# Creación de la primera solución de búsqueda con Búsqueda de Azure

-   [Requisitos previos][Requisitos previos]
-   [Creación de un índice de Búsqueda de Azure][Creación de un índice de Búsqueda de Azure]
-   [Exploración de CatalogIndexer][Exploración de CatalogIndexer]
-   [Creación de una aplicación de MVC4 mediante Búsqueda de Azure][Creación de una aplicación de MVC4 mediante Búsqueda de Azure]
-   [Exploración de AdventureWorksWeb][Exploración de AdventureWorksWeb]
-   [Pasos siguientes][Pasos siguientes]

## Información general

Este ejemplo demuestra una aplicación de búsqueda para la empresa de bicicletas Adventure Works. Después de realizar este tutorial, tendrá un catálogo de productos en línea con una experiencia de búsqueda enriquecida que incluye navegación por facetas, varias opciones de ordenación de los resultados de búsqueda y sugerencias de consulta anticipadas.

![][0]

Esta demostración le introduce en el uso de Búsqueda de Azure a través de estos ejercicios:

-   Creación de un índice de Búsqueda de Azure.
-   Carga de documentos (datos) en el índice de Búsqueda de Azure desde una base de datos SQL Server.
-   Creación de una aplicación de ASP.NET MVC4 que usa Búsqueda de Azure para:

    -   Buscar y mostrar los resultados desde un índice de Búsqueda de Azure
    -   Mostrar sugerencias anticipadas en un cuadro de búsqueda mientras escribe un término de búsqueda
    -   Filtrar los resultados de búsqueda mediante el uso de facetas

## Requisitos previos

-   Visual Studio 2012 o posterior con ASP.NET MVC 4 y SQL Server instalados. Puede obtener Visual Studio 2013 Express desde Internet en el [Centro de descarga][Centro de descarga].
-   Un servicio de Búsqueda de Azure. Necesitará el nombre del servicio de búsqueda y la clave de administrador. Consulte [Introducción a Búsqueda de Azure][Introducción a Búsqueda de Azure].
-   [Proyecto Adventure Works para la demostración de la función de Búsqueda de Azure en CodePlex][Proyecto Adventure Works para la demostración de la función de Búsqueda de Azure en CodePlex]. En la pestaña Origen, haga clic en **Descargar** para obtener un archivo zip de la solución.

    ![][1]

Esta solución contiene dos proyectos:

-   **CatalogIndex** crea un índice de Búsqueda de Azure y carga los datos desde una base de datos SQL Server incluida con el proyecto.
-   **AdventureWorksWeb** es una aplicación basada en MVC4 que realiza consultas al índice de Búsqueda de Azure. En este tutorial se asume que ya ha trabajado con ASP.NET MVC.

[WACOM.INCLUDE [Necesita una cuenta de Azure para completar este tutorial:](../includes/free-trial-note.md)]

## Creación de un índice de Búsqueda de Azure

En este paso, usará **CatalogIndex** para crear un nuevo índice de Búsqueda de Azure llamado "catalog" basado en los datos de una base de datos SQL Server de AdventureWorks.

1.  En Visual Studio, abra la solución de la demostración de Búsqueda de Azure denominada **AdventureWorksCatalog.sln**.
2.  Haga clic con el botón secundario en **CatalogIndexer** en el Explorador de soluciones y seleccione **Establecer como proyecto de inicio** para que al presionar **F5** se ejecute esta aplicación en lugar del proyecto **AdventureWorksWeb**.
3.  Abra **App.config** en este proyecto y actualice los valores de "SearchServiceName" y "SearchServiceApiKey" con los de su servicio de Búsqueda de Azure. En el nombre del servicio de búsqueda, si su servicio es "mysearch.search.windows.net", tendría que escribir "mysearch".
4.  Opcionalmente, App.config incluye una entrada para "SourceSqlConnectionString" que asume la base de datos LocalDB de SQL Server 2014 Express (Server=(LocalDB)\\v11.0). Si usa una edición diferente de SQL Server, actualice el nombre del servidor en consecuencia. Por ejemplo, si tiene una instancia predeterminada local, puede usar (local) o localhost.
5.  Guarde **App.config**.
6.  Presione **F5** para abrir el proyecto.

Se debe abrir un símbolo del sistema con el siguiente texto: "Creando índice..."

A los pocos minutos, se debe completar con el texto: "Completado. Presione <enter> para continuar:"

![][2]

Presione **Entrar** para cerrar la aplicación. En este punto, ha creado correctamente el índice de Búsqueda de Azure.

> [WACOM.NOTE] Si recibe errores como "valor no válido para la clave 'attachdbfilename'" o algún otro error relacionado con la base de datos, puede que se encuentre con conflictos de UAC. Para los fines de esta demostración, solucione estos errores de la siguiente manera:
> Copie la solución en una carpeta nueva o existente (por ejemplo, Temp) que proporcione acceso a los usuarios autenticados.
> Use **Ejecutar como administrador** para iniciar Visual Studio.
> Abra la solución y luego presione **F5** para crear el índice.

Para comprobar la creación del índice y la carga de documentos, vaya al panel del servicio de búsqueda en el [portal de vista previa de Azure][portal de vista previa de Azure]. En Utilización, el recuento de índices debe haber subido en uno, y debe tener 294 documentos, uno por cada producto de la base de datos.

Haga clic en el icono **Índices** para mostrar la lista de índices. La lista de índices se desliza para mostrar el nuevo recuento de índices y de documentos.

![][3]

## Exploración de CatalogIndexer

Vamos a examinar más de cerca el proyecto **CatalogIndexer** para entender cómo funciona.

1.  Abra **Program.cs** desde el Explorador de soluciones y vaya a la función `Main(string[] args)`.

    Observe cómo esta función crea un URI llamado `_serviceURI` basado en su servicio de Búsqueda de Azure específico y luego crea un HttpClient llamado `_httpClient` que usa la clave de API para autenticarse en este servicio de búsqueda.

2.  `ChangeEnumeratorSql` y `ChangeSet` se usan para enumerar los datos de la tabla Products en la base de datos AdventureWorks de SQL Server.

3.  En función de los datos recopilados de esta tabla, se llama entonces a `ApplyChanges` para aplicar estos datos al índice de Búsqueda de Azure.

4.  Vaya a `ApplyChanges` en el mismo archivo. Observe cómo esta función elimina el índice si ya existe (`DeleteCatalogIndex`) y crea luego uno nuevo llamado "catalog" (`CreateCatalogIndex`).

5.  Vaya a la función `CreateCatalogIndex`, y observe cómo se crea el índice con un esquema que coincide con las columnas de la tabla Products en SQL Server. Cada campo tiene un tipo (es decir, `Edm.String` o `Edm.Double`) así como atributos que definen para qué se usan estos campos. Consulte la [documentación de la API de REST de Búsqueda de Azure][documentación de la API de REST de Búsqueda de Azure] para obtener más información sobre estos atributos.

6.  Vuelva a la función `ApplyChanges`. Observe cómo esta función aplica un bucle a través de todos los datos de los cambios enumerados `ChangeSet`. En lugar de aplicar los cambios uno a uno, los procesa por lotes en grupos de 1000 y luego los aplica al servicio de búsqueda. Esta forma es mucho más eficiente que aplicar los documentos uno a uno.

Ahora que ha visto cómo crear y rellenar un índice mediante datos relacionales de SQL Server, vamos a examinar la forma de crear un catálogo de productos que use nuestros datos de búsqueda.

## Creación de una aplicación de MVC4 mediante Búsqueda de Azure

En este paso, creará la aplicación de búsqueda y la ejecutará en un explorador web.

1.  En Visual Studio, abra la solución de la demostración de Búsqueda de Azure denominada **AdventureWorksCatalog.sln**.

2.  Haga clic con el botón secundario en **AdventureWorksWeb** en el Explorador de soluciones y seleccione **Establecer como proyecto de inicio**.

3.  Abra **Web.config** en este proyecto y actualice los valores de "SearchServiceName" y "SearchServiceApiKey" con los de su servicio de Búsqueda de Azure. En el nombre del servicio de búsqueda, si su servicio es "mysearch.search.windows.net", tendría que escribir "mysearch".

4.  Guarde **Web.config**.

5.  Presione **F5** para abrir el proyecto. Siga estos pasos de [Solución de problemas][Solución de problemas] si recibe un error de compilación.

    ![][4]

6.  Deje el cuadro de búsqueda vacío y haga clic en **Buscar** para que se devuelvan los 294 productos.

7.  Observe la lista de facetas a lo largo del lado izquierdo. Intente hacer clic en una de estas facetas. La búsqueda se ejecuta de nuevo, pero esta vez agrega la faceta elegida para filtrar los resultados. Puede que desee agregar un punto de interrupción a la primera línea de la función **HomeController.cs** `Search` para recorrer (F11) cada línea.

8.  Escriba un término de búsqueda, como "mountain bikes". Mientras escribe, observe la lista desplegable de consultas sugeridas.

    ![][5]

Aparece de nuevo la captura de pantalla del inicio de esta demostración, como recordatorio de los pasos que se han cubierto. En las secciones anteriores, analizamos la navegación por facetas (a la izquierda), el recuento de documentos en la parte superior de la página, las sugerencias y las opciones de ordenación para ordenar por relevancia, lista o precio.

![][0]

## Exploración de AdventureWorksWeb

El proyecto AdventureWorksWeb nos muestra cómo se puede usar ASP.NET MVC 4 para interactuar con Búsqueda de Azure desde cualquier aplicación web. En esta sección, revisaremos elementos individuales del código de la aplicación para ver lo que hacen.

1.  En el Explorador de soluciones, expanda **AdventureWorksWeb** | **Controlador** y abra **HomeController.cs**

    Este es el controlador de MVC que gestiona la interacción desde la vista de índice. En la parte superior del controlador, observará una referencia a `CatalogSearch _catalogSearch` que crea un objeto de conexión HttpClient a su servicio de Búsqueda de Azure. El código de este objeto `CatalogSearch` está ubicado en **CatalogSeach.cs**

2.  En **HomeController.cs**, hay dos funciones principales:

    **Search**: cuando el usuario hace clic en el botón de búsqueda o elige una faceta, se llama a esta función para recuperar los resultados y reenviarlos a la vista de índice para visualizarlos.

    **Suggest**: mientras el usuario escribe en el cuadro de búsqueda, se llama a esta función para devolver una lista de sugerencias basada en el contenido del índice de Búsqueda de Azure.

Vamos a profundizar en estas dos funciones un poco más:

1.  En la función **HomeController.cs** `Search`, observará una llamada a `_catalogSearch.Search`, que incluye el objeto de conexión al servicio de Búsqueda de Azure. Cuando llama a la función de búsqueda ubicada en **CatalogSearch.cs**, puede ver que utiliza estos parámetros para elaborar una consulta de Búsqueda de Azure. Los resultados de la consulta se almacenan en un objeto JSON llamado `result` y luego se pasan a la vista `Index` donde se analizan y se muestran en la página web.

2.  Abra **Index.cshtml** en Vistas | Inicio; advertirá el código que se usa para analizar estos resultados.

3.  Detenga la aplicación si se está ejecutando, y abra el archivo **Index.cshtml** en Vistas | Inicio. Al final de este archivo, verá una función de JavaScript que usa `JQuery $(function ())`. Cuando la página se carga se llama a esta función. Se usa la función autocompletar de JQuery y se vincula esta función como una devolución de llamada desde el cuadro de texto de búsqueda, identificado como "q". Cada vez que alguien escribe en el cuadro de texto, se llama a esta función de sugerencia automática que, a su vez, llama a /home/suggest con lo que se escribe. `/home/suggest` es una referencia a la función en **HomeController.cs** llamada `Suggest`.

4.  Abra **HomeController.cs** y vaya a la función Suggest. Este código es muy parecido a la función Search que usa el objeto `_catalogSearch` para llamar a una función en **CatalogSearch.cs** denominada `Suggest`. En lugar de realizar una consulta de búsqueda, la función `Suggest` hace una llamada a la [API de sugerencias][API de sugerencias]. Dicha API usa los términos escritos en el cuadro de texto y genera una lista de posibles sugerencias. Los valores se devuelven al archivo **Index.cshtml** y se muestran automáticamente en el cuadro de búsqueda como opciones anticipadas.

Puede que en este punto se pregunte cómo sabe el servicio de Búsqueda de Azure sobre qué campos crear sugerencias. La respuesta a esto se remonta a cuando creó el índice. En la función `CreateCatalogIndex` que hay dentro del archivo Program.cs del proyecto **CatalogIndexer**, hay un atributo llamado `Suggestions`. Cada vez que este atributo se establece en `True`, significa que Búsqueda de Azure puede usarlo como campo para recuperar sugerencias.

Vamos a probar esto.

1.  Una vez más, cree la aplicación y luego presione **F5** para ejecutarla.

2.  En el cuadro de búsqueda, escriba la palabra "Road". Al cabo de un segundo, verá una lista de elementos debajo del cuadro de texto con sugerencias que el usuario podría elegir.

Quizás desee agregar un punto de interrupción a la función `Suggest` en **HomeController.cs** y recorrer (F11) cada llamada que se hace para elaborar la lista de sugerencias.

Con esto finaliza la demostración. Ya ha visto las operaciones principales que necesitará conocer para elaborar una aplicación de ASP.NET MVC4 basada usando Búsqueda de Azure.

## Procedimiento para resolver "No se pudo cargar el archivo o el ensamblado System.Web.Mvc"

Al compilar AdventureWorksWeb, si recibe el mensaje "No se puede cargar el archivo o ensamblado 'System.Web.Mvc, Version=4.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35' ni una de sus dependencias", intente seguir estos pasos para resolver el error.

1.  Abra la Consola del Administrador de paquetes: **Herramientas** | **Administrador de paquetes NuGet** | **Consola del Administrador de paquetes**
2.  En el símbolo del sistema PM\>, escriba "Update-package -reinstall Microsoft.AspNet.Mvc".
3.  Cuando se le pida volver a cargar el archivo, elija **Sí a todo**.
4.  Recompile la solución y presione **F5** de nuevo.

## Pasos siguientes

Si quiere ampliar conocimientos, puede agregar una página de detalles que se abra cuando un usuario hace clic en uno de los resultados de búsqueda. Para preparar esto, podría hacer lo siguiente:

-   Estudie la [API de búsqueda][API de búsqueda] que le permite hacer una consulta en Búsqueda de Azure para recuperar un documento específico (por ejemplo, podría pasar el productID).
-   Intente agregar una nueva función al archivo **HomeController.cs** llamada Details. Agregue una vista **Details.cshtml** correspondiente que reciba los resultados de esta búsqueda y los muestre.
-   Consulte este ejemplo de código adicional y el vídeo sobre búsqueda geoespacial: [Canal 9: Búsqueda de Azure y datos geoespaciales][Canal 9: Búsqueda de Azure y datos geoespaciales] y [CodePlex: Ejemplo de búsqueda geográfica con Búsqueda de Azure][CodePlex: Ejemplo de búsqueda geográfica con Búsqueda de Azure]

También puede revisar la [API de REST de Búsqueda de Azure][documentación de la API de REST de Búsqueda de Azure] en MSDN.

<!--Anchors--> <!--Image references-->

  [Requisitos previos]: #sub-1
  [Creación de un índice de Búsqueda de Azure]: #sub-2
  [Exploración de CatalogIndexer]: #sub-3
  [Creación de una aplicación de MVC4 mediante Búsqueda de Azure]: #sub-4
  [Exploración de AdventureWorksWeb]: #sub-5
  [Pasos siguientes]: #next-steps
  [0]: ./media/search-create-first-solution/AzureSearch_Create1_WebApp.PNG
  [Centro de descarga]: http://www.microsoft.com/es-es/download/details.aspx?id=40747
  [Introducción a Búsqueda de Azure]: ../search-get-started/
  [Proyecto Adventure Works para la demostración de la función de Búsqueda de Azure en CodePlex]: http://go.microsoft.com/fwlink/p/?LinkID=510972
  [1]: ./media/search-create-first-solution/AzureSearch_Create1_CodeplexDownload.PNG
  [2]: ./media/search-create-first-solution/AzureSearch_Create1_ConsoleMsg.PNG
  [portal de vista previa de Azure]: https://portal.azure.com
  [3]: ./media/search-create-first-solution/AzureSearch_Create1_DashboardIndexes.PNG
  [documentación de la API de REST de Búsqueda de Azure]: http://msdn.microsoft.com/es-es/library/azure/dn798935.aspx
  [Solución de problemas]: #err-mvc
  [4]: ./media/search-create-first-solution/AzureSearch_Create1_WebAppEmpty.PNG
  [5]: ./media/search-create-first-solution/AzureSearch_Create1_Suggestions.PNG
  [API de sugerencias]: http://msdn.microsoft.com/es-es/library/azure/dn798936.aspx
  [API de búsqueda]: http://msdn.microsoft.com/es-es/library/azure/dn798929.aspx
  [Canal 9: Búsqueda de Azure y datos geoespaciales]: http://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data
  [CodePlex: Ejemplo de búsqueda geográfica con Búsqueda de Azure]: http://azuresearchgeospatial.codeplex.com
