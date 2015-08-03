<properties 
	pageTitle="Integración del SDK de Android para Azure Mobile Engagement" 
	description="Procedimientos y actualizaciones más recientes para el SDK de Android para Azure Mobile Engagement"
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="kpiteira" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="kapiteir" />


#Notas de la versión

##4.0.0 (07/06/2015)

-   Cambios en el protocolo interno para que la inserción y los análisis sean más confiables.
-   La inserción nativa (GCM/ADM) ahora también se usa para notificaciones dentro de la aplicación, por lo que debe configurar las credenciales de inserción nativas para cualquier tipo de campaña de inserción.
-   Corrección de la notificación con imagen grande: se mostraban solo 10 s después de insertarse.
-   Corrección del clic en un vínculo dentro de un anuncio web que tiene una dirección URL de acción predeterminada.
-   Corrección de un bloqueo excepcional relacionado con la administración del almacenamiento local.
-   Corrección de la administración dinámica de cadenas de configuración.
-   Actualización del CLUF.

##3.0.0 (02/17/2015)

-   Versión inicial de Azure Mobile Engagement
-   La configuración de appID se reemplaza por una configuración de cadena de conexión.
-   Se ha eliminado la API para enviar y recibir mensajes XMPP arbitrarios de entidades XMPP arbitrarias.
-   Se ha eliminado la API para enviar y recibir mensajes entre dispositivos.
-   Mejoras de seguridad.
-   Seguimiento de SmartAd y Google Play eliminados.

 

<!---HONumber=July15_HO4-->