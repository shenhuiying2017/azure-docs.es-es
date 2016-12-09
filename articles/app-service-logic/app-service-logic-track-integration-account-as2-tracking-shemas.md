---
title: Esquema de seguimiento de as2 | Microsoft Docs
description: "Más información acerca del esquema de seguimiento de as2"
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
ms.sourcegitcommit: 63ba6e8fec8df1cdbc9928b011130f47c5facf89
ms.openlocfilehash: 1fd5bd1038be6c35cf5fc05c9cfa000d818c5e70


---

# <a name="as2-tracking-schemas"></a>Esquemas de seguimiento de AS2
Los esquemas de seguimiento de AS2 compatibles son

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

| Propiedad | Descripción |
| --- | --- |
| senderPartnerName |Opcional, cadena.  Indica el nombre de asociado del remitente del mensaje AS2 |
| receiverPartnerName |Opcional, cadena.  Indica el nombre de asociado del destinatario del mensaje AS2 |
| as2To |Obligatorio, cadena.  Indica el nombre del destinatario del mensaje AS2 que se incluye en los encabezados del mensaje AS2 |
| as2From |Obligatorio, cadena. Indica el nombre del remitente del mensaje AS2 que se incluye en los encabezados del mensaje AS2 |
| agreementName |Opcional, cadena.  Nombre del contrato de AS2 en el que se resuelven los mensajes |
| dirección |Obligatorio, cadena.  Indica la dirección del flujo de mensajes, recepción o envío |
| messageId |Opcional, cadena.  Indica el identificador del mensaje AS2 que se incluye en los encabezados del mensaje AS2 |
| dispositionType |Opcional, cadena. Indica el valor de tipo de disposición de MDN |
| fileName |Opcional, cadena.  Indica el nombre de archivo que se incluye en los encabezados del mensaje AS2 |
| isMessageFailed |Obligatorio, booleano.  Indica si se ha producido un error en el mensaje AS2, o no |
| isMessageSigned |Obligatorio, booleano.  Indica si el mensaje AS2 estaba firmado, o no |
| isMessageEncrypted |Obligatorio, booleano. Indica si el mensaje AS2 estaba cifrado, o no |
| isMessageCompressed |Obligatorio, booleano. Indica si el mensaje AS2 estaba comprimido, o no |
| correlationMessageId |Opcional, cadena. Indica AS2messageid para correlacionar mensajes con MDN |
| incomingHeaders |Opcional, diccionario de JToken.  Indica los detalles del encabezado del mensaje AS2 entrante |
| outgoingHeaders |Opcional, diccionario de JToken.  Indica los detalles del encabezado del mensaje AS2 saliente |
| isNrrEnabled |Obligatorio, booleano.  Si no se conoce el valor, use el valor predeterminado |
| isMdnExpected |Obligatorio, booleano. Si no se conoce el valor, use el valor predeterminado |
| mdnType |Obligatorio, enum. Los valores permitidos son NotConfigured, Sync o Async |

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

| Propiedad | Descripción |
| --- | --- |
| senderPartnerName |Opcional, cadena. Indica el nombre de asociado del remitente del mensaje AS2 |
| receiverPartnerName |Opcional, cadena. Indica el nombre de asociado del destinatario del mensaje AS2 |
| as2To |Obligatorio, cadena. Indica el nombre del asociado que recibe dicho mensaje AS2 |
| as2From |Obligatorio, cadena. Indica el nombre del asociado que envía dicho mensaje AS2 |
| agreementName |Opcional, cadena. Nombre del contrato de AS2 en el que se resuelven los mensajes |
| dirección |Obligatorio, cadena. Indica la dirección del flujo de mensajes, recepción o envío |
| messageId |Opcional, cadena. Indica el identificador del mensaje AS2 |
| originalMessageId |Opcional, cadena. Indica el identificador del mensaje AS2 original |
| dispositionType |Opcional, cadena. Indica el valor de tipo de disposición de MDN |
| isMessageFailed |Obligatorio, booleano. Indica si se ha producido un error en el mensaje AS2, o no |
| isMessageSigned |Obligatorio, booleano. Indica si el mensaje AS2 estaba firmado, o no |
| isNrrEnabled |Obligatorio, booleano.  Si no se conoce el valor, use el valor predeterminado |
| statusCode |Obligatorio, enum.  Los valores permitidos son Accepted, Rejected o AcceptedWithErrros |
| micVerificationStatus |Obligatorio, enum.  Los valores permitidos son NotApplicable, Succeeded o Failed |
| correlationMessageId |Opcional, cadena.  Indica el identificador de correlación.  El identificador de correlación es el identificador de mensaje original (identificador del mensaje para el que está configurando MDN) |
| incomingHeaders |Opcional, diccionario de JToken.  Indica los detalles del encabezado del mensaje entrante |
| outgoingHeaders |Opcional, diccionario de JToken.  Indica los detalles del encabezado del mensaje saliente |

## <a name="next-steps"></a>Pasos siguientes
[Más información acerca de Enterprise Integration Pack](app-service-logic-enterprise-integration-overview.md "Learn about Enterprise Integration Pack")    
[Más información acerca de la supervisión de mensajes B2B](app-service-logic-monitor-b2b-message.md "Learn more about tracking B2B messages")   
[Tracking B2B messages in OMS portal](app-service-logic-track-b2b-messages-omsportal.md "Tracking B2B messages")  (Seguimiento de mensajes B2B en el Portal de OMS)  
[Más información acerca del esquema de seguimiento personalizado de B2B](app-service-logic-track-integration-account-custom-tracking-shema.md "Learn about Custom Schema")   
[Más información acerca del esquema de seguimiento de X12](app-service-logic-track-integration-account-x12-tracking-shemas.md "Learn about X12 Tracking Schema")   
[Más información acerca de Enterprise Integration Pack](app-service-logic-enterprise-integration-overview.md "Learn about Enterprise Integration Pack")  




<!--HONumber=Nov16_HO3-->


