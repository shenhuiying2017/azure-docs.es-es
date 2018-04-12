---
title: archivo de inclusión
description: archivo de inclusión
services: container-registry
author: mmacy
ms.service: container-registry
ms.topic: include
ms.date: 03/23/2018
ms.author: marsma
ms.custom: include file
ms.openlocfilehash: 575483192954f4e05db50e701e223829e041cffc
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/05/2018
---
| Recurso | Básica | Estándar | Premium |
|---|---|---|---|---|
| Storage | 10 GiB | 100 GiB| 500 GiB |
| Operaciones de lectura por minuto<sup>1, 2</sup> | 1000 | 3000 | 10000 |
| Operaciones de escritura por minuto<sup>1, 3</sup> | 100 | 500 | 2000 |
| Ancho de banda de descarga en MBps<sup>1</sup> | 30 | 60 | 100 |
| Ancho de banda de carga en MBps<sup>1</sup> | 10 | 20 | 50 |
| Webhooks | 2 | 10 | 100 |
| Replicación geográfica | N/D | N/D | [Compatible *(versión preliminar)*](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication) |

<sup>1</sup> Las *operaciones de lectura*, las *operaciones de escritura* y el *ancho de banda* son estimaciones mínimas. ACR trata de mejorar el rendimiento que el uso requiere.

<sup>2</sup> La [extracción de docker](https://docs.docker.com/registry/spec/api/#pulling-an-image) se traduce en varias operaciones de lectura en función del número de capas de la imagen, además de la recuperación de manifiesto.

<sup>3</sup> La [inserción de docker](https://docs.docker.com/registry/spec/api/#pushing-an-image) se traduce en varias operaciones de escritura, en función del número de capas que se deben insertar. Un elemento `docker push` incluye *operaciones de lectura* para recuperar un manifiesto para una imagen existente.