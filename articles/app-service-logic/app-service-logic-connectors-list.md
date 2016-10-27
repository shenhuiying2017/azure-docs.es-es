<properties
    pageTitle="Lista de conectores y aplicaciones de API disponibles | Servicio de aplicaciones de Microsoft Azure"
    description="Obtenga información acerca de los conectores y las aplicaciones de API de Servicio de aplicaciones de Azure"
    services="logic-apps"
    documentationCenter=""
    authors="MandiOhlinger"
    manager="erikre"
    editor="cgronlun"/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/01/2016"
    ms.author="mandia"/>



# <a name="list-of-connectors-and-api-apps-to-use-in-your-logic-apps"></a>Lista de conectores y aplicaciones de API para usarlas en sus aplicaciones lógicas
>[AZURE.NOTE] Esta versión del artículo se aplica a la versión de esquema 2014-12-01-preview de las aplicaciones lógicas. Para la versión de disponibilidad General de Logic Apps, consulte [Lista de conectores](../connectors/apis-list.md).

Obtenga información acerca de todos los conectores disponibles y las aplicaciones de API creados por Microsoft para usarlos con Aplicaciones lógicas.

Para obtener información y una lista de lo que se incluye con cada nivel de servicio de precios, consulte [Precios del Servicio de aplicaciones de Azure](https://azure.microsoft.com/pricing/details/app-service/).

> [AZURE.NOTE] Para empezar a utilizar Logic Apps antes de suscribirse para obtener una cuenta de Azure, vaya a [Probar aplicación lógica](https://tryappservice.azure.com/?appservice=logic). Podrá crear inmediatamente una aplicación lógica de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

## <a name="core-connectors"></a>Conectores principales
En la siguiente tabla se enumeran todos los conectores y las aplicaciones de API disponibles creados por Microsoft, disponibles como conectores principales:

Nombre | Description
--- | ---
[Traductor de Bing](https://azure.microsoft.com/marketplace/partners/bing/microsofttranslator/) | Use Bing para traducir texto en otro idioma.
[HTTP](app-service-logic-connector-http.md) | El agente de escucha HTTP abre un extremo que actúa como servidor HTTP y escucha las solicitudes HTTP o HTTPS entrantes. La acción HTTP no requiere una aplicación de API y se admite de forma nativa en las aplicaciones lógicas.
[Slack](app-service-logic-connector-slack.md) | Conéctese a Slack y publique mensajes en los canales de Slack.


## <a name="enterprise-integration-connectors"></a>Conectores de integración de empresa
En la siguiente tabla se enumeran todos los conectores y las aplicaciones de API creados por Microsoft disponibles como conectores de integración de empresa:

Nombre  | Description
------------- | -------------
[Reglas de BizTalk](app-service-logic-use-biztalk-rules.md) | Usa las reglas de BizTalk para definir y controlar la lógica de negocios dentro de una organización. Las directivas empresariales se pueden actualizar sin volver a recopilar o sin volver a implementar las aplicaciones asociadas.
[Extractor XPath de BizTalk](app-service-logic-xpath-extract.md) | Busca y extrae datos de contenido XML basándose en una expresión XPath que elija.
[Conector de DB2](app-service-logic-connector-db2.md) | Se conecta a una base de datos DB2 de IBM de forma local y en una máquina virtual de Azure con un sistema operativo Windows. Puede asignar las operaciones Web API y API de OData a comandos de lenguaje de consulta estructurado de Informix. <br/><br/>Ningún desencadenador. Las acciones incluyen la selección, inserción, actualización, eliminación de tablas e instrucción personalizada<br/><br/>Este conector también incluye el cliente de Microsoft para DRDA para conectarse a un servidor de Informix a través de una red TCP/IP.
[Archivo](app-service-logic-connector-file.md) | Mediante este conector, puede conectarse a la red o al sistema de archivo local y completar diferentes tareas de archivos, incluidas la carga, la eliminación y la enumeración de archivos, etc.
[Informix](app-service-logic-connector-informix.md) | Se conecta a una base de datos Informix de IBM, de forma local y en una máquina virtual de Azure con un sistema operativo Windows. Puede asignar las operaciones Web API y API de OData a comandos de lenguaje de consulta estructurado de Informix.<br/><br/>Ningún desencadenador. Las acciones incluyen la selección, inserción, actualización, eliminación de tablas e instrucción personalizada.<br/><br/>Cuando se utiliza de forma local, puede usarse la VPN o Azure ExpressRoute. Este conector también incluye el cliente de Microsoft para DRDA para conectarse a un servidor de Informix a través de una red TCP/IP.
[Microsoft SQL Server](app-service-logic-connector-sql.md) | Se conecta a SQL Server local o a Base de datos SQL de Azure. Puede crear, actualizar, obtener y eliminar entradas en una tabla de base de datos SQL.
MQ | Se conecta a la versión 8 de IBM WebSphere MQ Server, en local y en una máquina virtual de Azure con un sistema operativo Windows. Cuando se utiliza de forma local, puede usarse la VPN o Azure ExpressRoute. El conector también incluye el cliente de Microsoft para MQ.<br/><br/>Ningún desencadenador. Ninguna acción.<br/><br/>**Nota** Actualmente no se puede utilizar con Logic Apps.

## <a name="connectors-as-triggers"></a>Conectores como desencadenadores
Varios conectores proporcionan desencadenadores para aplicaciones lógicas. Estos desencadenadores son de dos tipos:

1. Desencadenadores de sondeo: estos desencadenadores sondean el servicio que proceda a una frecuencia especificada para comprobar si hay nuevos datos. Cuando haya nuevos datos, se ejecuta una nueva instancia de la aplicación lógica con los datos como entrada. Para impedir que los mismos datos se consuman varias veces, el desencadenador puede limpiar los datos que se han leído y pasado a la aplicación lógica. Algunos de estos conectores son los de archivo, SQL y almacenamiento de Azure.
2. Desencadenadores de inserción: estos desencadenadores escuchan datos en un extremo o esperan a que se produzca un evento. A continuación, desencadenan una nueva instancia de una aplicación de la lógica. Ejemplos de estos conectores son los de escucha HTTP y Twitter.

## <a name="connectors-as-actions"></a>Conectores como acciones
Los conectores también pueden utilizarse como acciones dentro de una aplicación lógica. Las acciones son útiles para buscar datos dentro de la aplicación lógica que, a continuación, puede utilizarse en la ejecución. Por ejemplo, puede que necesite consultar datos de una base de datos SQL para obtener información adicional acerca de un cliente al procesar un pedido. O bien, puede que necesite escribir, actualizar o eliminar datos en un destino. Para ello, puede usar las acciones proporcionadas por los conectores. Las acciones se asignan a operaciones en las aplicaciones de API (como definen sus metadatos en Swagger).

## <a name="create-your-own-connectors-and-api-apps"></a>Cree sus propios conectores y aplicaciones de API
[Connectors and API Apps Reference](http://aka.ms/appservicesconnectorreference) (Referencia de conectores y API Apps)  
[Desencadenadores de aplicación de API de Azure App Service](../app-service-api/app-service-api-dotnet-triggers.md)  
[Referencia sobre aplicaciones lógicas](https://msdn.microsoft.com/library/azure/dn948510.aspx)

## <a name="more-on-connectors-and-api-apps"></a>Más sobre los conectores y las aplicaciones de API
[¿Qué son los conectores y las aplicaciones de la API de BizTalk](app-service-logic-what-are-biztalk-api-apps.md)  
[Uso del Administrador de conexiones híbridas en el Servicio de aplicaciones de Azure](app-service-logic-hybrid-connection-manager.md)  
[Administración y supervisión de las aplicaciones de API y los conectores integrados](app-service-logic-monitor-your-connectors.md)



<!--HONumber=Oct16_HO2-->


