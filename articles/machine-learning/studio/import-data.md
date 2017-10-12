---
title: "Importación de datos en Machine Learning Studio | Microsoft Docs"
description: "Cómo importar los datos en Estudio de aprendizaje automático de Azure desde varios orígenes de datos. Obtenga información sobre qué tipos de datos y formatos de datos son compatibles."
keywords: "importar datos, formato de datos, tipos de datos, orígenes de datos, datos de entrenamiento"
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: c194ee3b-838c-4efe-bb2a-c1d052326216
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: garye;bradsev
ms.openlocfilehash: 98a8fa15922ef100a7f8cadd92fb7ffef0d3bcde
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="import-your-training-data-into-azure-machine-learning-studio-from-various-data-sources"></a>Importación de datos de entrenamiento en Estudio de aprendizaje automático de Azure desde varios orígenes de datos
Para usar sus propios datos en Estudio de aprendizaje automático para desarrollar y entrenar una solución de análisis predictivo, puede: 

* Cargar datos de un **archivo local** con antelación desde el disco duro para crear un módulo de conjunto de datos en el área de trabajo.
* Acceder a los datos desde cualquiera de los **orígenes de datos** en línea mientras su experimento se ejecuta con el módulo [Importar datos][import-data]. 
* Usar datos de otro **experimento** de Azure Machine Learning guardado como un conjunto de datos.
* Usar los datos de instancia local de **SQL Server Database**.

Cada una de estas opciones se describen en uno de los temas del menú inferior. En estos temas se muestra cómo importar datos desde estos diversos orígenes de datos para usarlos en Machine Learning Studio. 

[!INCLUDE [import-data-into-aml-studio-selector](../../../includes/machine-learning-import-data-into-aml-studio.md)]

> [!NOTE]
> Existe una gran variedad de conjuntos de datos de ejemplo disponibles en Machine Learning Studio que puede usar como datos de aprendizaje. Para obtener información al respecto, consulte [Uso de los conjuntos de datos de ejemplo en Estudio de aprendizaje automático de Azure](use-sample-datasets.md).
> 
> 

En este tema de introducción también se explica cómo obtener datos listos para su uso en Machine Learning Studio de aprendizaje automático y se describe qué tipos y formatos de datos son compatibles. 

> [!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]
> 
> 

## <a name="get-data-ready-for-use-in-azure-machine-learning-studio"></a>Preparación de los datos para usarlos en Estudio de aprendizaje automático de Azure
Estudio de aprendizaje automático está diseñado para trabajar con datos rectangulares o tabulares, como datos de texto delimitados o datos estructurados de una base de datos, aunque en algunas circunstancias, es posible usar datos no rectangulares.

Se recomienda que los datos estén relativamente limpios. Es decir, querrá ocuparse de problemas como las cadenas sin comillas antes de cargar los datos en su experimento.

Sin embargo, hay módulos disponibles en Estudio de aprendizaje automáticos que le permitirán manipular levemente los datos en el experimento. Dependiendo de los algoritmos de aprendizaje automático que usará, es posible que deba decidir cómo controlar los problemas estructurales de los datos, como valores que faltan y datos esparcidos y existen módulos que pueden ayudar en esto. Observe la sección **Transformación de datos** de la paleta de módulos para los módulos que realizan estas funciones.

En cualquier momento del experimento puede ver o descargar los datos que genera un módulo con un clic en el puerto de salida. Dependiendo del módulo, es posible que haya distintas opciones de descarga disponibles, o bien que se puedan visualizar los datos dentro del explorador web en Machine Learning Studio.

## <a name="data-formats-and-data-types-supported"></a>Tipos y formatos de datos admitidos
Puede importar diversos tipos de datos al experimento, dependiendo del mecanismo que usa para importar los datos y de dónde provienen estos:

* Texto sin formato (.txt)
* Valores separados por coma (CSV) con un encabezado (.csv) o sin encabezado (.nh.csv)
* Valores separados con tabulaciones (TSV) con un encabezado (.tsv) o sin encabezado (.nh.tsv)
* Archivo de Excel
* Tabla de Azure
* Tabla de Hive
* Tabla de Base de datos SQL
* Valores de OData
* Datos SVMLight (.svmlight) (consulte la [definición de SVMLight](http://svmlight.joachims.org/) para obtener más información sobre el formato)
* Datos de formato de archivo con relación de atributo (ARFF) (.arff) (consulte la [definición de ARFF](http://weka.wikispaces.com/ARFF) si desea ver información sobre el formato)
* Archivo ZIP (.zip)
* Archivo de área de trabajo u objeto de R (.RData)

Si importa datos en un formato distinto de ARFF que incluyan metadatos, Estudio de aprendizaje automático usa estos metadatos para definir el tipo de datos y encabezado de cada columna.

Si importa datos en formato TSV o CSV que no incluyan estos metadatos, Estudio de aprendizaje automático infiere el tipo de datos de cada columna tomando una muestra de los mismos. Si los datos no tienen encabezados de columna, Estudio de aprendizaje automático proporciona nombres predeterminados.

Puede especificar o cambiar explícitamente los encabezados y los tipos de datos de las columnas usando el módulo [Editar metadatos][edit-metadata].

Estudio de aprendizaje automático reconoce los siguientes **tipos de datos** :

* String
* Entero
* Doble
* Booleano
* DateTime
* TimeSpan

Machine Learning Studio usa un tipo de datos interno llamado ***Tabla de datos*** para pasar datos entre los módulos. Puede convertir explícitamente sus datos en formato de tabla de datos con el módulo [Convertir al conjunto de datos][convert-to-dataset].

Todo módulo que acepta formatos distintos de Tabla de datos convertirá los datos a Tabla de datos de manera silenciosa antes de pasarlos al módulo siguiente.

En caso de ser necesario, puede convertir el formato Tabla de datos de vuelta al formato CSV, TSV, ARFF o SVMLight mediante el uso de otros módulos de conversión.
Consulte la sección **Conversiones de formatos de datos** de la paleta de módulos para ver los módulos que realizan estas funciones.

<!-- Module References -->
[convert-to-dataset]: https://msdn.microsoft.com/library/azure/72bf58e0-fc87-4bb1-9704-f1805003b975/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
