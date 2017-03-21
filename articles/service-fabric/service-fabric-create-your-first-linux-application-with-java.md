---
title: "Creación de la primera aplicación de microservicios de Azure en Linux con Java | Microsoft Docs"
description: "Creación e implementación de aplicación de Service Fabric con Java"
services: service-fabric
documentationcenter: java
author: seanmck
manager: timlt
editor: 
ms.assetid: 02b51f11-5d78-4c54-bb68-8e128677783e
ms.service: service-fabric
ms.devlang: java
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: seanmck
translationtype: Human Translation
ms.sourcegitcommit: 24d86e17a063164c31c312685c0742ec4a5c2f1b
ms.openlocfilehash: 88b16b0b7c951ab0350649de05c00263ec76e630
ms.lasthandoff: 03/11/2017


---
# <a name="create-your-first-azure-service-fabric-application"></a>Creación de la primera aplicación de Azure Service Fabric
> [!div class="op_single_selector"]
> * [C# - Windows](service-fabric-create-your-first-application-in-visual-studio.md)
> * [Java - Linux](service-fabric-create-your-first-linux-application-with-java.md)
> * [C# - Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
>
>

Service Fabric ofrece SDK para compilar servicios en Linux tanto en .NET Core como Java. En este tutorial, vamos a crear una aplicación para Linux y a compilar un servicio con Java.  

> [!NOTE]
> Java como lenguaje de programación de primera clase integrado es compatible solo con la versión preliminar de Linux (la compatibilidad con Windows está prevista). Sin embargo, todas las aplicaciones, incluidas las aplicaciones de Java, se pueden ejecutar como ejecutables invitados o dentro de contenedores en Windows o Linux. Para más información, consulte [Implementación de un ejecutable invitado en Azure Service Fabric](service-fabric-deploy-existing-app.md) e [Deploy containers to Service Fabric](service-fabric-deploy-container.md) (Implementación de contenedores en Service Fabric).
>

## <a name="video-tutorial"></a>Tutorial en vídeo

El siguiente vídeo de Microsoft Virtual Academy le guiará a través del proceso de creación de una aplicación de Java en Linux:  
<center><a target="\_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=DOX8K86yC_206218965">  
<img src="./media/service-fabric-create-your-first-linux-application-with-java/LinuxVid.png" WIDTH="360" HEIGHT="244">  
</a></center>


## <a name="prerequisites"></a>Requisitos previos
Antes de empezar, asegúrese de [configurar el entorno de desarrollo Linux](service-fabric-get-started-linux.md). Si usa Mac OS X, puede [configurar un entorno one-box de Linux en una máquina virtual mediante Vagrant](service-fabric-get-started-mac.md).

## <a name="create-the-application"></a>Creación de la aplicación
Una aplicación de Service Fabric puede contener uno o varios servicios, cada uno de ellos con un rol específico en la prestación de la funcionalidad de la aplicación. El SDK de Service Fabric para Linux incluye un generador [Yeoman](http://yeoman.io/) que permite crear fácilmente el primer servicio y agregar más posteriormente. Vamos a usar Yeoman para crear una aplicación con un único servicio.

1. En un terminal, escriba ``yo azuresfjava``.
2. Asigne un nombre a la aplicación.
3. Elija el tipo del primer servicio y asígnele un nombre. En este tutorial, elegiremos un servicio de actor confiable.

   ![Generador Yeoman de Service Fabric para Java][sf-yeoman]

> [!NOTE]
> Para más información acerca de las opciones, consulte [Información general del modelo de programación de Service Fabric](service-fabric-choose-framework.md).
>

## <a name="build-the-application"></a>Compilar la aplicación
Las plantillas de Yeoman de Service Fabric incluyen un script de compilación para [Gradle](https://gradle.org/), que se puede usar para compilar la aplicación desde el terminal.

  ```bash
  cd myapp
  gradle
  ```

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

## <a name="start-the-test-client-and-perform-a-failover"></a>Inicio del cliente de prueba y ejecución de una conmutación por error
Los proyectos de actor no hacen nada por sí solos. Necesitan que otro servicio o cliente les envíe mensajes. La plantilla de actor incluye un sencillo script de prueba que puede usar para interactuar con el servicio de actor.

1. Ejecute el script con la utilidad de inspección para ver la salida del servicio de actor.

    ```bash
    cd myactorsvcTestClient
    watch -n 1 ./testclient.sh
    ```

2. En Service Fabric Explorer, busque el nodo que hospeda la réplica principal del servicio de actor. En la captura de pantalla siguiente, es el nodo 3.

    ![Búsqueda de la réplica principal en Service Fabric Explorer][sfx-primary]

3. Haga clic en el nodo que encontró en el paso anterior y seleccione **Desactivar (reiniciar)** en el menú Acciones. Esta acción reiniciará uno de los cinco nodos del clúster local y forzará una conmutación por error a una de las réplicas secundarias que se ejecutan en otro nodo. Al realizar esta acción, preste atención a la salida del cliente de prueba y tenga en cuenta que el contador sigue incrementándose a pesar de la conmutación por error.

## <a name="create-and-deploy-an-application-with-the-eclipse-neon-plugin"></a>Creación e implementación de una aplicación con el complemento de Eclipse Neon

Service Fabric también proporciona el aprovisionamiento para crear, compilar e implementar aplicaciones de Java para Service Fabric mediante Eclipse. Al instalar Eclipse, elija el **IDE de Eclipse para desarrolladores de Java**. Además, Service Fabric admite actualmente el complemento de Eclipse **Neon**. Consulte la documentación detallada en [Creación e implementación de su primera aplicación de Java para Service Fabric mediante el complemento de Service Fabric para Eclipse en Linux](service-fabric-get-started-eclipse.md)

## <a name="adding-more-services-to-an-existing-application"></a>Incorporación de más servicios a una aplicación existente

### <a name="using-command-line-utility"></a>Uso de la utilidad de línea de comandos
Para agregar otro servicio a una aplicación ya creada mediante `yo`, realice los pasos siguientes:
1. Cambie el directorio al directorio raíz de la aplicación existente.  Por ejemplo, `cd ~/YeomanSamples/MyApplication`, si `MyApplication` es la aplicación creada por Yeoman.
2. Ejecute `yo azuresfjava:AddService`

### <a name="using-service-fabric-eclipse-plugin-for-java-on-linux"></a>Uso del complemento de Eclipse para Service Fabric de Java en Linux
Para agregar un servicio a una aplicación existente creada con el complemento de Eclipse para Service Fabric, consulte la documentación [aquí](service-fabric-get-started-eclipse.md#add-new-service-fabric-service-to-your-service-fabric-application).

## <a name="next-steps"></a>Pasos siguientes
* [Creación e implementación de su primera aplicación de Java para Service Fabric mediante el complemento de Service Fabric para Eclipse en Linux](service-fabric-get-started-eclipse.md)
* [Más información acerca de Reliable Actors](service-fabric-reliable-actors-introduction.md)
* [Interactuación con los clústeres de Service Fabric mediante la CLI de Azure](service-fabric-azure-cli.md)
* [Solución de problemas: implementación](service-fabric-azure-cli.md#troubleshooting)
* Más información sobre las [opciones de soporte técnico de Service Fabric](service-fabric-support.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-yeoman.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-java/sfx-primary.png
[sf-eclipse-templates]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-eclipse-templates.png

