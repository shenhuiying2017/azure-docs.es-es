---
title: "Configuración del entorno de desarrollo en Linux | Microsoft Docs"
description: "Instale el SDK y el motor en tiempo de ejecución, y cree un clúster de desarrollo local en Linux. Después de completar esta instalación, estará listo para crear aplicaciones."
services: service-fabric
documentationcenter: .net
author: seanmck
manager: timlt
editor: 
ms.assetid: d552c8cd-67d1-45e8-91dc-871853f44fc6
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/26/2016
ms.author: seanmck
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 567a998102558626df73878865b317b830ba1faa


---
# <a name="prepare-your-development-environment-on-linux"></a>Preparación del entorno de desarrollo en Linux
> [!div class="op_single_selector"]
> -[ Windows](service-fabric-get-started.md)
> 
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
> 
> 

 Para implementar y ejecutar [aplicaciones de Azure Service Fabric](service-fabric-application-model.md) en la máquina de desarrollo de Linux, instale el motor de tiempo de ejecución y el SDK común. También puede instalar SDK opcionales para Java y .NET Core.

## <a name="prerequisites"></a>Requisitos previos
### <a name="supported-operating-system-versions"></a>Versiones de sistemas operativos compatibles
Se admiten las siguientes versiones de sistemas operativos para desarrollo:

* Ubuntu 16.04 (Xenial Xerus)

## <a name="update-your-apt-sources"></a>Actualización de los orígenes de apt
Para instalar el SDK y el paquete en tiempo de ejecución asociado mediante apt-get, primero debe actualizar los orígenes de apt.

1. Abra un terminal.
2. Agregue el repositorio de Service Fabric a su lista de orígenes.
   
    ```bash
    sudo sh -c 'echo "deb [arch=amd64] http://apt-mo.trafficmanager.net/repos/servicefabric/ trusty main" > /etc/apt/sources.list.d/servicefabric.list'
    ```
3. Agregue la nueva clave GPG a su conjunto de claves apt.
   
    ```bash
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    ```
4. Actualice las listas de paquetes según los repositorios recién agregados.
   
    ```bash
    sudo apt-get update
    ```

## <a name="install-and-set-up-the-sdk"></a>Instalación y configuración del SDK
Una vez actualizados los orígenes, puede instalar el SDK.

1. Instale el paquete del SDK de Service Fabric. Se le pedirá que confirme la instalación y acepte un contrato de licencia.
   
    ```bash
    sudo apt-get install servicefabricsdkcommon
    ```
2. Ejecute el script de instalación del SDK.
   
    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/sdkcommonsetup.sh
    ```

## <a name="set-up-the-azure-crossplatform-cli"></a>Instalación de la CLI multiplataforma de Azure
La [CLI multiplataforma de Azure][azure-xplat-cli-github] incluye comandos para interactuar con las entidades de Service Fabric, incluidos los clústeres y las aplicaciones. Como se basa en Node.js, [asegúrese de que Node esté instalado][install-node] antes de continuar con las instrucciones siguientes.

1. Clone el repositorio de github en la máquina de desarrollo.
   
    ```bash
    git clone https://github.com/Azure/azure-xplat-cli.git
    ```
2. Cambie al repositorio clonado e instale dependencias de CLI mediante Node Package Manager (npm).
   
    ```bash
    cd azure-xplat-cli
    npm install
    ```
3. Cree un vínculo simbólico desde la carpeta bin/azure del repositorio clonado a /usr/bin/azure para que se agregue a la ruta de acceso y los comandos están disponibles desde cualquier directorio.
   
    ```bash
    sudo ln -s $(pwd)/bin/azure /usr/bin/azure
    ```
4. Por último, habilite los comandos de finalización automática de Service Fabric.
   
    ```bash
    azure --completion >> ~/azure.completion.sh
    echo 'source ~/azure.completion.sh' >> ~/.bash_profile
    source ~/azure.completion.sh
    ```

## <a name="set-up-a-local-cluster"></a>Instalación de un clúster local
Si todo se instaló correctamente, debe poder iniciar un clúster local.

1. Ejecute el script de instalación del clúster.
   
    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```
2. Abra el explorador y navegue hasta http://localhost:19080/Explorer. Si el clúster se ha iniciado, debería ver el panel Service Fabric Explorer.
   
    ![Service Fabric Explorer en Linux][sfx-linux]

En este punto, puede implementar paquetes de aplicación de Service Fabric precompilados o nuevos basados en contenedores de invitado o archivos ejecutables de invitado. Para compilar nuevos servicios usando los SDK de .NET Core o de Java, siga los pasos de configuración opcionales siguientes.

## <a name="install-the-java-sdk-and-eclipse-neon-plugin-optional"></a>Instalación del SDK de Java y el complemento para Eclipse Neon (opcional)
El SDK de Java proporciona las bibliotecas y plantillas necesarias para compilar servicios de Service Fabric con Java.

1. Instale el paquete del SDK de Java.
   
    ```bash
    sudo apt-get install servicefabricsdkjava
    ```
2. Ejecute el script de instalación del SDK.
   
    ```bash
    sudo /opt/microsoft/sdk/servicefabric/java/sdkjavasetup.sh
    ```

Puede instalar el complemento de Service Fabric para Eclipse desde el IDE de Eclipse Neon.

1. En Eclipse, asegúrese de tener instalado Buildship 1.0.17 o versiones posteriores. Puede comprobar las versiones de los componentes instalados en **Help > Installation Details** (Ayuda > Detalles de la instalación). Para actualizar Buildship, siga [estas instrucciones][buildship-update]..
2. Para instalar el complemento de Service Fabric, elija **Help > Install New Software...** (Ayuda > Instalar nuevo software...).
3. En el cuadro de texto "Trabajar con", escriba: http://dl.windowsazure.com/eclipse/servicefabric
4. Haga clic en Agregar.
   
    ![Complemento para Eclipse][sf-eclipse-plugin]
5. Elija el complemento de Service Fabric y haga clic en Siguiente.
6. Lleve a cabo la instalación y acepte el contrato de licencia de usuario final.

## <a name="install-the-net-core-sdk-optional"></a>Instalación del SDK de .NET Core (opcional)
El SDK de .NET Core proporciona las bibliotecas y plantillas necesarias para compilar servicios de Service Fabric con .NET Core multiplataforma.

1. Instale el paquete del SDK de .NET Core.
   
    ```bash
    sudo apt-get install servicefabricsdkcsharp
    ```
2. Ejecute el script de instalación del SDK.
   
    ```bash
    sudo /opt/microsoft/sdk/servicefabric/csharp/sdkcsharpsetup.sh
    ```

## <a name="next-steps"></a>Pasos siguientes
* [Creación de su primera aplicación de Java en Linux](service-fabric-create-your-first-linux-application-with-java.md)
* [Prepare your development environment on OSX](service-fabric-get-started-mac.md)

<!-- Links -->

[azure-xplat-cli-github]: https://github.com/Azure/azure-xplat-cli
[install-node]: https://nodejs.org/en/download/package-manager/#installing-node-js-via-package-manager
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

<!--Images -->

[sf-eclipse-plugin]: ./media/service-fabric-get-started-linux/service-fabric-eclipse-plugin.png
[sfx-linux]: ./media/service-fabric-get-started-linux/sfx-linux.png



<!---HONumber=Nov16_HO2-->


