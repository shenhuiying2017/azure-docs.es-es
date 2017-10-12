---
title: CI/CD con Azure Container Service Engine y modo Swarm | Microsoft Docs
description: "Uso de Azure Container Service Engine con modo Docker Swarm, Azure Container Registry y Visual Studio Team Services para proporcionar de forma continua de una aplicación .NET Core con varios contenedores"
services: container-service
documentationcenter: " "
author: diegomrtnzg
manager: esterdnb
tags: acs, azure-container-service, acs-engine
keywords: Docker, contenedores, microservicios, Swarm, Azure, Visual Studio Team Services, DevOps, ACS Engine
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/27/2017
ms.author: diegomrtnzg
ms.custom: mvc
ms.openlocfilehash: 2c0e5fe4f60738fcc1aa67a78674e6f3c62e5628
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="full-cicd-pipeline-to-deploy-a-multi-container-application-on-azure-container-service-with-acs-engine-and-docker-swarm-mode-using-visual-studio-team-services"></a>Canalización completa de CI/CD para implementar una aplicación de varios contenedores en Azure Container Service con ACS Engine y modo Docker Swarm mediante Visual Studio Team Services

*Este artículo se basa en el documento [Canalización completa de CI/CD para implementar una aplicación de varios contenedores en Azure Container Service con Docker Swarm mediante Visual Studio Team Services](container-service-docker-swarm-setup-ci-cd.md)*

En la actualidad, uno de los mayores desafíos al desarrollar aplicaciones modernas para la nube es la posibilidad de proporcionar estas aplicaciones de forma continua. En este artículo, aprenderá a implementar una integración completa continua y la canalización de la implementación (CI/CD) mediante: 
* Azure Container Service Engine con modo Docker Swarm
* Azure Container Registry
* Visual Studio Team Services

Este artículo se basa en una aplicación sencilla, disponible en [GitHub](https://github.com/jcorioland/MyShop/tree/docker-linux), desarrollada con ASP.NET Core. La aplicación se compone de cuatro servicios diferentes: tres API web y un front-end web:

![Aplicación de ejemplo MyShop](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/myshop-application.png)

El objetivo es proporcionar esta aplicación de forma continua en un clúster del modo Docker Swarm, mediante Visual Studio Team Services. La ilustración siguiente describe esta canalización de entrega continua:

![Aplicación de ejemplo MyShop](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/full-ci-cd-pipeline.png)

Esta es una breve explicación de los pasos:

1. Los cambios de código se realizan en el repositorio de código fuente (aquí, GitHub). 
2. GitHub desencadena una compilación en Visual Studio Team Services. 
3. Visual Studio Team Services obtiene la versión más reciente del código fuente y compila todas las imágenes que componen la aplicación. 
4. Visual Studio Team Services inserta cada imagen en un registro de Docker que se creó mediante el servicio Azure Container Registry. 
5. Visual Studio Team Services desencadena una nueva versión. 
6. La versión ejecuta algunos comandos mediante SSH en el nodo maestro del clúster de Azure Container Service. 
7. Modo Docker Swarm en el clúster extrae la versión más reciente de las imágenes 
8. La nueva versión de la aplicación se implementa mediante Docker Stack 

## <a name="prerequisites"></a>Requisitos previos

Antes de comenzar este tutorial, debe realizar las siguientes tareas:

- [Crear un clúster del modo Swarm en Azure Container Service con ACS Engine](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acsengine-swarmmode)
- [Conectar con el clúster de Swarm en Azure Container Service (servicio Contenedor de Azure)](../container-service-connect.md)
- [Crear una instancia de Azure Container Registry](../../container-registry/container-registry-get-started-portal.md)
- [Crear una cuenta y un proyecto de equipo de Visual Studio Team Services](https://www.visualstudio.com/en-us/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services)
- [Bifurcar el repositorio de GitHub a su cuenta de GitHub](https://github.com/jcorioland/MyShop/tree/docker-linux)

>[!NOTE]
> El orquestador de Docker Swarm de Azure Container Service utiliza Swarm, que es independiente y heredado. Actualmente, el modo [Swarm](https://docs.docker.com/engine/swarm/) integrado (en Docker 1.12 y versiones posteriores) no es un orquestador admitido en Azure Container Service. Por eso, usamos [ACS Engine](https://github.com/Azure/acs-engine/blob/master/docs/swarmmode.md), una [plantilla de inicio rápido](https://azure.microsoft.com/resources/templates/101-acsengine-swarmmode/) a la que ha contribuido la comunidad, o una solución de Docker en [Azure Marketplace](https://azuremarketplace.microsoft.com).
>

## <a name="step-1-configure-your-visual-studio-team-services-account"></a>Paso 1: Configuración de la cuenta de Visual Studio Team Services 

En esta sección, configurará su cuenta de Visual Studio Team Services. Para configurar los puntos de conexión de servicios de VSTS, en el proyecto de Visual Studio Team Services, haga clic en el icono **Settings** (Configuración) de la barra de herramientas y seleccione **Services** (Servicios).

![Abrir punto de conexión de servicios](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/services-vsts.PNG)

### <a name="connect-visual-studio-team-services-and-azure-account"></a>Conexión de Visual Studio Team Services y una cuenta de Azure

Configure una conexión entre el proyecto de VSTS y su cuenta de Azure.

1. En el lado izquierdo, haga clic en **Nuevo punto de conexión de servicio** > **Azure Resource Manager**.
2. Para autorizar a VSTS que trabaje con su cuenta de Azure, seleccione su **suscripción** y haga clic en **Aceptar**.

    ![Visual Studio Team Services: autorización a Azure](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-azure.PNG)

### <a name="connect-visual-studio-team-services-and-github"></a>Conexión de Visual Studio Team Services y GitHub

Configure una conexión entre el proyecto de VSTS y su cuenta de GitHub.

1. En el lado izquierdo, haga clic en **New Service Endpoint** > **GitHub** (Nuevo punto de conexión de servicio > GitHub).
2. Para autorizar a VSTS para trabajar con su cuenta de GitHub, haga clic en **Autorizar** y siga el procedimiento en la ventana que se abre.

    ![Visual Studio Team Services: autorización a GitHub](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-github.png)

### <a name="connect-vsts-to-your-azure-container-service-cluster"></a>Conexión de VSTS a un clúster de Azure Container Service

Los últimos pasos antes de acceder a la canalización de CI/CD son configurar conexiones externas a un clúster de Docker Swarm en Azure. 

1. Para el clúster de Docker Swarm, agregue un punto de conexión de tipo **SSH**. Luego, escriba la información de la conexión de SSH de su clúster de Swarm (nodo maestro).

    ![Visual Studio Team Services: SSH](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-ssh.png)

Ahora ya está lista toda la configuración. En los siguientes pasos, se crea la canalización de CI/CD que compila e implementa la aplicación en el clúster de Docker Swarm. 

## <a name="step-2-create-the-build-definition"></a>Paso 2: Creación de la definición de compilación

En este paso, configurará una definición de compilación para su proyecto de VSTS y definirá el flujo de trabajo de compilación de sus imágenes de contenedor

### <a name="initial-definition-setup"></a>Configuración de la definición inicial

1. Para crear una definición de compilación, conéctese al proyecto de Visual Studio Team Services y haga clic en **Build & Release** (Compilación y versión). En la sección **Build definitions** (Definiciones de compilación), haga clic en **+ Nuevo**. 

    ![Visual Studio Team Services: definición de nueva compilación](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/create-build-vsts.PNG)

2. Seleccione el **proceso vacío**.

    ![Visual Studio Team Services: nueva definición de compilación vacía](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/create-empty-build-vsts.PNG)

4. Luego, haga clic en la pestaña **Variables** y cree dos nuevas variables: **RegistryURL** y **AgentURL**. Pegue los valores de su registro y DNS de agentes de clúster.

    ![Visual Studio Team Services configuración de variables de compilación](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-variables.png)

5. En la página **Definiciones de compilación**, abra la pestaña **Desencadenadores** y configure la compilación para la integración continua con la bifurcación del proyecto MyShop que creó en los requisitos previos. Luego, seleccione **Cambios del lote**. Asegúrese de que selecciona *docker-linux* en **Especificación de rama**.

    ![Visual Studio Team Services: configuración del repositorio de compilación](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-github-repo-conf.PNG)


6. Por último, haga clic en la pestaña **Opciones** y en Cola de agente predeterminada, seleccione **Hosted Linux Preview**.

    ![Visual Studio Team Services: configuración del agente de host](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-agent.png)

### <a name="define-the-build-workflow"></a>Definición del flujo de trabajo de compilación
Los pasos siguientes definen el flujo de trabajo de compilación. En primer lugar, es preciso que configure el origen del código. Para hacerlo, seleccione **GitHub** y su **repositorio** y **rama** (docker-linux).

![Visual Studio Team Services: configurar origen del código](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-source-code.png)

Hay cinco imágenes de contenedor para compilar para la aplicación *MyShop*. Cada imagen se compila mediante el Dockerfile ubicado en las carpetas del proyecto:

* ProductsApi
* Proxy
* RatingsApi
* RecommandationsApi
* ShopFront

Necesita dos pasos de Docker para cada imagen, uno para compilarla y otro para insertar la imagen en Azure Registry Container. 

1. Para agregar un paso en el flujo de trabajo de compilación, haga clic en **+ Add build step** (+Agregar paso de compilación) y seleccione **Docker**.

    ![Visual Studio Team Services: adición de pasos de compilación](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-add-task.png)

2. Para cada imagen, configure un paso que use el comando `docker build`.

    ![Visual Studio Team Services: compilación de Docker](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-docker-build.png)

    Para la operación de compilación, seleccione su instancia de Azure Container Registry, la acción **Build an image** (Compilar una imagen) y el Dockerfile que define cada imagen. En **Directorio de trabajo**, seleccione el directorio raíz de Dockerfile, rellene el campo **Nombre de la imagen** y seleccione **Incluir etiqueta " latest"** .
    
    El valor de Nombre de la imagen debe estar en este formato: ```$(RegistryURL)/[NAME]:$(Build.BuildId)```. Reemplace **[NAME]** por el nombre de la imagen:
    - ```proxy```
    - ```products-api```
    - ```ratings-api```
    - ```recommendations-api```
    - ```shopfront```

3. Para cada imagen, configure un segundo paso que use el comando `docker push`.

    ![Visual Studio Team Services: inserción de Docker](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-docker-push.png)

    Para la operación de inserción, seleccione su instancia de Azure Container Registry, la acción **Push an image** (Insertar una imagen), especifique el valor de **Nombre de la imagen** y que se compiló en el paso anterior y seleccione **Incluir etiqueta " latest"**.

4. Después de configurar los pasos de compilación e inserción de cada una de las cinco imágenes, agregue tres pasos más al flujo de trabajo de compilación.

   ![Visual Studio Team Services: adición de una tarea de la línea de comandos](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-command-task.png)

      1. Una tarea de la línea de comandos que usa un script de bash para reemplazar la aparición de *BuildNumber* en el archivo docker-compose.yml por la variable RegistryURL. 
    
          ```-c "sed -i 's/RegistryUrl/$(RegistryURL)/g' src/docker-compose-v3.yml"```

          ![Visual Studio Team Services: actualización del archivo de Compose con la URL del Registro](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-replace-registry.png)

      2. Una tarea de la línea de comandos que usa un script de bash para reemplazar la aparición de *AgentURL* en el archivo docker-compose.yml por la variable AgentURL.
  
          ```-c "sed -i 's/AgentUrl/$(AgentURL)/g' src/docker-compose-v3.yml"```

     3. Una tarea que quita el archivo de Compose actualizado como un artefacto de compilación para que se pueda usar en la versión. Consulte la siguiente pantalla para más información.

         ![Visual Studio Team Services: publicación de artefacto](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-publish.png) 

         ![Visual Studio Team Services: publicación del archivo de Compose](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-publish-compose.png) 

5. Haga clic en **Guardar y poner en cola** para probar la definición de la compilación.

   ![Visual Studio Team Services: guardar y poner en cola](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-save.png) 

   ![Visual Studio Team Services: nueva cola](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-queue.png) 

6. Si la **compilación** es correcta, tiene que verá esta pantalla:

  ![Visual Studio Team Services: compilación correcta](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-succeeded.png) 

## <a name="step-3-create-the-release-definition"></a>Paso 3: Creación de la definición de versión

Visual Studio Team Services le permite [administrar versiones entre entornos](https://www.visualstudio.com/team-services/release-management/). Puede habilitar la implementación continua para asegurarse de que la aplicación se implementa en sus diferentes entornos (por ejemplo, desarrollo, prueba, preproducción y producción) sin problemas. Puede crear un entorno que represente el clúster del modo Docker Swarm de Azure Container Service.

![Visual Studio Team Services: versión para ACS](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-acs.png) 

### <a name="initial-release-setup"></a>Configuración inicial de la versión.

1. Para crear una definición de la versión, haga clic en **Versiones** > **+ Versión**.

2. Para configurar el origen de artefacto, haga clic en **Artefactos** > **Link an artifact source** (Vincular un origen de artefacto). Aquí, vincule esta nueva definición de versión a la compilación definida en el paso anterior. A partir de ese momento, el archivo docker-compose.yml estará disponible en el proceso de versión.

    ![Visual Studio Team Services: artefactos de versión](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-artefacts.png) 

3. Para configurar el desencadenador de versión, haga clic en **Desencadenadores** y seleccione **Implementación continua**. Establezca el desencadenador en el mismo origen de artefacto. Este valor garantiza que cuando finaliza la compilación correctamente, se inicia una nueva versión.

    ![Visual Studio Team Services: desencadenadores de versión](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-trigger.png) 

4. Para configurar las variables de la versión, haga clic en **Variables** y seleccione **+ Variable** para crear tres nuevas variables con la información del registro: **docker.username**, **docker.password** y **docker.registry**. Pegue los valores de su registro y DNS de agentes de clúster.

    ![Visual Studio Team Services: configuración del repositorio de compilación](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-variables.png)

    >[!IMPORTANT]
    > Como se muestra en la pantalla anterior, haga clic en la casilla **Bloquear** de docker.password. Este valor es importante restringir la contraseña.
    >

### <a name="define-the-release-workflow"></a>Definición del flujo de trabajo de versión

El flujo de trabajo de versión se compone de dos tareas que agrega.

1. Configure una tarea para que copie de forma segura el archivo de Compose en una carpeta de *implementación* en el nodo maestro de Docker Swarm, mediante la conexión SSH configurada anteriormente. Consulte la siguiente pantalla para más información.
    
    Carpeta de origen:```$(System.DefaultWorkingDirectory)/MyShop-CI/drop```

    ![Visual Studio Team Services: SCP de versión](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-scp.png)

2. Configure una segunda tarea para que ejecute un comando de bash para ejecutar los comandos `docker` y `docker stack deploy` en el nodo maestro. Consulte la siguiente pantalla para más información.

    ```docker login -u $(docker.username) -p $(docker.password) $(docker.registry) && export DOCKER_HOST=:2375 && cd deploy && docker stack deploy --compose-file docker-compose-v3.yml myshop --with-registry-auth```

    ![Visual Studio Team Services: bash de versión](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-bash.png)

    El comando ejecutado en el maestro usa la CLI de Docker y la CLI de Docker-Compose para realizar las siguientes tareas:

    - Inicie sesión en Azure Container Registry (usa tres variables de compilación que se definen en la pestaña **Variables**)
    - Defina la variable **DOCKER_HOST** para que funcione con el punto de conexión de Swarm (:2375)
    - Vaya a la carpeta de *implementación* que se creó en la tarea de copia segura anterior y que contiene el archivo docker-compose.yml. 
    - Ejecute comandos `docker stack deploy` que extraigan las nuevas imágenes y creen los contenedores.

    >[!IMPORTANT]
    > Como se muestra en la pantalla anterior, deje la casilla **Error en STDERR** desactivada. Este valor nos permite completar el proceso de lanzamiento, porque `docker-compose` imprime varios mensajes de diagnóstico (por ejemplo, los contenedores se están deteniendo o se están eliminando), en la salida de error estándar. Si activa la casilla, Visual Studio Team Services informa de que se produjeron errores durante la versión, incluso si todo va bien.
    >
3. Guarde esta nueva definición de versión.

## <a name="step-4-test-the-cicd-pipeline"></a>Paso 4: Prueba de la canalización de CI/CD

Ahora que ha terminado con la configuración, es hora de probar esta nueva canalización de CI/CD. La manera más fácil de hacerlo es actualizar el código fuente y confirmar los cambios en el repositorio de GitHub. Unos pocos segundos después de insertar el código, verá una nueva compilación que se ejecuta en Visual Studio Team Services. Una vez completada correctamente, se desencadena una nueva versión de la aplicación, que se implementa en el clúster de Azure Container Service.

## <a name="next-steps"></a>Pasos siguientes

* Para más información sobre CI/CD con Visual Studio Team Services, consulte la [introducción a la compilación de VSTS](https://www.visualstudio.com/docs/build/overview).
* Para más información acerca de ACS Engine, consulte el [repositorio de GitHub de ACS Engine](https://github.com/Azure/acs-engine).
* Para más información acerca del modo de Docker Swarm, consulte la [introducción al modo de Docker Swarm](https://docs.docker.com/engine/swarm/).
