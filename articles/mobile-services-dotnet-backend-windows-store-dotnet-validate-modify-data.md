<properties linkid="develop-mobile-tutorials-dotnet-backend-validate-modify-and-augment-data-dotnet" urlDisplayName="Validate and Modify Data" pageTitle="Use the .Net backend to validate and modify data (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to validate, modify, and augment data for your Windows Store app with .Net backend Windows Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using the .Net backend" authors="wesmc" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc"></tags>

# Validación y modificación de datos en los Servicios móviles mediante el back-end de .NET

<div class="dev-center-tutorial-selector sublanding">
<a href="/es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-validate-modify-data/" title="C# para Tienda Windows" class="current">C# para Tienda Windows</a>
<a href="/es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/" title="JavaScript para Tienda Windows">JavaScript para Tienda Windows</a>
<a href="/es-es/documentation/articles/mobile-services-dotnet-backend-windows-phone-validate-modify-data/" title="Windows Phone">Windows Phone</a>
<a href="/es-es/develop/mobile/tutorials/validate-modify-and-augment-data-ios" title="iOS">iOS</a>
<a href="/es-es/develop/mobile/tutorials/validate-modify-and-augment-data-android" title="Android">Android</a>
<a href="/es-es/develop/mobile/tutorials/validate-modify-and-augment-data-html" title="HTML">HTML</a><a href="/es-es/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a>
<a href="/es-es/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android" title="Xamarin.Android">Xamarin.Android</a>
</div>

<div class="dev-center-tutorial-subselector">
    <a href="/es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-validate-modify-data/" title="Back-end de .NET" class="current">Back-end de .NET</a> | 
    <a href="/es-es/documentation/articles/mobile-services-windows-store-dotnet-validate-modify-data-server-scripts/"  title="Back-end de JavaScript">Back-end de JavaScript</a>
</div>

Este tema muestra cómo usar el código en Servicios móviles de Azure del back-end de .NET para validar y modificar los datos. El servicio back-end de .Net es un servicio HTTP compilado con Entity Framework y el marco API Web. Si está familiarizado con la clase `ApiController`, definida con el marco API Web, la clase `TableController` que la solución Servicios móviles proporciona le resultará muy intuitiva. `TableController` proviene de la clase `ApiController` y proporciona funcionalidad adicional para la creación de la interfaz de la tabla de base de datos. Se puede utilizar para realizar operaciones en los datos que se van a insertar y actualizar, lo que incluye la validación y modificación de los datos que se presentan en este tutorial.

Este tutorial le guiará a través de estos pasos básicos:

1.  [Incorporación de la validación de longitud de cadena][]
2.  [Actualización del cliente para admitir la validación][]
3.  [Prueba de validación de la longitud][]
4.  [Incorporación de un campo de marca de tiempo para CompleteDate][]
5.  [Actualización del cliente para mostrar el valor de CompleteDate][]

Este tutorial se basa en los pasos y en el código de ejemplo del tutorial anterior [Introducción][] o [Introducción a los datos][]. Antes de comenzar este tutorial, primero debe completar el tutorial [Introducción a los Servicios móviles][Introducción] o [Introducción a los datos][].

## <a name="string-length-validation"></a>Incorporación de la validación

[WACOM.INCLUDE [mobile-services-dotnet-backend-add-validation][]]

## <a name="update-client-validation"></a>Actualización del cliente

Ahora que el servicio móvil está configurado para validar los datos y enviar respuestas de error para una longitud de texto no válida, debe actualizar la aplicación para que pueda identificar las respuestas de error de la validación. El error se detectará como `MobileServiceInvalidOperationException` en la llamada de la aplicación cliente a `IMobileServiceTable<TodoItem].InsertAsync()`.

1.  En la ventana del Explorador de soluciones en Visual Studio, diríjase al proyecto de cliente y abra el archivo MainPage.xaml.cs. Agregue la siguiente instrucción **using** en ese archivo:

        using Windows.UI.Popups;
        using Newtonsoft.Json;
        using Newtonsoft.Json.Linq;

2.  En MainPage.xaml.cs, sustituya el método **InsertTodoItem** existente por el código siguiente:

        private async void InsertTodoItem(TodoItem todoItem)
        {
            // This code inserts a new TodoItem into the database. When the operation completes
            // and Mobile Services has assigned an Id, the item is added to the CollectionView
            MobileServiceInvalidOperationException invalidOpException = null;
            try
            {
                await todoTable.InsertAsync(todoItem);
                items.Add(todoItem);
            }
            catch(MobileServiceInvalidOperationException e)
            {
                invalidOpException = e;
            }
            if (invalidOpException != null)
            {
                string strJsonContent = await invalidOpException.Response.Content.ReadAsStringAsync();
                var responseContent = JObject.Parse(strJsonContent);
                MessageDialog errormsg = new MessageDialog(string.Format("{0} (HTTP {1})", 
                    (string)responseContent["message"],(int)invalidOpException.Response.StatusCode), 
                    invalidOpException.Message);
                var ignoreAsyncOpResult = errormsg.ShowAsync();
            }
        }

    Esta versión del método incluye control de errores para la excepción **MobileServiceInvalidOperationException** que muestra el mensaje de error deserializado del contenido de respuesta en un cuadro de diálogo de mensaje.

## <a name="test-length-validation"></a>Prueba de validación de la longitud

1.  En el Explorador de soluciones en Visual Studio, haga clic con el botón secundario en el proyecto de aplicación cliente y, a continuación, haga clic en **Establecer como proyecto de inicio**. A continuación, presione la tecla **F5** para iniciar la aplicación que hospeda el servicio localmente en IIS Express.

2.  Escriba el texto para un nuevo elemento TodoItem con una longitud superior a 10 caracteres y, a continuación, haga clic en **Guardar**.

    ![][]

3.  Se mostrará un cuadro de diálogo de mensaje similar al siguiente en respuesta al texto no válido.

    ![][1]

## <a name="add-timestamp"></a>Incorporación de un campo de marca de tiempo para CompleteDate

[WACOM.INCLUDE [mobile-services-dotnet-backend-add-completedate][]]

## <a name="update-client-timestamp"></a>Actualización del cliente para mostrar el valor de CompleteDate

El paso final consiste en actualizar el cliente para que muestre los nuevos datos de **CompleteDate**.

1.  En el Explorador de soluciones para Visual Studio, en el proyecto de cliente TodoList, abra el archivo MainPage.xaml y reemplace el elemento **CheckBoxComplete** por las definiciones siguientes. A continuación, guarde el archivo. Esto cambia el controlador de eventos en **CheckBoxComplete** para que podamos controlar el evento `click`. Además, agregamos un bloque de texto junto a la casilla y lo enlazamos a la marca de tiempo CompleteDate.

        <CheckBox Name="CheckBoxComplete" IsChecked="{Binding Complete, Mode=TwoWay}" 
          Click="CheckBoxComplete_Clicked" Content="{Binding Text}" Margin="10,5" VerticalAlignment="Center"/>
        <TextBlock Name="textCompleteDate" Text="{Binding CompleteDate}" VerticalAlignment="Center"/>

2.  En el Explorador de soluciones de Visual Studio, en el proyecto de cliente TodoList, abra el archivo MainPage.xaml.cs, sustituya el controlador de eventos `CheckBoxComplete_Checked` por el controlador de eventos `CheckBoxComplete_Clicked` que se muestra a continuación. De esta forma, podemos ver la fecha completa después de haber finalizado el elemento.

        private void CheckBoxComplete_Clicked(object sender, RoutedEventArgs e)
        {
            CheckBox cb = (CheckBox)sender;
            TodoItem item = cb.DataContext as TodoItem;
            UpdateCheckedTodoItem(item);
        }

3.  En el archivo MainPage.xaml.cs, sustituya la clase **TodoItem** existente por la siguiente definición, que incluye la nueva propiedad **CompleteDate** como un valor null.

        public class TodoItem
        {
            public string Id { get; set; }
            [JsonProperty(PropertyName = "text")]
            public string Text { get; set; }
            [JsonProperty(PropertyName = "complete")]
            public bool Complete { get; set; }        
            [JsonProperty(PropertyName = "CompleteDate")]
            public DateTime? CompleteDate { get; set; }
        }

    > [WACOM.NOTE] `DataMemberAttribute` solicita al cliente la asignación de la nueva propiedad `CompleteDate`de la aplicación a la columna `CompleteDate` definida en la tabla TodoItem. Al usar este atributo, la aplicación puede tener nombres de propiedad en objetos distintos a los nombres de columna de la base de datos SQL.

4.  En MainPage.xaml.cs, quite o convierta en comentario la función de cláusula `.Where` del método **RefreshTodoItems** existente para que los elementos TodoItems completados se incluyan en los resultados.

            // This query filters out completed TodoItems and 
            // items without a timestamp. 
            items = await todoTable
               //.Where(todoItem => todoItem.Complete == false)
               .ToCollectionAsync();

5.  En MainPage.xaml.cs, actualice el método **UpdateCheckedTodoItem** como se muestra a continuación para que los elementos se actualicen después una actualización y los elementos completados no se eliminen de la lista. A continuación, guarde el archivo.

        private async void UpdateCheckedTodoItem(TodoItem item)
        {
            // This code takes a freshly completed TodoItem and updates the database.
            await todoTable.UpdateAsync(item);
            //items.Remove(item);
            RefreshTodoItems();
        }

6.  En las ventanas del Explorador de soluciones de Visual Studio, haga clic con el botón secundario en **Solución** y, a continuación, en **Volver a generar solución** para recompilar el cliente y el servicio back-end de .NET. Compruebe que el proyecto se compile sin errores.

    ![][2]

7.  Presione la tecla **F5** para ejecutar la aplicación y el servicio cliente localmente. Agregue algunos elementos nuevos y haga clic para marcar algunos elementos completados con el fin de comprobar que la marca de tiempo **CompleteDate** se esté actualizando.

    ![][3]

8.  En el Explorador de soluciones de Visual Studio, haga clic con el botón secundario en el proyecto de servicio TodoList y haga clic en **Publicar**. Publique el servicio back-end de .NET en Microsoft Azure con el archivo de configuración de publicación que descargó del portal de Azure.

9.  Actualice el archivo App.xaml.cs del proyecto de cliente quitando el comentario de la conexión a la dirección del servicio móvil. Pruebe la aplicación en el back-end de .NET que está hospedado en su cuenta de Azure.

## <a name="next-steps"> </a>Pasos siguientes

Ahora que ha completado este tutorial, considere continuar con el tutorial final de la serie de datos: [Limitación de consultas con paginación][].

Los scripts de servidor también se usan al autorizar usuarios y para enviar notificaciones de inserción. Para obtener más información, consulte los siguientes tutoriales:

-   [Autorización de servicios de usuarios][]
    Aprenda a filtrar datos por el identificador de un usuario autenticado.

-   [Introducción a las notificaciones de inserción][]
    Aprenda a enviar una notificación de inserción muy básica a la aplicación.

-   [Referencia conceptual de Servicios móviles con .NET][]
    Obtenga más información sobre cómo utilizar Servicios móviles con .NET.

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs. -->

  [C# para Tienda Windows]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-validate-modify-data/ "C# para Tienda Windows"
  [JavaScript para Tienda Windows]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/ "JavaScript para Tienda Windows"
  [Windows Phone]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-phone-validate-modify-data/ "Windows Phone"
  [iOS]: /es-es/develop/mobile/tutorials/validate-modify-and-augment-data-ios "iOS"
  [Android]: /es-es/develop/mobile/tutorials/validate-modify-and-augment-data-android "Android"
  [HTML]: /es-es/develop/mobile/tutorials/validate-modify-and-augment-data-html "HTML"
  [Xamarin.iOS]: /es-es/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios "Xamarin.iOS"
  [Xamarin.Android]: /es-es/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android "Xamarin.Android"
  [Back-end de .NET]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-validate-modify-data/ "Back-end de .NET"
  [Back-end de JavaScript]: /es-es/documentation/articles/mobile-services-windows-store-dotnet-validate-modify-data-server-scripts/ "Back-end de JavaScript"
  [Incorporación de la validación de longitud de cadena]: #string-length-validation
  [Actualización del cliente para admitir la validación]: #update-client-validation
  [Prueba de validación de la longitud]: #test-length-validation
  [Incorporación de un campo de marca de tiempo para CompleteDate]: #add-timestamp
  [Actualización del cliente para mostrar el valor de CompleteDate]: #update-client-timestamp
  [Introducción]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/
  [Introducción a los datos]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/
  [mobile-services-dotnet-backend-add-validation]: ../includes/mobile-services-dotnet-backend-add-validation.md
  []: ./media/mobile-services-dotnet-backend-windows-store-dotnet-validate-modify-data/mobile-services-invalid-text-length.png
  [1]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-validate-modify-data/mobile-services-invalid-text-length-exception-dialog.png
  [mobile-services-dotnet-backend-add-completedate]: ../includes/mobile-services-dotnet-backend-add-completedate.md
  [2]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-validate-modify-data/mobile-services-rebuild-solution.png
  [3]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-validate-modify-data/mobile-services-final-local-app-run.png
  [Limitación de consultas con paginación]: /es-es/develop/mobile/tutorials/add-paging-to-data-dotnet
  [Autorización de servicios de usuarios]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-authorize-users-in-scripts/
  [Introducción a las notificaciones de inserción]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/
  [Referencia conceptual de Servicios móviles con .NET]: /es-es/develop/mobile/how-to-guides/work-with-net-client-library
