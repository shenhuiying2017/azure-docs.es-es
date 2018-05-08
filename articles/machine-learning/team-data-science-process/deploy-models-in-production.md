---
title: 'Implementación de datos en producción: Azure Machine Learning | Microsoft Docs'
description: La implementación de modelos en producción les permite desempeñar un papel activo en la toma de decisiones empresariales.
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/30/2017
ms.author: deguhath
ms.openlocfilehash: ce12247f9b3e4ad3ffff307ee8848662ed7f5046
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/03/2018
---
# <a name="deploy-models-in-production"></a>Implementación de modelos en producción

La implementación de producción permite que un modelo desempeñe un rol activo en una empresa. Las predicciones a partir de un modelo implementado pueden usarse en las decisiones empresariales.

## <a name="production-platforms"></a>Plataformas de producción
Hay varios enfoques y plataformas para poner los modelos en producción. Estas son algunas opciones:


- [Implementación de modelos en Azure Machine Learning](../desktop-workbench/model-management-overview.md)
- [Implementación de un modelo en SQL-Server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Servidor de Microsoft Machine Learning](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)


>[!NOTE]
>Antes de la implementación, uno debe asegurarse de que la latencia de la puntuación de los modelos es lo suficientemente baja para usarse en producción.
>


>[!NOTE]
>Para implementar mediante Azure Machine Learning Studio, consulte [Implementar un servicio web de Azure Machine Learning](../studio/publish-a-machine-learning-web-service.md).
>

## <a name="ab-testing"></a>Pruebas A/B
Cuando hay varios modelos en producción, puede resultar útil realizar [pruebas A/B](https://en.wikipedia.org/wiki/A/B_testing) para comparar el rendimiento de los modelos. 

 
## <a name="next-steps"></a>Pasos siguientes

También se proporcionan tutoriales que muestran todos los pasos del proceso en **escenarios concretos**. Se enumeran y enlazan con descripciones en miniatura en los [tutoriales de ejemplo](walkthroughs.md). En ellos se ilustra cómo combinar herramientas y servicios locales y en la nube en un flujo de trabajo o canalización para crear una aplicación inteligente. 
 


