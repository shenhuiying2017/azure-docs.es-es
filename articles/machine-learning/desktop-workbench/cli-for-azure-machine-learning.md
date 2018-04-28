---
title: 'Instalación y uso de la CLI para las tareas principales: Azure Machine Learning'
description: Obtenga información sobre cómo instalar y usar la CLI para las tareas de aprendizaje automático más comunes en Azure Machine Learning.
services: machine-learning
author: haining
ms.author: haining
manager: cgronlun
ms.reviewer: mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/10/2018
ms.openlocfilehash: 45f96884bf6abe1c3868680be29b7f90cc50489d
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
---
# <a name="install-and-use-the-machine-learning-cli-for-top-tasks-in-azure-machine-learning"></a>Instalación y uso de la CLI de aprendizaje automático para las principales tareas en Azure Machine Learning

Los servicios de Azure Machine Learning son una solución integrada y completa de análisis avanzado y ciencia de datos. Los científicos de datos profesionales pueden usar los servicios de Azure Machine Learning a preparar datos, desarrollar experimentos e implementar modelos a escala de nube. 

Azure Machine Learning proporciona una interfaz de la línea de comandos (CLI) con la que puede:
+ Administrar el área de trabajo y los proyectos
+ Configurar los destinos de proceso
+ Ejecutar experimentos de entrenamiento
+ Ver el historial y las métricas de ejecuciones anteriores
+ Implementar modelos en producción como servicios web
+ Administrar los modelos y servicios de producción

En este artículo se presentan algunos de los comandos más útiles de la CLI para su comodidad. 

![CLI de Azure Machine Learning](media/cli-for-azure-machine-learning/flow.png)

## <a name="what-you-need-to-get-started"></a>Qué necesita para empezar

Necesita acceso de colaborador a una suscripción a Azure o a un grupo de recursos donde pueda implementar los modelos. Además, debe instalar Azure Machine Learning Workbench para ejecutar la CLI. 

>[!IMPORTANT]
>La CLI que se entrega con los servicios de Azure Machine Learning es distinta de la [CLI de Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) que se usa para administrar los recursos de Azure.

## <a name="get-and-start-cli"></a>Obtención e inicio de la CLI

Para obtener esta CLI, instale Azure Machine Learning Workbench, que se puede descargar aquí:
    + Windows: https://aka.ms/azureml-wb-msi 
    + MacOS: https://aka.ms/azureml-wb-dmg 

Para iniciar la CLI:
+ En Azure Machine Learning Workbench, inicie la CLI en el menú **Archivo -> Abrir símbolo del sistema**.

## <a name="get-command-help"></a>Obtención de ayuda sobre los comandos 

Puede obtener información adicional sobre los comandos de la CLI con `--debug` o `--help` después de comandos como `az ml <xyz> --debug`, donde `<xyz>` es el nombre del comando. Por ejemplo: 
```azurecli
az ml computetarget --debug 

az ml experiment --help
```

## <a name="common-cli-tasks-for-azure-machine-learning"></a>Tareas comunes de la CLI para Azure Machine Learning 

Obtenga información sobre las tareas más comunes que puede realizar con la CLI en esta sección, entre las que se incluyen:
+ [Configuración de los destinos de proceso](#target)
+ [Envío de trabajos para ejecución remota](#jobs)
+ [Trabajo con Jupyter Notebooks](#jupyter)
+ [Interacción con los historiales de ejecución](#history)
+ [Configuración del entorno para la operacionalización](#o16n)

<a name="target"></a>

### <a name="set-up-a-compute-target"></a>Configuración de un destino de proceso

Puede procesar el modelo de aprendizaje automático en distintos destinos, incluidos:
+ en el modo local
+ en una máquina virtual de ciencia de datos (DSVM)
+ en un clúster de HDInsight

Para adjuntar un destino de máquina virtual de ciencia de datos:
```azurecli
az ml computetarget attach remotedocker -n <target name> -a <ip address or FQDN> -u <username> -w <password>
``` 

Para adjuntar un destino de HDInsight:
```azurecli
az ml computetarget attach cluster -n <target name> -a <cluster name, e.g. myhdicluster-ssh.azurehdinsight.net> -u <ssh username> -w <ssh password>
```

Dentro de la carpeta **aml_config**, puede cambiar las dependencias de conda. 

Además, puede usar PySpark, Python o Python en una DSVM de GPU. 

Para definir el modo de operación de Python:
+ Para Python, agregue `Framework:Python` en `<target name>.runconfig` 

+ Para PySpark, agregue `Framework:PySpark` en `<target name>.runconfig` 

+ Para Python en una DSVM de GPU,
    1. Agregue `Framework:Python` en `<target name>.runconfig` 

    1. Además, agregue `baseDockerImage: microsoft/mmlspark:plus-gpu-0.9.9 and nvidiaDocker:true` en `<target name>.compute`

Para preparar el destino de proceso:
```azurecli
az ml experiment prepare -c <target name>
```

>[!TIP]
>Para mostrar la suscripción:<br/>
>`az account show`<br/>
>
>Para establecer la suscripción:<br/>
>`az account set –s "my subscription name" `

<a name="jobs"></a>

### <a name="submit-remote-jobs"></a>Envío de trabajos remotos

Para enviar un trabajo a un destino remoto:
```azurecli
az ml experiment submit -c <target name> myscript.py
```

<a name="jupyter"></a>

### <a name="work-with-jupyter-notebooks"></a>Trabajo con Jupyter Notebooks

Para iniciar una instancia de Jupyter Notebook:
```azurecli
az ml notebook start
```

Este comando inicia una instancia de Jupyter Notebook en localhost. Puede trabajar localmente si selecciona el kernel Python 3, o bien trabajar en la máquina virtual remota si selecciona el kernel `<target name>`.

<a name="history"></a>

### <a name="interact-with-and-explore-the-run-history"></a>Interacción y exploración del historial de ejecución

Para mostrar el historial de ejecución:
```azurecli
az ml history list -o table
```

Para mostrar todas las ejecuciones completadas:
```azurecli
az ml history list --query "[?status=='Completed']" -o table
```

Para buscar las ejecuciones con la mejor precisión:
```azurecli
az ml history list --query "@[?Accuracy != null] | max_by(@, &Accuracy).Accuracy"
```

También puede descargar los archivos que genera cada ejecución. 

Para promover un modelo guardado en las salidas de carpeta:
```azurecli
az ml history promote -r <run id> -ap outputs/model.pkl -n <model name>
```

Para descargar ese modelo:
```azurecli
az ml asset download -l assets/model.pkl.link -d <model folder path>
```

<a name="o16n"></a>

### <a name="configure-your-environment-to-operationalize"></a>Configuración del entorno para la operacionalización

Para configurar el entorno de operacionalización, debe crear:

> [!div class="checklist"]
> * Un grupo de recursos 
> * Una cuenta de almacenamiento
> * Una instancia de Azure Container Registry (ACR)
> * Una cuenta de Application Insights
> * Una implementación de Kubernetes en un clúster de Azure Container Service (ACS)


Para configurar una implementación local para probar en un contenedor de Docker:
```azurecli
az ml env setup -l <region, e.g. eastus2> -n <env name> -g <resource group name>
```

Para configurar un clúster de ACS con Kubernetes:
```azurecli
az ml env setup -l <region, e.g. eastus2> -n <env name> -g <resource group name> --cluster
```

Para supervisar el estado de la implementación:
```azurecli
az ml env show -n <environment name> -g <resource group name>
```

Para establecer el entorno que se debe usar:
```azurecli
az ml env set -n <environment name> -g <resource group name>
```

## <a name="next-steps"></a>Pasos siguientes

Comience a trabajar con uno de estos artículos: 
+ [Install and start using Azure Machine Learning](../service/quickstart-installation.md) (Instalación de Azure Machine Learning y comenzar a trabajar con él).
+ [Classifying Iris Data Tutorial: Part 1](tutorial-classifying-iris-part-1.md) (Tutorial de clasificación de datos de Iris: parte 1).

Profundice más con uno de estos artículos:
+ [CLI commands for managing models](model-management-cli-reference.md) (Comandos de la CLI para administrar modelos).
