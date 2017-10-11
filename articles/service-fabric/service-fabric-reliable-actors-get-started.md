---
title: "Creación del primer microservicio de Azure basado en actores en C# | Microsoft Docs"
description: "En este tutorial se explica paso a paso cómo crear, depurar e implementar un servicio de actor sencillo con Reliable Actors de Service Fabric."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: d4aebe72-1551-4062-b1eb-54d83297f139
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2017
ms.author: vturecek
ms.openlocfilehash: 3f447e049ccd33c77f422e8aa703ad6646f9ffa2
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="getting-started-with-reliable-actors"></a>Introducción a Reliable Actors
> [!div class="op_single_selector"]
> * [C# en Windows](service-fabric-reliable-actors-get-started.md)
> * [Java en Linux](service-fabric-reliable-actors-get-started-java.md)
> 
> 

En este artículo se explican los conceptos básicos de Reliable Actors de Azure Service Fabric, además de ofrecer orientación sobre cómo completar los pasos para crear, depurar e implementar una aplicación de Reliable Actors sencilla en Visual Studio.

## <a name="installation-and-setup"></a>Instalación y configuración
Antes de comenzar, asegúrese de que el entorno de desarrollo de Service Fabric está configurado en el equipo.
Para ello, vea instrucciones detalladas sobre [cómo configurar el entorno de desarrollo](service-fabric-get-started.md).

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

## <a name="create-a-new-project-in-visual-studio"></a>Creación de un proyecto en Visual Studio
Inicie Visual Studio 2015 o Visual Studio 2017 como administrador y cree un nuevo proyecto de aplicación de Service Fabric:

![Herramientas de Service Fabric para Visual Studio: nuevo proyecto][1]

En el siguiente cuadro de diálogo puede elegir el tipo de proyecto que desea crear.

![Plantillas de proyecto de Service Fabric][5]

Para el proyecto HelloWorld, se usará el servicio Reliable Actors de Service Fabric.

Después de haber creado la solución, debe ver la estructura siguiente:

![Estructura de proyecto de Service Fabric][2]

## <a name="reliable-actors-basic-building-blocks"></a>Bloques de creación básicos de Reliable Actors
Una solución típica de Reliable Actors se compone de tres proyectos:

* **El proyecto de aplicación (MyActorApplication)**. Este es el proyecto que empaqueta todos los servicios juntos para la implementación. Contiene los scripts de PowerShell y *ApplicationManifest.xml* para administrar la aplicación.
* **El proyecto de interfaz (MyActor.Interfaces)**. Este es el proyecto que contiene la definición de la interfaz del actor. En el proyecto MyActor.Interfaces puede definir las interfaces que usarán los actores de la solución. Las interfaces de actor pueden definirse en cualquier proyecto con el nombre que desee, pero la interfaz define el contrato de actor que comparten la implementación del actor y los clientes que llaman al actor. Por tanto, suele tener sentido definirlas en un ensamblado independiente de la implementación del actor y que se pueda compartir entre varios otros proyectos.

```csharp
public interface IMyActor : IActor
{
    Task<string> HelloWorld();
}
```

* **El proyecto de servicio de actor (MyActor)**. Este es el proyecto que se usa para definir el servicio de Service Fabric que hospedará al actor. Contiene la implementación del actor. La implementación del actor es una clase que deriva de un tipo base `Actor` e implementa las interfaces definidas en el proyecto MyActor.Interfaces. Una clase de actor también debe implementar un constructor que acepta una instancia `ActorService` y `ActorId` y los pasa a la clase `Actor` base. Esto permite la inyección de dependencia de constructor de dependencias de plataforma.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<string> HelloWorld()
    {
        return Task.FromResult("Hello world!");
    }
}
```

El servicio de actor debe registrarse con un tipo de servicio en el runtime de Service Fabric. Para que el servicio de actor ejecute las instancias de actor, el tipo de actor debe estar registrado también con el servicio de actor. El método de registro `ActorRuntime` realiza este trabajo para los actores.

```csharp
internal static class Program
{
    private static void Main()
    {
        try
        {
            ActorRuntime.RegisterActorAsync<MyActor>(
                (context, actorType) => new ActorService(context, actorType, () => new MyActor())).GetAwaiter().GetResult();

            Thread.Sleep(Timeout.Infinite);
        }
        catch (Exception e)
        {
            ActorEventSource.Current.ActorHostInitializationFailed(e.ToString());
            throw;
        }
    }
}

```

Si comienza a partir de un proyecto nuevo en Visual Studio y tiene una única definición de actor, el registro se incluye de forma predeterminada en el código que genera Visual Studio. Si define otros actores en el servicio, deberá agregar el registro de actor mediante:

```csharp
 ActorRuntime.RegisterActorAsync<MyOtherActor>();

```

> [!TIP]
> El sistema en tiempo de ejecución de Service Fabric Actors emite algunos [eventos y contadores de rendimiento relacionados con los métodos de actor](service-fabric-reliable-actors-diagnostics.md#actor-method-events-and-performance-counters). Son útiles para la supervisión del rendimiento y los diagnósticos.
> 
> 

## <a name="debugging"></a>Depuración
Las herramientas de Service Fabric para Visual Studio admiten la depuración en el equipo local. Puede iniciar una sesión de depuración presionando la tecla F5. Visual Studio compila (si es necesario) paquetes. También implementa la aplicación en el clúster de Service Fabric local y asocia el depurador.

Durante el proceso de implementación, puede ver el progreso en la ventana **Resultados** .

![Ventana de resultados de depuración de Service Fabric][3]

## <a name="next-steps"></a>Pasos siguientes
Aprenda más sobre [el uso de la plataforma de Service Fabric por Reliable Actor](service-fabric-reliable-actors-platform.md).

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject.PNG
[2]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-projectstructure.PNG
[3]: ./media/service-fabric-reliable-actors-get-started/debugging-output.PNG
[4]: ./media/service-fabric-reliable-actors-get-started/vs-context-menu.png
[5]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject1.PNG
