<properties 
	pageTitle="Integración del SDK de Windows Phone para Azure Mobile Engagement" 
	description="Cómo integrar Engagement en Windows Phone" 					
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="lalathie" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="kapiteir" />


<div class="dev-center-tutorial-selector sublanding"><a href="/documentation/articles/mobile-engagement-windows-store-integrate-engagement/" title="Windows Store">Tienda Windows</a><a href="/documentation/articles/mobile-engagement-windows-phone-integrate-engagement/" title="Windows Phone" class="current">Windows Phone</a><a href="/documentation/articles/mobile-engagement-ios-integrate-engagement/" title="iOS">iOS</a><a href="/documentation/articles/mobile-engagement-android-integrate-engagement/" title="Android" >Android</a></div>Android

# Cómo integrar Engagement en Windows Phone

En este procedimiento se describe la manera más sencilla de activar las funciones de análisis y supervisión de Engagement en su aplicación de Windows Phone.

Los siguientes pasos son suficientes para activar el informe de los registros necesarios para calcular todas las estadísticas en relación con los usuarios, las sesiones, las actividades, los bloqueos y los aspectos técnicos. El informe de los registros necesarios para calcular otras estadísticas, como eventos, errores y trabajos debe realizarse manualmente mediante la API de Engagement (vea [Cómo usar la API de etiquetado avanzado de Mobile Engagement en la aplicación de Windows Phone](mobile-engagement-windows-phone-use-engagement-api.md) a continuación) debido a que estas estadísticas dependen de la aplicación.

## Versiones compatibles

El SDK de Engagement Windows Phone solo se puede integrar en las aplicaciones destinadas a lo siguiente:

-   Windows Phone SO 8.0
-   Windows Phone SO 8.1 con Silverlight

El SDK de Engagement Windows Phone es compatible con dispositivos Windows Phone 8.0 y 8.1.

## Integrar el SDK de Engagement en el proyecto de Windows Phone

El paquete de NuGet para Mobile Engagement todavía no está disponible en línea, por lo que tendrá que descargar nuestro archivo SDK y descomprimirlo. Luego, en Visual Studio, haga clic en el proyecto, vaya a "Administrar paquetes de NuGet", Configuración y agregue la carpeta lib como nuevo origen de paquete.


> [AZURE.IMPORTANT] NuGet es el administrador de paquetes principal para los proyectos .NET. Permite descargar, agregar y copiar todo lo que necesite para usar el SDK de Engagement. En WP 8, Engagement usa bibliotecas de otros fabricantes (Microsoft.Bcl.Build v1.0.10 y paquete de portabilidad Microsoft.Bcl v1.1.3) para la portabilidad entre plataformas y NuGet preguntará automáticamente que los instale también. Puede encontrar más información en el [sitio web de NuGet](http://docs.nuget.org/docs/start-here/overview)..

## Agregar las capacidades

El SDK de Engagement necesita algunas capacidades del SDK de Windows Phone para que funcione correctamente.

Abra el archivo `WMAppManifest.xml` y asegúrese de que en el panel`Capabilities`  se han declarado las siguientes capacidades:

-   `ID_CAP_NETWORKING`
-   `ID_CAP_IDENTITY_DEVICE`

## Inicializar el SDK de Engagement

### Configuración de Engagement

La configuración de Engagement se centraliza en el archivo `Resources\EngagementConfiguration.xml` del proyecto.

Edite este archivo para especificar lo siguiente:

-   La cadena de conexión de la aplicación entre las etiquetas `<connectionString>` y `<\connectionString>`.

Si desea especificarla en tiempo de ejecución, puede llamar al método siguiente antes de la inicialización del agente de Engagement:

			/* Engagement configuration. */
			EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
			engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

			/* Initialize Engagement agent with above configuration. */
			EngagementAgent.Instance.Init(engagementConfiguration);

La cadena de conexión de la aplicación se muestra en el portal de Azure.

### Inicialización de Engagement

Al crear un nuevo proyecto, se genera un archivo`App.xaml.cs` . Esta clase hereda de `Application`y contiene muchos métodos importantes. También se usará para inicializar el SDK de Engagement.

Modifique `App.xaml.cs`:

-   Agregue las instrucciones `using` :

			using Microsoft.Azure.Engagement;

-   Inserte `EngagementAgent.Instance.Init` en el método `Application_Launching` :

			private void Application_Launching(object sender, LaunchingEventArgs e)
			{
			  EngagementAgent.Instance.Init();
			}

-   Inserte `EngagementAgent.Instance.OnActivated` en el método `Application_Activated`:

			private void Application_Activated(object sender, ActivatedEventArgs e)
			{
			   EngagementAgent.Instance.OnActivated(e);
			}

> [AZURE.WARNING] Se desaconseja encarecidamente agregar la inicialización de Engagement en otro lugar de la aplicación. Sin embargo, tenga en cuenta que el método `EngagementAgent.Instance.Init`se ejecuta en un subproceso dedicado y no en el subproceso de la interfaz de usuario.

## Informes básicos

### Método recomendado: sobrecarga de las clases `PhoneApplicationPage`

Para activar el informe de todos los registros que Engagement necesita para calcular las estadísticas de usuarios, sesiones, actividades, bloqueos y aspectos técnicos, puede hacer que todas las subclases `PhoneApplicationPage`hereden de las clases `EngagementPage`.

Este es un ejemplo de cómo hacer esto para una página de la aplicación. Puede hacer lo mismo para todas las páginas de la aplicación.

#### Archivo de código fuente C\#

Modifique el archivo de página `.xaml.cs`:

-   Agregue las instrucciones `using`:

			using Microsoft.Azure.Engagement;

-   Reemplace `PhoneApplicationPage` por `EngagementPage`:

**Sin Engagement:**

			namespace Example
			{
			  public partial class ExamplePage : PhoneApplicationPage
			  {
			    [...]
			  }
			}

**Con Engagement:**

			using Microsoft.Azure.Engagement;
			
			namespace Example
			{
			  public partial class ExamplePage : EngagementPage 
			  {
			    [...]
			  }
			}

> [AZURE.WARNING] Si la página hereda del método `OnNavigatedTo`, asegúrese de permitir la llamada `base.OnNavigatedTo(e)`. De lo contrario, no se informará la actividad. De hecho, `EngagementPage`llama a `StartActivity`dentro del método `OnNavigatedTo`.

#### Archivo XAML

Modifique el archivo de página `.xaml`:

-   Agregue a las declaraciones de espacios de nombres:

			xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"

-   Reemplace `phone:PhoneApplicationPage` por `engagement:EngagementPage`:

**Sin Engagement:**

			<phone:PhoneApplicationPage>
			    <!-- layout -->
			</phone:PhoneApplicationPage>

**Con Engagement:**

			<engagement:EngagementPage 
			    xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP">
			
			    <!-- layout -->
			</engagement:EngagementPage >

#### Invalidar el comportamiento predeterminado

De forma predeterminada, el nombre de clase de la página se informa como el nombre de actividad, sin más. Si la clase usa el sufijo "Página", Engagement también la quitará.

Si desea invalidar el comportamiento predeterminado para el nombre, simplemente agregue lo siguiente a su código:

			// in the .xaml.cs file
			protected override string GetEngagementPageName()
			{
			   /* your code */
			   return "new name";
			}

Si desea informar algunos datos adicionales con su actividad, puede agregar lo siguiente a su código:

			// in the .xaml.cs file
			protected override Dictionary<object,object> GetEngagementPageExtra()
			{
			   /* your code */
			   return extra;
			}

Encontrará más detalles sobre la información adicional [aquí](../mobile-engagement-windows-phone-use-engagement-api/#extras-parameters).

Estos métodos se invocan desde el método `OnNavigatedTo` de la página.

### Método alternativo: llamar a `StartActivity()` manualmente

Si no puede o no quiere sobrecargar las clases `PhoneApplicationPage`, en su lugar, puede iniciar las actividades mediante una llamada directa a los métodos `EngagementAgent`.

Recomendamos que llame a `StartActivity` dentro del método `OnNavigatedTo` de su PhoneApplicationPage.

			protected override void OnNavigatedTo(NavigationEventArgs e)
			{
			   base.OnNavigatedTo(e);
			   EngagementAgent.Instance.StartActivity("MyPage");
			}

> [AZURE.IMPORTANT] Asegúrese de finalizar la sesión correctamente.
>
> El SDK de Windows Phone llama automáticamente al método `EndActivity` cuando se cierra la aplicación. Por lo tanto, es **MUY** recomendable llamar al método `StartActivity` cada vez que cambie la actividad del usuario y **NUNCA** llamar al método `EndActivity`, ya que esto obliga la finalización de la sesión actual.

## Informes avanzados

Opcionalmente, es aconsejable informar eventos, errores y trabajos de aplicación específicos. Para ello, use los otros métodos que se encuentran en la clase `EngagementAgent`. La API de Engagement permite usar todas las capacidades avanzadas de Engagement.

Para obtener más información, consulte [Cómo usar la API de etiquetado avanzado de Mobile Engagement en la aplicación de Windows Phone](mobile-engagement-windows-phone-use-the-engagement-api.md).

## Configuración avanzada

### Deshabilitar los informes automáticos de bloqueo

Puede deshabilitar la característica de informes automáticos de bloqueo de Engagement. A continuación, cuando se produce una excepción no controlada, Engagement no hará nada.

> [AZURE.WARNING] Si tiene previsto deshabilitar esta característica, tenga en cuenta que cuando se produzca un error no controlado en la aplicación, Engagement no enviará la información del bloqueo **NI** tampoco cerrará la sesión ni los trabajos.

Para deshabilitar los informes automáticos de bloqueo, personalice la configuración según la manera en que la declaró:

#### Desde el archivo `EngagementConfiguration.xml`

Establezca el informe de bloqueo en  `false` entre las etiquetas `<reportCrash>`y `</reportCrash>`.

#### Desde el objeto `EngagementConfiguration` en tiempo de ejecución

Establezca el informe de bloqueo mediante el objeto EngagementConfiguration.

			/* Engagement configuration. */

			EngagementConfiguration engagementConfiguration = new EngagementConfiguration(); engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
			/\* Disable Engagement crash reporting. \*/ engagementConfiguration.Agent.ReportCrash = false;

### Modo de ráfaga [beta]

De forma predeterminada, el servicio de Engagement informa los registros en tiempo real. Si la aplicación informa los registros con mucha frecuencia, es mejor almacenar los registros en el búfer e informarlos todos a la vez de forma periódica (esto se denomina "modo de ráfaga").

Para ello, llame al método siguiente:

			EngagementAgent.Instance.SetBurstThreshold(int everyMs);

El argumento es un valor en **milisegundos**. En cualquier momento, si desea volver a activar el registro en tiempo real, simplemente llame al método sin ningún parámetro o con el valor 0.

El modo de ráfaga aumenta ligeramente la duración de la batería, pero afecta al monitor de Engagement: la duración de todas las sesiones y trabajos se redondeará al umbral de ráfaga (por lo tanto, es posible que las sesiones y los trabajos más cortos que el umbral de ráfaga no sean visibles). Se recomienda usar un umbral de ráfaga inferior a 30.000 (30 segundos).

> [AZURE.WARNING] El umbral de ráfaga no se puede configurar en un período inferior a un segundo. Si intenta hacerlo, el SDK mostrará un seguimiento con el error y se restablecerá automáticamente en el valor predeterminado; es decir, cero segundos. Esto hará que el SDK informe los registros en tiempo real.

<!--HONumber=47-->
