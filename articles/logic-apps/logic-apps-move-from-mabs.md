---
title: "Migración de aplicaciones desde BizTalk Services a Azure Logic Apps | Microsoft Docs"
description: "Migración de Azure BizTalk Services (MABS) a Logic Apps"
services: logic-apps
documentationcenter: 
author: jonfancey
manager: anneta
editor: 
ms.assetid: 
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: ladocs; jonfan; mandia
ms.openlocfilehash: e58c6950d1d9420f32fc98ca917216dc5fae4fc3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="move-from-biztalk-services-to-logic-apps"></a>Migración desde BizTalk Services a Logic Apps

Microsoft Azure BizTalk Services (MABS) está en retirada. Use este tema para migrar las soluciones de integración de MABS a Azure Logic Apps. 

## <a name="overview"></a>Información general

BizTalk Services consta de dos subservicios:

1.  Conexiones híbridas de Microsoft BizTalk Services
2.  Integración basada en puente de EAI y EDI

Si lo que busca es migrar conexiones híbridas, [Conexiones híbridas de Azure App Service](../app-service/app-service-hybrid-connections.md) describe los cambios y las características de este servicio. Conexiones híbridas de Azure reemplaza a Conexiones híbridas de BizTalk Services. Conexiones híbridas de Azure está disponible en Azure App Service y se ofrece en Azure Portal. Conexiones híbridas de Azure también proporciona un nuevo administrador de conexiones híbridas para administrar las conexiones híbridas existentes de BizTalk Services y las conexiones híbridas nuevas que cree en el portal. Conexiones híbridas de Azure App Service está disponible con carácter general (GA).

En el caso de la integración basada en puente de EAI y EDI, el reemplazo es Logic Apps. Logic Apps proporciona las mismas funcionalidades que BizTalk Services, e incluso más. Logic Apps proporciona características de orquestación y flujo de trabajo basado en consumo a escala de nube que permiten compilar rápida y fácilmente soluciones complejas de integración con un explorador o a través de herramientas dentro de Visual Studio.

La tabla siguiente muestra una asignación de las funcionalidades de BizTalk Services a Logic Apps.

| Servicios de BizTalk   | Aplicaciones lógicas            | Propósito                  |
| ------------------ | --------------------- | ---------------------------- |
| Conector          | Conector             | Envío y recepción de datos   |
| Puente             | Aplicación lógica             | Procesador de canalización           |
| Fase de validación     | Acción de validación XML      | Validación de un documento XML con respecto a un esquema             |
| Fase de enriquecimiento       | Tokens de datos      | Promoción de propiedades en mensajes o para enrutar decisiones             |
| Fase de transformación    | Acción de transformación      | Conversión de mensajes XML de un formato a otro             |
| Fase de descodificación       | Acción de descodificación de archivo plano      | Conversión de archivo plano a XML             |
| Fase de codificación       |  Acción de codificación de archivo plano      | Conversión de XML a archivo plano             |
| Inspector de mensajes       |  Azure Functions o API Apps      | Ejecución de código personalizado en las integraciones             |
| Acción de enrutamiento      |  Condición o modificador      | Enrutamiento de mensajes a uno de los conectores especificados             |

Existe una gran cantidad de tipos de artefactos distintos en BizTalk Services.

## <a name="connectors"></a>Conectores
Los conectores de BizTalk Services permiten que los puentes envíen y reciban datos, incluidos los puentes bidireccionales que habilitaron interacciones de solicitud/respuesta basadas en HTTP. La misma terminología se usa en Logic Apps. Los conectores de Logic Apps tienen el mismo propósito y también incluyen más de 140 que se pueden conectar a una amplia variedad de tecnologías y servicios, tanto locales con la puerta de enlace de datos local (que reemplaza el servicio de adaptador de BizTalk usado por BizTalk Services), como servicios de PaaS y SaaS de nube, como OneDrive, Office365, Dynamics CRM y muchos otros.

Los orígenes de BizTalk Services están limitados a FTP, SFTP y suscripción a Tema o Cola de Service Bus.

![](media/logic-apps-move-from-mabs/sources.png)

Cada puente tiene un punto de conexión HTTP predeterminado, el que se configura con las propiedades Dirección en tiempo de ejecución y Dirección relativa del puente. Para lograr el mismo resultado con Logic Apps, use las acciones [Solicitud y respuesta](../connectors/connectors-native-reqres.md).

## <a name="xml-processing-and-bridges"></a>Puentes y procesamiento de XML
Un puente en BizTalk Services es análogo a una canalización de procesamiento. Un puente puede tomar los datos recibidos desde un conector y trabajar con esos datos, para luego enviarlos a otro sistema. Logic Apps hace exactamente lo mismo ya que es compatible con los mismos patrones de interacción basada en canalización que BizTalk Services y, además, proporciona una variedad de otros patrones de integración. El [puente de solicitud-respuesta XML](https://msdn.microsoft.com/library/azure/hh689781.aspx) e BizTalk Services es conocido como una canalización VETER, que consta de fases en que se realiza:

* (V) Validación
* (E) Enriquecimiento
* (T) Transformación
* (E) Enriquecimiento
* (R) EnRutamiento

Tal como se muestra en la imagen siguiente, el procesamiento se divide entre solicitud y respuesta, y permite controlar las rutas de solicitud y respuesta de forma separada, por ejemplo, con distintas asignaciones para cada una:

![](media/logic-apps-move-from-mabs/xml-request-reply.png)

Además, un puente unidireccional XML agrega las fases de Descodificación y Codificación al comienzo y al final del procesamiento, y el puente de paso a través consta de una sola fase de Enriquecimiento.

### <a name="message-processing-and-decodingencoding"></a>Procesamiento y descodificación/codificación de mensajes
En BizTalk Services, se reciben mensajes XML de distintos tipos y se determina el esquema coincidente para el mensaje recibido. Esta acción se realiza en la fase **Tipos de mensaje** de la canalización de procesamiento de recepción. Luego, la fase de descodificación usa el tipo de mensaje detectado para descodificarlo con el esquema proporcionado. Si el esquema es un esquema de archivo plano, convierte el archivo plano entrante en XML. 

Logic Apps proporciona funcionalidades similares. Recibe un archivo plano a través de varios protocolos distintos con los distintos desencadenadores de conector (sistema de archivos, FTP, HTTP, etc.) y usa la acción [Descodificación de archivo plano](../logic-apps/logic-apps-enterprise-integration-flatfile.md) para convertir los datos entrantes en XML. Puede mover los esquemas de archivo plano existentes directamente a Logic Apps sin que sea necesario hacer ningún cambio y, luego, cargar los esquemas en la cuenta de integración.

### <a name="validation"></a>Validación
Una vez que los datos entrantes se convierten en XML (o si el formato de mensaje que se recibió era XML), la validación se ejecuta para determinar si el mensaje cumple con el esquema XSD. Para hacer esto mismo en Logic Apps, use la acción [Validación XML](../logic-apps/logic-apps-enterprise-integration-xml-validation.md). Como ya indicamos, puede usar los mismos esquemas de BizTalk Services sin hacer ningún cambio.

### <a name="transform-messages"></a>Transformación de mensajes
En BizTalk Services, la fase de transformación convierte un formato de mensaje XML en otro. Para hacerlo aplicando un mapa, use el mapeador basado en TRFM. El proceso es similar en Logic Apps. La acción de transformación ejecuta un mapa desde la cuenta de integración. La principal diferencia es que los mapas de Logic Apps están en el formato XSLT. XSLT incluye la capacidad de volver a usar el XSLT existente que ya tiene, incluidos los mapas creados para BizTalk Server que contienen functioids. 

### <a name="routing-rules"></a>Reglas de enrutamiento
BizTalk Services toma una decisión de enrutamiento sobre qué punto de conexión/conector envía los mensajes/datos entrantes. La capacidad de seleccionar uno de los puntos de conexión preconfigurados es posible con la opción de filtro de enrutamiento:

![](media/logic-apps-move-from-mabs/route-filter.png)

Logic Apps ofrece funcionalidades lógicas más sofisticadas con [Condition](../logic-apps/logic-apps-use-logic-app-features.md) (Condición) y [Switch](../logic-apps/logic-apps-switch-case.md) (Modificador), lo que permite un flujo de control y enrutamiento avanzados. Convertir los filtros de enrutamiento en BizTalk Services se logra mejor con una **condición** *si* solo hay dos opciones. Si hay más de dos, use un **modificador**.

### <a name="enrich"></a>Enriquecimiento
La fase de enriquecimiento del procesamiento de BizTalk Services agrega propiedades al contexto de mensaje asociado con los datos recibidos. Por ejemplo, promover una propiedad para usarla para el enrutamiento (descrito a continuación) desde una búsqueda en base de datos o mediante la extracción de un valor con una expresión XPath. Logic Apps proporciona acceso a todas las salidas de datos contextuales de acciones precedentes, lo que simplifica replicar el mismo comportamiento. Por ejemplo, mediante la acción de conexión SQL `Get Row`, devuelve datos desde una base de datos de SQL Server y usa esos datos en una acción de decisión para enrutamiento. Del mismo modo, las propiedades en mensajes en cola entrantes de Service Bus por un desencadenador son direccionables, al igual que XPath con la expresión de lenguaje de definición de flujo de trabajo XPath.

### <a name="use-custom-code"></a>Uso de código personalizado
BizTalk Services proporciona la capacidad de [ejecutar código personalizado](https://msdn.microsoft.com/library/azure/dn232389.aspx) cargado en sus propios ensamblados. Esto se implementa mediante la interfaz [IMessageInspector](https://msdn.microsoft.com/library/microsoft.biztalk.services.imessageinspector.aspx). Cada fase del puente incluye dos propiedades (On Enter Inspector y On Exit Inspector) que proporciona el tipo de .NET que se creó que implementa esta interfaz. El código personalizado le permite realizar procesamientos más complejos en los datos, así como volver a usar código existente en ensamblados que realizan una lógica de negocios común. 

Logic Apps proporciona dos maneras principales para ejecutar el código personalizado: Azure Functions y API Apps. Es posible crear y llamar a Azure Functions desde aplicaciones lógicas. Consulte [Adición y ejecución de código personalizado para aplicaciones lógicas con Azure Functions](../logic-apps/logic-apps-azure-functions.md). Use API Apps, parte de Azure App Service, para crear sus propios desencadenadores y acciones. Obtenga más información sobre cómo [crear una API personalizada para usarla con Logic Apps](../logic-apps/logic-apps-create-api-app.md). 

Si tiene código personalizado en ensamblados a los que llama desde BizTalk Services, puede mover este código a Azure Functions o crear API personalizadas con API Apps, en función de lo que está implementando. Por ejemplo, si tiene código que encapsula otro servicio para el que Logic Apps no tiene conector, cree una instancia de API Apps y use las acciones que esta instancia proporciona dentro de la aplicación lógica. Si tiene funciones o bibliotecas auxiliares, Azure Functions es probablemente la mejor opción.

### <a name="edi-processing-and-trading-partner-management"></a>Administración de entidades y procesamiento de EDI
BizTalk Services incluye procesamiento de EDI y B2B con compatibilidad con AS2 (Applicability Statement 2), X12 y EDIFACT. Logic Apps también lo hace. En BizTalk Services, crea puentes de EDI y crea y administra entidades y contratos en el portal de seguimiento y administración dedicado.

En Logic Apps, esta funcionalidad se incluye en [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md). Consta de la cuenta de integración y acciones de B2B para procesamiento de EDI y B2B. La [cuenta de integración](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) se usa para crear y administrar [entidades](../logic-apps/logic-apps-enterprise-integration-partners.md) y [contratos](../logic-apps/logic-apps-enterprise-integration-agreements.md). Una vez que crea una cuenta de integración, puede asociar una o más aplicaciones lógicas a la cuenta. Una vez que las asocie, puede usar las acciones de B2B para obtener acceso a la información de entidades dentro de la aplicación lógica. Se proporcionan las siguientes acciones:

* Codificación AS2
* Descodificación AS2
* Codificación X12
* Descodificación X12
* Codificación EDIFACT
* Descodificación EDIFACT

A diferencia de BizTalk Services, estas acciones se separan de los protocolos de transporte. Por lo tanto, cuando crea las aplicaciones lógicas, tiene más flexibilidad sobre qué conectores usar para enviar y recibir datos. Por ejemplo, es posible recibir archivos X12 como archivos adjuntos de correo electrónico y luego procesar esos archivos en una aplicación lógica. 

## <a name="manage-and-monitor"></a>Administración y supervisión
Del mismo modo que con la administración de entidades, el portal dedicado para BizTalk Services proporcionó funcionalidades de seguimiento para supervisar y solucionar problemas. 

Logic Apps ofrece funcionalidades más avanzadas de seguimiento y supervisión en [Azure Portal](../logic-apps/logic-apps-monitor-your-logic-apps.md) y con la [solución B2B de Operations Management Suite](../logic-apps/logic-apps-monitor-b2b-message.md), incluida una aplicación móvil para supervisión cuando esté en movimiento.

## <a name="high-availability"></a>Alta disponibilidad
Para alcanzar alta disponibilidad (HA) en BizTalk Services, usa más de una instancia de una región predeterminada para compartir la carga de procesamiento. Con las aplicaciones lógicas, la alta disponibilidad en la región está integrada y no supone ningún costo adicional. En el caso de la recuperación ante desastres fuera de la región para el procesamiento B2B en BizTalk Services, se requiere un proceso de copia de seguridad y restauración. En Logic Apps, se proporciona una [funcionalidad de DR](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md) activa/pasiva entre regiones, lo que permite realizar la sincronización de datos B2B entre cuentas de integración en distintas regiones para la continuidad empresarial.

## <a name="next"></a>Pasos siguientes
* [¿Qué es Logic Apps?](logic-apps-what-are-logic-apps.md)
* [Cree la primera aplicación lógica](logic-apps-create-a-logic-app.md) o empiece a trabajar rápidamente mediante una [plantilla precompilada](logic-apps-use-logic-app-templates.md)  
* [Vea todos los conectores disponibles](../connectors/apis-list.md) que se pueden utilizar en una aplicación lógica
