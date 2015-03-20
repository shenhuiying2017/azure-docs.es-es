<properties 
	pageTitle="Integración del SDK de Windows Phone para Azure Mobile Engagement" 
	description="Obtenga información sobre el contenido del SDK de Windows Phone para Azure Mobile Engagement" 					
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="lalathie" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="" 
	ms.topic="article" 
	ms.date="01/24/2015" 
	ms.author="kapiteir" />
	
# Contenido del SDK

En este documento se indica y describe el contenido del archivo SDK.

## Carpeta `Root`

En esta carpeta se incluyen copias de las licencias de software y un vínculo a la documentación en línea.

`documentation.html` : se incluye un vínculo a la documentación en línea del SDK de Engagement para Windows Phone.

En esta carpeta también se incluyen los archivos de licencia.

## Carpeta `/lib`

En esta carpeta se incluye información sobre dónde obtener el SDK de Engagement.

`azuresdk-mobileengagement-windowsphone-X.X.X.nupkg` : paquete de NuGet para la integración de SDK.

## Carpeta `/Resources`

En esta carpeta se incluyen todos los recursos que necesita Engagement. Además, puede personalizarlas para adaptarlas a su aplicación.

`EngagementIconNotification.png` : el icono de marca que se muestra a la izquierda de una notificación.

`EngagementIconOk.png` : el icono "Aceptar" del objeto ApplicationBar de las páginas de Reach.

`EngagementIconCancel.png` : el icono "Cancelar" del objeto ApplicationBar de las páginas de Reach.

`EngagementIconCloseLight.png` : el icono "Cerrar" de la notificación de Reach de Engagement para el tema claro de Windows Phone.

`EngagementIconCloseDark.png` : el icono "Cerrar" de la notificación de Reach de Engagement para el tema oscuro de Windows Phone.

`EngagementConfiguration.xml` : el archivo de configuración de Engagement, donde puede personalizar la configuración de Engagement (cadena de conexión, bloqueo de informes, etc.).

## Carpeta `/src/agent`

En esta carpeta se incluye el objeto EngagementPage.

`EngagementPage.cs` : la clase base para las páginas que informan automáticamente una actividad a Engagement.

## Carpeta `/src/reach`

Por último, en esta carpeta encontrará los valores predeterminados de XAML (y sus homólogos de C\#) de cada página.

Puede usarlos como base para sus propias páginas. Simplemente siga los comentarios para saber qué tiene que hacer exactamente.

### Anuncio TextView

`EngagementDefaultTextViewAnnouncementPage.xaml`

`EngagementDefaultTextViewAnnouncementPage.xaml.cs`

### Anuncio WebView

`EngagementDefaultWebViewAnnouncementPage.xaml`

`EngagementDefaultWebViewAnnouncementPage.xaml.cs`

### Sondeo

`EngagementDefaultPollPage.xaml`

`EngagementDefaultPollPage.xaml.cs`

### Notificación

`EngagementBasicNotificationView.xaml`

`EngagementBasicNotificationView.xaml.cs`

<!--HONumber=47-->
