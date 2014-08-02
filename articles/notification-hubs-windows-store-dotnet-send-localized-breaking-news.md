<properties linkid="develop-notificationhubs-tutorials-send-localized-breaking-news-windowsdotnet" urlDisplayName="Localized Breaking News" pageTitle="Notification Hubs Localized Breaking News Tutorial" metaKeywords="" description="Learn how to use Azure Service Bus Notification Hubs to send localized breaking news notifications." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="" title="Use Notification Hubs to send localized breaking news" authors="ricksal" solutions="" manager="" editor="" />

Uso de los Centros de notificaciones para enviar noticias de última hora localizadas
====================================================================================

[C\# para Tienda Windows](/en-us/manage/services/notification-hubs/breaking-news-localized-dotnet "C# para Tienda Windows")[iOS](/en-us/manage/services/notification-hubs/breaking-news-localized-ios "iOS")

En este tema se explica cómo usar la característica **template** de los Centros de notificaciones de Azure para difundir notificaciones de noticias de última hora localizadas por idioma y dispositivo. Para empezar este tutorial, se usa la aplicación de la Tienda Windows creada en [Uso de los Centros de notificaciones para enviar noticias de última hora](/en-us/manage/services/notification-hubs/breaking-news-dotnet). Una vez completado, podrá registrarse en las categorías de su interés, especificar un idioma para la recepción de notificaciones y recibir solo notificaciones de inserción para las categorías seleccionadas en dicho idioma.

Este tutorial le guiará a través de estos pasos básicos para habilitar este escenario:

1.  [Conceptos de las plantillas](#concepts)
2.  [Interfaz de usuario de la aplicación](#ui)
3.  [Creación de la aplicación cliente de la Tienda Windows](#building-client)
4.  [Envío de notificaciones desde su back-end](#send)

Este escenario se divide en dos partes:

-   La aplicación de la Tienda Windows permite que los dispositivos cliente especifiquen un idioma y se suscriban a distintas categorías de noticias de última hora.

-   El back-end difunde las notificaciones mediante las características de **tag** y **template** de los Centros de notificaciones de Azure.

Requisitos previos
------------------

Debe haber completado el tutorial [Uso de los Centros de notificaciones para enviar noticias de última hora](/en-us/manage/services/notification-hubs/breaking-news-dotnet) y tener disponible el código correspondiente, ya que este tutorial se basa directamente en dicho código.

También necesita Visual Studio 2012.

conceptosConceptos de las plantillas
------------------------------------

En [Uso de los Centros de notificaciones para enviar noticias de última hora](/en-us/manage/services/notification-hubs/breaking-news-dotnet) se creó una aplicación que usaba las **etiquetas** para suscribirse a las notificaciones en distintas categorías de noticias. Sin embargo, muchas aplicaciones se destinan a diversos mercados, por lo que es necesario localizarlas. Esto significa que el contenido de las notificaciones en sí debe localizarse y entregarse al conjunto de dispositivos correcto. En este tema mostraremos cómo usar la característica **template** de los Centros de notificaciones para una sencilla difusión de las notificaciones de noticias de última hora localizadas.

Nota: una forma de enviar notificaciones localizadas es crear varias versiones de cada etiqueta. Por ejemplo, para admitir inglés, francés y mandarín, necesitaríamos tres etiquetas distintas para las noticias del mundo: "world\_en", "world\_fr" y "world\_ch". A continuación, tendríamos que enviar una versión localizada de las noticias del mundo a cada una de estas etiquetas. En este tema usaremos las plantillas para evitar un uso excesivo de las etiquetas y el requisito de enviar varios mensajes.

En un nivel superior, las plantillas son una forma de especificar cómo debe recibir una notificación un dispositivo concreto. La plantilla especifica el formato de carga exacto mediante la referencia a propiedades que forman parte del mensaje enviado por el back-end de la aplicación. En nuestro caso, enviaremos un mensaje independiente de la configuración regional que contiene todos los idiomas compatibles:

    {
        "News_English": "...",
        "News_French": "...",
        "News_Mandarin": "..."
    }

A continuación, nos aseguraremos de que los dispositivos se registren en una plantilla que haga referencia a la propiedad correcta. Por ejemplo, una aplicación de la Tienda Windows que desea recibir un simple mensaje del sistema se registrará en la plantilla siguiente:

	<toast data-morhtml="true">
	  <visual data-morhtml="true">
		<binding data-morhtml="true" template=\"ToastText01\">
		  <text data-morhtml="true" id=\"1\">$(News_English)</text>
		</binding>
	  </visual>
	</toast>


Las plantillas son una característica muy importante; para obtener más información al respecto, consulte el artículo [Información general acerca de los centros de notificaciones de Azure](http://msdn.microsoft.com/en-us/library/jj927170.aspx). Asimismo, podrá consultar información de referencia sobre el lenguaje de expresión de las plantillas en los [procedimientos de los Centros de notificaciones para la Tienda Windows](http://msdn.microsoft.com/en-us/library/jj927172.aspx) (en inglés).

IU de la aplicaciónInterfaz de usuario de la aplicación
-------------------------------------------------------

Ahora modificaremos la aplicación de noticias de última hora que creó en el tema [Uso de los Centros de notificaciones para enviar noticias de última hora](/en-us/manage/services/notification-hubs/breaking-news-dotnet) a fin de enviar noticias de este tipo localizadas con la utilización de las plantillas.

Para adaptar las aplicaciones cliente de forma que reciban mensajes localizados, debe reemplazar los registros *nativos* (es decir, aquellos con los que se especifica una plantilla) por registros de plantilla.

En la aplicación de la Tienda Windows:

Modifique el archivo MainPage.xaml para que incluya un cuadro combinado de configuración regional:

    <Grid Margin="120, 58, 120, 80"  
            Background="{StaticResource ApplicationPageBackgroundThemeBrush}">
        <Grid.RowDefinitions>
            <RowDefinition />
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
        <ComboBox Name="Locale" HorizontalAlignment="Left" VerticalAlignment="Center" Width="200" Grid.Row="1" Grid.Column="0">
            <x:String>English</x:String>
            <x:String>French</x:String>
            <x:String>Mandarin</x:String>
        </ComboBox>
        <ToggleSwitch Header="World" Name="WorldToggle" Grid.Row="2" Grid.Column="0"/>
        <ToggleSwitch Header="Politics" Name="PoliticsToggle" Grid.Row="3" Grid.Column="0"/>
        <ToggleSwitch Header="Business" Name="BusinessToggle" Grid.Row="4" Grid.Column="0"/>
        <ToggleSwitch Header="Technology" Name="TechnologyToggle" Grid.Row="2" Grid.Column="1"/>
        <ToggleSwitch Header="Science" Name="ScienceToggle" Grid.Row="3" Grid.Column="1"/>
        <ToggleSwitch Header="Sports" Name="SportsToggle" Grid.Row="4" Grid.Column="1"/>
        <Button Content="Subscribe" HorizontalAlignment="Center" Grid.Row="5" Grid.Column="0" Grid.ColumnSpan="2" Click="Button_Click" />
    </Grid>

IU de la aplicaciónCreación de la aplicación cliente de la Tienda Windows
-------------------------------------------------------------------------

1.  En la clase Notifications, agregue un parámetro de configuración regional a los métodos *StoreCategoriesAndSubscribe* y *SubscribeToCategories*.

         public async Task StoreCategoriesAndSubscribe(string locale, IEnumerable<string> categories)
         {
             ApplicationData.Current.LocalSettings.Values["categorías"] = string.Join(",", categorías);
             ApplicationData.Current.LocalSettings.Values["locale"] = locale;
             await SubscribeToCategories(locale, categories);
         }

         public async Task SubscribeToCategories(string locale, IEnumerable<string> categories)
         {
             var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
             var template = String.Format(@"<toast><visual><binding template=""ToastText01""><text id=""1"">$(News_{0})</text></binding></visual></toast>", locale);

             await hub.RegisterTemplateAsync(channel.Uri, template, "newsTemplate", categories);
         }

    Observe que en lugar de llamar al método *RegisterNativeAsync*, llamamos a *RegisterTemplateAsync*: estamos registrando un formato de notificación específico en el que la plantilla depende de la configuración regional. Asimismo, proporcionamos un nombre para la plantilla ("newsTemplate"), ya que es posible que queramos registrar más de una (por ejemplo, una para las notificaciones del sistema y otra de iconos) y es necesario asignarles un nombre para poder actualizarlas o eliminarlas.

    Tenga en cuenta que, si un dispositivo registra varias plantillas con la misma etiqueta, el envío de un mensaje destinado a dicha etiqueta dará como resultado la entrega de varias notificaciones al dispositivo (una para cada plantilla). Este comportamiento resulta útil cuando un mismo mensaje lógico debe dar como resultado varias notificaciones visuales que muestren, por ejemplo, tanto un distintivo como una notificación del sistema en una aplicación de la Tienda Windows.

2.  Agregue el método siguiente para recuperar la configuración regional almacenada:

         public string RetrieveLocale()
         {
             var locale = (string) ApplicationData.Current.LocalSettings.Values["locale"];
             return locale != null ? locale : "English";
         }

3.  En el archivo MainPage.xaml.cs, actualice el controlador de clics de botón; para ello, recupere el valor actual del cuadro combinado de configuración regional y proporciónelo a la llamada a la clase Notifications, tal y como se muestra a continuación:

          var locale = (string)Locale.SelectedItem;
                
          var categories = new HashSet<string>();
          if (WorldToggle.IsOn) categories.Add("World");
          if (PoliticsToggle.IsOn) categories.Add("Politics");
          if (BusinessToggle.IsOn) categories.Add("Business");
          if (TechnologyToggle.IsOn) categories.Add("Technology");
          if (ScienceToggle.IsOn) categories.Add("Science");
          if (SportsToggle.IsOn) categories.Add("Sports");

          await ((App)Application.Current).Notifications.StoreCategoriesAndSubscribe(locale, categories);

          var dialog = new MessageDialog(String .Format("Locale: {0}; Subscribed to: {1}", locale, string.Join(",", categories)));
          dialog.Commands.Add(new UICommand("OK"));
          await dialog.ShowAsync();

4.  Por último, en el archivo App.xaml.cs, asegúrese de actualizar la llamada al singleton Notifications en el método *OnLaunched*:

         Notifications.SubscribeToCategories(Notifications.RetrieveLocale(), Notifications.RetrieveCategories());

Envío de notificaciones localizadasEnvío de notificaciones localizadas desde el back-end
----------------------------------------------------------------------------------------

[WACOM.INCLUDE [notification-hubs-localized-back-end](../includes/notification-hubs-localized-back-end.md)]

Pasos siguientes
----------------

Para obtener más información acerca del uso de las plantillas, consulte [Notificación a los usuarios con los Centros de notificaciones: ASP.NET](/en-us/manage/services/notification-hubs/notify-users-aspnet), [Notificación a los usuarios con los Centros de notificaciones: Servicios móviles](/en-us/manage/services/notification-hubs/notify-users) e [Información general acerca de los centros de notificaciones de Azure](http://msdn.microsoft.com/en-us/library/jj927170.aspx). Como referencia sobre el lenguaje de expresión de las plantillas, puede consultar los [procedimientos de los Centros de notificaciones para la Tienda Windows](http://msdn.microsoft.com/en-us/library/jj927172.aspx) (en inglés).

