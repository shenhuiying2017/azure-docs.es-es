---
title: Disponibilidad de recursos y regiones en Azure Container Instances
description: "Descubra qué regiones de Azure admiten la implementación de instancias de contenedor y los límites de CPU y de memoria para esas instancias."
services: container-instances
author: mmacy
manager: timlt
ms.service: container-instances
ms.topic: overview
ms.date: 12/15/2017
ms.author: marsma
ms.openlocfilehash: ec7f469c47924f4ae22d6509996ca9cf498fc9ad
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/16/2017
---
# <a name="region-availability-for-azure-container-instances"></a>Disponibilidad de regiones en Azure Container Instances

En la versión preliminar, Azure Container Instances está disponible en las siguientes regiones con los límites de memoria y de CPU especificados.

| La ubicación | SO | CPU | Memoria (GB) |
| -------- | -- | :---: | :-----------: |
| Europa occidental, Oeste de EE. UU., Este de EE. UU.  | Linux | 4 | 14 |
| Europa occidental, Oeste de EE. UU., Este de EE. UU.  | Windows | 4 | 14 |

## <a name="resource-availability"></a>Disponibilidad de recursos

Las instancias de contenedor creadas dentro de estos límites de recursos están sujetas a disponibilidad dentro de la región de implementación. Cuando una región está sometida a mucha carga, puede experimentar un error al implementar instancias.

Para mitigar este tipo de error de implementación, intente implementar las instancias con una configuración inferior de memoria y de CPU, o intente realizar la implementación en un momento posterior.

## <a name="next-steps"></a>pasos siguientes

Para más información sobre cómo solucionar problemas de la implementación de instancias de contenedor, consulte [Solución de problemas en las implementaciones en Azure Container Instances](container-instances-troubleshooting.md).