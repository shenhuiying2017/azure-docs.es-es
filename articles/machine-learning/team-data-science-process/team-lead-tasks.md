---
title: 'Tareas del responsable de equipo del Proceso de ciencia de datos en equipo: Azure | Microsoft Docs'
description: Un esquema de las tareas de un responsable de equipo en un proyecto de ciencia de datos en equipo.
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
ms.openlocfilehash: 58e8bfeb547fbcd7f472fbed50073b1ca3176b72
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/14/2017
---
# <a name="team-lead-tasks"></a>Tareas del responsable de equipo

En este tema se describen las tareas que debe realizar un responsable de equipo en su equipo de ciencia de datos. El objetivo es establecer un entorno de colaboración de equipo que se normaliza en el [Proceso de ciencia de datos en equipo](overview.md) (TDSP). TDSP es una metodología de ciencia de datos ágil e iterativa para proporcionar soluciones de análisis predictivo y aplicaciones inteligentes de manera eficiente. Está diseñado para ayudar a mejorar la colaboración y el aprendizaje en equipo. El proceso es una extracción de los procedimientos recomendados y las estructuras, tanto de Microsoft como de la industria, que son necesarios para la correcta implementación de iniciativas de ciencia de datos con el fin de ayudar a las empresas a darse cuenta verdaderamente de las ventajas de sus programas de análisis. Puede encontrar un esquema de los roles personales y sus tareas asociadas que se controlan en un equipo de ciencia de datos que se estandariza en este proceso en [Roles y tareas del Proceso de ciencia de datos en equipo](roles-tasks.md).

Un **responsable de equipo** administra un equipo de la unidad de ciencia de datos de una empresa. Un equipo está formada por varios científicos de datos. En una unidad de ciencia de datos con un número reducido de científicos de datos, el **Administrador del grupo** y el **Responsable de equipo** podrían ser la misma persona o podrían delegar sus tareas a un suplente. Pero las tareas en sí no cambian. El flujo de trabajo de las tareas que van a realizar los responsables de equipo para configurar este entorno se representan en la siguiente figura:

![1](./media/team-lead-tasks/team-leads-1-creating-teams.png)

>[AZURE.NOTE] Las tareas de los bloques 1 y 2 de la ilustración son necesarias si se usa Visual Studio Team Services (VSTS) como plataforma de hospedaje del código y se quiere tener un proyecto de equipo aparte para su propio equipo. Una vez que se completan estas tareas, todos los repositorios de su equipo pueden crearse en este proyecto de equipo. 

Después de que el administrador del grupo haya satisfecho varias tareas que son requisito previo, hay cinco tareas principales (algunas opcionales) que realizará en este tutorial. Estas tareas corresponden a las principales secciones numeradas de este tema:

1. Crear un **proyecto de equipo** en el servidor VSTS del grupo y dos repositorios de equipo en el proyecto:
    - **Repositorio de ProjectTemplate** 
    - **Repositorio de TeamUtilities**
2. Propagar el repositorio **ProjectTemplate** del equipo desde el repositorio **GroupProjectTemplate** que ha configurado el administrador del grupo. 
3. Crear datos de equipo y recursos de análisis:
    - Agregar las utilidades específicas del equipo al repositorio **TeamUtilities**. 
    - (Opcional) Crear un almacenamiento en **Azure File Storage** que se usará para almacenar los recursos de datos que puedan ser útiles para todo el equipo. 
4. (Opcional) Montar el almacenamiento de Azure File Storage en la instancia de **Data Science Virtual Machine** (DSVM) del responsable de equipo y agregarle los recursos de datos.
5. Configurar el **control de seguridad** mediante la agregación de los miembros del equipo y configurar sus privilegios.

>[AZURE.NOTE] En las instrucciones siguientes se detallan los pasos necesarios para configurar un entorno de equipo de TDSP mediante VSTS. Se especifica cómo llevar a cabo estas tareas con VSTS porque así es como se implementa TDSP en Microsoft. Si se usa otra plataforma de hospedaje de código para el grupo, las tareas que debe realizar el responsable de equipo seguirán siendo las mismas. Pero la forma de completar estas tareas va a ser diferente.

## <a name="repositories-and-directories"></a>Repositorios y directorios

En este tema se usan abreviaturas de nombres de los repositorios y directorios. Estos nombres facilitan el seguimiento de las operaciones entre los repositorios y los directorios. Esta notación (**R** para repositorios de Git y **D** para directorios locales de su DSVM) se usa en las siguientes secciones:

- **R1**: el repositorio **GroupProjectTemplate** de Git que configura el administrador de grupo en el servidor de grupos de VSTS.
- **R3**: el repositorio **ProjectTemplate** del equipo de Git que configura el responsable de equipo.
- **R4**: el repositorio **TeamUtilities** de Git que configura el responsable de equipo.
- **D1**: el directorio local clonado de R1 y copiado en D3.
- **D3**: el directorio local clonado de R3, personalizado y copiado de nuevo en R3.
- **D4**: el directorio local clonados de R4, personalizado y copiado de nuevo en R4.

Los nombres especificados para los directorios y repositorios de este tutorial se han proporcionado basándose en el supuesto de que su objetivo es establecer un proyecto de equipo independiente para su propio equipo dentro de un grupo de ciencia de datos mayor. Sin embargo, como responsable de equipo, dispone de otras opciones:

- El grupo entero puede elegir crear un único proyecto de equipo. En este caso, todos los proyectos de los equipos de ciencia de datos se englobarían dentro de este único proyecto de equipo. Para conseguir esto, puede designar un administrador de Git para que siga estas instrucciones de crear un único proyecto de equipo. Este escenario podría ser válido en estos casos:
    -  Un grupo de ciencia de datos pequeño que no tiene varios equipos de ciencia de datos. 
    -  Un grupo de ciencia de datos grande con varios equipos de ciencia de datos que, sin embargo, desea optimizar la colaboración entre equipos con actividades como el planeamiento de sprint en el nivel de grupo. 
- Los equipos pueden elegir tener plantillas de proyecto específicas del equipo o utilidades específicas del equipo dentro de un único proyecto de equipo para el grupo entero. En este caso, los responsables de equipo deben crear repositorios de plantillas de proyecto de equipo o repositorios de utilidades de equipo dentro del mismo proyecto de equipo. Asigne a estos repositorios los nombres *<TeamName\>ProjectTemplate* y *<TeamName\>Utilities*, por ejemplo, *TeamJohnProjectTemplate* y *TeamJohnUtilities*. 

En cualquier caso, los responsables de equipo deben dejar que los miembros de su equipo sepan qué repositorios de plantillas y utilidades deben adoptar cuando vayan a configurar y clonar los repositorios de proyectos y utilidades. Los responsables de proyecto deben seguir las indicaciones del artículo [Tareas del responsable de equipo en un equipo de ciencia de datos](project-lead-tasks.md) para crear repositorios de proyectos, bien dentro de proyectos de equipo independientes o dentro de un único proyecto de equipo. 


## <a name="0-prerequisites"></a>0. Requisitos previos

Para cumplir los requisitos previos, es necesario realizar las tareas asignadas al administrador del grupo que se describen en [Tareas del administrador del grupo en un equipo de ciencia de datos](group-manager-tasks.md). Para resumir aquí, se deben cumplir los siguientes requisitos antes de comenzar las tareas del responsable de equipo: 

- El administrador del grupo debe haber configurado el **servidor VSTS del grupo** (o la cuenta de grupo en otras plataformas de hospedaje de código).
- El administrador del grupo debe haber configurado el **repositorio GroupProjectTemplate** (R1) en la cuenta de grupo en la plataforma de hospedaje de código que planee usar.
- Tiene **autorización** en la cuenta de grupo para crear repositorios para su equipo.
- Git debe estar instalado en la máquina. Si usa una instancia de Data Science Virtual Machine (DSVM), Git se ha instalado previamente y está listo para continuar. En caso contrario, consulte el [apéndice de plataformas y herramientas](platforms-and-tools.md#appendix).  
- Si usa una **DSVM de Windows**, debe tener [Git Credential Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) instalado en su máquina. En el archivo README.md, desplácese a la sección **Descargar e instalar** y haga clic en el *instalador más reciente*. Este paso le lleva a la página más reciente del instalador. Descargue al instalador .exe desde aquí y ejecútelo. 
- Si usa una instancia de **DSVM de Linux**, cree una clave pública SSH en su DSVM y agréguela al servidor de VSTS del grupo. Para más información acerca de SSH, consulte la sección acerca de cómo **crear una clave pública SSH** en el [apéndice de plataformas y herramientas](platforms-and-tools.md#appendix). 
    
## <a name="1-create-a-team-project-and-repositories"></a>1. Creación de un proyecto de equipo y de los repositorios

Realice este paso si va a usar VSTS como plataforma de hospedaje de código para la colaboración y el control de versiones. En esta sección tiene que crear tres artefactos en el servidor VSTS de su grupo:

- Proyecto **MyTeam** en VSTS
- Repositorio **MyProjectTemplate** (**R3**) en Git
- Repositorio **MyTeamUtilities** (**R4**) en Git

### <a name="create-the-myteam-project"></a>Creación del proyecto MyTeam

- Vaya a la página principal del servidor VSTS de su grupo en la dirección URL `https://<VSTS Server Name\>.visualstudio.com`. 
- Haga clic en **Nuevo** para crear un proyecto de equipo. 

    ![2](./media/team-lead-tasks/team-leads-2-create-new-team.png)

- Una ventana Crear proyecto de equipo le pide que especifique el nombre del proyecto (**MyTeam** en este ejemplo). Asegúrese de que selecciona **Agile** como la **plantilla de proceso** y **Git** como el **control de versiones**. 

    ![3](./media/team-lead-tasks/team-leads-3-create-new-team-2.png)

- Haga clic en **Crear proyecto**. El proyecto de equipo **MyTeam** se crea en menos de un minuto. 

- Después de que el proyecto de equipo **MyTeam** se ha creado, haga clic en el botón **Navegar al proyecto**, para ir a la página principal del proyecto de equipo. 

    ![4](./media/team-lead-tasks/team-leads-4-create-new-team-3.png)

- Si ve una ventana emergente **Enhorabuena** , haga clic en el botón **Agregar código** (en el cuadro rojo). En caso contrario, haga clic en **Código** (en el cuadro amarillo). Se dirigirá a la página del repositorio de Git del proyecto de equipo. 

    ![5](./media/team-lead-tasks/team-leads-5-team-project-home.png)

### <a name="create-the-myprojecttemplate-repository-r3-on-git"></a>Creación del repositorio MyProjectTemplate (R3) en Git

- En la página del repositorio de Git del proyecto de equipo, haga clic en la flecha de descarga junto al nombre del repositorio **MyTeam** y seleccione **Administrar repositorios...**

    ![6](./media/team-lead-tasks/team-leads-6-rename-team-project-repo.png)

- En la pestaña **Control de versiones** del panel de control del proyecto de equipo, haga clic en **MyTeam** y seleccione **Cambiar nombre de repositorio...**. 

    ![7](./media/team-lead-tasks/team-leads-7-rename-team-project-repo-2.png)

- Especifique un nombre nuevo para el repositorio en la ventana **Cambiar el nombre del repositorio MyTeam**. En este ejemplo, *MyTeamProjectTemplate*. Puede elegir algo parecido a *<su nombre de equipo\>ProjectTemplate*. Haga clic en **Cambiar nombre** para continuar.

    ![8](./media/team-lead-tasks/team-leads-8-rename-team-project-repo-3.png)

### <a name="create-the-myteamutilities-repository-r4-on-git"></a>Creación del repositorio MyTeamUtilities (R4) en Git

- Para crear un nuevo repositorio *<su nombre de equipo\>Utilities* en el proyecto de equipo, haga clic en **Nuevo repositorio...**  en la pestaña **Control de versiones** del panel de control del proyecto de equipo.  

    ![9](./media/team-lead-tasks/team-leads-9-create-team-utilities.png)

- En la ventana **Crear nuevo repositorio** que aparece, proporcione un nombre para este repositorio. En este ejemplo, se llamará *MyTeamUtilities*, que en nuestra notación es **R4**. Elija algo parecido a *<su nombre de equipo\>Utilities*. Asegúrese de que selecciona **Git** como **Tipo**. A continuación, haga clic en **Crear** para continuar.

    ![10](./media/team-lead-tasks/team-leads-10-create-team-utilities-2.png)

- Confirme que los dos nuevos repositorios de Git se han creado en el proyecto de equipo **MyTeam**. En este ejemplo: 

- **MyTeamProjectTemplate** (R3) 
- **MyTeamUtilities** (R4).

    ![11](./media/team-lead-tasks/team-leads-11-two-repo-in-team.png)


## <a name="2-seed-your-team-projecttemplate-and-teamutilities-repositories"></a>2. Propagación de los repositorios ProjectTemplate y TeamUtilities del equipo

El procedimiento de propagación usa los directorios de su DSVM local como sitios de ensayo intermedios. Si necesita personalizar sus repositorios **ProjectTemplate** y **TeamUtilities** para satisfacer algunas necesidades específicas del equipo, lo hará en el penúltimo paso del procedimiento siguiente. Este es un resumen de los pasos que se usan para propagar el contenido de los repositorios **MyTeamProjectTemplate** y **MyTeamUtilities** para un equipo de ciencia de datos. Los pasos individuales corresponden a las subsecciones del procedimiento de propagación:

- Clone el repositorio de grupo en el directorio local: R1 de equipo - clonado en -> D1 local.
- Clone los repositorios de equipo en los directorios locales: R3 y R4 de equipo - clonados en -> D3 y D4 locales.
- Copie el contenido de la plantilla de proyecto de grupo en la carpeta de equipo local: D1 - contenido copiado en -> D3.
- (Opcional) Personalización de D3 y D4 locales
- Inserte el contenido del directorio local en los repositorios de equipo: D3 y D4 - el contenido se agrega a -> R3 y R4 de equipo.


### <a name="initialize-the-team-repositories"></a>Propagación de los repositorios de equipo

En este paso, se propagará el repositorio de plantillas de proyecto de equipo desde el repositorio de plantillas de proyecto de grupo:

- Repositorio **MyTeamProjectTemplate** (**R3**) desde el repositorio **GroupProjectTemplate** (**R1**)


### <a name="clone-group-repositories-into-local-directories"></a>Clonación de repositorios de grupo en directorios locales

Para comenzar este procedimiento:

- Cree directorios en la máquina local:
    - En **Windows**: **C:\GitRepos\GroupCommon** y **C:\GitRepos\MyTeam**.
    - En **Linux**: **GitRepos\GroupCommon** y **GitRepos\MyTeam** en el directorio principal. 
- Cambie al directorio **GitRepos\GroupCommon**.
- Ejecute el comando siguiente, según corresponda, en el sistema operativo de la máquina local.

**Windows**

    git clone https://<Your VSTS Server name>.visualstudio.com/GroupCommon/_git/GroupProjectTemplate
    

![12](./media/team-lead-tasks/team-leads-12-create-two-group-repos.png)

**Linux**
    
    git clone ssh://<Your VSTS Server name>@<Your VSTS Server name>.visualstudio.com:22/GroupCommon/_git/GroupProjectTemplate
    
    
![13](./media/team-lead-tasks/team-leads-13-clone_two_group_repos_linux.png)

Estos comandos clonan el repositorio **GroupProjectTemplate** (R1) del servidor VSTS del grupo en el directorio local de **GitRepos\GroupCommon** de la máquina local. Después de la clonación, se crea el directorio **GroupProjectTemplate** (D1) en el directorio **GitRepos\GroupCommon**. Aquí, se supone que el administrador del grupo ha creado un proyecto de equipo **GroupCommon** y que el repositorio **GroupProjectTemplate** está en este proyecto de equipo. 


### <a name="clone-your-team-repositories-into-local-directories"></a>Clonación de los repositorios de equipo en directorios locales

Estos comandos clonan los repositorios **MyTeamProjectTemplate** (R3) y **MyTeamUtilities** (R4) que están en el proyecto de equipo **MyTeam** del servidor VSTS del grupo en los directorios **MyTeamProjectTemplate** (D3) y **MyTeamUtilities** (D4) de **GitRepos\MyTeam** de la máquina local. 

- Cambie al directorio **GitRepos\MyTeam**.
- Ejecute los comandos siguientes, según corresponda, en el sistema operativo de la máquina local. 

**Windows**

    git clone https://<Your VSTS Server name>.visualstudio.com/<Your Team Name>/_git/MyTeamProjectTemplate
    git clone https://<Your VSTS Server name>.visualstudio.com/<Your Team Name>/_git/MyTeamUtilities

![14](./media/team-lead-tasks/team-leads-14-clone_two_empty_team_repos.png)
        
**Linux**
    
    git clone ssh://<Your VSTS Server name>@<Your VSTS Server name>.visualstudio.com:22/<Your Team Name>/_git/MyTeamProjectTemplate
    git clone ssh://<Your VSTS Server name>@<Your VSTS Server name>.visualstudio.com:22/<Your Team Name>/_git/MyTeamUtilities
    
![15](./media/team-lead-tasks/team-leads-15-clone_two_empty_team_repos_linux.png)

Después de la clonación, se crean dos directorios **MyTeamProjectTemplate** (D3) y **MyTeamUtilities** (D4) en el directorio **GitRepos\MyTeam**. Aquí se ha dado por supuesto que ha asignado un nombre a sus repositorios de plantillas de proyecto y utilidades de equipo **MyTeamProjectTemplate** y **MyTeamUtilities**. 

### <a name="copy-the-group-project-template-content-to-the-local-team-project-template-directory"></a>Copie el contenido de la plantilla de proyecto de grupo en el directorio de la plantilla de proyecto de equipo local.

Para copiar el contenido de la carpeta **GroupProjectTemplate** (D1) local en la carpeta **MyTeamProjectTemplate** (D3) local, ejecute uno de los siguientes scripts de shell: 

####<a name="from-the-powershell-command-line-for-windows"></a>En la línea de comandos de PowerShell para Windows:        

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_win.ps1" -outfile "tdsp_local_copy_win.ps1"
    .\tdsp_local_copy_win.ps1 2

    
![16](./media/team-lead-tasks/team-leads-16-local_copy_team_lead_new.png)

####<a name="from-the-linux-shell-for-the-linux-dsvm"></a>En el shell de Linux para la **DSVM de Linux**
    
    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_linux.sh"
    bash tdsp_local_copy_linux.sh 2
    
![17](./media/team-lead-tasks/team-leads-17-local-copy-team-lead-linux-new.png)

Los scripts excluyen el contenido del directorio .git. Los scripts le piden que proporcione las **rutas de acceso completas** al directorio de origen D1 y al directorio de destino D3.
        

### <a name="customize-your-team-project-template-or-team-utilities-optional"></a>Personalización de la plantilla de proyecto de equipo o de las utilidades de equipo (opcional)

En esta fase del proceso de configuración, puede personalizar los directorios **MyTeamProjectTemplate** (D3) y **MyTeamUtilities** (D4), si lo considera necesario. 

- Si quiere personalizar el contenido de D3 de forma que se ajuste a las necesidades específicas de su equipo, puede modificar los documentos de plantilla o cambiar la estructura de directorio.

- Si el equipo ha desarrollado algunas utilidades que quiere compartir con todo el equipo, cópielas y péguelas en el directorio D4. 


### <a name="push-local-directory-content-to-team-repositories"></a>Inserción de contenido del directorio local en los repositorios de equipo

Para agregar el contenido de los directorios locales (opcionalmente personalizados) D3 y D4 a los repositorios de equipo R3 y R4, ejecute los siguientes comandos de Bash de Git desde una consola de Windows PowerShell o desde un shell de Linux. Ejecute los comandos desde el directorio **GitRepos\MyTeam\MyTeamProjectTemplate**.

    git status
    git add .
    git commit -m"push from DSVM"
    git push
    
![18](./media/team-lead-tasks/team-leads-18-push-to-group-server-2.png)

Los archivos del repositorio MyTeamProjectTemplate del servidor de VSTS del grupo se sincronizan casi al instante cuando se ejecute este script.

![19](./media/team-lead-tasks/team-leads-19-push-to-group-server-showed-up.png)

Ejecute ahora el mismo conjunto de cuatro comandos de Git desde el directorio **GitRepos\MyTeam\MyTeamUtilities**. 

> [AZURE.NOTE]Si se trata de la primera vez que confirma un repositorio de Git, debe configurar los parámetros globales *user.name* y *user.email* para poder ejecutar el comando `git commit`. Ejecute los siguientes dos comandos:
        
    git config --global user.name <your name>
    git config --global user.email <your email address>
 
> Si está confirmando varios repositorios de Git, use el mismo nombre y la misma dirección de correo electrónico al confirmar cada uno de ellos. Usar el mismo nombre y la misma dirección de correo electrónico resulta muy cómodo más adelante al crear paneles de Power BI y realizar el seguimiento de las actividades de Git en varias bases de datos.

![20](./media/team-lead-tasks/team-leads-20-git-config-name.png)


## <a name="3-create-team-data-and-analytics-resources-optional"></a>3. Creación de datos y recursos de análisis de equipo (opcional)

El uso compartido de datos y recursos de análisis con su equipo entero presenta ventajas en cuanto a costo y rendimiento: los miembros del equipo pueden ejecutar sus proyectos en los recursos compartidos, ahorrar en los presupuestos y colaborar de manera más eficiente. En esta sección se proporcionan instrucciones sobre cómo crear almacenamiento de Azure File Storage. En la siguiente sección se proporcionan instrucciones sobre cómo montar almacenamiento de Azure File Storage en la máquina local. Para más información sobre el uso compartido de otros recursos, como instancias de Data Science Virtual Machine de Azure o clústeres de Azure HDInsight Spark, consulte [Plataformas y herramientas](platforms-and-tools.md). En este tema se proporcionan instrucciones desde una perspectiva de ciencia de datos sobre la selección de recursos que son adecuados para sus necesidades, junto con vínculos a páginas de productos y otros tutoriales interesantes y útiles que se han publicado.

>[AZURE.NOTE] Para evitar la transmisión de datos entre centros de datos, lo que podría ser lento y costoso, asegúrese de que el grupo de recursos, la cuenta de almacenamiento y la máquina virtual de Azure (por ejemplo, DSVM) estén en el mismo centro de datos de Azure. 

Ejecute los siguientes scripts para crear almacenamiento de Azure File Storage para su equipo. Este almacenamiento se puede usar para almacenar recursos de datos que son útiles para el equipo entero. Los scripts le piden información de su cuenta y suscripción de Azure, así que tenga a mano estas credenciales para escribirlas. 

### <a name="create-azure-file-storage-with-powershell-from-windows"></a>Creación de almacenamiento de Azure File Storage con PowerShell desde Windows

Ejecute este script desde la línea de comandos de PowerShell:

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/CreateFileShare.ps1" -outfile "CreateFileShare.ps1"
    .\CreateFileShare.ps1

![21](./media/team-lead-tasks/team-leads-21-create-fileshare-win.png)   

Cuando se le pida, inicie sesión en su cuenta de Microsoft Azure:

![22](./media/team-lead-tasks/team-leads-22-file-create-s1.png)

Seleccione la suscripción de Azure que quiere usar:

![23](./media/team-lead-tasks/team-leads-23-file-create-s2.png)

Seleccione la cuenta de almacenamiento que usará o cree una nueva en la suscripción seleccionada:

![24](./media/team-lead-tasks/team-leads-24-file-create-s3.png)

Escriba el nombre del almacenamiento de Azure File Storage que se va a crear. Solo se aceptan caracteres en minúscula, números y guiones (-):

![25](./media/team-lead-tasks/team-leads-25-file-create-s4.png)

Para facilitar el montaje y el uso compartido de este almacenamiento después de crearlo, guarde la información de Azure File Storage en un archivo de texto y anote la ruta de acceso a su ubicación. En concreto, necesita este archivo para montar el almacenamiento de Azure File Storage en las máquinas virtuales de Azure de la sección siguiente. 

Es una buena práctica insertar este archivo de texto en el repositorio ProjectTemplate del equipo. Se recomienda colocarlo en el directorio **Docs\DataDictionaries**. Por lo tanto, todos los proyectos de su equipo pueden acceder a este recurso de datos. 

![26](./media/team-lead-tasks/team-leads-26-file-create-s5.png)


### <a name="create-azure-file-storage-with-a-linux-script"></a>Creación de almacenamiento de Azure File Storage con un script de Linux

Ejecute este script desde el shell de Linux:

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/CreateFileShare.sh"
    bash CreateFileShare.sh

Inicie sesión en su cuenta de Microsoft Azure siguiendo las instrucciones que aparecen en esta pantalla:

![27](./media/team-lead-tasks/team-leads-27-file-create-linux-s1.png)

Seleccione la suscripción de Azure que quiere usar:

![28](./media/team-lead-tasks/team-leads-28-file-create-linux-s2.png)

Seleccione la cuenta de almacenamiento que usará o cree una nueva en la suscripción seleccionada:

![29](./media/team-lead-tasks/team-leads-29-file-create-linux-s3.png)

Escriba el nombre del almacenamiento de Azure File Storage que quiere crear; solo se aceptan caracteres en minúscula números y guiones (-):

![30](./media/team-lead-tasks/team-leads-30-file-create-linux-s4.png)

Para facilitar el acceso a este almacenamiento después de crearlo, guarde la información de Azure File Storage en un archivo de texto y anote la ruta de acceso a su ubicación. En concreto, necesita este archivo para montar el almacenamiento de Azure File Storage en las máquinas virtuales de Azure de la sección siguiente.

Es una buena práctica insertar este archivo de texto en el repositorio ProjectTemplate del equipo. Se recomienda colocarlo en el directorio **Docs\DataDictionaries**. Por lo tanto, todos los proyectos de su equipo pueden acceder a este recurso de datos. 

![31](./media/team-lead-tasks/team-leads-31-file-create-linux-s5.png)


## <a name="4-mount-azure-file-storage-optional"></a>4. Montaje del almacenamiento de Azure File Storage (opcional)

Después de que se ha creado correctamente el almacenamiento de Azure File Storage, se puede montar en la máquina local mediante uno de los siguientes scripts de PowerShell o Linux.

### <a name="mount-azure-file-storage-with-powershell-from-windows"></a>Montaje del almacenamiento de Azure File Storage con PowerShell desde Windows

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/AttachFileShare.ps1" -outfile "AttachFileShare.ps1"
    .\AttachFileShare.ps1
    
Primero se le pedirá que inicie sesión, si aún no lo ha hecho. 

Haga clic en **Entrar** o en **S** para continuar cuando se le pregunte si tiene un archivo de información de Azure File Storage y luego especifique el ***nombre y la ruta de acceso completa** del archivo que creó en el paso anterior. La información para montar un almacenamiento de Azure File Storage se lee directamente de ese archivo y ya está listo para ir al paso siguiente.

![32](./media/team-lead-tasks/team-leads-32-attach-s1.png)

> [AZURE.NOTE] Si no tiene un archivo que contenga la información de Azure File Storage, al final de esta sección se proporcionan los pasos para escribir la información con el teclado.

A continuación, se le pide que escriba el nombre de la unidad que se va a agregar a la máquina virtual. En la pantalla se imprime una lista de nombres de unidad existente. Debe proporcionar un nombre de unidad que no exista en la lista.

![33](./media/team-lead-tasks/team-leads-33-attach-s2.png)

Confirme que se ha montado correctamente una nueva unidad F en la máquina.

![34](./media/team-lead-tasks/team-leads-34-attach-s3.png)

**Cómo especificar manualmente la información de Azure File Storage:** si no tiene su información de Azure File Storage en un archivo de texto, puede seguir las instrucciones de la siguiente pantalla para escribir la suscripción necesaria, la cuenta de almacenamiento y la información de Azure File Storage:

![35](./media/team-lead-tasks/team-leads-35-attach-s4.png)

Escriba el nombre de la suscripción de Azure, seleccione la cuenta de almacenamiento donde se crea el almacenamiento de Azure File Storage y escriba el nombre de dicho almacenamiento:

![36](./media/team-lead-tasks/team-leads-36-attach-s5.png)

### <a name="mount-azure-file-storage-with-a-linux-script"></a>Montaje del almacenamiento de Azure File Storage con un script de Linux

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/AttachFileShare.sh"
    bash AttachFileShare.sh

![37](./media/team-lead-tasks/team-leads-37-attach-s1-linux.png)

Primero se le pedirá que inicie sesión, si aún no lo ha hecho. 

Haga clic en **Entrar** o en **S** para continuar cuando se le pregunte si tiene un archivo de información de Azure File Storage y luego especifique el ***nombre y la ruta de acceso completa** del archivo que creó en el paso anterior. La información para montar un almacenamiento de Azure File Storage se lee directamente de ese archivo y ya está listo para ir al paso siguiente.

![38](./media/team-lead-tasks/team-leads-38-attach-s2-linux.png)

A continuación, se le pide que escriba el nombre de la unidad que se va a agregar a la máquina virtual. En la pantalla se imprime una lista de nombres de unidad existente. Debe proporcionar un nombre de unidad que no exista en la lista.

![39](./media/team-lead-tasks/team-leads-39-attach-s3-linux.png)

Confirme que se ha montado correctamente una nueva unidad F en la máquina.

![40](./media/team-lead-tasks/team-leads-40-attach-s4-linux.png)

**Cómo especificar manualmente la información de Azure File Storage:** si no tiene su información de Azure File Storage en un archivo de texto, puede seguir las instrucciones de la siguiente pantalla para escribir la suscripción necesaria, la cuenta de almacenamiento y la información de Azure File Storage:

- Escriba **n**.
- Seleccione el índice del nombre de la suscripción donde se creó el almacenamiento de Azure File Storage en el paso anterior:

    ![41](./media/team-lead-tasks/team-leads-41-attach-s5-linux.png)

- Seleccione la cuenta de almacenamiento de la suscripción y escriba el nombre del almacenamiento de Azure File Storage:

    ![42](./media/team-lead-tasks/team-leads-42-attach-s6-linux.png)

- Escriba el nombre de la unidad que se va a agregar a la máquina, que debe ser distinto de los ya existentes:

    ![43](./media/team-lead-tasks/team-leads-43-attach-s7-linux.png)


## <a name="5-set-up-security-control-policy"></a>5. Configuración de la directiva de control de seguridad 

En la página principal del servidor de VSTS de su grupo, haga clic en el **icono de engranaje** situado junto al nombre de usuario en la esquina superior derecha y, después, seleccione la pestaña **Seguridad**. Puede agregar aquí miembros al equipo con diferentes permisos.

![44](./media/team-lead-tasks/team-leads-44-add-team-members.png)

## <a name="next-steps"></a>Pasos siguientes

Estos son vínculos a descripciones más detalladas de los roles y tareas definidas por el proceso de ciencia de datos en equipo:

- [Tareas del administrador de grupo en un equipo de ciencia de datos](group-manager-tasks.md)
- [Tareas del responsable de equipo en un equipo de ciencia de datos](team-lead-tasks.md)
- [Tareas del responsable de proyecto en un equipo de ciencia de datos](project-lead-tasks.md)
- [Colaboradores individuales del proyecto en un equipo de ciencia de datos](project-ic-tasks.md)