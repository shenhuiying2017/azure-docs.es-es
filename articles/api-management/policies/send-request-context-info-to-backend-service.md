---
title: 'Ejemplo de directiva de Azure API Management: envío de información contextual de la solicitud al servicio de back-end | Microsoft Docs'
description: 'Ejemplo de directiva de Azure API Management: demuestra cómo enviar información contextual de la solicitud al servicio back-end.'
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: 932555a20ae89581b557d9d9f0a73992205d1e18
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
ms.locfileid: "33933091"
---
# <a name="send-request-context-information-to-the-backend-service"></a>Envío de información contextual de la solicitud al servicio de back-end

Este artículo incluye un ejemplo de directiva de Azure API Management que muestra cómo enviar información contextual de la solicitud al servicio back-end. Para establecer o modificar un código de la directiva, siga los pasos descritos en [Establecimiento o modificación de directivas](../set-edit-policies.md). Para ver otros ejemplos, consulte los [ejemplos de directiva](../policy-samples.md).

## <a name="policy"></a>Directiva

Pegue el código en el bloque de **entrada**.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Send request context information to the backend service.policy.xml)]

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre las directivas APIM:

+ [Directivas de transformación](../api-management-transformation-policies.md)
+ [Ejemplos de directivas](../policy-samples.md)

