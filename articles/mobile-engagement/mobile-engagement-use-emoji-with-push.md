<properties 
	pageTitle="Uso de emoticonos Emoji dentro de notificaciones push" 
	description="Uso de emoticonos Emoji dentro de las notificaciones push"					
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="05/06/2015" 
	ms.author="piyushjo" />

#Uso de emoticonos Emoji dentro de notificaciones push

Puede incluir emoticonos Emoji en las notificaciones push. Actualmente, Azure Mobile Engagement solo admite los emoticonos Emoji de 3 bytes establecidos para notificaciones de texto dentro y fuera de la aplicación. Siga estos pasos:

1.  Primero, debe buscar una biblioteca de emoticonos Emoji de 3 bytes. Encontrará todos los emoticonos Emoji que puede usar en el siguiente [vínculo](http://stackoverflow.com/questions/10153529/emoji-on-mysql-and-php-why-some-symbol-yes-other-not).

	![][1]

2. Vaya a la pestaña Reach en el portal de Azure Mobile Engagement.

3. Seleccione el tipo de notificación push (anuncio, encuestas, etc.). En este ejemplo, elegimos una inserción de anuncio.

4. Especifique los distintos campos de la notificación hasta que llegue al texto de la notificación. Aquí es donde agregará el emoticono Emoji. Puede colocarlo en el título, en el mensaje o en ambos.

	![][2]

5. En el vínculo anterior, corte el emoticono Emoji que desea usar. Péguelo directamente en el título o en el mensaje, en el lugar que quiera.

6. Complete los demás campos de la notificación y guárdela.

7. Al ejecutar una prueba o activar el anuncio, verá una notificación con el emoticono tal y como lo especificó.

	![][3] ![][4]

<!-- Images. -->
[1]: ./media/mobile-engagement-use-emoji-with-push/emoji.png
[2]: ./media/mobile-engagement-use-emoji-with-push/notification_input.png
[3]: ./media/mobile-engagement-use-emoji-with-push/notification_android.png
[4]: ./media/mobile-engagement-use-emoji-with-push/notification_ios.png
 

<!---HONumber=July15_HO2-->