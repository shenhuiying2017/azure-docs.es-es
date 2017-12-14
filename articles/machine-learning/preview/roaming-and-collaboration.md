---
title: "Itinerancia y colaboración en Azure Machine Learning Workbench | Microsoft Docs"
description: "Aprenda a configurar la itinerancia y colaboración en Azure Machine Learning Workbench."
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 11/16/2017
ms.openlocfilehash: 137608007716452ec6468f1e13f494b095a11cb0
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/06/2017
---
# <a name="roaming-and-collaboration-in-azure-machine-learning-workbench"></a>Movilidad y colaboración en Azure Machine Learning Workbench
En este artículo se describe cómo puede usar Azure Machine Learning Workbench para configurar proyectos para la itinerancia entre equipos y colaborar con los miembros del equipo. 

Cuando se crea un proyecto de Azure Machine Learning que tiene un vínculo de repositorio de Git (repo) remoto, los metadatos del proyecto y las instantáneas se almacenan en la nube. Puede utilizar el vínculo en la nube para acceder al proyecto desde otro equipo (itinerancia). También puede colaborar con miembros del equipo proporcionándoles acceso al proyecto. 

## <a name="prerequisites"></a>Requisitos previos
1. Instale la aplicación Azure Machine Learning Workbench. Asegúrese de que tiene acceso a una cuenta de Experimentación de Azure Machine Learning. Para más información, consulte la [guía de instalación](quickstart-installation.md).

2. Acceda a [Visual Studio Team Services](https://www.visualstudio.com) (Team Services) y cree un repositorio al que vincular el proyecto. Para más información, consulte [Uso de un repositorio de Git con un proyecto de Azure Machine Learning Workbench](using-git-ml-project.md).

## <a name="create-a-new-machine-learning-project"></a>Creación de un nuevo proyecto de Machine Learning
Abra Machine Learning Workbench y cree un nuevo proyecto (por ejemplo, un proyecto denominado iris). En el cuadro **Dirección URL del repositorio de Git de Visualstudio.com**, escriba una dirección URL válida para un repositorio de Git de Team Services. 

> [!IMPORTANT]
> Si elige la plantilla de proyecto en blanco, el repositorio de Git que elige para usar ya podría tener una rama maestra. Machine Learning simplemente clona la rama maestra localmente. Agrega la carpeta aml_config y otros archivos de metadatos de proyecto a la carpeta del proyecto local. 
>
> Si elige cualquier otra plantilla de proyecto, su repositorio de Git ya *no puede* tener una rama maestra. Si la tiene, aparecerá un error. La alternativa es usar el comando `az ml project create` para crear el proyecto, con un modificador `--force`. Esta acción elimina los archivos de la rama maestra original y los reemplaza por los nuevos archivos en la plantilla que elija.

Después de crear el proyecto, envíe algunas ejecuciones de cualquier script que se encuentre en el proyecto. Esta acción confirma el estado del proyecto en la rama del historial de ejecución del repositorio de Git remoto. 

> [!NOTE] 
> Solo las ejecuciones de script desencadenan confirmaciones en la rama de historial de ejecución. La ejecución de preparación de datos y las ejecuciones de Notebook no desencadenan instantáneas del proyecto en la rama del historial de ejecución.

Si ha configurado la autenticación de Git, también puede operar en la rama maestra. O bien, puede crear una nueva rama. 

Ejemplo: 
```
# Check current repo status.
$ git status

# Stage all changes in the current repo.
$ git add -A

# Commit changes.
$ git commit -m "my commit fixes this weird bug!"

# Push to the remote repo.
$ git push origin master
```

## <a name="roaming"></a>Movilidad
<a name="roaming"></a>

### <a name="open-machine-learning-workbench-on-a-second-computer"></a>Apertura de Azure Machine Learning Workbench en un segundo equipo
Después de que el repositorio de Git de Team Services esté vinculado al proyecto, puede acceder al proyecto iris desde cualquier equipo que tenga instalado Machine Learning Workbench. 

Para acceder al proyecto iris desde otro equipo, debe iniciar sesión en la aplicación con las mismas credenciales que usó al crear el proyecto. También necesita estar en la misma área de trabajo y cuenta de Experimentación de Azure Machine Learning. El proyecto iris aparece enumerado alfabéticamente con otros proyectos del área de trabajo. 

### <a name="download-the-project-on-a-second-computer"></a>Descarga del proyecto en un segundo equipo
Cuando el área de trabajo se abre en el segundo equipo, el icono que se encuentra junto al proyecto iris es diferente del icono de carpeta habitual. El icono de descarga indica que el contenido del proyecto está en la nube y que el proyecto está preparado para descargarse en el equipo actual. 

![Crear proyecto](./media/roaming-and-collaboration/downloadable-project.png)

Seleccione el proyecto iris para comenzar una descarga. Cuando la descarga finalice, el proyecto estará listo para acceder a él en el segundo equipo. 

En Windows, el proyecto se encuentra en C:\Usuarios\\< nombre de usuario\>\Documents\AzureML.

En macOS, el proyecto se encuentra en /home/\<nombreDeUsuario\>/Documents/AzureML.

En una versión futura, tenemos previsto mejorar la funcionalidad para que pueda seleccionar una carpeta de destino. 

> [!NOTE]
> Si tiene una carpeta en el directorio de Machine Learning con el mismo nombre que el proyecto, no se produce la descarga. Para solucionar este problema, cambie temporalmente el nombre de la carpeta existente.


### <a name="work-on-the-downloaded-project"></a>Trabajo en el proyecto descargado 
El proyecto recién descargado refleja el estado del proyecto en la última ejecución del proyecto. Se confirma automáticamente una instantánea del estado del proyecto en la rama del historial de ejecución en el repositorio de Git de Team Services cada vez que se envía una ejecución. La instantánea asociada a la ejecución más reciente se usa para crear una instancia del proyecto en el segundo equipo. 
 

## <a name="collaboration"></a>Colaboración
Pueden colaborar con los miembros del equipo en proyectos que están vinculados a un repositorio de Git de Team Services. Puede asignar permisos a los usuarios en el área de trabajo, el proyecto y la cuenta de Experimentación de Machine Learning. Actualmente, puede ejecutar los comandos de Azure Resource Manager mediante la CLI de Azure. También puede usar [Azure Portal](https://portal.azure.com). Para más información, consulte [Uso de Azure Portal para agregar usuarios](#portal).    

### <a name="use-the-command-line-to-add-users"></a>Uso de la línea de comandos para agregar usuarios
Por ejemplo, Alice es la propietaria del proyecto iris. Alice desea compartir el acceso al proyecto con Bob. 

Alice selecciona el menú **Archivo** y luego selecciona el elemento de menú **Símbolo del sistema**. La ventana Símbolo del sistema se abre con el proyecto de iris. A continuación, Alice puede decidir qué nivel de acceso desea conceder a Bob. Alice concede permisos mediante la ejecución de los siguientes comandos:  

```azurecli
# Find the Resource Manager ID of the Experimentation account.
az ml account experimentation show --query "id"

# Add Bob to the Experimentation account as a Contributor.
# Bob now has read/write access to all workspaces and projects under the account by inheritance.
az role assignment create --assignee bob@contoso.com --role Contributor --scope <Experimentation account Resource Manager ID>

# Find the Resource Manager ID of the workspace.
az ml workspace show --query "id"

# Add Bob to the workspace as an Owner.
# Bob now has read/write access to all projects under the workspace by inheritance. Bob can invite or remove other users.
az role assignment create --assignee bob@contoso.com --role Owner --scope <workspace Resource Manager ID>
```

Después de la asignación de roles, ya sea directamente o por herencia, Bob puede ver el proyecto en la lista de proyectos de Machine Learning Workbench. Bob podría tener que reiniciar la aplicación para ver el proyecto. A continuación, Bob puede descargar el proyecto como se describe en la sección [Itinerancia](#roaming) y colaborar con Alice. 

El historial de ejecución para todos los usuarios que colaboran en un proyecto se confirma en el mismo repositorio de Git remoto. Cuando Alice realiza una ejecución, Bob puede ver la ejecución en la sección del historial de ejecución del proyecto en la aplicación Machine Learning Workbench. Bob también puede restaurar el proyecto al estado de una ejecución determinada, incluidas las ejecuciones que Alice inició. 

Mediante el uso compartido de un repositorio de Git remoto para el proyecto, Alice y Bob también pueden colaborar en la rama maestra. Si es necesario, también pueden crear ramas personales y usar combinaciones y solicitudes de extracción de Git para colaborar. 

### <a name="use-the-azure-portal-to-add-users"></a>Uso de Azure Portal para agregar usuarios
<a name="portal"></a>

Las cuentas de Experimentación de Machine Learning, las áreas de trabajo y los proyectos son recursos de Azure Resource Manager. Para asignar roles, puede usar el vínculo **Control de acceso** de [Azure Portal](https://portal.azure.com). 

Encuentre el recurso al que quiere agregar usuarios mediante la vista **Todos los recursos**. Seleccione el vínculo **Control de acceso (IAM)** y seleccione **Agregar usuarios**. 

<img src="./media/roaming-and-collaboration/iam.png" width="320px">

## <a name="sample-collaboration-workflow"></a>Ejemplo de flujo de trabajo de colaboración
Para ilustrar el flujo de trabajo de colaboración, veamos un ejemplo. Alice y Bob, empleados de Contoso, quieren colaborar en un proyecto de ciencia de datos mediante Machine Learning Workbench. Sus identidades pertenecen al mismo inquilino de Azure Active Directory (Azure AD) de Contoso. Estos son los pasos que Alicia y Bob llevan a cabo:

1. Alice crea un repositorio de Git vacío en un proyecto de Team Services. El proyecto de Team Services debe estar en una suscripción de Azure creada en el inquilino de Azure AD de Contoso. 

2. Alice crea una cuenta de Experimentación de Machine Learning, un área de trabajo y un proyecto de Machine Learning Workbench en su equipo. Cuando crea el proyecto, escribe la dirección URL del repositorio de Git de Team Services.

3. Alice empieza a trabajar en el proyecto. Crea algunos scripts y realiza unas ejecuciones. Para cada ejecución, se inserta una instantánea de toda la carpeta de proyecto automáticamente como una confirmación en una rama de historial de ejecución del repositorio de Git de Team Services que Machine Learning Workbench crea.

4. Alice está satisfecha con el trabajo en curso. Ella quiere confirmar sus cambios de la rama maestra local y los inserta en la rama maestra del repositorio de Git de Team Services. Con el proyecto abierto, en Machine Learning Workbench, abre la ventana Símbolo del sistema y luego escribe estos comandos:
    
    ```sh
    # Verify that the Git remote is pointing to the Team Services Git repo.
    $ git remote -v

    # Verify that the current branch is master.
    $ git branch

    # Stage all changes.
    $ git add -A

    # Commit changes with a comment.
    $ git commit -m "this is a good milestone"

    # Push the commit to the master branch of the remote Git repo in Team Services.
    $ git push
    ```

5. Alice agrega a Bob al área de trabajo como colaborador. Ella puede hacer esto en Azure Portal o mediante el comando `az role assignment`, como se mostró anteriormente. Alice también concede a Bob permisos de lectura y escritura en el repositorio de Git de Team Services.

6. Bob inicia sesión en Machine Learning Workbench en su equipo. Bob puede ver el área de trabajo que Alice compartió con él. Bob puede ver que el proyecto iris aparece en esa área de trabajo. 

7. Bob selecciona el nombre del proyecto. El proyecto se descarga en su equipo.
    * Los archivos de proyecto descargados son una copia de la instantánea de la última ejecución que se registró en el historial de ejecución. No se trata de la última confirmación en la rama "master".
    * La carpeta de proyecto local se establece en la rama maestra, con los cambios no almacenados provisionalmente.

8. Bob puede examinar las ejecuciones que Alicia ejecutó. Bob puede restaurar instantáneas de todas las ejecuciones anteriores.

9. Bob desea obtener los cambios más recientes que Alicia insertó y luego empezar a trabajar en una rama distinta. En Azure Machine Learning Workbench, Bob abre una ventana Símbolo del sistema y ejecuta los comandos siguientes:

    ```sh
    # Verify that the Git remote is pointing to the Team Services Git repo.
    $ git remote -v

    # Verify that the current branch is master.
    $ git branch

    # Get the latest commit in the Team Services Git master branch and overwrite current files.
    $ git pull --force

    # Create a new local branch named "bob" so that Bob's work is done in the "bob" branch
    $ git checkout -b bob
    ```

10. Bob modifica el proyecto y envía nuevas ejecuciones. Los cambios se realizan en la rama bob. Alice también puede ver las ejecuciones de Bob.

11. Bob ya está listo para insertar sus cambios en el repositorio de Git remoto. Para evitar conflictos con la rama maestra en la que Alice está trabajando, Bob decide insertar su trabajo en una nueva rama remota que también se denomina bob.

    ```sh
    # Verify that the current branch is "bob," and that it has unstaged changes.
    $ git status
    
    # Stage all changes.
    $ git add -A

    # Commit the changes with a comment.
    $ git commit -m "I found a cool new trick."

    # Create a new branch on the remote Team Services Git repo, and then push the changes.
    $ git push origin bob
    ```

12. Para indicar a Alice el nuevo artificio que usa en su código, Bob crea una solicitud de extracción en el repositorio de Git remoto desde la rama bob hasta la rama maestra. Luego Alice puede combinar la solicitud de extracción en la rama maestra.

## <a name="next-steps"></a>Pasos siguientes
- Más información sobre el [uso de un repositorio de Git con un proyecto de Azure Machine Learning Workbench](using-git-ml-project.md).
