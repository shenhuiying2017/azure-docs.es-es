---
title: Modelo de hospedaje de Azure Service Fabric | Microsoft Docs
description: "Describe la relación entre las réplicas o las instancias de un servicio implementado de Service Fabric y el proceso de host de servicio."
services: service-fabric
documentationcenter: .net
author: harahma
manager: timlt
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/15/2017
ms.author: harahma
ms.openlocfilehash: ecc9038cf895ddaeb06dd0e4e9852d5ef4a4513a
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2017
---
# <a name="service-fabric-hosting-model"></a>Modelo de hospedaje de Service Fabric
En este artículo se ofrece información general sobre los modelos de hospedaje de aplicaciones que Service Fabric proporciona y, además, se describen las diferencias entre los modelos de **proceso compartido** y **proceso exclusivo**. Se describe el aspecto de una aplicación implementada en un nodo de Service Fabric y la relación entre las réplicas o instancias del servicio y el proceso de host de servicio.

Antes de continuar, asegúrese de que está familiarizado con el [Modelo de aplicación de Service Fabric][a1] y de que conoce los diferentes conceptos y la relación entre ellos. 

> [!NOTE]
> En este artículo, para simplificar, a menos que se mencione explícitamente:
>
> - Todos los usos de la palabra *réplica* hacen referencia a una réplica de un servicio con estado o a una instancia de un servicio sin estado.
> - *CodePackage* recibe un tratamiento equivalente al proceso de *ServiceHost* que registra una instancia de *ServiceType* y hospeda réplicas de servicios de dicha instancia de *ServiceType*.
>

Para entender el modelo de hospedaje, se va a analizar un ejemplo. Se supone que hay una instancia de *ApplicationType* llamada "MyAppType" que tiene un valor de *ServiceType* "MyServiceType" proporcionado por *ServicePackage* "MyServicePackage" que incluye una instancia de *CodePackage* "MyCodePackage" que registra *ServiceType* "MyServiceType" cuando se ejecuta.

Por ejemplo, hay un clúster de 3 nodos y se crea una *aplicación* **fabric:/App1** de tipo "MyAppType". Dentro de la *aplicación* **fabric:/App1**, se crea un servicio **fabric:/App1/ServiceA** de tipo "MyServiceType" que tiene 2 particiones (**P1** & **P2**) y 3 réplicas por cada partición. En el siguiente diagrama se muestra la vista de esta aplicación tal y como se termina implementando en un nodo.

<center>
![Vista del nodo de la aplicación implementada][node-view-one]
</center>

Service Fabric activó "MyServicePackage", que inició "MyCodePackage", donde se hospedan las réplicas de ambas particiones, es decir, **P1** & **P2**. Tenga en cuenta que todos los nodos del clúster tendrán la misma vista, ya que se ha elegido un número de réplicas por partición igual al número de nodos del clúster. Se va a crear otro servicio **fabric:/App1/ServiceB** en la aplicación **fabric:/App1** que tiene 1 partición (**P3**) y 3 réplicas por cada partición. En el diagrama siguiente se muestra la nueva vista del nodo:

<center>
![Vista del nodo de la aplicación implementada][node-view-two]
</center>

Como se puede observar, Service Fabric ha colocado la nueva réplica de la partición **P3** del servicio **fabric:/App1/ServiceB** en la activación existente de "MyServicePackage". Ahora se crea otra *aplicación* **fabric:/App2** de tipo "MyAppType" y, dentro de **fabric:/App2**, se crea el servicio **fabric:/App2/ServiceA**, que tiene 2 particiones (**P4** & **P5**) y 3 por cada partición. En el siguiente diagrama se muestra la nueva vista del nodo:

<center>
![Vista del nodo de la aplicación implementada][node-view-three]
</center>

En esta ocasión, Service Fabric ha activado una nueva copia de "MyServicePackage" que inicia una nueva copia de "MyCodePackage", y las réplicas de las dos particiones del servicio **fabric:/App2/ServiceA** (es decir, **P4** & **P5**) se colocan en esta nueva copia "MyCodePackage".

## <a name="shared-process-model"></a>Modelo de proceso compartido
Lo que se ha expuesto anteriormente se corresponde con el modelo de hospedaje predeterminado que ofrece Service Fabric, al que se hace referencia como modelo de **proceso compartido**. En este modelo, para una *aplicación* determinada, solo se activa una copia de un *ServicePackage* determinado en un *nodo* (que inicia todas las instancias de *CodePackages* contenidas en él), y todas las réplicas de todos los servicios de un tipo *ServiceType* concreto se colocan en el *CodePackage* que registra dicho tipo *ServiceType*. En otras palabras, todas las réplicas de todos los servicios en un nodo de un determinado *ServiceType* comparten el mismo proceso.

## <a name="exclusive-process-model"></a>Modelo de proceso exclusivo
El otro modelo de hospedaje proporcionado por Service Fabric es el modelo de **proceso exclusivo**. En este modelo, en un determinado *nodo*, para colocar cada réplica, Service Fabric activa una nueva copia del *ServicePackage* (que inicia todas las instancias de *CodePackages* que contiene), y la réplica se coloca en el *CodePackage* que ha registrado el tipo *ServiceType* del servicio al que la réplica pertenece. En otras palabras, cada réplica reside en su propio proceso dedicado. 

Este modelo se admite a partir de la versión 5.6 de Service Fabric. El modelo de **proceso exclusivo** se puede elegir en el momento en que se crea el servicio (con [PowerShell][p1], [REST][r1] o [FabricClient][c1]); para ello, hay que especificar **ServicePackageActivationMode** como "ExclusiveProcess".

```powershell
PS C:\>New-ServiceFabricService -ApplicationName "fabric:/App1" -ServiceName "fabric:/App1/ServiceA" -ServiceTypeName "MyServiceType" -Stateless -PartitionSchemeSingleton -InstanceCount -1 -ServicePackageActivationMode "ExclusiveProcess"
```

```csharp
var serviceDescription = new StatelessServiceDescription
{
    ApplicationName = new Uri("fabric:/App1"),
    ServiceName = new Uri("fabric:/App1/ServiceA"),
    ServiceTypeName = "MyServiceType",
    PartitionSchemeDescription = new SingletonPartitionSchemeDescription(),
    InstanceCount = -1,
    ServicePackageActivationMode = ServicePackageActivationMode.ExclusiveProcess
};

var fabricClient = new FabricClient(clusterEndpoints);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Si el manifiesto de aplicación contiene un servicio predeterminado, puede elegir el modelo de **proceso exclusivo** si especifica el atributo **ServicePackageActivationMode** como se muestra a continuación:

```xml
<DefaultServices>
  <Service Name="MyService" ServicePackageActivationMode="ExclusiveProcess">
    <StatelessService ServiceTypeName="MyServiceType" InstanceCount="1">
      <SingletonPartition/>
    </StatelessService>
  </Service>
</DefaultServices>
```
Continuando con el ejemplo anterior, se crea otro servicio **fabric:/App1/ServiceC** en la aplicación **fabric:/App1** que tiene 2 particiones (**P6** & **P7**) y 3 réplicas por partición con **ServicePackageActivationMode** establecido en "ExclusiveProcess". En el diagrama siguiente se muestra la nueva vista del nodo:

<center>
![Vista del nodo de la aplicación implementada][node-view-four]
</center>

Como se puede observar, Service Fabric ha activado dos copias de "MyServicePackage" (una para cada réplica de la partición **P6** & **P7**) y ha colocado cada réplica en su copia dedicada de *CodePackage*. Otra cuestión que hay que tener en cuenta es que, cuando se usa el modelo de **proceso exclusivo**, en una *aplicación* determinada, puede haber varias copias de un *ServicePackage* determinado activas en un *nodo*. En el ejemplo anterior, se observa que hay tres copias de "MyServicePackage" activas para **fabric:/App1**. Cada una de estas copias activas de "MyServicePackage" tiene un **ServicePackageActivationId** asociado que identifica dicha copia en la *aplicación* **fabric:/App1**.

Si solo se usa el modelo de **proceso compartido** para una *aplicación*, como **fabric:/App2** en el ejemplo anterior, solo hay una copia activa de *ServicePackage* en un *nodo*, y **ServicePackageActivationId** en esta activación de *ServicePackage* es una "cadena vacía".

> [!NOTE]
>- La correspondencia del modelo de hospedaje de **proceso compartido** para **ServicePackageActivationMode** es igual a **SharedProcess**. Se trata del modelo de hospedaje predeterminado, y no es necesario especificar **ServicePackageActivationMode** en el momento en que se crea el servicio.
>
>- La correspondencia del modelo de hospedaje de **proceso exclusivo** para **ServicePackageActivationMode** es igual a **ExclusiveProcess** y debe especificarse explícitamente en el momento en que se crea el servicio. 
>
>- El modelo de hospedaje de un servicio se puede saber consultando la [descripción del servicio][p2] y el valor de **ServicePackageActivationMode**.
>
>

## <a name="working-with-deployed-service-package"></a>Trabajo con el paquete de servicio implementado
A una copia activa de *ServicePackage* en un nodo se le denomina [paquete de servicio implementado][p3]. Como se ha mencionado anteriormente, cuando se usa el modelo de **proceso exclusivo** para crear servicios en una *aplicación* determinada, podrían existir varios paquetes de servicio implementados para el mismo *ServicePackage*. Para realizar operaciones específicas para el paquete de servicio implementado, como [informar del estadio de un paquete de servicio implementado][p4] o [reiniciar el paquete de código de un paquete de servicio implementado][p5], entre otras, es necesario proporcionar el valor de **ServicePackageActivationId** para identificar un paquete de servicio implementado específico.

 El **ServicePackageActivationId** de un paquete de servicio implementado puede obtenerse consultando la lista de [paquetes de servicio implementados][p3] de un nodo. Al consultar los [tipos de servicios implementados][p6], las [réplicas implementadas][p7] y los [paquetes de código implementados][p8] de un nodo, el resultado de consulta contiene el valor de **ServicePackageActivationId** del paquete de servicio implementado primario.

> [!NOTE]
>- En el modelo de hospedaje de **proceso compartido** de un *nodo* concreto para una *aplicación* determinada, solo se activa una copia de un *ServicePackage*. Aquí, **ServicePackageActivationId** es igual al valor *cadena vacía*, y no debe especificarse al realizar las operaciones relacionadas con el paquete de servicio implementado. 
>
> - En el modelo de hospedaje de **proceso exclusivo** de un *nodo* concreto para una *aplicación* determinada, pueden activarse una o varias copias de *ServicePackage*. Cada activación tiene un valor *no vacío* para **ServicePackageActivationId**, y debe especificarse al realizar operaciones relacionadas con el paquete de servicio implementado. 
>
> - Si **ServicePackageActivationId** se omite, su valor predeterminado es "cadena vacía". Si existe un paquete de servicio implementado activado en el modelo de **proceso compartido**, la operación se realizará en él; de lo contrario, se producirá un error en la operación.
>
> - No se recomienda consultar una vez ni almacenar **ServicePackageActivationId** en caché, ya que se genera de forma dinámica y puede variar por diversos motivos. Antes de realizar una operación que necesita **ServicePackageActivationId**, primero debe consultar la lista de [paquetes de servicio implementados][p3] de un nodo y luego usar el valor de **ServicePackageActivationId** de los resultados de la consulta para realizar la operación original.
>
>

## <a name="guest-executable-and-container-applications"></a>Aplicaciones de contenedor y de archivo ejecutable invitado
Service Fabric trata las aplicaciones de [archivo ejecutable invitado][a2] y de [contenedor][a3] como servicios con estado con almacenamiento; es decir, no hay runtime de Service Fabric en *ServiceHost* (un proceso o contenedor). Como se trata de servicios con almacenamiento, el número de réplicas por *ServiceHost* no es aplicable para estos servicios. La configuración más común utilizada con estos servicios es la partición única con [InstanceCount][c2] igual a -1 (es decir, la ejecución de una copia del código de servicio en cada nodo del clúster). 

El valor predeterminado de **ServicePackageActivationMode** para estos servicios es **SharedProcess**, en cuyo caso, Service Fabric solo activa una copia de *ServicePackage* en uno *nodo* para una *aplicación* determinada, lo que significa que solo se ejecutará en el *nodo* una copia del código de servicio. Si desea ejecutar varias copias del código de servicio en un *nodo* cuando se crean varios servicios (*Service1* a *ServiceN*) de *ServiceType* (según se especifica en *ServiceManifest*) o cuando el servicio tiene varias particiones, debe especificar **ServicePackageActivationMode** como **ExclusiveProcess** en el momento en que se crea el servicio.

## <a name="changing-hosting-model-of-an-existing-service"></a>Cambio del modelo de hospedaje de un servicio existente
Actualmente no se admite cambiar el modelo de hospedaje de un servicio existente de **proceso compartido** a **proceso exclusivo**, y viceversa, a través de un mecanismo de actualización (o, en el caso de la especificación de servicio predeterminada, en el manifiesto de aplicación). La compatibilidad con esta característica se incluirá en futuras versiones.

## <a name="choosing-between-shared-process-and-exclusive-process-model"></a>Elección del modelo de proceso compartido o exclusivo
Ambos modelos de hospedaje tienen sus ventajas e inconvenientes, por lo que el usuario debe evaluar cuál de ellos satisface mejor sus necesidades. El modelo de **proceso compartido** permite utilizar mejor los recursos del sistema operativo, porque se generan menos procesos, varias réplicas del mismo proceso pueden compartir puertos, etc. Sin embargo, si una de las réplicas produce un error donde debe interrumpir el host de servicio, afectará a las demás réplicas del mismo proceso.

 El modelo de **proceso exclusivo** ofrece un aislamiento mejorado de cada réplica en su propio proceso, por lo que la réplica que presenta un comportamiento incorrecto no afectará a las demás. Resulta útil para los casos en que el protocolo de comunicación no admite el uso compartido de puertos. Facilita la capacidad de aplicar la regulación de recursos a nivel de réplica. Por otra parte, el modelo de **proceso exclusivo** consumirá más recursos del sistema operativo, ya que genera un proceso para cada réplica del nodo.

## <a name="exclusive-process-model-and-application-model-considerations"></a>Consideraciones sobre el modelo de proceso exclusivo y el modelo de aplicación
El modelo recomendado para la aplicación de Service Fabric es mantener un *ServiceType* por *ServicePackage*, y este modelo es conveniente para la mayoría de las aplicaciones. 

Destinado a determinados casos de uso, Service Fabric también permite más de un *ServiceType* por *ServicePackage* (y un *CodePackage* puede registrar más de un  *ServiceType*). A continuación se presentan algunos escenarios en que esta configuración puede resultar útil:

- Desea optimizar el uso de recursos del sistema operativo generando menos procesos y con una mayor densidad de réplica por proceso.
- Las réplicas de diferentes instancias de ServiceTypes deben compartir algunos datos comunes, lo que genera un alto coste de inicialización o memoria.
- Dispone de una oferta de servicio gratuita y desea establecer un límite en la utilización de recursos mediante la colocación de todas las réplicas del servicio en el mismo proceso.

El modelo de hospedaje de **proceso exclusivo** no es coherente con el modelo de aplicación que tiene varias instancias de *ServiceTypes* por cada *ServicePackage*. Esto se debe a que la disponibilidad de varios *ServiceTypes* por *ServicePackage* está pensada para conseguir un mayor nivel de uso compartido de recursos entre réplicas y, además, ofrece mayor densidad de réplica por proceso. Se trata de un objetivo distinto al que se pretende conseguir con el modelo de **proceso exclusivo**.

Pensemos en un caso en hay varios *ServiceTypes* por *ServicePackage* con distintos *CodePackage* que registran cada *ServiceType*. Por ejemplo, existe un *ServicePackage* "MultiTypeServicePackge" que tiene dos *CodePackages*:

- "MyCodePackageA", que registra el *ServiceType* "MyServiceTypeA".
- "MyCodePackageB", que registra el *ServiceType* "MyServiceTypeB".

Ahora, por ejemplo, se crea una *aplicación* **fabric:/SpecialApp** y, dentro de **fabric:/SpecialApp**, se crean los dos servicios siguientes con el modelo de **proceso exclusivo**:

- El servicio **fabric:/SpecialApp/ServiceA** de tipo "MyServiceTypeA" con dos particiones (**P1** y **P2**) y 3 réplicas por partición.
- El servicio **fabric:/SpecialApp/ServiceB** de tipo "MyServiceTypeB" con dos particiones (**P3** y **P4**) y 3 réplicas por partición.

En un nodo determinado, ambos servicios tendrán dos réplicas. Como se ha usado el modelo de **proceso exclusivo** para crear los servicios, Service Fabric activará una copia nueva de "MyServicePackge" para cada réplica. Cada activación de "MultiTypeServicePackge" iniciará una copia de "MyCodePackageA" y "MyCodePackageB". No obstante, solo una de las instancias de "MyCodePackageA" o "MyCodePackageB" hospedará la réplica para la que se ha activado "MultiTypeServicePackge". En el siguiente diagrama se muestra la vista del nodo:

<center>
![Vista del nodo de la aplicación implementada][node-view-five]
</center>

 Como se puede observar, en la activación de "MultiTypeServicePackge" para la réplica de la partición **P1** del servicio **fabric:/SpecialApp/ServiceA**, "MyCodePackageA" hospeda la réplica y "MyCodePackageB" solo se está ejecutando. De forma similar, en la activación de "MultiTypeServicePackge" para la réplica de la partición **P3** del servicio **fabric:/SpecialApp/ServiceB**, "MyCodePackageB" hospeda la réplica y "MyCodePackageA" solo está en ejecución, y así sucesivamente. Por tanto, mientras mayor sea el número de *CodePackages* (con el registro de diferentes *ServiceTypes*) por *ServicePackage*, mayor será el uso de recursos redundantes. 
 
 Por otro lado, si se crean los servicios **fabric:/SpecialApp/ServiceA** y **fabric:/SpecialApp/ServiceB** con el modelo de **proceso compartido**, Service Fabric activará solo una copia de "MultiTypeServicePackge" para la *aplicación* **fabric:/SpecialApp** (como se ha visto anteriormente). "MyCodePackageA" hospedará todas las réplicas del servicio **fabric:/SpecialApp/ServiceA**, o de cualquier servicio de tipo "MyServiceTypeA", para ser más precisos, y "MyCodePackageB" hospedará todas las réplicas del servicio **fabric:/SpecialApp/ServiceB**, o de cualquier servicio de tipo "MyServiceTypeB", para ser más exactos. En el siguiente diagrama se muestra la vista del nodo con esta configuración: 

<center>
![Vista del nodo de la aplicación implementada][node-view-six]
</center>

Según el ejemplo anterior, es posible que se plantee que, si "MyCodePackageA" registra "MyServiceTypeA" y "MyServiceTypeB" y no hay ninguna instancia de "MyCodePackageB", entonces no se producirá ninguna ejecución redundante de *CodePackage*. Este planteamiento es correcto, pero, como se ha mencionado anteriormente, este modelo de aplicación no es compatible con el modelo de hospedaje de **proceso exclusivo**. Si el objetivo consiste en colocar cada réplica en su propio proceso dedicado, no es necesario registrar ambas instancias de *ServiceTypes* desde el mismo *CodePackage*; lo más conveniente es colocar cada *ServiceType* es su propio *ServicePacakge*.

## <a name="next-steps"></a>Pasos siguientes
[Empaquete una aplicación][a4] y prepárela para la implementación.

En [Implementación y eliminación de aplicaciones][a5] se describe cómo usar PowerShell para administrar instancias de las aplicaciones.

<!--Image references-->
[node-view-one]: ./media/service-fabric-hosting-model/node-view-one.png
[node-view-two]: ./media/service-fabric-hosting-model/node-view-two.png
[node-view-three]: ./media/service-fabric-hosting-model/node-view-three.png
[node-view-four]: ./media/service-fabric-hosting-model/node-view-four.png
[node-view-five]: ./media/service-fabric-hosting-model/node-view-five.png
[node-view-six]: ./media/service-fabric-hosting-model/node-view-six.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[a1]: service-fabric-application-model.md
[a2]: service-fabric-deploy-existing-app.md
[a3]: service-fabric-containers-overview.md
[a4]: service-fabric-package-apps.md
[a5]: service-fabric-deploy-remove-applications.md

[r1]: https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-createservice

[c1]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync
[c2]: https://docs.microsoft.com/dotnet/api/system.fabric.description.statelessservicedescription.instancecount

[p1]: https://docs.microsoft.com/powershell/servicefabric/vlatest/new-servicefabricservice
[p2]: https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricservicedescription
[p3]: https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricdeployedservicePackage
[p4]: https://docs.microsoft.com/powershell/servicefabric/vlatest/send-servicefabricdeployedservicepackagehealthreport
[p5]: https://docs.microsoft.com/powershell/servicefabric/vlatest/restart-servicefabricdeployedcodepackage
[p6]: https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricdeployedservicetype
[p7]: https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricdeployedreplica
[p8]: https://docs.microsoft.com/powershell/servicefabric/vlatest/get-servicefabricdeployedcodepackage
