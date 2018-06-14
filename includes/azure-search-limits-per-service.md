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
ms.openlocfilehash: b4062aab5a453505ef4586f422a124d4bbf715cb
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/05/2018
ms.locfileid: "30844090"
---
El almacenamiento está limitado por el espacio en disco o el *número máximo* de índices, documentos u otros recursos de alto nivel, lo que ocurra primero. En la tabla siguiente se documentan los límites de almacenamiento. Para los límites máximos en índices, documentos y otros objetos, consulte los [límites por recurso](../articles/search/search-limits-quotas-capacity.md#index-limits).

| Recurso | Gratuito | Básico&nbsp;<sup>1</sup> | S1 | S2 | S3 | S3&nbsp;HD&nbsp;<sup>2</sup> |
| -------- | --- | --- | --- | --- | --- | --- |
| Acuerdo de Nivel de Servicio (SLA) <sup>3</sup>  |Sin  |Sí |Sí |Sí |Sí |Sí |
| Almacenamiento por partición |50 MB |2 GB |25 GB |100 GB |200 GB |200 GB |
| Particiones por servicio |N/D |1 |12 |12 |12 |3 |
| Tamaño de la partición |N/D |2 GB |25 GB |100 GB |200 GB |200 GB |
| Réplicas |N/D |3 |12 |12 |12 |12 |

<sup>1</sup> Básico tiene una partición fija. En este nivel, se utilizan SU adicionales para asignar más réplicas a más cargas de trabajo de consulta.

<sup>2</sup> S3 HD tiene un límite máximo de 3 particiones, que es inferior al límite de partición para S3. El límite inferior de la partición se impone porque el número de índice para S3 HD es mucho más alto. Como existen límites de servicio en ambos recursos informáticos (almacenamiento y procesamiento) y el contenido (índices y documentos), el límite de contenido se alcanza primero.

<sup>3</sup> Los Acuerdos de nivel de servicio (SLA) se ofrecen para los servicios facturables en recursos dedicados. Los servicios gratuitos y las características de versión preliminar no tienen SLA. Para los servicios facturables, los SLA tomarán efecto cuando se aprovisione suficiente redundancia para el servicio. Son necesarias dos o más réplicas para el SLA de consulta (lectura). Son necesarias tres o más réplicas para el SLA de consulta e indexación (lectura y escritura). El número de particiones no es una consideración del SLA. 