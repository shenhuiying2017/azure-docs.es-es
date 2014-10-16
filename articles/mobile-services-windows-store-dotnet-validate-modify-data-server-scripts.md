<properties linkid="develop-mobile-tutorials-validate-modify-and-augment-data-dotnet" urlDisplayName="Validate and Modify Data" pageTitle="User server scripts to validate and modify data (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to use server scripts to validate, modify, and augment data for your Windows Store app with Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using server scripts" authors="glenga" solutions="" manager="" editor="" />

Validación y modificación de datos en los Servicios móviles mediante los scripts del servidor
=============================================================================================

<div class="dev-center-tutorial-selector sublanding">
<a href="/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet" title="Windows Store C#" class="current">C\# para Tienda Windows</a>
<a href="/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-js" title="Windows Store JavaScript">JavaScript para Tienda Windows</a>
<a href="/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-wp8" title="Windows Phone">Windows Phone</a>
<a href="/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-ios" title="iOS">iOS</a>
<a href="/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-android" title="Android">Android</a><a href="/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-html" title="HTML">HTML</a>
<a href="/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android" title="Xamarin.Android">Xamarin.Android</a></div>


<div class="dev-center-tutorial-subselector">
	<a href="/es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-validate-modify-data/" title=".NET backend" class="current">.NET backend</a> | 
	<a href="es-es/documentation/articles/mobile-services-windows-store-dotnet-validate-modify-data-server-scripts/"  title="JavaScript backend" class="current">JavaScript backend</a>
</div>



<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>En este tema se muestra cómo aprovechar los scripts del servidor en Servicios móviles de Azure. Dichos scripts se registran en un servicio móvil y pueden usarse para realizar una gran variedad de operaciones en los datos que se han insertado y actualizado, incluidas la modificación y validación de los datos. En este tutorial, definirá y registrará scripts de servidor que sirven para validar y modificar datos. Dado que el comportamiento de los scripts del servidor suele afectar al cliente, también actualizará la aplicación de la Tienda Windows para que se beneficie de estos nuevos comportamientos.</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Validate-and-Modify-Data-with-Server-Scripts-in-Windows-Azure-Mobile-Services" target="_blank" class="label">Ver el tutorial (en inglés)</a> <a style="background-image: url('/media/devcenter/mobile/videos/validate-data-windows-store-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Validate-and-Modify-Data-with-Server-Scripts-in-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Reproducir vídeo (en inglés)</span></a> <span class="time">9:54</span></div>
</div>

Este tutorial le guiará a través de estos pasos básicos:

1.  [Incorporación de la validación de longitud de cadena](#string-length-validation)
2.  [Actualización del cliente para admitir la validación](#update-client-validation)
3.  [Incorporación de una marca de tiempo al insertar](#add-timestamp)
4.  [Actualización del cliente para mostrar la marca de tiempo](#update-client-timestamp)

Este tutorial se basa en los pasos y en la aplicación de muestra del tutorial anterior, [Introducción a los datos](/en-us/develop/mobile/tutorials/get-started-with-data-dotnet). Antes de comenzar este tutorial, primero debe completar [Introducción a los datos](/en-us/develop/mobile/tutorials/get-started-with-data-dotnet).

<a name="string-length-validation"></a>
Incorporación de la validación
------------------------------

Siempre es conveniente validar la longitud de los datos enviados por los usuarios. En primer lugar, registre un script que valide la longitud de los datos de cadena enviados al servicio móvil y que rechace las cadenas demasiado largas, en este caso con más de 10 caracteres.

1.  Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com/), haga clic en **Servicios móviles** y, a continuación, en su aplicación.

    ![](./media/mobile-services-windows-store-dotnet-validate-modify-data-server-scripts/mobile-services-selection.png)

2.  Haga clic en la pestańa **Data** y, a continuación, haga clic en la tabla **TodoItem**.

    ![](./media/mobile-services-windows-store-dotnet-validate-modify-data-server-scripts/mobile-portal-data-tables.png)

3.  Haga clic en **Script** y, a continuación, seleccione la operación **Insert**.

    ![](./media/mobile-services-windows-store-dotnet-validate-modify-data-server-scripts/mobile-insert-script-users.png)

4.  Sustituya el script existente por la siguiente función y, a continuación, haga clic en **Save**.

         function insert(item, user, request) {
             if (item.text.length > 10) {
                 request.respond(statusCodes.BAD_REQUEST, 'Text length must be under 10');
             } else {
                 request.execute();
             }
         }

    Este script comprueba la longitud de la propiedad **TodoItem.text** y envía una respuesta de error cuando esta sobrepasa los 10 caracteres. En caso de no sobrepasarlos, se llama al método **execute** para completar la inserción.

    <div class="dev-callout"><b>Nota:</b>

    <p>Puede quitar un script registrado en la pestańa <b>Script</b> haciendo clic en <b>Clear</b> y, a continuación, en <b>Save</b>.</p>
	</div>

<a name="update-client-validation"></a>
Actualización del cliente
-------------------------

Ahora que el servicio móvil puede validar los datos y enviar respuestas de error, debe actualizar la aplicación para que pueda identificar los errores de la validación.

1.  En Visual Studio 2012 Express para Windows 8, abra el proyecto que modificó cuando completó el tutorial [Introducción a los datos](/en-us/develop/mobile/tutorials/get-started-with-data-dotnet).

2.  Presione la tecla **F5** para ejecutar la aplicación, escriba un texto que sobrepase los 10 caracteres en **Insert a TodoItem** y haga clic en **Save**.

    Observe que la aplicación produce una excepción **MobileServiceInvalidOperationException** no controlada como resultado de la respuesta 400 (solicitud incorrecta) devuelta por el servicio móvil.   

3.  Abra el archivo MainPage.xaml.cs y agregue la siguiente instrucción **using**:

        using Windows.UI.Popups;

4.  Reemplace el método **InsertTodoItem** existente por lo siguiente:

         private async void InsertTodoItem(TodoItem todoItem)
         {
             // Este código inserta un nuevo elemento TodoItem en la base de datos.
             // Cuando la operación se completa y Servicios móviles ha
             // asignado un identificador, el elemento se agrega a la colección.
             try
             {
                 await todoTable.InsertAsync(todoItem);
                 items.Add(todoItem);
             }
             catch (MobileServiceInvalidOperationException e)
             {
                 MessageDialog errormsg = new MessageDialog(e.Message, 
                     string.Format("{0} (HTTP {1})",                     
                     e.Response.ReasonPhrase,
                     (int)e.Response.StatusCode));
                 var ignoreAsyncOpResult = errormsg.ShowAsync();
             }
         }

     Esta versión del método incluye control de errores para la excepción **MobileServiceInvalidOperationException**, que muestra la respuesta de error en un elemento emergente.

<a name="add-timestamp"></a>
Incorporación de una marca de tiempo
------------------------------------

Hemos aplicado las tareas anteriores para validar e insertar y, en consecuencia, aceptar o rechazar. Ahora, actualizará los datos insertados mediante un script de servidor que agrega una propiedad de marca de tiempo al objeto antes de insertarse.

<div class="dev-callout"><b>Nota:</b>

<p>La propiedad de marca de tiempo **createdAt** que se muestra en este ejemplo es redundante en este caso. Servicios móviles crea automáticamente una propiedad del sistema **\_\_createdAt** para cada tabla. Dicha propiedad del sistema podría usarse en su aplicación con solo agregar el siguiente miembro a la clase TodoItem:</p>
</div>

``` {}
[JsonProperty(PropertyName = "__createdAt")]
public DateTime createdAt { set; get; }
```

1.  En la pestańa **Scripts** del [Portal de administración](https://manage.windowsazure.com/), reemplace el script **Insert** actual por la siguiente función y, a continuación, haga clic en **Save**.

         function insert(item, user, request) {
             if (item.text.length > 10) {
                 request.respond(statusCodes.BAD_REQUEST, 'Text length must be under 10');
             } else {
                 item.createdAt = new Date();
                 request.execute();
             }
         }

    Esta función aumenta el script de inserción anterior mediante la incorporación de una nueva propiedad de marca de tiempo **createdAt** al objeto antes de que la llamada a **request**.**execute** lo inserte.

    <div class="dev-callout"><b>Nota:</b>

    <p>El esquema dinámico debe habilitarse la primera vez que se ejecute este script de inserción. Al tener habilitado dicho esquema, Servicios móviles agrega automáticamente la columna <b>createdAt</b> a la tabla <b>TodoItem</b> en la primera ejecución. El esquema dinámico está habilitado de forma predeterminada para un nuevo servicio móvil y debe deshabilitarse antes de publicar la aplicación en la Tienda Windows.</p>
	</div>

2.  En Visual Studio, presione la tecla **F5** para ejecutar la aplicación y, a continuación, escriba un texto (con menos de 10 caracteres) en **Insert a TodoItem** y haga clic en **Guardar**.

    Observe que la nueva marca de tiempo no aparece en la interfaz de usuario de la aplicación.

3.  Nuevamente en el Portal de administración, haga clic en la pestańa **Browse** en la tabla **todoitem**.

    Observe que ahora aparece una columna **createdAt** y el nuevo elemento insertado tiene un valor de marca de tiempo.

A continuación, debe actualizar la aplicación de la Tienda Windows para que muestre esta nueva columna.

<a name="update-client-timestamp"></a>
Nueva actualización del cliente
-------------------------------

El cliente Servicios móviles omitirá los datos de cualquier respuesta que no pueda serializar en propiedades en el tipo definido. El paso final consiste en actualizar el cliente para que muestre estos nuevos datos.

1.  En Visual Studio, abra el archivo MainPage.xaml.cs y, a continuación, reemplace la clase **TodoItem** existente por la siguiente definición:

         public class TodoItem
         {
             public string Id { get; set; }

             [JsonProperty(PropertyName = "text")]
             public string Text { get; set; }

             [JsonProperty(PropertyName = "complete")]
             public bool Complete { get; set; }
            
             [JsonProperty(PropertyName = "createdAt")]
             public DateTime
         CreatedAt { get; set; }
         }

    Esta nueva definición de clase incluye la nueva propiedad de marca de tiempo como tipo DateTime que acepta valores NULL.

    <div class="dev-callout"><b>Nota:</b>

    <p>El atributo `DataMemberAttribute` indica al cliente que asigne la nueva propiedad `CreatedAt` de la aplicación a la columna `createdAt` definida en la tabla TodoItem, que tiene un uso distinto de mayúsculas y minúsculas. Al usar este atributo, la aplicación puede tener nombres de propiedad en objetos distintos a los nombres de columna de la base de datos SQL. Sin el atributo en cuestión, se produce un error debido a las diferencias en el uso de mayúsculas y minúsculas.</p>
	</div>

2.  Agregue el siguiente elemento XAML justo debajo del elemento **CheckBoxComplete** en el archivo MainPage.xaml:

         <TextBlock Name="WhenCreated" Text="{Binding CreatedAt}" VerticalAlignment="Center"/>

    Esto muestra la nueva propiedad **CreatedAt** en un cuadro de texto. 

3.  Presione la tecla **F5** para ejecutar la aplicación.

    Observe que la marca de tiempo solo se muestra para los elementos insertados después de haber actualizado el script de inserción.

4.  Reemplace el método **RefreshTodoItems** existente por el siguiente código:

         private async void RefreshTodoItems()
         {
             // Esta consulta filtra TodoItems completados y 
             // los elementos sin una marca de tiempo. 
             items = await todoTable
                .Where(todoItem => todoItem.Complete == false
                    && todoItem.CreatedAt != null)
                .ToCollectionAsync();

             ListItems.ItemsSource = items;
         }

    Este método actualiza la consulta de forma que se filtren también los elementos que no tengan un valor de marca de tiempo.

5.  Presione la tecla **F5** para ejecutar la aplicación.

    Observe que todos los elementos creados sin un valor de marca de tiempo desaparecen de la interfaz de usuario.

Ha completado este tutorial de trabajo con datos.

<a name="next-steps"> </a>
Pasos siguientes
----------------

Ahora que ha completado este tutorial, considere continuar con el tutorial final de la serie de datos: [Limitación de consultas con paginación](/en-us/develop/mobile/tutorials/add-paging-to-data-dotnet).

Los scripts de servidor también se usan al autorizar usuarios y para enviar notificaciones push. Para obtener más información, consulte los siguientes tutoriales:

-   [Autorización de usuarios con scripts](/en-us/develop/mobile/tutorials/authorize-users-in-scripts-dotnet)
    

    Aprenda a filtrar datos basándose en el identificador de un usuario autenticado.

-   [Introducción a las notificaciones de inserción](/en-us/develop/mobile/tutorials/get-started-with-push-dotnet)
    
    Aprenda a enviar una notificación de inserción muy básica a la aplicación.

-   [Referencia del script del servidor de Servicios móviles](http://go.microsoft.com/fwlink/?LinkId=262293)
    
    Obtenga más información acerca del registro y uso de scripts de servidor.

-   [Referencia conceptual de Servicios móviles con .NET](/en-us/develop/mobile/how-to-guides/work-with-net-client-library)
    
    Obtenga más información sobre cómo utilizar Servicios móviles con .NET.


