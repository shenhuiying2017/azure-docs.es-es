<properties linkid="develop-mobile-tutorials-get-started-with-data-dotnet-vs2013" urlDisplayName="Get Started with Data" pageTitle="Get started with data (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to get started using Mobile Services to leverage data in your Windows Store app." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="glenga" solutions="" manager="" editor="" />


Introducción a los datos en Servicios móviles
=============================================

[C\# para Tienda Windows](/en-us/develop/mobile/tutorials/get-started-with-data-dotnet "C# para Tienda Windows")[JavaScript para Tienda Windows](/en-us/develop/mobile/tutorials/get-started-with-data-js "JavaScript para Tienda Windows")[Windows Phone](/en-us/develop/mobile/tutorials/get-started-with-data-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/get-started-with-data-ios "iOS")[Android](/en-us/develop/mobile/tutorials/get-started-with-data-android "Android")[HTML](/en-us/develop/mobile/tutorials/get-started-with-data-html "HTML")[Xamarin.iOS](/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-android "Xamarin.Android")

[Back-end de .NET](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/ "Back-end de .NET") | [Back-end de JavaScript](/en-us/develop/mobile/tutorials/get-started-with-data-dotnet/ "Back-end de JavaScript")

Este tema muestra cómo utilizar Servicios móviles de Azure para aprovechar los datos en una aplicación de la Tienda Microsoft. En este tutorial descargará un proyecto de Visual Studio 2013 para una aplicación que almacena datos en memoria, creará un nuevo servicio móvil, integrará el servicio móvil a la aplicación y luego iniciará sesión en el Portal de administración de Azure para ver los cambios que se hicieron en los datos durante la ejecución de la aplicación.

**Nota:**

Este tutorial requiere Visual Studio 2013, con lo que es más fácil conectar su aplicación de la Tienda de Windows con Servicios móviles. Para completar el mismo procedimiento básico con Visual Studio 2012, siga los pasos en el tema [Introducción a los datos en Servicios móviles con Visual Studio 2012](/en-us/develop/mobile/tutorials/get-started-with-data-dotnet-vs2012/).

Este tutorial le guiará a través de estos pasos básicos:

1.  [Descarga del proyecto de la aplicación de la Tienda Windows](#download-app)
2.  [Creación del servicio móvil desde Visual Studio](#create-service)
3.  [Incorporación de una tabla de datos para almacenamiento y actualización de la aplicación](#add-table)
4.  [Prueba de la aplicación en Servicios móviles](#test-app)

**Nota:**

Necesita una cuenta de Azure para completar este tutorial. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=AE564AB28).

Descarga del proyectoDescarga del proyecto GetStartedWithData
-------------------------------------------------------------

Este tutorial se basa en la [aplicación GetStartedWithMobileServices (en inglés)](http://go.microsoft.com/fwlink/p/?LinkId=328660), que es un proyecto de aplicación de la Tienda Windows en Visual Studio 2013. La interfaz de usuario de esta aplicación es idéntica a la aplicación generada por el inicio rápido de Servicios móviles, excepto en que los elementos agregados se almacenan de manera local en la memoria.

1.  Descargue la versión de C\# de la aplicación de muestra GetStartedWithMobileServices desde el [sitio de Muestras de código para desarrollador (en inglés)](http://go.microsoft.com/fwlink/p/?LinkId=328660).

	![][10]

2.  En Visual Studio 2013, abra el proyecto descargado y examine el archivo MainPage.xaml.cs.

	Observe que los objetos **TodoItem** agregados se almacenan en **ObservableCollection<TodoItem>** en memoria.

3.  Pulse la tecla **F5** para recompilar el proyecto e iniciar la aplicación.

4.  En la aplicación, escriba algún texto en **Insert a TodoItem** y, a continuación, haga clic en **Save**.

	![][0]  

	Observe que el texto guardado aparece en la segunda columna bajo **Query and update data**.

Creación del servicio móvilCreación de un servicio móvil desde Visual Studio
----------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-create-new-service-vs2013](../includes/mobile-services-create-new-service-vs2013.md)]

1.  En el Explorador de soluciones, abra el archivo de código App.xaml.cs y observe el nuevo campo estático que se agregó a la clase \*\*App\*\*, que se ve como el siguiente ejemplo:

    ``` {data-morhtml="true"}
    public static Microsoft.WindowsAzure.MobileServices.MobileServiceClient 
                todolistClient = new Microsoft.WindowsAzure.MobileServices.MobileServiceClient(
                    "https://todolist.azure-mobile.net/",
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");
            
    ```

    Este código proporciona acceso al nuevo servicio móvil en su aplicación mediante el uso de una instancia de la [clase MobileServiceClient](http://go.microsoft.com/fwlink/p/?LinkId=302030). El cliente se crea al suministrar el URI y la clave de aplicación del nuevo servicio móvil. Este campo estático está disponible para todas las páginas en la aplicación.

Incorporación de una tabla nuevaIncorporación de una tabla nueva al servicio móvil y actualización de la aplicación
-------------------------------------------------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-create-new-table-vs2013](../includes/mobile-services-create-new-table-vs2013.md)]

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

3.  Comente la línea que define la colección de elementos existente y luego quite la marca de comentario o agregue las siguientes líneas y reemplace *&lt;yourClient\>* por el campo MobileServiceClient que se agregó al archivo App.xaml.cs cuando conectó su proyecto al servicio móvil:

         private MobileServiceCollection<TodoItem data-morhtml="true", TodoItem> items;
         private IMobileServiceTable<TodoItem> todoTable = 
             App.<yourClient>.GetTable<TodoItem>();

    Este código crea una colección de enlaces (elementos) compatible con los servicios móviles y una clase proxy para la tabla de base de datos (todoTable).

4.  En el método **InsertTodoItem**, quite la línea de código que define la propiedad **TodoItem.Id**, agregue el modificador **async** al método y quite la marca de comentario de la siguiente línea de código:

         await todoTable.InsertAsync(todoItem);

    Este código inserta un elemento nuevo en la tabla.

    **Nota:**

    Las tablas nuevas se crean con las columnas Id, \_\_createdAt, \_\_updatedAt y \_\_version. Cuando está habilitado el esquema dinámico, Servicios móviles genera automáticamente columnas nuevas basadas en el objeto JSON en la solicitud de inserción o actualización. Para obtener más información, consulte [Esquema dinámico](http://msdn.microsoft.com/en-us/library/windowsazure/jj193175.aspx).

5.  En el método **RefreshTodoItems**, agregue el modificador **async** al método y quite la marca de comentario en la siguiente línea de código:

         items = await todoTable.ToCollectionAsync();

    Esto define el enlace a la colección de elementos en `todoTable`, que contiene todos los objetos **TodoItem** devueltos por el servicio móvil.

6.  En el método **UpdateCheckedTodoItem**, agregue el modificador **async** al método y quite la marca de comentario en la siguiente línea de código:

         await todoTable.UpdateAsync(item);

    Esto envía una actualización de elemento al servicio móvil.

Ahora que se ha actualizado la aplicación para utilizar Servicios móviles para almacenamiento back-end, es momento de probar la aplicación con Servicios móviles.

Prueba de la aplicaciónPrueba de la aplicación con su servicio móvil nuevo
--------------------------------------------------------------------------

1.  En Visual Studio, pulse la tecla F5 para ejecutar la aplicación.

2.  Al igual que anteriormente, escriba texto en **Insert a TodoItem** y, a continuación, haga clic en **Save**.

        Esto envía un elemento nuevo como inserción al servicio móvil.

3.  En el [Portal de administración](https://manage.windowsazure.com/), haga clic en **Servicios móviles** y, a continuación, en su servicio móvil.

4.  Haga clic en la pestaña **Data** y, a continuación, en **Browse**.

	![][9]
          
	Observe que la tabla **TodoItem** ahora contiene datos con valores de identificador generados por Servicios móviles, y que se agregaron automáticamente columnas a la tabla para coincidir con la clase TodoItem en la aplicación.

5.  En la aplicación, marque uno de los elementos de la lista, vuelva a la pestaña Browse del portal y haga clic en **Refresh**.

	Observe que el valor completo ha cambiado de **false** a **true**.

1.  En el archivo de proyecto MainPage.xaml.cs, reemplace el método **RefreshTodoItems** existente por el siguiente código que filtra los elementos completados:

         private async void RefreshTodoItems()
         {                       
             // Esta consulta filtra TodoItems completados. 
             items = await todoTable
                .Where(todoItem => todoItem.Complete == false)
                .ToCollectionAsync();

             ListItems.ItemsSource = items;            
         }

2.  En la aplicación, marque otro de los elementos de la lista y, a continuación, haga clic en el botón **Refresh**.

	Observe que el elemento marcado ahora desaparece de la lista. Cada actualización resulta en un recorrido de ida y vuelta al servicio móvil, que ahora devuelve datos filtrados.

Con esto concluye el tutorial **Introducción a los datos**.

Pasos siguientes
----------------

Este tutorial demostró los aspectos básicos de la habilitación de una aplicación de la Tienda Windows para trabajar con datos en Servicios móviles. A continuación, considere la realización de uno de los siguientes tutoriales que se basan en la aplicación GetStartedWithData que creó en este tutorial:

-   [Validación y modificación de datos con scripts](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet)
    Obtenga más información acerca del uso de scripts de servidor en Servicios móviles para validar y cambiar datos enviados desde su aplicación.

-   [Limitación de consultas con paginación](/en-us/develop/mobile/tutorials/add-paging-to-data-dotnet)
    Aprenda a utilizar la paginación en consultas para controlar la cantidad de datos que se manejan en una única solicitud.

Una vez que haya completado la serie de datos, intente con uno de estos otros tutoriales:

-   [Introducción a la autenticación](/en-us/develop/mobile/tutorials/get-started-with-users-dotnet)
    Aprenda a autenticar a los usuarios de su aplicación.

-   [Introducción a las notificaciones de inserción](../mobile-services-windows-store-dotnet-get-started-push/)
    Aprenda a enviar una notificación de inserción muy básica a la aplicación.

-   [Referencia conceptual de Servicios móviles con .NET](/en-us/develop/mobile/how-to-guides/work-with-net-client-library)
    Obtenga más información sobre cómo utilizar Servicios móviles con .NET.



<!-- Anchors. -->

[Get the Windows Store app]: #download-app
[Create the mobile service from Visual Studio]: #create-service
[Add a data table for storage and update the app]: #add-table
[Update the app to use Mobile Services]: #update-app
[Test the app against Mobile Services]: #test-app
[Next Steps]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2013/mobile-quickstart-startup.png








[9]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2013/mobile-todoitem-data-browse.png
[10]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2013/mobile-data-sample-download-dotnet-vs12.png


<!-- URLs. -->
[Validate and modify data with scripts]: /en-us/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Refine queries with paging]: /en-us/develop/mobile/tutorials/add-paging-to-data-dotnet
[Get started with Mobile Services]: /en-us/develop/mobile/tutorials/get-started
[Get started with data]: /en-us/develop/mobile/tutorials/get-started-with-data-dotnet
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet
[Get started with push notifications]: ../mobile-services-windows-store-dotnet-get-started-push/
[JavaScript and HTML]: /en-us/develop/mobile/tutorials/get-started-with-data-js

[Azure Management Portal]: https://manage.windowsazure.com/
[Management Portal]: https://manage.windowsazure.com/
[Mobile Services SDK]: http://go.microsoft.com/fwlink/p/?LinkId=257545
[Developer Code Samples site]:  http://go.microsoft.com/fwlink/p/?LinkId=328660
[Mobile Services .NET How-to Conceptual Reference]: /en-us/develop/mobile/how-to-guides/work-with-net-client-library
[MobileServiceClient class]: http://go.microsoft.com/fwlink/p/?LinkId=302030

