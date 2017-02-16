---
title: "Información general sobre Enterprise Integration Pack | Microsoft Docs"
description: "Utilice las características de Enterprise Integration Pack para posibilitar escenarios de integración y proceso empresariales mediante Aplicaciones lógicas."
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: cgronlun
ms.assetid: dd517c4d-1701-4247-b83c-183c4d8d8aae
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 4faf01ca10f263a5ecc18f51659b5004bf4f7c36
ms.openlocfilehash: 3ca870da1a01bc216604cdd1142ba68dfd5e96e2


---
# <a name="overview-of-the-enterprise-integration-pack"></a>Información general sobre Enterprise Integration Pack
## <a name="what-is-the-enterprise-integration-pack"></a>¿Qué es Enterprise Integration Pack?
Enterprise Integration Pack es la solución en la nube de Microsoft para posibilitar sin problemas comunicaciones de negocio a negocio (B2B). El paquete utiliza protocolos estándar del sector, entre ellos [AS2](app-service-logic-enterprise-integration-as2.md), [X12](app-service-logic-enterprise-integration-x12.md) y [EDIFACT](app-service-logic-enterprise-integration-edifact.md), para intercambiar mensajes entre socios de negocio. Si se desea, los mensajes pueden protegerse mediante cifrado y firmas digitales. 

El paquete permite a las organizaciones que utilizan distintos protocolos y formatos intercambiar mensajes electrónicamente transformando los diferentes formatos en uno que puedan interpretar y procesar los sistemas de las dos organizaciones. 

Si está familiarizado con BizTalk Server o los Servicios de BizTalk de Microsoft Azure, le resultará sencillo utilizar las características de Enterprise Integration Pack, ya que casi todos los conceptos son similares. Una diferencia importante es que Enterprise Integration Pack utiliza las cuentas de integración para simplificar la administración y el almacenamiento de los artefactos empleados en las comunicaciones B2B. 

Desde el punto de vista de su arquitectura, Enterprise Integration Pack se basa en las **cuentas de integración** que almacenan todos los artefactos que pueden utilizarse para diseñar, implementar y mantener aplicaciones B2B. Básicamente, una cuenta de integración es un contenedor en la nube donde se almacenan artefactos como esquemas, asociados, certificados, asignaciones y contratos. Estos artefactos pueden utilizarse posteriormente en Aplicaciones lógicas con el fin de crear flujos de trabajo B2B. Antes de poder usar los artefactos en una Aplicación lógica, basta con vincular la cuenta de integración a la Aplicación lógica. Cuando lo haya hecho, la Aplicación lógica tendrá acceso a los artefactos de la cuenta de integración.  

## <a name="why-should-you-use-enterprise-integration"></a>¿Por qué se debe utilizar Enterprise Integration Pack?
* Gracias a la integración de empresas, podrá almacenar todos los artefactos en un solo lugar: la cuenta de integración. 
* Puede utilizar el motor de Aplicaciones de lógica y todos sus conectores para crear flujos de trabajo B2B e integrarlos con aplicaciones de SaaS de terceros, aplicaciones locales y aplicaciones personalizadas.
* También puede utilizar las Funciones de Azure.

## <a name="how-to-get-started-with-enterprise-integration"></a>¿Cómo se puede empezar a disfrutar de Enterprise Integration Pack?
Puede compilar y administrar aplicaciones B2B con Enterprise Integration Pack mediante el diseñador de Logic Apps en el **Portal de Azure**.  

También puede usar [PowerShell](https://msdn.microsoft.com/library/azure/mt652195.aspx "Temas de Logic Apps y PowerShell") para administrar las aplicaciones lógicas. 

Aquí se proporciona información general sobre los pasos que hay que dar para crear aplicaciones en Azure Portal : ![imagen de información general](./media/app-service-logic-enterprise-integration-overview/overview-0.png)  

## <a name="what-are-some-common-scenarios"></a>¿Cuáles son algunos escenarios comunes?
Enterprise Integration Pack admite estos estándares del sector:   

* EDI: intercambio electrónico de datos  
* EAI: integración de aplicaciones empresariales  

## <a name="heres-what-you-need-to-get-started"></a>Requisitos para poder comenzar
* Tener una suscripción de Azure con una cuenta de integración
* Disponer de Visual Studio 2015 para crear esquemas y asignaciones
* [Herramientas de integración de empresas de Aplicaciones Lógicas de Microsoft Azure para la versión 2.0 de Visual Studio 2015](https://aka.ms/vsmapsandschemas)  

## <a name="try-it"></a>Pruébelo
[Pruébelo ahora](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-as2-send-receive) para implementar una aplicación lógica de envío y recepción de AS2 de ejemplo completamente operativa que usa las características B2B de Logic Apps.

## <a name="learn-more-about"></a>Más información sobre:
* [Contratos](app-service-logic-enterprise-integration-agreements.md "Información sobre los contratos de Enterprise Integration")
* [Escenarios de negocio a negocio (B2B)](app-service-logic-enterprise-integration-b2b.md "Aprenda a crear Logic Apps con características B2B.")  
* [Certificados](app-service-logic-enterprise-integration-certificates.md "Información sobre los certificados de Enterprise Integration")
* [Codificación y descodificación de archivos sin formato](app-service-logic-enterprise-integration-flatfile.md "Aprenda a codificar y descodificar contenido de archivos sin formato")  
* [Cuentas de Integration](app-service-logic-enterprise-integration-accounts.md "Información acerca de las cuentas de integración")
* [Asignaciones](app-service-logic-enterprise-integration-maps.md "Información sobre las asignaciones de Enterprise Integration")
* [Asociados](app-service-logic-enterprise-integration-partners.md "Información acerca de los asociados de Enterprise Integration")
* [Esquemas](app-service-logic-enterprise-integration-schemas.md "Información sobre los esquemas de Enterprise Integration")
* [Validación de mensajes XML](app-service-logic-enterprise-integration-xml.md "Aprenda a validar mensajes XML con Logic Apps")
* [Transformación de XML](app-service-logic-enterprise-integration-transform.md "Información sobre las asignaciones de Enterprise Integration")
* [Conectores de integración de empresa](../connectors/apis-list.md "Información sobre los conectores de Enterprise Integration Pack")
* [Metadatos de la cuenta de integración](app-service-logic-enterprise-integration-metadata.md "Obtenga más información acerca de los metadatos de la cuenta de integración")
* [Supervisión de mensajes B2B](app-service-logic-monitor-b2b-message.md "Más información acerca de la supervisión de mensajes B2B")
* [Seguimiento de mensajes B2B en el Portal de OMS](app-service-logic-track-b2b-messages-omsportal.md "Más información acerca del seguimiento de mensajes B2B en el Portal de OMS")




<!--HONumber=Nov16_HO4-->


