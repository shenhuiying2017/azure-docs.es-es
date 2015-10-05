<properties
	pageTitle="Introducción a Azure Mobile Engagement para aplicaciones universales de Windows"
	description="Aprenda a usar Azure Mobile Engagement con análisis y notificaciones push para aplicaciones universales de Windows."
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="dwrede"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows-store"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="09/22/2015"
	ms.author="piyushjo" />

# Introducción a Azure Mobile Engagement para aplicaciones universales de Windows

> [AZURE.SELECTOR]
- [Universal Windows](mobile-engagement-windows-store-dotnet-get-started.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-get-started.md)
- [iOS | Obj C](mobile-engagement-ios-get-started.md)
- [iOS | Swift](mobile-engagement-ios-swift-get-started.md)
- [Android](mobile-engagement-android-get-started.md)
- [Cordova](mobile-engagement-cordova-get-started.md)

En este tema se muestra cómo usar Azure Mobile Engagement para comprender el uso que hace de las aplicaciones y enviar notificaciones de inserción a los usuarios segmentados de una aplicación Windows Universal. En este tutorial se demuestra el escenario de difusión sencillo con Mobile Engagement. Creará una aplicación Windows Universal vacía que recopila datos básicos de uso de aplicaciones y recibe notificaciones de inserción mediante el Servicio de notificaciones de Windows (WNS).

Este tutorial requiere lo siguiente:

+ Visual Studio 2013
+ Paquete de Nuget [MicrosoftAzure.MobileEngagement]

> [AZURE.IMPORTANT]Completar este tutorial es un requisito previo para todos los tutoriales de Mobile Engagement para aplicaciones Windows Universal. Para completarlo, deberá tener una cuenta de Azure activa. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fes-ES%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Evaluación gratuita de Azure</a>.

##<a id="setup-azme"></a>Configure Mobile Engagement para su aplicación Windows Universal

[AZURE.INCLUDE [Crear aplicación de Mobile Engagement en el portal](../../includes/mobile-engagement-create-app-in-portal.md)]

##<a id="connecting-app"></a>Conectar la aplicación al backend de Mobile Engagement

En este tutorial se presenta una "integración básica", que es el conjunto mínimo necesario para recopilar los datos y enviar una notificación de inserción. Toda la documentación de integración se encuentra en la [Integración del SDK de Windows Universal para Mobile Engagement](../mobile-engagement-windows-store-sdk-overview/).

Crearemos una aplicación básica con Visual Studio para demostrar la integración.

###Cree un nuevo proyecto de aplicación Windows Universal

En los siguientes pasos se supone el uso de Visual Studio de 2015 aunque los pasos son similares en versiones anteriores de Visual Studio.

1. Inicie Visual Studio y, en la pantalla **Principal**, seleccione **Nuevo proyecto**.

2. En el menú emergente, seleccione **Windows 8** -> **Universal** -> **Aplicación vacía (Universal Windows 8.1)**. Rellene el **Nombre** de la aplicación y el **Nombre de la solución** y luego haga clic en **Aceptar**.

    ![][1]

> [AZURE.IMPORTANT]Azure Mobile Engagement no admite aún aplicaciones universales de Windows 10.

Ahora ha creado un nuevo proyecto de aplicación universal de Windows en el que se integrará el SDK de Azure Mobile Engagement.

###Conectar la aplicación al back-end de Mobile Engagement

1. Instale el paquete de NuGet [MicrosoftAzure.MobileEngagement] en su proyecto. Si desea usar las plataformas Windows y Windows Phone, necesitará hacer esto para los dos proyectos. El mismo paquete de Nuget colocará los binarios específicos de la plataforma correcta en cada proyecto.

2. Abra **Package.appxmanifest** y asegúrese de que se ha agregado la siguiente funcionalidad allí:

		Internet (Client)

	![][2]

3. Ahora, copie la cadena de conexión que copió anteriormente para la aplicación Mobile Engagement y péguela en el archivo `Resources\EngagementConfiguration.xml`, entre las etiquetas `<connectionString>` y `</connectionString>`:

	![][3]

	>[AZURE.TIP]Si su aplicación va a tener como destino Windows y las plataformas Windows Phone, a continuación, se deberán crear dos aplicaciones Mobile Engagement, una para cada plataforma compatible. Esto permite garantizar que puede crear una segmentación correcta de la audiencia y que puede enviar notificaciones de destino de forma adecuada para cada plataforma.

4. En el archivo `App.xaml.cs`:

	a. Agregue la instrucción `using`:

			using Microsoft.Azure.Engagement;

	b. Inicialice el SDK en el método **OnLaunched**:

			protected override void OnLaunched(LaunchActivatedEventArgs e)
			{
			  EngagementAgent.Instance.Init(e);

			  //... rest of the code
			}

	c. Inserte lo siguiente en el método **OnActivated** y agregue el método si aún no está presente:

			protected override void OnActivated(IActivatedEventArgs e)
			{
			  EngagementAgent.Instance.Init(e);

			  //... rest of the code
			}

##<a id="monitor"></a>Habilitación de la supervisión en tiempo real

Para comenzar a enviar datos y asegurarse de que los usuarios estén activos, debe enviar al menos una pantalla (Actividad) al back-end de Mobile Engagement.

1. 	En **MainPage.xaml.cs**, agregue la instrucción `using`:

		using Microsoft.Azure.Engagement;

2. Reemplace la clase base de **MainPage** de **Page** a **EngagementPage**:

		class MainPage : EngagementPage

3. En el archivo `MainPage.xaml`:

	a. Agregue a las declaraciones de espacios de nombres:

		xmlns:engagement="using:Microsoft.Azure.Engagement"

	b. Reemplace **Page** en el nombre de etiqueta XML por **engagement:EngagementPage**.
	
> [AZURE.IMPORTANT]Si la página invalida el método `OnNavigatedTo`, no olvide llamar a `base.OnNavigatedTo(e)`. De lo contrario, no se informará de la actividad (`EngagementPage` llama a `StartActivity` en su método `OnNavigatedTo`). Esto es especialmente importante en un proyecto de Windows Phone, donde la plantilla predeterminada tiene un método `OnNavigatedTo`.

##<a id="monitor"></a>Conectar la aplicación con la supervisión en tiempo real

[AZURE.INCLUDE [Conectar la aplicación con la supervisión en tiempo real](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Habilitación de notificaciones push y mensajería en la aplicación

Mobile Engagement permite interactuar y llegar a los usuarios mediante notificaciones push y mensajería en la aplicación en el contexto de las campañas. Este módulo se denomina REACH en el portal de Mobile Engagement. En las secciones siguientes se instala la aplicación para recibirlos.

###Habilitar la aplicación para recibir notificaciones de inserción de WNS

1. En el archivo `Package.appxmanifest`, en la pestaña **Aplicación**, en **Notificaciones**, establezca **Capacidad de aviso:** en **Sí**.

	![][5]

###Inicializar el SDK de REACH

1. En `App.xaml.cs`, llame a **EngagementReach.Instance.Init();** en la función **OnLaunched** justo después de la inicialización del agente:

		protected override void OnLaunched(LaunchActivatedEventArgs e)
		{
		   EngagementAgent.Instance.Init(e);
		   EngagementReach.Instance.Init(e);
		}

2. En `App.xaml.cs`, llame a **EngagementReach.Instance.Init();** en la función **OnActivated** justo después de la inicialización del agente:

		protected override void OnActivated(IActivatedEventArgs e)
		{
		   EngagementAgent.Instance.Init(e);
		   EngagementReach.Instance.Init(e);
		}

Ahora está todo listo para enviar un aviso. Ahora comprobaremos que ha llevado a cabo correctamente esta integración básica.

###Concesión de acceso a Mobile Engagement para enviar notificaciones

1. Tendrá que asociar la aplicación con una aplicación de la Tienda Windows para obtener su **Identificador de seguridad de paquete (SID)** y su **Clave secreta** (Secreto de cliente). Puede crear una aplicación desde el [Centro de desarrollo de la Tienda Windows] y, luego, asegúrese de **Asociar la aplicación con la Tienda** desde Visual Studio.

2. Desplácese a la **Configuración** del portal de Mobile Engagement y haga clic en la sección **Inserción nativa** de la izquierda.

3. Haga clic en el botón **Editar** para especificar el **Identificador de seguridad de paquete (SID)** y su **Clave secreta**, tal como se muestra a continuación:

	![][6]

##<a id="send"></a>Enviar una notificación a su aplicación

[AZURE.INCLUDE [Crear campaña de inserción de Windows](../../includes/mobile-engagement-windows-push-campaign.md)]

Ahora debería ver una notificación del sistema de su campaña en el dispositivo (la aplicación debe cerrarse para ver esta notificación). Si se estaba ejecutando la aplicación, asegúrese de que esté cerrada durante dos minutos antes de activar la campaña para poder recibir la notificación del sistema. Si desea integrar la notificación de la aplicación para que la notificación se muestre en la aplicación cuando se abre, consulte [Aplicaciones Windows Universal - Integración de superposición].

<!-- URLs. -->
[Mobile Engagement Windows Universal SDK documentation]: ../mobile-engagement-windows-store-integrate-engagement/
[MicrosoftAzure.MobileEngagement]: http://go.microsoft.com/?linkid=9864592
[Centro de desarrollo de la Tienda Windows]: http://go.microsoft.com/fwlink/p/?linkid=266582&clcid=0x409
[Aplicaciones Windows Universal - Integración de superposición]: ../mobile-engagement-windows-store-integrate-engagement-reach/#overlay-integration

<!-- Images. -->
[1]: ./media/mobile-engagement-windows-store-dotnet-get-started/UniversalAppCreation.png
[2]: ./media/mobile-engagement-windows-store-dotnet-get-started/manifest-capabilities.png
[3]: ./media/mobile-engagement-windows-store-dotnet-get-started/add-connection-info.png
[5]: ./media/mobile-engagement-windows-store-dotnet-get-started/manifest-toast.png
[6]: ./media/mobile-engagement-windows-store-dotnet-get-started/enter-credentials.png

<!---HONumber=Sept15_HO4-->