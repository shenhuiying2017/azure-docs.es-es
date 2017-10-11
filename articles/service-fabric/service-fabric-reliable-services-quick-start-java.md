---
title: "Creación del primer microservicio de Azure confiable en Java | Microsoft Docs"
description: "Introducción a la creación de una aplicación de Service Fabric de Microsoft Azure mediante servicios con y sin estado."
services: service-fabric
documentationcenter: java
author: vturecek
manager: timlt
editor: 
ms.assetid: 7831886f-7ec4-4aef-95c5-b2469a5b7b5d
ms.service: service-fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/29/2017
ms.author: vturecek
ms.openlocfilehash: 1ebabe4844732412e04bab8c277f7ebbc4a5737c
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="get-started-with-reliable-services"></a>Introducción a los servicios de confianza
> [!div class="op_single_selector"]
> * [C# en Windows](service-fabric-reliable-services-quick-start.md)
> * [Java en Linux](service-fabric-reliable-services-quick-start-java.md)
>
>

En este artículo se explican los conceptos básicos de Reliable Services de Azure Service Fabric, además de ofrecer orientación sobre cómo crear e implementar una aplicación de Reliable Service sencilla escrita en Java. Este vídeo de Microsoft Virtual Academy también muestra cómo crear un servicio confiable sin estado: <center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=DOX8K86yC_206218965">  
<img src="./media/service-fabric-reliable-services-quick-start-java/ReliableServicesJavaVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

## <a name="installation-and-setup"></a>Instalación y configuración
Antes de comenzar, asegúrese de que el entorno de desarrollo de Service Fabric está configurado en el equipo.
Si tiene que configurarlo, consulte la [introducción a Mac](service-fabric-get-started-mac.md) o a la [introducción a Linux](service-fabric-get-started-linux.md).

## <a name="basic-concepts"></a>Conceptos básicos
Para empezar a trabajar con Reliable Services, solo es necesario comprender cuatro conceptos básicos:

* **Tipo de servicio**: se trata de la implementación del servicio. Se ha definido a través de la clase que escribe y que extiende `StatelessService` y cualquier otro código y dependencias utilizados, junto con un nombre y un número de versión.
* **Instancia de servicio con nombre**: para ejecutar su servicio, debe crear instancias con nombre de su tipo de servicio, de forma muy similar a la creación de instancias de objetos de un tipo de clase. Las instancias de servicio son, en realidad, creaciones de instancias de objetos de la clase de servicio que escribe.
* **Host de servicio**: las instancias de servicio con nombre que cree deben ejecutarse dentro de un host. El host del servicio es simplemente un proceso donde se pueden ejecutar instancias de su servicio.
* **Registro de servicio**: el registro incluye todos los elementos. El tipo de servicio debe registrarse con Service Fabric en tiempo de ejecución en un host de servicio para permitir que Service Fabric cree instancias de él para ejecutarse.  

## <a name="create-a-stateless-service"></a>Creación de un servicio sin estado
Comience por crear una aplicación de Service Fabric. El SDK de Service Fabric para Linux incluye un generador Yeoman para proporcionar el scaffolding para una aplicación de Service Fabric con un servicio sin estado. Comience ejecutando el siguiente comando de Yeoman:

```bash
$ yo azuresfjava
```

Siga las instrucciones para crear un **servicio confiable sin estado**. Para este tutorial, asigne el nombre "HelloWorldApplication" a la aplicación y "HelloWorld" al servicio. El resultado incluirá los directorios para `HelloWorldApplication` y `HelloWorld`.

```bash
HelloWorldApplication/
├── build.gradle
├── HelloWorld
│   ├── build.gradle
│   └── src
│       └── statelessservice
│           ├── HelloWorldServiceHost.java
│           └── HelloWorldService.java
├── HelloWorldApplication
│   ├── ApplicationManifest.xml
│   └── HelloWorldPkg
│       ├── Code
│       │   ├── entryPoint.sh
│       │   └── _readme.txt
│       ├── Config
│       │   └── _readme.txt
│       ├── Data
│       │   └── _readme.txt
│       └── ServiceManifest.xml
├── install.sh
├── settings.gradle
└── uninstall.sh
```

## <a name="implement-the-service"></a>Implementación del servicio
Abra **HelloWorldApplication/HelloWorld/src/statelessservice/HelloWorldService.java**. Esta clase define el tipo de servicio y puede ejecutar cualquier código. La API de servicios proporciona dos puntos de entrada para el código:

* Un método de punto de entrada de extremo abierto, llamado `runAsync()`, donde puede comenzar a ejecutar cualquier carga de trabajo, incluidas cargas de trabajo de proceso de larga duración.

```java
@Override
protected CompletableFuture<?> runAsync(CancellationToken cancellationToken) {
    ...
}
```

* Un punto de entrada de comunicación en el que puede conectar su pila de comunicación preferida. Aquí es donde puede empezar a recibir las solicitudes de usuarios y otros servicios.

```java
@Override
protected List<ServiceInstanceListener> createServiceInstanceListeners() {
    ...
}
```

En este tutorial, nos centraremos en el método de punto de entrada `runAsync()`. Aquí es donde puede comenzar a ejecutar el código de inmediato.

### <a name="runasync"></a>RunAsync
La plataforma llama a este método cuando hay una instancia del servicio colocada y preparada para ejecutarse. En el caso de un servicio sin estado, esto simplemente significa que la instancia de servicio está abierta. Se proporciona un token de cancelación para coordinar cuando la instancia de servicio debe cerrarse. En Service Fabric, este ciclo de apertura y cierre de una instancia de servicio puede producirse muchas veces durante la vigencia del servicio en su conjunto. Esto puede ocurrir por diversos motivos, incluidos los siguientes:

* El sistema mueve las instancias de servicio para equilibrar los recursos.
* Se producen errores en el código.
* Se actualiza la aplicación o el sistema.
* El hardware subyacente experimenta una interrupción.

Service Fabric es quien se encarga de la coordinación para mantener el servicio con una alta disponibilidad y correctamente equilibrado.

`runAsync()` no debe bloquearse de forma sincrónica. La implementación de runAsync debe devolver un valor de CompletableFuture para que el tiempo de ejecución pueda continuar. Si la carga de trabajo necesita implementar una tarea de ejecución prolongada, debe realizarse dentro de CompletableFuture.

#### <a name="cancellation"></a>Cancelación
La cancelación de la carga de trabajo es un esfuerzo cooperativo organizado por el token de cancelación proporcionado. El sistema esperará a que la tarea finalice (finalización correcta, cancelación o con errores) antes de continuar. Es importante respetar el token de cancelación, finalizar cualquier trabajo y cerrar `runAsync()` lo antes posible cuando el sistema solicita la cancelación. En el siguiente ejemplo le mostraremos cómo controlar un evento de cancelación:

```java
    @Override
    protected CompletableFuture<?> runAsync(CancellationToken cancellationToken) {

        // TODO: Replace the following sample code with your own logic
        // or remove this runAsync override if it's not needed in your service.

        CompletableFuture.runAsync(() -> {
          long iterations = 0;
          while(true)
          {
            cancellationToken.throwIfCancellationRequested();
            logger.log(Level.INFO, "Working-{0}", ++iterations);

            try
            {
              Thread.sleep(1000);
            }
            catch (IOException ex) {}
          }
        });
    }
```

### <a name="service-registration"></a>Registro de servicios
Los tipos de servicio deben registrarse con el tiempo de ejecución de Service Fabric. El tipo de servicio se define en el archivo `ServiceManifest.xml` y la clase de servicio que implementa `StatelessService`. El registro del servicio se realiza en el punto de entrada principal del proceso. En este ejemplo, el punto de entrada principal del proceso es `HelloWorldServiceHost.java`:

```java
public static void main(String[] args) throws Exception {
    try {
        ServiceRuntime.registerStatelessServiceAsync("HelloWorldType", (context) -> new HelloWorldService(), Duration.ofSeconds(10));
        logger.log(Level.INFO, "Registered stateless service type HelloWorldType.");
        Thread.sleep(Long.MAX_VALUE);
    }
    catch (Exception ex) {
        logger.log(Level.SEVERE, "Exception in registration:", ex);
        throw ex;
    }
}
```

## <a name="run-the-application"></a>Ejecución de la aplicación

El scaffolding de Yeoman incluye un script de Gradle para compilar la aplicación y scripts de Bash para implementarla y quitarla. Para ejecutar la aplicación, primero hay que compilarla con Gradle:

```bash
$ gradle
```

Esto crea un paquete de aplicación de Service Fabric que puede implementarse mediante la CLI de Service Fabric.

### <a name="deploy-with-service-fabric-cli"></a>Implementación con la CLI de Service Fabric

El script install.sh contiene los comandos de la CLI de Service Fabric necesarios para implementar el paquete de aplicación. Ejecute el script install.sh para implementar la aplicación.

```bash
$ ./install.sh
```

## <a name="next-steps"></a>Pasos siguientes

* [Introducción a la CLI de Service Fabric](service-fabric-cli.md)
