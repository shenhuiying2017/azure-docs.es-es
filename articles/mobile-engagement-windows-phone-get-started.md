<properties 
	pageTitle="Introducción a Azure Mobile Engagement con Windows Phone" 
	description="Aprenda a usar Azure Mobile Engagement con capacidades de análisis y notificaciones de inserción en Windows Phone."
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="kpiteira" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="C#" 
	ms.topic="article" 
	ms.date="02/11/2015" 
	ms.author="kapiteir" />
	
# Introducción a Mobile Engagement

<div class="dev-center-tutorial-selector sublanding"><a href="/documentation/articles/mobile-engagement-windows-store-dotnet-get-started/" title="Windows Universal">Tienda Windows</a><a href="/documentation/articles/mobile-engagement-windows-phone-get-started/" title="Windows Phone" class="current">Windows Phone</a><a href="/documentation/articles/mobile-engagement-ios-get-started/" title="iOS">iOS</a><a href="/documentation/articles/mobile-engagement-android-get-started/" title="Android">Android</a></div>

En este tema se muestra cómo usar Azure Mobile Engagement para comprender el uso que hace de las aplicaciones y enviar notificaciones de inserción a los usuarios segmentados de una aplicación de Windows Phone. 
En este tutorial se demuestra el escenario de difusión sencillo con Mobile Engagement. En él, puede crear una aplicación de Windows Phone vacía que recopila datos básicos y recibe notificaciones de inserción mediante el Servicio de notificaciones push de Microsoft (MPNS). Cuando haya terminado, podrá difundir notificaciones de inserción a todos los dispositivos o dirigirse a usuarios específicos en función de las propiedades de sus dispositivos. Asegúrese de seguir el tutorial siguiente para ver cómo usar Mobile Engagement para dirigirse a usuarios y grupos de dispositivos específicos.


Este tutorial requiere lo siguiente:

+ Visual Studio 2013
+ El [SDK de Windows Phone para Mobile Engagement]

> [AZURE.IMPORTANT] La finalización de este tutorial es un requisito previo de todos los demás tutoriales de Mobile Engagement para aplicaciones de Windows Phone y, para completarlo, debe disponer de una cuenta activa de Azure. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fes-es%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Evaluación gratuita de Azure</a>.

## <a id="setup-azme"></a>Configurar Mobile Engagement para su aplicación

1. Inicie sesión en el [Portal de administración de Azure] y, a continuación, haga clic en **+NUEVO** en la parte inferior de la pantalla.

2. Haga clic en **Servicios de aplicaciones**, en **Mobile Engagement** y luego en **Crear**.

   	![][7]

3. En el menú emergente que aparece, escriba la siguiente información:
 
   	![][8]

	1. **Nombre de la aplicación**: puede escribir el nombre de la aplicación. Puede usar cualquier carácter.
	2. **Plataforma**: Seleccione la plataforma de destino de dicha aplicación (si la aplicación tiene como destino varias plataformas, repita este tutorial para cada plataforma).
	3. **Nombre de recurso de la aplicación**: Este es el nombre por el que la aplicación será accesible a través de las API y las direcciones URL. Le recomendamos que use solo caracteres convencionales de dirección URL: el nombre que se genera automáticamente debería proporcionar una sugerencia sólida. También recomendamos anexar el nombre de la plataforma para evitar cualquier conflicto de nombres, ya que este debe ser único.
	4. **Ubicación**: Seleccione el centro de datos en el que se hospedará la aplicación y, más importante aún, su colección (vea a continuación).
	5. **Colección**: Si ya creó una aplicación, seleccione una colección creada anteriormente. En caso contrario, seleccione Nueva colección.
	6. **Nombre de la colección**: Esto representa el grupo de aplicaciones. También garantizará que todas las aplicaciones se encuentren en un grupo que permitirá cálculos agregados. Recomendamos encarecidamente que use el nombre de la empresa o el departamento.


	Cuando haya terminado, haga clic en el botón de comprobación para finalizar la creación de la aplicación.

4. Ahora, en la pestaña **Aplicación**, haga clic en la aplicación recién creada o selecciónela.
 
   	![][9]

5. Luego, haga clic en **Información de conexión** para mostrar la configuración de conexión que se colocará en la integración de SDK.
 
   	![][10]

6. Por último, anote la **Cadena de conexión**, que es lo que necesitará para identificar esta aplicación en el código de aplicación.

   	![][11]

	>[AZURE.TIP] Para su comodidad, puede usar el icono "Copiar" a la derecha de la cadena de conexión para copiarlo en el Portapapeles.

## <a id="connecting-app"></a>Conectar la aplicación al back-end de Mobile Engagement

En este tutorial se presenta una "integración básica", que es el conjunto mínimo necesario para recopilar los datos y enviar una notificación de inserción. Toda la documentación de integración se encuentra en la [documentación del SDK de Windows Phone para Mobile Engagement].

Crearemos una aplicación básica con Visual Studio para demostrar la integración.

### Crear un nuevo proyecto de Windows Phone

Puede omitir este paso si ya dispone de una aplicación y está familiarizado con el desarrollo de Windows Phone.

1. Inicie Visual Studio y, en la pantalla principal, seleccione **Nuevo proyecto...**.

   	![][12]

2. En el menú emergente (1), seleccione  `Windows Phone` y luego (2) seleccione `Aplicación de Windows Phone`.  Además, (3) rellene los datos de la aplicación ( `Nombre` y (4) `Nombre de la solución` ) y haga clic en **Aceptar**.

   	![][13]

El proyecto se crea con la aplicación de demostración que se integrará a Mobile Engagement.

### Incluir la biblioteca de SDK en el proyecto

Descargar e integrar la biblioteca de SDK

1. Descargue el [SDK de Windows Phone para Mobile Engagement]
2. Extraiga el archivo .tar.gz en una carpeta del equipo.
3. Vaya a `PROYECTO` y luego a `Administrar paquetes de NuGet...`
4. En el menú emergente, haga clic en Configuración.
5. Presione el botón `+` para crear un nuevo origen.
	![][17]

6. Haga clic en el botón `...` situado en la parte inferior para seleccionar el origen y navegue hasta la carpeta lib de la descarga de SDK extraído (vea el paso 2). A continuación, presione `Seleccionar`.
	![][18]

7. El SDK se agregará como un origen. Haga clic en `Aceptar`.
	![][19]


### Conectar la aplicación al back-end de Mobile Engagement mediante la cadena de conexión

1. El SDK de Engagement necesita algunas capacidades del SDK de Windows Phone para que funcione correctamente.
Abra el archivo WMAppManifest.xml y asegúrese de que las siguientes capacidades se declaran en el panel Capacidades:
	- ID_CAP_NETWORKING
	- ID_CAP_IDENTITY_DEVICE


	![][20]

2. Vuelva a la página **Información de conexión** de la aplicación en el portal de Azure y copie la **Cadena de conexión**.

	![][11]

3. Péguelo en el archivo `Resources\EngagementConfiguration.xml`, entre las etiquetas `<connectionString>`y `</connectionString>`:

	![][21]

4. En el archivo `App.xaml.cs`:
	1. Agregar la instrucción `using`

			using Microsoft.Azure.Engagement;

	2. Inicialice el SDK en el método `Application_Launching`:
			
			private void Application_Launching(object sender, LaunchingEventArgs e)
			{
			  EngagementAgent.Instance.Init();
			}

	3. Inserte lo siguiente en `Application_Activated`.

			private void Application_Activated(object sender, ActivatedEventArgs e)
			{
			   EngagementAgent.Instance.OnActivated(e);
			}

### Enviar una pantalla a Mobile Engagement

Para comenzar a enviar datos y asegurarse de que los usuarios estén activos, debe enviar al menos una pantalla (Actividad) al back-end de Mobile Engagement. Para ello, se crearán subclases de `MainPage` con `EngagementPage` que proporciona el SDK de Mobile Engagement.

1. Para ello, reemplace la superclase de `MainPage`, que se encuentra antes de `PhoneApplicationPage`, con EngagementPage, tal como se muestra a continuación:

	![][22]

	>[AZURE.NOTE] No se olvide de resolver la clase si aparece subrayada en rojo. Para ello, agregue `using Microsoft.Azure.Engagement;` a las cláusulas `using`.

2. En el archivo `MainPage.xml`:
	1. Agregue a las declaraciones de espacios de nombres:

			xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"
	2. Reemplace `phone:PhoneApplicationPage` en el nombre de etiqueta xml con `engagement:EngagementPage`.

## <a id="monitor"></a>Cómo comprobar que la aplicación está conectada con la supervisión en tiempo real

En esta sección se muestra cómo asegurarse de que la aplicación se conecta al back-end de Mobile Engagement mediante la característica de supervisión en tiempo real de Mobile Engagement.

1. Desplácese hasta el portal de Mobile Engagement.

	En el portal de Azure, asegúrese de que se encuentra en la aplicación que está usando para este proyecto y haga clic en el botón **Ocupar** situado en la parte inferior.

	![][26]

2. Será dirigido a la página de configuración del portal de Engagement de la aplicación. Desde allí, haga clic en la pestaña **Monitor**, tal como se muestra a continuación.
	![][30]

3. El monitor está listo para mostrar cualquier dispositivo, en tiempo real, que iniciará la aplicación.
	![][31]

4. En Visual Studio, inicie la aplicación en el emulador o en un dispositivo conectado. Para ello, haga clic en el triángulo verde y seleccione el dispositivo.

5. Si funcionó, ahora debería ver una sesión en el monitor. 
	![][33]

**¡Felicidades!** Ha completado correctamente el primer paso de este tutorial con una aplicación que se conecta el back-end Mobile Engagement, que ya envía datos.


## <a id="integrate-push"></a>Habilitación de las notificaciones de inserción y mensajería en la aplicación

Mobile Engagement permite interactuar y llegar a los usuarios mediante notificaciones de inserción y mensajería en la aplicación en el contexto de las campañas. Este módulo se denomina REACH en el portal de Mobile Engagement.
En las secciones siguientes se instalará la aplicación para recibirlos.

### Habilitar la aplicación para recibir notificaciones de inserción de MPNS

Agregue nuevas capacidades a su archivo `WMAppManifest.xml`:

		ID_CAP_PUSH_NOTIFICATION
		ID_CAP_WEBBROWSERCOMPONENT

![][34]

### Inicializar el SDK de REACH

1. En `App.xaml.cs`, llame a `EngagementReach.Instance.Init();` en la función `Application_Launching` justo después de la inicialización del agente:

		private void Application_Launching(object sender, LaunchingEventArgs e)
		{
		   EngagementAgent.Instance.Init();
		   EngagementReach.Instance.Init();
		}

2. En `App.xaml.cs`, llame a `EngagementReach.Instance.OnActivated(e);` en la función`Application_Activated` justo después de la inicialización del agente:

		private void Application_Activated(object sender, ActivatedEventArgs e)
		{
		   EngagementAgent.Instance.OnActivated(e);
		   EngagementReach.Instance.OnActivated(e);
		}

Y está listo. Ahora comprobaremos que realizó correctamente la integración básica.

## <a id="send"></a>Cómo enviar una notificación a su aplicación

Ahora crearemos una campaña de notificación de inserción simple que enviará una notificación de inserción a nuestra aplicación.

1. Navegue hasta la pestaña **REACH** del portal de Mobile Engagement.
2. Haga clic en **Nuevo anuncio** para crear la campaña de inserción.
	![][35]

3. Configure el primer campo de la campaña mediante los pasos siguientes:
	![][36]

	1. Asigne el nombre deseado a la campaña.
	2. Seleccione **Tiempo de entrega** como *Any time* para permitir que la aplicación reciba una notificación, independientemente de si se ha iniciado o no.
	3. En el texto de la notificación, escriba el título que aparecerá en negrita en la inserción.
	4. Luego, escriba el mensaje.

4. Desplácese hacia abajo y, en la sección de contenido, seleccione **Solo notificación**.
![][37]

5. Ha terminado de configurar la campaña más básica posible. Ahora, desplácese hacia abajo de nuevo y cree la campaña para guardarla.
![][38]

6. Como último paso, active su campaña.
![][39]

Debería aparecer una notificación en el dispositivo, **¡Felicidades!**:
![][40]


<!-- URLs. -->
[SDK de Windows Phone para Mobile Engagement]: http://go.microsoft.com/?linkid=9874664

<!-- Images. -->
[7]: ./media/mobile-engagement-windows-phone-get-started/create-mobile-engagement-app.png
[8]: ./media/mobile-engagement-windows-phone-get-started/create-azme-popup.png
[9]: ./media/mobile-engagement-windows-phone-get-started/select-app.png
[10]: ./media/mobile-engagement-windows-phone-get-started/app-main-page-select-connection-info.png
[11]: ./media/mobile-engagement-windows-phone-get-started/app-connection-info-page.png
[12]: ./media/mobile-engagement-windows-phone-get-started/new-project.png
[13]: ./media/mobile-engagement-windows-phone-get-started/project-properties.png
[17]: ./media/mobile-engagement-windows-phone-get-started/manage-nuget-settings-new-source.png
[18]: ./media/mobile-engagement-windows-phone-get-started/manage-nuget-settings-new-source-add-lib.png
[19]: ./media/mobile-engagement-windows-phone-get-started/manage-nuget-settings-new-source-added.png
[20]: ./media/mobile-engagement-windows-phone-get-started/wmappmanifest-capabilities.png
[21]: ./media/mobile-engagement-windows-phone-get-started/add-connection-string.png
[22]: ./media/mobile-engagement-windows-phone-get-started/subclassing.png
[26]: ./media/mobile-engagement-windows-phone-get-started/engage-button.png
[27]: ./media/mobile-engagement-windows-phone-get-started/engagement-portal.png

[28]: ./media/mobile-engagement-windows-phone-get-started/native-push-settings.png
[29]: ./media/mobile-engagement-windows-phone-get-started/api-key.png
[30]: ./media/mobile-engagement-windows-phone-get-started/clic-monitor-tab.png
[31]: ./media/mobile-engagement-windows-phone-get-started/monitor.png
[32]: ./media/mobile-engagement-windows-phone-get-started/launch.png
[33]: ./media/mobile-engagement-windows-phone-get-started/monitor.png
[34]: ./media/mobile-engagement-windows-phone-get-started/reach-capabilities.png
[35]: ./media/mobile-engagement-windows-phone-get-started/new-announcement.png
[36]: ./media/mobile-engagement-windows-phone-get-started/campaign-first-params.png
[37]: ./media/mobile-engagement-windows-phone-get-started/campaign-content.png
[38]: ./media/mobile-engagement-windows-phone-get-started/campaign-create.png
[39]: ./media/mobile-engagement-windows-phone-get-started/campaign-activate.png
[40]: ./media/mobile-engagement-windows-phone-get-started/push-screenshot.png

<!--HONumber=47-->
