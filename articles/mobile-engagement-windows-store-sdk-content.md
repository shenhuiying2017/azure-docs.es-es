<properties 
	pageTitle="Contenido del SDK de la Tienda Windows para Azure Mobile Engagement" 
	description="Actualizaciones y procedimientos más recientes del SDK de la Tienda Windows para Azure Mobile Engagement" 					
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="lalathie" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="kapiteir" />

# Contenido del SDK

En este documento se indica y describe el contenido del archivo SDK.

## Carpeta `Root`

En esta carpeta se incluyen copias de las licencias de software y un vínculo a la documentación en línea.

`documentation.html` : se incluye un vínculo a la documentación en línea del SDK de Engagement para la aplicación Windows 8 C\# Metro.

En esta carpeta también se incluyen los archivos de licencia.

## Carpeta `/lib`

En esta carpeta se incluye información sobre dónde obtener el SDK de Engagement.

`azuresdk-mobileengagement-windows-X.X.X.nupkg` : paquete de NuGet para la integración de SDK.

## Carpeta `/Resources`

En esta carpeta se incluyen todos los recursos que necesita Engagement. Además, puede personalizarlas para adaptarlas a su aplicación.

`EngagementConfiguration.xml` : el archivo de configuración de Engagement, donde puede personalizar la configuración de Engagement (cadena de conexión, bloqueo de informes, etc.).

### Carpeta /html

`EngagementNotification.html` :  el diseño html de la vista web  `Notificación`.

`EngagementAnnouncement.html` :  el diseño html de la vista web  `Anuncio`.

### Carpeta /images

`EngagementIconNotification.png` : el icono de marca que se muestra a la izquierda de una notificación.

`EngagementIconOk.png` :  el icono `Correcto` de las páginas de contenido de Reach para el botón de acción o validación.

`EngagementIconNOK.png` :  el icono `Incorrecto` que se usa cuando se deshabilita el botón de validación de las páginas de contenido de Reach.

`EngagementIconClose.png` :  el icono `Cerrar` de las notificaciones y el contenido de Reach para el botón Descartar.

### Carpeta /overlay

`EngagementOverlayAnnouncement.xaml` :  el diseño xaml de  `Anuncio`.

`EngagementOverlayAnnouncement.xaml.cs` : el código vinculado `EngagementOverlayAnnouncement.xaml`.

`EngagementOverlayNotification.xaml` :  el diseño xaml de `Notificación`.

`EngagementOverlayNotification.xaml.cs` : el código vinculado `EngagementOverlayNotification.xaml`.

`EngagementPageOverlay.cs` :  el código de visualización de anuncios y notificaciones de `superposición`.

## Carpeta `/src/agent`

En esta carpeta se incluye el objeto EngagementPage.

`EngagementPage.cs` : la clase base para las páginas que informan automáticamente una actividad a Engagement.

<!--HONumber=47-->
