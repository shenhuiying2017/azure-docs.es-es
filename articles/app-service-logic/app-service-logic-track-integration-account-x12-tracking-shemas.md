---
title: Esquemas de seguimiento de X12 | Microsoft Docs
description: "Más información acerca de los esquemas de seguimiento de X12"
author: padmavc
manager: erikre
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: a5413f80-eaad-4bcf-b371-2ad0ef629c3d
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 63ba6e8fec8df1cdbc9928b011130f47c5facf89
ms.openlocfilehash: 48dfc8327b7e2dfb16b0392fd6d44e83bdfb1177


---
# <a name="x12-tracking-schemas"></a>Esquemas de seguimiento de X12
Los esquemas de seguimiento de X12 compatibles son

* Esquema de seguimiento de conjuntos de transacciones de X12
* Esquema de seguimiento de confirmación de conjuntos de transacciones de X12
* Esquema de seguimiento de intercambio de X12
* Esquema de seguimiento de confirmación de intercambio de X12
* Esquema de seguimiento de grupos funcionales de X12
* Esquema de seguimiento de confirmación de grupos funcionales de X12

## <a name="x12-transaction-set-tracking-schema"></a>Esquema de seguimiento de conjuntos de transacciones de X12
````java

    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "functionalGroupControlNumber": "",
                "transactionSetControlNumber": "",
                "CorrelationMessageId": "", 
                "messageType": "",
                "isMessageFailed": "",
                "isTechnicalAcknowledgmentExpected": "",
                "isFunctionalAcknowledgmentExpected": "",
                "needAk2LoopForValidMessages":  "",
                "segmentsCount": ""
            }
    }
````

| Propiedad | Descripción |
| --- | --- |
| senderPartnerName |Opcional, cadena.  Indica el nombre del asociado de envío de mensajes de X12 |
| receiverPartnerName |Opcional, cadena.  Indica el nombre del asociado de recepción de mensajes de X12 |
| senderQualifier |Obligatorio, cadena.  Indica el calificador del asociado de envío |
| senderIdentifier |Obligatorio, cadena.  Indica el identificador del asociado de envío |
| receiverQualifier |Obligatorio, cadena.  Indica el identificador del asociado de recepción |
| receiverIdentifier |Obligatorio, cadena.  Indica el identificador del asociado de recepción |
| agreementName |Opcional, cadena.  Nombre del contrato X12 en el que se resuelven los mensajes |
| dirección |Obligatorio, enum.  Indica la dirección del flujo de mensajes.  Los valores permitidos son recepción o envío |
| interchangeControlNumber |Opcional, cadena.  Indica el número de control de intercambio |
| functionalGroupControlNumber |Opcional, cadena.  Indica el número de control funcional |
| transactionSetControlNumber |Opcional, cadena.  Indica el número de control del conjunto de transacciones |
| CorrelationMessageId |Opcional, cadena.  Indica el identificador de mensaje de correlación.  El identificador de correlación es la combinación de {AgreementName}*{GroupControlNumber}*{TransactionSetControlNumber} |
| messageType |Opcional, cadena. Indica el conjunto de transacciones o el tipo de documento |
| isMessageFailed |Obligatorio, booleano.  Indica si el mensaje de X12 se ha enviado correctamente o se ha producido un error |
| isTechnicalAcknowledgmentExpected |Obligatorio, booleano.  Indica si la confirmación técnica se ha configurado, o no, en el acuerdo de X12 |
| isFunctionalAcknowledgmentExpected |Obligatorio, booleano.  Indica si la confirmación funcional se ha configurado, o no, en el acuerdo de X12 |
| needAk2LoopForValidMessages |Obligatorio, booleano.  -Si el bucle se AK2 se requiere para los mensajes válidos, o no. - Tipo booleano |
| segmentsCount |Opcional, entero.  Indica el número de segmentos del conjunto de transacciones de X12 |

## <a name="x12-transaction-set-acknowledgement-tracking-schema"></a>Esquema de seguimiento de confirmación de conjuntos de transacciones de X12
````java

    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "functionalGroupControlNumber": "",
                "isaSegment": "",
                "gsSegment": "",
                "respondingfunctionalGroupControlNumber": "",
                "respondingFunctionalGroupId": "",
                "respondingtransactionSetControlNumber": "",
                "respondingTransactionSetId": "",
                "statusCode": "",
                "processingStatus": "",
                "CorrelationMessageId": ""
                "isMessageFailed": "",
                "ak2Segment": "",
                "ak3Segment": "",
                "ak5Segment": ""
            }
    }
````

| Propiedad | Descripción |
| --- | --- |
| senderPartnerName |Opcional, cadena.  Indica el nombre de asociado del remitente del mensaje de X12 |
| receiverPartnerName |Opcional, cadena.  Indica el nombre de asociado del destinatario del mensaje de X12 |
| senderQualifier |Obligatorio, cadena.  Indica el calificador del asociado de envío |
| senderIdentifier |Obligatorio, cadena.  Indica el identificador del asociado de envío |
| receiverQualifier |Obligatorio, cadena.  Indica el identificador del asociado de recepción |
| receiverIdentifier |Obligatorio, cadena.  Indica el identificador del asociado de recepción |
| agreementName |Opcional, cadena.  Nombre del contrato X12 en el que se resuelven los mensajes |
| dirección |Obligatorio, enum.  Indica la dirección del flujo de mensajes.  Los valores permitidos son recepción o envío |
| interchangeControlNumber |Opcional, cadena.  Indica el número de control de intercambio de la confirmación funcional. El valor se rellena solo en el lado del envío en el que se ha recibido confirmación funcional de los mensajes enviados al asociado |
| functionalGroupControlNumber |Opcional, cadena.  Indica el número de control del grupo funcional de la confirmación funcional. El valor se rellena solo en el lado del envío en el que se ha recibido confirmación funcional de los mensajes enviados al asociado |
| isaSegment |Opcional, cadena.  Indica el segmento ISA del mensaje. El valor se rellena solo en el lado del envío en el que se ha recibido confirmación funcional de los mensajes enviados al asociado |
| gsSegment |Opcional, cadena.  Indica el segmento GS del mensaje. El valor se rellena solo en el lado del envío en el que se ha recibido confirmación funcional de los mensajes enviados al asociado |
| respondingfunctionalGroupControlNumber |Opcional, cadena.  Indica el número de control de intercambio de respuesta |
| respondingFunctionalGroupId |Opcional, cadena. Indica el identificador del grupo funcional de respuesta, que se asigna a AK101 en la confirmación |
| respondingtransactionSetControlNumber |Opcional, cadena.  Indica el número de control del conjunto de transacciones de la respuesta |
| respondingTransactionSetId |Opcional, cadena.  Indica el identificador del conjunto de transacciones de respuesta, que se asigna a AK201 en la confirmación |
| statusCode |Obligatorio, booleano.  Indica el código de estado de la confirmación del conjunto de transacciones |
| segmentsCount |Obligatorio, enum.  Indica el código de estado de la confirmación.  Se permiten los siguientes valores: Accepted, Rejected o AccpetedWithErrros |
| processingStatus |Obligatorio, enum.  Indica el estado de procesamiento de la confirmación.  Se permiten los siguientes valores: Received, Generated o Sent |
| CorrelationMessageId |Opcional, cadena.  Indica el identificador de mensaje de correlación.  El identificador de correlación es la combinación de {AgreementName}*{GroupControlNumber}*{TransactionSetControlNumber} |
| isMessageFailed |Obligatorio, booleano.  Indica si el mensaje de X12 se ha enviado correctamente o se ha producido un error |
| ak2Segment |Opcional, cadena. Indica el segmento ak2. El segmento ak2 indica una confirmación para un conjunto de transacciones del grupo funcional recibido |
| ak3Segment |Opcional, cadena. Indica el segmento ak3.  El segmento ak3 indica que hay errores en un segmento de datos |
| ak5Segment |Opcional, cadena. Indica el segmento ak5.  El segmento ak5 indica si el conjunto de transacciones identificado en el segmento de AK2 se acepta o se rechaza, y el motivo de la aceptación o rechazo |

## <a name="x12-interchange-tracking-schema"></a>Esquema de seguimiento de intercambio de X12
````java

    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "isaSegment": "",
                "isTechnicalAcknowledgmentExpected": "",
                "isMessageFailed": "",
                "isa09": "",
                "isa10": "",
                "isa11": "",
                "isa12": "",
                "isa14": "",
                "isa15": "",
                "isa16": ""
            }
    }
````

| Propiedad | Descripción |
| --- | --- |
| senderPartnerName |Opcional, cadena.  Indica el nombre de asociado del remitente del mensaje de X12 |
| receiverPartnerName |Opcional, cadena.  Indica el nombre de asociado del destinatario del mensaje de X12 |
| senderQualifier |Obligatorio, cadena.  Indica el calificador del asociado de envío |
| senderIdentifier |Obligatorio, cadena.  Indica el identificador del asociado de envío |
| receiverQualifier |Obligatorio, cadena.  Indica el identificador del asociado de recepción |
| receiverIdentifier |Obligatorio, cadena.  Indica el identificador del asociado de recepción |
| agreementName |Opcional, cadena.  Nombre del contrato X12 en el que se resuelven los mensajes |
| dirección |Obligatorio, enum.  Indica la dirección del flujo de mensajes.  Los valores permitidos son recepción o envío |
| interchangeControlNumber |Opcional, cadena.  Indica el número de control de intercambio |
| isaSegment |Opcional, cadena.  Indica el segmento ISA del mensaje |
| isTechnicalAcknowledgmentExpected |Obligatorio, booleano.  Indica si la confirmación técnica se ha configurado, o no, en el acuerdo de X12 |
| isMessageFailed |Obligatorio, booleano.  Indica si el mensaje de X12 se ha enviado correctamente o se ha producido un error |
| isa09 |Opcional, cadena.  Indica la fecha de intercambio del documento de X12 |
| isa10 |Opcional, cadena. Indica la hora de intercambio del documento de X12 |
| isa11 |Opcional, cadena. Indica el identificador de los estándares de control del intercambio de X12 |
| isa12 |Opcional, cadena.  Indica el número de versión de control del intercambio de X12 |
| isa14 |Opcional, cadena.  Indica que se solicita la confirmación de X12 |
| isa15 |Opcional, cadena.  Muestra el indicador de prueba o de producción |
| isa16 |Opcional, cadena. Indica el separador de elementos |

## <a name="x12-interchange-acknowledgement-tracking-schema"></a>Esquema de seguimiento de confirmación de intercambio de X12
````java
    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "isaSegment": "",
                "respondingInterchangeControlNumber": "",
                "isMessageFailed": "",
                "statusCode": "",
                "processingStatus": "",
                "ta102": "",
                "ta103": "",
                "ta105": ""
            }
    }
````

| Propiedad | Descripción |
| --- | --- |
| senderPartnerName |Opcional, cadena.  Indica el nombre de asociado del remitente del mensaje de X12 |
| receiverPartnerName |Opcional, cadena.  Indica el nombre de asociado del destinatario del mensaje de X12 |
| senderQualifier |Obligatorio, cadena.  Indica el calificador del asociado de envío |
| senderIdentifier |Obligatorio, cadena.  Indica el identificador del asociado de envío |
| receiverQualifier |Obligatorio, cadena.  Indica el identificador del asociado de recepción |
| receiverIdentifier |Obligatorio, cadena.  Indica el identificador del asociado de recepción |
| agreementName |Opcional, cadena.  Nombre del contrato X12 en el que se resuelven los mensajes |
| dirección |Obligatorio, enum.  Indica la dirección del flujo de mensajes.  Los valores permitidos son recepción o envío |
| interchangeControlNumber |Opcional, cadena.  Indica el número de control de intercambio de la confirmación técnica. La confirmación técnica recibida de los asociados tiene este valor |
| isaSegment |Opcional, cadena.  Indica el segmento ISA de la confirmación técnica. La confirmación técnica recibida de los asociados tiene este valor |
| respondingInterchangeControlNumber |Opcional, cadena.  Indica el número de control de intercambio de la confirmación técnica. La confirmación técnica recibida de los asociados tiene este valor |
| isMessageFailed |Obligatorio, booleano.  Indica si el mensaje de X12 se ha enviado correctamente o se ha producido un error |
| statusCode |Obligatorio, enum.  Indica el código de estado de la confirmación del intercambio.  Se permiten los siguientes valores: Accepted/Rejected/AccpetedWithErrros |
| processingStatus |Obligatorio, enum.  Indica el estado de la confirmación.  Se permiten los siguientes valores: Received/Generated/Sent |
| ta102 |Opcional, cadena. Indica la fecha de intercambio |
| ta103 |Opcional, cadena. Indica la hora de intercambio |
| ta105 |Opcional, cadena. Indica el código de nota del intercambio |

## <a name="x12-functional-group-tracking-schema"></a>Esquema de seguimiento de grupos funcionales de X12
````java

    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "functionalGroupControlNumber": "",
                "gsSegment": "",
                "isTechnicalAcknowledgmentExpected": "",
                "isFunctionalAcknowledgmentExpected": "",
                "isMessageFailed": "",
                "gs01": "",
                "gs02": "",
                "gs03": "",
                "gs04": "",
                "gs05": "",
                "gs07": "",
                "gs08": ""
            }
    }
````

| Propiedad | Descripción |
| --- | --- |
| senderPartnerName |Opcional, cadena.  Indica el nombre de asociado del remitente del mensaje de X12 |
| receiverPartnerName |Opcional, cadena.  Indica el nombre de asociado del destinatario del mensaje de X12 |
| senderQualifier |Obligatorio, cadena.  Indica el calificador del asociado de envío |
| senderIdentifier |Obligatorio, cadena.  Indica el identificador del asociado de envío |
| receiverQualifier |Obligatorio, cadena.  Indica el identificador del asociado de recepción |
| receiverIdentifier |Obligatorio, cadena.  Indica el identificador del asociado de recepción |
| agreementName |Opcional, cadena.  Nombre del contrato X12 en el que se resuelven los mensajes |
| dirección |Obligatorio, enum.  Indica la dirección del flujo de mensajes.  Los valores permitidos son recepción o envío |
| interchangeControlNumber |Opcional, cadena. Indica el número de control de intercambio |
| functionalGroupControlNumber |Opcional - número de control funcional - tipo cadena |
| gsSegment |Opcional, cadena.  Indica el segmento GS del mensaje |
| isTechnicalAcknowledgmentExpected |Obligatorio, booleano.  Indica si la confirmación técnica se ha configurado, o no, en el acuerdo de X12 |
| isFunctionalAcknowledgmentExpected |Obligatorio, booleano.  Indica si la confirmación funcional se ha configurado, o no, en el acuerdo de X12 |
| isMessageFailed |Obligatorio, booleano.  Indica si el mensaje de X12 se ha enviado correctamente o se ha producido un error |
| gs01 |Opcional, cadena. Indica el código del identificador funcional |
| gs02 |Opcional, cadena. Indica el código del remitente de la aplicación |
| gs03 |Opcional, cadena. Indica el código del destinatario de la aplicación |
| gs04 |Opcional, cadena. Indica la fecha del grupo funcional |
| gs05 |Opcional, cadena. Indica la hora del grupo funcional |
| gs07 |Opcional, cadena. Indica el código de la agencia responsable |
| gs08 |Opcional, cadena. Indica la versión/código identificador industrial - tipo cadena |

## <a name="x12-functional-group-acknowledgement-tracking-schema"></a>Esquema de seguimiento de confirmación de grupos funcionales de X12
````java
    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "functionalGroupControlNumber": "",
                "isaSegment": "",
                "gsSegment": "",
                "respondingfunctionalGroupControlNumber": "",
                "respondingFunctionalGroupId": "",
                "isMessageFailed": "",
                "statusCode": "",
                "processingStatus": "",
                "ak903": "",
                "ak904": "",
                "ak9Segment": ""
            }
    }
````

| Propiedad | Descripción |
| --- | --- |
| senderPartnerName |Opcional, cadena.  Indica el nombre de asociado del remitente del mensaje de X12 |
| receiverPartnerName |Opcional, cadena.  Indica el nombre de asociado del destinatario del mensaje de X12 |
| senderQualifier |Obligatorio, cadena.  Indica el calificador del asociado de envío |
| senderIdentifier |Obligatorio, cadena.  Indica el identificador del asociado de envío |
| receiverQualifier |Obligatorio, cadena.  Indica el identificador del asociado de recepción |
| receiverIdentifier |Obligatorio, cadena.  Indica el identificador del asociado de recepción |
| agreementName |Opcional, cadena.  Nombre del contrato X12 en el que se resuelven los mensajes |
| dirección |Obligatorio, enum.  Indica la dirección del flujo de mensajes.  Los valores permitidos son recepción o envío |
| interchangeControlNumber |Opcional, cadena. Indica el número de control de intercambio. El valor se rellena en el lado del envío cuando se recibe una confirmación técnica de los asociados |
| functionalGroupControlNumber |Opcional, cadena. Indica el número de control del grupo funcional de la confirmación funcional. El valor se rellena en el lado del envío cuando se recibe una confirmación técnica de los asociados |
| isaSegment |Opcional: igual que en el caso anterior, el número de control de intercambio se rellena solo en casos concretos. - tipo cadena |
| gsSegment |Opcional: igual que en el caso anterior, el número de control del grupo funcional se rellena solo en casos concretos. - tipo cadena |
| respondingfunctionalGroupControlNumber |Opcional: número de control del grupo funcional original - tipo cadena |
| respondingFunctionalGroupId |Opcional: se asigna a AK101 en la confirmación - identificador de grupo funcional - tipo cadena |
| isMessageFailed |Obligatorio, booleano.  Indica si el mensaje de X12 se ha enviado correctamente o se ha producido un error |
| statusCode |Obligatorio, enum.  Indica el código de estado de la confirmación. Se permiten los siguientes valores: Accepted/Rejected/AccpetedWithErrros |
| processingStatus |Obligatorio, enum.  Indica el estado de procesamiento de la confirmación. Se permiten los siguientes valores: Received/Generated/Sent |
| ak903 |Opcional, cadena. Indica el número de conjuntos de transacciones recibidos |
| ak904 |Opcional, cadena. Indica el número de conjuntos de transacciones aceptados en el grupo funcional identificado |
| ak9Segment |Opcional, cadena.  El segmento Ak9 indica si el grupo funcional identificado en el segmento AK1 se acepta o se rechaza, y el motivo de la aceptación o rechazo |

## <a name="next-steps"></a>Pasos siguientes
[Más información acerca de la supervisión de mensajes B2B](app-service-logic-monitor-b2b-message.md "Learn more about tracking B2B messages")   
[Tracking B2B messages in OMS portal](app-service-logic-track-b2b-messages-omsportal.md "Tracking B2B messages")  (Seguimiento de mensajes B2B en el Portal de OMS)  
[Más información acerca de los esquemas de seguimiento personalizado](app-service-logic-track-integration-account-custom-tracking-shema.md "Learn about Custom Schema")   
[Más información acerca de los esquemas de seguimiento de AS2](app-service-logic-track-integration-account-as2-tracking-shemas.md "Learn about AS2 Schema")   
[Más información acerca de Enterprise Integration Pack](app-service-logic-enterprise-integration-overview.md "Learn about Enterprise Integration Pack")  




<!--HONumber=Nov16_HO3-->


