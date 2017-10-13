---
title: Trabajo con mensajes XML en los flujos de trabajo - Azure Logic Apps | Microsoft Docs
description: "Procesar, validar, transformar y enriquecer mensajes XML en aplicaciones lógicas y escenarios empresariales mediante Enterprise Integration Pack"
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: 47672dc4-1caa-44e5-b8cb-68ec3a76b7dc
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 02/27/2017
ms.author: LADocs; mandia
ms.openlocfilehash: 3fec4935f5317be4bf8c9e05f1c24a7c05381b1e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="validate-and-transform-xml-encode-and-decode-flat-files-and-enrich-messages-features-in-logic-apps"></a>Validar y transformar XML, codificar y descodificar archivos planos y enriquecer características de mensajes en aplicaciones lógicas

Con las aplicaciones lógicas, tiene la capacidad de procesar los mensajes XML que envía y recibe. Esta característica se incluye con Enterprise Integration Pack. Para los usuarios con experiencia en BizTalk Server, Enterprise Integration Pack ofrece funcionalidades similares para transformar y validar los mensajes, trabajar con archivos planos e incluso usar XPath para enriquecer o extraer propiedades específicas de un mensaje. 

Para aquellos usuarios que no conocen nada de este espacio, estas características expanden la forma de procesar los mensajes dentro del flujo de trabajo. Por ejemplo, si se encuentra en un escenario de negocio a negocio y trabaja con esquemas XML específicos, puede usar Enterprise Integration Pack para mejorar cómo su empresa procesa estos mensajes. 

Enterprise Integration Pack incluye: 

* [Validación XML](logic-apps-enterprise-integration-xml-validation.md "Información sobre la validación de mensajes XML"): Permite validar un mensaje XML entrante o saliente en un esquema concreto.
* [Transformación XML](../logic-apps/logic-apps-enterprise-integration-transform.md "Información sobre las asignaciones y transformaciones de mensajes XML"): Permite convertir o personalizar un mensaje XML basándose en sus requisitos o en los de un asociado.
* [Codificación y descodificación de archivos sin formato](logic-apps-enterprise-integration-flatfile.md "Información sobre la codificación y descodificación de archivos sin formato"): permiten codificar o descodificar un archivo sin formato. Por ejemplo, SAP acepta y envía archivos IDOC en formato de archivo plano. Muchas plataformas de integración crean mensajes XML, incluida Logic Apps. Por lo tanto, puede crear una aplicación lógica que use el codificador de archivo plano para "convertir" archivos XML en archivos planos. 
* [XPath](https://msdn.microsoft.com/library/mt643789.aspx): permite enriquecer un mensaje y extraer propiedades específicas de él. Las propiedades extraídas se pueden utilizar después para redirigir el mensaje a un destino o un punto de conexión intermediario.

## <a name="try-it-out"></a>Prueba
[Implemente una aplicación lógica totalmente operativa](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-veter-pipeline) (ejemplo de GitHub) con las características XML de Azure Logic Apps.

## <a name="learn-more"></a>Más información
[Más información sobre Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Información sobre Enterprise Integration Pack")
