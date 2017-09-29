---
title: "API de facturación de Azure Enterprise - PriceSheet| Microsoft Docs"
description: "Obtenga información acerca de las API de informes que permiten a los clientes de Azure Enterprise extraer datos de consumo mediante programación."
services: 
documentationcenter: 
author: cwatson-cat
manager: aedwin
editor: 
tags: billing
ms.assetid: 3e817b43-0696-400c-a02e-47b7817f9b77
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 04/25/2017
ms.author: aedwin
ms.translationtype: HT
ms.sourcegitcommit: 6e76ac40e9da2754de1d1aa50af3cd4e04c067fe
ms.openlocfilehash: 2e7d6e883abe4cee13bc5f684baf2a1ea9c6c397
ms.contentlocale: es-es
ms.lasthandoff: 07/31/2017

---
# <a name="reporting-apis-for-enterprise-customers---price-sheet"></a>API de informes para clientes de Enterprise: hoja de precios

La API de hoja de precios proporciona el tipo aplicable de cada medidor para la inscripción y el período de facturación determinados.

##<a name="request"></a>Solicitud
Las propiedades de encabezado comunes que tienen que agregarse se especifican [aquí](billing-enterprise-api.md). Si no se especifica un período de facturación, se devuelven datos para el período de facturación actual.

|Método | URI de solicitud|
|-|-|
|GET|https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/pricesheet|
|GET|https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/pricesheet|

> [!Note]
> Para usar la versión preliminar de la API, reemplace v2 por v1 en la dirección URL anterior.
>

## <a name="response"></a>Respuesta

    
        [
            {
                "id": "enrollments/57354989/billingperiods/201601/products/343/pricesheets",
                "billingPeriodId": "201704",
                "meterId": "dc210ecb-97e8-4522-8134-2385494233c0",
                "meterName": "A1 VM",
                "unitOfMeasure": "100 Hours",
                "includedQuantity": 0,
                "partNumber": "N7H-00015",
                "unitPrice": 0.00,
                "currencyCode": "USD"
            },
            {
                "id": "enrollments/57354989/billingperiods/201601/products/2884/pricesheets",
                "billingPeriodId": "201404",
                "meterId": "dc210ecb-97e8-4522-8134-5385494233c0",
                "meterName": "Locally Redundant Storage Premium Storage - Snapshots - AU East",
                "unitOfMeasure": "100 GB",
                "includedQuantity": 0,
                "partNumber": "N9H-00402",
                "unitPrice": 0.00,
                "currencyCode": "USD"
            },
            ...
        ]
    

> [!Note]
>Si está utilizando la API de versión preliminar, el campo meterId no está disponible.
>

**Definiciones de propiedad de respuesta**

|Nombre de propiedad| Tipo| Descripción
|-|-|-|
|id| string| El identificador único que representa un elemento determinado de PriceSheet (medición por período de facturación)|
|billingPeriodId| cadena| El identificador único que representa un período de facturación determinado|
|meterId| cadena| Identificador del medidor. Se puede asignar a la propiedad meterId de utilización.|
|meterName| cadena| Nombre de medidor|
|unitOfMeasure| cadena| La unidad de medida para medir el servicio|
|includedQuantity| Decimal| Cantidad que se incluye |
|partNumber| string| El número de pieza asociado al medidor|
|unitPrice| Decimal| El precio por unidad del medidor|
|currencyCode| cadena| El código de divisa para unitPrice|
<br/>
## <a name="see-also"></a>Otras referencias

* [API de períodos de facturación](billing-enterprise-api-billing-periods.md)

* [API de detalles de uso](billing-enterprise-api-usage-detail.md)

* [API de saldo y resumen](billing-enterprise-api-balance-summary.md)

* [API de gastos en la tienda Marketplace](billing-enterprise-api-marketplace-storecharge.md)

