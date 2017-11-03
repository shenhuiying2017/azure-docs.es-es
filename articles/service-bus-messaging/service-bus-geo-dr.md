---
title: "Recuperación ante desastres con localización geográfica de Azure Service Bus | Microsoft Docs"
description: "Cómo usar regiones geográficas para conmutar por error y llevar a cabo una recuperación ante desastres en Azure Service Bus"
services: service-bus-messaging
documentationcenter: 
author: christianwolf42
manager: timlt
editor: 
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2017
ms.author: sethm
ms.openlocfilehash: 49f2992245d694f85b7b1f1c34339f1445c9d699
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/17/2017
---
# <a name="azure-service-bus-geo-disaster-recovery-preview"></a>Recuperación ante desastres con localización geográfica de Azure Service Bus (versión preliminar)

Cuando los centros de datos regionales experimentan un tiempo de inactividad, es crucial que el procesamiento de datos siga funcionando en otra región o centro de datos. De esta forma, la *recuperación ante desastres con localización geográfica* y la *replicación geográfica* son características importantes para cualquier empresa. Azure Service Bus admite tanto la recuperación ante desastres con localización geográfica como la replicación geográfica, en el nivel de espacio de nombres. 

Actualmente, la versión preliminar de la recuperación ante desastres con localización geográfica solo está disponible en dos regiones (**Centro-norte de EE. UU.** y **Centro-sur de EE. UU.)**.

## <a name="outages-and-disasters"></a>Interrupciones y desastres

En el artículo [Procedimientos recomendados para aislar aplicaciones ante desastres e interrupciones de Service Bus](service-bus-outages-disasters.md) se hace una distinción entre "interrupciones" y "desastres", la cual es importante tener en cuenta. Una *interrupción* es la falta de disponibilidad temporal de Azure Service Bus y puede afectar a algunos componentes del servicio, como un almacén de mensajes o incluso todo el centro de datos. Sin embargo, una vez corregido el problema, Service Bus vuelve a estar disponible. Normalmente, una interrupción no provoca la pérdida de mensajes ni otros datos. Un ejemplo de una interrupción de este tipo podría ser un error de corriente en el centro de datos.

Un *desastre* se define como la pérdida permanente o a largo plazo de un centro de datos o una [unidad de escalado](service-bus-architecture.md#service-bus-scale-units) de Service Bus. El centro de datos no volverá necesariamente a estar disponible, o puede que esté fuera de servicio durante horas o días. Algunos ejemplos de esos desastres son los incendios, las inundaciones o los terremotos. Un desastre que se convierte en permanente podría provocar la pérdida de algunos mensajes u otros datos. Sin embargo, en la mayoría de los casos, no debe producirse una pérdida de datos y se pueden recuperar los mensajes una vez que se realiza la copia de seguridad del centro de datos.

La característica de recuperación ante desastres con localización geográfica de Azure Service Bus es una solución de recuperación ante desastres. Los conceptos y el flujo de trabajo descritos en este artículo se aplican a situaciones catastróficas y no a interrupciones transitorias o temporales.  

## <a name="basic-concepts-and-terms"></a>Términos y conceptos básicos

La característica de recuperación ante desastres implementa la recuperación ante desastres de metadatos y depende de espacios de nombres de recuperación ante desastres principales y secundarios. Tenga en cuenta que la característica de recuperación ante desastres con localización geográfica solo está disponible para los [espacios de nombres premium](service-bus-premium-messaging.md). No es necesario realizar ningún cambio de la cadena de conexión, ya que la conexión se realiza a través de un alias.

Los siguientes términos se utilizan en este artículo:

-  *Alias*: Su cadena de conexión principal.

-  *Espacio de nombres principal o secundario*: Describe los espacios de nombres que corresponden al alias. El principal es "activo" y recibe mensajes, y el secundario es "pasivo" y no recibe mensajes. Los metadatos entre ambos están sincronizados, por lo que ambos pueden aceptar sin problemas mensajes sin ningún cambio de código de la aplicación.

-  *Metadatos*: Su representación de objetos en Azure Service Bus. Actualmente solo admitimos metadatos.

-  *Conmutación por error*: El proceso de activación del espacio de nombres secundario. Debe extraer mensajes de su espacio de nombres anteriormente principal una vez que vuelva a estar disponible y, a continuación, eliminarlo. Para crear otra conmutación por error, puede agregar un nuevo espacio de nombres secundario al emparejamiento. Si desea reutilizar el espacio de nombres principal anterior después de una conmutación por error, primero debe quitar todas las entidades existentes del espacio de nombres. Asegúrese de que recibe todos los mensajes de recepción antes de hacerlo.

## <a name="failover-workflow"></a>Flujo de trabajo de la conmutación por error

En la siguiente sección se incluye información general de todo el proceso de configuración de la conmutación por error inicial y de cómo avanzar a partir de ese punto.

![1][]

En primer lugar, configure un espacio de nombres principal y secundario y, a continuación, cree un emparejamiento. Este emparejamiento le proporciona un alias que puede usar para conectarse. Al usar un alias, no es necesario que cambie las cadenas de conexión. Solo pueden agregarse nuevos espacios de nombres al emparejamiento de la conmutación por error. Por último, debe agregar lógica del desencadenador (por ejemplo, lógica de negocios que detecta si el espacio de nombres no está disponible e inicia la conmutación por error). Puede comprobar la disponibilidad del espacio de nombres mediante la funcionalidad [Exploración de mensajes](message-browsing.md) de Service Bus.

Una vez que haya configurado tanto la supervisión como la recuperación ante desastres, puede echar un vistazo al proceso de conmutación por error. Si el desencadenador inicia una conmutación por error o la inicia usted manualmente, se deben seguir dos pasos:

1. En caso de otra interrupción, querrá poder realizar la conmutación por error de nuevo. Por lo tanto, configure un segundo espacio de nombres pasivo y actualice el emparejamiento. 
2. Extraiga mensajes del espacio de nombres anteriormente principal una vez que el nuevo espacio de nombres esté disponible. Después de eso, vuelva a usar o elimine el antiguo espacio de nombres principal.

![2][]

## <a name="set-up-disaster-recovery"></a>Configuración de la recuperación ante desastres

En esta sección se describe cómo crear su propio código de recuperación ante desastres con localización geográfica de Service Bus. Para ello, necesita dos espacios de nombres en ubicaciones independientes; por ejemplo, Sur de EE. UU. y Centro-norte de EE. UU. En el siguiente ejemplo se usa Visual Studio 2017.

1.  Cree un nuevo proyecto **Aplicación de consola (.NET Framework)** en Visual Studio y asígnele un nombre; por ejemplo, **SBGeoDR**.

2.  Instale los siguientes paquetes NuGet:
    1.  Microsoft.IdentityModel.Clients.ActiveDirectory
    2.  Microsoft.Azure.Management.ServiceBus

3. Asegúrese de que la versión del paquete NuGet Newtonsoft.Json que usa es la versión 10.0.3.

3.  Agregue las siguientes instrucciones `using` al código:

    ```csharp
    using System.Threading;
    using Microsoft.Azure.Management.ServiceBus;
    using Microsoft.Azure.Management.ServiceBus.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    ```

4. Modifique su método `main()` para agregar dos espacios de nombres premium:

    ```csharp
    // 1. Create primary namespace (optional).

    var namespaceParams = new SBNamespace()
    {
        Location = "South Central US",
        Sku = new SBSku()
        {
            Name = SkuName.Premium,
            Capacity = 1
        }

    };

    var namespace1 = client.Namespaces.CreateOrUpdate(resourceGroupName, geoDRPrimaryNS, namespaceParams);

    // 2. Create secondary namespace (optional if you already have an empty namespace available).

    var namespaceParams2 = new SBNamespace()
    {
        Location = "North Central US",
        Sku = new SBSku()
        {
            Name = SkuName.Premium,
            Capacity = 1
        }

    };

    // If you re-run this program while namespaces are still paired this operation will fail with a bad request.
    // This is because we block all updates on secondary namespaces once it is paired.

    var namespace2 = client.Namespaces.CreateOrUpdate(resourceGroupName, geoDRSecondaryNS, namespaceParams2);
    ```

5. Habilite el emparejamiento entre los dos espacios de nombres y obtenga el alias que usará más tarde para conectarse a sus entidades:

    ```csharp
    // 3. Pair the namespaces to enable DR.

    ArmDisasterRecovery drStatus = client.DisasterRecoveryConfigs.CreateOrUpdate(
        resourceGroupName,
        geoDRPrimaryNS,
        alias,
        new ArmDisasterRecovery { PartnerNamespace = geoDRSecondaryNS });

    // A similar loop can be used to check if other operations (Failover, BreakPairing, Delete) 
    // mentioned below have been successful.
    while (drStatus.ProvisioningState != ProvisioningStateDR.Succeeded)
    {
        Console.WriteLine("Waiting for DR to be set up. Current state: " +
        drStatus.ProvisioningState);
        drStatus = client.DisasterRecoveryConfigs.Get(
        resourceGroupName,
        geoDRPrimaryNS,
        alias);

        Thread.CurrentThread.Join(TimeSpan.FromSeconds(30));
    }
    ```

Ha configurado correctamente dos espacios de nombres emparejados. Ahora puede crear entidades para observar la sincronización de metadatos. Si desea realizar una conmutación por error inmediatamente después, debe dar algo de tiempo a los metadatos para sincronizarse. Puede agregar un tiempo de suspensión breve, por ejemplo:

```csharp
client.Topics.CreateOrUpdate(resourceGroupName, geoDRPrimaryNS, "myTopic", new SBTopic());
client.Subscriptions.CreateOrUpdate(resourceGroupName, geoDRPrimaryNS, "myTopic", "myTopic-Sub1", new SBSubscription());

// sleeping to allow metadata to sync across primary and secondary
Thread.Sleep(1000 * 60);
```

En este punto puede agregar entidades a través del portal o de Azure Resource Manager y ver cómo se sincronizan. A menos que tenga previsto realizar la conmutación por error manualmente, debe crear una aplicación que supervise su espacio de nombres principal e inicie la conmutación por error si deja de estar disponible. 

## <a name="initiate-a-failover"></a>Inicio de una conmutación por error

El siguiente código muestra cómo iniciar una conmutación por error:

```csharp
// Note that this failover operation is always run against the secondary namespace 
// (because primary might be down at time of failover).

client.DisasterRecoveryConfigs.FailOver(resourceGroupName, geoDRSecondaryNS, alias);
```

Una vez que desencadene la conmutación por error, agregue un nuevo espacio de nombres pasivo y vuelva a establecer el emparejamiento. El código para crear un nuevo emparejamiento se muestra en la sección anterior. Además, debe quitar los mensajes del antiguo espacio de nombres principal una vez que se complete la conmutación por error. Para ver ejemplos de cómo recibir mensajes de una cola, consulte [Introducción a las colas](service-bus-dotnet-get-started-with-queues.md).

## <a name="how-to-disable-geo-disaster-recovery"></a>Cómo deshabilitar la recuperación ante desastres con localización geográfica

El siguiente código muestra cómo deshabilitar un emparejamiento de espacios de nombres:

```csharp
client.DisasterRecoveryConfigs.BreakPairing(resourceGroupName, geoDRPrimaryNS, alias);
```

El siguiente código elimina el alias que creó:

```csharp
// Delete the DR config (alias).
// Note that this operation must run against the namespace to which the alias is currently pointing.
// If you break the pairing and want to delete the namespaces afterwards, you must delete the alias first.

client.DisasterRecoveryConfigs.Delete(resourceGroupName, geoDRPrimaryNS, alias);
```

## <a name="steps-after-a-failover-failback"></a>Pasos después de una conmutación por error (conmutación por recuperación)

Después de una conmutación por error, realice los dos pasos siguientes:

1.  Cree un nuevo espacio de nombres secundario pasivo. El código se muestra en una sección anterior.
2.  Quite los mensajes restantes de su cola.

## <a name="alias-connection-string-and-test-code"></a>Cadena de conexión del alias y código de prueba

Si desea probar el proceso de conmutación por error, puede escribir una aplicación de ejemplo que inserte mensajes en el espacio de nombres principal mediante el alias. Para ello, asegúrese de obtener la cadena de conexión del alias a partir de un espacio de nombres activo. Con la versión preliminar actual, no hay otra interfaz para obtener directamente la cadena de conexión. El siguiente código de ejemplo se conecta antes y después de la conmutación por error:

```csharp
var accessKeys = client.Namespaces.ListKeys(resourceGroupName, geoDRPrimaryNS, "RootManageSharedAccessKey");
var aliasPrimaryConnectionString = accessKeys.AliasPrimaryConnectionString;
var aliasSecondaryConnectionString = accessKeys.AliasSecondaryConnectionString;

if(aliasPrimaryConnectionString == null)
{
    accessKeys = client.Namespaces.ListKeys(resourceGroupName, geoDRSecondaryNS, "RootManageSharedAccessKey");
    aliasPrimaryConnectionString = accessKeys.AliasPrimaryConnectionString;
    aliasSecondaryConnectionString = accessKeys.AliasSecondaryConnectionString;
}
```

## <a name="next-steps"></a>Pasos siguientes

- Consulte la [referencia de la API de REST](/rest/api/servicebus/disasterrecoveryconfigs) de la recuperación ante desastres con localización geográfica aquí.
- Ejecute el [ejemplo de recuperación](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR2) ante desastres con localización geográfica en GitHub.
- Consulte el [ejemplo en el que se envían mensajes a un alias](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1) de la recuperación ante desastres con localización geográfica.

Para más información sobre la mensajería de Service Bus, consulte los siguientes artículos:

* [Elementos fundamentales de Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Colas, temas y suscripciones de Service Bus](service-bus-queues-topics-subscriptions.md)
* [Introducción a las colas de Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Uso de temas y suscripciones de Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
* [API DE REST](/rest/api/servicebus/) 

[1]: ./media/service-bus-geo-dr/geo1.png
[2]: ./media/service-bus-geo-dr/geo2.png
