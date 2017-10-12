---
title: "Introducción a Deep Learning Virtual Machine en Azure | Documentos de Microsoft"
description: "Escenarios de análisis fundamentales y componentes para las instancias de Deep Learning Virtual Machine."
keywords: "aprendizaje profundo, IA, herramientas de ciencia de datos, máquina virtual de ciencia de datos, herramientas para la ciencia de datos, ciencia de datos de Linux"
services: machine-learning
documentationcenter: 
author: gopitk
manager: cgronlun
editor: cgronlun
ms.assetid: d4f91270-dbd2-4290-ab2b-b7bfad0b2703
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2017
ms.author: gokuma
ms.openlocfilehash: a3e4c989c1dbb31b237115acfcc032aa2dee4f2a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-the-deep-learning-virtual-machine"></a>Introducción a Deep Learning Virtual Machine

## <a name="why-deep-learning-virtual-machine"></a>¿Por qué Deep Learning Virtual Machine? 

Cada vez más, los algoritmos de aprendizaje profundo y las redes neuronales profundas se están convirtiendo en métodos populares empleados en la resolución de muchos de los problemas del aprendizaje automático. Se trata de métodos especialmente adecuados para las tareas de computación cognitiva como comprensión de imagen, texto, audio o vídeo, alcanzando con frecuencia niveles cognitivos humanos en algunos dominios específicos con arquitecturas de red neuronal profunda avanzadas y acceso a un conjunto grande de datos para entrenar modelos. El aprendizaje profundo requiere una gran potencia de cálculo para entrenar modelos con estos grandes conjuntos de datos. Con la nube y la disponibilidad de unidades de procesamiento gráfico (GPU), está posibilitándose  la creación de sofisticadas arquitecturas neuronales profundas y su formación con un gran conjunto de datos con una infraestructura informática potente en la nube.  [Data Science Virtual Machine](overview.md) ha proporcionado un amplio conjunto de herramientas y ejemplos para la preparación de los datos, el aprendizaje automático y el aprendizaje profundo. Pero uno de los desafíos a los que se enfrentan los usuarios consiste en detectar fácilmente las herramientas y ejemplos de escenarios concretos como aprendizaje profundo y también aprovisionar fácilmente más instancias de máquina virtual basadas en GPU. Deep Learning Virtual Machine (DLVM) aborda estos retos. 

## <a name="what-is-deep-learning-virtual-machine"></a>¿Qué es Deep Learning Virtual Machine? 
Deep Learning Virtual Machine (DLVM) es una variante configurada especialmente de la popular [Data Science Virtual Machine](overview.md) (DSVM) para que resulte más fácil usar instancias de máquina virtual basadas en GPU para entrenar modelos de aprendizaje profundo. Se admite en Windows 2016 y Ubuntu Data Science Virtual Machine.  Comparte las mismas imágenes de máquina virtual principal (y por lo tanto, todo el completo conjunto de herramientas) que DSVM, pero está configurada para facilitar el aprendizaje profundo. También se proporcionan ejemplos de un extremo a otro para la comprensión de imagen y texto, que son ampliamente aplicables a muchos escenarios de inteligencia artificial de uso real. Deep Learning Virtual Machine también intenta que el amplio conjunto de herramientas y ejemplos en DSVM sea más fácilmente reconocible, mostrando un catálogo de las herramientas y ejemplos en la máquina virtual. En cuanto a las herramientas, Deep Learning Virtual Machine proporciona varias plataformas de aprendizaje profundo, herramientas para adquirir imágenes procesadas previamente y datos de texto, todos ellos populares. Si desea una lista completa de las herramientas, consultar la [página de introducción a Data Science Virtual Machine](overview.md#whats-included-in-the-data-science-vm). 

## <a name="next-steps"></a>Pasos siguientes

Empiece a trabajar con Deep Learning Virtual Machine con los pasos siguientes:

* [Aprovisionamiento de Deep Learning Virtual Machine](provision-deep-learning-dsvm.md)
* [Usar Deep Learning Virtual Machine](use-deep-learning-dsvm.md)
* [Referencia de las herramientas](dsvm-deep-learning-ai-frameworks.md)
* [Ejemplos](dsvm-samples-and-walkthroughs.md)
