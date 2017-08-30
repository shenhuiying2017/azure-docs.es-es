---
title: "Integración continua o implementación continua desde Jenkins en máquinas virtuales de Azure con Team Services | Microsoft Docs"
description: "Configure la integración continua (CI) y la implementación continua (CD) de una aplicación de Node.js con Jenkins en máquinas virtuales de Azure desde Release Management para Visual Studio Team Services (VSTS) o Microsoft Team Foundation Server (TFS)"
author: ahomer
manager: douge
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/15/2017
ms.author: ahomer
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: a40e26a8681df31fad664e4d1df4c1513311900d
ms.contentlocale: es-es
ms.lasthandoff: 08/21/2017

---

# <a name="deploy-your-app-to-linux-vms-using-jenkins-and-team-services"></a>Implementación de la aplicación en máquinas virtuales Linux con Jenkins y Team Services

La integración continua (CI) y la implementación continua (CD) constituyen una canalización mediante la que se puede compilar, publicar e implementar el código. Team Services proporciona un completo conjunto rico en contenido de herramientas de automatización de CI/CD para la implementación en Azure. Jenkins es una popular herramienta basada en servidor de CI/CD de terceros que además proporciona automatización de CI/CD. Puede usar ambos conjuntamente para personalizar la forma de entrega de la aplicación o el servicio en la nube.

En este tutorial se usa Jenkins para compilar una **aplicación web de Node.js** y Visual Studio Team Services para implementarla en un [grupo de implementación](https://www.visualstudio.com/docs/build/concepts/definitions/release/deployment-groups/) que contiene máquinas virtuales Linux.

Podrá:

> [!div class="checklist"]
> * Compilar la aplicación en Jenkins
> * Configurar Jenkins para la integración con Team Services
> * Crear un grupo de implementación para las máquinas virtuales de Azure
> * Crear una definición de versión que configure las máquinas virtuales e implemente la aplicación

## <a name="before-you-begin"></a>Antes de empezar

* Necesita acceso a una cuenta de Jenkins. Si aún no ha creado un servidor de Jenkins, vea [Jenkins Documentation (Documentación de Jenkins)](https://jenkins.io/doc/). 

* Inicie sesión en la cuenta de Team Services (`https://{youraccount}.visualstudio.com`). 
  Puede obtener una [cuenta de Team Services gratuita](https://go.microsoft.com/fwlink/?LinkId=307137&clcid=0x409&wt.mc_id=o~msft~vscom~home-vsts-hero~27308&campaign=o~msft~vscom~home-vsts-hero~27308).

  > [!NOTE]
  > Para más información, vea [Connect to Team Services (Conexión a Team Services)](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services).

* Si aún no tiene uno, cree un token de acceso personal (PAT) en la cuenta de Team Services. Jenkins necesita esta información para acceder a la cuenta de Team Services.
  Lea [How do I create a personal access token for Team Services and TFS (Cómo crear un token de acceso personal para Team Services y TFS)](https://www.visualstudio.com/docs/setup-admin/team-services/use-personal-access-tokens-to-authenticate) para aprender a generar uno.

## <a name="get-the-sample-app"></a>Obtención de la aplicación de ejemplo

Necesita una aplicación para implementar almacenada en un repositorio de Git.
Para este tutorial se recomienda usar [esta aplicación de ejemplo disponible en GitHub](https://github.com/azooinmyluggage/fabrikam-node).

1. Cree una bifurcación de esta aplicación y anote la ubicación (URL) para usarla en pasos posteriores de este tutorial.

1. Convierta la bifurcación en **pública** para simplificar la conexión a GitHub más adelante.

> [!NOTE]
> Para más información, vea [Fork a repo (Bifurcar un repositorio)](https://help.github.com/articles/fork-a-repo/) y [Making a private repository public (Hacer público un repositorio privado)](https://help.github.com/articles/making-a-private-repository-public/).

> [!NOTE]
> La aplicación se ha compilado mediante [Yeoman](http://yeoman.io/learning/index.html); usa **Express**, **Bower** y **Grunt** y tiene algunos paquetes **npm** como dependencias.
> La aplicación de ejemplo contiene un conjunto de [plantillas de Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#template-deployment) que se usan para crear de forma dinámica las máquinas virtuales para su implementación en Azure. Las tareas de la [definición de versión de Team Services](https://www.visualstudio.com/docs/build/actions/work-with-release-definitions) usan estas plantillas.
> La plantilla principal crea un grupo de seguridad de red, una máquina virtual y una red virtual. Asigna una dirección IP pública y abre el puerto de entrada 80. También agrega una etiqueta que usa el grupo de implementación para seleccionar las máquinas que van a recibir la implementación.
>
> El ejemplo también contiene un script que configura Nginx e implementa la aplicación. Se ejecuta en cada una de las máquinas virtuales. En concreto, el script instala Node, Nginx y PM2; configura Nginx y PM2 y luego inicia la aplicación Node.

## <a name="configure-jenkins-plugins"></a>Configuración de complementos de Jenkins

En primer lugar, debe configurar dos complementos de Jenkins para **NodeJS** y la **integración con Team Services**.

1. Abra la cuenta de Jenkins y elija **Manage Jenkins** (Administrar Jenkins).

1. En la página **Manage Jenkins** (Administrar Jenkins), elija **Manage Plugins** (Administrar complementos).

1. Filtre la lista para buscar el complemento **NodeJS** e instálelo sin reiniciar.

   ![Adición del complemento NodeJS a Jenkins](media/tutorial-build-deploy-jenkins/jenkins-nodejs-plugin.png)

1. Filtre la lista para buscar el complemento **Team Foundation Server** e instálelo. (Este complemento funciona con Team Services y Team Foundation Server). No es necesario reiniciar Jenkins.

## <a name="configure-jenkins-build-for-nodejs"></a>Configuración de la compilación de Jenkins para Node.js

En Jenkins, cree un nuevo proyecto de compilación y configúrelo como se indica a continuación:

1. En la pestaña **General**, escriba un nombre para el proyecto de compilación.

1. En la pestaña **Administración de código fuente**, seleccione **Git** y escriba los detalles del repositorio y la bifurcación que contienen el código de la aplicación.

   ![Adición de un repositorio a la compilación](media/tutorial-build-deploy-jenkins/jenkins-git.png)

   > [!NOTE]
   > Si el repositorio no es público, elija **Agregar** y proporcione las credenciales para conectarse a él.

1. En la pestaña **Build Triggers** (Desencadenadores de compilación), seleccione **Poll SCM** (Sondear SCM) y especifique la programación `H/03 * * * *` para sondear el repositorio de Git en busca de cambios cada tres minutos. 

1. En la pestaña **Build Environment** (Entorno de compilación), seleccione **Provide Node &amp; npm bin/ folder PATH** (Proporcionar ruta de acceso a la carpeta bin/ de Node y npm) y escriba `NodeJS` para el valor de instalación de Node JS. Deje **npmrc file** establecido en "Usar predeterminado del sistema".

1. En la pestaña **Compilación**, escriba el comando `npm install` para asegurarse de que se actualicen todas las dependencias.

## <a name="configure-jenkins-for-team-services-integration"></a>Configurar Jenkins para la integración con Team Services

1. En la pestaña **Post-build Actions** (Acciones posteriores a la compilación), en **Files to archive** (Archivos para archivar), escriba `**/*` para incluir todos los archivos.

1. En **Trigger release in TFS/Team Services** (Desencadenar versión en TFS/Team Services), escriba la dirección URL completa de la cuenta (por ejemplo, `https://your-account-name.visualstudio.com`), el nombre del proyecto, un nombre para la definición de versión (se crea más adelante) y las credenciales para conectarse a la cuenta.
   Necesita el nombre de usuario y el PAT que creó anteriormente. 

   ![Configuración de acciones posteriores a la compilación de Jenkins](media/tutorial-build-deploy-jenkins/trigger-release-from-jenkins.png)

1. Guarde el proyecto de compilación.

## <a name="create-a-jenkins-service-endpoint"></a>Creación de un punto de conexión de servicio de Jenkins

Un punto de conexión de servicio permite a Team Services conectarse a Jenkins.

1. Abra la página **Servicios** de Team Services, abra la lista **Nuevo punto de conexión de servicio** y elija **Jenkins**.

   ![Adición de un punto de conexión de Jenkins](media/tutorial-build-deploy-jenkins/add-jenkins-endpoint.png)

1. Escriba el nombre que vaya a usar para hacer referencia a esta conexión.

1. Escriba la dirección URL del servidor de Jenkins y active la opción **Aceptar certificados SSL que no son de confianza**.

1. Escriba el nombre de usuario y la contraseña de la cuenta de Jenkins.

1. Elija **Comprobar conexión** para comprobar que la información es correcta.

1. Elija **Aceptar** para crear el punto de conexión de servicio.

## <a name="create-a-deployment-group"></a>Creación de un grupo de implementación

Necesita un [grupo de implementación](https://www.visualstudio.com/docs/build/concepts/definitions/release/deployment-groups/) para incluir las máquinas virtuales en él.

1. Abra la pestaña **Versiones** del nodo **Compilación y versión** y luego abra la pestaña **Grupos de implementación** y elija **+ Nuevo**.

1. Especifique un nombre para el grupo de implementación y una descripción opcional.
   Luego elija **Crear**.

La tarea Implementación de un grupo de recursos de Azure crea y registra las máquinas virtuales cuando se ejecuta con la plantilla de Azure Resource Manager.
El usuario no tiene que crear y registrar las máquinas virtuales por sí mismo.

## <a name="create-a-release-definition"></a>Creación de una definición de versión

Una definición de versión especifica el proceso de Team Services que se ejecuta para implementar la aplicación.
Para crear la definición de versión en Team Services:

1. Abra la pestaña **Versiones** del nodo **Compilación y versión**, abra el desplegable **+** en la lista de definiciones de versión y elija **Crear definición de versión**. 

1. Seleccione la plantilla **Vacía** y elija **Siguiente**.

1. En la sección **Artefactos**, haga clic en **Vincular un origen de artefacto** y elija **Jenkins**. Seleccione la conexión del punto de conexión de servicio de Jenkins. Luego seleccione el trabajo de origen de Jenkins y elija **Crear**. 

1. En la nueva definición de versión, elija **+ Agregar tareas** y agregue una tarea **Implementación de un grupo de recursos de Azure** al entorno predeterminado.

1. Elija la flecha desplegable situada junto al vínculo **+ Agregar tareas** y agregue una fase de grupo de implementación a la definición.

   ![Adición de una fase de grupo de implementación](media/tutorial-build-deploy-jenkins/deployment-group-phase-in-release-definition.png) 

1. En el catálogo Tarea, abra la sección **Utilidad** y agregue una instancia de la tarea **Script de Shell**.

1. La plantilla de parámetros usada en la tarea Implementación de un grupo de recursos de Azure establece la contraseña de administrador que se emplea para conectarse a las máquinas virtuales.
   Indique esta contraseña con la variable **$(adminpassword)**:
   
   - Abra la pestaña **Variables** y, en la sección **Variables**, escriba el nombre `adminpassword`.

   - Escriba la contraseña de administrador.

   - Elija el icono de "candado" situado junto al cuadro de texto de valor para proteger la contraseña. 

## <a name="configure-the-azure-resource-group-deployment-task"></a>Configuración de la tarea Implementación de un grupo de recursos de Azure

La tarea **Implementación de un grupo de recursos de Azure** se usa para crear el grupo de implementación. Configúrelo de la siguiente forma:

* **Suscripción de Azure:** seleccione una conexión de la lista de **Conexiones a servicios de Azure disponibles**. 
  Si no aparece ninguna conexión, elija **Administrar**, seleccione **Nuevo punto de conexión de servicio**, luego **Azure Resource Manager** y siga los mensajes.
  Vuelva a la definición de versión, actualice la lista **Suscripción de AzureRM** y seleccione la conexión creada.

* **Grupo de recursos**: escriba el nombre del grupo de recursos creado anteriormente.

* **Ubicación**: seleccione una región para la implementación.

  ![Creación de un nuevo grupo de recursos](media/tutorial-build-deploy-jenkins/provision-web-server.png)

* **Ubicación de la plantilla**: `URL of the file`

* **Vínculo de la plantilla**: `{your-git-repo}/ARM-Templates/UbuntuWeb1.json`

* **Vínculo de parámetros de la plantilla**: `{your-git-repo}/ARM-Templates/UbuntuWeb1.parameters.json`

* **Reemplazar parámetros de plantilla**: una lista de los valores de invalidación, por ejemplo: `-location {location} -virtualMachineName {machine] -virtualMachineSize Standard_DS1_v2 -adminUsername {username} -virtualNetworkName fabrikam-node-rg-vnet -networkInterfaceName fabrikam-node-websvr1 -networkSecurityGroupName fabrikam-node-websvr1-nsg -adminPassword $(adminpassword) -diagnosticsStorageAccountName fabrikamnodewebsvr1 -diagnosticsStorageAccountId Microsoft.Storage/storageAccounts/fabrikamnodewebsvr1 -diagnosticsStorageAccountType Standard_LRS -addressPrefix 172.16.8.0/24 -subnetName default -subnetPrefix 172.16.8.0/24 -publicIpAddressName fabrikam-node-websvr1-ip -publicIpAddressType Dynamic`.<br />Inserte sus propios valores específicos para los {marcadores de posición}. 

* **Habilitar requisitos previos**: `Configure with Deployment Group agent`

* **Punto de conexión de TFS o VSTS**: elija **Agregar** y, en el cuadro de diálogo "Agregar nueva conexión de Team Foundation Server/Team Services", seleccione **Autenticación basada en token**. Escriba el nombre de la conexión y la dirección URL del proyecto de equipo. Luego genere y escriba un [token de acceso personal (PAT)]( https://www.visualstudio.com/docs/setup-admin/team-services/use-personal-access-tokens-to-authenticate) para autenticar la conexión al proyecto de equipo.

  ![Creación de un token de acceso personal](media/tutorial-build-deploy-jenkins/create-a-pat.png)

* **Proyecto de equipo**: seleccione el proyecto actual.

* **Grupo de implementación**: escriba el mismo nombre de grupo de implementación que ha usado para el parámetro **Grupo de recursos**.

La configuración predeterminada de la tarea Implementación de un grupo de recursos de Azure es crear o actualizar un recurso y hacerlo de forma incremental. La tarea crea las máquinas virtuales la primera vez que se ejecuta y posteriormente simplemente las actualiza.

## <a name="configure-the-shell-script-task"></a>Configuración de la tarea Script de Shell

La tarea **Script de Shell** se usa para proporcionar la configuración de un script que se ejecuta en cada servidor para instalar Node.js e iniciar la aplicación. Configúrelo de la siguiente forma:

* **Ruta de acceso del script**: `$(System.DefaultWorkingDirectory)/Fabrikam-Node/deployscript.sh`

* **Especificar directorio de trabajo**: `Checked`

* **Directorio de trabajo**: `$(System.DefaultWorkingDirectory)/Fabrikam-Node`
   
## <a name="rename-and-save-the-release-definition"></a>Cambio del nombre de la definición de versión y guardado

1. Cambie el nombre de la definición de versión por el nombre especificado en la pestaña **Post-build Actions** (Acciones posteriores a la compilación) de la compilación en Jenkins. Jenkins necesita este nombre para desencadenar una nueva versión cuando se actualizan los artefactos de origen.

1. Opcionalmente, cambie el nombre del entorno al hacer clic en el nombre. 

1. Elija **Guardar** y luego **Aceptar**.

## <a name="start-a-manual-deployment"></a>Inicio de una implementación manual

1. Elija **+ Versión** y seleccione **Crear versión**.

1. Seleccione la compilación completada en la lista desplegable resaltada y elija **Crear**.

1. Elija el vínculo de la versión en el mensaje emergente. Por ejemplo: "Versión **Release-1** se ha creado".

1. Abra la pestaña **Registros** para ver la salida de la consola de la versión.

1. En el explorador, abra la dirección URL de uno de los servidores agregados al grupo de implementación. Por ejemplo, escriba `http://{your-server-ip-address}`

## <a name="start-a-cicd-deployment"></a>Inicio de una implementación de CI/CD

1. En la definición de versión, desactive la casilla **Habilitado** de la sección **Opciones de control** de la configuración de la tarea Implementación de un grupo de recursos de Azure.
   Para implementaciones futuras en el grupo de implementación existente, no es necesario volver a ejecutar esta tarea.

1. Vaya al repositorio de Git de origen y modifique el contenido del encabezado **h1** del archivo [app/views/index.jade](https://github.com/azooinmyluggage/fabrikam-node/blob/master/app/views/index.jade).

1. Confirme el cambio.

1. Pasados unos minutos, verá una nueva versión creada en la página **Versiones** de Team Services o TFS. Abra la versión para ver cómo se realiza la implementación. ¡Enhorabuena!

## <a name="next-steps"></a>Pasos siguientes

En este tutorial se ha automatizado la implementación de una aplicación en Azure con la compilación de Jenkins y Team Services para la versión. Ha aprendido a:

> [!div class="checklist"]
> * Compilar la aplicación en Jenkins
> * Configurar Jenkins para la integración con Team Services
> * Crear un grupo de implementación para las máquinas virtuales de Azure
> * Crear una definición de versión que configure las máquinas virtuales e implemente la aplicación

Siga con el próximo tutorial para aprender a implementar una pila LAMP (Linux, Apache, MySQL y PHP).

> [!div class="nextstepaction"]
> [Implementación de una pila de LAMP](tutorial-lamp-stack.md)
