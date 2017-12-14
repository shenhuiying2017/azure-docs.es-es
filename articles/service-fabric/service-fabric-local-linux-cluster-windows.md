---
title: "Configuración de un clúster Linux de Azure Service Fabric en Windows | Microsoft Docs"
description: "En este artículo se explica cómo configurar los clústeres Linux de Service Fabric que se ejecutan en máquinas de desarrollo de Windows. Esto es especialmente útil para el desarrollo multiplataforma."
services: service-fabric
documentationcenter: .net
author: suhuruli
manager: mfussell
editor: 
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/20/2017
ms.author: suhuruli
ms.openlocfilehash: f21561269e90e3643ef5d8d48ee28712ee7f611c
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
# <a name="set-up-a-linux-service-fabric-cluster-on-your-windows-developer-machine"></a>Configuración de un clúster Linux de Service Fabric en la máquina del desarrollador de Windows

Este documento describe cómo configurar una instancia local de Service Fabric para Linux en máquinas de desarrollo Windows. La configuración de un clúster Linux local es útil para probar rápidamente las aplicaciones que se destinan a clústeres Linux, pero se desarrollan en un equipo Windows.

## <a name="prerequisites"></a>Requisitos previos
Los clústeres de Service Fabric basados en Linux no se ejecutan de forma nativa en Windows. Para ejecutar un clúster local de Service Fabric, se proporciona una imagen preconfigurada de contenedor de Docker. Antes de comenzar, necesita:

* Al menos 4 GB de RAM
* Versión más reciente de [Docker](https://store.docker.com/editions/community/docker-ce-desktop-windows)
* Acceso a la [imagen](https://hub.docker.com/r/servicefabricoss/service-fabric-onebox/) del contenedor de Docker todo en uno de Service Fabric

>[!TIP]
> * Puede seguir los pasos mencionados en la [documentación](https://store.docker.com/editions/community/docker-ce-desktop-windows/plans/docker-ce-desktop-windows-tier?tab=instructions) oficial de Docker para instalar Docker en Windows. 
> * Una vez que haya terminado la instalación, compruebe si se instaló correctamente siguiendo los pasos mencionados [aquí](https://docs.docker.com/docker-for-windows/#check-versions-of-docker-engine-compose-and-machine)


## <a name="create-a-local-container-and-setup-service-fabric"></a>Creación de un contenedor local e instalación de Service Fabric
Para configurar un contenedor local de Docker y hacer que un clúster de Service Fabric se ejecute en él, realice los pasos siguientes:

1. Extraiga la imagen del repositorio central de Docker:

    ```powershell
    docker pull servicefabricoss/service-fabric-onebox
    ```

2. Actualice la configuración del demonio de Docker en el host con lo siguiente y reinicie dicho demonio: 

    ```json
    {
      "ipv6": true,
      "fixed-cidr-v6": "2001:db8:1::/64"
    }
    ```
    La manera aconsejable de actualizar es ir al icono de Docker > Configuración > Demonio > Avanzada y realizar allí la actualización. Luego reinicie el demonio de Docker para que los cambios surtan efecto. 

3. Inicie una instancia del contenedor todo en uno de Service Fabric con la imagen:

    ```powershell
    docker run -itd -p 19080:19080 --name sfonebox servicefabricoss/service-fabric-onebox
    ```
    >[!TIP]
    > * Al especificar un nombre para la instancia del contenedor, puede tratarlo de forma más legible. 
    > * Si la aplicación está escuchando en determinados puertos, debe especificarse mediante etiquetas -p adicionales. Por ejemplo, si la aplicación está escuchando en el puerto 8080, ejecute docker run -itd -p 19080:19080 -p 8080:8080 --name sfonebox servicefabricoss/service-fabric-onebox

4. Inicie sesión en el contenedor de Docker en el modo interactivo ssh:

    ```powershell
    docker exec -it sfonebox bash
    ```

5. Ejecute el script de instalación, que recuperará las dependencias necesarias, y después inicie el clúster en el contenedor.

    ```bash
    ./setup.sh     # Fetches and installs the dependencies required for Service Fabric to run
    ./run.sh       # Starts the local cluster
    ```

6. Tras completar correctamente el paso 5, puede ir a ``http://localhost:19080`` desde su equipo Windows y podría ver Service Fabric Explorer. En este momento, puede conectarse a este clúster mediante cualquier herramienta desde la máquina del desarrollador de Windows e implementar aplicaciones destinadas a los clústeres de Service Fabric para Linux. 

    > [!NOTE]
    > El complemento de Eclipse no se admite actualmente en Windows. 

## <a name="next-steps"></a>Pasos siguientes
* Introducción a [Eclipse](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-eclipse)
* Consulte otros [ejemplos de Java](https://github.com/Azure-Samples/service-fabric-java-getting-started).


<!-- Image references -->

[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png
[app-parameters-solution-explorer]:./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png