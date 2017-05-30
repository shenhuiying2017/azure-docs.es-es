---
title: "Configuración del entorno de desarrollo en Linux | Microsoft Docs"
description: "Instale el SDK y el motor en tiempo de ejecución, y cree un clúster de desarrollo local en Linux. Después de completar esta instalación, estará listo para crear aplicaciones."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: d552c8cd-67d1-45e8-91dc-871853f44fc6
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/04/2017
ms.author: subramar
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: 8c0f3cc737b999d26359f33d3768dcc55893029c
ms.contentlocale: es-es
ms.lasthandoff: 05/25/2017


---
# <a name="prepare-your-development-environment-on-linux"></a>Preparación del entorno de desarrollo en Linux
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
>
>  

 Para implementar y ejecutar [aplicaciones de Azure Service Fabric](service-fabric-application-model.md) en la máquina de desarrollo de Linux, instale el motor de tiempo de ejecución y el SDK común. También puede instalar SDK opcionales para Java y .NET Core.

## <a name="prerequisites"></a>Requisitos previos

### <a name="supported-operating-system-versions"></a>Versiones de sistemas operativos compatibles
Se admiten las siguientes versiones de sistemas operativos para desarrollo:

* Ubuntu 16.04 (`Xenial Xerus`)

## <a name="update-your-apt-sources"></a>Actualización de los orígenes de apt
Para instalar el SDK y el paquete en tiempo de ejecución asociado mediante apt-get, primero debe actualizar los orígenes de apt.

1. Abra un terminal.
2. Agregue el repositorio de Service Fabric a su lista de orígenes.

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] http://apt-mo.trafficmanager.net/repos/servicefabric/ trusty main" > /etc/apt/sources.list.d/servicefabric.list'
    ```

3. Agregue el repositorio `dotnet` a su lista de orígenes.

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/dotnet-release/ xenial main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```

4. Agregue la nueva clave GPG a su conjunto de claves apt.

    ```bash
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 417A0893
    ```

5. Actualice las listas de paquetes según los repositorios recién agregados.

    ```bash
    sudo apt-get update
    ```

## <a name="install-and-set-up-the-sdk-for-containers-and-guest-executables"></a>Instalación y configuración del SDK en los contenedores y los ejecutables invitados

Una vez actualizados los orígenes, puede instalar el SDK.

1. Instale el paquete del SDK de Service Fabric. Se le pedirá que confirme la instalación y acepte un contrato de licencia.

    ```bash
    sudo apt-get install servicefabricsdkcommon
    ```

   Los siguientes comandos aceptan automáticamente la licencia para los paquetes de Service Fabric:
    
    ```bash
    echo "servicefabric servicefabric/accepted-eula-v1 select true" | debconf-set-selections
    echo "servicefabricsdkcommon servicefabricsdkcommon/accepted-eula-v1 select true" | debconf-set-selections
    ```

2. Ejecute el script de instalación del SDK.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/sdkcommonsetup.sh
    ```

Una vez que haya ejecutado los pasos necesarios para instalar el paquete Common SDK, la creación de aplicaciones con servicios de contenedor o ejecutable invitado debe ser posible mediante la ejecución de `yo azuresfguest`. Es posible que tenga que establecer la variable de entorno **NODE_PATH $** a la ubicación en que se encuentran los módulos de nodo. 

    ```bash
    export NODE_PATH=$NODE_PATH:$HOME/.node/lib/node_modules 
    ```

Si utiliza el entorno como raíz, es posible que tenga que establecer la variable con el siguiente comando:

    ```bash
    export NODE_PATH=$NODE_PATH:/root/.node/lib/node_modules 
    ```

> [!TIP]
> Estos comandos se pueden agregar al archivo ~/.bashrc para que no haya que establecer la variable de entorno cada vez que se inicia sesión.
>

## <a name="set-up-the-azure-cli"></a>Configuración de la CLI de Azure
La [CLI de Azure][azure-xplat-cli-github] incluye comandos para interactuar con las entidades de Service Fabric, incluidos los clústeres y las aplicaciones. Como se basa en Node.js, [asegúrese de que Node esté instalado][install-node] antes de continuar con las instrucciones siguientes:

1. Clone el repositorio de github en la máquina de desarrollo.

    ```bash
    git clone https://github.com/Azure/azure-xplat-cli.git
    ```

2. Cambie al repositorio clonado e instale dependencias de CLI mediante Node Package Manager (npm).

    ```bash
    cd azure-xplat-cli
    npm install
    ```

3. Crear un vínculo simbólico desde la carpeta `bin/azure` del repositorio clonado a `/usr/bin/azure`.

    ```bash
    sudo ln -s $(pwd)/bin/azure /usr/bin/azure
    ```

4. Por último, habilite los comandos de finalización automática de Service Fabric.

    ```bash
    azure --completion >> ~/azure.completion.sh
    echo 'source ~/azure.completion.sh' >> ~/.bash_profile
    source ~/azure.completion.sh
    ```

> [!NOTE]
> Los comandos de Service Fabric todavía no están disponibles en la CLI de Azure 2.0.


## <a name="set-up-a-local-cluster"></a>Instalación de un clúster local
Si todo se instaló correctamente, debe poder iniciar un clúster local.

1. Ejecute el script de instalación del clúster.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```

2. Abra el explorador y navegue hasta http://localhost:19080/Explorer. Si el clúster se ha iniciado, debería ver el panel Service Fabric Explorer.

    ![Service Fabric Explorer en Linux][sfx-linux]

En este punto, puede implementar paquetes de aplicación de Service Fabric precompilados o nuevos basados en contenedores de invitado o archivos ejecutables de invitado. Para compilar nuevos servicios usando los SDK de .NET Core o de Java, siga los pasos de configuración opcionales proporcionados en las siguientes secciones.


> [!NOTE]
> En Linux, no son compatibles tampoco los clústeres independientes: solo se admiten clústeres one box y de varios equipos de Linux de Azure en la versión preliminar.
>

## <a name="install-the-java-sdk-optional-if-you-wish-to-use-the-java-programming-models"></a>Instalación del SDK de Java (opcional, si desea usar los modelos de programación de Java)
El SDK de Java proporciona las bibliotecas y plantillas necesarias para compilar servicios de Service Fabric con Java.

1. Instale el paquete del SDK de Java.

    ```bash
    sudo apt-get install servicefabricsdkjava
    ```

2. Ejecute el script de instalación del SDK.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/java/sdkjavasetup.sh
    ```

## <a name="install-the-eclipse-neon-plugin-optional"></a>Instalación del complemento de Eclipse Neon (opcional)

Puede instalar el complemento de Eclipse para Service Fabric desde el **IDE de Eclipse para desarrolladores de Java**. Eclipse se puede usar para crear aplicaciones ejecutables y contenedoras de invitado de Service Fabric, además de aplicaciones Java de Service Fabric.

> [!NOTE]
> El SDK de Java es un requisito previo para usar el complemento de Eclipse, aunque solo se use para aplicaciones ejecutables y contenedoras de invitado.
>

1. En Eclipse, asegúrese de tener instaladas las versiones más recientes de Eclipse **Neon** y Buildship (1.0.17 o versiones posteriores). Puede comprobar las versiones de los componentes instalados en **Help > Installation Details** (Ayuda > Detalles de la instalación). Para actualizar Buildship, siga [estas instrucciones][buildship-update].
2. Para instalar el complemento de Service Fabric, elija **Help > Install New Software...** (Ayuda > Instalar nuevo software...).
3. En el cuadro “Trabajar con”, escriba http://dl.microsoft.com/eclipse.
4. Haga clic en Agregar.

    ![Complemento para Eclipse][sf-eclipse-plugin]

5. Elija el complemento de Service Fabric y haga clic en **Siguiente**.
6. Lleve a cabo la instalación y acepte el contrato de licencia de usuario final.

Si ya tiene instalado el complemento de Eclipse para Service Fabric, compruebe que se trata de la versión más reciente. Para comprobarlo, seleccione ``Help => Installation Details`` y busque Service Fabric en la lista de complementos instalados. Seleccione la actualización si hay disponible una versión más reciente. 

Para más información, consulte [Introducción a Service Fabric con Eclipse](service-fabric-get-started-eclipse.md).


## <a name="install-the-net-core-sdk-optional-if-you-wish-to-use-the-net-core-programming-models"></a>Instalación del SDK de .NET Core (opcional, si desea usar los modelos de programación de .NET Core)
El SDK de .NET Core proporciona las bibliotecas y plantillas necesarias para compilar servicios de Service Fabric con .NET Core.

1. Instale el paquete del SDK de .NET Core.

   ```bash
   sudo apt-get install servicefabricsdkcsharp
   ```

2. Ejecute el script de instalación del SDK.

   ```bash
   sudo /opt/microsoft/sdk/servicefabric/csharp/sdkcsharpsetup.sh
   ```

## <a name="updating-the-sdk-and-runtime"></a>Actualización del SDK y el tiempo de ejecución

Para actualizar a la versión más reciente del SDK y el motor de tiempo de ejecución, ejecute los siguientes pasos (desactive los SDK que no desee actualizar o instalar):

   ```bash
   sudo apt-get update
   sudo apt-get install servicefabric servicefabricsdkcommon servicefabricsdkcsharp servicefabricsdkjava
   ```
   
> [!NOTE]
> La actualización de los paquetes puede dar lugar a que se detenga el clúster de desarrollo local. Reinicie el clúster local después de cada actualización, para lo que debe seguir las instrucciones de esta página.
>
>

Para actualizar la CLI, vaya al directorio donde clonó la CLI y ejecute `git pull` para realizar la actualización.  Las notas de la versión pueden contener pasos adicionales. 


## <a name="next-steps"></a>Pasos siguientes
* [Creación e implementación de la primera aplicación de Java para Service Fabric en Linux con Yeoman](service-fabric-create-your-first-linux-application-with-java.md)
* [Creación e implementación de la primera aplicación de Java para Service Fabric con el complemento de Eclipse para Service Fabric](service-fabric-get-started-eclipse.md)
* [Creación de su primera aplicación de CSharp en Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
* [Prepare your development environment on OSX](service-fabric-get-started-mac.md)
* [Uso de la CLI de Azure para administrar las aplicaciones de Service Fabric](service-fabric-azure-cli.md)
* [Diferencias entre Service Fabric para Windows y para Linux](service-fabric-linux-windows-differences.md)

<!-- Links -->

[azure-xplat-cli-github]: https://github.com/Azure/azure-xplat-cli
[install-node]: https://nodejs.org/en/download/package-manager/#installing-node-js-via-package-manager
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

<!--Images -->

[sf-eclipse-plugin]: ./media/service-fabric-get-started-linux/service-fabric-eclipse-plugin.png
[sfx-linux]: ./media/service-fabric-get-started-linux/sfx-linux.png

