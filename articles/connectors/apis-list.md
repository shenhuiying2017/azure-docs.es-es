---
title: Conectores de Azure Logic Apps | Microsoft Docs
description: "Elija entre todos los conectores disponibles administrados por Microsoft para compilar y crear aplicaciones lógicas"
services: logic-apps
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: f1f1fd50-b7f9-4d13-824a-39678619aa7a
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/21/2017
ms.author: mandia; ladocs
ms.openlocfilehash: c14ac7592efabfec8668d7437463e2d8771ee072
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="connectors-list"></a>Lista de conectores
> [!TIP]
> La [lista completa de la A a la Z](#az) (en este tema) enumera todos los conectores disponibles que puede usar con Logic Apps. En [Detalles del conector](/connectors/) se enumeran no solo los desencadenadores y las acciones definidos en Swagger, sino también los límites de cada conector.

Los conectores son una parte integral de la creación de aplicaciones lógicas. Mediante el uso de conectores, puede expandir las aplicaciones locales y en la nube para realizar diferentes operaciones con los datos que cree, y con los que ya tiene. Los conectores están disponibles en las siguientes categorías: 

* **Conectores estándar**: automáticamente disponibles e incluidos al usar aplicaciones lógicas. Algunos ejemplos son Service Bus, Power BI, Oracle Database o OneDrive, entre otros muchos.

* **Conectores de cuenta de integración**: disponibles al adquirir una cuenta de integración. Mediante estos conectores, puede transformar y validar código XML, procesar mensajes de negocio a negocio con AS2, X12 o EDIFACT, y codificar y descodificar archivos sin formato. Si trabaja con BizTalk Server, estos conectores son adecuados para expandir los flujos de trabajo de BizTalk en Azure.  

    BizTalk Server también tiene un [adaptador de Logic Apps](https://msdn.microsoft.com/library/mt787163.aspx), que incluye la recepción desde una aplicación lógica y el envío a una aplicación lógica.

* **Conectores de empresa**: incluye MQ y SAP. Disponible con un costo adicional. 

[Precios de Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/) y [Modelo de precios de Logic Apps](../logic-apps/logic-apps-pricing.md) proporcionan más detalles sobre los costos. 

## <a name="popular-connectors"></a>Conectores populares
Hay miles de aplicaciones y millones de ejecuciones que están procesando correctamente datos e información mediante estos conectores. La tabla siguiente enumera los más populares y algunos favoritos de nuestros usuarios:

| |  |  |  |
| --- | --- | --- | --- |
| [![API Icon][AzureBlobStorageicon]<br/>**Azure Blob<br/>Storage**][AzureBlobStoragedoc] | Si desea automatizar algunas tareas con la cuenta de almacenamiento, debe mirar este conector. Admite operaciones CRUD (crear, leer, actualizar y eliminar). | [![API Icon][Azure-Functionsicon]<br/>**Azure Functions**][azure-functionsdoc] | Cree funciones que ejecutan fragmentos de código personalizados de C# o node.js y, a continuación, use estas funciones en las aplicaciones lógicas.  |
| [![API Icon][Dynamics-365icon]<br/>**Dynamics 365<br/>CRM Online**][Dynamics-365doc] | Uno de los conectores más solicitados. Dispone de desencadenadores y acciones para ayudar a automatizar los flujos de trabajo con clientes potenciales, y mucho más. | [![API Icon][Event-hubs-icon]<br/>**Event Hubs**][event-hubs-doc] | Consuma y publique eventos en un centro de eventos. Por ejemplo, puede obtener una salida de su aplicación lógica con Event Hubs y enviarla luego a un proveedor de análisis en tiempo real. |
| [![API Icon][FTPicon]<br/>**FTP**][FTPdoc] | Si el servidor FTP es accesible desde Internet, puede automatizar los flujos de trabajo para trabajar con archivos y carpetas. <br/><br/>SFTP también está disponible con el conector SFTP. | [![API Icon][HTTPicon]<br/>**HTTP**][httpdoc] | Utilice aplicaciones lógicas que se comuniquen con cualquier punto de conexión mediante HTTP. |
| [![API Icon][Office-365-Outlookicon]<br/>**Office 365<br/>Outlook**][office365-outlookdoc] | Gran número de desencadenadores y muchas más acciones para utilizar el correo electrónico y los eventos de Office 365 en los flujos de trabajo. <br/><br/>Este conector incluye una acción de *correo electrónico de aprobación* para aprobar solicitudes de vacaciones, informes de gastos y otros. <br/><br/>Los usuarios de Office 365 también están disponibles con el conector Usuarios de Office 365.| [![API Icon][HTTP-Requesticon]<br/>**Request / Response**][HTTP-Requestdoc] | Este conector proporciona una dirección URL HTTPS. Cuando la aplicación lógica recibe una solicitud para esta dirección URL, se inicia la aplicación lógica. |
| [![API Icon][Salesforceicon]<br/>**Salesforce**][salesforcedoc] | Inicie sesión fácilmente en su cuenta de Salesforce para obtener acceso a objetos, como clientes potenciales y mucho más. |  [![API Icon][Service-Busicon]<br/>**Service Bus**][Service-Busdoc] | El conector más popular entre las aplicaciones lógicas; incluye desencadenadores y acciones para mensajería asincrónica y publicación y suscripción con colas, suscripciones y temas. |
|  [![API Icon][SharePointicon]<br/>**SharePoint<br/>Online**][SharePointdoc] | Si utiliza SharePoint y puede beneficiarse de la automatización, se recomienda revisar este conector. Puede utilizarse con una instancia local de SharePoint y con SharePoint Online. | [![API Icon][SQL-Servericon]<br/>**SQL Server**][SQL-Serverdoc] | Uno de los conectores más usados; puede conectarse a una instancia local de SQL Server y a una instancia de Azure SQL Database. | 
| [![API Icon][Twittericon]<br/>**Twitter**][Twitterdoc] | Inicie sesión fácilmente con una cuenta de Twitter e inicie un flujo de trabajo cuando se publique un nuevo tweet. Después, guarde estos tweets en una base de datos SQL o en una lista de SharePoint. | | | 

## <a name="integration-account-connectors"></a>Conectores de la cuenta de integración 

Enterprise Integration Pack (EIP) incluye conectores bien conocidos en la comunidad de BizTalk Server. Cuando compre una [cuenta de integración](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), también obtendrá los conectores siguientes: 

|  |  |  |  |
| --- | --- | --- | --- |
| [![API Icon][as2icon]<br/>**Descodificación de</br> AS2**][as2decode] | [![API Icon][as2icon]<br/>**Codificación de</br> AS2**][as2encode] | [![API Icon][x12icon]<br/>**Descodificación de</br> EDIFACT**][EDIFACTdecode] | [![API Icon][x12icon]<br/>**Codificación de</br> EDIFACT**][EDIFACTencode] |
[![API Icon][flatfileicon]<br/>**Codificación de</br> archivos planos**][flatfiledoc] | [![API Icon][flatfiledecodeicon]<br/>**Descodificación</br> de archivos planos**][flatfiledecodedoc] | [![API Icon][integrationaccounticon]<br/>**Cuenta de<br/>integración**][integrationaccountdoc] | [![API Icon][xmltransformicon]<br/>**Transformación<br/>XML**][xmltransformdoc] |
| [![API Icon][x12icon]<br/>**Descodificación de</br> X12**][x12decode] | [![API Icon][x12icon]<br/>**Codificación de</br> X12**][x12encode] | [![API Icon][xmlvalidateicon]<br/>**Validación <br/>de XML**][xmlvalidatedoc] | |

## <a name="enterprise-connectors"></a>Conectores de empresa

Conecte con sus aplicaciones empresariales desde sus aplicaciones lógicas.

|  |  |
| --- | --- |
|[![API Icon][MQicon]<br/>**MQ**][mqdoc]|[![API Icon][SAPicon]<br/>**SAP**][sapconnector]|


## <a name="az"></a>Lista completa A-Z

En [Detalles del conector](/connectors/) se enumeran no solo los desencadenadores y las acciones definidos en Swagger, sino también los límites de cada conector.

| | | | | | | | | | | | | |
|---|---|---|---|---|---|---|---|---|---|---|---|---|
| [**1**](#1) | [**A**](#a) | [**B**](#b) | [**C**](#c) | [**D**](#d) | [**E**](#e) | [**F**](#f) | [**G**](#g) | [**H**](#h) | [**I**](#i) | [**J**](#j) | [**L**](#l) | [**M**](#m) |
| [**N**](#n) | [**O**](#o) | [**P**](#p) | [**R**](#r) | [**S**](#s) | [**T**](#t) | [**U**](#u) | [**V**](#v) | [**W**](#w) | [**X**](#x) | [**Y**](#y) | [**Z**](#z) | | 

| | |
|---|---|
|<a name="1"></a>10to8 Appointment Scheduling<br/><br/><a name="a"></a>Act!<br/>Adobe Creative Cloud<br/>appFigures<br/>[AS2][as2doc]<br/>Asana<br/>Azure Active Directory (AD)<br/>Azure API Management<br/>Azure App Services<br/>Aplicación de Azure<br/>Azure Automation<br/>[Azure Blob Storage][azureblobstoragedoc]<br/>Azure Data Lake<br/>Azure DocumentDB (Cosmos DB)<br/>[Azure Functions][azure-functionsdoc]<br/>[Azure Logic Apps][nested-logic-appdoc]<br/>AzureML<br/>Colas de Azure<br/>Administrador de recursos de Azure<br/>[Azure SQL Database][sql-serverdoc]<br/><br/><a name="b"></a>Basecamp 2<br/>Basecamp 3<br/>Batch<br/>Benchmark Email<br/>Bing Search<br/>Bitbucket<br/>Bitly<br/>BizTalk Server<br/>Blogger<br/>Box<br/>Buffer<br/><br/><a name="c"></a>Calendly<br/>Campfire<br/>Capsule CRM<br/>Chatter<br/>Formularios de Cognito<br/>Cognitive Services Computer Vision API<br/>Cognitive Services Face API<br/>Cognitive Services LUIS<br/>Cognitive Services Text Analytics<br/>Common Data Service<br/>Conversión de contenido<br/>Control-Terminate<br/>[API y aplicaciones web personalizadas][api/web-appdoc]<br/><br/><a name="d"></a>Data Operations<br/>[DB2][db2doc]<br/>Disqus<br/>DocuSign<br/>Do Until<br/>Dropbox<br/>[Dynamics 365 CRM Online][Dynamics-365doc]<br/>Dynamics 365 for Financials<br/>Dynamics 365 for Operations<br/>Dynamics NAV<br/><br/><a name="e"></a>Easy Redmine<br/>EDIFACT<br/>[Event Hubs][event-hubs-doc]<br/>Eventbrite<br/><br/><a name="f"></a>Facebook<br/>[File System][filesystemdoc]<br/>[Flat File][flatfiledoc]<br/>FreshBooks<br/>Freshdesk<br/>FreshService<br/>[FTP][ftpdoc]<br/><br/><a name="g"></a>GitHub<br/>Gmail<br/>Google Calendar<br/>Google Contacts<br/>Google Drive<br/>Google Sheets<br/>Google Tasks<br/>GoToMeeting<br/>GoToTraining<br/>GoToWebinar<br/><br/><a name="h"></a>Harvest<br/>HelloSign<br/>HipChat<br/>[HTTP][httpdoc]<br/>[HTTP + Swagger][http-swaggerdoc]<br/>[HTTP Webhook][webhookdoc]<br/><br/><a name="i"></a>[Informix][informixdoc]<br/>Infusionsoft<br/>Inoreader<br/>Insightly<br/>Instagram<br/>Instapaper<br/>Integration Account<br/>Intercom | <a name="j"></a>JotForm<br/>JIRA<br/><br/><a name="l"></a>LeanKit<br/>LiveChat<br/><br/><a name="m"></a>MailChimp<br/>Mandrill<br/>Mediano<br/>Microsoft Forms<br/>Equipos de Microsoft<br/>Microsoft Translator<br/>[MQ][mqdoc]<br/>MSN Weather<br/>Muhimbi PDF<br/>MySQL<br/><br/><a name="n"></a>Nexmo<br/><br/><a name="o"></a>[Office 365 Outlook][office365-outlookdoc]<br/>Usuarios de Office 365<br/>Office 365 Video<br/>OneDrive<br/>OneDrive para la Empresa<br/>OneNote (Business)<br/>[Oracle Database][oracle-db-doc]<br/>Administrador de clientes de Outlook<br/>Outlook Tasks<br/>Outlook.com<br/><br/><a name="p"></a>PagerDuty<br/>Parserr<br/>Paylocity<br/>Pinterest<br/>Pipedrive<br/>Pivotal Tracker<br/>Planner<br/>PostgreSQL<br/>Power BI<br/>Project Online<br/><br/><a name="r"></a>Redmine<br/>[Request / Response][http-requestdoc]<br/>RSS<br/><br/><a name="s"></a>[Salesforce][salesforcedoc]<br/>[SAP Application Server][sapconnector]<br/>[SAP Message Server][sapconnector]<br/>[Schedule][recurrencedoc]<br/>Scope<br/>SendGrid<br/>Envío de mensajes a Batch<br/>[Service Bus][service-busdoc]<br/>SFTP<br/>[SharePoint Online][sharepointdoc]<br/>[SharePoint Server][sharepointserver]<br/>Slack<br/>Smartsheet<br/>SMTP<br/>SparkPost<br/>[SQL Server][sql-serverdoc]<br/>Stripe<br/>SurveyMonkey<br/>Switch Case<br/><br/><a name="t"></a>Proyectos de trabajo en equipo<br/>Teradata<br/>Todoist<br/>Toodledo<br/>[Transformar XML][xmltransformdoc]<br/>Trello<br/>Twilio<br/>[Twitter][twitterdoc]<br/>Typeform<br/><br/><a name="u"></a>UserVoice<br/><br/><a name="v"></a>Variables<br/>Vimeo<br/>Visual Studio Team Services<br/><br/><a name="w"></a>WebMerge<br/>WordPress<br/>Wunderlist<br/><br/><a name="x"></a>[X12][x12doc]<br/>[Validación XML][xmlvalidatedoc]<br/><br/><a name="y"></a>Yammer<br/>YouTube<br/><br/><a name="z"></a>Zendesk |

> [!TIP]
> Para empezar a usar Azure Logic Apps antes de suscribirse para obtener una cuenta de Azure, vaya a [Probar Logic Apps](https://tryappservice.azure.com/?appservice=logic). Podrá crear inmediatamente una aplicación lógica de inicio de corta duración. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

## <a name="connectors-as-triggers-and-actions"></a>Conectores como desencadenadores y acciones

Un **desencadenador** inicia o ejecuta una instancia de la aplicación lógica. Algunos conectores proporcionan desencadenadores que envían una notificación a la aplicación cuando se producen determinados eventos. Por ejemplo, el conector FTP tiene el desencadenador `OnUpdatedFile` que inicia la aplicación lógica cuando se actualiza un archivo. 

Las aplicaciones lógicas incluyen los siguientes tipos de desencadenadores:  

* *Desencadenadores de sondeo*: estos desencadenadores sondean el servicio que proceda a una frecuencia especificada para comprobar si hay nuevos datos. 

    Cuando hay nuevos datos, se ejecuta una nueva instancia de la aplicación lógica con los datos como entrada. 

* *Desencadenadores push*: estos desencadenadores escuchan datos en un punto de conexión o esperan a que se produzca un evento para desencadenar una nueva instancia de su aplicación lógica.

* *Desencadenador de periodicidad*: este desencadenador crea una instancia de la aplicación lógica según una programación prescrita.

Los conectores también proporcionan **acciones** que puede usar en el flujo de trabajo. Por ejemplo, la aplicación lógica puede buscar datos y, después, usar estos datos en la aplicación lógica. Más específicamente, puede buscar datos de cliente en una base de datos SQL y, a continuación, usar estos datos de cliente para generar el flujo de trabajo. 

> [!TIP]
> [Información general de conectores](connectors-overview.md) proporciona más detalles sobre desencadenadores y acciones. 


## <a name="message-manipulation-actions"></a>Acciones de manipulación de mensajes

Las aplicaciones lógicas incluyen acciones integradas que pueden cambiar o manipular los datos de carga útil. El conector integrado **Data Operations** incluye las siguientes acciones: 

| | |
|---|---|
| **Compose** | Generar valores u objetos para su uso posterior o en el momento de creación del flujo de trabajo. Por ejemplo, puede crear un objeto JSON con valores de varios pasos, o calcular una constante para hacer referencia a ella más adelante en la ejecución de una aplicación lógica. |
| **Crear tabla CSV**<br/>**Crear tabla HTML** | Convertir una matriz de conjunto de resultados en una tabla HTML o CSV. Por ejemplo, agregar la acción "Enumerar registros" de CRM y agregar un filtro para los registros incorporados hoy. A continuación, enviar los resultados como una tabla HTML en un correo electrónico. |
| **Filtrar matriz** (consulta) | Filtrar las entradas que le interesen de un conjunto de resultados. Por ejemplo, buscar todos los tweets que contengan `#Azure` y, después, "filtrar" los tweets devueltos para devolver únicamente aquellos que cumplan la condición `Tweeted_by_followers > 50`. |
| **Join** | Unir una matriz con algún delimitador. Por ejemplo, la operación Detectar frases clave devuelve una matriz de frases clave. Las puede "unir" con un carácter `,`, o algo similar. Por lo que en lugar de `["Some", "Phrase"]`, tendrá `"Some, Phrase"`. |
| **Análisis del archivo JSON** | Analizar y acceder a los valores de un objeto JSON en el diseñador. Por ejemplo, si Azure Functions devuelve una carga útil JSON, puede analizarla para acceder a las propiedades JSON en un paso posterior. La acción también valida que el código JSON coincide con el esquema especificado en tiempo de ejecución. | 
| **Select** | Seleccione algunas propiedades de una matriz para su posterior procesamiento. Si usa la acción "Enumerar registros" y SQL devuelve 15 columnas, seleccione algunas para su procesamiento posterior. La salida es una matriz que solo contiene las propiedades que ha seleccionado. |

## <a name="custom-connectors-and-azure-certification"></a>Conectores personalizados y certificación de Azure 

Para llamar a las API que ejecutan código personalizado o no están disponibles como conectores, puede extender la plataforma de Logic Apps mediante la [creación de aplicaciones de API basadas en REST como conectores personalizados](../logic-apps/logic-apps-create-api-app.md). 

Si desea que las aplicaciones de API personalizadas se conviertan en públicas y estén disponibles para usarlas en Azure, envíe las nominaciones al [programa Microsoft Azure Certified](https://azure.microsoft.com/marketplace/programs/certified/logic-apps/).

## <a name="get-help"></a>Obtener ayuda

Para formular preguntas, o responderlas, y ver lo que hacen otros usuarios de Azure Logic Apps, visite el [foro de Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

Para ayudar a mejorar Azure Logic Apps y los conectores, vote las ideas que encontrará en el [sitio de comentarios de usuario de Azure Logic Apps](http://aka.ms/logicapps-wish) o envíe ideas nuevas.

¿Falta un tema sobre conectores o algún dato que considere importante? Si es así, puede ayudarnos. Agréguelo a nuestros temas existentes o escriba el suyo propio. Nuestra documentación es de código abierto y se hospeda en GitHub. Para comenzar, vaya a nuestro [repositorio de GitHub](https://github.com/Microsoft/azure-docs). 

## <a name="next-steps"></a>Pasos siguientes
* [Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS](../logic-apps/logic-apps-create-a-logic-app.md)
* [Creación de API personalizadas para las aplicaciones lógicas](../logic-apps/logic-apps-create-api-app.md)
* [Supervisar las aplicaciones lógicas](../logic-apps/logic-apps-monitor-your-logic-apps.md)

<!--Connectors Documentation-->

[api/web-appdoc]: ../logic-apps/logic-apps-custom-hosted-api.md "Integrar aplicaciones lógicas con App Service API Apps"
[azureblobstoragedoc]: ./connectors-create-api-azureblobstorage.md "Administrar archivos del contenedor de blobs con el conector de Azure Blob Storage"
[azure-functionsdoc]: ../logic-apps/logic-apps-azure-functions.md "Integración de aplicaciones lógicas con Azure Functions"
[db2doc]: ./connectors-create-api-db2.md "Conectar con una instancia de IBM DB2 en la nube o local. Actualizar una fila, obtener una tabla, etc."
[Dynamics-365doc]: ./connectors-create-api-crmonline.md "Conectar con Dynamics CRM Online para trabajar con datos de CRM Online"
[event-hubs-doc]: ./connectors-create-api-azure-event-hubs.md "Conexión a Azure Event Hubs. Recibir y enviar eventos entre instancias de Logic Apps y Event Hubs"
[filesystemdoc]: ../logic-apps/logic-apps-using-file-connector.md "Conectar con un sistema de archivos local"
[ftpdoc]: ./connectors-create-api-ftp.md "Conectar con un servidor FTP/FTPS para tareas de FTP, incluidas la carga, la obtención y la eliminación de archivos, entre otras"
[httpdoc]: ./connectors-native-http.md "Realizar llamadas HTTP con el conector HTTP"
[http-requestdoc]: ./connectors-native-reqres.md "Agregar acciones para las solicitudes HTTP y respuestas a las aplicaciones lógicas"
[http-swaggerdoc]: ./connectors-native-http-swagger.md "Realizar llamadas HTTP con el conector HTTP y Swagger"
[informixdoc]: ./connectors-create-api-informix.md "Conectar con una instancia de Informix en la nube o local. Leer una fila, enumerar las tablas, etc."
[nested-logic-appdoc]: ../logic-apps/logic-apps-http-endpoint.md "Integrar aplicaciones lógicas con flujos de trabajo anidados"
[office365-outlookdoc]: ./connectors-create-api-office365-outlook.md "Conectar con su cuenta de Office 365. Enviar y recibir correos electrónicos, administrar su calendario y los contactos, y más"
[oracle-db-doc]: ./connectors-create-api-oracledatabase.md "Conectarse a una base de datos de Oracle para agregar, insertar, eliminar filas y muchas otras acciones"
[mqdoc]: ./connectors-create-api-mq.md "Conectarse a MQ local o en Azure y enviar y recibir mensajes"
[recurrencedoc]:  ./connectors-native-recurrence.md "Desencadenar acciones que se repiten para aplicaciones lógicas"
[salesforcedoc]: ./connectors-create-api-salesforce.md "Conectar con su cuenta de Salesforce. Administrar cuentas, clientes potenciales, oportunidades, etc."
[sapconnector]: ../logic-apps/logic-apps-using-sap-connector.md "Conectar con un servidor SAP local"
[service-busdoc]: ./connectors-create-api-servicebus.md "Envío de mensajes desde los temas y las colas de Service Bus y recepción de mensajes desde suscripciones y colas de Service Bus"
[sharepointdoc]: ./connectors-create-api-sharepointonline.md "Conectar con SharePoint Online. Administrar documentos, elementos de lista, etc."
[sharepointserver]: ./connectors-create-api-sharepointserver.md "Conectarse a SharePoint en el servidor local. Administrar documentos, elementos de lista, etc."
[sql-serverdoc]: ./connectors-create-api-sqlazure.md "Conectar con Azure SQL Database o SQL Server. Crear, actualizar, obtener y eliminar entradas en una tabla de base de datos SQL."
[twitterdoc]: ./connectors-create-api-twitter.md "Conectar con Twitter. Obtener líneas de tiempo, publicar twits, etc."
[webhookdoc]: ./connectors-native-webhook.md "Agregar desencadenadores y acciones de Webhook para las aplicaciones lógicas"

[as2doc]: ../logic-apps/logic-apps-enterprise-integration-as2.md "Más información sobre AS2 para integración empresarial."
[x12doc]: ../logic-apps/logic-apps-enterprise-integration-x12.md "Más información sobre X12 para integración empresarial."
[flatfiledoc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Más información sobre archivos planos para integración empresarial."
[flatfiledecodedoc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Más información sobre archivos planos para integración empresarial."
[xmlvalidatedoc]: ../logic-apps/logic-apps-enterprise-integration-xml-validation.md "Más información sobre validación XML para integración empresarial."
[xmltransformdoc]: ../logic-apps/logic-apps-enterprise-integration-transform.md "Más información sobre transformaciones para integración empresarial."
[as2decode]: ../logic-apps/logic-apps-enterprise-integration-as2-decode.md "Más información sobre la descodificación AS2 para integración empresarial"
[as2encode]:../logic-apps/logic-apps-enterprise-integration-as2-encode.md "Más información sobre la codificación AS2 para integración empresarial"
[X12decode]: ../logic-apps/logic-apps-enterprise-integration-X12-decode.md "Más información sobre la descodificación X12 para integración empresarial"
[X12encode]: ../logic-apps/logic-apps-enterprise-integration-X12-encode.md "Más información sobre la codificación X12 para integración empresarial"
[EDIFACTdecode]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-decode.md "Más información sobre la descodificación EDIFACT para integración empresarial"
[EDIFACTencode]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-encode.md "Más información sobre la codificación EDIFACT para integración empresarial"
[integrationaccountdoc]: ../logic-apps/logic-apps-enterprise-integration-metadata.md "Búsqueda de esquemas, mapas o asociados en su cuenta de integración"


[boxDoc]: ./connectors-create-api-box.md "Conectar con Box. Cargar, obtener, eliminar y enumerar archivos, entre otras operaciones."
[delaydoc]: ./connectors-native-delay.md "Realizar acciones diferidas"
[dropboxdoc]: ./connectors-create-api-dropbox.md "Conectar con Dropbox. Cargar, obtener, eliminar y enumerar archivos, entre otras operaciones."
[facebookdoc]: ./connectors-create-api-facebook.md "Conectar con Facebook. Publicar en una línea de tiempo, obtener una fuente de páginas y más."
[githubdoc]: ./connectors-create-api-github.md "Conectar con GitHub y realizar un seguimiento de los problemas"
[google-drivedoc]: ./connectors-create-api-googledrive.md "Conectar con Google Drive para trabajar con sus datos"
[google-sheetsdoc]: ./connectors-create-api-googlesheet.md "Conectar con Google Sheets para modificar sus hojas"
[google-tasksdoc]: ./connectors-create-api-googletasks.md "Conectar con Google Tasks para administrar las tareas"
[google-calendardoc]: ./connectors-create-api-googlecalendar.md "Conecta con Google Calendar y puede administrar el calendario."
[http-responsedoc]: ./connectors-native-reqres.md "Agregar acciones para las solicitudes HTTP y respuestas a las aplicaciones lógicas"
[instagramdoc]: ./connectors-create-api-instagram.md "Conectar con Instagram. Desencadenar eventos o realizar acciones en ellos"
[mailchimpdoc]: ./connectors-create-api-mailchimp.md "Conectar con su cuenta de MailChimp. Administrar y automatizar los mensajes de correo"
[mandrilldoc]: ./connectors-create-api-mandrill.md "Conectar con Mandrill para realizar comunicaciones"
[microsoft-translatordoc]: ./connectors-create-api-microsofttranslator.md "Conectar con Microsoft Translator. Traducir texto, detectar idiomas, etc." 
[office365-usersdoc]: ./connectors-create-api-office365-users.md 
[office365-videodoc]: ./connectors-create-api-office365-video.md "Obtener información de vídeo, listas de vídeo y canales, y direcciones URL de reproducción de vídeos de Office 365"
[onedrivedoc]: ./connectors-create-api-onedrive.md "Conectar con su instancia personal de Microsoft OneDrive. Cargar, eliminar y enumerar archivos, entre otras tareas"
[onedrive-for-businessdoc]: ./connectors-create-api-onedriveforbusiness.md "Conectar con su instancia de empresa de Microsoft OneDrive. Cargar, eliminar y enumerar archivos, entre otras tareas"
[outlook.comdoc]: ./connectors-create-api-outlook.md "Conectar con el buzón de Outlook. Administrar el correo electrónico, los calendarios y los contactos, entre otras tareas"
[project-onlinedoc]: ./connectors-create-api-projectonline.md "Conectar con Microsoft Project Online. Administrar proyectos, tareas, recursos etc."
[querydoc]: ./connectors-native-query.md "Seleccionar y filtrar matrices con la acción Consulta"
[rssdoc]: ./connectors-create-api-rss.md "Publicar y recuperar elementos de fuente, desencadenar operaciones cuando se publica un nuevo elemento en una fuente RSS."
[sendgriddoc]: ./connectors-create-api-sendgrid.md "Conectar con SendGrid. Enviar correo electrónico y administrar listas de destinatarios"
[sftpdoc]: ./connectors-create-api-sftp.md "Conectar con su cuenta SFTP. Cargar, obtener y eliminar archivos, entre otras operaciones."
[slackdoc]: ./connectors-create-api-slack.md "Conectar con Slack y publicar mensajes en los canales de Slack"
[smtpdoc]: ./connectors-create-api-smtp.md "Conectar con un servidor SMTP y enviar correo con datos adjuntos"
[sparkpostdoc]: ./connectors-create-api-sparkpost.md "Conectar con SparkPost para la comunicación"
[trellodoc]: ./connectors-create-api-trello.md "Conectar con Trello. Administrar proyectos y organizar todo con todos"
[twiliodoc]: ./connectors-create-api-twilio.md "Conectar con Twilio. Enviar y recibir mensajes, obtener los números disponibles, administrar los números de teléfono entrantes y mucho más."
[wunderlistdoc]: ./connectors-create-api-wunderlist.md "Conectar con Wunderlist. Administrar tareas y listas de tareas pendientes, mantenerse sincronizado, y mucho más"
[yammerdoc]: ./connectors-create-api-yammer.md "Conectar con Yammer. Publicar mensajes, obtener nuevos mensajes, etc."
[youtubedoc]: ./connectors-create-api-youtube.md "Conectar con YouTube. Administrar sus vídeos y canales"


<!--Icon references-->
[appFiguresicon]: ./media/apis-list/appfigures.png
[Asanaicon]: ./media/apis-list/asana.png
[Azure-Automation-icon]: ./media/apis-list/azure-automation.png
[AzureBlobStorageicon]: ./media/apis-list/azureblob.png
[Azure-Data-Lake-icon]: ./media/apis-list/azure-data-lake.png
[Azure-DocumentDBicon]: ./media/apis-list/azure-documentdb.png
[Azure-MLicon]: ./media/apis-list/azureml.png
[Azure-Resource-Manager-icon]: ./media/apis-list/azure-resource-manager.png
[Azure-Queues-icon]: ./media/apis-list/azure-queues.png
[Basecamp-3icon]: ./media/apis-list/basecamp.png
[Bitbucket-icon]: ./media/apis-list/bitbucket.png
[Bitlyicon]: ./media/apis-list/bitly.png
[BizTalk-Servericon]: ./media/apis-list/biztalk.png
[Bloggericon]: ./media/apis-list/blogger.png
[Boxicon]: ./media/apis-list/box.png
[Campfireicon]: ./media/apis-list/campfire.png
[Cognitive-Services-Text-Analyticsicon]: ./media/apis-list/cognitiveservicestextanalytics.png
[DB2icon]: ./media/apis-list/db2.png
[Dropboxicon]: ./media/apis-list/dropbox.png
[Dynamics-365icon]: ./media/apis-list/dynamicscrmonline.png
[Dynamics-365-for-Financialsicon]: ./media/apis-list/madeira.png
[Dynamics-365-for-Operationsicon]: ./media/apis-list/dynamicsax.png
[Easy-Redmineicon]: ./media/apis-list/easyredmine.png
[Event-Hubs-icon]: ./media/apis-list/eventhubs.png
[Facebookicon]: ./media/apis-list/facebook.png
[FTPicon]: ./media/apis-list/ftp.png
[GitHubicon]: ./media/apis-list/github.png
[Google-Calendaricon]: ./media/apis-list/googlecalendar.png
[Google-Driveicon]: ./media/apis-list/googledrive.png
[Google-Sheetsicon]: ./media/apis-list/googlesheet.png
[Google-Tasksicon]: ./media/apis-list/googletasks.png
[HideKeyicon]: ./media/apis-list/hidekey.png
[HipChaticon]: ./media/apis-list/hipchat.png
[Informixicon]: ./media/apis-list/informix.png
[Insightlyicon]: ./media/apis-list/insightly.png
[Instagramicon]: ./media/apis-list/instagram.png
[Instapapericon]: ./media/apis-list/instapaper.png
[JIRAicon]: ./media/apis-list/jira.png
[MailChimpicon]: ./media/apis-list/mailchimp.png
[Mandrillicon]: ./media/apis-list/mandrill.png
[Microsoft-Translatoricon]: ./media/apis-list/microsofttranslator.png
[MQicon]: ./media/apis-list/mq.png
[Office-365-Outlookicon]: ./media/apis-list/office365.png
[Office-365-Usersicon]: ./media/apis-list/office365users.png
[Office-365-Videoicon]: ./media/apis-list/office365video.png
[OneDriveicon]: ./media/apis-list/onedrive.png
[OneDrive-for-Businessicon]: ./media/apis-list/onedriveforbusiness.png
[Oracle-DB-icon]: ./media/apis-list/oracle-db.png
[Outlook.comicon]: ./media/apis-list/outlook.png
[PagerDutyicon]: ./media/apis-list/pagerduty.png
[Pinteresticon]: ./media/apis-list/pinterest.png
[Project-Onlineicon]: ./media/apis-list/projectonline.png
[Redmineicon]: ./media/apis-list/redmine.png
[RSSicon]: ./media/apis-list/rss.png
[Common-Data-Serviceicon]: ./media/apis-list/runtimeservice.png
[Salesforceicon]: ./media/apis-list/salesforce.png
[SAPicon]: ./media/apis-list/sap.png
[SendGridicon]: ./media/apis-list/sendgrid.png
[Service-Busicon]: ./media/apis-list/servicebus.png
[SFTPicon]: ./media/apis-list/sftp.png
[SharePointicon]: ./media/apis-list/sharepointonline.png
[Slackicon]: ./media/apis-list/slack.png
[Smartsheeticon]: ./media/apis-list/smartsheet.png
[SMTPicon]: ./media/apis-list/smtp.png
[SparkPosticon]: ./media/apis-list/sparkpost.png
[SQL-Servericon]: ./media/apis-list/sql.png
[Todoisticon]: ./media/apis-list/todoist.png
[Trelloicon]: ./media/apis-list/trello.png
[Twilioicon]: ./media/apis-list/twilio.png
[Twittericon]: ./media/apis-list/twitter.png
[Vimeoicon]: ./media/apis-list/vimeo.png
[Visual-Studio-Team-Servicesicon]: ./media/apis-list/visualstudioteamservices.png
[WordPressicon]: ./media/apis-list/wordpress.png
[Wunderlisticon]: ./media/apis-list/wunderlist.png
[Yammericon]: ./media/apis-list/yammer.png
[YouTubeicon]: ./media/apis-list/youtube.png

<!-- Primitive Icons -->
[API/Web-Appicon]: ./media/apis-list/api.png
[Azure-Functionsicon]: ./media/apis-list/function.png
[Delayicon]: ./media/apis-list/delay.png
[FileSystemIcon]: ./media/apis-list/filesystem.png
[HTTPicon]: ./media/apis-list/http.png
[HTTP-Requesticon]: ./media/apis-list/request.png
[HTTP-Responseicon]: ./media/apis-list/response.png
[HTTP-Swaggericon]: ./media/apis-list/http_swagger.png
[Nested-Logic-Appicon]: ./media/apis-list/workflow.png
[Recurrenceicon]: ./media/apis-list/recurrence.png
[Queryicon]: ./media/apis-list/query.png
[Webhookicon]: ./media/apis-list/webhook.png

<!-- EIP Icons -->
[as2icon]: ./media/apis-list/as2.png
[x12icon]: ./media/apis-list/x12new.png
[flatfileicon]: ./media/apis-list/flatfileencoding.png
[flatfiledecodeicon]: ./media/apis-list/flatfiledecoding.png
[xmlvalidateicon]: ./media/apis-list/xmlvalidation.png
[xmltransformicon]: ./media/apis-list/xsltransform.png
[integrationaccounticon]: ./media/apis-list/integrationaccount.png
