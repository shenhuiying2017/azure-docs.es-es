---
title: "Habilitación del registro de diagnóstico para aplicaciones web en Azure App Service"
description: "Obtenga información acerca de cómo habilitar el registro de diagnóstico y agregar la instrumentación a su aplicación, así como la manera de acceder a la información registrada por Azure."
services: app-service
documentationcenter: .net
author: cephalin
manager: erikre
editor: jimbe
ms.assetid: c9da27b2-47d4-4c33-a3cb-1819955ee43b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2016
ms.author: cephalin
ms.openlocfilehash: a5ac6c02e28c19346abae9e5ea3dba9af4022dde
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/07/2017
---
# <a name="enable-diagnostics-logging-for-web-apps-in-azure-app-service"></a>Habilitación del registro de diagnóstico para aplicaciones web en Azure App Service
## <a name="overview"></a>Información general
Azure integra diagnósticos para ayudar a depurar [Aplicaciones web de App Service](http://go.microsoft.com/fwlink/?LinkId=529714). En este artículo se ofrece información acerca de cómo habilitar el registro de diagnóstico, agregar instrumentación a la aplicación y obtener acceso a la información que registra Azure.

En este artículo se usa [Azure Portal](https://portal.azure.com), Azure PowerShell y la interfaz de la línea de comandos de Azure (CLI de Azure) para trabajar con registros de diagnóstico. Para obtener información acerca de cómo trabajar con registros de diagnóstico mediante Visual Studio, consulte [Solución de problemas de Azure en Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md).

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="whatisdiag"></a>Diagnóstico del servidor web y diagnóstico de aplicaciones
Aplicaciones web de App Service ofrece la funcionalidad de diagnóstico para registrar información del servidor web y de la aplicación web. De forma lógica, estos diagnósticos se dividen en **diagnósticos del servidor web** y **diagnóstico de aplicaciones**.

### <a name="web-server-diagnostics"></a>Diagnósticos del servidor web
Puede habilitar o deshabilitar los siguientes tipos de registros:

* **Registro de errores detallado** : registra información detallada de errores para códigos de estado HTTP que indican un problema (código de estado 400 o superior). Puede contener información que puede ayudar a determinar por qué el servidor devolvió el código de error.
* **Seguimiento de solicitudes con error** : registra información detallada acerca de solicitudes con error, incluido un seguimiento de los componentes de IIS usados para procesar la solicitud y el tiempo dedicado a cada componente. Puede resultar útil si trata de aumentar el rendimiento del sitio o de aislar lo que causa la devolución de un error HTTP específico.
* **Registro del servidor web** : registra todas las transacciones HTTP con el [formato de archivo de registro extendido de W3C](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx). Resulta útil al determinar las métricas totales del sitio, como el número de solicitudes tramitadas o que proceden de una dirección IP específica.

### <a name="application-diagnostics"></a>diagnósticos de la aplicación
El diagnóstico de aplicaciones le permite capturar información generada por una aplicación web. Las aplicaciones de ASP.NET pueden usar la clase [System.Diagnostics.Trace](http://msdn.microsoft.com/library/36hhw2t6.aspx) para registrar información en el registro de diagnóstico de la aplicación. Por ejemplo:

    System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");

En tiempo de ejecución puede recuperar estos registros para ayudar a solucionar problemas. Para obtener más información, consulte [Solución de problemas de aplicaciones web de Azure en Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md).

Las aplicaciones web de App Service también registran información de implementación al publicar contenido en una aplicación web. Esta acción se lleva a cabo automáticamente, por lo que no es necesario realizar ninguna configuración para el registro de implementaciones. El registro de implementaciones le permite determinar por qué se ha producido un error con la implementación. Por ejemplo, si usa un script de implementación personalizado, puede usar el registro de implementaciones para determinar por qué se ha producido un error con el script.

## <a name="enablediag"></a>Habilitación de diagnósticos
Para habilitar el diagnóstico en [Azure Portal](https://portal.azure.com), vaya a la página de la aplicación web y haga clic en **Configuración > Registros de diagnóstico**.

<!-- todo:cleanup dogfood addresses in screenshot -->
![Parte de los registros](./media/web-sites-enable-diagnostic-log/logspart.png)

Cuando habilite **Diagnóstico de aplicaciones**, elija también **Nivel**. Esta configuración le permite filtrar la información capturada como **informativa**, **advertencia** o **error**. Si establece esta opción en **Registros detallados**, se registrará toda la información que genere la aplicación.

> [!NOTE]
> Al contrario de lo que ocurre al cambiar el archivo web.config, habilitar Diagnóstico de aplicaciones o cambiar los niveles del registro de diagnóstico no recicla el dominio de la aplicación en el que esta se ejecuta.
>
>

Para la opción **Registro de aplicaciones**, puede activar temporalmente la opción del sistema de archivos con fines de depuración. Esta opción se desactiva automáticamente en 12 horas. También puede activar la opción de Blob Storage para seleccionar un contenedor de blob en el que escribir registros.

Para la opción **Registro del servidor web**, puede seleccionar **Almacenamiento** o **Sistema de archivos**. Si selecciona **almacenamiento**, tiene la opción de seleccionar una cuenta de almacenamiento y, después, un contenedor de blobs en el que se escribirán los registros. 

Si almacena registros en el sistema de archivos, es posible obtener acceso a estos archivos por FTP, o bien se pueden descargar como un archivo ZIP mediante Azure PowerShell o la Interfaz de la línea de comandos de Azure (CLI de Azure).

De forma predeterminada, los registros no se eliminan automáticamente (con la excepción del **Registro de aplicaciones [sistema de archivos]**). Para eliminar automáticamente los registros, establezca el campo **Período de retención (días)**.

> [!NOTE]
> Si se [regeneran las claves de acceso de su cuenta de almacenamiento](../storage/common/storage-create-storage-account.md), deberá restablecer la configuración de registro correspondiente para usar las claves actualizadas. Para ello, siga estos pasos:
>
> 1. En la pestaña **Configurar**, establezca la característica de registro correspondiente de **Desactivar**. Guarde la configuración.
> 2. Vuelva a habilitar el registro en el blob de la cuenta de almacenamiento o en la tabla. Guarde la configuración.
>
>

Al mismo tiempo se puede habilitar cualquier combinación de sistema de archivos, almacenamiento de tablas o almacenamiento de blobs, y estas opciones tiene configuraciones de nivel de registro individuales. Por ejemplo, puede registrar errores y advertencias en el almacenamiento de blobs como una solución de registro a largo plazo, mientras habilita el registro en el sistema de archivos con un nivel detallado.

Si bien las tres ubicaciones de almacenamiento ofrecen la misma información básica de los eventos registrados, **table storage** y **blob storage** registran información adicional como el identificador de instancia, el identificador de subproceso y una marca de tiempo más pormenorizada (formato de marca de graduación) que el registro en **file system**.

> [!NOTE]
> Solo se puede obtener acceso a la información almacenada en **table storage** o **blob storage** mediante una aplicación o un cliente de almacenamiento que puedan trabajar directamente con estos sistemas de almacenamiento. Por ejemplo, Visual Studio 2013 contiene un Explorador de Storage que se puede usar para explorar el almacenamiento de tabla o de blobs y HDInsight puede obtener acceso a los datos almacenados en el almacenamiento de blobs. También puede escribir una aplicación que obtiene acceso a Azure Storage mediante algunos de los [SDK de Azure](/downloads/#).
>
> [!NOTE]
> Los diagnósticos también se pueden habilitar desde Azure PowerShell con el cmdlet **Set-AzureWebsite** . Si no tiene instalado Azure PowerShell o si no lo ha configurado para utilizar su suscripción a Azure, consulte [Uso de Azure PowerShell](/develop/nodejs/how-to-guides/powershell-cmdlets/).
>
>

## <a name="download"></a> Descarga de registros
Se puede obtener acceso a la información de diagnóstico almacenada en el sistema de archivos de la aplicación web directamente mediante FTP. No obstante, también se puede descargar como un archivo ZIP con Azure PowerShell o mediante la interfaz de la línea de comandos de Azure.

La estructura de directorios en que se almacenan los registros es la siguiente:

* **Registros de aplicaciones** : /LogFiles/Application/. Esta carpeta contiene uno o varios archivos de texto con información generada por el registro de aplicaciones.
* **Seguimientos de solicitudes con error** : /LogFiles/W3SVC#########/. Esta carpeta contiene un archivo XSL y uno o varios archivos XML. Asegúrese de descargar el archivo XSL en el mismo directorio de los archivos XML, porque el archivo XSL proporciona funcionalidad para dar formato y filtrar los contenidos de los archivos XML cuando se visualizan en Internet Explorer.
* **Registros detallados de errores** : /LogFiles/DetailedErrors/. Esta carpeta contiene uno o varios archivos .htm con información amplia de todos los errores HTTP que se han producido.
* **Registros de servidor web** : /LogFiles/http/RawLogs. Esta carpeta contiene uno o varios archivos de texto a los que se le aplica el [formato de archivo de registro extendido W3C](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx).
* **Registros de implementaciones** : /LogFiles/Git. Esta carpeta contiene registros generados por los procesos de implementación internos usados por las aplicaciones web de Azure, además de los registros de las implementaciones Git.

### <a name="ftp"></a>FTP

Para abrir una conexión FTP al servidor FTP de la aplicación, consulte [Implementación de la aplicación en Azure App Service mediante FTP/S](app-service-deploy-ftp.md).

Una vez conectado al servidor FTP/S de la aplicación web, abra la carpeta **LogFiles**, donde se almacenan los archivos de registro.

### <a name="download-with-azure-powershell"></a>Descarga con Azure PowerShell
Para descargar los archivos de registro, inicie una nueva instancia de Azure PowerShell y use el siguiente comando:

    Save-AzureWebSiteLog -Name webappname

Este comando guarda los registros de la aplicación web que especifica el parámetro **-Name** en un archivo denominado **logs.zip** en el directorio actual.

> [!NOTE]
> Si no tiene instalado Azure PowerShell o si no lo ha configurado para utilizar su suscripción a Azure, consulte [Uso de Azure PowerShell](/develop/nodejs/how-to-guides/powershell-cmdlets/).
>
>

### <a name="download-with-azure-command-line-interface"></a>Descarga con la interfaz de la línea de comandos de Azure
Para descargar los archivos de registro mediante la interfaz de la línea de comandos de Azure, abra una sesión nueva del símbolo del sistema, PowerShell, Bash o Terminal y escriba el siguiente comando:

    azure site log download webappname

Este comando guarda los registros de la aplicación web denominada "webappname" en un archivo denominado **diagnostics.zip** en el directorio actual.

> [!NOTE]
> Si no tiene instalada la interfaz de la línea de comandos de Azure (CLI de Azure) o si no la ha configurado para que use la suscripción de Azure, consulte [Cómo usar la CLI de Azure](../cli-install-nodejs.md).
>
>

## <a name="how-to-view-logs-in-application-insights"></a>Visualización de registros en Application Insights
Visual Studio Application Insights proporciona herramientas para filtrar y buscar registros y para correlacionar los registros con solicitudes y otros eventos.

1. Incorporación del SDK de Application Insights al proyecto de Visual Studio
   * En el Explorador de soluciones, haga clic con el botón derecho en el proyecto y elija Agregar Application Insights. La interfaz le guiará a través de pasos que incluyen la creación de un recurso de Application Insights. [Más información](../application-insights/app-insights-asp-net.md)
2. Agregue el paquete del agente de escucha.
   * Haga clic con el botón derecho en el proyecto y elija Administrar paquetes NuGet. Seleccione `Microsoft.ApplicationInsights.TraceListener` [Más información](../application-insights/app-insights-asp-net-trace-logs.md)
3. Cargue el proyecto y ejecútelo para generar datos de registro.
4. En [Azure Portal](https://portal.azure.com/), busque el nuevo recurso de Application Insights y abra **Buscar**. Verá los datos de registro, junto con la solicitud, el uso y otros datos de telemetría. Es posible que algunos datos de telemetría demoren unos minutos en aparecer: haga clic en Actualizar. [Más información](../application-insights/app-insights-diagnostic-search.md)

[Obtenga más información acerca del seguimiento del rendimiento con Application Insights](../application-insights/app-insights-azure-web-apps.md)

## <a name="streamlogs"></a> Registros
Al implementar una aplicación, suele resultar útil ver la información de registro casi en tiempo real. Para ello, puede transmitir la información de registro al entorno de desarrollo con Azure PowerShell o la Interfaz de la línea de comandos de Azure.

> [!NOTE]
> Algunos tipos de búfer de registros se escriben en el archivo de registro, lo que puede ocasionar la transmisión de eventos desordenados. Por ejemplo, una entrada de registro de aplicaciones que se genera cuando un usuario visita una página se puede visualizar en la transmisión antes de la entrada de registro HTTP correspondiente para la solicitud de la página.
>
> [!NOTE]
> La transmisión de registros también transmite información escrita en cualquier archivo de texto almacenado en la carpeta **D:\\home\\LogFiles\\**.
>
>

### <a name="streaming-with-azure-powershell"></a>Transmisión con Azure PowerShell
Para transmitir información de registro, inicie una nueva instancia de Azure PowerShell y use el siguiente comando:

    Get-AzureWebSiteLog -Name webappname -Tail

Este comando establecerá conexión con la aplicación web especificada por el parámetro **-Name** y comenzará a transmitir información a la ventana de PowerShell a medida que se produzcan los eventos de registro en la aplicación web. Toda la información escrita en archivos terminados en .txt, .log o .htm almacenados en el directorio /LogFiles (d:/home/logfiles) se transmite a la consola local.

Para filtrar eventos específicos, como errores, use el parámetro **-Message** . Por ejemplo:

    Get-AzureWebSiteLog -Name webappname -Tail -Message Error

Para filtrar tipos de registros específicos, como HTTP, use el parámetro **-Path** . Por ejemplo:

    Get-AzureWebSiteLog -Name webappname -Tail -Path http

Para ver una lista de rutas de acceso disponibles, use el parámetro -ListPath.

> [!NOTE]
> Si no tiene instalado Azure PowerShell o si no lo ha configurado para utilizar su suscripción a Azure, consulte [Uso de Azure PowerShell](/develop/nodejs/how-to-guides/powershell-cmdlets/).
>
>

### <a name="streaming-with-azure-command-line-interface"></a>Transmisión con la interfaz de la línea de comandos de Azure
Para transmitir información de registro, abra una nueva sesión del símbolo del sistema, PowerShell, Bash o Terminal y escriba el siguiente comando:

    az webapp log tail --name webappname --resource-group myResourceGroup

Este comando establece conexión con la aplicación web denominada "webappname" y comenzará a transmitir información a la ventana a medida que se produzcan los eventos de registro en la aplicación web. Toda la información escrita en archivos terminados en .txt, .log o .htm almacenados en el directorio /LogFiles (d:/home/logfiles) se transmite a la consola local.

Para filtrar eventos específicos, como errores, use el parámetro **--Filter** . Por ejemplo:

    az webapp log tail --name webappname --resource-group myResourceGroup --filter Error

Para filtrar tipos de registros específicos, como HTTP, use el parámetro **--Path** . Por ejemplo:

    az webapp log tail --name webappname --resource-group myResourceGroup --path http

> [!NOTE]
> Si no tiene instalada la interfaz de la línea de comandos de Azure o si no la ha configurado para que use la suscripción de Azure, consulte [Cómo utilizar la interfaz de línea de comandos de Azure](../cli-install-nodejs.md).
>
>

## <a name="understandlogs"></a> Información sobre los registros de diagnóstico
### <a name="application-diagnostics-logs"></a>Registros de diagnóstico de aplicaciones
El diagnóstico de aplicaciones almacena información con un formato específico para aplicaciones .NET, en función de si almacena los registros en el sistema de archivos, en el almacenamiento de tablas o en el almacenamiento de blobs. El conjunto base de datos almacenados es el mismo en los tres tipos de almacenamiento: la fecha y la hora en que se ha producido el evento, el identificador del proceso que ha producido el evento, el tipo de evento (información, advertencia o error) y el mensaje del evento.

**Sistema de archivos**

Cada línea registrada en el sistema de archivos o recibida mediante transmisión presentará el siguiente formato:

    {Date}  PID[{process ID}] {event type/level} {message}

Por ejemplo, un evento de error presentaría un formato similar al siguiente:

    2014-01-30T16:36:59  PID[3096] Error       Fatal error on the page!

El registro en el sistema de archivos ofrece la información más básica de los tres métodos disponibles, ya que ofrece solo la hora, el identificador del proceso, el nivel de evento y el mensaje.

**Almacenamiento de tablas**

Al realizar registros en el almacenamiento de tabla, se usan propiedades adicionales para facilitar la búsqueda de los datos almacenados en la tabla, así como información más pormenorizada sobre el evento. Las siguientes propiedades (columnas) se usan para cada entidad (fila) almacenada en la tabla.

| Nombre de propiedad | Valor/formato |
| --- | --- |
| PartitionKey |Fecha/hora del evento con el formato aaaaMMddHH |
| RowKey |Un valor de GUID que identifica a esta entidad de forma exclusiva |
| Timestamp |La fecha y la hora en que se ha producido el evento |
| EventTickCount |La fecha y hora en que se ha producido el evento, con formato de marca de graduación (mayor precisión) |
| ApplicationName |El nombre de la aplicación web |
| Nivel |Nivel del evento (por ejemplo, error, advertencia, información). |
| EventId |El identificador de este evento<p><p>El valor predeterminado es 0 si no se ha especificado ninguno |
| InstanceId |Instancia de la aplicación web en que se ha producido el evento |
| Pid |Identificador del proceso |
| Tid |El identificador del subproceso que ha generado el evento |
| Message |Mensaje detallado del evento |

**Blob storage**

Al realizar registros en el almacenamiento de blobs, los datos se almacenan con un formato de valores separados por comas (CSV). De manera similar al almacenamiento de tabla, se registran campos adicionales para ofrecer información más pormenorizada acerca del evento. Las siguientes propiedades se usan para cada fila con el formato CSV:

| Nombre de propiedad | Valor/formato |
| --- | --- |
| Date |La fecha y la hora en que se ha producido el evento |
| Nivel |Nivel del evento (por ejemplo, error, advertencia, información). |
| ApplicationName |El nombre de la aplicación web |
| InstanceId |Instancia de la aplicación web en que se ha producido el evento |
| EventTickCount |La fecha y hora en que se ha producido el evento, con formato de marca de graduación (mayor precisión) |
| EventId |El identificador de este evento<p><p>El valor predeterminado es 0 si no se ha especificado ninguno |
| Pid |Identificador del proceso |
| Tid |El identificador del subproceso que ha generado el evento |
| Message |Mensaje detallado del evento |

Los datos almacenados en un blob serían similares a los siguientes:

    date,level,applicationName,instanceId,eventTickCount,eventId,pid,tid,message
    2014-01-30T16:36:52,Error,mywebapp,6ee38a,635266966128818593,0,3096,9,An error occurred

> [!NOTE]
> La primera línea del registro contendrá los encabezados de columna tal y como se representan en este ejemplo.
>
>

### <a name="failed-request-traces"></a>Seguimientos de solicitudes con error
El seguimiento de solicitudes con error se almacena en archivos XML con nombre **fr######.xml**. Para facilitar la visualización de la información registrada, se proporciona una hoja de estilo XSL con nombre **freb.xsl** en el mismo directorio que los archivos XML. Si abre uno de los archivos XML en Internet Explorer, se usará la hoja de estilo XSL para ofrecer una visualización con formato de la información de seguimiento, similar a la siguiente:

![solicitud con error visualizada en el explorador](./media/web-sites-enable-diagnostic-log/tws-failedrequestinbrowser.png)

### <a name="detailed-error-logs"></a>Registros detallados de errores
Los registros de error detallados son documentos HTML que ofrecen información más detallada sobre los errores HTTP que se han producido. Habida cuenta de que se trata sencillamente de documentos HTML, se pueden ver en un explorador web.

### <a name="web-server-logs"></a>Registros de servidor web
A los registros del servidor web se les aplica el [formato de archivo de registro extendido W3C](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx). Esta información se puede leer con un editor de texto o analizarse con utilidades como el [analizador del registro](http://go.microsoft.com/fwlink/?LinkId=246619).

> [!NOTE]
> Los registros generados por aplicaciones web de Azure no admiten los campos **s-computername**, **s-ip** o **cs-version**.
>
>

## <a name="nextsteps"></a> Pasos siguientes
* [Supervisión de Web Apps](web-sites-monitor.md)
* [Solución de problemas de aplicaciones web de Azure en Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md)
* [Análisis de registros de aplicación web en HDInsight](http://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)

> [!NOTE]
> Si desea empezar a trabajar con Azure App Service antes de inscribirse para abrir una cuenta de Azure, vaya a [Prueba de App Service](https://azure.microsoft.com/try/app-service/), donde podrá crear inmediatamente una aplicación web de inicio de corta duración en App Service. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.
>
>
