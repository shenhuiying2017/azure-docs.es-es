---
title: "Generación de perfiles de aplicaciones web activas en Azure con Application Insights Profiler | Microsoft Docs"
description: "Identifique la ruta de acceso activa en el código del servidor web con un generador de perfiles de bajo impacto."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 02/08/2018
ms.author: mbullwin
ms.openlocfilehash: 80792a82adbb93e80c94b4829b704b70d2a8ed23
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/09/2018
---
# <a name="profile-live-azure-web-apps-with-application-insights"></a>Generación de perfiles de aplicaciones web de Azure activas con Application Insights

*Esta característica de Application Insights está disponible generalmente para Azure App Service y se encuentra en versión preliminar para los recursos de proceso de Azure.*

Averigüe cuánto tiempo se invierte en cada método de la aplicación web activa con [Application Insights](app-insights-overview.md). La herramienta de generación de perfiles de Application Insights muestra perfiles detallados de las solicitudes en vivo que atendió su aplicación, y destaca la *ruta de acceso activa* que se usa la mayor parte del tiempo. El perfil de las solicitudes con diferentes tiempos de respuesta se genera por muestreo. Se minimiza la sobrecarga de la aplicación mediante diversas técnicas.

Actualmente, el generador de perfiles puede usarse con aplicaciones web ASP.NET y ASP.NET Core que se ejecutan en Azure App Service. Es necesario disponer del nivel de servicio **Básico** o superior para poder utilizar el generador de perfiles.

## <a id="installation"></a> Habilitación del generador de perfiles para la aplicación web de App Services
Si ya tiene la aplicación publicada en App Services pero no ha hecho nada en el código fuente para utilizar Application Insights, vaya al panel de App Services en Azure Portal, vaya a **Supervisión | Application Insights** y siga las instrucciones que aparecen en el panel a fin de crear un nuevo recurso de Application Insights o seleccionar uno existente para supervisar la aplicación web.

![Habilitación de App Insights en el portal de App Services][appinsights-in-appservices]

Si tiene acceso al código fuente del proyecto, [instale Application Insights](app-insights-asp-net.md). Si ya está instalado, asegúrese de que tiene la última versión. Para comprobar la versión más reciente, en el Explorador de soluciones, haga clic con el botón derecho en el proyecto y, a continuación, seleccione **Administrar paquetes NuGet** > **Actualizaciones** > **Actualizar todo paquetes**. A continuación, implemente la aplicación.

Las aplicaciones de ASP.NET Core necesitan instalar el paquete NuGet Microsoft.ApplicationInsights.AspNetCore 2.1.0-beta6 o una versión posterior para funcionar con el generador de perfiles. Desde el 27 de junio de 2017, no se admiten versiones anteriores.

En [https://portal.azure.com](https://portal.azure.com), abra el recurso Application Insights para su aplicación web. Seleccione **Rendimiento** > **Habilitar Application Insights Profiler**.

![Selección del banner Habilitar Profiler][enable-profiler-banner]

Si lo desea, también puede seleccionar la opción de configuración **Profiler** para ver el estado y habilitar o deshabilitar el generador de perfiles.

![En Rendimiento, selección de la configuración de Profiler][performance-blade]

Las aplicaciones web que se configuran con Application Insights se muestran en el panel de configuración de **Profiler**. Si ha seguido los pasos descritos anteriormente, el agente del generador de perfiles ya debería estar instalado. Seleccione **Habilitar Profiler** en el panel de configuración de **Profiler**.

Siga las instrucciones para instalar al agente de Profiler si es necesario. Si no hay ninguna aplicación web configurada con Application Insights, seleccione **Agregar aplicaciones vinculadas**.

![Configuración de las opciones del panel][linked app services]

A diferencia de las aplicaciones web hospedadas mediante planes de App Service, las que se hospedan en recursos de proceso de Azure (por ejemplo, Azure Virtual Machines, conjuntos de escalado de máquinas virtuales, Azure Service Fabric o Azure Cloud Services) no se administran directamente mediante Azure. En este caso, no hay ninguna aplicación web a la que vincularlas. En lugar de vincularlas a una aplicación, seleccione el botón **Habilitar Profiler**.

### <a name="enable-the-profiler-for-azure-compute-resources-preview"></a>Habilitación de Profiler para recursos de Azure Compute (versión preliminar)

Obtenga información sobre una [versión preliminar de Profiler para los recursos de proceso de Azure](https://go.microsoft.com/fwlink/?linkid=848155).

## <a name="view-profiler-data"></a>Visualización de datos de Profiler

**Asegúrese de que la aplicación está recibiendo tráfico**. Si está realizando un experimento, puede generar solicitudes dirigidas a la aplicación web mediante [Pruebas de rendimiento de Application Insights](https://docs.microsoft.com/en-us/vsts/load-test/app-service-web-app-performance-test). Si acaba de habilitar Profiler, puede ejecutar una breve prueba de carga de unos 15 minutos, lo que debería generar seguimientos del generador de perfiles. Si tiene Profiler habilitado desde hace tiempo, tenga en cuenta que se ejecuta de forma aleatoria dos veces cada hora, con una duración de dos minutos cada vez. Se recomienda ejecutar primero la prueba de carga durante una hora para garantizar que se obtienen los seguimientos de ejemplo del generador de perfiles.

Cuando llegue tráfico a la aplicación, vaya a la hoja **Rendimiento** > **Take Actions** (Tomar acciones) para ver los seguimientos del generador de perfiles. Seleccione el botón **Seguimientos de Profiler**.

![Seguimientos de Profiler de vista previa del panel Rendimiento de Application Insights][performance-blade-v2-examples]

Seleccione una muestra para mostrar un desglose de nivel de código del tiempo dedicado a ejecutar la solicitud.

![Explorador de seguimiento de Application Insights][trace-explorer]

El explorador de seguimiento muestra la siguiente información:

* **Mostrar ruta de acceso activa** abre el nodo hoja más grande o al menos algo que se aproxime. En la mayoría de los casos, este nodo estará adyacente a un cuello de botella de rendimiento.
* **Etiqueta**: nombre de la función o el evento. El árbol muestra una combinación de código y eventos que se han producido (como, por ejemplo, eventos HTTP y SQL). El evento superior representa la duración total de la solicitud.
* **Transcurrido**: el intervalo de tiempo entre el inicio y el final de la operación.
* **Cuándo**: muestra el momento en que se ejecutó la función o el evento con relación a otras funciones.

## <a name="how-to-read-performance-data"></a>Cómo leer datos de rendimiento

Profiler de servicio de Microsoft usa una combinación de método de muestreo e instrumentación para analizar el rendimiento de la aplicación. Cuando la colección detallada está en curso, el generador de perfiles del servicio muestrea cada milisegundo el puntero de instrucción de las CPU de todas las máquinas. Cada una de las muestras captura toda la pila de llamadas del subproceso que se está ejecutando actualmente. Esto proporciona información detallada sobre lo que dicho subproceso estaba haciendo, con un grado de abstracción tanto de alto como de bajo nivel. Profiler de servicio también recopila otros eventos para realizar el seguimiento de la causalidad y la correlación de actividades, como eventos de cambio de contexto, eventos de biblioteca TPL y eventos de grupo de subprocesos.

La pila de llamadas que se muestra en la vista de escala de tiempo es el resultado del muestreo y la instrumentación. Como cada muestra captura la pila de llamadas completa del subproceso, estas muestras contienen código de Microsoft .NET Framework y de otros marcos a los que se haga referencia.

### <a id="jitnewobj"></a>Asignación de objetos (clr!JIT\_New o clr!JIT\_Newarr1)
**clr!JIT\_New** y **clr!JIT\_Newarr1** son funciones auxiliares de .NET Framework que asignan memoria desde un salto administrado. **clr!JIT\_New** se invoca cuando se asigna un objeto. **clr!JIT\_Newarr1** se invoca cuando se asigna una matriz de objetos. Estas dos funciones suelen ser rápidas y tardan relativamente poco tiempo. Si observa que **clr!JIT\_New** o **clr!JIT\_Newarr1** tardan más de lo normal, significa que el código podría estar asignando muchos objetos y consumiendo una importante cantidad de memoria.

### <a id="theprestub"></a>Código de carga (clr!ThePreStub)
**clr!ThePreStub** es una función auxiliar de .NET Framework que prepara el código para ejecutarse por primera vez. Suele incluir, pero sin limitarse a ello, la compilación JIT (Just-In-Time). Para cada método de C#, se debe invocar **clr!ThePreStub** al menos una vez mientras dura un proceso.

Si **clr!ThePreStub** se lleva una cantidad considerable de tiempo con una solicitud, significa que la solicitud es la primera que ejecuta ese método. El entorno de ejecución de .NET Framework tarda un tiempo considerable en cargar el primer método. Podría plantearse la posibilidad de usar un proceso de preparación que ejecute esa parte del código antes de que los usuarios accedan a él, o bien ejecutar el Generador de imágenes nativo (ngen.exe) en sus ensamblados.

### <a id="lockcontention"></a>Contención de bloqueo (clr!JITutil\_MonContention o clr!JITutil\_MonEnterWorker)
**clr!JITutil\_MonContention** o **clr!JITutil\_MonEnterWorker** indican que el subproceso actual está a la espera de que se libere un bloqueo. Normalmente se muestra al ejecutar una instrucción **LOCK** de C#, al invocar el método **Monitor.Enter** o al invocar un método con el atributo **MethodImplOptions.Synchronized**. La contención de bloqueo se produce normalmente cuando un subproceso _A_ adquiere un bloqueo y un subproceso _B_ intenta adquirir el mismo bloqueo antes de que el subproceso _A_ lo libere.

### <a id="ngencold"></a>Código de carga ([COLD])
Si el nombre del método contiene **[COLD]**, por ejemplo, **mscorlib.ni![COLD]System.Reflection.CustomAttribute.IsDefined**, significa que el entorno de tiempo de ejecución de .NET Framework es la primera vez que ejecuta código que no está optimizado mediante <a href="https://msdn.microsoft.com/library/e7k32f4k.aspx">profile-guided optimization</a>. Para cada método, debe presentarse como máximo una vez durante todo el proceso.

Si el código de carga tarda una cantidad de tiempo considerable con una solicitud, significa que la solicitud es la primera en ejecutar la parte no optimizada del método. Considere la posibilidad de usar un proceso de preparación que ejecute esa parte del código antes de que los usuarios accedan e él.

### <a id="httpclientsend"></a>Envío de una solicitud HTTP
Métodos como **HttpClient.Send** indican que el código está esperando a que finalice una solicitud HTTP.

### <a id="sqlcommand"></a>Operación de base de datos
Métodos como **SqlCommand.Execute** indican que el código está a la espera de que finalice una operación de base de datos.

### <a id="await"></a>Espera (AWAIT\_TIME)
**AWAIT\_TIME** indica que el código está a la espera de que finalice otra tarea. Esto sucede normalmente con la instrucción **AWAIT** de C#. Cuando el código efectúa una instrucción **AWAIT** de C#, el subproceso se desenreda y devuelve el control al grupo de subprocesos, y no hay ningún subproceso bloqueado a la espera de que finalice la **AWAIT**. Pero, lógicamente, el subproceso que realizó **AWAIT** está "bloqueado" a la espera de que finalice la operación. La instrucción **AWAIT\_TIME** indica el tiempo de bloqueo a la espera de que finalice la tarea.

### <a id="block"></a>Tiempo de bloqueo
**BLOCKED_TIME** indica que el código está a la espera de que otro recurso esté disponible. Por ejemplo, podría estar esperando un objeto de sincronización, a que un subproceso esté disponible o a que termine una solicitud.

### <a id="cpu"></a>Tiempo de CPU
La CPU está ocupada ejecutando las instrucciones.

### <a id="disk"></a>Tiempo de disco
La aplicación está ejecutando operaciones de disco.

### <a id="network"></a>Tiempo de red
La aplicación está ejecutando operaciones de red.

### <a id="when"></a>Columna Cuándo
La columna **Cuándo** es una visualización de cómo varían con el tiempo las muestras INCLUSIVAS recopiladas para un nodo. El intervalo total de la solicitud se divide en 32 depósitos de tiempo. Las muestras inclusivas para ese nodo se acumulan en esos 32 depósitos. Cada depósito se representa con una barra. El alto de la barra representa un valor escalado. En el caso de nodos marcados **CPU_TIME** o **BLOCKED_TIME**, o cuando existe una relación obvia de consumo de un recurso (CPU, disco, subproceso), la barra representa el consumo de uno de esos recursos durante el período de tiempo de ese depósito. Para estas métricas, puede obtener un valor superior al 100 % si consume varios recursos. Por ejemplo, si por término medio usa dos CPU a lo largo de un intervalo, consigue el 200 %.

## <a name="limitations"></a>Limitaciones

La retención de datos predeterminada es de cinco días. El número máximo de datos ingerido al día es de 10 GB.

No hay ningún cargo por el uso del servicio Profiler. Para poder usar el servicio Profiler, la aplicación web debe estar hospedada al menos en el nivel Básico de Azure App Service.

## <a name="overhead-and-sampling-algorithm"></a>Sobrecarga y algoritmo de muestreo

Profiler se ejecuta de manera aleatoria durante dos minutos cada hora en toda las máquinas virtuales que hospedan la aplicación donde el generador de perfiles está habilitado para capturar seguimientos. Cuando Profiler se ejecuta, supone entre un 5 y un 15 % de sobrecarga de CPU en el servidor.
Cuantos más servidores haya disponibles para hospedar la aplicación, menor será el impacto de Profiler en el rendimiento general de la aplicación. El motivo es que el algoritmo de muestreo da lugar a que Profiler se ejecute únicamente en un 5 % de los servidores en cualquier momento. Para compensar la sobrecarga del servidor ocasionada por la ejecución de Profiler, hay más servidores disponibles para atender las solicitudes web.

## <a name="disable-the-profiler"></a>Deshabilitar Profiler
Para detener o reiniciar Profiler para una instancia de App Services, en **Trabajos web**, vaya al recurso de App Service. Para eliminar Profiler, vaya a **Extensiones**.

![Deshabilitación de Profiler para los trabajos web][disable-profiler-webjob]

Se recomienda tener habilitado Profiler en todas las aplicaciones web para detectar cualquier problema de rendimiento lo antes posible.

Si usa WebDeploy para implementar cambios en la aplicación web, asegúrese de excluir la carpeta App_Data de la eliminación durante la implementación. De lo contrario, los archivos de la extensión de Profiler se eliminan la próxima vez que implemente la aplicación web en Azure.


## <a id="troubleshooting"></a>Solución de problemas

### <a name="too-many-active-profiling-sessions"></a>Hay demasiadas sesiones de generación de perfiles activas

Actualmente, puede habilitar Profiler en hasta cuatro aplicaciones web de Azure y ranuras de implementación que se ejecuten en el mismo plan de servicio. Si el trabajo web de Profiler notifica demasiadas sesiones activas de generación de perfiles, mueva algunas aplicaciones web a un plan de servicio diferente.

### <a name="how-do-i-determine-whether-application-insights-profiler-is-running"></a>¿Cómo se puede determinar si Application Insights Profiler se está ejecutando?

Profiler se ejecuta como un trabajo web continuo de la aplicación web. Puede abrir el recurso de aplicación web en [Azure Portal](https://portal.azure.com). En el panel **WebJobs**, compruebe el estado de **ApplicationInsightsProfiler**. Si no se está ejecutando, abra **Registros** para obtener más información.

### <a name="why-cant-i-find-any-stack-examples-even-though-the-profiler-is-running"></a>¿Por qué no encuentro ningún ejemplo de pila aunque Profiler se esté ejecutando?

Estas son algunas cosas que puede comprobar:

* Asegúrese de que el plan de App Service web es de nivel Básico o superior.
* Asegúrese de que la aplicación web tenga habilitado el SDK 2.2 Beta o superior de Application Insights.
* Asegúrese de que la aplicación web tenga el valor **APPINSIGHTS_INSTRUMENTATIONKEY** configurado con la misma clave de instrumentación que usa el SDK de Application Insights.
* Asegúrese de que la aplicación web se ejecuta en .NET Framework 4.6.
* Si la aplicación web es una aplicación ASP.NET Core, compruebe también las [dependencias requeridas](#aspnetcore).

Una vez iniciado Profiler, hay un breve proceso de preparación durante el cual recopila activamente varios seguimientos de rendimiento. Después, el generador de perfiles recopila los seguimientos de rendimiento durante dos minutos cada hora.

### <a name="i-was-using-azure-service-profiler-what-happened-to-it"></a>Yo usaba Azure Service Profiler. ¿Qué ha ocurrido?

Cuando se habilita Application Insights Profiler, se deshabilita el agente Azure Service Profiler.

### <a id="double-counting"></a>Doble recuento de subprocesos paralelos

En algunos casos, la métrica de tiempo total del visor de la pila es mayor que la duración de la solicitud.

Esto puede suceder cuando hay dos o más subprocesos que funcionan en paralelo asociados a una solicitud. En ese caso, el tiempo de subproceso total es superior al tiempo transcurrido. Es posible que uno de los subprocesos esté a la espera de que el otro finalice. El visor intenta detectar esta situación y omite la espera carente de interés, pero prefiere mostrar demasiado antes que omitir lo que podría ser información crítica.

Si ve subprocesos en paralelo en sus seguimientos, determine cuáles son los subprocesos que esperan para poder establecer la ruta crítica de la solicitud. En la mayoría de los casos, el subproceso que pasa rápidamente a un estado de espera tan solo espera a los demás subprocesos. Concéntrese en los demás subprocesos e ignore el tiempo de los subprocesos en espera.

### <a id="issue-loading-trace-in-viewer"></a>No hay datos de generación de perfiles

Estas son algunas cosas que puede comprobar:

* Si la antigüedad de los datos que intenta ver supera el par de semanas, procure limitar el filtro de tiempo y inténtelo de nuevo.
* Compruebe que no haya servidores proxy ni un firewall que bloqueen el acceso a https://gateway.azureserviceprofiler.net.
* Compruebe que la clave de instrumentación de Application Insights que usa en la aplicación sea igual a la del recurso de Application Insights que usó para habilitar la generación de perfiles. La clave se encuentra normalmente en ApplicationInsights.config, pero también podría estar ubicada en los archivos web.config o app.config.

### <a name="error-report-in-the-profiling-viewer"></a>Informe de errores del visor de generación de perfiles

Envíe una incidencia de soporte técnico desde el portal. Asegúrese de incluir el identificador de correlación del mensaje de error.

### <a name="deployment-error-directory-not-empty-dhomesitewwwrootappdatajobs"></a>Deployment error: Directory Not Empty (Error de implementación: el directorio no está vacío) "D:\\home\\site\\wwwroot\\App_Data\\jobs"

Si va a volver a implementar su aplicación web en un recurso de App Service con Profiler habilitado, puede que aparezca un mensaje similar al siguiente:

El directorio no está vacío "D:\\home\\site\\wwwroot\\App_Data\\jobs"

Este error se produce si Web Deploy se ejecuta desde scripts o desde la canalización de implementación de Visual Studio Team Services. La solución consiste en agregar los siguientes parámetros de implementación adicionales a la tarea de Web Deploy:

```
-skip:Directory='.*\\App_Data\\jobs\\continuous\\ApplicationInsightsProfiler.*' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs\\continuous$' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs$'  -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data$'
```

Estos parámetros eliminan la carpeta que usa Application Insights Profiler y desbloquea el proceso de reeimplementación. No afectan a la instancia de Profiler actualmente en ejecución.


## <a name="manual-installation"></a>Instalación manual

Cuando se configura Profiler, se realizan las siguientes actualizaciones en la configuración de la aplicación web. Estas actualizaciones se pueden aplicar manualmente si su entorno lo requiere. Por ejemplo, si la aplicación se ejecuta en una instancia de App Service Environment para PowerApps.

1. En el panel de control de la aplicación web, abra **Configuración**.
2. Establezca **Versión de .Net Framework** en **v4.6**.
3. Establezca **Siempre activado** en **Activado**.
4. Agregue el valor de configuración de la aplicación __APPINSIGHTS_INSTRUMENTATIONKEY__ y establezca el valor en la misma clave de instrumentación que usó el SDK.
5. Abra **Herramientas avanzadas**.
6. Seleccione **Ir** para abrir el sitio web de Kudu.
7. En el sitio web de Kudu, seleccione **Site extensions** (Extensiones de sitio).
8. Instale __Application Insights__ desde la galería de aplicaciones web de Azure.
9. Reinicie la aplicación web.

## <a id="profileondemand"></a> Desencadenar manualmente el generador de perfiles
Cuando se desarrolló el generador de perfiles, se agregó una interfaz de línea de comandos para que fuera posible probarlo en servicios de aplicaciones. Con esta misma interfaz, los usuarios también pueden personalizar cómo se inicia el generador de perfiles. A grandes rasgos, el generador de perfiles usa el sistema Kudu de App Service para administrar la generación de perfiles en segundo plano. Cuando se instala la extensión de Application Insights, se crea un trabajo web continuo que hospeda el generador de perfiles. Esta misma tecnología se usa para crear un nuevo trabajo web que se puede personalizar de acuerdo con sus necesidades.

En esta sección se explica cómo:

1. Crear un trabajo web que puede iniciar el generador de perfiles durante dos minutos con la acción de presionar un botón.
2. Crear un trabajo web que puede programar la ejecución del generador de perfiles.
3. Establecer los argumentos del generador de perfiles


### <a name="set-up"></a>Instalación
En primer lugar, es necesario familiarizarse con el panel del trabajo web. En Configuración, haga clic en la pestaña WebJobs.

![hoja de webjobs](./media/app-insights-profiler/webjobs-blade.png)

Como puede ver, este panel muestra todos los trabajos web que están instalados actualmente en su sitio. Puede ver que el trabajo web ApplicationInsightsProfiler2 tiene en ejecución el trabajo del generador de perfiles. Aquí es donde terminamos de crear nuestros nuevos trabajos web para la generación de perfiles manual y programada.

En primer lugar, hay que obtener los binarios necesarios.

1.  Vaya al sitio de Kudu. En la pestaña de herramientas de desarrollo, haga clic en la pestaña "Advanced Tools" (Herramientas avanzadas) que tiene el logotipo de Kudu. Haga clic en "Go" (Ir). Esta acción le llevará a un nuevo sitio e iniciará sesión automáticamente.
2.  A continuación, es necesario descargar los archivos binarios del generador de perfiles. Vaya al Explorador de archivos mediante Debug Console (Consola de depuración) -> CMD situado en la parte superior de la página.
3.  Haga clic en site -> wwwroot -> App_Data -> jobs -> continuous. Verá la carpeta "ApplicationInsightsProfiler2". Haga clic en el icono de descarga a la izquierda de la carpeta. Se descargará el archivo "ApplicationInsightsProfiler2.zip".
4.  Esta descarga contiene todos los archivos que necesita. Se recomienda crear un directorio limpio a donde mover este archivo comprimido antes de continuar.

### <a name="setting-up-the-web-job-archive"></a>Configuración del archivo de trabajo web
En esencia, cuando agrega un nuevo trabajo web al sitio web de Azure, lo que hace es crear un archivo ZIP que contiene un archivo run.cmd. El archivo run.cmd indica qué hacer al sistema de trabajo web cuando se ejecuta el trabajo web.

1.  Para empezar, cree una nueva carpeta llamada "RunProfiler2Minutes".
2.  Copie los archivos de la carpeta ApplicationInsightProfiler2 extraída en esta nueva carpeta.
3.  Cree un nuevo archivo run.cmd. (Para su comodidad, puede abrir esta carpeta de trabajo en VS Code antes de comenzar).
4.  Agregue el comando `ApplicationInsightsProfiler.exe start --engine-mode immediate --single --immediate-profiling-duration 120` y guarde el archivo.
a.  El comando `start` indica al generador de perfiles que se inicie.
b.  `--engine-mode immediate` indica al generador de perfiles la necesidad de comenzar inmediatamente a generar perfiles.
c.  `--single` significa ejecutar y detener automáticamente d.  `--immediate-profiling-duration 120` significa hacer que el generador de perfiles se ejecute durante 120 segundos o 2 minutos.
5.  Guarde este archivo.
6.  Archive esta carpeta; puede hacer clic con el botón derecho en la carpeta y elegir Enviar a -> Carpeta comprimida (en zip). Se crea un archivo .zip con el nombre de la carpeta.

![comando start profiler](./media/app-insights-profiler/start-profiler-command.png)

Ahora tenemos un .zip de trabajo web que se puede usar para configurar trabajos web en nuestro sitio.

### <a name="add-a-new-web-job"></a>Adición de un nuevo trabajo web
A continuación, vamos a agregar un nuevo trabajo web en el sitio. En este ejemplo, veremos cómo se agrega un trabajo web que se ha desencadenado manualmente. Luego, será capaz de realizar el proceso casi exactamente igual para el programado.

1.  Vaya al panel de trabajos web.
2.  Haga clic en el comando Agregar en la barra de herramientas.
3.  Asigne un nombre al trabajo web. Por motivos de claridad, puede resultarle útil utilizar el mismo nombre que el del archivo y abrirlo para tener diferentes versiones de run.cmd.
4.  En el archivo, cargue parte del formulario, haga clic en el icono Abrir archivo y busque el archivo .zip que creó anteriormente.
5.  Como tipo, elija Desencadenado.
6.  En Desencadenadores, elija Manual.
7.  Haga clic en Aceptar para guardar.

![comando start profiler](./media/app-insights-profiler/create-webjob.png)

### <a name="run-the-profiler"></a>Ejecución del generador de perfiles

Ahora que tenemos un nuevo trabajo web que se puede desencadenar manualmente, podemos intentar ejecutarlo.

1. Por naturaleza, solo se puede tener un proceso ApplicationInsightsProfiler.exe en ejecución en una máquina en un momento dado. Así que, para comenzar, no olvide deshabilitar el trabajo web Continuous desde este panel. Haga clic en la fila y presione "Detener". Seleccione Actualizar en la barra de herramientas y confirme que el estado indica que el trabajo se ha detenido.
2. Haga clic en la fila con el nuevo trabajo web que ha agregado y presione Ejecutar.
3. Con la fila aún seleccionada, haga clic en el comando Registros de la barra de herramientas; esta acción le llevará a un panel con el trabajo web que ha iniciado. En él se muestran las ejecuciones más recientes y sus resultados.
4. Haga clic en la instancia de ejecución que acaba de iniciar.
5. Si todo ha ido bien, debería ver algunos registros de diagnóstico procedentes del generador de perfiles que confirman que se ha iniciado la generación de perfiles.

### <a name="things-to-consider"></a>Aspectos que se deben tener en cuenta:

Aunque este método es relativamente sencillo, hay algunas cosas que deben tenerse en cuenta.

- Como nuestro servicio no administra este proceso, no hay manera de actualizar los archivos binarios del agente para el trabajo web. Actualmente, no disponemos de una página de descarga estable para los archivos binarios, por lo que la única forma de conseguir los más recientes es actualizar la extensión y obtenerla de la carpeta continuous, tal y como hicimos anteriormente.

- Como lo que se ha hecho es usar argumentos de la línea de comandos que originalmente se diseñaron pensando más en los desarrolladores que en los usuarios finales, estos argumentos pueden cambiar en el futuro, así que debe tenerlo en cuenta al actualizar. Esto no debería ser un problema, ya que puede agregar un trabajo web, ejecutarlo y probar que funciona. Finalmente, vamos a crear una interfaz de usuario para controlar todo esto sin necesidad de usar un proceso manual.

- La característica Web Jobs de App Services es única en el sentido de que, cuando se ejecuta el trabajo web, se garantiza que el proceso tiene las mismas variables de entorno y la configuración de aplicación que acabará teniendo el sitio web. Esto significa que no es necesario pasar la clave de instrumentación al generador de perfiles a través de la línea de comandos. Solamente habría que elegir la clave de instrumentación en el entorno. Sin embargo, si quiere ejecutar el generador de perfiles en el cuadro de desarrollo o en una máquina fuera de App Services, deberá suministrar una clave de instrumentación. Para ello, pase un argumento `--ikey <instrumentation-key>`. Este valor debe coincidir con la clave de instrumentación que esté usando la aplicación. En la salida del registro del generador de perfiles, se indicará con qué ikey se inició el generador de perfiles y si se detectó actividad en esa clave de instrumentación mientras se generaban los perfiles.

- Los trabajos web desencadenados manualmente se pueden desencadenar en realidad mediante Webhook. Para obtener esta dirección URL, en el panel, haga clic con el botón derecho en el trabajo web y consulte las propiedades. También puede elegir las propiedades en la barra de herramientas después de seleccionar el trabajo web en la tabla. Esto le brinda posibilidades ilimitadas, como desencadenar el generador de perfiles desde la canalización de CI/CD (como VSTS) o algo parecido a Microsoft Flow (https://flow.microsoft.com/en-us/). En última instancia, esto depende del nivel de complejidad que desee otorgar a run.cmd (que también puede ser run.ps1), pero la flexibilidad está ahí.

## <a name="next-steps"></a>pasos siguientes

* [Trabajo con Application Insights en Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[appinsights-in-appservices]:./media/app-insights-profiler/AppInsights-AppServices.png
[performance-blade]: ./media/app-insights-profiler/performance-blade.png
[performance-blade-examples]: ./media/app-insights-profiler/performance-blade-examples.png
[performance-blade-v2-examples]:./media/app-insights-profiler/performance-blade-v2-examples.png
[trace-explorer]: ./media/app-insights-profiler/trace-explorer.png
[trace-explorer-toolbar]: ./media/app-insights-profiler/trace-explorer-toolbar.png
[trace-explorer-hint-tip]: ./media/app-insights-profiler/trace-explorer-hint-tip.png
[trace-explorer-hot-path]: ./media/app-insights-profiler/trace-explorer-hot-path.png
[enable-profiler-banner]: ./media/app-insights-profiler/enable-profiler-banner.png
[disable-profiler-webjob]: ./media/app-insights-profiler/disable-profiler-webjob.png
[linked app services]: ./media/app-insights-profiler/linked-app-services.png
