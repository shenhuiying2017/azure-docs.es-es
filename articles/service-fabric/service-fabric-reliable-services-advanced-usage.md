<properties
   pageTitle="Uso avanzado del modelo de programación de servicios fiables de Service Fabric"
   description="Obtenga información sobre el uso avanzado del modelo de programación del servicio fiable de Service Fabric para obtener una mayor flexibilidad en los servicios."
   services="Service-Fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor="masnider"/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="06/09/2015"
   ms.author="jesseb"/>

# Uso avanzado del modelo de programación de servicios fiables
Service Fabric simplifica la escritura y la administración de servicios con y sin estado fiables. En esta guía se hablará de los usos avanzados del modelo de programación de los servicios fiables para obtener más control y flexibilidad sobre sus servicios. Antes de leer esta guía, familiarícese con [el modelo de programación de servicios fiables](service-fabric-reliable-services-introduction.md).

## Clases base del servicio sin estado
La clase base StatelessService proporciona CreateCommunicationListener() y RunAsync(), que es suficiente para la mayoría de los servicios sin estado. La clase StatelessServiceBase subyace a StatelessService y expone los eventos de ciclo de vida de servicio adicionales. Consulte la documentación de referencia para desarrolladores en [StatelessService](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.services.statelessservice.aspx) y [StatelessServiceBase](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.services.statelessservicebase.aspx) para obtener más información.

- `void OnInitialize(StatelessServiceInitializiationParameters)` OnInitialize es el primer método al que llama Service Fabric. La información de inicialización del servicio se proporciona como nombre del servicio, Id. de partición, Id. de instancia e información de paquete de código. Aquí no se debe realizar ningún procesamiento complejo. La inicialización prolongada debe realizarse en OnOpenAsync.

- `Task OnOpenAsync(IStatelessServicePartition, CancellationToken)` OnOpenAsync es llamado cuando está a punto de usarse la instancia de servicio sin estado. En este momento, se pueden iniciar las tareas de inicialización del servicio ampliado.

- `Task OnCloseAsync(CancellationToken)` OnCloseAsync es llamado cuando la instancia de servicio sin estado se va a apagar correctamente. Esto puede ocurrir cuando se está actualizando el código de servicio, cuando se está moviendo la instancia de servicio debido al equilibrio de carga o se detecta un error transitorio. OnCloseAsync puede utilizarse para cerrar de manera segura todos los recursos, detener cualquier procesamiento en segundo plano, terminar de guardar el estado externo o cerrar conexiones existentes.

- `void OnAbort()` OnCloseAsync es llamado cuando la instancia de servicio sin estado se apagar a la fuerza. Normalmente se llama cuando se detecta un error permanente en el nodo o cuando Service Fabric no puede administrar el ciclo de vida de la instancia de servicio debido a errores internos.

## Clases base del servicio con estado
La clase base StatefulService debería ser suficiente para la mayoría de servicios con estado. De modo similar a los servicios sin estado, la clase StatefulServiceBase subyace a StatefulService y expone los eventos de ciclo de vida de servicio adicionales. Además, permite proporcionar un proveedor de estado de confianza personalizado y, opcionalmente, dar soporte a los agentes de escucha de comunicación en Secundarios. Consulte la documentación de referencia para desarrolladores en [StatefulService](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.services.statefulservice.aspx) y [StatefulServiceBase](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.services.statefulservicebase.aspx) para obtener más información.

- `Task OnChangeRoleAsync(ReplicaRole, CancellationToken)` OnChangeRoleAsync es llamado cuando el servicio con estado está cambiando los roles, por ejemplo, a principal o secundario. Las réplicas principales reciben el estado de escritura (pueden crear y escribir en las colecciones fiables), mientras que las réplicas secundarias reciben el estado de lectura (solo pueden leer de las colecciones fiables existentes). Puede iniciar o actualizar las tareas en segundo plano en respuesta a cambios de rol, como realizar validaciones de solo lectura, la generación de informes o minería de datos en una base de datos secundaria.

- `IStateProviderReplica CreateStateProviderReplica()` Se espera que un servicio con estado tenga un proveedor de estado fiable. StatefulService utiliza la clase ReliableStateManager, que proporciona colecciones fiables (por ejemplo, diccionarios y colas). Puede proporcionar su propio proveedor si desea administrar el estado por sí mismo o ampliar la funcionalidad de uno de los proveedores de estado integrados.

- `bool EnableCommunicationListenerOnSecondary { get; }` De forma predeterminada, solo se crean escuchas de comunicación en Primarios. StatefulService y StatefulServiceBase permiten invalidar esta propiedad para permitir la creación de los agentes de escucha de comunicación en Secundarios. Puede permitir que Secundarios controle solicitudes de solo lectura para mejorar el rendimiento en las cargas de trabajo con muchas lecturas.

    > [AZURE.NOTE]Usted es responsable de garantizar que Secundarios no intente crear o escribir en colecciones confiables. Si se intenta escribir en Secundario se producirá una excepción que, si no se controla, hará que la réplica se cierre y se vuelva a abrir.

StatefulServiceBase también proporciona los mismos cuatro eventos de ciclo de vida que StatelessServiceBase, con la misma semántica y casos de uso:

- `void OnInitialize(StatelessServiceInitializiationParameters)`
- `Task OnOpenAsync(IStatelessServicePartition, CancellationToken)`
- `Task OnCloseAsync(CancellationToken)`
- `void OnAbort()`

## Pasos siguientes
Para obtener temas más avanzados relacionados con Service Fabric, consulte los siguientes artículos.

- [Configuración de los servicios fiables con estado](service-fabric-reliable-services-configuration.md)

- [Introducción al estado de Service Fabric](../service-fabric/service-fabric-health-introduction.md)

- [Uso de informes de estado del sistema para solucionar problemas](../service-fabric/service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

- [Información general de las restricciones de ubicación](../service-fabric/service-fabric-placement-constraint.md)

- [Proteger el tráfico de reproducción de servicios con estado en Azure Service Fabric](../service-fabric/service-fabric-replication-security.md)
 

<!---HONumber=August15_HO6-->