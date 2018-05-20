---
title: 'Ejemplo de directiva de Azure API Management: adición de funcionalidades a un servicio de back-end | Microsoft Docs'
description: 'Ejemplo de directiva de Azure API Management: demuestra cómo agregar funcionalidades a un servicio de back-end. Por ejemplo, aceptar el nombre de un lugar en vez de su latitud y longitud en una API de pronóstico meteorológico.'
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
ms.openlocfilehash: 3ae168473b4fa603afaf3dcf42516687b4dde33e
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
---
# <a name="add-capabilities-to-a-backend-service"></a>Adición de funcionalidades a un servicio de back-end

Este artículo incluye un ejemplo de directiva de Azure API Management que muestra cómo agregar funcionalidades a un servicio back-end. Por ejemplo, aceptar el nombre de un lugar en vez de su latitud y longitud en una API de pronóstico meteorológico. Para establecer o modificar un código de la directiva, siga los pasos descritos en [Establecimiento o modificación de directivas](../set-edit-policies.md). Para ver otros ejemplos, consulte los [ejemplos de directiva](../policy-samples.md).

## <a name="policy"></a>Directiva

Pegue el código en el bloque de **entrada**.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Call out to an HTTP endpoint and cache the response.policy.xml)]

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre las directivas APIM:

+ [Directivas de transformación](../api-management-transformation-policies.md)
+ [Ejemplos de directivas](../policy-samples.md)

