---
title: "Predicción del abandono de clientes con Azure Machine Learning | Microsoft Docs"
description: "Describe cómo realizar análisis de abandono mediante Azure ML Workbench."
services: machine-learning
documentationcenter: 
author: miprasad
manager: kristin.tolle
editor: miprasad
ms.assetid: 
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2017
ms.author: miprasad
ms.openlocfilehash: 7db93786b71fb7876ae02fd8fd006a1e8e0c2271
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/18/2017
---
# <a name="customer-churn-prediction-using-azure-machine-learning"></a>Predicción del abandono de clientes con Azure Machine Learning

En promedio, mantener los clientes existentes es cinco veces más económico que el costo de incorporar nuevos. Como resultado, los ejecutivos de marketing a menudo intentar calcular la probabilidad de abandono de clientes y buscan las acciones necesarias para minimizar la tasa de abandono.

El objetivo de esta solución es mostrar el análisis predictivo de abandono mediante el uso de Azure Machine Learning Workbench. Esta solución proporciona una plantilla fácil de usar para desarrollar canalizaciones de datos predictivos de abandono para distribuidores. La plantilla se puede usar con diferentes conjuntos de datos y definiciones diferentes de abandono. El objetivo del ejemplo práctico es:

1. Conocer las herramientas de preparación de datos de Azure Machine Learning Workbench para limpiar y recopilar datos de la relación con clientes para el análisis de abandono.

2. Realizar la transformación de características para administrar datos heterogéneos con ruido.

3. Integrar bibliotecas de terceros (como `scikit-learn` y `azureml`) para desarrollar clasificadores bayesianos y basados en árbol para predecir el abandono.

4. Implementar.

## <a name="link-of-the-gallery-github-repository"></a>Vínculo al repositorio de GitHub de la galería
A continuación se muestra el vínculo al repositorio de GitHub público donde se hospeda todo el código:

[https://github.com/Azure/MachineLearningSamples-ChurnPrediction](https://github.com/Azure/MachineLearningSamples-ChurnPrediction)

## <a name="use-case-overview"></a>Información general del caso de uso
Las empresas necesitan una estrategia eficaz para administrar el abandono de clientes. El abandono de clientes incluye a los clientes que cesan el uso de un servicio, cambiando a un servicio de un competidor, cambiando a una experiencia de nivel inferior en el servicio o reduciendo la interacción con el servicio.

En este caso de uso, nos centramos en datos de la empresa de telecomunicaciones francesa Orange para identificar a los clientes con probabilidad de abandono a corto plazo con el fin de mejorar el servicio y crear campañas de extensión personalizada que ayuden a mantener los clientes.

Las compañías de telecomunicaciones se enfrentan a un mercado competitivo. Muchas compañías tienen pérdida de ingresos de clientes de post-pago debido al abandono. Por lo tanto, la capacidad de identificar con precisión el abandono de clientes puede ser una enorme ventaja competitiva.

Algunos de los factores que contribuyen al abandono de clientes de telecomunicaciones son:

* Percepción de interrupciones del servicio frecuentes
* Experiencias de servicio al cliente negativas en tiendas minoristas y en línea
* Ofertas de otros operadores de la competencia (mejor plan de familia, plan de datos, etc).

En esta solución, se usará un ejemplo concreto de creación de un modelo predictivo de abandono de clientes para compañías de telecomunicaciones.

## <a name="prerequisites"></a>requisitos previos

* Una [cuenta de Azure](https://azure.microsoft.com/free/) (hay disponibles versiones gratuitas de prueba)

* Una copia instalada de [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md) siguiendo la [Guía de instalación de inicio rápido](./quickstart-installation.md) para instalar el programa y crear un área de trabajo

* Para la operacionalización, es mejor si dispone del motor de Docker instalado y en ejecución local. De lo contrario, puede utilizar la opción de un clúster, pero tenga en cuenta que la ejecución en Azure Container Service (ACS) puede resultar costosa.

* En esta solución se da por supuesto que está ejecutando Azure Machine Learning Workbench en Windows 10 con el motor de Docker instalado localmente. Si usa macOS, las instrucciones son en gran medida las mismas.

## <a name="create-a-new-workbench-project"></a>Creación de un nuevo proyecto de Workbench

Cree un nuevo proyecto usando este ejemplo como plantilla:
1.  Abra Azure Machine Learning Workbench
2.  En la página **Proyectos**, haga clic en el signo **+** y seleccione **Nuevo proyecto**
3.  En el panel **Crear nuevo proyecto**, rellene la información del proyecto nuevo
4.  En el cuadro de búsqueda **Buscar plantillas de proyecto**, escriba "Customer Churn Prediction" (Predicción de abandono de clientes) y seleccione la plantilla
5.  Haga clic en **Crear**

## <a name="data-description"></a>Descripción de los datos

El conjunto de datos utilizado en la solución procede de la competición SIDKDD 2009. Se llama `CATelcoCustomerChurnTrainingSample.csv` y se encuentra en la carpeta [`data` ](https://github.com/Azure/MachineLearningSamples-ChurnPrediction/tree/master/data). El conjunto de datos consta de datos heterogéneos con ruido (variables numéricas y de categorías) de la empresa de telecomunicaciones francesa Orange y es anónimo.

Las variables de capturan información demográfica del cliente, estadísticas de llamadas (por ejemplo, duración media de la llamada, porcentaje de errores de llamada, etc.), información de contratos y estadísticas de reclamaciones. La variable de abandono es binaria (0: no abandona, 1: abandona).

## <a name="scenario-structure"></a>Estructura del escenario

La estructura de carpetas se organiza de la siguiente forma:

__data__: contiene el conjunto de datos utilizado en la solución  

__docs__: contiene todos los laboratorios prácticos

El orden de los laboratorios prácticos para llevar a cabo la solución es el siguiente:
1. Preparación de datos: el archivo principal relacionado con la preparación de los datos en la carpeta data es `CATelcoCustomerChurnTrainingSample.csv`
2. Modelado y evaluación: el archivo principal relacionado con el modelado y la evaluación en la carpeta raíz es `CATelcoCustomerChurnModeling.py`
3. Modelado y evaluación sin .dprep: el archivo principal para esta tarea en la carpeta raíz es `CATelcoCustomerChurnModelingWithoutDprep.py`
4. Operacionalización: los archivos principales para implementar son el modelo (`model.pkl`) y `churn_schema_gen.py`

| Orden| Nombre de archivo | Archivos relacionados |
|--|-----------|------|
| 1 | [`DataPreparation.md`](https://github.com/Azure/MachineLearningSamples-ChurnPrediction/blob/master/docs/DataPreparation.md) | 'data/CATelcoCustomerChurnTrainingSample.csv' |
| 2 | [`ModelingAndEvaluation.md`](https://github.com/Azure/MachineLearningSamples-ChurnPrediction/blob/master/docs/ModelingAndEvaluation.md) | 'CATelcoCustomerChurnModeling.py' |
| 3 | [`ModelingAndEvaluationWithoutDprep.md`](https://github.com/Azure/MachineLearningSamples-ChurnPrediction/blob/master/docs/ModelingAndEvaluationWithoutDprep.md) | 'CATelcoCustomerChurnModelingWithoutDprep.py' |
| 4 | [`Operationalization.md`](https://github.com/Azure/MachineLearningSamples-ChurnPrediction/blob/master/docs/Operationalization.md) | 'model.pkl'<br>'churn_schema_gen.py' |

Siga los laboratorios de la manera secuencial que se ha descrito anteriormente.

## <a name="conclusion"></a>Conclusión
Este escenario práctico muestra cómo llevar a cabo predicciones de abandono con Azure Machine Learning Workbench. En primer lugar se ha realizado la limpieza de los datos para controlar los datos con ruido y heterogéneos, seguido de ingeniería de características con herramientas de preparación de datos. A continuación, se utilizan herramientas de aprendizaje automático de código abierto para crear y evaluar un modelo de clasificación y, a continuación, se usa un contenedor de Docker local para implementar el modelo, que está listo para producción.
