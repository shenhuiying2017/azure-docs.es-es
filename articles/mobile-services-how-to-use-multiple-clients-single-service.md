<properties linkid="" urlDisplayName="" pageTitle="" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="Considerations for supporting multiple clients from a single mobile service" authors="krisragh" solutions="" manager="" editor="" />

Consideraciones para admitir varios clientes desde un solo servicio móvil
=========================================================================

Uno de los principales beneficios de usar Servicios móviles de Azure para admitir el desarrollo de aplicaciones móviles es la capacidad de usar un servicio back-end único que admita una aplicación en varias plataformas de cliente. Servicios móviles proporciona bibliotecas de cliente nativas para todas las plataformas de dispositivos importantes. Para obtener más información, consulte [Tutoriales y recursos](/es-es/develop/mobile/resources/).

A pesar de que Servicios móviles simplifica la migración de su aplicación nativa a diversas plataformas de cliente con un servicio back-end único, existen algunas consideraciones para las que deberá planificarse. Este tema proporciona orientación sobre cómo hacer que las notificaciones de inserción funcionen en todas las plataformas de cliente. También se describe cómo solucionar un problema con el uso del inicio de sesión único dirigido por el cliente con la cuenta Microsoft tanto en aplicaciones de la Tienda Windows como de Windows Phone. Por último, este tema analiza algunos procedimientos recomendados para reutilizar código en proyectos de Visual Studio.

Notificaciones de inserción
---------------------------

En esta sección se analizan dos enfoques para enviar notificaciones de inserción desde el servicio móvil a aplicaciones cliente en varias plataformas.

### Uso de los Centros de notificaciones

Centros de notificaciones de Azure es un servicio de Azure, una solución escalable para enviar notificaciones de inserción desde el servicio móvil (o cualquier back-end) a aplicaciones cliente en todas las plataformas de dispositivos importantes. Para obtener más información, consulte [Centros de notificaciones de Azure](/es-es/develop/net/how-to-guides/service-bus-notification-hubs/).

Centros de notificaciones proporciona una infraestructura coherente y unificada para crear y administrar registros de dispositivos y para enviar notificaciones de inserción a dispositivos que se ejecutan en todas las plataformas de dispositivos importantes. Para obtener más información, consulte [Introducción a los Centros de notificaciones](/es-es/manage/services/notification-hubs/getting-started-windows-dotnet/). Centros de notificaciones es compatible con registros de plantillas específicos de la plataforma, lo que le permite usar una sola llamada de API para enviar una notificación a la aplicación que se ejecuta en cualquier plataforma registrada. Para obtener más información, consulte [Envío de notificaciones entre plataformas a los usuarios](/es-es/manage/services/notification-hubs/notify-users-xplat-mobile-services/).

Centros de notificaciones es la solución recomendada para enviar notificaciones desde el servicio móvil a varias plataformas de cliente.

### Uso de un identificador de plataforma y de una tabla de registro común

Si elige no usar los Centros de notificaciones, de todos modos puede admitir notificaciones de inserción a varios clientes desde el servicio móvil si define un mecanismo de registro de dispositivos común. Use una tabla para almacenar la información específica de dispositivos que utilizan los servicios de notificación de inserción de la plataforma compatible. Los tutoriales **Introducción a las notificaciones de inserción** ([C\# para Tienda Windows](/es-es/develop/mobile/tutorials/get-started-with-push-dotnet-vs2012/)/[JavaScript para Tienda Windows](/es-es/develop/mobile/tutorials/get-started-with-push-js-vs2012/)/[Windows Phone](/es-es/develop/mobile/tutorials/get-started-with-push-wp8/)/[iOS](/es-es/develop/mobile/tutorials/get-started-with-push-ios/)/[Android](/es-es/develop/mobile/tutorials/get-started-with-push-android/)) usan una tabla **Registrations** y almacenan el URI de canal (Windows), el token de dispositivo (iOS) o el controlador (Android) en una columna llamada *handle*.

**Nota:**

Cuando utilice el Asistente para agregar notificaciones de inserción en Visual Studio 2013 para agregar notificaciones de inserción a una aplicación de la Tienda Windows, el asistente crea automáticamente una tabla llamada **Channel** para almacenar los URI de canal. El tutorial **Introducción a las notificaciones de inserción en Visual Studio 2012** ([C\# para Tienda Windows](/es-es/develop/mobile/tutorials/get-started-with-push-dotnet-vs2012)/[JavaScript para Tienda Windows](/es-es/develop/mobile/tutorials/get-started-with-push-js-vs2012)) le muestra cómo habilitar notificaciones de inserción con la tabla de **Registrations**.

Para admitir varios clientes en esta tabla de registro, incluya una columna *platform* en la tabla, donde este campo está definido en la plataforma del cliente que inserta una fila durante el registro. Por ejemplo, la siguiente definición de clase **Registrations**, desde una aplicación de C\# para Tienda Windows o de Windows Phone, asigna el campo *ChannelUri* de cliente a la columna *handle* en la tabla Registrations:

     public class Registrations
        {
            [JsonProperty(PropertyName = "platform")]         
            public string Platform { 
                get
                {
                    return "windowsstore";
                    // devolver "windowsphone";
                }
                set {}
            }
            
            public string Id { get; set; }
        
            [JsonProperty(PropertyName = "handle")]
            public string ChannelUri { get; set; }
        }

Tenga en cuenta que en este dispositivo de Windows, el campo *Platform* siempre devuelve `windowsstore` (o `windowsphone`). Con el esquema dinámico habilitado (el valor predeterminado), Servicios móviles agrega una columna de plataforma en la tabla Registrations, si todavía no existe. Para obtener más información, consulte [Esquema dinámico](http://msdn.microsoft.com/es-es/library/windowsazure/jj193175.aspx).

En el script de servidor que envía notificaciones, use el campo de plataforma para determinar a qué función específica de plataforma se debe llamar en el [objeto de inserción](http://msdn.microsoft.com/es-es/library/windowsazure/jj554217.aspx). El siguiente script es una modificación del script de servidor de los tutoriales **Introducción a las notificaciones de inserción** ([C\# para Tienda Windows](/es-es/develop/mobile/tutorials/get-started-with-push-dotnet-vs2012/)/[JavaScript para Tienda Windows](/es-es/develop/mobile/tutorials/get-started-with-push-js-vs2012/)/[Windows Phone](/es-es/develop/mobile/tutorials/get-started-with-push-wp8/)/[iOS](/es-es/develop/mobile/tutorials/get-started-with-push-ios/)/[Android](/es-es/develop/mobile/tutorials/get-started-with-push-android/)) para habilitar varias plataformas de cliente:

     function insert(item, user, request) {
            request.execute({
                success: function() {
                    request.respond();
                    sendNotifications();
                }
            });
        
            function sendNotifications() {
                var registrationsTable = tables.getTable('Registrations');
                registrationsTable.read({
                    success: function(registrations) {
                        registrations.forEach(function(registration) {
                            if (registration.platform === 'winstore') {
                                push.wns.sendToastText04(registration.handle, {
                                    text1: item.text
                                }, {
                                    success: pushCompleted
                                });
                            } else if (registration.platform === 'winphone') {
                                push.mpns.sendFlipTile(registration.handle, {
                                    title: item.text
                                }, {
                                    success: pushCompleted
                                });
                            } else if (registration.platform === 'ios') {
                                push.apns.send(registration.handle, {
                                    alert: "Toast: " + item.text,
                                    payload: {
                                        inAppMessage: item.text
                                    }
                                });
                            } else if (registration.platform === 'android') {
                                push.gcm.send(registration.handle, item.text, {
                                    success: pushCompleted
                                });
                            } else {
                                console.error("Unknown push notification platform");
                            }
                        });
                    }
                });
            }
        
            function pushCompleted(pushResponse) {
                console.log("Sent push:", pushResponse);
            }
        }

Registro de una aplicación Windows
----------------------------------

A fin de usar la autenticación de cliente de inicio de sesión único con la cuenta Microsoft en aplicaciones de la Tienda Windows y de Windows Phone, primero debe registrar la aplicación de la Tienda Windows en el panel de la Tienda Windows. Esto se debe a que una vez que crea un registro de Live Connect para Windows Phone, no puede crear uno para la Tienda Windows. Si desea obtener más información acerca de cómo hacerlo, lea el tema **Autenticación de una aplicación de la Tienda Windows con el inicio de sesión único de Live Connect** ([Tienda Windows](/es-es/develop/mobile/tutorials/single-sign-on-windows-8-dotnet/)/[Windows Phone](/es-es/develop/mobile/tutorials/single-sign-on-wp8/)).

Procedimientos recomendados para reutilizar código en proyectos de Visual Studio
--------------------------------------------------------------------------------

Las bibliotecas de clases portables le permiten escribir y crear ensamblados administrados que funcionan en más de una plataforma, como la Tienda Windows y Windows Phone. Puede crear clases que contengan el código que desee compartir entre varios proyectos y, a continuación, hacer referencia a esas clases desde distintos tipos de proyectos.

Puede usar la biblioteca de clases portable de .NET Framework para implementar el patrón modelo-vista-vista-modelo (MVVM) y compartir ensamblados entre plataformas. Puede implementar las clases de modelo de visualización y modelo en un proyecto de biblioteca de clases portable en Visual Studio 2012 y, a continuación, cree vistas que estén personalizadas para distintas plataformas. El código de modelo, común entre plataformas, puede (como ejemplo) recuperar los datos desde un origen como un Servicio móvil de Azure independientemente de la plataforma. MSDN Library proporciona [información general y un ejemplo](http://msdn.microsoft.com/es-es/library/gg597391(v=vs.110)), un análisis de las [diferencias de API](http://msdn.microsoft.com/es-es/library/gg597392(v=vs.110)), un ejemplo del [uso de bibliotecas de clases portables para implementar el patrón MVVM](http://msdn.microsoft.com/es-es/library/hh563947(v=vs.110)), [recomendaciones](http://msdn.microsoft.com/es-es/library/windowsphone/develop/jj714086(v=vs.105).aspx) adicionales e información acerca de la [administración de recursos](http://msdn.microsoft.com/es-es/library/hh871422(v=vs.110)) en proyectos de bibliotecas de clases portables.

