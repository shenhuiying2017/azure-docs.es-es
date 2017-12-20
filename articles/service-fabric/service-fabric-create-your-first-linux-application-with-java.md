---
title: "Creación de una aplicación Java de Reliable Actors de Azure Service Fabric en Linux | Microsoft Docs"
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
ms.date: 09/20/2017
ms.author: ryanwi
ms.openlocfilehash: c7625a5670aca5d105601432fedfd0d7a78bb53c
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/18/2017
---
# <a name="create-your-first-java-service-fabric-reliable-actors-application-on-linux"></a>Creación de su primera aplicación Java de Reliable Actors de Service Fabric en Linux
> [!div class="op_single_selector"]
> * [C# - Windows](service-fabric-create-your-first-application-in-visual-studio.md)
> * [Java - Linux](service-fabric-create-your-first-linux-application-with-java.md)
> * [C# - Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
>
>

Este inicio rápido le ayuda a crear su primera aplicación Java de Azure Service Fabric en un entorno de desarrollo de Linux en tan solo unos minutos.  Cuando haya terminado, tendrá una sencilla aplicación Java con un único servicio en el clúster de desarrollo local.  

## <a name="prerequisites"></a>Requisitos previos
Antes de comenzar, instale el SDK de Service Fabric y la CLI de Service Fabric y configure un clúster de desarrollo en su [entorno de desarrollo de Linux](service-fabric-get-started-linux.md). Si usa Mac OS X, puede [configurar un entorno de desarrollo de Linux en una máquina virtual mediante Vagrant](service-fabric-get-started-mac.md).

Instale también la [CLI de Service Fabric](service-fabric-cli.md).

### <a name="install-and-set-up-the-generators-for-java"></a>Instalación y configuración de los generadores para Java
Service Fabric proporciona herramientas de scaffolding que le ayudarán a crear una aplicación Java de Service Fabric desde el terminal mediante el generador de plantillas Yeoman. Siga los pasos siguientes para asegurarse de que el generador de plantillas yeoman de Service Fabric para Java está en funcionamiento en la máquina.
1. Instalación de nodejs y NPM en la máquina

  ```bash
  sudo apt-get install npm
  sudo apt install nodejs-legacy
  ```
2. Instalación del generador de plantillas [Yeoman](http://yeoman.io/) en la máquina desde NPM

  ```bash
  sudo npm install -g yo
  ```
3. Instalación del generador de aplicaciones Yeoman Java de Service Fabric desde NPM

  ```bash
  sudo npm install -g generator-azuresfjava
  ```

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

## <a name="create-the-application"></a>Creación de la aplicación
Una aplicación de Service Fabric contiene uno o varios servicios, cada uno de ellos con un rol específico a la hora de proporcionar la funcionalidad de la aplicación. El generador que instaló en la última sección facilita la creación de su primer servicio y agregar más servicios posteriormente.  También puede crear, compilar e implementar aplicaciones Java de Service Fabric mediante un complemento para Eclipse. Consulte [Creación e implementación de la primera aplicación Java mediante Eclipse](service-fabric-get-started-eclipse.md). En este inicio rápido, use Yeoman para crear una aplicación con un único servicio que almacena y obtiene un valor de contador.

1. En un terminal, escriba ``yo azuresfjava``.
2. Asigne un nombre a la aplicación.
3. Elija el tipo del primer servicio y asígnele un nombre. En este tutorial, elija un servicio de Reliable Actor. Para más información sobre los otros tipos de servicios, consulte [Información general del modelo de programación de Service Fabric](service-fabric-choose-framework.md).
   ![Generador Yeoman de Service Fabric para Java][sf-yeoman]

Si asigna el nombre "HelloWorldActorApplication" a la aplicación y "HelloWorldActor" al actor, se crea el siguiente scaffolding:

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

## <a name="build-the-application"></a>Compilar la aplicación
Las plantillas de Yeoman de Service Fabric incluyen un script de compilación para [Gradle](https://gradle.org/), que se puede usar para compilar la aplicación desde el terminal.
Las dependencias de Java de Service Fabric se recuperan de Maven. Para crear y trabajar con las aplicaciones de Java de Service Fabric debe asegurarse de que tiene JDK y Gradle instalados. Si aún no se han instalado, puede ejecutar el siguiente procedimiento para instalar JDK(openjdk-8-jdk) y Gradle:

  ```bash
  sudo apt-get install openjdk-8-jdk-headless
  sudo apt-get install gradle
  ```

Para compilar y empaquetar la aplicación, ejecute el siguiente código:

  ```bash
  cd myapp
  gradle
  ```

## <a name="deploy-the-application"></a>Implementación de la aplicación
Una vez compilada la aplicación, se puede implementar en el clúster local.

1. Conéctese al clúster de Service Fabric local.

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```

2. Ejecute el script de instalación proporcionado en la plantilla para copiar el paquete de aplicación en el almacén de imágenes del clúster, registrar el tipo de aplicación y crear una instancia de la aplicación.

    ```bash
    ./install.sh
    ```

La aplicación compilada se implementa de la misma forma que cualquier otra aplicación de Service Fabric. Consulte la documentación sobre la [Administración de una aplicación de Service Fabric con la CLI de Service Fabric](service-fabric-application-lifecycle-sfctl.md) para obtener instrucciones detalladas.

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

## <a name="service-fabric-java-libraries-on-maven"></a>Bibliotecas de Java de Service Fabric en Maven
Las bibliotecas de Java de Service Fabric han sido hospedadas en Maven. Puede agregar las dependencias en el archivo ``pom.xml`` o ``build.gradle`` de sus proyectos para usar las bibliotecas de Java de Service Fabric desde **mavenCentral**. 

### <a name="actors"></a>Actores

Compatibilidad con Reliable Actor de Service Fabric para la aplicación.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-actors-preview</artifactId>
      <version>0.12.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-actors-preview:0.12.0'
  }
  ```

### <a name="services"></a>Services

Compatibilidad con Reliable Services de Service Fabric para la aplicación.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-services-preview</artifactId>
      <version>0.12.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-services-preview:0.12.0'
  }
  ```

### <a name="others"></a>Otros
#### <a name="transport"></a>Transporte

Compatibilidad de la capa de transporte para aplicaciones Java de Service Fabric. No es necesario agregar explícitamente esta dependencia a sus aplicaciones Reliable Actor o de servicio, a menos que programe la capa de transporte.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-transport-preview</artifactId>
      <version>0.12.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-transport-preview:0.12.0'
  }
  ```

#### <a name="fabric-support"></a>Compatibilidad con Fabric

Compatibilidad en el nivel de sistema para Service Fabric, que se comunica con el entorno de tiempo de ejecución nativo de Service Fabric. No es necesario agregar explícitamente esta dependencia a sus aplicaciones Reliable Actor o de servicio. Se recuperan automáticamente desde Maven, cuando se incluyen las dependencias anteriores.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-preview</artifactId>
      <version>0.12.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-preview:0.12.0'
  }
  ```

## <a name="migrating-old-service-fabric-java-applications-to-be-used-with-maven"></a>Migración de aplicaciones de Java de Service Fabric anteriores para su uso con Maven
Recientemente se han transferido las bibliotecas de Java de Service Fabric desde el SDK de Java de Service Fabric al repositorio de Maven. Aunque las nuevas aplicaciones que genere con Yeoman o Eclipse generarán proyectos actualizados (que podrán trabajar con Maven), puede actualizar las aplicaciones existentes sin estado o de actor de Service Fabric, que utilizaban anteriormente el SDK de Java de Service Fabric, para que usen las dependencias de Java de Service Fabric de Maven. Siga los pasos mencionados [aquí](service-fabric-migrate-old-javaapp-to-use-maven.md) para asegurarse de que las aplicaciones anteriores funcionan con Maven.

## <a name="next-steps"></a>Pasos siguientes

* [Creación de la primera aplicación Java de Service Fabric en Linux](service-fabric-get-started-eclipse.md)
* [Más información acerca de Reliable Actors](service-fabric-reliable-actors-introduction.md)
* [Interacción con los clústeres de Service Fabric mediante la CLI de Service Fabric](service-fabric-cli.md)
* Más información sobre las [opciones de soporte técnico de Service Fabric](service-fabric-support.md)
* [Introducción a la CLI de Service Fabric](service-fabric-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-yeoman.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-java/sfx-primary.png
[sf-eclipse-templates]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-eclipse-templates.png
