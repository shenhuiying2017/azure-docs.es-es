<properties 
   pageTitle="Seguimiento de mensajes B2B" 
   description="En este tema, se describe el seguimiento del procesamiento B2B." 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="rajram" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="07/01/2015"
   ms.author="rajram"/>


# Seguimiento de mensajes B2B

## Información sobre el seguimiento B2B
La comunicación B2B (del inglés Business-to-Business) implica el procesamiento de mensajes entre socios comerciales. Las relaciones se definen como "acuerdos entre dos socios comerciales". Una vez establecida la comunicación, hay que supervisar si la comunicación se produce según lo esperado. Al comercializar las aplicaciones de API de B2B como parte de los servicios de aplicaciones de Azure, hemos habilitado datos de seguimiento, que están también disponibles a través de la interfaz del Portal de Azure.

## AS2
Después de crear una instancia de una aplicación de API de AS2, vaya a esa instancia y acceda a la parte Seguimiento. Aquí podrá ver toda la información de seguimiento de AS2, así como filtrarla a través de las hojas de filtros mostrados.

![][1]

## EDIFACT
Después de crear una instancia de una aplicación de API de EDIFACT, vaya a esa instancia y acceda a la parte Seguimiento. Aquí podrá ver toda la información de seguimiento de EDIFACT, así como filtrarla a través de los filtros mostrados. Además, se pueden ver los datos de intercambio, de grupo y de conjunto de transacciones como pasos en la vista.

Si los lotes se han creado como parte de los acuerdos de EDIFACT en la aplicación asociada de API de administración de socios comerciales, la parte Procesamiento por lotes mostrará una lista con todos los lotes. Esto permite acceder a un lote para ver el mensaje que constituye el mensaje activo (si existe) y también la información de los lotes completados en el pasado.

![][2]

## X12
Después de crear una instancia de una aplicación de API de X12, vaya a esa instancia y acceda a la parte Seguimiento. Aquí podrá ver toda la información de seguimiento de X12, así como filtrarla a través de los filtros mostrados. Además, se pueden ver los datos de intercambio, de grupo y de conjunto de transacciones como pasos en la vista.

Si los lotes se han creado como parte de los acuerdos de X12 en la aplicación asociada de API de administración de socios comerciales, la parte Procesamiento por lotes mostrará una lista con todos los lotes. Esto permite acceder a un lote para ver el mensaje que constituye el mensaje activo (si existe) y también la información de los lotes completados en el pasado.

X12 y EDIFACT tienen vistas similares de seguimiento.

<!--Image references-->
[1]: ./media/app-service-logic-track-b2b-messages/AS2Tracking.jpg
[2]: ./media/app-service-logic-track-b2b-messages/EDIFACTTracking.jpg

<!---HONumber=July15_HO3-->