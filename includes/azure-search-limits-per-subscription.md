---
title: archivo de inclusión
description: archivo de inclusión
services: search
author: HeidiSteen
ms.service: search
ms.topic: include
ms.date: 04/04/2018
ms.author: heidist
ms.custom: include file
ms.openlocfilehash: 8a4f794c8ef24a90498954629c131904621c5b43
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/05/2018
ms.locfileid: "30844099"
---
Puede crear varios servicios en una suscripción, cada uno de ellos aprovisionado en un nivel concreto, el único límite lo establece el número de servicios que se permiten en cada nivel. Por ejemplo, puede crear hasta doce servicios en el nivel básico y otros doce en el nivel de S1 dentro de la misma suscripción. Para más información acerca de los niveles, consulte [Selección de SKU o plan de tarifa de Azure Search](../articles/search/search-sku-tier.md).

El límite máximo de servicios se puede elevar a petición. Si necesita tener más servicios en la misma suscripción, póngase en contacto con el soporte técnico de Azure.

| Recurso            | Gratis&nbsp;<sup>1</sup> | Básica | S1  | S2 | S3 | S3&nbsp;HD |
| ------------------- | ---- | ----- | --- | -- | -- | ----- |
| Servicios máximos    |1     | 12    | 12  | 6  | 6  | 6     |
| Escala máxima en SU&nbsp;<sup>2</sup> |N/D |3 unidades de búsqueda |36 unidades de búsqueda |36 unidades de búsqueda |36 unidades de búsqueda |36 unidades de búsqueda |

<sup>1</sup>Gratis se basa en recursos compartidos, no dedicados. El escalado vertical no se admite en los recursos compartidos.

<sup>2</sup> Las unidades de búsqueda (SU) son unidades facturables, asignadas como *réplica* o como *partición*. Ambos recursos se necesitan para las operaciones de almacenamiento, indexación y consulta. Para más información sobre los cálculos de SU, consulte [Escalado de niveles de recursos para cargas de trabajo de indexación y consulta en Azure Search](../articles/search/search-capacity-planning.md). 