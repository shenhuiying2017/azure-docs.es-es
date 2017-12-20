---
title: "Configuración del entorno de desarrollo en Mac OS X para trabajar con Azure Service Fabric | Microsoft Docs"
description: "Instale las herramientas, el SDK y el motor en tiempo de ejecución y cree un clúster de desarrollo local. Después de completar esta instalación, estará listo para crear aplicaciones en Mac OS X."
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
ms.openlocfilehash: 328b2778a68e32d95b666124bf7bba969a5f52a6
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/18/2017
---
# <a name="set-up-your-development-environment-on-mac-os-x"></a>Configuración de su entorno de desarrollo en Mac OS X
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
>
>  

Puede crear aplicaciones de Azure Service Fabric para que se ejecuten en clústeres de Linux con Mac OS X. En este documento se describe cómo configurar su Mac para desarrollo.

## <a name="prerequisites"></a>Requisitos previos
Azure Service Fabric no se ejecuta de forma nativa en Mac OS X. Para ejecutar un clúster local de Service Fabric, se proporciona una imagen preconfigurada del contenedor de Docker. Antes de comenzar, necesita:

* Al menos 4 GB de RAM.
* Versión más reciente de [Docker](https://www.docker.com/).
* Acceso a la [imagen del contenedor de Docker todo en uno](https://hub.docker.com/r/servicefabricoss/service-fabric-onebox/) de Service Fabric.

>[!TIP]
>
>Para instalar Docker en el equipo Mac, siga los pasos mencionados en la [documentación de Docker](https://docs.docker.com/docker-for-mac/install/#what-to-know-before-you-install). Después de instalar, [compruebe la instalación](https://docs.docker.com/docker-for-mac/#check-versions-of-docker-engine-compose-and-machine).
>

## <a name="create-a-local-container-and-set-up-service-fabric"></a>Creación de un contenedor local e instalación de Service Fabric
Para configurar un contenedor local de Docker y hacer que un clúster de Service Fabric se ejecute en él, realice los pasos siguientes:

1. Extraiga la imagen del contenedor onebox de Service Fabric desde el repositorio central de Docker:

    ```bash
    docker pull servicefabricoss/service-fabric-onebox
    ```

2. Actualice la configuración del demonio de Docker en el host con las opciones siguientes y reinicie dicho demonio: 

    ```json
    {
        "ipv6": true,
        "fixed-cidr-v6": "fd00::/64"
    }
    ```
    Puede actualizar estos valores directamente en el archivo daemon.json en la ruta de acceso de instalación de Docker.
    
    >[!NOTE]
    >
    >La ubicación del archivo daemon.json puede variar según la máquina. Por ejemplo, ~/Library/Containers/com.docker.docker/Data/database/com.docker.driver.amd64-linux/etc/docker/daemon.json.
    >
    >La opción recomendada es modificar directamente los valores de configuración del demonio en Docker. Seleccione el **icono de Docker** y, a continuación, seleccione **Preferencias** > **Daemon** > **Avanzadas**.
    >

3. Inicie una instancia del contenedor onebox de Service Fabric y use la imagen que extrajo en el primer paso:

    ```bash
    docker run -itd -p 19080:19080 --name sfonebox servicefabricoss/service-fabric-onebox
    ```
    >[!TIP]
    >Al especificar un nombre para la instancia del contenedor, puede tratarlo de forma más legible. 
    >
    >Si la aplicación está escuchando en determinados puertos, estos deben especificarse mediante etiquetas `-p` adicionales. Por ejemplo, si la aplicación está escuchando en el puerto 8080, agregue la siguiente etiqueta `-p`:
    >
    >`run docker run -itd -p 19080:19080 -p 8080:8080 --name sfonebox servicefabricoss/service-fabric-onebox`
    >

4. Inicie sesión en el contenedor de Docker en el modo interactivo SSH:

    ```bash
    docker exec -it sfonebox bash
    ```

5. Ejecute el script de instalación para recuperar las dependencias necesarias y después inicie el clúster en el contenedor:

    ```bash
    ./setup.sh     # Fetches and installs the dependencies required for Service Fabric to run
    ./run.sh       # Starts the local cluster
    ```

6. Una vez completado el paso 5, vaya a `http://localhost:19080` desde el equipo Mac. Debería ver el explorador de Service Fabric.

## <a name="set-up-the-service-fabric-cli-sfctl-on-your-mac"></a>Configuración de la CLI de Service Fabric (sfctl) en un Mac

Para instalar la CLI de Service Fabric (`sfctl`) en un Mac, siga las instrucciones que encontrará en [CLI de Service Fabric](service-fabric-cli.md#cli-mac).
Los comandos de la CLI permiten interactuar con las entidades de Service Fabric, lo que incluye clústeres, aplicaciones y servicios.

## <a name="create-your-application-on-your-mac-by-using-yeoman"></a>Creación de una aplicación en un equipo Mac mediante Yeoman

Service Fabric proporciona herramientas de scaffolding que le ayudarán a crear una aplicación de Service Fabric desde el terminal mediante el generador de plantillas Yeoman. Realice los pasos siguientes para asegurarse de que el generador de plantillas Yeoman de Service Fabric está en funcionamiento en la máquina:

1. Node.js y el administrador de paquetes de nodo (NPM) deben estar instalados en el equipo Mac. El software puede instalarse mediante [HomeBrew](https://brew.sh/), como se indica a continuación:

    ```bash
    brew install node
    node -v
    npm -v
    ```
2. Instale el generador de plantillas [Yeoman](http://yeoman.io/) en la máquina desde NPM:

    ```bash
    npm install -g yo
    ```
3. Para instalar el generador Yeoman que desea usar, siga los pasos descritos en la [documentación](service-fabric-get-started-linux.md) de introducción. Para crear aplicaciones de Service Fabric mediante Yeoman, siga estos pasos:

    ```bash
    npm install -g generator-azuresfjava       # for Service Fabric Java Applications
    npm install -g generator-azuresfguest      # for Service Fabric Guest executables
    npm install -g generator-azuresfcontainer  # for Service Fabric Container Applications
    ```
4. Para compilar una aplicación Java de Service Fabric en Mac, JDK versión 1.8 y Gradle deberían estar instalados en la máquina host. El software puede instalarse mediante [HomeBrew](https://brew.sh/), como se indica a continuación: 

    ```bash
    brew update
    brew cask install java
    brew install gradle
    ```

## <a name="deploy-your-application-on-your-mac-from-the-terminal"></a>Implementación de la aplicación en el equipo Mac desde el terminal

Una vez que cree y compile la aplicación de Service Fabric, puede implementar su aplicación mediante la [CLI de Service Fabric](service-fabric-cli.md#cli-mac):

1. Conéctese al clúster de Service Fabric que se ejecuta dentro de la instancia del contenedor en el equipo Mac:

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```

2. Vaya al directorio del proyecto y ejecute el script de instalación:

    ```bash
    cd MyProject
    bash install.sh
    ```

## <a name="set-up-net-core-20-development"></a>Configuración del desarrollo con .NET Core 2.0

Instale el [SDK de .NET Core 2.0 para Mac](https://www.microsoft.com/net/core#macos) para iniciar la [creación de aplicaciones de Service Fabric en C#](service-fabric-create-your-first-linux-application-with-csharp.md). Los paquetes de aplicaciones de Service Fabric en .NET Core 2.0 se hospedan en NuGet.org, actualmente en versión preliminar.

## <a name="install-the-service-fabric-plug-in-for-eclipse-neon-on-your-mac"></a>Instalación del complemento de Service Fabric para Eclipse Neon en un equipo Mac

Azure Service Fabric proporciona un complemento de Eclipse Neon para el entorno de desarrollo de Java. El complemento simplifica el proceso de creación, compilación e implementación de servicios de Java. Para instalar la versión más reciente del complemento de Service Fabric para Eclipse o actualizar a dicha versión, siga [estos pasos](service-fabric-get-started-eclipse.md#install-or-update-the-service-fabric-plug-in-in-eclipse-neon). Los otros pasos de la [documentación de Service Fabric para Eclipse](service-fabric-get-started-eclipse.md) también son aplicables: compilar una aplicación, agregar un servicio a una aplicación, desinstalar una aplicación, etcétera.

El último paso es crear una instancia del contenedor con una ruta de acceso que se comparte con el host. El complemento requiere este tipo de creación de instancias para funcionar con el contenedor de Docker en el equipo Mac. Por ejemplo:

```bash
docker run -itd -p 19080:19080 -v /Users/sayantan/work/workspaces/mySFWorkspace:/tmp/mySFWorkspace --name sfonebox servicefabricoss/service-fabric-onebox
```

Los atributos se definen de la manera siguiente:
* `/Users/sayantan/work/workspaces/mySFWorkspace` es la ruta de acceso completa del área de trabajo en el equipo Mac.
* `/tmp/mySFWorkspace` es la ruta de acceso que está dentro del contenedor al que se debe asignar el área de trabajo.

>[!NOTE]
> 
>Si tiene un nombre o ruta de acceso diferente para el área de trabajo, actualice estos valores con el comando `docker run`.
> 
>Si inicia el contenedor con otro nombre distinto de `sfonebox`, actualice el mismo valor en el archivo testclient.sh, en su aplicación Java del actor de Service Fabric.
>

## <a name="next-steps"></a>Pasos siguientes
<!-- Links -->
* [Creación e implementación de la primera aplicación de Java para Service Fabric en Linux con Yeoman](service-fabric-create-your-first-linux-application-with-java.md)
* [Creación e implementación de la primera aplicación de Java para Service Fabric con el complemento de Eclipse para Service Fabric](service-fabric-get-started-eclipse.md)
* [Configuración de un clúster de Service Fabric en Azure Portal](service-fabric-cluster-creation-via-portal.md)
* [Creación de un clúster de Service Fabric con Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
* [Entender el modelo de aplicación de Service Fabric](service-fabric-application-model.md)
* [Uso de la CLI de Service Fabric para administrar las aplicaciones](service-fabric-application-lifecycle-sfctl.md)
* [Preparación del entorno de desarrollo Linux en Windows](service-fabric-local-linux-cluster-windows.md)

<!-- Images -->
[cluster-setup-script]: ./media/service-fabric-get-started-mac/cluster-setup-mac.png
[sfx-mac]: ./media/service-fabric-get-started-mac/sfx-mac.png
[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-mac/sf-eclipse-plugin-install.png
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship
