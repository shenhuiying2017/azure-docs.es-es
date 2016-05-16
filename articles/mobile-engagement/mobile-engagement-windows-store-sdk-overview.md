<properties 
	pageTitle="Información general del SDK Windows Universal" 
	description="Introducción al SDK de Windows Universal para Azure Mobile Engagement" 									
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="05/03/2016" 
	ms.author="piyushjo" />

#Introducción al SDK de Windows Universal para Azure Mobile Engagement

Comience aquí para obtener los detalles sobre cómo integrar Azure Mobile Engagement en una aplicación de Windows Universal. Si primero quiere probarlo, asegúrese de completar nuestro [tutorial de 15 minutos](mobile-engagement-windows-store-dotnet-get-started.md).

Haga clic para ver el [contenido del SDK](mobile-engagement-windows-store-sdk-content.md)

##Procedimientos de integración

1. Comience aquí: [Cómo integrar Mobile Engagement en su aplicación de Windows Universal](mobile-engagement-windows-store-integrate-engagement.md)

2. Para las notificaciones: [Integración de cobertura (notificaciones) en su aplicación Windows Universal](mobile-engagement-windows-store-integrate-engagement-reach.md)

3. Implementación del plan de etiquetas: [Cómo usar la API de etiquetado avanzado de Mobile Engagement en la aplicación de Windows Universal](mobile-engagement-windows-store-use-engagement-api.md)

##Notas de la versión

###3\.4.0 (04/19/2016)

-   Mejoras de superposición de Reach.
-   API "TestLogLevel" agregada para habilitar, deshabilitar y filtrar registros de consola emitidos por el SDK.
-   Notificaciones en actividad corregidas dirigidas a la primera actividad que no aparecen en el inicio de la aplicación.

Para la versión anterior, consulte las [notas de la versión completas](mobile-engagement-windows-store-release-notes.md)

##Procedimientos de actualización

Si ya integró una versión anterior de Engagement en la aplicación, debería tener en cuenta los siguientes puntos a la hora de actualizar el SDK.

Es posible que tenga que seguir varios procedimientos si se perdió varias versiones del SDK, consulte la sección completa [Procedimientos de actualización](mobile-engagement-windows-store-upgrade-procedure.md). Por ejemplo, si migra desde 0.10.1 a 0.11.0, primero debe seguir el procedimiento "de 0.9.0 a 0.10.1" y luego el procedimiento "de 0.10.1 a 0.11.0".

###De 3.3.0 a 3.4.0

####Registros de prueba

Los registros de consola generados por el SDK ahora se pueden habilitar, deshabilitar o filtrar. Para personalizar esto, actualice la propiedad `EngagementAgent.Instance.TestLogEnabled` a uno de los valores disponibles en la enumeración `EngagementTestLogLevel`, por ejemplo:

			EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
			EngagementAgent.Instance.Init();

####Recursos

Se ha mejorado la superposición de Reach. Forma parte de los recursos del paquete NuGet del SDK.

Al actualizar a la nueva versión del SDK puede elegir si desea conservar o no los archivos existentes de la carpeta de superposición de los recursos:

* Si la superposición anterior funciona o va a integrar los elementos `WebView` manualmente, entonces puede decidir mantener los archivos de salida, lo cual seguirá funcionando. 
* Si desea actualizar a la nueva superposición, simplemente reemplace toda la carpeta `overlay` de sus recursos por la nueva desde el paquete del SDK (aplicaciones UWP: tras la actualización, puede obtener la nueva carpeta de superposición desde %USERPROFILE%\\.nuget\\packages\\MicrosoftAzure.MobileEngagement\\3.4.0\\content\\win81\\Resources).

> [AZURE.WARNING] El uso de la nueva superposición sobrescribirá todas las personalizaciones realizadas en la versión anterior.

### Actualizar de versiones anteriores

Consulte [Procedimientos de actualización](mobile-engagement-windows-store-upgrade-procedure.md)

<!---HONumber=AcomDC_0504_2016-->