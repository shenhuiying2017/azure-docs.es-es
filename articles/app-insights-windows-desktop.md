<properties 
	pageTitle="Application Insights para aplicaciones de escritorio de Windows" 
	description="Analice el uso y el rendimiento de la aplicación Windows con Application Insights." 
	services="application-insights" 
    documentationCenter="windows"
	authors="alancameronwills" 
	manager="keboyd"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/04/2015" 
	ms.author="awills"/>

# Application Insights en aplicaciones de escritorio de Windows

*Application Insights se encuentra en su versión de vista previa.*

[AZURE.INCLUDE [app-insights-selector-get-started](../includes/app-insights-selector-get-started.md)]

Application Insights permite supervisar el uso y rendimiento de la aplicación implementada.

*Aunque el SDK de Application Insights puede realizarse para que funcione en una aplicación de escritorio, no es un escenario que se admita actualmente. Pero si desea probarlo de manera experimental, estas son algunas sugerencias para hacerlo.*




## <a name="add"></a> Creación de recursos en Application Insights


1.  En el [portal de Azure][portal], cree un nuevo recurso de Application Insights. Para el tipo de aplicación, elija aplicación ASP.NET o aplicación de la Tienda Windows. 

    ![Haga clic en Nuevo, Application Insights.](./media/app-insights-windows-get-started/01-new.png)

    (Su elección del tipo de aplicación establece el contenido de la hoja de información general así como de las propiedades disponibles en el [explorador de métricas][metrics]).

2.  Realice una copia de la clave de instrumentación.

    ![Haga clic en Propiedades, seleccione la clave y presione ctrl + C.](./media/app-insights-windows-get-started/02-props.png)

## <a name="sdk"></a>Instalación del SDK en la aplicación


1. En Visual Studio, edite los paquetes de NuGet de su proyecto de aplicación de escritorio. ![Haga clic con el botón secundario en el proyecto y seleccione Administrar paquetes de Nuget.](./media/app-insights-windows-get-started/03-nuget.png)

2. Instale el núcleo del SDK de Application Insights.

    ![Seleccione **En línea**, **Incluir versión preliminar** y busque "Application Insights".](./media/app-insights-windows-get-started/04-ai-nuget.png)

    (Como alternativa, puede elegir el SDK de Application Insights para Aplicaciones web. Esto proporciona cierta telemetría de contador de rendimiento integrada).

3. Edite ApplicationInsights.config (que la instalación de NuGet ha agregado). Inserte esto justo antes de la etiqueta de cierre:

    &lt;InstrumentationKey&gt;*la clave que copió*&lt;/InstrumentationKey&gt;

    Asimismo, puede lograr el mismo efecto con este código:
    
    `TelemetryConfiguration.Active.InstrumentationKey = "your key";`

4. Si ha instalado el SDK de Aplicaciones web, también debe convertir en comentarios los módulos de telemetría de web en ApplicationInsights.config.

## <a name="telemetry"></a>Inserción de llamadas de telemetría

Cree una instancia `TelemetryClient` y después [utilícela para enviar telemetría][track].

Utilice `TelemetryClient.Flush` para enviar los mensajes antes de cerrar la aplicación. (Este procedimiento no se recomienda en otros tipos de aplicación).

Por ejemplo, en una aplicación de Windows Forms, podría escribir:

```C#

    public partial class Form1 : Form
    {
        private TelemetryClient tc = new TelemetryClient();
        ...
        private void Form1_Load(object sender, EventArgs e)
        {
            tc.TrackPageView("Form1");
            ...
        }

        protected override void OnClosing(CancelEventArgs e)
        {
            stop = true;
            if (tc != null)
            {
                tc.Flush(); // only for desktop apps
            }
            base.OnClosing(e);
        }

```

Utilice cualquiera de las [API de Application Insights][track] para enviar telemetría. En las aplicaciones de escritorio de Windows, no se envía automáticamente ninguna telemetría. Normalmente se usaría:

* TrackPageView(pageName) acerca de cómo cambiar formularios, páginas o pestañas
* TrackEvent(eventName) para las demás acciones de usuario
* TrackTrace(logEvent) para el [registro de diagnóstico][diagnostic]
* TrackException(exception) en cláusulas catch
* TrackMetric(name, value) en una tarea en segundo plano para enviar informes periódicos de métricas no asociados a eventos específicos.

Para ver los recuentos de usuarios y sesiones, establezca un inicializador de contexto:

    class TelemetryInitializer: IContextInitializer
    {
        public void Initialize(TelemetryContext context)
        {
            context.User.Id = Environment.UserName;
            context.Session.Id = DateTime.Now.ToFileTime().ToString();
            context.Session.IsNewSession = true;
        }
    }

    static class Program
    {
        ...
        static void Main()
        {
            TelemetryConfiguration.Active.ContextInitializers.Add(
                new TelemetryInitializer());
            ...



## <a name="run"></a>Ejecución del proyecto

[Ejecute la aplicación con F5](http://msdn.microsoft.com/library/windows/apps/bg161304.aspx) y úsela para generar telemetría.

En Visual Studio, aparecerá un recuento de los eventos que se han enviado.

![](./media/appinsights/appinsights-09eventcount.png)



## <a name="monitor"></a>Consulta de los datos del monitor

Vuelva a la hoja de la aplicación en el portal de Azure.

Los primeros eventos aparecerán en Búsqueda de diagnóstico.

Si espera más datos, haga clic en Actualizar después de unos segundos.

Si ha utilizado TrackMetric o el parámetro de mediciones de TrackEvent, abra el [explorador de métricas][metrics] y abra la hoja Filtros, donde podrá ver las métricas.



## <a name="usage"></a>Pasos siguientes

[Seguimiento del uso de la aplicación][track]

[Captura y búsqueda de registros de diagnóstico][diagnostic]

[Solución de problemas][qna]




<!--Link references-->

[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[track]: app-insights-custom-events-metrics-api.md


<!--HONumber=54-->