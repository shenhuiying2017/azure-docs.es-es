
<properties
   pageTitle="Actores de Service Fabric de Azure: Internet de las cosas"
   description="Actores de tejido de Azure Service es el bloque de creación clave (como capa intermedia) en un sistema que combina un sistema front-end de mensajería que admite varios protocolos de transporte como HTTPS, MQTT o AMQP y, después, se comunica con los actores que representan dispositivos individuales."
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/01/2015"
   ms.author="claudioc"/>

# Patrón de diseño de los actores de Service Fabric: Internet de las cosas
Dado que IoT se ha convertido en la tendencia nueva junto con los avances tecnológicos en dispositivos y servicios en la nube, los desarrolladores empezaron a considerar los bloques de creación clave en los que desarrollar sus sistemas. En el siguiente diagrama se ilustran los escenarios principales que se consiguen mediante actores de Service Fabric de Azure:

![][1]

Actores de tejido de Azure Service es el bloque de creación clave (como capa intermedia) en un sistema que combina un sistema front-end de mensajería que admite varios protocolos de transporte como HTTPS, MQTT o AMQP y, después, se comunica con los actores que representan dispositivos individuales. Puesto que los actores pueden mantener el estado, el modelado de flujos (especialmente el procesamiento de flujos con estado) y la agregación por dispositivo son procesos sencillos. Si los datos deben conservarse, es posible realizar un vaciado fácilmente a petición o en un temporizador, mientras se mantienen los últimos N bits de datos en otra variable para consultarlos con rapidez. Tenga en cuenta que en nuestros ejemplos, omitimos deliberadamente los detalles de la capa de mensajería y eventos, que permitirá que los actores se comuniquen con los dispositivos, para mantener la atención en el modelo de actor. Existen básicamente dos escenarios que suelen ir de la mano:

* *Recopilación los datos de telemetría y estado de un único dispositivo o un conjunto de ellos y mantenimiento de su estado*. Piense en decenas de miles de trampas para ratones (sí, este es un escenario de cliente real) enviando datos, tan básicos como si el dispositivo de captura ha atrapado un desagradable roedor o no. Los datos se agregan por región y, cuando hay suficientes ratones atrapados en una región, se envía a un técnico para limpiar los dispositivos. ¿Una trampa para ratones como un actor? Totalmente. ¿Un actor de grupo por región como el agregador? Por supuesto que sí.

* *Especificación del comportamiento y la configuración de los dispositivos en un único dispositivo o un conjunto de ellos*. Piense en dispositivos de energía solar para el hogar para los que el proveedor especifique distintas configuraciones, según la estacionalidad y los patrones de consumo.

## Agrupación de dispositivos y datos de telemetría

En primer lugar, echemos un vistazo al caso en que los dispositivos, del orden de decenas de miles, se agrupan y envían todos ellos datos de telemetría a su grupo asociado. En el ejemplo siguiente, el cliente ha implementado los dispositivos en cada región. Cuando se activa el dispositivo, lo primero que hace es enviar un mensaje ActivateMe con información relevante, como la ubicación, la versión, etc. A su vez, el actor asociado con el dispositivo (mediante el identificador de dispositivo) establece el estado inicial del dispositivo, como el almacenamiento del estado de forma local (que también podría haberse conservado) y el registro de un actor de grupo. En este caso, se asigna un grupo aleatorio para la simulación.

Como parte del proceso de inicialización, es posible configurar el dispositivo mediante la recuperación de datos de configuración de un actor de grupo u otro tipo de agente. De este modo, los dispositivos pueden ser bastante bobos inicialmente y "volverse listos" tras la inicialización. Cuando se ha hecho esto, el dispositivo y el actor están listos para enviar y procesar los datos de telemetría.

Todos los dispositivos envían periódicamente su información de telemetría al actor correspondiente. Si el actor ya está activado, se usará el mismo actor. De lo contrario, se activará. En este momento, puede recuperar el estado de un almacén estable si es necesario. Cuando el actor recibe la información de telemetría, la almacena en una variable local. Se hace de esta forma para simplificar el ejemplo. En una implementación real, probablemente se guardaría en un almacén externo para permitir operaciones que supervisen y diagnostiquen el rendimiento y el estado del dispositivo. Por último, se insertan los datos de telemetría en el actor de grupo al que pertenece lógicamente el actor de dispositivo.

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

public class Thing : Actor<ThingState>, IThing
{

    public override Task OnActivateAsync()
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
        return TaskDone.Done;
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

A nivel de grupo, como en el ejemplo, el objetivo es supervisar los dispositivos en el grupo y agregar datos de telemetría para generar alertas para los técnicos. En este caso, al cliente le gustaría enviar técnicos a regiones específicas donde hay un cierto número de dispositivos defectuosos. Por supuesto, el cliente quiere reducir los costos mediante la minimización del tiempo que el técnico está en la carretera. Por esta razón, cada actor de grupo mantiene un estado agregado de dispositivos defectuosos por región. Cuando este número alcanza un umbral, el cliente envía a un técnico a la región para reemplazar o reparar estos dispositivos. Echemos un vistazo a cómo se hace:

## Ejemplo de código IoT: agrupación y agregación

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

public class ThingGroup : Actor<ThingGroupState>, IThingGroup
{

    public override Task OnActivateAsync()
    {
        State._devices = new List<ThingInfo>();
        State._faultsPerRegion = new Dictionary<string, int>();
        State._faultyDevices = new List<ThingInfo>();

        return base.OnActivateAsync();
    }

    public Task RegisterDevice(ThingInfo deviceInfo)
    {
        State._devices.Add(deviceInfo);
        return TaskDone.Done;
    }

    public Task UnregisterDevice(ThingInfo deviceInfo)
    {
        State._devices.Remove(deviceInfo);
        return TaskDone.Done;
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

        return TaskDone.Done;
    }
}
```

Como se puede ver, es bastante sencillo. Después de ejecutar pruebas simples, se muestra un resultado como el siguiente:

```
Sending an engineer to repair/replace devices in Richmond
    Device = 33 Region = Richmond Version = 4
    Device = 79 Region = Richmond Version = 5
    Device = 89 Region = Richmond Version = 3
    Device = 63 Region = Richmond Version = 2
    Device = 85 Region = Richmond Version = 4
```

Por cierto, quizá piense que habría sido preferible que los dispositivos se agrupasen por región. Por supuesto, se puede decir por completo cómo se agrupan y se realizan las particiones de los dispositivos (por ubicación geográfica, tipo de dispositivo, versión, inquilino, etc.). Hay un aspecto que se debe considerar con cuidado: el estado del dispositivo frente a los informes y análisis. Este es el motivo por el que hemos hecho la ilustración del patrón explícita. Hay que evitar las consultas de distribución ramificada en los actores para crear actores de informes; las instancias innecesarias y el rendimiento son dos ejemplos de inconvenientes. Se recomiendan dos enfoques para la creación de informes:

* Usar el actor de nivel de grupo, como un agregador, para mantener una vista del grupo. Permitir que cada actor inserte solo datos relevantes proactivamente en este actor. De esta forma, el actor de nivel de este grupo puede usarse para ver el estado de los dispositivos en el grupo.

* Mantener un almacén explícito que esté diseñado para los informes. Un agregador puede almacenar datos en un búfer e insertarlos periódicamente en un almacén de informes para su posterior consulta y análisis.

## Funcionamiento del dispositivo
Por ahora todo está claro, pero ¿qué hay de las operaciones en estos dispositivos? Al igual que los dispositivos, los actores pueden exponer interfaces operativas para que un administrador pueda realizar operaciones en los dispositivos. Por ejemplo, un administrador quiere insertar una nueva configuración en un grupo de dispositivos de energía solar para el hogar (sí, otro escenario de la vida real) según los cambios de estación y regionales.

La idea principal aquí es que se tiene un control granular sobre cada dispositivo con actores "Thing", así como operaciones de grupo mediante actores "ThingGroup" (ya se estén agregando datos procedentes de dispositivos, como la telemetría, o enviando datos, como la configuración, a un gran número de dispositivos). La plataforma se encarga de la distribución de los actores del dispositivo y la mensajería entre ellos, que es completamente transparente para el desarrollador.

En cuanto a las comunicaciones de un equipo a otro (M2M), el patrón de concentrador y radio que se analizó en la sección de gráficos y redes distribuidas o la interacción directa de actor al actor funcionan bien. En escenarios de M2M, podríamos modelar un actor de directorio o índice para un grupo de dispositivos, que les permitan detectar y enviar mensajes entre sí como se muestra a continuación:

![][2]

Actores de Service Fabric de Azure también se encarga de la duración de los actores. Considérelo de este modo, habrá dispositivos siempre encendidos y dispositivos conectados ocasionalmente. ¿Por qué mantener al actor que se ocupa del dispositivo que se conecta cada 14 horas en la memoria? Service Fabric de Azure permite guardar y restaurar el estado del dispositivo cuándo y dónde se quiera.

La conclusión es que cada vez más clientes considerarán Actores de Service Fabric de Azure como un componente fundamental para sus implementaciones de IoT.

## Pasos siguientes
[Patrón: memoria caché inteligente](service-fabric-reliable-actors-pattern-smart-cache.md)

[Patrón: gráficos y redes distribuidas](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)

[Patrón: gobernanza de recursos](service-fabric-reliable-actors-pattern-resource-governance.md)

[Patrón: composición del servicio con estado](service-fabric-reliable-actors-pattern-stateful-service-composition.md)

[Patrón: procesamiento distribuido](service-fabric-reliable-actors-pattern-distributed-computation.md)

[Algunos antipatrones](service-fabric-reliable-actors-anti-patterns.md)

[Introducción a los actores de Service Fabric](service-fabric-reliable-actors-introduction.md)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-internet-of-things/internet-of-things-1.png
[2]: ./media/service-fabric-reliable-actors-pattern-internet-of-things/internet-of-things-2.png
 

<!---HONumber=July15_HO4-->