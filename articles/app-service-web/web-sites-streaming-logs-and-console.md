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
	ms.date="04/25/2015" 
	ms.author="adamab"/>

#Registros de transmisión y la consola

### Registros de transmisión ###

El [Portal de vista previa de Azure](http://go.microsoft.com/fwlink/?LinkId=529715) ofrece un visor de registros de streaming integrado que le permite ver los eventos de seguimiento desde Aplicaciones web del [Servicio de aplicaciones de Azure](http://go.microsoft.com/fwlink/?LinkId=529714) en tiempo real.

La configuración requiere algunos pasos sencillos:

- Escritura de seguimientos en el código.
- Habilitación de diagnóstico de aplicaciones desde el Portal de vista previa de Azure.
- Haga clic en los registros de streaming en el icono de la aplicación web

### Cómo escribir los seguimientos en el código ###

La escritura de seguimientos en el código es sencilla. En C# es tan fácil como escribir el siguiente código:

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

El diagnóstico se habilita según la aplicación web.

1. En el [Portal de vista previa de Azure](https://portal.azure.com), haga clic en **Examinar** > **Aplicaciones Web** para obtener la lista de todas las aplicaciones web.  

2. Haga clic en el nombre de la aplicación web que desea configurar.

3. Haga clic en **Toda la configuración** > **Registros de diagnóstico** y cambie **Registro de aplicaciones (Sistema de archivos)** a la configuración **ACTIVAR**.

4. Aparecerá la opción **Nivel**, que le permitirá cambiar el nivel de gravedad de los seguimientos que desea capturar. Debe configurar esto en **Detallado** si solo está intentando familiarizarse con la característica, ya que esto asegurará que se registran las instrucciones de seguimiento.

5. Haga clic en **Guardar** en la parte superior del cuadro y estará listo para ver los registros.

6. Para ver los registros de streaming desde dentro del portal, haga clic en el icono **Registros de streaming** de la hoja de la aplicación web. Si la aplicación escribe instrucciones de seguimiento activamente, debe verlas en la ventana resultante prácticamente en tiempo real.

![][StreamingLogsScreenshot]

## Acceso a la consola ##

El Portal de vista previa de Azure ofrece acceso a la consola de la aplicación web. Puede explorar el sistema de archivos de la aplicación web y ejecutar los scripts de cmd/powershell. Se realizará la vinculación mediante los mismos permisos establecidos en el código de la aplicación web en ejecución cuando se ejecuten los comandos de consola. No podrá obtener acceso a los directorios protegidos o ejecutar scripts que requieran permisos elevados.

1. Desplácese a la hoja de una aplicación web, como se describe en la sección anterior.

2. Haga clic en el icono **Consola** y se abrirá la consola.

![][ConsoleScreenshot]

Para familiarizarse con la consola, pruebe estos comandos básicos:

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- Images. -->
[BrowseSitesScreenshot]: ./media/web-sites-streaming-logs-and-console/browse-sites.png
[StreamingLogsScreenshot]: ./media/web-sites-streaming-logs-and-console/streaming-logs.png
[ConsoleScreenshot]: ./media/web-sites-streaming-logs-and-console/console.png
 

<!---HONumber=62-->