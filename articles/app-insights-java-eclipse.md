<properties 
	pageTitle="Introducción a Application Insights con Java en Eclipse" 
	description="Utilización del complemento de Eclipse para agregar supervisión del rendimiento y uso para su sitio web de Java con Application Insights" 
	services="application-insights" 
    documentationCenter="java"
	authors="alancameronwills" 
	manager="keboyd"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/03/2015" 
	ms.author="awills"/>
 
# Introducción a Application Insights con Java en Eclipse

El SDK de Application Insights envía telemetría desde la aplicación web de Java para que se pueda analizar el uso y el rendimiento. El complemento Eclipse de Application Insights instala automáticamente el SDK en el proyecto para que obtenga la telemetría de serie, además de una API que puede usar para escribir la telemetría personalizada.


## Requisitos previos

Actualmente el complemento funciona para proyectos web dinámicos en Eclipse. ([Agregue Application Insights a otros tipos de proyectos de Java][java]).

Necesitará:

* Oracle JRE 1.6 o posterior
* Una suscripción a [Microsoft Azure](http://azure.microsoft.com/). (Puede empezar con la [evaluación gratuita](http://azure.microsoft.com/pricing/free-trial/)).
* [Eclipse IDE para Java EE Developers](http://www.eclipse.org/downloads/), Indigo o superior.
* Windows 7 o posterior, o Windows Server 2008 o posterior

## Instalar el SDK en Eclipse (una vez)

Basta con hacerlo una vez por equipo. Este paso instala un kit de herramientas que puede agregar después el SDK a cada proyecto web dinámico.

1. En Eclipse, haga clic en Ayuda, Instalar nuevo software.

    ![Ayuda, Instalar nuevo software](./media/app-insights-java-eclipse/0-plugin.png)

2. El SDK se encuentra en http://dl.msopentech.com/eclipse, en el kit de herramientas de Azure.
3. Desactive **Ponerse en contacto con todos los sitios de actualización...**

    ![Para el SDK de Application Insights, desactive Ponerse en contacto con todos los sitios de actualización](./media/app-insights-java-eclipse/1-plugin.png)

Siga los pasos restantes para cada proyecto de Java.

## Obtención de una clave de instrumentación de Application Insights

El análisis de uso y de rendimiento se mostrará en un recurso de Azure en el portal web de Azure. En este paso, va a configurar un recurso de Azure para la aplicación.

1. Inicie sesión en el [Portal de Microsoft Azure](https://portal.azure.com). (Necesitará una [suscripción de Azure](http://azure.microsoft.com/)).
2. Creación de un recurso de Application Insights

    ![Haga clic en + y elija Application Insights](./media/app-insights-java-get-started/01-create.png)
3. Establezca el tipo de aplicación a una aplicación web de Java.

    ![Rellene un nombre, elija la aplicación web de Java y haga clic en Crear.](./media/app-insights-java-get-started/02-create.png)
4. Busque la clave de instrumentación del nuevo recurso. Tendrá que pegarla en el proyecto en Eclipse.

    ![En la información general de nuevos recursos, haga clic en Propiedades y copie la clave de instrumentación.](./media/app-insights-java-get-started/03-key.png)

## Incorporación del SDK al proyecto de Java

1. Agregue Application Insights en el menú contextual del proyecto web.

    ![En la información general de nuevos recursos, haga clic en Propiedades y copie la clave de instrumentación.](./media/app-insights-java-eclipse/4-addai.png)
2. Pegue la clave de instrumentación que obtuvo en el portal de Azure.

    ![En la información general de nuevos recursos, haga clic en Propiedades y copie la clave de instrumentación.](./media/app-insights-java-eclipse/5-config.png)


La clave se envía junto con todos los elementos de telemetría e indica a Application Insights que se muestre en el recurso.

## Ejecución de la aplicación y visualización de las métricas

Ejecute la aplicación.

Vuelva al recurso Application Insights en Microsoft Azure.

Los datos de las solicitudes HTTP aparecerán en la hoja de información general. (Si todavía no está ahí, espere unos segundos y, a continuación, haga clic en Actualizar).

![Respuesta del servidor, recuentos de solicitudes y errores](./media/app-insights-java-track-http-requests/5-results.png)
 

Haga clic en cualquier gráfico para ver métricas más detalladas.

![Recuentos de solicitudes por nombre](./media/app-insights-java-track-http-requests/6-barchart.png)


[Más información acerca de las métricas][metrics]

 

Y cuando vea las propiedades de una solicitud, podrá ver los eventos de telemetría asociados, como solicitudes y excepciones.
 
![Todos los seguimientos para esta solicitud](./media/app-insights-java-track-http-requests/7-instance.png)


## Telemetría de cliente

Desde el icono de inicio rápido, en la hoja de información general, puede obtener un script para agregar a sus páginas web.

Las métricas de vistas de páginas, usuarios y sesiones aparecerán en la hoja de información general:

![Sesiones, usuarios y vistas de página](./media/appinsights/appinsights-47usage-2.png)

[Más información acerca de la configuración de la telemetría de cliente.][usage]

## Pruebas web de disponibilidad

Application Insights puede probar su sitio web a intervalos regulares para comprobar que está activo y que responde correctamente. Haga clic en el gráfico de pruebas web vacío en la hoja de información general y especifique la dirección URL pública.

Obtendrá gráficos de tiempos de respuesta, junto con notificaciones por correo electrónico si su sitio deja de funcionar.

![Ejemplo de prueba web](./media/appinsights/appinsights-10webtestresult.png)

[Más información acerca de las pruebas web de disponibilidad][availability]

## Registros de diagnóstico

Si está usando Logback o Log4J (v1.2 o v2.0) para el seguimiento, los registros de seguimiento se pueden enviar automáticamente a Application Insights, donde puede explorarlos y buscar en ellos.

[Más información acerca de los registros de diagnóstico][javalogs]

## Telemetría personalizada 

Inserte unas pocas líneas de código en la aplicación web de Java para averiguar qué hacen los usuarios con él o para ayudar a diagnosticar problemas.

Puede insertar código en el JavaScript de la página web y en Java del servidor.

[Obtenga información acerca de la telemetría personalizad][track]



## Pasos siguientes

#### Detección y diagnóstico de problemas

* [Agregue telemetría de cliente web][usage] para obtener telemetría de rendimiento desde el cliente web.
* [Configure las pruebas web][availability] para comprobar que la aplicación efectivamente está activa y responde adecuadamente.
* [Busque eventos y registros][diagnostic] para ayudar a diagnosticar problemas.
* [Captura de los seguimientos Log4J o Logback][javalogs]

#### Seguir el uso

* [Agregue telemetría de cliente web][usage] para supervisar las vistas de páginas y las métricas básicas de usuario.
* [Realice el seguimiento de métricas y eventos personalizado][track] para obtener más información acerca de cómo se usa la aplicación tanto en el servidor como en el cliente.


<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[java]: app-insights-java-get-started.md
[javalogs]: app-insights-java-trace-logs.md
[metrics]: app-insights-metrics-explorer.md
[track]: app-insights-custom-events-metrics-api.md
[usage]: app-insights-web-track-usage.md


<!--HONumber=54-->