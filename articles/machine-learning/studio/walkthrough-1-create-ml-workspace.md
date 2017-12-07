---
title: "Paso 1: Creación de un área de trabajo de Machine Learning | Microsoft Docs"
description: "Paso 1 del tutorial Desarrollo de una solución predictiva: carga de datos públicos almacenados en Azure Machine Learning Studio."
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: b3c97e3d-16ba-4e42-9657-2562854a1e04
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.author: garye
ms.openlocfilehash: 58b37cf8927926cb128928e4d0e438945074f6e5
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/01/2017
---
# <a name="walkthrough-step-1-create-a-machine-learning-workspace"></a>Paso 1 del tutorial: Creación de un área de trabajo de Machine Learning
Este es el primer paso del tutorial [Desarrollo de una solución de análisis predictiva para la evaluación del riesgo de crédito en Azure Machine Learning](walkthrough-develop-predictive-solution.md).

1. **Creación de un área de trabajo de Machine Learning**
2. [Carga de los datos existentes](walkthrough-2-upload-data.md)
3. [Crear un experimento nuevo](walkthrough-3-create-new-experiment.md)
4. [Entrenamiento y evaluación de los modelos](walkthrough-4-train-and-evaluate-models.md)
5. [Implementación del servicio web](walkthrough-5-publish-web-service.md)
6. [Acceso al servicio web](walkthrough-6-access-web-service.md)

- - -
<!-- This needs to be updated to refer to the new way of creating workspaces in the Ibiza portal -->

Para usar Machine Learning Studio, debe tener un área de trabajo de Microsoft Azure Machine Learning. Esta área de trabajo contiene las herramientas que necesita para crear, administrar y publicar experimentos.  

El administrador de su suscripción de Azure debe crear el área de trabajo y, a continuación, agregarlo como un propietario o colaborador. Para obtener más información, consulte [Creación y uso compartido de un área de trabajo de Azure Machine Learning](create-workspace.md).

Una vez que haya creado el área de trabajo, abra Machine Learning Studio ([https://studio.azureml.net/Home](https://studio.azureml.net/Home)). Si tiene más de un área de trabajo, puede seleccionar la que desee en la barra de herramientas de la esquina superior derecha de la ventana.

![Selección del área de trabajo en Studio][2]

> [!TIP]
> Si le han convertido en el propietario del área de trabajo, puede compartir los experimentos en los que esté trabajando invitando a otros al área. Para ello, en Machine Learning Studio, vaya a la página **CONFIGURACIÓN** . Solo necesita la cuenta Microsoft o la cuenta de organización de cada usuario.
> 
> En la página **CONFIGURACIÓN**, haga clic en **USUARIOS** y, después, haga clic en **INVITE MORE USERS** (INVITAR A MÁS USUARIOS) en la parte inferior de la ventana.
> 
> 

- - -
**A continuación: [Cargue los datos existentes](walkthrough-2-upload-data.md)**

[1]: ./media/walkthrough-1-create-ml-workspace/create1.png
[2]: ./media/walkthrough-1-create-ml-workspace/open-workspace.png
