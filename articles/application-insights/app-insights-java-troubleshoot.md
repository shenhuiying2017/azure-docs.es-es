<properties 
	pageTitle="Solución de problemas de Application Insights en un proyecto web de Java" 
	description="Guía de solución de problemas, preguntas y respuestas." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="keboyd"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="awills"/>
 
# Solución de problemas, preguntas y respuestas sobre Application Insights para Java

Si tiene dudas o problemas relacionados con [Visual Studio Application Insights en Java][java], a continuación se incluyen algunas sugerencias.


## Errores de compilación

*En Eclipse, al agregar el SDK de Application Insights a través de Maven o Gradle, obtengo errores de compilación o de validación de la suma de comprobación.*

* Si el elemento de dependencia <version> usa un patrón con caracteres comodín (por ejemplo, <version>[0.9,)</version>), pruebe a especificar una versión concreta, como <version>0.9.1</version>.

## No aparecen datos 

*He agregado Application Insights correctamente y he ejecutado mi aplicación, pero no aparecen datos en el portal.*

* Espere un minuto y haga clic en Actualizar, ya que la actualización no se realiza de forma automática.
* Compruebe que tiene una clave de instrumentación definida en el archivo ApplicationInsights.xml (en la carpeta de recursos del proyecto).
* Compruebe que no haya ningún nodo `<DisableTelemetry>true</DisableTelemetry>` en el archivo xml.
* En el firewall, tendrá que abrir los puertos TCP 80 y 443 para el tráfico saliente en dc.services.visualstudio.com y f5.services.visualstudio.com.
* En el panel de inicio de Microsoft Azure, observe el mapa de estado del servicio. Si hay algunas indicaciones de alerta, espere hasta que hayan vuelto a su estado correcto y después cierre y vuelva a abrir el cuadro de la aplicación de Application Insights.
* Active el inicio de sesión en la ventana de la consola del IDE. Para ello, agregue un elemento <SDKLogger /> bajo el nodo raíz en el archivo ApplicationInsights.xml (en la carpeta de recursos del proyecto) y compruebe si hay entradas precedidas por la indicación [Error].


## No aparecen datos de uso

*Veo datos sobre solicitudes y tiempos de respuesta, pero no de vista de página, del explorador o de datos de usuarios.*

La aplicación se ha configurado correctamente para enviar datos de telemetría desde el servidor. El paso siguiente consiste en [configurar las páginas web para enviar datos de telemetría desde el explorador web][usage].

Como alternativa, si el cliente es una aplicación de [teléfono o de cualquier otro dispositivo][platforms], puede enviar datos de telemetría desde este. 

Use la misma clave de instrumentación para configurar la telemetría tanto de cliente como de servidor. Los datos aparecerán en el mismo recurso de Application Insights y podrá correlacionar eventos de cliente y servidor.



## Deshabilitación de la telemetría

*¿Cómo puedo deshabilitar la recopilación de telemetría?*

En el código:

    TelemetryConfiguration config = TelemetryConfiguration.getActive();
    config.setTrackingIsDisabled(true);


**O** 

Actualice ApplicationInsights.xml (en la carpeta de recursos del proyecto). Agregue lo siguiente bajo el nodo raíz:

    <DisableTelemetry>true</DisableTelemetry>

Si usa el método XML, debe reiniciar la aplicación al cambiar el valor.

## Cambio de destino

*¿Cómo puedo cambiar el recurso de Azure al que mi proyecto envía datos?*

* [Obtenga la clave de instrumentación del nuevo recurso.][java]
* Si ha agregado Application Insights al proyecto mediante el kit de herramientas de Azure para Eclipse, haga clic con el botón derecho en el proyecto web, seleccione **Azure**, **Configure Application Insights** (Configurar Application Insights) y cambie la clave.
* De lo contrario, actualice la clave en ApplicationInsights.xml en la carpeta de recursos del proyecto.


## Pantalla de inicio de Azure

*Estoy mirando el [portal de Azure](http://portal.azure.com). ¿El mapa indica algo sobre mi aplicación?*

No, este muestra el estado de los servidores de Azure en todo el mundo.

*¿Cómo puedo encontrar datos sobre mi aplicación desde el panel de inicio de Azure (pantalla principal)?*

Suponiendo que haya [configurado la aplicación para Application Insights][java], haga clic en Examinar, seleccione Application Insights y, a continuación, elija el recurso de aplicación que haya creado para su aplicación. Para mayor brevedad en un futuro, puede anclar la aplicación al panel de inicio.

## Servidores de intranet

*¿Puedo supervisar un servidor de mi intranet?*

Sí, siempre que dicho servidor pueda enviar datos de telemetría al portal de Application Insights a través de una conexión pública a Internet. 

En el firewall, tendrá que abrir los puertos TCP 80 y 443 para el tráfico saliente en dc.services.visualstudio.com y f5.services.visualstudio.com.

## Retención de datos 

*¿Cuánto tiempo se retienen los datos en el portal? ¿Es seguro?*

Consulte [Privacidad y retención de los datos][data].

## Pasos siguientes

*He configurado Application Insights para mi aplicación de servidor Java. ¿Qué más puedo hacer?*

* [Supervisar la disponibilidad de sus páginas web][availability]
* [Supervisar el uso de páginas web][usage]
* [Hacer un seguimiento del uso y diagnosticar problemas de sus aplicaciones de dispositivo][platforms]
* [Escribir código para hacer un seguimiento del uso de la aplicación][track]
* [Capturar registros de diagnóstico][javalogs]


## Obtener ayuda

* [Stackoverflow](http://stackoverflow.com/questions/tagged/ms-application-insights)

[AZURE.INCLUDE [app-insights-learn-more](../../includes/app-insights-learn-more.md)]





<!--HONumber=49--> 