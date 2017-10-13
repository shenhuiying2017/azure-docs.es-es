---
title: "Importación de datos desde un archivo en Azure Machine Learning Studio | Microsoft Docs"
description: "Obtenga información sobre cómo cargar un archivo de datos de entrenamiento desde la unidad de disco duro a Azure Machine Learning Studio. Esto crea un módulo de conjunto de datos en el área de trabajo."
keywords: "importar datos, formato de datos, tipos de datos, orígenes de datos, datos de entrenamiento"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: c0dd9e90-23c4-4f64-8b8f-489ad79f047b
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: garye;bradsev
ms.openlocfilehash: 67f66f9b8703f2cab93a2274a90c161a55848c34
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="import-training-data-from-a-file-on-your-hard-drive-into-machine-learning-studio"></a>Importar datos de entrenamiento desde un archivo en la unidad de disco duro en Machine Learning Studio
[!INCLUDE [import-data-into-aml-studio-selector](../../../includes/machine-learning-import-data-into-aml-studio.md)]

Obtenga información sobre cómo cargar un archivo de datos desde la unidad de disco duro para usar como datos de entrenamiento en Azure Machine Learning Studio. Al importar el archivo de datos, dispone de un módulo de conjunto de datos listo para su uso en el área de trabajo.

## <a name="steps-to-import-data-from-a-local-file"></a>Pasos para importar datos desde un archivo local
Para importar datos desde una unidad de disco duro local, siga estos pasos:

1. Haga clic en **+NUEVO** en la parte inferior de la ventana de Estudio de aprendizaje automático.
2. Seleccione **CONJUNTO DE DATOS** y **DESDE ARCHIVO LOCAL**.
3. En el cuadro de diálogo **Cargar un nuevo conjunto de datos** , vaya al archivo que desea cargar.
4. Escriba un nombre, identifique el tipo de datos y, si lo desea, escriba una descripción. Se recomienda incluir una descripción: le permite registrar cualquier característica acerca de los datos que quiera recordar cuando use los datos en el futuro.
5. La casilla **Esta es la versión nueva de un conjunto de datos existente** le permite actualizar una base de datos existente con datos nuevos. Haga clic en esta casilla y, después, escriba el nombre de un conjunto de datos existente.

![Carga de un conjunto de datos nuevo](./media/import-data-from-local-file/upload-dataset.png)

Durante la carga, verá un mensaje que le indica que se está cargando el archivo. El tiempo de la carga dependerá del tamaño de los datos y de la velocidad de conexión con el servicio. Si sabe que el archivo tardará mucho tiempo en cargarse, puede realizar otras operaciones en Estudio de aprendizaje automático mientras espera. Sin embargo, si cierra el explorador, la carga de los datos genera un error.

## <a name="dataset-module-is-ready-for-use"></a>Módulo de conjunto de datos listo para usarse
Una vez que los datos estén cargados, se almacenan en un módulo de conjunto de datos y se encontrarán disponibles para cualquier experimento que se realice en su área de trabajo.

Cuando edita un experimento, puede encontrar los conjuntos de datos que ha creado en la lista **My Datasets** (Mis conjuntos de datos) que aparece en la lista **Saved Datasets** (Conjuntos de datos guardados) en la paleta de módulos. Puede arrastrar y colocar el conjunto de datos en el lienzo de experimento cuando desee usarlo para profundizar en su análisis o para Machine Learning.
