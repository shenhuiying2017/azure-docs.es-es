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
ms.date: 8/23/2017
ms.author: subramar
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: 8d902ba9e2077b4b70762c76cfb3ebf12752fb11
ms.contentlocale: es-es
ms.lasthandoff: 09/14/2017

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

Se admiten las siguientes versiones de sistemas operativos para desarrollo:

* Ubuntu 16.04 (`Xenial Xerus`)

## <a name="update-your-apt-sources"></a>Actualización de los orígenes de APT
Para instalar el SDK y el paquete en tiempo de ejecución asociado mediante la herramienta de línea de comandos apt-get, primero debe actualizar los orígenes de Advanced Packaging Tool (APT).

1. Abra un terminal.
2. Agregue el repositorio de Service Fabric a su lista de orígenes.

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] http://apt-mo.trafficmanager.net/repos/servicefabric/ xenial main" > /etc/apt/sources.list.d/servicefabric.list'
    ```

3. Agregue el repositorio `dotnet` a su lista de orígenes.

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/dotnet-release/ xenial main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```

4. Agregue la clave de la protección de privacidad de Gnu (GnuPG o GPG) al conjunto de claves APT.

    ```bash
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 417A0893
    ```

5. Agregue la clave GPG oficial de Docker a su conjunto de claves APT.

    ```bash
    sudo apt-get install curl
    sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    ```

6. Configure el repositorio de Docker.

    ```bash
    sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
    ```

7. Actualice las listas de paquetes según los repositorios recién agregados.

    ```bash
    sudo apt-get update
    ```

## <a name="install-and-set-up-the-sdk-for-local-cluster-setup"></a>Instalación y configuración del SDK para la configuración del clúster local

Una vez actualizados los orígenes, puede instalar el SDK. Instale el paquete del SDK de Service Fabric, confirme la instalación y acepte el contrato de licencia.

```bash
sudo apt-get install servicefabricsdkcommon
```

>   [!TIP]
>   Los siguientes comandos aceptan automáticamente la licencia para los paquetes de Service Fabric:
>   ```bash
>   echo "servicefabric servicefabric/accepted-eula-v1 select true" | sudo debconf-set-selections
>   echo "servicefabricsdkcommon servicefabricsdkcommon/accepted-eula-v1 select true" | sudo debconf-set-selections
>   ```

## <a name="set-up-a-local-cluster"></a>Instalación de un clúster local
  Si todo se instaló correctamente, debe poder iniciar un clúster local.

  1. Ejecute el script de instalación del clúster.

      ```bash
      sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
      ```

  2. Abra un explorador web y vaya a [Service Fabric Explorer](http://localhost:19080/Explorer). Si el clúster se ha iniciado, debería ver el panel Service Fabric Explorer.

      ![Service Fabric Explorer en Linux][sfx-linux]

  En este punto, puede implementar paquetes de aplicación de Service Fabric precompilados o nuevos basados en contenedores de invitado o archivos ejecutables de invitado. Para compilar nuevos servicios con los SDK de .NET Core o de Java, siga los pasos de configuración opcionales proporcionados en las siguientes secciones.


  > [!NOTE]
  > No se admiten clústeres independientes en Linux. La versión preliminar admite únicamente un equipo y clústeres de múltiples máquinas Linux de Azure.
  >

## <a name="set-up-the-service-fabric-cli"></a>Configuración de la CLI de Service Fabric

La [CLI de Service Fabric](service-fabric-cli.md) tiene comandos para interactuar con las entidades de Service Fabric, incluidos los clústeres y las aplicaciones.
Siga las instrucciones que aparecen en la [CLI de Service Fabric](service-fabric-cli.md) para instalar la CLI.


## <a name="install-and-set-up-the-generators-for-containers-and-guest-executables"></a>Instalación y configuración de los generadores para los contenedores y los ejecutables invitados
Service Fabric proporciona herramientas de scaffolding que le ayudarán a crear aplicaciones de Service Fabric desde el terminal mediante el generador de plantillas Yeoman. Siga los pasos siguientes para asegurarse de que el generador de plantillas yeoman de Service Fabric está en funcionamiento en la máquina.

1. Instalación de nodejs y NPM en la máquina

  ```bash
  sudo apt-get install npm
  sudo apt install nodejs-legacy
  ```
2. Instalación del generador de plantillas [Yeoman](http://yeoman.io/) en la máquina desde NPM

  ```bash
  sudo npm install -g yo
  ```
3. Instalación del generador de contenedores Yeo de Service Fabric y el generador de ejecutables invitados desde NPM

  ```bash
  sudo npm install -g generator-azuresfcontainer  # for Service Fabric container application
  sudo npm install -g generator-azuresfguest      # for Service Fabric guest executable application
  ```

Una vez instalados los generadores anteriores, puede crear aplicaciones con ejecutables invitados o servicios de contenedor mediante la ejecución de `yo azuresfguest` o `yo azuresfcontainer` respectivamente.

## <a name="install-the-necessary-java-artifacts-optional-if-you-want-to-use-the-java-programming-models"></a>Instalación de los artefactos de Java necesarios (opcional, si desea usar los modelos de programación de Java)

Para compilar servicios de Service Fabric con Java, asegúrese de que tiene instalado JDK 1.8 junto con Gradle, que se utiliza para ejecutar tareas de compilación. El fragmento de código siguiente instala Open JDK 1.8 junto con Gradle. Las bibliotecas de Java de Service Fabric se extraen de Maven.

  ```bash
  sudo apt-get install openjdk-8-jdk-headless
  sudo apt-get install gradle
  ```

## <a name="install-the-eclipse-neon-plug-in-optional"></a>Instalación del complemento Eclipse Neon (opcional)

Puede instalar el complemento de Eclipse para Service Fabric desde el **IDE de Eclipse para desarrolladores de Java**. Eclipse se puede usar para crear aplicaciones ejecutables y contenedoras de invitado de Service Fabric, además de aplicaciones Java de Service Fabric.

1. En Eclipse, asegúrese de tener instaladas las versiones más recientes de Eclipse Neon y Buildship (1.0.17 o versiones posteriores). Puede comprobar las versiones de los componentes instalados seleccionando **Help** > **Installation Details** (Ayuda > Detalles de la instalación). Puede actualizar Buildship siguiendo las instrucciones de [Eclipse Buildship: Complementos de Eclipse para Gradle][buildship-update].

2. Para instalar el complemento de Service Fabric, seleccione **Help** > **Install New Software** (Ayuda > Instalar nuevo software).

3. En el cuadro de texto **Trabajar con**, escriba **http://dl.microsoft.com/eclipse**.

4. Haga clic en **Agregar**.

    ![Página de Software disponible][sf-eclipse-plugin]

5. Seleccione el complemento **ServiceFabric** y, a continuación, haga clic en **Siguiente**.

6. Complete los pasos de instalación y, a continuación, acepte el contrato de licencia de usuario final.

Si el complemento de Eclipse para Service Fabric ya está instalado, asegúrese de que tiene la versión más reciente. Puede comprobarlo seleccionando **Help** > **Installation Details** (Ayuda > Detalles de instalación) y buscando Service Fabric en la lista de complementos instalados. Si hay disponible una versión más reciente, seleccione **Update** (Actualizar).

Para más información, consulte [Complemento de Service Fabric para el desarrollo de aplicaciones Java de Eclipse](service-fabric-get-started-eclipse.md).


## <a name="install-the-net-core-sdk-optional-if-you-want-to-use-the-net-core-programming-models"></a>Instalación del SDK de .NET Core (opcional, si desea usar los modelos de programación de .NET Core)
El SDK de .NET Core proporciona las bibliotecas y plantillas necesarias para compilar servicios de Service Fabric con .NET Core. Instale el paquete SDK de .NET Core mediante los siguientes pasos:

   ```bash
   sudo apt-get install servicefabricsdkcsharp
   ```

## <a name="update-the-sdk-and-runtime"></a>Actualización del SDK y del runtime

Para actualizar a la versión más reciente del SDK y el motor de tiempo de ejecución, ejecute los siguientes pasos (desactive los SDK que no desee actualizar o instalar):

```bash
sudo apt-get update
sudo apt-get install servicefabric servicefabricsdkcommon servicefabricsdkcsharp
```
Para actualizar los archivos binarios del SDK de Java desde Maven, debe actualizar los detalles de la versión del archivo binario correspondiente en el archivo ``build.gradle`` para que señale a la versión más reciente. Para saber exactamente dónde debe actualizar la versión, puede hacer referencia a cualquier archivo ``build.gradle`` en los ejemplos de Introducción a Service Fabric [aquí](https://github.com/Azure-Samples/service-fabric-java-getting-started).

> [!NOTE]
> La actualización de los paquetes puede dar lugar a que se detenga el clúster de desarrollo local. Reinicie el clúster local después de cada actualización, para lo que debe seguir las instrucciones de esta página.

## <a name="next-steps"></a>Pasos siguientes

* [Creación e implementación de la primera aplicación de Java para Service Fabric en Linux con Yeoman](service-fabric-create-your-first-linux-application-with-java.md)
* [Creación e implementación de la primera aplicación de Java para Service Fabric en Linux con el complemento de Eclipse para Service Fabric](service-fabric-get-started-eclipse.md)
* [Creación de su primera aplicación de CSharp en Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
* [Prepare your development environment on OSX](service-fabric-get-started-mac.md)
* [Uso de la CLI de Service Fabric para administrar las aplicaciones](service-fabric-application-lifecycle-sfctl.md)
* [Diferencias entre Service Fabric para Windows y para Linux](service-fabric-linux-windows-differences.md)
* [Introducción a la CLI de Service Fabric](service-fabric-cli.md)

<!-- Links -->

[azure-xplat-cli-github]: https://github.com/Azure/azure-xplat-cli
[install-node]: https://nodejs.org/en/download/package-manager/#installing-node-js-via-package-manager
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

<!--Images -->

[sf-eclipse-plugin]: ./media/service-fabric-get-started-linux/service-fabric-eclipse-plugin.png
[sfx-linux]: ./media/service-fabric-get-started-linux/sfx-linux.png

