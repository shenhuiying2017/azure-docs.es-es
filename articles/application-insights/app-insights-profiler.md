---
title: "Generación de perfiles de aplicaciones web activas en Azure con Application Insights | Microsoft Docs"
description: "Identifique la ruta de acceso activa en el código del servidor web con un generador de perfiles de bajo impacto."
services: application-insights
documentationcenter: 
author: alancameronwills
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/03/2017
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: ed685f0a4ed26fb8d1c766f87210a99d2b5270ac
ms.lasthandoff: 04/12/2017


---
# <a name="profiling-live-azure-web-apps-with-application-insights-preview"></a>Introducción a la supervisión de aplicaciones web de Azure con Application Insights (vista previa)

*Esta característica de Application Insights se encuentra en su versión de vista previa.*

Averigüe cuánto tiempo se dedica a cada método en la aplicación web activa con la herramienta de generación de perfiles de [Azure Application Insights](app-insights-overview.md). Esta herramienta le muestra perfiles detallados de solicitudes activas que han sido atendidas por su aplicación y resalta la "ruta de acceso activa" que se usa la mayor parte del tiempo. Selecciona automáticamente ejemplos con distintos tiempos de respuesta. El generador de perfiles usa diversas técnicas para minimizar la sobrecarga. 

El generador de perfiles actualmente funciona para las aplicaciones web ASP.NET con Azure App Services, en al menos el plan de tarifa Básico. (Si usa ASP.NET Core, la plataforma de destino debe ser `.NetCoreApp`).

<a id="installation"></a>
## <a name="enable-the-profiler"></a>Habilitar el generador de perfiles

Instale [Application Insights](app-insights-asp-net.md) en el código. Si ya está instalado, asegúrese de que tiene la última versión. (Para ello, haga clic con el botón derecho en el proyecto en el Explorador de soluciones y elija Administrar paquetes de NuGet. Seleccione Actualizaciones y actualice todos los paquetes). Vuelva a implementar la aplicación.

*¿Usa ASP.NET Core? [Haga clic aquí](#aspnetcore).*

En [https://portal.azure.com](https://portal.azure.com), abra el recurso Application Insights para su aplicación. Abra **Rendimiento** y haga clic en **Configurar**. Seleccione la aplicación y siga los pasos del asistente.

![En la hoja Rendimiento, haga clic en Configurar][performance-blade]

* *¿No hay un botón Configurar? Use la [procedimiento manual](#manual-installation).*

Si tiene que detener o reiniciar el generador de perfiles, lo encontrará **en el recurso App Service**, en **Trabajos web**. Para eliminarlo, mire en **Extensiones**.

Si usa WebDeploy para implementar cambios en la aplicación web, asegúrese de que la carpeta **App_Data** no se elimine durante la implementación. De lo contrario, los archivos de la extensión del generador de perfiles se eliminarán la próxima vez que implemente la aplicación web en Azure.

## <a name="viewing-profiler-data"></a>Visualización de datos del generador de perfiles

Abra la hoja Rendimiento y desplácese hasta la lista de operaciones.




![Columna de ejemplos de la hoja Rendimiento de Application Insights][performance-blade-examples]

Las columnas de la tabla son las siguientes:

* **Recuento**: número de estas solicitudes en el intervalo de tiempo de la hoja.
* **Valor medio**: tiempo típico que tarda la aplicación en responder a una solicitud. La mitas de todas las respuestas fueron más rápidas.
* **95.° percentil** el 95 % de las respuestas fueron más rápidas. Si esta cifra se aleja mucho del valor medio, puede que haya un problema intermitente con la aplicación. (También es posible que se explique por una característica de diseño, como el almacenamiento en caché).
* **Ejemplos**: icono que indica que el generador de perfiles ha capturado seguimientos de la pila para esta operación.

Haga clic en el icono de Ejemplos para abrir el explorador de seguimiento. El explorador muestra varios muestras capturadas por el generador de perfiles, clasificadas por tiempo de respuesta.

Seleccione una muestra para mostrar un desglose de nivel de código del tiempo dedicado a ejecutar la solicitud.

![Explorador de seguimiento de Application Insights][trace-explorer]

**Show hot path** (Mostrar ruta de acceso activa) abre el mayor nodo hoja o al menos algo que se aproxime. En la mayoría de los casos, este nodo estará adyacente a un cuello de botella de rendimiento.



* **Etiqueta**: nombre de la función o el evento. El árbol muestra una combinación de código y eventos que se han producido (como, por ejemplo eventos http y SQL). El evento superior representa la duración total de la solicitud.
* **Métrica**: tiempo transcurrido.
* **Cuándo**: muestra el momento en que se ejecutó la función o el evento con relación a otras funciones. 

## <a name="how-to-read-performance-data"></a>Cómo leer datos de rendimiento

El generador de perfiles de servicios Microsoft usa una combinación de método de muestreo e instrumentación para analizar el rendimiento de la aplicación. Cuando la colección detallada está en curso, el generador de perfiles del servicio muestrea el puntero de instrucción de las CPU de cada una de las máquinas en cada milisegundo. Cada muestra captura la pila de llamadas completa del subproceso en ejecución, lo que proporciona información detallada y útil sobre lo que subproceso realizaba en los niveles de abstracción tanto altos como bajos. El generador de perfiles del servicio también recopila otros eventos como, por ejemplo, eventos de cambio de contexto, eventos de y eventos de grupo de subprocesos para realizar el seguimiento de la causalidad y la correlación de actividades. 

La pila de llamadas que se muestra en la vista de escala de tiempo es el resultado del muestreo y la instrumentación anteriores. Dado que cada muestra captura toda la pila de llamadas del subproceso, incluye código de .NET Framework, así como de otras plataformas a las que se haga referencia.

### <a id="jitnewobj"></a>Asignación de objetos (`clr!JIT\_New or clr!JIT\_Newarr1`)
`clr!JIT\_New and clr!JIT\_Newarr1` son funciones auxiliares dentro de .NET Framework que asignan memoria del montón administrado. `clr!JIT\_New` se invoca cuando se asigna un objeto. `clr!JIT\_Newarr1` se invoca cuando se asigna una matriz de objetos. Estas dos funciones suelen ser muy rápidas y deben tardar una cantidad relativamente pequeña de tiempo. Si ve que `clr!JIT\_New` o `clr!JIT\_Newarr1` tardan una cantidad sustancial de tiempo en la escala de tiempo, es una indicación de que tal vez el código esté asignando muchos objetos y consuma una cantidad significativa de memoria. 

### <a id="theprestub"></a>Carga de código (`clr!ThePreStub`)
`clr!ThePreStub` es una función auxiliar dentro de .NET Framework que prepara el código para ejecutarlo por primera vez. Suele incluir, pero sin limitarse a ello, compilación JIT (Just-In-Time). Para cada método de C#, `clr!ThePreStub` debe invocarse como máximo una vez durante todo el proceso.

Si ve que `clr!ThePreStub` tarda una cantidad considerable de tiempo en una solicitud, indica que la solicitud es la primera que ejecuta ese método y el tiempo que tarda el motor en tiempo de ejecución de .NET Framework en cargar ese método es significativo. Puede plantearse un proceso de preparación que ejecute esa parte del código antes de que los usuarios accedan a él o contemplar la ejecución de NGen en los ensamblados. 

### <a id="lockcontention"></a>Contención de bloqueo (`clr!JITutil\_MonContention` o `clr!JITutil\_MonEnterWorker`)
`clr!JITutil\_MonContention` o `clr!JITutil\_MonEnterWorker` indican que el subproceso actual espera a que se libere un bloqueo. Suele presentarse cuando se ejecuta una instrucción de bloqueo de C#, se invoca el método Monitor.Enter o se invoca un método con el atributo MethodImplOptions.Synchronized. La contención de bloqueo se produce normalmente cuando un subproceso A adquiere un bloqueo y un subproceso B intenta adquirir el mismo bloqueo antes de que el subproceso A lo libere. 

### <a id="ngencold"></a>Carga de código (`[COLD]`)
Si el nombre del método contiene `[COLD]`, como `mscorlib.ni![COLD]System.Reflection.CustomAttribute.IsDefined`, significa que el tiempo de ejecución de .NET Framework ejecuta código que no se ha optimizado mediante <a href="https://msdn.microsoft.com/library/e7k32f4k.aspx">optimización guiada por perfiles</a> por primera vez. Para cada método, debe presentarse como máximo una vez durante todo el proceso. 

Si la carga de código tarda una cantidad de tiempo considerable en una solicitud, indica que la solicitud es la primera que ejecuta la parte no optimizada del método. Puede plantearse un proceso de preparación que ejecute esa parte del código antes de que los usuarios accedan e él. 

### <a id="httpclientsend"></a>Envío de solicitud HTTP
Métodos como `HttpClient.Send` indican que el código espera a que se complete una solicitud HTTP.

### <a id="sqlcommand"></a>Operación de base de datos
Un método como SqlCommand.Execute indica que el código espera a que se complete una operación de base de datos.

### <a id="await"></a>En espera (`AWAIT\_TIME`)
`AWAIT\_TIME` indica que el código espera a que se complete otra tarea. Suele producirse con la instrucción "await"de C#. Cuando el código efectúa una instrucción "await" de C#, el subproceso se desenreda y devuelve el control al grupo de subprocesos, y no hay ningún subproceso bloqueado en espera de que finalice la "await". Pero, lógicamente, el subproceso que realizó la "await" se "bloquea" en espera de que se complete la operación. `AWAIT\_TIME` indica el tiempo de bloqueo en espera a que se complete la tarea.

### <a id="block"></a>Tiempo de bloqueo
`BLOCKED_TIME` indica que el código espera a que esté disponible otro recurso, como cuando se espera por un objeto de sincronización, se espera a que esté disponible un subproceso o se espera a que finalice una solicitud. 

### <a id="cpu"></a>Tiempo de CPU
La CPU está ocupada ejecutando las instrucciones.

### <a id="disk"></a>Tiempo de disco
La aplicación está ejecutando operaciones de disco.

### <a id="network"></a>Tiempo de red
La aplicación está ejecutando operaciones de red.

### <a id="when"></a>Columna Cuándo
Se trata de una visualización de cómo varían con el tiempo las muestras inclusivas recopiladas para un nodo varían. El intervalo total de la solicitud se divide en 32 ciclos y las muestras inclusivas para ese nodo se acumulan en los 32 depósitos respectivos. Cada depósito se representa luego como una barra cuyo alto representa un valor escalado. En el caso de nodos marcados `CPU_TIME` o `BLOCKED_TIME`, o cuando existe una relación obvia de consumo de un recurso (cpu, disco, subproceso), la barra representa el consumo de uno de esos recursos durante el tiempo de ese ciclo. Con estas métricas puede conseguir más del 100 % si consume varios recursos. Por ejemplo, si por término medio usa dos CPU a lo largo de un intervalo, consigue el 200 %.


## <a id="troubleshooting"></a>Solución de problemas

### <a name="how-can-i-know-whether-application-insights-profiler-is-running"></a>¿Cómo se puede saber si Application Insights Profiler se está ejecutando?

El generador de perfiles se ejecuta como un trabajo web continuo de Aplicación web. Puede abrir el recurso Aplicación web en https://portal.azure.com y comprobar el estado de "ApplicationInsightsProfiler" en la hoja Trabajos Web. Si se está ejecutando, abra **Registros** para saber más. 

### <a name="why-cant-i-find-any-stack-examples-even-though-the-profiler-is-running"></a>¿Por qué no encuentro ningún ejemplo de pila aunque el generador de perfiles se esté ejecutando?

Aquí tiene algunas cosas que puede comprobar.

1. Asegúrese de que el plan de App Service web es de nivel Básico o superior.
2. Asegúrese de que la Aplicación web tiene habilitado el SDK 2.2 Beta y superior de Application Insights.
3. Asegúrese de que la Aplicación web tiene el ajuste de configuración APPINSIGHTS_INSTRUMENTATIONKEY con la misma clave de instrumentación que usa el SDK de Application Insights.
4. Asegúrese de que la Aplicación web se ejecuta en .Net Framework 4.6.
5. Si se trata de una aplicación ASP.NET Core, compruebe también las [dependencias requeridas](#aspnetcore).

Una vez iniciado el generador de perfiles, hay un breve proceso de preparación cuando el generador de perfiles recopila activamente varios seguimientos de rendimiento. Después, el generador de perfiles recopila seguimientos de rendimiento durante dos minutos cada hora.  

### <a name="i-was-using-azure-service-profiler-what-happened-to-it"></a>Yo usaba Azure Service Profiler. ¿Qué ha ocurrido?  

Cuando se habilita Application Insights Profiler, se deshabilita el agente Azure Service Profiler.

### <a id="double-counting"></a>Doble recuento de subprocesos paralelos

En algunos casos, la métrica de tiempo total del visor de la pila es mayor que la duración real de la solicitud. 

Esto puede suceder cuando hay dos o más subprocesos que funcionan en paralelo asociados a una solicitud. El tiempo total de los subprocesos es entonces mayor que el tiempo transcurrido. En muchos casos, es posible que uno de los subprocesos espere a que el otro se complete. El visor intenta detectar esto y omite la espera carente de interés, pero es preferible mostrar demasiado que omitir lo que pudiera ser información crítica.  

Cuando vea subprocesos en paralelo en su seguimientos, tendrá que determinar cuáles son los subprocesos que esperan para poder establecer la ruta crítica de la solicitud. En la mayoría de los casos, el subproceso que pasa rápidamente a un estado de espera tan solo espera a los demás subprocesos. Concéntrese en los demás e ignore el tiempo de los subprocesos en espera.

### <a id="issue-loading-trace-in-viewer"></a>No hay datos de generación de perfiles

1. Si la antigüedad de los datos que intenta ver supera el par de semanas, procure limitar el filtro de tiempo y inténtelo de nuevo.

2. Compruebe que no haya servidores proxy ni un firewall que bloqueen el acceso a https://gateway.azureserviceprofiler.net. 

3. Compruebe que la clave de instrumentación de Application Insights que usa en las aplicaciones sea la misma del recurso de Application Insights con el que ha habilitado la generación de perfiles. La clave suele estar en ApplicationInsights.config pero también puede encontrarse en web.config o app.config.

### <a name="error-report-in-the-profiling-viewer"></a>Informe de errores del visor de generación de perfiles

Abra una incidencia de soporte técnico desde el portal. Incluya el identificador de correlación del mensaje de errores.


## <a name="manual-installation"></a>Instalación manual

Cuando se configura el generador de perfiles, se realizan las siguientes actualizaciones a la configuración de la Aplicación web. Puede realizarías manualmente usted mismo:

1. En la hoja Control de la Aplicación web, abra Configuración.
2. Establezca "Versión de .Net Framework" en v4.6.
3. Establezca "Siempre activado" en activado.
4. Agregue el ajuste de configuración de la aplicación "__APPINSIGHTS_INSTRUMENTATIONKEY__" y establezca el valor en la misma clave de instrumentación que el SDK.
5. En **Extensiones**, agregue "Application Insights Profiler". Tardará dos o tres minutos en instalarse.

## <a id="aspnetcore"></a>Compatibilidad con ASP.NET Core

La aplicación ASP.NET Core es actualmente compatible con .NET Core Runtime.

La aplicación debe incluir también los componentes siguientes para habilitar la generación de perfiles.

1. [Application Insights para ASP.NET Core 2.0](https://github.com/Microsoft/ApplicationInsights-aspnetcore/releases/tag/v2.0.0)
2. [System.Diagnostics.DiagnosticSource 4.4.0-beta-25022-02](https://dotnet.myget.org/feed/dotnet-core/package/nuget/System.Diagnostics.DiagnosticSource/4.4.0-beta-25022-02)
    * En Visual Studio, seleccione Herramientas -> Administrador de paquetes de NuGet -> Configuración del Administrador de paquetes.
    * En el cuadro de diálogo Opciones, seleccione "Administrador de paquetes de NuGet -> Orígenes del paquete".
    * haga clic en el botón "+" para agregar un nuevo origen del paquete con el nombre "DotNet-Core-MyGet" y el valor"https://dotnet.myget.org/F/dotnet-core/api/v3/index.json".
    * Haga clic en el botón "Actualizar" y cierre el cuadro de diálogo Opciones.
    * Abra el Explorador de soluciones, haga clic con el botón derecho en el proyecto de ASP.NET Core y seleccione "Administrar paquetes NuGet".
    * Haga clic en la pestaña "Examinar", seleccione "Origen del paquete: DotNet-Core-MyGet" y active "Incluir versión previa".
    * Busque "System.Diagnostics.DiagnosticSource" y elija "__4.4.0-beta-25022-02__" para realizar la instalación.


## <a name="next-steps"></a>Pasos siguientes

* [Trabajo con Application Insights en Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[performance-blade]: ./media/app-insights-profiler/performance-blade.png
[performance-blade-examples]: ./media/app-insights-profiler/performance-blade-examples.png
[trace-explorer]: ./media/app-insights-profiler/trace-explorer.png
[trace-explorer-toolbar]: ./media/app-insights-profiler/trace-explorer-toolbar.png
[trace-explorer-hint-tip]: ./media/app-insights-profiler/trace-explorer-hint-tip.png
[trace-explorer-hot-path]: ./media/app-insights-profiler/trace-explorer-hot-path.png

