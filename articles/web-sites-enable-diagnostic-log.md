<properties 
	pageTitle="Habilitación del registro de diagnóstico - Sitios web Azure" 
	description="Obtenga información acerca de cómo habilitar el registro de diagnóstico y agregar la instrumentación a su aplicación, así como la manera de acceder a la información registrada por Azure." 
	services="web-sites" 
	documentationCenter=".net" 
	authors="blackmist" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="larryfr"/>





#Habilitación del registro de diagnóstico para Sitios web Azure

Azure integra diagnósticos para ayudar a depurar una aplicación hospedada en Sitios web Azure. En este artículo se ofrece información acerca de cómo habilitar el registro de diagnósticos, agregar instrumentación a la aplicación y obtener acceso a la información registrada por Azure.

> [AZURE.NOTE]  En este artículo se describe el uso del Portal de administración de Azure, Azure PowerShell y la interfaz de la línea de comandos multiplataforma de Azure para trabajar con registros de diagnóstico. Para obtener información acerca de cómo trabajar con registros de diagnóstico mediante Visual Studio, consulte [Solución de problemas de Sitios web Azure en Visual Studio](/es-es/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/)..

##Tabla de contenido##

- [¿Qué son los diagnósticos de sitios web?](#whatisdiag)
- [Uso de de diagnósticos](#enablediag)
- [Uso de registros](#download)
- [Uso de registros](#streamlogs)
- [Uso de de los registros de diagnóstico](#understandlogs)
- [Pasos siguientes](#nextsteps)

<a name="whatisdiag"></a><h2>¿Qué es el diagnóstico de sitios web?</h2>

Sitios web Azure ofrece la funcionalidad de diagnóstico para registrar información del servidor web y de la aplicación web. De forma lógica, estos diagnósticos se dividen en **diagnóstico del sitio** y **diagnóstico de la aplicación**.

###Diagnósticos del sitio

Los diagnósticos del sitio le permiten habilitar o deshabilitar lo siguiente:

- **Detailed Error Logging**: Registra información detallada de errores para códigos de estado HTTP que indican un problema (código de estado 400 o superior). Puede contener información que puede ayudar a determinar por qué el servidor ha devuelto el código de error.
- **Failed Request Tracing**: Registra información detallada acerca de las solicitudes con error, incluido un seguimiento de los componentes de IIS usados para procesar la solicitud y el tiempo dedicado a cada componente. Esto puede resultar útil si trata de aumentar el rendimiento del sitio o de aislar lo que causa la devolución de un error HTTP específico.
- **Web Server Logging**: Registra todas las transacciones HTTP de un sitio web con el [formato de archivo de registro extendido W3C (en inglés)](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx). Este informe resulta útil para determinar las métricas totales del sitio, como el número de solicitudes tramitadas o cuántas solicitudes proceden de una dirección IP específica.

###Diagnósticos de aplicaciones

El diagnóstico de aplicaciones le permite capturar información generada por una aplicación web. Las aplicaciones de ASP.NET pueden usar la clase [System.Diagnostics.Trace](http://msdn.microsoft.com/library/36hhw2t6.aspx) para registrar información en el registro de diagnóstico de la aplicación. Por ejemplo:

	System.Diagnostics.Trace.TraceError("Si está viendo esto, ha surgido algún problema");

El diagnóstico de la aplicación le permite solucionar los problemas de la aplicación en ejecución mediante la emisión de información cuando se usan determinadas piezas de código. Esto resulta especialmente útil al tratar de terminar por qué el código usa una ruta de acceso específica, normalmente si la ruta de acceso genera un error u otro comportamiento no deseado.

Para obtener información acerca de cómo trabajar con diagnósticos de aplicaciones mediante Visual Studio, consulte [Solución de problemas de Sitios web Azure en Visual Studio](http://azure.microsoft.com/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/).

> [AZURE.NOTE] Al contrario de lo que ocurre al cambiar el archivo web.config, habilitar Diagnóstico de aplicaciones o cambiar los niveles del registro de diagnóstico no recicla el dominio de la aplicación en el que esta se ejecuta.

Sitios web Azure también registra información de implementación al publicar una aplicación en un sitio web. Esta acción se lleva a cabo automáticamente, por lo que no es necesario realizar ninguna configuración para el registro de implementaciones. El registro de implementaciones le permite determinar por qué se ha producido un error con la implementación. Por ejemplo, si usa un script de implementación personalizado, puede usar el registro de implementaciones para determinar por qué se ha producido un error con el script.

<a name="enablediag"></a><h2>Cómo: de diagnósticos</h2>

Los diagnósticos se pueden habilitar en la página **Configurar** del sitio web de Azure en el [Portal de administración de Azure](https://manage.microsoft.com). En la página **Configurar**, use las secciones **Diagnóstico de aplicaciones** y **Diagnósticos del sitio** para habilitar el registro.

Al habilitar el **diagnóstico de aplicaciones**, también debe seleccionar el **nivel de registro** y si desea habilitar el registro para el **sistema de archivos**, el **almacenamiento de tablas** o el **almacenamiento de blobs**. Si bien las tres ubicaciones de almacenamiento ofrecen la misma información básica de los eventos registrados, **almacenamiento de tablas** y **almacenamiento de blobs** registran información adicional como el identificador de instancia, el identificador de subproceso y una marca de tiempo más pormenorizada (formato de marca de graduación) que el registro en el **sistema de archivos**.

Si habilita los **diagnósticos del sitio**, debe seleccionar **almacenamiento** o **sistema de archivos** para el **registro del servidor web**. Si selecciona **storage**, tiene la opción de seleccionar una cuenta de almacenamiento y, a continuación, un contenedor de blob en el que se escribirán los registros. Todos los demás registros de **diagnósticos del sitio** se escriben solo en el sistema de archivos.

> [AZURE.NOTE] Solo se puede obtener acceso a la información almacenada en **almacenamiento de tablas** o **almacenamiento de blobs** mediante un cliente de almacenamiento o una aplicación que puedan funcionar directamente con estos sistemas de almacenamiento. Por ejemplo, Visual Studio 2013 contiene un Explorador de almacenamiento que se puede usar para explorar el almacenamiento de tabla o de blobs y HDInsight puede obtener acceso a los datos almacenados en el almacenamiento de blobs. También puede escribir una aplicación que obtiene acceso al servicio Almacenamiento de Azure mediante alguno de los [SDK de Azure](http://azure.microsoft.com/downloads/#).

A continuación se indica la configuración disponible al habilitar **Diagnóstico de aplicaciones**:

* **Logging level**: Le permite filtrar la información capturada como **informational**, **warning** o **error**. Si configura esta opción como **verbose**, registrará toda la información generada por la aplicación. **Nivel de registro** puede definirse de manera diferente para el registro en **sistema de archivos**, **almacenamiento de tablas** y **almacenamiento de blobs**.
* **File system**: Almacena la información de diagnóstico de la aplicación en el sistema de archivos del sitio web. Es posible obtener acceso a estos archivos por FTP, o bien se pueden descargar como un archivo ZIP con la utilización de Azure PowerShell o de las herramientas de línea de comandos de Azure.
* **Almacenamiento de tablas**: almacena la información de diagnóstico de aplicaciones en el nombre de tabla y en la cuenta de almacenamiento de Azure.
* **Almacenamiento de blobs**: almacena la información de diagnóstico de aplicaciones en el contenedor de blob y en la cuenta de almacenamiento de Azure especificada.
* **Retention period**: De forma predeterminada, los registros no se eliminan automáticamente de **almacenamiento de blobs**. Seleccione **establecer retención** y escriba el número de días durante los cuales desea que se conserven los registros si desea que estos se eliminen automáticamente.

> [AZURE.NOTE]  Al mismo tiempo se puede habilitar cualquier combinación de sistema de archivos, almacenamiento de tablas o almacenamiento de blobs, y estas opciones tiene configuraciones de nivel de registro individuales. Por ejemplo, puede registrar errores y advertencias en el almacenamiento de blobs como una solución de registro a largo plazo, mientras habilita el registro en el sistema de archivos con un nivel detallado.

> [AZURE.NOTE] Los diagnósticos también se pueden habilitar desde Azure PowerShell con el cmdlet **Set-AzureWebsite**. Si no tiene instalado Azure PowerShell o si no lo ha configurado para utilizar su suscripción a Azure, consulte [Uso de Azure PowerShell](http://azure.microsoft.com/develop/nodejs/how-to-guides/powershell-cmdlets/).

<a name="download"></a><h2>Cómo: registros</h2>

Se puede obtener acceso a la información de diagnóstico almacenada en el sistema de archivos del sitio web directamente mediante FTP. No obstante, también se puede descargar como un archivo ZIP con Azure PowerShell o mediante las herramientas de línea de comandos de Azure.

La estructura de directorios en que se almacenan los registros es la siguiente:

* **Registros de aplicación** - /LogFiles/Application/. Esta carpeta contiene uno o varios archivos de texto con información generada por el registro de aplicaciones.

* **Seguimientos de solicitudes con error** - /LogFiles/W3SVC#########/. Esta carpeta contiene un archivo XSL y uno o varios archivos XML. Asegúrese de descargar el archivo XSL en el mismo directorio de los archivos XML, porque el archivo XSL proporciona funcionalidad para dar formato y filtrar los contenidos de los archivos XML cuando se visualizan en Internet Explorer.

* **Registros de error detallados** - /LogFiles/DetailedErrors/. Esta carpeta contiene uno o varios archivos .htm con información amplia de todos los errores HTTP que se han producido. 

* **Registros del servidor web**/LogFiles/http/RawLogs Esta carpeta contiene uno o varios archivos de texto a los que se le aplica el [formato de archivo de registro extendido W3C (en inglés)](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx). 

* **Registros de implementación** - /LogFiles/Git. Esta carpeta contiene registros generados por los procesos de implementación internos usados por Sitios web Azure, además de los registros de las implementaciones Git.

###FTP

Para obtener acceso a la información de diagnóstico mediante FTP, visite el **Panel** del sitio web en el Portal de administración de Azure. En la sección **Quick Glance**, haga clic en el vínculo **FTP Diagnostic Logs** para obtener acceso a los archivos de registro mediante FTP. La entrada **Usuario de implementación / FTP** enumera el nombre de usuario que debe usarse para obtener acceso al sitio FTP.

> [AZURE.NOTE] Si no se define la entrada **Deployment / FTP User** o si ha olvidado la contraseña de este usuario, puede crear un nuevo usuario y una contraseña mediante el vínculo **Reset deployment credentials** en la sección **Quick Glance** del **Panel**.

###Descarga con Azure PowerShell

Para descargar los archivos de registro, inicie una nueva instancia de Azure PowerShell y use el siguiente comando:

	Save-AzureWebSiteLog -Name nombresitioweb

Este comando guardará los registros del sitio web especificados mediante el parámetro **-Name** en un archivo con nombre **logs.zip** en el directorio actual.

> [AZURE.NOTE] Si no tiene instalado Azure PowerShell o si no lo ha configurado para utilizar su suscripción a Azure, consulte [Uso de Azure PowerShell](http://azure.microsoft.com/develop/nodejs/how-to-guides/powershell-cmdlets/).

###Descarga con las herramientas de línea de comandos de Azure

Para descargar los archivos de registro mediante las herramientas de línea de comandos de Azure, abra una sesión nueva del símbolo del sistema, PowerShell, Bash o Terminal y escriba el siguiente comando:

	azure site log download websitename

Este comando guardará los registros en el sitio web denominado 'websitename' en un archivo con nombre **diagnostics.zip** en el directorio actual.

> [AZURE.NOTE] Si no tiene instaladas las herramientas de línea de comandos de Azure o las ha configurado para que usen la suscripción de Azure, consulte [Uso de las herramientas de línea de comandos de Azure](http://azure.microsoft.com/develop/nodejs/how-to-guides/command-line-tools/).

<a name="streamlogs"></a><h2>Cómo: registros</h2>

Al implementar una aplicación, suele resultar útil ver la información de registro casi en tiempo real. Para ello, puede transmitir la información de registro al entorno de desarrollo con Azure PowerShell o las herramientas de línea de comandos de Azure.

> [AZURE.NOTE]  Algunos tipos de búfer de registros escriben en el archivo de registro, lo que puede ocasionar la transmisión de eventos desordenados. Por ejemplo, una entrada de registro de aplicaciones que se genera cuando un usuario visita una página se puede visualizar en la transmisión antes de la entrada de registro HTTP correspondiente para la solicitud de la página.

> [AZURE.NOTE] La transmisión por secuencias del registro también transmitirá información escrita en cualquier archivo de texto almacenado en la carpeta **D:\\home\\LogFiles\\**.

###Transmisión con Azure PowerShell

Para transmitir información de registro, inicie una nueva instancia de Azure PowerShell y use el siguiente comando:

	Get-AzureWebSiteLog -Name websitename -Tail

Este comando establecerá conexión con el sitio web especificado mediante el parámetro **-Name** y comenzará a transmitir información a la ventana de PowerShell a medida que se produzcan los eventos de registro en el sitio web. Toda la información escrita en archivos que terminan en .txt, .log o .htm almacenados en el directorio /LogFiles (d:/home/logfiles) se transmitirá a la consola local.

Para filtrar eventos específicos, como errores, use el parámetro **-Message**. Por ejemplo:

	Get-AzureWebSiteLog -Name websitename -Tail -Message Error

Para filtrar tipos de registros específicos, como HTTP, use el parámetro **-Path**. Por ejemplo:

	Get-AzureWebSiteLog -Name websitename -Tail -Path http

Para ver una lista de rutas de acceso disponibles, use el parámetro -ListPath.

> [AZURE.NOTE] Si no tiene instalado Azure PowerShell o si no lo ha configurado para utilizar su suscripción a Azure, consulte [Uso de Azure PowerShell](http://azure.microsoft.com/develop/nodejs/how-to-guides/powershell-cmdlets/).

###Transmisión con las herramientas de línea de comandos de Azure

Para transmitir información de registro, abra una nueva sesión del símbolo del sistema, PowerShell, Bash o Terminal y escriba el siguiente comando:

	azure site log tail websitename

Este comando establecerá conexión con el sitio web denominado 'websitename' y comenzará a transmitir información a la ventana a medida que se produzcan los eventos de registro en el sitio web. Toda la información escrita en archivos que terminan en .txt, .log o .htm almacenados en el directorio /LogFiles (d:/home/logfiles) se transmitirá a la consola local.

Para filtrar eventos específicos, como errores, use el parámetro **--Filter**. Por ejemplo:

	azure site log tail websitename --filter Error

Para filtrar tipos de registros específicos, como HTTP, use el parámetro **--Path**. Por ejemplo:

	azure site log tail websitename --path http

> [AZURE.NOTE] Si no tiene instaladas las herramientas de línea de comandos de Azure o las ha configurado para que usen la suscripción de Azure, consulte [Uso de las herramientas de línea de comandos de Azure](http://azure.microsoft.com/develop/nodejs/how-to-guides/command-line-tools/).

<a name="understandlogs"></a><h2>Cómo: de los registros de diagnóstico</h2>

###Registros de diagnóstico de aplicaciones

El diagnóstico de aplicaciones almacena información con un formato específico para aplicaciones .NET, en función de si almacena los registros en el sistema de archivos, en el almacenamiento de tablas o en el almacenamiento de blobs. El conjunto base de datos almacenados es el mismo en los tres tipos de almacenamiento: la fecha y la hora en que se ha producido el evento, el identificador del proceso que ha producido el evento, el tipo de evento (información, advertencia o error) y el mensaje del evento.

__File system__

Cada línea registrada en el sistema de archivos o recibida mediante transmisión presentará el siguiente formato:

	{Date}  PID[{process id}] {event type/level} {message}

Por ejemplo, un evento de error presentaría un formato similar al siguiente:

	2014-01-30T16:36:59  PID[3096] Error       Fatal error on the page!

El registro en el sistema de archivos ofrece la información más básica de los tres métodos disponibles, ya que ofrece solo la hora, el identificador del proceso, el nivel de evento y el mensaje.

__Table storage__

Al realizar registros en el almacenamiento de tabla, se usan propiedades adicionales para facilitar la búsqueda de los datos almacenados en la tabla, así como información más pormenorizada sobre el evento. Las siguientes propiedades (columnas) se usan para cada entidad (fila) almacenada en la tabla.

<table style="width:100%;border-collapse:collapse">
<thead>
<tr>
<th style="width:45%;border:1px solid black;background-color:#0099dd">Property name</th>
<th style="border:1px solid black;vertical-align:top;background-color:#0099dd">Value/format</th>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">PartitionKey</td>
<td style="border:1px solid black;vertical-align:top">Fecha/hora del evento con el formato aaaaMMddHH</td>
</tr>
</thead>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">RowKey</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">A GUID value that uniquely identifies this entity</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">Timestamp</td>
<td style="border:1px solid black;vertical-align:top">La fecha y la hora en que se ha producido el evento</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">EventTickCount</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">The date and time that the event occurred, in Tick format (greater precision)</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">ApplicationName</td>
<td style="border:1px solid black;vertical-align:top">El nombre del sitio web</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">Level</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">Event level (e.g. error, warning, information)</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">EventId</td>
<td style="border:1px solid black;vertical-align:top">El identificador de evento de este evento<br>El valor predeterminado es 0 si no especifica ninguno</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">InstanceId</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">Instance of the website that the even occurred on</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">Pid</td>
<td style="border:1px solid black;vertical-align:top">Identificador del proceso</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">Tid</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">The thread ID of the thread that produced the event</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">Message</td>
<td style="border:1px solid black;vertical-align:top">Mensaje detallado del evento</td>
</tr>
</table>

__Blob storage__

Al realizar registros en el almacenamiento de blobs, los datos se almacenan con un formato de valores separados por comas (CSV). De manera similar al almacenamiento de tabla, se registran campos adicionales para ofrecer información más pormenorizada acerca del evento. Las siguientes propiedades se usan para cada fila con el formato CSV:

<table style="width:100%;border-collapse:collapse">
<thead>
<tr>
<th style="width:45%;border:1px solid black;background-color:#0099dd">Property name</th>
<th style="border:1px solid black;vertical-align:top;background-color:#0099dd">Value/format</th>
</tr>
</thead>
<tr>
<td style="border:1px solid black;vertical-align:top">Date</td>
<td style="border:1px solid black;vertical-align:top">La fecha y la hora en que se ha producido el evento</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">Level</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">Event level (e.g. error, warning, information)</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">ApplicationName</td>
<td style="border:1px solid black;vertical-align:top">El nombre del sitio web</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">InstanceId</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">Instance of the website that the even occurred on</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">EventTickCount</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">The date and time that the event occurred, in Tick format (greater precision)</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">EventId</td>
<td style="border:1px solid black;vertical-align:top">El identificador de evento de este evento<br>El valor predeterminado es 0 si no especifica ninguno</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">Pid</td>
<td style="border:1px solid black;vertical-align:top">Identificador del proceso</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">Tid</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">The thread ID of the thread that produced the event</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">Message</td>
<td style="border:1px solid black;vertical-align:top">Mensaje detallado del evento</td>
</tr>
</table>

Los datos almacenados en un blob serían similares a los siguientes:

	date,level,applicationName,instanceId,eventTickCount,eventId,pid,tid,message
	2014-01-30T16:36:52,Error,mywebsite,6ee38a,635266966128818593,0,3096,9,An error occurred

> [AZURE.NOTE] La primera línea del registro contendrá los encabezados de columna tal y como se representan en este ejemplo.

###Seguimiento de solicitudes con error

El seguimiento de solicitudes con error se almacena en archivos XML con nombre __fr######.xml__. Para facilitar la visualización de la información registrada, se facilita una hoja de estilo XSL con nombre __freb.xsl__ en el mismo directorio que los archivos XML. Al abrir uno de los archivos XML en Internet Explorer, se usará la hoja de estilo XSL para ofrecer una visualización con formato de la información de seguimiento. El formato será similar al siguiente:

![failed request viewed in the browser](./media/web-sites-enable-diagnostic-log/tws-failedrequestinbrowser.png)

###Registros de error detallados

Los registros de error detallados son documentos HTML que ofrecen información más detallada sobre los errores HTTP que se han producido. Habida cuenta de que se trata sencillamente de documentos HTML, se pueden ver en un explorador web.

###Registros del servidor web

A los registros del servidor web se les aplica el [formato de archivo de registro extendido W3C (en inglés)](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx). Esta información se puede leer con un editor de texto o analizarse con utilidades como el [analizador del registro (en inglés)](http://go.microsoft.com/fwlink/?LinkId=246619).

> [AZURE.NOTE] Los registros que genera Sitios web Azure no admiten los campos __s-computername__, __s-ip__ o __cs-version__.

<a name="nextsteps"></a><h2>Pasos siguientes</h2>

- [Supervisión de sitios web](/es-es/manage/services/web-sites/how-to-monitor-websites/)
- [Tutorial: solución de problemas de Sitios web Azure en Visual Studio](/es-es/develop/net/best-practices/troubleshooting-web-sites/)
- [Solución de problemas de Sitios web Azure en Visual Studio](/es-es/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/)
- [Análisis de registros de sitios web en HDInsight](http://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)





<!--HONumber=42-->
