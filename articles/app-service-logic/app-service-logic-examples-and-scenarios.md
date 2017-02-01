---
title: Ejemplos y escenarios de Logic Apps | Microsoft Docs
description: "Vea ejemplos comunes de Aplicaciones lógicas y aprenda a implementar escenarios comunes"
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: erikre
editor: 
ms.assetid: e06311bc-29eb-49df-9273-1f05bbb2395c
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 4fde1a22ac76306e79a68a37e110828da9a2ef13
ms.openlocfilehash: 3cf2d8b19dd5785317a382dc429feda02d8a2f20


---
# <a name="logic-apps-examples-and-common-scenarios"></a>Ejemplos de aplicaciones lógicas y escenarios comunes
En este documento se detallan escenarios y ejemplos comunes que le ayudan a conocer algunas de las formas que Aplicaciones lógicas puede utilizar para automatizar los procesos empresariales. 

## <a name="custom-triggers-and-actions"></a>Desencadenadores y acciones personalizados
Hay varias maneras de desencadenar una aplicación lógica desde otra aplicación. Presentamos algunos ejemplos comunes:

* [Crear un desencadenador o una acción personalizados](app-service-logic-create-api-app.md)
* [Acciones de ejecución prolongada](app-service-logic-create-api-app.md)
* [Desencadenador de solicitud HTTP (POST)](app-service-logic-http-endpoint.md)
* [Desencadenadores y acciones de Webhook](app-service-logic-create-api-app.md)
* [Desencadenadores de sondeo](app-service-logic-create-api-app.md)

### <a name="scenarios"></a>Escenarios
* [Respuesta sincrónica de solicitudes](app-service-logic-http-endpoint.md)
* [Solicitud/respuesta con SMS](https://channel9.msdn.com/Blogs/Windows-Azure/Azure-Logic-Apps-Walkthrough-Webhook-Functions-and-an-SMS-Bot)

## <a name="error-handling-and-logging"></a>Registro y control de errores
* [Excepciones y control de errores](app-service-logic-exception-handling.md)
* [Configuración de alertas y diagnósticos de Azure](app-service-logic-monitor-your-logic-apps.md)

### <a name="scenarios"></a>Escenarios
* [Caso de uso: Control de errores y excepciones](app-service-logic-scenario-error-and-exception-handling.md)

## <a name="deploying-and-managing"></a>Implementación y administración
* [Creación de una implementación automatizada](app-service-logic-create-deploy-template.md)
* [Creación e implementación de aplicaciones lógicas en Visual Studio](app-service-logic-deploy-from-vs.md)
* [Supervisión de aplicaciones lógicas](app-service-logic-monitor-your-logic-apps.md)

## <a name="content-types-conversions-and-transformations"></a>Tipos de contenido, conversiones y transformaciones
El [lenguaje de definición de flujo de trabajo](http://aka.ms/logicappsdocs) de Logic Apps contiene muchas funciones que permiten convertir distintos tipos de contenido y trabajar con ellos. Además, el motor hará todo que lo posible para conservar los tipos de contenido, ya que los datos pasan por el flujo de trabajo.

* [Control del tipo de contenido](app-service-logic-content-type.md) como application/json, application/xml y text/plain
* [Creación de definiciones de flujos de trabajo](app-service-logic-author-definitions.md)
* [Referencia de lenguaje de definición de flujo de trabajo](http://aka.ms/logicappsdocs)

## <a name="batches-and-looping"></a>Lotes y bucles
* [SplitOn](app-service-logic-loops-and-scopes.md)
* [ForEach](app-service-logic-loops-and-scopes.md)
* [Until](app-service-logic-loops-and-scopes.md)

## <a name="integrating-with-azure-functions"></a>Integración con Funciones de Azure
* [Integración de Funciones de Azure](app-service-logic-azure-functions.md)

### <a name="scenarios"></a>Escenarios
* [Funciones de Azure como desencadenador de Bus de servicio](app-service-logic-scenario-function-sb-trigger.md)

## <a name="http-rest-and-soap"></a>HTTP, REST y SOAP
* [Llamadas a SOAP](https://blogs.msdn.microsoft.com/logicapps/2016/04/07/using-soap-services-with-logic-apps/)

Seguiremos agregando ejemplos y escenarios a este documento. Utilice la sección de comentarios que aparece a continuación para indicarnos qué ejemplos o escenarios que le gustaría ver aquí.




<!--HONumber=Dec16_HO2-->


