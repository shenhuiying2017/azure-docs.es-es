<properties 
	pageTitle="Solución de problemas y preguntas y respuestas acerca de Application Insights" 
	description="¿Hay algo que no entienda o que no funcione? Pruebe aquí." 
	services="application-insights" 
	authors="alancameronwills" 
	manager="kamrani"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="2015-02-03" 
	ms.author="awills"/>
 
# Solución de problemas y P+F - Application Insights en Microsoft Azure en vista previa

+ [No veo ninguna opción para agregar Application Insights a mi proyecto en Visual Studio](#q01)
+ [Mi nuevo proyecto web se ha creado, pero se produjo un error al agregar Application Insights.](#q02)
+ [He agregado Application Insights correctamente y he ejecutado mi aplicación, pero no he visto ningún dato en el portal.](#q03)
+ [No veo ningún dato en el análisis de uso](#q04)
+ [Estoy consultando el panel de inicio en vista previa de Microsoft Azure. ¿Cómo encuentro mis datos en Application Insights?](#q05)
+ [¿Cómo puedo cambiar el recurso de Azure donde aparecen mis datos?](#update)
+ [Aparece el mensaje de error "La clave de instrumentación no puede estar vacía"](#emptykey)
+ [En la pantalla principal de Microsoft Azure en vista previa, ¿muestra ese mapa el estado de mi aplicación?](#q06)
+ [Cuando agrego Application Insights a mi aplicación y abro el portal de Application Insights, todo parece completamente diferente de las capturas de pantalla.](#q07)
+ [¿Puedo usar Application Insights para supervisar un servidor web de intranet?](#q08)
+ [¿Cómo obtengo datos para Windows Phone o Windows Store?](#q09)
+ [¿Cómo puedo ver los eventos y las vistas de página que he registrado en mi código?](#q10)
+ [¿Por qué hay dos versiones de Application Insights?](#q11)
+ [¿Cómo recupero todas las características que tenía en la versión de Visual Studio Online de Application Insights?](#q13)
+ [¿Qué modifica Application Insights en mi proyecto?](#q14)
+ [¿Cómo encuentro mis resultados en Application Insights?](#q15)
+ [¿Qué puertos debo abrir en el firewall?](#q16)
+ [¿He habilitado todo en Application Insights?](#q17)
+ [Más información](#next)



## <a name="q01"></a>No veo ninguna opción para agregar Application Insights a mi proyecto en Visual Studio

+ Asegúrese de que tiene [Visual Studio Update 3](http://go.microsoft.com/fwlink/?LinkId=397827). Viene preinstalado con Application Insights Tools, que debe poder ver en el Administrador de extensiones.
+ Application Insights en Microsoft Azure en vista previa está disponible solo para proyectos web de ASP.NET en C# o Visual Basic.
+ Si tiene un proyecto existente, vaya al Explorador de soluciones y asegúrese de que hace clic en el proyecto web (no en otro proyecto de la solución). Debería ver el elemento de menú 'Agregar Application Insights Telemetry al proyecto'.
+ Si está creando un nuevo proyecto, en Visual Studio, abra Archivo > Nuevo proyecto y seleccione {Visual C#|Visual Basic} > Web > Aplicación web ASP.NET. Debería haber una opción para agregar Application Insights al proyecto.

## <a name="q02"></a>Mi nuevo proyecto web se ha creado, pero se produjo un error al agregar Application Insights.

Esto puede ocurrir si no se ha realizado la comunicación con el portal de Application Insights o si existe algún problema con la cuenta.

+ Compruebe que ha proporcionado las credenciales de inicio de sesión para la cuenta de Azure correcta. Las credenciales de Microsoft Azure, que ve en el cuadro de diálogo Nuevo proyecto, pueden ser diferentes de las credenciales de Visual Studio Online que ve en la parte superior derecha de Visual Studio.
+ Espere un momento y, a continuación, [agregue Application Insights al proyecto existente][start].
+ Vaya a la configuración de su cuenta de Microsoft Azure y compruebe si hay restricciones. Compruebe si puede agregar manualmente una aplicación de Application Insights.


## <a name="q03"></a>He agregado Application Insights correctamente y he ejecutado mi aplicación, pero no he visto ningún dato en el portal.

+ Tiene que cerrar y abrir los cuadros en donde esté esperando datos. En la versión actual, el contenido de un cuadro no se actualiza automáticamente.
+ En el panel de inicio de Microsoft Azure, observe el mapa de estado del servicio. Si hay algunas indicaciones de alerta, espere hasta que hayan vuelto a su estado correcto y después cierre y vuelva a abrir el cuadro de la aplicación de Application Insights.
+ En el firewall, tendrá que abrir los puertos TCP 80 y 443 para el tráfico saliente en dc.services.visualstudio.com y f5.services.visualstudio.com.

## <a name="q04"></a>No veo ningún dato en el análisis de uso

+ Los datos proceden de los scripts de las páginas web. Si agrega Application Insights a un proyecto web existente, [tendrá que agregar los scripts manualmente][start].


## <a name="q05"></a>Estoy consultando el panel de inicio en vista previa de Microsoft Azure. ¿Cómo encuentro mis datos en Application Insights?

Puede:

* Elegir Examinar, Application Insights, su nombre de proyecto. Si no tiene allí ningún proyecto, tiene que [agregar Application Insights a su proyecto web en Visual Studio][start].

* En el Explorador de soluciones de Visual Studio, haga clic con el botón secundario en el proyecto web y elija Abrir portal de Application Insights.

## <a name="update"></a>¿Cómo puedo cambiar mi recurso de Azure al que mi proyecto envía datos?

En el Explorador de soluciones, haga clic con el botón secundario en `ApplicationInsights.config` y elija **Actualizar Application Insights**. Puede enviar los datos a un recurso nuevo o existente en Azure. El Asistente para actualización cambia la clave de instrumentación en ApplicationInsights.config, que determina dónde el SDK del servidor envía los datos. A menos que desactive la opción "Actualizar todo", también cambiará la clave donde aparece en las páginas web.

## <a name="emptykey"></a>Aparece el mensaje de error "La clave de instrumentación no puede estar vacía"

Parece que algo salió mal durante la instalación de Application Insights o puede ser un adaptador del registro.

En el Explorador de soluciones, haga clic con el botón secundario en `ApplicationInsights.config` y elija **Actualizar Application Insights**. Aparecerá un cuadro de diálogo que le invita a iniciar sesión en Azure y a crear un recurso de Application Insights, o a volver a utilizar uno existente.

## <a name="q06"></a>En la pantalla principal de Microsoft Azure en vista previa, ¿muestra ese mapa el estado de mi aplicación?

¡No! Muestra el estado del servicio Azure. Para ver los resultados de la prueba web, elija Examinar > Application Insights > (su aplicación) y, a continuación, consulte los resultados de la prueba web. 


## <a name="q07"></a>Cuando agrego Application Insights a mi aplicación y abro el portal de Application Insights, todo parece completamente diferente de las capturas de pantalla.

Es posible que esté utilizando [la versión anterior de Application Insights Tools](http://msdn.microsoft.com/library/dn793604.aspx), que se conecta a la versión de Visual Studio Online.

Las páginas de ayuda que está buscando se refieren a [Application Insights para Microsoft Azure en vista previa][start], que ya está conectado en Visual Studio Update 3. 

## <a name="q08"></a>¿Puedo usar Application Insights para supervisar un servidor web de intranet?

Sí, puede supervisar el estado y el uso si el servidor puede enviar datos a la red pública de internet.

Pero si desea ejecutar pruebas web para su servicio, debe estar accesible desde la red pública de internet.

## <a name="q09"></a>¿Cómo obtengo datos para Windows Phone o Windows Store?

Aún no se admite en la versión de Microsoft Azure. Utilice la versión de [Visual Studio Online][older].


## <a name="q10"></a>¿Cómo puedo ver los eventos y las vistas de página que he registrado en mi código?

Todavía no lo admitimos en la versión de Microsoft Azure. Próximamente se admitirá. De momento, puede probar la [versión anterior][older].


## <a name="q11"></a>¿Por qué hay dos versiones de Application Insights?

El portal más antiguo forma parte de Visual Studio Online. No haremos más cambios sustanciales en esa versión. Si cuenta con la versión más antigua de las herramientas de Application Insights para Visual Studio, se conectan al portal de Visual Studio Online.

Visual Studio Update 3 incluye una versión preinstalada de las nuevas herramientas de Application Insights. Se conectan al nuevo portal de Application Insights, que es un componente de Microsoft Azure en vista previa. Actualmente estamos migrando Application Insights a este nuevo entorno. El trabajo aún no está completado.

## <a name="q13"></a>¿Cómo recupero todas las características que tenía en la versión de Visual Studio Online de Application Insights?

1. Vaya al Administrador de extensiones de Visual Studio. 
2. Desinstale Application Insights Tools.
3. Ejecute [el instalador de la versión anterior de las herramientas](http://visualstudiogallery.msdn.microsoft.com/82367b81-3f97-4de1-bbf1-eaf52ddc635a) y lea su [guía de introducción][older].

## <a name="q14"></a>¿Qué modifica Application Insights en mi proyecto?

Los detalles dependen del tipo de proyecto. Para una aplicación web:+


+ Agregue estos archivos al proyecto:

 + ApplicationInsights.config. 
 + ai.js


+ Instale estos paquetes de NuGet:

 -  *Application Insights API*: el API central

 -  *Application Insights API for Web Applications*: se utiliza para enviar la telemetría del servidor

 -  *Application Insights API for JavaScript Applications*: se utiliza para enviar la telemetría del cliente

    El paquete incluye estos ensamblados:

 - Microsoft.ApplicationInsights

 - Microsoft.ApplicationInsights.Platform

+ Inserta elementos en:

 - Web.config

 - packages.config

+ (Solo nuevos proyectos - si [agrega Application Insights a un proyecto existente][start], tiene que hacerlo manualmente). Inserte fragmentos de código en el código de cliente y servidor para inicializarlos con el ID de recursos de Application Insights. Por ejemplo, en una aplicación MVC, el código se inserta en la página maestra Views/Shared/_Layout.cshtml.


## <a name="q15"></a>¿Cómo encuentro mis resultados en Application Insights?
1. Abra Microsoft Azure:
 - En Visual Studio, haga clic con el botón secundario en el proyecto de la aplicación web y elija **Abrir portal de Azure en vista previa**.
 - O en un explorador web puede abrir la cuenta en Microsoft Azure en vista previa.

2. Elija Examinar, Application Insights y, después, seleccione su proyecto.

## <a name="q16"></a>Hay un firewall entre mi servidor o máquina de desarrollo y la red Internet pública. ¿Tráfico que debería permitir para habilitar Application Insights?

Los datos de uso y rendimiento se envían a los puertos TCP 80 y 443 en dc.services.visualstudio.com y f5.services.visualstudio.com.

Las pruebas de disponibilidad web dependen del acceso entrante al servidor web en el puerto 80.

## <a name="q17"></a> ¿He habilitado todo en Application Insights?

<table border="1">
<tr><th>Qué debe ver</th><th>Cómo obtenerlo</th><th>¿Por qué lo quiere?</th></tr>
<tr><td>Gráficos de disponibilidad</td><td><a href="../app-insights-monitor-web-app-availability/">Pruebas Web</a></td><td>Saber si la aplicación está funcionando</td></tr>
<tr><td>Rendimiento de la aplicación de servidor: tiempos de respuesta, etc.
</td><td><a href="../app-insights-start-monitoring-app-health-usage/">Agregar Application Insights al proyecto</a><br/>o <br/><a href="../app-insights-monitor-performance-live-website-now/">Instalar el monitor de estado de AI en el servidor</a></td><td>Detectar problemas de rendimiento</td></tr>
<!-- ####future#### <tr><td>Telemetría de dependencias</td><td><a href="../app-insights-monitor-performance-live-website-now/">Instalar el monitor de estado de AI en el servidor</a></td><td>Diagnosticar problemas con bases de datos u otros componentes externos</td></tr> -->
<!-- #####74.1#### <tr><td>Variables globales: CPU, memoria, etc.</td><td><a href="../app-insights-monitor-performance-live-website-now/">Instalar el monitor de estado de AI en el servidor</a></td><td>Diagnosticar problemas de capacidad</td></tr> --> 
<tr><td>Buscar seguimientos de registro</td><td><a href="../app-insights-search-diagnostic-logs/">Agregar un adaptador de registro</a></td><td>Diagnosticar excepciones y problemas de rendimiento</td></tr>
<tr><td>Conceptos básicos del uso de cliente: vistas de página, devoluciones, etc.</td><td><a href="../app-insights-start-monitoring-app-health-usage/#webclient">Inicializador de JavaScript en páginas web</a></td><td>Análisis de uso</td></tr>
<tr><td>Métricas personalizadas del cliente</td><td><a href="../app-insights-web-track-usage-custom-events-metrics/">Seguimiento de llamadas en páginas web</a></td><td>Mejorar la experiencia del usuario</td></tr>
<tr><td>Métricas personalizadas del servidor</td><td><a href="../app-insights-web-track-usage-custom-events-metrics/">Seguimiento de llamadas en el código de servidor</a></td><td>Inteligencia empresarial</td></tr>
</table>

Si el servicio web se ejecuta en una máquina virtual de Azure, también puede [obtener diagnósticos][azurediagnostic] aquí.



[AZURE.INCLUDE [app-insights-learn-more](../includes/app-insights-learn-more.md)]




[azurediagnostic]: ../insights-how-to-use-diagnostics/

[older]: http://www.visualstudio.com/get-started/get-usage-data-vs


<!--HONumber=46--> 
