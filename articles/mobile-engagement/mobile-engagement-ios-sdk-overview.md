<properties 
	pageTitle="Información general del SDK de iOS de Azure Mobile Engagement" 
	description="Actualizaciones y procedimientos más recientes para el SDK de iOS para Azure Mobile Engagement"
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="05/04/2015" 
	ms.author="piyushjo" />

#SDK de iOS para Azure Mobile Engagement

Comience aquí a obtener todos los detalles sobre cómo integrar Azure Mobile Engagement en una aplicación de iOS. Si primero quiere probarlo, asegúrese de seguir nuestro [tutorial de 15 minutos](mobile-engagement-ios-get-started.md)

Haga clic para ver el [contenido del SDK](mobile-engagement-ios-sdk-content.md)

##Procedimientos de integración
1. Comience aquí: [Cómo integrar Mobile Engagement en su aplicación de iOS](mobile-engagement-ios-integrate-engagement.md)

2. Para las notificaciones: [Integración de cobertura (notificaciones) en su aplicación iOS](mobile-engagement-ios-integrate-engagement-reach.md)

3. Implementación del plan de etiquetas: [Uso de la API de etiquetado de Mobile Engagement avanzada en su aplicación iOS](mobile-engagement-ios-use-engagement-api.md)


##Notas de la versión

###2.1.0 (24/04/2015)

-   Agregar compatibilidad con Swift.
-   Al hacer clic en una notificación, la dirección URL de la acción se ejecuta ahora justo después de abrir la aplicación.
-   Se ha agregado el archivo de encabezado que faltaba en el paquete del SDK.
-   Se ha corregido un problema cuando se deshabilitaba el generador de informes de error de Mobile Engagement.

Para la versión anterior, consulte las [notas de la versión completas](mobile-engagement-ios-release-notes.md)

##Procedimientos de actualización

Si ya integró una versión anterior de Engagement en la aplicación, debería tener en cuenta los siguientes puntos a la hora de actualizar el SDK.

Es posible que tenga que seguir varios procedimientos si se perdió varias versiones del SDK, consulte la sección completa [Procedimientos de actualización](mobile-engagement-ios-upgrade-procedure.md).

Para cada nueva versión del SDK debe reemplazar primero (quitar y volver a importar en xcode) las carpetas EngagementSDK y EngagementReach.

###De 2.0.0 a 2.1.0
Ninguno.

<!--HONumber=54--> 