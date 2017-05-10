---
title: "API de facturación de Azure Enterprise - Detalles de uso| Microsoft Docs"
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
ms.translationtype: Human Translation
ms.sourcegitcommit: f6006d5e83ad74f386ca23fe52879bfbc9394c0f
ms.openlocfilehash: d6fe7a020100e3372c1fa0e244838d4c40839dbf
ms.contentlocale: es-es
ms.lasthandoff: 05/03/2017


---
# <a name="reporting-apis-for-enterprise-customers---usage-details-preview"></a>API de informes para clientes de Enterprise - Detalles de uso (versión preliminar)

La API de detalles de uso ofrece un desglose diario de cantidades consumidas y gastos estimados por una inscripción. El resultado también incluye información sobre instancias, medidores y departamentos. La API se puede consultar por período de facturación o por una fecha de inicio y finalización especificada. 
## <a name="consumption-apis"></a>API de consumo


##<a name="request"></a>Solicitud 
Las propiedades de encabezado comunes que tienen que agregarse se especifican [aquí](billing-enterprise-api.md). Si no se especifica un período de facturación, se devuelven datos para el período de facturación actual. Los intervalos de tiempo personalizados pueden especificarse con los parámetros de fecha de inicio y finalización que están en formato aaaa-MM-dd. El intervalo de tiempo máximo compatible es de 36 meses.  

|Método | URI de solicitud|
|-|-|
|GET|https://consumption.azure.com/v1/enrollments/{enrollmentNumber}/usagedetails 
|GET|https://consumption.azure.com/v1/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/usagedetails|
|GET|https://consumption.azure.com/v1/enrollments/{enrollmentNumber}/usagedetailsbycustomdate?startTime=2017-01-01&endTime=2017-01-10|

## <a name="response"></a>Response

> Debido al volumen potencialmente grande de datos, se pagina el conjunto de datos. La propiedad nextLink, si está presente, especifica el vínculo de la página siguiente de datos. Si el vínculo está vacío, indica que es la última página. 
<br/>

    {
        "id": "string",
        "data": [
            {                        
            "accountId": 0,
            "productId": 0,
            "resourceLocationId": 0,
            "consumedServiceId": 0,
            "departmentId": 0,
            "accountOwnerEmail": "string",
            "accountName": "string",
            "serviceAdministratorId": "string",
            "subscriptionId": 0,
            "subscriptionGuid": "string",
            "subscriptionName": "string",
            "date": "2017-04-27T23:01:43.799Z",
            "product": "string",
            "meterId": "string",
            "meterCategory": "string",
            "meterSubCategory": "string",
            "meterRegion": "string",
            "meterName": "string",
            "consumedQuantity": 0,
            "resourceRate": 0,
            "extendedCost": 0,
            "resourceLocation": "string",
            "consumedService": "string",
            "instanceId": "string",
            "serviceInfo1": "string",
            "serviceInfo2": "string",
            "additionalInfo": "string",
            "tags": "string",
            "storeServiceIdentifier": "string",
            "departmentName": "string",
            "costCenter": "string",
            "unitOfMeasure": "string",
            "resourceGroup": "string"
            }
        ]
        "nextLink": "string"
    }

<br/>

**Definiciones de propiedad de respuesta**

|Nombre de propiedad| Tipo| Descripción
|-|-|-|
|id| cadena| El identificador único de la llamada API. |
|data| Matriz JSON| La matriz de detalles de uso diario para cada instancia/medidor|
|nextLink| cadena| Cuando hay más páginas de datos, los puntos de nextLink de la dirección URL para devolver la siguiente página de datos |

## <a name="see-also"></a>Otras referencias
* [API de períodos de facturación](billing-enterprise-api-billing-periods.md)

* [API de saldo y resumen](billing-enterprise-api-balance-summary.md)

* [API de gastos en la tienda Marketplace](billing-enterprise-api-marketplace-storecharge.md) 

* [API de hoja de precios](billing-enterprise-api-pricesheet.md)
