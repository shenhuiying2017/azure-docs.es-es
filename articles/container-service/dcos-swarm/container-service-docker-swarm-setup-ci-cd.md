---
title: CI/CD con Azure Container Service y Swarm
description: "Uso de Azure Container Service con Docker Swarm, Azure Container Registry y Visual Studio Team Services para proporcionar de forma continua de una aplicación .NET Core de varios contenedores."
services: container-service
author: jcorioland
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 12/08/2016
ms.author: jucoriol
ms.custom: mvc
ms.openlocfilehash: 38877afb63e993eeaab723a6ea5f4c40d3c956a5
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/06/2017
---
# <a name="full-cicd-pipeline-to-deploy-a-multi-container-application-on-azure-container-service-with-docker-swarm-using-visual-studio-team-services"></a>Canalización completa de CI/CD para implementar una aplicación de varios contenedores en Azure Container Service con Docker Swarm mediante Visual Studio Team Services

Uno de los mayores desafíos al desarrollar aplicaciones modernas para la nube es la posibilidad de proporcionar estas aplicaciones de forma continua. En este artículo, aprenderá a implementar una canalización completa de integración e implementación (CI/CD) continuas mediante Azure Container Service con la administración de compilaciones y versiones de Docker Swarm, Azure Container Registry, y Visual Studio Team Services.

Este artículo se basa en una aplicación sencilla, disponible en [GitHub](https://github.com/jcorioland/MyShop/tree/acs-docs), desarrollada con ASP.NET Core. La aplicación se compone de cuatro servicios diferentes: tres API web y un front-end web:

![Aplicación de ejemplo MyShop](./media/container-service-docker-swarm-setup-ci-cd/myshop-application.png)

El objetivo es entregar continuamente esta aplicación en un clúster de Docker Swarm, mediante Visual Studio Team Services. La ilustración siguiente describe esta canalización de entrega continua:

![Aplicación de ejemplo MyShop](./media/container-service-docker-swarm-setup-ci-cd/full-ci-cd-pipeline.png)

Esta es una breve explicación de los pasos:

1. Los cambios de código se realizan en el repositorio de código fuente (aquí, GitHub). 
2. GitHub desencadena una compilación en Visual Studio Team Services. 
3. Visual Studio Team Services obtiene la versión más reciente del código fuente y compila todas las imágenes que componen la aplicación. 
4. Visual Studio Team Services inserta cada imagen en un registro de Docker que se creó mediante el servicio Azure Container Registry. 
5. Visual Studio Team Services desencadena una nueva versión. 
6. La versión ejecuta algunos comandos mediante SSH en el nodo maestro del clúster de Azure Container Service. 
7. Docker Swarm en el clúster extrae la última versión de las imágenes. 
8. La nueva versión de la aplicación se implementa con Docker Compose. 

## <a name="prerequisites"></a>Requisitos previos

Antes de comenzar este tutorial, debe realizar las siguientes tareas:

- [Crear un clúster de Swarm en Azure Container Service (servicio Contenedor de Azure)](container-service-deployment.md)
- [Conectar con el clúster de Swarm en Azure Container Service (servicio Contenedor de Azure)](../container-service-connect.md)
- [Crear una instancia de Azure Container Registry](../../container-registry/container-registry-get-started-portal.md)
- [Crear una cuenta y un proyecto de equipo de Visual Studio Team Services](https://www.visualstudio.com/en-us/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services)
- [Bifurcar el repositorio de GitHub a su cuenta de GitHub](https://github.com/jcorioland/MyShop/)

[!INCLUDE [container-service-swarm-mode-note](../../../includes/container-service-swarm-mode-note.md)]

También se necesita una máquina Ubuntu (14.04 o 16.04) que tenga instalado Docker. Visual Studio Team Services usa esta máquina durante los procesos de compilación y versión. Una manera de crear esta máquina es usar la imagen disponible en [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/canonicalandmsopentech/dockeronubuntuserver1404lts/). 

## <a name="step-1-configure-your-visual-studio-team-services-account"></a>Paso 1: Configuración de la cuenta de Visual Studio Team Services 

En esta sección, configurará su cuenta de Visual Studio Team Services.

### <a name="configure-a-visual-studio-team-services-linux-build-agent"></a>Configuración de un agente de compilación de Linux de Visual Studio Team Services

Para crear imágenes de Docker e insertarlas en una instancia de Azure Container Registry desde una compilación de Visual Studio Team Services, debe registrar un agente de Linux. Dispone de estas opciones de instalación:

* [Implementar un agente en Linux](https://www.visualstudio.com/docs/build/admin/agents/v2-linux)

* [Usar Docker para ejecutar al agente de VSTS](https://hub.docker.com/r/microsoft/vsts-agent)

### <a name="install-the-docker-integration-vsts-extension"></a>Instalar la extensión de VSTS de integración con Docker

Microsoft proporciona una extensión de VSTS para trabajar con Docker en los procesos de compilación y versión. Esta extensión está disponible en [Marketplace de VSTS](https://marketplace.visualstudio.com/items?itemName=ms-vscs-rm.docker). Haga clic en **Instalar** para agregar esta extensión a su cuenta de VSTS:

![Instalar la integración con Docker](./media/container-service-docker-swarm-setup-ci-cd/install-docker-vsts.png)

Se le pedirá que se conecte a su cuenta de VSTS con sus credenciales. 

### <a name="connect-visual-studio-team-services-and-github"></a>Conexión de Visual Studio Team Services y GitHub

Configure una conexión entre el proyecto de VSTS y su cuenta de GitHub.

1. En el proyecto de Visual Studio Team Services, haga clic en el icono **Configuración** de la barra de herramientas y seleccione **Servicios**.

    ![Visual Studio Team Services: conexión externa](./media/container-service-docker-swarm-setup-ci-cd/vsts-services-menu.png)

2. En el lado izquierdo, haga clic en **New Service Endpoint** > **GitHub** (Nuevo punto de conexión de servicio > GitHub).

    ![Visual Studio Team Services: GitHub](./media/container-service-docker-swarm-setup-ci-cd/vsts-github.png)

3. Para autorizar a VSTS para trabajar con su cuenta de GitHub, haga clic en **Autorizar** y siga el procedimiento en la ventana que se abre.

    ![Visual Studio Team Services: autorización a GitHub](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-authorize.png)

### <a name="connect-vsts-to-your-azure-container-registry-and-azure-container-service-cluster"></a>Conexión de VSTS a su instancia de Azure Container Registry y al clúster de Azure Container Service

Los últimos pasos antes de entrar en la canalización de CI/CD son configurar conexiones externas al registro de contenedores y al clúster de Docker Swarm en Azure. 

1. En la configuración **Servicios** de su proyecto de Visual Studio Team Services, agregue un punto de conexión de servicio de tipo **Docker Registry**. 

2. En la ventana emergente que se abre, escriba la dirección URL y las credenciales de su instancia de Azure Container Registry.

    ![Visual Studio Team Services: Docker Registry](./media/container-service-docker-swarm-setup-ci-cd/vsts-registry.png)

3. Para el clúster de Docker Swarm, agregue un punto de conexión de tipo **SSH**. A continuación, escriba la información de conexión de SSH de su clúster de Swarm.

    ![Visual Studio Team Services: SSH](./media/container-service-docker-swarm-setup-ci-cd/vsts-ssh.png)

Ahora ya está lista toda la configuración. En los siguientes pasos, se crea la canalización de CI/CD que compila e implementa la aplicación en el clúster de Docker Swarm. 

## <a name="step-2-create-the-build-definition"></a>Paso 2: Creación de la definición de compilación

En este paso, configurará una definición de compilación para su proyecto de VSTS y definirá el flujo de trabajo de compilación de sus imágenes de contenedor.

### <a name="initial-definition-setup"></a>Configuración de la definición inicial

1. Para crear una definición de compilación, conéctese al proyecto de Visual Studio Team Services y haga clic en **Build & Release** (Compilación y versión). 

2. En la sección **Build definitions** (Definiciones de compilación), haga clic en **+ Nuevo**. Seleccione la plantilla **Vacía**.

    ![Visual Studio Team Services: definición de nueva compilación](./media/container-service-docker-swarm-setup-ci-cd/create-build-vsts.png)

3. Configure la nueva compilación con un origen de repositorio de GitHub, marque **Integración continua** y seleccione la cola de agentes donde registró el agente de Linux. Haga clic en **Crear** para crear la definición de compilación.

    ![Visual Studio Team Services: creación de la definición de compilación](./media/container-service-docker-swarm-setup-ci-cd/vsts-create-build-github.png)

4. En la página **Build Definitions** (Definiciones de compilación), abra primero la pestaña **Repositorio** y configure la compilación para usar la bifurcación del proyecto MyShop que creó en los requisitos previos. Asegúrese de que selecciona *acs-docs* como la **bifurcación predeterminada**.

    ![Visual Studio Team Services: configuración del repositorio de compilación](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-repo-conf.png)

5. En la pestaña **Desencadenadores**, configure la compilación que se desencadenará después de cada confirmación. Seleccione **Integración continua** y **Batch changes** (Cambios en lote).

    ![Visual Studio Team Services: configuración del desencadenador de compilación](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-trigger-conf.png)

### <a name="define-the-build-workflow"></a>Definición del flujo de trabajo de compilación
Los pasos siguientes definen el flujo de trabajo de compilación. Hay cinco imágenes de contenedor para compilar para la aplicación *MyShop*. Cada imagen se compila mediante el Dockerfile ubicado en las carpetas del proyecto:

* ProductsApi
* Proxy
* RatingsApi
* RecommandationsApi
* ShopFront

Deberá agregar dos pasos de Docker para cada imagen, uno para compilar la imagen y otro para insertar la imagen en Azure Registry Container. 

1. Para agregar un paso en el flujo de trabajo de compilación, haga clic en **+ Add build step** (+Agregar paso de compilación) y seleccione **Docker**.

    ![Visual Studio Team Services: adición de pasos de compilación](./media/container-service-docker-swarm-setup-ci-cd/vsts-build-add-task.png)

2. Para cada imagen, configure un paso que use el comando `docker build`.

    ![Visual Studio Team Services: compilación de Docker](./media/container-service-docker-swarm-setup-ci-cd/vsts-docker-build.png)

    Para la operación de compilación, seleccione su instancia de Azure Container Registry, la acción **Build an image** (Compilar una imagen) y el Dockerfile que define cada imagen. Establezca el **contexto de compilación** como el directorio raíz y defina el **nombre de la imagen**. 
    
    Como se ha mostrado en la pantalla anterior, inicie el nombre de la imagen con el URI de Azure Container Registry. (También puede usar una variable de compilación para parametrizar la etiqueta de la imagen, por ejemplo, el identificador de la compilación en este ejemplo).

3. Para cada imagen, configure un segundo paso que use el comando `docker push`.

    ![Visual Studio Team Services: inserción de Docker](./media/container-service-docker-swarm-setup-ci-cd/vsts-docker-push.png)

    Para la operación de inserción, seleccione su instancia de Azure Container Registry, la acción **Push an image** (Insertar una imagen) y escriba el **nombre de la imagen** que se compiló en el paso anterior.

4. Después de configurar los pasos de compilación e inserción para cada una de las cinco imágenes, agregue dos pasos más en el flujo de trabajo de compilación.

    a. Una tarea de la línea de comandos que usa un script de bash para reemplazar la aparición de *BuildNumber* en el archivo docker-compose.yml por el identificador de la compilación actual. Consulte la siguiente pantalla para más información.

    ![Visual Studio Team Services: actualización de archivo de Compose](./media/container-service-docker-swarm-setup-ci-cd/vsts-build-replace-build-number.png)

    b. Una tarea que quita el archivo de Compose actualizado como un artefacto de compilación para que se pueda usar en la versión. Consulte la siguiente pantalla para más información.

    ![Visual Studio Team Services: publicación del archivo de Compose](./media/container-service-docker-swarm-setup-ci-cd/vsts-publish-compose.png) 

5. Haga clic en **Guardar** y asigne un nombre a la definición de compilación.

## <a name="step-3-create-the-release-definition"></a>Paso 3: Creación de la definición de versión

Visual Studio Team Services le permite [administrar versiones entre entornos](https://www.visualstudio.com/team-services/release-management/). Puede habilitar la implementación continua para asegurarse de que la aplicación se implementa en sus diferentes entornos (por ejemplo, desarrollo, prueba, preproducción y producción) sin problemas. Puede crear un nuevo entorno que representa el clúster de Docker Swarm de Azure Container Service.

![Visual Studio Team Services: versión para ACS](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-acs.png) 

### <a name="initial-release-setup"></a>Configuración inicial de la versión.

1. Para crear una definición de la versión, haga clic en **Versiones** > **+ Versión**.

2. Para configurar el origen de artefacto, haga clic en **Artefactos** > **Link an artifact source** (Vincular un origen de artefacto). Aquí, vincule esta nueva definición de versión a la compilación definida en el paso anterior. De esta forma, el archivo docker-compose.yml estará disponible en el proceso de versión.

    ![Visual Studio Team Services: artefactos de versión](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-artefacts.png) 

3. Para configurar el desencadenador de versión, haga clic en **Desencadenadores** y seleccione **Implementación continua**. Establezca el desencadenador en el mismo origen de artefacto. Este valor garantiza que tan pronto como finaliza la compilación correctamente, se inicia una nueva versión.

    ![Visual Studio Team Services: desencadenadores de versión](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-trigger.png) 

### <a name="define-the-release-workflow"></a>Definición del flujo de trabajo de versión

El flujo de trabajo de versión se compone de dos tareas que agrega.

1. Configure una tarea para que copie de forma segura el archivo de Compose en una carpeta de *implementación* en el nodo maestro de Docker Swarm, mediante la conexión SSH configurada anteriormente. Consulte la siguiente pantalla para más información.

    ![Visual Studio Team Services: SCP de versión](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-scp.png)

2. Configure una segunda tarea para que ejecute un comando de bash para ejecutar los comandos `docker` y `docker-compose` en el nodo maestro. Consulte la siguiente pantalla para más información.

    ![Visual Studio Team Services: bash de versión](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-bash.png)

    El comando ejecutado en el nodo maestro usa la CLI de Docker y la CLI de Docker-Compose para realizar las siguientes tareas:

    - Inicie sesión en Azure Container Registry (usa tres variables de compilación que se definen en la pestaña **Variables**).
    - Defina la variable **DOCKER_HOST** para que funcione con el punto de conexión de Swarm (:2375)
    - Vaya a la carpeta de *implementación* que se creó en la tarea de copia segura anterior y que contiene el archivo docker-compose.yml. 
    - Ejecute comandos `docker-compose` que extraigan las nuevas imágenes, detengan los servicios, quiten los servicios y creen los contenedores.

    >[!IMPORTANT]
    > Como se muestra en la pantalla anterior, deje la casilla **Fail on STDERR** (Error en STDERR) desactivada. Se trata de una opción importante, porque `docker-compose` imprime varios mensajes de diagnóstico (por ejemplo, los contenedores se están deteniendo o se están eliminando), en la salida de error estándar. Si activa la casilla, Visual Studio Team Services informa de que se produjeron errores durante la versión, incluso si todo va bien.
    >
3. Guarde esta nueva definición de versión.


>[!NOTE]
>Esta implementación incluye algún tiempo de inactividad porque se detendrán los servicios antiguos y se ejecutará el nuevo. Para evitar esto, realice una implementación Blue-Green.
>

## <a name="step-4-test-the-cicd-pipeline"></a>Paso 4 Prueba de la canalización de CI/CD

Ahora que ha terminado con la configuración, es hora de probar esta nueva canalización de CI/CD. La manera más fácil de hacerlo es actualizar el código fuente y confirmar los cambios en el repositorio de GitHub. Unos pocos segundos después de insertar el código, verá una nueva compilación que se ejecuta en Visual Studio Team Services. Una vez completada correctamente, se desencadenará una nueva versión y se implementará la nueva versión de la aplicación en el clúster de Azure Container Service.

## <a name="next-steps"></a>Pasos siguientes

* Para más información sobre CI/CD con Visual Studio Team Services, consulte la [introducción a la compilación de VSTS](https://www.visualstudio.com/docs/build/overview).
