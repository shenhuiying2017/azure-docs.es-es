---
title: "Artículo tutorial sobre las características de la versión preliminar de Azure Machine Learning: interfaz de línea de comandos | Microsoft Docs"
description: "Este tutorial recorre todos los pasos necesarios para completar una clasificación de iris de un extremo a otro mediante la interfaz de línea de comandos."
services: machine-learning
author: ahgyger
ms.author: ahgyger, ritbhat
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: tutorial
ms.date: 10/15/2017
ms.openlocfilehash: ad81cd02ba0c46cbe58de7071d2164aaefea6514
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="tutorial-classifying-iris-using-the-command-line-interface"></a>Tutorial: Clasificación de iris mediante la interfaz de línea de comandos
Los servicios de Azure Machine Learning (versión preliminar) son una solución de análisis avanzado y ciencia de datos completa e integrada que permite a los científicos de datos profesionales preparar datos, desarrollar experimentos e implementar modelos a escala de la nube.

En este tutorial, aprenderá a usar las herramientas de la interfaz de línea de comandos (CLI) en las características de la versión preliminar de Azure Machine Learning para: 
> [!div class="checklist"]
> * Configurar una cuenta de Experimentación de ML y crear un área de trabajo
> * Crear un proyecto
> * Enviar un experimento a varios destinos de proceso
> * Promocionar y registrar un modelo entrenado
> * Implementar un servicio web para puntuar nuevos datos

## <a name="prerequisites"></a>Requisitos previos
Para completar este tutorial, necesita:
- Tener acceso a una suscripción de Azure y permisos para crear recursos en esa suscripción. 
  
  Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

- Tener instalada la aplicación Azure Machine Learning Workbench tal y como se describe en [Quickstart: Install and start Azure Machine Learning services](quickstart-installation.md) (Inicio rápido: instalación e inicio de los servicios de Azure Machine Learning). 

  >[!IMPORTANT]
  >No cree cuentas de servicios de Azure Machine Learning, ya que lo hará mediante la CLI en este artículo.
 
## <a name="getting-started"></a>Introducción
La interfaz de línea de comandos (CLI) de Azure Machine Learning le permite realizar todas las tareas necesarias para un flujo de trabajo de ciencia de datos de un extremo a otro. Puede tener acceso a las herramientas de la CLI de las maneras siguientes:

### <a name="option-1-launch-azure-ml-cli-from-azure-ml-workbench-log-in-dialog-box"></a>Opción 1. Inicie la CLI de Azure ML desde el cuadro de diálogo de inicio de sesión de Azure ML Workbench
Cuando inicie Azure ML Workbench e inicie sesión por primera vez, si aún no tiene acceso a una cuenta de Experimentación, se le presentará la siguiente pantalla:

![no se encuentra ninguna cuenta](media/tutorial-iris-azure-cli/no_account_found.png)

Haga clic en el vínculo **Command Line Window** (Ventana de línea de comandos) en el cuadro de diálogo para iniciar la ventana de línea de comandos.

### <a name="option-2-launch-azure-ml-cli-from-azure-ml-workbench-app"></a>Opción 2. Inicie la CLI de Azure ML desde la aplicación Azure ML Workbench
Si ya tiene acceso a una cuenta de Experimentación, podrá iniciar sesión correctamente. Y, a continuación, puede abrir la ventana de línea de comandos al hacer clic en el menú **File** --> **Open Command Prompt** (Archivo > Abrir símbolo del sistema).

### <a name="option-3-enable-azure-ml-cli-in-an-arbitrary-command-line-window"></a>Opción 3. Habilite la CLI de Azure ML en una ventana de línea de comandos arbitraria
También puede habilitar la CLI de Azure ML en cualquier ventana de línea de comandos. Para ello, inicie una ventana de comandos y escriba los siguientes comandos:

```sh
# Windows Command Prompt
set PATH=%LOCALAPPDATA%\amlworkbench\Python;%LOCALAPPDATA%\amlworkbench\Python\Scripts;%PATH%

# Windows PowerShell
$env:Path = $env:LOCALAPPDATA+"\amlworkbench\Python;"+$env:LOCALAPPDATA+"\amlworkbench\Python\Scripts;"+$env:Path

# macOS Bash Shell
PATH=$HOME/Library/Caches/AmlWorkbench/Python/bin:$PATH
```
Para hacer permanente el cambio, puede usar `SETX` en Windows. Para macOS, puede usar `setenv`.

>[!TIP]
>Puede habilitar la CLI de Azure en su ventana de terminal favorita estableciendo las variables de entorno anteriores.

## <a name="step-1-log-in-to-azure"></a>Paso 1. Inicio de sesión en Azure
El primer paso es abrir la CLI desde la aplicación AMLWorkbench: File > Open Command Prompt (Archivo > Abrir símbolo del sistema). De este modo, se asegura de tener el entorno de Python correcto y de que los comandos de la CLI de ML estarán disponibles. 

A continuación, puede establecer el contexto adecuado en la CLI para tener acceso y administrar los recursos de Azure.
 
```azure-cli
# log in
$ az login

# list all subscriptions
$ az account list -o table

# set the current subscription
$ az account set -s <subscription id or name>
```

## <a name="step-2-create-a-new-azure-machine-learning-experimentation-account-and-workspace"></a>Paso 2. Creación de una nueva cuenta de Experimentación de Azure Machine Learning y un área de trabajo

En este paso, crea una nueva cuenta de Experimentación y un área de trabajo nueva. Consulte los [conceptos de Azure Machine Learning](overview-general-concepts.md) para más información acerca de las cuentas de Experimentación y áreas de trabajo.

> [!NOTE]
> Las cuentas de Experimentación requieren una cuenta de almacenamiento, que se utiliza para almacenar las salidas de las ejecuciones de los experimentos. El nombre de la cuenta de almacenamiento tiene que ser único globalmente en Azure porque hay una dirección URL asociada a él. Si no especifica una cuenta de almacenamiento existente, se usa el nombre de la cuenta de experimentación para crear una nueva cuenta de almacenamiento. Asegúrese de usar un nombre único o se producirá un error similar a _"The storage account named \<storage_account_name> is already taken."_ (La cuenta de almacenamiento llamada nombre_cuenta_almacenamiento > ya está en uso). Como alternativa, puede usar el argumento `--storage` para proporcionar una cuenta de almacenamiento existente.

```azure-cli
# create a resource group 
$ az group create --name <resource group name> --location <supported Azure region>

# create a new experimentation account with a new storage account
$ az ml account experimentation create --name <experimentation account name> --resource-group <resource group name>

# create a new experimentation account with an existing storage account
$ az ml account experimentation create --name <experimentation account name>  --resource-group <resource group name> --storage <storage account Azure Resource ID>

# create a workspace in the experimentation account
az ml workspace create --name <workspace name> --account <experimentation account name> --resource-group <resource group name>
```

## <a name="step-2a-optional-share-a-workspace-with-co-worker"></a>Paso 2.a (opcional) Uso compartido de un área de trabajo con compañeros de trabajo
Aquí, puede explorar cómo compartir el acceso a un área de trabajo con un compañero de trabajo. Los pasos para compartir el acceso a una cuenta de Experimentación o a un proyecto serían los mismos. Solo la forma de obtener el identificador de recurso de Azure debe actualizarse.

```azure-cli
# find the workspace Azure Resource ID
$az ml workspace show --name <workspace name> --account <experimentation account name> --resource-group <resource group name>

# add Bob to this workspace as a new owner
$az role assignment create --assignee bob@contoso.com --role owner --scope <workspace Azure Resource ID>
```

> [!TIP]
> En el comando anterior, `bob@contoso.com` debe ser una identidad de Azure AD válida en el directorio al que pertenece la suscripción actual.

## <a name="step-3-create-a-new-project"></a>Paso 3. Creación de un nuevo proyecto
El siguiente paso consiste en crear un nuevo proyecto. Hay varias maneras de empezar a trabajar con un nuevo proyecto.

### <a name="create-a-new-blank-project"></a>Creación de un proyecto en blanco nuevo

```azure-cli
# create a new project
$ az ml project create --name <project name> --workspace <workspace name> --account <experimentation account name> --resource-group <resource group name> --path <local folder path>
```

### <a name="create-a-new-project-with-a-default-project-template"></a>Creación de un nuevo proyecto con una plantilla de proyecto predeterminada
Puede crear un nuevo proyecto con una plantilla predeterminada.

```azure-cli
$ az ml project create --name <project name> --workspace <workspace name> --account <experimentation account name> --resource-group <resource group name> --path <local folder path> --template
```

### <a name="create-a-new-project-associated-with-a-cloud-git-repository"></a>Creación de un nuevo proyecto asociado a un repositorio de Git de la nube
Puede crear un nuevo proyecto asociado a un repositorio de Git de VSTS (Visual Studio Team Service). Cada vez que se envía un experimento, se confirma en el repositorio de Git remoto una instantánea de la carpeta de proyecto completa. Consulte [Uso del repositorio de Git con un proyecto de Azure Machine Learning Workbench](using-git-ml-project.md) para más detalles.

> [!NOTE]
> Azure Machine Learning solo admite repositorios de Git vacíos creados en VSTS.

```azure-cli
$ az ml project create --name <project name> --workspace <workspace name> --account <experimentation account name> --resource-group <resource group name> --path <local folder path> --repo <VSTS repo URL>
```
> [!TIP]
> Si recibe un error "La dirección URL del repositorio puede no ser válida o puede que el usuario no tenga acceso", puede crear un token de seguridad en VSTS (en el menú _Seguridad_, _Agregar tokens de acceso personal_) y usar el argumento `--vststoken` al crear el proyecto. 

### <a name="sample_create"></a>Creación de un nuevo proyecto a partir de un ejemplo
En este ejemplo, crea un nuevo proyecto con un proyecto de ejemplo como plantilla.

```azure-cli
# List the project samples, find the Classifying Iris sample
$ az ml project sample list

# Create a new project from the sample
az ml project create --name <project name> --workspace <workspace name> --account <experimentation account name> --resource-group <resource group name> --path <local folder path> --template-url https://github.com/MicrosoftDocs/MachineLearningSamples-Iris
```
Una vez creado el proyecto, utilice el comando `cd` para especificar el directorio del proyecto.

## <a name="step-4-run-the-training-experiment"></a>Paso 4. Ejecución del experimento de entrenamiento 
En los pasos siguientes se supone que tiene un proyecto con el ejemplo Iris (consulte [Creación de un nuevo proyecto a partir de un ejemplo en línea](#sample_create)).

### <a name="prepare-your-environment"></a>Preparación del entorno 
Para obtener el ejemplo Iris, debe instalar matplotlib.

```azure-cli
$ pip install matplotlib
```

###  <a name="submit-the-experiment"></a>Envío del experimento

```azure-cli
# Execute the file
$ az ml experiment submit --run-configuration local iris_sklearn.py
```

### <a name="iterate-on-your-experiment-with-descending-regularization-rates"></a>Repetición del experimento con tasas de regularización descendentes
Con alguna creatividad, es fácil crear un script de Python que envíe los experimentos con tasas de regularización diferentes. (Tendrá que editar el archivo para que apunte a la ruta de acceso del proyecto correcto).

```azure-cli
$ python run.py
```

## <a name="step-5-view-run-history"></a>Paso 5. Visualización del historial de ejecuciones
El siguiente comando enumera todas las ejecuciones anteriores. 

```azure-cli
$ az ml history list -o table
```
Al ejecutar el comando anterior, se muestra una lista de todas las ejecuciones que pertenecen a este proyecto. Puede ver que las métricas de precisión y tasa de regularización también se enumeran. Esto hace sencillo identificar la mejor ejecución de la lista.

## <a name="step-5a-view-attachment-created-by-a-given-run"></a>Paso 5.a Visualización de los datos adjuntos creados por una ejecución específica 
Para ver los datos adjuntos asociados a una ejecución determinada, puede usar el comando info del historial de ejecución. Busque un identificador de ejecución de una ejecución específica de la lista anterior.

```azure-cli
$ az ml history info --run <run id> --artifact driver_log
```

Para descargar los artefactos de una ejecución, puede utilizar comando siguiente:

```azure-cli
# Stream a given attachment 
$ az ml history info --run <run id> --artifact <artifact location>
```

## <a name="step-6-promote-artifacts-of-a-run"></a>Paso 6. Promoción de los artefactos de una ejecución 
Una de las ejecuciones tiene mejor AUC, por lo que se debe usar para crear un servicio web de puntuación para su implementación en producción. Para ello, debe promover los artefactos en un recurso.

```azure-cli
$ az ml history promote --run <run id> --artifact-path outputs/model.pkl --name model.pkl
```

Esto crea una carpeta `assets` en el directorio del proyecto con un archivo `model.pkl.link`. Este archivo de vínculo se usa para hacer referencia a un recurso promovido.

## <a name="step-7-download-the-files-to-be-operationalized"></a>Paso 7. Descarga de los archivos para su operacionalización
Descargue el modelo promovido a fin de que pueda usarlo para crear un servicio web de predicción. 

```azure-cli
$ az ml asset download --link-file assets\pickle.link -d asset_download
```

## <a name="step-8-set-up-your-model-management-environment"></a>Paso 8. Configuración del entorno de administración del modelo 
Cree un entorno para implementar servicios web. Puede ejecutar el servicio web en la máquina local con Docker. O implementarlo en un clúster de ACS para operaciones de gran escala. 

```azure-cli
# Create new local operationalization environment
$ az ml env setup -l <supported Azure region> -n <env name>
# Once setup is complete, set your environment for current context
$ az ml env set -g <resource group name> -n <env name>
```

## <a name="step-9-create-a-model-management-account"></a>Paso 9. Creación de una cuenta de administración del modelo 
Se requiere una cuenta de administración del modelo para implementar y realizar un seguimiento de los modelos en producción. 

```azure-cli
$ az ml account modelmanagement create -n <model management account name> -g <resource group name> -l <supported Azure region>
```

## <a name="step-10-create-a-web-service"></a>Paso 10. Creación de un servicio web
Cree un servicio web que devuelva una predicción mediante el modelo que ha implementado. 

```azure-cli
$ az ml service create realtime -m asset_download/model.pkl -f score_iris.py -r python –n <web service name>
```

## <a name="step-11-run-the-web-service"></a>Paso 11. Ejecución del servicio web
Con el identificador del servicio web de la salida del paso anterior, llame al servicio web y pruébelo. 

```azure-cli
# Get web service usage infomration
$ az ml service usage realtime -i <web service id>

# Call the web service with the run command:
$ az ml service run realtime -i <web service id> -d <input data>
```

## <a name="step-12-deleting-all-the-resources"></a>Paso 12. Eliminación de todos los recursos 
Vamos a finalizar este tutorial con la eliminación de todos los recursos que se han creado, a menos que desee seguir trabajando con ellos. 

Para ello, elimine el grupo de recursos que contiene los recursos. 

```azure-cli
az group delete --name <resource group name>
```

## <a name="next-steps"></a>Pasos siguientes
En este tutorial, ha aprendido a usar Azure Machine Learning para: 
> [!div class="checklist"]
> * Configurar una cuenta de Experimentación de ML y crear un área de trabajo
> * Crear proyectos
> * Enviar experimentos a varios destinos de proceso
> * Promocionar y registrar un modelo entrenado
> * Crear una cuenta de administración del modelo para la administración del modelo
> * Crear un entorno para implementar servicios web
> * Implementar un servicio web y realizar la puntuación con nuevos datos
