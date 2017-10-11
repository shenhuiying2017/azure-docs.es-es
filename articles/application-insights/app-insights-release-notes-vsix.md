---
title: "Notas de la versión de la extensión de Visual Studio para Developer Analytics"
description: "Las últimas novedades sobre las herramientas de Visual Studio para Developer Analytics."
services: application-insights
documentationcenter: 
author: acearun
manager: carmonm
ms.assetid: 2001db30-efc5-417a-a413-93c1b218975f
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: bwren
ms.openlocfilehash: 2e6987d30b56407559f54c02817ee94eff8df46b
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2017
---
# <a name="release-notes-for-developer-analytics-tools"></a>Notas de la versión para Developer Analytics Tools

## <a name="version-718-visual-studio-2015"></a>Versión 7.18 (Visual Studio 2015)

* Notificaciones del sistema rediseñadas.
* Filtros "No" en la vista Detalles para eventos en Búsqueda de Application Insights.
* Corrección de errores

## <a name="version-86-visual-studio-2017-rtw-and-rc4-and-version-717-visual-studio-2015"></a>Versión 8.6 (Visual Studio 2017 RTW y RC4) y versión 7.17 (Visual Studio 2015)

* Actualmente, las anotaciones donde se marca cuándo publica la aplicación de Visual Studio se incluyen en el Explorador de métricas de Azure Portal
* Los marcadores se agregan ahora a las barras de desplazamiento de los archivos de código, correspondientes a las advertencias de CodeLens rojas y amarillas de Application Insights
* Información de precios actualizada en la ventana Configuración
* Corrección de errores

[Consulte las notas detalladas aquí](https://www.visualstudio.com/news/releasenotes/vs2017-relnotes#devanalytics)

## <a name="version-716-visual-studio-2015"></a>Versión 7.16 (Visual Studio 2015)

* Corrección de errores

## <a name="version-85-visual-studio-2017-rc3-and-version-715-visual-studio-2015"></a>Versión 8.5 (Visual Studio 2017 RC3) y versión 7.15 (Visual Studio 2015)

* Ahora, CodeLens muestra datos tanto de depuración como de telemetría activa en proyectos que envían datos a un recurso de Application Insights
* La información de precios de Application Insights aparece actualmente en la ventana Configuración
* CodeLens para solicitudes y excepciones admite ahora proyectos de ASP.NET escritos en Visual Basic
* Actualmente, Búsqueda de Application Insights muestra números de eventos no muestreados para eventos que se han muestreado
* Corrección de errores

## <a name="version-714-visual-studio-2015"></a>Versión 7.14 (Visual Studio 2015)

* Compatibilidad de Búsqueda con los eventos de vista de página y la disponibilidad (prueba web)
* Compatibilidad de Tendencias con los eventos de vista de página y la disponibilidad (prueba web)
* Herramientas de diagnóstico y etiqueta de detalles del evento para el muestreo adaptable del SDK
* Corrección de errores

## <a name="version-712-visual-studio-2015"></a>Versión 7.12 (Visual Studio 2015)

* Nuevo formato de notificación de publicaciones
* Corrección de errores

## <a name="version-84-visual-studio-2017-rc2-and-version-711-visual-studio-2015"></a>Versión 8.4 (Visual Studio 2017 RC2) y versión 7.11 (Visual Studio 2015)

* CodeLens muestra solicitudes de sesiones de depuración local para proyectos con el SDK de Application Insights
* CodeLens puede dirigirle directamente a Application Analytics para ver el impacto en el usuario
* Inserción de JavaScript para recopilar vistas de página
* Corrección de errores

## <a name="version-710-visual-studio-2015"></a>Versión 7.10 (Visual Studio 2015)

* Nuevo diseño de la ventana Configuración de Application Insights
* Corrección de errores

## <a name="version-79-visual-studio-2015"></a>Versión 7.9 (Visual Studio 2015)

* CodeLens muestra las excepciones que se han producido durante las sesiones de depuración local para proyectos con el SDK de Application Insights
* Corrección de errores

## <a name="version-83-visual-studio-2017-rc-and-version-78-visual-studio-2015"></a>Versión 8.3 (Visual Studio 2017 RC) y versión 7.8 (Visual Studio 2015)

* Nueva experiencia para agregar Application Insights en la ventana Configuración
* Corrección de errores

## <a name="version-77-visual-studio-2015"></a>Versión 7.7 (Visual Studio 2015)

* Asignaciones más precisas de eventos de telemetría a métodos mediante un enrutamiento de ASP.NET personalizado
* Corrección de errores

## <a name="version-76-visual-studio-2015"></a>Versión 7.6 (Visual Studio 2015)

* Análisis de eventos implicados en una operación de la nueva pestaña Track Operation (Seguimiento de operaciones) en eventos de la herramienta Búsqueda
* Corrección de errores

## <a name="version-75-visual-studio-2015"></a>Versión 7.5 (Visual Studio 2015)

* Información de telemetría de producción para solicitudes en Herramientas de diagnóstico
* Creación de elementos de trabajo en Elementos relacionados, en la herramienta Búsqueda
* Corrección de errores

## <a name="version-74-visual-studio-2015"></a>Version 7.4 (Visual Studio 2015)

* Ahora se puede cambiar el tamaño del panel de filtro en Tendencias
* Corrección de errores

## <a name="version-73-visual-studio-2015"></a>Version 7.3 (Visual Studio 2015)

* Solicitudes en CodeLens
* Ventana Configuración
* SDK de HockeyApp actualizado a v4.2.2
* Corrección de errores

## <a name="version-72-visual-studio-2015"></a>Version 7.2 (Visual Studio 2015)

* Corrección de errores

## <a name="version-71-visual-studio-2015"></a>Version 7.1 (Visual Studio 2015)

* Indicador de Telemetry Readiness (Disponibilidad de telemetría) en Tendencias de Application Insights
* Corrección de errores

## <a name="version-70"></a>Versión 7.0
### <a name="azure-application-insights-trends"></a>Tendencias de Azure Application Insights
Azure Application Insights es una nueva herramienta de Visual Studio que puede usar para analizar el funcionamiento de una aplicación en un período de tiempo. Para empezar, en el botón de la barra de herramientas de **Application Insights** o en la ventana Búsqueda de Application Insights, elija **Explorar tendencias de telemetría**. O bien, en el menú **Ver**, haga clic en **Otras ventanas** y, después, en **Tendencias de Application Insights**. Seleccione una de las cinco consultas comunes para empezar. Puede analizar diferentes conjuntos de datos en función de los tipos de telemetría, los intervalos de tiempo y otras propiedades. Para detectar anomalías en los datos, elija una de las opciones de anomalías en la lista desplegable **Tipo de vista** . Con las opciones de filtrado de la parte inferior de la ventana, resulta más sencillo centrarse en subconjuntos específicos de la telemetría.

![Tendencias de Application Insights](./media/app-insights-release-notes-vsix/Trends.png)

### <a name="exceptions-in-codelens"></a>Excepciones en CodeLens
La telemetría de excepciones se muestra ahora en CodeLens. Si ha conectado el proyecto al servicio de Application Insights, verá el número de excepciones que se han producido en cada método en producción en las últimas 24 horas. Desde CodeLens, puede ir a Búsqueda o Tendencias para investigar las excepciones con más detalle.

![Excepciones en CodeLens](./media/app-insights-release-notes-vsix/ExceptionsCodeLens.png)

### <a name="aspnet-core-support"></a>Compatibilidad con ASP.NET Core
Application Insights ahora admite proyectos ASP.NET Core RC2 en Visual Studio. Puede agregar Application Insights a los nuevos proyectos de ASP.NET Core RC2 desde el cuadro de diálogo **Nuevo proyecto** , como se muestra en la captura de pantalla siguiente. O bien, puede agregarlo a un proyecto existente; para ello, haga clic en el proyecto en el Explorador de soluciones y, a continuación, en **Agregar telemetría de Application Insights**.

![Compatibilidad con ASP.NET Core](./media/app-insights-release-notes-vsix/NetCoreSupport.png)

Los proyectos de ASP.NET 5 RC1 y ASP.NET Core RC2 ahora también se admiten en la ventana Herramientas de diagnóstico. Verá los eventos de Application Insights como solicitudes y excepciones desde su aplicación ASP.NET mientras se depuran localmente en su equipo. En cada evento, haga clic en **Buscar** para más información.

![Compatibilidad de Herramientas de diagnóstico](./media/app-insights-release-notes-vsix/DiagnosticTools.png)

### <a name="hockeyapp-for-universal-windows-apps"></a>HockeyApp para aplicaciones universales de Windows
Además de distribución beta y comentarios de los usuarios, HockeyApp proporciona informes de bloqueo simbólicos para las aplicaciones universales de Windows. Hemos logrado que sea incluso más fácil de agregar el SDK de HockeyApp: haga clic con el botón derecho en el proyecto Universal Windows y haga clic en **HockeyApp - Habilitar análisis de bloqueos**. Esto instala el SDK, configura colecciones de bloqueos y aprovisiona un recurso de HockeyApp en la nube, todo sin necesidad de cargar la aplicación en el servicio HockeyApp.

Otras características nuevas:

* Hemos conseguido que la experiencia de Búsqueda de Application Insights sea más rápida y más intuitiva. Ahora, los intervalos de tiempo y los filtros de detalles se aplican automáticamente al seleccionarlos.
* También en Búsqueda de Application Insights, ahora hay una opción para saltar al código directamente desde la telemetría de solicitudes.
* Hemos realizado mejoras en la experiencia de inicio de sesión de HockeyApp.
* En Herramientas de diagnóstico, se muestra información de telemetría de producción para las excepciones.

## <a name="version-52"></a>Versión 5.2
Nos complace anunciar la presentación de los escenarios de HockeyApp en Visual Studio. La primera integración está en la distribución beta de las aplicaciones universales de Windows y de Windows Forms dentro de Visual Studio.

Con la distribución beta, se cargan las primeras versiones de las aplicaciones en HockeyApp para su distribución en un subconjunto seleccionado de clientes o evaluadores. La distribución beta, combinada con las características de comentarios del usuario y colecciones de bloqueos de HockeyApp, puede proporcionar información importante acerca de su aplicación antes de su lanzamiento general. Puede utilizar esta información para solucionar problemas con la aplicación para evitar o minimizar problemas en el futuro, como bajas clasificaciones de la aplicación, comentarios negativos, etc.

Compruebe lo sencillo que es cargar compilaciones para la distribución beta en Visual Studio.

### <a name="universal-windows-apps"></a>Aplicaciones universales de Windows
El menú contextual de un nodo de proyecto para aplicaciones universales de Windows incluye ahora una opción para cargar la compilación en HockeyApp.

![Menú contextual del proyecto para las aplicaciones universales de Windows](./media/app-insights-release-notes-vsix/UniversalContextMenu.png)

Elija el elemento; se abre el cuadro de diálogo de carga de HockeyApp. Necesitará una cuenta de HockeyApp para cargar la compilación. Si es un usuario nuevo, no se preocupe. La creación de una cuenta es un proceso sencillo.

Una vez conectado, verá el formulario de carga en el cuadro de diálogo.

![Cuadro de diálogo de carga de las aplicaciones universales de Windows](./media/app-insights-release-notes-vsix/UniversalUploadDialog.png)

Seleccione el contenido que se cargará (un archivo .appxbundle o .appx) y elija las opciones de la versión en el asistente. Opcionalmente, puede agregar notas de la versión en la página siguiente. Haga clic en **Finalizar** para comenzar la carga.

Una vez finalizada la carga, aparecerá una notificación de HockeyApp con la confirmación y un vínculo a la aplicación en el portal de HockeyApp.

![Cargar notificación completa](./media/app-insights-release-notes-vsix/UploadComplete.png)

¡Ya está! Acaba de cargar una compilación para la distribución beta con tan solo unos clics.

Puede administrar la aplicación de muchas formas en el portal de HockeyApp. Esto incluye invitar a usuarios, ver comentarios e informes de bloqueos, cambiar detallan, etc.

![Portal de HockeyApp](./media/app-insights-release-notes-vsix/HockeyAppPortal.png)

Consulte la [base de conocimiento de HockeyApp](http://support.hockeyapp.net/kb/app-management-2) para más información acerca de la administración de la aplicación.

### <a name="windows-forms-apps"></a>Aplicaciones de Windows Forms
El menú contextual de un nodo de proyecto de Windows Forms incluye ahora una opción para cargar la compilación en HockeyApp.

![Menú contextual del proyecto para las aplicaciones de Windows Forms](./media/app-insights-release-notes-vsix/WinFormContextMenu.png)

Esto abre el cuadro de diálogo de carga de HockeyApp, que es similar a una aplicación universal de Windows.

![Cuadro de diálogo de carga de las aplicaciones de Windows Forms](./media/app-insights-release-notes-vsix/WinFormsUploadDialog.png)

Observe que hay un nuevo campo en este asistente para especificar la versión de la aplicación. Para aplicaciones universales de Windows, se rellena la información desde el manifiesto. Lamentablemente, las aplicaciones de Windows Forms no tienen ninguna característica equivalente. Debe especificarla manualmente.

El resto del flujo es similar al de las aplicaciones universales de Windows: elegir las opciones de la compilación y la versión, agregar notas de la versión, cargar y administrar en el portal de HockeyApp.

Es así de sencillo. Pruébelo y díganos qué le parece.

## <a name="version-43"></a>Versión 4.3
### <a name="search-telemetry-from-local-debug-sessions"></a>Búsqueda de telemetría desde sesiones de depuración local
Con esta versión, ahora puede buscar la telemetría de Application Insights generada en la sesión de depuración de Visual Studio. Antes, solo podía usar la búsqueda si había registrado su aplicación con Application Insights. Ahora, la aplicación solo necesita que el SDK de Application Insights esté instalado para buscar la telemetría local.

Si tiene una aplicación de ASP.NET con el SDK de Application Insights, siga estos pasos para usar la búsqueda.

1. Depure la aplicación.
2. Abra Búsqueda de Application Insights de una de estas formas:
   * En el menú **Ver**, haga clic en **Otras ventanas** y, después, en **Búsqueda de Application Insights**.
   * Haga clic en el botón de la barra de herramientas **Application Insights** .
   * En el Explorador de soluciones, expanda **ApplicationInsights.config** y haga clic en **Buscar telemetría de la sesión de depuración**.
3. Si aún no se ha registrado con Application Insights, se abrirá la ventana Búsqueda en el modo de telemetría de la sesión de depuración.
4. Haga clic en el icono **Buscar** para ver la telemetría local.

![Carga completa](./media/app-insights-release-notes-vsix/LocalSearch.png)

## <a name="version-42"></a>Versión 4.2
En esta versión hemos agregado características para facilitar la búsqueda de datos en el contexto de eventos, con la posibilidad de saltar al código desde más eventos de datos, y una experiencia fácil para enviar los datos de registro a Application Insights. Esta extensión se actualiza mensualmente. Si tiene comentarios o solicitudes de características, envíelos a aidevtools@microsoft.com.

### <a name="no-click-logging-experience"></a>Experiencia de registro sin clics
Si ya usa NLog, log4net o System.Diagnostics.Tracing, no tiene que preocuparse acerca de cómo mover todos los seguimientos a Application Insights. En esta versión, hemos integrado los adaptadores de registro de Application Insights con la experiencia de configuración normal.
Si ya tiene una de estas plataformas de registro configuradas, en la siguiente sección se describe cómo obtenerla.
**Si ya ha agregado Application Insights:**

1. Haga clic con el botón derecho en el nodo del proyecto, haga clic en **Application Insights** y, a continuación, en **Configurar Application Insights**. Asegúrese de que ve la opción para agregar el adaptador correcto en la ventana de configuración.
2. O bien, cuando compile la solución, observe la ventana emergente que aparece en la parte superior derecha de la pantalla y haga clic en **Configurar**.

![Notificación del registro](./media/app-insights-release-notes-vsix/LoggingToast.png)

Cuando tenga instalado el adaptador de registro, ejecute la aplicación y asegúrese de que ve los datos en la pestaña Herramientas de diagnóstico de la forma siguiente:

![Seguimientos](./media/app-insights-release-notes-vsix/Traces.png)

### <a name="jump-to-or-find-the-code-where-the-telemetry-event-property-is-emitted"></a>Salte al código o búsquelo donde se genera la propiedad de evento de telemetría.
Con la nueva versión, el usuario puede hacer clic en cualquier valor en el detalle de eventos para buscar una cadena coincidente en la solución abierta en ese momento. Se mostrarán resultados en la lista "Resultados de la búsqueda" de Visual Studio tal como se muestra a continuación:

![Buscar coincidencia](./media/app-insights-release-notes-vsix/FindMatch.png)

### <a name="new-search-window-for-when-you-are-not-signed-in"></a>Nueva ventana Búsqueda para cuando no ha iniciado sesión
Hemos mejorado la apariencia de la ventana Búsqueda de Application Insights para ayudarle a buscar los datos mientras la aplicación está en producción.

![Ventana de búsqueda](./media/app-insights-release-notes-vsix/SearchWindow.png)

### <a name="see-all-telemetry-events-associated-with-the-event"></a>Consulta de todos los eventos de telemetría asociados al evento
Hemos agregado una nueva pestaña con consultas predefinidas para todos los datos relacionados con el evento de telemetría que el usuario está viendo, junto a la pestaña de detalles del evento. Por ejemplo, una solicitud tiene un campo denominado **Id. de operación**. Todos los eventos asociados a esta solicitud tiene el mismo valor para **Id. de operación**. Si se produce una excepción mientras la operación está procesando la solicitud, la excepción tiene el mismo identificador de operación que la solicitud para que sea más fácil encontrarla. Si está viendo una solicitud, haga clic en **Toda la telemetría para esta operación** para abrir una nueva pestaña que muestra los nuevos resultados de la búsqueda.

![Elementos relacionados](./media/app-insights-release-notes-vsix/RelatedItems.png)

### <a name="forward-and-back-history-in-search"></a>Historial de Adelante y Atrás en Búsqueda
Ahora puede ir adelante y atrás en los resultados de búsqueda.

![Volver](./media/app-insights-release-notes-vsix/GoBAck.png)

## <a name="version-41"></a>Versión 4.1
Esta versión incluye una serie de nuevas características y actualizaciones. Debe tener instalada la actualización 1 para instalar esta versión.

### <a name="jump-from-an-exception-to-method-in-source-code"></a>Salto de una excepción al método en el código fuente
Ahora, si ve excepciones procedentes de su aplicación de producción en la ventana Búsqueda de Application Insights, puede saltar al método en el código donde se produce la excepción. Solo debe tener el proyecto correcto cargado y Application Insights se encarga del resto. (Para más información acerca de la ventana Búsqueda de Application Insights, consulte las notas de la versión 4.0 en las secciones siguientes).

¿Cómo funciona? Puede usar Búsqueda de Application Insights incluso cuando una solución no está abierta. El área de seguimiento de la pila muestra un mensaje de información, y muchos de los elementos en el seguimiento de la pila no están disponibles.

Si hay información sobre el archivo disponible, es posible que algunos elementos sean vínculos, pero el elemento de información de la solución seguirá estando visible.

Si hace clic en el hipervínculo, saltará a la ubicación del método seleccionado en el código. Puede haber una diferencia en el número de versión, pero la característica para saltar a la versión del código correcta, estará en las versiones posteriores.

![Clic en detalles de la excepción](./media/app-insights-release-notes-vsix/jumptocode.png)

### <a name="new-entry-points-to-the-search-experience-in-solution-explorer"></a>Nuevos puntos de entrada a la experiencia de búsqueda en el Explorador de soluciones
Ahora puede tener acceso a Búsqueda mediante el Explorador de soluciones.

![Buscar en el Explorador de soluciones](./media/app-insights-release-notes-vsix/searchentry.png)

### <a name="displays-a-notification-when-publish-is-completed"></a>Notificación cuando la publicación se ha completado
Aparecerá un cuadro de diálogo emergente cuando el proyecto se publique en línea, para que pueda ver los datos de Application Insights en producción.

![Publicar notificación completa](./media/app-insights-release-notes-vsix/publishtoast.png)

## <a name="version-40"></a>Versión 4.0
### <a name="search-application-insights-data-from-within-visual-studio"></a>Datos de Búsqueda de Application Insights en Visual Studio
Al igual que la función de búsqueda en el portal de Application Insights, ahora en Visual Studio puede filtrar y hacer búsquedas por tipos de eventos, valores de propiedades y texto, y después inspeccionar eventos individuales.

![Ventana de búsqueda](./media/app-insights-release-notes-vsix/search.png)

### <a name="see-data-coming-from-your-local-computer-in-diagnostic-tools"></a>Consulta de datos procedentes de un equipo local en Herramientas de diagnóstico
Puede ver la telemetría, además de otros datos de depuración, en la página de herramientas de diagnóstico de Visual Studio. Solo se admite ASP.NET 4.5.

![Página Herramientas de diagnóstico](./media/app-insights-release-notes-vsix/diagtools.png)

### <a name="add-the-sdk-to-your-project-without-signing-in-to-azure"></a>Incorporación del SDK al proyecto sin iniciar sesión en Azure
Ya no tiene que iniciar sesión en Azure para agregar paquetes de Application Insights al proyecto, ya sea en el cuadro de diálogo **Nuevo proyecto** o en el menú contextual del proyecto. Si inicia sesión, se instalará y configurará el SDK para enviar la telemetría al portal como antes. Si no inicia sesión, el SDK se agregará al proyecto y generará la telemetría para el concentrador de diagnósticos. Puede configurarlo más adelante si lo desea.

![Cuadro de diálogo Nuevo proyecto](./media/app-insights-release-notes-vsix/newproject.png)

### <a name="device-support"></a>Compatibilidad con dispositivos
En *Connect();* 2015 [anunciamos](https://azure.microsoft.com/blog/deep-diagnostics-for-web-apps-with-application-insights/) que nuestra experiencia de desarrolladores para móviles con dispositivos es HockeyApp. HockeyApp ayuda a distribuir compilaciones beta a los evaluadores, recopilar y analizar todos los bloqueos de la aplicación y obtener comentarios directamente de los clientes.
HockeyApp es compatible con su aplicación en cualquier plataforma en la que decida compilarla, ya sea iOS, Android, Windows o una solución multiplataforma como Xamarin, Cordova o Unity.

En versiones futuras de la extensión Application Insights, presentaremos una experiencia más integrada entre HockeyApp y Visual Studio. Por ahora, para empezar a trabajar con HockeyApp, basta con agregar la referencia de NuGet. Consulte la [documentación](http://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone) para más información.
