<properties 
   pageTitle="Archivado de mensajes AS2" 
   description="En este tema se trata el archivado de mensajes AS2." 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="harishkragarwal" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="03/18/2015"
   ms.author="hariag"/>


# Archivado de mensajes AS2

El **Conector AS2** expone la posibilidad de archivar mensajes. Al archivar se almacena el mensaje en el **Contenedor de blobs de Azure** que forma parte de la configuración del paquete. 

El archivado se expone en dos puntos tanto para mensajes como para confirmaciones (MDN):

1. **Desencadenador de recepción/decodificación**: el mensaje se archiva en cuanto se recibe en la instancia de aplicación de API 
2. **Acción de codificación/envío**: el mensaje codificado se archiva después de que todo el procesamiento está completo y justo antes de enviarse al socio 

##Trabajo Recuperación de la URL archivada de un mensaje

Vaya a la instancia de aplicación de API del Conector AS2 y haga clic en 'Tracking'. Limite la información de seguimiento usando los parámetros de filtro. Una vez que el mensaje se ve, haga clic para ver su vista detallada. La URL archivada del mensaje se mostrará en esta vista detallada.

![][1]  

##Trabajo Recuperación del mensaje archivado

Use la URL recuperada anteriormente para recuperar el mensaje archivado del Almacenamiento de blobs de Azure.


<!--Image references-->
[1]: ./media/app-service-logic-archive-as2-messages/Tracking.jpg

<!--HONumber=49-->