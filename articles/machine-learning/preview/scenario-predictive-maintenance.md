--- 
title: Escenario real de mantenimiento predictivo | Microsoft Docs
description: Escenario real de mantenimiento predictivo con PySpark
services: machine-learning
author: jaymathe
ms.author: jaymathe
manager: jhubbard
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.custom: mvc
ms.date: 09/25/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 7bb5672a2b9de0a62af8b6155bfdead6a0105eb5
ms.contentlocale: es-es
ms.lasthandoff: 09/25/2017

--- 

# <a name="predictive-maintenance-real-world-scenario"></a>Escenario real de mantenimiento predictivo


El impacto del tiempo de inactividad no programado de los equipos puede ser perjudicial para las empresas. Es fundamental, por tanto, mantener el equipamiento de campo en funcionamiento para maximizar la utilización y el rendimiento y minimizar el costoso tiempo de inactividad no programado. La identificación temprana de los problemas le ayuda a asignar recursos de mantenimiento limitado de una forma más rentable y a mejorar la calidad y los procesos de la cadena de suministro. 

En este escenario, usamos [datos a gran escala](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data) relativamente extensos para guiar al usuario a través de los pasos principales de ingesta de datos, ingeniería de características, creación del modelo y, por último, operacionalización e implementación del modelo. El código para todo el proceso está escrito en PySpark y se implementa mediante Jupyter Notebooks en Azure ML Workbench. El mejor modelo finalmente se operacionaliza mediante la Administración de modelos de Azure Machine Learning para su uso en un entorno de producción para realizar predicciones de error en tiempo real.   

## <a name="link-to-the-gallery-github-repository"></a>Vínculo al repositorio de GitHub de la galería

A continuación se muestra el vínculo en el repositorio público de GitHub: [https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance)


## <a name="use-case-overview"></a>Información general del caso de uso

Un problema importante al que se enfrentan las empresas en sectores de uso intensivo de activos son los considerables costos asociados a retrasos por problemas mecánicos. La mayoría de las empresas están interesadas en la predicción de cuándo se producen estos problemas con el fin de evitarlos proactivamente antes de que ocurran. Esto reduce los costos al reducir el tiempo de inactividad y, en algunos casos, aumenta la seguridad. Consulte la [Guía para mantenimiento predictivo](https://docs.microsoft.com/en-us/azure/machine-learning/cortana-analytics-playbook-predictive-maintenance) para obtener una explicación detallada de casos de uso comunes y del enfoque de modelado para mantenimiento predictivo.

Este escenario aprovecha las ideas de la guía con el fin de proporcionar los pasos para implementar un modelo de predicción para un escenario, que se basa en una síntesis de varios problemas empresariales reales. Este ejemplo combina los elementos de datos comunes observados entre muchos casos de uso de mantenimiento predictivo.

El problema empresarial para estos datos simulados es predecir problemas causados por errores en los componentes. Por lo tanto, la pregunta empresarial es "*¿cuál es la probabilidad de que un equipo deje de funcionar debido a un error de un componente?*". Este problema tiene el formato de un problema de clasificación multiclase (varios componentes por equipo) y se utiliza un algoritmo de aprendizaje automático para crear el modelo de predicción. El modelo se entrena con datos históricos recopilados de los equipos. En este escenario, el usuario pasa por las distintas fases de la implementación de este tipo de modelo en el entorno de Azure Machine Learning Workbench.

## <a name="prerequisites"></a>Requisitos previos

* Una [cuenta de Azure](https://azure.microsoft.com/en-us/free/) (hay disponibles versiones gratuitas de prueba).
* Una copia instalada de [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md) siguiendo la [Guía de instalación de inicio rápido](./quickstart-installation.md) para instalar el programa y crear un área de trabajo.
* Los resultados intermedios utilizados por los Jupyter Notebooks en este escenario se almacenan en un contenedor de Azure Blob Storage. Las instrucciones para configurar una cuenta de Azure Storage están disponibles en este [vínculo](https://docs.microsoft.com/en-us/azure/storage/blobs/storage-python-how-to-use-blob-storage). 
* Para la [operacionalización](https://github.com/Azure/Machine-Learning-Operationalization) del modelo, es mejor si el usuario dispone de un [motor de Docker](https://www.docker.com/) instalado y en ejecución de modo local. De lo contrario, puede utilizar la opción de un clúster, pero tenga en cuenta que la ejecución en [Azure Container Service (ACS)](https://azure.microsoft.com/en-us/services/container-service/) puede resultar costosa.
* En este escenario se da por supuesto que el usuario está ejecutando Azure ML Workbench en un equipo Windows 10 con el motor de Docker instalado localmente. 
* El escenario se ha creado y probado en un equipo Windows 10 con las siguientes especificaciones: CPU de Intel Core i7 4600U @ 2.10 GHz, 8 GB de RAM, sistema operativo de 64 bits, procesador x64 con Docker versión 17.06.0-ce-win19 (12801). 
* La operacionalización del modelo se realizó con esta versión de la CLI de Azure ML: azure-cli-ml==0.1.0a22

## <a name="create-a-new-workbench-project"></a>Creación de un nuevo proyecto de Workbench

Cree un nuevo proyecto usando este ejemplo como plantilla:
1.  Abra Azure Machine Learning Workbench
2.  En la página **Proyectos**, haga clic en el signo **+** y seleccione **Nuevo proyecto**
3.  En el panel **Crear nuevo proyecto**, rellene la información del proyecto nuevo
4.  En el cuadro de búsqueda **Buscar plantillas de proyecto**, escriba "Predictive Maintenance" (Mantenimiento predictivo) y seleccione la plantilla
5.  Haga clic en **Crear**

## <a name="data-description"></a>Descripción de los datos

Los [datos simulados](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data) se componen de cinco archivos de valores separados por comas (.csv). 

* [Máquinas](https://pdmmodelingguide.blob.core.windows.net/pdmdata/machines.csv): características diferenciales de cada máquina. Por ejemplo, la edad y el modelo.
* [Error](https://pdmmodelingguide.blob.core.windows.net/pdmdata/errors.csv): el registro de errores contiene errores que no causan interrupción producidos mientras el equipo todavía está operativo. Estos errores no se consideran como tal, aunque pueden ser predicción de un evento de error en el futuro. La fecha y hora del error se redondean a la hora más cercana, ya que los datos de telemetría se recopilan cada hora.
* [Mantenimiento](https://pdmmodelingguide.blob.core.windows.net/pdmdata/maint.csv): el registro de mantenimiento contiene registros de mantenimiento programado y no programado. El mantenimiento programado se corresponde con la inspección periódica de los componentes, el mantenimiento no programado puede surgir ante errores mecánicos u otra degradación del rendimiento. La fecha y hora del mantenimiento se redondean a la hora más cercana, ya que los datos de telemetría se recopilan cada hora.
* [Telemetría](https://pdmmodelingguide.blob.core.windows.net/pdmdata/telemetry.csv): los datos de series temporales de telemetría constan de voltaje, rotación, presión y mediciones del sensor de vibración recopiladas de cada máquina en tiempo real. Los datos se promedian sobre una hora y se almacenan en los registros de telemetría
* [Errores](https://pdmmodelingguide.blob.core.windows.net/pdmdata/failures.csv): los errores corresponden a reemplazos de componentes dentro del registro de mantenimiento. Cada registro contiene el identificador de máquina, el tipo de componente y la fecha y hora de reemplazo. Estos registros se utilizan para crear las etiquetas de aprendizaje automático que está intentando predecir el modelo.

Consulte la [Ingesta de datos](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb) del escenario del Jupyter Notebook en la sección de código para descargar los conjuntos de datos sin formato desde el repositorio de GitHub y crear los conjuntos de datos de PySpark para este análisis.

## <a name="scenario-structure"></a>Estructura del escenario
El contenido del escenario está disponible en el [repositorio de GitHub](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance). 

En el repositorio, hay un archivo [Léame](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/README.md) que describe los procesos desde la preparación de los datos hasta la creación de algunos modelos y, finalmente, la operacionalización de uno de los mejores modelos. Los cuatro Jupyter Notebooks están disponibles en la carpeta [Code](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/tree/master/Code) en el repositorio.   

A continuación se describe el flujo de trabajo del escenario paso a paso. El escenario de extremo a extremo está escrito en PySpark y se divide en cuatro Notebooks, tal como se describe a continuación:

* [Ingesta de datos](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb): este Notebook controla la ingesta de datos de los cinco archivos .csv de entrada, realiza una limpieza preliminar, crea algunos gráficos de resumen para comprobar la descarga de los datos y, finalmente, almacena los conjuntos de datos resultantes en un contenedor de blobs de Azure para su uso por el Notebook siguiente.

* [Ingeniería de características](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/2_feature_engineering.ipynb): utilizando el conjunto de datos limpios del paso anterior, se crean las características de desfase para los sensores de telemetría, junto con otra ingeniería de características para crear variables como los días desde el último reemplazo. Por último, los errores se etiquetan en los registros pertinentes para crear un conjunto de datos final, que se guarda en un blob de Azure para el paso siguiente. 

* [Creación del modelo](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/3_model_building.ipynb): el conjunto de datos final con ingeniería de características, a continuación, se divide en dos conjuntos de datos, de entrenamiento y de prueba, en función de una marca de fecha y hora. A continuación se crean dos modelos, el clasificador de bosque aleatorio y el clasificador de árbol de decisión, sobre del conjunto de datos de entrenamiento y se puntúan sobre el conjunto de datos de prueba. 

* [Operacionalización e implementación del modelo](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/4_operationalization.ipynb): el mejor modelo creado en el paso anterior, a continuación, se guarda como un archivo .model junto con el archivo de esquema .json relevante para la implementación. En primer lugar, las funciones init() y run() se prueban localmente antes de la operacionalización del modelo, utilizando el entorno de Administración de modelos de Azure Machine Learning para su uso en un entorno de producción para realizar predicciones de error de en tiempo real.  

## <a name="conclusion"></a>Conclusión

Este escenario proporciona al lector una visión general de cómo crear una solución de extremo a extremo de mantenimiento predictivo usando PySpark en el entorno de Jupyter Notebook en Azure ML Workbench. El escenario también guía al lector sobre cómo operacionalizar fácilmente el mejor modelo e implementarlo con el entorno de Administración de modelos de Azure Machine Learning para su uso en un entorno de producción para hacer predicciones de error en tiempo real. A continuación, el lector puede editar las partes relevantes del escenario para ajustarlo a sus necesidades empresariales.  

## <a name="references"></a>Referencias

Este caso de uso se ha desarrollado previamente en varias plataformas como se muestra a continuación:

* [Plantilla de solución de mantenimiento predictivo](https://docs.microsoft.com/en-us/azure/machine-learning/cortana-analytics-playbook-predictive-maintenance)
* [Guía de modelado de mantenimiento predictivo](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1)
* [Guía de modelado de mantenimiento predictivo con SQL R Services](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-Modeling-Guide-using-SQL-R-Services-1)
* [Guía de modelado de mantenimiento predictivo con Python Notebook](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-Python-Notebook-1)
* [Mantenimiento predictivo con PySpark](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-using-PySpark)



