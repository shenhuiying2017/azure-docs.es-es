---
title: 'Tareas del administrador de grupo del proceso de ciencia de datos en equipo: Azure | Microsoft Docs'
description: Esquema de las tareas de un administrador de grupo en un proyecto en equipo de ciencia de datos.
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
ms.openlocfilehash: 58cea8b0288469a76dd8c4eb967caa8e87cd3dd7
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/14/2017
---
# <a name="group-manager-tasks"></a>Tareas del administrador de grupo

En este tema se describen las tareas que se espera que un administrador de grupo realice para la organización de ciencia de datos. El objetivo es establecer un entorno de colaboración en grupo que normalice el [proceso de ciencia de datos en equipos](overview.md) (TDSP). Para obtener un esquema de los roles personales y sus tareas asociadas que se controlan en un equipo de ciencia de datos que se estandariza en este proceso, consulte [Roles y tareas del proceso de ciencia de datos en equipo](roles-tasks.md).

El **Administrador de grupo** es el administrador de la unidad de ciencia de datos completa en una empresa. Una unidad de ciencia de datos puede tener varios equipos, cada uno de ellos trabajando en varios proyectos de ciencia de datos en segmentos verticales de negocio distintos. Un Administrador de grupo puede delegar sus tareas en un suplente, pero las tareas asociadas al rol no cambian. En el siguiente diagrama se muestran las seis tareas principales:

![0](./media/group-manager-tasks/tdsp-group-manager.png)


>[AZURE.NOTE] En las instrucciones siguientes se detallan los pasos necesarios para configurar un entorno de grupo de TDSP mediante VSTS. También se especifica cómo llevar a cabo estas tareas con VSTS, ya que es cómo se implementa TDSP en Microsoft. Si se utiliza otra plataforma de hospedaje de código para el grupo, las tareas que debe realizar el Administrador de grupo seguirán siendo las mismas. Sin embargo, la forma de completar estas tareas sí que será diferente.

1. Configure el **servidor de Visual Studio Team Services (VSTS)** para el grupo.
2. Cree un **proyecto de equipo de grupo** en el servidor de Visual Studio Team Services (para los usuarios de VSTS)
3. Cree el repositorio de **GroupProjectTemplate**
4. Cree el repositorio de **GroupUtilities**
5. Propague los repositorios **GroupProjectTemplate** y **GroupUtilities** al servidor de VSTS con contenido de los repositorios de TDSP.
6. Configure los **controles de seguridad** para que los miembros del equipo puedan tener acceso a los repositorios de GroupProjectTemplate y GroupUtilities.

A continuación se describe cada uno de los pasos anteriores. Pero, en primer lugar, queremos que se familiarice con las abreviaturas y analice los requisitos previos para trabajar con los repositorios.

### <a name="abbreviations-for-repositories-and-directories"></a>Abreviaturas de los repositorios y directorios

En este tutorial se usan abreviaturas de los nombres de repositorios y directorios. Estas definiciones facilitan el seguimiento de las operaciones entre los repositorios y los directorios. Esta notación se usa en las secciones siguientes:

- **G1**: repositorio de la plantilla del proyecto que desarrolla y administra el equipo de TDSP de Microsoft.
- **G2**: repositorio de utilidades que desarrolla y administra el equipo de TDSP de Microsoft.
- **R1**: repositorio GroupProjectTemplate de Git que configuró en el servidor de VSTS del grupo.
- **R2**: repositorio GroupUtilities de Git que configuró en el servidor de VSTS del grupo.
- **LG1** y **LG2**: directorios locales de su equipo en los que clona, respectivamente, G1 y G2.
- **LR1** y **LR2**: directorios locales de su equipo en los que clona, respectivamente,R1 y R2.

### <a name="pre-requisites-for-cloning-repositories-and-checking-code-in-and-out"></a>Requisitos previos para la clonación de repositorios y la inserción y eliminación de código
 
- Git debe estar instalado en la máquina. Si usa una instancia de Data Science Virtual Machine (DSVM), Git se ha instalado previamente y está listo para continuar. En caso contrario, consulte el [apéndice de plataformas y herramientas](platforms-and-tools.md#appendix).  
- Si usa una **DSVM de Windows**, debe tener [Git Credential Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) instalado en su máquina. En el archivo README.md, desplácese a la sección **Descargar e instalar** y haga clic en el *instalador más reciente*. Este paso le lleva a la página más reciente del instalador. Descargue el instalador .exe desde aquí y ejecútelo. 
- Si usa una instancia de **DSVM de Linux**, cree una clave pública SSH en su DSVM y agréguela al servidor de VSTS del grupo. Para más información acerca de SSH, consulte la sección acerca de cómo **crear una clave pública SSH** en el [apéndice de plataformas y herramientas](platforms-and-tools.md#appendix). 


## <a name="1-create-account-on-vsts-server"></a>1. Crear una cuenta en el servidor de VSTS

El servidor de VSTS hospeda los repositorios siguientes:

- **repositorios comunes del grupo**: repositorios de uso general que pueden adoptar varios equipos dentro de un grupo para diferentes proyectos de ciencia de datos. Por ejemplo, los repositorios *GroupProjectTemplate* y *GroupUtilities*.
- **repositorios de equipo**: repositorios para equipos específicos de un grupo. Estos repositorios son específicos para las necesidades de un equipo, y pueden ser adoptados por múltiples proyectos ejecutados por ese equipo, pero no son lo suficientemente generales como para ser útiles para diferentes equipos dentro de un grupo de ciencia de datos. 
- **repositorios de proyecto**: repositorios disponibles para determinados proyectos. Dichos repositorios pueden no ser lo suficientemente generales como para ser útiles para los diferentes proyectos que realiza un equipo, ni para los diferentes equipos de un grupo de ciencia de datos.


### <a name="setting-up-the-vsts-server-sign-into-your-microsoft-account"></a>Configuración del inicio de sesión del servidor de VSTS en su cuenta de Microsoft
    
Vaya a [Visual Studio Online](https://www.visualstudio.com/), haga clic en **Iniciar sesión** en la esquina superior derecha e inicie sesión en su cuenta de Microsoft. 
    
![1](./media/group-manager-tasks/login.PNG)

Si no tiene una cuenta de Microsoft, haga clic en **Registrarse ahora** para crear una cuenta de Microsoft y, después, inicie sesión con esa cuenta. 

Si su organización tiene una suscripción de Visual Studio o MSDN, haga clic en el cuadro verde **Iniciar sesión con una cuenta profesional o educativa** e inicie sesión con las credenciales asociadas a esta suscripción. 
        
![2](./media/group-manager-tasks/signin.PNG)


        
Después de iniciar sesión, haga clic en **Crear nueva cuenta** en la esquina superior derecha tal como se muestra en la siguiente imagen:
        
![3](./media/group-manager-tasks/create-account-1.PNG)
        
Rellene la información del servidor de VSTS que quiere crear en el asistente para **crear su cuenta** con los valores siguientes: 

- **Dirección URL del servidor**: reemplace *mysamplegroup* con su propio *nombre de servidor*. La dirección URL del servidor será: *https://\<servername\>.visualstudio.com* 
- **Administrar código con:** seleccione  **_Git_**.
- **Nombre del proyecto:** escriba *GroupCommon*. 
- **Organizar el trabajo usando:** elija *Agile*.
- **Hospedar sus proyectos en:** elija una ubicación geográfica. En este ejemplo, hemos elegido *Centro y Sur de EE. UU.* 
        
![4](./media/group-manager-tasks/fill-in-account-information.png)

>[AZURE.NOTE] Si ve la ventana emergente siguiente después de hacer clic en **Crear nueva cuenta**, es necesario que haga clic en **Cambiar detalles** para mostrar todos los campos detallados.

![5](./media/group-manager-tasks/create-account-2.png)


Haga clic en **Continue**. 

## <a name="2-groupcommon-team-project"></a>2. Proyecto de equipo de GroupCommon

La página **GroupCommon** (*https://\<servername\>.visualstudio.com/GroupCommon*) se abre una vez que se crea el servidor de VSTS.
                            
![6](./media/group-manager-tasks/server-created-2.PNG)

## <a name="3-create-the-grouputilities-r2-repository"></a>3. Crear el repositorio GroupUtilities (R2)

Para crear el repositorio **GroupUtilities** (R2) en el servidor de VSTS:

- Para abrir el asistente para **crear un nuevo repositorio**, haga clic en **Nuevo repositorio** en la pestaña **Control de versiones** del proyecto de equipo. 

![7](./media/group-manager-tasks/create-grouputilities-repo-1.png) 

- Seleccione *Git* como el **Tipo**, escriba *GroupUtilities* como el **Nombre** y, después, haga clic en **Crear**. 

![8](./media/group-manager-tasks/create-grouputilities-repo-2.png)
                
Ahora debería ver dos repositorios de Git, **GroupProjectTemplate** y **GroupUtilities**, en la columna izquierda de la página **Control de versiones**: 

![9](./media/group-manager-tasks/two-repo-under-groupCommon.PNG)


## <a name="4-create-the-groupprojecttemplate-r1-repository"></a>4. Crear el repositorio GroupProjectTemplate (R1)

El programa de instalación de los repositorios del servidor de VSTS del grupo consta de dos tareas:

- Cambiar el nombre del repositorio predeterminado **GroupCommon** a ***GroupProjectTemplate***.
- Crear el repositorio **GroupUtilities** en el servidor de VSTS en el proyecto de equipo **GroupCommon**. 

Las instrucciones para la primera tarea se incluyen en esta sección después de los comentarios acerca de las convenciones de los nombres para los repositorios y directorios. Las instrucciones para la segunda tarea se encuentran en la sección siguiente para el paso 4.

### <a name="rename-the-default-groupcommon-repository"></a>Cambiar el nombre del repositorio GroupCommon predeterminado

Para cambiar el nombre predeterminado del repositorio **GroupCommon** por *GroupProjectTemplate* (denominado **R1** en este tutorial):
    
- Haga clic en **Colaborar en el código** en la página **GroupCommon** del proyecto del equipo. Esto le llevará a la página predeterminada del repositorio de Git del proyecto del equipo **GroupCommon**. Actualmente, este repositorio de Git está vacío. 

![10](./media/group-manager-tasks/rename-groupcommon-repo-3.png)
        
- Haga clic en **GroupCommon** en la esquina superior izquierda (resaltado con un cuadro rojo en la ilustración siguiente) en la página del repositorio de Git de **GroupCommon** y seleccione **Administrar repositorios** (resaltado con un cuadro de color verde en la ilustración siguiente). Este procedimiento abrirá el **PANEL DE CONTROL**. 
- Seleccione la pestaña **Control de versiones** del proyecto de equipo. 

![11](./media/group-manager-tasks/rename-groupcommon-repo-4.png)

- Haga clic en **...** a la derecha del repositorio **GroupCommon** en el panel izquierdo y seleccione **Cambiar nombre de repositorio**. 

![12](./media/group-manager-tasks/rename-groupcommon-repo-5.png)
        
- En el asistente para **cambiar el nombre del repositorio GroupCommon** que aparece, escriba *GroupProjectTemplate* en el cuadro **Nombre del repositorio** y, después, haga clic en **Cambiar nombre** . 

![13](./media/group-manager-tasks/rename-groupcommon-repo-6.png)



## <a name="5-seed-the-r1--r2-repositories-on-the-vsts-server"></a>5. Propagar los repositorios R1 y R2 en el servidor de VSTS

En esta fase del procedimiento, propague los repositorios *GroupProjectTemplate* (R1) y *GroupUtilities* (R2) que configuró en la sección anterior. Estos repositorios se propagarán con los repositorios ***ProjectTemplate*** (**G1**) y ***Utilities*** (**G2**) que administra Microsoft para el proceso de ciencia de datos de equipo. Cuando se complete esta propagación:

- el repositorio R1 va a tener el mismo conjunto de directorios y de plantillas de documentos que el repositorio G1
- el repositorio R2 va a contener el conjunto de utilidades de ciencia de datos que desarrolló Microsoft.

El procedimiento de propagación usa los directorios de su DSVM local como sitios de ensayo intermedios. Estos son los pasos que se realizan en esta sección:

- G1 y G2 se clonan en LG1 y LG2
- R1 y R2 se clonan en LR1 y LR2
- Los archivos de LG1 y LG2 se copian en LR1 y LR2
- (Opcional) personalización de LR1 y LR2
- El contenido de LR1 y LR2 se agrega a R1 y R2


### <a name="clone-g1--g2-repositories-to-your-local-dsvm"></a>Los repositorios de G1 y G2 se clonan en la DSVM local

En este paso, se clonan el repositorio ProjectTemplate (G1) y Utilities (G2) del Proceso de ciencia de los datos en equipos (TDSP) de los repositorios de github de TDSP a las carpetas de la DSVM local como LG1 y LG2:

- Cree un directorio para que actúe como el directorio raíz para hospedar todos los clones de los repositorios. 
    -  En la DSVM de Windows, cree un directorio *C:\GitRepos\TDSPCommon*. 
    -  En la DSVM de Linux, cree un directorio *GitRepos\TDSPCommon* en su directorio particular. 

- Ejecute el siguiente conjunto de comandos desde el directorio *GitRepos\TDSPCommon*.

    `git clone https://github.com/Azure/Azure-TDSP-ProjectTemplate`<br>
    `git clone https://github.com/Azure/Azure-TDSP-Utilities`
        
![14](./media/group-manager-tasks/two-folder-cloned-from-TDSP-windows.PNG)

- Con los nombres abreviados de nuestro repositorio, esto es lo que consiguen estas secuencias de comandos: 
    - G1 se clona en LG1
    - G2 se clona en LG2
- Una vez completada la clonación, debería ver los dos directorios, _ProjectTemplate_ y _Utilities_, en el directorio **GitRepos\TDSPCommon**. 

### <a name="clone-r1--r2-repositories-to-your-local-dsvm"></a>Clonar los repositorios R1 y R2 en la DSVM local

En este paso, se clonan el repositorio GroupProjectTemplate (R1) y el repositorio GroupUtilities (R2) en los directorios locales (denominados, respectivamente, LR1 y LR2) en **GitRepos\GroupCommon** en su DSVM.

- Para obtener las direcciones URL de los repositorios R1 y R2, vaya a la página principal **GroupCommon** en VSTS. Normalmente, la dirección URL suele ser *https://\<El nombre del servidor de VSTS\>.visualstudio.com/GroupCommon*. 
- Haga clic en **CÓDIGO**. 
- Elija los repositorios **GroupProjectTemplate** y **GroupUtilities**. Copie y guarde cada una de las direcciones URL (HTTPS para Windows; SSH para Linux) desde el elemento **Clonar URL** para su uso en las secuencias de comandos siguientes:  

![15](./media/group-manager-tasks/find_https_ssh_2.PNG)

- Cambie al directorio **GitRepos\GroupCommon** de la DSVM de Windows o Linux, y ejecute uno de los siguientes conjuntos de comandos para clonar R1 y R2 en ese directorio.
        
Estas son las secuencias de comandos de Windows y Linux:

    # Windows DSVM

    git clone <the HTTPS URL of the GroupProjectTemplate repository>
    git clone <the HTTPS URL of the GroupUtilities repository>

![16](./media/group-manager-tasks/clone-two-empty-group-reo-windows-2.PNG)

    # Linux DSVM

    git clone <the SSH URL of the GroupProjectTemplate repository>
    git clone <the SSH URL of the GroupUtilities repository>

![17](./media/group-manager-tasks/clone-two-empty-group-reo-linux-2.PNG)

>[AZURE.NOTE] Recibirá mensajes de advertencia de que LR1 y LR2 están vacíos.    

- Con los nombres abreviados de nuestro repositorio, esto es lo que consiguen estas secuencias de comandos: 
    - R1 se clona en LR1
    - R2 se clona en LR2   


### <a name="seed-your-groupprojecttemplate-lr1-and-grouputilities-lr2"></a>Propagar GroupProjectTemplate (LR1) y GroupUtilities (LR2)

A continuación, en el equipo local, copie el contenido de los directorios ProjectTemplate y Utilities (excepto los metadatos de los directorios .git) en GitRepos\TDSPCommon a los directorios GroupProjectTemplate y GroupUtilities en **GitRepos\GroupCommon**. Estas son las dos tareas que deberá completar en este paso:

- Copie los archivos de GitRepos\TDSPCommon\ProjectTemplate (**LG1**) en GitRepos\GroupCommon\GroupProjectTemplate (**LR1**) 
- Copie los archivos de GitRepos\TDSPCommon\Utilities (**LG2** en GitRepos\GroupCommon\Utilities (**LR2**). 

Para realizar estas dos tareas, ejecute las secuencias de comandos siguientes en la consola de PowerShell (Windows) o en la consola de la secuencia de comandos de Shell (Linux). Se le solicitará que introduzca las rutas de acceso completas a LG1, LR1, LG2 y LR2. Se validan las rutas de acceso especificadas. Si especifica un directorio que no existe, se le solicitará que lo introduzca de nuevo. 

    # Windows DSVM      
    
    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_win.ps1" -outfile "tdsp_local_copy_win.ps1"
    .\tdsp_local_copy_win.ps1 1

![18](./media/group-manager-tasks/copy-two-folder-to-group-folder-windows-2.PNG)

Ahora puede ver qué archivos de los directorios LG1 y LG1 (excepto los archivos del directorio .git) se copiaron, respectivamente, en LR1 y LR2.

![19](./media/group-manager-tasks/copy-two-folder-to-group-folder-windows.PNG)

    # Linux DSVM

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_linux.sh"
    bash tdsp_local_copy_linux.sh 1

![20](./media/group-manager-tasks/copy-two-folder-to-group-folder-linux-2.PNG)
        
También puede ver que los archivos de las dos carpetas (excepto los archivos del directorio .git) se copian, respectivamente, en GroupProjectTemplate y GroupUtilities.
    
![21](./media/group-manager-tasks/copy-two-folder-to-group-folder-linux.PNG)

- Con los nombres abreviados de nuestro repositorio, esto es lo que consiguen estas secuencias de comandos:
    - Los archivos de LG1 se copian en LR1
    - Los archivos de LG2 se copian en LR2

### <a name="option-to-customize-the-contents-of-lr1--lr2"></a>Opción para personalizar el contenido de LR1 y LR2
    
Si desea personalizar el contenido de LR1 y LR2 para satisfacer las necesidades específicas de su grupo, esta es la fase del procedimiento donde puede hacerlo. Puede modificar los documentos de plantilla, cambiar la estructura de los directorios y agregar utilidades existentes que ha desarrollado su grupo o que son útiles para todo el grupo. 

### <a name="add-the-contents-in-lr1--lr2-to-r1--r2-on-group-server"></a>Agregue el contenido de LR1 y LR2 a R1 y R2 en el servidor de grupo

Ahora, debe agregar el contenido de LR1 y LR2 a los repositorios R1 y R2. Estos son los comandos Bash de git que puede ejecutar en Windows PowerShell o Linux. 

Ejecute los siguientes comandos desde el directorio GitRepos\GroupCommon\GroupProjectTemplate:

    git status
    git add .
    git commit -m"push from DSVM"
    git push

La opción -m le permite establecer un mensaje de confirmación de git.

![22](./media/group-manager-tasks/push-to-group-server-2.PNG)

Puede ver cómo los archivos se sincronizan al instante en el servidor de VSTS del grupo, en el repositorio GroupProjectTemplate.

![23](./media/group-manager-tasks/push-to-group-server-showed-up-2.PNG)

Por último, cambie al directorio **GitRepos\GroupCommon\GroupUtilities** y ejecute el mismo conjunto de comandos Bash de git:

    git status
    git add .
    git commit -m"push from DSVM"
    git push

>[AZURE.NOTE] Si se trata de la primera vez que confirma un repositorio Git, debe configurar los parámetros globales *user.name* y *user.email* para poder ejecutar el comando `git commit`. Ejecute los siguientes dos comandos:
        
    git config --global user.name <your name>
    git config --global user.email <your email address>
 
>Si está confirmando varios repositorios de Git, use el mismo nombre y la misma dirección de correo electrónico al confirmar cada uno de ellos. Usar el mismo nombre y la misma dirección de correo electrónico resulta muy cómodo más adelante al crear paneles de Power BI y realizar el seguimiento de las actividades de Git en varias bases de datos.


- Con los nombres abreviados de nuestro repositorio, esto es lo que consiguen estas secuencias de comandos:
    - El contenido de LR1 se agrega a R1
    - El contenido de LR2 se agrega a R2

## <a name="6-add-group-members-to-the-group-server"></a>6. Agregar miembros del grupo al servidor de grupo

En la página principal del servidor de VSTS de su grupo, haga clic en el **icono de engranaje** situado junto al nombre de usuario en la esquina superior derecha y, después, seleccione la pestaña **Seguridad**. Puede agregar miembros al grupo con diferentes permisos.

![24](./media/group-manager-tasks/add_member_to_group.PNG) 


## <a name="next-steps"></a>Pasos siguientes

Estos son vínculos a descripciones más detalladas de los roles y tareas definidas por el proceso de ciencia de datos en equipo:

- [Tareas del administrador de grupo en un equipo de ciencia de datos](group-manager-tasks.md)
- [Tareas del responsable de equipo en un equipo de ciencia de datos](team-lead-tasks.md)
- [Tareas del responsable de proyecto en un equipo de ciencia de datos](project-lead-tasks.md)
- [Colaboradores individuales del proyecto en un equipo de ciencia de datos](project-ic-tasks.md)