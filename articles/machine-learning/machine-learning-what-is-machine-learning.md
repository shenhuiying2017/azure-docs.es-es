<properties 
    pageTitle="¿Qué es el Aprendizaje automático de Azure? | Microsoft Azure" 
    description="Información general del servicio de Aprendizaje automático de Azure." 
    services="machine-learning" 
    documentationCenter="" 
    authors="tedway" 
    manager="neerajkh" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="04/22/2015" 
    ms.author="tedway;olgali"/>


# ¿Qué es el aprendizaje automático?
El aprendizaje automático está en todo lo que le rodea en su trabajo. Cuando compra en línea, el aprendizaje automático ayuda a recomendar otros productos según lo que haya adquirido. Al pasar su tarjeta de crédito, el aprendizaje automático ayuda al banco a detectar fraudes y le notifica si la transacción le parece sospechosa. El aprendizaje automático es el proceso de creación de modelos para aprender a partir de datos existentes y realizar análisis predictivos de datos en el futuro.

## ¿Qué es el Aprendizaje automático de Microsoft Azure?
El Aprendizaje automático de Azure es un eficaz servicio de análisis predictivo basado en la nube que permite crear rápidamente soluciones de análisis. Hemos creado el Aprendizaje automático de Azure para democratizar el aprendizaje automático: hemos eliminado el trabajo pesado de creación e implementación de tecnología de aprendizaje automático para hacerlo accesible a todo el mundo. Es un servicio completamente administrado, lo que significa que no es necesario comprar ningún hardware ni administrar manualmente máquinas virtuales.

Puede utilizar la herramienta basada en explorador [Estudio de aprendizaje automático](machine-learning-what-is-ml-studio.md) para crear rápidamente y automatizar flujos de trabajo de aprendizaje automático. Puede arrastrar y colocar cientos de [bibliotecas de aprendizaje automático](https://msdn.microsoft.com/library/azure/f5c746fd-dcea-4929-ba50-2a79c4c067d7) existentes para impulsar sus soluciones de análisis predictivos y, a continuación, agregar de forma opcional sus propios scripts [R](machine-learning-r-quickstart.md) y [Python](machine-learning-execute-python-scripts.md) personalizados para ampliarlos. El Estudio funciona en cualquier explorador y le permite desarrollar e iterar en soluciones rápidamente. ![Experimentos de análisis predictivos en la nube con el Estudio de aprendizaje automático de Azure](./media/machine-learning-what-is-machine-learning/AzureMLStudio.png)

Puede detectar y crear fácilmente [servicios web](machine-learning-publish-a-machine-learning-web-service.md), [entrenar y volver a entrenar sus modelos a través de API](machine-learning-retrain-models-programmatically.md), [administrar extremos](machine-learning-create-endpoint.md) y [escalar servicios web](machine-learning-scaling-endpoints.md) por clientes, así como configurar diagnósticos para la depuración y supervisión de servicios. Las nuevas características incluyen:

- La capacidad para crear un módulo R personalizado configurable, incorporar sus propios scripts R de entrenamiento/predicción y agregar scripts de Python con un gran ecosistema de bibliotecas, como numpy, scipy, pandas o scikit-learn. Ahora puede entrenar terabytes de datos mediante el [aprendizaje con recuentos][learning-with-counts], el uso de PCA o SVM de una clase para la detección de anomalías y la modificación, el filtrado y la limpieza de datos mediante el conocido SQLite. 
- La [Galería de la comunidad de Aprendizaje automático](machine-learning-gallery-how-to-use-contribute-publish.md) le permite descubrir y utilizar interesantes experimentos creados por otros usuarios. También puede hacer preguntas o publicar comentarios acerca de experimentos en la Galería o publicar sus experimentos. Puede compartir vínculos a experimentos interesantes a través de canales sociales como LinkedIn y Twitter. La galería es una excelente forma para que los usuarios empiecen a trabajar con el Aprendizaje automático de Azure y aprendan de otros usuarios de la comunidad. ![Pruebe ejemplos de experimentos predictivos o contribuya con los suyos propios en la Galería de Aprendizaje automático de Azure.](./media/machine-learning-what-is-machine-learning/AzureMLGallery.png)
- Puede adquirir [aplicaciones de Marketplace](https://datamarket.azure.com/browse?query=machine+learning) a través de una suscripción de Azure y consumir servicios web terminados de recomendaciones, análisis de texto y detección de anomalías directamente desde Azure Marketplace. 
- Una guía paso a paso para el viaje de la ciencia de los datos partiendo de datos sin procesar hasta un servicio web consumible para facilitar el acceso a la ciencia de los datos basados en la nube. Hemos agregado la capacidad para usar herramientas conocidas, como el Bloc de notas de iPython y Python Tools para Visual Studio con el Aprendizaje automático de Azure.

## Empiece ahora
Puede aprender los conceptos básicos de análisis predictivos y de aprendizaje automático mediante un [tutorial paso a paso](machine-learning-create-experiment.md) y [creando muestras](machine-learning-sample-experiments.md). Al probar experimentos en Estudio, no se requiere ninguna tarjeta de crédito ni suscripción a Azure para empezar a usar el Aprendizaje automático.


<!-- Module References -->
[learning-with-counts]: https://msdn.microsoft.com/library/azure/81c457af-f5c0-4b2d-922c-fdef2274413c/

<!--HONumber=54--> 