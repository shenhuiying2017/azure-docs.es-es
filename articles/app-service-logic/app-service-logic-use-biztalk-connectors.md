<properties
   pageTitle="Uso de conectores"
   description="Uso de conectores"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="anuragdalmia"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="07/07/2015"
   ms.author="prkumar"/>

#Conectores#

> [AZURE.NOTE]Este tema se ha retirado. Consulte la [lista de aplicaciones de API y conectores](app-service-logic-connectors-list.md) para ver todas las aplicaciones API y conectores integrados que se encuentran disponibles.


Los conectores son aplicaciones de API que permiten conectarse a datos y servicios que se ejecutan en la nube o de forma local. Los conectores facilitan llegar a los datos con una variedad de desencadenadores integrados y acciones a las que se puede obtener acceso fácilmente en las aplicaciones lógicas y mucho más.

Servicios de aplicaciones de Azure proporciona una serie de conectores listos para usar:

##Conectores estándar##
* [Conector de Bus de servicio de Azure]
* [Conector de blobs de Almacenamiento de Azure]
* Conector de Webjobs de Azure
* [Conector de Box]
* [Conector de Chatter]
* [Conector de Dropbox]
* [Conector de Facebook]
* [Conector de archivos]
* [Conector de FTP]
* [Conector de HDInsight]
* [Conector de HTTP]
* [Conector de Office 365]
* [Conector de OneDrive]
* [Conector de POP3]
* [Conector de QuickBooks]
* [Conector de Salesforce]
* [Conector de SFTP]
* [Conector de SharePoint]
* [Conector de Slack]
* [Conector de SFTP]
* [Conector de SQL]
* [Conector de SugarCRM]
* [Conector de Twilio]
* [Conector de Twitter]
* [Conector de espera]
* [Conector de Yammer]


##Conectores y aplicaciones de API Premium##
* [Conector AS2]
* [BizTalk EDIFACT]
* [Codificador de archivos sin formato de BizTalk]
* [Codificador JSON de BizTalk]
* [Servicio de transformación de BizTalk]
* [Reglas de BizTalk]
* [BizTalk X12]
* [Extractor XPath de BizTalk]
* [Validador XML de BizTalk]
* Conector de DB2
* Conector de Informix
* [Conector de Oracle]
* Conector de MQ
* [Conector de SAP]

Para obtener más información, consulte la referencia de las API de conectores [http://aka.ms/appservicesconnectorreference](http://aka.ms/appservicesconnectorreference).

##Aplicaciones lógicas y conectores##
Las aplicaciones lógicas constan de desencadenadores y acciones. Algunos conectores pueden utilizarse como desencadenadores para crear una instancia de un flujo de trabajo basado en un evento o la disponibilidad de algunos datos. Los conectores también se usan como acciones para leer y escribir datos o realizar otras acciones admitidas por el conector.

###Conectores como desencadenadores###
Varios conectores proporcionan desencadenadores para aplicaciones lógicas. Estos desencadenadores son de dos tipos:

1. Desencadenadores de sondeo: estos desencadenadores sondearán el servicio que proceda a una frecuencia especificada para comprobar si hay nuevos datos. Cuando haya nuevos datos, se ejecutará una nueva instancia de la aplicación lógica con los datos como entrada. El desencadenador puede realizar tareas adicionales (como limpiar los datos que se han leído y pasado a la aplicación lógica) para impedir que los mismos datos que se consuman varias veces. Algunos de estos conectores son los de archivo, SQL y almacenamiento de Azure.
2. Desencadenadores de inserción: estos desencadenadores escuchan datos en un extremo o esperan a que se produzca un evento para desencadenar una nueva instancia de una aplicación lógica. Ejemplos de estos conectores son los de escucha HTTP y Twitter.

###Conectores como acciones###
Los conectores también pueden utilizarse como acciones que forman parte de una aplicación lógica. Las acciones son útiles para buscar datos en la aplicación lógica a fin de utilizarlos en la ejecución; por ejemplo, puede que necesite examinar una base de datos SQL para obtener información adicional acerca de un cliente cuando esté procesando un pedido. Alternativamente, puede que necesite escribir, actualizar o eliminar datos en un destino, para lo que puede utilizar las acciones proporcionadas por los conectores. Las acciones se asignan a operaciones en las aplicaciones de la API (como se define por sus metadatos de Swagger).


<!-- Links -->

[Conector de Box]: app-service-logic-connector-box.md
[Conector de Facebook]: app-service-logic-connector-facebook.md
[Conector de Salesforce]: app-service-logic-connector-salesforce.md
[Conector de Twitter]: app-service-logic-connector-twitter.md
[Conector de SAP]: app-service-logic-connector-sap.md
[Conector de FTP]: app-service-logic-connector-ftp.md
[Conector de HTTP]: app-service-logic-connector-http.md
[Conector de blobs de Almacenamiento de Azure]: app-service-logic-connector-azurestorageblob.md
[Conector de Office 365]: app-service-logic-connector-office365.md
[Conector de SharePoint]: app-service-logic-connector-sharepoint.md
[Conector de SugarCRM]: app-service-logic-connector-sugarcrm.md
[Conector de QuickBooks]: app-service-logic-connector-quickbooks.md
[Conector de Yammer]: app-service-logic-connector-yammer.md
[Conector de Twilio]: app-service-logic-connector-twilio.md
[Conector de SFTP]: app-service-logic-connector-smtp.md
[Conector de SFTP]: app-service-logic-connector-sftp.md
[Conector de POP3]: app-service-logic-connector-pop3.md
[Conector de Dropbox]: app-service-logic-connector-dropbox.md
[Conector de Chatter]: app-service-logic-connector-chatter.md
[Conector de HDInsight]: app-service-logic-connector-hdinsight.md
[Conector de Bus de servicio de Azure]: app-service-logic-connector-azureservicebus.md
[Conector de Oracle]: app-service-logic-connector-oracle.md
[Conector de SQL]: app-service-logic-connector-sql.md
[Conector de OneDrive]: app-service-logic-connector-onedrive.md
[Conector de archivos]: app-service-logic-connector-file.md
[Conector de Slack]: app-service-logic-connector-slack.md
[Codificador de archivos sin formato de BizTalk]: app-service-logic-flatfile-encoder.md
[Extractor XPath de BizTalk]: app-service-logic-xpath-extract.md
[Validador XML de BizTalk]: app-service-logic-xml-validator.md
[Reglas de BizTalk]: app-service-logic-use-biztalk-rules.md
[Conector AS2]: app-service-logic-connector-as2.md
[BizTalk EDIFACT]: app-service-logic-connector-edifact.md
[BizTalk X12]: app-service-logic-connector-x12.md
[JSON Encoder]: app-service-logic-json-encoder.md
[Servicio de transformación de BizTalk]: app-service-logic-transform-xml-documents.md
[Conector de espera]: app-service-logic-wait.md

<!---HONumber=July15_HO5-->