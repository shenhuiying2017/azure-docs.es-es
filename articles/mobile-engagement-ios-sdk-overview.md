<properties 
	pageTitle="Información general del SDK de iOS de Azure Mobile Engagement" 
	description="Actualizaciones y procedimientos más recientes para el SDK de iOS para Azure Mobile Engagement"
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="kpiteira" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="kapiteir" />

# 2.0.0

Comience aquí a obtener todos los detalles sobre cómo integrar Azure Mobile Engagement en una aplicación de iOS. Si primero quiere probarla, asegúrese de realizar nuestro [tutorial de 15 minutos](../mobile-engagement-ios-get-started/).

Haga clic para ver el [contenido del SDK](../mobile-engagement-ios-sdk-content)

## Procedimientos de integración
1. Comience aquí: [Cómo integrar Mobile Engagement en su aplicación de iOS](../mobile-engagement-ios-integrate-engagement/)

2. Para notificaciones: [Cómo integrar notificaciones de cobertura en la aplicación de iOS](../mobile-engagement-ios-integrate-engagement-reach/)

3. Implementación del plan de etiquetas: [Cómo usar la API de etiquetado de Mobile Engagement en la aplicación de iOS](../mobile-engagement-ios-use-engagement-api/)


## Notas de la versión

### 2.0.0 (17/02/2015)

-   Versión inicial de Azure Mobile Engagement
-   La configuración de appId o sdkKey se sustituye por una configuración de la cadena de conexión.
-   Se ha eliminado la API para enviar y recibir mensajes XMPP arbitrarios de entidades XMPP arbitrarias.
-   Se ha eliminado la API para enviar y recibir mensajes entre dispositivos.
-   Mejoras de seguridad.
-   Se ha eliminado el seguimiento de SmartAd.

Para la versión anterior, consulte las [notas de la versión completas](../mobile-engagement-ios-release-notes/)

## Procedimientos de actualización

Si ya integró una versión anterior de Engagement en la aplicación, debería tener en cuenta los siguientes puntos a la hora de actualizar el SDK.

Es posible que tenga que seguir varios procedimientos si se perdió varias versiones del SDK, consulte la sección completa [Procedimientos de actualización](../mobile-engagement-ios-upgrade-procedure/).

Para cada nueva versión del SDK debe reemplazar primero (quitar y volver a importar en xcode) las carpetas EngagementSDK y EngagementReach.

### De 1.16.0 a 2.0.0
A continuación se describe cómo migrar una integración del SDK desde el servicio Capptain que ofrece Capptain SAS en una aplicación con la tecnología de Azure Mobile Engagement. 

>[Azure.IMPORTANT] Capptain y Mobile Engagement no son los mismos servicios, y el procedimiento que se indica a continuación destaca únicamente cómo migrar la aplicación cliente. La migración del SDK en la aplicación NO migrará los datos desde los servidores Capptain a los servidores Mobile Engagement.

Si va a migrar desde una versión anterior, consulte el sitio web de Capptain para migrar a la versión 1.16 en primer lugar y, a continuación, aplique el siguiente procedimiento

#### Agente

El método `registerApp:`  se ha reemplazado por el nuevo método `init:`. El delegado de la aplicación deben actualizarse como corresponda y usar una cadena de conexión:

			- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
			{
			  [...]
			  [EngagementAgent init:@"YOUR_CONNECTION_STRING"];
			  [...]
			}

Seguimiento de SmartAd se ha quitado del SDK y solo tiene que quitar todas las instancias de la clase `AETrackModule`

#### Cambios del nombre de la clase

Como parte de la reconfiguración de marca, hay algunos nombres de clase/archivo que deben cambiarse.

Todas las clases precedidas de "CP" cambian su nombre introduciendo el prefijo "AE".

Ejemplo:

-   `CPModule.h` cambia su nombre a `AEModule.h`.

Todas las clases con el prefijo "Capptain" cambian su nombre para introducir el prefijo "Engagement".

Ejemplos:

-   La clase `CapptainAgent` cambia su nombre a `EngagementAgent`.
-   La clase `CapptainTableViewController` cambia su nombre a `EngagementTableViewController`.
-   La clase `CapptainUtils` cambia su nombre a `EngagementUtils`.
-   La clase `CapptainViewController` cambia su nombre a `EngagementViewController`.

<!--HONumber=47-->
