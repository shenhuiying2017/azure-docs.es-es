---
title: Tareas del responsable de proyecto del proceso de ciencia de datos en equipo para Azure | Microsoft Docs
description: Un esquema de las tareas de un responsable de proyecto en un proyecto en equipo de ciencia de datos.
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
ms.openlocfilehash: fe7cf1e6167de7d0dfaa5ed75c0b7f5ec23b18cf
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/14/2017
---
# <a name="project-lead-tasks"></a>Tareas del responsable de proyecto

Este tutorial describe las tareas que el responsable de proyecto debe completar para su equipo de proyecto. El objetivo es establecer el entorno de colaboración de equipo que se estandariza en el [proceso de ciencia de datos en equipo](overview.md) (TDSP). El proceso de ciencia de datos en equipo es una plataforma desarrollada por Microsoft que proporciona una secuencia estructurada de actividades para ejecutar de forma eficaz soluciones de análisis predictivos y basadas en la nube. Para un esquema de los roles personales y sus tareas asociadas que se controlan en un equipo de ciencia de datos que se estandariza en este proceso, consulte [Roles y tareas del proceso de ciencia de datos en equipo](roles-tasks.md).

Un **responsable de proyecto** administra las actividades diarias de los científicos de datos en un proyecto de ciencia de datos específico. El flujo de trabajo de las tareas que van a realizar los responsables de equipo para configurar este entorno se representa en la figura siguiente:

![1](./media/project-lead-tasks/project-leads-1-tdsp-creating-projects.png)

En este tema se tratan las tareas 1,2 y 6 de este flujo de trabajo para los responsables de proyecto.

>[AZURE.NOTE] En las instrucciones siguientes se detallan los pasos necesarios para configurar un entorno de equipo de proceso de ciencia de datos en equipo para un proyecto mediante Visual Studio Team Services (VSTS). Se especifica cómo llevar a cabo estas tareas con VSTS ya que es así cómo se implementa TDSP en Microsoft. Si se usa otra plataforma de hospedaje de código para el grupo, las tareas que debe realizar el responsable de equipo seguirán siendo las mismas. Pero la forma de completar estas tareas va a ser diferente.


## <a name="repositories-and-directories"></a>Repositorios y directorios

En este tutorial se usan abreviaturas de nombres de los repositorios y directorios. Estos nombres facilitan el seguimiento de las operaciones entre los repositorios y los directorios. Esta notación (R para repositorios Git y D para directorios locales de su DSVM) se usa en las siguientes secciones:

- **R3**: el repositorio **ProjectTemplate** del equipo de Git que ha configurado el responsable de equipo.
- **R5**: el repositorio de proyecto de Git que ha configurado para el proyecto.
- **D3**: el directorio local se clona de R3.
- **D5**: el directorio local clonado de R5.


## <a name="0-prerequisites"></a>0. Requisitos previos

Para cumplir los requisitos previos, es necesario realizar las tareas asignadas al administrador de grupo que se describen en [Tareas del administrador de grupo en un equipo de ciencia de datos](group-manager-tasks.md) y al responsable de equipo descrito en [Tareas del responsable de equipo para un equipo de ciencia de datos](team-lead-tasks.md). 

Para resumir aquí, se deben cumplir los siguientes requisitos antes de comenzar las tareas del responsable de equipo: 

- El administrador de grupo debe haber configurado el **servidor VSTS del grupo** (o la cuenta de grupo en otras plataformas de hospedaje de código).
- El responsable de equipo debe haber configurado el **repositorio TeamProjectTemplate** (R3) en la cuenta de grupo en la plataforma de hospedaje de código que planee usar.
- El responsable de equipo le ha **autorizado** para crear repositorios en su cuenta de grupo para su equipo.
- Git debe estar instalado en la máquina. Si usa una instancia de Data Science Virtual Machine (DSVM), Git se ha instalado previamente y está listo para continuar. En caso contrario, consulte el [apéndice de plataformas y herramientas](platforms-and-tools.md#appendix).  
- Si usa una **DSVM de Windows**, debe tener [Git Credential Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) instalado en su máquina. En el archivo README.md, desplácese a la sección **Descargar e instalar** y haga clic en el *instalador más reciente*. Este paso le lleva a la página más reciente del instalador. Descargue al instalador .exe desde aquí y ejecútelo. 
- Si usa una instancia de **DSVM de Linux**, cree una clave pública SSH en su DSVM y agréguela al servidor de VSTS del grupo. Para más información acerca de SSH, consulte la sección acerca de cómo **crear una clave pública SSH** en el [apéndice de plataformas y herramientas](platforms-and-tools.md#appendix). 


## <a name="1-create-a-project-repository-r5"></a>1. Creación de un repositorio del proyecto (R5)

- Inicie sesión en el servidor de VSTS del grupo en *https://\<Nombre del servidor de VSTS\>. visualstudio.com*. 
- En **Proyectos y equipos recientes**, haga clic en **Examinar**. Una ventana emergente muestra todos los proyectos de equipo en el servidor VSTS. 

    ![2](./media/project-lead-tasks/project-leads-2-create-project-repo.png)

- Haga clic en el nombre del proyecto de equipo en el que va a crear el repositorio del proyecto. En este ejemplo, haga clic en **MyTeam**. 
- Después, haga clic en **Navegar** para acceder a la página principal del proyecto de equipo **MyTeam**:

    ![3](./media/project-lead-tasks/project-leads-3-create-project-repo-2.png)

- Haga clic en **Colaborar en el código** para que se dirijan a la página principal de git del proyecto de equipo.  

    ![4](./media/project-lead-tasks/project-leads-4-create-project-repo-3.png)

- Haga clic en la flecha hacia abajo en la esquina superior izquierda y seleccione **+ Nuevo repositorio**. 
    
    ![5](./media/project-lead-tasks/project-leads-5-create-project-repo-4.png)

- En la ventana **Crear un nuevo repositorio**, indique un nombre para el repositorio de git del proyecto. Asegúrese de que selecciona **Git** como tipo de repositorio. En este ejemplo, se utiliza el nombre *DSProject1*. 

    ![6](./media/project-lead-tasks/project-leads-6-create-project-repo-5.png)

- Para crear el repositorio de Git del proyecto ***DSProject1***, haga clic en **Crear**.


## <a name="2-seed-the-dsproject1-project-repository"></a>2. Propagación del repositorio del proyecto DSProject1

La tarea aquí es propagar el repositorio del proyecto **DSProject1** (R5) desde el repositorio de plantillas de proyecto de equipo (R3). El procedimiento de propagación usa los directorios D3 y D5 de instancia de DSVM como sitios de almacenamiento provisional intermedios. En resumen, es la ruta de acceso de propagación: R3 -> D3 -> D5 -> R5.

Si necesita personalizar el repositorios del proyecto **DSProject1** para satisfacer algunas necesidades específicas, lo hará en el penúltimo paso del procedimiento siguiente. Este es un resumen de los pasos que se usan para propagar el contenido del repositorio del proyecto **DSProject1**. Los pasos individuales corresponden a las subsecciones del procedimiento de propagación:

- Clone el repositorio de plantillas de proyecto de equipo en el directorio local: equipo R3 - clonado en -> D3 local.
- Clone el repositorio de DSProject1 en un directorio local: R5 de equipo - clonado a -> D5 local.
- Copie el contenido de la plantilla de proyecto de equipo clonado en clon local del repositorio DSProject1: D3 - contenido copiado en -> D5.
- (Opcional) Personalización de D5 local
- Inserte el contenido de DSProject1 local en los repositorios de equipo: D5 - el contenido se agrega a -> R5 de equipo.


### <a name="clone-your-team-project-template-repository-r3-to-a-directory-d3-on-your-local-machine"></a>Clone el repositorio de plantillas de proyecto de equipo (R3) en un directorio (D3) en la máquina local.

En la máquina local, cree un directorio:

- *C:\GitRepos\MyTeamCommon* para Windows 
- *$home/GitRepos/MyTeamCommon* para Linux

Cambie a ese directorio. Después, ejecute el comando siguiente para clonar el repositorio de plantillas de proyecto de equipo en la máquina local. 

**Windows**
            
    git clone <the HTTPS URL of the TeamProjectTemplate repository>
    
Si se utiliza VSTS como plataforma de hospedaje de código, por lo general, la *dirección URL HTTPS del repositorio de plantillas de proyecto de equipo* es la siguiente:

 ***https://\<Nombre de servidor VSTS\>.visualstudio.com/\<Nombre del proyecto de equipo\>/_git/\<Nombre de repositorio de plantillas de proyecto de equipo\>***. 

En este ejemplo, tenemos:

***https://mysamplegroup.visualstudio.com/MyTeam/_git/MyTeamProjectTemplate***. 

![7](./media/project-lead-tasks/project-leads-7-clone-team-project-template.png)
            
**Linux**

    git clone <the SSH URL of the TeamProjectTemplate repository>
        
![8](./media/project-lead-tasks/project-leads-8-clone-team-project-template-linux.png)

Si se utiliza VSTS como plataforma de hospedaje de código, por lo general, la *dirección URL SSH del repositorio de plantillas de proyecto de equipo* es la siguiente:

***ssh://\<Nombre de servidor VSTS\>@\<Nombre de servidor VSTS\>.visualstudio.com:22/\<Nombre de proyecto de equipo>/_git/\<Nombre de repositorio de plantillas de proyecto de equipo\>.*** 

En este ejemplo, tenemos:

***ssh://mysamplegroup@mysamplegroup.visualstudio.com:22/MyTeam/_git/MyTeamProjectTemplate***. 

### <a name="clone-dsproject1-repository-r5-to-a-directory-d5-on-your-local-machine"></a>Clonación del repositorio de DSProject1 (R5) en un directorio (D5) en el equipo local

Cambie el directorio a **GitRepos** y ejecute el comando siguiente para clonar el repositorio del proyecto en la máquina local. 

**Windows**
            
    git clone <the HTTPS URL of the Project repository>

![9](./media/project-lead-tasks/project-leads-9-clone-project-repository.png)

Si se utiliza VSTS como plataforma de hospedaje de código, por lo general, la _dirección URL HTTPS del repositorio del proyecto_ es ***https://\<Nombre de servidor de VSTS\>.visualstudio.com/\<Nombre de proyecto de equipo>/_git/<Nombre de repositorio del proyecto\>***. En este ejemplo, tenemos ***https://mysamplegroup.visualstudio.com/MyTeam/_git/DSProject1***.

**Linux**

    git clone <the SSH URL of the Project repository>

![10](./media/project-lead-tasks/project-leads-10-clone-project-repository-linux.png)

Si se utiliza VSTS como plataforma de hospedaje de código, por lo general, la _dirección URL SSH del repositorio del proyecto_ es _ssh://<Nombre de servidor de VSTS\>@<Nombre de servidor de VSTS\>.visualstudio.com:22/<Your Team Project Name>/\_git/<Su nombre de repositorio del proyecto\>. En este ejemplo, tenemos ***ssh://mysamplegroup@mysamplegroup.visualstudio.com:22/MyTeam/_git/DSProject1***.

### <a name="copy-contents-of-d3-to-d5"></a>Copia del contenido de D3 a D5 

Ahora en el equipo local, tiene que copiar el contenido de _D3_ a _D5_, excepto los metadatos de git en el directorio .git. Los siguientes scripts realizarán el trabajo. Asegúrese de escribir las rutas de acceso correctas y completas para los directorios. La carpeta de origen es para el equipo (_D3_); la carpeta de destino es para el proyecto (_D5_).    

**Windows**
    
    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_win.ps1" -outfile "tdsp_local_copy_win.ps1"
    .\tdsp_local_copy_win.ps1 -role 3
    
![11](./media/project-lead-tasks/project-leads-11-local-copy-project-lead-new.png)

Ahora puede ver que en la carpeta _DSProject1_ se copian todos los archivos (excepto .git) en _MyTeamProjectTemplate_.

![12](./media/project-lead-tasks/project-leads-12-teamprojectTemplate_copied_to_local.png)

**Linux**
            
    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_linux.sh"
    bash tdsp_local_copy_linux.sh 3
        
![13](./media/project-lead-tasks/project-leads-13-local_copy_project_lead_linux_new.png)

Ahora puede ver que en la carpeta _DSProject1_ se copian todos los archivos (excepto los metadatos de .git) en _MyTeamProjectTemplate_.

![14](./media/project-lead-tasks/project-leads-14-teamprojectTemplate_copied_to_local_linux_new.png)


### <a name="customize-d5-if-you-need-to-optional"></a>Personalización de D5 si se necesita (opcional)

Si el proyecto necesita algunos directorios o documentos específicos distintos de los que se obtiene de la plantilla de proyecto de equipo (que se ha copiado al directorio D5 en el paso anterior), puede personalizar ahora el contenido de D5. 

### <a name="add-contents-of-dsproject1-in-d5-to-r5-on-your-group-vsts-server"></a>Adición de contenido de DSProject1 de D5 a R5 en el servidor de VSTS del grupo

Ahora tiene que insertar contenido de **_DSProject1_** al repositorio _R5_ en el proyecto de equipo en el servidor VSTS del grupo. 


- Cambie al directorio **D5**. 
- Use los siguientes comandos de git para agregar el contenido de **D5** a **R5**. Los comandos son los mismos para los sistemas Windows y Linux. 
    
    git status git add .
    inserción de GIT confirmación -m "inserción de win DSVM" git
    
- Confirme el cambio y la inserción. 

>[AZURE.NOTE] Si se trata de la primera vez que confirma un repositorio Git, debe configurar los parámetros globales *user.name* y *user.email* para poder ejecutar el comando `git commit`. Ejecute los dos comandos siguientes:
        
    git config --global user.name <your name>
    git config --global user.email <your email address>
 
> Si están confirmando varios repositorios de Git, use el mismo nombre y la misma dirección de correo electrónico en todos ellos. Usar el mismo nombre y la misma dirección de correo electrónico resulta muy cómodo más adelante al crear paneles de Power BI y realizar el seguimiento de las actividades de Git en varias bases de datos.

![15](./media/project-lead-tasks/project-leads-15-git-config-name.png)


## <a name="6-create-and-mount-azure-file-storage-as-project-resources-optional"></a>6. Creación y montaje del almacenamiento de archivos de Azure como recursos del proyecto (opcional)

Si desea crear el almacenamiento de archivos de Azure para compartir datos, como los datos sin formato del proyecto o las características que se generan para el proyecto, para que todos los miembros del proyecto tengan acceso a los mismos conjuntos de datos desde varias DSVM, siga las instrucciones de las secciones 3 y 4 de [Tareas del responsable de equipo en un equipo de ciencia de datos](team-lead-tasks.md). 


## <a name="next-steps"></a>Pasos siguientes

Estos son vínculos a descripciones más detalladas de los roles y tareas definidas por el proceso de ciencia de datos en equipo:

- [Tareas del administrador de grupo en un equipo de ciencia de datos](group-manager-tasks.md)
- [Tareas del responsable de equipo en un equipo de ciencia de datos](team-lead-tasks.md)
- [Tareas del responsable de proyecto en un equipo de ciencia de datos](project-lead-tasks.md)
- [Colaboradores individuales del proyecto en un equipo de ciencia de datos](project-ic-tasks.md)