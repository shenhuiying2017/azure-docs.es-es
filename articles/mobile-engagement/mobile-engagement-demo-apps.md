<properties
	pageTitle="Aplicación de demostración de Azure Mobile Engagement"
	description="Explica de dónde se puede descargar, cómo usar y qué ventajas presenta mediante la aplicación de demostración de Azure Mobile Engagement"
	services="mobile-engagement"
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="erikre" 
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/29/2016"
	ms.author="piyushjo" />

# Aplicación de demostración de Azure Mobile Engagement

Se ha publicado una aplicación de demostración de Azure Mobile Engagement para las plataformas **iOS**, **Android** y **Windows** para ayudarle a encontrar recursos útiles y obtener más información acerca de Azure Mobile Engagement.

La aplicación le ayudará a:

1. Encontrar fácilmente vínculos útiles a recursos específicos de Azure Mobile Engagement como vídeos, documentación, foros de soporte técnico, sitios dónde debe ir para generar solicitudes de características etc. 
2. Experimentar notificaciones de ejemplo compatibles con Azure Mobile Engagement para obtener ideas para sus propias aplicaciones móviles. 
3. Le proporcionará una implementación de referencia que puede usar para estudiar cómo implementar Mobile Engagement en su propia aplicación para: 

	- recopilar datos de análisis 
	- implementar escenarios avanzados de notificación del tipo *Pantalla completa intersticial* o *Emergente*
	- implementar sondeos o encuestas
	- implementar escenarios de inserción silenciosa o inserción de datos   

## Instalación de la aplicación
Esta aplicación está disponible en las respectivas tiendas de aplicaciones:

1. **Aplicación de demostración universal de Windows**

	- [Vínculo de descarga en la Tienda de aplicaciones Windows](https://www.microsoft.com/es-ES/store/apps/azure-mobile-engagement/9nblggh4qmh2) 
	- La aplicación se desarrolló como aplicación universal de Windows 10 y el código fuente está disponible en [Github](https://github.com/Azure/azure-mobile-engagement-app-ios)

2. **Aplicación de demostración de iOS**

	- [Vínculo de descarga en la Tienda Apple](https://itunes.apple.com/us/app/azure%20mobile%20engagement/id1105090090) 
	- La aplicación se desarrolló en iOS Swift y el código fuente está disponible en [Github](https://github.com/Azure/azure-mobile-engagement-app-ios)

3. **Aplicación de demostración de Android**

	- [Vínculo de descarga en Google Play Store](https://play.google.com/store/apps/details?id=com.microsoft.azure.engagement)
	- El código fuente está disponible en [GitHub](https://github.com/Azure/azure-mobile-engagement-app-android).

![][1]

![][2] ![][3]


## Uso

Puede usar estas aplicaciones de las siguientes maneras:

**(1) Descargue las aplicaciones en el dispositivo mediante los vínculos de las tiendas de aplicaciones proporcionados anteriormente.**

>[AZURE.IMPORTANT] No necesita ninguna cuenta de Azure ni conectar la aplicación a ningún back-end. La aplicación funcionará de forma independiente.

- Una vez que tenga la aplicación en el dispositivo podrá recorrer los vínculos en el menú de la izquierda para buscar todos los recursos útiles acerca de Azure Mobile Engagement. 
- También hemos agregado nuestra [fuente RSS del servicio](https://aka.ms/azmerssfeed) directamente en esta aplicación para que siempre esté al corriente de las actualizaciones de producto más recientes.
- También puede ir a través de los escenarios de notificación de ejemplo para experimentar qué tipo de notificaciones son compatibles con Azure Mobile Engagement para cada plataforma. Estas notificaciones se pueden experimentar localmente, es decir, puede hacer clic en los botones de las pantallas para que le muestren una experiencia de las notificaciones que sea idéntica a las notificaciones que se le enviarán desde la plataforma de Azure Mobile Engagement. 

![][4]
    
![][5] ![][6]

**(2) Descargue el código fuente mediante los vínculos de Github proporcionados anteriormente.**

- Una vez que haya descargado el código fuente, puede abrirlo en los respectivos entornos de desarrollo, XCode en el caso de iOS, Android Studio para Android y Visual Studio para Windows. 
- A continuación debe seguir los [pasos de integración básicos de SDK](mobile-engagement-windows-store-dotnet-get-started.md) para que pueda conectar esta aplicación con su propia instancia de back-end de Mobile Engagement. 
	- Debe configurar una cadena de conexión en la aplicación.  
	- También debe configurar la plataforma de notificaciones push de la aplicación. 
- Observe que esta aplicación se instrumenta con Azure Mobile Engagement y por tanto, cuando abra la aplicación después de conectarla al back-end, podrá ver la sesión de usuario, actividades, eventos, etc. en la pestaña Supervisar. 
- También podrá enviar notificaciones a esta aplicación desde su propia instancia de Mobile Engagement en lugar de usar notificaciones locales. 
	- Aquí puede agregar el dispositivo como dispositivo de prueba mediante el elemento de menú **Get the Device ID** (Obtener el id. de dispositivo) de la aplicación que le proporcionará un identificador de dispositivo que, a continuación, puede registrar como un dispositivo de prueba con la instancia de back-end de la plataforma. 

	![][7]
	    
	![][8] ![][9]

## Características principales de la aplicación de demostración

1. Como se mencionó anteriormente, con esta aplicación, tendrá los recursos principales de Azure Mobile Engagement a mano. Puede recorrer los vínculos en el menú de la izquierda. 

2. Puede experimentar notificaciones fuera de aplicación para cada plataforma. Estas notificaciones se pueden enviar como **solo notificación** en cuyo caso, al hacer clic en la notificación, simplemente se abrirá una pantalla nativa de la aplicación (mediante **vinculación en profundidad**) o como **anuncio Web**, en cuyo caso podrá enviar contenido HTML adicional desde el back-end de Mobile Engagement que aparece cuando se hace clic en la notificación.

	![][29]

	
	- En iOS, tendrá que cerrar la aplicación para ver las notificaciones push fuera de la aplicación o sistema. Puede ver aquí la implementación para agregar **botones de acción** como los que se han agregado a esta notificación fuera de la aplicación para recibir *comentarios* y *compartir* de forma que el usuario pueda realizar acciones directamente desde la notificación. 
	    
	![][11] ![][14]
	
	
	- En Android, verá las opciones admitidas por Android, como agregar un texto de varias líneas (**Texto grande**) o agregar una imagen en la notificación (**Imagen grande**) junto con los **botones de acción** tal y como los admite iOS. 
	
	![][12] ![][15]
	
	
	- En Windows 10, puede ver qué aspecto tendrán las notificaciones en el equipo. Esta notificación también se mostrará en el **Centro de notificaciones** de Windows 10. De momento, no se pueden agregar **botones de acción** con el SDK de Windows. 
	
	![][10] ![][13]

3. Pruebe las notificaciones en aplicación para cada plataforma. Se trata de una experiencia de dos pasos en la que aparece una ventana de **notificaciones** en la que, al hacer clic, se abrirá una pantalla completa denominada **Anuncio** tal como se muestra a continuación. El contenido de este anuncio procede de la instancia de back-end de Mobile Engagement. El SDK tiene las plantillas de notificación y anuncio que se pueden personalizar fácilmente, como se muestra en esta aplicación de demostración, con la adición de nuestro logotipo y color corporativo.

	![][16]
	
	![][17] **iOS** ![][18] **Android**

4. También puede utilizar la característica **Categoría** de Azure Mobile Engagement para personalizar esta experiencia predeterminada. En la aplicación de demostración, hemos mostrado dos formas habituales de cambiar la experiencia de las notificaciones. Tenga en cuenta que la característica Categoría no se ha admitido aún en el SDK de Windows.

	**Pantalla completa intersticial**
	
	![][30]

	![][21] ![][22]

	**Notificación emergente**

	![][31]
	
	![][19] ![][20]

5. Azure Mobile Engagement también admite un tipo especializado de notificación en aplicación denominado **Sondeos** que le permite enviar encuestas rápidas a los usuarios de su aplicación por segmentos. Puede agregar preguntas y opciones para cada pregunta, como las que aparecen a continuación, que se mostrarán como una notificación en aplicación para el usuario de la misma.

	![][32]

	![][26]
	    
	![][27] **iOS** ![][28] **Android**

5. Azure Mobile Engagement también admite el envío de notificaciones silenciosas de **inserción de datos** mediante el cual puede enviar algunos datos de su servicio, como los datos JSON del ejemplo siguiente, datos que puede controlar en la aplicación y en los que puede realizar alguna acción. Por ejemplo, cómo cambiar el precio de un artículo mediante la notificación de inserción de datos de forma selectiva.

	![][33]

	![][23]
	    
	![][24] **iOS** ![][25] **Android**

> [AZURE.NOTE] Tenga en cuenta que puede ver instrucciones por pasos detalladas para cualquiera de estas notificaciones haciendo clic en *Haga clic aquí para obtener instrucciones sobre cómo enviar estas notificaciones desde la plataforma Mobile Engagement* en cualquier pantalla de ejemplo de notificación.


[1]: ./media/mobile-engagement-demo-apps/home-windows.png
[2]: ./media/mobile-engagement-demo-apps/home-ios.png
[3]: ./media/mobile-engagement-demo-apps/home-android.png
[4]: ./media/mobile-engagement-demo-apps/menu-windows.png
[5]: ./media/mobile-engagement-demo-apps/menu-ios.png
[6]: ./media/mobile-engagement-demo-apps/menu-android.png
[7]: ./media/mobile-engagement-demo-apps/device-id-windows.png
[8]: ./media/mobile-engagement-demo-apps/device-id-ios.png
[9]: ./media/mobile-engagement-demo-apps/device-id-android.png
[10]: ./media/mobile-engagement-demo-apps/out-of-app-windows.png
[11]: ./media/mobile-engagement-demo-apps/out-of-app-ios.png
[12]: ./media/mobile-engagement-demo-apps/out-of-app-android.png
[13]: ./media/mobile-engagement-demo-apps/out-of-app-display-windows.png
[14]: ./media/mobile-engagement-demo-apps/out-of-app-display-ios.png
[15]: ./media/mobile-engagement-demo-apps/out-of-app-display-android.png
[16]: ./media/mobile-engagement-demo-apps/in-app-windows.png
[17]: ./media/mobile-engagement-demo-apps/in-app-ios.png
[18]: ./media/mobile-engagement-demo-apps/in-app-android.png
[19]: ./media/mobile-engagement-demo-apps/pop-up-ios.png
[20]: ./media/mobile-engagement-demo-apps/pop-up-android.png
[21]: ./media/mobile-engagement-demo-apps/interstitial-ios.png
[22]: ./media/mobile-engagement-demo-apps/interstitial-android.png
[23]: ./media/mobile-engagement-demo-apps/data-push-windows.png
[24]: ./media/mobile-engagement-demo-apps/data-push-ios.png
[25]: ./media/mobile-engagement-demo-apps/data-push-android.png
[26]: ./media/mobile-engagement-demo-apps/survey-windows.png
[27]: ./media/mobile-engagement-demo-apps/survey-ios.png
[28]: ./media/mobile-engagement-demo-apps/survey-android.png
[29]: ./media/mobile-engagement-demo-apps/out-of-app.png
[30]: ./media/mobile-engagement-demo-apps/in-app-interstitial.png
[31]: ./media/mobile-engagement-demo-apps/in-app-pop-up.png
[32]: ./media/mobile-engagement-demo-apps/notification-poll.png
[33]: ./media/mobile-engagement-demo-apps/notification-data-push.png

<!---HONumber=AcomDC_0504_2016-->