---
title: "Introducción a Reliable Services | Microsoft Docs"
description: "Introducción a la creación de una aplicación de Service Fabric de Microsoft Azure mediante servicios con y sin estado."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: 7831886f-7ec4-4aef-95c5-b2469a5b7b5d
ms.service: service-fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/04/2017
ms.author: vturecek
translationtype: Human Translation
ms.sourcegitcommit: 4450ad62a9b05ac4c963ae3271590f9431b782ed
ms.openlocfilehash: 2a2378dbeb5e7994039291deffd35cb04bf8057c


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
Comience por crear una nueva aplicación de Service Fabric. El SDK de Service Fabric para Linux incluye un generador Yeoman para proporcionar el scaffolding para una aplicación de Service Fabric con un servicio sin estado. Comience ejecutando el siguiente comando de Yeoman:

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
protected CompletableFuture<?> runAsync() {
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

En este tutorial, nos centraremos en el método de punto de entrada `runAsync()` . Aquí es donde puede comenzar a ejecutar el código de inmediato.

### <a name="runasync"></a>RunAsync
La plataforma llama a este método cuando hay una instancia del servicio colocada y preparada para ejecutarse. El ciclo de apertura y cierre de una instancia de servicio puede producirse muchas veces durante la vigencia del servicio en su conjunto. Esto puede ocurrir por diversos motivos, incluidos los siguientes:

* El sistema mueve las instancias de servicio para equilibrar los recursos.
* Se producen errores en el código.
* Se actualiza la aplicación o el sistema.
* El hardware subyacente experimenta una interrupción.

Service Fabric es quien se encarga de la coordinación para mantener el servicio con una alta disponibilidad y correctamente equilibrado.

#### <a name="cancellation"></a>Cancelación
Es fundamental que el código en `runAsync()` pueda detener la ejecución cuando Service Fabric se lo notifique. El valor de `CompletableFuture` devuelto desde `runAsync()` se cancela cuando Service Fabric solicita al servicio que detenga la ejecución. En el siguiente ejemplo le mostraremos cómo controlar un evento de cancelación: 

```java
    @Override
    protected CompletableFuture<?> runAsync() {

        CompletableFuture<?> completableFuture = new CompletableFuture<>();
        ExecutorService service = Executors.newFixedThreadPool(1);

        Future<?> userTask = service.submit(() -> {
            while (!Thread.currentThread().isInterrupted()) {
                try
                {
                   logger.log(Level.INFO, this.context().serviceName().toString());
                   Thread.sleep(1000);
                }
                catch (InterruptedException ex)
                {
                    logger.log(Level.INFO, this.context().serviceName().toString() + " interrupted. Exiting");
                    return;
                }
            }
         });

        completableFuture.handle((r, ex) -> {
            if (ex instanceof CancellationException) {
                userTask.cancel(true);
                service.shutdown();
            }
            return null;
        });

        return completableFuture;
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
        logger.log(Level.SEVERE, "Exception in registration: {0}", ex.toString());
        throw ex;
    }
}
```

## <a name="run-the-application"></a>Ejecución de la aplicación
El scaffolding de Yeoman incluye un script de Gradle para compilar la aplicación y scripts de Bash para implementar y anular la implementación de la aplicación. Para ejecutar la aplicación, primero hay que compilarla con Gradle:

```bash
$ gradle
```

Esto creará un paquete de aplicación de Service Fabric que puede implementarse mediante la CLI de Azure para Service Fabric. El script install.sh contiene los comandos de la CLI de Azure necesarios para implementar el paquete de aplicación. Simplemente ejecute el script install.sh para implementar:

```bask
$ ./install.sh
```



<!--HONumber=Dec16_HO2-->


