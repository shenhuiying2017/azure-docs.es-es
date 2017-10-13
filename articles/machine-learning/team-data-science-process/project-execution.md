---
title: "Ejecución de proyectos de ciencia de datos en Azure | Microsoft Docs"
description: "Describe cómo un científico de datos puede ejecutar un proyecto de ciencia de datos de forma controlada por versiones, de colaboración y cuyo seguimiento se puede realizar."
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
ms.date: 09/04/2017
ms.author: bradsev;
ms.openlocfilehash: 8c318f87243d0c98b6a42bebcdffb433f9cc456e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="execution-of-data-science-projects"></a>Ejecución de proyectos de ciencia de datos

Este documento describe cómo un científico de datos puede ejecutar un proyecto de ciencia de datos de forma sistemática, con control de versiones y de colaboración dentro de un equipo de proyecto mediante el [proceso de ciencia de datos en equipo](overview.md) (TDSP). El proceso de ciencia de datos en equipo es una plataforma desarrollada por Microsoft que proporciona una secuencia estructurada de actividades para ejecutar de forma eficaz soluciones de análisis predictivos y basadas en la nube. Para obtener un esquema de los roles personales y sus tareas asociadas que se controlan en un equipo de ciencia de datos que se estandariza en este proceso, consulte [Roles y tareas del proceso de ciencia de datos en equipo](roles-tasks.md). 

En este tema se incluyen instrucciones sobre cómo realizar las siguientes acciones: 

1. Realizar el **planeamiento de sprint** para los elementos de trabajo implicados en un proyecto.<br> Si no está familiarizado con el planeamiento de sprint, puede encontrar detalles a continuación e información general [aquí](https://en.wikipedia.org/wiki/Sprint_(software_development) "aquí"). 
2. **Agregar elementos de trabajo** a los sprint.
3. **Vincular los elementos de trabajo con actividades de codificación** cuyo seguimiento se realiza mediante git.
4. Realizar **revisión de código**. 


>[AZURE.NOTE] En el conjunto de instrucciones siguiente se detallan los pasos necesarios para configurar un entorno de equipo de proceso de ciencia de datos en equipo mediante Visual Studio Team Services (VSTS). Se especifica cómo llevar a cabo estas tareas con VSTS ya que es así cómo se implementa TDSP en Microsoft. Los elementos (3) y (4) de la lista anterior son las ventajas que obtendrá naturalmente si decide usar VSTS. Si se usa otra plataforma de hospedaje de código para el grupo, las tareas que debe realizar el responsable de equipo seguirán siendo las mismas. Pero la forma de completar estas tareas va a ser diferente. Por ejemplo, el elemento de la sección seis, **Vincular un elemento de trabajo a una rama de GIT**, podría no ser tan sencillo como en VSTS.

En la ilustración siguiente se muestra un planeamiento de sprint típico, la codificación y el flujo de trabajo de control de código fuente implicados en la implementación de un proyecto de ciencia de datos:

![1](./media/project-execution/project-execution-1-project-execute.png)


##  1. <a name='Terminology-1'></a>Terminología 

En la plataforma de planeamiento de sprint del proceso de ciencia de datos en equipo, hay cuatro tipos más usados de **elementos de trabajo**: **característica**, **caso de usuario**, **tarea** y **error**. Cada proyecto de equipo mantiene un único trabajo pendiente para todos los elementos de trabajo. No hay ningún trabajo pendiente en el nivel de repositorio de git en un proyecto de equipo. Estas son sus definiciones:

- **Característica**: una característica se corresponde a una interacción de proyecto. Las distintas interacciones con un cliente se consideran características diferentes. De igual forma, es mejor considerar las distintas fases de un proyecto con un cliente como características diferentes. Si elige un esquema como ***ClientName-EngagementName*** para denominar las características, puede reconocer fácilmente el contexto del proyecto o interacción y de los propios nombres.
- **Caso**: los casos son elementos de trabajo diferentes que son necesarios para completar una característica (proyecto) completa. Ejemplos de casos incluyen:
    - Obtención de datos 
    - Exploración de datos 
    - Generación de características
    - Creación de modelos
    - Puesta en marcha de modelos 
    - Reentrenamiento de modelos
- **Tarea**: las tareas son elementos de trabajo de documentos o de código asignables u otras actividades que se deben realizar para completar un caso concreto. Por ejemplo, las tareas del caso *Obtención de datos* podrían ser:
    -  Obtención de credenciales de SQL Server 
    -  Actualización de datos en SQL Data Warehouse 
- **Error**: los errores normalmente hacen referencia a las correcciones necesarias en un código existente o documento que se llevan a cabo al completar una tarea. Puede pasar a convertirse en un caso o una tarea si el error se debe a fases o tareas que faltan, respectivamente. 

>[AZURE.NOTE] Se toman prestados los conceptos de características, casos, tareas y errores de la administración de código de software (SCM) para su uso en la ciencia de datos. Pueden diferir ligeramente de sus definiciones de SCM convencionales.

##  2. <a name='SprintPlanning-2'></a>Planeamiento de sprint 

El planeamiento de sprint es útil para la priorización del proyecto y el planeamiento y asignación de recursos. Muchos científicos de datos interactúan con varios proyectos, cada uno de los cuales puede tardar meses en completarse. Los proyectos suelen transcurrir a diferente ritmo. En el servidor de VSTS, puede crear, administrar y realizar el seguimiento fácilmente de los elementos de trabajo en el proyecto de equipo, y llevar a cabo el planeamiento de sprint para garantizar que los proyectos avanzan de la forma prevista. 

Siga [este vínculo](https://www.visualstudio.com/en-us/docs/work/scrum/sprint-planning) para obtener instrucciones paso a paso sobre el planeamiento de sprint en VSTS. 


##  3. <a name='AddFeature-3'></a>Adición de una característica  

Una vez creado el repositorio del proyecto en un proyecto de equipo, vaya a la página **Introducción** y haga clic en **Administrar el trabajo**.

![2](./media/project-execution/project-execution-2-sprint-team-overview.png)

Para incluir una característica en el trabajo pendiente, haga clic en **Trabajos pendientes** --> **Características** --> **Nuevo**, escriba el **título** de la característica (normalmente el nombre del proyecto) y, después, haga clic en **Agregar**.

![3](./media/project-execution/project-execution-3-sprint-team-add-work.png)

Haga doble clic en la característica que acaba de crear. Rellene las descripciones, asigne los miembros del equipo a esta característica y establezca los parámetros de planeamiento para esta característica. 

También puede vincular esta característica al repositorio del proyecto. Haga clic en **Agregar vínculo** en la sección **Desarrollo**. Cuando haya terminado de editar la característica, haga clic en **Guardar y cerrar** para salir.


##  4. <a name='AddStoryunderfeature-4'></a>Adición de un caso en la característica 

En la característica, se pueden agregar casos para describir los pasos principales necesarios para finalizar el proyecto (característica). Para agregar un nuevo caso, haga clic en el signo **+** situado a la izquierda de la característica en la vista de trabajo pendiente.  

![4](./media/project-execution/project-execution-4-sprint-add-story.png)

En la ventana emergente puede editar los detalles del caso, como el estado, la descripción, los comentarios, el planeamiento y la prioridad.

![5](./media/project-execution/project-execution-5-sprint-edit-story.png)

Puede vincular este caso a un repositorio existente haciendo clic en **+ Agregar vínculo** en **Desarrollo**. 

![6](./media/project-execution/project-execution-6-sprint-link-existing-branch.png)


##  5. <a name='AddTaskunderstory-5'></a>Adición de una tarea a un caso 

Las tareas son pasos detallados específicos necesarios para completar cada caso. Una vez completadas todas las tareas de un caso, se debe completar también el caso. 

Para agregar una tarea a un caso, haga clic en el signo **+** situado junto al elemento de caso, seleccione **Tarea** y, después, rellene la información detallada de esta tarea en la ventana emergente.

![7](./media/project-execution/project-execution-7-sprint-add-task.png)

Una vez creadas las características, los casos y las tareas, puede verlo todo en las vistas **Trabajo pendiente** o **Panel** para realizar el seguimiento de su estado.

![8](./media/project-execution/project-execution-8-sprint-backlog-view.png)

![9](./media/project-execution/project-execution-9-link-to-a-new-branch.png)


##  6. <a name='Linkaworkitemwithagitbranch-6'></a>Vincular un elemento de trabajo a una rama de GIT 

VSTS proporciona una manera práctica de conectar un elemento de trabajo (un caso o una tarea) a una rama de GIT. Esto le permite vincular el caso o la tarea directamente al código asociado. 

Para conectar un elemento de trabajo a una nueva rama, haga doble clic en un elemento de trabajo y, en la ventana emergente, haga clic en **Crear una rama nueva** en **+ Agregar vínculo**.  

![10](./media/project-execution/project-execution-10-sprint-board-view.png)

Proporcione la información para esta nueva rama, como el nombre de la rama, repositorio de GIT base y la rama. El repositorio de git elegido debe ser el repositorio en el mismo proyecto de equipo al que pertenece el elemento de trabajo. La rama base puede ser la rama principal o cualquier otra rama existente.

![11](./media/project-execution/project-execution-11-create-a-branch.png)

Un procedimiento recomendado es crear una rama de git para cada elemento de trabajo del caso. A continuación, para cada elemento de trabajo de la tarea, cree una rama en función de la rama del caso. La organización de las ramas de esta manera jerárquica que se corresponde a las relaciones entre caso y tarea es muy útil cuando hay varias personas trabajando en distintos casos del mismo proyecto, o si hay varias personas trabajando en tareas distintas del mismo caso. Los conflictos pueden reducirse cuando cada miembro del equipo trabaja en una rama distinta y cuando cada miembro trabaja en códigos diferentes u otros artefactos cuando se comparte una rama. 

En la siguiente imagen se muestra la estrategia de bifurcación recomendada para el proceso de ciencia de datos en equipo. Es posible que no necesite tantas ramas como se muestra aquí, especialmente cuando solo tiene a una o dos personas que trabajan en el mismo proyecto, o cuando solo una persona trabaja en todas las tareas de un caso. Pero siempre es un procedimiento recomendado separar la rama de desarrollo de la rama maestra. Esto puede ayudar a evitar que la rama de la versión se interrumpa por las actividades de desarrollo. Encontrará una descripción más completa del modelo de rama de GIT en [A Successful Git Branching Model](http://nvie.com/posts/a-successful-git-branching-model/) (Un modelo correcto de rama de GIT).

![12](./media/project-execution/project-execution-12-git-branches.png)

Para cambiar a la rama en la que desea trabajar, ejecute el siguiente comando en un comando shell (Windows o Linux). 

    git checkout <branch name>

Si se cambia el *<nombre de la rama\>* a **maestro**, se devuelve a la rama **maestra**. Después de cambiar a la rama de trabajo, puede comenzar a trabajar en ese elemento de trabajo mediante el desarrollo de los artefactos de código o documentación necesarios para completar el elemento. 

También puede vincular un elemento de trabajo a una rama existente. En la página **Detalle** de un elemento de trabajo, en lugar de hacer clic en **Crear una rama nueva**, haga clic en **+ Agregar vínculo**. Después, seleccione la rama a la que desea vincular el elemento de trabajo. 

![13](./media/project-execution/project-execution-13-link-to-an-existing-branch.png)

También puede crear una nueva rama en los comandos bash de git. Si falta <nombre de la rama base\>, el <nuevo nombre de rama\> se basa en la rama _maestra_. 
    
    git checkout -b <new branch name> <base branch name>


##  7. <a name='WorkonaBranchandCommittheChanges-7'></a>Trabajo en una rama y confirmación de los cambios 

Ahora suponga que realiza algún cambio la rama *data\_ingesta* para el elemento de trabajo, por ejemplo, al agregar un archivo de R en la rama en la máquina local. Puede confirmar el archivo de R que se agrega a la rama para este elemento de trabajo, siempre que esté en esa rama en el shell de git, mediante los comandos de Git siguientes:

    git status
    git add .
    git commit -m"added a R scripts"
    git push origin data_ingestion

![14](./media/project-execution/project-execution-14-sprint-push-to-branch.png)

##  8. <a name='CreateapullrequestonVSTS-8'></a>Creación de una solicitud de incorporación de cambios en VSTS 

Cuando esté listo después de unas confirmaciones e inserciones, para combinar la rama actual en su rama base, puede enviar una **solicitud de incorporación de cambios** en el servidor de VSTS. 

Vaya a la página principal del proyecto de equipo y haga clic en **CODE** (Código). Seleccione la rama que se va a combinar y el nombre del repositorio de git en el que se desea combinar la rama. Después, haga clic en **Solicitudes de incorporación de cambios** y en **Nueva solicitud de incorporación de cambios** para crear una revisión de la solicitud de incorporación de cambios antes de que el trabajo en la rama se combine en su rama base.

![15](./media/project-execution/project-execution-15-spring-create-pull-request.png)

Rellene alguna descripción sobre esta solicitud de incorporación de cambios, agregue revisores y envíelos.

![16](./media/project-execution/project-execution-16-spring-send-pull-request.png)

##  9. <a name='ReviewandMerge-9'></a>Revisión y combinación 

Cuando se crea la solicitud de incorporación de cambios, los revisores reciben una notificación de correo electrónico para revisar dichas solicitudes. Los revisores deben comprobar si los cambios funcionan o no y probar los cambios con el solicitante si es posible. En función de su evaluación, los revisores pueden aprobar o rechazar la solicitud de incorporación de cambios. 

![17](./media/project-execution/project-execution-17-add_comments.png)

![18](./media/project-execution/project-execution-18-spring-approve-pullrequest.png)

Después de realizar la revisión, la rama de trabajo se combina en la rama base, haga clic en el botón **Completar**. Puede optar por eliminar la rama de trabajo después de haberse combinado. 

![19](./media/project-execution/project-execution-19-spring-complete-pullrequest.png)

Confirme en la esquina superior izquierda que la solicitud está marcada como **COMPLETADO**. 

![20 |](./media/project-execution/project-execution-20-spring-merge-pullrequest.png)

Al volver al repositorio, bajo **CÓDIGO**, se le pide que haya cambiado a la rama principal.

![21](./media/project-execution/project-execution-21-spring-branch-deleted.png)

También puede utilizar los comandos de Git siguientes para combinar la rama de trabajo con la rama base y eliminar la rama de trabajo después de la combinación:

    git checkout master
    git merge data_ingestion
    git branch -d data_ingestion

![22](./media/project-execution/project-execution-22-spring-branch-deleted-commandline.png)


##  10. <a name='DataQualityReportUtility-10'></a>Utilidad de exploración interactiva de datos, análisis y servicio de informes (IDEAR)

Esta utilidad de R basado en marcado proporciona una herramienta flexible e interactiva para evaluar y explorar conjuntos de datos. Los usuarios pueden generar rápidamente informes desde el conjunto de datos con codificación mínima. Los usuarios pueden hacer clic en los botones para exportar los resultados de la exploración que ven en la herramienta interactiva a un informe final, que se puede entregar a los clientes o utilizar para tomar decisiones sobre qué variables hay que incluir en el paso de modelado siguiente.

En este momento, la herramienta solo funciona en tramas de datos en memoria. Se necesita un archivo .yaml para especificar los parámetros del conjunto de datos que se van a explorar. Para más información, consulte [IDEAR en utilidades de ciencia de datos de TDSP](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/DataReport-Utils).


##  11. <a name='ModelingUtility-11'></a>Utilidad de modelado y servicio de informes de base de referencia

Esta utilidad proporciona una herramienta personalizable y parcialmente automatizada para realizar la creación de modelos con barrido de hiperparámetros, y comparar la precisión de los modelos. 

La utilidad de creación de modelos es un archivo Markdown de R que se puede ejecutar para producir una salida html autónoma con una tabla de contenido para facilitar la navegación a través de sus diferentes secciones. Se realizan tres algoritmos cuando el archivo Markdown se ejecuta (convierte): regresión regularizada con el paquete glmnet, bosque aleatorio mediante el paquete randomForest y potenciación de árboles mediante el paquete xgboost). Cada uno de estos algoritmos produce un modelo entrenado. Después se compara la precisión de estos modelos y se notifican los trazados de la importancia relativa de la característica. Actualmente, hay dos utilidades: uno para una tarea de clasificación binaria y otra para una tarea de regresión. Las principales diferencias entre ellos es la forma cómo los parámetros de control y las métricas de precisión se especifican para estas tareas de aprendizaje. 

Se usa un archivo Yaml se usa para especificar:

- la entrada de datos (un origen de SQL o un archivo de datos de R) 
- qué parte de los datos se usa para el aprendizaje y qué parte para la prueba
- qué algoritmos se van a ejecutar 
- la elección de los parámetros de control para la optimización de modelo:
    - validación cruzada 
    - arranque
    - subconjuntos de la validación cruzada
- los conjuntos de hiperparámetros para cada algoritmo. 

También es posible modificar en el archivo Yaml para ejecutar los modelos rápidamente el número de algoritmos, el número de subconjuntos para la optimización, los hiperparámetros y el número de conjuntos de hiperparámetros que se itera. Por ejemplo, se pueden ejecutar con un número menor de subconjuntos de CV, un número menor de conjuntos de parámetros. También se pueden ejecutar de manera más completa con un número mayor de subconjuntos de CV o un número mayor de conjuntos de parámetros, si está justificado.

Para más información, consulte la [utilidad de modelado y servicio de informes automatizada en las utilidades de ciencia de datos de TDSP](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/Modeling).


##  12. <a name='PowerBI-12'></a>Seguimiento del progreso de los proyectos con paneles de Power BI

Los administradores de grupo de ciencia de datos, los responsables de equipo y de proyecto tienen que realizar el seguimiento del progreso de sus proyectos, qué trabajo se ha realizado en ellos y por quién, y lo que queda en la lista de tareas pendientes. Si se usa VSTS, es posible crear paneles de Power BI para realizar el seguimiento de las actividades y de los elementos de trabajo asociados a un repositorio de Git. Para más información sobre cómo conectar Power BI a Visual Studio Team Services, consulte el artículo sobre cómo [conectar Power BI a Team Services](https://www.visualstudio.com/en-us/docs/report/powerbi/connect-vso-pbi-vs). 

Para obtener información sobre cómo crear informes y paneles de Power BI para realizar un seguimiento de las actividades del repositorio de Git, así como de los elementos de trabajo después de que los datos de VSTS estén conectados a Power BI, consulte el artículo sobre cómo [crear paneles e informes de Power BI](https://www.visualstudio.com/en-us/docs/report/powerbi/report-on-vso-with-power-bi-vs). 

Presentamos dos paneles de ejemplo sencillos creados para realizar un seguimiento de las actividades de Git y de los elementos de trabajo. En el primer panel de ejemplo, se muestran las actividades de confirmación de git por usuarios diferentes, en fechas distintas y en distintos repositorios. Puede segmentar fácilmente para filtrar los que le interesen.

![23](./media/project-execution/project-execution-23-powerbi-git.png)

En el segundo panel de ejemplo, se muestran los elementos de trabajo (casos y tareas) en iteraciones diferentes. Se agrupan por personas asignadas y niveles de prioridad, y se colorean por estado.

![24](./media/project-execution/project-execution-24-powerbi-workitem.png)

 
## <a name="next-steps"></a>Pasos siguientes

También se proporcionan tutoriales completos que muestran todos los pasos del proceso en **escenarios concretos** . Se enumeran y enlazan con descripciones en miniatura en los [tutoriales de ejemplos](walkthroughs.md). En ellos se ilustra cómo combinar herramientas y servicios locales y en la nube en un flujo de trabajo o canalización para crear una aplicación inteligente. 

Para ver ejemplos de ejecución de los pasos en el Proceso de ciencia de datos en equipo que usan Azure Machine Learning Studio, consulte la ruta de aprendizaje [Con Azure ML](http://aka.ms/datascienceprocess).