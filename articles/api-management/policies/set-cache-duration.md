---
title: 'Ejemplo de directiva de Azure API Management: establecimiento de la duración en caché de las respuestas | Microsoft Docs'
description: 'Ejemplo de directiva de Azure API Management: muestra cómo establecer la duración en caché de las respuestas con el valor maxAge en el encabezado de control de caché que envía el back-end.'
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
ms.openlocfilehash: 8640668ae51c113cc467501b44dbd03b257325c3
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
ms.locfileid: "33937290"
---
# <a name="set-response-cache-duration"></a>Establecimiento de la duración en caché de las respuestas

Este artículo incluye un ejemplo de directiva de Azure API Management que muestra cómo establecer la duración en caché de las respuestas con el valor maxAge en el encabezado de control de caché que envía el back-end. Para establecer o modificar un código de la directiva, siga los pasos descritos en [Establecimiento o modificación de directivas](../set-edit-policies.md). Para ver otros ejemplos, consulte los [ejemplos de directiva](../policy-samples.md).

## <a name="policy"></a>Directiva

Pegue el código en el bloque de **entrada**.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Set cache duration using response cache control header.policy.xml)]

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre las directivas APIM:

+ [Directivas de transformación](../api-management-transformation-policies.md)
+ [Ejemplos de directivas](../policy-samples.md)

