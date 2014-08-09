<properties linkid="develop-notificationhubs-tutorials-send-breaking-news-windowsdotnet" urlDisplayName="Breaking News" pageTitle="Notification Hubs Breaking News Tutorial" metaKeywords="" description="Learn how to use Azure Service Bus Notification Hubs to send breaking news notifications." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="" title="Use Notification Hubs to send breaking news" authors="ricksal" solutions="" manager="" editor="" />

Uso de los Centros de notificaciones para enviar noticias de última hora
========================================================================

[C\# para Tienda Windows](/es-es/manage/services/notification-hubs/breaking-news-dotnet "C# para Tienda Windows")[Windows Phone](/es-es/manage/services/notification-hubs/breaking-news-wp8 "Windows Phone")[iOS](/es-es/manage/services/notification-hubs/breaking-news-ios "iOS")

Este tema muestra cómo puede usar los Centros de notificaciones de Azure para difundir notificaciones de noticias de última hora en una aplicación de la Tienda Windows. Cuando lo complete, podrá registrar las categorías de noticias de última hora en las que esté interesado y recibir solo notificaciones de inserción para esas categorías. Este escenario es un patrón común para muchas aplicaciones en las que las notificaciones tienen que enviarse a grupos de usuarios que han mostrado previamente interés en ellas, por ejemplo, lectores RSS, aplicaciones para aficionados a la música, etc.

Los escenarios de difusión se habilitan mediante la inclusión de una o más *etiquetas* cuando se crea un registro en el centro de notificaciones. Cuando las notificaciones se envían a una etiqueta, todos los dispositivos registrados para la etiqueta recibirán la notificación. Puesto que las etiquetas son cadenas simples, no tendrán que aprovisionarse antes. Para obtener información sobre las etiquetas, consulte [Notification Hubs Guidance](http://msdn.microsoft.com/es-es/library/jj927170.aspx).

Este tutorial le guiará a través de estos pasos básicos para habilitar este escenario:

1.  [Incorporación de una selección de categorías a la aplicación](#adding-categories)
2.  [Registro de notificaciones](#register)
3.  [Envío de notificaciones desde su back-end](#send)
4.  [Ejecución de la aplicación y generación de notificaciones](#test-app)

Este tema se basa en la aplicación que creó en [Introducción a los Centros de notificaciones](/es-es/manage/services/notification-hubs/getting-started-windows-dotnet/). Antes de comenzar este tutorial, debe haber completado la [Introducción a los Centros de notificaciones](/es-es/manage/services/notification-hubs/getting-started-windows-dotnet/).

Incorporación de una selección de categorías a la aplicación
------------------------------------------------------------

El primer paso es agregar los elementos de la interfaz de usuario a la página principal existente que permiten al usuario seleccionar las categorías para registrar. Las categorías seleccionadas por un usuario se almacenan en el dispositivo. Cuando la aplicación se inicia, se crea un registro de dispositivos en el centro de notificaciones con las categorías seleccionadas como etiquetas.

1.  Abra el archivo de proyecto MainPage.xaml y, a continuación, copie el siguiente código en el elemento **Grid**:

         <Grid Margin="120, 58, 120, 80" >
             <Grid.RowDefinitions>
                 <RowDefinition />
                 <RowDefinition />
                 <RowDefinition />
                 <RowDefinition />
                 <RowDefinition />
             </Grid.RowDefinitions>
             <Grid.ColumnDefinitions>
                 <ColumnDefinition />
                 <ColumnDefinition />
             </Grid.ColumnDefinitions>
             <TextBlock Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"  TextWrapping="Wrap" Text="Breaking News" FontSize="42" VerticalAlignment="Top"/>
             <ToggleSwitch Header="Mundo" Name="WorldToggle" Grid.Row="1" Grid.Column="0"/>
             <ToggleSwitch Header="Política" Name="PoliticsToggle" Grid.Row="2" Grid.Column="0"/>
             <ToggleSwitch Header="Negocios" Name="BusinessToggle" Grid.Row="3" Grid.Column="0"/>
             <ToggleSwitch Header="Tecnología" Name="TechnologyToggle" Grid.Row="1" Grid.Column="1"/>
             <ToggleSwitch Header="Ciencia" Name="ScienceToggle" Grid.Row="2" Grid.Column="1"/>
             <ToggleSwitch Header="Deportes" Name="SportsToggle" Grid.Row="3" Grid.Column="1"/>
             <Button Name="SubscribeButton" Content="Subscribe" HorizontalAlignment="Center" Grid.Row="4" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click" />
         </Grid>

2.  En el proyecto, cree una nueva clase llamada **Notifications**, agregue el modificador **public** en la definición de clase y, a continuación, agregue las siguientes instrucciones **using** al nuevo archivo de código:

         using Windows.Networking.PushNotifications;
         using Microsoft.WindowsAzure.Messaging;
         using Windows.Storage;

3.  Agregue el siguiente código a la nueva clase **Notifications**:

         private NotificationHub hub;

         public Notifications()
         {
             hub = new NotificationHub("<hub name>", "<connection cadena con acceso de escucha>");
         }

         public async Task StoreCategoriesAndSubscribe(IEnumerable<string> categorías)
         {
             ApplicationData.Current.LocalSettings.Values["categorías"] = string.Join(",", categorías);
             await SubscribeToCategories(categorías);
         }

         public async Task SubscribeToCategories(IEnumerable<string> categorías)
         {
             var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
             await hub.RegisterNativeAsync(channel.Uri, categorías);
         }

    Esta clase usa el almacenamiento local para almacenar las categorías de noticias que este dispositivo ha de recibir. También contiene métodos para registrar estas categorías.

4.  En el código anterior, sustituya los marcadores de posición `<hub name>` y `<connection cadena con acceso de escucha>` por el nombre del centro de notificaciones y la cadena de conexión de *FirmaAccesoCompartidoEscuchaPredeterminada* que obtuvo anteriormente.

    **Nota:**

    Puesto que las credenciales que se distribuyen con una aplicación de cliente no son normalmente seguras, solo debe distribuir la clave para el acceso de escucha con la aplicación cliente. El acceso de escucha permite a la aplicación el registro de notificaciones, pero los registros existentes no pueden modificarse y las notificaciones no se pueden enviar. La clave de acceso completo se usa en un servicio back-end protegido para el envío de notificaciones y el cambio de registros existentes.

5.  En el archivo de proyecto App.xaml.cs, agregue la siguiente propiedad a la clase **App**:

         public Notifications notifications = new Notifications();

    Esta propiedad se usa para crear y obtener acceso a una instancia de **Notifications**.

6.  En MainPage.xaml.cs, agregue la siguiente línea:

         using Windows.UI.Popups;

7.  En el archivo de proyecto MainPage.xaml.cs, agregue el siguiente método:

         private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
         {
             var categories = new HashSet<string>();
             if (WorldToggle.IsOn) categories.Add("Mundo");
             if (PoliticsToggle.IsOn) categories.Add("Política");
             if (BusinessToggle.IsOn) categories.Add("Negocios");
             if (TechnologyToggle.IsOn) categories.Add("Tecnología");
             if (ScienceToggle.IsOn) categories.Add("Ciencia");
             if (SportsToggle.IsOn) categories.Add("Deportes");

             await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(categorías);

             var dialog = new MessageDialog("Suscrito a: " + string.Join(",", categorías));
             dialog.Commands.Add(new UICommand("OK"));
             await dialog.ShowAsync();
         }

    Este método crea una lista de categorías y usa la clase **Notifications** para almacenar la lista en el almacenamiento local y registrar las etiquetas correspondientes en el centro de notificaciones. Cuando se modifican las categorías, el registro vuelve a crearse con las nuevas categorías.

La aplicación ahora puede almacenar un conjunto de categorías en el almacenamiento local en el dispositivo y registrarse en el centro de notificaciones siempre que el usuario cambie la selección de categorías.

Registro de notificaciones
--------------------------

Estos pasos permiten registrar el centro de notificaciones en el inicio mediante las categorías que se han almacenado en el almacén local.

**Nota:**

Puesto que el URI de canal asignado por el servicio de notificaciones de inserción de Windows (WNS) puede cambiar en cualquier momento, debe registrar las notificaciones con frecuencia para evitar errores de notificación. En este ejemplo se registra la notificación cada vez que se inicia la aplicación. En las aplicaciones que se ejecutan con frecuencia, más de una vez al día, es posible que pueda omitir el registro para conservar el ancho de banda si pasa menos de un día del registro previo.

1.  Agregue el siguiente código a la clase **Notifications**:

         public IEnumerable<string> RetrieveCategories()
         {
             var categories = (string) ApplicationData.Current.LocalSettings.Values["categorías"];
             return categories != null 
         categories.Split(','): new string[0];
         }

    Esto devuelve las categorías definidas en la clase.

2.  Abra el archivo App.xaml.cs y agregue el modificador **async** al método **OnLaunched**.

3.  En el método **OnLaunched**, ubique y reemplace la llamada existente por el método **InitNotificationsAsync** con la siguiente línea de código:

         await notifications.SubscribeToCategories(notifications.RetrieveCategories());

    De esta forma, se garantiza que cada vez que la aplicación se inicia, se recuperan las categorías del almacenamiento local y se solicita un registro de estas categorías. El método **InitNotificationsAsync** se creó como parte del tutorial [Introducción a los Centros de notificaciones], pero no es necesario en este tema.

4.  En el archivo de proyecto MainPage.xaml.cs, agregue el siguiente código en el método *OnNavigatedTo*:

         var categories = ((App)Application.Current).notifications.RetrieveCategories();

         if (categories.Contains("Mundo")) WorldToggle.IsOn = true;
         if (categories.Contains("Política")) PoliticsToggle.IsOn = true;
         if (categories.Contains("Negocios")) BusinessToggle.IsOn = true;
         if (categories.Contains("Tecnología")) TechnologyToggle.IsOn = true;
         if (categories.Contains("Ciencia")) ScienceToggle.IsOn = true;
         if (categories.Contains("Deportes")) SportsToggle.IsOn = true;

    De esta forma, se actualiza la página principal según el estado de las categorías guardadas anteriormente.

La aplicación está ahora completa y puede almacenar un conjunto de categorías en el almacenamiento local del dispositivo usado para registrarse en el centro de notificaciones cuando el usuario cambie la selección de categorías. A continuación, definiremos un back-end que pueda enviar notificaciones de categorías a esta aplicación.

Envío de notificacionesEnvío de notificaciones desde el back-end
----------------------------------------------------------------

[WACOM.INCLUDE [notification-hubs-back-end](../includes/notification-hubs-back-end.md)]

Ejecución de la aplicación y generación de notificaciones
---------------------------------------------------------

1.  En Visual Studio, presione F5 para compilar e iniciar la aplicación.

    ![](./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-breakingnews-win1.png)

    Tenga en cuenta que la interfaz de usuario de la aplicación ofrece un conjunto de elementos de alternancia que le permite seleccionar las categorías a las que suscribirse.

2.  Habilite uno o más elementos de alternancia de las categorías y, a continuación, haga clic en **Subscribe**.

    La aplicación convierte las categorías seleccionadas en etiquetas y solicita un nuevo registro de dispositivo para las etiquetas seleccionadas al centro de notificaciones. Las categorías registradas se devuelven y se muestran en un cuadro de diálogo.

    ![](./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-reg-2.png)

3.  Envíe una nueva notificación desde el back-end de alguna de las siguientes formas:

    -   **Aplicación de consola:** Inicie la aplicación de consola.

    -   **Servicios móviles:** Haga clic en la pestaña **Scheduler**, en la tarea y, a continuación, en **Run once**.

    Las notificaciones para las categorías seleccionadas aparecen como notificaciones del sistema.

    ![](./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-toast-2.png)

Pasos siguientes
----------------

En este tutorial hemos aprendido cómo difundir noticias de última hora por categoría. Considere la posibilidad de llevar a cabo uno de los siguientes tutoriales que destacan otros escenarios de centros de notificaciones avanzados:

-   [Uso de los Centros de notificaciones para difundir noticias de última hora localizadas](/es-es/manage/services/notification-hubs/breaking-news-localized-dotnet/)

    Conozca cómo expandir la aplicación de noticias de última hora para habilitar el envío de notificaciones localizadas.

-   [Notificación a los usuarios con los Centros de notificaciones](/es-es/manage/services/notification-hubs/notify-users)

    Conozca cómo insertar notificaciones para usuarios autenticados específicos. Esta es una buena solución para enviar notificaciones solo a usuarios específicos.


<!-- Anchors. -->
[Add category selection to the app]: #adding-categories
[Register for notifications]: #register
[Send notifications from your back-end]: #send
[Run the app and generate notifications]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[1]: ./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-breakingnews-win1.png

[14]: ./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-toast-2.png


[19]: ./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-reg-2.png

<!-- URLs.-->
[get-started]: /es-es/manage/services/notification-hubs/getting-started-windows-dotnet/
[Use Notification Hubs to broadcast localized breaking news]: /es-es/manage/services/notification-hubs/breaking-news-localized-dotnet/ 
[Notify users with Notification Hubs]: /es-es/manage/services/notification-hubs/notify-users
[Mobile Service]: /es-es/develop/mobile/tutorials/get-started/
[Notification Hubs Guidance]: http://msdn.microsoft.com/es-es/library/jj927170.aspx
[Notification Hubs How-To for Windows Store]: http://msdn.microsoft.com/es-es/library/jj927172.aspx
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Azure Management Portal]: https://manage.windowsazure.com/
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
