<properties 
	pageTitle="Qué son los conectores y las Aplicaciones de API de BizTalk" 
	description="Obtenga información acerca de Aplicaciones de API, conectores y Aplicaciones de API de BizTalk" 
	services="app-service\logic" 
	documentationCenter="" 
	authors="joshtwist" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-logic" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="jtwist"/>

# Qué son los conectores y las Aplicaciones de API de BizTalk

Servicios de aplicaciones de Azure se basa en un principio de extensibilidad y conectividad común a través de aplicaciones de API. Un *Connector* es un tipo de aplicación de API que se centra en la conectividad. Los conectores, al igual que cualquier otra aplicación de API, se utilizan desde aplicaciones web, aplicaciones móviles y aplicaciones lógicas. Los conectores hacen que sea fácil conectarse a servicios existentes y ayuda a administrar la autenticación, ofrece supervisión, proporciona análisis, etc.

Cualquier desarrollador puede crear sus propias aplicaciones de API e implementarlas de forma privada y, en el futuro, podrá compartirlas y rentabilizarlas a través del Marketplace. 

![API Apps Marketplace](./media/app-service-learn-about-flows-preview/Marketplace.png)

Para que los desarrolladores aceleren la creación de soluciones con los Servicios de aplicaciones de Azure, el equipo de Microsoft Azure agregó una serie de conectores al marketplace para satisfacer muchos escenarios comunes. Además, para ampliar el alcance de los Servicios de aplicaciones para escenarios de integración complejos y avanzados, también hay disponibles varias capacidades Premium y BizTalk.

[Lista de conectores y aplicaciones de API del Servicio de aplicaciones de Microsoft Azure](app-service-logic-connectors-list.md)


## Conectores de protocolos
Servicios de aplicaciones ha surgido de la web y favorece la comunicación mediante HTTP, así como el uso compartido de metadatos a través de formatos abiertos como Swagger. Por supuesto, las empresas necesitan comunicarse a través de una variedad de protocolos. Los conectores de protocolos pueden salvar la brecha y hacer que la comunicación con servicios que utilizan FTP, SFTP, POP3/IMAP, SMTP y SOAP sea tan sencillo como realizar una llamada HTTP.  

[Conectores de protocolos en el Servicio de aplicaciones de Microsoft Azure](app-service-logic-protocol-connectors.md)


## Conectores SaaS
Los conectores SaaS del Servicio de aplicaciones proporcionan una llave en mano para conectar las aplicaciones web, móviles y lógicas a algunos de los principales nombres de SaaS actuales, incluidos Office 365, SalesForce, Sugar CRM, OneDrive, DropBox, Marketo, Facebook y muchos más.

[Conectores sociales en el Servicio de aplicaciones de Microsoft Azure](app-service-logic-social-connectors.md)

[Conectores de servicios de datos y aplicaciones en el Servicio de aplicaciones de Microsoft Azure](app-service-logic-data-connectors.md)


## Conectores Premium 
Los conectores Premium amplían el alcance de los Servicios de aplicaciones aún más en la empresa con conectividad para SAP, Siebel, Oracle, DB2, etc.

[Conectores empresariales del Servicio de aplicaciones de Microsoft Azure](app-service-logic-enterprise-connectors.md)


## Aplicaciones de API de BizTalk
La creación de aplicaciones críticas para la empresa requiere más que solo conectividad. Basado en la plataforma de integración líder del sector de Microsoft - BizTalk Server - las Aplicaciones de API de BizTalk proporcionan capacidades de integración avanzadas que se acoplarán a las aplicaciones web, móviles y lógica fácilmente. Incluye el procesamiento por lotes y desagrupación, VETR (validación, extracción, transformación y enrutamiento) y la compatibilidad con formatos EDI como X 12, EDIFACT y AS2.

[Conectores de negocio a negocio y Aplicaciones de API en el Servicio de aplicaciones de Microsoft Azure](app-service-logic-b2b-connectors.md)<br/>

[Integración en BizTalk de Aplicaciones de API en Servicios de aplicaciones de Microsoft Azure](app-service-logic-integration-connectors.md)

<!--HONumber=49-->