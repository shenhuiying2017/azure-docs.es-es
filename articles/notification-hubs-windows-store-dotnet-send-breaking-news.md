<properties linkid="notification-hubs-windows-store-dotnet-send-breaking-news" pageTitle="Use Notification Hubs to send breaking news (Windows Universal)" metaKeywords="" description="Use  Azure Notification Hubs with tags in the registration to send breaking news to a universal Windows app." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="" title="Use Notification Hubs to send breaking news" authors="ricksal" solutions="" manager="" editor="" />

<properties linkid="notification-hubs-windows-store-dotnet-send-breaking-news" pagetitle="Use Notification Hubs to send breaking news (Windows Phone)" metakeywords description="Use  Azure Notification Hubs to use tag in registrations to send breaking news to a Windows Phone app." metacanonical services="notification-hubs" documentationcenter="Mobile" title="Use Notification Hubs to send breaking news" authors="glenga" solutions manager="dwrede" editor></properties>

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="ricksal" />

# Uso de los Centros de notificaciones para enviar noticias de última hora

<div class="dev-center-tutorial-selector sublanding"> 
        <a href="/es-es/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/" title="Windows Universal" class="current">Windows Universal</a><a href="/es-es/documentation/articles/notification-hubs-windows-phone-send-breaking-news/" title="Windows Phone">Windows Phone</a><a href="/es-es/documentation/articles/notification-hubs-ios-send-breaking-news/" title="iOS">iOS</a>
        <a href="/es-es/documentation/articles/notification-hubs-aspnet-backend-android-breaking-news/" title="Android">Android</a>
</div>

Este tema muestra cómo puede usar los Centros de notificaciones de Azure para difundir notificaciones de noticias de última hora en una aplicación (no Silverlight) de la Tienda Windows o de Windows Phone 8.1. Si su objetivo es Silverlight para Windows 8.1, consulte la versión de [Windows Phone][1]. Cuando lo complete, podrá registrar las categorías de noticias de última hora en las que esté interesado y recibir solo notificaciones de inserción para esas categorías. Este escenario es un patrón común para muchas aplicaciones en las que las notificaciones tienen que enviarse a grupos de usuarios que han mostrado previamente interés en ellas, por ejemplo, lectores RSS, aplicaciones para aficionados a la música, etc.

Los escenarios de difusión se habilitan mediante la inclusión de una o más *etiquetas* cuando se crea un registro en el centro de notificaciones. Cuando las notificaciones se envían a una etiqueta, todos los dispositivos registrados para la etiqueta recibirán la notificación. Puesto que las etiquetas son cadenas simples, no tendrán que aprovisionarse antes. Para obtener información sobre las etiquetas, consulte [Notification Hubs Guidance][Notification Hubs Guidance].

Este tutorial le guiará a través de estos pasos básicos para habilitar este escenario:

1.  [Incorporación de una selección de categorías a la aplicación][Incorporación de una selección de categorías a la aplicación]
2.  [Registro de notificaciones][Registro de notificaciones]
3.  [Envío de notificaciones desde su back-end][Envío de notificaciones desde su back-end]
4.  [Ejecución de la aplicación y generación de notificaciones][Ejecución de la aplicación y generación de notificaciones]

Este tema se basa en la aplicación que creó en [Introducción a los Centros de notificaciones][Introducción a los Centros de notificaciones]. Antes de comenzar este tutorial, debe haber completado la [Introducción a los Centros de notificaciones][Introducción a los Centros de notificaciones].

## <a name="adding-categories"></a>Incorporación de una selección de categorías a la aplicación

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
            <ToggleSwitch Header="World" Name="WorldToggle" Grid.Row="1" Grid.Column="0"/>
            <ToggleSwitch Header="Politics" Name="PoliticsToggle" Grid.Row="2" Grid.Column="0"/>
            <ToggleSwitch Header="Business" Name="BusinessToggle" Grid.Row="3" Grid.Column="0"/>
            <ToggleSwitch Header="Technology" Name="TechnologyToggle" Grid.Row="1" Grid.Column="1"/>
            <ToggleSwitch Header="Science" Name="ScienceToggle" Grid.Row="2" Grid.Column="1"/>
            <ToggleSwitch Header="Sports" Name="SportsToggle" Grid.Row="3" Grid.Column="1"/>
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
            hub = new NotificationHub("<hub name>", "<connection string with listen access>");
        }

        public async Task StoreCategoriesAndSubscribe(IEnumerable<string> categories)
        {
            ApplicationData.Current.LocalSettings.Values["categories"] = string.Join(",", categories);
            await SubscribeToCategories(categories);
        }

        public async Task SubscribeToCategories(IEnumerable<string> categories)
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
            await hub.RegisterNativeAsync(channel.Uri, categories);
        }

    Esta clase usa el almacenamiento local para almacenar las categorías de noticias que este dispositivo ha de recibir. También contiene métodos para registrar estas categorías.

4.  En el código anterior, reemplace los marcadores de posición `<hub name>` y `<connection string with listen access>` por el nombre de su centro de notificaciones y la cadena de conexión para *DefaultListenSharedAccessSignature* que obtuvo anteriormente.

    <div class="dev-callout"><strong>Nota:</strong> 
    <p>Puesto que las credenciales que se distribuyen con una aplicaci&oacute;n de cliente no son normalmente seguras, solo debe distribuir la clave para el acceso de escucha con la aplicaci&oacute;n cliente. El acceso de escucha permite a la aplicaci&oacute;n el registro de notificaciones, pero los registros existentes no pueden modificarse y las notificaciones no se pueden enviar. La clave de acceso completo se usa en un servicio back-end protegido para el env&iacute;o de notificaciones y el cambio de registros existentes.</p>
</div>

5.  En el archivo de proyecto App.xaml.cs, agregue la siguiente propiedad a la clase **App**:

        public Notifications notifications = new Notifications();

    Esta propiedad se usa para crear y obtener acceso a una instancia de **Notifications**.

6.  En MainPage.xaml.cs, agregue la siguiente línea:

        using Windows.UI.Popups;

7.  En el archivo de proyecto MainPage.xaml.cs, agregue el siguiente método:

        private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
        {
            var categories = new HashSet<string>();
            if (WorldToggle.IsOn) categories.Add("World");
            if (PoliticsToggle.IsOn) categories.Add("Politics");
            if (BusinessToggle.IsOn) categories.Add("Business");
            if (TechnologyToggle.IsOn) categories.Add("Technology");
            if (ScienceToggle.IsOn) categories.Add("Science");
            if (SportsToggle.IsOn) categories.Add("Sports");

            await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(categories);

            var dialog = new MessageDialog("Subscribed to: " + string.Join(",", categories));
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }

    Este método crea una lista de categorías y usa la clase **Notifications** para almacenar la lista en el almacenamiento local y registrar las etiquetas correspondientes en el centro de notificaciones. Cuando se modifican las categorías, el registro vuelve a crearse con las nuevas categorías.

La aplicación ahora puede almacenar un conjunto de categorías en el almacenamiento local en el dispositivo y registrarse en el centro de notificaciones siempre que el usuario cambie la selección de categorías.

## <a name="register"></a>Registro de notificaciones

Estos pasos permiten registrar el centro de notificaciones en el inicio mediante las categorías que se han almacenado en el almacén local.

<div class="dev-callout"><strong>Nota:</strong> 
    <p>Puesto que el URI de canal asignado por el servicio de notificaciones de inserci&oacute;n de Windows (WNS) puede cambiar en cualquier momento, debe registrar las notificaciones con frecuencia para evitar errores de notificaci&oacute;n. En este ejemplo se registra la notificaci&oacute;n cada vez que se inicia la aplicaci&oacute;n. En las aplicaciones que se ejecutan con frecuencia, m&aacute;s de una vez al d&iacute;a, es posible que pueda omitir el registro para conservar el ancho de banda si pasa menos de un d&iacute;a del registro previo.</p>
</div>

1.  Agregue el siguiente código a la clase **Notifications**:

        public IEnumerable<string> RetrieveCategories()
        {
            var categories = (string) ApplicationData.Current.LocalSettings.Values["categories"];
            return categories != null ? categories.Split(','): new string[0];
        }

    Esto devuelve las categorías definidas en la clase.

2.  Abra el archivo App.xaml.cs y agregue el modificador **async** al método **OnLaunched**.

3.  En el método **OnLaunched**, ubique y reemplace la llamada existente por el método **InitNotificationsAsync** con la siguiente línea de código:

        await notifications.SubscribeToCategories(notifications.RetrieveCategories());

    De esta forma, se garantiza que cada vez que la aplicación se inicia, se recuperan las categorías del almacenamiento local y se solicita un registro de estas categorías. El método **InitNotificationsAsync** se creó como parte del tutorial [Introducción a los Centros de notificaciones], pero no es necesario en este tema.

4.  En el archivo de proyecto MainPage.xaml.cs, agregue el siguiente código en el método *OnNavigatedTo*:

        var categories = ((App)Application.Current).notifications.RetrieveCategories();

        if (categories.Contains("World")) WorldToggle.IsOn = true;
        if (categories.Contains("Politics")) PoliticsToggle.IsOn = true;
        if (categories.Contains("Business")) BusinessToggle.IsOn = true;
        if (categories.Contains("Technology")) TechnologyToggle.IsOn = true;
        if (categories.Contains("Science")) ScienceToggle.IsOn = true;
        if (categories.Contains("Sports")) SportsToggle.IsOn = true;

    De esta forma, se actualiza la página principal según el estado de las categorías guardadas anteriormente.

La aplicación está ahora completa y puede almacenar un conjunto de categorías en el almacenamiento local del dispositivo usado para registrarse en el centro de notificaciones cuando el usuario cambie la selección de categorías. A continuación, definiremos un back-end que pueda enviar notificaciones de categorías a esta aplicación.

## <a name="send"></a><span class="short-header">Envío de notificaciones</span>Envío de notificaciones desde el back-end

[WACOM.INCLUDE [notification-hubs-back-end](../includes/notification-hubs-back-end.md)]

## <a name="test-app"></a>Ejecución de la aplicación y generación de notificaciones

1.  En Visual Studio, presione F5 para compilar e iniciar la aplicación.

    ![][0]

    Tenga en cuenta que la interfaz de usuario de la aplicación ofrece un conjunto de elementos de alternancia que le permite seleccionar las categorías a las que suscribirse.

2.  Habilite uno o más elementos de alternancia de las categorías y, a continuación, haga clic en **Subscribe**.

    La aplicación convierte las categorías seleccionadas en etiquetas y solicita un nuevo registro de dispositivo para las etiquetas seleccionadas al centro de notificaciones. Las categorías registradas se devuelven y se muestran en un cuadro de diálogo.

    ![][2]

3.  Envíe una nueva notificación desde el back-end de alguna de las siguientes formas:

    -   **Aplicación de consola:** inicie la aplicación de consola.

    -   **Java/PHP:** ejecute su aplicación o script.

    Las notificaciones para las categorías seleccionadas aparecen como notificaciones del sistema.

    ![][3]

## <a name="next-steps"> </a>Pasos siguientes

En este tutorial hemos aprendido cómo difundir noticias de última hora por categoría. Considere la posibilidad de llevar a cabo uno de los siguientes tutoriales que destacan otros escenarios de centros de notificaciones avanzados:

-   [Uso de los Centros de notificaciones para difundir noticias de última hora localizadas][Uso de los Centros de notificaciones para difundir noticias de última hora localizadas]

    Conozca cómo expandir la aplicación de noticias de última hora para habilitar el envío de notificaciones localizadas.

-   [Notificación a los usuarios con los Centros de notificaciones][Notificación a los usuarios con los Centros de notificaciones]

    Conozca cómo insertar notificaciones para usuarios autenticados específicos. Esta es una buena solución para enviar notificaciones solo a usuarios específicos.


  [Windows Phone]: /es-es/documentation/articles/notification-hubs-windows-phone-send-breaking-news/ "Windows Phone"
  [1]: /es-es/documentation/articles/notification-hubs-ios-send-breaking-news
  [Notification Hubs Guidance]: http://msdn.microsoft.com/es-es/library/jj927170.aspx
  [Incorporación de una selección de categorías a la aplicación]: #adding-categories
  [Registro de notificaciones]: #register
  [Envío de notificaciones desde su back-end]: #send
  [Ejecución de la aplicación y generación de notificaciones]: #test-app
  [Introducción a los Centros de notificaciones]: /es-es/manage/services/notification-hubs/getting-started-windows-dotnet/
  [notification-hubs-back-end]: ../includes/notification-hubs-back-end.md
  [0]: ./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-breakingnews-win1.png
  [2]: ./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-reg-2.png
  [3]: ./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-toast-2.png
  [Uso de los Centros de notificaciones para difundir noticias de última hora localizadas]: /es-es/manage/services/notification-hubs/breaking-news-localized-dotnet/
  [Notificación a los usuarios con los Centros de notificaciones]: /es-es/manage/services/notification-hubs/notify-users
