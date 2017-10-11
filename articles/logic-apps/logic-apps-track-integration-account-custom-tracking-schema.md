---
title: "Esquemas de seguimiento personalizados para la supervisión de B2B - Azure Logic Apps | Microsoft Docs"
description: "Cree esquemas de seguimiento personalizados para supervisar mensajes B2B de transacciones en la cuenta de la integración de Azure."
author: padmavc
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 433ae852-a833-44d3-a3c3-14cca33403a2
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: LADocs; padmavc
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b71a4938dde2a71f1ce29403af7aa9101358d64c
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="enable-tracking-to-monitor-your-complete-workflow-end-to-end"></a>Habilitación del seguimiento para supervisar el flujo de trabajo completo de manera integral
Hay disponible una característica de seguimiento integrada que puede habilitar para las distintas partes de su flujo de trabajo de negocio a negocio, por ejemplo, para realizar un seguimiento de mensajes AS2 o X12. Cuando cree flujos de trabajo que incluyan una lógica de aplicación, BizTalk Server, SQL Server o cualquier otra capa, podrá habilitar el seguimiento personalizado que registre los eventos desde el principio hasta el final del flujo de trabajo. 

En este tema se proporciona un código personalizado que puede usar en las capas externas de la aplicación lógica. 

## <a name="custom-tracking-schema"></a>Esquema de seguimiento personalizado
````java

        {
            "sourceType": "",
            "source": {

            "workflow": {
                "systemId": ""
            },
            "runInstance": {
                "runId": ""
            },
            "operation": {
                "operationName": "",
                "repeatItemScopeName": "",
                "repeatItemIndex": "",
                "trackingId": "",
                "correlationId": "",
                "clientRequestId": ""
                }
            },
            "events": [
            {
                "eventLevel": "",
                "eventTime": "",
                "recordType": "",
                "record": {                
                }
            }
         ]
      }

````

| Propiedad | Escriba | Descripción |
| --- | --- | --- |
| sourceType |   | Tipo del origen de ejecución. Los valores permitidos son **Microsoft.Logic/workflows** y **custom**. (Obligatorio). |
| Origen |   | Si el tipo de origen es **Microsoft.Logic/workflows**, la información de origen tiene que ir detrás de este esquema. Si el tipo de origen es **custom**, el esquema es un JToken (Obligatorio). |
| systemId | String | Id. de sistema de aplicación lógica. (Obligatorio). |
| runId | string | Id. de ejecución de aplicación lógica. (Obligatorio). |
| operationName | string | Nombre de la operación (por ejemplo, acción o desencadenador). (Obligatorio). |
| repeatItemScopeName | String | Repita el nombre del elemento si la acción se encuentra dentro de un bucle `foreach`/`until`. (Obligatorio). |
| repeatItemIndex | Entero  | Si la acción se encuentra dentro de un bucle `foreach`/`until`. Indica el índice del elemento repetido. (Obligatorio). |
| trackingId | String | Id. de seguimiento para correlacionar los mensajes. (Opcional). |
| correlationId | String | Id. de correlación para correlacionar los mensajes. (Opcional). |
| clientRequestId | String | El cliente puede rellenarlo para correlacionar mensajes. (Opcional). |
| eventLevel |   | Nivel del evento. (Obligatorio). |
| eventTime |   | Hora del evento, en formato UTC: AAAA-MM-DDTHH:MM:SS.00000Z. (Obligatorio). |
| recordType |   | Tipo del registro. El valor permitido es **custom**. (Obligatorio). |
| record |   | Tipo de registro personalizado. El formato permitido es JToken. (Obligatorio). |

## <a name="b2b-protocol-tracking-schemas"></a>Esquemas de seguimiento de protocolos B2B
Para obtener información sobre los esquemas de seguimiento de protocolos B2B, consulte:
* [Esquemas de seguimiento de AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)   
* [Esquemas de seguimiento de X12](logic-apps-track-integration-account-x12-tracking-schema.md)

## <a name="next-steps"></a>Pasos siguientes
* Más información sobre la [supervisión de mensajes B2B](logic-apps-monitor-b2b-message.md)   
* Más información sobre el [seguimiento de mensajes B2B en el portal de Operations Management Suite](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)
* Más información sobre el [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)
