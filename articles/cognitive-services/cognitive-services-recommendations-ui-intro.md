---
title: "Creación de un modelo con Recommendations UI | Microsoft Docs"
description: "Azure Machine Learning Recommendations: creación de un modelo con Recommendations UI"
services: cognitive-services
documentationcenter: 
author: luiscabrer
manager: jhubbard
editor: cgronlun
ms.assetid: b264fe44-f94e-40ae-9754-60ad7d6cfeb9
ms.service: cognitive-services
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2016
ms.author: luisca
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 5f98395e2beba671192e50f8e53c6198e1efed29


---
# <a name="building-a-model-with-the-recommendations-ui"></a>Creación de un modelo con Recommendations UI
Este documento es una guía paso a paso. Nuestro objetivo es guiarle a través de los pasos necesarios para entrenar un modelo con [Recommendations UI](https://recommendations-portal.azurewebsites.net/).
Pasar por el ejercicio le permite comprender el proceso para crear un modelo antes de hacerlo mediante programación. También le familiariza con la interfaz de usuario, lo cual resulta práctico al empezar a utilizar el servicio.

Este ejercicio dura unos 30 minutos.

<a name="Step1"></a>

## <a name="step-1---sign-in-to-the-recommendations-ui"></a>Paso 1: Inicio de sesión en Recommendations UI
1. Si aún no lo ha hecho, deberá [suscribirse](https://portal.azure.com/#create/Microsoft.CognitiveServices/apitype/Recommendations/pricingtier/S1) a [Recommendations API](https://www.microsoft.com/cognitive-services/en-us/recommendations-api). Puede suscribirse al servicio en **Azure** en [http://portal.azure.com/](https://portal.azure.com/#create/Microsoft.CognitiveServices/apitype/Recommendations/pricingtier/S1) e iniciar sesión con su cuenta de Azure. Se proporcionan instrucciones detalladas sobre el proceso de suscripción en la *tarea 1* de la [guía de introducción](cognitive-services-recommendations-quick-start.md) 
2. Cuando haya obtenido una **clave** para la suscripción a Recommendations API, vaya a [Recommendations UI](https://recommendations-portal.azurewebsites.net/). 
3. Inicie sesión en el portal especificando la clave en el campo **Clave de cuenta** y haga clic en el botón **Inicio de sesión**.
   
    ![Recommendations UI: cuadro de diálogo de inicio de sesión.][reco_signin]

<a name="Step2"></a>

## <a name="step-2---lets-gather-some-training-data"></a>Paso 2: Vamos a recopilar algunos datos de aprendizaje
Antes de crear una compilación, el motor necesita dos informaciones: un archivo de catálogo y un conjunto de archivos de uso. 

El archivo de catálogo contiene información sobre los artículos que ofrece al cliente. Un archivo de uso contiene información sobre cómo se usan los artículos o las transacciones de su negocio.

Normalmente, realizaría una consulta en la base de datos de almacenamiento para estas informaciones. En la actualidad, le proporcionamos algunos datos de ejemplo para que aprenda a usar Recommendations API.

Puede descargar los datos de [http://aka.ms/RecoSampleData](http://aka.ms/RecoSampleData). Copie y desempaquete el archivo **Books.Zip** en una carpeta de la máquina local. Por ejemplo, **c:\data**.

Encontrará información detallada sobre el esquema de los archivos de catálogo y uso en el artículo [Recopilación de datos para entrenar modelos](cognitive-services-recommendations-collecting-data.md).

Para este ejercicio, vamos a trabajar con un archivo pequeño, por lo que el aprendizaje no tardará mucho. Si quiere probar con un archivo más realista, también hemos colocado **MsStoreData.zip**, que contiene las transacciones de ejemplo de Microsoft Store en la [misma ubicación](http://aka.ms/RecoSampleData).

<a name="Step3"></a>

## <a name="step-3---create-a-project-and-upload-catalog-and-usage-data"></a>Paso 3: Creación de un proyecto y carga de los datos de uso y de catálogo
Al iniciar sesión en [Recommendations UI](https://recommendations-portal.azurewebsites.net/), verá la página Projects (Proyectos). Si previamente ha creado algún proyecto, debería verlo aquí.
Un proyecto (también conocido como *modelo* en la referencia de API) es un contenedor para los datos de uso y de catálogo. Puede crear varias *compilaciones* dentro del proyecto. Le guiaremos a través del proceso con los siguientes pasos.

1. Para crear un nuevo proyecto, escriba el nombre en el cuadro de texto (basta con algo parecido a "MiPrimerModelo") y haga clic en **Add Project** (Agregar proyecto).
   
    ![Recommendations UI: página de proyectos.][reco_projects]
2. Una vez creado el proyecto, haga clic en el botón **Browse for File** (Buscar archivo), situado en la sección **Add a Catalog File** (Agregar un archivo de catálogo). 
   Cargue el catálogo que obtuvo en el paso 2. Si lo guardó en *c:\data*, debe navegar hasta esa carpeta.
   
     ![Recommendations UI: adición de datos a un proyecto.][reco_firstmodel]
3. Después de cargar el catálogo, haga clic en el botón **Browse for File** (Buscar archivo), situado en la sección **Add Usage Files** (Agregar archivos de uso). Agregue el archivo usage_large.txt.

> **¿Qué ocurre si tengo un archivo de datos de uso grande?**
> 
> Solo se pueden cargar archivos de hasta 200 MB. Es decir, el sistema puede contener datos de transacción de hasta 2 GB, por lo que puede cargar más de un archivo si es necesario.
> Puede que no necesite tantos datos para generar un buen modelo, no solo importa el tamaño de los datos, también su calidad. Es habitual ver datos de uso donde la mayoría de las transacciones se encuentran en tan solo unos cuantos elementos populares y hay "poca señal" para otros elementos.
> 
> 

<a name="Step4"></a>

## <a name="step-4---lets-do-some-training"></a>Paso 4: Entrenemos.
Ahora que ha cargado los datos de catálogo y de uso, estamos preparados para entrenar el motor para que aprenda patrones de nuestros datos.

1. Haga clic en el botón **New Build** (Compilación nueva).
2. Seleccione el tipo de compilación **Recommendation** (Recomendación). Observe que se admite una clasificación de compilación y también tipos de compilación FBT (artículos que con frecuencia se compran juntos).
   
   ![Recommendations UI: cuadro de diálogo de compilación.][reco_build_dialog.png]

    Una compilación FBT le permite identificar los patrones de los productos que se suelen adquirir/consumir en la misma transacción.
    La compilación de clasificación se utiliza para identificar características interesantes. 
    En este taller, no entraremos en detalle en las compilaciones FBT o de clasificación, pero si le interesa, consulte la [página de documentación Tipos de compilación y calidad de los modelos](cognitive-services-recommendations-buildtypes.md).

1. Haga clic en el botón **Build** (Compilación). El proceso de compilación tarda unos cinco minutos si usa los datos de Books. Se tarda más con conjuntos de datos mayores.

<a name="Step5"></a>

## <a name="step-5---lets-find-out-what-the-machine-learned"></a>Paso 5: Descubramos lo que ha aprendido la máquina.
Una vez completada la compilación, observará una nueva en la lista de compilaciones. Esta compilación pueden solicitarla las recomendaciones de usuarios y de elementos.

1. Una vez completada la compilación, haga clic en **Score** (Puntuación). Esto le permite jugar con el modelo y ver qué elementos se recomiendan.
   
    ![Recommendations UI: botón Score (Puntuación)][reco_score_button]
2. Seleccione un elemento para ver qué elementos se devuelven como recomendaciones para él. Tenga en cuenta que si no hay suficientes transacciones para predecir una recomendación para un elemento determinado, el sistema no devolverá las recomendaciones para él.  Si por alguna razón tiene un elemento que no devuelve ninguna recomendación, pruebe a puntuar otros elementos.

<a name="Step6"></a>

## <a name="step-6---next-steps"></a>Paso 6: Pasos siguientes
¡Enhorabuena! ha entrenado un modelo y ha obtenido sus recomendaciones.  Recommendations UI es una herramienta útil que le permite ver el estado de los proyectos y las compilaciones. 

Ahora que tiene un modelo, puede aprender a realizar todos los pasos anteriores mediante programación. Para aprender a llamar a la API mediante programación, compruebe [Recommendations API Reference](http://go.microsoft.com/fwlink/?LinkId=759348) (Referencia de la API de Recommendations) y descargue la [Recommendations Sample Application](http://go.microsoft.com/fwlink/?LinkID=759344) (aplicación de ejemplo de Recommendations).

[reco_signin]:../media/cognitive-services/reco_signin.PNG
[reco_projects]:../media/cognitive-services/reco_projects.PNG
[reco_firstmodel]:../media/cognitive-services/reco_firstmodel.png
[reco_build_dialog.png]:../media/cognitive-services/reco_build_dialog.png
[reco_score_button]:../media/cognitive-services/reco_score_button.png



<!--HONumber=Dec16_HO2-->


