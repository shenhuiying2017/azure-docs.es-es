---
title: Reciclaje de un modelo de Machine Learning | Microsoft Docs
description: "Obtenga información sobre cómo reciclar un modelo y actualizar el servicio web para utilizar el modelo recién entrenado en Azure Machine Learning."
services: machine-learning
documentationcenter: 
author: garyericson
manager: raymondl
editor: 
ms.assetid: d1cb6088-4f7c-4c32-94f2-f7523dad9059
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: v-donglo
ms.openlocfilehash: 54655c33336ff3fef261024984947108d6f1df9c
ms.sourcegitcommit: 42ee5ea09d9684ed7a71e7974ceb141d525361c9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/09/2017
---
# <a name="retrain-a-machine-learning-model"></a>Reciclaje de un modelo de Machine Learning
Como parte del proceso de operacionalización de modelos de Azure Machine Learning, el modelo se debe entrenar y guardar. Posteriormente, podrá usarlo para crear un servicio web predicativo. A continuación, el servicio web se puede consumir en sitios web, paneles y aplicaciones móviles. 

Los modelos que crea mediante Machine Learning no suelen ser estáticos. Cuando hay nuevos datos disponibles o cuando el consumidor de la API tiene sus propios datos, el modelo debe volver a entrenarse. 

El reentrenamiento puede producirse con frecuencia. Con la característica de la API de reentrenamiento mediante programación, puede reciclar el modelo mediante programación con las API de reentrenamiento y actualizar el servicio web con el modelo recién entrenado. 

Este documento describe el proceso anterior y muestra cómo usar las API de reentrenamiento.

## <a name="why-retrain-defining-the-problem"></a>Por qué volver a entrenar: definición del problema
Como parte del proceso de entrenamiento de aprendizaje automático, un modelo se entrena usando un conjunto de datos. Los modelos que crea mediante Machine Learning no suelen ser estáticos. Cuando hay nuevos datos disponibles o cuando el consumidor de la API tiene sus propios datos, el modelo debe volver a entrenarse.

En estos casos, una API de programación proporciona una manera cómoda de permitirle a usted o al consumidor de sus API crear un cliente que pueda, en un momento determinado o de forma habitual, volver a entrenar un modelo usando sus propios datos. A continuación, puede evaluar los resultados del reentrenamiento y actualizar la API del servicio web para utilizar el modelo recién entrenado.

> [!NOTE]
> Si tiene un experimento de entrenamiento existente y el servicio web nuevo, es posible que desee comprobar el reciclaje de un servicio web predictivo existente en lugar de seguir el tutorial mencionado en la sección siguiente.
> 
> 

## <a name="end-to-end-workflow"></a>Flujo de trabajo de un extremo a otro
El proceso engloba los siguientes componentes: un experimento de entrenamiento y un experimento predictivo publicados como un servicio web. Para habilitar el reentrenamiento de un modelo entrenado, el experimento de entrenamiento debe publicarse como servicio web con el resultado de un modelo entrenado. Esto permite a la API obtener acceso al modelo para el nuevo entrenamiento. 

Los pasos siguientes se aplican a los servicios nuevos y web clásicos:

Crear el servicio web predictivo inicial:

* Crear un experimento de entrenamiento
* Crear un experimento web predictivo
* Implementar un servicio web predictivo

Reciclar el servicio web:

* Actualizar el experimento de entrenamiento para permitir el reciclaje
* Implementar el servicio web de reciclaje
* Usar el código de servicio de ejecución por lotes para reciclar el modelo

Para obtener un tutorial de los anteriores pasos, consulte [Reciclar modelos de Machine Learning mediante programación](retrain-models-programmatically.md).

> [!NOTE] 
> Para implementar un nuevo servicio web, debe tener permisos suficientes en la suscripción en la que lo implementa. Para obtener más información, consulte [Administración de un servicio web mediante el portal Servicios web Azure Machine Learning](manage-new-webservice.md). 

Si ha implementado un servicio web clásico:

* Crear un nuevo punto de conexión del servicio web predictivo
* Obtener la dirección URL de revisión y el código
* Utilice la dirección URL de revisión para señalar el nuevo punto de conexión en el modelo reciclado 

Para obtener un tutorial de los anteriores pasos, consulte [Reciclaje de un servicio web clásico](retrain-a-classic-web-service.md).

Si tiene dificultades para reciclar un servicio web clásico, consulte [Solución de problemas de reciclaje de un servicio web clásico de Azure Machine Learning](troubleshooting-retraining-models.md).

Si ha implementado un nuevo servicio web:

* Inicie sesión en la cuenta de Azure Resource Manager
* Obtenga la definición de servicio web
* Exporte la definición de servicio web como JSON
* Actualice la referencia al blob `ilearner` en JSON
* Importe JSON en una definición de servicio web
* Actualice el servicio web con la nueva definición de servicio web

Para obtener un tutorial de los pasos anteriores, consulte [Reciclaje de un servicio web nuevo mediante los cmdlets de PowerShell de Machine Learning](retrain-new-web-service-using-powershell.md).

El proceso de configuración de reentrenamiento de un servicio web clásico implica los pasos siguientes:

![Descripción del proceso de reentrenamiento][1]

El proceso de configuración de reciclaje de un servicio web nuevo implica los pasos siguientes:

![Descripción del proceso de reentrenamiento][7]

## <a name="other-resources"></a>Otros recursos
* [Reciclaje y actualización de modelos de Azure Machine Learning con Azure Data Factory](https://azure.microsoft.com/blog/retraining-and-updating-azure-machine-learning-models-with-azure-data-factory/)
* [Creación de varios modelos de Machine Learning y puntos de conexión de servicio web a partir de un experimento mediante PowerShell](create-models-and-endpoints-with-powershell.md)
* El vídeo [Modelos de reciclaje de AML mediante API](https://www.youtube.com/watch?v=wwjglA8xllg) muestra cómo reciclar modelos de Machine Learning creados en Azure Machine Learning mediante las API de reciclaje y PowerShell.

<!--image links-->
[1]: ./media/retrain-machine-learning-model/machine-learning-retrain-models-programmatically-IMAGE01.png
[7]: ./media/retrain-machine-learning-model/machine-learning-retrain-models-programmatically-IMAGE07.png

