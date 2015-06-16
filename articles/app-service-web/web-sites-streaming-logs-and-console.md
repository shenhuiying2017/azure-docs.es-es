<properties 
	pageTitle="Consola y registros de streaming" 
	description="Información general de la consola y los registros de transmisión" 
	authors="adamabdelhamed" 
	manager="wpickett" 
	editor="" 
	services="app-service\web" 
	documentationCenter=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/17/2014" 
	ms.author="adamab"/>

#Registros de transmisión y la consola

### Registros de streaming ###

El Portal de Microsoft Azure ofrece un visor de registros de streaming integrado que le permite ver los eventos de seguimiento desde las aplicaciones web del Servicio de aplicaciones de Azure en tiempo real.  

La configuración requiere algunos pasos sencillos:

- Escritura de seguimientos en el código.
- Habilitación de Diagnóstico de aplicaciones desde el Portal de Azure.
- Clic en los registros de streaming en la hoja de la aplicación web

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

La clase Trace reside en el espacio de nombres System.Diagnostics.

En una aplicación node.js, puede escribir este código para conseguir el mismo resultado:

`````````````````````````
console.log("My trace statement").
`````````````````````````

### Habilitación y visualización de registros de transmisión ###

El diagnóstico se habilita según la aplicación web.  Desde el [portal](https://portal.azure.com), haga clic en el botón **Examinar** situado en la barra de menú de la izquierda y, a continuación, haga clic en **Aplicaciones web** para obtener la lista de todas las aplicaciones web.  

![][BrowseSitesScreenshot]

Haga clic en el nombre de la aplicación web que desea configurar.  A continuación, haga clic en la parte denominada **DIAGNOSTIC LOGS** y cambie **Application Logging (Filesystem)** a la configuración **ON**.  Aparecerá la opción **Level**, que le permitirá cambiar el nivel de gravedad de los seguimientos que desea capturar.  Debe configurar esto en **Detallado** si solo está intentando familiarizarse con la característica, ya que esto asegurará que se registran las instrucciones de seguimiento.

Haga clic en **SAVE** en la parte superior del cuadro y estará listo para ver los registros.

Para ver los registros de streaming desde dentro del portal, haga clic en la parte **STREAMING LOGS** de la hoja del sitio web.  Si la aplicación escribe instrucciones de seguimiento activamente, debe verlas en la ventana resultante prácticamente en tiempo real.

![][StreamingLogsScreenshot]

## Consola ##

El Portal de Azure proporciona acceso a la consola para el entorno de la aplicación web. Puede explorar el sistema de archivos de la aplicación web y ejecutar los scripts de cmd/powershell.  Se realizará la vinculación mediante los mismos permisos establecidos en el código de la aplicación web en ejecución cuando se ejecuten los comandos de consola. No podrá obtener acceso a los directorios protegidos o ejecutar scripts que requieran permisos elevados.  

Para ir a la consola, desplácese a una aplicación web, como se describe en la sección anterior.  Haga clic en la parte **Consola** y se abrirá la consola.

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

<!--HONumber=52--> 