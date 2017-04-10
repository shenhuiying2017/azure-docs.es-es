---
title: "Configuración del entorno de desarrollo en Mac OS X | Microsoft Docs"
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
ms.date: 04/06/2017
ms.author: saysa
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: e5d14eb0a656d67030f4c0d3d510aec0e9cafae7
ms.lasthandoff: 03/29/2017


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
    Este paso reduce el archivo `Vagrantfile` que contiene la configuración de la máquina virtual, junto con la ubicación desde la que se descarga la máquina virtual.


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

   En este paso, se descarga la imagen de máquina virtual preconfigurada, se arranca localmente y, a continuación, se configura un clúster de Service Fabric local en ella. Tenga en cuenta que puede tardar unos minutos. Si la instalación se completa correctamente, verá un mensaje en la salida que indica que el clúster se está iniciando.

    ![El programa de instalación del clúster se inicia después de aprovisionar la máquina virtual][cluster-setup-script]

>[!TIP]
> Si la descarga de la máquina virtual tarda mucho tiempo, puede descargarla mediante wget o curl, o si usa un explorador, navegando al vínculo que especifica **config.vm.box_url** en el archivo `Vagrantfile`. Después de descargarlo localmente, edite `Vagrantfile` para que apunte a la ruta de acceso local en la que descargó la imagen. Por ejemplo si la descargó en /home/users/test/azureservicefabric.tp8.box, establezca **config.vm.box_url** en dicha ruta.
>

5. Para comprobar que el clúster se ha instalado correctamente, vaya a Service Fabric Explorer en http://192.168.50.50:19080/Explorer (suponiendo que mantenga la IP de la red privada predeterminada).

    ![Service Fabric Explorer visto desde el equipo Mac host][sfx-mac]

## <a name="install-the-service-fabric-plugin-for-eclipse-neon"></a>Instalación del complemento de Eclipse Neon para Service Fabric

Service Fabric proporciona un complemento para **IDE de Java para Eclipse Neon** que puede simplificar el proceso de creación, compilación e implementación de servicios de Java. Puede seguir los pasos de instalación que se indican en esta [documentación](service-fabric-get-started-eclipse.md#install-or-update-the-service-fabric-plug-in-in-eclipse-neon) general acerca de cómo instalar o actualizar el complemento de Eclipse para Service Fabric.

## <a name="using-service-fabric-eclipse-plugin-on-mac"></a>Uso del complemento de Eclipse para Service Fabric en Mac

Asegúrese realizar todos los pasos mencionados en el [documentación sobre el complemento de Eclipse para Service Fabric](service-fabric-get-started-eclipse.md). Los pasos para crear, compilar e implementar una aplicación de Java para Service Fabric mediante el contenedor vagrant-guest en un host de Mac son prácticamente iguales que los que se indican en la documentación general, salvo los siguientes elementos:

* Dado que la aplicación de Java para Service Fabric necesita las bibliotecas de Service Fabric, el proyecto de Eclipse tiene que crearse en una ruta de acceso compartido. De forma predeterminada, el contenido en la ruta de acceso en el host donde ``Vagrantfile`` existe se comparte con la ruta de acceso ``/vagrant`` en el invitado.
* Si ``Vagrantfile`` se encuentra en una ruta de acceso, por ejemplo ``~/home/john/allprojects/``, será preciso que cree el proyecto de Service Fabric ``MyActor`` en la ubicación ``~/home/john/allprojects/MyActor`` y la ruta de acceso al área de trabajo de Eclipse será ``~/home/john/allprojects``.

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

