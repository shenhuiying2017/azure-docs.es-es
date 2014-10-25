<properties linkid="develop-mobile-tutorials-get-started-with-data-dotnet-vs2013" urlDisplayName="Get Started with Data" pageTitle="Get started with data (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to get started using Mobile Services to leverage data in your Windows Store app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services (universal Windows apps)" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Introducción a los datos en Servicios móviles (aplicación Windows universal)

<div class="dev-center-tutorial-selector sublanding"><a href="/es-es/documentation/articles/mobile-services-windows-store-dotnet-get-started-data/" title="C# para Tienda Windows">C# para Tienda Windows</a><a href="/es-es/documentation/articles/mobile-services-windows-store-javascript-get-started-data/" title="JavaScript para Tienda Windows">JavaScript para Tienda Windows</a><a href="/es-es/documentation/articles/mobile-services-windows-phone-get-started-data/" title="Windows Phone">Windows Phone</a><a href="/es-es/documentation/articles/mobile-services-ios-get-started-data/" title="iOS">iOS</a><a href="/es-es/documentation/articles/mobile-services-android-get-started-data/" title="Android">Android</a><a href="/es-es/documentation/articles/mobile-services-html-get-started-data/" title="HTML">HTML</a><a href="/es-es/documentation/articles/partner-xamarin-mobile-services-ios-get-started-data/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/es-es/documentation/articles/partner-xamarin-mobile-services-android-get-started-data/" title="Xamarin.Android">Xamarin.Android</a></div>

<!---<div class="dev-center-tutorial-subselector"><a href="/es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/" title=".NET backend">.NET backend</a> | <a href="/es-es/documentation/articles/mobile-services-windows-store-dotnet-get-started-data/"  title="JavaScript backend" class="current">JavaScript backend</a></div>-->

Este tema muestra cómo utilizar Servicios móviles de Azure para aprovechar los datos en una aplicación de Windows 8.1 universal. En este tutorial descargará un proyecto de Visual Studio para una aplicación Windows universal que almacena datos en memoria, creará un nuevo servicio móvil, integrará el servicio móvil a la aplicación y luego iniciará sesión en el Portal de administración de Azure para ver los cambios que se hicieron en los datos durante la ejecución de la aplicación.

> [WACOM.NOTE]Este tutorial requiere Visual Studio 2013 Update 2.

Este tutorial le guiará a través de estos pasos básicos:

1.  [Descarga del proyecto de la aplicación de la Tienda Windows][]
2.  [Creación del servicio móvil desde Visual Studio][]
3.  [Incorporación de una tabla de datos para almacenamiento y actualización de la aplicación][]
4.  [Prueba de la aplicación en Servicios móviles][]

> [WACOM.NOTE] Necesita una cuenta de Azure para completar este tutorial. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure][].

## <a name="download-app"></a>Descarga del proyecto GetStartedWithData

Este tutorial se basa en la [aplicación GetStartedWithMobileServices (en inglés)][], que es un proyecto de aplicación de la Tienda Windows en Visual Studio 2013. La interfaz de usuario de esta aplicación es idéntica a la aplicación generada por el inicio rápido de Servicios móviles, excepto en que los elementos agregados se almacenan de manera local en la memoria.

1.  Descargue la versión de C\# de la aplicación de muestra GetStartedWithMobileServices desde el [sitio de Muestras de código para desarrollador (en inglés)][aplicación GetStartedWithMobileServices (en inglés)].

    ![][]

2.  En Visual Studio 2013, abra el proyecto descargado y examine el archivo MainPage.xaml.cs.

    Observe que los objetos **TodoItem** agregados se almacenan en **ObservableCollection\<TodoItem\>** en memoria.

3.  Presione la tecla **F5** para recompilar el proyecto e iniciar la aplicación.

4.  En la aplicación, escriba algún texto en **Insertar un TodoItem** y, a continuación, haga clic en **Guardar**.

    ![][1]

    Observe que el texto guardado aparece en la segunda columna bajo **Consultar y actualizar datos**.

## <a name="create-service"></a><span class="short-header">Creación del servicio móvil</span>Creación de un servicio móvil desde Visual Studio

[WACOM.INCLUDE [mobile-services-create-new-service-vs2013][]]

1.  En el Explorador de soluciones, abra el archivo de código App.xaml.cs y observe el nuevo campo estático que se agregó a la clase \*\*App\*\*, que se ve como el siguiente ejemplo:

        public static Microsoft.WindowsAzure.MobileServices.MobileServiceClient 
                    todolistClient = new Microsoft.WindowsAzure.MobileServices.MobileServiceClient(
                        "https://todolist.azure-mobile.net/",
                        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");
                

    Este código proporciona acceso al nuevo servicio móvil en su aplicación mediante el uso de una instancia de la [clase MobileServiceClient][]. El cliente se crea al suministrar el URI y la clave de aplicación del nuevo servicio móvil. Este campo estático está disponible para todas las páginas en la aplicación.

## <a name="add-table"></a><span class="short-header">Incorporación de una tabla nueva</span>Incorporación de una tabla nueva al servicio móvil y actualización de la aplicación

[WACOM.INCLUDE [mobile-services-create-new-table-vs2013][]]

1.  En el archivo MainPage.xaml.cs, agregue o quite la marca de comentario en las siguientes instrucciones Using:

        using Microsoft.WindowsAzure.MobileServices;
        using Newtonsoft.Json;

2.  En este mismo archivo, reemplace la definición de clase TodoItem por el siguiente código:

        public class TodoItem
        {
            public string Id { get; set; }

            [JsonProperty(PropertyName = "text")]
            public string Text { get; set; }

            [JsonProperty(PropertyName = "complete")] 
            public bool Complete { get; set; }
        }

    El atributo **JsonPropertyAttribute** se usa para definir la asignación entre los nombres de propiedad en el tipo de cliente y los nombres de columna en la tabla de datos subyacente.

3.  Comente la línea que define la colección de elementos existente y luego quite la marca de comentario o agregue las siguientes líneas y reemplace *\<yourClient\>* por el campo `MobileServiceClient` que se agregó al archivo App.xaml.cs cuando conectó su proyecto al servicio móvil:

        private MobileServiceCollection<TodoItem, TodoItem> items;
        private IMobileServiceTable<TodoItem> todoTable = 
            App.<yourClient>.GetTable<TodoItem>();

    Este código crea una colección de enlaces (elementos) compatible con los servicios móviles y una clase proxy para la tabla de base de datos (todoTable).

4.  En el método **InsertTodoItem**, quite la línea de código que define la propiedad **TodoItem.Id**, agregue el modificador **async** al método y quite la marca de comentario de la siguiente línea de código:

        await todoTable.InsertAsync(todoItem);

    Este código inserta un elemento nuevo en la tabla.

    <div class="dev-callout"><strong>Nota:</strong><p>Las tablas nuevas se crean con las columnas Id, __createdAt, __updatedAt y __version. Cuando est&aacute; habilitado el esquema din&aacute;mico, Servicios m&oacute;viles genera autom&aacute;ticamente columnas nuevas basadas en el objeto JSON en la solicitud de inserci&oacute;n o actualizaci&oacute;n. Para obtener m&aacute;s informaci&oacute;n, consulte <a href="http://msdn.microsoft.com/en-us/library/windowsazure/jj193175.aspx">Esquema din&aacute;mico</a>.</p></div>

5.  En el método **RefreshTodoItems**, agregue el modificador **async** al método y quite la marca de comentario en la siguiente línea de código:

        items = await todoTable.ToCollectionAsync();

    Esto define el enlace a la colección de elementos en `todoTable`, que contiene todos los objetos **TodoItem** devueltos por el servicio móvil.

6.  En el método **UpdateCheckedTodoItem**, agregue el modificador **async** al método y quite la marca de comentario en la siguiente línea de código:

        await todoTable.UpdateAsync(item);

    Esto envía una actualización de elemento al servicio móvil.

Ahora que se ha actualizado la aplicación para utilizar Servicios móviles para almacenamiento back-end, es momento de probar la aplicación con Servicios móviles.

## <a name="test-app"></a><span class="short-header">Prueba de la aplicación</span>Prueba de la aplicación con su servicio móvil nuevo

1.  En Visual Studio, presione la tecla F5 para ejecutar la aplicación.

2.  Al igual que anteriormente, escriba texto en **Insertar un TodoItem** y, a continuación, haga clic en **Guardar**.

    Esto envía un elemento nuevo como inserción al servicio móvil.

3.  En el [Portal de administración][], haga clic en **Servicios móviles** y, a continuación, en su servicio móvil.

4.  Haga clic en la pestaña **Datos** y, a continuación, en **Examinar**.

    ![][2]

    Observe que la tabla **TodoItem** ahora contiene datos con valores de identificador generados por Servicios móviles, y que se agregaron automáticamente columnas a la tabla para que coincida con la clase TodoItem de la aplicación.

5.  En la aplicación, marque uno de los elementos de la lista, vuelva a la pestaña Examinar del portal y haga clic en **Actualizar**.

    Observe que el valor completo ha cambiado de **false** a **true**.

6.  En el archivo de proyecto MainPage.xaml.cs, reemplace el método **RefreshTodoItems** existente por el siguiente código que filtra los elementos completados:

        private async void RefreshTodoItems()
        {                       
            // This query filters out completed TodoItems. 
            items = await todoTable
               .Where(todoItem => todoItem.Complete == false)
               .ToCollectionAsync();

            ListItems.ItemsSource = items;            
        }

7.  En la aplicación, marque otro de los elementos de la lista y, a continuación, haga clic en el botón **Actualizar**.

    Observe que el elemento marcado ahora desaparece de la lista. Cada actualización resulta en un recorrido de ida y vuelta al servicio móvil, que ahora devuelve datos filtrados.

Con esto concluye el tutorial **Introducción a los datos**.

## <a name="next-steps"> </a>Pasos siguientes

Este tutorial demostró los aspectos básicos de la habilitación de una aplicación de la Tienda Windows para trabajar con datos en Servicios móviles. A continuación, considere la realización de uno de los siguientes tutoriales que se basan en la aplicación GetStartedWithData que creó en este tutorial:

-   [Validación y modificación de datos con scripts][]
    Obtenga más información acerca del uso de scripts de servidor en Servicios móviles para validar y cambiar datos enviados desde su aplicación.

-   [Limitación de consultas con paginación][]
    Aprenda a utilizar la paginación en consultas para controlar la cantidad de datos que se manejan en una única solicitud.

Una vez que haya completado la serie de datos, intente con uno de estos otros tutoriales:

-   [Introducción a la autenticación][]
    Aprenda a autenticar a los usuarios de su aplicación.

-   [Introducción a las notificaciones de inserción][]
    Aprenda a enviar una notificación de inserción muy básica a la aplicación.

-   [Referencia conceptual de Servicios móviles con .NET][]

    Obtenga más información sobre cómo utilizar Servicios móviles con .NET.

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs. -->

  [C\# para Tienda Windows]: /es-es/documentation/articles/mobile-services-windows-store-dotnet-get-started-data/ "C# para Tienda Windows"
  [JavaScript para Tienda Windows]: /es-es/documentation/articles/mobile-services-windows-store-javascript-get-started-data/ "JavaScript para Tienda Windows"
  [Windows Phone]: /es-es/documentation/articles/mobile-services-windows-phone-get-started-data/ "Windows Phone"
  [iOS]: /es-es/documentation/articles/mobile-services-ios-get-started-data/ "iOS"
  [Android]: /es-es/documentation/articles/mobile-services-android-get-started-data/ "Android"
  [HTML]: /es-es/documentation/articles/mobile-services-html-get-started-data/ "HTML"
  [Xamarin.iOS]: /es-es/documentation/articles/partner-xamarin-mobile-services-ios-get-started-data/ "Xamarin.iOS"
  [Xamarin.Android]: /es-es/documentation/articles/partner-xamarin-mobile-services-android-get-started-data/ "Xamarin.Android"
  [Descarga del proyecto de la aplicación de la Tienda Windows]: #download-app
  [Creación del servicio móvil desde Visual Studio]: #create-service
  [Incorporación de una tabla de datos para almacenamiento y actualización de la aplicación]: #add-table
  [Prueba de la aplicación en Servicios móviles]: #test-app
  [Evaluación gratuita de Azure]: http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=AE564AB28
  [aplicación GetStartedWithMobileServices (en inglés)]: http://go.microsoft.com/fwlink/p/?LinkId=328660
  []: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2013/mobile-data-sample-download-dotnet-vs12.png
  [1]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2013/mobile-quickstart-startup.png
  [mobile-services-create-new-service-vs2013]: ../includes/mobile-services-create-new-service-vs2013.md
  [clase MobileServiceClient]: http://go.microsoft.com/fwlink/p/?LinkId=302030
  [mobile-services-create-new-table-vs2013]: ../includes/mobile-services-create-new-table-vs2013.md
  [Esquema dinámico]: http://msdn.microsoft.com/en-us/library/windowsazure/jj193175.aspx
  [Portal de administración]: https://manage.windowsazure.com/
  [2]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2013/mobile-todoitem-data-browse.png
  [Validación y modificación de datos con scripts]: /en-us/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
  [Limitación de consultas con paginación]: /en-us/develop/mobile/tutorials/add-paging-to-data-dotnet
  [Introducción a la autenticación]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet
  [Introducción a las notificaciones de inserción]: ../mobile-services-windows-store-dotnet-get-started-push/
  [Referencia conceptual de Servicios móviles con .NET]: /en-us/develop/mobile/how-to-guides/work-with-net-client-library
