---
title: "Configuración del entorno de desarrollo en Mac OS X para trabajar con Azure Service Fabric | Microsoft Docs"
description: "Instale las herramientas, el SDK y el motor en tiempo de ejecución y cree un clúster de desarrollo local. Después de completar esta instalación, estará listo para compilar aplicaciones en Mac OS X."
services: service-fabric
documentationcenter: java
author: sayantancs
manager: timlt
editor: 
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: java
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/17/2017
ms.author: saysa
ms.openlocfilehash: 309fcb901a1a3878edbfbe06e12122615b74664e
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/23/2017
---
# <a name="set-up-your-development-environment-on-mac-os-x"></a>Configuración de su entorno de desarrollo en Mac OS X
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
>
>  

Puede compilar aplicaciones de Service Fabric para que se ejecuten en clústeres de Linux con Mac OS X. En este documento se describe cómo configurar su Mac para desarrollo.

## <a name="prerequisites"></a>Requisitos previos
Service Fabric no se ejecuta de forma nativa en OS X. Para ejecutar un clúster local de Service Fabric, se proporciona una imagen preconfigurada de contenedor de Docker. Antes de comenzar, necesita:

* Al menos 4 GB de RAM
* Versión más reciente de [Docker](https://www.docker.com/)
* Acceso a la [imagen](https://hub.docker.com/r/servicefabricoss/service-fabric-onebox/) del contenedor de Docker todo en uno de Service Fabric

>[!TIP]
> * Puede seguir los pasos mencionados en la [documentación](https://docs.docker.com/docker-for-mac/install/#what-to-know-before-you-install) oficial de Docker para instalar Docker en el equipo Mac. 
> * Una vez que haya terminado la instalación, compruebe si se instaló correctamente siguiendo los pasos mencionados [aquí](https://docs.docker.com/docker-for-mac/#check-versions-of-docker-engine-compose-and-machine)


## <a name="create-a-local-container-and-setup-service-fabric"></a>Creación de un contenedor local e instalación de Service Fabric
Para configurar un contenedor local de Docker y hacer que un clúster de Service Fabric se ejecute en él, realice los pasos siguientes:

1. Extraiga la imagen del repositorio central de Docker:

    ```bash
    docker pull servicefabricoss/service-fabric-onebox
    ```

2. Actualice la configuración del demonio de Docker en el host con lo siguiente y reinicie dicho demonio: 

    ```json
    {
        "ipv6": true,
        "fixed-cidr-v6": "fd00::/64"
    }
    ```
    Se puede actualizar directamente esto en daemon.json en la ruta de acceso de instalación de Docker (cuya ubicación puede variar de una máquina a otra, por ejemplo, ~/Library/Containers/com.docker.docker/Data/database/com.docker.driver.amd64-linux/etc/docker/daemon.json). La manera aconsejable de actualizar es ir al icono de Docker > Preferencias > Demonio > Avanzado y realizar allí la actualización.

3. Inicie una instancia del contenedor todo en uno de Service Fabric con la imagen:

    ```bash
    docker run -itd -p 19080:19080 --name sfonebox servicefabricoss/service-fabric-onebox
    ```
    >[!TIP]
    > * Al especificar un nombre para la instancia del contenedor, puede tratarlo de forma más legible. 
    > * Si la aplicación está escuchando en determinados puertos, debe especificarse mediante etiquetas -p adicionales. Por ejemplo, si la aplicación está escuchando en el puerto 8080, ejecute docker run -itd -p 19080:19080 -p 8080:8080 --name sfonebox servicefabricoss/service-fabric-onebox.

4. Inicie sesión en el contenedor de Docker en el modo interactivo ssh:

    ```bash
    docker exec -it sfonebox bash
    ```

5. Ejecute el script de instalación, que recuperará las dependencias necesarias, y después inicie el clúster en el contenedor.

    ```bash
    ./setup.sh     # Fetches and installs the dependencies required for Service Fabric to run
    ./run.sh       # Starts the local cluster
    ```

6. Tras completar correctamente el paso 5, puede ir a ``http://localhost:19080`` desde su equipo Mac y puede ver Service Fabric Explorer.

## <a name="set-up-the-service-fabric-cli-sfctl-on-your-mac"></a>Configuración de la CLI de Service Fabric (sfctl) en un Mac

Para instalar la CLI de Service Fabric (`sfctl`) en un Mac, siga las instrucciones que encontrará en [CLI de Service Fabric](service-fabric-cli.md#cli-mac).
Los comandos de la CLI para interactuar con las entidades de Service Fabric, lo que incluye clústeres, aplicaciones y servicios.

## <a name="create-application-on-your-mac-using-yeoman"></a>Creación de una aplicación en un equipo Mac mediante Yeoman

Service Fabric proporciona herramientas de scaffolding que le ayudan a crear una aplicación de Service Fabric desde el terminal mediante el generador de plantillas Yeoman. Siga los pasos siguientes para asegurarse de que el generador de plantillas Yeoman de Service Fabric está en funcionamiento en la máquina.

1. Debe tener Node.js y NPM instalados en el equipo Mac. Si no los tiene, puede instalarlos mediante Homebrew haciendo lo siguiente:

    ```bash
    brew install node
    node -v
    npm -v
    ```
2. Instale el generador de plantillas [Yeoman](http://yeoman.io/) en la máquina desde NPM.

    ```bash
    npm install -g yo
    ```
3. Para instalar el generador Yeoman que desea usar, siga los pasos descritos en la [documentación](service-fabric-get-started-linux.md) de introducción. Para crear aplicaciones de Service Fabric mediante Yeoman, siga estos pasos:

    ```bash
    npm install -g generator-azuresfjava       # for Service Fabric Java Applications
    npm install -g generator-azuresfguest      # for Service Fabric Guest executables
    npm install -g generator-azuresfcontainer  # for Service Fabric Container Applications
    ```
4. Para compilar una aplicación Java de Service Fabric en Mac, necesita tener JDK 1.8 y Gradle instalados en el equipo. Si no los tiene, puede instalarlos mediante [HomeBrew](https://brew.sh/). 

    ```bash
    brew update
    brew cask install java
    brew install gradle
    ```

## <a name="deploy-application-on-your-mac-from-terminal"></a>Implementación de la aplicación en el equipo Mac desde el terminal

Una vez que cree y compile la aplicación de Service Fabric, puede implementar su aplicación mediante la [CLI de Service Fabric](service-fabric-cli.md#cli-mac), con los siguientes pasos:

1. Conéctese al clúster de Service Fabric que se ejecuta dentro de la instancia del contenedor en el equipo Mac.

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```

2. Vaya al directorio del proyecto y ejecute el script de instalación.

    ```bash
    cd MyProject
    bash install.sh
    ```

## <a name="set-up-net-core-20-development"></a>Configuración del desarrollo con .NET Core 2.0

Instale el [SDK de .NET Core 2.0 para Mac](https://www.microsoft.com/net/core#macos) para iniciar la [creación de aplicaciones de Service Fabric en C#](service-fabric-create-your-first-linux-application-with-csharp.md). Los paquetes de aplicaciones de Service Fabric en .NET Core 2.0 se hospedan en NuGet.org, actualmente en versión preliminar.

## <a name="install-the-service-fabric-plugin-for-eclipse-neon-on-your-mac"></a>Instalación del complemento de Service Fabric para Eclipse Neon en un equipo Mac

Service Fabric proporciona un complemento para **IDE de Java para Eclipse Neon** que puede simplificar el proceso de creación, compilación e implementación de servicios de Java. Puede seguir los pasos de instalación que se indican en esta [documentación](service-fabric-get-started-eclipse.md#install-or-update-the-service-fabric-plug-in-in-eclipse-neon) general acerca de cómo instalar o actualizar el complemento de Eclipse para Service Fabric a la versión más reciente.

Todos los demás pasos mencionados en la [documentación de Eclipse para Service Fabric](service-fabric-get-started-eclipse.md) para compilar una aplicación, agregar el servicio a la aplicación, instalar o desinstalar aplicaciones, etc. se pueden aplicar aquí también.

Además de los pasos anteriores, para que el complemento de Eclipse para Service Fabric funcione con el contenedor de Docker en el equipo Mac, debe crear el una instancia del contenedor con una ruta de acceso compartida con el host, como se indica a continuación:
```bash
docker run -itd -p 19080:19080 -v /Users/sayantan/work/workspaces/mySFWorkspace:/tmp/mySFWorkspace --name sfonebox servicefabricoss/service-fabric-onebox
```
donde ``/Users/sayantan/work/workspaces/mySFWorkspace`` es la ruta de acceso completa del área de trabajo en el equipo Mac y ``/tmp/mySFWorkspace`` es la ruta de acceso dentro del contenedor, donde sería asignado.

> [!NOTE]
>1. Si el nombre y la ruta de acceso del área de trabajo son diferentes, actualice el mismo valor como corresponda en el comando anterior ``docker run``.
>2. Si inicia el contenedor con otro nombre distinto de ``sfonebox``, actualice el mismo valor en el archivo ``testclient.sh`` en su aplicación Java del actor de Service Fabric.

## <a name="next-steps"></a>Pasos siguientes
<!-- Links -->
* [Creación e implementación de la primera aplicación de Java para Service Fabric en Linux con Yeoman](service-fabric-create-your-first-linux-application-with-java.md)
* [Creación e implementación de la primera aplicación de Java para Service Fabric con el complemento de Eclipse para Service Fabric](service-fabric-get-started-eclipse.md)
* [Configuración de un clúster de Service Fabric en Azure Portal](service-fabric-cluster-creation-via-portal.md)
* [Creación de un clúster de Service Fabric en Azure mediante Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
* [Entender el modelo de aplicación de Service Fabric](service-fabric-application-model.md)
* [Uso de la CLI de Service Fabric para administrar las aplicaciones](service-fabric-application-lifecycle-sfctl.md)
* [Preparación del entorno de desarrollo Linux en Windows](service-fabric-local-linux-cluster-windows.md)

<!-- Images -->
[cluster-setup-script]: ./media/service-fabric-get-started-mac/cluster-setup-mac.png
[sfx-mac]: ./media/service-fabric-get-started-mac/sfx-mac.png
[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-mac/sf-eclipse-plugin-install.png
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship