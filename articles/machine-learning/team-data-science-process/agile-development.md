---
title: Desarrollo de Agile de proyectos de ciencia de datos - Azure Machine Learning | Microsoft Docs
description: "Forma en que los desarrolladores pueden ejecutar un proyecto de ciencia de datos de forma sistemática, con control de versiones y en colaboración dentro de un equipo de proyecto mediante el Proceso de ciencia de los datos en equipos."
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
ms.openlocfilehash: 686f751b241d49d116948711c683f4b504d5d5f9
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/30/2017
---
# <a name="agile-development-of-data-science-projects"></a>Desarrollo de Agile de proyectos de ciencia de datos

En este documento describe la forma en que los desarrolladores pueden ejecutar un proyecto de ciencia de datos de forma sistemática, con control de versiones y en colaboración dentro de un equipo de proyecto mediante el [Proceso de ciencia de los datos en equipos](overview.md) (TDSP). El proceso de ciencia de datos en equipo es una plataforma desarrollada por Microsoft que proporciona una secuencia estructurada de actividades para ejecutar de forma eficaz soluciones de análisis predictivos y basadas en la nube. En [Roles y tareas del proceso de ciencia de datos en equipo](roles-tasks.md) dispone de un esquema de los roles profesionales y las tareas asociadas de las que se ocupa un equipo de ciencia de datos que sigue este proceso como estándar. 

En este artículo se incluyen instrucciones acerca de cómo: 

1. Realizar el **planeamiento de sprint** para los elementos de trabajo implicados en un proyecto.<br> Si no conoce el planeamiento de sprint, puede encontrar detalles e información general [aquí](https://en.wikipedia.org/wiki/Sprint_(software_development) "aquí"). 
2. **Agregar elementos de trabajo** a los sprint. 

> [!NOTE]
> En el siguiente conjunto de instrucciones se detallan los pasos necesarios para configurar un entorno de equipo de proceso de ciencia de datos en equipo mediante Visual Studio Team Services (VSTS). Especifican cómo llevar a cabo estas tareas con VSTS, ya que es así cómo se implementa TDSP en Microsoft.  Si decide usar VSTS, los elementos (3) y (4) de la lista anterior son las ventajas que obtendrá de forma natural. Si se usa otra plataforma de hospedaje de código para el grupo, las tareas que debe realizar el responsable de equipo seguirán siendo las mismas. Pero la forma de completar estas tareas va a ser diferente. Por ejemplo, el elemento de la sección seis, **Vincular un elemento de trabajo a una rama de GIT**, podría no ser tan sencillo como en VSTS.
>
>

En la siguiente ilustración se muestra un flujo de trabajo habitual de planeación, codificación y control de origen de sprint que se realiza al implementar un proyecto de ciencia de datos:

![1](./media/agile-development/1-project-execute.png)


##  1. <a name='Terminology-1'></a>Terminología 

En la plataforma de planeamiento de sprint del proceso de ciencia de datos en equipo, hay cuatro tipos más usados de **elementos de trabajo**: **característica**, **caso de usuario**, **tarea** y **error**. Cada proyecto de equipo mantiene un único trabajo pendiente para todos los elementos de trabajo. No hay ningún trabajo pendiente en el nivel de repositorio de Git en un proyecto de equipo. Estas son sus definiciones:

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
- **Error**: los errores normalmente hacen referencia a las correcciones necesarias en un código existente o documento que se llevan a cabo al completar una tarea. Si el error se debe a que faltan fases o tareas, respectivamente, puede pasar a convertirse en un caso o una tarea. 

> [!NOTE]
> Los conceptos se toman prestados de las características, los casos, las tareas y los errores de la administración de código de software (SCM) para su uso en la ciencia de datos. Pueden diferir ligeramente de sus definiciones de SCM convencionales.
>
>

> [!NOTE]
> Los científicos de datos pueden sentirse más cómodos si usan una plantilla de Agile que se adapte específicamente a las fases del ciclo de vida de TDSP. Con esto en mente, se ha creado una plantilla de planeación de sprint derivada de Agile, donde las epopeyas, los casos, etc. se reemplazan por fases o subfases del ciclo de vida de TDSP. Para obtener instrucciones sobre cómo crear una plantilla de Agile, consulte [Set up agile data science process in Visual Studio Online](agile-development.md#set-up-agile-dsp-6) (Configurar el proceso de ciencia de datos de Agile en Visual Studio Online).
>
>

## 2. <a name='SprintPlanning-2'></a>Planeamiento de sprint 

El planeamiento de sprint es útil para la priorización del proyecto y el planeamiento y asignación de recursos. Muchos científicos de datos interactúan con varios proyectos, cada uno de los cuales puede tardar meses en completarse. Los proyectos suelen transcurrir a diferente ritmo. En el servidor de VSTS, puede crear, administrar y realizar el seguimiento fácilmente de los elementos de trabajo en el proyecto de equipo, y llevar a cabo el planeamiento de sprint para garantizar que los proyectos avanzan de la forma prevista. 

Siga [este vínculo](https://www.visualstudio.com/en-us/docs/work/scrum/sprint-planning) para obtener instrucciones paso a paso sobre el planeamiento de sprint en VSTS. 


## 3. <a name='AddFeature-3'></a>Adición de una característica  

Una vez creado el repositorio del proyecto en un proyecto de equipo, vaya a la página **Introducción** y haga clic en **Administrar el trabajo**.

![2](./media/agile-development/2-sprint-team-overview.png)

Para incluir una característica en el trabajo pendiente, haga clic en **Trabajos pendientes** --> **Características** --> **Nuevo**, escriba el **título** de la característica (normalmente el nombre del proyecto) y, después, haga clic en **Agregar**.

![3](./media/agile-development/3-sprint-team-add-work.png)

Haga doble clic en la característica que ha creado. Rellene las descripciones, asigne los miembros del equipo a esta característica y establezca los parámetros de planeamiento para esta característica. 

También puede vincular esta característica al repositorio del proyecto. Haga clic en **Agregar vínculo** en la sección **Desarrollo**. Cuando haya terminado de editar la característica, haga clic en **Guardar y cerrar** para salir.


## 4. <a name='AddStoryunderfeature-4'></a>Adición de un caso en la característica 

En la característica, se pueden agregar casos para describir los pasos principales necesarios para finalizar el proyecto (característica). Para agregar un nuevo caso, haga clic en el signo **+** situado a la izquierda de la característica en la vista de trabajo pendiente.  

![4](./media/agile-development/4-sprint-add-story.png)

En la ventana emergente puede editar los detalles del caso, como el estado, la descripción, los comentarios, el planeamiento y la prioridad.

![5](./media/agile-development/5-sprint-edit-story.png)

Puede vincular este caso a un repositorio existente haciendo clic en **+ Agregar vínculo** en **Desarrollo**. 

![6](./media/agile-development/6-sprint-link-existing-branch.png)


## 5. <a name='AddTaskunderstory-5'></a>Adición de una tarea a un caso 

Las tareas son pasos detallados específicos necesarios para completar cada caso. Una vez completadas todas las tareas de un caso, se debe completar también el caso. 

Para agregar una tarea a un caso, haga clic en el signo **+** situado junto al elemento de caso, seleccione **Tarea** y, después, rellene la información detallada de esta tarea en la ventana emergente.

![7](./media/agile-development/7-sprint-add-task.png)

Una vez creadas las características, los casos y las tareas, puede verlo todo en las vistas **Trabajo pendiente** o **Panel** para realizar el seguimiento de su estado.

![8](./media/agile-development/8-sprint-backlog-view.png)

![9](./media/agile-development/9-link-to-a-new-branch.png)


## 6. <a name='set-up-agile-dsp-6'></a> Configuración de una plantilla de trabajo de TDSP de Agile en Visual Studio Online

En este artículo se explica cómo configurar una plantilla de proceso de ciencia de datos de Agile que usa las fases del ciclo de vida de ciencia de datos de TDSP y realiza un seguimiento de los elementos de trabajo con Visual Studio Online (vso). En los pasos indicados a continuación se muestra un ejemplo de cómo configurar la plantilla de proceso de Agile específica de la ciencia de datos *AgileDataScienceProcess* y de cómo crear elementos de trabajo de ciencia de datos basados en la plantilla.

### <a name="agile-data-science-process-template-setup"></a>Configuración de la plantilla de proceso de ciencia de datos de Agile

1. Vaya a la página principal del servidor,  **Configurar** -> **Proceso**.

    ![10](./media/agile-development/10-settings.png) 

2. Vaya a **Todos los procesos** -> **Procesos**, en **Agile** y haga clic en **Crear proceso heredado**. A continuación, ponga el nombre de proceso "AgileDataScienceProcess" y haga clic en **Crear proceso**.

    ![11](./media/agile-development/11-agileds.png)

3. En la pestaña **AgileDataScienceProcess** -> **Tipos de elementos de trabajo**, deshabilite los tipos de elementos de trabajo **Epopeya**, **Característica**, **Caso de usuario** y **Tarea** por **Configurar -> Deshabilitar**

    ![12](./media/agile-development/12-disable.png)

4. Vaya a la pestaña **AgileDataScienceProcess** -> **Niveles de trabajo pendiente**. Reemplace el nombre "Epopeyas" por "Proyectos de TDSP" haciendo clic en **Configurar** -> **Editar/Cambiar nombre**. En el mismo cuadro de diálogo, haga clic en **+Nuevo tipo de elemento de trabajo** en "Proyecto de ciencia de datos" y establezca el valor de **Tipo de elemento de trabajo predeterminado** en "Proyecto de TDSP" 

    ![13](./media/agile-development/13-rename.png)  

5. De forma similar, reemplace el nombre de trabajo pendiente "Características" por "Fases de TDSP" y agregue lo siguiente al **Nuevo tipo de elemento de trabajo**:

    - Conocimiento del negocio
    - Adquisición de datos
    - Modelado
    - Implementación

6. Reemplace el nombre "Caso de usuario" por "Subfases de TDSP" con el tipo de elemento de trabajo predeterminado establecido en el tipo "Subfase de TDSP" recién creado.

7. Establezca las "Tareas" en el tipo de elemento de trabajo recién creado "Tarea de TDSP" 

8. Después de estos pasos, los niveles de trabajo pendiente deberían tener el aspecto siguiente:

    ![14](./media/agile-development/14-template.png)  

 
### <a name="create-data-science-work-items"></a>Crear elementos de trabajo de ciencia de datos

Una vez creada la plantilla de proceso de ciencia de datos, puede crear y realizar un seguimiento de los elementos de trabajo de ciencia de datos correspondientes al ciclo de vida del TDSP.

1. Cuando cree un nuevo proyecto de equipo, seleccione "Agile\AgileDataScienceProcess" como **Proceso de elemento de trabajo**:

    ![15](./media/agile-development/15-newproject.png)

2. Vaya al proyecto de equipo recién creado y haga clic en **Trabajo** -> **Trabajos pendientes**.

3. Haga que "Proyectos de TDSP" sea visible haciendo clic en **Definir la configuración del equipo** y compruebe "Proyectos de TDSP"; a continuación, haga clic en Guardar.

    ![16](./media/agile-development/16-enabledsprojects.png)

4. Ahora puede empezar a crear los elementos de trabajo específicos de la ciencia de datos.

    ![17](./media/agile-development/17-dsworkitems.png)

5. A continuación se muestra un ejemplo del aspecto que deberían tener los elementos de trabajo del proyecto de ciencia de datos:

    ![18](./media/agile-development/18-workitems.png)


## <a name="next-steps"></a>Pasos siguientes

En [Collaborative coding with Git](collaborative-coding-with-git.md) (Codificación colaborativa con Git) se describe cómo desarrollar código colaborativo para proyectos de ciencia de datos con Git como marco de desarrollo de código compartido y cómo vincular estas actividades de codificación al trabajo planeado con el proceso de Agile.

A continuación se muestran vínculos adicionales a los recursos de los procesos de Agile.

- Proceso de Agile   [https://www.visualstudio.com/en-us/docs/work/guidance/agile-process](https://www.visualstudio.com/en-us/docs/work/guidance/agile-process)
- Flujo de trabajo y tipos de elementos de trabajo de proceso de Agile  [https://www.visualstudio.com/en-us/docs/work/guidance/agile-process-workflow](https://www.visualstudio.com/en-us/docs/work/guidance/agile-process-workflow)


También se proporcionan tutoriales que muestran todos los pasos del proceso en **escenarios concretos**. Se enumeran y enlazan con descripciones en miniatura en los [tutoriales de ejemplo](walkthroughs.md). En ellos se ilustra cómo combinar herramientas y servicios locales y en la nube en un flujo de trabajo o canalización para crear una aplicación inteligente. 
