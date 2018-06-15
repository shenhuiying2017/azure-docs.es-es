---
title: archivo de inclusión
description: archivo de inclusión
services: api-management
author: vladvino
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: api-management
ms.topic: include
ms.date: 03/22/2018
ms.author: vlvinogr
ms.custom: include file
ms.openlocfilehash: b40e2ba448236bcef72ffe501b4b14fa0dd64e1c
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2018
ms.locfileid: "31434946"
---
| Recurso | Límite |
| --- | --- |
| Unidades de escala | 10 por región<sup>1</sup> |
| Memoria caché | 5 GB por unidad<sup>1</sup> |
| Conexiones simultáneas de back-end<sup>2</sup> por autoridad HTTP | 2048 por unidad<sup>3</sup> |
| Tamaño máximo de respuestas en caché | 10 MB |
| Tamaño máximo del documento de directiva | 256 KB |
| Número máximo de dominios de puerta de enlace personalizada | 20 por instancia de servicio<sup>4</sup> |


<sup>1</sup>Los límites de Administración de API son diferentes para cada nivel de precios. Para ver los planes de tarifa y sus límites de escalado, vaya a [Precios de API Management](https://azure.microsoft.com/pricing/details/api-management/).
<sup>2</sup> Las conexiones se agrupan y se vuelven a utilizar, a menos que el back-end las cierre explícitamente.
<sup>3</sup> Por unidad de niveles Básico, Estándar y Premium. El nivel de desarrollador está limitado a 1024.
<sup>4</sup> Disponible solo en el nivel Premium.


