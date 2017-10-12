---
title: "Resolución de descodificación de edifact de Logic Apps B2B UNH2.5 - Azure Logic Apps | Microsoft Docs"
description: "Resolución de descodificación de edifact de Azure Logic Apps B2B UNH2.5"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: cf44af18-1fe5-41d5-9e06-cc57a968207c
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: 62ad8183cc6e9f56255b2729a04ee7710d00a21a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-handle-edifact-documents-having-unh25-segment"></a>Administración de documentos EDIFACT que tienen segmentos UNH2.5
Si UNH2.5 está presente en el documento EDIFACT, se va a usar para la búsqueda de esquema. 

Ejemplo: El campo UNH es **EAN008** en el mensaje EDIFACT  
UNH+SSDD1+ORDERS:D:03B:UN:**EAN008**'  

Pasos que deben seguirse para controlar el mensaje: 
1. Revisión del esquema
2. Comprobación de la configuración del acuerdo  

## <a name="update-the-schema"></a>Revisión del esquema
Para procesar el mensaje, debe implementar un esquema con el nombre del nodo raíz UNH2.5.  Para un ejemplo dado, el nombre de raíz de esquema sería **EFACT_D03B_ORDERS_EAN008**.  

Para cada D03B_ORDERS con un segmento UNH2.5 diferente, tendría que implementar un esquema individual.  

## <a name="add-schema-to-the-edifact-agreement"></a>Incorporación de un esquema al acuerdo de EDIFACT
### <a name="edifact-decode"></a>Descodificación EDIFACT
Para descodificar el mensaje entrante, configure el esquema en la configuración de recepción del acuerdo EDIFACT:
1. Agregue el esquema a la cuenta de integración.    
2. Configure el esquema en la configuración de recepción del acuerdo EDIFACT. 
3. Seleccione el acuerdo EDIFACT y haga clic en **Editar como JSON**.  Agregue un valor UNH2.5 en el acuerdo de recepción **schemaReferences**
![](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image1.png).

### <a name="edifact-encode"></a>Codificación EDIFACT
Para codificar el mensaje entrante, configure el esquema en la configuración de envío del acuerdo EDIFACT:
1. Agregue el esquema a la cuenta de integración.    
2. Configure el esquema en la configuración de envío del acuerdo EDIFACT. 
3. Seleccione el acuerdo EDIFACT y haga clic en **Editar como JSON**.  Agregue un valor UNH2.5 en el acuerdo de envío **schemaReferences**
![](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image2.png).

## <a name="next-steps"></a>Pasos siguientes
* [Más información sobre los acuerdos de cuentas de integración](../logic-apps/logic-apps-enterprise-integration-agreements.md "Más información sobre los acuerdos de cuentas de integración")  