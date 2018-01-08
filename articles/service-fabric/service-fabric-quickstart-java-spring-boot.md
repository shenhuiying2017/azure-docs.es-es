---
title: "Implementación de una aplicación de Spring Boot en Azure Service Fabric | Microsoft Docs"
description: "Implemente una aplicación de Spring Boot en Azure Service Fabric con el ejemplo Getting Started de Spring Boot."
services: service-fabric
documentationcenter: java
author: suhuruli
manager: msfussell
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: java
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/23/2017
ms.author: suhuruli
ms.custom: mvc, devcenter
ms.openlocfilehash: 544f189e79733c6476bf71e9ce39ab5f35e3d032
ms.sourcegitcommit: 901a3ad293669093e3964ed3e717227946f0af96
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="deploy-a-spring-boot-application"></a>Implementación de una aplicación de Spring Boot
Azure Service Fabric es una plataforma de sistemas distribuidos para implementar y administrar microservicios y contenedores. 

En este tutorial rápido se muestra cómo implementar una aplicación de Spring Boot en Service Fabric. Este tutorial rápido se usa el ejemplo [Getting Started](https://spring.io/guides/gs/spring-boot/) del sitio web de Spring. Al utilizar las conocidas herramientas de línea de comandos, esta guía de inicio rápido le guía a través de la implementación del ejemplo de Spring Boot como una aplicación de Service Fabric. Cuando haya terminado, tendrá el ejemplo Getting Started de Spring Boot funcionando en Service Fabric. 

![Captura de pantalla de la aplicación](./media/service-fabric-quickstart-java-spring-boot/springbootsflocalhost.png)

En esta guía de inicio rápido, ha aprendido a hacer lo siguiente:

> [!div class="checklist"]
> * Implementación de una aplicación de Spring Boot en Service Fabric
> * Implementar la aplicación en el clúster local 
> * Implementar la aplicación en un clúster en Azure
> * Escalar horizontalmente la aplicación en varios nodos
> * Realización de la conmutación por error del servicio sin impacto en la disponibilidad

## <a name="prerequisites"></a>requisitos previos
Para completar esta guía de inicio rápido:
1. [Instale el SDK de Service Fabric y la interfaz de la línea de comandos (CLI) de Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#installation-methods)
2. [Instalación de Git](https://git-scm.com/)
3. [Instalación de Yeoman](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-yeoman-generators-for-containers-and-guest-executables)
4. [Configuración del entorno de Java](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-java-development)

## <a name="download-the-sample"></a>Descarga del ejemplo
En una ventana de comandos, ejecute el comando siguiente para clonar la aplicación de ejemplo Getting Started de Spring Boot en la máquina local.
```
git clone https://github.com/spring-guides/gs-spring-boot.git
```

## <a name="package-the-spring-boot-application"></a>Empaquetado de una aplicación de Spring Boot 
1. En el directorio `gs-spring-boot` que se clonó, ejecute el comando `yo azuresfguest`. 

2. Escriba la siguiente información para cada mensaje. 

    ![Entradas de Yeoman](./media/service-fabric-quickstart-java-spring-boot/yeomanspringboot.png)

3. En la carpeta `SpringServiceFabric/SpringServiceFabric/SpringGettingStartedPkg/code`, cree un archivo llamado `entryPoint.sh`. Agregue al archivo lo siguiente. 

    ```bash
    #!/bin/bash
    BASEDIR=$(dirname $0)
    cd $BASEDIR
    java -jar gs-spring-boot-0.1.0.jar
    ```

En este punto, ha creado una aplicación de Service Fabric del ejemplo Getting Started de Spring Boot que se puede implementar en Service Fabric.

## <a name="run-the-application-locally"></a>Ejecución de la aplicación de forma local
1. Inicie el clúster local mediante la ejecución del siguiente comando:

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```
    El inicio del clúster local tarda algún tiempo. Para confirmar que el clúster está totalmente operativo, acceda a Service Fabric Explorer en **http://localhost:19080**. Los cinco nodos con estado correcto indican que el clúster local está en funcionamiento. 
    
    ![Estado correcto del clúster local](./media/service-fabric-quickstart-java-spring-boot/sfxlocalhost.png)

2. Vaya a la carpeta `gs-spring-boot/SpringServiceFabric`.
3. Ejecute el comando siguiente para conectarse a su clúster local. 

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```
4. Ejecute el script `install.sh`. 

    ```bash
    ./install.sh
    ```

5. Abra el explorador web favorito y acceda a la aplicación a través de **http://localhost:8080**. 

    ![Front-end local de la aplicación](./media/service-fabric-quickstart-java-spring-boot/springbootsflocalhost.png)
    
Ahora puede acceder a la aplicación de Spring Boot que se implementó en un clúster de Service Fabric.  

## <a name="deploy-the-application-to-azure"></a>Implementación de la aplicación en Azure

### <a name="set-up-your-azure-service-fabric-cluster"></a>Configuración del clúster de Service Fabric
Para implementar la aplicación en un clúster de Azure, cree su propio clúster.

Los Party Cluster son clústeres de Service Fabric gratuitos, de duración limitada, hospedados en Azure. Los ejecuta el equipo de Service Fabric, donde cualquier usuario puede implementar aplicaciones y conocer más información sobre la plataforma. Para obtener acceso a un Party Cluster, [siga estas instrucciones](http://aka.ms/tryservicefabric). 

Para obtener información sobre cómo crear su propio clúster, vea [Creación de un clúster de Service Fabric en Azure](service-fabric-tutorial-create-vnet-and-linux-cluster.md).

> [!Note]
> El servicio Spring Boot está configurado para escuchar en el puerto 8080 el tráfico entrante. Asegúrese de que dicho puerto está abierto en el clúster. Si está usando el Party Cluster, el puerto estará abierto.
>

### <a name="deploy-the-application-using-cli"></a>Implementación de la aplicación con la CLI
Ahora que la aplicación y el clúster están listos, puede implementarlos en el clúster directamente desde la línea de comandos.

1. Vaya a la carpeta `gs-spring-boot/SpringServiceFabric`.
2. Ejecute el siguiente comando para conectarse al clúster de Azure. 

    ```bash
    sfctl cluster select --endpoint http://<ConnectionIPOrURL>:19080
    ```
    
    Si el clúster está protegido con un certificado autofirmado, ejecute el comando siguiente: 

    ```bash
    sfctl cluster select --endpoint https://<ConnectionIPOrURL>:19080 --pem <path_to_certificate> --no-verify
    ```
3. Ejecute el script `install.sh`. 

    ```bash
    ./install.sh
    ```

4. Abra el explorador web favorito y acceda a la aplicación a través de **http://\<ConnectionIPOrUrl>:8080**. 

    ![Front-end local de la aplicación](./media/service-fabric-quickstart-java-spring-boot/springbootsfazure.png)
    
Ahora puede acceder a la aplicación de Spring Boot que se implementó en un clúster de Service Fabric.  
    
## <a name="scale-applications-and-services-in-a-cluster"></a>Escalar aplicaciones y servicios en un clúster
Los servicios se pueden escalar en un clúster para adaptarse a un cambio en la carga en los servicios. Para escalar un servicio, cambie el número de instancias que se ejecutan en el clúster. Existen varias formas de escalar los servicios, ya sea mediante scripts o comandos de la CLI de Service Fabric (sfctl). En este ejemplo, se usa Service Fabric Explorer.

Service Fabric Explorer se ejecuta en todos los clústeres de Service Fabric y es accesible desde un explorador. Para ello, vaya al puerto de administración HTTP de clústeres (19080), por ejemplo, `http://localhost:19080`.

Para escalar el servicio front-end web, siga estos pasos:

1. Abra Service Fabric Explorer en el clúster, por ejemplo, `http://localhost:19080`.
2. Haga clic en el botón de puntos suspensivos (tres puntos) situado junto al nodo **fabric:/SpringServiceFabric/SpringGettingStarted** en la vista de árbol y seleccione **Scale Service** (Escalar servicio).

    ![Escalar servicio de Service Fabric Explorer](./media/service-fabric-quickstart-java-spring-boot/sfxscaleservicehowto.png)

    Ahora puede escalar el número de instancias del servicio.

3. Cambie el número a **3** y haga clic en **Scale Service** (Escalar servicio).

    También puede escalar el servicio mediante la línea de comandos.

    ```bash 
    # Connect to your local cluster
    sfctl cluster select --endpoint http://localhost:19080

    # Run Bash command to scale instance count for your service
    sfctl service update --service-id 'SpringServiceFabric~SpringGettingStarted` --instance-count 3 --stateless 
    ``` 

4. Haga clic en el nodo **fabric:/SpringServiceFabric/SpringGettingStarted** en la vista de árbol y expanda el nodo de partición (representado por un GUID).

    ![Escalar servicio de Service Fabric Explorer completado](./media/service-fabric-quickstart-java-spring-boot/sfxscaledservice.png)

    El servicio tiene tres instancias y la vista de árbol muestra en qué nodos se ejecutan las instancias.

Mediante esta sencilla tarea de administración, se aumentan los recursos disponibles para el servicio de Spring a fin de procesar la carga de usuarios. Es importante entender que no hacen falta varias instancias de un servicio para que se ejecute de forma confiable. Si se produce un error en un servicio, Service Fabric se asegurará de que se ejecute una nueva instancia de servicio en el clúster.

## <a name="fail-over-services-in-a-cluster"></a>Servicios de conmutación por error en un clúster 
Para demostrar la conmutación por error del servicio, se simula un reinicio del nodo mediante Service Fabric Explorer. Asegúrese de que solo se esté ejecutando una instancia del servicio. 

1. Abra Service Fabric Explorer en el clúster, por ejemplo, `http://localhost:19080`.
2. Haga clic en el botón de puntos suspensivos (tres puntos) junto al nodo que ejecuta la instancia del servicio y reinicie el nodo. 

    ![Reinicio del nodo de Service Fabric Explorer](./media/service-fabric-quickstart-java-spring-boot/sfxhowtofailover.png)
3. La instancia del servicio se moverá a otro nodo y la aplicación no tendrá ningún tiempo de inactividad. 

    ![Reinicio del nodo de Service Fabric Explorer completado](./media/service-fabric-quickstart-java-spring-boot/sfxfailedover.png)

## <a name="next-steps"></a>pasos siguientes
En este tutorial, ha aprendido a hacer lo siguiente:

> [!div class="checklist"]
> * Implementación de una aplicación de Spring Boot en Service Fabric
> * Implementar la aplicación en el clúster local 
> * Implementar la aplicación en un clúster en Azure
> * Escalar horizontalmente la aplicación en varios nodos
> * Realización de la conmutación por error del servicio sin impacto en la disponibilidad

* Obtenga más información sobre cómo [generar microservicios Java con los modelos de programación de Service Fabric](service-fabric-quickstart-java-reliable-services.md)
* Obtenga más información sobre cómo [configurar la integración e implementación continuas con Jenkins](service-fabric-cicd-your-linux-applications-with-jenkins.md).
* Consulte otros [ejemplos de Java](https://github.com/Azure-Samples/service-fabric-java-getting-started).