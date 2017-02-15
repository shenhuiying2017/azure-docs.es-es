---
title: Novedades de Machine Learning | Microsoft Docs
description: "Nuevas características disponibles en Aprendizaje automático de Azure."
services: machine-learning
documentationcenter: 
author: vDonGlover
manager: raymondl
editor: 
ms.assetid: ddc716ed-2615-4806-bf27-6c9a5662a7f2
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/05/2016
ms.author: v-donglo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 528ae3db20815cf7409cf0e2c7eb5c4473ba16ce


---
# <a name="whats-new-in-azure-machine-learning"></a>Novedades de Aprendizaje automático de Azure
### <a name="the-august-2016-release-of-microsoft-azure-machine-learning-updates-provide-the-following-features"></a>El lanzamiento en agosto de 2016 de las actualizaciones de Microsoft Azure Machine Learning ofrecen las siguientes características:
* Ahora, los servicios web clásicos se pueden administrar en el nuevo portal [Servicios web Microsoft Azure Machine Learning](https://services.azureml.net/) donde pueden administrarse todos los aspectos de los servicios web.    
  * Dicho portal ofrece las [estadísticas de uso](machine-learning-manage-new-webservice.md) de los servicios web.
  * Simplifica las pruebas de llamadas de solicitud remota a Azure Machine Learning con datos de ejemplo.
  * Ofrece una nueva página de prueba de servicio de ejecución por lotes con datos de ejemplos e historial de envío de trabajos.
  * Facilita la administración de los puntos de conexión.

### <a name="the-july-2016-release-of-microsoft-azure-machine-learning-updates-provide-the-following-features"></a>El lanzamiento en julio de 2016 de las actualizaciones de Microsoft Azure Machine Learning ofrecen las siguientes características:
* Los servicios web ahora se administran como recursos de Azure administrados a través de interfaces de [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) (ARM), con lo que se aportan las siguientes mejoras:
  * Nuevas [API de REST](https://msdn.microsoft.com/library/azure/Dn950030.aspx) para implementar y administrar los servicios web basados en Resource Manager
  * Nuevo portal [Servicios web Microsoft Azure Machine Learning](https://services.azureml.net/), donde pueden administrarse todos los aspectos de los servicios web
* Incorpora un nuevo modelo de implementación de servicios web en varias regiones y mediante suscripción que utiliza API basadas en Azure Resource Manager que emplean el proveedor de recursos de esta solución en los servicios web.
* Introduce nuevos [planes de precios](https://azure.microsoft.com/pricing/details/machine-learning/) y funcionalidades de administración de planes que utilizan el nuevo RP de Azure Resource Manager para las tareas de facturación.
  * Ahora puede [implementar el servicio web en varias regiones](machine-learning-how-to-deploy-to-multiple-regions.md) sin necesidad de crear una suscripción en cada región.
* Proporciona las [estadísticas de uso](machine-learning-manage-new-webservice.md)de los servicios web.
* Simplifica las pruebas de llamadas de solicitud remota a Azure Machine Learning mediante datos de ejemplo.
* Ofrece una nueva página de prueba de servicio de ejecución por lotes con datos de ejemplos e historial de envío de trabajos.

Además, Machine Learning Studio se ha actualizado para poder implementar según el modelo de servicios web nuevo o seguir implementando con el modelo de servicios web clásico. 




<!--HONumber=Nov16_HO3-->


