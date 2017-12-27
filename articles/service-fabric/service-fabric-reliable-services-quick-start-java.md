---
title: "Creación del primer microservicio de Azure confiable en Java | Microsoft Docs"
description: "Introducción a la creación de una aplicación de Service Fabric de Microsoft Azure mediante servicios con y sin estado."
services: service-fabric
documentationcenter: java
author: suhuruli
manager: timlt
editor: 
ms.assetid: 7831886f-7ec4-4aef-95c5-b2469a5b7b5d
ms.service: service-fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: suhuruli
ms.openlocfilehash: ab675207094bc8ee317573192c33c20039780fe2
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
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

    return CompletableFuture.runAsync(() -> {
        long iterations = 0;
        while(true)
        {
        cancellationToken.throwIfCancellationRequested();
        logger.log(Level.INFO, "Working-{0}", ++iterations);

        try {
            Thread.sleep(1000);
        } catch (InterruptedException ex){}
        }
    });
}
```

En este ejemplo de servicio sin estado, el número se almacena en una variable local. Pero dado que se trata de un servicio sin estado, el valor almacenado solo existe para el ciclo de vida actual de la instancia de su servicio. Cuando se mueve o se reinicia el servicio, el valor se pierde.

## <a name="create-a-stateful-service"></a>Creación de un servicio con estado
Service Fabric presenta un nuevo tipo de servicio que es con estado. Un servicio con estado puede mantener el estado confiablemente dentro del propio servicio, ubicado junto al código que lo está usando. Service Fabric ofrece una alta disponibilidad del estado sin necesidad de conservarlo en un almacén externo.

Para cambiar el valor sin estado del contador a una alta disponibilidad y persistencia, incluso cuando se mueve o reinicia el servicio, se necesita un servicio con estado.

En el mismo directorio que la aplicación HelloWorld, puede agregar un nuevo servicio mediante la ejecución del comando `yo azuresfjava:AddService`. Elija "Reliable Stateful Service" para el marco y asigne el nombre de "HelloWorldStateful" al servicio. 

La aplicación ahora debe tener dos servicios: el servicio sin estado HelloWorld y el servicio con estado HelloWorldStateful.

Un servicio con estado tiene los mismos puntos de entrada que un servicio sin estado. La principal diferencia es la disponibilidad de un proveedor de estado que puede almacenar estados de forma confiable. Service Fabric incluye una implementación de proveedor de estado denominada Reliable Collections, que permite crear estructuras de datos con replicación mediante Reliable State Manager. Un servicio de confianza con estado usa este proveedor de estado de forma predeterminada.

Abra HelloWorldStateful.java en **HelloWorldStateful -> src**, que contiene el método RunAsync siguiente:

```java
@Override
protected CompletableFuture<?> runAsync(CancellationToken cancellationToken) {
    Transaction tx = stateManager.createTransaction();
    return this.stateManager.<String, Long>getOrAddReliableHashMapAsync("myHashMap").thenCompose((map) -> {
        return map.computeAsync(tx, "counter", (k, v) -> {
            if (v == null)
                return 1L;
            else
                return ++v;
            }, Duration.ofSeconds(4), cancellationToken)
                .thenCompose((r) -> tx.commitAsync())
                .whenComplete((r, e) -> {
            try {
                tx.close();
            } catch (Exception e) {
                logger.log(Level.SEVERE, e.getMessage());
            }
        });
    });
}
```

### <a name="runasync"></a>RunAsync
`RunAsync()` funciona de forma similar en los servicios con y sin estado. Sin embargo, en los servicios con estado, la plataforma realiza una tarea adicional de forma automática antes de ejecutar `RunAsync()`. Al realizarla, se puede garantizar que Reliable State Manager y Reliable Collections estén listos para usarse.

### <a name="reliable-collections-and-the-reliable-state-manager"></a>Reliable Collections y Reliable State Manager
```java
ReliableHashMap<String,Long> map = this.stateManager.<String, Long>getOrAddReliableHashMapAsync("myHashMap")
```

[ReliableHashMap](https://docs.microsoft.com/en-us/java/api/microsoft.servicefabric.data.collections._reliable_hash_map) es una implementación de diccionario que se puede usar para almacenar estados en el servicio de manera confiable. Con Service Fabric y Reliable Hashmap, ahora puede almacenar datos directamente en el servicio sin necesidad de un almacén persistente externo. Gracias a Reliable Hashmap, los datos tendrán una alta disponibilidad. Service Fabric realiza esta tarea creando y administrando automáticamente varias *réplicas* de su servicio. También proporciona una API que elimina las complejidades derivadas de la administración de las réplicas y sus transiciones de estado.

Reliable Collections puede almacenar cualquier tipo de Java, incluidos los tipos personalizados, con un par de advertencias:

* Service Fabric logra que su estado presente una alta disponibilidad mediante su *replicación* entre nodos y Reliable Hashmap almacena los datos en el disco local en cada réplica. Es decir, todo lo que se almacena en Reliable Hashmap debe ser *serializable*. 
* Los objetos se replican para obtener una alta disponibilidad cuando se confirman transacciones en Reliable Hashmap. Los objetos almacenados en Reliable Hashmap se mantienen en la memoria local del servicio. Esto significa que tiene una referencia local al objeto.
  
   Es importante no modificar las instancias locales de los objetos sin tener que realizar una operación de actualización en la colección fiable de una transacción. Esto se debe a que los cambios en las instancias de objetos locales no se replicarán automáticamente. Debe volver a insertar el objeto en el diccionario o utilizar uno de los métodos de *actualización* del diccionario.

Reliable State Manager administra instancias de Reliable Hashmap de forma automática. Basta con solicitar a Reliable State Manager una colección confiable por nombre en cualquier momento y lugar del servicio. Reliable State Manager garantiza la obtención de una referencia. No recomendamos guardar referencias a instancias de colecciones fiables en variables o propiedades de miembros de clase. Debe tener especial cuidado para asegurarse de que se establece la referencia a una instancia en todo momento del ciclo de vida de servicio. Reliable State Manager controla esta tarea automáticamente y está optimizado para la repetición de visitas.


### <a name="transactional-and-asynchronous-operations"></a>Operaciones transaccionales y asincrónicas
```java
return map.computeAsync(tx, "counter", (k, v) -> {
    if (v == null)
        return 1L;
    else
        return ++v;
    }, Duration.ofSeconds(4), cancellationToken)
        .thenCompose((r) -> tx.commitAsync())
        .whenComplete((r, e) -> {
    try {
        tx.close();
    } catch (Exception e) {
        logger.log(Level.SEVERE, e.getMessage());
    }
});
```

Sin embargo, las operaciones relacionadas con Reliable Hashmap son asincrónicas. El motivo es que las operaciones de escritura con Reliable Collections realizarán operaciones de E/S para replicar y conservar los datos en el disco.

Las operaciones de Reliable Hashmap son *transaccionales*, de modo que el estado puede mantenerse de forma coherente entre varias operaciones y colecciones de Reliable Hashmap. Por ejemplo, puede obtener un elemento de trabajo de Reliable Dictionary, realizar una operación en él y guardar el resultado en otra instancia Reliable Hashmap, todo en una única transacción. Se trata como una operación atómica y así se garantiza que toda la operación se complete correctamente o se revierta por completo. Si se produce un error después de quitar el elemento de la cola pero antes de guardar el resultado, toda la transacción se revierte y el elemento permanece en la cola para su procesamiento.


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
