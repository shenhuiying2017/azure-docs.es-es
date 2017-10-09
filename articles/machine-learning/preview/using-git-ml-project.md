---
title: Uso de un repositorio de Git con un proyecto de Azure Machine Learning Workbench | Microsoft Docs
description: "En este artículo se explica cómo usar un repositorio de Git junto con un proyecto de Azure Machine Learning Workbench."
services: machine-learning
author: ahgyger
ms.author: ahgyger
manager: hning86
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/20/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 59b07c9834904e01256b75344ba2e6892e56438c
ms.contentlocale: es-es
ms.lasthandoff: 09/25/2017

---
# <a name="using-git-repository-with-an-azure-machine-learning-workbench-project"></a>Uso de un repositorio de Git con un proyecto de Azure Machine Learning Workbench
En este documento se proporciona información sobre cómo Azure Machine Learning Workbench usa Git para garantizar la reproducibilidad en el experimento de ciencia de datos. También se proporcionan instrucciones sobre cómo asociar el proyecto con un repositorio de Git en la nube.

## <a name="introduction"></a>Introducción
Azure Machine Learning Workbench está diseñado desde cero con la integración de Git. Cuando se crea un nuevo proyecto, la carpeta del proyecto se "inicializa en Git" automáticamente como un repositorio (repo) de Git local, mientras se crea también un segundo repositorio de Git oculto con una rama llamada _AzureMLHistory/<project_GUID>_ para mantener el seguimiento de los cambios de la carpeta de proyecto en cada ejecución. 

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


> [!TIP]
> Asegúrese de que inicia sesión con la cuenta de Azure Active Directory (AAD) usada para acceder a Azure Machine Learning Workbench. De lo contrario, el nuevo proyecto de equipo podría acabar con el id. de inquilino equivocado y puede que Azure Machine Learning no lo encuentre. En este caso, tendría que usar la interfaz de línea de comandos y proporcionar el token de VSTS.

Una vez creado el proyecto de equipo, está listo para continuar con el paso siguiente.

Para desplazarse directamente al proyecto de equipo que acaba de crear, la dirección URL es `https://<team_project_name>.visualstudio.com`.

> [!NOTE]
> Actualmente, Azure Machine Learning solo admite repositorios de Git vacíos sin rama "master". Desde la interfaz de la línea de comandos, puede usar el argumento --force para eliminar primero la rama "master". 

## <a name="step-3-create-a-new-azure-ml-project-with-a-remote-git-repo"></a>Paso 3: Creación de un proyecto de Azure ML con un repositorio de Git remoto
Inicie Azure ML Workbench y cree un nuevo proyecto. Rellene el cuadro de texto del repositorio de Git con la dirección URL del repositorio de Git de VSTS que obtuvo en el paso 2. Normalmente tendrá este aspecto: http://<vsts_account_name>.visualstudio.com/_git/<project_name>

![Creación de un proyecto de Azure ML con un repositorio de Git](media/using-git-ml-project/create_project_with_git_rep.png)

Ahora se crea un nuevo proyecto de Azure ML con la integración del repositorio de Git habilitada y listo para funcionar. La carpeta de proyecto siempre se inicializa en Git como un repositorio de Git local. Y el Git _remote_ se establece en el repositorio de Git de VSTS remoto de forma que se puedan insertar en él las confirmaciones.

## <a name="step-3a-associate-an-existing-azure-ml-project-with-a-vsts-git-repo"></a>Paso 3.a Asociación de un proyecto de Azure ML existente con un repositorio de Git de VSTS
Opcionalmente, puede crear también un proyecto de Azure ML sin un repositorio de Git de VSTS y confiar solamente en el repositorio de Git local para las instantáneas del historial de ejecución. Además, puede asociar posteriormente un repositorio de Git de VSTS con este proyecto de Azure ML existente mediante el siguiente comando:

```azurecli
# make sure you are in the project path so CLI has context of your current project
az ml project update --repo http://<vsts_account_name>.visualstudio.com/_git/<project_name
```

## <a name="step-4-capture-project-snapshot-in-git-repo"></a>Paso 4 Captura de una instantánea de proyecto en el repositorio de Git
Ahora puede ejecutar unas cuantas veces el proyecto y realizar algunos cambios entre ellas. Puede hacerlo desde la aplicación de escritorio o desde la CLI con el comando `az ml experiment submit`. Para más información, puede seguir el [tutorial de clasificación de Iris](tutorial-classifying-iris-part-1.md). En cada ejecución, si se realiza algún cambio en los archivos de la carpeta de proyecto, se confirma e inserta una instantánea de la carpeta de proyecto entera en el repositorio de Git remoto. Para ver las ramas y las confirmaciones, vaya a la dirección URL del repositorio de Git de VSTS.

![rama de historial de ejecución](media/using-git-ml-project/run_history_branch.png)

## <a name="step-5-restore-a-previous-project-snapshot"></a>Paso 5. Restauración de una instantánea anterior del proyecto 
Para restaurar la carpeta de proyecto entera al estado de una instantánea de estado anterior del proyecto de historial de ejecución, desde AML Workbench, siga estos pasos:
1. Haga clic en **Ejecuciones** en la barra de actividad (icono de reloj de arena).
2. En la vista **Lista de ejecuciones**, haga clic en la ejecución que quiere restaurar.
3. En la vista **Detalles de ejecución**, haga clic en **Restaurar**.

![rama de historial de ejecución](media/using-git-ml-project/restore_project.png)

Como alternativa, puede usar el siguiente comando desde la ventana de la CLI de Azure ML Workbench.

```azurecli
# discover the run I want to restore snapshot from:
az ml history list -o table

# restore the snapshot from a particular run
az ml project restore --run-id <run_id>
```

Al ejecutar este comando, se sobrescribirá la carpeta de proyecto entera con la instantánea realizada cuando se inició esa ejecución en particular. Esto significa que **perderá todos los cambios** de la carpeta de proyecto actual. Por lo tanto, tener especial cuidado al ejecutar este comando.

## <a name="step-6-use-the-master-branch"></a>Paso 6. Uso de la rama "master"
Una manera de evitar la pérdida accidental del estado actual del proyecto, es confirmar el proyecto en la rama "master" del repositorio de Git. Puede usar directamente Git desde la línea de comandos (u otra herramienta de cliente Git favorita que prefiera) para operar en la rama "master". Por ejemplo:

```
# make sure you are on the master branch
git checkout master

# stage all changes
git add -A

# commit all changes locally on the master branch
git commit -m 'this is my updates so far'

# push changes into the remote VSTS Git repo master branch.
git push origin master
```

Ahora puede restaurar sin ningún riesgo el proyecto en una instantánea anterior siguiendo el paso 5, ya que sabe que siempre puede volver a la confirmación que acaba de realizar en la rama "master".

## <a name="authentication"></a>Autenticación
Si confía en las funciones del historial de ejecución de Azure ML para realizar instantáneas del proyecto y restaurarlas, no tiene que preocuparse por la autenticación del repositorio de Git. De ello se ocupa la capa del servicio Experimentación.

Sin embargo, si usa sus propias herramientas de Git para administrar el control de versiones, deberá tratar de forma correcta la autenticación en el repositorio de Git remoto en VSTS. Es decir, deberá configurar la autenticación en el repositorio de Git en el equipo local antes de poder emitir comandos de Git para dicho repositorio. 

La manera más fácil de hacerlo es crear un par de claves SSH y cargar la parte de clave pública en la configuración de seguridad del repositorio de Git.

### <a name="generate-ssh-key"></a>Generación de la clave SSH 
En primer lugar, vamos a generar un par de claves SSH en el equipo.

#### <a name="on-windows"></a>En Windows:
Inicie la aplicación de escritorio de GUI de Git en Windows y, en el menú _Ayuda_, haga clic en _Mostrar clave SSH_.

![Clave SSH](media/using-git-ml-project/git_gui.png)

Copie SSH en el Portapapeles.

#### <a name="on-macos"></a>En macOS:
Pasos rápidos desde el shell de comandos:
```
# generate the SSH key
$ ssh-keygen -t rsa -b 4096 -C "your_email@example.com"

# start the SSH agent in the background
$ eval "$(ssh-agent -s)"

# add newly generated SSH key to the SSH agent
$ ssh-add -K ~/.ssh/id_rsa

# display the public key so you can copy it.
$ more ~/.ssh/id_rsa.pub
```
Se pueden encontrar pasos más detallados en [este artículo de GitHub](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/).

### <a name="upload-public-key-to-git-repo"></a>Carga de clave pública en el repositorio de Git
Vaya a la página principal de la cuenta de Visual Studio: https://<vsts_account_name>.visualstudio.com, inicie sesión y haga clic en Seguridad dentro de su avatar.

A continuación, agregue una clave pública SSH, pegue la clave pública SSH que obtuvo en el paso anterior y asígnele un nombre. Aquí puede agregar varias claves.

Ahora puede emitir comandos de Git de forma local para el repositorio remoto sin que sea necesaria ninguna autenticación explícita.

### <a name="read-more"></a>Más información
Siga estos dos artículos (cualquiera de los enfoques puede servir) si desea más información sobre cómo habilitar la autenticación local en el repositorio de Git remoto en VSTS.
- [Use SSH Key Authentication](https://www.visualstudio.com/en-us/docs/git/use-ssh-keys-to-authenticate) (Uso de la autenticación de clave SSH)
- [Use Git Credential Managers](https://www.visualstudio.com/en-us/docs/git/set-up-credential-managers) (Uso de administradores de credenciales de Git)


## <a name="next-steps"></a>Pasos siguientes
Aprenda a usar el Proceso de ciencia de datos en equipo para organizar la estructura del proyecto. Consulte [Estructuración de un proyecto con TDSP](how-to-use-tdsp-in-azure-ml.md)

