<properties linkid="develop-net-common-tasks-diagnostics-logging-and-instrumentation" urlDisplayName="Enable diagnostic logging" pageTitle="Enable diagnostic logging - Azure Websites" metaKeywords="Azure diagnostics web sites, Azure Management Portal diagnostics, Azure diagnostics, web site diagnostics, web site debug" description="Learn how to enable diagnostic logging and add instrumentation to your application, as well as how to access the information logged by Azure." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Enable diagnostic logging for Azure Websites" authors="larryfr" solutions="" manager="" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />

# Habilitación del registro de diagnóstico para Sitios web Azure

Azure integra diagnósticos para ayudar a depurar una aplicación hospedada en Sitios web Azure. En este artículo se ofrece información acerca de cómo habilitar el registro de diagnósticos, agregar instrumentación a la aplicación y obtener acceso a la información registrada por Azure.

> [WACOM.NOTE] En este artículo se describe el uso del Portal de administración de Azure, Azure PowerShell y la interfaz de la línea de comandos entre plataformas de Azure para trabajar con registros de diagnóstico. Para obtener información acerca de cómo trabajar con registros de diagnóstico mediante Visual Studio, consulte [Solución de problemas de Sitios web Azure en Visual Studio][Solución de problemas de Sitios web Azure en Visual Studio].

## Tabla de contenido

-   [¿Qué son los diagnósticos de sitios web?][¿Qué son los diagnósticos de sitios web?]
-   [Direccionamiento del de diagnósticos][Direccionamiento del de diagnósticos]
-   [Direccionamiento del registros][Direccionamiento del registros]
-   [Direccionamiento del registros][1]
-   [Direccionamiento del de los registros de diagnóstico][Direccionamiento del de los registros de diagnóstico]
-   [Pasos siguientes][Pasos siguientes]

<a name="whatisdiag"></a>

## ¿Qué es el diagnóstico de sitios web?

</p>
Sitios web Azure ofrece la funcionalidad de diagnóstico para registrar información del servidor web y de la aplicación web. De forma lógica, estos diagnósticos se dividen en **diagnóstico del sitio** y **diagnóstico de la aplicación**.

### Diagnósticos del sitio

Los diagnósticos del sitio le permiten habilitar o deshabilitar lo siguiente:

-   **Detailed Error Logging**: Registra información detallada de errores para códigos de estado HTTP que indican un problema (código de estado 400 o superior). Puede contener información que puede ayudar a determinar por qué el servidor ha devuelto el código de error.
-   **Failed Request Tracing**: Registra información detallada acerca de las solicitudes con error, incluido un seguimiento de los componentes de IIS usados para procesar la solicitud y el tiempo dedicado a cada componente. Esto puede resultar útil si trata de aumentar el rendimiento del sitio o de aislar lo que causa la devolución de un error HTTP específico.
-   **Web Server Logging**: Registra todas las transacciones HTTP de un sitio web con el [formato de archivo de registro extendido W3C (en inglés)][formato de archivo de registro extendido W3C (en inglés)]. Este informe resulta útil para determinar las métricas totales del sitio, como el número de solicitudes tramitadas o cuántas solicitudes proceden de una dirección IP específica.

### Diagnósticos de aplicaciones

El diagnóstico de aplicaciones le permite capturar información generada por una aplicación web. Las aplicaciones de ASP.NET pueden usar la clase [System.Diagnostics.Trace][System.Diagnostics.Trace] para registrar información en el registro de diagnóstico de la aplicación. Por ejemplo:

    System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");

El diagnóstico de la aplicación le permite solucionar los problemas de la aplicación en ejecución mediante la emisión de información cuando se usan determinadas piezas de código. Esto resulta especialmente útil al tratar de terminar por qué el código usa una ruta de acceso específica, normalmente si la ruta de acceso genera un error u otro comportamiento no deseado.

Para obtener información acerca de cómo trabajar con diagnósticos de aplicaciones mediante Visual Studio, consulte [Solución de problemas de Sitios web Azure en Visual Studio][2].

> [WACOM.NOTE] Al contrario de lo que ocurre al cambiar el archivo web.config, habilitar el diagnóstico de la aplicación o cambiar los niveles del registro de diagnóstico no recicla el dominio de la aplicación en el que esta se ejecuta.

Sitios web Azure también registra información de implementación al publicar una aplicación en un sitio web. Esta acción se lleva a cabo automáticamente, por lo que no es necesario realizar ninguna configuración para el registro de implementaciones. El registro de implementaciones le permite determinar por qué se ha producido un error con la implementación. Por ejemplo, si usa un script de implementación personalizado, puede usar el registro de implementaciones para determinar por qué se ha producido un error con el script.

<a name="enablediag"></a>

## Reproducción de de diagnósticos

</p>
Los diagnósticos se pueden habilitar en la página **Configure** del sitio web de Azure en el [Portal de administración de Azure][Portal de administración de Azure]. En la página **Configure**, use las secciones **Diagnóstico de aplicaciones** y **Site Diagnostics** para habilitar el registro.

Si habilita **Diagnóstico de aplicaciones**, también debe seleccionar el nivel de registro en **logging level** y si desea habilitar el registro para **file system**, **table storage** o **blob storage**. Si bien las tres ubicaciones de almacenamiento ofrecen la misma información básica de los eventos registrados, **table storage** y **blob storage** registran información adicional como el identificador de instancia, el identificador de subproceso y una marca de tiempo más pormenorizada (formato de marca de graduación) que el registro en **file system**.

Si habilita **site diagnostics**, debe seleccionar **storage** o **file system** para **web server logging**. Si selecciona **storage**, tiene la opción de seleccionar una cuenta de almacenamiento y, a continuación, un contenedor de blob en el que se escribirán los registros. Todos los demás registros de **site diagnostics** se escriben solo en el sistema de archivos.

> [WACOM.NOTE] Solo se puede obtener acceso a la información almacenada en **table storage** o **blob storage** mediante un cliente de almacenamiento o una aplicación que puedan funcionar directamente con estos sistemas de almacenamiento. Por ejemplo, Visual Studio 2013 contiene un Explorador de almacenamiento que se puede usar para explorar el almacenamiento de tabla o de blobs y HDInsight puede obtener acceso a los datos almacenados en el almacenamiento de blobs. También puede escribir una aplicación que obtiene acceso al servicio Almacenamiento de Azure mediante alguno de los [SDK de Azure][SDK de Azure].

A continuación se indica la configuración disponible al habilitar **Diagnóstico de aplicaciones**:

-   **Logging level**: Le permite filtrar la información capturada como **informational**, **warning** o **error**. Si configura esta opción como **verbose**, registrará toda la información generada por la aplicación. **Logging level** puede definirse de manera diferente para el registro en **file system**, **table storage** y **blob storage**.
-   **File system**: Almacena la información de diagnóstico de la aplicación en el sistema de archivos del sitio web. Es posible obtener acceso a estos archivos por FTP, o bien se pueden descargar como un archivo ZIP con la utilización de Azure PowerShell o de las herramientas de línea de comandos de Azure.
-   **Table storage**: Almacena la información de diagnóstico de aplicaciones en el nombre de tabla y en la cuenta de almacenamiento de Azure.
-   **Blob storage**: Almacena la información de diagnóstico de aplicaciones en el contenedor de blob y en la cuenta de almacenamiento de Azure especificada.
-   **Retention period**: De forma predeterminada, los registros no se eliminan automáticamente de **almacenamiento de blobs**. Seleccione **set retention** y escriba el número de días durante los cuales desea que se conserven los registros si desea que estos se eliminen automáticamente.

> [WACOM.NOTE] Al mismo tiempo se puede habilitar cualquier combinación de sistema de archivos, almacenamiento de tabla o almacenamiento de blobs, y estas opciones tiene configuraciones individuales del nivel de registro. Por ejemplo, puede registrar errores y advertencias en el almacenamiento de blobs como una solución de registro a largo plazo, mientras habilita el registro en el sistema de archivos con un nivel detallado.

> [WACOM.NOTE] El diagnóstico también se puede habilitar desde Azure PowerShell mediante el cmdlet **Set-AzureWebsite**. Si no tiene instalado Azure PowerShell o si no lo ha configurado para utilizar su suscripción a Azure, consulte [Uso de Azure PowerShell][Uso de Azure PowerShell].

<a name="download"></a>

## Reproducción de registros

</p>
Se puede obtener acceso a la información de diagnóstico almacenada en el sistema de archivos del sitio web directamente mediante FTP. No obstante, también se puede descargar como un archivo ZIP con Azure PowerShell o mediante las herramientas de línea de comandos de Azure.

La estructura de directorios en que se almacenan los registros es la siguiente:

-   **Registros de aplicaciones**: /LogFiles/Application/. Esta carpeta contiene uno o varios archivos de texto con información generada por el registro de aplicaciones.

-   **Seguimientos de solicitudes con error**: /LogFiles/W3SVC#\#\#\#\#\#\#\#\#/. Esta carpeta contiene un archivo XSL y uno o varios archivos XML. Asegúrese de descargar el archivo XSL en el mismo directorio de los archivos XML, porque el archivo XSL proporciona funcionalidad para dar formato y filtrar los contenidos de los archivos XML cuando se visualizan en Internet Explorer.

-   **Registros detallados de errores**: /LogFiles/DetailedErrors/. Esta carpeta contiene uno o varios archivos .htm con información amplia de todos los errores HTTP que se han producido.

-   **Registros de servidor web**: /LogFiles/http/RawLogs. Esta carpeta contiene uno o varios archivos de texto a los que se le aplica el [formato de archivo de registro extendido W3C (en inglés)][formato de archivo de registro extendido W3C (en inglés)].

-   **Registros de implementaciones**: /LogFiles/Git. Esta carpeta contiene registros generados por los procesos de implementación internos usados por los sitios web de Azure, además de los registros de las implementaciones Git.

### FTP

Para obtener acceso a la información de diagnóstico mediante FTP, visite el **Panel** del sitio web en el Portal de administración de Azure. En la sección **Quick Glance**, haga clic en el vínculo **FTP Diagnostic Logs** para obtener acceso a los archivos de registro mediante FTP. La entrada **Deployment / FTP User** enumera el nombre de usuario que debe usarse para obtener acceso al sitio FTP.

> [WACOM.NOTE] Si no se define la entrada **Deployment / FTP User** o si ha olvidado la contraseña de este usuario, puede crear un nuevo usuario y una contraseña mediante el vínculo **Reset deployment credentials** en la sección **Quick Glance** del **Panel**.

### Descarga con Azure PowerShell

Para descargar los archivos de registro, inicie una nueva instancia de Azure PowerShell y use el siguiente comando:

    Save-AzureWebSiteLog -Name websitename

Este comando guardará los registros del sitio web especificados mediante el parámetro **-Name** en un archivo con nombre **logs.zip** en el directorio actual.

> [WACOM.NOTE] Si no tiene instalado Azure PowerShell o lo ha configurado para usar la suscripción de Azure, consulte [Uso de Azure PowerShell][Uso de Azure PowerShell].

### Descarga con las herramientas de línea de comandos de Azure

Para descargar los archivos de registro mediante las herramientas de línea de comandos de Azure, abra una sesión nueva del símbolo del sistema, PowerShell, Bash o Terminal y escriba el siguiente comando:

    azure site log download websitename

Este comando guardará los registros en el sitio web denominado "nombre del sitio web" en un archivo con nombre **diagnostics.zip** en el directorio actual.

> [WACOM.NOTE] Si no tiene instaladas las herramientas de línea de comandos de Azure o las ha configurado para que usen la suscripción de Azure, consulte [Uso de las herramientas de línea de comandos de Azure][Uso de las herramientas de línea de comandos de Azure].

<a name="streamlogs"></a>

## Reproducción de registros

</p>
Al implementar una aplicación, suele resultar útil ver la información de registro casi en tiempo real. Para ello, puede transmitir la información de registro al entorno de desarrollo con Azure PowerShell o las herramientas de línea de comandos de Azure.

> [WACOM.NOTE] Algunos tipos de búfer de registros escriben en el archivo de registro, lo que puede ocasionar la transmisión de eventos desordenados. Por ejemplo, una entrada de registro de aplicaciones que se genera cuando un usuario visita una página se puede visualizar en la transmisión antes de la entrada de registro HTTP correspondiente para la solicitud de la página.

> [WACOM.NOTE] La transmisión de registros también transmitirá información escrita en cualquier archivo de texto almacenado en la carpeta **D:\\home\\LogFiles\\**.

### Transmisión con Azure PowerShell

Para transmitir información de registro, inicie una nueva instancia de Azure PowerShell y use el siguiente comando:

    Get-AzureWebSiteLog -Name websitename -Tail

Este comando establecerá conexión con el sitio web especificado mediante el parámetro **-Name** y comenzará a transmitir información a la ventana de PowerShell a medida que se produzcan los eventos de registro en el sitio web. Toda la información escrita en archivos terminados en .txt, .log o .htm almacenados en el directorio /LogFiles (d:/home/logfiles) se transmitirá a la consola local.

Para filtrar eventos específicos, como errores, use el parámetro **-Message**. Por ejemplo:

    Get-AzureWebSiteLog -Name websitename -Tail -Message Error

Para filtrar tipos de registros específicos, como HTTP, use el parámetro **-Path**. Por ejemplo:

    Get-AzureWebSiteLog -Name websitename -Tail -Path http

Para ver una lista de rutas de acceso disponibles, use el parámetro -ListPath.

> [WACOM.NOTE] Si no tiene instalado Azure PowerShell o lo ha configurado para usar la suscripción de Azure, consulte [Uso de Azure PowerShell][Uso de Azure PowerShell].

### Transmisión con las herramientas de línea de comandos de Azure

Para transmitir información de registro, abra una nueva sesión del símbolo del sistema, PowerShell, Bash o Terminal y escriba el siguiente comando:

    azure site log tail websitename

Este comando establecerá conexión con el sitio web denominado "nombre del sitio web" y comenzará a transmitir información a la ventana a medida que se produzcan los eventos de registro en el sitio web. Toda la información escrita en archivos terminados en .txt, .log o .htm almacenados en el directorio /LogFiles (d:/home/logfiles) se transmitirá a la consola local.

Para filtrar eventos específicos, como errores, use el parámetro **--Filter**. Por ejemplo:

    azure site log tail websitename --filter Error

Para filtrar tipos de registros específicos, como HTTP, use el parámetro **--Path**. Por ejemplo:

    azure site log tail websitename --path http

> [WACOM.NOTE] Si no tiene instaladas las herramientas de línea de comandos de Azure o las ha configurado para que usen la suscripción de Azure, consulte [Uso de las herramientas de línea de comandos de Azure][Uso de las herramientas de línea de comandos de Azure].

<a name="understandlogs"></a>

## Reproducción de de los registros de diagnóstico

</p>
### Registros de diagnóstico de aplicaciones

El diagnóstico de aplicaciones almacena información con un formato específico para aplicaciones .NET, en función de si almacena los registros en el sistema de archivos, en el almacenamiento de tabla o en el almacenamiento de blobs. El conjunto base de datos almacenados es el mismo en los tres tipos de almacenamiento: la fecha y la hora en que se ha producido el evento, el identificador del proceso que ha producido el evento, el tipo de evento (información, advertencia o error) y el mensaje del evento.

**Sistema de archivos**

Cada línea registrada en el sistema de archivos o recibida mediante transmisión presentará el siguiente formato:

    {Date}  PID[{process id}] {event type/level} {message}

Por ejemplo, un evento de error presentaría un formato similar al siguiente:

    2014-01-30T16:36:59  PID[3096] Error       Fatal error on the page!

El registro en el sistema de archivos ofrece la información más básica de los tres métodos disponibles, ya que ofrece solo la hora, el identificador del proceso, el nivel de evento y el mensaje.

**Almacenamiento de tabla**

Al realizar registros en el almacenamiento de tabla, se usan propiedades adicionales para facilitar la búsqueda de los datos almacenados en la tabla, así como información más pormenorizada sobre el evento. Las siguientes propiedades (columnas) se usan para cada entidad (fila) almacenada en la tabla.

<table style="width:100%;border-collapse:collapse">
<thead>
<tr>
<th style="width:45%;border:1px solid black;background-color:#0099dd">
Nombre de propiedad

</th>
<th style="border:1px solid black;vertical-align:top;background-color:#0099dd">
Valor/formato

</th>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">
PartitionKey

</td>
<td style="border:1px solid black;vertical-align:top">
Fecha/hora del evento con el formato aaaaMMddHH

</td>
</tr>
</thead>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">
RowKey

</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">
Un valor de GUID que identifica a esta entidad de forma exclusiva

</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">
Timestamp

</td>
<td style="border:1px solid black;vertical-align:top">
La fecha y la hora en que se ha producido el evento

</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">
EventTickCount

</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">
La fecha y hora en que se ha producido el evento, con formato de marca de graduación (mayor precisión)

</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">
ApplicationName

</td>
<td style="border:1px solid black;vertical-align:top">
El nombre del sitio web

</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">
Level

</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">
Nivel del evento (por ejemplo, error, advertencia o información)

</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">
EventId

</td>
<td style="border:1px solid black;vertical-align:top">
El identificador de este evento
El valor predeterminado es 0 si no se ha especificado ninguno

</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">
InstanceId

</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">
Instancia del sitio web en que se ha producido el evento

</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">
Pid

</td>
<td style="border:1px solid black;vertical-align:top">
Identificador del proceso

</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">
Tid

</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">
El identificador del subproceso que ha generado el evento

</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">
Message

</td>
<td style="border:1px solid black;vertical-align:top">
Mensaje detallado del evento

</td>
</tr>
</table>
**Almacenamiento de blobs**

Al realizar registros en el almacenamiento de blobs, los datos se almacenan con un formato de valores separados por comas (CSV). De manera similar al almacenamiento de tabla, se registran campos adicionales para ofrecer información más pormenorizada acerca del evento. Las siguientes propiedades se usan para cada fila con el formato CSV:

| Nombre de propiedad | Valor/formato                                                                                          |
|---------------------|--------------------------------------------------------------------------------------------------------|
| Date                | La fecha y la hora en que se ha producido el evento                                                    |
| Level               | Nivel del evento (por ejemplo, error, advertencia o información)                                       |
| ApplicationName     | El nombre del sitio web                                                                                |
| InstanceId          | Instancia del sitio web en que se ha producido el evento                                               |
| EventTickCount      | La fecha y hora en que se ha producido el evento, con formato de marca de graduación (mayor precisión) |
| EventId             | El identificador de este evento                                                                        
                       El valor predeterminado es 0 si no se ha especificado ninguno                                           |
| Pid                 | Identificador del proceso                                                                              |
| Tid                 | El identificador del subproceso que ha generado el evento                                              |
| Message             | Mensaje detallado del evento                                                                           |

Los datos almacenados en un blob serían similares a los siguientes:

    date,level,applicationName,instanceId,eventTickCount,eventId,pid,tid,message
    2014-01-30T16:36:52,Error,mywebsite,6ee38a,635266966128818593,0,3096,9,An error occurred

> [WACOM.NOTE] La primera línea del registro contendrá los encabezados de columna tal y como se representan en este ejemplo.

### Seguimiento de solicitudes con error

El seguimiento de solicitudes con error se almacena en archivos XML con nombre **fr\#\#\#\#\#\#.xml**. Para facilitar la visualización de la información registrada, se facilita una hoja de estilo XSL con nombre **freb.xsl** en el mismo directorio que los archivos XML. Al abrir uno de los archivos XML en Internet Explorer, se usará la hoja de estilo XSL para ofrecer una visualización con formato de la información de seguimiento. El formato será similar al siguiente:

![solicitud con error visualizada en el explorador][solicitud con error visualizada en el explorador]

### Registros de error detallados

Los registros de error detallados son documentos HTML que ofrecen información más detallada sobre los errores HTTP que se han producido. Habida cuenta de que se trata sencillamente de documentos HTML, se pueden ver en un explorador web.

### Registros del servidor web

A los registros del servidor web se les aplica el [formato de archivo de registro extendido W3C (en inglés)][formato de archivo de registro extendido W3C (en inglés)]. Esta información se puede leer con un editor de texto o analizarse con utilidades como el [analizador del registro (en inglés)][analizador del registro (en inglés)].

> [WACOM.NOTE] Los registros generados por sitios web de Azure no admiten los campos **s-computername**, **s-ip** o **cs-version**.

<a name="nextsteps"></a>

## Pasos siguientes

</p>
-   [Supervisión de sitios web][Supervisión de sitios web]
-   [Tutorial - Solución de problemas de sitios web][Tutorial - Solución de problemas de sitios web]
-   [Solución de problemas de Sitios web Azure en Visual Studio][Solución de problemas de Sitios web Azure en Visual Studio]
-   [Análisis de registros de sitios web en HDInsight][Análisis de registros de sitios web en HDInsight]

  [Solución de problemas de Sitios web Azure en Visual Studio]: /es-es/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/
  [¿Qué son los diagnósticos de sitios web?]: #whatisdiag
  [Direccionamiento del de diagnósticos]: #enablediag
  [Direccionamiento del registros]: #download
  [1]: #streamlogs
  [Direccionamiento del de los registros de diagnóstico]: #understandlogs
  [Pasos siguientes]: #nextsteps
  [formato de archivo de registro extendido W3C (en inglés)]: http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx
  [System.Diagnostics.Trace]: http://msdn.microsoft.com/es-es/library/36hhw2t6.aspx
  [2]: http://www.windowsazure.com/es-es/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/
  [Portal de administración de Azure]: https://manage.microsoft.com
  [SDK de Azure]: http://www.windowsazure.com/es-es/downloads/#
  [Uso de Azure PowerShell]: http://www.windowsazure.com/es-es/develop/nodejs/how-to-guides/powershell-cmdlets/
  [Uso de las herramientas de línea de comandos de Azure]: http://www.windowsazure.com/es-es/develop/nodejs/how-to-guides/command-line-tools/
  [solicitud con error visualizada en el explorador]: ./media/web-sites-enable-diagnostic-log/tws-failedrequestinbrowser.png
  [analizador del registro (en inglés)]: http://go.microsoft.com/fwlink/?LinkId=246619
  [Supervisión de sitios web]: /es-es/manage/services/web-sites/how-to-monitor-websites/
  [Tutorial - Solución de problemas de sitios web]: /es-es/develop/net/best-practices/troubleshooting-web-sites/
  [Análisis de registros de sitios web en HDInsight]: http://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413
