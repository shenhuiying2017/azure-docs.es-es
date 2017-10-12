---
title: Expresiones condicionales del motor de reglas de la red CDN de Azure | Microsoft Docs
description: "Documentación de referencia sobre las condiciones y características de coincidencia del motor de reglas de la red CDN de Azure."
services: cdn
documentationcenter: 
author: Lichard
manager: akucer
editor: 
ms.assetid: 669ef140-a6dd-4b62-9b9d-3f375a14215e
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: rli
ms.openlocfilehash: 57e56c38e003cb83dcf44f455c4451d159db8a59
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cdn-rules-engine-conditional-expressions"></a>Expresiones condicionales del motor de reglas de la red CDN de Azure
En este tema se muestran descripciones detalladas de las expresiones condicionales para el [motor de reglas](cdn-rules-engine.md)de Azure Content Delivery Network (CDN).

La primera parte de una regla es la expresión condicional.

Expresión condicional | Descripción
-----------------------|-------------
IF | Una expresión IF siempre es una parte de la primera instrucción en una regla. Al igual que las demás expresiones condicionales, esta instrucción IF debe asociarse a una coincidencia. Si no se ha definido ninguna expresión condicional adicional, esta coincidencia determina los criterios que deben cumplirse antes de poder aplicar un conjunto de funciones a una solicitud.
AND IF | Una expresión AND IF solo puede agregarse después de los siguientes tipos de expresiones condicionales: IF, AND IF. Indica que hay otra condición que debe cumplirse para la instrucción IF inicial.
ELSE IF| Una expresión ELSE IF especifica una condición alternativa que debe cumplirse antes de que se realice un conjunto de funciones específicas de esta instrucción ELSE IF. La presencia de una instrucción IF ELSE indica el final de la instrucción anterior. La única expresión condicional que se puede colocar después de una instrucción ELSE IF es otra instrucción ELSE IF. Esto significa que una instrucción ELSE IF solo puede usarse para especificar una única condición adicional que debe cumplirse.

**Ejemplo**: ![Condición de coincidencia de red CDN](./media/cdn-rules-engine-reference/cdn-rules-engine-conditional-expression.png)

 > [!TIP]
   > Una regla posterior puede invalidar las acciones especificadas por una regla anterior. Ejemplo: Una regla de detección de todo protege todas las solicitudes a través de la autenticación basada en token. Puede crearse otra regla directamente debajo de ella para realizar una excepción para ciertos tipos de solicitudes.

### <a name="next-steps"></a>Pasos siguientes
* [Información general de la red CDN de Azure](cdn-overview.md)
* [Referencia del motor de reglas](cdn-rules-engine-reference.md)
* [Condiciones de coincidencia del motor de reglas](cdn-rules-engine-reference-match-conditions.md)
* [Funciones del motor de reglas](cdn-rules-engine-reference-features.md)
* [Invalidación del comportamiento HTTP predeterminado mediante el motor de reglas](cdn-rules-engine.md)
