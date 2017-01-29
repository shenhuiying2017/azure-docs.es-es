---
title: Esquemas de seguimiento de AS2 | Microsoft Docs
description: "Más información sobre los esquemas de seguimiento de AS2"
author: padmavc
manager: erikre
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: f169c411-1bd7-4554-80c1-84351247bf94
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: e79f93c94d729a604944fc065b7016e9dbfcb9b6
ms.openlocfilehash: 65a1da23b1fdd671035f99a2a66da7c948573eb9


---
# <a name="as2-tracking-schemas"></a>Esquemas de seguimiento de AS2
Puede usar estos esquemas de seguimiento de AS2 en su cuenta de integración de Azure para ayudarle a supervisar las transacciones de negocio a negocio (B2B):

* Esquema de seguimiento de mensaje AS2
* Esquema de seguimiento de MDN AS2

## <a name="as2-message-tracking-schema"></a>Esquema de seguimiento de mensaje AS2
````java

    {
       "agreementProperties": {  
            "senderPartnerName": "",  
            "receiverPartnerName": "",  
            "as2To": "",  
            "as2From": "",  
            "agreementName": ""  
        },  
        "messageProperties": {
            "direction": "",
            "messageId": "",
            "dispositionType": "",
            "fileName": "",
            "isMessageFailed": "",
            "isMessageSigned": "",
            "isMessageEncrypted": "",
            "isMessageCompressed": "",
            "correlationMessageId": "",
            "incomingHeaders": {
            },
            "outgoingHeaders": {
            },
        "isNrrEnabled": "",
        "isMdnExpected": "",
        "mdnType": ""
        }
    }
````

| Propiedad | Tipo | Descripción |
| --- | --- | --- |
| senderPartnerName | string | Nombre de asociado del remitente del mensaje AS2. (Opcional) |
| receiverPartnerName | String | Nombre de asociado del destinatario del mensaje AS2. (Opcional) |
| as2To | string | Nombre del destinatario del mensaje AS2, de los encabezados del mensaje AS2. (Obligatorio) |
| as2From | string | Nombre del remitente del mensaje AS2, de los encabezados del mensaje AS2. (Obligatorio) |
| agreementName | String | Nombre del contrato de AS2 en el que se resuelven los mensajes. (Opcional) |
| dirección | String | Dirección del flujo de mensajes, recibidos o enviados. (Obligatorio) |
| messageId | string | Identificador del mensaje AS2, de los encabezados del mensaje AS2 (opcional) |
| dispositionType |string | Valor del tipo de disposición de notificación de disposición del mensaje (MDN). (Opcional) |
| fileName | string | Nombre de archivo, del encabezado del mensaje AS2. (Opcional) |
| isMessageFailed |Booleano | Si el mensaje AS2 genera error. (Obligatorio) |
| isMessageSigned | Booleano | Si el mensaje AS2 está firmado. (Obligatorio) |
| isMessageEncrypted | Booleano | Si el mensaje AS2 está cifrado. (Obligatorio) |
| isMessageCompressed |Booleano | Si el mensaje AS2 está comprimido. (Obligatorio) |
| correlationMessageId | string | Identificador de mensajes AS2, para correlacionar mensajes con MDN. (Opcional) |
| incomingHeaders |Diccionario de JToken | Detalles del encabezado del mensaje AS2 entrante. (Opcional) |
| outgoingHeaders |Diccionario de JToken | Detalles del encabezado del mensaje AS2 saliente. (Opcional) |
| isNrrEnabled | Booleano | Si no se conoce el valor, use el valor predeterminado. (Obligatorio) |
| isMdnExpected | Booleano | Si no se conoce el valor, use el valor predeterminado. (Obligatorio) |
| mdnType | Enum | Los valores permitidos son **NotConfigured**, **Sync** y **Async**. (Obligatorio) |

## <a name="as2-mdn-tracking-schema"></a>Esquema de seguimiento de MDN AS2
````java

    {
        "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "as2To": "",
                "as2From": "",
                "agreementName": "g"
            },
            "messageProperties": {
                "direction": "",
                "messageId": "",
                "originalMessageId": "",
                "dispositionType": "",
                "isMessageFailed": "",
                "isMessageSigned": "",
                "isNrrEnabled": "",
                "statusCode": "",
                "micVerificationStatus": "",
                "correlationMessageId": "",
                "incomingHeaders": {
                },
                "outgoingHeaders": {
                }
            }
    }
````

| Propiedad | Tipo | Descripción |
| --- | --- | --- |
| senderPartnerName | string | Nombre de asociado del remitente del mensaje AS2. (Opcional) |
| receiverPartnerName | String | Nombre de asociado del destinatario del mensaje AS2. (Opcional) |
| as2To | String | Nombre del asociado que recibe el mensaje AS2. (Obligatorio) |
| as2From | string | Nombre del asociado que envía el mensaje AS2. (Obligatorio) |
| agreementName | string | Nombre del contrato de AS2 en el que se resuelven los mensajes. (Opcional) |
| dirección |string | Dirección del flujo de mensajes, recibidos o enviados. (Obligatorio) |
| messageId | string | Identificador del mensaje AS2. (Opcional) |
| originalMessageId |string | Identificador del mensaje original AS2. (Opcional) |
| dispositionType | string | Valor de tipo de disposición de MDN. (Opcional) |
| isMessageFailed |Booleano | Si el mensaje AS2 genera error. (Obligatorio) |
| isMessageSigned |Booleano | Si el mensaje AS2 está firmado. (Obligatorio) |
| isNrrEnabled | Booleano | Si no se conoce el valor, use el valor predeterminado. (Obligatorio) |
| statusCode | Enum | Los valores permitidos son **Accepted**, **Rejected**, y **AcceptedWithErrors**. (Obligatorio) |
| micVerificationStatus | Enum | Los valores permitidos son **NotApplicable**, **Succeeded** y **Failed**. (Obligatorio) |
| correlationMessageId | string | Id. de correlación. Identificador del mensaje original (identificador del mensaje para el que está configurando MDN). (Opcional) |
| incomingHeaders | Diccionario de JToken | Indica los detalles del encabezado del mensaje entrante. (Opcional) |
| outgoingHeaders |Diccionario de JToken | Indica los detalles del encabezado del mensaje saliente. (Opcional) |

## <a name="next-steps"></a>Pasos siguientes
* Más información sobre [Enterprise Integration Pack](app-service-logic-enterprise-integration-overview.md).    
* Más información sobre la [supervisión de mensajes B2B](app-service-logic-monitor-b2b-message.md).   
* Más información sobre los [esquemas de seguimiento personalizado de B2B](app-service-logic-track-integration-account-custom-tracking-shema.md).   
* Más información sobre los [esquemas de seguimiento de X12](app-service-logic-track-integration-account-x12-tracking-shemas.md).   
* Más información sobre el [seguimiento de mensajes B2B en el portal de Operations Management Suite](app-service-logic-track-b2b-messages-omsportal.md).



<!--HONumber=Dec16_HO3-->


