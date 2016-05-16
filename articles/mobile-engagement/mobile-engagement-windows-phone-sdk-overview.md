<properties 
	pageTitle="Introducción al SDK de Windows Phone Silverlight" 
	description="Introducción al SDK de Windows Phone Silverlight para Azure Mobile Engagement" 					
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="dwrede"
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/03/2016" 
	ms.author="piyushjo" />

#Introducción al SDK de Windows Phone Silverlight para Azure Mobile Engagement

Comience aquí para obtener los detalles sobre cómo integrar Azure Mobile Engagement en una aplicación de Windows Phone Silverlight. Si primero quiere probarlo, asegúrese de completar nuestro [tutorial de 15 minutos](mobile-engagement-windows-phone-get-started.md).

Haga clic para ver el [contenido del SDK](mobile-engagement-windows-phone-sdk-content.md)

##Procedimientos de integración

1. Comience aquí: [Cómo integrar Mobile Engagement en su aplicación de Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)

2. Para las notificaciones: [Integración de cobertura (notificaciones) en su aplicación Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement-reach.md)

3. Implementación del plan de etiquetas: [Cómo usar la API de etiquetado avanzado de Mobile Engagement en la aplicación de Windows Phone Silverlight](mobile-engagement-windows-phone-use-engagement-api.md)

##Notas de la versión

###3\.3.0 (04/19/2016)
Parte del paquete NuGet *MicrosoftAzure.MobileEngagement* **v3.4.0**

-   API "TestLogLevel" agregada para habilitar, deshabilitar y filtrar registros de consola emitidos por el SDK.

Para la versión anterior, consulte las [notas de la versión completas](mobile-engagement-windows-phone-release-notes.md)

##Procedimientos de actualización

Si ya integró una versión anterior de nuestro SDK en la aplicación, debería tener en cuenta los siguientes puntos a la hora de actualizar el SDK.

Es posible que tenga que seguir varios procedimientos si se perdió varias versiones del SDK. Consulte los [procedimientos de actualización](mobile-engagement-windows-phone-upgrade-procedure.md) completos. Por ejemplo, si migra desde 0.10.1 a 0.11.0, primero debe seguir el procedimiento "de 0.9.0 a 0.10.1" y luego el procedimiento "de 0.10.1 a 0.11.0".

###De 2.0.0 a 3.3.0

####Registros de prueba

Los registros de consola generados por el SDK ahora se pueden habilitar, deshabilitar o filtrar. Para personalizar esto, actualice la propiedad `EngagementAgent.Instance.TestLogEnabled` a uno de los valores disponibles en la enumeración `EngagementTestLogLevel`, por ejemplo:

			EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
			EngagementAgent.Instance.Init();

### Actualizar de versiones anteriores

Consulte [Procedimientos de actualización](mobile-engagement-windows-phone-upgrade-procedure/)
 

<!---HONumber=AcomDC_0504_2016-->