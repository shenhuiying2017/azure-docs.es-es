---
title: "Guía para la solución de problemas del depurador de instantáneas de Azure Application Insights | Microsoft Docs"
description: "Preguntas más frecuentes sobre el depurador de instantáneas de Azure Application Insights."
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 
ms.service: application-insights
ms.workload: 
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: mbullwin
ms.openlocfilehash: 5d6a2fe4c3ee373172f5324b6c7a39e4f94f4652
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/29/2017
---
# <a name="snapshot-debugger-troubleshooting-guide"></a>Depurador de instantáneas: guía para la solución de problemas

El depurador de instantáneas de Azure Application Insights le permite recopilar automáticamente una instantánea de depuración de aplicaciones web en vivo. La instantánea muestra el estado del código fuente y las variables en el momento en que se produjo una excepción. Si tiene dificultades para poner en marcha el depurador de instantáneas de Application Insights, este artículo le muestra cómo funciona el depurador y describe algunas soluciones para los escenarios de solución de problemas más habituales. 

## <a name="how-does-application-insights-snapshot-debugger-work"></a>Cómo funciona el depurador de instantáneas de Application Insights

El depurador de instantáneas de Azure Application Insights forma parte de la canalización de telemetría de Application Insights (una instancia de ITelemetryProcessor), el recopilador de instantáneas supervisa tanto las excepciones lanzadas en el código (AppDomain.FirstChanceException) como las excepciones de las que la canalización de telemetría de excepciones de Application Insights realiza un seguimiento. Una vez que haya agregado correctamente el recopilador de instantáneas a su proyecto y este haya detectado una excepción en la canalización de telemetría de Application Insights, se enviará un evento personalizado de Application Insights con el nombre "AppInsightsSnapshotCollectorLogs" y "SnapshotCollectorEnabled" en los datos personalizados. Al mismo tiempo, se iniciará un proceso con el nombre "MinidumpUploader.exe" para cargar los archivos de datos de instantáneas recopilados en Application Insights.  Cuando se inicie el proceso "MinidumpUploader.exe", se enviará un evento personalizado con el nombre "UploaderStart". Después de los pasos anteriores, el recopilador de instantáneas se establecerá en su comportamiento normal de supervisión.

Mientras el recolector de instantáneas supervisa la telemetría de excepciones de Application Insights, utiliza los parámetros (por ejemplo, ThresholdForSnapshotting, MaximumSnapshotsRequired, MaximumCollectionPlanSize, ProblemCounterResetInterval) definidos en la configuración para determinar cuándo se recopila una instantánea. Cuando se cumplen todas las reglas, el recopilar solicitará una instantánea para la siguiente excepción en el mismo lugar. Al mismo tiempo, se enviará un evento personalizado de Application Insights con el nombre "AppInsightsSnapshotCollectorLogs" y "RequestSnapshots". Puesto que el compilador optimizará el código de "Release", es posible que las variables locales no estén visibles en la instantánea recopilada. El recopilador de instantáneas tratará de desoptimizar el método que lanzó la excepción cuando solicita instantáneas. Durante este tiempo, se enviará un evento personalizado de Application Insights con el nombre "AppInsightsSnapshotCollectorLogs" y "ProductionBreakpointsDeOptimizeMethod" en los datos personalizados.  Cuando se recopile la instantánea de la excepción siguiente, las variables locales estarán disponibles. Una vez recopilada la instantánea, se volverá a optimizar el código para garantizar el rendimiento. 

> [!NOTE]
> La desoptimización requiere que la extensión de sitio de Application Insights esté instalada.

Cuando se solicita una instantánea para una excepción concreta, el recopilador de instantáneas empezará a supervisar la canalización que controla la excepción de la aplicación (AppDomain.FirstChanceException). Cuando la excepción vuelva a producirse, el recopilador iniciará una instantánea (un evento personalizado de Application Insights con el nombre "AppInsightsSnapshotCollectorLogs" y "SnapshotStart" en los datos personalizados). A continuación, se realiza una instantánea del proceso en ejecución (se duplicará la tabla de la página). Normalmente, este proceso tardará de 10 a 20 milisegundos. Después de ello, se enviará un evento personalizado de Application Insights con el nombre "AppInsightsSnapshotCollectorLogs" y "SnapshotStop" en los datos personalizados. Cuando se crea el proceso bifurcado, el total de memoria paginada aumentará en la misma cantidad que la memoria paginada de la aplicación en ejecución (el espacio de trabajo será mucho menor). Mientras el proceso de la aplicación se ejecuta con normalidad, la memoria del proceso en instantánea se volcará en el disco y se cargará en Application Insights. Una vez cargada la instantánea, se enviará un evento personalizado de Application Insights con el nombre "UploadSnapshotFinish".

## <a name="is-the-snapshot-collector-working-properly"></a>¿Funciona correctamente el recopilador de instantáneas?

### <a name="how-to-find-snapshot-collector-logs"></a>Búsqueda de registros del recopilador de instantáneas
Los registros del recopilador de instantáneas se envían a su cuenta de Application Insights si está instalada la versión 1.1.0 o posterior del [paquete de NuGet del recopilador de instantáneas](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector). Asegúrese de que *ProvideAnonymousTelemetry* no esté establecido en false (el valor es true de forma predeterminada).

* Vaya al recurso de Application Insights en Azure Portal.
* Haga clic en *Buscar* en la sección Información general.
* En el cuadro de búsqueda, escriba la siguiente cadena:
    ```
    AppInsightsSnapshotCollectorLogs OR AppInsightsSnapshotUploaderLogs OR UploadSnapshotFinish OR UploaderStart OR UploaderStop
    ```
* Nota: Cambie el *intervalo de tiempo* si fuera necesario.

![Captura de pantalla de la búsqueda de registros del recopilador de instantáneas](./media/app-insights-troubleshoot-snapshot-debugger/001.png)


### <a name="examine-snapshot-collector-logs"></a>Examen de registros del recopilador de instantáneas
Al buscar registros del recopilador de instantáneas, debe haber eventos "UploadSnapshotFinish" en el intervalo de tiempo objetivo. Si aún no ve el botón "Abrir instantánea de depuración" para abrir la instantánea, envíe un correo electrónico a snapshothelp@microsoft.com con la clave de instrumentación de Application Insights.

![Captura de pantalla del examen de registros del recopilador de instantáneas](./media/app-insights-troubleshoot-snapshot-debugger/005.png)

## <a name="i-cannot-find-a-snapshot-to-open"></a>No se encuentra ninguna instantánea para abrir
Si estos pasos no ayudan a resolver el problema, envíe un correo electrónico a snapshothelp@microsoft.com con la clave de instrumentación de Application Insights.

### <a name="step-1-make-sure-your-application-is-sending-telemetry-data-and-exception-data-to-application-insights"></a>Paso 1: Asegúrese de que la aplicación envía datos de excepción y datos de telemetría a Application Insights
Navegue al recurso de Application Insights y compruebe que se han enviado datos desde la aplicación.

### <a name="step-2-make-sure-snapshot-collector-is-added-correctly-to-your-applications-application-insights-telemetry-pipeline"></a>Paso 2: Asegúrese de que el recopilador de instantáneas se ha agregado correctamente a la canalización de Telemetría de Application Insights de la aplicación
Si puede encontrar registros en el paso "Búsqueda de registros del recopilador de instantáneas", el recopilador de instantáneas se agregó correctamente a su proyecto y puede omitir este paso.

Si no hay ningún registro del recopilador de instantáneas, compruebe lo siguiente:
* En el caso de las aplicaciones ASP.NET clásicas, busque esta línea *<Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">* en el archivo *ApplicationInsights.config*.

* En el caso de las aplicaciones ASP.NET Core, asegúrese de que *ITelemetryProcessorFactory* con *SnapshotCollectorTelemetryProcessor* se ha agregado a los servicios de *IServiceCollection*.

* Asegúrese también de que está usando la clave de instrumentación correcta en la aplicación publicada.

* El recopilador de instantáneas no admite varias claves de instrumentación dentro de una aplicación, enviará instantáneas a la clave de instrumentación de la primera excepción que observe.

* Si establece el valor de *InstrumentationKey* manualmente en el código, actualice el elemento *InstrumentationKey* desde *ApplicationInsights.config*.

### <a name="step-3-make-sure-the-minidump-uploader-is-started"></a>Paso 3: Asegúrese de que se inicia la el usuario de carga de minivolcado
En los registros del recopilador de instantáneas, busque *UploaderStart* (escriba UploaderStart en el cuadro de texto de búsqueda). Debe haber un evento del momento en que el recopilador de instantáneas supervisó la primera excepción. Si este evento no existe, compruebe los otros registros para obtener más información. Un posible método para resolver este problema consiste en reiniciar la aplicación.

### <a name="step-4-make-sure-snapshot-collector-expressed-its-intent-to-collect-snapshots"></a>Paso 4: Asegúrese de que recopilador de instantáneas expresó su intención de recopilar las instantáneas
En los registros del recopilador de instantáneas, busque *RequestSnapshots* (escriba ```RequestSnapshots``` en el cuadro de texto de búsqueda).  Si no hay ninguno, compruebe la configuración, por ejemplo, *ThresholdForSnapshotting*, que indica el número de excepciones específicas que se pueden producir antes de que empiece a recopilar las instantáneas.

### <a name="step-5-make-sure-that-snapshot-is-not-disabled-due-to-memory-protection"></a>Paso 5: Asegúrese de que la instantánea no está deshabilitada debido a la protección de la memoria
Para proteger el rendimiento de su aplicación, solo se capturará una instantánea cuando haya un buen búfer de memoria. En los registros del recopilador de instantáneas, busque "CannotSnapshotDueToMemoryUsage". En los datos personalizados del evento tendrá un motivo detallado. Si la aplicación se ejecuta en una instancia de Azure Web App, la restricción podría ser estricta, dado que Azure Web App reiniciará la aplicación cuando se cumplan ciertas reglas de memoria. Puede intentar escalar verticalmente el plan del servicio en equipos con más memoria para resolver este problema.

### <a name="step-6-make-sure-snapshots-were-captured"></a>Paso 6: Asegúrese de que se capturaron instantáneas
En los registros del recopilador de instantáneas, busque ```RequestSnapshots```.  Si no hay ninguno presente, compruebe la configuración; por ejemplo, ```ThresholdForSnapshotting``` indica el número de una determinada excepción antes de recopilar una instantánea.

### <a name="step-7-make-sure-snapshots-are-uploaded-correctly"></a>Paso 7: Asegúrese de que las instantáneas se cargan correctamente
En los registros del recopilador de instantáneas, busque ```UploadSnapshotFinish```.  Si no está presente, envíe un correo electrónico a snapshothelp@microsoft.com con la clave de instrumentación de Application Insights. Si este evento existe, abra uno de los registros y copie el valor de "SnapshotId" en los datos personalizados. A continuación, busque el valor. De este modo se buscará la excepción correspondiente a la instantánea. A continuación, haga clic en la excepción y abra la instantánea de depuración. (Si no hay ninguna excepción correspondiente, es posible que la telemetría de la excepción esté muestreada debido al alto volumen; pruebe con otro snapshotId).

![Captura de pantalla de la búsqueda de snapshotId](./media/app-insights-troubleshoot-snapshot-debugger/002.png)

![Captura de pantalla de la apertura de excepción](./media/app-insights-troubleshoot-snapshot-debugger/004b.png)

![Captura de pantalla de la apertura de la instantánea de depuración](./media/app-insights-troubleshoot-snapshot-debugger/003.png)

## <a name="snapshot-view-local-variables-are-not-complete"></a>Vista de instantánea: Las variables locales no están completas

Faltan algunas de las variables locales. Si la aplicación ejecuta código de liberación, el compilador optimizará algunas variables inmediatamente. Por ejemplo:

```csharp
    const int a = 1; // a will be discarded by compiler and the value 1 will be inline.
    Random rand = new Random();
    int b = rand.Next() % 300; // b will be discarded and the value will be directly put to the 'FindNthPrimeNumber' call stack.
    long primeNumber = FindNthPrimeNumber(b);
```

Si su caso es diferente, podría deberse a un error. Envíe un correo electrónico a snapshothelp@microsoft.com con la clave de instrumentación de Application Insights junto con el fragmento de código.

## <a name="snapshot-view-cannot-obtain-value-of-the-local-variable-or-argument"></a>Vista de instantánea: No se puede obtener el valor del argumento o la variable local
Asegúrese de que está instalada la [extensión del sitio de Application Insights](https://www.siteextensions.net/packages/Microsoft.ApplicationInsights.AzureWebSites/). Si el problema persiste, envíe un correo electrónico a snapshothelp@microsoft.com con la clave de instrumentación de Application Insights.
