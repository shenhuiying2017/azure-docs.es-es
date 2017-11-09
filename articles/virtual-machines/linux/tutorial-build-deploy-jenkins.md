---
title: "Integración continua o implementación continua desde Jenkins en máquinas virtuales de Azure con VSTS | Microsoft Docs"
description: "Configure la integración continua (CI) y la implementación continua (CD) de una aplicación de Node.js con Jenkins en máquinas virtuales de Azure desde Release Management para Visual Studio Team Services (VSTS) o Microsoft Team Foundation Server (TFS)"
author: ahomer
manager: douge
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/19/2017
ms.author: ahomer
ms.custom: mvc
ms.openlocfilehash: d5c15d6ab36a8454d1c69bac498be89b990c7e33
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/31/2017
---
# <a name="deploy-your-app-to-linux-vms-using-jenkins-and-vsts"></a>Implementación de la aplicación en máquinas virtuales Linux con Jenkins y VSTS

La integración continua (CI) y la implementación continua (CD) constituyen una canalización mediante la que se puede compilar, publicar e implementar el código. Visual Studio Team Services (VSTS) proporciona un completo conjunto rico en contenido de herramientas de automatización de CI/CD para la implementación en Azure. Jenkins es una popular herramienta basada en servidor de CI/CD de terceros que además proporciona automatización de CI/CD. Puede usar ambos conjuntamente para personalizar la forma de entrega de la aplicación o el servicio en la nube.

En este tutorial se usa Jenkins para compilar una **aplicación web de Node.js** y VSTS o Team Foundation Server (TFS) para implementarla en un [grupo de implementación](https://www.visualstudio.com/docs/build/concepts/definitions/release/deployment-groups/) que contiene máquinas virtuales Linux.

Podrá:

> [!div class="checklist"]
> * Obtención de la aplicación de ejemplo
> * Configuración de complementos de Jenkins
> * Configurar un proyecto de estilo libre de Jenkins para Node.js
> * Configurar Jenkins para la integración con VSTS
> * Creación de un punto de conexión de servicio de Jenkins
> * Crear un grupo de implementación para las máquinas virtuales de Azure
> * Crear una definición de la versión de VSTS
> * Ejecutar las implementaciones desencadenadas por CI y de manera manual

## <a name="before-you-begin"></a>Antes de empezar

* Necesita acceso a un servidor Jenkins. Si aún no ha creado un servidor Jenkins, consulte [Create a Jenkins master on an Azure Virtual Machine](https://docs.microsoft.com/en-us/azure/jenkins/install-jenkins-solution-template) (Creación de un patrón de Jenkins en una máquina virtual de Azure). 

* Inicie sesión en su cuenta de VSTS (`https://{youraccount}.visualstudio.com`). 
  Puede obtener una [cuenta de VSTS gratuita](https://go.microsoft.com/fwlink/?LinkId=307137&clcid=0x409&wt.mc_id=o~msft~vscom~home-vsts-hero~27308&campaign=o~msft~vscom~home-vsts-hero~27308).

  > [!NOTE]
  > Para obtener más información, vea [Connect to VSTS](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services) (Conexión con VSTS).

*  Necesita una máquina virtual de Linux para un destino de implementación.  Para obtener más información, consulte [Creación y administración de máquinas virtuales Linux con la CLI de Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/tutorial-manage-vm).

*  Abra el puerto de entrada 80 para la máquina virtual.  Para obtener más información, consulte [Creación de grupos de seguridad de red con Azure Portal](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-create-nsg-arm-pportal).

## <a name="get-the-sample-app"></a>Obtención de la aplicación de ejemplo

Necesita una aplicación para implementar almacenada en un repositorio de Git.
Para este tutorial se recomienda usar [esta aplicación de ejemplo disponible en GitHub](https://github.com/azooinmyluggage/fabrikam-node).  Este tutorial contiene un script de ejemplo que se utiliza para la instalación de Node.js y una aplicación.  Si desea trabajar con su propio repositorio, debe configurar un ejemplo similar.

1. Cree una bifurcación de esta aplicación y anote la ubicación (URL) para usarla en pasos posteriores de este tutorial.  Para más información, consulte [Fork a repo](https://help.github.com/articles/fork-a-repo/) (Bifurcar un repositorio).    

> [!NOTE]
> La aplicación se ha compilado mediante [Yeoman](http://yeoman.io/learning/index.html); usa **Express**, **Bower** y **Grunt** y tiene algunos paquetes **npm** como dependencias.
> El ejemplo también contiene un script que configura Nginx e implementa la aplicación. Se ejecuta en las máquinas virtuales. En concreto, el script instala Node, Nginx y PM2; configura Nginx y PM2 y luego inicia la aplicación Node.

## <a name="configure-jenkins-plugins"></a>Configuración de complementos de Jenkins

En primer lugar, debe configurar dos complementos de Jenkins para **NodeJS** y la **implementación continua de VS Team Services**.

1. Abra la cuenta de Jenkins y elija **Manage Jenkins** (Administrar Jenkins).
2. En la página **Manage Jenkins** (Administrar Jenkins), elija **Manage Plugins** (Administrar complementos).
3. Filtre la lista para buscar el complemento **NodeJS** y elija la opción **Install without restart** (Instalar sin reinicio).
    ![Adición del complemento NodeJS a Jenkins](media/tutorial-build-deploy-jenkins/jenkins-nodejs-plugin.png)
4. Filtre la lista para buscar el complemento **VS Team Services Continuous Deployment** (Implementación continua de VS Team Services) y elija la opción **Install without restart** (Instalar sin reinicio).
5. Vuelva al panel de Jenkins y elija **Manage Jenkins** (Administrar Jenkins).
6. Elija **Global Tool Configuration** (Configuración de herramienta global).  Busque **NodeJS** y haga clic en **NodeJS installations** (Instalaciones de NodeJS).
7. Habilite la opción **Install automatically** (Instalar automáticamente) y, a continuación, escriba un valor para **Name** (Nombre).
8. Haga clic en **Guardar**.

## <a name="configure-a-jenkins-freestyle-project-for-nodejs"></a>Configurar un proyecto de estilo libre de Jenkins para Node.js

1. Haga clic en **New Item** (Nuevo elemento).  Escriba un **nombre para el elemento**.
2. Seleccione **Freestyle project** (Proyecto de estilo libre).  Haga clic en **Aceptar**.
3. En la pestaña **Administración de código fuente**, seleccione **Git** y escriba los detalles del repositorio y la bifurcación que contienen el código de la aplicación.    
    ![Adición de un repositorio a la compilación](media/tutorial-build-deploy-jenkins/jenkins-git.png)
4. En la pestaña **Build Triggers** (Desencadenadores de compilación), seleccione **Poll SCM** (Sondear SCM) y especifique la programación `H/03 * * * *` para sondear el repositorio de Git en busca de cambios cada tres minutos. 
5. En la pestaña **Build Environment** (Entorno de compilación), seleccione **Provide Node &amp; npm bin/ folder PATH** (Proporcionar ruta de acceso a la carpeta bin/ de Node y npm) y seleccione el valor de **instalación de Node JS**. Deje **npmrc file** establecido en "Usar predeterminado del sistema".
6. En la pestaña **Build** (Compilación), elija **Execute shell** (Ejecutar shell) y escriba el comando `npm install` para asegurarse de que se actualicen todas las dependencias.


## <a name="configure-jenkins-for-vsts-integration"></a>Configurar Jenkins para la integración con VSTS

  > [!NOTE]
  Asegúrese de que el token de acceso personal (PAT) que usa para los siguientes pasos contiene el **permiso de publicación (leer, escribir, ejecutar y administrar) en VSTS**.
 
1.  Cree una cuenta de PAT en la cuenta de VSTS si aún no tiene una. Jenkins necesita esta información para acceder a la cuenta de VSTS.  Asegúrese de **almacenar** la información del token para los próximos pasos de esta sección.
  Lea [How do I create a personal access token for VSTS and TFS](https://www.visualstudio.com/docs/setup-admin/team-services/use-personal-access-tokens-to-authenticate) (Creación de un token de acceso personal para VSTS y TFS) para aprender a generar uno.
2. En la pestaña **Post-build Actions** (Acciones posteriores a la compilación), haga clic en **Add post-build action** (Agregar acción posterior a la compilación). Elija **Archive the artifacts** (Archivar los artefactos).
3. Para **Files to archive** (Archivos para archivar), escriba `**/*` para incluir todos los archivos.
4. Para crear otra acción, haga clic en **Add post-build action** (Agregar acción posterior a la compilación).
5. Elija **Trigger release in TFS/Team Services** (Desencadenar publicación en TFS/Team Services) y escriba el URI para la cuenta de VSTS como: `https://{your-account-name}.visualstudio.com`.
6. Escriba el nombre de **Team Project** (Proyecto de equipo).
7. Elija un nombre para la **definición de la versión** (creará esta definición de la versión más adelante en VSTS).
8. Elija las credenciales para conectarse a su entorno de VSTS o TFS.  Deje el campo **Username** (Nombre de usuario) en blanco si usa VSTS.
   Escriba un valor para **Username and Password** (Nombre de usuario y contraseña) si usa una versión local de TFS.    
    ![Configuración de acciones posteriores a la compilación de Jenkins](media/tutorial-build-deploy-jenkins/trigger-release-from-jenkins.png)
5. **Guarde** el proyecto de Jenkins.

## <a name="create-a-jenkins-service-endpoint"></a>Creación de un punto de conexión de servicio de Jenkins

Un punto de conexión de servicio permite a VSTS conectarse a Jenkins.

1. Abra la página **Servicios** de VSTS, abra la lista **Nuevo punto de conexión de servicio** y elija **Jenkins**.
    ![Adición de un punto de conexión de Jenkins](media/tutorial-build-deploy-jenkins/add-jenkins-endpoint.png)
2. Escriba un nombre para la conexión.
3. Escriba la dirección URL del servidor de Jenkins y active la opción **Aceptar certificados SSL que no son de confianza**.  Una dirección URL de ejemplo es la siguiente: `http://{YourJenkinsURL}.westcentralus.cloudapp.azure.com`
4. Escriba el **nombre de usuario y la contraseña** de la cuenta de Jenkins.
5. Elija **Comprobar conexión** para comprobar que la información es correcta.
6. Elija **Aceptar** para crear el punto de conexión de servicio.

## <a name="create-a-deployment-group-for-azure-virtual-machines"></a>Creación de un grupo de implementación para Azure Virtual Machines

Necesita un [grupo de implementación](https://www.visualstudio.com/docs/build/concepts/definitions/release/deployment-groups/) para registrar el agente de VSTS de manera que la definición de la versión pueda implementarse en la máquina virtual.  Los grupos de implementación facilitan la definición de grupos lógicos de equipos de destino para la implementación, e instalan el agente requerido en cada máquina.

   > [!NOTE]
   > En los siguientes pasos, asegúrese de instalar los requisitos previos y **no ejecute el script con privilegios sudo.**

1. Abra la pestaña **Versiones** del nodo **Compilación y versión** y luego abra la pestaña **Grupos de implementación** y elija **+ Nuevo**.
2. Especifique un nombre para el grupo de implementación y una descripción opcional.
   Luego elija **Crear**.
3. Elija el sistema operativo para la máquina virtual del destino de implementación.  Por ejemplo, elija **Ubuntu 16.04+**.
4. Marque **Usar un token de acceso personal en el script para la autenticación**.
5. Seleccione el vínculo **Requisitos previos del sistema**.  Instalar los requisitos previos para su sistema operativo.
6. Seleccione **Copiar script en el Portapapeles** para copiar el script.
7. **Inicie sesión** en la máquina virtual del destino de implementación y **ejecute** el script.  **No** ejecute el script con privilegios sudo.
8. Después de la instalación, se le pedirán etiquetas para el grupo de implementación.  Acepte los valores predeterminados.
9. En VSTS, busque la máquina virtual recién registrada en **Destinos**, bajo **Grupos de implementación**.

## <a name="create-a-vsts-release-definition"></a>Crear una definición de la versión de VSTS

Una definición de versión especifica el proceso que VSTS ejecuta para implementar la aplicación.  En este ejemplo, se ejecuta un script de shell.

Para crear la definición de versión en VSTS:

1. Abra la pestaña **Versiones** en el concentrador **Compilación y versión** y elija**Crear definición de versión**. 
2. Seleccione la plantilla **Vacío** seleccionando empezar con un **Proceso vacío**.
3. En la sección **Artefactos**, haga clic en **+ Agregar artefacto** y elija **Jenkins** como **Tipo de origen**. Seleccione la conexión del punto de conexión de servicio de Jenkins. Luego seleccione el trabajo de origen de Jenkins y elija **Agregar**.
4.  Haga clic en los puntos suspensivos junto a **Entorno 1**.  Haga clic en **Agregar fase de grupo de implementación**.
5.  Elija su **Grupo de implementación**.
5. Haga clic en **+** para agregar una tarea a la **Fase de grupo de implementación**.
6. Elija la tarea **Script de shell** y haga clic en **Agregar**.    
    La tarea **Script de Shell** se usa para proporcionar la configuración de un script que se ejecuta en cada servidor para instalar Node.js e iniciar la aplicación. Configúrelo de la siguiente forma:
8. **Ruta de acceso del script**:     
    `$(System.DefaultWorkingDirectory)/Fabrikam-Node/deployscript.sh`
9.  Haga clic en **Opciones avanzadas** y, a continuación, habilite **Especificar directorio de trabajo**.
10.  **Directorio de trabajo**: `$(System.DefaultWorkingDirectory)/Fabrikam-Node`
11. Cambie el nombre de la definición de versión por el nombre especificado en la pestaña **Post-build Actions** (Acciones posteriores a la compilación) de la compilación en Jenkins. Jenkins necesita este nombre para desencadenar una nueva versión cuando se actualizan los artefactos de origen.
12. Haga clic en **Guardar** y en **Aceptar** para guardar la definición de la versión.

## <a name="execute-manual-and-ci-triggered-deployments"></a>Ejecutar las implementaciones desencadenadas por CI y de manera manual

1. Elija **+ Versión** y seleccione **Crear versión**.
2. Seleccione la compilación completada en la lista desplegable resaltada y elija **Poner en cola**.
3. Elija el vínculo de la versión en el mensaje emergente. Por ejemplo: "Versión **Release-1** se ha creado".
4. Abra la pestaña **Registros** para ver la salida de la consola de la versión.
5. En el explorador, abra la dirección URL de uno de los servidores agregados al grupo de implementación. Por ejemplo, escriba `http://{your-server-ip-address}`
6. Vaya al repositorio de Git de origen y modifique el contenido del encabezado **h1** del archivo app/views/index.jade con parte del texto modificado.
7. **Confirme** el cambio.
8. Pasados unos minutos, verá una nueva versión creada en la página **Versiones** de VSTS o TFS. Abra la versión para ver cómo se realiza la implementación. ¡Enhorabuena!

## <a name="next-steps"></a>Pasos siguientes

En este tutorial se ha automatizado la implementación de una aplicación en Azure con la compilación de Jenkins y VSTS para la versión. Ha aprendido a:

> [!div class="checklist"]
> * Compilar la aplicación en Jenkins
> * Configurar Jenkins para la integración con VSTS
> * Crear un grupo de implementación para las máquinas virtuales de Azure
> * Crear una definición de versión que configure las máquinas virtuales e implemente la aplicación

Siga con el próximo tutorial para aprender a implementar una pila LAMP (Linux, Apache, MySQL y PHP).

> [!div class="nextstepaction"]
> [Implementación de una pila de LAMP](tutorial-lamp-stack.md)