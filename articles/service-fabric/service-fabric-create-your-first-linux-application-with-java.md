---
title: "Creación de una aplicación de Java de Reliable Actors de Azure Service Fabric en Linux | Microsoft Docs"
description: "Aprenda a crear e implementar una aplicación Java de Reliable Actors de Service Fabric en cinco minutos."
services: service-fabric
documentationcenter: java
author: rwike77
manager: timlt
editor: 
ms.assetid: 02b51f11-5d78-4c54-bb68-8e128677783e
ms.service: service-fabric
ms.devlang: java
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2017
ms.author: ryanwi
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 254f38a600ea4026120bc411368eeb01310e56b2
ms.contentlocale: es-es
ms.lasthandoff: 07/21/2017

---
# <a name="create-your-first-java-service-fabric-reliable-actors-application-on-linux"></a>Creación de su primera aplicación Java de Reliable Actors de Service Fabric en Linux

Este inicio rápido le ayuda a crear su primera aplicación Java de Azure Service Fabric en un entorno de desarrollo de Linux en tan solo unos minutos.  Cuando haya terminado, tendrá una sencilla aplicación Java con un único servicio en el clúster de desarrollo local.  

## <a name="prerequisites"></a>Requisitos previos
Antes de comenzar, instale el SDK de Service Fabric y la CLI de Azure CLI y configure un clúster de desarrollo en su [entorno de desarrollo de Linux](service-fabric-get-started-linux.md). Si usa Mac OS X, puede [configurar un entorno de desarrollo de Linux en una máquina virtual mediante Vagrant](service-fabric-get-started-mac.md).

También deseará configurar la [CLI de Azure 2.0](service-fabric-azure-cli-2-0.md) (se recomienda) o la [CLI de XPlat](service-fabric-azure-cli.md) para implementar la aplicación.

## <a name="create-the-application"></a>Creación de la aplicación
Una aplicación de Service Fabric contiene uno o varios servicios, cada uno de ellos con un rol específico a la hora de proporcionar la funcionalidad de la aplicación. El SDK de Service Fabric para Linux incluye un generador [Yeoman](http://yeoman.io/) que permite crear fácilmente el primer servicio y agregar más posteriormente.  También puede crear, compilar e implementar aplicaciones Java de Service Fabric mediante un complemento para Eclipse. Consulte [Creación e implementación de la primera aplicación Java mediante Eclipse](service-fabric-get-started-eclipse.md). En este inicio rápido, use Yeoman para crear una aplicación con un único servicio que almacena y obtiene un valor de contador.

1. En un terminal, escriba ``yo azuresfjava``.
2. Asigne un nombre a la aplicación. 
3. Elija el tipo del primer servicio y asígnele un nombre. En este tutorial, elija un servicio de Reliable Actor. Para más información sobre los otros tipos de servicios, consulte [Información general del modelo de programación de Service Fabric](service-fabric-choose-framework.md).
   ![Generador Yeoman de Service Fabric para Java][sf-yeoman]

## <a name="build-the-application"></a>Compilar la aplicación
Las plantillas de Yeoman de Service Fabric incluyen un script de compilación para [Gradle](https://gradle.org/), que se puede usar para compilar la aplicación desde el terminal. Para compilar y empaquetar la aplicación, ejecute el siguiente código:

  ```bash
  cd myapp
  gradle
  ```

## <a name="deploy-the-application"></a>Implementación de la aplicación
Una vez compilada la aplicación, se puede implementar en el clúster local.

### <a name="using-xplat-cli"></a>Uso de la CLI multiplataforma

1. Conéctese al clúster de Service Fabric local.

    ```bash
    azure servicefabric cluster connect
    ```

2. Ejecute el script de instalación proporcionado en la plantilla para copiar el paquete de aplicación en el almacén de imágenes del clúster, registrar el tipo de aplicación y crear una instancia de la aplicación.

    ```bash
    ./install.sh
    ```

### <a name="using-azure-cli-20"></a>Uso de la CLI de Azure 2.0

La aplicación compilada se implementa de la misma forma que cualquier otra aplicación de Service Fabric. Para obtener instrucciones detalladas, consulte la documentación relativa a la [administración de una aplicación de Service Fabric con la CLI de Azure](service-fabric-application-lifecycle-azure-cli-2-0.md).

Los parámetros de estos comandos se pueden encontrar en los manifiestos generados dentro del paquete de aplicación.

Una vez que se haya implementado la aplicación, abra un explorador y navegue hasta el [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) en [http://localhost:19080/Explorer](http://localhost:19080/Explorer).
Luego, expanda el nodo **Applications** y observe que ahora hay una entrada para su tipo de aplicación y otra para la primera instancia de dicho tipo.

## <a name="start-the-test-client-and-perform-a-failover"></a>Inicio del cliente de prueba y ejecución de una conmutación por error
Los actores no hacen nada por sí solos, necesitan que otro servicio o cliente les envíe mensajes. La plantilla de actor incluye un sencillo script de prueba que puede usar para interactuar con el servicio de actor.

1. Ejecute el script con la utilidad de inspección para ver la salida del servicio de actor.  El script de prueba llamad al método `setCountAsync()` en el servicio de actor para aumentar el contador, llama al método `getCountAsync()` en el servicio de actor para obtener el nuevo valor de contador y muestra ese valor en la consola.

    ```bash
    cd myactorsvcTestClient
    watch -n 1 ./testclient.sh
    ```

2. En Service Fabric Explorer, busque el nodo que hospeda la réplica principal del servicio de actor. En la captura de pantalla siguiente, es el nodo 3. La réplica de servicio principal gestiona las operaciones de lectura y escritura.  Los cambios en el estado del servicio se replican entonces en las réplicas secundarias, que se ejecutan en los nodos 0 y 1 en la siguiente captura de pantalla.

    ![Búsqueda de la réplica principal en Service Fabric Explorer][sfx-primary]

3. En **Nodos**, haga clic en el nodo que encontró en el paso anterior y seleccione **Desactivar (reiniciar)** en el menú Acciones. Esta acción reinicia el nodo que ejecuta la réplica principal del servicio y fuerza una conmutación por error a una de las réplicas secundarias que se ejecutan en otro nodo.  Esa réplica secundaria se promueve a principal, se crea otra réplica secundaria en un nodo diferente y la réplica principal comienza a aceptar operaciones de lectura/escritura. Cuando se reinicie el nodo, preste atención a la salida del cliente de prueba y observe que el contador sigue incrementándose a pesar de la conmutación por error.

## <a name="remove-the-application"></a>Eliminación de la aplicación
Use el script de desinstalación proporcionado en la plantilla para eliminar la instancia de la aplicación, anular el registro del paquete de aplicación y quitar dicho paquete del almacén de imágenes del clúster.

```bash
./uninstall.sh
```

En Service Fabric Explorer, verá que la aplicación y el tipo de aplicación ya no aparecen en el nodo **Aplicaciones**.

## <a name="next-steps"></a>Pasos siguientes
* [Creación de la primera aplicación Java de Service Fabric en Linux](service-fabric-get-started-eclipse.md)
* [Más información acerca de Reliable Actors](service-fabric-reliable-actors-introduction.md)
* [Interacción con clústeres de Service Fabric mediante la CLI de Azure](service-fabric-azure-cli.md)
* [Solución de problemas: implementación](service-fabric-azure-cli.md#troubleshooting)
* Más información sobre las [opciones de soporte técnico de Service Fabric](service-fabric-support.md)

## <a name="related-articles"></a>Artículos relacionados

* [Getting started with Service Fabric and Azure CLI 2.0](service-fabric-azure-cli-2-0.md) (Introducción a Service Fabric y la CLI de Azure 2.0)
* [Getting started with Service Fabric XPlat CLI](service-fabric-azure-cli.md) (Introducción a la CLI de XPlat de Service Fabric)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-yeoman.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-java/sfx-primary.png
[sf-eclipse-templates]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-eclipse-templates.png

