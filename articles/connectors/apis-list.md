---
title: Conectores de Azure Logic Apps | Microsoft Docs
description: "Elija entre todos los conectores de Microsoft disponibles para compilar y crear aplicaciones lógicas"
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
ms.openlocfilehash: 948b91a9fabc3ab3c4d6708968a88cb9d203b171
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/19/2018
---
# <a name="connectors-list"></a>Lista de conectores
Para obtener los desencadenadores y las acciones definidas por la descripción de Swagger de cada conector, así como los límites de cada uno, consulte los [detalles del conector](/connectors/).

Los conectores son una parte integral de la creación de aplicaciones lógicas. Mediante el uso de conectores, puede expandir las aplicaciones locales y de nube para realizar diversas operaciones con los datos que cree, y con los que ya tiene. Los conectores están disponibles como acciones integradas o conectores administrados.

**Acciones integradas**: el propio motor el Logic Apps proporciona acciones integradas para comunicarse con los puntos de conexión y realizar tareas. Por ejemplo, puede usar estas acciones para llamar a los puntos de conexión HTTP, a Azure Functions y a operaciones de API Management, así como para manipular los mensajes con variables y operaciones de datos.

**Conectores administrados**: proporcionan acceso a las API de diversos servicios mediante la creación de conexiones de API que el servicio Logic Apps hospeda y administra. Los conectores administrados pertenecen a estas categorías:

* **Conectores estándar**: automáticamente disponibles e incluidos al usar aplicaciones lógicas. Algunos ejemplos son Service Bus, Power BI, OneDrive y muchos más.

* **Conectores locales**: conéctese a las aplicaciones de servidor locales mediante la [puerta de enlace de datos local][gatewaydoc]. Los conectores locales incluyen conectividad a aplicaciones de servidor, como SharePoint Server, SQL Server, Oracle DB, recursos compartidos de archivos y otras.

* **Conectores de cuenta de integración**: disponibles al adquirir una cuenta de integración. Mediante estos conectores, puede transformar y validar código XML, procesar mensajes de negocio a negocio con AS2, X12 o EDIFACT, y codificar y descodificar archivos sin formato. Si trabaja con BizTalk Server, estos conectores son adecuados para expandir los flujos de trabajo de BizTalk en Azure.  

    BizTalk Server también tiene un [adaptador de Logic Apps](https://msdn.microsoft.com/library/mt787163.aspx), que incluye la recepción desde una aplicación lógica y el envío a una aplicación lógica.

* **Conectores de empresa**: incluye MQ y SAP. Disponible con un costo adicional. 

Para más información sobre los costos, consulte los [detalles de precios](https://azure.microsoft.com/pricing/details/logic-apps/) y el [modelo de precios](../logic-apps/logic-apps-pricing.md) de Logic Apps. 

## <a name="popular-connectors"></a>Conectores populares
Hay miles de aplicaciones y millones de ejecuciones que están procesando correctamente datos e información mediante estos conectores. 

### <a name="built-in-actions"></a>Acciones integradas
El motor de Logic Apps proporciona acciones que pueden manipular datos, comunicarse a través de HTTP y controlar el flujo de la definición de aplicación lógica. Algunas de estas acciones incluyen:

| |  |  |  |
| --- | --- | --- | --- |
| [![Icono de la API][HTTPicon]<br/>**HTTP**][httpdoc] | Utilice aplicaciones lógicas que se comuniquen con cualquier punto de conexión mediante HTTP.| [![Icono de la API][Azure-Functionsicon]<br/>**Azure Functions**][azure-functionsdoc] | Cree funciones que ejecutan fragmentos de código personalizados de C# o node.js y, a continuación, use estas funciones en las aplicaciones lógicas.  |
| [![Icono de API][HTTP-Requesticon]<br/>**Solicitud**][HTTP-Requestdoc] | Proporciona una dirección URL HTTPS invocable que se usa normalmente como webhook en otras aplicaciones. Cuando la aplicación lógica recibe una solicitud para esta dirección URL, se inicia la aplicación lógica. | [![Icono de API][Recurrenceicon]<br/>**Programación**][recurrencedoc] | Inicia aplicaciones lógicas según programaciones de periodicidad sencillas o complejas. Por ejemplo, cree programaciones tan sencillas como repetir cada día a repetir cada hora el último viernes de cada mes entre las 9 de la mañana y las 5 de la tarde. |
| [![Icono de API][CallLogicApp-icon]<br/>**Llamada<br/>Logic App**][nested-logic-appdoc] | Llama a una aplicación de lógica anidada. Se puede llamar a cualquier aplicación lógica con un desencadenador de solicitud como una aplicación lógica anidada.| [![Icono de API][API/Web-Appicon]<br/>**Aplicación de API**][api/web-appdoc] | Llama a una aplicación de API de App Service. El servicio API Apps con Swagger se representa igual que cualquier otra acción de primera clase.|

### <a name="standard-connectors"></a>Conectores estándar
La tabla siguiente enumera los más populares y algunos favoritos de nuestros usuarios:

| |  |  |  |
| --- | --- | --- | --- |
| [![Icono de la API][AzureBlobStorageicon]<br/>**Azure Blob<br/>Storage**][AzureBlobStoragedoc] | Si desea automatizar algunas tareas con la cuenta de almacenamiento, debe mirar este conector. Admite operaciones CRUD (crear, leer, actualizar y eliminar). | [![Icono de la API][Dynamics-365icon]<br/>**Dynamics 365<br/>CRM Online**][Dynamics-365doc] | Uno de los conectores más solicitados. Dispone de desencadenadores y acciones para ayudar a automatizar los flujos de trabajo con clientes potenciales, y mucho más. |
| [![Icono de la API][Event-Hubs-icon]<br/>**Event Hubs**][event-hubs-doc] | Consuma y publique eventos en un centro de eventos. Por ejemplo, puede obtener una salida de su aplicación lógica con Event Hubs y enviarla luego a un proveedor de análisis en tiempo real. | [![Icono de la API][FTPicon]<br/>**FTP**][FTPdoc] | Si el servidor FTP es accesible desde Internet, puede automatizar los flujos de trabajo para trabajar con archivos y carpetas. <br/><br/>SFTP también está disponible con el conector SFTP. |
| [![Icono de la API][Office-365-Outlookicon]<br/>**Office 365<br/>Outlook**][office365-outlookdoc] | Gran número de desencadenadores y muchas más acciones para utilizar el correo electrónico y los eventos de Office 365 en los flujos de trabajo. <br/><br/>Este conector incluye una acción de *correo electrónico de aprobación* para aprobar solicitudes de vacaciones, informes de gastos y otros. <br/><br/>Los usuarios de Office 365 también están disponibles con el conector Usuarios de Office 365.| [![Icono de la API][Salesforceicon]<br/>**Salesforce**][salesforcedoc] | Inicie sesión fácilmente en su cuenta de Salesforce para obtener acceso a objetos, como clientes potenciales y mucho más. | 
| [![Icono de la API][Service-Busicon]<br/>**Service Bus**][Service-Busdoc] | El conector más popular entre las aplicaciones lógicas; incluye desencadenadores y acciones para mensajería asincrónica y publicación y suscripción con colas, suscripciones y temas. |  [![Icono de la API][SharePointicon]<br/>**SharePoint<br/>Online**][SharePointdoc] | Si utiliza SharePoint y puede beneficiarse de la automatización, se recomienda revisar este conector. Puede utilizarse con una instancia local de SharePoint y con SharePoint Online. |
| [![Icono de la API][SQL-Servericon]<br/>**SQL Server**][SQL-Serverdoc] | Uno de los conectores más usados; puede conectarse a una instancia local de SQL Server y a una instancia de Azure SQL Database. | [![Icono de la API][Twittericon]<br/>**Twitter**][Twitterdoc] | Inicie sesión fácilmente con una cuenta de Twitter e inicie un flujo de trabajo cuando se publique un nuevo tweet. Después, guarde estos tweets en una base de datos SQL o en una lista de SharePoint. | 

### <a name="on-premises-connectors"></a>Conectores locales 

Los conectores locales proporcionan acceso a datos de servidores locales.  La creación de una conexión a un servidor local requiere una [puerta de enlace de datos local][gatewaydoc] que proporcione un canal de comunicación seguro sin necesidad de configurar la infraestructura de red.  Algunos de los conectores son:

|  |  |  |  |
| --- | --- | --- | --- |
| [![Icono de API][db2icon]<br/>**DB2**][db2doc] | [![Icono de API][oracle-DB-icon]<br/>**Oracle DB**][oracle-db-doc] | [![Icono de API][sharepointicon]<br/>**SharePoint</br> Server**][sharepointserver] | [![Icono de API][filesystem-icon]<br/>**File</br> System**][filesystemdoc] |
[![Icono de API][sql-servericon]<br/>**SQL Server</br>**][sql-serverdoc] | ![Icono de API][Biztalk-Servericon]<br/>**BizTalk</br> Server**| |

### <a name="integration-account-connectors"></a>Conectores de la cuenta de integración 

Enterprise Integration Pack (EIP) incluye conectores bien conocidos en la comunidad de BizTalk Server. Cuando compre una [cuenta de integración](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), también obtendrá los conectores siguientes: 

|  |  |  |  |
| --- | --- | --- | --- |
| [![Icono de la API][as2icon]<br/>**Descodificación de</br> AS2**][as2decode] | [![Icono de la API][as2icon]<br/>**Codificación de</br> AS2**][as2encode] | [![Icono de la API][x12icon]<br/>**Descodificación de</br> EDIFACT**][EDIFACTdecode] | [![Icono de la API][x12icon]<br/>**Codificación de</br> EDIFACT**][EDIFACTencode] |
[![Icono de la API][flatfileicon]<br/>**Codificación de</br> archivos planos**][flatfiledoc] | [![Icono de la API][flatfiledecodeicon]<br/>**Descodificación de</br> archivos planos**][flatfiledecodedoc] | [![Icono de la API][integrationaccounticon]<br/>**Cuenta de<br/>integración**][integrationaccountdoc] | [![Icono de la API][xmltransformicon]<br/>**Transformación<br/>XML**][xmltransformdoc] |
| [![Icono de la API][x12icon]<br/>**Descodificación de</br> X12**][x12decode] | [![Icono de la API][x12icon]<br/>**Codificación de</br> X12**][x12encode] | [![Icono de la API][xmlvalidateicon]<br/>**Validación de <br/>XML**][xmlvalidatedoc] | |

### <a name="enterprise-connectors"></a>Conectores de empresa

Conecte con sus aplicaciones empresariales desde sus aplicaciones lógicas.

|  |  |
| --- | --- |
|[![Icono de la API][MQicon]<br/>**MQ**][mqdoc]|[![Icono de la API][SAPicon]<br/>**SAP**][sapconnector]|

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
| **Join** | Une una matriz por algún delimitador. Por ejemplo, la operación Detectar frases clave devuelve una matriz de frases clave. Las puede "unir" con un carácter `,`, o algo similar. Por lo que en lugar de `["Some", "Phrase"]`, tendrá `"Some, Phrase"`. |
| **Análisis del archivo JSON** | Analizar y acceder a los valores de un objeto JSON en el diseñador. Por ejemplo, si Azure Functions devuelve una carga útil JSON, puede analizarla para acceder a las propiedades JSON en un paso posterior. La acción también valida que el código JSON coincide con el esquema especificado en tiempo de ejecución. | 
| **Selección** | Seleccione algunas propiedades de una matriz para su posterior procesamiento. Si usa la acción "Enumerar registros" y SQL devuelve 15 columnas, seleccione algunas para su procesamiento posterior. La salida es una matriz que solo contiene las propiedades que ha seleccionado. |

## <a name="custom-connectors-and-azure-certification"></a>Conectores personalizados y certificación de Azure 

Para llamar a las API que ejecutan código personalizado o que no están disponibles como conectores, puede extender la plataforma de Logic Apps mediante la [creación de aplicaciones de API basadas en REST](../logic-apps/logic-apps-create-api-app.md). También puede crear sus propios [conectores personalizada](../logic-apps/custom-connector-overview.md) que pueden estar disponibles para cualquier aplicación lógica de su suscripción.

Si quiere que las aplicaciones de API sean públicas y estén disponibles para usarlas en Azure, también puede [enviar sus conectores para que Microsoft los certifique](../logic-apps/custom-connector-submit-certification.md).

## <a name="get-help"></a>Obtención de ayuda

Para formular preguntas, o responderlas, y ver lo que hacen otros usuarios de Azure Logic Apps, visite el [foro de Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

Para ayudar a mejorar Azure Logic Apps y los conectores, vote las ideas que encontrará en el [sitio de comentarios de usuario de Azure Logic Apps](http://aka.ms/logicapps-wish) o envíe ideas nuevas.

¿Falta un tema sobre conectores o algún dato que considere importante? Si es así, puede ayudarnos. Agréguelo a nuestros temas existentes o escriba el suyo propio. Nuestra documentación es de código abierto y se hospeda en GitHub. Para comenzar, vaya a nuestro [repositorio de GitHub](https://github.com/Microsoft/azure-docs). 

## <a name="next-steps"></a>pasos siguientes
* [Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Creación de API personalizadas para las aplicaciones lógicas](../logic-apps/logic-apps-create-api-app.md)
* [Supervisar las aplicaciones lógicas](../logic-apps/logic-apps-monitor-your-logic-apps.md)

<!--Connectors Documentation-->

[gatewaydoc]: ../logic-apps/logic-apps-gateway-connection.md "Conexión a orígenes de datos locales desde aplicaciones lógicas con una puerta de enlace de datos local"
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
[sharepointserver]: ./connectors-create-api-sharepointserver.md "Conectarse al servidor local de SharePoint. Administrar documentos, elementos de lista, etc."
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
[AppServices-icon]: ./media/apis-list/AppServices.png
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
[FileSystem-icon]: ./media/apis-list/filesystem.png
[FileSystemIcon]: ./media/apis-list/filesystem.png
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
[API/Web-Appicon]: ./media/apis-list/appservices.png
[Azure-Functionsicon]: ./media/apis-list/function.png
[CallLogicApp-icon]: ./media/apis-list/calllogicapp.png
[Delayicon]: ./media/apis-list/delay.png
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
