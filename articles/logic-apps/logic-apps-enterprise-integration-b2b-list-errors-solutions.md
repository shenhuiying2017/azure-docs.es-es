---
title: 'Lista de errores y soluciones para Logic Apps B2B: Azure App Service | Microsoft Docs'
description: Lista de errores y soluciones para Logic Apps B2B
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
ms.date: 06/02/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: 1865d75f1b4c2aa18d5a3130f639572d19563b3e
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="logic-apps-b2b-list-of-errors-and-solutions"></a>Lista de errores y soluciones para Logic Apps B2B  
Este artículo le ayuda a solucionar los errores que puedan ocurrir en escenarios de Logic Apps B2B y le sugiere las acciones adecuadas para corregirlos.


## <a name="agreement-resolution"></a>Resolución de contrato

### <a name="no-agreement-found"></a>*No agreement found (No se encontró ningún contrato) 

|   |   |  
|---|---|
| Descripción del error | No se encontró ningún contrato con parámetros de resolución de contrato|    
| Acción del usuario | Se debe agregar el contrato a la cuenta de integración con identidades de negocio acordadas.</br> Las identidades de negocio deben coincidir con los identificadores de mensaje de entrada|  
|   |   |

### <a name="-no-agreement-found-with-identities"></a>* No se encontró ningún contrato con las identidades

|   |   | 
|---|---|
| Descripción del error | No se encontró ningún contrato con las identidades: 'AS2Identity'::'Partner1' y 'AS2Identity'::'Partner3'| 
| Acción del usuario | AS2-From o AS2-To configurados para el contrato no válidos. </br> Corrija los encabezados AS2-From o AS2-To del mensaje AS2 o el contrato para que coincidan con los identificadores AS2 en los encabezados del mensaje AS2 con configuraciones de contrato |
|   |   |     

## <a name="as2"></a>AS2

### <a name="-missing-as2-message-headers"></a>* Missing AS2 message headers (Faltan encabezados del mensaje AS2)  

|   |   |  
|---|---|
| Descripción del error| Encabezados AS2 no válidos. Uno de los encabezados "AS2-To" o "AS2-From" está vacío| 
| Acción del usuario | Se recibió un mensaje AS2 que no contenía el encabezado AS2-From, el AS2-To o ambos. </br> Compruebe los encabezados AS2-From y AS2-To del mensaje AS2 y corríjalos basándose en la configuración del contrato |
|  |  | 


### <a name="-missing-as2-message-body-and-headers"></a>* Missing AS2 message body and headers (Faltan el cuerpo y los encabezados del mensaje AS2)    

|   |   |  
|---|---|
| Descripción del error| El contenido de la solicitud es nulo o está vacío | 
| Acción del usuario | Se recibió un mensaje AS2 que no contenía el cuerpo del mensaje |
|  |  | 

### <a name="-as2-message-decryption-failure"></a>* AS2 message decryption failure (Error de descifrado del mensaje AS2)

|   |   | 
|---|---|
| Descripción del error |  [processed/Error: decryption-failed] | 
| Acción del usuario | Agregue @base64ToBinary a AS2Message antes de enviarlo al asociado 
```java
            "HTTP": {
                "inputs": {
                    "body": "@base64ToBinary(body('Encode_to_AS2_message')?['AS2Message']?['Content'])",
                    "headers": "@body('Encode_to_AS2_message')?['AS2Message']?['OutboundHeaders']",
                    "method": "POST",
                    "uri": "xxxxx.xxx"
                },
                
``` 

### <a name="-mdn-decryption-failure"></a>* MDN decryption failure﻿ (Error de descifrado de MDN)

|   |   | 
|---|---|
| Descripción del error |  [processed/Error: decryption-failed] | 
| Acción del usuario | Agregue @base64ToBinary a la MDN antes de enviarlo al asociado 
```java
            "Response": {
                "inputs": {
                    "body": "@base64ToBinary(body('Decode_AS2_message')?['OutgoingMDN']?['Content'])",
                    "headers": "@body('Decode_AS2_message')?['OutgoingMDN']?['OutboundHeaders']",
                    "statusCode": 200
                },
                
``` 

### <a name="-missing-signing-certificate"></a>* Missing signing certificate (Falta el certificado de firma)

|   |   |  
|---|---|
| Descripción del error| El certificado de firma no se ha configurado para la entidad AS2. </br> AS2-From: partner1 AS2-To: partner2 | 
| Acción del usuario | Configure el contrato AS2 con el certificado correcto para la firma |
|  |  | 

## <a name="x12-and-edifact"></a>X12 y EDIFACT

### <a name="-leading-or-trailing-space-found"></a>* Espacio inicial o final detectado    
    
|   |   | 
|---|---|
| Descripción del error | Error al analizar. El conjunto de transacciones Edifact con Id. '123456' contenido en el intercambio (sin grupo) con Id. '987654', Id. de remitente 'Partner1' e Id. de destinatario 'Partner2' se está suspendiendo por los errores siguientes: Separador final encontrado |
| Acción del usuario | Se debe definir la configuración de contrato para que permita espacios iniciales y finales. </br> Edite la configuración de contrato para permitir un espacio inicial y final |
|   |   |

![permitir espacio](./media/logic-apps-enterprise-integration-b2b-list-errors-solutions/leadingandtrailing.png)

### <a name="-duplicate-check-has-enabled-in-the-agreement"></a>* Duplicate check has enabled in the agreement (Comprobación de duplicados habilitada en el contrato)

|   |   | 
|---|---| 
| Descripción del error | Número de control duplicado |
| Acción del usuario | Este error indica que el mensaje recibido tiene números de control duplicados. </br> Corrija el número de control y vuelva a enviar el mensaje |
|   |   |

### <a name="-missing-schema-in-the-agreement"></a>* Missing schema in the agreement (Falta un esquema en el contrato)

|   |   | 
|---|---| 
| Descripción del error | Error al analizar. El conjunto de transacciones X12 con Id. '564220001', contenido en el grupo funcional con Id. '56422', en el intercambio con Id. '000056422', con Id. de remitente '12345678' e Id. de destinatario '87654321' se suspende por los siguientes errores. "El mensaje tiene un tipo de documento desconocido y no se resolvió en ninguno de los esquemas existentes configurados en el contrato" |
| Acción del usuario | Configure el esquema en la configuración del contrato  |
|   |   |

### <a name="-incorrect-schema-in-the-agreement"></a>* Incorrect schema in the agreement (Esquema incorrecto en el contrato)

|   |   | 
|---|---| 
| Descripción del error | El mensaje tiene un tipo de documento desconocido y no se resolvió en ninguno de los esquemas existentes configurados en el contrato. |
| Acción del usuario | Configure el esquema correcto en la configuración del contrato  |
|   |   |

## <a name="flat-file"></a>Archivo plano

### <a name="-input-message-with-no-body"></a>* Input message with no body (Mensaje de entrada sin cuerpo)

|   |   | 
|---|---|
| Descripción del error | InvalidTemplate. No se pueden procesar las expresiones de lenguaje de plantilla en las entradas de la acción 'Flat_File_Decoding' en la línea '1' y la columna '1902': 'La propiedad obligatoria 'content' espera un valor pero obtuvo nulo. Ruta de acceso ''.'. |
| Acción del usuario | Este error indica que el mensaje de entrada no contiene un cuerpo |
|   |   | 

## <a name="learn-more"></a>Más información
[Más información acerca de Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md)