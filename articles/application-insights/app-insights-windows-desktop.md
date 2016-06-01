<properties 
	pageTitle="Supervisión del uso y el rendimiento en las aplicaciones de escritorio de Windows" 
	description="Analice el uso y el rendimiento de la aplicación de escritorio de Windows con HockeyApp y Application Insights." 
	services="application-insights" 
    documentationCenter="windows"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/27/2016" 
	ms.author="awills"/>

# Supervisión del uso y el rendimiento en las aplicaciones de escritorio de Windows

*Application Insights se encuentra en su versión de vista previa.*

[Application Insights de Visual Studio](app-insights-get-started.md) y [HockeyApp](https://hockeyapp.net) permiten supervisar la aplicación implementada en cuanto al uso y el rendimiento:

> [AZURE.IMPORTANT] Se recomienda [HockeyApp](https://hockeyapp.net) para distribuir y supervisar las aplicaciones de escritorio y para dispositivos. Con HockeyApp, se puede administrar la distribución, las pruebas en directo y los comentarios de los usuarios, así como supervisar los informes de uso y bloqueo.

> Aunque puede enviarse telemetría a Application Insights desde una aplicación de escritorio, esto es principalmente útil para fines experimentales y de depuración.


## Para enviar telemetría a Application Insights desde una aplicación Windows

1. En el [portal de Azure](https://portal.azure.com), cree un nuevo recurso de Application Insights. Para el tipo de aplicación, elija la aplicación ASP.NET.
2. Realice una copia de la clave de instrumentación. Busque la clave en la lista desplegable Essentials del recurso que acaba de crear. Cierre la asignación de aplicaciones o desplácese a la izquierda hasta la hoja de información general del recurso.
3. En Visual Studio, edite los paquetes NuGet de su proyecto de aplicación y agregue Microsoft.ApplicationInsights.WindowsServer. (O elija Microsoft.ApplicationInsights si únicamente le interesa la API sola, sin los módulos de recopilación de telemetría estándar).
4. Establezca la clave de instrumentación en el código:

    `TelemetryConfiguration.Active.InstrumentationKey = "` **su clave** `";`

    o en ApplicationInsights.config (si tiene instalado uno de los paquetes de telemetría estándar):
 
    `<InstrumentationKey>`*su clave*`</InstrumentationKey>`

    Si usa ApplicationInsights.config, asegúrese de que sus propiedades en el Explorador de soluciones se establecen en **Acción de compilación = Contenido, Copiar en el directorio de salida = Copiar**.
5. [Use la API](app-insights-api-custom-events-metrics.md) para enviar telemetría.
6. Ejecute la aplicación y vea la telemetría en el recurso que creó en el Portal de Azure.

## <a name="telemetry"></a>Ejemplo de código

```C#

    public partial class Form1 : Form
    {
        private TelemetryClient tc = new TelemetryClient();
        ...
        private void Form1_Load(object sender, EventArgs e)
        {
            // Alternative to setting ikey in config file:
            tc.InstrumentationKey = "key copied from portal";

            // Set session data:
            tc.Context.User.Id = Environment.UserName;
            tc.Context.Session.Id = Guid.NewGuid().ToString();
            tc.Context.Device.OperatingSystem = Environment.OSVersion.ToString();

            // Log a page view:
            tc.TrackPageView("Form1");
            ...
        }

        protected override void OnClosing(CancelEventArgs e)
        {
            stop = true;
            if (tc != null)
            {
                tc.Flush(); // only for desktop apps

                // Allow time for flushing:
                System.Threading.Thread.Sleep(1000);
            }
            base.OnClosing(e);
        }

```


 

<!---HONumber=AcomDC_0518_2016-->