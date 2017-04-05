---
title: "API de facturación de Azure | Microsoft Docs"
description: "Obtenga información sobre las API de RateCard y de uso de facturación de Azure que se usan para proporcionar información sobre el consumo de recursos y tendencias de Azure."
services: 
documentationcenter: 
author: BryanLa
manager: ruchic
editor: 
tags: billing
ms.assetid: 3e817b43-0696-400c-a02e-47b7817f9b77
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 03/25/2017
ms.author: mobandyo;bryanla
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 869734ad4eb2d7a408cc0d33d1492b50b1b8dc0f
ms.lasthandoff: 03/29/2017


---
# <a name="use-azure-billing-apis-to-programmatically-get-insight-into-your-azure-usage"></a>Use las API de facturación de Azure para obtener información mediante programación sobre cómo usa Azure
Use las API de facturación de Azure para extraer datos de uso y de recursos e incorporarlos en las herramientas de análisis de datos de su preferencia. Las API de RateCard y de uso de recursos de Azure pueden ayudarlo a predecir y administrar los costos de forma precisa. Las API se implementan como proveedor de recursos, como parte de la familia de API expuesta por Azure Resource Manager.  

## <a name="azure-resource-usage-api-preview"></a>API de uso de recursos de Azure (vista previa)
Use la API de uso de recursos de Azure para obtener los datos estimados de su consumo de Azure. La API incluye:

* **Control de acceso basado en rol de Azure**: configure las directivas de acceso en [Azure Portal](https://portal.azure.com) o mediante [cmdlets de Azure PowerShell](/powershell/azureps-cmdlets-docs) para especificar qué usuarios o aplicaciones pueden obtener acceso a los datos de uso de la suscripción. Los autores de llamadas deben utilizar tokens de Azure Active Directory estándar para la autenticación. Agregue el autor de la llamada al rol Lector, Propietario o Colaborador para que obtenga acceso a los datos de uso de una suscripción de Azure específica.
* **Agregaciones cada hora o diarias** : los autores de llamadas pueden especificar si desean sus datos de uso de Azure en depósitos cada hora o diarios. El valor predeterminado es diario.
* **Metadatos de instancia (incluye etiquetas de recursos)**: obtenga detalles de nivel de instancia, como el URI de recurso completo (/subscriptions/{id-suscripción}/..), la información del grupo de recursos y las etiquetas de recursos. Estos metadatos lo ayudarán de forma determinista y mediante programación a asignar el uso mediante las etiquetas, para casos de uso como cargos cruzados.
* **Metadatos de recursos**: detalles de recursos, como el nombre del medidor, la categoría del medidor, la subcategoría del medidor, la unidad y la región permiten que el autor de la llamada comprenda mejor lo que se ha consumido. También estamos trabajando para alinear la terminología de metadatos de recursos en todo Azure Portal, CSV de uso de Azure, CSV de facturación de EA y otras experiencias orientadas al público, para permitirle que pueda poner en correlación los datos en todas las experiencias.
* **Uso para todos los tipos de ofertas**: los datos de uso están disponibles para todos los tipos de ofertas, incluidos el pago por uso, MSDN, compromiso monetario, crédito monetario y EA.

## <a name="azure-resource-ratecard-api-preview"></a>API de RateCard de recursos de Azure (vista previa)
Use la API de RateCard de recursos de Azure para obtener la lista de recursos disponibles de Azure y una información de precios estimada para cada uno. La API incluye:

* **Control de acceso basado en rol de Azure**: configure las directivas de acceso en [Azure Portal](https://portal.azure.com) o mediante [cmdlets de Azure PowerShell](/powershell/azureps-cmdlets-docs) para especificar qué usuarios o aplicaciones pueden obtener acceso a los datos de RateCard. Los autores de llamadas deben utilizar tokens de Azure Active Directory estándar para la autenticación. Agregue el autor de la llamada al rol Lector, Propietario o Colaborador para que obtenga acceso a los datos de uso de una suscripción de Azure determinada.
* **Compatibilidad con ofertas de pago por uso, MSDN, compromiso monetario y crédito monetario (no compatible con EA)**: esta API proporciona información de tarifas de nivel de oferta de Azure.  El autor de llamada de esta API debe pasar la información de oferta para obtener detalles y tarifas de recursos Actualmente no podemos proporcionar tarifas de EA porque las ofertas de EA tienen tarifas personalizadas por inscripción. 

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
[API de uso de Microsoft Azure y de RateCard que permiten a Cloudyn proporcionar ITFM a clientes](billing-usage-rate-card-partner-solution-cloudyn.md) describe la experiencia de integración ofrecida por el socio de API de facturación de Azure, [Cloudyn](https://www.cloudyn.com/microsoft-azure/). En este artículo se habla de sus experiencias e incluye un vídeo que muestra cómo puede usar Cloudyn y las API de facturación de Azure para obtener información a partir de los datos de consumo de Azure.

[Integración de Cloud Cruiser y de las API de facturación de Microsoft Azure](billing-usage-rate-card-partner-solution-cloudcruiser.md) describe cómo [Cloud Cruiser Express para el paquete de Azure](http://www.cloudcruiser.com/partners/microsoft/) funciona directamente desde el portal de Windows Azure Pack (WAP). Puede administrar sin ningún problema los aspectos operativos y financieros de la nube pública hospedada o privada de Microsoft Azure desde una interfaz de usuario única.   

## <a name="next-steps"></a>Pasos siguientes
* Revise la [referencia de las API de REST de facturación en Azure](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) para más detalles sobre ambas API.
* Si quiere profundizar en el código de ejemplo, consulte nuestros ejemplos de código de la API de facturación de Microsoft Azure en [Ejemplos de código de Azure](https://azure.microsoft.com/documentation/samples/?term=billing).

## <a name="learn-more"></a>Más información
* Consulte la [información general de Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).
* Para más información sobre el conjunto de herramientas necesarias para ayudarlo a comprender el gasto en la nube, consulte el artículo de Gartner [Guía de mercado para las herramientas de administración financiera de IT (ITFM)](http://www.gartner.com/technology/reprints.do?id=1-212F7AL&ct=140909&st=sb).


