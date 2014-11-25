<properties linkid="develop-mobile-tutorials-get-started-with-data-xamarin-ios" urlDisplayName="Get Started with Data" pageTitle="Get started with data (Xamarin.iOS) - Azure Mobile Services" metaKeywords="Azure Xamarin.iOS data, Azure mobile services data, " description="Learn how to store and access data from your Azure Mobile Services Xamarin.iOS app." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="donnam" manager="dwrede" services="mobile-services"/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="donnam" />

# Introducción a los datos en Servicios móviles

[WACOM.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

Este tema muestra cómo utilizar Servicios móviles de Azure para aprovechar los datos en una aplicación de Xamarin.iOS. En este tutorial descargará una aplicación que almacena datos en memoria, creará un nuevo servicio móvil, integrará el servicio móvil en la aplicación y luego iniciará sesión en el Portal de administración de Azure para ver los cambios que se hicieron en los datos durante la ejecución de la aplicación.

<div class="dev-callout"><b>Nota:</b>
<p>Este tutorial tiene como fin ayudarle a comprender c&oacute;mo los Servicios m&oacute;viles le permiten usar Azure para almacenar y recuperar datos de una aplicaci&oacute;n Xamarin.iOS. Para ello, en este tema se recorren muchos de los pasos que se completan autom&aacute;ticamente en el inicio r&aacute;pido de Servicios m&oacute;viles. Si esta es la primera vez que usa los Servicios m&oacute;viles, considere la posibilidad de completar antes el tutorial <a href="/es-es/develop/mobile/tutorials/get-started-xamarin-ios">Introducci&oacute;n a los Servicios m&oacute;viles</a>.</p>
</div>

Este tutorial le guiará a través de estos pasos básicos:

1.  [Descarga del proyecto de la aplicación Xamarin.iOS][Descarga del proyecto de la aplicación Xamarin.iOS]
2.  [Creación del servicio móvil][Creación del servicio móvil]
3.  [Incorporación de una tabla de datos para almacenamiento][Incorporación de una tabla de datos para almacenamiento]
4.  [Actualización de la aplicación para usar Servicios móviles][Actualización de la aplicación para usar Servicios móviles]
5.  [Prueba de la aplicación en Servicios móviles][Prueba de la aplicación en Servicios móviles]

Este tutorial requiere el [componente Servicios móviles de Azure][componente Servicios móviles de Azure], [XCode 5.0][XCode 5.0], [Xamarin.iOS] e iOS 5.0 o versiones posteriores.

<div class="dev-callout"><strong>Nota:</strong> <p>para completar este tutorial, deber&aacute; tener una cuenta de Azure. En caso de no tener ninguna, puede crear una cuenta de evaluaci&oacute;n gratuita en tan solo unos minutos. Para obtener m&aacute;s informaci&oacute;n, consulte <a href="http://www.windowsazure.com/es-es/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fes-es%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-xamarin-ios%2F" target="_blank">Evaluaci&oacute;n gratuita de Azure</a>.</p></div>

## <a name="download-app"></a><span class="short-header">Descarga del proyecto</span>Descarga del proyecto GetStartedWithData

Este tutorial se ha creado sobre la [aplicación GetStartedWithData][Descarga del proyecto de la aplicación Xamarin.iOS], que es una aplicación Xamarin.iOS. La interfaz de usuario de esta aplicación es idéntica a la de la aplicación generada por el inicio rápido de Xamarin.iOS para Servicios móviles, salvo por los elementos agregados que se almacenan localmente en la memoria.

1.  Descargue la [aplicación GetStartedWithData][Descarga del proyecto de la aplicación Xamarin.iOS].

2.  En Xamarin Studio, abra el proyecto descargado y examine la clase **TodoService**.

    Tenga en cuenta que hay varios comentarios **// TODO** que especifican los pasos que debe realizar para que la aplicación funcione con su servicio móvil.

3.  Vaya al menú **Run** y elija **Start Without Debugging** para iniciar la aplicación.

4.  En la aplicación, escriba algo de texto en el cuadro de texto y, a continuación, haga clic en el botón **+**.

    ![][0]

    Observe que el texto guardado se muestra en la lista a continuación.

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

    Con esto se crea una tabla de almacenamiento **TodoItem** con el conjunto de permisos predeterminado, lo que significa que todo usuario de la aplicación podrá tener acceso a los datos de la tabla y modificarlos.

    <div class="dev-callout"> 
<b>Nota:</b> 
<p>El mismo nombre de tabla se utiliza en la gu&iacute;a de inicio r&aacute;pido de Servicios m&oacute;viles. Sin embargo, cada tabla se crea en un esquema que es espec&iacute;fico de un servicio m&oacute;vil determinado. De esta manera, se evita que colisionen los datos cuando varios servicios m&oacute;viles utilizan la misma base de datos.</p> 
</div>

4.  Haga clic en la nueva tabla **TodoItem** y verifique que no haya filas de datos.

5.  Haga clic en la pestaña **Columns** y compruebe que hay solo una columna **id**, que se crea automáticamente.

    Este es el requisito mínimo para una tabla en Servicios móviles.

    <div class="dev-callout"><b>Nota:</b>
<p>Cuando est&aacute; habilitado el esquema din&aacute;mico en su servicio m&oacute;vil, se crean columnas autom&aacute;ticamente cuando se env&iacute;an objetos JSON al servicio m&oacute;vil mediante una operaci&oacute;n de inserci&oacute;n o de actualizaci&oacute;n.</p>
</div>

Ahora ya está listo para utilizar el nuevo servicio móvil como almacenamiento de datos para la aplicación.

## <a name="update-app"></a><span class="short-header">Actualización de la aplicación</span>Actualización de la aplicación para usar el servicio móvil para el acceso a datos

Ahora que el servicio móvil está listo, puede actualizar la aplicación a fin de almacenar elementos en Servicios móviles en lugar de en la colección local.

1.  Si todavía no aparece **Servicios móviles de Azure** en la carpeta de componentes, haga clic con el botón secundario en **Components**, elija **Get More Components** y, a continuación, busque **Servicios móviles de Azure**.

2.  En la vista de soluciones de Xamarin Studio, abra la clase **TodoService** y quite la marca de comentario de la siguiente instrucción `using`:

        using Microsoft.WindowsAzure.MobileServices;

3.  En el Portal de administración, haga clic en **Servicios móviles** y, a continuación, en el servicio móvil que acaba de crear.

4.  Haga clic en la pestaña **Panel** y anote la **dirección URL del sitio**, a continuación haga clic en **Administrar claves** y anote la **clave de la aplicación**.

    ![][3]

5.  En la clase **Constants**, quite la marca de comentario de las siguientes constantes:

        public const string ApplicationURL = @"AppUrl";
        public const string ApplicationKey = @"AppKey";

6.  Reemplace **AppUrl** y **AppKey** de las constantes anteriores por los valores que encontró anteriormente en el Portal de administración.

7.  En la clase **TodoService**, quite la marca de comentario de la siguiente línea de código:

        private MobileServiceClient client;

    De esta manera se crea una propiedad que representa el MobileServiceClient que se conecta al servicio.

8.  En la clase **TodoService**, quite la marca de comentario de la siguiente línea de código:

        private IMobileServiceTable<TodoItem> todoTable;  

    Así se crea una representación de propiedad para su tabla de servicios móviles.

9.  Quite la marca de comentario de las siguientes líneas en el constructor **TodoService**:

        // TODO:: Uncomment these lines to use Mobile Services
        client = new MobileServiceClient(Constants.ApplicationURL, Constants.ApplicationKey).WithFilter(this); 
        todoTable = client.GetTable<TodoItem>(); 

    Con esto se crea una instancia del cliente de Servicios móviles y se crea la instancia de tabla TodoItem.

10. Quite la marca de comentario de las siguientes líneas en el método **RefreshDataAsync** en **TodoService**

        // TODO:: Uncomment these lines to use Mobile Services

    try
     {
     // Este código actualiza las entradas de la vista de lista al consultar la tabla TodoItems.
     // Esta consulta excluye los elementos TodoItems
     Items = await todoTable
     .Where (todoItem =\> todoItem.Complete == false).ToListAsync();
     }
     catch (MobileServiceInvalidOperationException e)
     {
     Console.Error.WriteLine (@"ERROR {0}", e.Message);
     return null;
     }

    Así se crea una consulta para devolver todas las tareas que no se hayan completado todavía.

11. Busque el método **InsertTodoItemAsync** y quite la marca de comentario de la siguiente línea:

        await todoTable.InsertAsync(todoItem);

    Este código envía una solicitud de inserción al servicio móvil.

12. Busque el método **CompleteItemAsync** y quite la marca de comentario de la siguiente línea:

        await todoTable.UpdateAsync(item);

    Este código quita los elementos TodoItems una vez que se han marcado como completados.

Ahora que se ha actualizado la aplicación para utilizar Servicios móviles para almacenamiento back-end, es momento de probar la aplicación con Servicios móviles.

## <a name="test-app"></a><span class="short-header">Prueba de la aplicación</span>Prueba de la aplicación con su servicio móvil nuevo

1.  En Xamarin Studio, seleccione un emulador o un dispositivo en el que implementar desde uno de los cuadros combinados principales, luego vaya al menú **Run** y seleccione **Start WithoutDebugging** para iniciar la aplicación.

    Esta acción ejecuta el cliente de Servicios móviles de Azure, que se ha creado con Xamarin.iOS, que consulta elementos desde su servicio móvil.

2.  Al igual que anteriormente, escriba texto en el cuadro de texto y, a continuación, haga clic en el botón **+**.

    Esto envía un elemento nuevo como inserción al servicio móvil.

3.  En el [Portal de administración][Portal de administración], haga clic en **Servicios móviles** y, a continuación, en su servicio móvil.

4.  Haga clic en la pestaña **Data** y, a continuación, en **Browse**.

    ![][4]

    Observe que la tabla **TodoItem** ahora contiene datos con valores de identificador generados por Servicios móviles, y que se agregaron automáticamente columnas a la tabla para que coincida con la clase TodoItem de la aplicación.

Así concluye el tutorial **Introducción a los datos** para Xamarin.iOS.

## Obtener un ejemplo completado

Descargue el [proyecto de ejemplo completado][Descarga del proyecto de la aplicación Xamarin.iOS]. Asegúrese de actualizar las variables **applicationURL** y **applicationKey** con su propia configuración de Azure.

## <a name="next-steps"> </a>Pasos siguientes

Este tutorial demostró los aspectos básicos de la habilitación de una aplicación de iOS para trabajar con datos en Servicios móviles.

A continuación, considere la realización de uno de los siguientes tutoriales que se basan en la aplicación GetStartedWithData que creó en este tutorial:

-   [Validación y modificación de datos con scripts][Validación y modificación de datos con scripts]
    Obtenga más información acerca del uso de scripts de servidor en Servicios móviles para validar y cambiar datos enviados desde su aplicación.

-   [Limitación de consultas con paginación][Limitación de consultas con paginación]
    Aprenda a utilizar la paginación en consultas para controlar la cantidad de datos que se manejan en una única solicitud.

Una vez que haya completado la serie de datos, pruebe con uno de estos otros tutoriales para iOS:

-   [Introducción a la autenticación][Introducción a la autenticación]
    Aprenda a autenticar a los usuarios de su aplicación.

-   [Introducción a las notificaciones de inserción][Introducción a las notificaciones de inserción]
    Aprenda a enviar una notificación de inserción muy básica a la aplicación con Servicios móviles.

  <!-- URLs. TODO:: update download link, github link, and completed example project with new Xamarin.iOs projects -->

  [Descarga del proyecto de la aplicación Xamarin.iOS]: http://go.microsoft.com/fwlink/p/?LinkId=331302
  [Creación del servicio móvil]: #create-service
  [Incorporación de una tabla de datos para almacenamiento]: #add-table
  [Actualización de la aplicación para usar Servicios móviles]: #update-app
  [Prueba de la aplicación en Servicios móviles]: #test-app
  [componente Servicios móviles de Azure]: http://components.xamarin.com/view/azure-mobile-services/
  [XCode 5.0]: https://go.microsoft.com/fwLink/p/?LinkID=266532
  [0]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-quickstart-startup-ios.png
  [1]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-data-tab-empty.png
  [2]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-create-todoitem-table.png
  [3]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-dashboard-tab.png
  [Portal de administración]: https://manage.windowsazure.com/
  [4]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-todoitem-data-browse.png
  [Validación y modificación de datos con scripts]: /es-es/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios
  [Limitación de consultas con paginación]: /es-es/develop/mobile/tutorials/add-paging-to-data-xamarin-ios
  [Introducción a la autenticación]: /es-es/develop/mobile/tutorials/get-started-with-users-xamarin-ios
  [Introducción a las notificaciones de inserción]: /es-es/develop/mobile/tutorials/get-started-with-push-xamarin-ios
