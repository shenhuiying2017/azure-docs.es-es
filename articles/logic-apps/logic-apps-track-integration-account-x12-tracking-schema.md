---
title: Esquemas de seguimiento de X12 para la supervisión de B2B - Azure Logic Apps | Microsoft Docs
description: Utilice esquemas de seguimiento de X12 para supervisar mensajes B2B de transacciones en la cuenta de la integración de Azure.
author: padmavc
manager: anneta
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: a5413f80-eaad-4bcf-b371-2ad0ef629c3d
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: LADocs; padmavc
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e5a43b9bdf522b6b26f27c082f5cb623f7a76a8b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2018
---
# <a name="start-or-enable-tracking-of-x12-messages-to-monitor-success-errors-and-message-properties"></a>Inicio o habilitación del seguimiento de mensajes X12 para supervisar propiedades de mensaje, errores y éxitos
Puede usar estos esquemas de seguimiento de X12 en su cuenta de integración de Azure para ayudarle a supervisar las transacciones de negocio a negocio (B2B):

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

| Propiedad | Escriba | DESCRIPCIÓN |
| --- | --- | --- |
| senderPartnerName | string | Nombre de asociado del remitente del mensaje X12. (Opcional) |
| receiverPartnerName | string | Nombre de asociado del destinatario del mensaje X12. (Opcional) |
| senderQualifier | string | Calificador del asociado remitente. (Obligatorio) |
| senderIdentifier | string | Identificador del asociado remitente. (Obligatorio) |
| receiverQualifier | string | Calificador del asociado destinatario. (Obligatorio) |
| receiverIdentifier | string | Identificador del asociado destinatario. (Obligatorio) |
| agreementName | string | Nombre del contrato X12 en el que se resuelven los mensajes. (Opcional) |
| dirección | Enum | Dirección del flujo de mensajes, recibidos o enviados. (Obligatorio) |
| interchangeControlNumber | string | Número de control de intercambio. (Opcional) |
| functionalGroupControlNumber | string | Número de control funcional. (Opcional) |
| transactionSetControlNumber | string | Número de control de conjuntos de transacciones. (Opcional) |
| CorrelationMessageId | string | Identificador del mensaje de correlación. Una combinación de {AgreementName}{*GroupControlNumber*}{TransactionSetControlNumber}. (Opcional) |
| messageType | string | Conjunto de transacciones o tipo de documento. (Opcional) |
| isMessageFailed | boolean | Si el mensaje X12 genera error. (Obligatorio) |
| isTechnicalAcknowledgmentExpected | boolean | Si la confirmación técnica se ha configurado en el contrato X12. (Obligatorio) |
| isFunctionalAcknowledgmentExpected | boolean | Si la confirmación funcional se ha configurado en el contrato X12. (Obligatorio) |
| needAk2LoopForValidMessages | boolean | Si el bucle AK2 se requiere para un mensaje válido. (Obligatorio) |
| segmentsCount | Entero | Número de segmentos del conjunto de transacciones de X12. (Opcional) |

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

| Propiedad | Escriba | DESCRIPCIÓN |
| --- | --- | --- |
| senderPartnerName | string | Nombre de asociado del remitente del mensaje X12. (Opcional) |
| receiverPartnerName | string | Nombre de asociado del destinatario del mensaje X12. (Opcional) |
| senderQualifier | string | Calificador del asociado remitente. (Obligatorio) |
| senderIdentifier | string | Identificador del asociado remitente. (Obligatorio) |
| receiverQualifier | string | Calificador del asociado destinatario. (Obligatorio) |
| receiverIdentifier | string | Identificador del asociado destinatario. (Obligatorio) |
| agreementName | string | Nombre del contrato X12 en el que se resuelven los mensajes. (Opcional) |
| dirección | Enum | Dirección del flujo de mensajes, recibidos o enviados. (Obligatorio) |
| interchangeControlNumber | string | Número de control de intercambio de la confirmación funcional. El valor se rellena solo en el lado del envío en el que se ha recibido confirmación funcional de los mensajes enviados al asociado. (Opcional) |
| functionalGroupControlNumber | string | Número de control del grupo funcional de la confirmación funcional. El valor se rellena solo en el lado del envío en el que se ha recibido confirmación funcional de los mensajes enviados al asociado. (Opcional) |
| isaSegment | string | Segmento ISA del mensaje. El valor se rellena solo en el lado del envío en el que se ha recibido confirmación funcional de los mensajes enviados al asociado. (Opcional) |
| gsSegment | string | Segmento GS del mensaje. El valor se rellena solo en el lado del envío en el que se ha recibido confirmación funcional de los mensajes enviados al asociado. (Opcional) |
| respondingfunctionalGroupControlNumber | string | Número de control de intercambio de respuesta. (Opcional) |
| respondingFunctionalGroupId | string | Identificador del grupo funcional de respuesta, que se asigna a AK101 en la confirmación. (Opcional) |
| respondingtransactionSetControlNumber | string | Número de control del conjunto de transacciones de respuesta. (Opcional) |
| respondingTransactionSetId | string | Identificador del conjunto de transacciones de respuesta, que se asigna a AK201 en la confirmación. (Opcional) |
| statusCode | boolean | Código de estado de la confirmación del conjunto de transacciones. (Obligatorio) |
| segmentsCount | Enum | Código de estado de la confirmación. Los valores permitidos son **Accepted**, **Rejected**, y **AcceptedWithErrors**. (Obligatorio) |
| processingStatus | Enum | Estado de procesamiento de la confirmación. Los valores permitidos son **Received**, **Generated** y **Sent**. (Obligatorio) |
| CorrelationMessageId | string | Identificador del mensaje de correlación. Una combinación de {AgreementName}{*GroupControlNumber*}{TransactionSetControlNumber}. (Opcional) |
| isMessageFailed | boolean | Si el mensaje X12 genera error. (Obligatorio) |
| ak2Segment | string | Confirmación para un conjunto de transacciones del grupo funcional recibido. (Opcional) |
| ak3Segment | string | Indica que hay errores en un segmento de datos. (Opcional) |
| ak5Segment | string | Indica si el conjunto de transacciones identificado en el segmento de AK2 se acepta o se rechaza, y el motivo de la aceptación o rechazo. (Opcional) |

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

| Propiedad | Escriba | DESCRIPCIÓN |
| --- | --- | --- |
| senderPartnerName | string | Nombre de asociado del remitente del mensaje X12. (Opcional) |
| receiverPartnerName | string | Nombre de asociado del destinatario del mensaje X12. (Opcional) |
| senderQualifier | string | Calificador del asociado remitente. (Obligatorio) |
| senderIdentifier | string | Identificador del asociado remitente. (Obligatorio) |
| receiverQualifier | string | Calificador del asociado destinatario. (Obligatorio) |
| receiverIdentifier | string | Identificador del asociado destinatario. (Obligatorio) |
| agreementName | string | Nombre del contrato X12 en el que se resuelven los mensajes. (Opcional) |
| dirección | Enum | Dirección del flujo de mensajes, recibidos o enviados. (Obligatorio) |
| interchangeControlNumber | string | Número de control de intercambio. (Opcional) |
| isaSegment | string | Segmento ISA del mensaje. (Opcional) |
| isTechnicalAcknowledgmentExpected | boolean | Si la confirmación técnica se ha configurado en el contrato X12. (Obligatorio) |
| isMessageFailed | boolean | Si el mensaje X12 genera error. (Obligatorio) |
| isa09 | string | Fecha de intercambio del documento X12. (Opcional) |
| isa10 | string | Hora de intercambio del documento X12. (Opcional) |
| isa11 | string | Identificador de los estándares de control del intercambio de X12. (Opcional) |
| isa12 | string | Número de versión de control del intercambio de X12. (Opcional) |
| isa14 | string | Se solicita la confirmación de X12. (Opcional) |
| isa15 | string | Indicador de prueba o de producción. (Opcional) |
| isa16 | string | Separador de elementos. (Opcional) |

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

| Propiedad | Escriba | DESCRIPCIÓN |
| --- | --- | --- |
| senderPartnerName | string | Nombre de asociado del remitente del mensaje X12. (Opcional) |
| receiverPartnerName | string | Nombre de asociado del destinatario del mensaje X12. (Opcional) |
| senderQualifier | string | Calificador del asociado remitente. (Obligatorio) |
| senderIdentifier | string | Identificador del asociado remitente. (Obligatorio) |
| receiverQualifier | string | Calificador del asociado destinatario. (Obligatorio) |
| receiverIdentifier | string | Identificador del asociado destinatario. (Obligatorio) |
| agreementName | string | Nombre del contrato X12 en el que se resuelven los mensajes. (Opcional) |
| dirección | Enum | Dirección del flujo de mensajes, recibidos o enviados. (Obligatorio) |
| interchangeControlNumber | string | Número de control de intercambio de la confirmación técnica que los asociados reciben. (Opcional) |
| isaSegment | string | Segmento ISA de la confirmación técnica que los asociados reciben. (Opcional) |
| respondingInterchangeControlNumber |string | Número de control de intercambio de la confirmación técnica que los asociados reciben. (Opcional) |
| isMessageFailed | boolean | Si el mensaje X12 genera error. (Obligatorio) |
| statusCode | Enum | Código de estado de la confirmación del intercambio. Los valores permitidos son **Accepted**, **Rejected**, y **AcceptedWithErrors**. (Obligatorio) |
| processingStatus | Enum | Estado de la confirmación. Los valores permitidos son **Received**, **Generated** y **Sent**. (Obligatorio) |
| ta102 | string | Fecha de intercambio. (Opcional) |
| ta103 | string | Hora de intercambio. (Opcional) |
| ta105 | string | Código de nota de intercambio. (Opcional) |

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

| Propiedad | Escriba | DESCRIPCIÓN |
| --- | --- | --- |
| senderPartnerName | string | Nombre de asociado del remitente del mensaje X12. (Opcional) |
| receiverPartnerName | string | Nombre de asociado del destinatario del mensaje X12. (Opcional) |
| senderQualifier | string | Calificador del asociado remitente. (Obligatorio) |
| senderIdentifier | string | Identificador del asociado remitente. (Obligatorio) |
| receiverQualifier | string | Calificador del asociado destinatario. (Obligatorio) |
| receiverIdentifier | string | Identificador del asociado destinatario. (Obligatorio) |
| agreementName | string | Nombre del contrato X12 en el que se resuelven los mensajes. (Opcional) |
| dirección | Enum | Dirección del flujo de mensajes, recibidos o enviados. (Obligatorio) |
| interchangeControlNumber | string | Número de control de intercambio. (Opcional) |
| functionalGroupControlNumber | string | Número de control funcional. (Opcional) |
| gsSegment | string | Segmento GS del mensaje. (Opcional) |
| isTechnicalAcknowledgmentExpected | boolean | Si la confirmación técnica se ha configurado en el contrato X12. (Obligatorio) |
| isFunctionalAcknowledgmentExpected | boolean | Si la confirmación funcional se ha configurado en el contrato X12. (Obligatorio) |
| isMessageFailed | boolean | Si el mensaje X12 genera error. (Obligatorio)|
| gs01 | string | Código del identificador funcional. (Opcional) |
| gs02 | string | Código del remitente de la aplicación. (Opcional) |
| gs03 | string | Código del destinatario de la aplicación. (Opcional) |
| gs04 | string | Fecha del grupo funcional. (Opcional) |
| gs05 | string | Hora del grupo funcional. (Opcional) |
| gs07 | string | Código de agencia responsable. (Opcional) |
| gs08 | string | Código de identificador de versión/lanzamiento/industria. (Opcional) |

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

| Propiedad | Escriba | DESCRIPCIÓN |
| --- | --- | --- |
| senderPartnerName | string | Nombre de asociado del remitente del mensaje X12. (Opcional) |
| receiverPartnerName | string | Nombre de asociado del destinatario del mensaje X12. (Opcional) |
| senderQualifier | string | Calificador del asociado remitente. (Obligatorio) |
| senderIdentifier | string | Identificador del asociado remitente. (Obligatorio) |
| receiverQualifier | string | Calificador del asociado destinatario. (Obligatorio) |
| receiverIdentifier | string | Identificador del asociado destinatario. (Obligatorio) |
| agreementName | string | Nombre del contrato X12 en el que se resuelven los mensajes. (Opcional) |
| dirección | Enum | Dirección del flujo de mensajes, recibidos o enviados. (Obligatorio) |
| interchangeControlNumber | string | Número de control de intercambio, que se rellena para el lado del envío cuando los asociados reciben la confirmación técnica. (Opcional) |
| functionalGroupControlNumber | string | Número de control de grupo funcional de la confirmación técnica, que se rellena para el lado del envío cuando los asociados reciben la confirmación técnica. (Opcional) |
| isaSegment | string | Igual que el número de control de intercambio, pero se rellena solo en casos específicos. (Opcional) |
| gsSegment | string | Igual que el número de control de grupo funcional, pero se rellena solo en casos específicos. (Opcional) |
| respondingfunctionalGroupControlNumber | string | Número de control del grupo funcional original. (Opcional) |
| respondingFunctionalGroupId | string | Se asocia con AK101 en el identificador del grupo funcional de confirmación. (Opcional) |
| isMessageFailed | boolean | Si el mensaje X12 genera error. (Obligatorio) |
| statusCode | Enum | Código de estado de la confirmación. Los valores permitidos son **Accepted**, **Rejected**, y **AcceptedWithErrors**. (Obligatorio) |
| processingStatus | Enum | Estado de procesamiento de la confirmación. Los valores permitidos son **Received**, **Generated** y **Sent**. (Obligatorio) |
| ak903 | string | Número de conjuntos de transacciones recibidos. (Opcional) |
| ak904 | string | Número de conjuntos de transacciones aceptados en el grupo funcional identificado. (Opcional) |
| ak9Segment | string | Si el grupo funcional identificado en el segmento AK1 se acepta o se rechaza, y el motivo de la aceptación o el rechazo. (Opcional) |

## <a name="next-steps"></a>Pasos siguientes
* Más información sobre la [supervisión de mensajes B2B](logic-apps-monitor-b2b-message.md).
* Más información sobre los [esquemas de seguimiento de AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md).
* Más información sobre los [esquemas de seguimiento personalizado de B2B](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md).
* Obtenga información acerca del [seguimiento de mensajes B2B en Log Analytics](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).
* Más información sobre el [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)  
