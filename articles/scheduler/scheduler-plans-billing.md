---
title: Planes y facturación en Azure Scheduler
description: Planes y facturación en Azure Scheduler
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: ''
ms.assetid: 13a2be8c-dc14-46cc-ab7d-5075bfd4d724
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/18/2016
ms.author: deli
ms.openlocfilehash: b25e97b0f0d0b6f63134a774856eb7ec8f77b679
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/05/2018
ms.locfileid: "30837445"
---
# <a name="plans-and-billing-in-azure-scheduler"></a>Planes y facturación en Azure Scheduler
## <a name="job-collection-plans"></a>Planes de colección de trabajos
Las colecciones de trabajos son la entidad facturable en Azure Scheduler. Las colecciones de trabajos contienen varios trabajos y vienen en tres planes que se describen a continuación: Estándar, P10 Premium y P20 Premium.

| **Plan de colección de trabajos** | **Nº máximo de trabajos por colección de trabajos** | **Periodicidad máxima** | **N.º máximo de colecciones de trabajo por suscripción** | **Límites** |
|:--- |:--- |:--- |:--- |:--- |
| **Estándar** |50 trabajos por colección de trabajos |Una vez por minuto. No se pueden ejecutar trabajos con una frecuencia superior a una vez por minuto |Se permite una suscripción de 100 colecciones de trabajos estándar como máximo. |Acceso al conjunto completo de características de Scheduler |
| **Premium P10** |50 trabajos por colección de trabajos |Una vez por minuto. No se pueden ejecutar trabajos con una frecuencia superior a una vez por minuto |Se permite una suscripción de 10 000 colecciones de trabajos premium P10 como máximo. <a href="mailto:wapteams@microsoft.com">Póngase en contacto con nosotros</a> para obtener más información. |Acceso al conjunto completo de características de Scheduler |
| **Premium P20** |1000 trabajos por colección de trabajos |Una vez por minuto. No se pueden ejecutar trabajos con una frecuencia superior a una vez por minuto |Se permite una suscripción de 10 000 colecciones de trabajos premium P20 como máximo. <a href="mailto:wapteams@microsoft.com">Póngase en contacto con nosotros</a> para obtener más información. |Acceso al conjunto completo de características de Scheduler |

## <a name="upgrades-and-downgrades-of-job-collection-plans"></a>Actualizaciones y degradaciones de planes de colección de trabajos
Puede actualizar o degradar un plan de colección de trabajos siempre que quiera entre los planes Estándar, P10 Premium y P20 Premium.

## <a name="billing-and-azure-plans"></a>Planes de Azure y facturación
Si tiene más de 100 colecciones de trabajos estándar (10 unidades de facturación estándar), resulta más conveniente tener todas las colecciones de trabajo en el plan Premium.

Si tiene una colección de trabajos estándar y una colección de trabajos premium, se le facturará una unidad de facturación estándar *y* una unidad de facturación premium. Las facturas del servicio Scheduler se basan en el número de colecciones de trabajos activas que se establecen en Standard o Premium; esto se explica más detalladamente en las dos secciones siguientes.

## <a name="standard-billable-units"></a>Unidades facturables estándar
Una unidad facturable estándar puede incluir como máximo 10 colecciones de trabajos estándar. Puesto que una colección de trabajos estándar puede tener hasta 50 trabajos por colección de trabajos, una unidad de facturación estándar permite que una suscripción tenga hasta 500 trabajos: casi 22 millones de ejecuciones de trabajos al mes.

Si tiene entre 1 y 10 colecciones de trabajos estándar, se le facturará 1 unidad de facturación estándar. Si tiene entre 11 y 20 colecciones de trabajos estándar, se le facturarán 2 unidades de facturación estándar. Si tiene entre 21 y 30 colecciones de trabajos estándar, se le facturarán 3 unidades de facturación estándar.

## <a name="p10-premium-billable-units"></a>Unidades facturables premium P10
Una unidad facturable premium P10 puede incluir como máximo 10 000 colecciones de trabajos premium P10. Puesto que una colección de trabajos premium P10 puede tener hasta 50 trabajos por colección de trabajos, una unidad de facturación premium permite que una suscripción tenga hasta 500 000 trabajos: casi 22 000 millones de ejecuciones de trabajos al mes.

Si tiene entre 1 y 10 000 colecciones de trabajos premium, se le facturará 1 unidad de facturación premium P10. Si tiene entre 10 001 y 20 000 colecciones de trabajos premium, se le facturarán 2 unidades de facturación premium P10.

Por lo tanto, las colecciones de trabajos premium P10 tienen la misma funcionalidad que las colecciones de trabajos estándar, pero ofrecen un descuento en caso de que la aplicación requiera gran cantidad de colecciones de trabajos.

## <a name="p20-premium-billable-units"></a>Unidades facturables premium P20
Una unidad facturable premium P20 puede incluir como máximo 5000 colecciones de trabajos premium P20. Puesto que una colección de trabajos premium P20 puede tener hasta 1000 trabajos por colección de trabajos, una unidad de facturación premium permite que una suscripción tenga hasta 5 000 000 trabajos: casi 220 000 millones de ejecuciones de trabajos al mes.

Las colecciones de trabajos premium P20 proporcionan las mismas funcionalidades que las de los trabajos premium P10. Sin embargo, también admiten un mayor número de trabajos por colección de trabajos y más trabajos globales que los de premium P10, con lo que puede obtener una mayor escalabilidad.

## <a name="billing-and-active-status"></a>Estado de facturación y activo
Las colecciones de trabajos están siempre activas, salvo que la suscripción completa pase a un estado deshabilitado temporal debido a problemas de facturación. La única manera de garantizar que una colección de trabajos no se facture consiste en establecerla en el plan *Gratis* o en eliminar la colección de trabajos.

Aunque puede deshabilitar todos los trabajos de una colección de trabajos en una sola operación, esto no cambia el estado de facturación de la colección de trabajos: la colección de trabajos se *seguirá* facturando. De forma similar, las colecciones de trabajos vacías se consideran activas y se facturarán.

## <a name="pricing"></a>Precios
Para obtener información detallada sobre los precios, vea [Precios de Scheduler](https://azure.microsoft.com/pricing/details/scheduler/).

## <a name="see-also"></a>Otras referencias
 [¿Qué es Scheduler?](scheduler-intro.md)

 [Conceptos, terminología y jerarquía de entidades de Azure Scheduler](scheduler-concepts-terms.md)

 [Introducción a Azure Scheduler en Azure Portal](scheduler-get-started-portal.md)

 [Referencia de API de REST de Azure Scheduler](https://msdn.microsoft.com/library/mt629143)

 [Referencia de cmdlets de PowerShell de Azure Scheduler](scheduler-powershell-reference.md)

 [Alta disponibilidad y confiabilidad de Azure Scheduler](scheduler-high-availability-reliability.md)

 [Límites, valores predeterminados y códigos de error de Azure Scheduler](scheduler-limits-defaults-errors.md)

 [Autenticación de salida de Azure Scheduler](scheduler-outbound-authentication.md)

