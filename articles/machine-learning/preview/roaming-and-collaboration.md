---
title: "Movilidad y colaboración en Azure Machine Learning Workbench | Microsoft Docs"
description: "Lista de problemas conocidos y una guía para ayudar a solucionar problemas"
services: machine-learning
author: svankam
ms.author: svankam
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/05/2017
ms.openlocfilehash: 156dd1b7f928df22b3feb9e7a13396d3b53a91d7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="roaming-and-collaboration-in-azure-machine-learning-workbench"></a>Movilidad y colaboración en Azure Machine Learning Workbench
Este documento le guía sobre cómo Azure Machine Learning Workbench puede ayudar en la itinerancia de los proyectos entre máquinas, así como habilitar la colaboración con sus compañeros de equipo. 

Cuando se crea un proyecto de Azure Machine Learning con un vínculo de repositorio de Git (repo) remoto, los metadatos del proyecto y las instantáneas se almacenan en la nube. El vínculo en la nube permite tener acceso al proyecto desde otro equipo (Movilidad). También puede dar acceso a sus compañeros de trabajo, lo que permite la colaboración. 

## <a name="prerequisites"></a>Requisitos previos
En primer lugar, instale Azure Machine Learning Workbench con acceso a una cuenta de Experimentación. Siga la [Guía de instalación](quickstart-installation.md) para más detalles.

En segundo lugar, abra [Visual Studio Team System](https://www.visualstudio.com) y cree un repositorio para vincular el proyecto. Para obtener información detallada acerca de Git, consulte el artículo [Uso de un repositorio de Git con un proyecto de Azure Machine Learning Workbench](using-git-ml-project.md).

## <a name="create-a-new-azure-machine-learning-project"></a>Creación de un nuevo proyecto de Azure Machine Learning
Inicie Azure Machine Learning Workbench y cree un nuevo proyecto (por ejemplo, _iris_). Rellene el cuadro de texto **Dirección URL del repositorio de GIT de Visualstudio.com** con una dirección URL del repositorio de Git de VSTS válida. 
>[!IMPORTANT]
>Se produce un error en la creación del proyecto si no tiene acceso de lectura y escritura en el repositorio Git y el repositorio Git no está vacío, por ejemplo, si ya tiene una bifurcación principal.

Una vez creado el proyecto, envíe algunas ejecuciones de cualquier script del proyecto. Esta acción confirma el estado del proyecto en la bifurcación del historial de ejecución del repositorio Git remoto. 

Si ha configurado la autenticación de Git, también puede operar explícitamente en la bifurcación principal o crear una nueva bifurcación. 

Por ejemplo: 
```
# check current repo status
$ git status

# stage all changes in the current repo
$ git add -A

# commit changes
$ git commit -m "my commit fixes this weird bug!"

# push to remote repo.
$ git push origin master
```

## <a name="roaming"></a>Movilidad
<a name="roaming"></a>

### <a name="open-azure-machine-learning-workbench-on-second-machine"></a>Inicio de Azure Machine Learning Workbench en un segundo equipo
Una vez que el repositorio Git de VSTS está vinculado al proyecto, puede tener acceso al proyecto _iris_ desde cualquier equipo que tenga instalado Azure Machine Learning Workbench. 

Para tener acceso al proyecto iris desde otro equipo, debe iniciar sesión en la aplicación con las mismas credenciales que usó al crear el proyecto. Además, debe navegar a la misma cuenta de Experimentación y área de trabajo. El proyecto _iris_ aparece enumerado alfabéticamente con los otros proyectos del área de trabajo. 

### <a name="download-project-on-second-machine"></a>Descarga del proyecto en un segundo equipo
Cuando se abre el área de trabajo en el segundo equipo, el icono junto al proyecto _iris_ es diferente del icono de carpeta habitual. El icono de descarga indica que el contenido del proyecto está en la nube y debe descargarse en el equipo actual. 

![creación de proyecto](./media/roaming-and-collaboration/downloadable-project.png)

Al hacer clic en el proyecto _iris_ se inicia una acción de descarga. Pasado un momento, cuando se completa la descarga, el proyecto está listo para su acceso en el segundo equipo. 

En Windows, está en `C:\Users\<username>\Documents\AzureML`

En Mac OS, se encuentra en: `/home/<username>/Documents/AzureML`

En una versión futura, tenemos previsto mejorar la funcionalidad para que pueda seleccionar una carpeta de destino. 

>Tenga en cuenta que si existe una carpeta en el directorio de Azure ML con el mismo nombre que el proyecto, la descarga produce un error. En este momento, debe cambiar el nombre de la carpeta existente con el fin de solucionar este problema.


### <a name="work-on-the-downloaded-project"></a>Trabajo en el proyecto descargado 
El proyecto recién descargado refleja el estado del proyecto a partir de la última ejecución del proyecto. Se confirma automáticamente una instantánea del estado del proyecto en la bifurcación del historial de ejecución en el repositorio Git de VSTS cada vez que se envía una ejecución. Utilizamos la instantánea asociada a la ejecución más reciente al crear una instancia del proyecto en el segundo equipo. 
 

## <a name="collaboration"></a>Colaboración
Puede colaborar con sus compañeros de equipo en los proyectos vinculados a un repositorio Git de VSTS. Se pueden asignar permisos a los usuarios en la cuenta de Experimentación, el área de trabajo y el proyecto. En este momento, puede ejecutar los comandos de Azure Resource Manager mediante la CLI de Azure. También puede usar [Azure Portal](https://portal.azure.com). Consulte la [sección siguiente](#portal).    

### <a name="using-command-line-to-add-users"></a>Uso de la línea de comandos para agregar usuarios
Utilicemos un ejemplo. Supongamos que Alice es el propietario del proyecto _Iris_ y desea compartir el acceso con Bob. 

Alice hace clic en el menú **Archivo** y selecciona el elemento de menú **Símbolo del sistema** para iniciar la línea de comandos configurada para el proyecto _iris_. Alice puede decidir qué nivel de acceso desea otorgar a Bob, ejecutando los siguientes comandos.  

```azurecli
# Find ARM ID of the experimnetation account
az ml account experimentation show --query "id"

# Add Bob to the Experimentation Account as a Reader.
# Bob now has read access to all workspaces and projects under the Account by inheritance.
az role assignment create --assignee bob@contoso.com --role Reader --scope <experimentation account ARM ID>

# Find ARM ID of the workspace
az ml workspace show --query "id"

# Add Bob to the workspace as a Contributor.
# Bob now has read/write access to all projects under the Workspace by inheritance.
az role assignment create --assignee bob@contoso.com --role Contributor --scope <workspace ARM ID>

# find ARM ID of the project 
az ml project show --query "id"

# Add Bob to the Project as an Owner.
# Bob now has read/write access to the Project, and can add others too.
az role assignment create --assignee bob@contoso.com --role Owner --scope <project ARM ID>
```

Después de la asignación de roles, directamente o por herencia, Bob puede ver el proyecto en la lista de proyectos de Workbench. La aplicación podría requerir un reinicio para ver el proyecto. Bob, a continuación, puede descargar el proyecto como se describe en la [sección Movilidad](#roaming) y colaborar con Alice. 

El historial de ejecución para todos los usuarios que colaboran en un proyecto se confirma en el mismo repositorio Git remoto. Por lo que, cuando Alice realiza una ejecución, Bob puede ver la ejecución en la sección de historial de ejecución del proyecto en la aplicación Workbench. Bob también puede restaurar el proyecto al estado de una ejecución determinada, incluidas las ejecuciones iniciadas por Alice. 

El uso compartido de un repositorio Git remoto para el proyecto también permite a Alice y Bob colaborar en la bifurcación principal. Si es necesario, también pueden crear bifurcaciones personales y usar las solicitudes de extracción de Git y combinaciones para colaborar. 

### <a name="using-azure-portal-to-add-users"></a>Uso de Azure Portal para agregar usuarios
<a name="portal"></a>

Las cuentas de Experimentación de Azure Machine Learning, las áreas de trabajo y los proyectos son recursos de Azure Resource Manager. Puede usar el vínculo Control de acceso en [Azure Portal](https://portal.azure.com) para asignar roles. 

Encuentre el recurso al que quiere agregar usuarios desde la vista Todos los recursos. Haga clic en el enlace Control de acceso (IAM) dentro de la página. Agregar usuarios 

<img src="./media/roaming-and-collaboration/iam.png" width="320px">

