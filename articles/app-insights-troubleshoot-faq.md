<properties title="Troubleshooting and Q & A about Application Insights" pageTitle="Troubleshooting and Q & A about Application Insights" description="Tips and troubleshooting" metaKeywords="analytics monitoring" authors="awills"  />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="awills" />

# Solución de problemas y P+F - Application Insights en Microsoft Azure en vista previa

-   [No veo ninguna opción para agregar Application Insights a mi proyecto en Visual Studio][No veo ninguna opción para agregar Application Insights a mi proyecto en Visual Studio]
-   [Mi nuevo proyecto web se ha creado, pero se produjo un error al agregar Application Insights.][Mi nuevo proyecto web se ha creado, pero se produjo un error al agregar Application Insights.]
-   [He agregado Application Insights correctamente y he ejecutado mi aplicación, pero no he visto ningún dato en el portal.][He agregado Application Insights correctamente y he ejecutado mi aplicación, pero no he visto ningún dato en el portal.]
-   [No veo ningún dato en el análisis de uso][No veo ningún dato en el análisis de uso]
-   [Estoy consultando el panel de inicio en vista previa de Microsoft Azure. ¿Cómo encuentro mis datos en Application Insights?][Estoy consultando el panel de inicio en vista previa de Microsoft Azure. ¿Cómo encuentro mis datos en Application Insights?]
-   [En la pantalla principal de Microsoft Azure en vista previa, ¿muestra ese mapa el estado de mi aplicación?][En la pantalla principal de Microsoft Azure en vista previa, ¿muestra ese mapa el estado de mi aplicación?]
-   [Cuando agrego Application Insights a mi aplicación y abro el portal de Application Insights, todo parece completamente diferente de las capturas de pantalla.][Cuando agrego Application Insights a mi aplicación y abro el portal de Application Insights, todo parece completamente diferente de las capturas de pantalla.]
-   [¿Puedo usar Application Insights para supervisar un servidor web de intranet?][¿Puedo usar Application Insights para supervisar un servidor web de intranet?]
-   [¿Cómo obtengo datos para Windows Phone o Windows Store?][¿Cómo obtengo datos para Windows Phone o Windows Store?]
-   [¿Cómo puedo ver los eventos y las vistas de página que he registrado en mi código?][¿Cómo puedo ver los eventos y las vistas de página que he registrado en mi código?]
-   [¿Por qué hay dos versiones de Application Insights?][¿Por qué hay dos versiones de Application Insights?]
-   [¿Qué es lo que falta en la versión de Azure de Application Insights?][¿Qué es lo que falta en la versión de Azure de Application Insights?]
-   [¿Cómo recupero todas las fantásticas características que tenía en la versión de Visual Studio Online de Application Insights?][¿Cómo recupero todas las fantásticas características que tenía en la versión de Visual Studio Online de Application Insights?]
-   [¿Qué modifica Application Insights en mi proyecto?][¿Qué modifica Application Insights en mi proyecto?]
-   [¿Cómo encuentro mis resultados en Application Insights?][¿Cómo encuentro mis resultados en Application Insights?]
-   [Pasos siguientes][Pasos siguientes]

## <a name="q01"></a>No veo ninguna opción para agregar Application Insights a mi proyecto en Visual Studio

-   Asegúrese de que tiene [Visual Studio Update 3][Visual Studio Update 3]. Viene preinstalada con Application Insights Tools, que debe poder ver en el Administrador de extensiones.
-   Application Insights en Microsoft Azure en vista previa está disponible solo para proyectos web de ASP.NET en C# o Visual Basic.
-   Si tiene un proyecto existente, vaya al Explorador de soluciones y asegúrese de que hace clic en el proyecto web (no en otro proyecto de la solución). Debería ver el elemento de menú 'Agregar Application Insights Telemetry al proyecto'.
-   Si está creando un nuevo proyecto, en Visual Studio, abra Archivo \> Nuevo proyecto y seleccione {Visual C#|Visual Basic} \> Web \> Aplicación web ASP.NET. Debería haber una opción para agregar Application Insights al proyecto.

## <a name="q02"></a>Mi nuevo proyecto web se ha creado, pero se produjo un error al agregar Application Insights.

Esto puede ocurrir si no se ha realizado la comunicación con el portal de Application Insights o si existe algún problema con la cuenta.

-   Compruebe que ha proporcionado las credenciales de inicio de sesión para la cuenta de Azure correcta. Las credenciales de Microsoft Azure, que ve en el cuadro de diálogo Nuevo proyecto, pueden ser diferentes de las credenciales de Visual Studio Online que ve en la parte superior derecha de Visual Studio.
-   Espere un momento y, a continuación, [agregue Application Insights al proyecto existente][agregue Application Insights al proyecto existente].
-   Vaya a la configuración de su cuenta de Microsoft Azure y compruebe si hay restricciones. Compruebe si puede agregar manualmente una aplicación de Application Insights.

## <a name="q03"></a>He agregado Application Insights correctamente y he ejecutado mi aplicación, pero no he visto ningún dato en el portal.

-   Tiene que cerrar y abrir los cuadros en donde esté esperando datos. En la versión actual, el contenido de un cuadro no se actualiza automáticamente.
-   En el panel de inicio de Microsoft Azure, observe el mapa de estado del servicio. Si hay algunas indicaciones de alerta, espere hasta que hayan vuelto a su estado correcto y después cierre y vuelva a abrir el cuadro de la aplicación de Application Insights.
-   En el firewall del servidor web, puede que tenga que abrir el puerto 443 para el tráfico saliente.

## <a name="q04"></a>No veo ningún dato en el análisis de uso

-   Los datos proceden de los scripts de las páginas web. Si agrega Application Insights a un proyecto web existente, [tendrá que agregar los scripts manualmente][agregue Application Insights al proyecto existente].

## <a name="q05"></a>Estoy consultando el panel de inicio en vista previa de Microsoft Azure. ¿Cómo encuentro mis datos en Application Insights?

Puede:

-   Elegir Examinar, Application Insights, su nombre de proyecto. Si no tiene allí ningún proyecto, tiene que [agregar Application Insights a su proyecto web en Visual Studio][agregue Application Insights al proyecto existente].

-   En el Explorador de soluciones de Visual Studio, haga clic con el botón secundario en el proyecto web y elija Abrir portal de Application Insights.

## <a name="q06"></a>En la pantalla principal de Microsoft Azure en vista previa, ¿muestra ese mapa el estado de mi aplicación?

No. Muestra el estado del servicio Azure. Para ver los resultados de la prueba web, elija Examinar \> Application Insights \> (su aplicación) y, a continuación, consulte los resultados de la prueba web.

## <a name="q07"></a>Cuando agrego Application Insights a mi aplicación y abro el portal de Application Insights, todo parece completamente diferente de las capturas de pantalla.

Es posible que esté utilizando la versión anterior de Application Insights Tools, que se conecta a la versión de Visual Studio Online.

Las páginas de ayuda que está buscando se refieren a Application Insights para Microsoft Azure en vista previa, que ya está conectado en Visual Studio Update 3.

## <a name="q08"></a>¿Puedo usar Application Insights para supervisar un servidor web de intranet?

Sí, puede supervisar el estado y el uso si el servidor puede enviar datos a la red pública de internet.

Pero si desea ejecutar pruebas web para su servicio, debe estar accesible desde la red pública de internet.

## <a name="q10"></a>¿Cómo puedo ver los eventos y las vistas de página que he registrado en mi código?

Todavía no lo admitimos en la versión de Microsoft Azure. Próximamente se admitirá. De momento, puede probar la [versión anterior][versión anterior].

## <a name="q11"></a>¿Por qué hay dos versiones de Application Insights?

El portal más antiguo forma parte de Visual Studio Online. No haremos más cambios sustanciales en esa versión. Si cuenta con la versión más antigua de las herramientas de Application Insights para Visual Studio, se conectan al portal de Visual Studio Online.

Visual Studio Update 3 incluye una versión preinstalada de las nuevas herramientas de Application Insights. Se conectan al nuevo portal de Application Insights, que es un componente de Microsoft Azure en vista previa. Actualmente estamos migrando Application Insights a este nuevo entorno. El trabajo todavía no está completo y hay muchas restricciones.

## <a name="q12"></a>¿Qué es lo que falta en la versión de Azure de Application Insights?

Estamos preparando excelentes novedades.

Pero de momento, las principales características que faltan son: soporte para aplicaciones de dispositivos como Windows Phone y Windows Store; e informes para la telemetría personalizada como `trackEvent()` y `trackPageView()`.

## <a name="q13"></a>¿Cómo recupero todas las características que tenía en la versión de Visual Studio Online de Application Insights?

1.  Vaya al Administrador de extensiones de Visual Studio.
2.  Desinstale Application Insights Tools.
3.  Ejecute [el instalador de la versión anterior de las herramientas][el instalador de la versión anterior de las herramientas] y lea su [guía de introducción][versión anterior].

## <a name="q14"></a>¿Qué modifica Application Insights en mi proyecto?

-   Agregue estos archivos al proyecto:

 + ApplicationInsights.config.
 + ai.js

-   Instale estos paquetes de NuGet:
	+ Application Insights API*: el API central
   * Application Insights API for Web Applications*: se utiliza para enviar la telemetría del servidor

 + *Application Insights API for JavaScript Applications*: se utiliza para enviar la telemetría del cliente

    El paquete incluye estos ensamblados:

 + Microsoft.ApplicationInsights

 + Microsoft.ApplicationInsights.Platform

-   (Solo nuevos proyectos - si [agrega Application Insights a un proyecto existente][agregue Application Insights al proyecto existente], tiene que hacerlo manualmente). Inserte fragmentos de código en el código de cliente y servidor para inicializarlos con el ID de recursos de Application Insights. Por ejemplo, en una aplicación MVC, el código se inserta en:

 + la página maestra Views/Shared/\_Layout.cshtml

 + Web.config

 + packages.config

## <a name="q15"></a>¿Cómo encuentro mis resultados en Application Insights?

1.  Abra Microsoft Azure:

 + En Visual Studio, haga clic con el botón secundario en el proyecto de la aplicación web y elija **Abrir portal de Azure en vista previa**.
 + O en un explorador web puede abrir la cuenta en Microsoft Azure en vista previa.

1.  Elija Examinar, Application Insights y, después, seleccione su proyecto.

## <a name="next"></a>Más información

-   [Application Insights][Application Insights]
-   [Incorporación de Application Insights al proyecto][agregue Application Insights al proyecto existente]
-   [Supervisión inmediata de un servidor web activo][Supervisión inmediata de un servidor web activo]
-   [Exploración de métricas en Application Insights][Exploración de métricas en Application Insights]
-   [Búsqueda del registro de diagnóstico][Búsqueda del registro de diagnóstico]
-   [Seguimiento de disponibilidad con pruebas web][Seguimiento de disponibilidad con pruebas web]
-   [Seguimiento de uso con eventos y métricas][Seguimiento de uso con eventos y métricas]
-   [Preguntas y repuestas y solución de problemas][Preguntas y repuestas y solución de problemas]

<!--Link references-->

  [No veo ninguna opción para agregar Application Insights a mi proyecto en Visual Studio]: #q01
  [Mi nuevo proyecto web se ha creado, pero se produjo un error al agregar Application Insights.]: #q02
  [He agregado Application Insights correctamente y he ejecutado mi aplicación, pero no he visto ningún dato en el portal.]: #q03
  [No veo ningún dato en el análisis de uso]: #q04
  [Estoy consultando el panel de inicio en vista previa de Microsoft Azure. ¿Cómo encuentro mis datos en Application Insights?]: #q05
  [En la pantalla principal de Microsoft Azure en vista previa, ¿muestra ese mapa el estado de mi aplicación?]: #q06
  [Cuando agrego Application Insights a mi aplicación y abro el portal de Application Insights, todo parece completamente diferente de las capturas de pantalla.]: #q07
  [¿Puedo usar Application Insights para supervisar un servidor web de intranet?]: #q08
  [¿Cómo obtengo datos para Windows Phone o Windows Store?]: #q09
  [¿Cómo puedo ver los eventos y las vistas de página que he registrado en mi código?]: #q10
  [¿Por qué hay dos versiones de Application Insights?]: #q11
  [¿Qué es lo que falta en la versión de Azure de Application Insights?]: #q12
  [¿Cómo recupero todas las fantásticas características que tenía en la versión de Visual Studio Online de Application Insights?]: #q13
  [¿Qué modifica Application Insights en mi proyecto?]: #q14
  [¿Cómo encuentro mis resultados en Application Insights?]: #q15
  [Pasos siguientes]: #next
  [Visual Studio Update 3]: http://go.microsoft.com/fwlink/?LinkId=397827
  [agregue Application Insights al proyecto existente]: ../app-insights-monitor-application-health-usage/
  [versión anterior]: http://www.visualstudio.com/get-started/get-usage-data-vs
  [el instalador de la versión anterior de las herramientas]: http://visualstudiogallery.msdn.microsoft.com/82367b81-3f97-4de1-bbf1-eaf52ddc635a
  [Application Insights]: ../app-insights-get-started/
  [Supervisión inmediata de un servidor web activo]: ../app-insights-monitor-performance-live-website-now/
  [Exploración de métricas en Application Insights]: ../app-insights-explore-metrics/
  [Búsqueda del registro de diagnóstico]: ../app-insights-search-diagnostic-logs/
  [Seguimiento de disponibilidad con pruebas web]: ../app-insights-monitor-web-app-availability/
  [Seguimiento de uso con eventos y métricas]: ../app-insights-track-usage-custom-events-metrics/
  [Preguntas y repuestas y solución de problemas]: ../app-insights-troubleshoot-faq/
