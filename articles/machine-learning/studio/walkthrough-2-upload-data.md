---
title: 'Paso 2: Carga de datos en un experimento de Machine Learning | Microsoft Docs'
description: "Paso 2 del tutorial Desarrollo de una solución predictiva: carga de datos públicos almacenados en Estudio de aprendizaje automático de Azure."
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 9f4bc52e-9919-4dea-90ea-5cf7cc506d85
ms.service: machine-learning
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.author: garye
ms.openlocfilehash: 469c94f6115f99bc4cf067e9c8f0e55c64990358
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="walkthrough-step-2-upload-existing-data-into-an-azure-machine-learning-experiment"></a>Paso 2 del tutorial: Carga de los datos existentes en un experimento de Aprendizaje automático de Azure
Este es el segundo paso del tutorial [Desarrollo de una solución de análisis predictiva para la evaluación del riesgo de crédito en Aprendizaje automático de Azure](walkthrough-develop-predictive-solution.md)

1. [Creación de un área de trabajo de Aprendizaje automático](walkthrough-1-create-ml-workspace.md)
2. **Carga de los datos existentes**
3. [Crear un experimento nuevo](walkthrough-3-create-new-experiment.md)
4. [Entrenamiento y evaluación de los modelos](walkthrough-4-train-and-evaluate-models.md)
5. [Implementación del servicio web](walkthrough-5-publish-web-service.md)
6. [Acceso al servicio web](walkthrough-6-access-web-service.md)

- - -
Para desarrollar un modelo de predicción de riesgo de crédito, se necesitan datos para entrenar y probar el modelo. Para este tutorial, se usará el conjunto de datos "UCI Statlog (German Credit Data)" del repositorio de Machine Learning de UC Irvine. Puede encontrarlo aquí:   
<a href="http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)</a>

Usaremos el archivo llamado **german.data**. Descargue este archivo en la unidad de disco duro local.  

El conjunto de datos **german.data** contiene filas de 20 variables para 1000 solicitantes de crédito. Estas 20 variables representan el conjunto de características (el *vector de características*) del conjunto de datos que proporciona características de identificación de cada solicitante de crédito. Una columna adicional en cada fila representa el riesgo de crédito del solicitante, donde 700 solicitantes se identificaron como de bajo riesgo y 300 como de alto riesgo.

El sitio web de UCI proporciona una descripción de los atributos del vector de características de estos datos. Entre estos figuran la información financiera, el historial crediticio, el estado de empleo e información personal. A cada solicitante se le ha dado una calificación binaria para indicar si son de riesgo de crédito alto o bajo. 

Estos datos los usaremos para entrenar un modelo de análisis predictivo. Cuando hayamos terminado, nuestro modelo debe poder aceptar un nuevo vector de características para una nueva persona y predecir si esta presenta un alto o bajo riesgo de crédito.  

Aquí hay un giro interesante. La descripción del conjunto de datos en el sitio web de UCI menciona lo que cuesta si clasificamos erróneamente el riesgo de crédito de una persona.
Si el modelo predice un riesgo de crédito alto para un usuario que realmente tiene un riesgo de crédito bajo, el modelo ha realizado una clasificación incorrecta.
Pero las clasificaciones inversas incorrectas son cinco veces más costosas para la institución financiera: si el modelo predice un riesgo de crédito bajo para un usuario que realmente tiene un riesgo de crédito alto.

Por lo tanto, es deseable entrenar el modelo para que el costo de este último tipo de clasificación incorrecta sea cinco veces mayor que clasificar erróneamente de la otra forma.
Una forma sencilla de hacerlo al formar el modelo en nuestro experimento es duplicar (cinco veces) esas entradas que representan a alguien con un riesgo de crédito alto. A continuación, si el modelo clasifica erróneamente a una persona como de riesgo de crédito bajo cuando realmente tiene un riesgo alto, el modelo realiza esa misma clasificación incorrecta cinco veces, una vez para cada duplicado. Esto aumentará el coste de este error en los resultados del entrenamiento.


## <a name="convert-the-dataset-format"></a>Conversión del formato del conjunto de datos
El conjunto de datos original utiliza un formato separado por espacios en blanco. Estudio de aprendizaje automático funciona mejor con un archivo de valores delimitados por comas (CSV), así que vamos a convertir el conjunto de datos y reemplazar los espacios por comas.  

Hay muchas maneras de convertir los datos. En primer lugar, es posible convertirlos con el siguiente comando de Windows PowerShell:   

    cat german.data | %{$_ -replace " ",","} | sc german.csv  

También se puede hacer con el comando sed de Unix:  

    sed 's/ /,/g' german.data > german.csv  

En cualquier caso, se creará una versión separada por comas de los datos en un archivo denominado **german.csv** que usaremos en nuestro experimento.

## <a name="upload-the-dataset-to-machine-learning-studio"></a>Carga del conjunto de datos en Estudio de aprendizaje automático
Una vez que los datos se han convertido al formato CSV, debemos cargarlos en el Estudio de aprendizaje automático. 

1. Abra la página principal de Machine Learning Studio ([https://studio.azureml.net](https://studio.azureml.net)). 

2. Haga clic en el ![menú][1] de la esquina superior izquierda de la ventana, haga clic en **Azure Machine Learning**, seleccione **Studio** e inicie sesión.

3. Haga clic en **+NUEVO** en la parte inferior de la página.

4. Seleccione **CONJUNTO DE DATOS**.

5. Seleccione **DE ARCHIVO LOCAL**.

    ![Adición de un conjunto de datos desde un archivo local][2]

6. En el diálogo **Cargar un nuevo conjunto de datos**, haga clic en **Examinar** y busque el archivo **german.csv** que ha creado.

7. Escriba un nombre para el conjunto de datos. En este tutorial, lo llamaremos "UCI German Credit Card Data".

8. Para el tipo de datos, seleccione **Archivo CSV genérico sin encabezado (.nh.csv)**.

9. Agregue una descripción si así lo desea.

10. Haga clic en la marca de verificación **Aceptar**.  

    ![Carga del conjunto de datos][3]

De esta manera los datos se cargan en un módulo de conjunto de datos que podemos usar en un experimento.

Para administrar los conjuntos de datos que cargó en Studio, haga clic en la pestaña **CONJUNTOS DE DATOS** a la izquierda de la ventana de Studio.

![Administración de conjuntos de datos][4]

Para obtener más información sobre la importación de diversos tipos de datos a un experimento, consulte [Importar datos de entrenamiento en Azure Machine Learning Studio](import-data.md).

**Siguiente: [Crear un experimento nuevo](walkthrough-3-create-new-experiment.md)**

[1]: media/walkthrough-2-upload-data/menu.png
[2]: media/walkthrough-2-upload-data/add-dataset.png
[3]: media/walkthrough-2-upload-data/upload-dataset.png
[4]: media/walkthrough-2-upload-data/dataset-list.png
