<properties
	pageTitle="Lista de conectores administrados por Microsoft | Servicio de aplicaciones de Microsoft Azure"
	description="Obtenga una lista completa de los conectores administrados de Microsoft que puede usar para crear Aplicaciones lógicas en el Servicio de aplicaciones de Azure"
	services="app-service\logic"
	documentationCenter=""
	authors="MSFTMAN"
	manager="erikre"
	editor=""
    tags="connectors"/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="04/29/2016"
	ms.author="deonhe"/>

# Lista de conectores administrados

>[AZURE.NOTE] Esta versión del artículo se aplica a la versión de esquema 2015-08-01-preview de las aplicaciones lógicas. Para la versión de esquema 2014-12-01-preview, haga clic en [Lista de conectores](../app-service-logic/app-service-logic-connectors-list.md).

Para obtener información y una lista de lo que se incluye con cada nivel de servicio de precios, consulte [Precios del Servicio de aplicaciones de Azure](https://azure.microsoft.com/pricing/details/app-service/).

> [AZURE.NOTE] Si desea empezar a utilizar Aplicaciones lógicas de Azure antes de registrar una cuenta de Azure, vaya a [Pruebe el Servicios de aplicaciones de Azure](https://tryappservice.azure.com/?appservice=logic). Podrá crear inmediatamente una aplicación lógica de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

Seleccione un icono para aprender a aprovechar rápidamente estos conectores a fin de crear aplicaciones que llamen a estos servicios. Estos conectores se pueden utilizar para crear aplicaciones lógicas, PowerApps y flujos.

|API existentes||||
|-----------|-----------|-----------|-----------|
|[![Icono de API][blobicon]<br/>**Blob de Azure**][azureblobdoc]|[![Icono de API][bingsearchicon]<br/>**Búsqueda de Bing**][bingsearchdoc]|[![Icono de API][boxicon]<br/>**Box**][boxDoc]|[![Icono de API][crmonlineicon]<br/>**CRM en línea**][crmonlinedoc]|
|[![Icono de API][dropboxicon]<br/>**Dropbox**][dropboxdoc]|[![Icono de API][facebookicon]<br/>**Facebook**][facebookdoc]|[![Icono de API][ftpicon]<br/>**FTP**][ftpdoc]|[![Icono de API][githubicon]<br/>**GitHub**][githubdoc]|
[![Icono de API][googledriveicon]<br/>**Google Drive**][googledrivedoc]|[![Icono de API][mailchimpicon]<br/>**MailChimp**][mailchimpdoc]|[![Icono de API][microsofttranslatoricon]<br/>**Translator**][microsofttranslatordoc]|[![Icono de API][office365icon]<br/>**Office 365**<br/>**Outlook**][office365outlookdoc]|
|[![Icono de API][office365icon]<br/>**Office 365**<br/>**Usuarios**][office365usersdoc]|[![Icono de API][office365icon]<br/>**Office 365**<br/>**Vídeo**][office365videodoc]|[![Icono de API][onedriveicon]<br/>**OneDrive**][onedrivedoc]|[![Icono de API][onedriveicon]<br/>** OneDrive<br/>para la Empresa**][onedriveforbusinessdoc]|
|[![Icono de API][outlookicon]<br/>**Outlook**][outlookdoc]|[![Icono de API][projectonlineicon]<br/>**Project<br/>Online**][projectonlinedoc]|[![Icono de API][rssicon]<br/>**RSS**][rssdoc]|[![Icono de API][salesforceicon]<br/>**Salesforce**][salesforcedoc]|
|[![Icono de API][sendgridicon]<br/>**SendGrid**][sendgriddoc]|[![Icono de API][servicebusicon]<br/>**Bus de servicio**][servicebusdoc]|[![Icono de API][sftpicon]<br/>**SFTP**][sftpdoc]|[![Icono de API][sharepointicon]<br/>**SharePoint**<br/>**Online**][sharepointdoc]|
|[![Icono de API][slackicon]<br/>**Slack**<br/>][slackdoc]|[![Icono de API][smtpicon]<br/>**SMTP**][smtpdoc]|[![Icono de API][sqlicon]<br/>**SQL Azure**][sqldoc]|[![Icono de API][sqlicon]<br/>**SQL Mashup**][mashupsqldoc]|
|[![Icono de API][trelloicon]<br/>**Trello**][trellodoc]|[![Icono de API][twilioicon]<br/>**Twilio**][twiliodoc]|[![Icono de API][twittericon]<br/>**Twitter**][twitterdoc]|[![Icono de API][wunderlisticon]<br/>**Wunderlist**][wunderlistdoc]|
[![Icono de API][yammericon]<br/>**Yammer**][yammerdoc] | |

> [AZURE.NOTE] Si ha creado aplicaciones lógicas con el esquema 2014-12-01-preview, observará que los conectores de integración de empresas, como las de BizTalk, no se han enumerado. Sabemos que son importantes y trabajamos mucho para pueda disponer de ellas pronto. Aunque aún no se conoce la fecha en que estarán disponibles, debe saber que una de nuestras principales prioridades es que lleguen a los usuarios. Entretanto, puede acceder a sus [API v1 y a las API de BizTalk desde Aplicaciones lógicas](https://blogs.msdn.microsoft.com/logicapps/2016/02/25/accessing-v1-apis-and-biztalk-apis-from-logic-apps/). Gracias por su comprensión. Permanezca atento.


### Los conectores pueden ser desencadenadores
Varios conectores proporcionan desencadenadores que pueden notificar a la aplicación que se producen eventos determinados. Por ejemplo, el conector de FTP tiene el desencadenador OnUpdatedFile. Puede crear una aplicación lógica o una PowerApp o flujo que escuche al desencadenador y realice alguna acción cuando este se ponga en marcha.

Existen dos tipos de desencadenadores:

* Desencadenadores de sondeo: estos desencadenadores sondean el servicio que proceda a una frecuencia especificada para comprobar si hay nuevos datos. Cuando haya nuevos datos disponibles, se ejecutará una nueva instancia de la aplicación con los datos como entrada. Para impedir que los mismos datos se consuman varias veces, el desencadenador puede limpiar los datos que se han leído y pasado a la aplicación.
* Desencadenadores de inserción: estos desencadenadores escuchan datos en un extremo o esperan a que se produzca un evento. A continuación, desencadena una nueva instancia de la aplicación. El conector de Twitter es un ejemplo.


### Los conectores pueden ser acciones
Los conectores también pueden utilizarse como acciones dentro de las aplicaciones. Las acciones resultan útiles para buscar datos que luego se pueden utilizar en la ejecución de la aplicación. Por ejemplo, puede que necesite buscar datos de clientes de una base de datos SQL al procesar un pedido. O bien, puede que necesite escribir, actualizar o eliminar datos en una tabla de destino. Para ello, puede usar las acciones proporcionadas por los conectores. Las acciones se asignan a las operaciones que se definen en los metadatos de Swagger.


[Novedades](../app-service-logic/app-service-logic-schema-2015-08-01.md) 
[Compilación de una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md) 
[Introducción a PowerApps](../power-apps/powerapps-get-started-azure-portal.md) 
[Migración de las aplicaciones lógicas existentes a la última versión de esquema](connectors-schema-migration.md)

<!--API Documentation-->
[azureblobdoc]: ./connectors-create-api-azureblobstorage.md "Conéctese a un blob de Azure para administrar archivos en el contenedor de blobs."
[bingsearchDoc]: ./connectors-create-api-bingsearch.md "Busque en Bing para web, imágenes, noticias y vídeo."
[boxDoc]: ./connectors-create-api-box.md "Se conecta a Box y puede realizar las operaciones de cargar, obtener, eliminar, enumerar y más tareas de archivos."
[crmonlinedoc]: ./connectors-create-api-crmonline.md "Conéctese a Dynamics CRM Online y haga más con los datos de CRM Online."
[dropboxdoc]: ./connectors-create-api-dropbox.md "Se conecta a Dropbox y puede realizar las operaciones de obtener, eliminar, enumerar y más tareas de archivos."
[exceldoc]: ./connectors-create-api-excel.md "Conéctese con Excel."
[facebookdoc]: ./connectors-create-api-facebook.md "Conéctese con Facebook para publicar en una escala de tiempo, obtener una fuente de páginas y más."
[ftpdoc]: ./connectors-create-api-ftp.md "Se conecta a un servidor FTP/FTPS y realiza diferentes tareas de FTP, incluidas la carga, la obtención y la eliminación de archivos, etc."
[googledrivedoc]: ./connectors-create-api-googledrive.md "Conéctese con Google Drive e interactúe con los datos."
[microsofttranslatordoc]: ./connectors-create-api-microsofttranslator.md
[office365outlookdoc]: ./connectors-create-api-office365-outlook.md "El conector Office 365 puede enviar y recibir correos electrónicos, administrar su calendario y administrar sus contactos con su cuenta de Office 365."
[officeunifieddoc]: ./connectors-create-api-bingsearch.md
[office365usersdoc]: ./connectors-create-api-office365-users.md
[office365videodoc]: ./connectors-create-api-office365-video.md
[onedrivedoc]: ./connectors-create-api-onedrive.md "Se conecta a su Microsoft OneDrive personal y cargue, elimine, mostrar archivos, etc."
[onedriveforbusinessdoc]: ./connectors-create-api-onedriveforbusiness.md "Se conecta a su Microsoft OneDrive para la Empresa y carga, elimina y enumera los archivos y mucho más."
[outlookdoc]: ./connectors-create-api-outlook.md "Conéctese con el buzón de Outlook y acceda a su correo electrónico y mucho más."
[projectonlinedoc]: ./connectors-create-api-projectonline.md "Se conecta a Microsoft Project Online."
[rssdoc]: ./connectors-create-api-rss.md "El conector de RSS permite a los usuarios publicar y recuperar elementos de fuente. También permite a los usuarios desencadenar operaciones cuando se publica un nuevo elemento en la fuente."
[salesforcedoc]: ./connectors-create-api-salesforce.md "Se conecta a su cuenta de Salesforce y puede administrar cuentas, clientes potenciales, oportunidades y mucho más."
[sendgriddoc]: ./connectors-create-api-sendgrid.md "Se conecta a Microsoft Project Online."
[servicebusdoc]: ./connectors-create-api-servicebus.md "Puede enviar mensajes desde los temas y las colas del Bus de servicio y recibir mensajes de suscripciones y colas del Bus de servicio."
[sharepointdoc]: ./connectors-create-api-sharepointonline.md "Se conecta a SharePoint Online para administrar documentos y enumerar elementos."
[slackdoc]: ./connectors-create-api-slack.md "Conéctese a Slack y publique mensajes en los canales de Slack."
[sftpdoc]: ./connectors-create-api-sftp.md "Se conecta a SFTP y puede cargar, obtener, eliminar archivos, etc."
[githubdoc]: ./connectors-create-api-github.md "Se conecta a GitHub y puede realizar un seguimiento de problemas."
[mailchimpdoc]: ./connectors-create-api-mailchimp.md "Envía mejores correos electrónicos."
[smtpdoc]: ./connectors-create-api-smtp.md "Se conecta a un servidor SMTP y envía correo con datos adjuntos."
[sqldoc]: ./connectors-create-api-sqlazure.md "Se conecta a Base de datos de SQL Azure. Puede crear, actualizar, obtener y eliminar entradas en una tabla de base de datos SQL."
[mashupsqldoc]: ./connectors-create-api-mashupsql.md "Se conecta a SQL. Puede crear, actualizar, obtener y eliminar entradas en una tabla de base de datos SQL."
[trellodoc]: ./connectors-create-api-trello.md "Trello es la manera más libre, flexible y visual para organizar cualquier cosa con alguien."
[twiliodoc]: ./connectors-create-api-twilio.md "Se conecta a Twilio y puede enviar y recibir mensajes, obtener los números disponibles, administrar los números de teléfono entrantes y mucho más."
[twitterdoc]: ./connectors-create-api-twitter.md "Se conecta a Twitter y puede obtener escalas de tiempo, publicar tweets, etc."
[wunderlistdoc]: ./connectors-create-api-wunderlist.md "Mantiene su vida sincronizada."
[yammerdoc]: ./connectors-create-api-yammer.md "Se conecta a Yammer para publicar mensajes y obtener nuevos mensajes."

<!--Icon references-->
[blobicon]: ./media/apis-list/blobicon.png
[bingsearchicon]: ./media/apis-list/bingsearchicon.png
[boxicon]: ./media/apis-list/boxicon.png
[ftpicon]: ./media/apis-list/ftpicon.png
[githubicon]: ./media/apis-list/githubicon.png
[crmonlineicon]: ./media/apis-list/dynamicscrmicon.png
[dropboxicon]: ./media/apis-list/dropboxicon.png
[excelicon]: ./media/apis-list/excelicon.png
[facebookicon]: ./media/apis-list/facebookicon.png
[googledriveicon]: ./media/apis-list/googledriveicon.png
[mailchimpicon]: ./media/apis-list/mailchimpicon.png
[microsofttranslatoricon]: ./media/apis-list/translatoricon.png
[office365icon]: ./media/apis-list/office365icon.png
[onedriveicon]: ./media/apis-list/onedriveicon.png
[onedriveforbusinessicon]: ./media/apis-list/onedriveforbusinessicon.png
[outlookicon]: ./media/apis-list/outlookicon.png
[projectonlineicon]: ./media/apis-list/projectonlineicon.png
[rssicon]: ./media/apis-list/rssicon.png
[salesforceicon]: ./media/apis-list/salesforceicon.png
[sendgridicon]: ./media/apis-list/sendgridicon.png
[servicebusicon]: ./media/apis-list/servicebusicon.png
[sftpicon]: ./media/apis-list/sftpicon.png
[sharepointicon]: ./media/apis-list/sharepointicon.png
[slackicon]: ./media/apis-list/slackicon.png
[smtpicon]: ./media/apis-list/smtpicon.png
[sqlicon]: ./media/apis-list/sqlicon.png
[mashupsqlicon]: ./media/apis-list/mashupsqlicon.png
[trelloicon]: ./media/apis-list/trelloicon.png
[twilioicon]: ./media/apis-list/twilioicon.png
[twittericon]: ./media/apis-list/twittericon.png
[wunderlisticon]: ./media/apis-list/wunderlisticon.png
[yammericon]: ./media/apis-list/yammericon.png

<!---HONumber=AcomDC_0504_2016-->