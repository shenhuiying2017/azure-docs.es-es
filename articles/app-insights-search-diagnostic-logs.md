<properties title="Search diagnostic logs with Application Insights" pageTitle="Buscar registros de diagnóstico" description="Search logs generated with Trace, NLog, or Log4Net." metaKeywords="analytics web test" authors="awills"  manager="kamrani" />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="2014-09-24" ms.author="awills" />
 
# Búsqueda de diagnóstico en Application Insights

Uno de los métodos de depuración más tradicionales consiste en insertar líneas de código que emiten un registro de seguimiento. [Application Insights][start] puede capturar los registros de servidor web y le ayuda a buscarlos y filtrarlos. Si ya usa log4Net, NLog o System.Diagnostics.Trace, puede capturar los registros con nuestro adaptador. O bien, puede usar los métodos TrackTrace y TrackException integrados en el SDK de Application Insights.

Los resultados de búsqueda también pueden incluir la vista de página normal y solicitar los eventos que se utilizan para generar los informes de [uso][usage] y [rendimiento][perf], junto con las [llamadas a TrackEvent personalizadas][track] que ha escrito.


2. [¿Instalar un adaptador para el marco de registro?](#capture)
+ [Insertar llamadas de registro de diagnóstico](#pepper)
+ [Excepciones](#exceptions)
+ [Visualización de datos de registro](#view)
+ [Búsqueda de datos de registro](#search)
+ [Solución de problemas](#questions)
+ [Pasos siguientes](#next)



## <a name="capture"></a> ¿Instalar un adaptador para el marco de registro?

Si aún no ha [instalado Application Insights en su proyecto][start], hágalo ahora.

Si usará las llamadas integradas del SDK Track*() de Application Insights, no necesita un adaptador, [Vaya a la sección siguiente](#pepper).

Para buscar los registros generados con log4Net, NLog o System.Diagnostics.Trace, instale el adaptador adecuado:

1. Si planea usar log4Net o NLog, instálelo en su proyecto. 
2. En el Explorador de soluciones, haga clic con el botón derecho en el proyecto y seleccione **Administrar paquetes de NuGet**.
3. Seleccione En línea > Todo, seleccione **Incluir versión preliminar** y busque "Microsoft.ApplicationInsights"

    ![Get the prerelease version of the appropriate adapter](./media/appinsights/appinsights-36nuget.png)

4. Seleccione el paquete adecuado entre los siguientes:
  + Microsoft.ApplicationInsights.TraceListener (para capturar las llamadas de System.Diagnostics.Trace)
  + Microsoft.ApplicationInsights.NLogTarget
  + Microsoft.ApplicationInsights.Log4NetAppender

El paquete de NuGet instala los ensamblados necesarios y también modifica el archivo web.config o app.config.

## <a name="pepper"></a>3. Inserción de llamadas de registro de diagnóstico

Inserte las llamadas de registro de eventos utilizando el marco de registro elegido. 

Por ejemplo, si utiliza el SDK de Application Insights, puede insertar:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");

O bien, si utiliza System.Diagnostics.Trace:

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

Si prefiere log4net o NLog:

    logger.Warn("Slow response - database01");

Ejecute la aplicación en modo de depuración o impleméntela en su servidor web.

### <a name="exceptions"></a>Excepciones

Para enviar las excepciones al registro:

JavaScript en el cliente

    try 
    { ...
    }
    catch (ex)
    {
      appInsights.TrackException(ex, "handler loc",
        {Game: currentGame.Name, 
         State: currentGame.State.ToString()});
    }

C# en el servidor

    var telemetry = new TelemetryClient();
    ...
    try 
    { ...
    }
    catch (Exception ex)
    {
       // Set up some properties:
       var properties = new Dictionary <string, string> 
         {{"Game", currentGame.Name}};

       var measurements = new Dictionary <string, double>
         {{"Users", currentGame.Users.Count}};

       // Send the exception telemetry:
       telemetry.TrackException(ex, properties, measurements);
    }

VB en el servidor

    Dim telemetry = New TelemetryClient
    ...
    Try
      ...
    Catch ex as Exception
      ' Set up some properties:
      Dim properties = New Dictionary (Of String, String)
      properties.Add("Game", currentGame.Name)

      Dim measurements = New Dictionary (Of String, Double)
      measurements.Add("Users", currentGame.Users.Count)
  
      ' Send the exception telemetry:
      telemetry.TrackException(ex, properties, measurements)
    End Try

Los parámetros de las propiedades y las medidas son opcionales, pero son útiles para filtrar y agregar información adicional. Por ejemplo, si tiene una aplicación que se puede ejecutar varios juegos, podría buscar todos los informes de excepción relacionados con un juego en particular. Puede agregar tantos elementos como desee para cada diccionario.

## <a name="view"></a>4. Visualización de datos de registro


1. En Application Insights, abra la búsqueda de diagnóstico.

    ![Open diagnostic search](./media/appinsights/appinsights-30openDiagnostics.png)
   
2. Establezca el filtro para los tipos de eventos que desea ver.

    ![Open diagnostic search](./media/appinsights/appinsights-331filterTrace.png)


Los tipos de evento son:

* **Seguimiento**: buscar registros de diagnóstico que se han capturado desde el servidor web. Esto incluye log4Net, NLog, System.Diagnostic.Trace y llamadas a ApplicationInsights TrackTrace.
* **Solicitud**: buscar solicitudes HTTP recibidas por el componente de servidor de la aplicación web, incluidas las solicitudes de página, las solicitudes de datos, las imágenes, etc. Los eventos que verá se corresponden con la telemetría enviada por el SDK del servidor de Application Insights, que se utiliza para crear el informe de recuento de solicitudes.
* **Vista de página**: buscar eventos de vista de página. Estos eventos se envían por el cliente web y se utilizan para crear informes de la vista de página. (Si no ve nada aquí, configure la [supervisión de cliente de web][usage]).
* **Evento personalizado**: si inserta llamadas a TrackEvent() y TrackMetric() para [supervisar el uso][track], puede buscarlas aquí.

Seleccione cualquier evento de registro para ver sus detalles. 

![Open diagnostic search](./media/appinsights/appinsights-32detail.png)

Puede usar cadenas sin formato (sin caracteres comodines) para filtrar los datos de campos dentro de un elemento.

Los campos disponibles dependen del marco de registro y los parámetros utilizados en la llamada.


## <a name="search"></a>5. Búsqueda de los datos

Establezca un intervalo de tiempo y busque los términos. Las búsquedas en un intervalo más breve son más rápidas. 

![Open diagnostic search](./media/appinsights/appinsights-311search.png)

Tenga en cuenta que está buscando términos, no subcadenas. Los términos son cadenas alfanuméricas incluyendo algunos signos de puntuación, como '.' y '_'. Por ejemplo:

<table>
  <tr><th>término</th><th>NO coincide con</th><th>pero estos coinciden con</th></tr>
  <tr><td>HomeController.About</td><td>about<br/>home</td><td>h*about<br/>home*</td></tr>
  <tr><td>IsLocal</td><td>local<br/>is<br/>*local</td><td>isl*<br/>islocal<br/>i*l</td></tr>
  <tr><td>Nuevo retraso</td><td>w d</td><td>new<br/>delay<br/>n* AND d*</td></tr>
</table>

A continuación se muestran las expresiones de búsqueda que puede utilizar:

<table>
                    <tr>
                      <th>
                        <p>Consulta de ejemplo</p>
                      </th>
                      <th>
                        <p>Efecto</p>
                      </th>
                    </tr>
                    <tr>
                      <td>
                        <p>
                          <span class="code">lento</span>
                        </p>
                      </td>
                      <td>
                        <p>Busca todos los eventos del intervalo de datos cuyos campos incluyen el término "lento"</p>
                      </td>
                    </tr>
                    <tr>
                      <td>
                        <p>
                          <span class="code">base de datos??</span>
                        </p>
                      </td>
                      <td>
                        <p>Coincide con base de datos01, base de datosAB, ...</p>
                        <p>? no se permite al comienzo de un término de búsqueda.</p>
                      </td>
                    </tr>
                     <tr>
                      <td>
                        <p>
                          <span class="code">base de datos*</span>
                        </p>
                      </td>
                      <td>
                        <p>Coincide con base de datos, base de datos01, base de datosNNNN</p>
                        <p>* no se permite al comienzo de un término de búsqueda.</p>
                      </td>
                    </tr>
                   <tr>
                      <td>
                        <p>
                          <span class="code">manzana AND plátano</span>
                        </p>
                      </td>
                      <td>
                        <p>Buscar eventos que contienen ambos términos. Utilizar "AND" en mayúsculas, no "and".</p>
                      </td>
                    </tr>
                    <tr>
                      <td>
                        <p>
                          <span class="code">manzana OR plátano</span>
                        </p>
                        <p>
                          <span class="code">manzana plátano</span>
                        </p>
                      </td>
                      <td>
                        <p>Buscar eventos que contienen cualquiera de los dos términos. Usar "OR" no "or".</p>
                        <p>Forma breve.</p>
                      </td>
                    </tr>
                    <tr>
                      <td>
                        <p>
                          <span class="code">manzana NOT plátano</span>
                        </p>
                        <p>
                          <span class="code">manzana -plátano</span>
                        </p>
                      </td>
                      <td>
                        <p>Encontrar eventos que contienen un término pero no el otro.</p>
                        <p>Forma breve.</p>
                      </td>
                    </tr>
                    <tr>
                      <td>
                        <p>manz* AND plátano NOT (uvas OR pera)</p>
                        <p>
                          <span class="code">manz* AND plátano -(uvas pera)</span>
                        </p>
                      </td>
                      <td>
                        <p>Operadores lógicos y corchetes.</p>
                        <p>Forma más breve.</p>
                      </td>
                    </tr>
       <!-- -- fielded search feature not ready yet --
                    <tr>
                      <td>
                        <p>
                          <span class="code">mensaje:lento</span>
                        </p>
                        <p>
                          <span class="code">ipaddress:(10.0.0.* OR 192.168.0.*)</span>
                        </p>
                        <p>
                          <span class="code">properties.logEventInfo.level:Error</span>
                        </p>
                      </td>
                      <td>
                        <p>Coincidir con el campo especificado. De manera predeterminada, se buscan todos los campos. Para ver qué campos están disponibles, seleccione un evento para consultarlo con detalle.</p>
                      </td>
                    </tr>
 -->
</table>


## <a name="questions"></a>Preguntas y respuestas

### <a name="emptykey"></a>Aparece el mensaje de error "La clave de instrumentación no puede estar vacía"

Parece que ha instalado el paquete de NuGet del adaptador de registro sin tener que instalar Application Insights.

En el Explorador de soluciones, haga clic con el botón derecho en `ApplicationInsights.config` y elija **Actualizar Application Insights**. Aparecerá un cuadro de diálogo que le invita a iniciar sesión en Azure; cree un recurso de Application Insights o vuelva a utilizar uno existente. Esto debería solucionarlo.

### <a name="limits"></a>¿Qué cantidad de datos se conserva?

Hasta 500 eventos por segundo de cada aplicación. Los eventos se conservan durante siete días.

### <a name="cani"></a>¿Puedo hacer lo siguiente?

- Establecer alertas en los eventos y excepciones
- Exportar registros para realizar un análisis pormenorizado
- Buscar propiedades específicas

Todavía no, pero todas estas características se encuentran entre las tareas pendientes.

## <a name="add"></a>Pasos siguientes

* [Configuración de pruebas de disponibilidad y de capacidad de respuesta][availability]
* [Solución de problemas][qna]





[AZURE.INCLUDE [app-insights-learn-more](../includes/app-insights-learn-more.md)]




