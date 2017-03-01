---
title: Consola y registros de streaming
description: "Información general de la consola y los registros de transmisión"
author: btardif
manager: erikre
editor: 
services: app-service\web
documentationcenter: 
ms.assetid: 3e50a287-781b-4c6a-8c53-eec261889d7a
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/12/2016
ms.author: byvinyal
translationtype: Human Translation
ms.sourcegitcommit: 0921b01bc930f633f39aba07b7899ad60bd6a234
ms.openlocfilehash: 120ce6b115820728b9f853e9ff349beb0ef29c9d
ms.lasthandoff: 03/01/2017


---
# <a name="streaming-logs-and-the-console"></a>Registros de transmisión y la consola
## <a name="streaming-logs"></a>Registros de transmisión
**Azure Portal** ofrece un visor de registros de streaming integrado que le permite ver los eventos de seguimiento desde las aplicaciones de **App Service** en tiempo real.  

Configurar esta característica requiere algunos pasos sencillos:

* Escritura de seguimientos en el código.
* Habilitación de **registros de diagnósticos** de aplicación para la aplicación
* Vea la transmisión desde la interfaz de usuario de **registros de streaming** integrados en **Azure Portal**.

### <a name="how-to-write-traces-in-your-code"></a>Cómo escribir los seguimientos en el código
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

### <a name="how-to-enable-and-view-the-streaming-logs"></a>Habilitación y visualización de registros de transmisión
![][BrowseSitesScreenshot] El diagnóstico se habilita por aplicación. Para comenzar, vaya al sitio en el que quiere habilitar esta característica.  

![][DiagnosticsLogs] En el menú de configuración, desplácese hacia abajo hasta la sección **Supervisión**y haga clic en **(1) Registros de diagnóstico**. Luego, **(2) habilite** **Registro de la aplicación (sistema de archivos)** o **Registro de la aplicación (blob)**. La opción **Nivel** le permite cambiar el nivel de gravedad del seguimiento que se captura. Si solo se está intentando familiarizar con la característica, establezca el nivel en **Detallado** para asegurarse de que se recopilarán todas las instrucciones de seguimiento.

Haga clic en **SAVE** en la parte superior del cuadro y estará listo para ver los registros.

> [!NOTE]
> Cuanto más alto sea el **nivel de gravedad**, más recursos del registro se consumen y más seguimientos se generarán. Asegúrese de que el **nivel de gravedad** está configurado en el nivel de detalle correcto para un sitio de producción o de alto tráfico. 
> 
> 

![][StreamingLogsScreenshot] Para ver los **registros de streaming** desde Azure Portal, haga clic en **(1) Secuencia de registro** también en la sección **Supervisión** del menú de configuración. Si la aplicación escribe instrucciones de seguimiento activamente, debe verlas en la **(2) interfaz de usuario de registros de streaming** prácticamente en tiempo real.

## <a name="console"></a>Consola
**Azure Portal** proporciona acceso a la consola para la aplicación. Puede explorar el sistema de archivos de la aplicación y ejecutar los scripts de cmd/powershell. Se realizará la vinculación mediante los mismos permisos establecidos en el código de la aplicación en ejecución cuando se ejecuten los comandos de consola. El acceso a directorios protegidos o a scripts de ejecución que requieren permisos elevados están bloqueado.  

![][ConsoleScreenshot] En el menú de configuración, desplácese hacia abajo hasta la sección **Herramientas de desarrollo** y haga clic en **(1) Consola**; luego, la interfaz de usuario de la **(2) consola** aparece a la derecha.

Para familiarizarse con la **consola**, pruebe estos comandos básicos:

`````````````````````````
dir
`````````````````````````

`````````````````````````
cd
`````````````````````````

<!-- Images. -->
[DiagnosticsLogs]: ./media/web-sites-streaming-logs-and-console/diagnostic-logs.png
[BrowseSitesScreenshot]: ./media/web-sites-streaming-logs-and-console/browse-sites.png
[StreamingLogsScreenshot]: ./media/web-sites-streaming-logs-and-console/streaming-logs.png
[ConsoleScreenshot]: ./media/web-sites-streaming-logs-and-console/console.png

