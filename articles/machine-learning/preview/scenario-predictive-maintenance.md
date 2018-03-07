---
title: Mantenimiento predictivo en escenarios reales | Microsoft Docs
description: Mantenimiento predictivo en escenarios reales con PySpark
services: machine-learning
author: ehrlinger
ms.author: jehrling
manager: jhubbard
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.custom: mvc
ms.date: 10/05/2017
ms.openlocfilehash: 81e227194ff64d7b7af842a208349ccc63528ab8
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/24/2018
---
# <a name="predictive-maintenance-for-real-world-scenarios"></a>Mantenimiento predictivo en escenarios reales

El impacto del tiempo de inactividad no programado de los equipos puede ser perjudicial para las empresas. Es fundamental mantener el equipamiento de campo en funcionamiento para maximizar la utilización y el rendimiento y para minimizar el costoso tiempo de inactividad no programado. La identificación temprana de los problemas le ayuda a asignar recursos de mantenimiento limitado de una forma más rentable y a mejorar la calidad y los procesos de la cadena de suministro. 

En este escenario se explora un [conjunto de datos simulado a gran escala](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data) para guiarle por un proyecto de ciencia de datos de mantenimiento predictivo a través de los pasos principales de ingesta de datos, ingeniería de características, creación del modelo, y operacionalización e implementación del modelo. El código para todo el proceso está escrito en Jupyter Notebook con PySpark en Azure Machine Learning Workbench. El modelo final se implementa con Administración de modelos de Azure Machine Learning para realizar predicciones de errores en equipos en tiempo real.   

### <a name="cortana-intelligence-gallery-github-repository"></a>Repositorio GitHub de la Galería de Cortana Intelligence

El tutorial de la Galería de Cortana Intelligence para el mantenimiento predictivo es un repositorio GitHub público ([https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance](https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance)) en el que puede notificar problemas y realizar contribuciones.


## <a name="use-case-overview"></a>Información general del caso de uso

Un problema importante al que se enfrentan las empresas en sectores de uso intensivo de activos son los considerables costos asociados a retrasos debidos a problemas mecánicos. La mayoría de las empresas están interesadas en la predicción de cuándo podrían surgir estos problemas con el fin de evitarlos proactivamente antes de que se produzcan. El objetivo es reducir los costos mediante la reducción del tiempo de inactividad y, posiblemente, aumentar la seguridad. 

En este escenario se toman ideas de la [guía de mantenimiento predictivo](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/cortana-analytics-playbook-predictive-maintenance) para demostrar como compilar un modelo predictivo para un conjunto de datos simulado. Los datos de ejemplo derivan de elementos comunes que se observan en muchos casos de uso de mantenimiento predictivo.

El problema empresarial para estos datos simulados es predecir problemas provocados por errores en los componentes. La pregunta empresarial es "*¿Cuál es la probabilidad de que una máquina deje de funcionar debido al error de un componente?*". Este problema tiene el formato de un problema de clasificación multiclase (varios componentes por máquina). Un algoritmo de aprendizaje automático se usa para crear el modelo predictivo. El modelo se entrena con datos históricos que se recopilan de las máquinas. En este escenario, el usuario pasa por distintos pasos para implementar el modelo en el entorno de Machine Learning Workbench.

## <a name="prerequisites"></a>requisitos previos

* Una [cuenta de Azure](https://azure.microsoft.com/free/) (hay disponibles versiones gratuitas de prueba).
* Una copia instalada de [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md). Siga la [Guía de instalación rápida](./quickstart-installation.md) para instalar el programa y crear un área de trabajo.
* La operacionalización de Azure Machine Learning requiere un entorno de implementación local y una [cuenta de Administración de modelos de Azure Machine Learning](model-management-overview.md).

Este ejemplo se ejecuta en cualquier contexto de proceso de Machine Learning Workbench. Sin embargo, se recomienda ejecutarlo con 16 GB de memoria como mínimo. Este escenario se compiló y probó en una máquina Windows 10 que se ejecutaba en una [Máquina virtual remota de ciencia de datos (DSVM) para Linux (Ubuntu)](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu) DS4_V2 estándar.

La operacionalización del modelo se realizó con la versión 0.1.0a22 de la CLI de Azure Machine Learning.

## <a name="create-a-new-workbench-project"></a>Creación de un nuevo proyecto de Workbench

Cree un proyecto usando este ejemplo como plantilla:
1.  Abra Machine Learning Workbench.
2.  En la página **Proyectos**, seleccione **+** y luego **Nuevo proyecto**.
3.  En el panel **Crear nuevo proyecto**, rellene la información del proyecto nuevo.
4.  En el cuadro de búsqueda **Buscar plantillas de proyecto**, escriba "Predictive Maintenance" (Mantenimiento predictivo) y seleccione la plantilla **Mantenimiento predictivo**.
5.  Seleccione **Crear**.

## <a name="prepare-the-notebook-server-computation-target"></a>Preparación del destino de cálculo del servidor de Notebook

Para la ejecución en la máquina local, en el menú **Archivo** de Machine Learning Workbench, seleccione **Abrir símbolo del sistema** o **Abrir la CLI de PowerShell**. La interfaz CLI permite obtener acceso a los servicios de Azure mediante los comandos `az`. Primero, inicie sesión en la cuenta de Azure con el comando:

```
az login
``` 

Este comando proporciona una clave de autenticación para usarla con la dirección URL https:\\aka.ms\devicelogin. La CLI espera hasta que la operación de inicio de sesión del dispositivo vuelve y proporciona información de conexión. A continuación, si tiene una instalación de [Docker](https://www.docker.com/get-docker) local, prepare el entorno de proceso local con el comando:

```
az ml experiment prepare --target docker --run-configuration docker
```

Es preferible ejecutar en una [DSVM para Linux (Ubuntu)](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu) debido a los requisitos de memoria y disco. Una vez configurada la DSVM, prepare el entorno de Docker remoto con estos dos comandos:

```
az ml computetarget attach remotedocker --name [Connection_Name] --address [VM_IP_Address] --username [VM_Username] --password [VM_UserPassword]
```

Una vez que se conecta al contenedor de Docker remoto, prepare el entorno de proceso de Docker de DSVM con el comando: 

```
az ml experiment prepare --target [Connection_Name] --run-configuration [Connection_Name]
```

Con el entorno de proceso de Docker preparado, abra el servidor de Jupyter Notebook desde la pestaña **Notebook** de Azure Machine Learning Workbench, o bien inicie un servidor basado en un explorador con el comando: 

```
az ml notebook start
```

Los Notebooks de ejemplo se almacenan en el directorio Code. Los Notebooks están configurados para ejecutarse de manera secuencial, empezando por el primero (Code\1_data_ingestion.ipynb). Cuando abra cada Notebook, se le solicitará que seleccione el kernel de proceso. Elija el kernel [Nombre_de_proyecto]_Template [Nombre_de_conexión] para ejecutarlo en la DSVM configurada anteriormente.

## <a name="data-description"></a>Descripción de los datos

Los [datos simulados](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data) se componen de cinco archivos de valores separados por comas (.csv). Use los vínculos siguientes para obtener descripciones detalladas sobre los conjuntos de datos.

* [Máquinas](https://pdmmodelingguide.blob.core.windows.net/pdmdata/machines.csv): características que distinguen cada máquina, como la antigüedad y el modelo.
* [Error](https://pdmmodelingguide.blob.core.windows.net/pdmdata/errors.csv): el registro de errores contiene errores que no causan interrupción que se generan mientras la máquina todavía está operativa. Estos errores no se consideran como tal, aunque pueden ser predicción de un evento de error en el futuro. Los valores de fecha y hora de los errores se redondean a la hora más cercana, ya que los datos de telemetría se recopilan cada hora.
* [Mantenimiento](https://pdmmodelingguide.blob.core.windows.net/pdmdata/maint.csv): el registro de mantenimiento contiene registros de mantenimiento programado y no programado. El mantenimiento programado se corresponde con la inspección habitual de los componentes. El mantenimiento no programado puede surgir debido a errores mecánicos u otro tipo de degradación del rendimiento. Los valores de fecha y hora del mantenimiento se redondean a la hora más cercana, ya que los datos de telemetría se recopilan cada hora.
* [Telemetría](https://pdmmodelingguide.blob.core.windows.net/pdmdata/telemetry.csv): los datos de telemetría consisten en medidas de series temporales desde varios sensores en cada máquina. Los datos se registran calculando el promedio de los valores del sensor cada hora.
* [Errores](https://pdmmodelingguide.blob.core.windows.net/pdmdata/failures.csv): los errores corresponden a reemplazos de componentes dentro del registro de mantenimiento. Cada registro contiene el identificador de máquina, el tipo de componente y la fecha y hora de reemplazo. Estos registros se utilizan para crear las etiquetas de aprendizaje automático que está intentando predecir el modelo.

Para descargar los conjuntos de datos sin procesar del repositorio GitHub y crear los conjuntos de datos de PySpark para este análisis, consulte el escenario de [Ingesta de datos](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb) de Jupyter Notebook en la carpeta Code.

## <a name="scenario-structure"></a>Estructura del escenario
El contenido del escenario está disponible en el [repositorio de GitHub](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance). 

El archivo [Léame](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/README.md) describe el flujo de trabajo desde la preparación de los datos y la compilación de un modelo hasta la implementación de una solución para producción. Cada paso del flujo de trabajo se encapsula en Jupyter Notebook en la carpeta [Código](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/tree/master/Code) dentro del repositorio.   

[Code\1_data_ingestion.ipynb](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb): este Notebook descarga los cinco archivos .csv de entrada y realiza una limpieza y una visualización preliminar de los datos. El Notebook convierte los conjuntos de datos al formato de PySpark y los almacena en un contenedor de blobs de Azure para su uso en el Notebook Diseño de características.

[Code\2_feature_engineering.ipynb](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/2_feature_engineering.ipynb): las características del modelo se construyen a partir del conjunto de datos sin procesar de Azure Blob Storage mediante el uso de un enfoque de serie temporal estándar para los datos de telemetría, de errores y de mantenimiento. La sustitución de componentes relacionada con errores se usa para construir las etiquetas de modelo que describen qué componente generó el error. Los datos de la característica etiquetada se guardan en un blob de Azure para el Notebook Compilación de modelos.

[Code\3_model_building.ipynb](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/3_model_building.ipynb): el Notebook Compilación de modelos usa el conjunto de datos de características etiquetadas y divide los datos en conjuntos de datos de entrenamiento y desarrollo junto con una marca de fecha y hora. El Notebook se configura como un experimento con los modelos pyspark.ml.classification. Los datos de entrenamiento están vectorizados. El usuario puede experimentar con **DecisionTreeClassifier** o **RandomForestClassifier** para manipular los hiperparámetros con el fin de encontrar el modelo con mejor rendimiento. El rendimiento se determina mediante la evaluación de las estadísticas de medida del conjunto de datos de desarrollo. Estas estadísticas se registran en la pantalla del entorno en tiempo de ejecución de Machine Learning Workbench para realizar un seguimiento. En cada ejecución, el Notebook guarda el modelo resultante en el disco local que ejecuta el kernel de Jupyter Notebook. 

[Code\4_operationalization.ipynb](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/4_operationalization.ipynb): este Notebook usa el último modelo que se guarda en el disco local (el kernel de Jupyter Notebook) para compilar los componentes para la implementación del modelo en un servicio web de Azure. Todos los recursos operacionales se compactan en el archivo o16n.zip almacenado en otro contenedor de blobs de Azure. El archivo comprimido contiene:

* **service_schema.json**: el archivo de definición de esquema para la implementación. 
* **pdmscore.py**: las funciones **init()** y **run()** que requiere el servicio web de Azure.
* **pdmrfull.model**: el directorio de definición del modelo.
    
El Notebook comprueba las funciones con la definición del modelo antes de empaquetar los recursos de operacionalización para la implementación. Al final del Notebook se incluyen las instrucciones para la implementación.

## <a name="conclusion"></a>Conclusión

Este escenario proporciona una descripción general de cómo compilar una solución de mantenimiento predictivo integral con PySpark dentro del entorno Jupyter Notebook en Machine Learning Workbench. Este escenario de ejemplo también describe la implementación de modelos a través del entorno de Administración de modelos de Machine Learning para realizar predicciones de errores en los equipos en tiempo real.

## <a name="references"></a>Referencias

Las referencias siguientes proporcionan ejemplos de otros casos de uso de mantenimiento predictivo para distintas plataformas:

* [Plantilla de solución de mantenimiento predictivo](https://docs.microsoft.com/azure/machine-learning/cortana-analytics-playbook-predictive-maintenance)
* [Guía de modelado de mantenimiento predictivo](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1)
* [Guía de modelado de mantenimiento predictivo con SQL R Services](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-Modeling-Guide-using-SQL-R-Services-1)
* [Guía de modelado de mantenimiento predictivo con Python Notebook](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-Python-Notebook-1)
* [Mantenimiento predictivo con PySpark](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-using-PySpark)
* [Aprendizaje profundo para mantenimiento predictivo](https://docs.microsoft.com/en-us/azure/machine-learning/preview/scenario-deep-learning-for-predictive-maintenance)

## <a name="next-steps"></a>pasos siguientes

Hay otros escenarios de ejemplo disponibles en Machine Learning Workbench que muestran características adicionales del producto. 
