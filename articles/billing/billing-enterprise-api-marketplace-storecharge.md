---
title: "API de facturación de Azure Enterprise: Gastos en Marketplace | Microsoft Docs"
description: "Obtenga información acerca de las API de informes que permiten a los clientes de Azure Enterprise extraer datos de consumo mediante programación."
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
ms.openlocfilehash: 5539623f7ae35e14b6dafe6fdf9efe4bcaba4fd3
ms.contentlocale: es-es
ms.lasthandoff: 07/31/2017

---
# <a name="reporting-apis-for-enterprise-customers---marketplace-store-charge"></a>API de informes para clientes de Enterprise: gastos de la tienda Marketplace

La API de gastos de la tienda Marketplace devuelve el desglose de los gastos de Marketplace basado en el uso por día para el período de facturación o las fechas de inicio y finalización especificadas (no se incluyen las cuotas de una vez).

##<a name="request"></a>Solicitud 
Las propiedades de encabezado comunes que tienen que agregarse se especifican [aquí](billing-enterprise-api.md). Si no se especifica un período de facturación, se devuelven datos para el período de facturación actual. Los intervalos de tiempo personalizados pueden especificarse con los parámetros de fecha de inicio y finalización que están en formato aaaa-MM-dd. El intervalo de tiempo máximo compatible es de 36 meses.  

|Método | URI de solicitud|
|-|-|
|GET|https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/marketplacecharges|
|GET|https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/marketplacecharges|
|GET|https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/marketplacechargesbycustomdate?startTime=2017-01-01&endTime=2017-01-10|

> [!Note]
> Para usar la versión preliminar de la API, reemplace v2 por v1 en la dirección URL anterior.
>

## <a name="response"></a>Respuesta
 
    
        [
            {
                "id": "id",
                "subscriptionGuid": "00000000-0000-0000-0000-000000000000",
                "subscriptionName": "subName",
                "meterId": "2core",
                "usageStartDate": "2015-09-17T00:00:00Z",
                "usageEndDate": "2015-09-17T23:59:59Z",
                "offerName": "Virtual LoadMaster™ (VLM) for Azure",
                "resourceGroup": "Res group",
                "instanceId": "id",
                "additionalInfo": "{\"ImageType\":null,\"ServiceType\":\"Medium\"}",
                "tags": "",
                "orderNumber": "order",
                "unitOfMeasure": "",
                "costCenter": "100",
                "accountId": 100,
                "accountName": "Account Name",
                "accountOwnerId": "account@live.com",
                "departmentId": 101,
                "departmentName": "Department 1",
                "publisherName": "Publisher 1",
                "planName": "Plan name",
                "consumedQuantity": 1.15,
                "resourceRate": 0.1,
                "extendedCost": 1.11
            },
            ...
        ]
    

**Definiciones de propiedad de respuesta**

|Nombre de propiedad| Tipo| Descripción
|-|-|-|
|id|cadena|Identificador único para el elemento de gastos en Marketplace|
|subscriptionGuid|Guid|GUID de suscripción|
|subscriptionName|cadena|Nombre de suscripción|
|meterId|cadena|Identificador del medidor emitido|
|usageStartDate|DateTime|Hora de inicio del registro de uso|
|usageEndDate|DateTime|Hora de finalización del registro de uso|
|offerName|cadena|Nombre de la oferta|
|resourceGroup|string|Grupo de recursos|
|instanceId|cadena|Identificador de instancia|
|additionalInfo|string|Cadena JSON de información adicional|
|etiquetas|cadena|Cadena JSON de etiqueta|
|orderNumber|string|Número de pedido|
|unitOfMeasure|cadena|Unidad de medida del medidor|
|costCenter|string|Centro de coste|
|accountId|int|Identificador de cuenta|
|accountName|cadena |Nombre de cuenta|
|accountOwnerId|cadena|Identificador de propietario de cuenta|
|departmentId|int|Identificador de departamento|
|departmentName|cadena|Nombre de departamento|
|publisherName|cadena|Nombre de publicador|
|planName|cadena|Nombre de plan|
|consumedQuantity|Decimal|Cantidad consumida durante este período|
|resourceRate|Decimal|Precio por unidad del medidor|
|extendedCost|Decimal|Gasto estimado según la cantidad consumida y el costo total|
<br/>
## <a name="see-also"></a>Otras referencias

* [API de períodos de facturación](billing-enterprise-api-billing-periods.md)

* [API de detalles de uso](billing-enterprise-api-usage-detail.md) 

* [API de saldo y resumen](billing-enterprise-api-balance-summary.md)

* [API de hoja de precios](billing-enterprise-api-pricesheet.md)
