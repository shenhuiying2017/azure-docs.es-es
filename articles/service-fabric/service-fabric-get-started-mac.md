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
ms.date: 08/21/2017
ms.author: saysa
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 28424d139499b797b09664f73657a7f73361e3bc
ms.contentlocale: es-es
ms.lasthandoff: 09/25/2017

---
# <a name="set-up-your-development-environment-on-mac-os-x"></a>Configuración de su entorno de desarrollo en Mac OS X
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
>
>  

Puede compilar aplicaciones de Service Fabric para que ejecuten en clústeres de Linux con Mac OS X. En este artículo se describe cómo configurar su Mac para desarrollo.

## <a name="prerequisites"></a>Requisitos previos
Service Fabric no se ejecuta de forma nativa en OS X. Para ejecutar un clúster de Service Fabric local, proporcionamos una máquina virtual de Ubuntu preconfigurada mediante Vagrant y VirtualBox. Antes de comenzar, necesita:

* [Vagrant (v1.8.4 o versiones posteriores)](http://www.vagrantup.com/downloads.html)
* [VirtualBox](http://www.virtualbox.org/wiki/Downloads)

>[!NOTE]
> Tiene que utilizar versiones de Vagrant y VirtualBox que sean compatibles entre sí. Con una versión no compatible de VirtualBox, Vagrant pueden comportarse de forma errática.
>

## <a name="create-the-local-vm"></a>Creación de la máquina virtual local
Para crear la máquina virtual local que contenga un clúster de Service Fabric de 5 nodos, realice los pasos siguientes:

1. Clone el repositorio `Vagrantfile`

    ```bash
    git clone https://github.com/azure/service-fabric-linux-vagrant-onebox.git
    ```
    Este paso reduce el archivo `Vagrantfile` que contiene la configuración de la máquina virtual, junto con la ubicación desde la que se descarga la máquina virtual.  El archivo apunta a una imagen de Ubuntu existente. 

2. Vaya al clon local del repositorio

    ```bash
    cd service-fabric-linux-vagrant-onebox
    ```
3. (Opcional) Modifique la configuración predeterminada de la máquina virtual

    De forma predeterminada, la máquina virtual local está configurada de la siguiente manera:

   * 3 GB de memoria asignada
   * Red host privada configurada en la dirección IP 192.168.50.50, que permite el acceso directo del tráfico desde el host de Mac

     Puede cambiar cualquiera de estas opciones o agregar otra configuración a la máquina virtual en `Vagrantfile`. Consulte la [documentación de Vagrant](http://www.vagrantup.com/docs) para obtener una lista completa de las opciones de configuración.
4. Creación de la máquina virtual

    ```bash
    vagrant up
    ```


5. Inicie sesión en la máquina virtual e instale el SDK de Service Fabric.

    ```bash
    vagrant ssh
    ```
   
   Instale el SDK como se describe en [Instalación del SDK](service-fabric-get-started-linux.md).  El siguiente script se proporciona para facilitar la instalación del motor de tiempo de ejecución y el SDK común de Service Fabric junto con la CLI de sfctl. Con la ejecución del script se da por supuesto que ha leído y acepta las licencias para todo el software que se está instalando.

    ```bash
    sudo curl -s https://raw.githubusercontent.com/Azure/service-fabric-scripts-and-templates/master/scripts/SetupServiceFabric/SetupServiceFabric.sh | sudo bash
    ```

5.  Inicie el clúster de Service Fabric

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```

    >[!TIP]
    > Si la descarga de la máquina virtual tarda mucho tiempo, puede descargarla mediante wget o curl, o si usa un explorador, navegando al vínculo que especifica **config.vm.box_url** en el archivo `Vagrantfile`. Después de descargarlo localmente, edite `Vagrantfile` para que apunte a la ruta de acceso local en la que descargó la imagen. Por ejemplo si la descargó en /home/users/test/azureservicefabric.tp8.box, establezca **config.vm.box_url** en dicha ruta.
    >

5. Para comprobar que el clúster se ha instalado correctamente, vaya a Service Fabric Explorer en http://192.168.50.50:19080/Explorer (suponiendo que mantenga la IP de la red privada predeterminada).

    ![Service Fabric Explorer visto desde el equipo Mac host][sfx-mac]


## <a name="create-application-on-mac-using-yeoman"></a>Creación de la aplicación en el equipo Mac usando Yeoman
Service Fabric proporciona herramientas de scaffolding que le ayudarán a crear una aplicación de Service Fabric desde el terminal mediante el generador de plantillas Yeoman. Siga los pasos siguientes para asegurarse de que el generador de plantillas yeoman de Service Fabric está en funcionamiento en la máquina.

1. Debe tener Node.js y NPM instalados en el equipo Mac. Si no los tiene, puede instalar Node.js y NPM mediante Homebrew utilizando lo siguiente. Para comprobar las versiones de Node.js y NPM instaladas en el equipo Mac, puede usar la opción ``-v``.

  ```bash
  brew install node
  node -v
  npm -v
  ```
2. Instalación del generador de plantillas [Yeoman](http://yeoman.io/) en la máquina desde NPM

  ```bash
  npm install -g yo
  ```
3. Para instalar el generador Yeoman que desea usar, siga los pasos descritos en la [documentación](service-fabric-get-started-linux.md) de introducción. Para crear aplicaciones de Service Fabric mediante Yeoman, siga estos pasos:

  ```bash
  npm install -g generator-azuresfjava       # for Service Fabric Java Applications
  npm install -g generator-azuresfguest      # for Service Fabric Guest executables
  npm install -g generator-azuresfcontainer  # for Service Fabric Container Applications
  ```
4. Para compilar una aplicación de Java de Service Fabric en Mac, necesita JDK 1.8 y Gradle instalados en el equipo.

## <a name="set-up-net-core-20-development"></a>Configuración del desarrollo con .NET Core 2.0

Instale el [SDK de .NET Core 2.0 para Mac](https://www.microsoft.com/net/core#macos) para iniciar la [creación de aplicaciones de Service Fabric en C#](service-fabric-create-your-first-linux-application-with-csharp.md). Los paquetes de aplicaciones de Service Fabric en .NET Core 2.0 se hospedan en NuGet.org, actualmente en versión preliminar.


## <a name="install-the-service-fabric-plugin-for-eclipse-neon"></a>Instalación del complemento de Eclipse Neon para Service Fabric

Service Fabric proporciona un complemento para **IDE de Java para Eclipse Neon** que puede simplificar el proceso de creación, compilación e implementación de servicios de Java. Puede seguir los pasos de instalación que se indican en esta [documentación](service-fabric-get-started-eclipse.md#install-or-update-the-service-fabric-plug-in-in-eclipse-neon) general acerca de cómo instalar o actualizar el complemento de Eclipse para Service Fabric.

>[!TIP]
> De forma predeterminada, se admite la dirección IP predeterminada como se menciona en ``Vagrantfile``, en el archivo ``Local.json`` de la aplicación generada. En caso de cambiar esta configuración e implementar Vagrant con una dirección IP diferente, actualice la dirección IP correspondiente en el archivo ``Local.json`` de la aplicación.

## <a name="next-steps"></a>Pasos siguientes
<!-- Links -->
* [Creación e implementación de la primera aplicación de Java para Service Fabric en Linux con Yeoman](service-fabric-create-your-first-linux-application-with-java.md)
* [Creación e implementación de la primera aplicación de Java para Service Fabric con el complemento de Eclipse para Service Fabric](service-fabric-get-started-eclipse.md)
* [Configuración de un clúster de Service Fabric en Azure Portal](service-fabric-cluster-creation-via-portal.md)
* [Creación de un clúster de Service Fabric en Azure mediante Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
* [Entender el modelo de aplicación de Service Fabric](service-fabric-application-model.md)

<!-- Images -->
[cluster-setup-script]: ./media/service-fabric-get-started-mac/cluster-setup-mac.png
[sfx-mac]: ./media/service-fabric-get-started-mac/sfx-mac.png
[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-mac/sf-eclipse-plugin-install.png
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

