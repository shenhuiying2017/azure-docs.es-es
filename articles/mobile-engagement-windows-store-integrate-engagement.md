<properties 
	pageTitle="Información general del SDK de la Tienda Windows para Azure Mobile Engagement" 
	description="Actualizaciones y procedimientos más recientes del SDK de la Tienda Windows para Azure Mobile Engagement" 					
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="lalathie" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="kapiteir" />

<div class="dev-center-tutorial-selector sublanding"><a href="/documentation/articles/mobile-engagement-windows-store-integrate-engagement/" title="Windows Store" class="current">Tienda Windows</a><a href="/documentation/articles/mobile-engagement-windows-phone-integrate-engagement/" title="Windows Phone">Windows Phone</a><a href="/documentation/articles/mobile-engagement-ios-integrate-engagement/" title="iOS">iOS</a><a href="/documentation/articles/mobile-engagement-android-integrate-engagement/" title="Android" >Android</a></div>Android

# Cómo integrar Engagement en Windows

En este procedimiento se describe la manera más sencilla de activar las funciones de análisis y supervisión de Engagement en su aplicación de Windows.

Los siguientes pasos son suficientes para activar el informe de los registros necesarios para calcular todas las estadísticas en relación con los usuarios, las sesiones, las actividades, los bloqueos y los aspectos técnicos. El informe de los registros necesarios para calcular otras estadísticas, como eventos, errores y trabajos debe realizarse manualmente mediante la API de Engagement (vea [Cómo usar la API de etiquetado avanzado de Mobile Engagement en la aplicación de la Tienda Windows](mobile-engagement-windows-store-use-engagement-api.md)) debido a que estas estadísticas dependen de la aplicación..

## Versiones compatibles

Este SDK de Windows solo admite lo siguiente:

-   Aplicación de la Tienda Windows 8 C\#
-   Aplicación de la tienda Windows 8.1 C\#

## Integrar el SDK de Engagement en el proyecto de Windows

Para integrar el SDK de Engagement, use el administrador de paquetes de NuGet para la plataforma de desarrollo de Microsoft.

El paquete de NuGet para Mobile Engagement todavía no está disponible en línea, por lo que tendrá que descargar nuestro archivo SDK y descomprimirlo. Luego, en Visual Studio, haga clic en el proyecto, vaya a "Administrar paquetes de NuGet", Configuración y agregue la carpeta lib como nuevo origen de paquete.

> [AZURE.NOTE] NuGet es el administrador de paquetes principal para los proyectos .NET. Permite descargar, agregar y copiar todo lo que necesite para usar el SDK de Engagement. En la aplicación Engagement Windows 8 C\# Metro, se usan bibliotecas de otros fabricantes (Microsoft.Bcl.Build v1.0.10 y paquete de portabilidad Microsoft.Bcl v1.1.3) para la portabilidad entre plataformas y NuGet preguntará automáticamente que los instale también. Puede encontrar más información en el [sitio web de NuGet].

## Agregar las capacidades

El SDK de Engagement necesita algunas capacidades del SDK de Windows para que funcione correctamente.

Abra el archivo`Package.appxmanifest` y asegúrese de que en el panel se han declarado las siguientes capacidades:

-   `Internet (Client)`

Vaya al panel`Declarations`del archivo Package.appxmanifest.

En `Declaraciones disponibles`,seleccione `Asociaciones de tipo de archivo` y agréguelo. En la pantalla de la derecha, en el campo `Nombre`, escriba `engagement_log_file` y en el campo Tipo de archivo escriba `.set`.

Luego, en `Declaraciones disponibles`, seleccione `Actualizador de archivos en caché` y agréguelo.

## Inicializar el SDK de Engagement

### Configuración de Engagement

La configuración de Engagement se centraliza en el archivo `Resources\EngagementConfiguration.xml` del proyecto.

Edite este archivo para especificar lo siguiente:

-   La cadena de conexión de la aplicación entre las etiquetas `<connectionString>`y `<\connectionString>`.

Si desea especificarla en tiempo de ejecución, puede llamar al método siguiente antes de la inicialización del agente de Engagement:

			/* Engagement configuration. */
			EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
			engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
			
			/* Initialize Engagement angent with above configuration. */
			EngagementAgent.Instance.Init(args, engagementConfiguration);

La cadena de conexión de la aplicación se muestra en el portal de Azure.

### Inicialización de Engagement

Al crear un nuevo proyecto, se genera un archivo `App.xaml.cs`. Esta clase hereda de la `Aplicación` y contiene muchos métodos importantes. También se usará para inicializar el SDK de Engagement.

Modificar `App.xaml.cs`:

-   Agregue las instrucciones `using`:

			using Microsoft.Azure.Engagement;

-   Insert `EngagementAgent.Instance.Init` in the `OnLaunched` method:

			protected override void OnLaunched(LaunchActivatedEventArgs args)
			{
			  EngagementAgent.Instance.Init(args);
			
			  // or
			
			  EngagementAgent.Instance.Init(args, engagementConfiguration);
			}

-   Cuando la aplicación se inicia mediante un esquema personalizado, otra aplicación o la línea de comandos, se llama al método `OnActivated`. También debe iniciar el agente de Engagement cuando se activa la aplicación. Para ello, invalide el método `OnActivated`:

			protected override void OnActivated(IActivatedEventArgs args)
			{
			  EngagementAgent.Instance.Init(args);
			
			  // or
			
			  EngagementAgent.Instance.Init(args, engagementConfiguration);
			}

> [AZURE.IMPORTANT] Se desaconseja encarecidamente agregar la inicialización de Engagement en otro lugar de la aplicación.

## Informes básicos

### Método recomendado: sobrecarga de las clases `Page`

Para activar el informe de todos los registros que Engagement necesita para calcular las estadísticas de usuarios, sesiones, actividades, bloqueos y aspectos técnicos, puede hacer que todas las subclases `Page`  hereden de las clases `EngagementPage` .

Este es un ejemplo de cómo hacer esto para una página de la aplicación. Puede hacer lo mismo para todas las páginas de la aplicación.

#### Archivo de código fuente C\#

Modifique el archivo de página `.xaml.cs`:

-   Agregue las instrucciones `using`:

			using Microsoft.Azure.Engagement;

-   Reemplace `Page` por `EngagementPage`:

**Sin Engagement:**

			namespace Example
			{
			  public sealed partial class ExamplePage : Page
			  {
			    [...]
			  }
			}

**Con Engagement:**

			using Microsoft.Azure.Engagement;
			
			namespace Example
			{
			  public sealed partial class ExamplePage : EngagementPage 
			  {
			    [...]
			  }
			}

> [AZURE.IMPORTANT] Si la página invalida el método `OnNavigatedTo`, no olvide llamar a `base.OnNavigatedTo(e)`. De lo contrario, no se informará la actividad (`EngagementPage` llama a`StartActivity` en su método`OnNavigatedTo` ).

#### Archivo XAML

Modifique el archivo de página `.xaml`:

-   Agregue a las declaraciones de espacios de nombres:

			xmlns:engagement="using:Microsoft.Azure.Engagement"

-   Reemplace `Page` por `engagement:EngagementPage`:

**Sin Engagement:**

			<Page>
			    <!-- layout -->
			    ...
			</Page>

**Con Engagement:**

			<engagement:EngagementPage 
			    xmlns:engagement="using:Microsoft.Azure.Engagement">
			    <!-- layout -->
			    ...
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

Encontrará más detalles sobre la información adicional aquí \<windows-sdk-extras-parameters\>.

Estos métodos se invocan desde el método `OnNavigatedTo` de la página.

### Método alternativo: llamar a `StartActivity()` manualmente

Si no puede o no quiere sobrecargar las clases `Page`, en su lugar, puede iniciar las actividades mediante una llamada directa a los métodos `EngagementAgent`.

Recomendamos que llame a `StartActivity` dentro del método `OnNavigatedTo` de su página.

			protected override void OnNavigatedTo(NavigationEventArgs e)
			{
			  base.OnNavigatedTo(e);
			  EngagementAgent.Instance.StartActivity("MyPage");
			}

> [AZURE.IMPORTANT] El SDK de Windows llama automáticamente al método `EndActivity` cuando se cierra la aplicación. Por lo tanto, es  *HIGHLY*recomendable llamar al método `StartActivity` cada vez que cambie la actividad del usuario y  *NEVER* llamar al método `EndActivity`, ya que esto obliga la finalización de la sesión actual.

## Informes avanzados

Opcionalmente, es aconsejable informar eventos, errores y trabajos de aplicación específicos. Para ello, use los otros métodos que se encuentran en la clase `EngagementAgent`. La API de Engagement permite usar todas las capacidades avanzadas de Engagement.

Para obtener más información, consulte Cómo usar la API de Engagement en Windows.

## Configuración avanzada

### Deshabilitar los informes automáticos de bloqueo

Puede deshabilitar la característica de informes automáticos de bloqueo de Engagement. A continuación, cuando se produce una excepción no controlada, Engagement no hará nada.

> [AZURE.WARNING] Si tiene previsto deshabilitar esta característica, tenga en cuenta que cuando se produzca un error no controlado en la aplicación, Engagement no enviará la información del bloqueo **NI** tampoco cerrará la sesión ni los trabajos.

Para deshabilitar los informes automáticos de bloqueo, personalice la configuración según la manera en que la declaró:

#### Desde el archivo `EngagementConfiguration.xml`

Establezca el informe de bloqueo en  `false` entre las etiquetas `<reportCrash>` y `</reportCrash>`.

#### Desde el objeto `EngagementConfiguration` en tiempo de ejecución

Establezca el informe de bloqueo mediante el objeto EngagementConfiguration.

			/* Engagement configuration. */
			EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
			engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
			
			/* Disable Engagement crash reporting. */
			engagementConfiguration.Agent.ReportCrash = false;

### Modo de ráfaga [beta]

De forma predeterminada, el servicio de Engagement informa los registros en tiempo real. Si la aplicación informa los registros con mucha frecuencia, es mejor almacenar los registros en el búfer e informarlos todos a la vez de forma periódica (esto se denomina "modo de ráfaga").

Para ello, llame al método siguiente:

			EngagementAgent.Instance.SetBurstThreshold(int everyMs);

El argumento es un valor en **milisegundos**. En cualquier momento, si desea volver a activar el registro en tiempo real, simplemente llame al método sin ningún parámetro o con el valor 0.

El modo de ráfaga aumenta ligeramente la duración de la batería, pero afecta al monitor de Engagement: la duración de todas las sesiones y trabajos se redondeará al umbral de ráfaga (por lo tanto, es posible que las sesiones y los trabajos más cortos que el umbral de ráfaga no sean visibles). Se recomienda usar un umbral de ráfaga inferior a 30.000 (30 segundos).

> [AZURE.WARNING] El umbral de ráfaga no se puede configurar en un período inferior a un segundo. Si intenta hacerlo, el SDK mostrará un seguimiento con el error y se restablecerá automáticamente en el valor predeterminado; es decir, cero segundos. Esto hará que el SDK informe los registros en tiempo real.

[aquí]:http://www.nuget.org/packages/Capptain.WindowsCS
[Sitio web de NuGet]:http://docs.nuget.org/docs/start-here/overview

<!--HONumber=47-->
