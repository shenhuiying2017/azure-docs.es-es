---
title: Referencia del motor de reglas de la red CDN de Azure | Microsoft Docs
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
ms.openlocfilehash: 602b4303dd1940791c11b8b71ac6a27f0474a6d5
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/02/2018
---
# <a name="azure-cdn-rules-engine-reference"></a>Referencia del motor de reglas de Azure CDN
En este artículo se muestran descripciones detalladas de las condiciones de coincidencia disponibles para el [motor de reglas](cdn-rules-engine.md) de Azure Content Delivery Network (CDN).

El motor de reglas está diseñado para ser la entidad de certificación final sobre cómo la red CDN procesa tipos específicos de solicitudes.

**Usos habituales**:

- Invalidar o definir una directiva de memoria caché personalizada.
- Proteger o denegar las solicitudes de contenido confidencial.
- Redirigir solicitudes.
- Almacenar datos de registro personalizados.

## <a name="terminology"></a>Terminología
Una regla se define mediante el uso de [**expresiones condicionales**](cdn-rules-engine-reference-conditional-expressions.md), [**coincidencias**](cdn-rules-engine-reference-match-conditions.md) y [**características**](cdn-rules-engine-reference-features.md). Estos elementos se resaltan en la siguiente ilustración:

 ![Condición de coincidencia de red CDN](./media/cdn-rules-engine-reference/cdn-rules-engine-terminology.png)

## <a name="syntax"></a>Sintaxis

La manera en que se tratarán los caracteres especiales varía según la forma en la que una condición de coincidencia o función administra los valores de texto. Una condición de coincidencia o función puede interpretar el texto de una de las siguientes maneras:

1. [**Valores literales**](#literal-values) 
2. [**Valores de carácter comodín**](#wildcard-values)
3. [**Expresiones regulares**](#regular-expressions)

### <a name="literal-values"></a>Valores literales
El texto que se interpreta como un valor literal trata todos los caracteres especiales, excepto el símbolo %, como parte del valor que debe coincidir. En otras palabras, una condición de coincidencia literal establecida en `\'*'\` solo se puede satisfacer cuando se encuentra ese valor exacto (es decir, `\'*'\`).
 
Se usa un símbolo de porcentaje para indicar la codificación de direcciones URL (p. ej., `%20`).

### <a name="wildcard-values"></a>Valores de carácter comodín
El texto que se interpreta como un valor comodín asigna un significado adicional a los caracteres especiales. En la tabla siguiente se describe cómo se interpreta el siguiente conjunto de caracteres:

Character | DESCRIPCIÓN
----------|------------
\ | Se usa una barra diagonal inversa para el escape de cualquier carácter especificado en esta tabla. Debe especificarse una barra diagonal inversa directamente antes del carácter especial al que debe aplicarse el escape.<br/>Por ejemplo, la sintaxis siguiente aplica el escape a un asterisco: `\*`
% | Se usa un símbolo de porcentaje para indicar la codificación de direcciones URL (p. ej., `%20`).
* | Un asterisco es un carácter comodín que representa uno o más caracteres.
Espacio | Un carácter de espacio indica que una condición de coincidencia debe cumplirse mediante uno de los patrones o valores especificados.
'valor' | Las comillas simples no tienen un significado especial. Sin embargo, se utiliza un conjunto de comillas simples para indicar que un valor debe tratarse como un valor literal. Puede usarse de las siguientes maneras:<br><br/>- Permite que se cumpla una condición de coincidencia siempre que el valor especificado coincida con cualquier parte del valor de comparación.  Por ejemplo, `'ma'` coincidiría con cualquiera de las siguientes cadenas: <br/><br/>/business/**ma**rathon/asset.htm<br/>**ma**p.gif<br/>/business/template.**ma**p<br /><br />- Permite a un carácter especial que se especifique como un carácter literal. Por ejemplo, puede especificar un carácter de espacio literal mediante la inclusión de un carácter de espacio entre un conjunto de comillas simples (es decir, `' '` o `'sample value'`).<br/>- Permite que se especifique un valor en blanco. Especifique un valor en blanco mediante la especificación de un conjunto de comillas simples (es decir, '').<br /><br/>**Importante:**<br/>-Si el valor especificado no contiene un carácter comodín, entonces se considera automáticamente un valor literal, lo que significa que no es necesario especificar un juego de comillas simples.<br/>- Si una barra diagonal inversa no aplica el escape a otro carácter de esta tabla, se omite cuando se especifique dentro de un conjunto de comillas simples.<br/>- Otra manera de especificar un carácter especial como un carácter literal es aplicarle el escape con una barra diagonal inversa (es decir, `\`).

### <a name="regular-expressions"></a>Expresiones regulares

Las expresiones regulares definen un patrón que se busca dentro de un valor de texto. La notación de expresiones regulares define significados concretos en una variedad de símbolos. La tabla siguiente indica cómo se tratan los caracteres especiales mediante las condiciones de coincidencia y funciones que admiten expresiones regulares.

Carácter especial | DESCRIPCIÓN
------------------|------------
\ | Una barra diagonal inversa aplica el escape al carácter que le sigue, lo que hace que ese carácter se trate como un valor literal en lugar de tomar el significado de su expresión regular. Por ejemplo, la sintaxis siguiente aplica el escape a un asterisco: `\*`
% | El significado de un símbolo de porcentaje depende de su uso.<br/><br/> `%{HTTPVariable}`: esta sintaxis identifica una variable HTTP.<br/>`%{HTTPVariable%Pattern}`: esta sintaxis utiliza un símbolo de porcentaje para identificar una variable HTTP y se usa como delimitador.<br />`\%`: el escape de un símbolo de porcentaje permite que se use como valor literal o para indicar la codificación de la dirección URL (p. ej., `\%20`).
* | Un asterisco permite que el carácter anterior coincida con cero o más veces. 
Espacio | Normalmente, un carácter de espacio se trata como un carácter literal. 
'valor' | Las comillas simples se tratan como caracteres literales. Un conjunto de comillas simples no tiene un significado especial.


## <a name="next-steps"></a>Pasos siguientes
* [Condiciones de coincidencia del motor de reglas](cdn-rules-engine-reference-match-conditions.md)
* [Expresiones condicionales del motor de reglas](cdn-rules-engine-reference-conditional-expressions.md)
* [Características del motor de reglas](cdn-rules-engine-reference-features.md)
* [Invalidación del comportamiento HTTP mediante el motor de reglas](cdn-rules-engine.md)
* [Información general de Azure CDN](cdn-overview.md)
