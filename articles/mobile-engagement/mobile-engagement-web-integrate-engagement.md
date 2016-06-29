<properties
	pageTitle="Integración de SDK web de Azure Mobile Engagement | Microsoft Azure"
	description="Actualizaciones y procedimientos más recientes para el SDK web para Azure Mobile Engagement"
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="erikre"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="web"
	ms.devlang="js"
	ms.topic="article"
	ms.date="02/29/2016"
	ms.author="piyushjo" />

#Cómo se integra Engagement en una aplicación web

> [AZURE.SELECTOR]
- [Windows Universal](mobile-engagement-windows-store-integrate-engagement.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
- [iOS](mobile-engagement-ios-integrate-engagement.md)
- [Android](mobile-engagement-android-integrate-engagement.md)

Este procedimiento describe la manera más sencilla de activar las funciones de supervisión y análisis de Engagement en su aplicación web.

Los siguientes pasos son suficientes para activar el informe de registros necesarios para calcular todas las estadísticas relativas a Usuarios, Sesiones, Actividades, Bloqueos y Aspectos técnicos. El informe de los registros necesarios para calcular otras estadísticas, como eventos, errores y trabajos debe realizarse manualmente mediante la API de Engagement (consulte [How to use the advanced Mobile Engagement tagging API in a Web application](mobile-engagement-web-use-engagement-api.md)[Cómo usar la API de etiquetado avanzado de Mobile Engagement en una aplicación web]), ya que estas estadísticas dependen de la aplicación.

## Introducción

Descargue el SDK web [aquí](http://aka.ms/P7b453). El SDK se distribuye como un único archivo de JavaScript denominado **azure-engagement.js** que se debe incluir en cada página de su sitio o aplicación web.

Este script **DEBE** cargarse **DESPUÉS** de un fragmento de código o script que se debe escribir para configurar Engagement para su aplicación.

## Compatibilidad con el explorador

El SDK web de Engagement usa codificación/descodificación JSON nativas y solicitudes entre dominios AJAX (de confianza en la especificación W3C CORS).

* Edge 12+
* IE 10+
* Firefox 3.5+
* Chrome 4+
* Safari 6+
* Opera 12+

## Configuración de Engagement

Escriba un script que cree un objeto de JavaScript **azureEngagement** global como el siguiente.
 
Como su sitio puede contener varias páginas, este ejemplo se supone que el script también se incluye en todas las páginas, lo denominaremos `azure-engagement-conf.js` en este procedimiento.

	window.azureEngagement = {
	  connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
	  appVersionName: '1.0.0',
	  appVersionCode: 1
	};

El script `connectionString` para la aplicación se muestra en el Portal de Azure.

> [AZURE.NOTE] `appVersionName` y `appVersionCode` son opcionales, se recomienda configurarlos para que en el análisis se procese la información de versión.

## Scripts de Engagement para las páginas

	<head>
	  ...
	  <script type="text/javascript" src="azure-engagement-conf.js"></script>
	  <script type="text/javascript" src="azure-engagement.js"></script>
	  ...
	</head>

O

	<body>
	  ...
	  <script type="text/javascript" src="azure-engagement-conf.js"></script>
	  <script type="text/javascript" src="azure-engagement.js"></script>
	  ...
	</body>

## Alias

Una vez cargado, el script del SDK crea el alias de **engagement** para obtener acceso a las API del SDK (no sirve para definir la configuración del SDK). Este alias se usará como referencia en esta documentación.

Tenga en cuenta que si el alias predeterminado está en conflicto con otra variable global de la página, puede volver a definirlo en la configuración antes de cargar el SDK, como sigue:

	window.azureEngagement = {
	  connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
	  appVersionName: '1.0.0',
	  appVersionCode: 1
	  alias:'anotherAlias'
	};

## Informes básicos

### Seguimiento de sesión

Las sesiones de Engagement se dividen en secuencias de actividades que se identifican por el nombre.

En un sitio web clásico, se recomienda indicar una actividad diferente en cada página del sitio. En un sitio web o aplicación web que nunca cambia la página actual puede realizar un seguimiento de las actividades de manera más precisa.

En cualquier caso, para iniciar o cambiar la actividad del usuario actual, llame a la función `engagement.agent.startActivity`, como en este ejemplo:

	<body onload="yourOnload()">

	<!-- -->

	yourOnload = function() {
      [...]
      engagement.agent.startActivity('welcome');
	};

El servidor de Engagement cierra las sesiones abiertas automáticamente en los 3 minutos posteriores al cierre de la página de la aplicación.

Como alternativa, puede cerrar la sesión manualmente mediante una llamada a `engagement.agent.endActivity`, que establecerá la actividad del usuario actual como "inactiva" y cerrará la sesión 10 segundos después, a menos que una nueva llamada a `engagement.agent.startActivity` reanude la sesión mientras tanto.
 
Este retraso de 10 segundos se puede configurar en el objeto de **engagement** global:

	engagement.sessionTimeout = 2000; // 2 seconds
	// or
	engagement.sessionTimeout = 0; // end the session as soon as endActivity is called

> [AZURE.NOTE] `engagement.agent.endActivity` no se puede usar en la devolución de llamada de `onunload`, ya que en este punto no se pueden realizar llamadas AJAX.

## Informes avanzados

De manera opcional, si quiere notificar `events`, `errors` y `jobs` específicos de la aplicación, deberá usar la API de Engagement a través del objeto `engagement.agent`.

La API de Engagement permite usar todas las funcionalidades avanzadas de Engagement y se detalla en [How to use the advanced Mobile Engagement tagging API in a Web application](mobile-engagement-web-use-engagement-api.md) (Cómo usar la API de etiquetado avanzado de Mobile Engagement en una aplicación web).

## Direcciones URL personalizadas para las llamadas AJAX

Puede personalizar las direcciones URL que usa el SDK. Por ejemplo, para volver a definir la dirección URL de registro (punto de conexión de SDK para el registro) se puede invalidar la configuración de la siguiente manera:

	window.azureEngagement = {
	  ...
	  urls: {
	    ...        
	    getLoggerUrl: function() {
	    return 'someProxy/log';
	    }
	  }
	};

Si las funciones de la dirección URL devuelven una cadena que comienza por `/`, `//`, `http://` o `https://`, no se usa el esquema predeterminado.

De forma predeterminada, para las direcciones URL se usa el esquema `https://`. Si quiere personalizar el esquema predeterminado, invalide la configuración de la manera siguiente:

	window.azureEngagement = {
	  ...
	  urls: {
        ...	     
	    scheme: '//'
	  }
	};

<!---HONumber=AcomDC_0615_2016-->