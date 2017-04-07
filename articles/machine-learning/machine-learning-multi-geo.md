---
title: "Documentación de ayuda multigeográfica | Microsoft Docs"
description: "Aprenda a crear un área de trabajo y a publicar un servicio web en una región de Azure diferente de la región central del sur de Estados Unidos (SCUS) de Azure."
services: machine-learning
documentationcenter: 
author: tedway
manager: jhubbard
editor: rmca14
tags: 
ms.assetid: ed0ca8a8-fa53-4e56-b824-2d7e44641967
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 4/6/2017
ms.author: tedway; neerajkh
translationtype: Human Translation
ms.sourcegitcommit: ec9d29701915d237686625fbc3abec9827fda467
ms.openlocfilehash: 22c30bfea89e3c650d81e1ebae20c9d151e6f36c
ms.lasthandoff: 12/10/2016


---
# <a name="multi-geo-help-documentation"></a>Documentación de ayuda multigeográfica
En este artículo se describe cómo crear un área de trabajo y publicar un servicio web en diferentes regiones de Azure.  La página [Productos de Azure por región](https://azure.microsoft.com/en-us/regions/services/) enumera las áreas donde Azure Machine Learning está disponible.

## <a name="create-a-workspace"></a>Crear un área de trabajo
1. Inicie sesión en el Portal de Azure clásico.
2. Haga clic en **+ NUEVO** > **DATA SERVICES** > **MACHINE LEARNING** > **CREACIÓN RÁPIDA**.  En **UBICACIÓN**, seleccione otra región, como **Sudeste Asiático**.
   ![Imagen de ayuda multigeográfica 1][1]
3. Seleccione el área de trabajo y, a continuación, haga clic en **Inicio de sesión en Estudio de aprendizaje automático**.
   ![Imagen de ayuda multigeográfica 2][2]
4. Ahora dispone de un área de trabajo en otra región que se puede usar como cualquier otra área de trabajo. Para cambiar entre las áreas de trabajo, busque la esquina superior derecha de la pantalla. Haga clic en la lista desplegable, seleccione la región y, a continuación, seleccione el área de trabajo. Todo es local para la región del área de trabajo.  Por ejemplo, todos los servicios web creados a partir de un área de trabajo estarán en la misma región en la que se encuentra el área de trabajo.
   ![Imagen de ayuda multigeográfica 3][3]

## <a name="open-an-experiment-from-gallery"></a>Abrir un experimento de Galería
Si abre un experimento de Galería, también puede seleccionar en qué región desea copiar el experimento.

![Imagen de ayuda multigeográfica 4][4a]

## <a name="web-service-management"></a>Administración de servicios web
Para administrar mediante programación los servicios web, como el reciclaje, use la dirección específica de la región:

* https://asiasoutheast.management.azureml.net
* https://europewest.management.azureml.net

### <a name="things-to-note"></a>Puntos a tener en cuenta
1. Solo se pueden copiar experimentos entre áreas de trabajo que pertenecen a la misma región. Si necesita copiar el experimento en áreas de trabajo de distintas regiones, puede usar el commandlet [*Copy-AmlExperiment*](https://github.com/hning86/azuremlps/blob/master/README.md#copy-amlexperiment) de [PowerShell](http://aka.ms/amlps). Otra solución consiste en publicar el experimento en la galería en el modo fuera de lista y abrirlo en el área de trabajo de la otra región.
2. El selector de región solo mostrará áreas de trabajo de una región de cada vez.  
3. Se creará un área de trabajo libre o un área de trabajo (anónima) de acceso a invitados y se alojará en la zona central del sur de EE. UU.  
4. Los servicios web implementados desde un área de trabajo del sudeste asiático también se hospedarán en el sudeste asiático.  

## <a name="more-information"></a>Más información
Formule una pregunta en el [Foro de Aprendizaje automático de Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=MachineLearning).

<!--Image references-->
[1]: ./media/machine-learning-multi-geo/multi-geo_1.png
[2]: ./media/machine-learning-multi-geo/multi-geo_2.png
[3]: ./media/machine-learning-multi-geo/multi-geo_3.png
[4a]: ./media/machine-learning-multi-geo/multi-geo_4a.png

