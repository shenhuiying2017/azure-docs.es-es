---
title: "API de facturación de Azure Enterprise - Saldos y resumen| Microsoft Docs"
description: "Obtenga información sobre las API de RateCard y de uso de facturación de Azure que se usan para proporcionar información sobre el consumo de recursos y tendencias de Azure."
services: 
documentationcenter: 
author: aedwin
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
ms.openlocfilehash: f6b149f0e656d2263705048aa5b644f5bb4a5712
ms.contentlocale: es-es
ms.lasthandoff: 07/31/2017

---
# <a name="reporting-apis-for-enterprise-customers---balance-and-summary"></a>API de informes para clientes de Enterprise: saldos y resumen

La API de saldos y resumen ofrece un resumen mensual de información sobre saldos, nuevas compras, gastos de servicios en Azure Marketplace, ajustes y gastos por de uso por encima del límite.


##<a name="request"></a>Solicitud 
Las propiedades de encabezado comunes que tienen que agregarse se especifican [aquí](billing-enterprise-api.md). Si no se especifica un período de facturación, se devuelven datos para el período de facturación actual.

|Método | URI de solicitud|
|-|-|
|GET| https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/balancesummary|
|GET| https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/balancesummary|

> [!Note]
> Para usar la versión preliminar de la API, reemplace v2 por v1 en la dirección URL anterior.
>

## <a name="response"></a>Response

        {
            "id": "enrollments/100/billingperiods/201507/balancesummaries",
            "billingPeriodId": 201507,
            "currencyCode": "USD",
            "beginningBalance": 0,
            "endingBalance": 1.1,
            "newPurchases": 1,
            "adjustments": 1.1,
            "utilized": 1.1,
            "serviceOverage": 1,
            "chargesBilledSeparately": 1,
            "totalOverage": 1,
            "totalUsage": 1.1,
            "azureMarketplaceServiceCharges": 1,
            "newPurchasesDetails": [
                {
                "name": "",
                "value": 1
                }
            ],
            "adjustmentDetails": [
                {
                "name": "Promo Credit",
                "value": 1.1
                },
                {
                "name": "SIE Credit",
                "value": 1.0
                }
            ]
        }


**Definiciones de propiedad de respuesta**

|Nombre de propiedad| Tipo| Descripción
|-|-|-|
|id|cadena|Identificador único para una inscripción y un período de facturación específicos|
|billingPeriodId|cadena |Identificador de período de facturación|
|currencyCode|cadena |Código de divisa|
|beginningBalance|Decimal| Saldo inicial del período de facturación|
|endingBalance|Decimal| Saldo final del período de facturación (para períodos abiertos, se actualizará a diario)|
|newPurchases|Decimal| Cantidad total de nuevas compras|
|adjustments|Decimal| Importe total de ajuste|
|utilized|Decimal| Uso total de compromiso|
|serviceOverage|Decimal| Uso por encima del límite de servicios de Azure|
|chargesBilledSeparately|Decimal| Gastos facturados por separado|
|totalOverage|Decimal| serviceOverage + chargesBilledSeparately|
|totalUsage|Decimal| Compromiso de servicio de Azure + uso por encima del límite total|
|azureMarketplaceServiceCharges|Decimal| Gastos totales de Azure Marketplace|
|newPurchasesDetails|Matriz de cadenas JSON de pares de nombre y valor|Lista de nuevas compras|
|adjustmentDetails|Matriz de cadenas JSON de pares de nombre y valor|Lista de ajustes (crédito promocional, crédito SIE, etc.) |


<br/>
## <a name="see-also"></a>Otras referencias

* [API de períodos de facturación](billing-enterprise-api-billing-periods.md)

* [API de detalles de uso](billing-enterprise-api-usage-detail.md) 

* [API de gastos en la tienda Marketplace](billing-enterprise-api-marketplace-storecharge.md) 

* [API de hoja de precios](billing-enterprise-api-pricesheet.md)
