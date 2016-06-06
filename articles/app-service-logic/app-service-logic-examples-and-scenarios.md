<properties
   pageTitle="Ejemplos de aplicaciones lógicas y escenarios comunes | Microsoft Azure"
   description="Vea ejemplos comunes de Aplicaciones lógicas y aprenda a implementar escenarios comunes"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="msftman"
   manager="erikre"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="04/25/2016"
   ms.author="deonhe"/>

# Ejemplos de aplicaciones lógicas y escenarios comunes

En este documento se detallan escenarios y ejemplos comunes que le ayudan a conocer algunas de las formas que Aplicaciones lógicas puede utilizar para automatizar los procesos empresariales.

## Desencadenamiento de una aplicación lógica

Hay varias maneras de desencadenar una aplicación lógica desde otra aplicación. Estas son algunas de ellas:

- [Solicitud HTTP (POST)](app-service-logic-http-endpoint.md)
- [Recepción de un webhook](app-service-logic-create-api-app.md)
- [Adición de un punto de conexión](app-service-logic-create-api-app.md)

### Escenarios

- [Respuesta sincrónica de las solicitudes de las aplicaciones lógicas](app-service-logic-http-endpoint.md)

## Tipos de contenido, conversiones y transformaciones

El [lenguaje de definición de flujo de trabajo](http://aka.ms/logicappsdocs) de las aplicaciones lógicas contiene muchas funciones que permiten convertir y trabajar con distintos tipos de contenido. Además, el motor hará todo que lo posible para conservar los tipos de contenido, ya que los datos pasan por el flujo de trabajo.

- [Control de tipos de contenido](app-service-logic-content-type.md) como application/json, application/xml, and plain/text.
- [Lenguaje de definición de flujo de trabajo](http://aka.ms/logicappsdocs)

## Acciones de ejecución prolongada

- [Creación de una acción de ejecución prolongada](app-service-logic-create-api-app.md)

## Lotes y bucles

- [SplitOn](app-service-logic-loops-and-scopes.md)
- [ForEach](app-service-logic-loops-and-scopes.md)
- [Until](app-service-logic-loops-and-scopes.md)

## Integración con flujos de trabajo anidados y funciones de Azure

- [Integración de funciones de Azure](app-service-logic-azure-functions.md)

## HTTP, REST y SOAP

 - [Llamadas a SOAP](https://blogs.msdn.microsoft.com/logicapps/2016/04/07/using-soap-services-with-logic-apps/)


Seguiremos agregando ejemplos y escenarios a este documento. Utilice la sección de comentarios que aparece a continuación para indicarnos qué ejemplos o escenarios que le gustaría ver aquí.

<!---HONumber=AcomDC_0525_2016-->