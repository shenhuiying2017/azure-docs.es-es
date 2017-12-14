---
title: "Ejemplo de directiva de Azure API Management: use OAuth2 para la autorización entre la puerta de enlace y un back-end | Microsoft Docs"
description: "Ejemplo de directiva de Azure API Management: se explica cómo usar OAuth2 para la autorización entre la puerta de enlace y un back-end. Muestra cómo obtener un token de acceso en AAD y reenviarlo al back-end."
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: e0aeec66f23033f916c782c8a895e725b0735b62
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/04/2017
---
# <a name="use-oauth2-for-authorization-between-the-gateway-and-a-backend"></a>Use OAuth2 para la autorización entre la puerta de enlace y un back-end

En este artículo se explica un ejemplo de directiva de Azure API Management que demuestra cómo usar OAuth2 para la autorización entre la puerta de enlace y un back-end. Muestra cómo obtener un token de acceso en AAD y reenviarlo al back-end. Para establecer o modificar un código de la directiva, siga los pasos descritos en [Establecimiento o modificación de directivas](../set-edit-policies.md). Para ver otros ejemplos, consulte los [ejemplos de directiva](../policy-samples.md).

## <a name="policy"></a>Directiva

Pegue el código en el bloque de **entrada**.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Get OAuth2 access token from AAD and forward it to the backend.xml)]

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre las directivas APIM:

+ [Directivas de transformación](../api-management-transformation-policies.md)
+ [Ejemplos de directivas](../policy-samples.md)

