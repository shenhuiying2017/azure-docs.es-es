---
title: "Transformación Expandir JSON con Azure Machine Learning Workbench"
description: "Documento de referencia para la transformación \"Expandir JSON\""
services: machine-learning
author: ranvijaykumar
ms.author: ranku
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, reference
ms.topic: article
ms.date: 09/14/2017
ms.openlocfilehash: 614f4422aa987fc32dcce62826bb2477473fdc32
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/18/2017
---
# <a name="expand-json-transformation"></a>Transformación Expandir JSON
La transformación **Expandir JSON** permite que los usuarios expandan a varias columnas una columna existente que contiene un texto JSON válido.

## <a name="how-to-perform-this-transformation"></a>Cómo llevar a cabo esta transformación

Siga estos pasos para llevar a cabo esta transformación:
1. Seleccione la columna de origen que contiene el texto JSON.
2. Seleccione **Expand JSON** (Expandir JSON) en el menú **Transforms** (Transformaciones). También puede hacer clic con el botón derecho en el encabezado de la columna de origen y seleccionar **Expand JSON** (Expandir JSON) en el menú contextual. 
3. Haga clic en **OK**. 
 
Junto a la columna de origen se agregan nuevas columnas. Estas columnas contienen propiedades del siguiente nivel de jerarquía del texto JSON. "Property Key" (Clave de propiedad), si aparece, se usa para crear el nombre de la columna correspondiente. Las propiedades anidadas se conservan como texto JSON que el usuario puede expandir o descartar de forma iterativa según sus necesidades.

## <a name="examples"></a>Ejemplos

La columna de origen *Cliente* se expande en dos columnas, *Nombre.Cliente* y *Teléfono.Cliente*.

| Cliente                                                | Nombre.Cliente   | Teléfono.Cliente |
|---------------------------------------------------------|-----------------|----------------|
| { "Name" : "Carrie Dodson", "Phone" : "123-4567-890"}   | Carrie Dodson   | 123-4567-890   |
| { "Name" : "Leonard Robledo", "Phone" : "456-7890-123"} | Leonard Robledo | 456-7890-123   |

