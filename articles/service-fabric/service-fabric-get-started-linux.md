---
title: Configuración del entorno de desarrollo en Linux | Microsoft Docs
description: Instale el SDK y el motor en tiempo de ejecución, y cree un clúster de desarrollo local en Linux. Después de completar esta instalación, estará listo para crear aplicaciones.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: ''
ms.assetid: d552c8cd-67d1-45e8-91dc-871853f44fc6
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: cf678eac16f8b13c5ffaa1d5673ca1cb47440cf9
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/18/2018
---
# <a name="prepare-your-development-environment-on-linux"></a>Preparación del entorno de desarrollo en Linux
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
>
>  

Para implementar y ejecutar [aplicaciones de Azure Service Fabric](service-fabric-application-model.md) en la máquina de desarrollo de Linux, instale el motor de tiempo de ejecución y el SDK común. También puede instalar SDK opcionales para el desarrollo con Java y .NET Core. 

En los pasos descritos en este artículo se supone que va a instalar de forma nativa en Linux o que va a usar la imagen de contenedor de Service Fabric OneBox, `microsoft/service-fabric-onebox`. 

No se admite la instalación del SDK ni del entorno de tiempo de ejecución de Service Fabric en el subsistema Windows para Linux. Sin embargo, sí se admite la interfaz de la línea de comandos de Azure Service Fabric (CLI), que permite administrar las entidades de Service Fabric hospedadas en otra parte de la nube o del entorno local. Para más información sobre cómo instalar la CLI, consulte [Configuración de la CLI de Service Fabric](./service-fabric-cli.md).


## <a name="prerequisites"></a>requisitos previos

* Se admiten las siguientes versiones de sistemas operativos para desarrollo:

    * Ubuntu 16.04 (`Xenial Xerus`)

      * Asegúrese de que el paquete `apt-transport-https` está instalado:
         
         ```bash
         sudo apt-get install apt-transport-https
         ```
    * Red Hat Enterprise Linux 7.4 (compatibilidad con la versión preliminar de Service Fabric)


## <a name="installation-methods"></a>Métodos de instalación

### <a name="1-script-installation-ubuntu"></a>1. Instalación mediante script (Ubuntu)

Se proporciona un script para facilitar la instalación del motor de tiempo de ejecución y el SDK común de Service Fabric junto con la CLI de **sfctl**. Ejecute los pasos de instalación manual descritos en la sección siguiente para determinar lo que se está instalando y las licencias que se acuerdan. Con la ejecución del script se da por supuesto que acepta las licencias para todo el software que se está instalando. 

Una vez que el script se ejecuta correctamente, puede ir directamente a [Instalación de un clúster local](#set-up-a-local-cluster).

```bash
sudo curl -s https://raw.githubusercontent.com/Azure/service-fabric-scripts-and-templates/master/scripts/SetupServiceFabric/SetupServiceFabric.sh | sudo bash
```

### <a name="2-manual-installation"></a>2. Instalación manual
Para la instalación manual del motor de tiempo de ejecución y el SDK común de Service Fabric, siga los pasos descritos más abajo en esta guía.

## <a name="update-your-apt-sourcesyum-repositories"></a>Actualización de los orígenes de APT o repositorios de Yum
Para instalar el SDK y el paquete en tiempo de ejecución asociado mediante la herramienta de línea de comandos apt-get, primero debe actualizar los orígenes de Advanced Packaging Tool (APT).

### <a name="ubuntu"></a>Ubuntu

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


### <a name="red-hat-enterprise-linux-74-service-fabric-preview-support"></a>Red Hat Enterprise Linux 7.4 (compatibilidad con la versión preliminar de Service Fabric)

1. Abra un terminal.
2. Descargue e instale Extra Packages for Enterprise Linux (EPEL).

    ```bash
    wget https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
    sudo yum install epel-release-latest-7.noarch.rpm
    ```
3. Agregue el repositorio de paquetes EfficiOS RHEL7 a su sistema.

    ```bash
    sudo wget -P /etc/yum.repos.d/ https://packages.efficios.com/repo.files/EfficiOS-RHEL7-x86-64.repo
    ```

4. Importe la clave de firma del paquete efficios en el conjunto de claves de GPG local.

    ```bash
    sudo rpmkeys --import https://packages.efficios.com/rhel/repo.key
    ```
5. Agregue repositorio RHEL de Microsoft a su sistema.
   ```bash
      curl https://packages.microsoft.com/config/rhel/7.4/prod.repo > ./microsoft-prod.repo
      sudo cp ./microsoft-prod.repo /etc/yum.repos.d/
   ```
6. Instale el sdk de dotnet.
   ```bash
      yum install rh-dotnet20 -y
      scl enable rh-dotnet20 bash
   ```

## <a name="install-and-set-up-the-service-fabric-sdk-for-local-cluster-setup"></a>Instalación y configuración del SDK de Service Fabric para la configuración del clúster local

Una vez actualizados los orígenes, puede instalar el SDK. Instale el paquete del SDK de Service Fabric, confirme la instalación y acepte el contrato de licencia.

### <a name="ubuntu"></a>Ubuntu

```bash
sudo apt-get install servicefabricsdkcommon
```

>   [!TIP]
>   Los siguientes comandos aceptan automáticamente la licencia para los paquetes de Service Fabric:
>   ```bash
>   echo "servicefabric servicefabric/accepted-eula-ga select true" | sudo debconf-set-selections
>   echo "servicefabricsdkcommon servicefabricsdkcommon/accepted-eula-ga select true" | sudo debconf-set-selections
>   ```

### <a name="red-hat-enterprise-linux-74-service-fabric-preview-support"></a>Red Hat Enterprise Linux 7.4 (compatibilidad con la versión preliminar de Service Fabric)

```bash
sudo yum install servicefabricsdkcommon
```

El entorno de tiempo de ejecución de Service Fabric que viene con la instalación anterior incluye los paquetes de la tabla siguiente. 

 | | DotNetCore | Java | Python | NodeJS | 
--- | --- | --- | --- |---
Ubuntu | 2.0.0 | 1.8 OpenJDK | Implícito de npm | más reciente |
RHEL | - | 1.8 OpenJDK | Implícito de npm | más reciente |

## <a name="set-up-a-local-cluster"></a>Instalación de un clúster local
  Una vez finalizada la instalación, debe poder iniciar un clúster local.

  1. Ejecute el script de instalación del clúster.

      ```bash
      sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
      ```

  2. Abra un explorador web y vaya a [Service Fabric Explorer](http://localhost:19080/Explorer) (`http://localhost:19080/Explorer`). Si el clúster se ha iniciado, debería ver el panel Service Fabric Explorer. El clúster puede tardar varios minutos en configurarse totalmente. Si el explorador no abre la dirección URL o si Service Fabric Explorer no muestra que el sistema está listo, espere unos minutos y vuelva a intentarlo.

      ![Service Fabric Explorer en Linux][sfx-linux]

  En este punto, puede implementar paquetes de aplicación de Service Fabric precompilados o nuevos basados en contenedores de invitado o archivos ejecutables de invitado. Para compilar nuevos servicios con los SDK de .NET Core o de Java, siga los pasos de configuración opcionales proporcionados en las siguientes secciones.


  > [!NOTE]
  > No se admiten clústeres independientes en Linux.
  >


>   [!TIP]
    Si tiene una unidad de estado sólido, es aconsejable que pase una ruta de acceso de las carpetas de dicha unidad mediante `--clusterdataroot` con devclustersetup.sh, ya que mejorará el rendimiento.

## <a name="set-up-the-service-fabric-cli"></a>Configuración de la CLI de Service Fabric

La [CLI de Service Fabric](service-fabric-cli.md) tiene comandos para interactuar con las entidades de Service Fabric, incluidos los clústeres y las aplicaciones.
Siga las instrucciones que aparecen en la [CLI de Service Fabric](service-fabric-cli.md) para instalar la CLI.


## <a name="set-up-yeoman-generators-for-containers-and-guest-executables"></a>Instalación de los generadores Yeoman para los contenedores y los ejecutables invitados
Service Fabric proporciona herramientas de scaffolding que le ayudarán a crear aplicaciones de Service Fabric desde el terminal mediante el generador de plantillas Yeoman. Siga estos pasos para configurar los generadores de plantillas de Service Fabric Yeoman:

1. Instalación de nodejs y NPM en la máquina

Ubuntu
  ```bash
  sudo apt-get install npm
  sudo apt install nodejs-legacy
  ```

Red Hat Enterprise Linux 7.4 (compatibilidad con la versión preliminar de Service Fabric)
  ```bash
  sudo yum install nodejs
  sudo yum install npm
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

Una vez instalados los generadores, puede crear ejecutables invitados o servicios de contenedor mediante la ejecución de `yo azuresfguest` o `yo azuresfcontainer` respectivamente.

## <a name="set-up-net-core-20-development"></a>Configuración del desarrollo con .NET Core 2.0

Instale el [SDK de .NET Core 2.0 para Ubuntu](https://www.microsoft.com/net/core#linuxubuntu) para iniciar la [creación de aplicaciones de Service Fabric en C#](service-fabric-create-your-first-linux-application-with-csharp.md). Los paquetes de aplicaciones de Service Fabric en .NET Core 2.0 se hospedan en NuGet.org, actualmente en versión preliminar.

## <a name="set-up-java-development"></a>Configuración del desarrollo de Java

Para compilar servicios de Service Fabric mediante Java, instale JDK 1.8 y Gradle para ejecutar las tareas de compilación. El fragmento de código siguiente instala Open JDK 1.8 junto con Gradle. Las bibliotecas de Java de Service Fabric se extraen de Maven.


Ubuntu 
 ```bash
  sudo apt-get install openjdk-8-jdk-headless
  sudo apt-get install gradle
  ```

Red Hat Enterprise Linux 7.4 (compatibilidad con la versión preliminar de Service Fabric)
  ```bash
  sudo yum install java-1.8.0-openjdk-devel
  curl -s https://get.sdkman.io | bash
  sdk install gradle
  ```
 
## <a name="install-the-eclipse-plug-in-optional"></a>Instalación del complemento de Eclipse (opcional)

El complemento de Eclipse para Service Fabric se puede instalar desde el IDE de Eclipse para desarrolladores de Java o desarrolladores de Java EE. Eclipse se puede usar para crear aplicaciones ejecutables y contenedoras de invitado de Service Fabric, además de aplicaciones Java de Service Fabric.

> [!IMPORTANT]
> El complemento de Service Fabric requiere Eclipse Neon o una versión posterior. Consulte las instrucciones que siguen a esta nota acerca de cómo comprobar la versión de Eclipse. Si tiene una versión anterior de Eclipse instalado, puede descargar las versiones más recientes de el [sitio web de Eclipse](https://www.eclipse.org). No se recomienda instalar (sobrescribir) sobre una instalación existente de Eclipse. Puede eliminarla antes de ejecutar el programa de instalación o instalar la versión más reciente en un directorio diferente. 
> 
> En Ubuntu, se recomienda instalar directamente desde el sitio de Eclipse, en lugar de usar un instalador de paquetes (`apt` o `apt-get`). Si lo hace así, se asegura de que tendrá la versión más reciente de Eclipse. Puede instalar el IDE de Eclipse para desarrolladores de Java o para desarrolladores de Java EE.

1. En Eclipse, asegúrese de que tanto Eclipse Neon, o posterior, como la versión 2.2.1 de Buildship, o posterior, están instalados. Para comprobar las versiones de los componentes instalados, seleccione **Help** > **About Eclipse** > **Installation Details** (Ayuda > Acerca de Eclipse > Detalles de instalación). Puede actualizar Buildship siguiendo las instrucciones de [Eclipse Buildship: Complementos de Eclipse para Gradle][buildship-update].

2. Para instalar el complemento de Service Fabric, seleccione **Help** > **Install New Software** (Ayuda > Instalar nuevo software).

3. En el cuadro **Work with** (Trabajar con), escriba **http://dl.microsoft.com/eclipse**.

4. Haga clic en **Agregar**.

    ![Página de Software disponible][sf-eclipse-plugin]

5. Seleccione el complemento **ServiceFabric** y, a continuación, haga clic en **Siguiente**.

6. Complete los pasos de instalación y, a continuación, acepte el contrato de licencia de usuario final.

Si el complemento de Eclipse para Service Fabric ya está instalado, asegúrese de que tiene la versión más reciente. Para comprobarlo, seleccione **Help** > **About Eclipse** > **Installation Details** (Ayuda > Acerca de Eclipse > Detalles de instalación) y busque Service Fabric en la lista de complementos instalados. Si hay disponible una versión más reciente, seleccione **Update** (Actualizar).

Para más información, consulte [Complemento de Service Fabric para el desarrollo de aplicaciones Java de Eclipse](service-fabric-get-started-eclipse.md).

## <a name="update-the-sdk-and-runtime"></a>Actualización del SDK y del runtime

Para actualizar a la versión más reciente del SDK y el motor de tiempo de ejecución, ejecute los siguientes pasos:

```bash
sudo apt-get update
sudo apt-get install servicefabric servicefabricsdkcommon
```
Para actualizar los archivos binarios del SDK de Java desde Maven, debe actualizar los detalles de la versión del archivo binario correspondiente en el archivo ``build.gradle`` para que señale a la versión más reciente. Para saber exactamente dónde debe actualizar la versión, puede hacer referencia a cualquier archivo ``build.gradle`` en los ejemplos de Introducción a Service Fabric [aquí](https://github.com/Azure-Samples/service-fabric-java-getting-started).

> [!NOTE]
> La actualización de los paquetes puede dar lugar a que se detenga el clúster de desarrollo local. Reinicie el clúster local después de cada actualización, para lo que debe seguir las instrucciones de esta página.

## <a name="remove-the-sdk"></a>Eliminación del SDK
Para quitar los SDK de Service Fabric, haga lo siguiente:

### <a name="ubuntu"></a>Ubuntu

```bash
sudo apt-get remove servicefabric servicefabicsdkcommon
sudo npm uninstall generator-azuresfcontainer
sudo npm uninstall generator-azuresfguest
sudo apt-get install -f
```


### <a name="red-hat-enterprise-linux-74-service-fabric-preview-support"></a>Red Hat Enterprise Linux 7.4 (compatibilidad con la versión preliminar de Service Fabric)

```bash
sudo yum remote servicefabric servicefabicsdkcommon
sudo npm uninstall generator-azuresfcontainer
sudo npm uninstall generator-azuresfguest
```

## <a name="next-steps"></a>Pasos siguientes

* [Creación e implementación de la primera aplicación de Java para Service Fabric en Linux con Yeoman](service-fabric-create-your-first-linux-application-with-java.md)
* [Creación e implementación de la primera aplicación de Java para Service Fabric en Linux con el complemento de Eclipse para Service Fabric](service-fabric-get-started-eclipse.md)
* [Creación de su primera aplicación de CSharp en Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
* [Prepare your development environment on OSX](service-fabric-get-started-mac.md)
* [Preparación del entorno de desarrollo Linux en Windows](service-fabric-local-linux-cluster-windows.md)
* [Uso de la CLI de Service Fabric para administrar las aplicaciones](service-fabric-application-lifecycle-sfctl.md)
* [Diferencias entre Service Fabric para Windows y para Linux](service-fabric-linux-windows-differences.md)
* [Automatización de la aplicación de revisiones del sistema operativo en el clúster de Linux](service-fabric-patch-orchestration-application-linux.md)
* [Introducción a la CLI de Service Fabric](service-fabric-cli.md)

<!-- Links -->

[azure-xplat-cli-github]: https://github.com/Azure/azure-xplat-cli
[install-node]: https://nodejs.org/en/download/package-manager/#installing-node-js-via-package-manager
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

<!--Images -->

[sf-eclipse-plugin]: ./media/service-fabric-get-started-linux/service-fabric-eclipse-plugin.png
[sfx-linux]: ./media/service-fabric-get-started-linux/sfx-linux.png
