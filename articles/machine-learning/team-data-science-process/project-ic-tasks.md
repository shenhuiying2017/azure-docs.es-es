---
title: Tareas del proceso de ciencia de datos en equipo para un colaborador individual en Azure | Microsoft Docs
description: Esquema de las tareas de un colaborador individual en un proyecto de equipo de ciencia de datos.
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
ms.date: 11/13/2017
ms.author: bradsev;
ms.openlocfilehash: 526260c3f61a203fe2770a0c6d0d23d95916a2b7
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/14/2017
---
# <a name="individual-contributor-tasks"></a>Tareas de colaborador individual

En este tema se describen las tareas que debe realizar un colaborador individual en su equipo de ciencia de datos. El objetivo es establecer el entorno de colaboración de equipo que se estandariza en el [proceso de ciencia de datos en equipo](overview.md) (TDSP). Para un esquema de los roles personales y sus tareas asociadas que se controlan en un equipo de ciencia de datos que se estandariza en este proceso, consulte [Roles y tareas del proceso de ciencia de datos en equipo](roles-tasks.md).

Las tareas de los colaboradores individuales del proyecto (científicos de datos) para configurar el entorno del proceso de ciencia de datos en equipo para el proyecto se muestran como sigue: 

![1](./media/project-ic-tasks/project-ic-1-tdsp-data-scientist.png)

- **GroupUtilities** es el repositorio que el grupo mantiene para compartir utilidades a todo el grupo. 
- **TeamUtilities** es el repositorio que el equipo mantiene específicamente para él. 

Para obtener instrucciones sobre cómo ejecutar un proyecto de ciencia de datos en el proceso de ciencia de datos en equipo, consulte [Ejecución de proyectos de ciencia de datos](project-execution.md). 

>[AZURE.NOTE] En las instrucciones siguientes se detallan los pasos necesarios para configurar un entorno de equipo de proceso de ciencia de datos en equipo mediante Visual Studio Team Services (VSTS). Se especifica cómo llevar a cabo estas tareas con VSTS ya que es así cómo se implementa TDSP en Microsoft. Si se usa otra plataforma de hospedaje de código para el grupo, las tareas que debe realizar el responsable de equipo seguirán siendo las mismas. Pero la forma de completar estas tareas va a ser diferente.


## <a name="repositories-and-directories"></a>Repositorios y directorios

En este tutorial se usan abreviaturas de nombres de los repositorios y directorios. Estos nombres facilitan el seguimiento de las operaciones entre los repositorios y los directorios. Esta notación (**R** para repositorios de Git y **D** para directorios locales en su DSVM) se usa en las siguientes secciones:

- **R2**: el repositorio GroupUtilities de Git que ha configurado el administrador de grupo en el servidor de grupos de VSTS.
- **R4**: el repositorio TeamUtilities de Git que haya configurado su responsable de equipo.
- **R5**: repositorio Project de Git que se ha establecido por el responsable de proyecto.
- **D2**: el directorio local se clona de R2.
- **D4**: el directorio local se clona de R4.
- **D5**: el directorio local clonado de R5.


## <a name="step-0-prerequisites"></a>Paso 0: Requisitos previos

Para cumplir los requisitos previos, es necesario realizar las tareas asignadas al administrador de grupo que se describen en [Tareas del administrador de grupo en un equipo de ciencia de datos](group-manager-tasks.md). Para resumir aquí, se deben cumplir los siguientes requisitos antes de comenzar las tareas del responsable de equipo: 
- El administrador del grupo ha configurado el repositorio **GroupUtilities** (si existe). 
- El responsable de equipo ha configurado el repositorio **TeamUtilities** (si existe).
- El responsable de proyecto ha configurado el repositorio del proyecto. 
- El responsable de proyecto le ha agregado al repositorio del proyecto con el privilegio de clonar el repositorio del proyecto y de volver a insertarlo.

En el segundo repositorio **TeamUtilities** los requisitos previos son opcionales, dependiendo de si su equipo tiene un repositorio de utilidades específico para el equipo. Si no se ha completado cualquiera de los otros tres requisitos previos, póngase en contacto con el responsable de equipo, el responsable de proyecto o sus delegados para realizar la configuración siguiendo las instrucciones en los artículos sobre las [tareas del responsable de equipo para un equipo de ciencia de datos](team-lead-tasks.md) o sobre las [tareas del responsable de proyecto para un equipo de ciencia de datos](project-lead-tasks.md).

- Git debe estar instalado en la máquina. Si usa una instancia de Data Science Virtual Machine (DSVM), Git se ha instalado previamente y está listo para continuar. En caso contrario, consulte el [apéndice de plataformas y herramientas](platforms-and-tools.md#appendix).  
- Si usa una **DSVM de Windows**, debe tener [Git Credential Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) instalado en su máquina. En el archivo README.md, desplácese a la sección **Descargar e instalar** y haga clic en el *instalador más reciente*. Este paso le lleva a la página más reciente del instalador. Descargue al instalador .exe desde aquí y ejecútelo. 
- Si usa una instancia de **DSVM de Linux**, cree una clave pública SSH en su DSVM y agréguela al servidor de VSTS del grupo. Para más información acerca de SSH, consulte la sección acerca de cómo **crear una clave pública SSH** en el [apéndice de plataformas y herramientas](platforms-and-tools.md#appendix). 
- Si el responsable de equipo o de proyecto han creado algún almacenamiento de archivos de Azure que se necesite para montar la DSVM, debe obtener la información de almacenamiento de archivos de Azure de ellos. 

## <a name="step-1-3-clone-group-team-and-project-repositories-to-local-machine"></a>Paso 1-3: Clonar los repositorios de grupos, equipos y proyectos en la máquina local

En esta sección se proporcionan instrucciones sobre cómo completar las tres primeras tareas de los colaboradores individuales del proyecto: 

- Clonar el repositorio **GroupUtilities** R2 en D2
- Clonar el repositorio **GroupUtilities** R4 en D4 
- Clone el repositorio **Project** R5 en D5.

En la máquina local, cree un directorio ***C:\GitRepos*** (para Windows) o ***$home/GitRepos*** (para Linux) y, después, cambie a ese directorio. 

Ejecute uno de los siguientes comandos (según corresponda al sistema operativo) para clonar los repositorios **GroupUtilities**, **TeamUtilities** y **Project** en directorios en la máquina local: 

**Windows**
    
    git clone <the URL of the GroupUtilities repository>
    git clone <the URL of the TeamUtilities repository>
    git clone <the URL of the Project repository>
    
![2](./media/project-ic-tasks/project-ic-2-clone-three-repo-to-ic.png)

Confirme que ve las tres carpetas en el directorio del proyecto.

![3](./media/project-ic-tasks/project-ic-3-three-repo-cloned-to-ic.png)

**Linux**
    
    git clone <the SSH URL of the GroupUtilities repository>
    git clone <the SSH URL of the TeamUtilities repository>
    git clone <the SSH URL of the Project repository>

![4](./media/project-ic-tasks/project-ic-4-clone-three-repo-to_ic-linux.png)

Confirme que ve las tres carpetas en el directorio del proyecto.

![5](./media/project-ic-tasks/project-ic-5-three-repo-cloned-to-ic-linux.png)

## <a name="step-4-5-mount-azure-file-storage-to-your-dsvm-optional"></a>Paso 4-5: Montar el almacenamiento de archivos de Azure en la DSVM (opcional)

Para montar el almacenamiento de archivos de Azure en la DSVM, consulte las instrucciones que aparecen en la sección 4 de las [tareas del responsable de equipo en un equipo de ciencia de datos](team-lead-tasks.md).

## <a name="next-steps"></a>Pasos siguientes

Estos son vínculos a descripciones más detalladas de los roles y tareas definidas por el proceso de ciencia de datos en equipo:

- [Tareas del administrador de grupo en un equipo de ciencia de datos](group-manager-tasks.md)
- [Tareas del responsable de equipo en un equipo de ciencia de datos](team-lead-tasks.md)
- [Tareas del responsable de proyecto en un equipo de ciencia de datos](project-lead-tasks.md)
- [Colaboradores individuales del proyecto en un equipo de ciencia de datos](project-ic-tasks.md)

