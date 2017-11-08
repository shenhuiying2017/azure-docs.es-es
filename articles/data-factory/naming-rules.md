---
title: Reglas para asignar nombres a entidades de Azure Data Factory | Microsoft Docs
description: "Describe las reglas de nomenclatura para las entidades de Factoría de datos."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: bc5e801d-0b3b-48ec-9501-bb4146ea17f1
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2017
ms.author: shlo
ms.openlocfilehash: 8f0207853097a3d31e06a722025f3964678414bf
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/01/2017
---
# <a name="azure-data-factory---naming-rules"></a>Azure Data Factory: reglas de nomenclatura
La tabla siguiente proporciona las reglas de nomenclatura para los artefactos de Factoría de datos.

> [!NOTE]
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general, consulte las [reglas de denominación en Data Factory versión 1](v1/data-factory-naming-rules.md).

| Nombre | Exclusividad del nombre | Comprobaciones de validación |
|:--- |:--- |:--- |
| Factoría de datos |Único en Microsoft Azure. Los nombres no distinguen mayúsculas de minúsculas, es decir, `MyDF` y `mydf` hacen referencia a la misma factoría de datos. |<ul><li>Cada factoría de datos está asociada exactamente a una suscripción de Azure.</li><li>Los nombres de objeto deben comenzar por una letra o un número, y pueden contener solo letras, números y el carácter de guión (-).</li><li>Los caracteres de guión (-) debe estar inmediatamente precedidos y seguidos por una letra o un número. No se permiten guiones consecutivos en los nombres de contenedor.</li><li>El nombre puede tener entre 3 y 63 caracteres.</li></ul> |
| Servicios, tablas o canalizaciones vinculados |Único en una factoría de datos. Los nombres no distinguen mayúsculas de minúsculas. |<ul><li>Número máximo de caracteres incluido en un nombre de tabla: 260.</li><li>Los nombres de objeto deben empezar con una letra, un número o un carácter de subrayado (_).</li><li>No se permiten los caracteres siguientes: “.”, “+”, “?”, “/”, “<”, ”>”,”*”,”%”,”&”,”:”,”\\”</li></ul> |
| Grupo de recursos |Único en Microsoft Azure. Los nombres no distinguen mayúsculas de minúsculas. |<ul><li>Número máximo de caracteres: 1000.</li><li>El nombre puede contener letras, dígitos y los siguientes caracteres: “-”, “_”, “,” y “.”</li></ul> |

## <a name="next-steps"></a>Pasos siguientes
Aprenda a crear factorías de datos siguiendo las instrucciones paso a paso de la [guía de inicio rápido para crear una factoría de datos](quickstart-create-data-factory-powershell.md). 