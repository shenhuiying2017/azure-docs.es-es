<properties
   pageTitle="Introducción a los servicios fiables del Service Fabric de Microsoft Azure"
   description="Creación de una aplicación de Service Fabric mediante servicios con y sin estado."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor="jessebenson"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/02/2015"
   ms.author="vturecek"/>

# Introducción a los servicios fiables del Service Fabric de Microsoft Azure

Una aplicación de Service Fabric contiene uno o más servicios que ejecutan el código. Este tutorial le guiará a través del proceso de creación de aplicaciones de Service Fabric "Hello World" sin estado y con estado mediante el modelo de programación [*servicios fiables*](../Service-Fabric/service-fabric-reliable-services-introduction.md).

Un servicio sin estado es el tipo de servicio que existe principalmente en aplicaciones en la nube hoy en día. El servicio se considera sin estado porque el propio servicio no contiene datos que se necesiten almacenar de forma fiable o ofrecer con alta disponibilidad: en otras palabras, si una instancia de un servicio sin estado se cierra, se perderá todo su estado interno. En estos tipos de servicios, el estado debe almacenarse en un almacén externo, como Tablas de Azure o en una base de datos SQL, para que resulten fiables y tengan una alta disponibilidad.

Service Fabric presenta una nueva clase de servicio con estado: un servicio que puede mantener el estado de forma fiable en el propio servicio, colocados con el código que lo está usando. Service Fabric ofrece una alta disponibilidad de su estado sin necesidad de conservar el estado de un almacén externo.

En este tutorial, implementará un servicio sin estado y con estado que mantenga un contador interno. En el servicio sin estado, el valor del contador se pierde cuando el servicio se reinicia o se mueve. Sin embargo, en el servicio con estado, el estado del contador se vuelve fiable gracias a Service Fabric de modo que si la ejecución del servicio se interrumpe por algún motivo en el medio del recuento, puede retomarse desde donde se quedó.

## Crear un servicio sin estado

Comencemos con un servicio sin estado.

Inicie Visual Studio 2015 RC como **Administrador** y cree un nuevo proyecto de **Servicio sin estado de Service Fabric** denominado *HelloWorld*:

![Utilice el cuadro de diálogo Nuevo proyecto para crear un nuevo servicio sin estado de Service Fabric](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject.png)

Verá dos proyectos en la solución creada:

 + **HelloWorldApplication**: se trata del proyecto de *aplicación* que contiene sus *servicios*. También contiene el manifiesto de aplicación que describe la aplicación y un número de secuencias de comandos de PowerShell que le ayudarán a implementar la aplicación.
 + **HelloWorld** Este es el proyecto de servicio que contiene la implementación del servicio sin estado.


## Implementación del servicio

Abra el archivo **HelloWorld.cs** en el proyecto de servicio. En el Service Fabric, un servicio puede ejecutar cualquier tipo de lógica de negocios. La API de servicios proporciona dos puntos de entrada para el código:

 - Llama a un método de punto de entrada abierto denominado *RunAsync* donde puede comenzar la ejecución de cualquier carga de trabajo que desee, como cargas de trabajo de proceso de larga duración.

```C#
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    ...
}
```

 - Punto de entrada de comunicación en el que puede conectar la pila de comunicación que desee, como la API web, donde puede empezar a recibir las solicitudes de los usuarios u otros servicios.

```C#
protected override ICommunicationListener CreateCommunicationListener()
{
    ...
}
```

En este tutorial, nos centraremos en el método de punto de entrada `RunAsync()` donde puede comenzar inmediatamente a ejecutar su código. La plantilla de proyecto incluye una implementación de ejemplo de `RunAsync()` que incrementa un recuento gradual.

> [AZURE.NOTE]Para obtener más información sobre cómo trabajar con una pila de comunicación, consulte [Introducción a servicios de API web de Microsoft Azure Service Fabric con autohospedaje OWIN](service-fabric-reliable-services-communication-webapi.md)


### RunAsync

```C#
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    // TODO: Replace the following with your own logic.

    int iterations = 0;
    while (!cancellationToken.IsCancellationRequested)
    {
        ServiceEventSource.Current.ServiceMessage(this, "Working-{0}", iterations++);
        await Task.Delay(TimeSpan.FromSeconds(1), cancellationToken);
    }
}
```

La plataforma llama a este método cuando hay una instancia del servicio colocada y preparada para ejecutarse. Para los servicios sin estado, que simplemente significa cuando la instancia de servicio está abierta. Se proporciona un token de cancelación para coordinar cuando la instancia de servicio debe cerrarse. En Service Fabric, este ciclo de apertura y cierre de una instancia de servicio puede producirse muchas veces durante la vigencia del servicio en su conjunto, por varias razones, como:

- El sistema puede mover las instancias de servicio para equilibrar los recursos.
- Se han producido errores en el código.
- Durante las actualizaciones de la aplicación o del sistema.
- Cuando el hardware subyacente experimenta una interrupción inesperada. 

Esta orquestación es administrada por el sistema para mantener el servicio con una alta disponibilidad y correctamente equilibrado.

`RunAsync()` se ejecuta en su propia **tarea**. Tenga en cuenta en el fragmento de código anterior presentamos un bucle **while** (no es necesario programar una tarea independiente para la carga de trabajo). La cancelación de la carga de trabajo es un esfuerzo cooperativo organizado por el token de cancelación proporcionado. El sistema esperará a que la tarea finalice (ya sea mediante una realización correcta, una cancelación o con errores) antes de moverla. Es **importante** respetar el token de cancelación, finalizar cualquier trabajo y cerrar `RunAsync()` lo antes posible cuando el sistema solicita la cancelación.

En este ejemplo de servicio sin estado, el número se almacena en una variable local. Pero dado que se trata de un servicio sin estado, el valor que se va a almacenar solo existe para el ciclo de vida actual de la instancia de servicio en que se encuentra. Cuando se mueve o se reinicia el servicio, el valor se pierde.

## Crear un servicio con estado

Para cambiar el valor sin estado del contador a una alta disponibilidad y persistencia, incluso cuando se mueve o reinicia el servicio, necesitamos un servicio con estado.

En la misma aplicación **HelloWorld**, agregue un nuevo servicio haciendo clic con el botón secundario en el proyecto de aplicación y seleccionando **Nuevo Fabric Service**.

![Agregue un servicio a su aplicación Service Fabric](media/service-fabric-reliable-services-quick-start/hello-stateful-NewService.png)

Seleccione **Servicio con estado de Service Fabric** y asígnele el nombre "HelloWorldStateful". Haga clic en **Agregar**.

![Utilice el cuadro de diálogo Nuevo proyecto para crear un nuevo servicio con estado de Service Fabric](media/service-fabric-reliable-services-quick-start/hello-stateful-NewProject.png)

La aplicación ahora debería tener dos servicios: el servicio sin estado *HelloWorld* y el servicio con estado *HelloWorldStateful*.

Abra **HelloWorldStateful.cs** en *HelloWorldStateful*, que contiene el método `RunAsync` siguiente:

```C#
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    // TODO: Replace the following with your own logic.
    var myDictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");

    while (!cancellationToken.IsCancellationRequested)
    {
        using (var tx = this.StateManager.CreateTransaction())
        {
            var result = await myDictionary.TryGetValueAsync(tx, "Counter-1");
            ServiceEventSource.Current.ServiceMessage(
                this,
                "Current Counter Value: {0}",
                result.HasValue ? result.Value.ToString() : "Value does not exist.");

            await myDictionary.AddOrUpdateAsync(tx, "Counter-1", 0, (k, v) => ++v);

            await tx.CommitAsync();
        }

        await Task.Delay(TimeSpan.FromSeconds(1), cancellationToken);
    }
}
```

### RunAsync

Un servicio con estado tiene los mismos puntos de entrada que un servicio sin estado. La diferencia principal es la disponibilidad de *Colecciones fiables* y *Administrador de estado*. `RunAsync()` en un servicio con estado funciona de forma similar a un servicio sin estado, salvo que en un servicio con estado la plataforma realiza un trabajo adicional en su nombre antes de ejecutar `RunAsync()`, como asegurarse de que el *Administrador de estados* y *Colecciones fiables* están listos para usarse.

### Colecciones fiables y el Administrador de estado

```C#
var myDictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");
```

**IReliableDictionary** es una implementación de diccionario que permite almacenar el estado de manera confiable en el servicio. Esto forma parte de [Colecciones confiables](service-fabric-reliable-services-reliable-collections.md) integradas de Service Fabric. Con Service Fabric y Colecciones fiables, ahora puede almacenar datos directamente en el servicio sin necesidad de un almacén persistente externo, de forma que los datos ofrezcan alta disponibilidad. Service Fabric consigue esto mediante la creación y administración de varias *réplicas* de su servicio para usted al tiempo que proporciona una API que abstrae las complejidades de la administración de las réplicas y sus transiciones de estado.

Colecciones fiables puede almacenar cualquier tipo de .NET, incluidos los tipos personalizados, con un par de advertencias:

 1. Service Fabric hace su estado presente una alta disponibilidad *replicando* el estado entre nodos y almacenándolo en el disco local. Esto significa que todo lo que se almacena en una colección fiable debe ser *serializable*. De forma predeterminada, Colecciones fiables utilizan [DataContract](https://msdn.microsoft.com/library/system.runtime.serialization.datacontractattribute%28v=vs.110%29.aspx) para la serialización, por lo que resulta importante asegurarse de que los tipos sean [compatibles con el serializador de contratos de datos](https://msdn.microsoft.com/library/ms731923%28v=vs.110%29.aspx) cuando se usa el serializador predeterminado.

 2. Los objetos se replican para obtener una alta disponibilidad cuando se confirma una transacción en una colección fiable. Los objetos almacenados en Colecciones fiables se mantienen en la memoria local en el servicio, lo cual significa que tiene una referencia local al objeto.
 
    Es importante no modificar las instancias locales de los objetos sin tener que realizar una operación de actualización en la Colección fiable de una transacción, ya que esos cambios no se replicarán automáticamente.

El *StateManager* se encarga de administrar las colecciones fiables por usted. Simplemente pida al StateManager una colección fiable por nombre en cualquier momento, en cualquier lugar de su servicio y garantizará que obtendrá una referencia. No se recomienda guardar referencias a las instancias de Colección fiable en variables o propiedades de miembro de clase, ya que debe tener especial cuidado de asegurarse de que se establece la referencia a una instancia en todo momento del ciclo de vida del servicio. El StateManager controla este trabajo por usted, optimizado para la repetición de visitas.

### Transaccional y asincrónico

```C#
using (ITransaction tx = this.StateManager.CreateTransaction())
{
    var result = await myDictionary.TryGetValueAsync(tx, "Counter-1");

    await myDictionary.AddOrUpdateAsync(tx, "Counter-1", 0, (k, v) => ++v);

    await tx.CommitAsync();
}
```

Las Colecciones confiables tienen muchas de las mismas operaciones que sus homólogos `System.Collections.Generic` y `System.Collections.Concurrent`, incluido LINQ. Sin embargo, las operaciones sobre las Colecciones confiables son asincrónicas. Esto se debe a que las operaciones de escritura de Colecciones fiables se *replican*, es decir, la operación se envía a otras réplicas del servicio en distintos nodos para obtener una alta disponibilidad.

También admiten operaciones *transaccionales* para que pueda mantener el estado coherente entre varias Colecciones fiables. Por ejemplo, puede quitar de la cola un elemento de trabajo de una cola fiable, realizar una operación en ella y guardar el resultado en un diccionario fiable, todo en una única transacción. Esto se trata como una operación atómica, lo que garantiza que toda la operación se realizará correctamente o nada (por lo que si se produce un error después de que se ha quitado de la cola el elemento, pero antes de que se pueda guardar el resultado, se revertirá toda la transacción y el elemento permanecerá en la cola para su procesamiento).

## Ejecución de la aplicación

Vuelva a la aplicación *HelloWorld*. Ahora puede compilar e implementar sus servicios. Presione **F5**, y la aplicación se creará y se implementará en el clúster local.

Una vez que los servicios se estén ejecutando, podrá ver los eventos ETW generados en una ventana de **Eventos de diagnóstico**. Tenga en cuenta que hay eventos que se muestran desde el servicio sin estado y el servicio con estado en la aplicación. Puede pausar el flujo haciendo clic en el botón *Pausar* y, a continuación, examinando los detalles del mensaje al expandir un mensaje.

>[AZURE.NOTE]Antes de ejecutar la aplicación, asegúrese de tener un clúster de desarrollo local ejecutándose. Consulte la [guía de introducción](service-fabric-get-started.md) para configurar su entorno local.

![Ver eventos de diagnóstico en Visual Studio](media/service-fabric-reliable-services-quick-start/hello-stateful-Output.png)


## Pasos siguientes

[Depuración de la aplicación del Service Fabric en Visual Studio](service-fabric-debugging-your-application.md)

[Introducción a los servicios de la API web de Service Fabric de Microsoft Azure con el autohospedaje de OWIN](service-fabric-reliable-services-communication-webapi.md)

[Obtenga más información acerca de las colecciones fiables](service-fabric-reliable-services-reliable-collections.md)

[Administración de un servicio de Service Fabric](service-fabric-manage-your-service-index.md)

[Referencia para desarrolladores de servicios confiables](https://msdn.microsoft.com/library/azure/dn706529.aspx)
 

<!---HONumber=July15_HO2-->