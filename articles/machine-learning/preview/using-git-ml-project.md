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
ms.openlocfilehash: 0cd447a52964578dd2348a786dd57a45ea87516e
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/22/2017
---
# <a name="using-git-repository-with-an-azure-machine-learning-workbench-project"></a>Uso de un repositorio de Git con un proyecto de Azure Machine Learning Workbench
En este documento se proporciona información acerca de cómo Azure Machine Learning Workbench usa GIT para proporcionar control de versiones y garantizar la reproducibilidad en el experimento de ciencia de datos. También se proporcionan instrucciones sobre cómo asociar el proyecto con un repositorio de Git en la nube.

## <a name="introduction"></a>Introducción
Azure Machine Learning Workbench está diseñado desde cero con la integración de Git. Al crear un nuevo proyecto, la carpeta del proyecto se inicializa automáticamente en GIT como un repositorio de GIT local (repositorio). Mientras tanto, también se crea un segundo repositorio de GIT local oculto con una rama denominada _AzureMLHistory/<project_GUID>_ para realizar un seguimiento de los cambios en la carpeta de proyecto para cada ejecución. 

Al asociar el proyecto de Azure ML con un repositorio de Git, hospedado en un proyecto de Visual Studio Team Service (VSTS), se habilita el control automático de versiones tanto de forma local como remota. Esta asociación permite que cualquiera con acceso al repositorio remoto descargue el código fuente más reciente en otro equipo (roaming).  

> [!NOTE]
> VSTS tiene su propia lista de control de acceso que es independiente del servicio Experimentación de Azure Machine Learning. El acceso de usuario puede variar entre un repositorio de Git y un área de trabajo o proyecto de Azure ML, y puede que sea necesario administrarlo. Así que, si quiere compartir su proyecto de Azure ML con un miembro del equipo, lo que incluye el acceso de nivel de código, además de compartir únicamente el área de trabajo, deberá concederle el acceso adecuado al repositorio de Git de VSTS. 

Con Git, también es posible administrar el control de versiones explícitamente mediante la rama _master_ o creando otras ramas en el repositorio. Puede usar simplemente el repositorio de Git local y también puede insertarlo en el repositorio de Git remoto si se aprovisiona.

Este diagrama representa la relación entre un repositorio de Git de VSTS y un proyecto de Azure ML:

![Git de AML](media/using-git-ml-project/aml_git.png)

Para comenzar a usar un repositorio de Git remoto, siga estas instrucciones básicas.

> [!NOTE]
> Actualmente, Azure Machine Learning solo admite repositorios de Git en cuentas de VSTS. La compatibilidad con repositorios de Git generales (como GitHub, etc.) está prevista en un futuro.

## <a name="step-1-create-an-azure-ml-experimentation-account"></a>Paso 1. Creación de una cuenta de Experimentación de Azure ML
Si no lo ha hecho ya, cree una cuenta de Experimentación de Azure ML e instale la aplicación de Azure ML Workbench. Consulte más detalles en la [guía de inicio rápido de instalación y creación](quickstart-installation.md).

## <a name="step-2-create-a-team-project-or-use-an-existing-team-project"></a>Paso 2: Creación de un proyecto de equipo o uso de un proyecto de equipo existente
En [Azure Portal](https://portal.azure.com/), cree un nuevo **proyecto de equipo**.
1. Haga clic en **+**.
2. Busque **"Proyecto de equipo"**.
3. Escriba la información necesaria.
    - Nombre: nombre de equipo.
    - Control de versiones: **Git**
    - Suscripción: la que tiene una cuenta de Experimentación de Azure Machine Learning.
    - Ubicación: lo ideal es que esté en una región cercana a sus recursos de Experimentación de Azure Machine Learning.
4. Haga clic en **Crear**. 

![Creación de un proyecto de equipo desde Azure Portal](media/using-git-ml-project/create_vsts_team.png)

Asegúrese de que inicia sesión con la misma cuenta de Azure Active Directory (AAD) que usa para acceder a Azure Machine Learning Workbench. En caso contrario, el sistema no podrá acceder con sus credenciales de AAD, a menos que use la línea de comandos para crear el proyecto de Azure Machine Learning y proporcione un token de acceso personal para tener acceso al repositorio de GIT. Más información sobre esto más adelante.

Una vez creado el proyecto de equipo, está listo para continuar con el paso siguiente.

Para desplazarse directamente al proyecto de equipo que acaba de crear, la dirección URL es `https://<team_project_name>.visualstudio.com`.

## <a name="step-3-create-a-new-azure-ml-project-with-a-remote-git-repo"></a>Paso 3: Creación de un proyecto de Azure ML con un repositorio de Git remoto
Inicie Azure ML Workbench y cree un nuevo proyecto. Rellene el cuadro de texto del repositorio de Git con la dirección URL del repositorio de Git de VSTS que obtuvo en el paso 2. Por lo general, suele tener el aspecto siguiente: `http://<vsts_account_name>.visualstudio.com/_git/<project_name>`

![Creación de un proyecto de Azure ML con un repositorio de Git](media/using-git-ml-project/create_project_with_git_rep.png)

También puede crear el proyecto con la herramienta de línea de comandos. Tiene la opción de proporcionar un token de acceso personal. Azure ML podrá utilizar ese token para acceder al repositorio de GIT en su nombre, en lugar de depender de sus credenciales de AAD:

```
# create a new project with a Git repo and personal access token.
$ az ml project create -a <experimentation account name> -n <project name> -g <resource group name> -w <workspace name> -r <Git repo URL> --vststoken <VSTS personal access token>
```
> [!IMPORTANT]
> Si elige la plantilla de proyecto en blanco, es correcto si el repositorio de GIT elegido ya tiene una rama _master_. Azure ML simplemente clona la rama _master_ localmente y agrega la carpeta `aml_config` y otros archivos de metadatos del proyecto a la carpeta del proyecto local. Sin embargo, si elige cualquier otra plantilla del proyecto, su repositorio de GIT aún no debe tener una rama _master_ o se mostrará un error. La alternativa es usar la herramienta de línea de comandos `az ml project create` para crear el proyecto y proporcionar un modificador `--force`. Esta acción elimina los archivos de la rama "master" original y los reemplaza con los nuevos archivos en la plantilla que elija.

Ahora se crea un nuevo proyecto de Azure ML con la integración del repositorio de Git habilitada y listo para funcionar. La carpeta de proyecto siempre se inicializa en Git como un repositorio de Git local. Y el Git _remote_ se establece en el repositorio de Git de VSTS remoto de forma que se puedan insertar en él las confirmaciones.

## <a name="step-3a-associate-an-existing-azure-ml-project-with-a-vsts-git-repo"></a>Paso 3a. Asociación de un proyecto de Azure ML existente con un repositorio de GIT de VSTS
Opcionalmente, puede crear también un proyecto de Azure ML sin un repositorio de Git de VSTS y confiar solamente en el repositorio de Git local para las instantáneas del historial de ejecución. Además, puede asociar posteriormente un repositorio de Git de VSTS con este proyecto de Azure ML existente mediante el siguiente comando:

```azurecli
# make sure you are in the project path so CLI has context of your current project
$ az ml project update --repo http://<vsts_account_name>.visualstudio.com/_git/<project_name>
```

> [!NOTE] 
> Solo puede realizar la operación de actualización de repositorio en un proyecto de Azure ML que no tenga un repositorio de GIT asociado. Además, una vez que el repositorio de GIT está asociado, no se puede quitar.

## <a name="step-4-capture-project-snapshot-in-git-repo"></a>Paso 4 Captura de una instantánea de proyecto en el repositorio de Git
Ahora puede ejecutar unas cuantas ejecuciones de script en el proyecto y realizar algunos cambios entre ellas. Puede hacerlo desde la aplicación de escritorio o desde la CLI con el comando `az ml experiment submit`. Para más información, puede seguir el [tutorial de clasificación de Iris](tutorial-classifying-iris-part-1.md). En cada ejecución, si se realiza algún cambio en los archivos de la carpeta de proyecto, se confirma e inserta una instantánea de la carpeta de proyecto entera en el repositorio de GIT remoto en una rama denominada `AzureMLHistory/<Project_GUID>`. Para ver las ramas y las confirmaciones, vaya a la dirección URL del repositorio de GIT de VSTS y busque esta rama. 

> [!NOTE] 
> La instantánea solo se confirma antes de una ejecución de script. Actualmente, una ejecución de preparación de datos o una ejecución de celdas de Notebook no desencadena la instantánea.

![rama de historial de ejecución](media/using-git-ml-project/run_history_branch.png)

> [!IMPORTANT] 
> Es mejor si no trabaja en la rama de historial con comandos de GIT. Si lo hace, podría estropearse el historial de ejecución. Use la rama "master" o cree otras ramas para sus propias operaciones de GIT.

## <a name="step-5-restore-a-previous-project-snapshot"></a>Paso 5. Restauración de una instantánea anterior del proyecto 
Para restaurar toda la carpeta de proyecto al estado de una instantánea de estado anterior del proyecto de historial de ejecución, desde Azure ML Workbench, siga estos pasos:
1. Haga clic en **Ejecuciones** en la barra de actividad (icono de reloj de arena).
2. En la vista **Lista de ejecuciones**, haga clic en la ejecución que quiere restaurar.
3. En la vista **Detalles de ejecución**, haga clic en **Restaurar**.

![rama de historial de ejecución](media/using-git-ml-project/restore_project.png)

Como alternativa, puede usar el siguiente comando desde la ventana de la CLI de Azure ML Workbench.

```azurecli
# discover the run I want to restore snapshot from:
$ az ml history list -o table

# restore the snapshot from a particular run
$ az ml project restore --run-id <run_id>
```

Al ejecutar este comando, se sobrescribirá la carpeta de proyecto entera con la instantánea realizada cuando se inició esa ejecución en particular. Sin embargo, el proyecto se mantiene en la rama actual. Esto significa que **perderá todos los cambios** de la carpeta de proyecto actual. Por lo tanto, tener especial cuidado al ejecutar este comando. Es posible que quiera que GIT confirme los cambios en la rama actual antes de realizar la operación anterior. Vaya más arriba para obtener más información.

## <a name="step-6-use-the-master-branch"></a>Paso 6. Uso de la rama "master"
Una manera de evitar la pérdida accidental del estado actual del proyecto, es confirmar el proyecto en la rama "master"(o cualquier rama que haya creado) del repositorio de GIT. Puede usar directamente Git desde la línea de comandos (u otra herramienta de cliente Git favorita que prefiera) para operar en la rama "master". Por ejemplo:

```sh
# check status to make sure you are on the master branch (or branch of your choice)
$ git status

# stage all changes
$ git add -A

# commit all changes locally on the master branch
$ git commit -m 'these are my updates so far'

# push changes into the remote VSTS Git repo master branch.
$ git push origin master
```

Ahora puede restaurar sin ningún riesgo el proyecto en una instantánea anterior siguiendo el paso 5, ya que sabe que siempre puede volver a la confirmación que acaba de realizar en la rama "master".

## <a name="authentication"></a>Autenticación
Si confía en las funciones del historial de ejecución de Azure ML para realizar instantáneas del proyecto y restaurarlas, no tiene que preocuparse por la autenticación del repositorio de Git. De ello se ocupa la capa del servicio Experimentación.

Sin embargo, si usa sus propias herramientas de Git para administrar el control de versiones, deberá tratar de forma correcta la autenticación en el repositorio de Git remoto en VSTS. En Azure ML, el repositorio de GIT remoto se agrega al repositorio local como un GIT remoto con el protocolo HTTPS. Esto significa que cuando emita comandos de GIT al equipo remoto (como de inserción o de extracción), deberá proporcionar el nombre de usuario y una contraseña o un token de acceso personal. Siga [estas instrucciones](https://docs.microsoft.com/vsts/accounts/use-personal-access-tokens-to-authenticate) para crear el token de acceso personal en un repositorio de GIT de VSTS.

## <a name="next-steps"></a>Pasos siguientes
Aprenda a usar el Proceso de ciencia de datos en equipo para organizar la estructura del proyecto. Consulte [Estructuración de un proyecto con TDSP](how-to-use-tdsp-in-azure-ml.md)
