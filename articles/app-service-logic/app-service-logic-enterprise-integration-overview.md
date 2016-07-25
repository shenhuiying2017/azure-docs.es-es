<properties 
	pageTitle="Información general sobre Enterprise Integration Pack | Servicio de aplicaciones de Microsoft Azure | Microsoft Azure" 
	description="Utilice las características de Enterprise Integration Pack para posibilitar escenarios de integración y proceso empresariales mediante Aplicaciones lógicas." 
	services="app-service\logic" 
	documentationCenter=".net,nodejs,java"
	authors="msftman" 
	manager="erikre" 
	editor="cgronlun"/>

<tags 
	ms.service="app-service-logic" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/08/2016" 
	ms.author="deonhe"/>

# Información general sobre Enterprise Integration Pack

## ¿Qué es Enterprise Integration Pack?
Enterprise Integration Pack es la solución en la nube de Microsoft para posibilitar sin problemas comunicaciones de negocio a negocio (B2B). El paquete utiliza protocolos estándar del sector, entre ellos [AS2](./app-service-logic-enterprise-integration-as2.md), [X12](./app-service-logic-enterprise-integration-x12.md) y EDIFACT (próximamente), con el objetivo de intercambiar mensajes entre socios comerciales. Si se desea, los mensajes pueden protegerse mediante cifrado y firmas digitales.

El paquete permite a las organizaciones que utilizan distintos protocolos y formatos intercambiar mensajes electrónicamente transformando los diferentes formatos en uno que puedan interpretar y procesar los sistemas de las dos organizaciones.

Si está familiarizado con BizTalk Server o los Servicios de BizTalk de Microsoft Azure, le resultará sencillo utilizar las características de Enterprise Integration Pack, ya que casi todos los conceptos son similares. Una diferencia importante es que Enterprise Integration Pack utiliza las cuentas de integración para simplificar la administración y el almacenamiento de los artefactos empleados en las comunicaciones B2B.

Desde el punto de vista de su arquitectura, Enterprise Integration Pack se basa en las **cuentas de integración** que almacenan todos los artefactos que pueden utilizarse para diseñar, implementar y mantener aplicaciones B2B. Básicamente, una cuenta de integración es un contenedor en la nube donde se almacenan artefactos como esquemas, partners, certificados, asignaciones y contratos. Estos artefactos pueden utilizarse posteriormente en Aplicaciones lógicas con el fin de crear flujos de trabajo B2B. Antes de poder usar los artefactos en una Aplicación lógica, basta con vincular la cuenta de integración a la Aplicación lógica. Cuando lo haya hecho, la Aplicación lógica tendrá acceso a los artefactos de la cuenta de integración.

## ¿Por qué se debe utilizar Enterprise Integration Pack?
- Gracias a la integración de empresas, podrá almacenar todos los artefactos en un solo lugar: la cuenta de integración.
- Puede utilizar el motor de Aplicaciones de lógica y todos sus conectores para crear flujos de trabajo B2B e integrarlos con aplicaciones de SaaS de terceros, aplicaciones locales y aplicaciones personalizadas.
- También puede utilizar las Funciones de Azure.

## ¿Cómo se puede empezar a disfrutar de Enterprise Integration Pack?
Puede compilar y administrar aplicaciones B2B con Enterprise Integration Pack mediante el diseñador de Aplicaciones lógicas en el **Portal de Azure**.

A continuación, se muestra un resumen de los pasos que hay que llevar a cabo para crear aplicaciones en el Portal de Azure: ![overviewimage](./media/app-service-logic-enterprise-integration-overview/overview-0.png)

## ¿Cuáles son algunos escenarios comunes?

Enterprise Integration Pack admite estos estándares del sector:

- EDI: intercambio electrónico de datos
- EAI: integración de aplicaciones empresariales

## Requisitos para poder comenzar
- Tener una suscripción de Azure con una cuenta de integración
- Disponer de Visual Studio 2015 para crear esquemas y asignaciones
- [Herramientas de integración de empresas de Aplicaciones Lógicas de Microsoft Azure para la versión 2.0 de Visual Studio 2015](https://aka.ms/vsmapsandschemas)

## Más información sobre:
- [Contratos](./app-service-logic-enterprise-integration-agreements.md "Información sobre los contratos de integración de empresas")
- [Escenarios de negocio a negocio (B2B)](./app-service-logic-enterprise-integration-b2b.md "Aprenda a crear Aplicaciones lógicas con características B2B.")
- [Certificados](./app-service-logic-enterprise-integration-certificates.md "Información sobre los certificados de integración de empresas")
- [Codificación y descodificación de archivos sin formato](./app-service-logic-enterprise-integration-flatfile.md "Sepa cómo codificar y descodificar el contenido de archivos sin formato.")
- [Cuentas de integración](./app-service-logic-enterprise-integration-accounts.md "Información sobre las cuentas de integración")
- [Asignaciones](./app-service-logic-enterprise-integration-maps.md "Información sobre las asignaciones de integración de empresas")
- [Asociados](./app-service-logic-enterprise-integration-partners.md "Información sobre los partners de integración de empresas")
- [Esquemas](./app-service-logic-enterprise-integration-schemas.md "Información sobre los esquemas de integración de empresas")
- [Validación de mensajes XML](./app-service-logic-enterprise-integration-xml.md "Obtenga información sobre cómo validar mensajes XML con Aplicaciones lógicas.")
- [Transformación XML](./app-service-logic-enterprise-integration-transform.md "Información sobre las asignaciones de integración de empresas")

<!---HONumber=AcomDC_0713_2016-->