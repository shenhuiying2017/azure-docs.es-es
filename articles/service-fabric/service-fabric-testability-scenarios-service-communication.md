<properties 
   pageTitle="Escenarios de Testability de Service Fabric: comunicación del servicio"
	description="La comunicación entre servicios es un punto crítico de integración de una aplicación de Service Fabric. En este artículo se describen las consideraciones de diseño y las técnicas de prueba."
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
	ms.date="08/25/2015"
	ms.author="vturecek"/>

# Escenarios de Testability de Service Fabric: comunicación del servicio

Los microservicios y los estilos de arquitectura orientados a servicios emergen naturalmente en Service Fabric. En estos tipos de arquitecturas distribuidas, las aplicaciones de microservicio divididas en componentes suelen constar de varios servicios que necesitan comunicarse entre sí. Incluso en el caso más simple, por lo general dispone al menos de un servicio web sin estado y de un servicio de almacenamiento de datos con estado que necesitan comunicarse.

La comunicación entre servicios es un punto crítico de integración de una aplicación, ya que cada servicio expone una API remota a otros servicios. El trabajo con un conjunto de límites de API que implican E/S generalmente requiere tomar algunas precauciones y realizar una buena cantidad de pruebas y validación.

Existen numerosas consideraciones que deben tenerse en cuenta cuando estos límites de servicio se conectan entre sí en un sistema distribuido:

 - **Protocolo de transporte**. ¿Se utilizará HTTP para una mayor interoperabilidad, o bien un protocolo binario personalizado para el máximo rendimiento?
 - **Control de errores**. ¿Cómo se controlan los errores transitorios y permanentes, y qué ocurre cuando un servicio se mueve a un nodo diferente?
 - **Tiempos de espera y latencia**. En las aplicaciones de varios niveles, ¿cómo controla cada capa de servicio la latencia a través de la pila hasta el usuario?

Tanto si utiliza uno de los componentes de comunicación de servicio integrados que aporta Service Fabric como si crea uno propio, la prueba de la interacción entre los servicios es una parte fundamental del proceso que garantiza la resistencia de la aplicación.

## ¿Dónde está mi servicio?

Las instancias de servicio pueden desplazarse con el tiempo, especialmente cuando se configuran con métricas de carga para el equilibrio óptimo personalizado de los recursos. Las actualizaciones, las conmutaciones por error, el escalado horizontal y otras situaciones diversas que se producen durante el ciclo de vida de un sistema distribuido son los elementos son los que provocan que Service Fabric mueva las instancias de servicio para maximizar la disponibilidad.

Dado que los servicios se desplazan en el clúster, hay dos escenarios que los clientes y otros servicios deben estar preparados para controlar al comunicarse con un servicio:

 + La instancia de servicio o la réplica de partición se han movido desde la última comunicación con ellas. Esto forma parte normal del ciclo de vida de un servicio y se espera que suceda mientras exista la aplicación.
 + La instancia de servicio o la réplica de partición están en un proceso de movimiento. Aunque la conmutación por error de un servicio de un nodo a otro se realiza rápidamente en Service Fabric, puede haber un retraso en la disponibilidad si el componente de comunicación del servicio tarda en iniciarse.

El control correcto de estas situaciones es importante para lograr que un sistema funcione sin problemas. Para ello, debemos tener en cuenta algunas consideraciones:

+ Todos los servicios con los que se puede conectar tienen una *dirección* que escucha (HTTP, WebSockets, etc.). Cuando se mueve una instancia de servicio o una partición, su extremo de dirección cambia (se ha movido a otro nodo con una dirección IP diferente). Si utiliza los componentes de comunicación integrados, ellos se ocuparán de volver a resolver las direcciones del servicio. 
+ Puede haber un aumento temporal de latencia del servicio mientras la instancia de servicio inicia de nuevo su agente de escucha, en función de la rapidez con la que el servicio lo abre después del movimiento.
+ Las conexiones existentes deben cerrarse y volverse a abrir de nuevo cuando el servicio se abre en un nuevo nodo. Un cierre o un reinicio estables del nodo permiten que se produzca un cierre estable de las conexiones existentes.

### Pruébelo: mover instancias de servicio

Mediante las herramientas de Testability de Service Fabric, podemos crear un escenario de prueba para probar estas situaciones de maneras diferentes.

1. Mover la réplica principal de un servicio con estado.
 
    La réplica principal de una partición de servicio con estado se pueden mover por diversos motivos. Utilice la siguiente línea para establecer como objetivo la réplica principal de una partición específica y ver cómo los servicios reaccionan al movimiento de manera controlada.

    ```powershell

    PS > Move-ServiceFabricPrimaryReplica -PartitionId 6faa4ffa-521a-44e9-8351-dfca0f7e0466 -ServiceName fabric:/MyApplication/MyService

    ```

2. Detener un nodo.

    Cuando se detiene un nodo, Service Fabric mueve todas las instancias de servicio o las particiones incluidas en el nodo a uno de los otros nodos disponibles del clúster. Utilice la siguiente línea para probar un situación en la que se pierde un nodo del clúster y se deben mover todas las instancias de servicio y las réplicas de dicho nodo.

    Los nodos se pueden detener mediante el cmdlet de PowerShell Stop-ServiceFabricNode:

    ```powershell

    PS > Restart-ServiceFabricNode -NodeName Node.1

    ```

    
    


### Disponibilidad del servicio

Service Fabric es una plataforma diseñada para ofrecer una alta disponibilidad de los servicios. Sin embargo, los problemas de infraestructura subyacentes pueden provocar que no exista disponibilidad en casos extremos. Por lo tanto, es importante también probar este escenario.

Los servicios con estado utilizan un sistema basado en quórum para la replicación de estado, lo que permite ofrecer una alta disponibilidad. Esto significa que debe haber un quórum de réplicas disponible para realizar operaciones de escritura. En casos excepcionales, como en un error generalizado de hardware, puede que no esté disponible el quórum de réplicas. En este caso, no podrá realizar operaciones de escritura, pero sí podrá realizar operaciones de lectura.

### Pruébelo: falta de disponibilidad de la operación de escritura

Las herramientas de Testability de Service Fabric permiten insertar un error que provoca la pérdida de quórum para probar este tipo de escenario. Aunque es poco habitual, es importante que los clientes y los servicios que dependen de un servicio con estado estén preparados para controlar situaciones en las que no pueden realizar solicitudes de escritura al servicio con estado. Al mismo tiempo, también es importante que el propio servicio con estado sea consciente de esta posibilidad y pueda comunicarla correctamente a los autores de llamadas.

La pérdida de quórum se puede inducir mediante el cmdlet de PowerShell Invoke-ServiceFabricPartitionQuorumLoss:

```powershell

PS > Invoke-ServiceFabricPartitionQuorumLoss -ServiceName fabric:/Myapplication/MyService -QuorumLossMode PartialQuorumLoss -QuorumLossDurationInSeconds 20

```

En este ejemplo, se establece `QuorumLossMode` en `PartialQuorumLoss` para indicar que deseamos provocar la pérdida de quórum sin desconectar todas las réplicas, de modo que las operaciones de lectura sigan siendo posibles. Para probar un escenario en el que no esté disponible una partición completa, puede establecer este modificador en `FullQuorumLoss`.

## Pasos siguientes

[Más información sobre las acciones de Testability](service-fabric-testability-actions.md)

[Más información sobre los escenarios de Testability](service-fabric-testability-scenarios.md)

<!---HONumber=August15_HO9-->