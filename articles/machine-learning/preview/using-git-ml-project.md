---
title: Uso de un repositorio de Git con un proyecto de Azure Machine Learning Workbench | Microsoft Docs
description: "En este artículo se explica cómo usar un repositorio de Git junto con un proyecto de Azure Machine Learning Workbench."
services: machine-learning
author: hning86
ms.author: haining
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 11/18/2017
ms.openlocfilehash: f4f1112fe68bdb2a26f68b3da08fe97f9d22d3b7
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/06/2017
---
# <a name="use-a-git-repo-with-a-machine-learning-workbench-project"></a>Uso de un repositorio de Git con un proyecto de Machine Learning Workbench
Aprenda cómo Azure Machine Learning Workbench usa Git para proporcionar control de versiones y garantizar la capacidad de reproducción en el experimento de ciencia de datos. Aprenda a asociar el proyecto con un repositorio de Git en la nube.

Machine Learning Studio se ha diseñado para la integración de Git. Al crear un nuevo proyecto, la carpeta del proyecto se inicializa automáticamente en Git como un repositorio de Git local. También se crea un segundo repositorio de Git local oculto, con una rama denominada AzureMLHistory/\<GUID del proyecto\>. La rama realiza un seguimiento de los cambios de la carpeta de proyecto para cada ejecución. 

La asociación del proyecto de Azure Machine Learning a un repositorio de Git habilita el control automático de versiones, local y remotamente. El repositorio de Git está hospedado en Visual Studio Team Services (Team Services). Dado que el proyecto de Machine Learning está asociado a un repositorio de Git, cualquier persona que tenga acceso al repositorio remoto puede descargar el código fuente más reciente a otro equipo (itinerancia).  

> [!NOTE]
> Team Services tiene su propia lista de control de acceso (ACL) que es independiente del servicio Experimentación de Azure Machine Learning. El acceso de usuario podría variar entre un repositorio de Git y un proyecto o área de trabajo de Machine Learning. Puede que tenga que administrar el acceso. 
> 
> Si desea conceder a un miembro del equipo acceso de nivel de código a su proyecto de Machine Learning o simplemente compartir el área de trabajo, debe conceder al usuario los permisos correctos para acceder al repositorio de Git de Team Services. 

Para administrar el control de versiones con Git, puede usar la rama maestra o crear otras ramas en el repositorio. También puede usar el repositorio de Git local e insertarlo en el repositorio de Git remoto, si está aprovisionado.

Este diagrama representa la relación entre un repositorio de Git de Team Services y un proyecto de Machine Learning:

![Git de Azure Machine Learning](media/using-git-ml-project/aml_git.png)

Para comenzar a usar un repositorio de Git remoto, complete los pasos que se describen en las secciones siguientes.

> [!NOTE]
> Actualmente, Azure Machine Learning admite repositorios de Git solo en cuentas de Team Services. La compatibilidad con repositorios de Git generales, como GitHub, está prevista para el futuro.

## <a name="step-1-create-a-machine-learning-experimentation-account"></a>Paso 1. Crear una cuenta de Experimentación de Machine Learning
Cree una cuenta de Experimentación de Machine Learning e instale la aplicación Azure Machine Learning Workbench. Para más información, vea [Creación de cuentas de la versión preliminar de Azure Machine Learning e instalación de Azure Machine Learning Workbench](quickstart-installation.md).

## <a name="step-2-create-a-team-project-or-use-an-existing-team-project"></a>Paso 2: Crear un proyecto de equipo o usar un proyecto de equipo existente
En [Azure Portal](https://portal.azure.com/), cree un nuevo proyecto de equipo:
1. Seleccione **+**.
2. Busque **Proyecto de equipo**.
3. Escriba la información necesaria:
    - **Nombre**: nombre de equipo.
    - **Control de versiones**: seleccione **Git**.
    - **Suscripción**: seleccione una suscripción que tenga una cuenta de Experimentación de Machine Learning.
    - **Ubicación**: lo ideal es elegir una región cercana a los recursos de Experimentación de Machine Learning.
4. Seleccione **Crear**. 

![Creación de un proyecto de equipo en Azure Portal](media/using-git-ml-project/create_vsts_team.png)

Asegúrese de que inicia sesión con la misma cuenta de Azure Active Directory (Azure AD) que usa para acceder a Machine Learning Workbench. En caso contrario, el sistema no puede acceder a Machine Learning Workbench mediante las credenciales de Azure AD. Una excepción es si usa la línea de comandos para crear el proyecto de Machine Learning y proporciona un token de acceso personal para acceder al repositorio de Git. Analizaremos esto con más detalle más adelante en el artículo.

Para ir directamente al proyecto de equipo que ha creado, use la dirección URL https://\<nombre del proyecto de equipo\>.visualstudio.com.

## <a name="step-3-set-up-a-machine-learning-project-and-git-repo"></a>Paso 3: Configurar un proyecto de Machine Learning y el repositorio de Git

Para configurar un proyecto de Machine Learning, tiene dos opciones:
- Crear un proyecto de Machine Learning que tiene un repositorio de Git remoto
- Asociar un proyecto de Machine Learning existente a un repositorio de Git de Team Services

### <a name="create-a-machine-learning-project-that-has-a-remote-git-repo"></a>Creación de un proyecto de Machine Learning que tiene un repositorio de Git remoto
Abra Machine Learning Workbench y cree un nuevo proyecto. En el cuadro **Git repo** (Repositorio de Git), escriba la dirección URL del repositorio de Git de Team Services del paso 2. Normalmente tendrá este formato: https://\<nombre de cuenta de Team Services\>.visualstudio.com/_git/\<nombre del proyecto\>

![Creación de un proyecto de Machine Learning que tiene un repositorio de Git](media/using-git-ml-project/create_project_with_git_rep.png)

También se puede crear el proyecto mediante la herramienta de la línea de comandos de Azure (CLI de Azure). Tiene la opción de especificar un token de acceso personal. Machine Learning puede utilizar este token para acceder al repositorio de Git en lugar de usar sus credenciales de Azure AD:

```
# Create a new project that has a Git repo by using a personal access token.
$ az ml project create -a <Experimentation account name> -n <project name> -g <resource group name> -w <workspace name> -r <Git repo URL> --vststoken <Team Services personal access token>
```

> [!IMPORTANT]
> Si elige la plantilla de proyecto en blanco, el repositorio de Git que elije para usar ya podría tener una rama maestra. Machine Learning simplemente clona la rama maestra localmente. Agrega la carpeta aml_config y otros archivos de metadatos de proyecto a la carpeta del proyecto local. 
>
> Si elige cualquier otra plantilla de proyecto, su repositorio de Git ya *no puede* tener una rama maestra. Si la tiene, aparecerá un error. La alternativa es usar el comando `az ml project create` para crear el proyecto, con un modificador `--force`. Esta acción elimina los archivos de la rama maestra original y los reemplaza por los nuevos archivos en la plantilla que elija.

Se crea un nuevo proyecto de Machine Learning, con la integración del repositorio de Git habilitada. La carpeta de proyecto siempre se inicializa en Git como un repositorio de Git local. El Git remoto se establece en el repositorio de Git de Team Services remoto de forma que se pueden insertar confirmaciones en el repositorio de Git remoto.

### <a name="associate-an-existing-machine-learning-project-with-a-team-services-git-repo"></a>Asociación de un proyecto de Machine Learning existente a un repositorio de Git de Team Services
Puede crear un proyecto de Machine Learning sin un repositorio de Git de Team Services y confiar solamente en el repositorio de Git local para las instantáneas del historial de ejecución. Más adelante, puede asociar un repositorio de Git de Team Services a este proyecto de Machine Learning existente mediante el comando siguiente:

```azurecli
# Ensure that you are in the project path so Azure CLI has the context of your current project.
$ az ml project update --repo https://<Team Services account name>.visualstudio.com/_git/<project name>
```

> [!NOTE] 
> Puede realizar la operación de actualización de repositorio solo en un proyecto de Machine Learning que no tenga un repositorio de Git asociado. Además, después de asociar un repositorio de Git a Machine Learning, no puede quitarlo.

## <a name="step-4-capture-a-project-snapshot-in-the-git-repo"></a>Paso 4 Capturar una instantánea de proyecto en el repositorio de Git
Lleve a cabo unas cuantas ejecuciones de script en el proyecto y realice algunos cambios entre ellas. Puede hacerlo en la aplicación de escritorio o desde la CLI de Azure con el comando `az ml experiment submit`. Para más información, consulte el [tutorial de la clasificación de Iris](tutorial-classifying-iris-part-1.md). En cada ejecución, si se realiza algún cambio en los archivos de la carpeta de proyecto, se confirma e inserta una instantánea de la carpeta de proyecto entera en el repositorio de Git remoto en una rama denominada AzureMLHistory/\<GUID del proyecto\>. Para ver las ramas y las confirmaciones, incluida la rama AzureMLHistory/\<GUID del proyecto\>, vaya a la dirección URL del repositorio de Git de Team Services. 

> [!NOTE] 
> La instantánea solo se confirma antes de una ejecución de script. Actualmente, una ejecución de preparación de datos o una ejecución de celdas de Notebook no desencadena la instantánea.

![Rama de historial de ejecución](media/using-git-ml-project/run_history_branch.png)

> [!IMPORTANT] 
> Es mejor si no trabaja en la rama de historial mediante comandos de Git. Podría interferir con el historial de ejecución. En su lugar, use la rama maestra o cree otras ramas para sus propias operaciones de Git.

## <a name="step-5-restore-a-previous-project-snapshot"></a>Paso 5. Restauración de una instantánea anterior del proyecto 
Para restaurar la carpeta de proyecto entera al estado de una instantánea de historial de ejecución, en Machine Learning Workbench:
1. En la barra de actividad (icono de reloj de arena), seleccione **Ejecuciones**.
2. En la vista **Lista de ejecuciones**, seleccione la ejecución que desea restaurar.
3. En la vista **Detalles de ejecución**, seleccione **Restaurar**.

![Rama de historial de ejecución](media/using-git-ml-project/restore_project.png)

Si lo desea, puede usar los siguientes comandos en la ventana de la CLI de Azure en Machine Learning Workbench:

```azurecli
# Discover the run I want to restore a snapshot from.
$ az ml history list -o table

# Restore the snapshot from a specific run.
$ az ml project restore --run-id <run ID>
```

Tenga cuidado cuando ejecute este comando. La ejecución de este comando sobrescribe la carpeta de proyecto entera con la instantánea realizada cuando se inició esa ejecución en particular. El proyecto se mantiene en la rama actual. Esto significa que **pierde todos los cambios** de la carpeta de proyecto actual.  

Es posible que quiera usar Git para confirmar los cambios en la rama actual antes de realizar esta operación.

## <a name="step-6-use-the-master-branch"></a>Paso 6. Uso de la rama "master"
Una manera de evitar la pérdida accidental del estado actual del proyecto, es confirmar el proyecto en la rama maestra del repositorio de Git (o en cualquier rama que haya creado). Puede usar Git desde la línea de comandos o desde su herramienta de cliente Git favorita para operar en la rama maestra. Por ejemplo:

```sh
# Check status to make sure you are on the master branch (or branch of your choice).
$ git status

# Stage all changes.
$ git add -A

# Commit all changes locally on the master branch.
$ git commit -m 'these are my updates so far'

# Push changes to the remote Team Services Git repo master branch.
$ git push origin master
```

Ahora, puede restaurar sin ningún riesgo el proyecto a una instantánea anterior completando el paso 5. Siempre puede volver a la confirmación que acaba de realizar en la rama maestra.

## <a name="authentication"></a>Autenticación
Si solo confía en las funciones del historial de ejecución de Machine Learning para realizar instantáneas del proyecto y restaurarlas, no tiene que preocuparse por la autenticación del repositorio de Git. La autenticación se controla mediante el nivel de servicio de Experimentación de Machine Learning.

Sin embargo, si usa sus propias herramientas de Git para administrar el control de versiones, debe controlar la autenticación en el repositorio de Git remoto en Team Services. En Machine Learning, el repositorio de Git remoto se agrega al repositorio local como un Git remoto mediante el protocolo HTTPS. Esto significa que cuando emita comandos de Git (como de inserción o de extracción) al equipo remoto, deberá proporcionar el nombre de usuario y una contraseña o un token de acceso personal. Para crear un token de acceso personal en un repositorio de Git de Team Services, siga las instrucciones de [Use a personal access token to authenticate](https://docs.microsoft.com/vsts/accounts/use-personal-access-tokens-to-authenticate) (Uso de un token de acceso personal para autenticar).

## <a name="next-steps"></a>Pasos siguientes
- Aprenda a [usar el Proceso de ciencia de datos en equipo para organizar la estructura del proyecto](how-to-use-tdsp-in-azure-ml.md).
