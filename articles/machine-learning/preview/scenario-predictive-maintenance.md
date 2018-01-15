---
title: Escenario real de mantenimiento predictivo | Microsoft Docs
description: Escenario real de mantenimiento predictivo con PySpark
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
ms.openlocfilehash: 0299e73aecca3b3e5714b37c8b0b776ec8561e29
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2018
---
# <a name="predictive-maintenance-real-world-scenario"></a>Escenario real de mantenimiento predictivo.

El impacto del tiempo de inactividad no programado de los equipos puede ser perjudicial para las empresas. Es fundamental, por tanto, mantener el equipamiento de campo en funcionamiento para maximizar la utilización y el rendimiento y minimizar el costoso tiempo de inactividad no programado. La identificación temprana de los problemas le ayuda a asignar recursos de mantenimiento limitado de una forma más rentable y a mejorar la calidad y los procesos de la cadena de suministro. 

En este escenario se explora un [conjunto de datos simulado a gran escala](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data) para guiarle por un proyecto de ciencia de datos de mantenimiento predictivo a través de los pasos principales de ingesta de datos, ingeniería de características, creación del modelo, y operacionalización e implementación del modelo. El código para todo el proceso está escrito en Jupyter Notebooks con PySpark en Azure ML Workbench. El modelo final se implementa con Administración de modelos de Azure Machine Learning para realizar predicciones de errores en equipos en tiempo real.   

## <a name="link-to-the-gallery-github-repository"></a>Vínculo al repositorio de GitHub de la galería

A continuación se muestra el vínculo en el repositorio público de GitHub: [https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance)


## <a name="use-case-overview"></a>Información general del caso de uso

Un problema importante al que se enfrentan las empresas en sectores de uso intensivo de activos son los considerables costos asociados a retrasos por problemas mecánicos. La mayoría de las empresas están interesadas en la predicción de cuándo se producen estos problemas con el fin de evitarlos proactivamente antes de que ocurran. El objetivo es reducir los costos mediante la reducción del tiempo de inactividad y, posiblemente, aumentar la seguridad. 

En este escenario se toman ideas de la [guía de mantenimiento predictivo](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/cortana-analytics-playbook-predictive-maintenance) para demostrar la creación de un modelo predictivo para un conjunto de datos simulado. Los datos de ejemplo derivan de elementos comunes observados en muchos casos de uso de mantenimiento predictivo.

El problema empresarial para estos datos simulados es predecir problemas causados por errores en los componentes. Por lo tanto, la pregunta empresarial es "*¿cuál es la probabilidad de que un equipo deje de funcionar debido a un error de un componente?*". Este problema tiene el formato de un problema de clasificación multiclase (varios componentes por equipo) y se utiliza un algoritmo de aprendizaje automático para crear el modelo de predicción. El modelo se entrena con datos históricos recopilados de los equipos. En este escenario, el usuario pasa por las distintas fases de la implementación de este tipo de modelo en el entorno de Azure Machine Learning Workbench.

## <a name="prerequisites"></a>Requisitos previos

* Una [cuenta de Azure](https://azure.microsoft.com/en-us/free/) (hay disponibles versiones gratuitas de prueba).
* Una copia instalada de [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md) siguiendo la [Guía de instalación de inicio rápido](./quickstart-installation.md) para instalar el programa y crear un área de trabajo.
* La operacionalización de Azure Machine Learning requiere un entorno de implementación local y una [cuenta de administración de modelos](https://docs.microsoft.com/azure/machine-learning/preview/model-management-overview)

Este ejemplo se puede ejecutar en cualquier contexto de proceso de Azure Machine Learning Workbench. Sin embargo, se recomienda ejecutarlo con al menos de 16 GB de memoria. Este escenario se compiló y probó en una máquina con Windows 10 que se ejecutaba en una [Máquina virtual remota de ciencia de datos para Linux (Ubuntu)](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu) DS4_V2 estándar.

La operacionalización del modelo se realizó con la versión 0.1.0a22 de la CLI de Azure Machine Learning.

## <a name="create-a-new-workbench-project"></a>Creación de un nuevo proyecto de Workbench

Cree un nuevo proyecto usando este ejemplo como plantilla:
1.  Abra Azure Machine Learning Workbench
2.  En la página **Proyectos**, haga clic en el signo **+** y seleccione **Nuevo proyecto**
3.  En el panel **Crear nuevo proyecto**, rellene la información del proyecto nuevo
4.  En el cuadro de búsqueda **Buscar plantillas de proyecto**, escriba "Predictive Maintenance" (Mantenimiento predictivo) y seleccione la plantilla
5.  Haga clic en **Crear**

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

Los [datos simulados](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data) se componen de cinco archivos de valores separados por comas (.csv). Siga los vínculos para obtener una descripción más detallada de los conjuntos de datos.

* [Máquinas](https://pdmmodelingguide.blob.core.windows.net/pdmdata/machines.csv): características diferenciales de cada máquina. Por ejemplo, la edad y el modelo.
* [Error](https://pdmmodelingguide.blob.core.windows.net/pdmdata/errors.csv): el registro de errores contiene errores que no causan interrupción producidos mientras el equipo todavía está operativo. Estos errores no se consideran como tal, aunque pueden ser predicción de un evento de error en el futuro. La fecha y hora del error se redondean a la hora más cercana, ya que los datos de telemetría se recopilan cada hora.
* [Mantenimiento](https://pdmmodelingguide.blob.core.windows.net/pdmdata/maint.csv): el registro de mantenimiento contiene registros de mantenimiento programado y no programado. El mantenimiento programado se corresponde con la inspección periódica de los componentes, el mantenimiento no programado puede surgir ante errores mecánicos u otra degradación del rendimiento. La fecha y hora del mantenimiento se redondean a la hora más cercana, ya que los datos de telemetría se recopilan cada hora.
* [Telemetría](https://pdmmodelingguide.blob.core.windows.net/pdmdata/telemetry.csv): los datos de telemetría consisten en medidas de series temporales desde varios sensores en cada máquina. Los datos se registran calculando el promedio de los valores del sensor cada hora.
* [Errores](https://pdmmodelingguide.blob.core.windows.net/pdmdata/failures.csv): los errores corresponden a reemplazos de componentes dentro del registro de mantenimiento. Cada registro contiene el identificador de máquina, el tipo de componente y la fecha y hora de reemplazo. Estos registros se utilizan para crear las etiquetas de aprendizaje automático que está intentando predecir el modelo.

Consulte la [Ingesta de datos](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb) del escenario del Jupyter Notebook en la sección de código para descargar los conjuntos de datos sin formato desde el repositorio de GitHub y crear los conjuntos de datos de PySpark para este análisis.

## <a name="scenario-structure"></a>Estructura del escenario
El contenido del escenario está disponible en el [repositorio de GitHub](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance). 

El archivo [Léame](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/README.md) describe el flujo de trabajo desde la preparación de los datos y la compilación de un modelo hasta la implementación de una solución para producción. Cada paso del flujo de trabajo se encapsula en Jupyter Notebook en la carpeta [Código](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/tree/master/Code) dentro del repositorio.   

[`Code\1_data_ingestion.ipynb`](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb): Este Notebook descarga los cinco archivos .csv de entrada y realiza una limpieza, y una visualización, preliminar de los datos. Notebook convierte los conjuntos de datos al formato de PySpark y los almacena en un contenedor de blobs de Azure para su uso en el bloc de notas de ingeniería ofrecida.

[`Code\2_feature_engineering.ipynb`](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/2_feature_engineering.ipynb): con el conjunto de datos sin formato de un blob de Azure, las características del modelo se construyen con un enfoque de serie temporal estándar para los datos de telemetría, de errores y de mantenimiento. La sustitución de componentes relacionada con errores se usa para construir las etiquetas de modelo con la descripción sobre qué componente generó el error. Los datos de la característica etiquetada se guardan en un blob de Azure para el cuaderno de notas de compilación de modelos.

[`Code\3_model_building.ipynb`](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/3_model_building.ipynb): mediante el conjunto de datos de la característica etiquetada, el Notebook de modelado divide los datos en conjuntos de datos de entrenamiento y de desarrollo junto con una marca de fecha y hora. El Notebook configura los experimentos con modelos `pyspark.ml.classification`. Los datos de entrenamiento se vectorizan y el usuario puede experimentar con un `DecisionTreeClassifier` o un `RandomForestClassifier`, y manipular hiperparámetros para encontrar el modelo con el mejor rendimiento. El rendimiento se determina mediante la evaluación de las estadísticas de medida del conjunto de datos de desarrollo. Estas estadísticas se registran en la pantalla del entorno en tiempo de ejecución de Azure Machine Learning Workbench para realizar un seguimiento. En cada ejecución, el Notebook guarda el modelo resultante en el disco local que ejecuta el kernel de Jupyter Notebook. 

[`Code\4_operationalization.ipynb`](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/4_operationalization.ipynb): mediante la utilización del último modelo guardado en el disco local (kernel de Jupyter Notebook), este Notebook compila los componentes para implementar el modelo como un servicio web de Azure. Todos los recursos operacionales se compactan en el archivo `o16n.zip` almacenado en otro contenedor de blobs de Azure. El archivo comprimido contiene:

* `service_schema.json` El archivo de definición de esquema para la implementación. 
* `pdmscore.py` Las funciones init() y run() que requiere el servicio web de Azure
* `pdmrfull.model` El directorio de definición del modelo.
    
 El Notebook comprueba las funciones con la definición del modelo antes de empaquetar los recursos de operacionalización para la implementación. Al final del Notebook se incluyen las instrucciones para la implementación.

## <a name="conclusion"></a>Conclusión

Este escenario proporciona al lector una visión general de cómo crear una solución de extremo a extremo de mantenimiento predictivo usando PySpark en el entorno de Jupyter Notebook en Azure ML Workbench. Este escenario de ejemplo también describe la implementación de modelos a través del entorno de Administración de modelos de Azure Machine Learning para realizar predicciones de errores en los equipos en tiempo real.

## <a name="references"></a>Referencias

Este caso de uso se ha desarrollado previamente en varias plataformas:

* [Plantilla de solución de mantenimiento predictivo](https://docs.microsoft.com/azure/machine-learning/cortana-analytics-playbook-predictive-maintenance)
* [Guía de modelado de mantenimiento predictivo](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1)
* [Guía de modelado de mantenimiento predictivo con SQL R Services](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-Modeling-Guide-using-SQL-R-Services-1)
* [Guía de modelado de mantenimiento predictivo con Python Notebook](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-Python-Notebook-1)
* [Mantenimiento predictivo con PySpark](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-using-PySpark)

## <a name="next-steps"></a>pasos siguientes

Hay muchos otros escenarios de ejemplo disponibles en Azure Machine Learning Workbench que muestran características adicionales del producto. 