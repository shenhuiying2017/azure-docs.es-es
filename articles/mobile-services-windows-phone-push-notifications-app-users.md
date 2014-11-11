<properties linkid="develop-net-tutorials-push-notifications-to-users-wp8" urlDisplayName="Push Notifications to Users (WP8)" pageTitle="Push notifications to users (Windows Phone) | Mobile Dev Center" metaKeywords="" description="Learn how to use Mobile Services to push notifications to users of your Windows Phone app." metaCanonical="" services="" documentationCenter="" title="Push notifications to users by using Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Notificaciones de inserción para usuarios mediante Servicios móviles

<div class="dev-center-tutorial-selector sublanding"> 
    <a href="/es-es/develop/mobile/tutorials/push-notifications-to-users-wp8" title="Windows Phone" class="current">Windows Phone</a><a href="/es-es/develop/mobile/tutorials/push-notifications-to-users-ios" title="iOS">iOS</a><a href="/es-es/develop/mobile/tutorials/push-notifications-to-users-android" title="Android">Android</a>
</div>

Este tema amplía el [tutorial de notificaciones de inserción anterior][tutorial de notificaciones de inserción anterior] mediante la incorporación de una nueva tabla para almacenar los URI de canal del servicio notificaciones de inserción de Microsoft (MPNS). Estos canales pueden usarse para enviar notificaciones de inserción a los usuarios de la aplicación para Windows Phone 8.

Este tutorial le guiará a través de estos pasos en la actualización de las notificaciones de inserción en su aplicación:

1.  [Creación de la tabla Channel][Creación de la tabla Channel]
2.  [Actualización de la aplicación][Actualización de la aplicación]
3.  [Actualización de los scripts de servidor][Actualización de los scripts de servidor]
4.  [Pruebas de notificaciones de inserción en su aplicación][Pruebas de notificaciones de inserción en su aplicación]

Este tutorial se basa en el inicio rápido de Servicios móviles y en el tutorial anterior, [Introducción a las notificaciones de inserción][tutorial de notificaciones de inserción anterior]. Antes de comenzar este tutorial, primero debe completar [Introducción a las notificaciones de inserción][tutorial de notificaciones de inserción anterior].

## <a name="create-table"></a>Creación de una tabla

1.  Inicie sesión en el [Portal de administración de Azure][Portal de administración de Azure], haga clic en **Servicios móviles** y, a continuación, en su aplicación.

    ![][0]

2.  Haga clic en la pestaña **Data** y, a continuación, en **Create**.

    ![][1]

    Esto muestra el cuadro de diálogo **Create new table**.

3.  Con la configuración predeterminada **Anybody with the application key** para todos los permisos, escriba *Channel* en **Table name** y, a continuación, haga clic en el botón de comprobación.

    ![][2]

    Esto crea la tabla **Channel**, la cual almacena los URI de canal que se usan para enviar notificaciones de inserción de manera independiente de los datos de elementos.

A continuación, modificará la aplicación de notificaciones de inserción para almacenar datos en esta nueva tabla en lugar de en la tabla **TodoItem**.

## <a name="update-app"></a>Actualización de la aplicación

1.  En Visual Studio 2012 Express para Windows Phone, abra el proyecto del tutorial [Introducción a las notificaciones de inserción][tutorial de notificaciones de inserción anterior], abra el archivo MainPage.xaml.cs y quite la propiedad **Channel** de la clase **TodoItem**. Tendrá el siguiente aspecto:

        public class TodoItem
        {
            public int Id { get; set; }

            [JsonProperty(PropertyName = "text")]
            public string Text { get; set; }

            [JsonProperty(PropertyName = "complete")]
            public bool Complete { get; set; }
        }

2.  Reemplace el método del controlador de eventos **ButtonSave\_Click** por la versión original de este método de la siguiente forma:

        private void ButtonSave_Click(object sender, RoutedEventArgs e)
        {
            var todoItem = new TodoItem { Text = TextInput.Text };
            InsertTodoItem(todoItem);
        }

3.  Agregue el siguiente código que crea una nueva clase **Channel**:

        public class Channel
        {
            public int Id { get; set; }

            [JsonProperty(PropertyName = "uri")]
            public string Uri { get; set; }
        }

4.  Abra el archivo App.xaml.cs y reemplace el método **AcquirePushChannel** por el siguiente código:

        private void AcquirePushChannel()
        {
            CurrentChannel = HttpNotificationChannel.Find("MyPushChannel");

            if (CurrentChannel == null)
            {
                CurrentChannel = new HttpNotificationChannel("MyPushChannel");
                CurrentChannel.Open();
                CurrentChannel.BindToShellTile();
            }

           IMobileServiceTable<Channel> channelTable = App.MobileService.GetTable<Channel>();
           var channel = new Channel { Uri = CurrentChannel.ChannelUri.ToString() };
           channelTable.InsertAsync(channel);
        }

    Este código inserta un canal en la tabla Channel.

## <a name="update-scripts"></a>Actualización de los scripts de servidor

1.  En el Portal de administración, haga clic en la pestaña **Data** y, a continuación, en la tabla **Channel**.

    ![][3]

2.  En **Channel**, haga clic en la pestaña **Script** y seleccione **Insert**.

    ![][4]

    Se muestra la función que se invoca cuando se produce una inserción en la tabla **Channel**.

3.  Reemplace la función de inserción por el siguiente código y, a continuación, haga clic en **Save**:

            function insert(item, user, request) {
                var channelTable = tables.getTable('Channel');
                channelTable
                    .where({ uri: item.uri })
                    .read({ success: insertChannelIfNotFound });
                function insertChannelIfNotFound(existingChannels) {
                    if (existingChannels.length > 0) {
                        request.respond(200, existingChannels[0]);
                    } else {
                        request.execute();
                    }
                }
            }

    Este script comprueba la tabla **Channel** por si existiera un canal existente con el mismo URI. La inserción solo se lleva a cabo si no se encuentra ningún canal que coincida. De esta forma, se evita la duplicación de registros en el canal.

4.  Haga clic en **TodoItem**, en **Script** y seleccione **Insert**.

    ![][5]

5.  Reemplace la función de inserción por el siguiente código y, a continuación, haga clic en **Save**:

        function insert(item, user, request) {
            request.execute({
                success: function() {
                    request.respond();
                    sendNotifications();
                }
            });

            function sendNotifications() {
                var channelTable = tables.getTable('Channel');
                channelTable.read({
                    success: function(channels) {
                        channels.forEach(function(channel) {
                            push.mpns.sendFlipTile(channel.uri, {
                                title: item.text
                            }, {
                                success: function(pushResponse) {
                                    console.log("Sent push:", pushResponse);
                                }
                            });
                        });
                    }
                });
            }
        }

    Este script de inserción envía una notificación de inserción (con el texto del elemento insertado) a todos los canales almacenados en la tabla **Channel**.

## <a name="test-app"></a>Prueba de la aplicación

1.  En Visual Studio, seleccione **Implementar solución** en el menú **Compilar**.

2.  Toque el icono llamado **TodoList** o **hello push** para iniciar la aplicación.

    ![][6]

3.  En la aplicación, escriba el texto "hello push again" en el cuadro de texto y, a continuación, haga clic en **Guardar**.

    ![][7]

    Esto envía una solicitud de inserción al servicio móvil para almacenar el elemento agregado.

4.  Pulse el botón **Inicio** para volver al menú de inicio.

    ![][8]

    Observe que la aplicación recibió la notificación de inserción y que el título del icono ahora es **hello push**.

5.  (Opcional) Ejecute la aplicación en dos dispositivos a la vez y repita el paso anterior.

    La notificación se envía a todas las instancias de aplicación en ejecución.

## Pasos siguientes

Este es el último tutorial que muestra los aspectos básicos relacionados con el uso de las notificaciones de inserción. Considere la posibilidad de profundizar más en los siguientes temas de Servicios móviles:

-   [Introducción a los datos][Introducción a los datos]
    Obtenga más información sobre cómo almacenar y consultar datos con los Servicios móviles.

-   [Introducción a la autenticación][Introducción a la autenticación]
    Aprenda a autenticar a los usuarios de su aplicación con cuenta de Windows.

-   [Referencia del script del servidor de Servicios móviles][Referencia del script del servidor de Servicios móviles]
    Obtenga más información acerca del registro y uso de scripts de servidor.

<!-- anchors --> <!-- Images. --> <!-- URLs. -->

  [tutorial de notificaciones de inserción anterior]: /es-es/develop/mobile/tutorials/get-started-with-push-wp8
  [Creación de la tabla Channel]: #create-table
  [Actualización de la aplicación]: #update-app
  [Actualización de los scripts de servidor]: #update-scripts
  [Pruebas de notificaciones de inserción en su aplicación]: #test-app
  [Portal de administración de Azure]: https://manage.windowsazure.com/
  [0]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-services-selection.png
  [1]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-create-table.png
  [2]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-create-channel-table.png
  [3]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-portal-data-tables-channel.png
  [4]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-insert-script-channel.png
  [5]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-insert-script-push2.png
  [6]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-quickstart-push4-wp8.png
  [7]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-quickstart-push5-wp8.png
  [8]: ./media/mobile-services-windows-phone-push-notifications-app-users/mobile-quickstart-push6-wp8.png
  [Introducción a los datos]: /es-es/develop/mobile/tutorials/get-started-with-data-wp8
  [Introducción a la autenticación]: /es-es/develop/mobile/tutorials/get-started-with-users-wp8
  [Referencia del script del servidor de Servicios móviles]: http://go.microsoft.com/fwlink/p/?LinkId=262293
