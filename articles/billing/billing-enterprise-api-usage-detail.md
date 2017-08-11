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
ms.translationtype: HT
ms.sourcegitcommit: 6e76ac40e9da2754de1d1aa50af3cd4e04c067fe
ms.openlocfilehash: 0f751063510707d53ac76a287aa420818a31b04b
ms.contentlocale: es-es
ms.lasthandoff: 07/31/2017

---
# <a name="reporting-apis-for-enterprise-customers---usage-details"></a>API de informes para clientes de Enterprise: detalles de uso

La API de detalles de uso ofrece un desglose diario de cantidades consumidas y gastos estimados por una inscripción. El resultado también incluye información sobre instancias, medidores y departamentos. La API se puede consultar por período de facturación o por una fecha de inicio y finalización especificada. 
## <a name="consumption-apis"></a>API de consumo


##<a name="request"></a>Solicitud 
Las propiedades de encabezado comunes que tienen que agregarse se especifican [aquí](billing-enterprise-api.md). Si no se especifica un período de facturación, se devuelven datos para el período de facturación actual. Los intervalos de tiempo personalizados pueden especificarse con los parámetros de fecha de inicio y finalización que están en formato aaaa-MM-dd. El intervalo de tiempo máximo compatible es de 36 meses.  

|Método | URI de solicitud|
|-|-|
|GET|https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/usagedetails 
|GET|https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/usagedetails|
|GET|https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/usagedetailsbycustomdate?startTime=2017-01-01&endTime=2017-01-10|

> [!Note]
> Para usar la versión preliminar de la API, reemplace v2 por v1 en la dirección URL anterior.
>

## <a name="response"></a>Respuesta

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
            "Cost": 0,
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
        ],
        "nextLink": "string"
    }

<br/>
**Definiciones de propiedad de respuesta**

|Nombre de propiedad| Tipo| Descripción
|-|-|-|
|id| cadena| El identificador único de la llamada API. |
|data| Matriz JSON| La matriz de detalles de uso diario para cada instancia/medidor.|
|nextLink| cadena| Cuando hay más páginas de datos, los puntos de nextLink de la dirección URL para devolver la siguiente página de datos. |
|accountId| int| Campo obsoleto. Presente por compatibilidad con versiones anteriores. |
|productId| int| Campo obsoleto. Presente por compatibilidad con versiones anteriores. |
|resourceLocationId| int| Campo obsoleto. Presente por compatibilidad con versiones anteriores. |
|consumedServiceID| int| Campo obsoleto. Presente por compatibilidad con versiones anteriores. |
|departmentId| int| Campo obsoleto. Presente por compatibilidad con versiones anteriores. |
|accountOwnerEmail| cadena| Cuenta de correo electrónico del propietario de la cuenta. |
|accountName| cadena| Nombre de cliente especificado de la cuenta. |
|serviceAdministratorId| cadena| Dirección de correo electrónico del administrador de servicios. |
|subscriptionId| int| Campo obsoleto. Presente por compatibilidad con versiones anteriores. |
|subscriptionGuid| cadena| Identificador único global para la suscripción. |
|subscriptionName| cadena| Nombre de la suscripción. |
|fecha| cadena| Fecha en la que se produjo el consumo. |
|product| cadena| Detalles adicionales sobre el medidor. Ejemplo: A1 (VM) Windows - Este de Asia Pacífico|
|meterId| cadena| Identificador para el medidor que emitió la utilización. |
|meterCategory| cadena| Servicio de la plataforma de Azure que se usó. |
|meterSubCategory| cadena| Define el tipo de servicio de Azure que puede afectar a la tarifa. Ejemplo : VM A1 (no Windows)|
|meterRegion| cadena| Identifica la ubicación del centro de datos para ciertos servicios cuyos precios se establecen según la ubicación del centro de datos. |
|meterName| cadena| Nombre del medidor. |
|consumedQuantity| double| Cantidad del medidor consumida. |
|resourceRate| double| Tipo aplicable por unidad facturable. |
|cost| double| El cargo que se ha aplicado para el medidor. |
|resourceLocation| cadena| Identifica el centro de datos donde se está ejecutando el medidor. |
|consumedService| cadena| Servicio de la plataforma de Azure que se usó. |
|instanceId| cadena| Este identificador es el nombre del recurso o el identificador de recurso completo. Para más información, consulte [Azure Resource Manager API](https://docs.microsoft.com/rest/api/resources/resources) (API de Azure Resource Manager). |
|serviceInfo1| cadena| Metadatos de servicio de Azure interno. |
|serviceInfo2| cadena| Por ejemplo, un tipo de imagen de una máquina virtual y un nombre de ISP para ExpressRoute. |
|additionalInfo| cadena| Metadatos específicos del servicio. Por ejemplo, un tipo de imagen de una máquina virtual. |
|etiquetas| cadena| Etiquetas agregadas del cliente. Para más información, consulte [Organize your Azure resources with tags](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-using-tags) (Organización de los recursos de Azure con etiquetas). |
|storeServiceIdentifier| cadena| Esta columna no se utiliza. Presente por compatibilidad con versiones anteriores. |
|departmentName| cadena| Nombre del departamento. |
|costCenter| cadena| Centro de costo al que está asociada la utilización. |
|unitOfMeasure| cadena| Identifica la unidad en que se cobra el servicio. Por ejemplo, GB, horas, 10 000 s. |
|resourceGroup| cadena| Grupo de recursos en el que se ejecuta el medidor implementado. Para más información, consulte [Información general de Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). |
<br/>
## <a name="see-also"></a>Otras referencias

* [API de períodos de facturación](billing-enterprise-api-billing-periods.md)

* [API de saldo y resumen](billing-enterprise-api-balance-summary.md)

* [API de gastos en la tienda Marketplace](billing-enterprise-api-marketplace-storecharge.md) 

* [API de hoja de precios](billing-enterprise-api-pricesheet.md)

