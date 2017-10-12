---
title: "API de facturación de Azure | Microsoft Docs"
description: "Obtenga información sobre las API de RateCard y de uso de facturación de Azure que se usan para proporcionar información sobre el consumo de recursos y tendencias de Azure."
services: 
documentationcenter: 
author: BryanLa
manager: tonguyen
editor: 
tags: billing
ms.assetid: 3e817b43-0696-400c-a02e-47b7817f9b77
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 10/9/2017
ms.author: mobandyo;bryanla
ms.openlocfilehash: 26217d6f4e14166a89fbb561cb12d0af78ae6f4d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-billing-apis-to-programmatically-get-insight-into-your-azure-usage"></a>Use las API de facturación de Azure para obtener información mediante programación sobre cómo usa Azure
Use las API de facturación de Azure para extraer datos de uso y de recursos e incorporarlos en las herramientas de análisis de datos de su preferencia. Las API de RateCard y de uso de recursos de Azure pueden ayudarlo a predecir y administrar los costos de forma precisa. Las API se implementan como proveedor de recursos, como parte de la familia de API expuesta por Azure Resource Manager.  

## <a name="azure-invoice-download-api-preview"></a>API de descarga de facturas de Azure (vista previa)
Una vez [completada la suscripción](billing-manage-access.md#opt-in), descargue facturas con la versión de vista previa de [API de facturas](/rest/api/billing). Las características incluyen:

* **Control de acceso basado en rol de Azure**: configure las directivas de acceso en [Azure Portal](https://portal.azure.com) o mediante [cmdlets de Azure PowerShell](/powershell/azure/overview) para especificar qué usuarios o aplicaciones pueden obtener acceso a los datos de uso de la suscripción. Los autores de llamadas deben utilizar tokens de Azure Active Directory estándar para la autenticación. Agregue el autor de la llamada al rol Lector de facturación, Lector, Propietario o Colaborador para que obtenga acceso a los datos de uso de una suscripción de Azure específica.
* **Filtrado de fechas**: use el parámetro `$filter` para obtener todas las facturas en orden cronológico inverso por la fecha de finalización del periodo de facturación. 

> [!NOTE]
> Esta característica se encuentra en su primera versión de vista previa y puede estar sujeta a cambios no compatibles con versiones anteriores. De momento no está disponible para determinadas ofertas de suscripción (EA, CSP, AIO no admitidas) y Azure Germany.

## <a name="azure-resource-usage-api-preview"></a>API de uso de recursos de Azure (vista previa)
Use la [API de uso de recursos](https://msdn.microsoft.com/library/azure/mt219003) de Azure para obtener los datos estimados de consumo de Azure. La API incluye:

* **Control de acceso basado en rol de Azure**: configure las directivas de acceso en [Azure Portal](https://portal.azure.com) o mediante [cmdlets de Azure PowerShell](/powershell/azure/overview) para especificar qué usuarios o aplicaciones pueden obtener acceso a los datos de uso de la suscripción. Los autores de llamadas deben utilizar tokens de Azure Active Directory estándar para la autenticación. Agregue el autor de la llamada al rol Lector de facturación, Lector, Propietario o Colaborador para que obtenga acceso a los datos de uso de una suscripción de Azure específica.
* **Agregaciones cada hora o diarias** : los autores de llamadas pueden especificar si desean sus datos de uso de Azure en depósitos cada hora o diarios. El valor predeterminado es diario.
* **Metadatos de instancia (incluye etiquetas de recursos)**: obtenga detalles de nivel de instancia, como el URI de recurso completo (/subscriptions/{id-suscripción}/..), la información del grupo de recursos y las etiquetas de recursos. Estos metadatos lo ayudarán de forma determinista y mediante programación a asignar el uso mediante las etiquetas, para casos de uso como cargos cruzados.
* **Metadatos de recursos**: detalles de recursos, como el nombre del medidor, la categoría del medidor, la subcategoría del medidor, la unidad y la región permiten que el autor de la llamada comprenda mejor lo que se ha consumido. También estamos trabajando para alinear la terminología de metadatos de recursos en todo Azure Portal, CSV de uso de Azure, CSV de facturación de EA y otras experiencias orientadas al público, para permitirle que pueda poner en correlación los datos en todas las experiencias.
* **Uso para diferentes tipos de ofertas**: los datos de uso están disponibles para tipos de ofertas como el pago por uso, MSDN, compromiso monetario, crédito monetario y EA, excepto [CSP](https://docs.microsoft.com/azure/cloud-solution-provider/billing/azure-csp-invoice#retrieve-usage-data-for-a-specific-subscription).

## <a name="azure-resource-ratecard-api-preview"></a>API de RateCard de recursos de Azure (vista previa)
Use la [API de RateCard de recursos de Azure](https://msdn.microsoft.com/library/azure/mt219005) para obtener la lista de recursos disponibles de Azure y una información de precios estimada para cada uno. La API incluye:

* **Control de acceso basado en rol de Azure**: configure las directivas de acceso en [Azure Portal](https://portal.azure.com) o mediante [cmdlets de Azure PowerShell](/powershell/azure/overview) para especificar qué usuarios o aplicaciones pueden obtener acceso a los datos de RateCard. Los autores de llamadas deben utilizar tokens de Azure Active Directory estándar para la autenticación. Agregue el autor de la llamada al rol Lector, Propietario o Colaborador para que obtenga acceso a los datos de uso de una suscripción de Azure determinada.
* **Compatibilidad con ofertas de pago por uso, MSDN, compromiso monetario y crédito monetario (no compatible con EA y [CSP](https://docs.microsoft.com/azure/cloud-solution-provider/billing/azure-csp-pricelist#get-prices-by-using-the-azure-rate-card))**: esta API proporciona información de tarifas de nivel de oferta de Azure.  El autor de llamada de esta API debe pasar la información de oferta para obtener detalles y tarifas de recursos Actualmente no podemos proporcionar tarifas de EA porque las ofertas de EA tienen tarifas personalizadas por inscripción. 

## <a name="scenarios"></a>Escenarios
Éstos son algunos de los escenarios posibles con la combinación de las API de uso y de RateCard:

* **Gasto en Azure durante el mes**: use la combinación de las API de uso y de RateCard para obtener información más detallada sobre el gasto por usar la nube durante el mes. Puede analizar los depósitos por hora y diarios de las estimaciones de usos y cargos.
* **Configurar alertas**: use las API de uso y de RateCard para obtener una estimación del consumo de la nube y los cargos, y configure alertas basadas en recursos o basadas en importes monetarios.
* **Predicción de facturas**: obtenga el gasto en la nube y el consumo estimados, y aplique algoritmos de aprendizaje automático para poder predecir cuál sería la factura al final del ciclo de facturación.
* **Análisis de costos previos al consumo**: use la API de RateCard para predecir cuál sería el importe de su factura para el uso esperado cuando mueva las cargas de trabajo a Azure. Si tiene cargas de trabajo existentes en otras nubes o nubes privadas, también puede asignar su uso con las tarifas de Azure para obtener una mejor estimación del gasto de Azure. Esta estimación le permite tener la capacidad de dinamizar una oferta y de comparar y contrastar los distintos tipos de ofertas que van más allá del pago por uso, como son el compromiso monetario y el crédito monetario. La API también le ofrece la posibilidad de ver las diferencias de costos por región y le permite realizar un análisis de hipótesis para ayudarlo a tomar decisiones en cuanto a la implementación.
* **Análisis de hipótesis** -
  
  * Puede determinar si es más rentable ejecutar las cargas de trabajo en otra región o en otra configuración del recurso de Azure. Los costos de los recursos de Azure pueden variar en función de la región de Azure que usa.
  * También puede determinar si otro tipo de oferta de Azure ofrece una mejor tarifa en un recurso de Azure.
  
## <a name="partner-solutions"></a>Soluciones de socios
[Integración de Cloud Cruiser y de las API de facturación de Microsoft Azure](billing-usage-rate-card-partner-solution-cloudcruiser.md) describe cómo [Cloud Cruiser Express para el paquete de Azure](http://www.cloudcruiser.com/partners/microsoft/) funciona directamente desde el portal de Windows Azure Pack (WAP). Puede administrar sin ningún problema los aspectos operativos y financieros de la nube pública hospedada o privada de Microsoft Azure desde una interfaz de usuario única.   

## <a name="next-steps"></a>Pasos siguientes
* Vea los ejemplos de código en GitHub:
  * [Ejemplo de código de API de facturas](https://go.microsoft.com/fwlink/?linkid=845124)

  * [Ejemplo de código de API de uso](https://github.com/Azure-Samples/billing-dotnet-usage-api)

  * [Ejemplo de código de API de RateCard](https://github.com/Azure-Samples/billing-dotnet-ratecard-api)

* Para más información sobre Azure Resource Manager, vea [Información general de Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). 

* Para más información sobre el conjunto de herramientas necesarias para ayudarlo a comprender el gasto en la nube, consulte el artículo de Gartner [Guía de mercado para las herramientas de administración financiera de IT (ITFM)](http://www.gartner.com/technology/reprints.do?id=1-212F7AL&ct=140909&st=sb).

