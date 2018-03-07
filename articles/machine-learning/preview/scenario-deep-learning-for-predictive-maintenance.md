---
title: 'Aprendizaje profundo para escenarios reales de mantenimiento predictivo: Azure | Microsoft Docs'
description: Aprenda a replicar el tutorial de aprendizaje profundo para un mantenimiento predictivo con Azure Machine Learning Workbench.
services: machine-learning
author: ehrlinger
ms.author: jehrling
manager: ireiter
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.devlang: 
ms.topic: article
ms.date: 11/22/2017
ms.openlocfilehash: 022e629469745fceb4fb9355cb6259a144dda222
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/24/2018
---
# <a name="deep-learning-for-predictive-maintenance-real-world-scenarios"></a>Aprendizaje profundo para escenarios reales de mantenimiento predictivo

El aprendizaje profundo es una de las tendencias más populares en el aprendizaje automático y tiene aplicaciones en muchas áreas, entre las cuales se incluyen:
- Vehículos sin conductor y robótica.
- Reconocimiento de voz e imágenes.
- Previsión financiera.

También conocidos como redes neuronales profundas (DNN), estos métodos se inspiran en las neuronas individuales que existen en el cerebro (redes neuronales biológicas).

El impacto del tiempo de inactividad no programado de los equipos puede ser perjudicial para las empresas. Es fundamental mantener el equipamiento de campo en funcionamiento para maximizar el rendimiento y minimizar el costoso tiempo de inactividad no programado. La identificación temprana de los problemas le ayuda a asignar recursos de mantenimiento limitado de una forma más rentable y a mejorar la calidad y los procesos de la cadena de suministro. 

Una estrategia de mantenimiento predictivo (PM) usa métodos de aprendizaje automático para determinar el estado del equipo a fin de realizar tareas de mantenimiento de forma preventiva para evitar un rendimiento de la máquina adverso. En el mantenimiento predictivo, los datos se recopilan con el tiempo para supervisar el estado de la máquina y luego se analizan en búsqueda de patrones para predecir errores. Las redes [Long Short Term Memory (LSTM)](http://colah.github.io/posts/2015-08-Understanding-LSTMs/) son atractivas para esta configuración, ya que están diseñadas para aprender de secuencias de datos.

### <a name="cortana-intelligence-gallery-github-repository"></a>Repositorio GitHub de la Galería de Cortana Intelligence

El tutorial de la Galería de Cortana Intelligence para el mantenimiento predictivo es un repositorio GitHub público ([https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance](https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance)) en el que puede notificar problemas y realizar contribuciones.


## <a name="use-case-overview"></a>Información general del caso de uso

Este tutorial usa el ejemplo de eventos "de funcionamiento a fallo" de un motor de avión simulado para demostrar el proceso de modelado de mantenimiento predictivo. El escenario se describe en [Mantenimiento predictivo](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Template-3).

La principal suposición en esta configuración es el motor degradado de forma progresiva mientras dura. La degradación se puede detectar en las medidas del sensor del motor. PM intenta modelar la relación entre los cambios en estos valores del sensor y los errores históricos. El modelo puede entonces predecir cuándo y es posible que el motor falle en el futuro en función del estado actual de las medidas del sensor.

Este escenario crea una red LSTM para predecir la vida útil restante (RUL) de los motores de aviones mediante valores del sensor históricos. El escenario usa la biblioteca de [Keras](https://keras.io/) con el marco de aprendizaje profundo [Tensorflow](https://www.tensorflow.org/) como motor de proceso. El escenario entrena a LSTM con un conjunto de motores y prueba la red en un conjunto de motores no visto.

## <a name="prerequisites"></a>requisitos previos
- Una [cuenta de Azure](https://azure.microsoft.com/free/) (hay disponibles versiones gratuitas de prueba).
- Azure Machine Learning Workbench con un área de trabajo creada.
- Para la operacionalización del modelo: la operacionalización de Azure Machine Learning con una configuración de entorno de implementación local y una cuenta de [Administración de modelos de Azure Machine Learning](model-management-overview.md).

## <a name="create-a-new-workbench-project"></a>Creación de un nuevo proyecto de Workbench

Cree un proyecto usando este ejemplo como plantilla:

1. Abra Machine Learning Workbench.
2. En la página **Proyectos**, seleccione **+** y luego **Nuevo proyecto**.
3. En el panel **Crear nuevo proyecto**, especifique la información del nuevo proyecto.
4. En el cuadro de búsqueda **Buscar plantillas de proyecto**, escriba "Predictive Maintenance" (Mantenimiento predictivo) y seleccione la plantilla **Aprendizaje profundo para escenario de mantenimiento predictivo**.
5. Seleccione **Crear**.

## <a name="prepare-the-notebook-server-computation-target"></a>Preparación del destino de cálculo del servidor de Notebook

Para la ejecución en la máquina local, en el menú **Archivo** de Machine Learning Workbench, seleccione **Abrir símbolo del sistema** o **Abrir la CLI de PowerShell**. La interfaz CLI permite obtener acceso a los servicios de Azure mediante los comandos `az`. Primero, inicie sesión en la cuenta de Azure con el comando:

```
az login
``` 

Este comando proporciona una clave de autenticación para usarla con la dirección URL https:\\aka.ms\devicelogin. La CLI espera hasta que la operación de inicio de sesión del dispositivo vuelve y proporciona información de conexión. A continuación, si tiene una instalación de [Docker](https://www.docker.com/get-docker) local, prepare el entorno de proceso local con el comando:

```
az ml experiment prepare --target docker --run-configuration docker
```

Es preferible ejecutar en una [Máquina virtual de ciencia de datos (DSVM) para Linux (Ubuntu)](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu) debido a los requisitos de memoria y disco. Una vez configurada la DSVM, prepare el entorno de Docker remoto con estos dos comandos:

```
az ml computetarget attach remotedocker --name [Connection_Name] --address [VM_IP_Address] --username [VM_Username] --password [VM_UserPassword]
```

Una vez que se conecta al contenedor de Docker remoto, prepare el entorno de proceso de Docker de DSVM con el comando: 

```
az ml experiment prepare --target [Connection_Name] --run-configuration [Connection_Name]
```

Con el entorno de proceso de Docker preparado, abra el servidor de Jupyter Notebook desde la pestaña **Notebook** de Machine Learning Workbench, o bien inicie un servidor basado en un explorador con el comando: 

```
az ml notebook start
```

Los Notebooks de ejemplo se almacenan en el directorio Code. Los Notebooks están configurados para ejecutarse de manera secuencial, empezando por el primero (Code\1_data_ingestion.ipynb). Cuando abra cada Notebook, se le solicitará que seleccione el kernel de proceso. Elija el kernel [Nombre_de_proyecto]_Template [Nombre_de_conexión] para ejecutarlo en la DSVM configurada anteriormente.

## <a name="data-description"></a>Descripción de los datos

La plantilla usa tres conjuntos de datos como entradas en los archivos PM_train.txt, PM_test.txt y PM_truth.txt. 

- **Datos de aprendizaje**: los datos "de funcionamiento a fallo" del motor de avión. El conjunto de datos de aprendizaje (PM_train.txt) se compone de varias series de tiempo multivariadas, con *ciclo* como unidad de tiempo. Incluye 21 lecturas de sensor para cada ciclo. 

    - Cada serie temporal se genera a partir de un motor diferente del mismo tipo. Cada motor se inicia con diferentes grados de desgaste inicial y variación de fabricación única. Esta información es desconocida para el usuario. 

    - En estos datos simulados, se supone que el motor funciona normalmente al principio de cada serie temporal. En algún momento durante la serie de los ciclos de funcionamiento se inicia la degradación. La degradación progresa y aumenta en magnitud. 

    - Cuando se alcanza un umbral predefinido, se considera que no es seguro que el motor siga funcionando. El último ciclo de cada serie temporal es el punto de error de ese motor.

- **Datos de prueba**: los datos de funcionamiento del motor del avión sin eventos de fallo registrados. Los datos de prueba (PM_test.txt) tienen el mismo esquema de datos que los datos de aprendizaje. La única diferencia es que los datos no indican cuándo se produce el error (el último período de tiempo *no* representan el punto de fallo). No se sabe cuántos ciclos más puede durar este motor antes de que se produzca un fallo.

- **Datos verdaderos**: la información de los ciclos restantes verdaderos para cada motor en los datos de prueba. Los datos verdaderos y precisos proporcionan el número de ciclos de trabajo restantes para los motores de los datos de prueba.

## <a name="scenario-structure"></a>Estructura del escenario

El flujo de trabajo del escenario se divide en tres pasos y cada uno de ellos se ejecuta en una instancia de Jupyter Notebook. Cada Notebook produce artefactos de datos que son persistentes localmente para su uso en los Notebook.

### <a name="task-1-data-ingestion-and-preparation"></a>Tarea 1: Ingesta y preparación de datos

La instancia de Jupyter Notebook de ingesta de datos en Code/1_data_ingestion_and_preparation.ipnyb carga los tres conjuntos de datos de entrada en el formato de trama de datos de Pandas. A continuación, prepara los datos para el modelado y realiza la visualización de algunos datos preliminares. Los conjuntos de datos se almacenan localmente en el contexto de proceso para su uso en la instancia de Jupyter Notebook de Compilación del modelo.

### <a name="task-2-model-building-and-evaluation"></a>Tarea 2: Compilación del modelo y evaluación

La instancia de Jupyter Notebook de Compilación del modelo en Code/2_model_building_and_evaluation.ipnyb lee los conjuntos de datos de aprendizaje y prueba en el disco y crea una red LSTM para el conjunto de datos de aprendizaje. El rendimiento del modelo se mide en el conjunto de datos de prueba. El modelo resultante se serializa y se almacena en el contexto de proceso local para su uso en la tarea de operacionalización.

### <a name="task-3-operationalization"></a>Tarea 3: Operacionalización

La instancia de Jupyter Notebook de Operacionalización en Code/3_operationalization.ipnyb usa el modelo almacenado para compilar las funciones y esquemas para llamar al modelo en un servicio web hospedado de Azure. El Notebook comprueba las funciones y, luego, comprime los recursos en el archivo LSTM_o16n.zip. El archivo se carga en el contenedor de almacenamiento de Azure para su implementación.

El archivo de implementación LSTM_o16n.zip contiene los artefactos siguientes:

- **webservices_conda.yaml**: define los paquetes de Python necesarios para ejecutar el modelo LSTM en el destino de implementación.  
- **service_schema.json**: define el esquema de datos que el modelo LSTM espera.   
- **lstmscore.py**: define las funciones que ejecuta el destino de implementación para puntuar datos nuevos.    
- **modellstm.json**: define la arquitectura LSTM. Las funciones lstmscore.py leen la arquitectura y los pesos para inicializar el modelo.
- **modellstm.h5**: define los pesos del modelo.
- **test_service.py**: un script de prueba que llama al punto de conexión de implementación con registros de datos de prueba. 
- **PM_test_files.pkl**: el script test_service.py lee datos históricos del motor del archivo PM_test_files.pkl y envía al servicio web ciclos suficientes para que LSTM devuelva una probabilidad de error del motor.

El Notebook comprueba las funciones usando la definición del modelo antes de empaquetar los recursos de operacionalización para la implementación. Hay instrucciones para configurar y probar el servicio web al final del Notebook Code/3_operationalization.ipnyb.

## <a name="conclusion"></a>Conclusión

En este tutorial se proporciona un escenario sencillo que usa valores del sensor para realizar predicciones. Escenarios de mantenimiento predictivo más avanzados, como la [Guía de modelado de mantenimiento predictivo con R Notebook](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-R-Notebook-1), puede usar muchos orígenes de datos como registros de mantenimiento históricos, registros de errores y características de la máquina. Los orígenes de datos adicionales pueden requerir distintos tratamientos para su uso con el aprendizaje profundo.


## <a name="references"></a>Referencias

Las referencias siguientes proporcionan ejemplos de otros casos de uso de mantenimiento predictivo para distintas plataformas:

* [Plantilla de solución de mantenimiento predictivo](https://docs.microsoft.com/azure/machine-learning/cortana-analytics-playbook-predictive-maintenance)
* [Guía de modelado de mantenimiento predictivo](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1)
* [Guía de modelado de mantenimiento predictivo con SQL R Services](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-Modeling-Guide-using-SQL-R-Services-1)
* [Guía de modelado de mantenimiento predictivo con Python Notebook](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-Python-Notebook-1)
* [Mantenimiento predictivo con PySpark](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-using-PySpark)
* [Escenarios reales de mantenimiento predictivo](https://docs.microsoft.com/en-us/azure/machine-learning/preview/scenario-predictive-maintenance)

## <a name="next-steps"></a>pasos siguientes

Hay otros escenarios de ejemplo disponibles en Machine Learning Workbench que muestran características adicionales del producto. 
