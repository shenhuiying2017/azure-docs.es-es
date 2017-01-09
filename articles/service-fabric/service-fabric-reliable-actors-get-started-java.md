---
title: "Introducción a Reliable Actors de Service Fabric | Microsoft Docs"
description: "En este tutorial se explica paso a paso cómo crear, depurar e implementar un servicio de actor sencillo con Reliable Actors de Service Fabric."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: d31dc8ab-9760-4619-a641-facb8324c759
ms.service: service-fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/04/2017
ms.author: vturecek
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 3e349d0c76078889b6e41340ee8bb2f599819ba3


---
# <a name="getting-started-with-reliable-actors"></a>Introducción a Reliable Actors
> [!div class="op_single_selector"]
> * [C# en Windows](service-fabric-reliable-actors-get-started.md)
> * [Java en Linux](service-fabric-reliable-actors-get-started-java.md)
> 
> 

En este artículo se explican los conceptos básicos de Reliable Actors de Azure Service Fabric, además de ofrecer orientación sobre cómo crear e implementar una aplicación de Reliable Actors sencilla escrita en Java.

## <a name="installation-and-setup"></a>Instalación y configuración
Antes de comenzar, asegúrese de que el entorno de desarrollo de Service Fabric está configurado en el equipo.
Si tiene que configurarlo, consulte la [introducción a Mac](service-fabric-get-started-mac.md) o a la [introducción a Linux](service-fabric-get-started-linux.md).

## <a name="basic-concepts"></a>Conceptos básicos
Para empezar a trabajar con Reliable Actors, solo es necesario comprender cuatro conceptos básicos:

* **Servicio de actor**. Reliable Actors se incluye en Reliable Services, que puede implementarse en la infraestructura de Service Fabric. Las instancias de actor se activan en una instancia de servicio con nombre.
* **Registro de actor**. Como con Reliable Services, un servicio de Reliable Actors debe estar registrado en el runtime de Service Fabric. Además, el tipo de actor debe registrarse con el tiempo de ejecución de Actor.
* **Interfaz de actor**. La interfaz de actor se usa para definir una interfaz pública fuertemente tipada de un actor. En la terminología del modelo de Reliable Actors, la interfaz de actor define los tipos de mensajes que el actor puede entender y procesar. Otros actores y aplicaciones de cliente usan la interfaz de actor para "enviar" (asincrónicamente) mensajes al actor. Reliable Actors pueden implementar varias interfaces.
* **Clase ActorProxy**. La clase ActorProxy la usan las aplicaciones cliente para invocar los métodos expuestos a través de una interfaz de actor. La clase ActorProxy ofrece dos funciones importantes:
  
  * Resolución de nombre: Es capaz de ubicar el actor en el clúster (encontrar el nodo del cluster en el que se hospeda).
  * Control de errores: Puede reintentar las invocaciones de métodos y volver a resolver la ubicación del actor, por ejemplo, tras un error que requiere que el actor se reubique en otro nodo del clúster.

Conviene destacar las siguientes reglas que pertenecen a las interfaces de actor:

* No se pueden sobrecargar los métodos de interfaz de actor.
* Los métodos de interfaz de actor no deben tener parámetros out, ref u opcionales.
* No se admiten las interfaces genéricas.

## <a name="create-an-actor-service"></a>Creación de un servicio de actor
Comience por crear una nueva aplicación de Service Fabric. El SDK de Service Fabric para Linux incluye un generador Yeoman para proporcionar el scaffolding para una aplicación de Service Fabric con un servicio sin estado. Comience ejecutando el siguiente comando de Yeoman:

```bash
$ yo azuresfjava
```

Siga las instrucciones para crear un **servicio de Reliable Actors**. Para este tutorial, asigne el nombre "HelloWorldActorApplication" a la aplicación y "HelloWorldActor" al servicio. Se creará el siguiente scaffolding:

```bash
HelloWorldActorApplication/
├── build.gradle
├── HelloWorldActor
│   ├── build.gradle
│   ├── settings.gradle
│   └── src
│       └── reliableactor
│           ├── HelloWorldActorHost.java
│           └── HelloWorldActorImpl.java
├── HelloWorldActorApplication
│   ├── ApplicationManifest.xml
│   └── HelloWorldActorPkg
│       ├── Code
│       │   ├── entryPoint.sh
│       │   └── _readme.txt
│       ├── Config
│       │   ├── _readme.txt
│       │   └── Settings.xml
│       ├── Data
│       │   └── _readme.txt
│       └── ServiceManifest.xml
├── HelloWorldActorInterface
│   ├── build.gradle
│   └── src
│       └── reliableactor
│           └── HelloWorldActor.java
├── HelloWorldActorTestClient
│   ├── build.gradle
│   ├── settings.gradle
│   ├── src
│   │   └── reliableactor
│   │       └── test
│   │           └── HelloWorldActorTestClient.java
│   └── testclient.sh
├── install.sh
├── settings.gradle
└── uninstall.sh
```

## <a name="reliable-actors-basic-building-blocks"></a>Bloques de creación básicos de Reliable Actors
Los conceptos básicos descritos anteriormente se traducen en los bloques de creación básicos de un servicio de Reliable Actors.

### <a name="actor-interface"></a>Interfaz de actor
Contiene la definición de la interfaz del actor. Esta interfaz define el contrato de actor que comparten la implementación del actor y los clientes que llaman al actor. Por tanto, suele tener sentido definirlas en un lugar independiente de la implementación del actor y que se pueda compartir entre varios servicios o aplicaciones cliente.

`HelloWorldActorInterface/src/reliableactor/HelloWorldActor.java`:

```java
public interface HelloWorldActor extends Actor {
    @Readonly   
    CompletableFuture<Integer> getCountAsync();

    CompletableFuture<?> setCountAsync(int count);
}
```

### <a name="actor-service"></a>Servicio de actor
Contiene la implementación del actor y el código de registro del actor. La clase actor implementa la interfaz de actor. Aquí es donde el actor realiza su trabajo.

`HelloWorldActor/src/reliableactor/HelloWorldActorImpl`:

```java
@ActorServiceAttribute(name = "HelloWorldActor.HelloWorldActorService")
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
public class HelloWorldActorImpl extends ReliableActor implements HelloWorldActor {
    Logger logger = Logger.getLogger(this.getClass().getName());

    protected CompletableFuture<?> onActivateAsync() {
        logger.log(Level.INFO, "onActivateAsync");

        return this.stateManager().tryAddStateAsync("count", 0);
    }

    @Override
    public CompletableFuture<Integer> getCountAsync() {
        logger.log(Level.INFO, "Getting current count value");
        return this.stateManager().getStateAsync("count");
    }

    @Override
    public CompletableFuture<?> setCountAsync(int count) {
        logger.log(Level.INFO, "Setting current count value {0}", count);
        return this.stateManager().addOrUpdateStateAsync("count", count, (key, value) -> count > value ? count : value);
    }
}
```

### <a name="actor-registration"></a>Registro del actor
El servicio de actor debe registrarse con un tipo de servicio en el runtime de Service Fabric. Para que el servicio de actor ejecute las instancias de actor, el tipo de actor debe estar registrado también con el servicio de actor. El método de registro `ActorRuntime` realiza este trabajo para los actores.

`HelloWorldActor/src/reliableactor/HelloWorldActorHost`:

```java
public class HelloWorldActorHost {

    public static void main(String[] args) throws Exception {

        try {
            ActorRuntime.registerActorAsync(HelloWorldActorImpl.class, (context, actorType) -> new ActorServiceImpl(context, actorType, ()-> new HelloWorldActorImpl()), Duration.ofSeconds(10));

            Thread.sleep(Long.MAX_VALUE);

        } catch (Exception e) {
            e.printStackTrace();
            throw e;
        }
    }
}
```

### <a name="test-client"></a>Cliente de prueba
Se trata de una sencilla aplicación cliente de prueba que puede ejecutar por separado de la aplicación de Service Fabric para probar el servicio de actor. Este es un ejemplo de dónde se puede usar ActorProxy para activar y establecer comunicación con instancias de actor. No se implementa con el servicio.

### <a name="the-application"></a>Aplicación
Por último, los paquetes de aplicación que el servicio de actor y otros servicios puedan agregar en el futuro para la implementación. Contiene el archivo *ApplicationManifest.xml* y marcadores de posición para el paquete del servicio de actor.

## <a name="run-the-application"></a>Ejecución de la aplicación
El scaffolding de Yeoman incluye un script de Gradle para compilar la aplicación y scripts de Bash para implementar y anular la implementación de la aplicación. Para ejecutar la aplicación, primero hay que compilarla con Gradle:

```bash
$ gradle
```

Esto creará un paquete de aplicación de Service Fabric que puede implementarse mediante la CLI de Azure para Service Fabric. El script install.sh contiene los comandos de la CLI de Azure necesarios para implementar el paquete de aplicación. Simplemente ejecute el script install.sh para implementar:

```bask
$ ./install.sh
```



<!--HONumber=Nov16_HO3-->


