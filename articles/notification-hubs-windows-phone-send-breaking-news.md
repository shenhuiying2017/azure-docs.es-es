<properties 
	pageTitle="Uso de los Centros de notificaciones para enviar noticias de última hora (Windows Phone)" 
	description="Use Centros de notificaciones de Azure para usar la etiqueta en registros para enviar noticias de última hora a una aplicación de Windows Phone." 
	services="notification-hubs" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="12/03/2014" 
	ms.author="glenga"/>

# Uso de los Centros de notificaciones para enviar noticias de última hora
<div class="dev-center-tutorial-selector sublanding"> 
    	<a href="/es-es/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/" title="Windows Universal">Windows Universal</a><a href="/es-es/documentation/articles/notification-hubs-windows-phone-send-breaking-news/" title="Windows Phone" class="current">Windows Phone</a><a href="/es-es/documentation/articles/notification-hubs-ios-send-breaking-news/" title="iOS">iOS</a><a href="/es-es/documentation/articles/notification-hubs-aspnet-backend-android-breaking-news/" title="Android">Android</a>
</div>

Este tema muestra cómo puede usar los Centros de notificaciones de Azure para difundir notificaciones de noticias de última hora en una aplicación Silverlight de Windows Phone 8.0/8.1. Si su objetivo es una aplicación de la Tienda Windows o de Windows Phone 8.1, consulte la versión [Windows Universal] (notification-hubs-windows-store-dotnet-send-breaking-news.md). Cuando lo complete, podrá registrar las categorías de noticias de última hora en las que esté interesado y recibir solo notificaciones de inserción para esas categorías. Este escenario es un patrón común para muchas aplicaciones en las que las notificaciones tienen que enviarse a grupos de usuarios que han mostrado previamente interés en ellas, por ejemplo, lectores RSS, aplicaciones para aficionados a la música, etc. 

Los escenarios de difusión se habilitan mediante la inclusión de una o más _etiquetas_ cuando se crea un registro en el Centro de notificaciones. Cuando las notificaciones se envían a una etiqueta, todos los dispositivos registrados para la etiqueta recibirán la notificación. Puesto que las etiquetas son cadenas simples, no tendrán que aprovisionarse antes. Para obtener más información sobre las etiquetas, consulte [Orientación sobre los Centros de notificaciones]. 

Este tutorial le guiará a través de estos pasos básicos para habilitar este escenario:

1. [Adición de una selección de categorías a la aplicación]
2. [Registro de notificaciones]
3. [Envío de notificaciones desde el back-end]
4. [Ejecución de la aplicación y generación de notificaciones]

Este tema se basa en la aplicación que creó en [Introducción a los Centros de notificaciones]. Antes de comenzar este tutorial, debe haber completado la [Introducción a los Centros de notificaciones].

##<a name="adding-categories"></a>Adición de una selección de categorías a la aplicación

El primer paso es agregar los elementos de la interfaz de usuario a la página principal existente que permiten al usuario seleccionar las categorías para registrar. Las categorías seleccionadas por un usuario se almacenan en el dispositivo. Cuando la aplicación se inicia, se crea un registro de dispositivos en el Centro de notificaciones con las categorías seleccionadas como etiquetas. 

1. Abra el archivo de proyecto MainPage.xaml y, a continuación, reemplace los elementos **Grid** denominados `TitlePanel` y `ContentPanel` por el código siguiente:
			
        <StackPanel x:Name="TitlePanel" Grid.Row="0" Margin="12,17,0,28">
            <TextBlock Text="Breaking News" Style="{StaticResource PhoneTextNormalStyle}" Margin="12,0"/>
            <TextBlock Text="Categories" Margin="9,-7,0,0" Style="{StaticResource PhoneTextTitle1Style}"/>
        </StackPanel>

        <Grid Name="ContentPanel" Grid.Row="1" Margin="12,0,12,0">
            <Grid.RowDefinitions>
                <RowDefinition Height="auto"/>
                <RowDefinition Height="auto" />
                <RowDefinition Height="auto" />
                <RowDefinition Height="auto" />
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition />
                <ColumnDefinition />
            </Grid.ColumnDefinitions>
            <CheckBox Name="WorldCheckBox" Grid.Row="0" Grid.Column="0">World</CheckBox>
            <CheckBox Name="PoliticsCheckBox" Grid.Row="1" Grid.Column="0">Politics</CheckBox>
            <CheckBox Name="BusinessCheckBox" Grid.Row="2" Grid.Column="0">Business</CheckBox>
            <CheckBox Name="TechnologyCheckBox" Grid.Row="0" Grid.Column="1">Technology</CheckBox>
            <CheckBox Name="ScienceCheckBox" Grid.Row="1" Grid.Column="1">Science</CheckBox>
            <CheckBox Name="SportsCheckBox" Grid.Row="2" Grid.Column="1">Sports</CheckBox>
            <Button Name="SubscribeButton" Content="Subscribe" HorizontalAlignment="Center" Grid.Row="3" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click" />
        </Grid>

2. En el proyecto, cree una nueva clase llamada **Notifications**, agregue el modificador **public** en la definición de clase y, a continuación, agregue las siguientes instrucciones **using** al nuevo archivo de código:

		using Microsoft.Phone.Notification;
		using Microsoft.WindowsAzure.Messaging;
		using System.IO.IsolatedStorage;

3. Agregue el siguiente código a la nueva clase **Notifications**:

		private NotificationHub hub;

		public Notifications()
		{
		    hub = new NotificationHub("<hub name>", "<connection string with listen access>");
		}
		
		public async Task StoreCategoriesAndSubscribe(IEnumerable<string> categories)
		{
		    var categoriesAsString = string.Join(",", categories);
		    var settings = IsolatedStorageSettings.ApplicationSettings;
		    if (!settings.Contains("categories"))
		    {
		        settings.Add("categories", categoriesAsString);
		    }
		    else
		    {
		        settings["categories"] = categoriesAsString;
		    }
		    settings.Save();
		
		    await SubscribeToCategories(categories);
		}
		
		public async Task SubscribeToCategories(IEnumerable<string> categories)
		{
		    var channel = HttpNotificationChannel.Find("MyPushChannel");
		
		    if (channel == null)
		    {
		        channel = new HttpNotificationChannel("MyPushChannel");
		        channel.Open();
		        channel.BindToShellToast();
		    }
		
		    await hub.RegisterNativeAsync(channel.ChannelUri.ToString(), categories);
		}

    Esta clase usa el almacenamiento local para almacenar las categorías de noticias que este dispositivo ha de recibir. También contiene métodos para registrar estas categorías.

4. En el código anterior, reemplace los marcadores de posición `<hub name>` y `<connection string with listen access>` por el nombre de su Centro de notificaciones y la cadena de conexión para *DefaultListenSharedAccessSignature* que obtuvo anteriormente.

	> [AZURE.NOTE] Puesto que las credenciales que se distribuyen con una aplicación de cliente no son normalmente seguras, solo debe distribuir la clave para el acceso de escucha con la aplicación cliente. El acceso de escucha permite a la aplicación el registro de notificaciones, pero los registros existentes no pueden modificarse y las notificaciones no se pueden enviar. La clave de acceso completo se usa en un servicio back-end protegido para el envío de notificaciones y el cambio de registros existentes.

4. En el archivo de proyecto App.xaml.cs, agregue la siguiente propiedad a la clase **App**:

		public Notifications notifications = new Notifications();

	Esta propiedad se usa para crear y obtener acceso a una instancia de **Notifications**.

5. En MainPage.xaml.cs, agregue la siguiente línea:

		using Windows.UI.Popups;

6. En el archivo de proyecto MainPage.xaml.cs, agregue el siguiente método:

		private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
		{
		    var categories = new HashSet<string>();
		    if (WorldCheckBox.IsChecked == true) categories.Add("World");
		    if (PoliticsCheckBox.IsChecked == true) categories.Add("Politics");
		    if (BusinessCheckBox.IsChecked == true) categories.Add("Business");
		    if (TechnologyCheckBox.IsChecked == true) categories.Add("Technology");
		    if (ScienceCheckBox.IsChecked == true) categories.Add("Science");
		    if (SportsCheckBox.IsChecked == true) categories.Add("Sports");
		
		    await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(categories);
		
		    MessageBox.Show("Subscribed to: " + string.Join(",", categories));
		}
	
	Este método crea una lista de categorías y usa la clase **Notifications** para almacenar la lista en el almacenamiento local y registrar las etiquetas correspondientes en el Centro de notificaciones. Cuando se modifican las categorías, el registro vuelve a crearse con las nuevas categorías.

La aplicación ahora puede almacenar un conjunto de categorías en el almacenamiento local en el dispositivo y registrarse en el Centro de notificaciones siempre que el usuario cambie la selección de categorías. 

##<a name="register"></a>Registro de notificaciones

Estos pasos permiten registrar el Centro de notificaciones en el inicio mediante las categorías que se han almacenado en el almacén local. 

> [AZURE.NOTE] Puesto que el URI de canal asignado por el servicio de notificaciones de inserción de Microsoft (MPNS) puede cambiar en cualquier momento, debe registrar las notificaciones con frecuencia para evitar errores de notificación. En este ejemplo se registra la notificación cada vez que se inicia la aplicación. En las aplicaciones que se ejecutan con frecuencia, más de una vez al día, es posible que pueda omitir el registro para conservar el ancho de banda si pasa menos de un día del registro previo.

1. Agregue el siguiente código a la clase **Notifications**:

		public IEnumerable<string> RetrieveCategories()
		{
		    var categories = (string)IsolatedStorageSettings.ApplicationSettings["categories"];
		    return categories != null ? categories.Split(',') : new string[0];
		}

	Esto devuelve las categorías definidas en la clase.

1. Abra el archivo App.xaml.cs y agregue el modificador **async** al método **Application_Launching**.

2. En el método **Application_Launching**, busque y reemplace el código de registro de los Centros de notificaciones que agregó en [Introducción a los Centros de notificaciones] con la siguiente línea de código:

		await notifications.SubscribeToCategories(notifications.RetrieveCategories());

	De esta forma, se garantiza que cada vez que la aplicación se inicia, se recuperan las categorías del almacenamiento local y se solicita un registro de estas categorías. 

3. En el archivo de proyecto MainPage.xaml.cs, agregue el siguiente código que implementa el método **OnNavigatedTo**:

		protected override void OnNavigatedTo(NavigationEventArgs e)
		{
		    var categories = ((App)Application.Current).notifications.RetrieveCategories();
		
		    if (categories.Contains("World")) WorldCheckBox.IsChecked = true;
		    if (categories.Contains("Politics")) PoliticsCheckBox.IsChecked = true;
		    if (categories.Contains("Business")) BusinessCheckBox.IsChecked = true;
		    if (categories.Contains("Technology")) TechnologyCheckBox.IsChecked = true;
		    if (categories.Contains("Science")) ScienceCheckBox.IsChecked = true;
		    if (categories.Contains("Sports")) SportsCheckBox.IsChecked = true;
		}

	De esta forma, se actualiza la página principal según el estado de las categorías guardadas anteriormente. 

La aplicación está ahora completa y puede almacenar un conjunto de categorías en el almacenamiento local del dispositivo usado para registrarse en el Centro de notificaciones cuando el usuario cambie la selección de categorías. A continuación, definiremos un back-end que pueda enviar notificaciones de categorías a esta aplicación.

<h2><a name="send"></a>Envío de notificaciones desde el back-end</h2>

[AZURE.INCLUDE [notification-hubs-back-end](../includes/notification-hubs-back-end.md)]

##<a name="test-app"></a>Ejecución de la aplicación y generación de notificaciones

1. En Visual Studio, presione F5 para compilar e iniciar la aplicación.

	![][1] 

	Tenga en cuenta que la interfaz de usuario de la aplicación ofrece un conjunto de elementos de alternancia que le permite seleccionar las categorías a las que suscribirse. 

2. Habilite uno o más elementos de alternancia de las categorías y, a continuación, haga clic en **Suscribirse**.

	La aplicación convierte las categorías seleccionadas en etiquetas y solicita un nuevo registro de dispositivo para las etiquetas seleccionadas al Centro de notificaciones. Las categorías registradas se devuelven y se muestran en un cuadro de diálogo.

	![][2]

4. Envíe una nueva notificación desde el back-end de alguna de las siguientes formas:

	+ **Aplicación de consola:** inicio de la aplicación de consola.

	+ **Java/PHP:** ejecución de la aplicación/script.

	Las notificaciones para las categorías seleccionadas aparecen como notificaciones del sistema.

	![][3]

Ha completado este tema.

<!--## <a name="next-steps"> </a>Pasos siguientes

En este tutorial hemos aprendido cómo difundir noticias de última hora por categoría. Considere la posibilidad de llevar a cabo uno de los siguientes tutoriales que destacan otros escenarios de Centros de notificaciones avanzados:

+ [Uso de los Centros de notificaciones para difundir noticias de última hora localizadas]

	Conozca cómo expandir la aplicación de noticias de última hora para habilitar el envío de notificaciones localizadas. 

+ [Notificación a los usuarios con los Centros de notificaciones]

	Conozca cómo insertar notificaciones para usuarios autenticados específicos. Esta es una buena solución para enviar notificaciones solo a usuarios específicos.
-->

<!-- Anchors. -->
[Adición de una selección de categorías a la aplicación]: #adding-categories
[Registro de notificaciones]: #register
[Envío de notificaciones desde el back-end]: #send
[Ejecución de la aplicación y generación de notificaciones]: #test-app
[Pasos siguientes]: #next-steps

<!-- Images. -->
[1]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-breakingnews.png
[2]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-registration.png
[3]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-toast.png



<!-- URLs.-->
[Introducción a los Centros de notificaciones]: /es-es/manage/services/notification-hubs/get-started-notification-hubs-wp8/
[Uso de los Centros de notificaciones para difundir noticias de última hora localizadas]: ./breakingnews-localized-wp8.md 
[Notificación a los usuarios con los Centros de notificaciones]: /es-es/manage/services/notification-hubs/notify-users/
[Servicio móvil]: /es-es/develop/mobile/tutorials/get-started
[Información general acerca de los Centros de notificaciones ]: http://msdn.microsoft.com/library/jj927170.aspx
[Procedimientos de los Centros de notificaciones para Windows Phone]: ??

[Portal de administración de Azure]: https://manage.windowsazure.com/





<!--HONumber=45--> 
