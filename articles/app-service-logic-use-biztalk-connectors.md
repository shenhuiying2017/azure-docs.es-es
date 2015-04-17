<properties 
   pageTitle="Uso de conectores" 
   description="Uso de conectores" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="prkumar" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="03/20/2015"
   ms.author="prkumar"/>


#Uso de conectores#

Los conectores son aplicaciones de API que permiten conectarse a los servicios que se ejecutan en la nube o de forma local para obtener datos, colocarlos o realizar acciones. Pueden utilizarse con los Servicios de aplicaciones de Azure, desde aplicaciones web, móviles y lógicas.

Los servicios de aplicaciones de Azure tienen integrados varios conectores que le permiten conectarse a los servicios que le interesen, mientras centra sus esfuerzos en la lógica empresarial actual de la aplicación. Además, la plataforma se puede extender fácilmente escribiendo sus propias aplicaciones de API para conectarse a las aplicaciones heredadas.

Estas son algunas de las características clave de los conectores de BizTalk:

1. Administración de la seguridad de OAuth para conectarse a servicios SaaS populares (Facebook, Twitter, Office 365 o SalesForce)
2. Conectividad con los activos locales a través de una retransmisión de bus de servicio.
3. Conectividad con protocolos comunes para recibir y enviar datos.

##Aplicaciones lógicas y conectores##
Las aplicaciones lógicas constan de desencadenadores y acciones. Algunos conectores pueden utilizarse como desencadenadores para identificar si hay nuevos datos disponibles y desencadenar una aplicación lógica usando esos datos como entrada. Los conectores también pueden utilizarse como una acción en el medio de una aplicación lógica para buscar y escribir datos o realizar otras acciones que admita el conector.

###Conectores como desencadenadores###
Varios conectores proporcionan desencadenadores para aplicaciones lógicas. Estos desencadenadores son de dos tipos:

1. Desencadenadores de sondeo: estos desencadenadores sondearán el servicio que proceda a una frecuencia especificada para comprobar si hay nuevos datos. Cuando haya nuevos datos, se ejecutará una nueva instancia de la aplicación lógica con los datos como entrada. El desencadenador puede realizar tareas adicionales (como limpiar los datos que se han leído y pasado a la aplicación lógica) para impedir que los mismos datos que se consuman varias veces. Algunos de estos conectores son los de archivo, SQL y almacenamiento de Azure.
2. Desencadenadores de inserción: estos desencadenadores escuchan datos en un extremo o esperan a que se produzca un evento para desencadenar una nueva instancia de una aplicación lógica. Ejemplos de estos conectores son los de escucha HTTP y Twitter.

###Conectores como acciones###
Los conectores también pueden utilizarse como acciones que forman parte de una aplicación lógica. Esto puede ser útil para buscar datos en la aplicación lógica a fin de utilizarlos en la ejecución; por ejemplo, puede que necesite examinar una base de datos SQL para obtener información adicional acerca de un cliente cuando esté procesando un pedido del cliente en una aplicación lógica. Alternativamente, puede que necesite escribir, actualizar o eliminar datos en un destino, para lo que puede utilizar las acciones proporcionadas por los conectores.

##Uso de los conectores##
En los artículos siguientes, se proporcionan ejemplos sobre cómo se pueden utilizar algunos de los conectores integrados.

* [Uso del conector HTTP]
* [Uso del conector FTP]
* [Uso del conector de Box]
* [Uso del conector del blob de almacenamiento de Azure]
* [Uso del conector de Facebook]
* [Uso del conector de Twitter]
* [Uso del conector de Salesforce]
* [Uso del conector de Office 365]
* [Uso del conector de SharePoint]
* [Uso del conector de SAP]

Para obtener más información, consulte la referencia de las API de conectores [http://aka.ms/appservicesconnectorreference](http://aka.ms/appservicesconnectorreference).


<!-- Links -->

[Uso del conector de Box]: app-service-logic-connector-box.md
[Uso del conector de Facebook]: app-service-logic-connector-facebook.md
[Uso del conector de Salesforce]: app-service-logic-connector-salesforce.md
[Uso del conector de Twitter]: app-service-logic-connector-twitter.md
[Uso del conector de SAP]: app-service-logic-connector-sap.md
[Uso del conector FTP]: app-service-logic-connector-ftp.md
[Uso del conector HTTP]: app-service-logic-connector-http.md
[Uso del conector del blob de almacenamiento de Azure]: app-service-logic-connector-azurestorageblob.md
[Uso del conector de Office 365]: app-service-logic-connector-office365.md
[Uso del conector de SharePoint]: app-service-logic-connector-sharepoint.md


<!--HONumber=49-->