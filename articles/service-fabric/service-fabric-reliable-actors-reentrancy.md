<properties
   pageTitle="Reentrada de actores de Service Fabric de Azure"
   description="Introducción a la reentrada de actores de Service Fabric de Azure."
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
   ms.date="03/17/2015"
   ms.author="amanbha"/>


# Reentrada de actores
Los actores de Fabric permiten de manera predeterminada la reentrada en basada en el contexto de llamada lógico. Esto permite que los actores sean reentrantes si están en la misma cadena de contexto de llamada. Por ejemplo, si el actor A envía un mensaje al actor B que envía el mensaje al actor C, como parte del procesamiento del mensaje, si el actor C llama al actor A, el mensaje es reentrante y, por tanto, se permitirá. Los demás mensajes que formen parte de un contexto de llamada distinto se bloquearán en el actor A hasta que complete el procesamiento.

Los actores que no quieran permitir la reentrada basada en el contexto de llamada lógico pueden deshabilitarla decorando la clase del actor con `ReentrantAttribute(ReentrancyMode.Disallowed)`.

```csharp
public enum ReentrancyMode
{
    LogicalCallContext = 1,
    Disallowed = 2
}
```

En el código siguiente se muestra la clase de actor que establece el modo de reentrada a `ReentrancyMode.Disallowed`. En este caso, si un actor envía un mensaje reentrante a otro actor, se generará una excepción de tipo `FabricException`.

```csharp
[Reentrant(ReentrancyMode.Disallowed)]
class VoicemailBoxActor : Actor<VoicemailBox>, IVoicemailBoxActor
{
    ...
}
```
 

<!---HONumber=July15_HO2-->