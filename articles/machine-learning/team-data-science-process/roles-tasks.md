---
title: 'Roles y tareas del proceso de ciencia de datos en equipo: Azure | Microsoft Docs'
description: "Una descripción de los componentes clave, las funciones del personal y las tareas asociadas para un proyecto de equipo de ciencia de datos."
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
ms.openlocfilehash: b502a586cdb7351d5b22c6b0db966723b8ebb7b4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="team-data-science-process-roles-and-tasks"></a>Roles y tareas del proceso de ciencia de datos en equipo

El proceso de ciencia de datos en equipo es una plataforma desarrollada por Microsoft que proporciona una metodología estructurada para crear soluciones de análisis predictivo y aplicaciones inteligentes de un modo eficaz. En este artículo se describen los roles clave del personal y sus tareas asociadas, que se administran mediante un equipo de ciencia de datos en equipo que estandariza este proceso. 

Esta introducción enlaza a tutoriales que proporcionan instrucciones sobre cómo configurar el entorno de TDSP para el grupo de ciencia de datos completo, los equipos de ciencia de datos y los proyectos. En los tutoriales se proporcionan instrucciones detalladas del uso de Visual Studio Team Services (VSTS) como plataforma de hospedaje de código y herramienta de planeación de Agile para administrar las tareas del equipo, controlar el acceso y administrar los repositorios. 

También podrá utilizar esta información para implementar TDSP en su propia herramienta de planeación de Agile y hospedaje de código. 

## <a name="structures-of-data-science-groups-and-teams"></a>Estructuras de los grupos y equipos de ciencia de datos
Las funciones de ciencia de datos en las empresas a menudo se pueden organizar en la siguiente jerarquía:

1. ***Grupos de ciencia de datos***

2. ***Equipos de ciencia de datos dentro de grupos***

En este tipo de estructura habrá responsables de grupo y equipo. Normalmente, un proyecto de ciencia de datos lo realiza un equipo de ciencia de datos, que puede estar formado por responsables de proyecto (para tareas de administración y regulación del proyecto) y científicos de datos o ingenieros (colaboradores individuales y personal técnico) que ejecutarán las partes de ciencia de datos e ingeniería de datos del proyecto. Antes de la ejecución, los responsables de grupo, equipo o proyecto realizan la configuración y regulación.

## <a name="definition-of-four-tdsp-roles"></a>Definición de los cuatro roles de TDSP
Con la suposición anterior, especificamos cuatro roles diferentes para el personal del equipo:

1. ***Administrador de grupo***. El Administrador de grupo es el administrador de la unidad de ciencia de datos completa en una empresa. Una unidad de ciencia de datos podría tener varios equipos, cada uno de ellos trabajando en varios proyectos de ciencia de datos en segmentos verticales de negocio distintos. Un Administrador de grupo puede delegar sus tareas en un suplente, pero no cambian las tareas asociadas al rol.

2. ***Responsable de equipo***. Un responsable de equipo administra un equipo en la unidad de ciencia de datos de una empresa. Un equipo está formada por varios científicos de datos. En una unidad de ciencia de datos con un número reducido de científicos de datos, el Administrador de grupo y el responsable de equipo podrían ser la misma persona.

3. ***Responsable de proyecto***. Un responsable de proyecto administra las actividades diarias de los científicos de datos en un proyecto de ciencia de datos específico.

4. ***Colaborador individual del proyecto***. Científico de datos, analista de negocios, ingeniero de datos, arquitecto, etc. Un colaborador individual del proyecto ejecuta un proyecto de ciencia de datos. 


**[AZURE.NOTE]**: En función de la estructura de una empresa, una sola persona puede desempeñar más de un rol o puede haber más de una persona trabajando en un rol. Con frecuencia, este puede ser el caso de pequeñas empresas o empresas con un pequeño número de empleados en la organización de ciencia de datos.

## <a name="tasks-to-be-completed-by-four-personnel"></a>Tareas que debe realizar el personal de los cuatro roles

La siguiente imagen muestra las tareas de nivel superior por cada rol del personal necesarias para adoptar e implementar el proceso de ciencia de datos en equipo según la conceptualización de Microsoft. 

![Información general de roles y tareas](./media/roles-tasks/overview-tdsp-top-level.png)

Este esquema y el esquema siguiente, con más detalle de las tareas que se asignan a cada rol en TDSP, le ayudarán a elegir el tutorial adecuado en función de sus responsabilidades en la organización.

>[AZURE.NOTE] En las instrucciones siguientes, se muestran los pasos necesarios para configurar un entorno de TDSP y realizar otras tareas de ciencia de datos en Visual Studio Team Services (VSTS). Se especifica cómo llevar a cabo estas tareas con VSTS ya que es lo que se está usando para implementar TDSP en Microsoft. VSTS facilita la colaboración mediante la integración de la administración de elementos de trabajo que realizan el seguimiento de tareas y un servicio de hospedaje de código utilizado para compartir utilidades, organizar versiones y proporcionar seguridad basada en roles. Es posible elegir otras plataformas, si lo prefiere, para implementar las tareas descritas por TDSP. Pero, dependiendo de la plataforma, puede que algunas características que hemos aprovechado de VSTS no estén disponibles. 
>
>También utilizamos [Data Science Virtual Machine (DSVM)](http://aka.ms/dsvm) en la nube de Azure como escritorio de análisis con varias herramientas de ciencia de datos populares configuradas previamente e integradas con distintos software de Microsoft y servicios de Azure. Puede utilizar DSVM o cualquier otro entorno de desarrollo para implementar TDSP. 


## <a name="group-manager-tasks"></a>Tareas del administrador de grupo

Las siguientes tareas las realiza el administrador de grupo (o un administrador del sistema TDSP designado) para adoptar TDSP:

- Crear una **cuenta de grupo** en una plataforma de hospedaje de código (por ejemplo, Github, Git, VSTS u otras)
- Crear un **repositorio de plantillas de proyecto** en la cuenta de grupo y su inicialización desde el repositorio de plantillas de proyecto desarrollado por el equipo TDSP de Microsoft. El repositorio de plantillas de proyecto de TDSP de Microsoft proporciona una **estructura de directorios estandarizada**, incluidos directorios de datos, código y documentos y proporciona un conjunto de **plantillas de documentos estandarizadas** para guiar un proceso de ciencia de datos eficaz. 
- Crear un **repositorio de utilidades** e inicializarlo desde el repositorio de utilidades desarrollado por el equipo de TDSP de Microsoft. El repositorio de utilidades de TDSP de Microsoft proporciona un conjunto de utilidades para realizar el trabajo de un científico de datos de un modo eficaz, lo que incluye utilidades para exploración interactiva de datos, análisis e informes y para informes y modelos de base de referencia.
- Configurar la **directiva de control de seguridad** de estos dos repositorios en la cuenta de grupo.  

Para obtener instrucciones paso a paso detalladas, consulte [Tareas del administrador de grupo en un equipo de ciencia de datos](group-manager-tasks.md). 


## <a name="team-lead-tasks"></a>Tareas del responsable de equipo

Las siguientes tareas las realiza el responsable de equipo (o un administrador de proyecto del equipo designado) para adoptar TDSP:

- Si se selecciona VSTS como la plataforma de hospedaje de código para control de versiones y colaboración, crear un **proyecto de equipo** en el servidor de VSTS del grupo. En caso contrario, se puede omitir esta tarea.
- Crear el **repositorio de plantillas de proyecto de equipo** en el proyecto de equipo e inicializarlo desde el repositorio de plantillas de proyecto del grupo creado por el administrador del grupo o el delegado por el administrador. 
- Crear el **repositorio de utilidades del equipo** y agregar las utilidades específicas del equipo en el repositorio. 
- (Opcional) Crear almacenamiento en  **[Azure File Storage](https://azure.microsoft.com/services/storage/files/)**  que se utilizará para almacenar los recursos de datos que puedan ser útiles para todo el equipo. Otros miembros del equipo pueden montar este almacén de archivos compartidos en la nube en sus escritorios de análisis.
- (Opcional) Montar el almacenamiento de Azure File Storage en la instancia de **Data Science Virtual Machine** (DSVM) del responsable de equipo y agregar los recursos de datos en ella.
- Configurar el **control de seguridad** mediante la agregación de los miembros del equipo y configurar sus privilegios. 

Para obtener instrucciones paso a paso detalladas, consulte [Tareas del responsable de equipo en un equipo de ciencia de datos](team-lead-tasks.md).  


## <a name="project-lead-tasks"></a>Tareas del responsable de proyecto

Las siguientes tareas las realiza el responsable de proyecto para adoptar TDSP:

- Crear un **repositorio del proyecto** en el proyecto de equipo e inicializarlo desde el repositorio de plantillas del proyecto de equipo. 
- (Opcional) Crear almacenamiento en **Azure File Storage** que se utilizará para almacenar los recursos de datos del proyecto. 
- (Opcional) Montar el almacenamiento de Azure File Storage en la instancia de **Data Science Virtual Machine** (DSVM) del responsable de proyecto y agregar los recursos de datos del proyecto en ella.
- Configurar el **control de seguridad** mediante la agregación de los miembros del proyecto y configurar sus privilegios. 

Para obtener instrucciones paso a paso detalladas, consulte [Tareas del responsable de proyecto en un equipo de ciencia de datos](project-lead-tasks.md). 

## <a name="project-individual-contributor-tasks"></a>Tareas del colaborador individual del proyecto

Las siguientes tareas las realiza un colaborador individual del proyecto (normalmente un científico de datos) para llevar a cabo el proyecto de ciencia de datos mediante TDSP:

- Clonar el **repositorio del proyecto** configurado por el responsable de proyecto. 
- (Opcional) Montar el recurso compartido de **Azure File Storage** del equipo y el proyecto en la instancia de **Data Science Virtual Machine** (DSVM).
- Ejecutar el proyecto. 

 
Para obtener instrucciones detalladas paso a paso sobre la incorporación a un proyecto, consulte [Colaboradores individuales del proyecto en un equipo de ciencia de datos](project-ic-tasks.md). 


## <a name="data-science-project-execution"></a>Ejecución del proyecto de ciencia de datos
 
Siguiendo el conjunto de instrucciones pertinente, los científicos de datos, el responsable de proyecto y los responsables de equipo pueden crear elementos de trabajo para realizar el seguimiento de todas las tareas y las fases que necesita un proyecto de principio a fin. El uso de Git también fomenta la colaboración entre los científicos de datos y garantiza que los artefactos que se generan durante la ejecución del proyecto se controlan mediante versión y son compartidos por todos los miembros del proyecto.

Las instrucciones proporcionadas para la ejecución del proyecto se han desarrollado en función de la suposición de que los elementos de trabajo y los repositorios Git del proyecto están en VSTS. El uso de VSTS para ambos permite vincular los elementos de trabajo con las bifurcaciones de Git de los repositorios del proyecto. De esta manera, puede controlar fácilmente qué se ha realizado para un elemento de trabajo. 

La siguiente ilustración muestra este flujo de trabajo para la ejecución del proyecto mediante TDSP.

![Ejecución de proyecto de ciencia de datos típica](./media/roles-tasks/overview-project-execute.png)

El flujo de trabajo incluye pasos que se pueden agrupar en tres actividades:

- Planeamiento de sprint (responsable de proyecto)
- Desarrollo de artefactos en bifurcaciones de Git para tratar los elementos de trabajo (científico de datos)
- Revisión de código y combinación de bifurcaciones con bifurcaciones maestras (responsable de proyecto u otros miembros del equipo)

Para obtener instrucciones detalladas paso a paso del flujo de trabajo de ejecución del proyecto, consulte [Ejecución de proyectos de ciencia de datos](project-execution.md).

## <a name="next-steps"></a>Pasos siguientes

Estos son vínculos a descripciones más detalladas de los roles y tareas definidas por el proceso de ciencia de datos en equipo:

- [Tareas del administrador de grupo en un equipo de ciencia de datos](group-manager-tasks.md)
- [Tareas del responsable de equipo en un equipo de ciencia de datos](team-lead-tasks.md)
- [Tareas del responsable de proyecto en un equipo de ciencia de datos](project-lead-tasks.md)
- [Colaboradores individuales del proyecto en un equipo de ciencia de datos](project-ic-tasks.md)