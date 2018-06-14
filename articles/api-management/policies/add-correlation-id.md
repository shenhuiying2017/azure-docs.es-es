---
title: 'Ejemplo de directiva de Azure API Management: adición de un encabezado que contiene un identificador de correlación | Microsoft Docs'
description: 'Ejemplo de directiva de Azure API Management: muestra cómo agregar un encabezado con un identificador de correlación para la solicitud de entrada.'
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
ms.openlocfilehash: 7d339ba08720d482f622aa9b708d3a6e057eaaa8
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
ms.locfileid: "33935121"
---
# <a name="add-a-header-containing-a-correlation-id"></a>Incorporación de un encabezado con un identificador de correlación

Este artículo muestra un ejemplo de directiva de Azure API Management que demuestra cómo agregar un encabezado con un identificador de correlación para la solicitud de entrada. Para establecer o modificar un código de la directiva, siga los pasos descritos en [Establecimiento o modificación de directivas](../set-edit-policies.md). Para ver otros ejemplos, consulte los [ejemplos de directiva](../policy-samples.md).

## <a name="policy"></a>Directiva

Pegue el código en el bloque de **entrada**.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Add correlation id to inbound request.policy.xml)]

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre las directivas APIM:

+ [Directivas de transformación](../api-management-transformation-policies.md)
+ [Ejemplos de directivas](../policy-samples.md)

