<properties pageTitle="Consola y registros de streaming" description="Streaming logs and console overview" title="Streaming logs and console" authors="adamab" manager="wpickett" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="adamab" />

#Registros de transmisión y la consola

### Registros de streaming ###

El portal de vista previa de Microsoft Azure ofrece un visor de registros de streaming integrado que le permite ver los eventos de seguimiento desde los sitios web en tiempo real.  

La configuración requiere algunos pasos sencillos:

- Escritura de seguimientos en el código.
- Habilitación de diagnóstico de aplicaciones desde el portal de vista previa de Azure.
- Clic en los registros de transmisión que forman parte del cuadro del sitio web.

### Cómo escribir los seguimientos en el código ###

La escritura de seguimientos en el código es sencilla.  En C# es tan fácil como escribir el siguiente código:

`````````````````````````
Trace.TraceInformation("My trace statement");
`````````````````````````

`````````````````````````
Trace.TraceWarning("My warning statement");
`````````````````````````

`````````````````````````
Trace.TraceError("My error statement");
`````````````````````````

La clase de seguimientos se encuentra en el espacio de nombres System.Diagnostics.

En una aplicación node.js, puede escribir este código para conseguir el mismo resultado:

`````````````````````````
console.log("My trace statement").
`````````````````````````

### Habilitación y visualización de registros de transmisión ###

El diagnóstico se habilita según el sitio web.  En el [portal](https://portal.azure.com), haga clic en el botón **Browse** de la barra de menú izquierda y, a continuación, haga clic en **Websites** para obtener la lista de todos los sitios web.  

![][BrowseSitesScreenshot]

Haga clic en el nombre del sitio web que desea configurar.  A continuación, haga clic en la parte denominada **DIAGNOSTIC LOGS** y cambie **Application Logging (Filesystem)** a la configuración **ON**.  Aparecerá la opción **Level**, que le permitirá cambiar el nivel de gravedad de los seguimientos que desea capturar.  Debe configurar esto en **Detallado** si solo está intentando familiarizarse con la característica, ya que esto asegurará que se registran las instrucciones de seguimiento.

Haga clic en **GUARDAR** en la parte superior del cuadro y estará listo para ver los registros.

Para ver los registros de streaming desde dentro del portal, haga clic en la parte **REGISTROS DE STREAMING** del cuadro del sitio web.  Si el sitio escribe instrucciones de seguimiento activamente, debe verlas en la ventana resultante prácticamente en tiempo real.

![][StreamingLogsScreenshot]

## Consola ##

El portal de vista previa de Azure ofrece acceso a la consola para el entorno del sitio web. Puede explorar el sistema de archivos del sito web y ejecutar los scripts de cmd/powershell.  Se realizará la vinculación mediante los mismos permisos establecidos en el código del sitio web en ejecución cuando se ejecuten los comandos de consola. No podrá obtener acceso a los directorios protegidos o ejecutar scripts que requieran permisos elevados.  

Para ir a la consola, desplácese al sitio web como se describe en la sección anterior.  Haga clic en la parte **Consola** y se abrirá la consola.

![][ConsoleScreenshot]

Para familiarizarse con la consola, pruebe estos comandos básicos:



`````````````````````````
dir
`````````````````````````

`````````````````````````
cd
`````````````````````````



<!-- Images. -->
[BrowseSitesScreenshot]: ./media/web-sites-streaming-logs-and-console/browse-sites.png
[StreamingLogsScreenshot]: ./media/web-sites-streaming-logs-and-console/streaming-logs.png
[ConsoleScreenshot]: ./media/web-sites-streaming-logs-and-console/console.png
