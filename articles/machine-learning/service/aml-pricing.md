---
title: Precios y facturación para servicios de Azure Machine Learning
description: Este artículo contiene las preguntas más frecuentes sobre precios y facturación para características de la versión preliminar de Azure Machine Learning.
services: machine-learning
author: j-martens
ms.author: jmartens
manager: cgronlund
ms.reviewer: mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 03/30/2018
ms.openlocfilehash: 5e057f3fe4a4ff06e0acac29b3dcd11fa901fc40
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/19/2018
---
# <a name="pricing-and-billing-for-azure-machine-learning-services"></a>Precios y facturación para servicios de Azure Machine Learning

Para más información o para ver una factura de ejemplo, visite la [página de precios de Azure](https://azure.microsoft.com/pricing/details/machine-learning-services/).  

Estas son algunas de las preguntas más frecuentes sobre facturación y precios.

## <a name="can-i-use-azure-machine-learning-for-free-during-preview"></a>¿Se puede utilizar Azure Machine Learning de forma gratuita durante la versión preliminar?    

La aplicación Azure Machine Learning Workbench está disponible de forma gratuita para los suscriptores de Azure.

El servicio Experimentación y Administración de modelos ofrecen niveles gratuitos, además de niveles de pago, disponibles con un descuento durante la versión preliminar pública.

## <a name="am-i-charged-based-on-how-many-experiments-i-run"></a>¿Los cargos van en función del número de experimentos que se ejecuten?

No, el servicio Experimentación permite tantos experimentos como necesite. Los cargos se realizan únicamente en función del número de usuarios. Los recursos de proceso de Experimentación se cobran aparte.  Le recomendamos realizar varios experimentos para encontrar el mejor modelo para su solución. 

## <a name="am-i-charged-based-on-how-many-times-my-web-services-is-called"></a>¿Se cobrará en función del número de veces que se llame a los servicios web?

Nº Se puede llamar a los servicios web con la frecuencia que sea necesaria, sin que ello afecte a la facturación de Administración de modelos. Usted tiene el control total para escalar sus implementaciones según la necesidad de sus aplicaciones.

## <a name="how-can-i-scale-the-units-i-purchase-in-the-azure-machine-learning-model-management"></a>¿Cómo se pueden escalar las unidades adquiridas en Administración de modelos de Azure Machine Learning?

Puede aumentar o reducir el número de unidades usando la CLI o Azure Portal. 

## <a name="what-does-a-bill-look-like"></a>¿Qué aspecto tiene una factura?

Las facturas se crean a diario. En términos de facturación, un día comienza a medianoche (UTC). Las facturas se generan mensualmente. Se incurre en cargos independientes por cualquier servicio de Azure consumido junto con Azure Machine Learning. Los cargos pueden incluir, entre otras cosas: 
- Cargos de proceso
- HDInsight
- Azure Container Service
- Azure Container Registry 
- Azure Blob Storage
- Application Insights
- Azure Key Vault
- Visual Studio Team Services
- Centro de eventos de Azure
- Azure Stream Analytics. Para más información o para ver una factura de ejemplo, visite la [página de precios de Azure](https://azure.microsoft.com/pricing/details/machine-learning-services/). 
