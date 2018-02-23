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
ms.openlocfilehash: 7d4fe98b5c45767fb06391218e80789fc0c96a3b
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/14/2018
---
# <a name="deep-learning-for-predictive-maintenance-real-world-scenario"></a>Aprendizaje profundo para escenario real de mantenimiento predictivo.

El aprendizaje profundo es una de las tendencias más populares en el aprendizaje automático, con aplicaciones en muchas áreas, entre las cuales se incluyen:
- vehículos sin conductor y robótica
- reconocimiento de voz e imágenes
- previsión financiera.

También conocidos como redes neuronales profundas (DNN), estos métodos se inspiran en las neuronas individuales del cerebro (redes neuronales biológicas).

El impacto del tiempo de inactividad no programado de los equipos puede ser perjudicial para las empresas. Es fundamental mantener el equipamiento de campo en funcionamiento para maximizar el rendimiento y minimizar el costoso tiempo de inactividad no programado. La identificación temprana de los problemas le ayuda a asignar recursos de mantenimiento limitado de una forma más rentable y a mejorar la calidad y los procesos de la cadena de suministro. 

Una estrategia de mantenimiento predictivo (PM) usa métodos de aprendizaje automático para determinar el estado del equipo a fin de realizar tareas de mantenimiento de forma preventiva para evitar un rendimiento de la máquina adverso. En PM, los datos, recopilados con el tiempo para supervisar el estado de la máquina, se analizan en busca de patrones que se puedan usar para predecir errores. Las redes [Long Short Term Memory (LSTM)](http://colah.github.io/posts/2015-08-Understanding-LSTMs/) son atractivas para esta configuración, ya que están diseñadas para aprender de secuencias de datos.

## <a name="link-to-the-gallery-github-repository"></a>Vínculo al repositorio de GitHub de la galería

A continuación se muestra el vínculo en el repositorio público de GitHub para informes de problemas y contribuciones: [https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance](https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance) 

## <a name="use-case-overview"></a>Información general del caso de uso

Este tutorial usa el ejemplo de eventos "de funcionamiento a fallo" de un motor de avión simulado para demostrar el proceso de modelado de mantenimiento predictivo. El escenario se describe en [Mantenimiento predictivo](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Template-3)

La principal suposición en esta configuración es el motor degradado de forma progresiva mientras dura. La degradación se puede detectar en las medidas del sensor del motor. PM intenta modelar la relación entre los cambios en estos valores del sensor y los errores históricos. El modelo puede entonces predecir cuándo y es posible que el motor falle en el futuro en función del estado actual de las medidas del sensor.

Este escenario crea una red LSTM para predecir la vida útil restante (RUL) de los motores de aviones mediante valores del sensor históricos. Al usar [Keras](https://keras.io/) con el marco de trabajo de aprendizaje profundo [TensorFlow](https://www.tensorflow.org/) como motor de proceso, el escenario entrena la LSTM con un conjunto de motores y prueba la red en un conjunto de motores no visto anteriormente.

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
5. Haga clic en el botón **Crear**.

## <a name="prepare-the-notebook-server-computation-target"></a>Preparación del destino de cálculo del servidor de Notebook

Para ejecutar en la máquina local, en el menú `File` de Azure Machine Learning, seleccione `Open Command Prompt` o `Open PowerShell CLI`. La interfaz CLI permite obtener acceso a los servicios de Azure con los comandos `az`. Primero, inicie sesión en la cuenta de Azure con el comando:

```
az login
``` 

Este comando proporciona una clave de autenticación para su uso con la dirección URL `https:\\aka.ms\devicelogin`. La CLI espera hasta que la operación de inicio de sesión del dispositivo vuelve y proporciona información de conexión. A continuación, si tiene que instalar un [Docker](https://www.docker.com/get-docker) local, prepare el entorno de proceso local con los siguientes comandos:

```
az ml experiment prepare --target docker --run-configuration docker
```

Es preferible ejecutar en una [Máquina virtual de ciencia de datos para Linux (Ubuntu)](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu) debido a los requisitos de memoria y disco. Una vez configurada la Máquina virtual de ciencia de datos, prepare el entorno de Docker remoto con los dos comandos siguientes:

```
az ml computetarget attach remotedocker --name [Connection_Name] --address [VM_IP_Address] --username [VM_Username] --password [VM_UserPassword]
```

Una vez conectado al contenedor de Docker remoto, prepare el entorno de proceso de Docker de la Máquina virtual de ciencia de datos con: 

```
az ml experiment prepare --target [Connection_Name] --run-configuration [Connection_Name]
```

Con el entorno de proceso de Docker preparado, abra el servidor de Jupyter Notebook dentro de la pestaña de Notebook de Azure Machine Learning Workbench, o bien inicie un servidor basado en un explorador con: 
```
az ml notebook start
```

Los blocs de notas de ejemplo se almacenan en el directorio `Code`. Los blocs de notas están configurados para ejecutarse secuencialmente, empezando por el primero (`Code\1_data_ingestion.ipynb`). Cuando abra cada bloc de notas, se le solicitará que seleccione el kernel de proceso. Elija el kernel `[Project_Name]_Template [Connection_Name]` para ejecutar en la Máquina virtual de ciencia de datos configurada previamente.

## <a name="data-description"></a>Descripción de los datos

La plantilla utiliza tres conjuntos de datos como entradas en los archivos **PM_train.txt**, **PM_test.txt** y **PM_truth.txt**. 

-  **Datos de aprendizaje**: los datos "de funcionamiento a fallo" del motor de avión. El conjunto de datos de aprendizaje (PM_train.txt) se compone de varias series de tiempo multivariadas, con *ciclo* como unidad de tiempo. Incluye 21 lecturas de sensor para cada ciclo. 

    - Cada serie temporal se genera a partir de un motor diferente del mismo tipo. Cada motor se inicia con diferentes grados de desgaste inicial y variación de fabricación única. Esta información es desconocida para el usuario. 

    - En estos datos simulados, se supone que el motor funciona normalmente al principio de cada serie temporal. En algún momento durante la serie de los ciclos de funcionamiento se inicia la degradación. La degradación progresa y aumenta en magnitud. 

    - Cuando se alcanza un umbral predefinido, se considera que no es seguro que el motor siga funcionando. El último ciclo de cada serie temporal es el punto de error de ese motor.

-   **Datos de prueba**: los datos de funcionamiento del motor del avión sin eventos de fallo registrados. Los datos de prueba (PM_test.txt) tienen el mismo esquema de datos que los datos de aprendizaje. La única diferencia es que los datos no indican cuándo se produce el error (el último período de tiempo *no* representan el punto de fallo). No se sabe cuántos ciclos más puede durar este motor antes de que se produzca un fallo.

- **Datos verdaderos**: la información de los ciclos restantes verdaderos para cada motor en los datos de prueba. Los datos verdaderos y precisos proporcionan el número de ciclos de trabajo restantes para los motores de los datos de prueba.

## <a name="scenario-structure"></a>Estructura del escenario

El flujo de trabajo del escenario se divide en tres pasos, cada uno de ellos ejecutado en instancias de Jupyter Notebook. Cada Notebook produce artefactos de datos que son persistentes localmente para su uso en los siguientes Notebook: 

### <a name="task-1-data-ingestion-and-preparation"></a>Tarea 1: Ingesta y preparación de datos

El Jupyter Notebook de ingesta de datos en `Code/1_data_ingestion_and_preparation.ipnyb` carga los tres conjuntos de datos de entrada en formato de trama de datos de Pandas. A continuación, prepara los datos para el modelado y realiza la visualización de algunos datos preliminares. Los conjuntos de datos se almacenan localmente en el contexto de proceso para su uso en el Notebook de compilación del modelo.

### <a name="task-2-model-building-and-evaluation"></a>Tarea 2: Compilación del modelo y evaluación

El Jupyter Notebook de compilación del modelo en `Code/2_model_building_and_evaluation.ipnyb` lee los conjuntos de datos de aprendizaje y prueba en el disco y crea una red LSTM que los datos de aprendizaje establecen. Se mide el rendimiento del modelo en el conjunto de pruebas. El modelo resultante se serializa y se almacena en el contexto de proceso local para su uso en la tarea de operacionalización.

### <a name="task-3-operationalization"></a>Tarea 3: Operacionalización

El Jupyter Notebook de operacionalización en `Code/3_operationalization.ipnyb` usa el modelo almacenado para crear funciones y esquemas para llamar al modelo en un servicio web hospedado de Azure. El Notebook comprueba las funciones y, a continuación, comprime los recursos en el archivo `LSTM_o16n.zip`, que se carga en su contenedor de almacenamiento de Azure para su implementación.

El archivo de implementación `LSTM_o16n.zip` contiene los siguientes artefactos:

- `webservices_conda.yaml` define los paquetes de Python necesarios para ejecutar el modelo LSTM en el destino de implementación.  
- `service_schema.json` define el esquema de datos esperado por el modelo LSTM.     
- `lstmscore.py` define las funciones que ejecuta el destino de implementación para puntuar nuevos datos.    
- `modellstm.json` define la arquitectura LSTM. Las funciones lstmscore.py leen la arquitectura y los pesos para inicializar el modelo.
- `modellstm.h5` define los pesos del modelo.
- `test_service.py` Un script de prueba que llama al punto de conexión de implementación con registros de datos de prueba. 
- `PM_test_files.pkl` El script `test_service.py` lee datos del motor históricos en el archivo `PM_test_files.pkl` y envía al servicio web ciclos suficientes para que la LSTM devuelva una probabilidad de error del motor.

El Notebook comprueba las funciones usando la definición del modelo antes de empaquetar los recursos de operacionalización para la implementación. Instrucciones para configurar y probar el servicio web se incluyen al final del Notebook `Code/3_operationalization.ipnyb`.

## <a name="conclusion"></a>Conclusión

En este tutorial se usa un escenario sencillo con valores del sensor para realizar predicciones. Escenarios de mantenimiento predictivo más avanzados, como la [Guía de modelado de mantenimiento predictivo con R Notebook](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-R-Notebook-1), puede usar muchos orígenes de datos como registros de mantenimiento históricos, registros de errores y características de la máquina. Los orígenes de datos adicionales pueden requerir distintos tratamientos para su uso con el aprendizaje profundo.


## <a name="references"></a>Referencias

Hay otros ejemplos de casos de uso de mantenimiento predictivo disponibles en diversas plataformas:

* [Plantilla de solución de mantenimiento predictivo](https://docs.microsoft.com/azure/machine-learning/cortana-analytics-playbook-predictive-maintenance)
* [Guía de modelado de mantenimiento predictivo](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1)
* [Guía de modelado de mantenimiento predictivo con SQL R Services](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-Modeling-Guide-using-SQL-R-Services-1)
* [Guía de modelado de mantenimiento predictivo con Python Notebook](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-Python-Notebook-1)
* [Mantenimiento predictivo con PySpark](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-using-PySpark)

* [Escenario real de mantenimiento predictivo](https://docs.microsoft.com/en-us/azure/machine-learning/preview/scenario-predictive-maintenance)

## <a name="next-steps"></a>pasos siguientes

Hay muchos otros escenarios de ejemplo disponibles en Azure Machine Learning Workbench que muestran características adicionales del producto. 