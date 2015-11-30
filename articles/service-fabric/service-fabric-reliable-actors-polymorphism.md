<properties
   pageTitle="Polimorfismo en el marco de trabajo de Actores | Microsoft Azure"
   description="Cree jerarquías de tipos e interfaces de .NET en el marco de trabajo de Reliable Actors para volver a usar la funcionalidad y las definiciones de la API."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="coreysa"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/15/2015"
   ms.author="seanmck"/>

# Polimorfismo en el marco de trabajo de Reliable Actors

El marco de trabajo de Reliable Actors simplifica la programación de sistemas distribuidos al permitirle crear su servicio mediante muchas de las mismas técnicas que usaría en el diseño orientado a objetos. Una de esas técnicas es el polimorfismo, lo que permite la herencia de tipos e interfaces de elementos primarios más generalizados. La herencia en el marco de trabajo de actores generalmente sigue el modelo de .NET con algunas restricciones adicionales.

## Interfaces

El marco de trabajo de actores requiere definir que se implemente por su tipo de actor una interfaz como mínimo. Esta interfaz se usa para generar una clase de proxy que se puede usar por los clientes para comunicarse con los actores. Las interfaces pueden heredar de otras interfaces siempre que todas las interfaces implementadas por un tipo de actor y todos sus elementos principales se deriven de IActor, la interfaz base definida por la plataforma para los actores. Así, el ejemplo clásico de polimorfismo en el que se usan formas sería algo parecido a lo siguiente:

![Jerarquía de la interfaz de los actores de forma][shapes-interface-hierarchy]


## Tipos

También puede crear una jerarquía de tipos de actores, derivados de la clase Actor base ofrecida por la plataforma. Para actores con estado, puede crear del mismo modo una jerarquía de tipos de estado. En el caso de formas, es posible tener un tipo `Shape` base con un tipo de estado de `ShapeState`.

    public abstract class Shape : Actor<ShapeState>, IShape
    {
        ...
    }

Los subtipos de `Shape` pueden usar subtipos de `ShapeType` para almacenar propiedades más específicas.

    [ActorService(Name = "Circle")]
    public class Circle : Shape, ICircle
    {
        private CircleState CircleState => this.State as CircleState;

        public override ShapeState InitializeState()
        {
            return new CircleState();
        }

        [Readonly]
        public override Task<int> GetVerticeCount()
        {
            return Task.FromResult(0);
        }

       [Readonly]
       public override Task<double> GetArea()
       {
           return Task.FromResult(
               Math.PI*
               this.CircleState.Radius*
               this.CircleState.Radius);
       }

       ...
    }

Observe el atributo `ActorService` en el tipo de actor. Esto indica al SDK de Service Fabric que debería crear automáticamente un servicio para hospedar actores de este tipo. En algunos casos, puede que quiera crear un tipo base que esté pensado únicamente para compartir la funcionalidad con subtipos y que nunca se usará para crear instancias de actores concretos. En esos casos, debe usar la palabra clave `abstract` para indicar que nunca va a crear un actor basado en ese tipo.


## Pasos siguientes

- Vea [cómo el marco de trabajo de Reliable Actors aprovecha la plataforma de Service Fabric](service-fabric-reliable-actors-platform.md) para ofrecer confiabilidad, escalabilidad y un estado coherente.
- Más información sobre el [ciclo de vida de los actores](service-fabric-reliable-actors)

<!-- Image references -->

[shapes-interface-hierarchy]: ./media/service-fabric-reliable-actors-polymorphism/Shapes-Interface-Hierarchy.png

<!---HONumber=Nov15_HO4-->