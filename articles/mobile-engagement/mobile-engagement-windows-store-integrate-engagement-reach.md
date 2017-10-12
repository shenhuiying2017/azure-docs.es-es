---
title: "Integración del SDK de Cobertura de Windows Universal Apps"
description: "Cómo integrar Cobertura de Azure Mobile Engagement con aplicaciones Windows Universal"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: a31ca1d6-856f-4aec-898a-07969ae5f7ec
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 9311e998e67d8d0d56da68fc9460df32ce7ce5a9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="windows-universal-apps-reach-sdk-integration"></a>Integración del SDK de Cobertura de Windows Universal Apps
Debe seguir el procedimiento de integración descrito en el documento [Integración del SDK de Windows Universal Engagement](mobile-engagement-windows-store-integrate-engagement.md) antes de seguir con esta guía.

## <a name="embed-the-engagement-reach-sdk-into-your-windows-universal-project"></a>Integre el SDK de Cobertura de Engagement en el proyecto de Windows Universal
No hace falta agregar nada. `EngagementReach` ya están en el proyecto.

> [!TIP]
> Puede personalizar las imágenes que se encuentran en la carpeta `Resources` del proyecto, especialmente el icono de marca (el valor predeterminado para el icono Engagement). En las aplicaciones universales también puede mover la carpeta `Resources` del proyecto compartido para compartir su contenido entre aplicaciones, pero tendrá que mantener el archivo `Resources\EngagementConfiguration.xml` en su ubicación predeterminada debido a que depende de la plataforma.
> 
> 

## <a name="enable-the-windows-notification-service"></a>Habilitar el Servicio de notificaciones de Windows
### <a name="windows-8x-and-windows-phone-81-only"></a>Solo Windows 8.x y Windows Phone 8.1
Para poder usar el **Servicio de notificaciones de Windows** (conocido como WNS) en el archivo `Package.appxmanifest` en `Application UI`, haga clic en `All Image Assets` en el cuadro inferior izquierdo. A la derecha del cuadro de `Notifications`, cambie `toast capable` de `(not set)` a `(Yes)`.

### <a name="all-platforms"></a>Todas las plataformas
Deberá sincronizar la aplicación con su cuenta de Microsoft y la plataforma de Engagement. Para esto deberá crear una cuenta o iniciar sesión en el [Centro de desarrollo de Windows](https://dev.windows.com). Después, cree una nueva aplicación y busque el SID y la clave secreta. En el servidor front-end de Engagement, vaya a la opción de configuración de la aplicación en `native push` y pegue sus credenciales. Luego, haga clic con el botón secundario del mouse en el proyecto, seleccione `store` y `Associate App with the Store...`. Solo tiene que seleccionar la aplicación que creó antes para sincronizarla.

## <a name="initialize-the-engagement-reach-sdk"></a>Inicializar el SDK de Engagement Reach
Modifique `App.xaml.cs`:

* Inserte `EngagementReach.Instance.Init` justo después de `EngagementAgent.Instance.Init` en su método `InitEngagement`:
  
      private void InitEngagement(IActivatedEventArgs e)
      {
        EngagementAgent.Instance.Init(e);
        EngagementReach.Instance.Init(e);
      }
  
  `EngagementReach.Instance.Init` se ejecuta en un subproceso dedicado. No es necesario que lo haga usted.

> [!NOTE]
> Si está usando las notificaciones de inserción en otra parte de la aplicación, tendrá que [compartir su canal de inserción](#push-channel-sharing) con Engagement Reach.
> 
> 

## <a name="integration"></a>Integración
Engagement proporciona dos maneras de agregar las vistas intersticiales y los banners en aplicación de Reach para anuncios y sondeos en la aplicación: la integración de superposición y la integración manual de vistas web. No debe combinar ambos enfoques en la misma página.

La elección entre las dos integraciones podía resumirse así:

* Puede elegir la integración de superposición si las páginas ya heredan desde el agente `EngagementPage`; solo es cuestión de reemplazar `EngagementPage` por `EngagementPageOverlay` y `xmlns:engagement="using:Microsoft.Azure.Engagement"` por `xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay"` en las páginas.
* Puede elegir la integración manual de vistas web si desea colocar de forma precisa la IU de Reach dentro de las páginas o si no desea agregar otro nivel de herencia a las páginas. 

### <a name="overlay-integration"></a>Integración de superposición
La superposición de Engagement agrega dinámicamente los elementos de IU utilizados para mostrar las campañas de Reach en la página. Si la superposición no se adapta a su diseño, plantéese la integración manual de vistas web en su lugar.

En el cambio de archivo .xaml, cambie la referencia `EngagementPage` a `EngagementPageOverlay`.

* Agregue a las declaraciones de espacios de nombres:
  
      xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay"
* Reemplace `engagement:EngagementPage` por `engagement:EngagementPageOverlay`:

**Con EngagementPage:**

        <engagement:EngagementPage 
            xmlns:engagement="using:Microsoft.Azure.Engagement">

            <!-- Your layout -->
        </engagement:EngagementPage>

**Con EngagementPageOverlay:**

        <engagement:EngagementPageOverlay 
            xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay">

            <!-- Your layout -->
        </engagement:EngagementPageOverlay>

Después, en el archivo. cs, etiquete la página en `EngagementPageOverlay` en lugar de `EngagementPage` e importe `Microsoft.Azure.Engagement.Overlay`.

            using Microsoft.Azure.Engagement.Overlay;

* Reemplace `EngagementPage` por `EngagementPageOverlay`:

**Con EngagementPage:**

            using Microsoft.Azure.Engagement;

            namespace Example
            {
              public sealed partial class ExamplePage : EngagementPage
              {
                [...]
              }
            }

**Con EngagementPageOverlay:**

            using Microsoft.Azure.Engagement.Overlay;

            namespace Example
            {
              public sealed partial class ExamplePage : EngagementPageOverlay 
              {
                [...]
              }
            }


La superposición de Engagement agrega un elemento `Grid` encima de la página compuesta del diseño y los dos elementos `WebView`, uno para el banner y el otro para la vista intersticial.

Puede personalizar los elementos de superposición directamente en el archivo `EngagementPageOverlay.cs`.

### <a name="web-views-manual-integration"></a>Integración manual de vistas web
Reach buscará las páginas para los dos elementos `WebView` responsables de mostrar el banner y la vista intersticial. Lo único que debe hacer es agregar esos dos elementos `WebView` en algún lugar de las páginas. A continuación se muestra un ejemplo:

    <Grid x:Name="engagementGrid">

      <!-- Your layout -->

      <WebView x:Name="engagement_notification_content" Visibility="Collapsed" Height="80" HorizontalAlignment="Stretch" VerticalAlignment="Top"/>
      <WebView x:Name="engagement_announcement_content" Visibility="Collapsed"  HorizontalAlignment="Stretch"  VerticalAlignment="Stretch"/>
    </Grid>


En este ejemplo los elementos `WebView` se estiran para ajustarse a su contenedor que automáticamente los cambia el tamaño en caso de rotación de la pantalla o de cambio de tamaño de la ventana.

> [!WARNING]
> Es importante mantener la misma convención de nomenclatura `engagement_notification_content` y `engagement_announcement_content` para los elementos `WebView`. Reach los identifica por su nombre. 
> 
> 

## <a name="handle-datapush-optional"></a>Controlar la inserción de datos (opcional)
Si desea que la aplicación pueda recibir inserciones de datos Reach, debe implementar dos eventos de la clase EngagementReach:

En App.xaml.cs, en el constructor App(), agregue:

            EngagementReach.Instance.DataPushStringReceived += (body) =>
            {
              Debug.WriteLine("String data push message received: " + body);
              return true;
            };

            EngagementReach.Instance.DataPushBase64Received += (decodedBody, encodedBody) =>
            {
              Debug.WriteLine("Base64 data push message received: " + encodedBody);
              // Do something useful with decodedBody like updating an image view
              return true;
            };

Puede ver que la devolución de llamada de cada método devuelve un valor booleano. Engagement envía un comentario a su back-end después de enviar la inserción de datos. Si la devolución de llamada devuelve un valor falso, se enviará el comentario `exit` . De lo contrario, será `action`. Si no se establece ninguna devolución de llamada para los eventos, el comentario `drop` se devolverá a Engagement.

> [!WARNING]
> Engagement no puede recibir varios comentarios para la inserción de datos. Si tiene previsto establecer varios controladores en un evento, tenga en cuenta que los comentarios corresponderán al último controlador enviado. En este caso, es recomendable devolver siempre el mismo valor para evitar tener comentarios confusos en el front-end.
> 
> 

## <a name="customize-ui-optional"></a>Personalizar la interfaz de usuario (opcional)
### <a name="first-step"></a>Primer paso
Puede personalizar la interfaz de usuario de Reach.

Para ello, debe crear una subclase de la clase `EngagementReachHandler` .

**Código de ejemplo:**

            using Microsoft.Azure.Engagement;

            namespace Example
            {
              internal class ExampleReachHandler : EngagementReachHandler
              {
               // Override EngagementReachHandler methods depending on your needs
              }
            }

A continuación, establezca el contenido del campo `EngagementReach.Instance.Handler` con el objeto personalizado en la clase `App.xaml.cs` del método `App()`.

**Código de ejemplo:**

            protected override void OnLaunched(LaunchActivatedEventArgs args)
            {
              // your app initialization 
              EngagementReach.Instance.Handler = new ExampleReachHandler();
              // Engagement Agent and Reach initialization
            }

> [!NOTE]
> De forma predeterminada, Engagement usa su propia implementación de `EngagementReachHandler`.
> No hace falta crear elementos propios. Si lo hace, tiene que invalidar todos los métodos. El comportamiento predeterminado consiste en seleccionar el objeto base de Engagement.
> 
> 

### <a name="web-view"></a>Vista web
De forma predeterminada, Reach usará los recursos integrados del archivo DLL para mostrar las notificaciones y páginas.

Para proporcionar posibilidades de personalización completa, solo se usa la vista web. Si desea personalizar diseños, invalide directamente los archivos de recursos `EngagementAnnouncement.html` y `EngagementNotification.html`. Engagement necesita todo el código de `<body></body>` para que se ejecute correctamente. No obstante, puede agregar una etiqueta exterior `engagement_webview_area`.

Sin embargo, puede decidir usar sus propios recursos.

Puede invalidar los métodos `EngagementReachHandler` en la subclase para indicar a Engagement que debe usar sus diseños (asegúrese de integrar el mecanismo de Engagement):

**Código de ejemplo:**

            // In your subclass of EngagementReachHandler

            public override string GetAnnouncementHTML()
            {
              return base.GetAnnouncementHTML();
            }
            public override string GetAnnouncementName()
            {
              return base.GetAnnouncementName();
            }
            public override string GetNotfificationHTML()
            {
              return base.GetNotfificationHTML();
            }
            public override string GetNotfificationName()
            {
              return base.GetNotfificationName();
            }


De forma predeterminada, AnnouncementHTML es `ms-appx-web:///Resources/EngagementAnnouncement.html`. Representa el archivo html que diseña el contenido de un mensaje de inserción (anuncio de texto, anuncio web y anuncio de sondeo). AnnouncementName es `engagement_announcement_content`. Es el nombre del diseño de vista web de la página xaml.

NotfificationHTML es `ms-appx-web:///Resources/EngagementNotification.html`. Representa el archivo html que diseña la notificación de un mensaje de inserción. NotfificationName es `engagement_notification_content`. Es el nombre del diseño de vista web de la página xaml.

### <a name="customization"></a>Personalización
Puede personalizar la vista web de notificación y anuncio como desee si quiere conservar el objeto de Engagement. Asegúrese de que el objeto de vista web se describa tres veces: la primera, en el archivo xaml, la segunda en el archivo .cs del método "setwebview()" y la tercera en el archivo html.

* En el código xaml, describa el componente webview de diseño gráfico actual.
* En el archivo .cs, puede definir "setwebview()" en el que establecer la dimensión de las dos vistas web de dos (notificación, anuncio). Es muy eficaz al cambiar el tamaño de la aplicación.
* En el archivo html de Engagement, se describen el contenido de la vista web, el diseño y las posiciones de los elementos entre sí.

### <a name="launch-message"></a>Iniciar el mensaje
Cuando un usuario hace clic en una notificación del sistema, Engagement inicia la aplicación, carga el contenido de los mensajes de inserción y muestra la página de la campaña correspondiente.

Hay un retraso entre el inicio de la aplicación y la presentación de la página (según la velocidad de la red).

Para indicar al usuario que algo se está cargando, debería proporcionar algún tipo de información visual, tal como una barra de progreso o un indicador de progreso. Engagement no puede controlar esto por sí solo, pero le ofrece algunos controladores para ello.

Para implementar la devolución de llamada, en App.xaml.cs, en "Public App(){}", agregue lo siguiente:

            /* The application has launched and the content is loading.
             * You should display an indicator here.
             */
            EngagementReach.Instance.RetrieveLaunchMessageStarted += () => { [...] };

            /* The application has finished loading the content and the page
             * is about to be displayed.
             * You should hide the indicator here.
             */
            EngagementReach.Instance.RetrieveLaunchMessageCompleted += () => { [...] };

            /* The content has been loaded, but an error has occurred.
             * You can provide an information to the user.
             * You should hide the indicator here.
             */
            EngagementReach.Instance.RetrieveLaunchMessageFailed += () => { [...] };

Puede establecer la devolución de llamada en el método "Public App(){}" del archivo `App.xaml.cs`, preferentemente antes de la llamada `EngagementReach.Instance.Init()`.

> [!TIP]
> A cada controlador lo llama el subproceso de interfaz de usuario. No tiene que preocuparse cuando se usa un objeto MessageBox u otro elemento relacionado con la interfaz de usuario.
> 
> 

## <a id="push-channel-sharing"></a> Uso compartido de canal de inserción
Si está usando las notificaciones de inserción para otra finalidad en la aplicación, tendrá que usar la característica de uso compartido del canal de inserción del SDK de Engagement. Esto es para evitar la inserción perdida.

* Puede proporcionar su propio canal de inserción a la inicialización de Engagement Reach. El SDK lo usará en lugar de solicitar uno nuevo.

Actualice la inicialización de Engagement Reach con el canal de inserción en el método de `InitEngagement` desde el archivo `App.xaml.cs`:

    /* Your own push channel logic... */
    var pushChannel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    /*...Engagement initialization */
    EngagementAgent.Instance.Init(e);
    EngagementReach.Instance.Init(e,pushChannel);

* También, si desea usar el canal de inserción después de la inicialización de Reach, puede establecer una devolución de llamada en Engagement Reach para obtener el canal de inserción una vez creado por el SDK.

Establezca la devolución de llamada en cualquier lugar **después** de la inicialización de Reach:

    /* Set action on the SDK push channel. */
    EngagementReach.Instance.SetActionOnPushChannel((PushNotificationChannel channel) => 
    {
      /* The forwarded channel can be null if its creation fails for any reason. */
      if (channel != null)
      {
        /* Your own push channel logic... */
      });
    }

## <a name="custom-scheme-tip"></a>Sugerencia de esquema personalizado
Se incluye el uso de esquemas personalizados. Puede enviar otro tipo de URI desde el front-end de Engagement para usarse en la aplicación de Engagement. Los esquemas predeterminados, como `http, ftp, ...`, los administra Windows. Una ventana pedirá información si no hay ninguna aplicación predeterminada instalada en el dispositivo. También puede crear su propio esquema personalizado para su aplicación.

Es una manera sencilla de establecer un esquema personalizado en la aplicación es abrir `Package.appxmanifest` en el panel `Declarations`. Seleccione `Protocol` del cuadro desplegable Declaraciones disponibles y agréguelo. Edite el campo `Name` con el nombre deseado del nuevo protocolo.

Ahora, para usar este protocolo, edite `App.xaml.cs` mediante el método `OnActivated` y no se olvide de inicializar Engagement aquí también:

            /// <summary>
            /// Enter point when app his called by another way than user click
            /// </summary>
            /// <param name="args">Activation args</param>
            protected override void OnActivated(IActivatedEventArgs args)
            {
              /* Init engagement like it was launch by a custom uri scheme */
              EngagementAgent.Instance.Init(args);
              EngagementReach.Instance.Init(args);

              //TODO design action to do when app is launch

              #region Custom scheme use
              if (args.Kind == ActivationKind.Protocol)
              {
                ProtocolActivatedEventArgs myProtocol = (ProtocolActivatedEventArgs)args;

                if (myProtocol.Uri.Scheme.Equals("protocolName"))
                {
                  string path = myProtocol.Uri.AbsolutePath;
                }
              }
              #endregion

