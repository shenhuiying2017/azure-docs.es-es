---
title: "Uso de Azure Notification Hubs para enviar noticias de última hora (Plataforma universal de Windows)"
description: "Use Azure Notification Hubs con etiquetas en el registro para enviar noticias de última hora a una aplicación de la Plataforma universal de Windows."
services: notification-hubs
documentationcenter: windows
author: ysxu
manager: erikre
editor: 
ms.assetid: 994d2eed-f62e-433c-bf65-4afebf1c0561
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 06/29/2016
ms.author: yuaxu
ms.openlocfilehash: d510e7e665adec9607aeee80802c466b363d5d5b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="use-notification-hubs-to-send-breaking-news"></a>Uso de los Centros de notificaciones para enviar noticias de última hora
[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

## <a name="overview"></a>Información general
Este tema muestra cómo puede usar los Centros de notificaciones de Azure para difundir notificaciones de noticias de última hora en una aplicación (no Silverlight) de la Tienda Windows o de Windows Phone 8.1. Si su destino es Silverlight para Windows Phone 8.1, consulte la versión de [Windows Phone](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md). 

Después de haber completado este proceso, puede registrarse en las categorías de noticias de última hora que le interesen y recibirá notificaciones push solo de esas categorías. Este es un escenario común en muchas aplicaciones (por ejemplo, lectores de RSS o aplicaciones de aficionados a la música) en las que se deben enviar notificaciones a grupos de usuarios que han declarado su interés en ellas. 

Puede habilitar escenarios de difusión mediante la inclusión de una o más *etiquetas* al crear un registro en el centro de notificaciones. Cuando las notificaciones se envían a una etiqueta, todos los dispositivos registrados en la etiqueta reciben la notificación. Puesto que las etiquetas son cadenas simples, no tienen que configurarse por adelantado. Para información sobre las etiquetas, consulte [Expresiones de etiqueta y enrutamiento de Notification Hubs](notification-hubs-tags-segment-push-message.md).

> [!NOTE]
> Los proyectos de la Tienda Windows y Windows Phone versión 8.1 y versiones anteriores no se admiten en Visual Studio 2017. Para más información, consulte [Compatibilidad y destinatarios de la plataforma de Visual Studio 2017](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs). 

## <a name="prerequisites"></a>Requisitos previos
Este tema se basa en la aplicación que creó en [Introducción a Notification Hubs][get-started]. Antes de comenzar este tutorial, debe completar [Introducción a Notification Hubs][get-started].

## <a name="add-category-selection-to-the-app"></a>Adición de una selección de categorías a la aplicación
El primer paso consiste en agregar elementos de la interfaz de usuario a la página principal existente de modo que los usuarios puedan seleccionar categorías de registro. Las categorías seleccionadas se almacenan en el dispositivo. Cuando la aplicación se inicia, se crea un registro de dispositivos en el centro de notificaciones con las categorías seleccionadas como etiquetas.

1. Abra el archivo de proyecto MainPage.xaml y, a continuación, copie el siguiente código en el elemento **Grid**:
   
        <Grid>
            <Grid.RowDefinitions>
                <RowDefinition/>
                <RowDefinition/>
                <RowDefinition/>
                <RowDefinition/>
                <RowDefinition/>
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition/>
                <ColumnDefinition/>
            </Grid.ColumnDefinitions>
            <TextBlock Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"  TextWrapping="Wrap" Text="Breaking News" FontSize="42" VerticalAlignment="Top" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="World" Name="WorldToggle" Grid.Row="1" Grid.Column="0" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Politics" Name="PoliticsToggle" Grid.Row="2" Grid.Column="0" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Business" Name="BusinessToggle" Grid.Row="3" Grid.Column="0" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Technology" Name="TechnologyToggle" Grid.Row="1" Grid.Column="1" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Science" Name="ScienceToggle" Grid.Row="2" Grid.Column="1" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Sports" Name="SportsToggle" Grid.Row="3" Grid.Column="1" HorizontalAlignment="Center"/>
            <Button Name="SubscribeButton" Content="Subscribe" HorizontalAlignment="Center" Grid.Row="4" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click"/>
        </Grid>

2. Haga clic con el botón derecho en el proyecto **Shared** y agregue una nueva clase llamada **Notifications**, agregue el modificador **public** a la definición de clase y, a continuación, agregue las siguientes instrucciones **using** al nuevo archivo de código:
   
        using Windows.Networking.PushNotifications;
        using Microsoft.WindowsAzure.Messaging;
        using Windows.Storage;
        using System.Threading.Tasks;

3. Copie el siguiente código a la nueva clase **Notifications**:
   
        private NotificationHub hub;
   
        public Notifications(string hubName, string listenConnectionString)
        {
            hub = new NotificationHub(hubName, listenConnectionString);
        }
   
        public async Task<Registration> StoreCategoriesAndSubscribe(IEnumerable<string> categories)
        {
            ApplicationData.Current.LocalSettings.Values["categories"] = string.Join(",", categories);
            return await SubscribeToCategories(categories);
        }
   
        public IEnumerable<string> RetrieveCategories()
        {
            var categories = (string) ApplicationData.Current.LocalSettings.Values["categories"];
            return categories != null ? categories.Split(','): new string[0];
        }
   
        public async Task<Registration> SubscribeToCategories(IEnumerable<string> categories = null)
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
   
            if (categories == null)
            {
                categories = RetrieveCategories();
            }
   
            // Using a template registration to support notifications across platforms.
            // Any template notifications that contain messageParam and a corresponding tag expression
            // will be delivered for this registration.
   
            const string templateBodyWNS = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(messageParam)</text></binding></visual></toast>";
   
            return await hub.RegisterTemplateAsync(channel.Uri, templateBodyWNS, "simpleWNSTemplateExample",
                    categories);
        }
   
    Esta clase usa el almacenamiento local para almacenar las categorías de noticias que este dispositivo debe recibir. En lugar de llamar al método *RegisterNativeAsync*, llamamos al método *RegisterTemplateAsync* para registrar las categorías mediante un registro de plantilla. 
   
    Como es posible que quiera registrar más de una plantilla (por ejemplo, una para las notificaciones del sistema y otra para los iconos), proporcione también un nombre de plantilla (por ejemplo, "simpleWNSTemplateExample"). Se asigna un nombre a las plantillas para que pueda actualizarlas o eliminarlas.
   
    >[!NOTE]
    >Si un dispositivo registra varias plantillas con la misma etiqueta, un mensaje entrante destinado a dicha etiqueta dará como resultado la entrega de varias notificaciones al dispositivo (una por cada plantilla). Este comportamiento resulta útil cuando un mismo mensaje lógico debe dar como resultado varias notificaciones visuales (que muestren, por ejemplo, tanto un distintivo como una notificación del sistema en una aplicación de la Tienda Windows).
   
    Para obtener más información, consulte [Plantillas](notification-hubs-templates-cross-platform-push-messages.md).

4. En el archivo de proyecto App.xaml.cs, agregue la siguiente propiedad a la clase **App** :
   
        public Notifications notifications = new Notifications("<hub name>", "<connection string with listen access>");
   
    Esta propiedad se usa para crear una instancia de **Notifications** y tener acceso a ella.
   
    En el código, reemplace los marcadores de posición `<hub name>` y `<connection string with listen access>` por el nombre del centro de notificaciones y la cadena de conexión de *DefaultListenSharedAccessSignature* que obtuvo anteriormente.
   
   > [!NOTE]
   > Puesto que las credenciales que se distribuyen con una aplicación cliente no son normalmente seguras, distribuya solo la clave para el acceso de *escucha* con la aplicación cliente. El acceso de escucha permite a la aplicación registrarse en las notificaciones, pero los registros existentes no pueden modificarse y las notificaciones no se pueden enviar. La clave de acceso completo se usa en un servicio back-end protegido para el envío de notificaciones y el cambio de registros existentes.
   > 
   > 
5. En el archivo de proyecto MainPage.xaml.cs, agregue la siguiente línea:
   
        using Windows.UI.Popups;

6. En el archivo de proyecto MainPage.xaml.cs, agregue el siguiente método:
   
        private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
        {
            var categories = new HashSet<string>();
            if (WorldToggle.IsOn) categories.Add("World");
            if (PoliticsToggle.IsOn) categories.Add("Politics");
            if (BusinessToggle.IsOn) categories.Add("Business");
            if (TechnologyToggle.IsOn) categories.Add("Technology");
            if (ScienceToggle.IsOn) categories.Add("Science");
            if (SportsToggle.IsOn) categories.Add("Sports");
   
            var result = await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(categories);
   
            var dialog = new MessageDialog("Subscribed to: " + string.Join(",", categories) + " on registration Id: " + result.RegistrationId);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
   
    Este método crea una lista de categorías y usa la clase **Notifications** para almacenar la lista en el almacenamiento local. También registra las etiquetas correspondientes en el centro de notificaciones. Cuando se modifican las categorías, el registro vuelve a crearse con las nuevas categorías.

La aplicación puede almacenar ahora un conjunto de categorías en el almacenamiento local en el dispositivo. La aplicación se registra en el centro de notificaciones siempre que los usuarios cambian la selección de categoría.

## <a name="register-for-notifications"></a>Registro de notificaciones
En esta sección, se registrará en el centro de notificaciones al inicio mediante las categorías que ha almacenado en el almacenamiento local.

> [!NOTE]
> Puesto que el URI de canal asignado por el Servicio de notificaciones de Windows (WNS) puede cambiar en cualquier momento, debe registrarse en las notificaciones con frecuencia a fin de evitar errores de notificación. En este ejemplo se registra la notificación cada vez que se inicia la aplicación. En las aplicaciones que se ejecutan con frecuencia (más de una vez al día), es posible que pueda omitir el registro para conservar el ancho de banda si pasa menos de un día desde el registro anterior.
> 
> 

1. Para usar la clase `notifications` para suscribirse por categorías, abra el archivo App.xaml.cs y luego actualice el método **InitNotificationsAsync**.
   
        // *** Remove or comment out these lines *** 
        //var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
        //var hub = new NotificationHub("your hub name", "your listen connection string");
        //var result = await hub.RegisterNativeAsync(channel.Uri);
   
        var result = await notifications.SubscribeToCategories();
   
    Este proceso garantiza que cuando se inicia la aplicación, recupera las categorías del almacenamiento local y solicita el registro de estas categorías. El método **InitNotificationsAsync** se creó como parte del tutorial [Introducción a Notification Hubs][get-started].

2. En el archivo de proyecto MainPage.xaml.cs, agregue el siguiente código en el método *OnNavigatedTo* :
   
        protected override void OnNavigatedTo(NavigationEventArgs e)
        {
            var categories = ((App)Application.Current).notifications.RetrieveCategories();
   
            if (categories.Contains("World")) WorldToggle.IsOn = true;
            if (categories.Contains("Politics")) PoliticsToggle.IsOn = true;
            if (categories.Contains("Business")) BusinessToggle.IsOn = true;
            if (categories.Contains("Technology")) TechnologyToggle.IsOn = true;
            if (categories.Contains("Science")) ScienceToggle.IsOn = true;
            if (categories.Contains("Sports")) SportsToggle.IsOn = true;
        }
   
    Este código actualiza la página principal según el estado de las categorías guardadas anteriormente.

La aplicación ya está completa y puede almacenar un conjunto de categorías en el almacenamiento local del dispositivo que se usa para registrarse en el centro de notificaciones cuando los usuarios cambian la selección de categoría. En la siguiente sección, definirá un back-end que puede enviar notificaciones de categoría a esta aplicación.

## <a name="send-tagged-notifications"></a>Envío de notificaciones con etiquetas
[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="run-the-app-and-generate-notifications"></a>Ejecución de la aplicación y generación de notificaciones
1. En Visual Studio, presione **F5** para compilar e iniciar la aplicación.  
    La interfaz de usuario de la aplicación ofrece un conjunto de elementos de alternancia que le permiten seleccionar las categorías a las que suscribirse. 
   
    ![Aplicación de noticias de última hora][1]

2. Habilite uno o más elementos de alternancia de categorías y, a continuación, haga clic en **Suscribirse**.
   
    La aplicación convierte las categorías seleccionadas en etiquetas y solicita un nuevo registro de dispositivo para las etiquetas seleccionadas al Centro de notificaciones. Las categorías registradas se devuelven y se muestran en un cuadro de diálogo.
   
    ![Elementos de alternancia de categorías y el botón Suscribirse][19]

3. Envíe una nueva notificación desde el back-end de alguna de las siguientes formas:

   * **Aplicación de consola**: inicie la aplicación de consola.
   * **Java/PHP**: ejecute la aplicación o el script.
     
     Las notificaciones para las categorías seleccionadas aparecen como notificaciones del sistema.
     
     ![Notificaciones del sistema][14]

## <a name="next-steps"></a>Pasos siguientes
En este tutorial, ha aprendido a difundir noticias de última hora por categoría. Considere la posibilidad de completar el siguiente tutorial, que resalta otro escenario avanzado de Notification Hubs:

* [Uso de Notification Hubs para difundir noticias de última hora localizadas]: en este tutorial se describe cómo expandir la aplicación de noticias de última hora para permitir el envío de notificaciones localizadas.

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
[get-started]: /azure/notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification
[Uso de Notification Hubs para difundir noticias de última hora localizadas]: /manage/services/notification-hubs/breaking-news-localized-dotnet/
[Notify users with Notification Hubs]: /manage/services/notification-hubs/notify-users
[Mobile Service]: /develop/mobile/tutorials/get-started/
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Store]: http://msdn.microsoft.com/library/jj927172.aspx
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
