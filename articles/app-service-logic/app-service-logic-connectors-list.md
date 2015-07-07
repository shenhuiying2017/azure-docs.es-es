<properties 
	pageTitle="Lista de conectores y aplicaciones de API | Azure" 
	description="Obtenga información acerca de los conectores y las aplicaciones de API del Servicio de aplicaciones de Azure; arquitectura de microservicios" 
	services="app-service\logic" 
	documentationCenter="" 
	authors="MandiOhlinger" 
	manager="dwrede" 
	editor="cgronlun"/>

<tags 
	ms.service="app-service-logic" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/08/2015" 
	ms.author="mandia"/>


# Lista de conectores y aplicaciones de API del Servicio de aplicaciones de Microsoft Azure
En este tema se enumeran todos los conectores disponibles y aplicaciones de API creados por Microsoft.

Para obtener información y una lista de lo que se incluye con cada nivel de servicio de precios, consulte [Precios del Servicio de aplicaciones de Azure](http://azure.microsoft.com/pricing/details/app-service/).


## Conectores estándar
En la siguiente tabla se enumeran todos los conectores y las aplicaciones de API disponibles creados por Microsoft, disponibles con los conectores estándar:

Nombre | Descripción
--- | ---
[HDInsight de Azure](app-service-logic-connector-hdinsight.md) | Use este conector para crear un clúster de Hadoop en Azure y enviar trabajos de Hadoop diferentes como Hive, Pig, MapReduce y MapReduce de streaming. Mediante este conector, también puede hacer girar un clúster, enviar un trabajo y esperar a que el trabajo finalice.
[Bus de servicio de Azure](app-service-logic-connector-azureservicebus.md) | Mediante este conector puede enviar mensajes desde entidades de Bus de servicio, como colas y temas, y recibir mensajes de entidades de Bus de servicio, como colas y suscripciones.
[Blob de almacenamiento de Azure](app-service-logic-connector-azurestorageblob.md) | Se conecta al almacenamiento de blobs y puede cargar blobs, obtener blobs, eliminar blobs, enumerar blobs del contenedor, hacer instantáneas de blobs, copiar blobs y usar un desencadenador para recuperar blobs.
WebJobs de Azure | Se conecta a WebJobs.
[Box](app-service-logic-connector-box.md) | Se conecta a Box y puede cargar archivos, obtener archivos, eliminar archivos, enumerar archivos y usar un desencadenador para recuperar archivos.
[Chatter](app-service-logic-connector-chatter.md) | Se conecta a Chatter y puede exponer mensajes y buscar y usar un desencadenador para recuperar mensajes nuevos.
[Dropbox](app-service-logic-connector-dropbox.md) | Se conecta a Dropbox y puede cargar archivos, obtener archivos, eliminar archivos, enumerar archivos y usar un desencadenador para recuperar archivos.
[Facebook](app-service-logic-connector-facebook.md) | Se conecta a Facebook y puede publicar mensajes, vídeos, fotos, ofertas, etc. También puede recuperar mensajes, comentarios, eventos, fuentes de usuario, información de usuario sobre diferentes gustos, incluidos libros, juegos, películas, etc. Este conector también incluye un desencadenador para recuperar nuevos mensajes expuestos en una página.
[Archivo](app-service-logic-connector-file.md) | Permite a los clientes enviar o cargar archivos en un sistema de archivos o una red, así como recibir o descargar archivos desde un sistema de archivos o red. Mediante este conector puede conectarse al servidor de archivos local y cargar archivos, obtener archivos, eliminar archivos, enumerar archivos y también usar un desencadenador para recuperar archivos.
[FTP<br/>FTPS](app-service-logic-connector-ftp.md) | Se conecta a un servidor FTP/FTPS y puede cargar archivos, obtener archivos, eliminar archivos, enumerar archivos y también usar un desencadenador para recuperar archivos.
[HTTP](app-service-logic-connector-http.md) | El agente de escucha HTTP abre un extremo que actúa como un servidor HTTP y escucha las solicitudes HTTP entrantes. Puede PUBLICAR, OBTENER, ELIMINAR y COLOCAR. La acción HTTP no requiere una aplicación de API y se admite de forma nativa en las aplicaciones lógicas. Puede utilizar el protocolo HTTP o HTTPS.
[Microsoft Office 365](app-service-logic-connector-office365.md) | El conector Office 365 puede enviar y recibir correos electrónicos, administrar su calendario y administrar sus contactos con su cuenta de Office 365. También puede enviar, recibir y obtener correos electrónicos, crear y eliminar eventos en el calendario y crear, actualizar, obtener y eliminar sus contactos.
[Microsoft OneDrive](app-service-logic-connector-onedrive.md) | Microsoft OneDrive es un servicio de hospedaje de archivos. El conector OneDrive se conecta a su Microsoft OneDrive personal y puede cargar archivos, obtener archivos, eliminar archivos, enumerar archivos y utilizar un desencadenador para recuperar archivos.
[Microsoft SharePoint](app-service-logic-connector-sharepoint.md) | Se conecta a Microsoft SharePoint Server o SharePoint Online local, administra documentos y enumera elementos. También puede crear, actualizar, obtener y eliminar documentos y enumerar elementos. Se admiten métodos de autenticación diferentes, como credenciales predeterminadas, OAuth 2.0, autenticación de Windows y autenticación basada en formularios.
[Microsoft SQL Server](app-service-logic-connector-sql.md) | Se conecta a SQL Server local o a Base de datos SQL de Azure. Puede crear, actualizar, obtener y eliminar entradas en una tabla de base de datos SQL.
[Microsoft Yammer](app-service-logic-connector-yammer.md) | Se conecta a Yammer. Incluye la acción Publicar mensaje y un desencadenador para recuperar los mensajes nuevos.
[POP3](app-service-logic-connector-pop3.md) (Protocolo de oficina de correos)| Se conecta a un servidor POP3 e incluye un desencadenador para recuperar correos electrónicos con datos adjuntos.
[QuickBooks](app-service-logic-connector-quickbooks.md) | Mediante este conector, puede crear, actualizar, leer, eliminar y consultar diferentes entidades de Intuit QuickBooks como clientes, artículos, facturas, etc.
[SFTP](app-service-logic-connector-sftp.md) (Protocolo de transferencia de archivos SSH)| Se conecta a SFTP y puede cargar archivos, obtener archivos, eliminar archivos, enumerar archivos y usar un desencadenador para recuperar archivos.
[SMTP](app-service-logic-connector-smtp.md) (Protocolo simple de transferencia de correo) | Se conecta a un servidor SMTP y envía correo con datos adjuntos.
[Slack](app-service-logic-connector-slack.md) | 
[Salesforce](app-service-logic-connector-salesforce.md) | El conector Salesforce administra diferentes entidades como cuentas, clientes potenciales, oportunidades, casos, etc. en su cuenta de Salesforce. También puede crear, actualizar, realizar operaciones upsert, consultar y eliminar diversas entidades.
[SugarCRM](app-service-logic-connector-sugarcrm.md) | Se conecta a SugarCRM en línea y puede crear, actualizar, leer y eliminar diferentes tipos de módulos, como cuentas, contactos, productos, etc.
[Twilio](app-service-logic-connector-twilio.md) | Se conecta a Twilio y puede enviar mensajes, obtener mensajes, enumerar mensajes, enumerar usos, obtener números gratuitos disponibles, obtener números de teléfono móvil disponibles, obtener números locales disponibles, enumerar números de teléfono entrantes, obtener números de teléfono entrantes y agregar números de teléfono entrantes.
[Twitter](app-service-logic-connector-twitter.md) | Se conecta a Twitter y puede obtener escalas de tiempo de usuario, buscar tweets, obtener seguidores, buscar usuarios, obtener escalas de tiempo de inicio, obtener escalas de tiempo de menciones, exponer tweets, exponer tweets a usuarios y enviar mensajes directos. El conector Twitter también usa desencadenadores como Obtener tweets por palabra clave, Obtener tweets por controlador de usuario y Obtener tweets por hashtag.
Esperar | Utilice este conector para retrasar la ejecución de la aplicación. Puede retrasar la aplicación durante un tiempo determinado o hasta una repetición en un momento determinado.


## Conectores Premium
En la siguiente tabla se enumeran todos los conectores y las aplicaciones de API creados por Microsoft disponibles en los conectores Premium:

Nombre | Descripción
------------- | -------------
Conector AS2 | El conector AS2 puede recibir y enviar mensajes mediante el protocolo de transporte AS2 en comunicaciones negocio a negocio. Los datos se transportan de manera segura y confiable a través de Internet mediante cifrado y certificados digitales.
BizTalk EDIFACT | La aplicación de API de EDIFACT recibe y envía mensajes mediante el protocolo EDIFACT en comunicaciones negocio a negocio.
BizTalk X12 | La aplicación de API de X12 recibe y envía mensajes mediante el protocolo X12 en comunicaciones negocio a negocio.
Administración de socios comerciales de BizTalk | La aplicación de API Administración de socios comerciales define y conserva las relaciones negocio a negocio con socios, acuerdos y esquemas y certificados que se usan en los contratos. Estas relaciones se hacen valer a través de aplicaciones de API de AS2, EDIFACT y X12.
Codificador JSON de BizTalk | Un codificador y descodificador que ayuda a la interoperabilidad de la aplicación entre los datos JSON y XML. Puede convertir una instancia JSON en XML y viceversa.
[Reglas de BizTalk](app-service-logic-use-biztalk-rules.md) | Las reglas de BizTalk definen y controlan la estructura, operación y estrategia de una organización. Las directivas empresariales se pueden actualizar sin volver a recopilar e implementar las aplicaciones asociadas.
Conector de DB2 | Se conecta a una base de datos DB2 de IBM, de forma local y en una máquina virtual de Azure con un sistema operativo Windows. Puede asignar las operaciones Web API y API de OData a comandos de lenguaje de consulta estructurado de Informix. <br/><br/>Ningún desencadenador. Las acciones incluyen la selección, inserción, actualización, eliminación de tablas e instrucción personalizada<br/><br/>Este conector también incluye el cliente de Microsoft para DRDA para conectarse a un servidor de Informix a través de una red TCP/IP.
Informix | Se conecta a una base de datos Informix de IBM, de forma local y en una máquina virtual de Azure con un sistema operativo Windows. Puede asignar las operaciones Web API y API de OData a comandos de lenguaje de consulta estructurado de Informix.<br/><br/>Ningún desencadenador. Las acciones incluyen la selección, inserción, actualización, eliminación de tablas e instrucción personalizada.<br/><br/>Cuando se utiliza de forma local, puede usarse la VPN o Azure ExpressRoute. Este conector también incluye el cliente de Microsoft para DRDA para conectarse a un servidor de Informix a través de una red TCP/IP.
MQ | Se conecta a la versión 8 de IBM WebSphere MQ Server, en local y en una máquina virtual de Azure con un sistema operativo Windows. Cuando se utiliza de forma local, puede usarse la VPN o Azure ExpressRoute. El conector también incluye el cliente de Microsoft para MQ.<br/><br/>Ningún desencadenador. Ninguna acción.<br/><br/><strong>Nota</strong>: actualmente no se puede utilizar con aplicaciones lógicas.
[Base de datos de Oracle](app-service-logic-connector-oracle.md) | El conector Base de datos de Oracle se conecta a la base de datos de Oracle local y puede crear, actualizar, obtener y eliminar entradas en una tabla de base de datos.
[SAP](app-service-logic-connector-sap.md) | El conector SAP se conecta a un servidor SAP local e invoca RFC, BAPI y tRFC, y envía IDOC.
[Codificador de archivos sin formato de BizTalk](app-service-logic-flatfile-encoder.md) | Proporciona interoperabilidad entre los datos de archivo sin formato (como excel y csv) y los datos XML. Esta aplicación de API puede convertir un archivo sin formato a XML y viceversa.
[Servicio de transformación de BizTalk](app-service-logic-transform-xml-documents.md) | La aplicación de API Transformación de BizTalk convierte los datos de un formato a otro. También puede cargar un mapa existente (archivo .trfm), ver la representación gráfica del mapa que muestra los vínculos entre los esquemas de origen y destino y usar la funcionalidad 'Probar’ con un contenido XML de entrada de ejemplo. También hay disponibles diferentes funciones integradas, como manipulaciones de cadenas, asignación condicional, expresiones aritméticas, formateadores de hora y fecha, bucles, etc.
[Validador XML de BizTalk](app-service-logic-xml-validator.md) | La aplicación de API Validador valida los datos XML con esquemas XML predefinidos. Puede usar los esquemas existentes o generar esquemas basándose en una instancia de archivo sin formato, instancia de JSON o conectores existentes.
[Extractor XPath de BizTalk](app-service-logic-xpath-extract.md) | La aplicación de API Extractor busca y extrae datos de contenido XML basándose en una expresión XPath determinada.

## Conectores como desencadenadores
Varios conectores proporcionan desencadenadores para aplicaciones lógicas. Estos desencadenadores son de dos tipos:

1. Desencadenadores de sondeo: estos desencadenadores sondean el servicio que proceda a una frecuencia especificada para comprobar si hay nuevos datos. Cuando haya nuevos datos, se ejecuta una nueva instancia de la aplicación lógica con los datos como entrada. Para impedir que los mismos datos se consuman varias veces, el desencadenador puede limpiar los datos que se han leído y pasado a la aplicación lógica. Algunos de estos conectores son los de archivo, SQL y almacenamiento de Azure.
2. Desencadenadores de inserción: estos desencadenadores escuchan datos en un extremo o esperan a que se produzca un evento. A continuación, desencadenan una nueva instancia de una aplicación de la lógica. Ejemplos de estos conectores son los de escucha HTTP y Twitter.

## Conectores como acciones
Los conectores también pueden utilizarse como acciones dentro de una aplicación lógica. Las acciones son útiles para buscar datos dentro de la aplicación lógica que, a continuación, puede utilizarse en la ejecución. Por ejemplo, puede que necesite consultar datos de una base de datos SQL para obtener información adicional acerca de un cliente al procesar un pedido. O bien, puede que necesite escribir, actualizar o eliminar datos en un destino. Para ello, puede usar las acciones proporcionadas por los conectores. Las acciones se asignan a operaciones en las aplicaciones de API (como definen sus metadatos en Swagger).

## Cree sus propios conectores y aplicaciones de API
[Referencia sobre conectores y aplicaciones de API](http://aka.ms/appservicesconnectorreference)<br/> [Desencadenadores de aplicaciones de API del Servicio de aplicaciones de Azure](../app-service-api/app-service-api-dotnet-triggers.md)


## Más conectores

[¿Qué son los conectores y Aplicaciones de API de BizTalk?](app-service-logic-what-are-biztalk-api-apps.md)<br/> [Uso del Administrador de conexiones híbridas del Servicio de aplicaciones de Azure](app-service-logic-hybrid-connection-manager.md)

<!---HONumber=62-->