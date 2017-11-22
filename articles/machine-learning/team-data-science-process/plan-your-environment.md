---
title: "Identificación de escenarios y planeamiento del proceso analítico (Azure) | Microsoft Docs"
description: "Plan para análisis avanzado teniendo en cuenta una serie de cuestiones claves."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 421520dd-7728-4d29-889c-ebe6a0a6fb07
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: bradsev
ms.openlocfilehash: f4cf702b899b285b18c09d7a5951589d2ae71b7d
ms.sourcegitcommit: 659cc0ace5d3b996e7e8608cfa4991dcac3ea129
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/13/2017
---
# <a name="how-to-identify-scenarios-and-plan-for-advanced-analytics-data-processing"></a>Identificación de escenarios y planeamiento del procesamiento analítico avanzado de datos
¿Qué recursos debe incluir al configurar un entorno para realizar un procesamiento de análisis avanzado en un conjunto de datos? Este artículo sugiere una serie de preguntas que le ayudan a identificar las tareas y los recursos pertinentes para su escenario. El orden de los pasos generales para el análisis predictivo se describe en [¿Qué es el proceso de ciencia de datos en equipos (TDSP)?](overview.md). Cada uno de estos pasos requiere recursos específicos para las tareas pertinentes para su escenario concreto. La clave para identificar su escenario la encontrará en las cuestiones relacionadas con logística de datos, las características, la calidad de los conjuntos de datos y las herramientas y lenguajes que prefiera usar para el análisis.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="logistic-questions-data-locations-and-movement"></a>Cuestiones de logística: movimiento y ubicaciones de los datos
Las cuestiones logísticas se refieren a la ubicación del **origen de datos**, el **destino** en Azure y los requisitos para mover los datos, incluida la programación, la cantidad y los recursos implicados. Es posible que datos tengan que moverse varias veces durante el proceso de análisis. Un escenario común es mover datos locales a algún tipo de almacenamiento en Azure y, a continuación, en Machine Learning Studio.

1. **¿Cuál es el origen de datos?** ¿Es local o está la nube? Por ejemplo:
   
   * Los datos están disponibles públicamente en una dirección HTTP.
   * Los datos residen en una ubicación de archivos de red o local.
   * Los datos están en una base de datos de SQL Server.
   * Los datos se almacenan en un contenedor de almacenamiento de Azure.
2. **¿Cuál es el destino en Azure?** ¿Dónde tiene que estar para procesar o modelar? Por ejemplo:
   
   * Azure Blob Storage
   * Bases de datos SQL Azure
   * SQL Server en máquinas virtuales de Azure
   * HDInsight (Hadoop en Azure) o tablas de Hive
   * Azure Machine Learning
   * Discos duros virtuales de Azure que se pueden montar.
3. **¿Cómo va a mover los datos?** En los artículos siguientes se describen los procedimientos y los recursos disponibles para ingerir o cargar datos en una variedad de entornos de almacenamiento y procesamiento diferentes:
   
   * [Carga de datos en entornos de almacenamiento para el análisis](ingest-data.md)
   * [Importación de datos de entrenamiento en Azure Machine Learning Studio desde varios orígenes de datos](../studio/import-data.md)
4. **¿Los datos necesitan moverse siguiendo una programación regular o modificarse durante la migración?** Considere el uso de Azure Data Factory (ADF) cuando los datos tengan que migrarse continuamente, especialmente en el caso de un escenario híbrido en el que se tenga acceso a recursos locales y en la nube, o cuando se realice una transacción de datos o estos tengan que modificarse o tener lógica de negocios agregada mientras se migran. Para más información, consulte [Mover datos desde un servidor SQL Server local a SQL Azure con Azure Data Factory](move-sql-azure-adf.md)
5. **¿Qué cantidad de datos se va a mover a Azure?** Los conjuntos de datos sumamente grandes pueden superar la capacidad de almacenamiento de ciertos entornos. Para ver un ejemplo, consulte la explicación de los límites de tamaño para Machine Learning Studio en la sección siguiente. En tales casos, puede usarse una muestra de los datos durante el análisis. Para obtener más información sobre cómo reducir la muestra de un conjunto de datos en diversos entornos de Azure, consulte [Muestreo de datos del proceso de ciencia de datos en equipos](sample-data.md).

## <a name="data-characteristics-questions-type-format-and-size"></a>Cuestiones sobre las características de los datos: tipo, formato y tamaño
Estas cuestiones son clave para planear los entornos de almacenamiento y procesamiento, cada uno de los cuales es adecuado para diversos tipos de datos y cada uno cuenta con ciertas restricciones.

1. **¿Cuáles son los tipos de datos?** Por ejemplo:
   
   * Numérico
   * Categorías
   * Cadenas
   * Binary
2. **¿Qué formato tienen los datos?** Por ejemplo:
   
   * Archivos sin formato separados por comas (CSV) o separados por tabulaciones (TSV)
   * Comprimidos o sin comprimir
   * Blobs de Azure
   * Tablas de Hadoop Hive
   * Tablas de SQL Server
3. **¿Qué tamaño tienen los datos?**
   
   * Pequeño: menos de 2 GB
   * Medio: más de 2 GB y menos de 10 GB
   * Grande: más de 10 GB

Veamos por ejemplo Azure Machine Learning Studio:

* Para obtener una lista de los formatos de datos y los tipos admitidos por Azure Machine Learning Studio, consulte la sección [Tipos y formatos de datos admitidos](../studio/import-data.md#data-formats-and-data-types-supported) .
* Para más información sobre las limitaciones en los datos para Azure Machine Learning Studio, consulte la sección **¿Cómo de grande puede ser el conjunto de datos para mis módulos?** en [Importación y exportación de datos para Machine Learning](../studio/faq.md#machine-learning-studio-questions)

Para obtener más información sobre las limitaciones de otros servicios de Azure usados en el proceso de análisis, consulte [Límites, cuotas y restricciones de suscripción y servicios de Microsoft Azure](../../azure-subscription-service-limits.md).

## <a name="data-quality-questions-exploration-and-pre-processing"></a>Cuestiones sobre la calidad de los datos: exploración y procesamiento previo
1. **¿Qué sabe acerca de los datos?** Explore los datos para obtener un conocimiento de sus características básicas. ¿Qué patrones o tendencias muestran, qué valores atípicos tienen o cuántos valores faltan? Este paso es importante para determinar el alcance de procesamiento previo necesario, para la formulación de hipótesis que podrían sugerir las características o el tipo de análisis más apropiados y para elaborar planes de recopilación de datos adicionales. Calcular estadísticas descriptivas y trazar visualizaciones son técnicas útiles para la inspección de datos. Para obtener más información sobre cómo explorar un conjunto de datos en diversos entornos de Azure, consulte [Exploración de datos en el proceso de ciencia de datos en equipos](explore-data.md).
2. **¿Los datos requieren un procesamiento previo o una limpieza?**
   El preprocesamiento y la limpieza de datos son tareas importantes que normalmente se deben llevar a cabo para que el conjunto de datos se pueda usar de forma eficaz para el aprendizaje automático. Los datos sin procesar son a menudo ruidosos no confiables y es posible que les falten valores. El uso de estos datos para el modelado puede producir resultados engañosos. Para ver una descripción, consulte [Tareas para preparar los datos para el aprendizaje automático mejorado](prepare-data.md).

## <a name="tools-and-languages-questions"></a>Cuestiones sobre herramientas y lenguajes
Hay muchas opciones dependiendo de qué lenguajes y entornos de desarrollo o herramientas necesita o prefiere usar.

1. **¿Qué lenguajes prefiere usar para el análisis?**  
   
   * R
   * Python
   * SQL
2. **¿Qué herramientas debe usar para analizar los datos?**
   
   * [Microsoft Azure Powershell](/powershell/azure/overview) : un lenguaje de script que se usa para administrar los recursos de Azure en un lenguaje de script.
   * [Azure Machine Learning Studio](../studio/what-is-ml-studio.md)
   * [Revolution Analytics](http://www.revolutionanalytics.com/revolution-r-open)
   * [RStudio](http://www.rstudio.com)
   * [Python Tools para Visual Studio](http://microsoft.github.io/PTVS/)
   * [Anaconda](https://www.continuum.io/why-anaconda)
   * [Jupyter Notebooks](http://jupyter.org/)
   * [Microsoft Power BI](http://powerbi.microsoft.com)

## <a name="identify-your-advanced-analytics-scenario"></a>Identificación del escenario de análisis avanzado
Cuando haya respondido a las preguntas de la sección anterior, estará listo para determinar qué escenario se adapta mejor a su caso. Los escenarios de ejemplo que se describen en [Escenarios para análisis avanzado en Azure Machine Learning](plan-sample-scenarios.md).

