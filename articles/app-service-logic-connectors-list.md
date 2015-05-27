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
	ms.date="03/31/2015" 
	ms.author="mandia"/>


# Lista de conectores y aplicaciones de API del Servicio de aplicaciones de Microsoft Azure
En este tema se enumeran todos los conectores disponibles y aplicaciones de API creados por Microsoft.

Para obtener información y una lista de lo que se incluye con cada nivel de servicio de precios, consulte [Precios del Servicio de aplicaciones de Azure](http://azure.microsoft.com/pricing/details/app-service/).


## Conectores estándar
En la siguiente tabla se enumeran todos los conectores y las aplicaciones de API disponibles creados por Microsoft, disponibles con los conectores estándar:

<table border="1">
<tr bgcolor="FAF9F9">
        <th>Nombre</th>
        <th>Descripción</th>
</tr>

<tr>
<td colspan="2"><strong>Conectores de aplicaciones + servicios de datos</strong><br/><br/><a HREF="http://azure.microsoft.com/documentation/articles/app-service-logic-data-connectors/">Conectores de aplicaciones + servicios de datos</a> proporciona información más detallada sobre estos conectores.</td>
</tr>

<tr>
<td>HDInsight de Azure</td>
<td>HDInsight de Azure implementa y aprovisiona clústeres de Apache Hadoop en la nube con el fin de proporcionar un marco de software diseñado para realizar tareas de administración, análisis y generación de informes en Big Data. Este conector crea un clúster de Hadoop en Azure y envía trabajos de Hadoop diferentes como Hive, Pig, MapReduce y MapReduce de streaming. Mediante este conector puede hacer girar un clúster, enviar un trabajo y esperar a que el trabajo finalice.</td>
</tr>

<tr>
<td>Bus de servicio</td>
<td>Bus de servicio de Azure es un sistema de mensajería genérico basado en la nube para conectarse a prácticamente cualquier cosa. Permite el intercambio de mensajes de forma flexible para ofrecer mayor escalabilidad y confiabilidad. Mediante este conector puede enviar mensajes desde entidades de Bus de servicio, como colas y temas, y recibir mensajes de entidades de Bus de servicio, como colas y suscripciones.</td>
</tr>

<tr>
<td>Blob de almacenamiento de Azure</td>
<td>Blob de almacenamiento de Azure almacena grandes cantidades de datos no estructurados, como texto o datos binarios, a los que se puede obtener acceso desde cualquier lugar del mundo. Mediante este conector puede conectarse al almacenamiento de blobs y cargar blobs, obtener blobs, eliminar blobs, enumerar blobs del contenedor, hacer instantáneas de blobs, copiar blobs y usar un desencadenador para recuperar blobs.</td>
</tr>

<tr>
<td>Box</td>
<td>Box es un servicio de uso compartido de archivo. El conector Box se conecta a Box y puede cargar archivos, obtener archivos, eliminar archivos, enumerar archivos y usar un desencadenador para recuperar archivos.</td>
</tr>

<tr>
<td>Dropbox</td>
<td>Dropbox es un servicio de hospedaje de archivos. Con este conector puede conectarse a Dropbox y cargar archivos, obtener archivos, eliminar archivos, enumerar archivos y usar un desencadenador para recuperar archivos.</td>
</tr>

<tr>
<td>Microsoft Office 365</td>
<td>El conector Office 365 puede enviar y recibir correos electrónicos, administrar su calendario y administrar sus contactos con su cuenta de Office 365. También puede enviar, recibir y obtener correos electrónicos, crear y eliminar eventos en el calendario y crear, actualizar, obtener y eliminar sus contactos.</td>
</tr>

<tr>
<td>Microsoft OneDrive</td>
<td>Microsoft OneDrive es un servicio de hospedaje de archivos. El conector OneDrive se conecta a su Microsoft OneDrive personal y puede cargar archivos, obtener archivos, eliminar archivos, enumerar archivos y utilizar un desencadenador para recuperar archivos.</td>
</tr>

<tr>
<td>Microsoft SharePoint</td>
<td>El conector Microsoft SharePoint se conecta a Microsoft SharePoint Server o SharePoint Online local, administra documentos y enumera elementos. También puede crear, actualizar, obtener y eliminar documentos y enumerar elementos. Se admiten métodos de autenticación diferentes, como credenciales predeterminadas, OAuth 2.0, autenticación de Windows y autenticación basada en formularios.</td>
</tr>

<tr>
<td>Microsoft SQL Server</td>
<td>El conector Microsoft SQL se conecta a SQL Server local o a Base de datos SQL de Azure. Puede crear, actualizar, obtener y eliminar entradas en una tabla de base de datos SQL.</td>
</tr>

<tr>
<td colspan="2"><strong>Conectores sociales</strong><br/><br/><a HREF="http://azure.microsoft.com/documentation/articles/app-service-logic-social-connectors/">Conectores sociales</a> proporciona información más detallada sobre estos conectores.</td>
</tr>

<tr>
<td>Chatter</td>
<td>Chatter es una red social empresarial. El conector Chatter se conecta a Chatter y puede exponer mensajes y buscar y usar un desencadenador para recuperar mensajes nuevos.</td>
</tr>

<tr>
<td>Facebook</td>
<td>Facebook es un servicio de red social. El conector Facebook se conecta a Facebook y puede publicar mensajes, vídeos, fotos, ofertas, etc. También puede recuperar mensajes, comentarios, eventos, fuentes de usuario, información de usuario sobre diferentes gustos, incluidos libros, juegos, películas, etc. Este conector también incluye un desencadenador para recuperar nuevos mensajes expuestos en una página.</td>
</tr>

<tr>
<td>Microsoft Yammer</td>
<td>Microsoft Yammer es una red social empresarial. El conector Yammer se conecta a Yammer, incluye una acción Exponer mensaje y un desencadenador para recuperar mensajes nuevos.</td>
</tr>

<tr>
<td>Twilio</td>
<td>Twilio es un servicio SaaS orientado a la comunicación. El conector Twilio se conecta a Twilio y puede enviar mensajes, obtener mensajes, enumerar mensajes, enumerar usos, obtener números gratuitos disponibles, obtener números de teléfono móvil disponibles, obtener números locales disponibles, enumerar números de teléfono entrantes, obtener números de teléfono entrantes y agregar números de teléfono entrantes.</td>
</tr>

<tr>
<td>Twitter</td>
<td>Twitter es un servicio de red social en línea que permite a los usuarios enviar y leer mensajes cortos de 140 caracteres. El conector Twitter se conecta a Twitter y puede obtener escalas de tiempo de usuario, buscar tweets, obtener seguidores, buscar usuarios, obtener escalas de tiempo de inicio, obtener escalas de tiempo de menciones, exponer tweets, exponer tweets a usuarios y enviar mensajes directos. El conector Twitter también usa desencadenadores como Obtener tweets por palabra clave, Obtener tweets por controlador de usuario y Obtener tweets por hashtag.</td>
</tr>

<tr>
<td colspan="2"><strong>Conectores de protocolos</strong><br/><br/><a HREF="http://azure.microsoft.com/documentation/articles/app-service-logic-protocol-connectors/">Conectores de protocolos</a> proporciona información más detallada sobre estos conectores.</td>
</tr>

<tr>
<td>Archivo</td>
<td>El conector Archivo permite a los clientes enviar o cargar archivos en un sistema de archivos o una red, así como recibir o descargar archivos desde un sistema de archivos o red. Mediante este conector puede conectarse al servidor de archivos local y cargar archivos, obtener archivos, eliminar archivos, enumerar archivos y también usar un desencadenador para recuperar archivos.</td>
</tr>

<tr>
<td>FTP<br/>FTPS</td>
<td>FTP (Protocolo de transferencia de archivos) es un conocido protocolo de red utilizado para transferir archivos desde un host a otro. El conector FTP puede cargar archivos, obtener archivos, eliminar archivos, enumerar archivos y también usar un desencadenador para recuperar archivos.</td>
</tr>

<tr>
<td>Agente de escucha HTTP</td>
<td>El agente de escucha HTTP abre un extremo que actúa como un servidor HTTP y escucha las solicitudes HTTP entrantes.</td>
</tr>

<tr>
<td>POP3<br/>IMAP</td>
<td>POP3 (Protocolo de oficina de correos) es el protocolo usado por un cliente de correo electrónico para recuperar el correo electrónico de un servidor de correo. El conector POP3 se conecta a un servidor POP3 e incluye un desencadenador para recuperar correos electrónicos con datos adjuntos.</td>
</tr>

<tr>
<td>SFTP</td>
<td>SFTP (Protocolo de transferencia de archivos SSH) es un protocolo común que se usa para transferir archivos de forma segura. El conector SFTP puede cargar archivos, obtener archivos, eliminar archivos, enumerar archivos y usar un desencadenador para recuperar archivos.</td>
</tr>

<tr>
<td>SMTP</td>
<td>SMTP (Protocolo Simple de transferencia de correo) es el protocolo que se usa entre un cliente de correo y un servidor de correo para enviar mensajes de correo. El conector SMTP se conecta a un servidor SMTP y envía correo con datos adjuntos.</td>
</tr>

<tr>
<td colspan="2"><strong>Conectores para empresas</strong><br/><br/><a HREF="http://azure.microsoft.com/documentation/articles/app-service-logic-enterprise-connectors">Conectores para empresas</a> proporciona información más detallada sobre estos conectores.</td>
</tr>

<tr>
<td>Marketo</td>
<td>Marketo desarrolla software de automatización de marketing que proporciona marketing de entrada, marketing social, CRM y otros servicios relacionados. El conector Marketo se conecta a Marketo y puede crear y actualizar clientes potenciales, obtener cambios de clientes potenciales, programar campañas, solicitar campañas, obtener clientes potenciales, obtener información de campañas y listas, agregar clientes potenciales a listas y quitar clientes potenciales de listas.</td>
</tr>

<tr>
<td>QuickBooks</td>
<td>Mediante el conector QuickBooks puede crear, actualizar, leer, eliminar y consultar diferentes entidades de Intuit QuickBooks como clientes, artículos, facturas, etc.</td>
</tr>

<tr>
<td>Salesforce</td>
<td>El conector Salesforce administra diferentes entidades como cuentas, clientes potenciales, oportunidades, casos, etc. en su cuenta de Salesforce. También puede crear, actualizar, realizar operaciones upsert, consultar y eliminar diversas entidades.</td>
</tr>

<tr>
<td>SugarCRM</td>
<td>El conector SugarCRM se conecta a SugarCRM en línea y puede crear, actualizar, leer y eliminar diferentes tipos de módulos, como cuentas, contactos, productos, etc.</td>
</tr>

</table>


## Aplicaciones de API de BizTalk EAI
En la siguiente tabla se enumeran todas las aplicaciones de API disponibles creadas por Microsoft, disponibles con los servicios EAI de BizTalk:

	Name  | Description
------------- | -------------
Codificador de archivos sin formato de BizTalk | Proporciona interoperabilidad entre los datos de archivo sin formato (como excel y csv) y los datos XML. Esta aplicación de API puede convertir un archivo sin formato a XML y viceversa.
Servicio de transformación de BizTalk | La aplicación de API Transformación de BizTalk convierte los datos de un formato a otro. También puede cargar un mapa existente (archivo .trfm), ver la representación gráfica del mapa que muestra los vínculos entre los esquemas de origen y destino y usar la funcionalidad 'Probar’ con un contenido XML de entrada de ejemplo. También hay disponibles diferentes funciones integradas, como manipulaciones de cadenas, asignación condicional, expresiones aritméticas, formateadores de hora y fecha, bucles, etc.
Validador XML de BizTalk | La aplicación de API Validador valida los datos XML con esquemas XML predefinidos. Puede usar los esquemas existentes o generar esquemas basándose en una instancia de archivo sin formato, instancia de JSON o conectores existentes.
Extractor XPath de BizTalk | La aplicación de API Extractor busca y extrae datos de contenido XML basándose en una expresión XPath determinada.

Consulte [conectores de integración de BizTalk](app-service-logic-integration-connectors.md) para obtener más información y detalles sobre estas aplicaciones de API.


## Conectores de Biztalk EDI y aplicaciones de API
En la siguiente tabla se enumeran todos los conectores y aplicaciones de API disponibles creados por Microsoft, que están disponibles con los servicios BizTalk EDI:

	Name  | Description
------------- | -------------
Conector AS2 | El conector AS2 puede recibir y enviar mensajes mediante el protocolo de transporte AS2 en comunicaciones negocio a negocio. Los datos se transportan de manera segura y confiable a través de Internet mediante cifrado y certificados digitales.
BizTalk EDIFACT | La aplicación de API de EDIFACT recibe y envía mensajes mediante el protocolo EDIFACT en comunicaciones negocio a negocio.
BizTalk X12 | La aplicación de API de X12 recibe y envía mensajes mediante el protocolo X12 en comunicaciones negocio a negocio.
Administración de socios comerciales de BizTalk | La aplicación de API Administración de socios comerciales define y conserva las relaciones negocio a negocio con socios, acuerdos y esquemas y certificados que se usan en los contratos. Estas relaciones se hacen valer a través de aplicaciones de API de AS2, EDIFACT y X12.

Consulte [Conectores negocio a negocio](app-service-logic-b2b-connectors.md) para obtener más información y detalles sobre estas aplicaciones de API.


## Aplicación de API de reglas de BizTalk
En la siguiente tabla se enumeran todas las aplicaciones de API disponibles creadas por Microsoft, disponibles con las reglas de BizTalk:

	Name  | Description
------------- | -------------
Reglas de BizTalk | Las reglas de BizTalk definen y controlan la estructura, operación y estrategia de una organización. Las directivas empresariales se pueden actualizar sin volver a recopilar e implementar las aplicaciones asociadas.

Consulte [Conectores de integración de BizTalk](app-service-logic-integration-connectors.md) para obtener más información y detalles sobre estas aplicaciones de API.


## Conectores Premium
En la siguiente tabla se enumeran todos los conectores y las aplicaciones de API creados por Microsoft disponibles en los conectores Premium:

	Name  | Description
------------- | -------------
Conector de DB2 | El conector de DB2 conecta una base de datos DB2 de IBM, de forma local y en una máquina virtual de Azure con un sistema operativo Windows. Puede asignar las operaciones Web API y API de OData a comandos de lenguaje de consulta estructurado de Informix. Las acciones incluyen la selección, inserción, actualización, eliminación de tablas e instrucción personalizada<br/><br/>[Conector de aplicaciones + servicios de datos](app-service-logic-data-connectors.md)
Informix | El conector de Informix se conecta a una base de datos Informix de IBM, de forma local y en una máquina virtual de Azure con un sistema operativo Windows. Puede asignar las operaciones Web API y API de OData a comandos de lenguaje de consulta estructurado de Informix. Las acciones incluyen la selección, inserción, actualización, eliminación de tablas e instrucción personalizada<br/><br/>[Conector de aplicaciones + servicios de datos](app-service-logic-data-connectors.md)
MQ | El conector de MQ se conecta a IBM WebSphere MQ Server, de forma local y en una máquina virtual de Azure que ejecuta un sistema operativo Windows.<br/><br/><strong>Nota</strong> Actualmente no se puede utilizar con Aplicaciones lógicas.<br/><br/>[Conectores de aplicaciones + servicios de datos](app-service-logic-data-connectors.md)
Base de datos de Oracle | El conector Base de datos de Oracle se conecta a la base de datos de Oracle local y puede crear, actualizar, obtener y eliminar entradas en una tabla de base de datos. <br/><br/>[Conectores de aplicaciones + servicios de datos](app-service-logic-data-connectors.md)
SAP | El conector SAP se conecta a un servidor SAP local e invoca RFC, BAPI y tRFC, y envía IDOC.<br/><br/>[Conectores empresariales](app-service-logic-enterprise-connectors.md)


## Cree sus propios conectores y aplicaciones de API
Consulte [Creación de conectores con las API de REST](http://go.microsoft.com/fwlink/p/?LinkId=529766).


## Más conectores

[Conectores de integración de BizTalk](app-service-logic-integration-connectors.md)<br/> [Conectores empresariales](app-service-logic-enterprise-connectors.md)<br/> [Conectores de negocio a negocio](app-service-logic-b2b-connectors.md)<br/> [Conectores sociales](app-service-logic-social-connectors.md)<br/> [Conectores de protocolos](app-service-logic-protocol-connectors.md)<br/> [Conectores de aplicaciones + servicios de datos](app-service-logic-data-connectors.md)<br/><br/> [Qué son los conectores y las aplicaciones de la API de BizTalk](app-service-logic-what-are-biztalk-api-apps.md)

<!--HONumber=54-->