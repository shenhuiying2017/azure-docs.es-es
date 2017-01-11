---
title: "Creación de la primera aplicación de Service Fabric en Linux con C#| Microsoft Docs"
description: "Creación e implementación de aplicación de Service Fabric con C#"
services: service-fabric
documentationcenter: csharp
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: 5a96d21d-fa4a-4dc2-abe8-a830a3482fb1
ms.service: service-fabric
ms.devlang: csharp
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/04/2016
ms.author: subramar
translationtype: Human Translation
ms.sourcegitcommit: 6d8f489ac053db4898741671df73b6abfabeb0dd
ms.openlocfilehash: e3632d89501c1c25b76e9160f0ad82f2b76327d7


---
# <a name="create-your-first-azure-service-fabric-application"></a>Creación de la primera aplicación de Azure Service Fabric
> [!div class="op_single_selector"]
> * [C# - Windows](service-fabric-create-your-first-application-in-visual-studio.md)
> * [Java - Linux](service-fabric-create-your-first-linux-application-with-java.md)
> * [C# - Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
> 
> 

Service Fabric ofrece SDK para compilar servicios en Linux tanto en .NET Core como Java. En este tutorial, veremos cómo crear una aplicación para Linux y cómo compilar un servicio con C# (.NET Core).

## <a name="prerequisites"></a>Requisitos previos
Antes de empezar, asegúrese de [configurar el entorno de desarrollo Linux](service-fabric-get-started-linux.md). Si usa Mac OS X, puede [configurar un entorno one-box de Linux en una máquina virtual mediante Vagrant](service-fabric-get-started-mac.md).

## <a name="create-the-application"></a>Creación de la aplicación
Una aplicación de Service Fabric puede contener uno o varios servicios, cada uno de ellos con un rol específico en la prestación de la funcionalidad de la aplicación. El SDK de Service Fabric para Linux incluye un generador [Yeoman](http://yeoman.io/) que permite crear fácilmente el primer servicio y agregar más posteriormente. Vamos a usar Yeoman para crear una aplicación con un único servicio.

1. En un terminal, escriba el comando siguiente para comenzar a crear el scaffolding: `yo azuresfcsharp`
2. Asigne un nombre a la aplicación.
3. Elija el tipo del primer servicio y asígnele un nombre. En este tutorial, elegiremos un servicio de actor confiable.
   
   ![Generador Yeoman de Service Fabric para C#][sf-yeoman]

> [!NOTE]
> Para más información acerca de las opciones, consulte [Información general del modelo de programación de Service Fabric](service-fabric-choose-framework.md).
> 
> 

## <a name="build-the-application"></a>Compilar la aplicación
Las plantillas de Yeoman de Service Fabric incluyen un script de compilación que puede usar para crear la aplicación desde el terminal (después de ir a la carpeta de la aplicación).

  ```sh
 cd myapp 
 ./build.sh 
  ```

## <a name="deploy-the-application"></a>Implementación de la aplicación
Una vez compilada la aplicación, puede implementarla en el clúster local mediante la CLI de Azure.

1. Conéctese al clúster de Service Fabric local.
   
    ```sh
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
3. Haga clic en el nodo que encontró en el paso anterior y seleccione **Desactivar (reiniciar)** en el menú Acciones. Esta acción reiniciará un nodo del clúster local y forzará una conmutación por error a una réplica secundaria que se ejecuta en otro nodo. Al realizar esta acción, preste atención a la salida del cliente de prueba y tenga en cuenta que el contador sigue incrementándose a pesar de la conmutación por error.

## <a name="adding-more-services-to-an-existing-application"></a>Incorporación de más servicios a una aplicación existente

Para agregar otro servicio a una aplicación ya creada mediante `yo`, realice los pasos siguientes: 
1. Cambie el directorio al directorio raíz de la aplicación existente.  Por ejemplo, `cd ~/YeomanSamples/MyApplication`, si `MyApplication` es la aplicación creada por Yeoman.
2. Ejecute `yo azuresfcsharp:AddService`

## <a name="next-steps"></a>Pasos siguientes
* [Más información acerca de Reliable Actors](service-fabric-reliable-actors-introduction.md)
* [Interactuación con los clústeres de Service Fabric mediante la CLI de Azure](service-fabric-azure-cli.md)
* Más información sobre las [opciones de soporte técnico de Service Fabric](service-fabric-support.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-csharp/yeoman-csharp.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-csharp/sfx-primary.png



<!--HONumber=Dec16_HO2-->


