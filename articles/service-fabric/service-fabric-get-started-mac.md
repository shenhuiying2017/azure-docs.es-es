---
title: Configuración del entorno de desarrollo en Mac OS X | Microsoft Docs
description: Instale las herramientas, el SDK y el motor en tiempo de ejecución y cree un clúster de desarrollo local. Después de completar esta instalación, estará listo para compilar aplicaciones en Mac OS X.
services: service-fabric
documentationcenter: .net
author: seanmck
manager: timlt
editor: ''

ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/25/2016
ms.author: seanmck

---
# Configuración de su entorno de desarrollo en Mac OS X
> [!div class="op_single_selector"]
> -[ Windows](service-fabric-get-started.md)
> 
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
> 
> 

Puede compilar aplicaciones de Service Fabric para que ejecuten en clústeres de Linux con Mac OS X. En este artículo se describe cómo configurar su Mac para desarrollo.

## Requisitos previos
Service Fabric no se ejecuta de forma nativa en OS X. Para ejecutar un clúster de Service Fabric local, proporcionamos una máquina virtual de Ubuntu preconfigurada mediante Vagrant y VirtualBox. Antes de comenzar, necesita:

* [Vagrant (v1.8.4 o versiones posteriores)](http://wwww.vagrantup.com/downloads)
* [VirtualBox](http://www.virtualbox.org/wiki/Downloads)

## Creación de la máquina virtual local
Para crear la máquina virtual local que contenga un clúster de Service Fabric de 5 nodos, siga estos pasos:

1. Clone el repositorio Vagrantfile
   
    ```bash
    git clone https://github.com/azure/service-fabric-linux-vagrant-onebox.git
    ```
2. Vaya al clon local del repositorio
   
    ```bash
    cd service-fabric-linux-vagrant-onebox
    ```
3. (Opcional) Modifique la configuración predeterminada de la máquina virtual
   
    De forma predeterminada, la máquina virtual local está configurada de la siguiente manera:
   
   * 3 GB de memoria asignada
   * Red host privada configurada en la dirección IP 192.168.50.50, que permite el acceso directo del tráfico desde el host de Mac
     
     Puede cambiar estas opciones o agregar otra configuración para la máquina virtual en Vagrantfile. Consulte la [documentación de Vagrant](http://www.vagrantup.com/docs) para obtener una lista completa de las opciones de configuración.
4. Creación de la máquina virtual
   
    ```bash
    vagrant up
    ```
   
    En este paso, se descarga la imagen de máquina virtual preconfigurada, se arranca localmente y, a continuación, se configura un clúster de Service Fabric local en ella. Tenga en cuenta que puede tardar unos minutos. Si la instalación se completa correctamente, verá un mensaje en la salida que indica que el clúster se está iniciando.
   
    ![El programa de instalación del clúster se inicia después de aprovisionar la máquina virtual][cluster-setup-script]
5. Para comprobar que el clúster se ha instalado correctamente, vaya a Service Fabric Explorer en http://192.168.50.50:19080/Explorer (suponiendo que mantenga la IP de la red privada predeterminada).
   
    ![Service Fabric Explorer visto desde el equipo Mac host][sfx-mac]

## Instalación del complemento de Service Fabric para Eclipse Neon (opcional)
Service Fabric ofrece un complemento para el IDE de Eclipse Neon que puede simplificar el proceso de compilación e implementación de servicios de Java.

1. En Eclipse, asegúrese de tener instalado Buildship 1.0.17 o versiones posteriores. Puede comprobar las versiones de los componentes instalados en **Help > Installation Details** (Ayuda > Detalles de la instalación). Para actualizar Buildship, siga [estas instrucciones][buildship-update].
2. Para instalar el complemento de Service Fabric, elija **Help > Install New Software...** (Ayuda > Instalar nuevo software).
3. En el cuadro de texto "Work with" (Trabajar con), escriba: http://dl.windowsazure.com/eclipse/servicefabric.
4. Haga clic en Agregar.
   
    ![Complemento de Service Fabric para Eclipse Neon][sf-eclipse-plugin-install]
5. Elija el complemento de Service Fabric y haga clic en Siguiente.
6. Lleve a cabo la instalación y acepte el contrato de licencia de usuario final.

## Pasos siguientes
* [Creación de su primera aplicación de Service Fabric para Linux](service-fabric-create-your-first-linux-application-with-java.md)

<!-- Links -->

* [Configuración de un clúster de Service Fabric en Azure Portal](service-fabric-cluster-creation-via-portal.md)
* [Creación de un clúster de Service Fabric en Azure mediante Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
* [Entender el modelo de aplicación de Service Fabric](service-fabric-application-model.md)

<!-- Images -->
[cluster-setup-script]: ./media/service-fabric-get-started-mac/cluster-setup-mac.png
[sfx-mac]: ./media/service-fabric-get-started-mac/sfx-mac.png
[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-mac/sf-eclipse-plugin-install.png
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

<!---HONumber=AcomDC_0928_2016-->