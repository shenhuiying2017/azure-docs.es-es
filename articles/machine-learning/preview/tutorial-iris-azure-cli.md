---
title: "Artículo tutorial sobre las características de la versión preliminar de Azure Machine Learning: interfaz de línea de comandos | Microsoft Docs"
description: "Este tutorial recorre todos los pasos necesarios para completar una clasificación Iris de un extremo a otro mediante la interfaz de línea de comandos."
services: machine-learning
author: ahgyger
ms.author: ahgyger, ritbhat
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 09/20/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: c2a3b9702afd99c29b64133a05515a1b5f395130
ms.contentlocale: es-es
ms.lasthandoff: 09/25/2017

---

# <a name="tutorial-classifying-iris-using-the-command-line-interface"></a>Tutorial: clasificación Iris mediante la interfaz de línea de comandos
Los servicios de Azure Machine Learning (versión preliminar) son una solución integrada de análisis avanzado y ciencia de datos de un extremo a otro diseñada para que los científicos de datos profesionales preparen datos, desarrollen experimentos e implementen modelos a escala de nube.

En este tutorial, aprenderá a usar las herramientas de la interfaz de línea de comandos (CLI) en las características de la versión preliminar de Azure Machine Learning para: 
> [!div class="checklist"]
> * Configurar una cuenta de Experimentación de ML y crear un área de trabajo
> * Creación de un proyecto
> * Enviar un experimento a varios destinos de proceso
> * Promocionar y registrar un modelo entrenado
> * Implementar un servicio web para puntuar nuevos datos

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="prerequisites"></a>Requisitos previos
- Debe tener acceso a una suscripción de Azure y permisos para crear recursos en esa suscripción. 
- Debe instalar la aplicación Azure Machine Learning Workbench siguiendo la [Guía de inicio rápido de instalación y creación](quickstart-installation.md). 

  >[!NOTE]
  >Solo es necesario instalar Azure Machine Learning Workbench de modo local. Solo debe seguir los pasos descritos en la sección titulada Instalación de Azure Machine Learning Workbench, ya que los pasos para la creación de la cuenta y la creación de un nuevo proyecto se realizarán mediante la línea de comandos en este artículo.
 
## <a name="getting-started"></a>Introducción
La interfaz de línea de comandos (CLI) de Azure Machine Learning le permite realizar todas las tareas necesarias para un flujo de trabajo de ciencia de datos de un extremo a otro. Puede tener acceso a las herramientas de la CLI de las maneras siguientes:

### <a name="option-1-launch-azure-ml-cli-from-azure-ml-workbench-log-in-dialog-box"></a>Opción 1. Inicie la CLI de Azure ML desde el cuadro de diálogo de inicio de sesión de Azure ML Workbench
Cuando inicie Azure ML Workbench e inicie sesión por primera vez, si aún no tiene acceso a una cuenta de Experimentación, se le presentará la siguiente pantalla:

![no se encuentra ninguna cuenta](media/tutorial-iris-azure-cli/no_account_found.png)

Haga clic en el vínculo **Ventana de línea de comandos** en el cuadro de diálogo para iniciar la ventana de línea de comandos.

### <a name="option-2-launch-azure-ml-cli-from-azure-ml-workbench-app"></a>Opción 2. Inicie la CLI de Azure ML desde la aplicación Azure ML Workbench
Si ya tiene acceso a una cuenta de Experimentación, podrá iniciar sesión correctamente. Y, a continuación, puede abrir la ventana de línea de comandos al hacer clic en el menú **Archivo** --> **Abrir símbolo del sistema**.

### <a name="option-3-enable-azure-ml-cli-in-an-arbitrary-command-line-window"></a>Opción 3. Habilite la CLI de Azure ML en una ventana de línea de comandos arbitraria
También puede habilitar la CLI de Azure ML en cualquier ventana de línea de comandos. Simplemente inicie una ventana de comandos y escriba los siguientes comandos:

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

## <a name="step-1-log-in-to-azure"></a>Paso 1. Inicie sesión en Azure.
El primer paso es abrir la CLI desde la aplicación AMLWorkbench (Archivo > Abrir símbolo del sistema). De este modo, se asegura de usar el entorno de Python correcto y tendrá disponibles los comandos de la CLI de ML. 

A continuación, es necesario establecer el contexto adecuado en la CLI para tener acceso y administrar los recursos de Azure.
 
```bash
az login
az account set -s d128f140-94e6-1206-80a7-954b9d27d007
```

> [!TIP]
> Para obtener una lista de todas las suscripciones, ejecute: 
>```
>az account list -o table
>```

## <a name="step-2-create-a-new-azure-machine-learning-experimentation-account-and-workspace"></a>Paso 2: Creación de una nueva cuenta de Experimentación de Azure Machine Learning y un área de trabajo
Comenzamos creando una nueva cuenta de Experimentación y un área de trabajo nueva. Consulte los [conceptos de Azure Machine Learning](overview-general-concepts.md) para más información acerca de las cuentas de Experimentación y áreas de trabajo. 

> [!NOTE]
> Las cuentas de Experimentación requieren una cuenta de almacenamiento, que se utiliza para almacenar las salidas de las ejecuciones de los experimentos. El nombre de la cuenta de almacenamiento tiene que ser único globalmente en Azure porque hay una dirección URL asociada a él. El nombre de la cuenta de Experimentación se usa para crear, en su nombre, una cuenta de almacenamiento. Asegúrese de usar un nombre único o se producirá un error similar a _"ya está en uso la cuenta de almacenamiento llamada amlsampleexp."_ Como alternativa, puede usar el argumento `--storage` para usar una cuenta de almacenamiento existente.

```bash
az group create --name amlsample --location eastus2
az ml account experimentation create --name amlsampleexp --resource-group amlsample
az ml account experimentation create --name amlsampleexp --resource-group amlsample --storage /subscriptions/6d48cffb-b787-47bd-8d20-1696afa33b67/resourceGroups/existing/providers/Microsoft.Storage/storageAccounts/mystorageacct
az ml workspace create --name amlsamplew --account amlsampleexp --resource-group amlsample
```

## <a name="step-2a-optional-share-a-workspace-with-co-worker"></a>Paso 2.a (opcional) Compartición de un área de trabajo con compañero de trabajo
A continuación, exploramos cómo compartir el acceso a un área de trabajo con un compañero de trabajo. Los pasos para compartir el acceso a una cuenta de Experimentación o a un proyecto serían los mismos. Solo la forma de obtener el identificador de recurso de Azure debe actualizarse.

```bash
az ml workspace show --name amlsamplew --account amlsampleexp --resource-group amlsample 
az role assignment create --assignee ahgyger@microsoft.com --role owner --scope "/subscriptions/d128f140-94e6-4175-87a7-954b9d27db16/resourceGroups/amlsample/providers/Microsoft.MachineLearningExperimentation/accounts/amlsampleexp/workspaces/amlsamplew"
```

> [!TIP]
> Debe utilizar la dirección de correo electrónico real del compañero de trabajo, no un alias. 

## <a name="step-3-create-a-new-project"></a>Paso 3: Crear un nuevo proyecto
El siguiente paso consiste en crear un nuevo proyecto. Hay varias maneras de empezar a trabajar con un nuevo proyecto.

### <a name="create-a-new-blank-project"></a>Creación de un proyecto en blanco nuevo

```bash
az ml project create --name 9_25_1 --workspace amlsamplew --account amlsampleexp --resource-group amlsample --path c:\Users\ahgyger\Documents\AMLworkbench_Demo\9_25\
```

### <a name="create-a-new-project-with-template-files"></a>Creación de un nuevo proyecto con archivos de plantilla
Los archivos de plantilla no son ejemplos, pero proporcionan un marco para el nuevo proyecto. El proyecto se rellena previamente con dos archivos: `train.py` y `score.py`.

```bash
az ml project create --name 9_25_1 --workspace amlsamplew --account amlsampleexp --resource-group amlsample --path c:\Users\ahgyger\Documents\AMLworkbench_Demo\ --template
```

### <a name="create-a-new-project-associated-with-a-cloud-git-repository"></a>Creación de un nuevo proyecto asociado a un repositorio de Git de la nube
Podemos crear un nuevo proyecto asociado a un repositorio de Git de la nube. Cada vez que se envía un experimento, se toma una instantánea del contenido del proyecto como una confirmación de Git en la bifurcación del historial de ejecución. Consulte [Uso del repositorio de Git con un proyecto de Azure Machine Learning Workbench](using-git-ml-project.md) para más detalles.

> [!NOTE]
> Azure Machine Learning solo admite proyectos de equipo vacíos (VSTS) con Git como control de versiones.

> [!TIP]
> Si recibe un error "La dirección URL del repositorio puede no ser válida o puede que el usuario no tenga acceso", puede crear un token de seguridad en VSTS (Seguridad, Agregar tokens de acceso personal) y usar el argumento __vststoken__ al crear el proyecto. 

```bash
az ml project create --name 9_25_2 --workspace amlsamplew --account amlsampleexp --resource-group amlsample --path c:\Users\ahgyger\Documents\AMLworkbench_Demo\ --repo https://ahgyger.visualstudio.com/AMLWorkbench/_git/9_25 --vststoken m2fholwwrcn7u4nrdqfxx007u5rztjfhgofnemvuvtue6pbwo3sa
```

### <a name="sample_create"></a>Creación de un nuevo proyecto a partir de un ejemplo en línea
En este ejemplo, se usa una plantilla de un proyecto de la base de datos central de Git para crear el nuevo proyecto. 

```bash
# List the project samples
az ml project sample list

# Create a new project from a sample
az ml project create --name 9_25_3 --workspace amlsamplew --account amlsampleexp --resource-group amlsample --path c:\Users\ahgyger\Documents\AMLworkbench_Demo\ --repo https://ahgyger.visualstudio.com/AMLWorkbench/_git/9_25 --template-url https://github.com/MicrosoftDocs/MachineLearningSamples-Iris
```

Una vez creado el proyecto, cambie el directorio al del proyecto antes de ir al paso siguiente.

```bash
cd c:\Users\ahgyger\Documents\AMLworkbench_Demo\9_25\9_25_1
```

## <a name="step-4-run-the-training-experiment"></a>Paso 4 Ejecución del experimento de entrenamiento 
En los pasos siguientes se supone que tiene un proyecto con el ejemplo de Iris (consulte [Creación de un nuevo proyecto a partir de un ejemplo en línea](#sample_create)).

### <a name="prepare-your-environment"></a>Preparación del entorno 
Para obtener el ejemplo de Iris, es necesario instalar matplotlib.

```bash
pip install matplotlib
```

###  <a name="submit-the-experiment"></a>Envío del experimento

```bash
# Execute the file
az ml experiment submit --run-configuration local iris_sklearn.py
```

### <a name="iterate-on-your-experiment-with-descending-regularization-rates"></a>Repetición del experimento con tasas de regularización descendentes
Con alguna creatividad, es fácil crear un script de Python que envíe los experimentos con tasas de regularización diferentes. (Tendrá que editar el archivo para que apunte a la ruta de acceso del proyecto correcto.)

```bash
python run.py
```

## <a name="step-5-view-run-history"></a>Paso 5. Visualización del historial de ejecución
El siguiente comando enumera todas las ejecuciones anteriores. 

```bash
az ml history list -o table
```
Al ejecutar el comando anterior se mostrará una lista de todas las ejecuciones que pertenecen a este proyecto. Puede ver que las métricas de precisión y tasa de regularización también se enumeran. Esto hace sencillo identificar la mejor ejecución de la lista.

## <a name="step-5a-view-attachment-created-by-a-given-run"></a>Paso 5.a Visualización de datos adjuntos creados por una ejecución específica 
Para ver los datos adjuntos asociados a una ejecución determinada, podemos usar el comando info del historial de ejecución.

```bash
az ml history info --run 9_16_4_1505589545267 --artifact driver_log
```

Para descargar los artefactos de una ejecución, puede utilizar comando siguiente:

```bash
# Stream a given attachment 
az ml history info --run <run id> --artifact <artifact location>
```

## <a name="step-6-promote-artifacts-of-a-run"></a>Paso 6. Promoción de los artefactos de una ejecución 
Una de las ejecuciones que hemos realizado tiene mejor AUC, por lo que se usará para crear un servicio web de puntuación para su implementación en producción. Para ello, debemos promocionar los artefactos en un recurso.

```bash
az ml history promote --run 9_25_1505346632975 --artifact-path outputs/model.pkl --name model.pkl
```

Esto crea una carpeta __assets__ en el directorio del proyecto que contiene el archivo pickle.link. Este archivo de vínculo se usa para realizar el seguimiento de la versión del archivo promovido.

## <a name="step-7-download-the-files-to-be-operationalized"></a>Paso 7. Descarga de los archivos para su operacionalización
Ahora tenemos que descargar los archivos promocionados, para que se pueden usar para crear el servicio web de predicción. 

```bash
az ml asset download --link-file assets\pickle.link -d asset_download
```

## <a name="step-8-setup-your-model-management-environment"></a>Paso 8. Configuración del entorno de administración del modelo 
Creamos un entorno para implementar servicios web. Se puede ejecutar el servicio web en el equipo local con Docker. O implementarlo en un clúster de ACS para operaciones de gran escala. 

```bash
# Create new environment
az ml env setup -l eastus2 -n amlsamplesenv
# Once setup is complete, set your environment for current context
az ml env set -g amlsamplesenvrg -n amlsamplesenv
```

## <a name="step-9-create-a-model-management-account"></a>Paso 9. Creación de una cuenta de administración del modelo 
Se requiere una cuenta de administración del modelo para implementar y realizar un seguimiento de los modelos en producción. 

```bash
az ml account modelmanagement create -n amlsamplesacct -g amlsamplesenvrg -l eastus2
```

## <a name="step-10-create-a-web-service"></a>Paso 10. Creación de un servicio web
A continuación, creamos un servicio web que devuelve una predicción usando el modelo que se ha implementado. 

```bash
az ml service create realtime -m modelfilename -f score.py -r python –n amlsamplews
```

## <a name="step-10-run-the-web-service"></a>Paso 10. Ejecución del servicio web
Con el identificador del servicio web de la salida del paso anterior, podemos llamar al servicio web y probarlo. 

```
# Get web service usage infomration
az ml service usage realtime -i <web service id>

# Call the web service with the run command:
az ml service run realtime -i <web service id> -d <input data>
```

## <a name="deleting-all-the-resources"></a>Eliminación de todos los recursos 
Vamos a finalizar este tutorial con la eliminación de todos los recursos que hemos creado, a menos que desee seguir trabajando con ellos. 

Para ello, simplemente eliminamos el grupo de recursos que contiene todos los recursos. 

```bash
az group delete --name amlsample
az group delete --name amlsamplesenvrg
```

## <a name="next-steps"></a>Pasos siguientes
En este tutorial, ha aprendido a usar las características de la versión preliminar de Azure Machine Learning para 
> [!div class="checklist"]
> * Configurar una cuenta de Experimentación de ML y crear un área de trabajo
> * Crear proyectos
> * Enviar experimentos a varios destinos de proceso
> * Promocionar y registrar un modelo entrenado
> * Crear una cuenta de administración del modelo para la administración del modelo
> * Crear un entorno para implementar un servicio web
> * Implementar un servicio web y realizar la puntuación con nuevos datos


