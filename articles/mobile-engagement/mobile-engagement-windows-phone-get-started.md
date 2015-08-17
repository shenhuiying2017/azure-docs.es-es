<properties
	pageTitle="Introducción a Azure Mobile Engagement para aplicaciones Windows Phone Silverlight"
	description="Aprenda a usar Azure Mobile Engagement con capacidades de análisis y notificaciones de inserción en aplicaciones Windows Phone Silverlight."
	services="mobile-engagement"
	documentationCenter="windows"
	authors="piyushjo"
	manager="dwrede"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows-phone"
	ms.devlang="dotnet"
	ms.topic="hero-article" 
	ms.date="04/30/2015"
	ms.author="piyushjo" />

# Introducción a Azure Mobile Engagement para aplicaciones Windows Phone Silverlight

> [AZURE.SELECTOR]
- [Windows Universal](mobile-engagement-windows-store-dotnet-get-started.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-get-started.md)
- [iOS - Obj C](mobile-engagement-ios-get-started.md)
- [iOS - Swift](mobile-engagement-ios-swift-get-started.md)
- [Android](mobile-engagement-android-get-started.md)
- [Cordova](mobile-engagement-cordova-get-started.md)

En este tema se muestra cómo usar Azure Mobile Engagement para comprender el uso que hace de las aplicaciones y enviar notificaciones de inserción a los usuarios segmentados de una aplicación de Windows Phone Silverlight. En este tutorial se demuestra el escenario de difusión sencillo con Mobile Engagement. En él, puede crear una aplicación de Windows Phone Silverlight vacía que recopila datos básicos y recibe notificaciones de inserción mediante el Servicio de notificaciones de inserción de Microsoft (MPNS). Cuando haya terminado, podrá difundir notificaciones de inserción a todos los dispositivos o dirigirse a usuarios específicos en función de las propiedades de sus dispositivos (mediante MPNS). Asegúrese de seguir el tutorial siguiente para ver cómo usar Mobile Engagement para dirigirse a usuarios y grupos de dispositivos específicos.

> [AZURE.NOTE]Si va a desarrollar para Windows Phone 8.1 (no Silverlight), consulte entonces el [tutorial Windows Universal](mobile-engagement-windows-store-dotnet-get-started.md).

Este tutorial requiere lo siguiente:

+ Visual Studio 2013
+ El [SDK de Windows Phone para Mobile Engagement]

> [AZURE.IMPORTANT]La finalización de este tutorial es un requisito previo de todos los demás tutoriales de Mobile Engagement para aplicaciones de Windows Phone Silverlight y, para completarlo, debe disponer de una cuenta activa de Azure. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fes-es%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Evaluación gratuita de Azure</a>.

##<a id="setup-azme"></a>Configure Mobile Engagement para su aplicación Windows Phone Silverlight

1. Inicie sesión en el Portal de administración de Azure y, luego, haga clic en **+NUEVO** en la parte inferior de la pantalla.

2. Haga clic en **Servicios de aplicaciones**, en **Mobile Engagement** y, luego, en **Crear**.

   	![][7]

3. En el menú emergente que aparece, escriba la siguiente información:

   	![][8]

	- **Nombre de la aplicación**: escriba el nombre de la aplicación. Puede usar cualquier carácter.
	- **Plataforma**: seleccione la plataforma de destino (**Windows Phone Silverlight**) para la aplicación (si la aplicación tiene como destino varias plataformas, repita este tutorial para cada plataforma).
	- **Nombre del recurso de la aplicación**: este es el nombre por el que la aplicación será accesible a través de las API y las direcciones URL. Solo debe usar caracteres convencionales de dirección URL. El nombre que se genera automáticamente debería proporcionar una sugerencia sólida. También debe anexar el nombre de la plataforma para evitar cualquier conflicto de nombres, ya que este debe ser único.
	- **Ubicación**: seleccione el centro de datos donde se alojará esta aplicación y, más importante aún, su colección.
	- **Colección**: si ya ha creado una aplicación, seleccione una colección creada anteriormente; en caso contrario, seleccione una colección nueva.
	- **Nombre de la colección**: esto representa el grupo de aplicaciones. También garantizará que todas las aplicaciones se encuentren en un grupo que permitirá cálculos agregados de métricas. Debe usar el nombre de la empresa o departamento aquí si es aplicable.

4. Seleccione la aplicación que acaba de crear en la pestaña **Aplicación**:

5. Haga clic en **Información de conexión** para mostrar la configuración de la conexión que se debe incluir en la integración de SDK en su aplicación móvil.

   	![][10]

6. Copie la **Cadena de conexión**, la necesitará para identificar esta aplicación en el código de aplicación y conectar con Mobile Engagement desde la aplicación de teléfono.

   	![][11]

##<a id="connecting-app"></a>Conectar la aplicación al back-end de Mobile Engagement

En este tutorial se presenta una "integración básica», que es el conjunto mínimo necesario para recopilar los datos y enviar una notificación de inserción. Toda la documentación de integración se encuentra en la [documentación del SDK de Windows Phone para Mobile Engagement].

Crearemos una aplicación básica con Visual Studio para demostrar la integración.

###Crear un nuevo proyecto de Windows Phone Silverlight

1. Inicie Visual Studio y, en la pantalla principal, seleccione **Nuevo proyecto...**.

2. En el menú emergente, seleccione `Store Apps` -> `Windows Phone Apps` -> `Blank App (Windows Phone Silverlight)`. Rellene la aplicación `Name` y `Solution name` y, a continuación, haga clic en **Aceptar**.

   	![][13]

3. Puede elegir como destino la versión **Windows Phone 8.0** o **Windows Phone 8.1**.

Ahora ha creado una nueva aplicación Windows Phone Silverlight en la que se integrará el SDK de Azure Mobile Engagement.

###Conectar la aplicación al back-end de Mobile Engagement

1. Instale el paquete nuget del [SDK de Mobile Engagement Windows Phone] en su proyecto.

2. Abra `WMAppManifest.xml` (en la carpeta Propiedades) y asegúrese de que se declara lo siguiente (agréguelo en caso de que no) en la etiqueta `<Capabilities />`:

		<Capability Name="ID_CAP_NETWORKING" />
		<Capability Name="ID_CAP_IDENTITY_DEVICE" />

	![][20]

3. Ahora, pegue la cadena de conexión que copió anteriormente para la aplicación Mobile Engagement y péguela en el archivo `Resources\EngagementConfiguration.xml`, entre las etiquetas `<connectionString>` y `</connectionString>`:

	![][21]

4. En el archivo `App.xaml.cs`:

	a. Agregar la instrucción `using`

			using Microsoft.Azure.Engagement;

	b. Inicialice el SDK en el método `Application_Launching`:

			private void Application_Launching(object sender, LaunchingEventArgs e)
			{
			  EngagementAgent.Instance.Init();
			}

	c. Inserte lo siguiente en `Application_Activated`.

			private void Application_Activated(object sender, ActivatedEventArgs e)
			{
			   EngagementAgent.Instance.OnActivated(e);
			}

##<a id="monitor"></a>Habilitar supervisión en tiempo real

Para comenzar a enviar datos y asegurarse de que los usuarios estén activos, debe enviar al menos una pantalla (Actividad) al back-end de Mobile Engagement. Para ello, se crearán subclases de `MainPage` con `EngagementPage` que proporciona el SDK de Mobile Engagement.

1. Agregar la instrucción `using`:

		using Microsoft.Azure.Engagement;

2. Reemplace la superclase de `MainPage`, que es anterior a `PhoneApplicationPage`, por `EngagementPage`, tal como se muestra a continuación:

	![][22]

3. En el archivo `MainPage.xml`: a. Agregue a las declaraciones de espacios de nombres:

			xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"

	b. Reemplace `phone:PhoneApplicationPage` en el nombre de etiqueta xml por `engagement:EngagementPage`.

###Asegúrese de que la aplicación está conectada con la supervisión en tiempo real

En esta sección se muestra cómo asegurarse de que la aplicación se conecta al back-end de Mobile Engagement mediante la característica de supervisión en tiempo real de Mobile Engagement.

1. Desplácese hasta el portal de Mobile Engagement.

	En el portal de Azure, asegúrese de que se encuentra en la aplicación que estamos utilizando para este proyecto y, a continuación, haga clic en el botón **Interactuar** en la parte inferior:

	![][26]

2. Será dirigido a la página de configuración del portal de Engagement de la aplicación. Desde allí, haga clic en la pestaña **Monitor**, tal como se muestra a continuación. ![][30]

3. El monitor está listo para mostrar cualquier dispositivo, en tiempo real, que iniciará la aplicación.

4. En Visual Studio, inicie la aplicación en el simulador o en un dispositivo conectado.

5. Si funcionó, ahora debería ver una sesión en el monitor.![][33]

**¡Enhorabuena!** Ha completado correctamente el primer paso de este tutorial con una aplicación que se conecta el back-end Mobile Engagement, que ya envía datos.

##<a id="integrate-push"></a>Habilitación de las notificaciones de inserción y mensajería en la aplicación

Mobile Engagement permite interactuar y llegar a los usuarios mediante notificaciones de inserción y mensajería en la aplicación en el contexto de las campañas. Este módulo se denomina REACH en el portal de Mobile Engagement. En las secciones siguientes se instalará la aplicación para recibirlos.

###Habilitar la aplicación para recibir notificaciones de inserción de MPNS

Agregue nuevas capacidades a su archivo `WMAppManifest.xml`:

		ID_CAP_PUSH_NOTIFICATION
		ID_CAP_WEBBROWSERCOMPONENT

![][34]

###Inicializar el SDK de REACH

1. En `App.xaml.cs`, llame a `EngagementReach.Instance.Init();` en la función `Application_Launching` justo después de la inicialización del agente:

		private void Application_Launching(object sender, LaunchingEventArgs e)
		{
		   EngagementAgent.Instance.Init();
		   EngagementReach.Instance.Init();
		}

2. En `App.xaml.cs`, llame a `EngagementReach.Instance.OnActivated(e);` en la función `Application_Activated` justo después de la inicialización del agente:

		private void Application_Activated(object sender, ActivatedEventArgs e)
		{
		   EngagementAgent.Instance.OnActivated(e);
		   EngagementReach.Instance.OnActivated(e);
		}

Y está listo. Ahora comprobaremos que realizó correctamente la integración básica.

##<a id="send"></a>Enviar una notificación a su aplicación

Ahora crearemos una campaña de notificación de inserción simple que enviará una notificación de inserción a nuestra aplicación.

1. Vaya a la pestaña **COBERTURA** en el portal de Mobile Engagement.

2. Haga clic en **Nuevo anuncio** para crear la campaña de inserción. ![][35]

3. Configure el primer campo de la campaña mediante los pasos siguientes:![][36]

	1. Asigne el nombre deseado a la campaña.
	2. Seleccione **Tiempo de entrega** como *Cualquier momento* para permitir que la aplicación reciba una notificación, independientemente de si se ha iniciado o no.
	3. En el texto de la notificación, escriba el título que aparecerá en negrita en la inserción.
	4. Luego, escriba el mensaje.

4. Desplácese hacia abajo. En la sección del contenido, elija **Solo notificación**. ![][37]

5. Ha terminado de configurar la campaña más básica posible. Ahora, desplácese hacia abajo de nuevo y haga clic en el botón **Crear** para guardar su campaña.

6. Último paso, haga clic en **Activar** para activar su campaña y enviar notificaciones de inserción. ![][39]

7. Ahora debería aparecer una notificación en el dispositivo, **¡Felicidades!**: ![][40]

<!-- URLs. -->
[SDK de Mobile Engagement Windows Phone]: http://go.microsoft.com/?linkid=9874664
[SDK de Windows Phone para Mobile Engagement]: http://go.microsoft.com/?linkid=9874664
[documentación del SDK de Windows Phone para Mobile Engagement]: ../mobile-engagement-windows-phone-integrate-engagement/

<!-- Images. -->
[7]: ./media/mobile-engagement-windows-phone-get-started/create-mobile-engagement-app.png
[8]: ./media/mobile-engagement-windows-phone-get-started/create-azme-popup.png
[10]: ./media/mobile-engagement-windows-phone-get-started/app-main-page-select-connection-info.png
[11]: ./media/mobile-engagement-windows-phone-get-started/app-connection-info-page.png
[13]: ./media/mobile-engagement-windows-phone-get-started/project-properties.png
[20]: ./media/mobile-engagement-windows-phone-get-started/wmappmanifest-capabilities.png
[21]: ./media/mobile-engagement-windows-phone-get-started/add-connection-string.png
[22]: ./media/mobile-engagement-windows-phone-get-started/subclassing.png
[26]: ./media/mobile-engagement-windows-phone-get-started/engage-button.png
[30]: ./media/mobile-engagement-windows-phone-get-started/clic-monitor-tab.png
[33]: ./media/mobile-engagement-windows-phone-get-started/monitor.png
[34]: ./media/mobile-engagement-windows-phone-get-started/reach-capabilities.png
[35]: ./media/mobile-engagement-windows-phone-get-started/new-announcement.png
[36]: ./media/mobile-engagement-windows-phone-get-started/campaign-first-params.png
[37]: ./media/mobile-engagement-windows-phone-get-started/campaign-content.png
[39]: ./media/mobile-engagement-windows-phone-get-started/campaign-activate.png
[40]: ./media/mobile-engagement-windows-phone-get-started/push-screenshot.png
 

<!---HONumber=August15_HO6-->