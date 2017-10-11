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
ms.openlocfilehash: c10145661a8c575381493c9aaa901c3ef92c2e81
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="azure-cdn-rules-engine"></a>Motor de reglas de la red CDN de Azure
En este tema se muestran descripciones detalladas de las características y las condiciones de coincidencia disponibles para el [Motor de reglas](cdn-rules-engine.md)de la Red de entrega de contenido (CDN) de Azure.

El motor de reglas de HTTP está diseñado para ser la entidad de certificación final sobre cómo la CDN procesa los tipos específicos de solicitudes.

**Usos habituales**:

- Invalidar o definir una directiva de memoria caché personalizada.
- Proteger o denegar las solicitudes de contenido confidencial.
- Redirigir solicitudes.
- Almacenar datos de registro personalizados.

## <a name="terminology"></a>Terminología
Una regla se define mediante el uso de [ **expresiones condicionales**](cdn-rules-engine-reference-conditional-expressions.md), [ **coincidencias**](cdn-rules-engine-reference-match-conditions.md) y [ **funciones**](cdn-rules-engine-reference-features.md). Estos elementos se resaltan en la siguiente ilustración.

 ![Condición de coincidencia de red CDN](./media/cdn-rules-engine-reference/cdn-rules-engine-terminology.png)

## <a name="syntax"></a>Sintaxis

La manera en que se tratarán los caracteres especiales varía según la forma en la que una condición de coincidencia o función administra los valores de texto. Una condición de coincidencia o función puede interpretar el texto de una de las siguientes maneras:

1. [**Valores literales**](#literal-values) 
2. [**Valores de carácter comodín**](#wildcard-values)
3. [**Expresiones regulares**](#regular-expressions)

### <a name="literal-values"></a>Valores literales
El texto que se interpreta como un valor literal tratará todos los caracteres especiales, excepto el símbolo %, como parte del valor que debe coincidir. En otras palabras, una condición de coincidencia literal establecida en `\'*'\` solo se puede lograr cuando se detecta ese valor exacto (es decir, `\'*'\`).
 
Se utiliza un símbolo de porcentaje para indicar la codificación de direcciones URL (p. ej., `%20`).

### <a name="wildcard-values"></a>Valores de carácter comodín
El texto que se interpreta como un valor comodín asignará un significado adicional a los caracteres especiales. En la tabla siguiente se describe cómo se interpretará el siguiente conjunto de caracteres.

Character | Descripción
----------|------------
\ | Se usa una barra diagonal inversa para el escape de cualquier carácter especificado en esta tabla. Debe especificarse una barra diagonal inversa directamente antes del carácter especial al que debe aplicarse el escape.<br/>Por ejemplo, la sintaxis siguiente aplica el escape a un asterisco: `\*`
% | Se utiliza un símbolo de porcentaje para indicar la codificación de direcciones URL (p. ej., `%20`).
* | Un asterisco es un carácter comodín que representa uno o más caracteres.
Espacio | Un carácter de espacio indica que una condición de coincidencia debe cumplirse mediante uno de los patrones o valores especificados.
'valor' | Las comillas simples no tienen un significado especial. Sin embargo, se utiliza un conjunto de comillas simples para indicar que un valor debe tratarse como un valor literal. Puede usarse de las siguientes maneras:<br><br/>- Permite que se cumpla una condición de coincidencia siempre que el valor especificado coincida con cualquier parte del valor de comparación.  Por ejemplo, `'ma'` coincidiría con cualquiera de las siguientes cadenas: <br/><br/>/business/**ma**rathon/asset.htm<br/>**ma**p.gif<br/>/business/template.**ma**p<br /><br />- Permite a un carácter especial que se especifique como un carácter literal. Por ejemplo, puede especificar un carácter de espacio literal incluyendo un carácter de espacio en un conjunto de comillas simples (es decir, `' '` o `'sample value'`).<br/>- Permite que se especifique un valor en blanco. Especifique un valor en blanco mediante la especificación de un conjunto de comillas simples (es decir, '').<br /><br/>**Importante:**<br/>- Si el valor especificado no contiene un carácter comodín, automáticamente se considerará un valor literal. Esto significa que no es necesario especificar un conjunto de comillas simples.<br/>- Si una barra diagonal inversa no aplica el escape a otro carácter de esta tabla, se omitirá cuando se especifique dentro de un conjunto de comillas simples.<br/>- Otra manera de especificar un carácter especial como carácter literal es aplicar el escape con una barra diagonal inversa (es decir, `\`).

### <a name="regular-expressions"></a>Expresiones regulares

Las expresiones regulares definen un patrón que se buscará dentro de un valor de texto. La notación de expresiones regulares define significados concretos en una variedad de símbolos. La tabla siguiente indica cómo se tratan los caracteres especiales mediante las condiciones de coincidencia y funciones que admiten expresiones regulares.

Carácter especial | Descripción
------------------|------------
\ | Una barra diagonal inversa aplica el escape al carácter que le sigue. Esto hace que ese carácter se trate como un valor literal en lugar de tener su significado de expresión habitual. Por ejemplo, la sintaxis siguiente aplica el escape a un asterisco: `\*`
% | El significado de un símbolo de porcentaje depende de su uso.<br/><br/> `%{HTTPVariable}`: esta sintaxis identifica una variable HTTP.<br/>`%{HTTPVariable%Pattern}`: esta sintaxis utiliza un símbolo de porcentaje para identificar una variable HTTP y se usa como delimitador.<br />`\%`: el escape de un símbolo de porcentaje permite que se use como valor literal o para indicar la codificación de la dirección URL (p. ej., `\%20`).
* | Un asterisco permite que el carácter anterior coincida con cero o más veces. 
Espacio | Normalmente, un carácter de espacio se trata como un carácter literal. 
'valor' | Las comillas simples se tratan como caracteres literales. Un conjunto de comillas simples no tiene un significado especial.


## <a name="next-steps"></a>Pasos siguientes
* [Condiciones de coincidencia del motor de reglas](cdn-rules-engine-reference-match-conditions.md)
* [Expresiones condicionales del motor de reglas](cdn-rules-engine-reference-conditional-expressions.md)
* [Funciones del motor de reglas](cdn-rules-engine-reference-features.md)
* [Invalidación del comportamiento HTTP predeterminado mediante el motor de reglas](cdn-rules-engine.md)
* [Información general de la red CDN de Azure](cdn-overview.md)
