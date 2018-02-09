---
title: "API de facturación de Azure Enterprise | Microsoft Docs"
description: "Obtenga información acerca de las API de informes que permiten a los clientes de Azure Enterprise extraer datos de consumo mediante programación."
services: 
documentationcenter: 
author: anandedwin
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
ms.openlocfilehash: f7a480c77c93035e655606433aea2547a1c105cc
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/01/2018
---
# <a name="overview-of-reporting-apis-for-enterprise-customers"></a>Información general de API de informes para clientes de Enterprise
Las API de informes permiten a los clientes de Azure Enterprise extraer datos de facturación y consumo mediante programación en las herramientas de análisis de datos preferidas. 

## <a name="enabling-data-access-to-the-api"></a>Habilitación del acceso de datos a la API
* **Generar o recuperar la clave de API**: inicie sesión en el portal de Enterprise y siga el tutorial en la sección de API de informes de la ayuda. La primera sección de este artículo de ayuda explica cómo generar o recuperar la clave de API para la inscripción especificada.
* **Pasar claves en la API**: la clave de API tiene que pasarse para cada llamada para la autenticación y autorización. La siguiente propiedad tiene que ser los encabezados HTTP.

|Clave de encabezado de solicitud | Valor|
|-|-|
|Autorización| Especifique el valor con este formato: **bearer {API_KEY}** <br/> Ejemplo: bearer eyr....09|

## <a name="consumption-apis"></a>API de consumo
Hay disponible un punto de conexión de Swagger [aquí](https://consumption.azure.com/swagger/ui/index) para la API descrita a continuación que debe habilitar una introspección sencilla de la API y la capacidad de generar SDK de cliente con [AutoRest](https://github.com/Azure/AutoRest) o [Swagger CodeGen](http://swagger.io/swagger-codegen/). Los datos a partir del 1 de mayo de 2014 están disponibles a través de esta API. 

* **Saldos y resumen**: la [API de saldos y resumen](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) ofrece un resumen mensual de información sobre saldos, nuevas compras, gastos de servicios en Azure Marketplace, ajustes y gastos de uso por encima del límite.

* **Detalles de uso**: la [API de detalles de uso](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail) ofrece un desglose diario de cantidades consumidas y gastos estimados por una inscripción. El resultado también incluye información sobre instancias, medidores y departamentos. La API se puede consultar por período de facturación o por una fecha de inicio y finalización especificada. 

* **Gasto en la tienda Marketplace**: la [API de gasto en la tienda Marketplace](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) devuelve el desglose de los gastos de Marketplace basado en el uso por día para el período de facturación o las fechas de inicio y finalización especificadas (no se incluyen las cuotas puntuales).

* **Hoja de precios**: la [API de hoja de precios](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) proporciona el tipo aplicable de cada medidor para la inscripción y el período de facturación determinados. 

## <a name="data-freshness"></a>Actualización de datos
Como respuesta a todas las API anteriores, se devuelven etiquetas ETag. Un cambio en una etiqueta ETag indica que se han actualizado los datos.  En las sucesivas llamadas a la misma API con los mismos parámetros, pase la etiqueta ETag capturada con la clave "If-None-Match" en el encabezado de solicitud HTTP. El código de estado de respuesta sería "NotModified" si los datos no se han actualizado más y no se devuelve ningún dato. La API devolverá el conjunto de datos completo para el periodo necesario cada vez que haya un cambio de etiqueta ETag.

## <a name="helper-apis"></a>API de ayuda
 **Enumerar períodos de facturación**: la [API de períodos de facturación](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) devuelve una lista de períodos de facturación que tienen datos de consumo para la inscripción especificada en orden cronológico inverso. Cada período contiene una propiedad que señala a la ruta de la API para los cuatro conjuntos de datos: BalanceSummary, UsageDetails, Marketplace Charges y Price Sheet.


## <a name="api-response-codes"></a>Códigos de respuesta de la API  
|Código de estado de respuesta|Message|DESCRIPCIÓN|
|-|-|-|
|200| OK|Sin errores|
|401| No autorizado| Clave de API no encontrada, no válida, expirada, etc.|
|404| No disponible| Punto de conexión de informe no encontrado|
|400| Bad Request| Parámetros no válidos: intervalos de fechas, números EA, etc.|
|500| Error de servidor| Error inesperado al procesar la solicitud| 









