---
title: "Service Fabric e implementación de contenedores en Linux | Microsoft Docs"
description: "Service Fabric y el uso de contenedores de Docker para implementar aplicaciones de microservicios. En este artículo se describen las funcionalidades que ofrece Service Fabric para los contenedores y cómo implementar una imagen de contenedor de Docker en un clúster"
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: 
ms.assetid: 4ba99103-6064-429d-ba17-82861b6ddb11
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/24/2016
ms.author: msfussell
translationtype: Human Translation
ms.sourcegitcommit: 57aec98a681e1cb5d75f910427975c6c3a1728c3
ms.openlocfilehash: 9c45513f7d2987a83026adab39257d298292b7a5


---
# <a name="deploy-a-docker-container-to-service-fabric"></a>Implementación de un contenedor de Docker en Service Fabric
> [!div class="op_single_selector"]
> * [Implementación de un contenedor de Windows](service-fabric-deploy-container.md)
> * [Implementación de un contenedor de Docker](service-fabric-deploy-container-linux.md)
>
>

Este artículo lo guiará a través de la creación de servicios de contenedor en contenedores de Docker en Linux.

Service Fabric tiene varias funcionalidades de contenedor que le ayudarán a crear aplicaciones que se componen de microservicios en contenedores. Son los llamados servicios en contenedores.

Entre estas funcionalidades, cabe destacar:

* Activación e implementación de la imagen de contenedor
* Regulador de recursos
* Autenticación de repositorio
* Asignación de puerto a host de contenedor
* Detección y comunicación entre contenedores
* Capacidad de configurar y establecer variables de entorno

## <a name="packaging-a-docker-container-with-yeoman"></a>Empaquetado de un contenedor de Docker con Yeoman
Al empaquetar un contenedor en Linux, puede usar una plantilla de Yeoman o [crear el paquete de aplicación manualmente](service-fabric-deploy-container.md#manually).

Una aplicación de Service Fabric puede contener uno o varios contenedores, cada uno de ellos con un rol específico en la prestación de la funcionalidad de la aplicación. El SDK de Service Fabric para Linux incluye un generador [Yeoman](http://yeoman.io/) que permite crear fácilmente la aplicación y agregar una imagen de contenedor. Vamos a usar Yeoman para crear una nueva aplicación con un único contenedor de Docker denominado "*SimpleContainerApp*". Puede agregar más servicios más adelante editando los archivos de manifiesto generados.

## <a name="create-the-application"></a>Creación de la aplicación
1. En un terminal, escriba **yo azuresfjava**.
2. El marco de trabajo, elija **Contenedor**.
3. El nombre de la aplicación, por ejemplo, SimpleContainerApp.
4. Proporcione la dirección URL de la imagen de contenedor desde un repositorio de DockerHub. Esto toma la forma [repositorio]/[nombre de imagen].

![Generador Yeoman de Service Fabric para contenedores][sf-yeoman]

## <a name="deploy-the-application"></a>Implementación de la aplicación
Una vez compilada la aplicación, puede implementarla en el clúster local mediante la CLI de Azure.

1. Conéctese al clúster de Service Fabric local.

    ```bash
    azure servicefabric cluster connect
    ```
2. Use el script de instalación proporcionado en la plantilla para copiar el paquete de aplicación en el almacén de imágenes del clúster, registrar el tipo de aplicación y crear una instancia de la aplicación.

    ```bash
    ./install.sh
    ```
3. Abra un explorador y vaya a Service Fabric Explorer en http://localhost:19080/Explorer (reemplace localhost por la dirección IP privada de la VM si usa Vagrant en Mac OS X).
4. Expanda el nodo Applications y observe que ahora hay una entrada para su tipo de aplicación y otra para la primera instancia de ese tipo.
5. Para eliminar la instancia de aplicación y anular el registro del tipo de aplicación, utilice el script de desinstalación proporcionado en la plantilla.

    ```bash
    ./uninstall.sh
    ```

## <a name="next-steps"></a>Pasos siguientes
* [Información general: Service Fabric y contenedores](service-fabric-containers-overview.md)
* [Interactuación con los clústeres de Service Fabric mediante la CLI de Azure](service-fabric-azure-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-deploy-container-linux/sf-container-yeoman.png



<!--HONumber=Nov16_HO3-->


