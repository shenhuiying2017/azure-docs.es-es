---
title: Esquemas de seguimiento personalizados | Microsoft Docs
description: "Obtenga más información sobre los esquemas de seguimiento personalizados."
author: padmavc
manager: erikre
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 433ae852-a833-44d3-a3c3-14cca33403a2
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: d88fa57c2f343636d7529780dc1b27ccb604ee02
ms.openlocfilehash: 0de8128b01f760340e85078b433707c566fa2e4f


---
# <a name="custom-tracking-schemas"></a>Esquemas de seguimiento personalizados
Puede usar estos esquemas de seguimiento personalizados en su cuenta de integración de Azure para ayudarle a supervisar las transacciones de negocio a negocio (B2B):

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
* [Esquemas de seguimiento de AS2](app-service-logic-track-integration-account-as2-tracking-shemas.md)   
* [Esquemas de seguimiento de X12](app-service-logic-track-integration-account-x12-tracking-shemas.md)

## <a name="next-steps"></a>Pasos siguientes
* Más información sobre la [supervisión de mensajes B2B](app-service-logic-monitor-b2b-message.md)   
* Más información sobre el [seguimiento de mensajes B2B en el portal de Operations Management Suite](app-service-logic-track-b2b-messages-omsportal.md)
* Más información sobre el [Enterprise Integration Pack](app-service-logic-enterprise-integration-overview.md)



<!--HONumber=Dec16_HO3-->


