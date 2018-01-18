---
title: 'Aprendizaje profundo para escenarios reales de mantenimiento predictivo: Azure | Microsoft Docs'
description: Aprenda a replicar el tutorial de aprendizaje profundo para un mantenimiento predictivo con Azure Machine Learning Workbench.
services: machine-learning
author: FrancescaLazzeri
ms.author: Lazzeri
manager: ireiter
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 11/22/2017
ms.openlocfilehash: a55209256c29fa62cc2da72f9653fbc7fc0e7c54
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2018
---
# <a name="deep-learning-for-predictive-maintenance-real-world-scenarios"></a>Aprendizaje profundo para escenarios reales de mantenimiento predictivo

El aprendizaje profundo es una de las tendencias más populares en el aprendizaje automático. El aprendizaje profundo se usa en muchos campos y aplicaciones, incluidos los vehículos sin conductor, reconocimiento de voz y de imagen, robótica y finanzas. Se trata de un conjunto de algoritmos que está inspirado en la forma del cerebro (redes neuronales biológicas) y el aprendizaje automático. Los científicos cognitivos normalmente se refieren al aprendizaje profundo como redes neuronales artificiales (ANN).

El mantenimiento predictivo es también una tendencia popular. En el mantenimiento predictivo, se diseñan muchas técnicas diferentes para ayudar a determinar el estado del equipo y predecir cuándo debe realizarse el mantenimiento. Algunos usos habituales del mantenimiento predictivo son predicción de fallos, diagnostico de errores (análisis de causa raíz), detección de errores, clasificación de tipo de error y recomendación de acciones de mitigación o mantenimiento después de un error.

En los escenarios de mantenimiento predictivo, los datos se recopilan a lo largo del tiempo para supervisar el estado del equipo. El objetivo es identificar patrones que pueden ayudar a predecir y en última instancia a evitar errores. El uso de redes [de memoria a corto y largo plazo (LSTM)](http://colah.github.io/posts/2015-08-Understanding-LSTMs/) es un método de aprendizaje profundo que es especialmente atractivo en el mantenimiento predictivo. Las redes LSTM aprenden bien a partir de secuencias. Los datos de serie temporal pueden utilizarse para mirar hacia atrás a períodos más largos de tiempo para detectar patrones de error.

En este tutorial, se crea una red LSTM para el conjunto de datos y el escenario que se describen en [Mantenimiento predictivo](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Template-3). La red se usa para predecir la vida útil de motores de avión. La plantilla utiliza valores del sensor de avión simulados para predecir cuándo un motor de avión fallará en el futuro. Con esta predicción el mantenimiento se puede planear de antemano, evitando así los fallos.

Este tutorial usa la biblioteca de aprendizaje profundo [Keras](https://keras.io/) y Microsoft Cognitive Toolkit [CNTK](https://docs.microsoft.com/cognitive-toolkit/Using-CNTK-with-Keras) como back-end.

El repositorio público de GitHub que tiene los ejemplos de este tutorial está en [https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance](https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance).

## <a name="use-case-overview"></a>Información general del caso de uso

Este tutorial usa el ejemplo de eventos "de funcionamiento a fallo" de un motor de avión simulado para demostrar el proceso de modelado de mantenimiento predictivo. 

La presunción implícita de los datos de modelado que se describe aquí es que hay un patrón de degradación en marcha en el recurso. El patrón se refleja en las mediciones del sensor del recurso. Mediante el examen de los valores del sensor del recurso a lo largo de un tiempo, el algoritmo de aprendizaje de la máquina puede obtener información sobre la relación entre los valores del sensor, los cambios en los valores del sensor y los errores que se han registrado en el pasado. Esta relación se usa para predecir los errores en el futuro. 

Le recomendamos que examine el formato de datos y complete los tres pasos de la plantilla antes de reemplazar los datos del ejemplo con sus propios datos.

## <a name="prerequisites"></a>Requisitos previos

- Una [cuenta de Azure](https://azure.microsoft.com/free/) (hay disponibles versiones gratuitas de prueba).
- Azure Machine Learning Workbench con un área de trabajo creada.
- Para la operacionalización del modelo: la operacionalización de Azure Machine Learning con una configuración de entorno de implementación local y una cuenta de [Administración de modelos de Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/preview/model-management-overview).

## <a name="create-a-new-workbench-project"></a>Creación de un nuevo proyecto de Workbench

Cree un proyecto usando este ejemplo como plantilla:

1. Abra Machine Learning Workbench.
2. En la página **Proyectos**, seleccione **+** y luego **Nuevo proyecto**.
3. En el panel **Crear nuevo proyecto**, especifique la información del nuevo proyecto.
4. En el cuadro de búsqueda **Buscar plantillas de proyecto**, escriba **Mantenimiento predictivo** y seleccione la plantilla.
5. Seleccione **Crear**.

## <a name="prepare-the-notebook-server-computation-target"></a>Preparación del destino de cálculo del servidor de Notebook

En el equipo local, en el menú **Archivo** de Machine Learning Workbench, seleccione **Abrir símbolo del sistema** o **Abrir PowerShell**. En la ventana del símbolo del sistema de la opción que elija, ejecute los comandos siguientes:

`az ml experiment prepare --target docker --run-configuration docker`

Le recomendamos que ejecute el servidor de Notebook en una instancia de [Data Science Virtual Machine (DSVM) para Linux (Ubuntu)](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu) DS4_V2 estándar. Después de que la DSVM esté configurada, ejecute los siguientes comandos para preparar las imágenes de Docker:

`az ml computetarget attach remotedocker --name [connection_name] --address [VM_IP_address] --username [VM_username] --password [VM_password]`

`az ml experiment prepare --target [connection_name] --run-configuration [connection_name]`

Con las imágenes de Docker preparadas, abra el servidor de Jupyter Notebook. Para abrir el servidor de Jupyter Notebook, vaya a la pestaña **Notebooks** de Machine Learning Workbench, o inicie un servidor basado en explorador: `az ml notebook start`

Los Notebooks se almacenan en el directorio Code del entorno de Jupyter. Ejecute estos Notebooks siguiendo la secuencia de su numeración, empezando con Code\1_data_ingestion_and_preparation.ipynb.

Seleccione el kernel que coincida con su [Nombre_proyecto]_Plantilla [nombre_conexión] y, a continuación, seleccione **Set Kernel** (Establecer kernel).

## <a name="data-description"></a>Descripción de los datos

La plantilla utiliza tres conjuntos de datos como entradas en los archivos PM_train.txt, PM_test.txt y PM_truth.txt.

-  **Datos de aprendizaje**: los datos "de funcionamiento a fallo" del motor de avión. El conjunto de datos de aprendizaje (PM_train.txt) se compone de varias series de tiempo multivariadas, con *ciclo* como unidad de tiempo. Incluye 21 lecturas de sensor para cada ciclo. 

    Cada serie temporal se supone que se generara a partir de un motor diferente del mismo tipo. Se supone que cada motor se inicia con diferentes grados de desgaste inicial y variación de fabricación. Esta información es desconocida para el usuario. 

    En estos datos simulados, se supone que el motor funciona normalmente al principio de cada serie temporal. En algún momento durante la serie de los ciclos de funcionamiento se inicia la degradación. La degradación progresa y aumenta en magnitud. 

    Cuando se alcanza un umbral predefinido, se considera que no es seguro que el motor siga funcionando. El último ciclo de cada serie temporal puede considerarse como el punto de fallo del motor correspondiente.

-   **Datos de prueba**: los datos de funcionamiento del motor del avión sin eventos de fallo registrados. Los datos de prueba (PM_test.txt) tienen el mismo esquema de datos que los datos de aprendizaje. La única diferencia es que los datos no indican cuándo se produce el error (el último período de tiempo *no* representan el punto de fallo). No se sabe cuántos ciclos más puede durar este motor antes de que se produzca un fallo.

- **Datos verdaderos**: la información de los ciclos restantes verdaderos para cada motor en los datos de prueba. Los datos verdaderos y precisos proporcionan el número de ciclos de trabajo restantes para los motores de los datos de prueba.

## <a name="scenario-structure"></a>Estructura del escenario

El contenido para el escenario está disponible en [repositorio de GitHub] (https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance). 

En el repositorio un archivo [Léame] (https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance/blob/master/README.md) describe los procesos, desde preparación de los datos a la creación y puesta en marcha del modelo. Los tres Jupyter Notebooks están disponibles en la carpeta [Code] (https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance/tree/master/Code) en el repositorio. 

A continuación se describe el flujo de trabajo del escenario paso a paso.

### <a name="task-1-data-ingestion-and-preparation"></a>Tarea 1: Ingesta y preparación de datos

El Jupyter Notebook de ingesta de datos en Code/1_data_ingestion_and_preparation.ipnyb carga los tres conjuntos de datos de entrada en formato de trama de datos de Pandas. A continuación, prepara los datos para el modelado y realiza la visualización de algunos datos preliminares. Luego los datos se transforma al formato PySpark y se almacenan en un contenedor de Azure Blob Storage en su suscripción de Azure para su uso en la siguiente tarea de modelado.

### <a name="task-2-model-building-and-evaluation"></a>Tarea 2: Compilación del modelo y evaluación

El Jupyter Notebook de compilación del modelo Code/2_model_building_and_evaluation.ipnyb lee los conjuntos de datos de aprendizaje y prueba de PySpark en Blob Storage. A continuación, se crea una red LSTM con los conjuntos de datos de aprendizaje. Se mide el rendimiento del modelo en el conjunto de pruebas. El modelo resultante se serializa y se almacena en el contexto de proceso local para su uso en la tarea de operacionalización.

### <a name="task-3-operationalization"></a>Tarea 3: Operacionalización

El Jupyter Notebook de operacionalización en Code/3_operationalization.ipnyb usa el modelo almacenado para crear las funciones y esquemas que son necesarios para llamar al modelo en un servicio web hospedado de Azure. El Notebook comprueba las funciones y a continuación comprime los recursos de operacionalización en un archivo .zip.

El archivo comprimido contiene estos archivos:

- **modellstm.json**: el archivo de definición de esquema para la implementación. 
- **lstmscore.py**: las funciones **init()** y **run()** que requiere el servicio web de Azure.
- **lstm.model**: el directorio de definición del modelo.

El Notebook comprueba las funciones usando la definición del modelo antes de empaquetar los recursos de operacionalización para la implementación. Al final del Notebook se incluyen las instrucciones para la implementación.


## <a name="conclusion"></a>Conclusión

Este tutorial utiliza un escenario simple en el que se utiliza un solo origen de datos (valores del sensor) para realizar predicciones. En escenarios más avanzados de mantenimiento predictivo, como [Predictive Maintenance Modeling Guide R Notebook](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-R-Notebook-1), pueden utilizarse varios orígenes de datos. Otros orígenes de datos pueden incluir registros del historial de mantenimiento, registros de errores y características de máquina y operador. Los orígenes de datos adicionales pueden requerir distintos tipos de tratamientos para su uso en redes de aprendizaje profundo. También es importante optimizar los modelos para los parámetros correctos, como tamaño de la ventana. 

Puede editar partes pertinentes de este escenario y probar escenarios con problemas diferentes, como los que se describen en la [Guía de modelado de mantenimiento predictivo](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1), que incorpora varios otros orígenes de datos.


## <a name="references"></a>Referencias

- [Plantilla de solución de mantenimiento predictivo](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/cortana-analytics-playbook-predictive-maintenance)
- [Guía de modelado de mantenimiento predictivo](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1)
- [Guía de modelado de mantenimiento predictivo con Python Notebook](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-Python-Notebook-1)
- [Mantenimiento predictivo con PySpark](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-using-PySpark)

