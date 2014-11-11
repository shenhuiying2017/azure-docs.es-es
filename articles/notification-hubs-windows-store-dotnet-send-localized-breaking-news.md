<properties linkid="develop-notificationhubs-tutorials-send-localized-breaking-news-windowsdotnet" urlDisplayName="Localized Breaking News" pageTitle="Notification Hubs Localized Breaking News Tutorial" metaKeywords="" description="Learn how to use Azure Service Bus Notification Hubs to send localized breaking news notifications." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="" title="Use Notification Hubs to send localized breaking news" authors="ricksal" solutions="" manager="" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="ricksal" />

# Uso de los Centros de notificaciones para enviar noticias de última hora localizadas

<div class="dev-center-tutorial-selector sublanding"> 
        <a href="/es-es/documentation/articles/notification-hubs-windows-store-dotnet-send-localized-breaking-news/" title="C# para Tienda Windows" class="current">C# para Tienda Windows</a><a href="/es-es/documentation/articles/notification-hubs-ios-send-localized-breaking-news/" title="iOS">iOS</a>
</div>

Este tema muestra cómo usar la característica de **plantilla** de los Centros de notificaciones de Azure para difundir notificaciones de noticias de última hora localizadas por lenguaje y dispositivo. En este tutorial comenzará con la aplicación de la Tienda Windows que se creó en el tutorial [Uso de los Centros de notificaciones para enviar noticias de última hora][Uso de los Centros de notificaciones para enviar noticias de última hora]. Una vez que lo haya completado, podrá registrarse en las categorías que le interesan, especificar un idioma para recibir las notificaciones y recibir solo notificaciones de inserción para las categorías seleccionadas en dicho idioma.

Este tutorial le guiará a través de estos pasos básicos para habilitar este escenario:

1.  [Conceptos de plantilla][Conceptos de plantilla]
2.  [La interfaz de usuario de la aplicación][La interfaz de usuario de la aplicación]
3.  [Creación de la aplicación cliente de la Tienda Windows][Creación de la aplicación cliente de la Tienda Windows]
4.  [Envío de notificaciones desde su back-end][Envío de notificaciones desde su back-end]

Este escenario tiene dos partes:

-   La aplicación de la Tienda Windows permite que los dispositivos cliente especifiquen un idioma y se suscriban a distintas categorías de noticias de última hora.

-   el back-end difunde las notificaciones, mediante las características de **etiqueta** y **plantilla** de los Centros de notificaciones de Azure.

## Requisitos previos

Debe haber completado el tutorial [Uso de Centros de notificaciones para enviar noticias de última hora][Uso de los Centros de notificaciones para enviar noticias de última hora] y debe tener disponible el código, porque este tutorial se basa directamente en ese código.

También necesita Visual Studio 2012.

## <a name="concepts"></a><span class="short-header">conceptos</span>Conceptos de plantilla

En el tutorial [Use Notification Hubs to send breaking news][Uso de los Centros de notificaciones para enviar noticias de última hora] (Uso de Centros de notificaciones para enviar noticias de última hora) creó una aplicación que utilizó **etiquetas** para suscribirse a notificaciones para distintas categorías de noticias.
Sin embargo, muchas aplicaciones están dirigidas a varios mercados y requieren localización. Esto significa que el contenido de las propias notificaciones se debe localizar y entregar al conjunto de dispositivos correcto.
En este tema podremos mostrar cómo usar la característica de **plantilla** de los Centros de notificaciones para entregar fácilmente notificaciones de noticias de última hora localizadas.

Nota: una forma de enviar notificaciones localizadas es crear varias versiones de cada etiqueta. Por ejemplo, para admitir inglés, francés y chino mandarín, necesitaríamos tres etiquetas distintas para noticias mundiales: "mundo\_in", "mundo\_fr" y "mundo\_ch". Luego tendríamos que enviar una versión localizada de las noticias mundiales a cada una de estas etiquetas. En este tema usamos plantillas para evitar la proliferación de etiquetas y el requisito de enviar varios mensajes.

A un alto nivel, las plantillas son una forma de especificar la manera en que un dispositivo específico debe recibir una notificación. La plantilla especifica el formato de carga exacto haciendo referencia a las propiedades que forman parte del mensaje enviado por el back-end de la aplicación. En nuestro caso, enviaremos un mensaje independiente de la configuración regional que contengan todos los idiomas compatibles:

    {
        "News_English": "...",
        "News_French": "...",
        "News_Mandarin": "..."
    }

Esto garantizará que los dispositivos se registren con una plantilla que hace referencia a la propiedad correcta. Por ejemplo, una aplicación de la Tienda Windows que desea recibir un simple mensaje del sistema se registrará en la plantilla siguiente:

    <toast>
      <visual>
        <binding template=\"ToastText01\">
          <text id=\"1\">$(News_English)</text>
        </binding>
      </visual>
    </toast>

Las plantillas son una característica muy potente de la que puede aprender más en nuestro artículo [Información general acerca de los Centros de notificaciones de Azure][Información general acerca de los Centros de notificaciones de Azure]. Asimismo, podrá consultar información de referencia sobre el lenguaje de expresión de las plantillas en los [procedimientos de los Centros de notificaciones para la Tienda Windows][procedimientos de los Centros de notificaciones para la Tienda Windows] (en inglés).

## <a name="ui"></a><span class="short-header">Interfaz de usuario de aplicación</span>La interfaz de usuario de la aplicación

Ahora modificaremos la aplicación de noticias de última hora que creó en el tema [Uso de los Centros de notificaciones para enviar noticias de última hora][Uso de los Centros de notificaciones para enviar noticias de última hora] para enviar noticias de última hora localizadas con plantillas.

Con la finalidad de adaptar sus aplicaciones clientes para que reciban mensajes localizados, debe reemplazar sus registros *nativos* (es decir, registros que especifica en una plantilla) por registros de plantilla.

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

## <a name="building-client"></a><span class="building app">IU de la aplicación</span>Creación de la aplicación cliente de la Tienda Windows

1.  En la clase Notifications, agregue un parámetro de configuración regional a los métodos *StoreCategoriesAndSubscribe* y *SubscribeToCategories*.

        public async Task StoreCategoriesAndSubscribe(string locale, IEnumerable<string> categories)
        {
            ApplicationData.Current.LocalSettings.Values["categories"] = string.Join(",", categories);
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

4.  Por último, en el archivo App.xaml.cs, asegúrese de actualizar la llamada al
    singleton Notifications en el método *OnLaunched*:

        Notifications.SubscribeToCategories(Notifications.RetrieveLocale(), Notifications.RetrieveCategories());

## <a name="send"></a><span class="short-header">Envío de notificaciones localizadas</span>Envío de notificaciones localizadas desde el back-end

[WACOM.INCLUDE [notification-hubs-localized-back-end](../includes/notification-hubs-localized-back-end.md)]

## Pasos siguientes

Para obtener más información acerca del uso de las plantillas, consulte [Notificación a los usuarios con los Centros de notificaciones: ASP.NET][Notificación a los usuarios con los Centros de notificaciones: ASP.NET], [Notificación a los usuarios con los Centros de notificaciones: Servicios móviles][Notificación a los usuarios con los Centros de notificaciones: Servicios móviles] e [Información general acerca de los centros de notificaciones de Azure][Información general acerca de los Centros de notificaciones de Azure]. Como referencia sobre el lenguaje de expresión de las plantillas, puede consultar los [procedimientos de los Centros de notificaciones para la Tienda Windows][procedimientos de los Centros de notificaciones para la Tienda Windows] (en inglés).


  [Uso de los Centros de notificaciones para enviar noticias de última hora]: /es-es/manage/services/notification-hubs/breaking-news-dotnet
  [Conceptos de plantilla]: #concepts
  [La interfaz de usuario de la aplicación]: #ui
  [Creación de la aplicación cliente de la Tienda Windows]: #building-client
  [Envío de notificaciones desde su back-end]: #send
  [Información general acerca de los Centros de notificaciones de Azure]: http://msdn.microsoft.com/es-es/library/jj927170.aspx
  [procedimientos de los Centros de notificaciones para la Tienda Windows]: http://msdn.microsoft.com/es-es/library/jj927172.aspx
  [notification-hubs-localized-back-end]: ../includes/notification-hubs-localized-back-end.md
  [Notificación a los usuarios con los Centros de notificaciones: ASP.NET]: /es-es/manage/services/notification-hubs/notify-users-aspnet
  [Notificación a los usuarios con los Centros de notificaciones: Servicios móviles]: /es-es/manage/services/notification-hubs/notify-users
