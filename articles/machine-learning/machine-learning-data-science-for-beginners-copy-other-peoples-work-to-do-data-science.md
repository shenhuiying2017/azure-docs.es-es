---
title: Copia de trabajos de ciencia de datos de otros usuarios (ejemplo de Azure Machine Learning) | Microsoft Docs
description: "El secreto comercial de la ciencia de datos: consiga que otras personas hagan el trabajo por usted. Obtenga ejemplos de Machine Learning de la Galería de Cortana Analytics."
keywords: "ejemplos de ciencia de datos,ejemplo de aprendizaje automático, algoritmo de agrupación en clústeres, ejemplo de algoritmo de agrupación en clústeres"
services: machine-learning
documentationcenter: na
author: cjgronlund
manager: jhubbard
editor: cjgronlund
ms.assetid: ec2be823-c325-4ad8-b8b2-3e664f1a44b4
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/09/2017
ms.author: cgronlun;garye
translationtype: Human Translation
ms.sourcegitcommit: 1e941778c194462b311e6347c56670d3e0193e90
ms.openlocfilehash: ebc44a4190bac697697e0b651039a0e8ee8e4a95


---
# <a name="copy-other-peoples-work-to-do-data-science"></a>Copia del trabajo de otras personas para realizar ciencia de datos
## <a name="video-5-data-science-for-beginners-series"></a>Vídeo 5: Ciencia de datos para principiantes
Uno de los secretos empresariales de la ciencia de datos es conseguir que otras personas hagan el trabajo por usted. Vea un ejemplo de algoritmo de agrupación en clústeres de la Galería de Cortana Analytics que usará en su propio experimento de Machine Learning.

Para obtener el máximo partido de la serie, véalos en orden. [Ir a la lista de vídeos](#other-videos-in-this-series)

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/data-science-for-beginners-series-copy-other-peoples-work-to-do-data-science/player]
>
>

## <a name="other-videos-in-this-series"></a>Otros vídeos de la serie
*Ciencia de datos para principiantes* es una introducción rápida a la ciencia de datos en cinco vídeos de corta duración.

* Vídeo 1: [Las cinco preguntas a las que responde la ciencia de datos](machine-learning-data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 minutos y 14 segundos)*
* Vídeo 2: [¿Están sus datos preparados para la ciencia de datos?](machine-learning-data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 minutos y 56 segundos)*
* Vídeo 3: [Realización de preguntas que pueden responderse con datos](machine-learning-data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 minutos y 17 segundos)*
* Vídeo 4: [Predicción de respuestas con un modelo sencillo](machine-learning-data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 minutos y 42 segundos)*
* Vídeo 5: Copia del trabajo de otras personas para realizar ciencia de datos.

## <a name="transcript-copy-other-peoples-work-to-do-data-science"></a>Transcript: Copia del trabajo de otras personas para realizar ciencia de datos
Este es el quinto vídeo de la serie Ciencia de datos para principiantes.

En este caso, descubrirá un lugar donde puede buscar ejemplos que puede usar como punto de partida para su propio trabajo. Aprovechará al máximo este vídeo si ve primero los vídeos anteriores de esta serie.

Uno de los secretos empresariales de la ciencia de datos es conseguir que otras personas hagan el trabajo por usted.

## <a name="find-examples-in-the-cortana-intelligence-gallery"></a>Búsqueda de ejemplos en la Galería de Cortana Intelligence
Microsoft tiene un servicio basado en la nube denominado [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) que puede probar de forma gratuita. Proporciona un área de trabajo donde puede experimentar con diferentes algoritmos de aprendizaje automático y, si consigue una solución que funciona, puede iniciarla como un servicio web.

Parte de este servicio es algo llamado **[Galería de Cortana Intelligence](http://aka.ms/CortanaIntelligenceGallery)**. Contiene una variedad de recursos, uno de los cuales es una colección de experimentos de Aprendizaje automático de Azure, o modelos, que los usuarios han creado y contribuido para que otros usuarios los utilicen. Estos experimentos son una excelente manera de aprovechar las ideas y el duro trabajo de otras personas para empezar a crear sus propias soluciones.

Puede encontrar la galería en [aka.ms/CortanaIntelligenceGallery](http://aka.ms/CortanaIntelligenceGallery). Todo el mundo puede consultarla.

![Galería de Cortana Intelligence](./media/machine-learning-data-science-for-beginners-copy-other-peoples-work-to-do-data-science/cortana-intelligence-gallery.png)

Si hace clic en **Experiments** (Experimentos) en la parte superior, verá los experimentos más recientes y populares de la galería. Puede buscar consultar los demás experimentos haciendo clic en **Browse All** (Examinar todo) en la parte superior de la pantalla, ahí puede escribir términos de búsqueda y elegir filtros de búsqueda.

## <a name="find-and-use-a-clustering-algorithm-example"></a>Localización y uso de un ejemplo de algoritmo de clústeres
Por ejemplo, suponga que desea ver un ejemplo de cómo funcionan los clústeres, por lo que busca experimentos de **clustering** .

![Búsqueda de experimentos de clústeres](./media/machine-learning-data-science-for-beginners-copy-other-peoples-work-to-do-data-science/search-for-clustering-experiments.png)

Aquí hay algo interesante que alguien ha aportado a la galería.

![Experimento de clústeres](./media/machine-learning-data-science-for-beginners-copy-other-peoples-work-to-do-data-science/clustering-experiment.png)

Haga clic en ese experimento para obtener una página web en la que se describe el trabajo que hizo este colaborador, junto con algunos de sus resultados.

![Página de descripción del experimento de clústeres](./media/machine-learning-data-science-for-beginners-copy-other-peoples-work-to-do-data-science/clustering-experiment-description-page.png)

Observe el vínculo que dice **Abrir en Estudio**.

![Botón Open in Studio (Abrir en Estudio)](./media/machine-learning-data-science-for-beginners-copy-other-peoples-work-to-do-data-science/open-in-studio.png)

Puedo hacer clic en él y me lleva directamente a **Estudio de aprendizaje automático de Azure**. Crea una copia del experimento y la coloca en mi propia área de trabajo. Incluye el conjunto de datos del colaborador, todo el procesamiento que realizaron, todos los algoritmos que usaron y cómo se guardaron los resultados.

![Abra un experimento de la Galería en Machine Learning Studio (ejemplo de algoritmo de agrupación en clústeres)](./media/machine-learning-data-science-for-beginners-copy-other-peoples-work-to-do-data-science/cluster-experiment-open-in-studio.png)

Y ahora tengo un punto de partida. Puedo cambiar sus datos por los míos y retocar el modelo. Esto me proporciona un punto de partida que funciona y me permite trabajar aprovechando el trabajo de las personas que realmente saben lo que hacen.

## <a name="find-experiments-that-demonstrate-machine-learning-techniques"></a>Búsqueda de experimentos que muestran técnicas de aprendizaje automático
Hay otros experimentos en la [Galería de Cortana Intelligence](http://aka.ms/CortanaIntelligenceGallery) incluidos específicamente para proporcionar ejemplos de procedimientos para personas nuevas en la ciencia de datos. Por ejemplo, hay un experimento en la galería que muestra qué hacer cuando faltan valores ([Methods for handling missing values](https://gallery.cortanaintelligence.com/Experiment/Methods-for-handling-missing-values-1)[Métodos para gestionar valores que faltan]). Le guía por 15 maneras diferentes de sustituir valores vacíos, y habla sobre las ventajas de cada método y cuándo usarlo.

![Experimento de la Galería de Machine Learning Studio: métodos para valores ausentes](./media/machine-learning-data-science-for-beginners-copy-other-peoples-work-to-do-data-science/experiment-methods-for-handling-missing-values.png)

[Galería de Cortana Intelligence](http://aka.ms/CortanaIntelligenceGallery) es un lugar para buscar experimentos que funcionan y que puede usar como punto de partida para sus propias soluciones.

Asegúrese de consultar los demás vídeos de la serie "Ciencia de datos para principiantes" de Aprendizaje automático de Microsoft Azure.

## <a name="next-steps"></a>Pasos siguientes
* [Prueba de su primer experimento de ciencia de datos con Aprendizaje automático de Azure](machine-learning-create-experiment.md)
* [Introducción a Aprendizaje automático en Microsoft Azure](machine-learning-what-is-machine-learning.md)



<!--HONumber=Feb17_HO2-->


