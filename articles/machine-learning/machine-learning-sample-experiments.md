<properties
    pageTitle="Uso de experimentos de ejemplo para crear nuevos experimentos | Microsoft Azure"
    description="Cree nuevos experimentos de Aprendizaje automático de Azure a partir de experimentos de ejemplo y de experimentos compartidos por la comunidad."
    services="machine-learning"
    documentationCenter=""
    authors="cjgronlund"
    manager="paulettm"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="04/28/2016"
    ms.author="chhavib;olgali"/>

# Usar experimentos de ejemplo para crear nuevos experimentos
Cuando desee crear un experimento, busque ejemplos en [Cortana Intelligence Gallery](http://gallery.cortanaintelligence.com/) (Galería de inteligencia para Cortana). Puede encontrar experimentos de ejemplo entre los que aportó el equipo de Aprendizaje automático y los compartidos por la amplia comunidad de Aprendizaje automático. También puede plantear preguntas o publicar comentarios acerca de experimentos.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## Búsqueda en la Galería de Cortana Intelligence
Para ver qué experimentos hay disponibles en la galería, vaya a la [galería](http://gallery.cortanaintelligence.com/) y haga clic en **Experiments** (Experimentos) en la parte superior de la página. En esta página puede ver experimentos **agregados recientemente** (Recently added), averiguar **qué es popular** (What's popular) o consultar los **experimentos populares de Microsoft** (Popular Microsoft experiments) más recientes.

Para examinar todos los experimentos:

1. Haga clic en **Browse all** (Examinar todo) en la parte superior de la página.
2. En **Refine by** (Refinar por), seleccione **Experiment** (Experimento) para ver todos los experimentos de la galería.
3. Para restringir más la lista, seleccione filtros de la izquierda. Por ejemplo, para examinar los experimentos que utilizan un algoritmo de detección de anomalías basado en PCA, seleccione **Experiment** (Experiment) en **Categories** (Categorías), y **PCA-Based Anomaly Detection** (Detección de anomalías basada en PCA) en **Algorithms Used** (Algoritmos usados). Si no ve ese algoritmo, haga clic en **Show All** (Mostrar todo) en la parte inferior de la lista.<br></br> ![](./media/machine-learning-sample-experiments/refine-the-view.png) 
4. Puede usar el cuadro de búsqueda para restringir aún más la selección. Por ejemplo, para buscar experimentos aportados por Microsoft relacionados con el reconocimiento de dígitos que usen un algoritmo de máquina de vectores de soporte de dos clases, escriba "digit recognition" (reconocimiento de dígitos) en el cuadro de búsqueda y seleccione **Experiment** (Experimento), **Microsoft content only** (Solo contenido de Microsoft) y **Two-Class Support Vector Machine** (Máquina de vectores de soporte de dos clases): ![](./media/machine-learning-sample-experiments/search-for-experiments.png) 
5. Haga clic en un experimento para obtener más información. Para ejecutar o modificar el experimento, haga clic en **Open in Studio** (Abrir en Estudio) en la página del experimento.

> [AZURE.NOTE] Para abrir un experimento en Estudio de aprendizaje automático, debe iniciar sesión con las credenciales de su cuenta Microsoft. Si aún no tiene un área de trabajo de Aprendizaje automático, se creará un área de trabajo de prueba gratuita. [Información sobre qué incluye la evaluación gratuita de Aprendizaje automático](https://azure.microsoft.com/pricing/details/machine-learning/)

![](./media/machine-learning-sample-experiments/example-experiment.png)


## Uso de una plantilla en Estudio de aprendizaje automático

También puede crear un nuevo experimento en Estudio de aprendizaje automático si usa un ejemplo de la galería como plantilla.

1. Inicie sesión con las credenciales de su cuenta Microsoft en [Estudio](https://studio.azureml.net) y después haga clic en **Nuevo*** para crear un nuevo experimento.
2. Examine el contenido de ejemplo y haga clic en uno.

Se crea un nuevo experimento en el área de trabajo con el experimento de ejemplo como plantilla.

## Pasos siguientes
- [Preparación de sus datos](machine-learning-data-science-import-data.md)
- [Probar el uso de R en el experimento](machine-learning-r-quickstart.md)
- [Revisar los experimentos de ejemplo R](machine-learning-r-csharp-web-service-examples.md)
- [Crear una API de servicio web](machine-learning-publish-a-machine-learning-web-service.md)
- [Examinar aplicaciones listas para utilizar](https://datamarket.azure.com/browse?query=machine+learning)

<!---HONumber=AcomDC_0504_2016-->