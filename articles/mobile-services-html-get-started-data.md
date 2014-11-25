<properties linkid="develop-mobile-tutorials-get-started-with-data-html" urlDisplayName="Get Started with Data (HTML5)" pageTitle="Get started with data (HTML 5) | Mobile Dev Center" metaKeywords="" description="Learn how to get started using Mobile Services to leverage data in your HTML app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-html" ms.devlang="javascript" ms.topic="article" ms.date="09/24/2014" ms.author="glenga" />

# Introducción a los datos en Servicios móviles

[WACOM.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

Este tema muestra cómo utilizar Servicios móviles de Azure para aprovechar los datos en una aplicación HTML. En este tutorial descargará una aplicación que almacena datos en memoria, creará un nuevo servicio móvil, integrará el servicio móvil en la aplicación y luego iniciará sesión en el Portal de administración de Azure para ver los cambios que se hicieron en los datos durante la ejecución de la aplicación.

> [WACOM.NOTE] Este tutorial está destinado a profundizar en el uso de Azure con los Servicios móviles para almacenar y recuperar datos en una aplicación HTML. Para ello, en este tema se recorren muchos de los pasos que se completan automáticamente en el inicio rápido de Servicios móviles. Si esta es la primera vez que usa los Servicios móviles, considere la posibilidad de completar antes el tutorial [Introducción a los Servicios móviles][Introducción a los Servicios móviles].

Este tutorial le guiará a través de estos pasos básicos:

1.  [Descarga del proyecto de la aplicación HTML][Descarga del proyecto de la aplicación HTML]
2.  [Creación del servicio móvil][Creación del servicio móvil]
3.  [Incorporación de una tabla de datos para almacenamiento][Incorporación de una tabla de datos para almacenamiento]
4.  [Actualización de la aplicación para usar Servicios móviles][Actualización de la aplicación para usar Servicios móviles]
5.  [Prueba de la aplicación en Servicios móviles][Prueba de la aplicación en Servicios móviles]

<div class="dev-callout"><strong>Nota:</strong> <p>Para completar este tutorial, deber&aacute; tener una cuenta de Azure. En caso de no tener ninguna, puede crear una cuenta de evaluaci&oacute;n gratuita en tan solo unos minutos. Para obtener m&aacute;s informaci&oacute;n, consulte <a href="http://www.windowsazure.com/es-es/pricing/free-trial/?WT.mc_id=A756A2826&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fes-es%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-html%2F" target="_blank">Evaluaci&oacute;n gratuita de Azure</a>.</p></div>

### Requisitos adicionales

Puede hospedar la aplicación GetStartedWithData en cualquier servidor web. Sin embargo, para mayor comodidad se proporcionan scripts que permiten ejecutar la aplicación en `http://localhost:8000`.

-   Para usar localhost, este tutorial requiere que uno de los siguientes servidores web se esté ejecutando en su equipo local:

    -   **En Windows**: IIS Express. IIS Express lo instala el [instalador de plataforma web de Microsoft].
    -   **En MacOS X**: Python, que ya debería estar instalado.
    -   **En Linux**: Python. Debe instalar la [última versión de Python].

    Puede usar cualquier servidor web para hospedar la aplicación, aunque estos son los servidores web que admiten los scripts descargados.

-   Un servidor web compatible con HTML5.

## <a name="download-app"></a><span class="short-header">Descarga del proyecto</span>Descarga del proyecto GetStartedWithData

Este tutorial se basa en la [aplicación GetStartedWithData][aplicación GetStartedWithData], una aplicación HTML5. La interfaz de usuario de esta aplicación es idéntica a la de la aplicación generada por el inicio rápido de Servicios móviles, con la excepción de que los elementos agregados se almacenan localmente en la memoria.

1.  [Descarga de los archivos del proyecto de la aplicación HTML][aplicación GetStartedWithData].

2.  En un editor HTML, abra el proyecto descargado y examine el archivo app.js.

    Observe que los elementos agregados se almacenan en un objeto **Array** en memoria (**staticItems**). Actualice la página y los datos desaparecerán. No se mantienen.

3.  Inicie uno de los archivos de comandos siguientes desde la subcarpeta **server**.

    -   **launch-windows** (equipos con Windows)
    -   **launch-mac.command** (equipos con Mac OS X)
    -   **launch-linux.sh** (equipos con Linux)

    <div class="dev-callout"><b>Nota:</b>
    <p>En un equipo con Windows, escriba &quot;R&quot; cuando PowerShell le pida que confirme que desea ejecutar el script. Su explorador web podr&iacute;a advertirle de no ejecutar el script porque se ha descargado de Internet. Cuando esto ocurra, debe solicitar que el explorador contin&uacute;e con la carga del script.</p>
</div>

    De este modo se inicia un servidor web en su equipo local para hospedar la nueva aplicación.

4.  Abra la URL <http://localhost:8000/> en un explorador web para iniciar la aplicación.

5.  En la aplicación, escriba un texto significativo, como *Realice el tutorial*, en **Enter new task** y, a continuación, haga clic en **Add**.

    ![][0]

    Observe que el texto guardado se agrega a la matriz **staticItems** y la página se actualiza para mostrar el nuevo elemento.

## <a name="create-service"></a><span class="short-header">Creación de un servicio móvil</span>Creación de un servicio móvil en el Portal de administración

[WACOM.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

## <a name="add-table"></a><span class="short-header">Incorporación de una tabla nueva</span>Incorporación de una tabla nueva al servicio móvil

Para poder almacenar datos de aplicaciones en el nuevo servicio móvil, primero debe crear una tabla en la instancia de Base de datos SQL asociada.

1.  En el Portal de administración, haga clic en **Servicios móviles** y, a continuación, en el servicio móvil que acaba de crear.

2.  Haga clic en la pestaña **Data** y, a continuación, en **+Create**.

    ![][1]

    Esto muestra el cuadro de diálogo **Create new table**.

3.  En **Table name**, escriba *TodoItem* y, a continuación, haga clic en el botón de comprobación.

    ![][2]

    Se crea la nueva tabla de almacenamiento **TodoItem** con los permisos predeterminados definidos. Esto significa que cualquiera que tenga la clave de aplicación, que se distribuye con la aplicación, puede tener acceso a los datos de la tabla y modificarlos.

    <div class="dev-callout"> 
<b>Nota:</b> 
<p>El mismo nombre de tabla se utiliza en la gu&iacute;a de inicio r&aacute;pido de Servicios m&oacute;viles. Sin embargo, cada tabla se crea en un esquema que es espec&iacute;fico de un servicio m&oacute;vil determinado. De esta manera, se evita que colisionen los datos cuando varios servicios m&oacute;viles utilizan la misma base de datos.</p> 
</div>

4.  Haga clic en la nueva tabla **TodoItem** y verifique que no haya filas de datos.

5.  Haga clic en la pestaña **Columns**. Verifique que las siguientes columnas predeterminadas se hayan creado automáticamente:

    | Nombre de columna | Tipo                    | Índice                             |
    |-------------------|-------------------------|------------------------------------|
    | id                | cadena                  | Indizada                           |
    | \_\_createdAt     | fecha                   | Indizada                           |
    | \_\_updatedAt     | fecha                   | <font color="transparent">-</font> |
    | \_\_version       | marca de tiempo (MSSQL) | <font color="transparent">-</font> |

    Este es el requisito mínimo para una tabla en Servicios móviles.

    <div class="dev-callout"><b>Nota:</b>
<p>Cuando est&aacute; habilitado el esquema din&aacute;mico en su servicio m&oacute;vil, se crean columnas autom&aacute;ticamente cuando se env&iacute;an objetos JSON al servicio m&oacute;vil mediante una operaci&oacute;n de inserci&oacute;n o de actualizaci&oacute;n.</p>
</div>

6.  En la pestaña **Configurar**, compruebe que `localhost` ya esté incluido en la lista **Permitir solicitudes de nombres de host** bajo **Cross-Origin Resource Sharing (CORS)**. Si no lo está, escriba `localhost` en el campo **Nombre de host** y, a continuación, haga clic en **Guardar**.

    ![][3]

    <div class="dev-callout"><b>Nota:</b>
    <p>Si implementa la aplicaci&oacute;n de inicio r&aacute;pido en un servidor web que no sea localhost, debe agregar el nombre de host del servidor web a la lista <strong>Permitir solicitudes de nombres de host</strong>. Para obtener m&aacute;s informaci&oacute;n, consulte <a href="http://msdn.microsoft.com/es-es/library/windowsazure/dn155871.aspx" target="_blank">Uso compartido de recursos entre or&iacute;genes</a>.</p>
</div>

Ahora ya está listo para utilizar el nuevo servicio móvil como almacenamiento de datos para la aplicación.

## <a name="update-app"></a><span class="short-header">Actualización de la aplicación</span>Actualización de la aplicación para usar el servicio móvil para el acceso a datos

Ahora que el servicio móvil está listo, puede actualizar la aplicación a fin de almacenar elementos en Servicios móviles en lugar de en la colección local.

1.  En el Portal de administración, haga clic en **Servicios móviles** y, a continuación, en el servicio móvil que acaba de crear.

2.  Haga clic en la pestaña **Panel** y anote la **dirección URL del sitio**, a continuación haga clic en **Administrar claves** y anote la **clave de la aplicación**.

    ![][4]

    Necesitará estos valores para obtener acceso al servicio móvil desde su código de aplicación.

3.  En el editor web, abra el archivo de proyecto index.html y agregue lo siguiente a las referencias de script de la página:

        <script src='http://ajax.aspnetcdn.com/ajax/mobileservices/MobileServices.Web-1.1.2.min.js'></script>

4.  En el editor, abra el archivo app.js, quite la marca del comentario del siguiente código que define la variable **MobileServiceClient** y, a continuación, proporcione la dirección URL y la clave de la aplicación desde el servicio móvil en el constructor **MobileServiceClient**, en dicho orden.

        var MobileServiceClient = WindowsAzure.MobileServiceClient,
            client = new MobileServiceClient('AppUrl', 'AppKey'),               

    De este modo, se crea la nueva instancia de MobileServiceClient que se usa para obtener acceso a su servicio móvil.

5.  Convierta en comentario las líneas de código siguientes:

        var itemCount = 0;
        var staticItems = [];

    Los datos se almacenarán en el servicio móvil y no en una matriz en memoria.

6.  Quite la marca de comentario de la línea de código siguiente:

        todoItemTable = client.getTable('todoitem');

    Este código crea un objeto proxy (**todoItemTable**) para la base de datos SQL **TodoItem**.

7.  Reemplace el controlador de eventos **$('\#add-item').submit** por el código siguiente:

        $('#add-item').submit(function(evt) {
            var textbox = $('#new-item-text'),
                itemText = textbox.val();
            if (itemText !== '') {
                todoItemTable.insert({ text: itemText, complete: false })
                    .then(refreshTodoItems);
            }
            textbox.val('').focus();
            evt.preventDefault();
        });

    Este código inserta un elemento nuevo en la tabla.

8.  Reemplace el método **refreshTodoItems** por el código siguiente:

        function refreshTodoItems() {

            var query = todoItemTable;

            query.read().then(function(todoItems) {
                listItems = $.map(todoItems, function(item) {
                    return $('<li>')
                        .attr('data-todoitem-id', item.id)
                        .append($('<button class="item-delete">Delete</button>'))
                        .append($('<input type="checkbox" class="item-complete">').prop('checked', item.complete))
                        .append($('<div class="dev-callout"><b>Nota:</b>
<p>Si es necesario reiniciar el servidor web, repita los pasos de la primera secci&oacute;n.</p>
</div>

2.  Al igual que anteriormente, escriba texto en **Enter new task** y, a continuación, haga clic en **Add**.

    Esto envía un elemento nuevo como inserción al servicio móvil.

3.  En el [Portal de administración][Portal de administración], haga clic en **Servicios móviles** y, a continuación, en su servicio móvil.

4.  Haga clic en la pestaña **Data** y, a continuación, en **Browse**.

    ![][5]

    Observe que la tabla **TodoItem** ahora contiene datos con valores de identificador generados por Servicios móviles, y que se agregaron automáticamente columnas a la tabla para que coincida con la clase TodoItem de la aplicación.

5.  En la aplicación, marque uno de los elementos de la lista, vuelva a la pestaña Browse del portal y haga clic en **Refresh**.

    Observe que el valor completo ha cambiado de **false** a **true**.

6.  En el archivo de proyecto app.js, busque el método **RefreshTodoItems** y reemplace la línea de código que define `query` por la siguiente:

        var query = todoItemTable.where({ complete: false });

7.  Vuelva a cargar la página y marque otro de los elementos de la lista.

    Observe que el elemento marcado ahora desaparece de la lista. Cada actualización resulta en un recorrido de ida y vuelta al servicio móvil, que ahora devuelve datos filtrados.

Con esto concluye el tutorial **Introducción a los datos**.

## <a name="next-steps"> </a>Pasos siguientes

Este tutorial muestra los aspectos básicos de la habilitación de una aplicación HTML para que funcione con los datos de los Servicios móviles. A continuación, considere la realización de uno de los siguientes tutoriales que se basan en la aplicación GetStartedWithData que creó en este tutorial:

-   [Validación y modificación de datos con scripts][Validación y modificación de datos con scripts]
    Obtenga más información acerca del uso de scripts de servidor en Servicios móviles para validar y cambiar datos enviados desde su aplicación.

-   [Limitación de consultas con paginación][Limitación de consultas con paginación]
    Aprenda a utilizar la paginación en consultas para controlar la cantidad de datos que se manejan en una única solicitud.

Una vez que haya completado la serie de datos y aprendido a autenticar a los usuarios de su aplicación, pruebe uno de estos otros tutoriales al finalizar [Introducción a la autenticación][Introducción a la autenticación].


 


  [Introducción a los Servicios móviles]: /es-es/develop/mobile/tutorials/get-started-html
  [Descarga del proyecto de la aplicación HTML]: #download-app
  [Creación del servicio móvil]: #create-service
  [Incorporación de una tabla de datos para almacenamiento]: #add-table
  [Actualización de la aplicación para usar Servicios móviles]: #update-app
  [Prueba de la aplicación en Servicios móviles]: #test-app
  [aplicación GetStartedWithData]: http://go.microsoft.com/fwlink/?LinkID=286345
  [0]: ./media/mobile-services-html-get-started-data/mobile-quickstart-startup-html.png
  [1]: ./media/mobile-services-html-get-started-data/mobile-data-tab-empty.png
  [2]: ./media/mobile-services-html-get-started-data/mobile-create-todoitem-table.png
  [3]: ./media/mobile-services-html-get-started-data/mobile-services-set-cors-localhost.png
  [4]: ./media/mobile-services-html-get-started-data/mobile-dashboard-tab.png
  [Portal de administración]: https://manage.windowsazure.com/
  [5]: ./media/mobile-services-html-get-started-data/mobile-todoitem-data-browse.png
  [Validación y modificación de datos con scripts]: /es-es/develop/mobile/tutorials/validate-modify-and-augment-data-html
  [Limitación de consultas con paginación]: /es-es/develop/mobile/tutorials/add-paging-to-data-html
  [Introducción a la autenticación]: /es-es/develop/mobile/tutorials/get-started-with-users-html
