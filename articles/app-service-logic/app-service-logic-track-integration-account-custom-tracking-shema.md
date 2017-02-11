---
title: Esquema de seguimiento personalizado | Microsoft Docs
description: "Más información acerca del esquema de seguimiento personalizado"
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
ms.sourcegitcommit: 63ba6e8fec8df1cdbc9928b011130f47c5facf89
ms.openlocfilehash: f846f23bef61e8e772920196b9c81d059546d8a6


---
# <a name="custom-tracking-schema"></a>Esquema de seguimiento personalizado
## <a name="supported-custom-tracking-schema"></a>Esquema de seguimiento personalizado que se admite
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

| Propiedad | Descripción |
| --- | --- |
| sourceType |Obligatorio.  Indica el tipo de origen de la ejecución. Los valores permitidos son Microsoft.Logic/workflows o personalizado |
| Source |Obligatorio: si el tipo de origen es Microsoft.Logic/workflows, la información de origen tiene que ir detrás de este esquema. Si el tipo de origen es 'personalizado'; el esquema es un JToken  |
| systemId |Obligatorio, cadena.  Indica el identificador de sistema de la aplicación lógica |
| runId |Obligatorio, cadena.  Indica el identificador de ejecución de la aplicación lógica |
| operationName |Obligatorio, cadena.  Indica el nombre de la operación (por ejemplo, acción o desencadenador) |
| repeatItemScopeName |Obligatorio, cadena. Indica el nombre del elemento de repetición si la acción está dentro de un bucle foreach/until |
| repeatItemIndex |Obligatorio, entero.  Si la acción está dentro de un bucle foreach/until; indica el índice del elemento repetido  |
| trackingId |Opcional, cadena. Indica el identificador de seguimiento para correlacionar los mensajes |
| correlationId |Opcional, cadena. Indica el identificador de correlación para correlacionar los mensajes |
| clientRequestId |Opcional, cadena.  El cliente puede rellenarlo para correlacionar mensajes |
| eventLevel |Obligatorio. Indica el nivel del evento |
| eventTime |Obligatorio. Indica la hora del evento, en formato UTC AAAA-MM-DD:MM:SS.00000Z |
| recordType |Obligatorio, indica el tipo de registro de seguimiento. El valor permitido es Personalizado |
| record |Obligatorio.  Indica el tipo de registro personalizado y el formato permitido es JToken |
|  | |

## <a name="supported-b2b-protocol-tracking-schemas"></a>Esquemas de seguimiento de protocolos B2B permitidos
* [Esquema de seguimiento de AS2](app-service-logic-track-integration-account-as2-tracking-shemas.md)   
* [Esquema de seguimiento de X12](app-service-logic-track-integration-account-x12-tracking-shemas.md) 

## <a name="next-steps"></a>Pasos siguientes
[Más información acerca de la supervisión de mensajes B2B](app-service-logic-monitor-b2b-message.md "Learn more about tracking B2B messages")   
[Tracking B2B messages in OMS portal](app-service-logic-track-b2b-messages-omsportal.md "Tracking B2B messages")  (Seguimiento de mensajes B2B en el Portal de OMS)  
[Más información acerca de Enterprise Integration Pack](app-service-logic-enterprise-integration-overview.md "Learn about Enterprise Integration Pack")   




<!--HONumber=Nov16_HO3-->


