---
title: "Introducción a Azure Mobile Engagement para aplicaciones universales de Windows"
description: "Aprenda a usar Azure Mobile Engagement con análisis y notificaciones push para aplicaciones universales de Windows."
services: mobile-engagement
documentationcenter: windows
author: piyushjo
manager: erikre
editor: 
ms.assetid: 48103867-7f64-4646-b019-42bd797d38e2
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/12/2016
ms.author: piyushjo;ricksal
ms.openlocfilehash: 40db7e4dd151ec391c754dc6d4145aeeb8058eca
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2017
---
# <a name="get-started-with-azure-mobile-engagement-for-windows-universal-apps"></a>Introducción a Azure Mobile Engagement para aplicaciones universales de Windows
[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

En este tema se muestra cómo usar Azure Mobile Engagement para comprender el uso que hace de las aplicaciones y enviar notificaciones push a los usuarios segmentados de una aplicación Windows Universal.
En este tutorial se demuestra el escenario de difusión sencillo con Mobile Engagement. Creará una aplicación Windows Universal vacía que recopila datos básicos de uso de aplicaciones y recibe notificaciones push mediante el Servicio de notificaciones de Windows (WNS).

> [!NOTE]
> El servicio Azure Mobile Engagement se retirará en marzo de 2018 y actualmente solo está disponible para los clientes existentes. Para más información, consulte [Mobile Engagement](https://azure.microsoft.com/en-us/services/mobile-engagement/).

## <a name="prerequisites"></a>Requisitos previos
[!INCLUDE [Prereqs](../../includes/mobile-engagement-windows-store-prereqs.md)]

## <a name="set-up-mobile-engagement-for-your-windows-universal-app"></a>Configuración de Mobile Engagement para su aplicación universal de Windows
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>Conectar la aplicación al backend de Mobile Engagement
En este tutorial se presenta una "integración básica", que es el conjunto mínimo necesario para recopilar los datos y enviar una notificación de inserción. Toda la documentación de integración se encuentra en la [Integración del SDK de Windows Universal para Mobile Engagement](mobile-engagement-windows-store-sdk-overview.md).

Crearemos una aplicación básica con Visual Studio para demostrar la integración.

### <a name="create-a-windows-universal-app-project"></a>Creación de un proyecto de aplicación universal de Windows
En los siguientes pasos se supone el uso de Visual Studio de 2015 aunque los pasos son similares en versiones anteriores de Visual Studio.

1. Inicie Visual Studio y, en la pantalla **principal**, seleccione **Nuevo proyecto**.
2. En el menú emergente, seleccione **Windows** -> **Universal** -> **Aplicación vacía** (Windows universal). Rellene los campos **Nombre de la aplicación** y **Nombre de la solución** y luego haga clic en **Aceptar**.

    ![][1]

Ahora ha creado un nuevo proyecto de aplicación universal de Windows en el que se integrará el SDK de Azure Mobile Engagement.

### <a name="connect-your-app-to-mobile-engagement-backend"></a>Conectar la aplicación al back-end de Mobile Engagement
1. Instale el paquete de NuGet [MicrosoftAzure.MobileEngagement] en el proyecto. Si quiere usar las plataformas Windows y Windows Phone, necesitará hacer esto para los dos proyectos. En Windows 8.x y Windows Phone 8.1, el mismo paquete de Nuget coloca los archivos binarios específicos de la plataforma correctos en cada proyecto.
2. Abra **Package.appxmanifest** y asegúrese de que la siguiente se agrega a él:

        Internet (Client)

    ![][2]
3. Ahora, copie la cadena de conexión que copió anteriormente para la aplicación Mobile Engagement y péguela en el archivo `Resources\EngagementConfiguration.xml`, entre las etiquetas `<connectionString>` y `</connectionString>`:

    ![][3]

    > [!TIP]
    > Si su aplicación va a tener como destino las plataformas Windows y Windows Phone, aun así se deben crear dos aplicaciones Mobile Engagement, una para cada plataforma compatible. De esta forma se garantiza que puede crear una segmentación correcta de la audiencia y que puede enviar notificaciones de destino de forma adecuada para cada plataforma.

    > [!IMPORTANT]
    > NuGet no copia automáticamente los recursos SDK en su aplicación de Windows 10 UWP. Tendrá que hacerlo manualmente siguiendo los pasos que aparecen cuando se instala el paquete Nuget (readme.txt).  

1. En el archivo `App.xaml.cs`:

    a. Agregue la instrucción `using`:

            using Microsoft.Azure.Engagement;

    b. Agregue un método que inicialice Engagement:

           private void InitEngagement(IActivatedEventArgs e)
           {
             EngagementAgent.Instance.Init(e);

             //... rest of the code
           }

    c. Inicialice el SDK del método **OnLaunched** :

            protected override void OnLaunched(LaunchActivatedEventArgs e)
            {
              InitEngagement(e);

              //... rest of the code
            }

    c. Inserte el siguiente código en el método **OnActivated** y agregue el método si aún no está presente:

            protected override void OnActivated(IActivatedEventArgs e)
            {
              InitEngagement(e);

              //... rest of the code
            }

## <a id="monitor"></a>Habilitación de la supervisión en tiempo real
Para comenzar a enviar datos y asegurarse de que los usuarios estén activos, debe enviar al menos una pantalla (Actividad) al back-end de Mobile Engagement.

1. En **MainPage.xaml.cs**, agregue la siguiente instrucción `using`:

    using Microsoft.Azure.Engagement.Overlay;
2. Cambie la clase base de **MainPage** de **Page** a **EngagementPageOverlay**:

        class MainPage : EngagementPageOverlay
3. En el archivo `MainPage.xaml`:

    a. Agregue a las declaraciones de espacios de nombres:

        xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay"

    b. Reemplace **Page** en el nombre de etiqueta XML por **engagement:EngagementPageOverlay**.

> [!IMPORTANT]
> Si la página invalida el método `OnNavigatedTo`, no olvide llamar a `base.OnNavigatedTo(e)`. De lo contrario, la actividad no se notifica (`EngagementPage` llama a `StartActivity` dentro de su método `OnNavigatedTo`). Esto es especialmente importante en un proyecto de Windows Phone cuando la plantilla predeterminada tiene un método `OnNavigatedTo` .
>
> Para **aplicaciones universales de Windows 10**, use el método recomendado en la sección "Método recomendado: sobrecarga de las clases Page" de [Informes avanzados con el SDK de Engagement para aplicaciones universales de Windows](mobile-engagement-windows-store-advanced-reporting.md), en lugar del que se mencionó anteriormente.

## <a id="monitor"></a>Conexión de la aplicación con la supervisión en tiempo real
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>Habilitación de las notificaciones de inserción y la mensajería en aplicación
Mobile Engagement permite interactuar y llegar a los usuarios mediante notificaciones push y mensajería en la aplicación en el contexto de las campañas. Este módulo se denomina REACH en el portal de Mobile Engagement.
En las secciones siguientes se instala la aplicación para recibirlos.

### <a name="enable-your-app-to-receive-wns-push-notifications"></a>Habilitar la aplicación para recibir notificaciones de inserción de WNS
1. En el archivo , en la pestaña `Package.appxmanifest`Aplicación**, en** Notificaciones**, seleccione** Sí**** en **Capacidad de aviso:**.

    ![][5]

### <a name="initialize-the-reach-sdk"></a>Inicializar el SDK de REACH
En `App.xaml.cs`, llame a **EngagementReach.Instance.Init();** en la función **InitEngagement** inmediatamente después de la inicialización del agente:

        private void InitEngagement(IActivatedEventArgs e)
        {
           EngagementAgent.Instance.Init(e);
           EngagementReach.Instance.Init(e);
        }

Está listo para enviar una notificación del sistema. Ahora comprobaremos que ha llevado a cabo correctamente esta integración básica.

### <a name="grant-access-to-mobile-engagement-to-send-notifications"></a>Concesión de acceso a Mobile Engagement para enviar notificaciones
1. Abra el [Centro de desarrollo de Tienda Windows] en el explorador web, inicie sesión y cree una cuenta, si es necesario.
2. Haga clic en **Panel** en la esquina superior derecha y, luego, haga clic en **Crear una nueva aplicación** en el menú del panel izquierdo.

    ![][9]
3. Cree la aplicación mediante la reserva de su nombre.

    ![][10]
4. Una vez creada la aplicación, navegue a **Servicios -> Notificaciones push** en el menú izquierdo.

    ![][11]
5. En la sección Notificaciones push, haga clic en el vínculo **sitio de Live Services**.

    ![][12]
6. Desplácese a la sección de credenciales de inserción. Asegúrese de que se encuentra en la sección **Configuración de aplicaciones** y, luego, copie su **SID del paquete** y **Secreto del cliente**

    ![][13]
7. Navegue a **Configuración** en el portal de Mobile Engagement y haga clic en la sección **Inserción nativa** de la izquierda. Luego, haga clic en el botón **Editar** para especificar el **identificador de seguridad de paquete (SID)** y su **clave secreta**, como se muestra a continuación:

    ![][6]
8. Por último, asegúrese de que asoció la aplicación de Visual Studio a esta aplicación creada en la tienda de aplicaciones. Haga clic en **Asociar aplicación con la Tienda** en Visual Studio.

    ![][7]

## <a id="send"></a>Enviar una notificación a su aplicación
[!INCLUDE [Create Windows Push campaign](../../includes/mobile-engagement-windows-push-campaign.md)]

Si se ejecuta la aplicación, verá una notificación en aplicación. De lo contrario, si la aplicación está cerrada, verá una notificación del sistema.
Si ve una notificación en aplicación, pero no una notificación del sistema, y la aplicación se ejecuta en modo de depuración en Visual Studio, pruebe **Eventos del ciclo de vida -> Suspender** en la barra de herramientas para asegurarse de que la aplicación está realmente suspendida. Si acaba de hacer clic en el botón de inicio mientras se depura la aplicación en Visual Studio, no siempre se suspenderá y, mientras vea la notificación como desde la aplicación, no se mostrará como notificación del sistema.  

![][8]

<!-- URLs. -->
[Mobile Engagement Windows Universal SDK documentation]: ../mobile-engagement-windows-store-integrate-engagement/
[MicrosoftAzure.MobileEngagement]: http://go.microsoft.com/?linkid=9864592
[Centro de desarrollo de Tienda Windows]: https://dev.windows.com
[Windows Universal Apps - Overlay integration]: ../mobile-engagement-windows-store-integrate-engagement-reach/#overlay-integration

<!-- Images. -->
[1]: ./media/mobile-engagement-windows-store-dotnet-get-started/universal-app-creation.png
[2]: ./media/mobile-engagement-windows-store-dotnet-get-started/manifest-capabilities.png
[3]: ./media/mobile-engagement-windows-store-dotnet-get-started/add-connection-info.png
[5]: ./media/mobile-engagement-windows-store-dotnet-get-started/manifest-toast.png
[6]: ./media/mobile-engagement-windows-store-dotnet-get-started/enter-credentials.png
[7]: ./media/mobile-engagement-windows-store-dotnet-get-started/associate-app-store.png
[8]: ./media/mobile-engagement-windows-store-dotnet-get-started/vs-suspend.png
[9]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_create_app.png
[10]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_app_name.png
[11]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_services_push.png
[12]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_services_push_1.png
[13]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_services_push_creds.png
