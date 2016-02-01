
<properties
   pageTitle="Reliable Actors para EL Internet de las cosas | Microsoft Azure"
   description="Reliable Actors de Service Fabric es el bloque de creación clave en un sistema que combina un front-end del sistema de mensajería que admite varios transportes como HTTPS, MQTT y AMQP."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/14/2015"
   ms.author="vturecek"/>

# Patrón de diseño de Actores confiables: Internet de las cosas
Dado que Internet de las cosas (IoT) se convertido en la nueva tendencia junto con los avances tecnológicos en dispositivos y servicios en la nube, los desarrolladores han empezado a tener en cuenta los principales bloques de creación para desarrollar sus sistemas. En el siguiente diagrama se ilustran los escenarios principales que se consiguen mediante Reliable Actors de Azure Service Fabric:

![Escenarios principales de Reliable Actors de Service Fabric][1]

Reliable Actors de Service Fabric son los bloques de creación principales (como nivel intermedio) en un sistema que combina un front-end del sistema de mensajería que admite varios transportes, como HTTPS, MQTT y AMQP y después se comunica con los actores que representan dispositivos individuales. Puesto que los actores pueden mantener el estado, el modelado de secuencias (especialmente el procesamiento de flujos con estado) y la agregación por dispositivo son procesos sencillos. Si se deben conservar los datos, puede vaciarlos fácilmente a petición o con un temporizador. Esto puede ocurrir aunque mantenga los últimos N bits de datos en otra variable para consultarlos con rapidez.

Tenga en cuenta que, para mantener el enfoque en el modelo de Reliable Actors en nuestros ejemplos, hemos omitido deliberadamente los detalles del nivel de evento/mensajería, lo que permite a los actores comunicarse con los dispositivos. Existen básicamente dos escenarios que suelen ir de la mano:

* **Recopilación de los datos de telemetría y estado de un único dispositivo o un conjunto de ellos y mantenimiento de su estado**. Piense en decenas de miles de trampas para ratones (es un escenario de cliente real) enviando datos. Estos datos son tan básicos como si el dispositivo ha atrapado o no alguna plaga. Los datos se agregan por región y, cuando hay suficientes ratones atrapados en una región, se envía a un técnico para limpiar los dispositivos. ¿Una trampa para ratones como un actor? Sí. ¿Un actor de grupo por región como el agregador? Totalmente.

* **Especificación del comportamiento y configuración de los dispositivos en un único dispositivo o un conjunto de ellos**. Piense en dispositivos de energía solar para el hogar para los que el proveedor especifique distintas configuraciones, según la estacionalidad y los patrones de consumo.

## Agrupación de dispositivos y datos de telemetría

En primer lugar, echemos un vistazo a un caso en que los dispositivos, del orden de decenas de miles, se agrupan y envían todos ellos datos de telemetría a su grupo asociado. En el ejemplo siguiente, el cliente ha implementado los dispositivos en cada región. Cuando se activa el dispositivo, lo primero que hace es enviar un mensaje **ActivateMe** con información pertinente, como la ubicación y la versión. El actor asociado con el dispositivo (mediante el identificador de dispositivo) establece el estado inicial del dispositivo, como el almacenamiento del estado de forma local (que también se puede conservar) y el registro de un actor de grupo. En este caso, hemos asignado un grupo aleatorio para la simulación.

Como parte del proceso de inicialización, es posible configurar el dispositivo mediante la recuperación de datos de configuración de un actor de grupo u otro tipo de agente. De este modo, los dispositivos pueden ser bastante “bobos” inicialmente y "volverse listos" tras la inicialización. Cuando se ha hecho esto, el dispositivo y el actor están listos para enviar y procesar los datos de telemetría.

Todos los dispositivos envían periódicamente su información de telemetría al actor correspondiente. Si el actor ya está activado, se usará el mismo actor. De lo contrario, se activará. En este momento, puede recuperar el estado de un almacén estable, si es necesario. Cuando el actor recibe la información de telemetría, la almacena en una variable local.

Se hace de esta forma para simplificar el ejemplo. En una implementación real, probablemente se guardaría en un almacén externo. Esto permite a las operaciones supervisar y diagnosticar el rendimiento y el estado del dispositivo. Por último, se insertan los datos de telemetría en el actor de grupo al que pertenece lógicamente el actor de dispositivo.

## Ejemplo de código de IoT: telemetría

```csharp
public interface IThing : IActor
{
    Task ActivateMe(string region, int version);
    Task SendTelemetryAsync(ThingTelemetry telemetry);
}

[DataContract]
class ThingState
{
	[DataMember]
	public List<ThingTelemetry> _telemetry;
	[DataMember]
	public ThingInfo _deviceInfo;
	[DataMember]
	long _deviceGroupId;
}

public class Thing : StatefulActor<ThingState>, IThing
{

    protected override Task OnActivateAsync()
    {
        State._telemetry = new List<ThingTelemetry>();
        State._deviceGroupId = -1; // not activated
        return base.OnActivateAsync();
    }

    public Task SendTelemetryAsync(ThingTelemetry telemetry)
    {
        State._telemetry.Add(telemetry); // saving data at the device level
        if (State._deviceGroupId != -1)
        {
            var deviceGroup = ActorProxy.Create<IThingGroup>(State._deviceGroupId);
            return deviceGroup.SendTelemetryAsync(telemetry); // sending telemetry data for aggregation
        }
        return Task.FromResult(true);
    }

    public Task ActivateMe(string region, int version)
    {
        State._deviceInfo = new ThingInfo()
        {
            DeviceId = this.GetPrimaryKeyLong(),
            Region = region,
            Version = version
        };

        // based on the info, assign a group... for demonstration we are assigning a random group
        State._deviceGroupId = new Random().Next(10, 12);

        var deviceGroup = ActorProxy.Create<IThingGroup>(State._deviceGroupId);
        return deviceGroup.RegisterDevice(State._deviceInfo);
    }
}
```

A nivel de grupo, el objetivo es supervisar los dispositivos en el grupo y agregar datos de telemetría para generar alertas para los técnicos. En este caso, al cliente le gustaría enviar técnicos a regiones específicas donde hay un cierto número de dispositivos defectuosos. Por supuesto, el cliente quiere reducir los costos mediante la minimización del tiempo que el técnico está en la carretera. Por esta razón, cada actor de grupo mantiene un estado agregado de dispositivos defectuosos por región. Cuando este número alcanza un umbral, el cliente envía a un técnico a la región para reemplazar o reparar estos dispositivos defectuosos.

Echemos un vistazo a cómo se hace:

## Ejemplo de código de IoT: agrupación y agregación

```csharp
public interface IThingGroup : IActor
{
    Task RegisterDevice(ThingInfo deviceInfo);
    Task UnregisterDevice(ThingInfo deviceInfo);
    Task SendTelemetryAsync(ThingTelemetry telemetry);
}

[DataContract]
class ThingGroupState
{
    [DataMember]
    public List<ThingInfo> _devices;
    [DataMember]
    public Dictionary<string, int> _faultsPerRegion;
    [DataMember]
    public List<ThingInfo> _faultyDevices;
}

public class ThingGroup : StatefulActor<ThingGroupState>, IThingGroup
{

    protected override Task OnActivateAsync()
    {
        State._devices = new List<ThingInfo>();
        State._faultsPerRegion = new Dictionary<string, int>();
        State._faultyDevices = new List<ThingInfo>();

        return base.OnActivateAsync();
    }

    public Task RegisterDevice(ThingInfo deviceInfo)
    {
        State._devices.Add(deviceInfo);
        return Task.FromResult(true);
    }

    public Task UnregisterDevice(ThingInfo deviceInfo)
    {
        State._devices.Remove(deviceInfo);
        return Task.FromResult(true);
    }

    public Task SendTelemetryAsync(ThingTelemetry telemetry)
    {
        if (telemetry.DevelopedFault)
        {
            if (false == _faultsPerRegion.ContainsKey(telemetry.Region))
            {
                State._faultsPerRegion[telemetry.Region] = 0;
            }
            State._faultsPerRegion[telemetry.Region]++;
            State._faultyDevices.Add(_devices.Where(d => d.DeviceId == telemetry.DeviceId).FirstOrDefault());

            if (State._faultsPerRegion[telemetry.Region] > _devices.Count(d => d.Region == telemetry.Region) / 3)
            {
                Console.WriteLine("Sending an engineer to repair/replace devices in {0}", telemetry.Region);
                foreach(var device in State._faultyDevices.Where(d => d.Region == telemetry.Region).ToList())
                {
                    Console.WriteLine("\t{0}", device);
                }
            }
        }

        return Task.FromResult(true);
    }
}
```

Como puede ver, es relativamente sencillo. Después de ejecutar pruebas simples, se muestra un resultado como el siguiente:

```
Sending an engineer to repair/replace devices in Richmond
    Device = 33 Region = Richmond Version = 4
    Device = 79 Region = Richmond Version = 5
    Device = 89 Region = Richmond Version = 3
    Device = 63 Region = Richmond Version = 2
    Device = 85 Region = Richmond Version = 4
```

Quizá piense que habría sido preferible que los dispositivos se agrupasen por región. Depende totalmente de usted cómo agrupar o particionar los dispositivos, ya sea por geolocalización, tipo de dispositivo, versión, inquilino u otros criterios.

Aquí hay que tener en cuenta el estado del dispositivo con respecto a los informes y análisis: debe evitar las consultas de distribución ramificada a los actores para crear actores de informes. Esto puede causar problemas de rendimiento y creación de instancias innecesarias, por citar solo dos inconvenientes. Este es el motivo por el que hemos hecho la ilustración del patrón explícita. Se recomiendan dos enfoques para la creación de informes:

* Usar el actor de nivel de grupo, como un agregador, para mantener una vista del grupo. Permitir que cada actor inserte solo datos pertinentes proactivamente en este actor. El actor de nivel de este grupo puede usarse para ver el estado de los dispositivos en el grupo.

* Mantener un almacén explícito que esté diseñado para los informes. Un agregador puede almacenar datos en un búfer e insertarlos periódicamente en un almacén de informes para su posterior consulta y análisis.

## Funcionamiento del dispositivo
Por ahora todo está claro. Pero ¿qué hay de las operaciones en estos dispositivos? Al igual que los dispositivos, los actores pueden exponer interfaces operativas para que un administrador pueda realizar operaciones en los dispositivos. Por ejemplo, piense que un administrador quiere insertar una nueva configuración en un grupo de dispositivos de energía solar para el hogar (otro escenario de la vida real) según los cambios de estación y regionales.

La idea clave aquí es mantener un control granular sobre cada dispositivo mediante actores "Thing", así como controlar las operaciones de grupo mediante actores "ThingGroup". Esto es así si se agregan datos como datos de telemetría procedentes de dispositivos, o bien si se envían datos como la configuración de un gran número de dispositivos. La plataforma se encarga de la distribución de los actores del dispositivo, así como de la mensajería entre ellos. Esto es completamente transparente para el desarrollador.

En cuanto a las comunicaciones de un equipo a otro (M2M), el patrón de concentrador y radio que se analizó en [la sección de gráficos y redes distribuidos](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md) y la interacción directa de actor a actor funcionan bien. En escenarios de M2M, podríamos modelar un actor de “directorio o índice” para un grupo de dispositivos, que les permitan detectar y enviar mensajes entre sí como se muestra a continuación:

![Modelo de un actor de directorio o índice para un grupo de dispositivos][2]

El modelo de Reliable Actors de Service Fabric también se ocupa de las duraciones de los actores. Piénselo de esta forma: si tiene dispositivos siempre activos, así como dispositivos conectados con carácter ocasional, ¿por qué debe guardar en memoria un actor que se ocupa de un dispositivo que se conecta cada 14 horas? Service Fabric le permite guardar y restaurar el estado de un dispositivo cuando y donde desee.

Cada vez más clientes considerarán Reliable Actors de Service Fabric como un componente fundamental para sus implementaciones de IoT.

## Pasos siguientes
[Patrón: memoria caché inteligente](service-fabric-reliable-actors-pattern-smart-cache.md)

[Patrón: gráficos y redes distribuidos](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)

[Patrón: gobernanza de recursos](service-fabric-reliable-actors-pattern-resource-governance.md)

[Patrón: composición del servicio con estado](service-fabric-reliable-actors-pattern-stateful-service-composition.md)

[Patrón: procesamiento distribuido](service-fabric-reliable-actors-pattern-distributed-computation.md)

[Algunos antipatrones](service-fabric-reliable-actors-anti-patterns.md)

[Introducción a los actores de Service Fabric](service-fabric-reliable-actors-introduction.md)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-internet-of-things/internet-of-things-1.png
[2]: ./media/service-fabric-reliable-actors-pattern-internet-of-things/internet-of-things-2.png

<!---HONumber=AcomDC_0121_2016-->