---
title: Novedades de Azure Machine Learning | Microsoft Docs
description: "Nuevas características disponibles en Azure Machine Learning."
services: machine-learning
documentationcenter: 
author: garyericson
manager: raymondl
editor: 
ms.assetid: ddc716ed-2615-4806-bf27-6c9a5662a7f2
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2017
ms.author: v-donglo
ms.openlocfilehash: 82ec23f4edba88ec75c2073e24cad24b66323d72
ms.sourcegitcommit: 42ee5ea09d9684ed7a71e7974ceb141d525361c9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/09/2017
---
# <a name="whats-new-in-azure-machine-learning"></a>Novedades de Azure Machine Learning

### <a name="the-march-2017-release-of-microsoft-azure-machine-learning-updates-provides-the-following-feature"></a>El lanzamiento en marzo de 2017 de las actualizaciones de Microsoft Azure Machine Learning incluye la siguiente característica:



* Función dedicada para trabajos de BES de Azure Machine Learning

    El procesamiento de grupo de lote de Machine Learning emplea el servicio [Azure Batch](../../batch/batch-technical-overview.md) para proporcionar una escala administrada por el cliente para el servicio de ejecución de lotes de Azure Machine Learning. El procesamiento de grupo de lote le permite crear grupos de Azure Batch en los que puede enviar trabajos por lotes y hacer que se ejecuten de manera predecible.

    Para obtener más información, consulte [Servicio de Azure Batch para trabajos de Machine Learning](dedicated-capacity-for-bes-jobs.md).


### <a name="the-august-2016-release-of-microsoft-azure-machine-learning-updates-provide-the-following-features"></a>El lanzamiento en agosto de 2016 de las actualizaciones de Microsoft Azure Machine Learning ofrecen las siguientes características:
* Ahora, los servicios web clásicos se pueden administrar en el nuevo portal [Servicios web Microsoft Azure Machine Learning](https://services.azureml.net/) donde pueden administrarse todos los aspectos de los servicios web.    
  * Dicho portal ofrece las [estadísticas de uso](manage-new-webservice.md) de los servicios web.
  * Simplifica las pruebas de llamadas de solicitud remota a Azure Machine Learning con datos de ejemplo.
  * Ofrece una nueva página de prueba de servicio de ejecución por lotes con datos de ejemplos e historial de envío de trabajos.
  * Facilita la administración de los puntos de conexión.

### <a name="the-july-2016-release-of-microsoft-azure-machine-learning-updates-provide-the-following-features"></a>El lanzamiento en julio de 2016 de las actualizaciones de Microsoft Azure Machine Learning ofrecen las siguientes características:
* Los servicios web ahora se administran como recursos de Azure administrados a través de interfaces de [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) (ARM), con lo que se aportan las siguientes mejoras:
  * Nuevas [API de REST](https://msdn.microsoft.com/library/azure/Dn950030.aspx) para implementar y administrar los servicios web basados en Resource Manager
  * Nuevo portal [Servicios web Microsoft Azure Machine Learning](https://services.azureml.net/), donde pueden administrarse todos los aspectos de los servicios web
* Incorpora un nuevo modelo de implementación de servicios web en varias regiones y mediante suscripción que utiliza API basadas en Azure Resource Manager que emplean el proveedor de recursos de esta solución en los servicios web.
* Introduce nuevos [planes de precios](https://azure.microsoft.com/pricing/details/machine-learning/) y funcionalidades de administración de planes que utilizan el nuevo RP de Azure Resource Manager para las tareas de facturación.
  * Ahora puede [implementar el servicio web en varias regiones](how-to-deploy-to-multiple-regions.md) sin necesidad de crear una suscripción en cada región.
* Proporciona las [estadísticas de uso](manage-new-webservice.md)de los servicios web.
* Simplifica las pruebas de llamadas de solicitud remota a Azure Machine Learning con datos de ejemplo.
* Ofrece una nueva página de prueba de servicio de ejecución por lotes con datos de ejemplos e historial de envío de trabajos.

Además, Machine Learning Studio se ha actualizado para poder implementar según el modelo de servicios web nuevo o seguir implementando con el modelo de servicios web clásico. 

