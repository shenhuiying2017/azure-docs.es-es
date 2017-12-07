---
title: "Codificación colaborativa con GIT - Azure Machine Learning | Microsoft Docs"
description: "Cómo realizar el desarrollo de código de colaboración para los proyectos de ciencia de datos con GIT con una planeación ágil."
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: bradsev;
ms.openlocfilehash: e6808c0d9d4ca14101eeebe8b3e286d64e2328f9
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/30/2017
---
# <a name="collaborative-coding-with-git"></a>Codificación colaborativa con GIT

En este artículo se describe cómo realizar el desarrollo de código de colaboración para los proyectos de ciencia de datos con GIT como el marco de desarrollo de código compartido. En el artículo se abarca cómo vincular estas actividades de codificación con el trabajo planeado en el [desarrollo ágil](agile-development.md) y cómo revisar el código.


## 1. <a name='Linkaworkitemwithagitbranch-1'></a>Vínculo de un elemento de trabajo con una rama de GIT 

VSTS proporciona una manera práctica de conectar un elemento de trabajo (un caso o una tarea) a una rama de GIT. Esto le permite vincular el caso o la tarea directamente al código asociado. 

Para conectar un elemento de trabajo a una nueva rama, haga doble clic en un elemento de trabajo y, en la ventana emergente, haga clic en **Crear una rama nueva** en **+ Agregar vínculo**.  

![1](./media/collaborative-coding-with-git/1-sprint-board-view.png)

Proporcione la información para esta nueva rama, como el nombre de la rama, repositorio de GIT base y la rama. El repositorio de GIT elegido debe ser el repositorio en el mismo proyecto de equipo al que pertenece el elemento de trabajo. La rama base puede ser la rama principal o cualquier otra rama existente.

![2](./media/collaborative-coding-with-git/2-create-a-branch.png)

Un procedimiento recomendado es crear una rama de GIT para cada elemento de trabajo del caso. A continuación, para cada elemento de trabajo de la tarea, cree una rama en función de la rama del caso. La organización de las ramas de esta manera jerárquica que se corresponde a las relaciones entre caso y tarea es muy útil cuando hay varias personas trabajando en distintos casos del mismo proyecto, o si hay varias personas trabajando en tareas distintas del mismo caso. Los conflictos pueden reducirse cuando cada miembro del equipo trabaja en una rama distinta y cuando cada miembro trabaja en códigos diferentes u otros artefactos cuando se comparte una rama. 

En la siguiente imagen se muestra la estrategia de bifurcación recomendada para el proceso de ciencia de datos en equipo. Es posible que no necesite tantas ramas como se muestra aquí, especialmente cuando solo tiene a una o dos personas que trabajan en el mismo proyecto, o cuando solo una persona trabaja en todas las tareas de un caso. Pero siempre es un procedimiento recomendado separar la rama de desarrollo de la rama maestra. Esto puede ayudar a evitar que la rama de la versión se interrumpa por las actividades de desarrollo. Encontrará una descripción más completa del modelo de rama de GIT en [A Successful Git Branching Model](http://nvie.com/posts/a-successful-git-branching-model/) (Un modelo correcto de rama de GIT).

![3](./media/collaborative-coding-with-git/3-git-branches.png)

Para cambiar a la rama en la que desea trabajar, ejecute el siguiente comando en un comando shell (Windows o Linux). 

    git checkout <branch name>

Si se cambia el *<nombre de la rama\>* a **maestro**, se devuelve a la rama **maestra**. Después de cambiar a la rama de trabajo, puede comenzar a trabajar en ese elemento de trabajo mediante el desarrollo de los artefactos de código o documentación necesarios para completar el elemento. 

También puede vincular un elemento de trabajo a una rama existente. En la página **Detalle** de un elemento de trabajo, en lugar de hacer clic en **Crear una rama nueva**, haga clic en **+ Agregar vínculo**. Después, seleccione la rama a la que desea vincular el elemento de trabajo. 

![4](./media/collaborative-coding-with-git/4-link-to-an-existing-branch.png)

También puede crear una nueva rama en los comandos GIT Bash. Si falta <nombre de la rama base\>, el <nuevo nombre de rama\> se basa en la rama _maestra_. 
    
    git checkout -b <new branch name> <base branch name>


## 2. <a name='WorkonaBranchandCommittheChanges-2'></a>Trabajo en una rama y confirmación de los cambios 

Ahora suponga que realiza algún cambio la rama *data\_ingesta* para el elemento de trabajo, por ejemplo, al agregar un archivo de R en la rama en la máquina local. Puede confirmar el archivo de R que se agrega a la rama para este elemento de trabajo, siempre que esté en esa rama en el shell de git, mediante los comandos de Git siguientes:

    git status
    git add .
    git commit -m"added a R scripts"
    git push origin data_ingestion

![5](./media/collaborative-coding-with-git/5-sprint-push-to-branch.png)

## 3. <a name='CreateapullrequestonVSTS-3'></a>Creación de una solicitud de incorporación de cambios en VSTS 

Cuando esté listo después de unas confirmaciones e inserciones, para combinar la rama actual en su rama base, puede enviar una **solicitud de incorporación de cambios** en el servidor de VSTS. 

Vaya a la página principal del proyecto de equipo y haga clic en **CODE** (Código). Seleccione la rama que se va a combinar y el nombre del repositorio de GIT en el que se desea combinar la rama. Después, haga clic en **Solicitudes de incorporación de cambios** y en **Nueva solicitud de incorporación de cambios** para crear una revisión de la solicitud de incorporación de cambios antes de que el trabajo en la rama se combine en su rama base.

![6](./media/collaborative-coding-with-git/6-spring-create-pull-request.png)

Rellene alguna descripción sobre esta solicitud de incorporación de cambios, agregue revisores y envíelos.

![7](./media/collaborative-coding-with-git/7-spring-send-pull-request.png)

## 4. <a name='ReviewandMerge-4'></a>Revisión y combinación 

Cuando se crea la solicitud de incorporación de cambios, los revisores reciben una notificación de correo electrónico para revisar dichas solicitudes. Los revisores deben comprobar si los cambios funcionan o no y probar los cambios con el solicitante si es posible. En función de su evaluación, los revisores pueden aprobar o rechazar la solicitud de incorporación de cambios. 

![8](./media/collaborative-coding-with-git/8-add_comments.png)

![9](./media/collaborative-coding-with-git/9-spring-approve-pullrequest.png)

Después de realizar la revisión, la rama de trabajo se combina en la rama base, haga clic en el botón **Completar**. Puede optar por eliminar la rama de trabajo después de haberse combinado. 

![10](./media/collaborative-coding-with-git/10-spring-complete-pullrequest.png)

Confirme en la esquina superior izquierda que la solicitud está marcada como **COMPLETADO**. 

![11](./media/collaborative-coding-with-git/11-spring-merge-pullrequest.png)

Al volver al repositorio, bajo **CÓDIGO**, se le pide que haya cambiado a la rama principal.

![12](./media/collaborative-coding-with-git/12-spring-branch-deleted.png)

También puede utilizar los comandos de Git siguientes para combinar la rama de trabajo con la rama base y eliminar la rama de trabajo después de la combinación:

    git checkout master
    git merge data_ingestion
    git branch -d data_ingestion

![13](./media/collaborative-coding-with-git/13-spring-branch-deleted-commandline.png)


 
## <a name="next-steps"></a>Pasos siguientes

En el artículo sobre la [ejecución de las tareas de ciencia de datos](execute-data-science-tasks.md) se muestra cómo usar las utilidades para completar varias tareas comunes de ciencia de datos, como la exploración interactiva de los datos, el análisis de los datos, la creación de informes y la creación de modelos.

También se proporcionan tutoriales que muestran todos los pasos del proceso en **escenarios concretos**. Se enumeran y enlazan con descripciones en miniatura en los [tutoriales de ejemplo](walkthroughs.md). En ellos se ilustra cómo combinar herramientas y servicios locales y en la nube en un flujo de trabajo o canalización para crear una aplicación inteligente. 

