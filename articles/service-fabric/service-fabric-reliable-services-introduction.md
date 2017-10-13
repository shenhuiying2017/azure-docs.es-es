---
title: "Información general sobre el modelo de programación de Service Fabric Reliable Services | Microsoft Docs"
description: "Obtenga información sobre el modelo de programación de un servicio fiable de Service Fabric y comience a escribir sus propios servicios."
services: Service-Fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: vturecek; mani-ramaswamy
ms.assetid: 0c88a533-73f8-4ae1-a939-67d17456ac06
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: masnider;
ms.openlocfilehash: 601b1c7713c9785d949c1c72000ec7f3f63dd682
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="reliable-services-overview"></a>Información general sobre Reliable Services
Azure Service Fabric simplifica la escritura y administración de los servicios de Reliable Services con y sin estado. En este tema se explica:

* El modelo de programación de Reliable Services para los servicios sin estado y con estado.
* Las opciones que debe elegir al escribir un servicio de Reliable Services.
* Algunos escenarios y ejemplos de cuándo utilizar Reliable Services y cómo se escriben.

Servicios fiables es uno de los modelos de programación disponibles en Service Fabric. El otro es el modelo de programación de Reliable Actor, que proporciona un modelo de programación de Virtual Actor sobre el modelo de Reliable Services. Para más información sobre el modelo de programación de Reliable Actors, consulte [Introducción a Reliable Actors de Service Fabric](service-fabric-reliable-actors-introduction.md).

Service Fabric administra la duración de los servicios, desde el aprovisionamiento hasta la implementación, mediante actualizaciones y eliminaciones, a través de la [administración de aplicaciones de Service Fabric](service-fabric-deploy-remove-applications.md).

## <a name="what-are-reliable-services"></a>¿Qué son los servicios de Reliable Services?
Los servicios de confianza le ofrecen un modelo de programación de nivel superior, sencillo y eficaz para ayudarle a expresar lo que es importante para su aplicación. Con el modelo de programación de Reliable Services, obtiene lo siguiente:

* Acceso al resto de las API de programación de Service Fabric. A diferencia de los servicios de Service Fabric modelados como [archivos ejecutables invitados](service-fabric-deploy-existing-app.md), Reliable Services puede usar directamente el resto de las API de Service Fabric. Esto permite a los servicios:
  * consultar al sistema,
  * informar del mantenimiento de las entidades del clúster,
  * recibir notificaciones sobre los cambios de configuración y de código,
  * buscar otros servicios y comunicarse con ellos,
  * (opcionalmente) usar [Reliable Collections](service-fabric-reliable-services-reliable-collections.md) y
  * ofrecerles acceso a muchas otras funciones, todas ellas desde un modelo de programación de primer nivel en varios lenguajes de programación.
* Un modelo sencillo para ejecutar su propio código parecido a los modelos de programación que está acostumbrado a utilizar. El código tiene un punto de entrada bien definido y un ciclo de vida de fácil administración.
* Un modelo de comunicación acoplable. Use el transporte de su elección, por ejemplo, HTTP con [API web](service-fabric-reliable-services-communication-webapi.md), WebSockets, protocolos TCP personalizados o cualquier otro. Reliable Services proporciona excelentes opciones ya integradas, o puede proporcionar las suyas propias.
* En los servicios con estado, el modelo de programación de Reliable Services permite almacenar de forma coherente y confiable su estado justo dentro de su servicio mediante [Reliable Collections](service-fabric-reliable-services-reliable-collections.md). Reliable Collections es un conjunto sencillo de clases de colecciones de alta disponibilidad y confiabilidad que le resultará familiar a cualquiera que haya usado colecciones de C#. Tradicionalmente, los servicios necesitaban sistemas externos para efectuar la administración de estados de confianza. Con Reliable Collections, puede almacenar el estado junto a su proceso con la misma alta disponibilidad y confiabilidad que cabe esperar de los almacenes externos de alta disponibilidad. Este modelo también mejora la latencia porque está ubicando conjuntamente los procesos y el estado que necesita para funcionar.

Vea este vídeo de Microsoft Virtual Academy para obtener información general de Reliable Services: <center>
<a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=HhD9566yC_4106218965">
<img src="./media/service-fabric-reliable-services-introduction/ReliableServicesVid.png" WIDTH="360" HEIGHT="244" />
</a>
</center>

## <a name="what-makes-reliable-services-different"></a>¿Qué hace que Reliable Services sea diferente?
Los servicios fiables de Service Fabric son diferentes de los servicios que haya podido escribir previamente. Service Fabric proporciona confiabilidad, disponibilidad, coherencia y escalabilidad.

* **Confiabilidad**: su servicio sigue activo incluso en entornos poco confiables en los que sus equipos no funcionan correctamente o sufren problemas de red, o en casos en los que los propios servicios encuentran errores y se bloquean o dejan de responder. Para los servicios con estado, su estado se conserva incluso aunque se produzcan problemas de red u otros errores.
* **Disponibilidad**: su servicio es accesible y responde. Service Fabric mantiene el número de copias en ejecución deseado.
* **Escalabilidad**: los servicios se desacoplan de hardware específico y pueden ampliarse o reducirse según sea necesario mediante la adición o eliminación de hardware u otros recursos. Los servicios se particionan fácilmente (especialmente en el caso con estado) para asegurarse de que el servicio puede escalarse y administrar errores parciales. Se pueden crear y eliminar servicios dinámicamente mediante código, lo cual permite rotar más instancias según sea necesario, por ejemplo, en respuesta a solicitudes de clientes. Por último, Service Fabric favorece que los servicios sean ligeros. Service Fabric permite que se aprovisionen miles de servicios en un único proceso, en lugar de requerir o dedicar instancias o procesos completos de sistema operativo a una única instancia de un servicio.
* **Coherencia**: se garantiza la coherencia de toda la información almacenada en este servicio. Esto se cumple incluso con varias colecciones de confianza dentro de un servicio. Pueden realizarse cambios en varias colecciones dentro de un servicio de un modo transaccional pormenorizado.

## <a name="service-lifecycle"></a>Ciclo de vida del servicio
Tanto si el servicio está con estado como sin estado, Reliable Services proporciona un ciclo de vida simple que permite conectar rápidamente el código y empezar a trabajar.  Solo hay uno o dos métodos que se deben implementar para poner su servicio en funcionamiento.

* **CreateServiceReplicaListeners/CreateServiceInstanceListeners**: este método es en el que el servicio define las pilas de comunicación que quiere usar. La pila de comunicación, como [API web](service-fabric-reliable-services-communication-webapi.md), es lo que define los puntos de conexión de escucha o del servicio (cómo se comunicarán los clientes con el servicio). También define cómo los mensajes que aparecen interactúan con el resto del código de servicio.
* **RunAsync**: este método es en el que su servicio ejecuta su lógica de negocios, y donde debería iniciar las tareas en segundo plano que se deben ejecutar durante la vigencia del servicio. El token de cancelación que se proporciona es una señal de cuándo debe detenerse ese trabajo. Por ejemplo, si el servicio necesita extraer mensajes de una cola de Reliable y procesarlos, aquí es donde se produce ese trabajo.

Si este es su primer contacto con Reliable Services, siga leyendo. Si busca un tutorial detallado del ciclo de vida de Reliable Services, puede consultar [este artículo](service-fabric-reliable-services-lifecycle.md).

## <a name="example-services"></a>Servicios de ejemplo
Tras conocer este modelo de programación, echemos un vistazo rápido a dos servicios diferentes para ver cómo encajan estas piezas.

### <a name="stateless-reliable-services"></a>Servicios fiables sin estado
Un servicio sin estado es aquel en el que no se mantiene ningún estado en el servicio entre llamadas. Cualquier estado que esté presente es completamente descartable y no requiere sincronización, replicación, persistencia o alta disponibilidad.

Por ejemplo, tomemos una calculadora que no tiene memoria y que recibe todos los términos y las operaciones que debe realizar a la vez.

En este caso, el `RunAsync()` (C#) o `runAsync()` (Java) del servicio puede estar vacío, ya que no hay ningún procesamiento de tareas en segundo plano que el servicio necesite realizar. Cuando se crea el servicio de calculadora, devuelve un elemento `ICommunicationListener` (C#) o `CommunicationListener` (Java) (por ejemplo, [API web](service-fabric-reliable-services-communication-webapi.md)), que abre un punto de conexión de escucha en algún puerto. Este punto de conexión de escucha se enlaza con los diferentes métodos de cálculo (por ejemplo: "Add(n1, n2)") que definen la API pública de la calculadora.

Cuando se realiza una llamada desde un cliente, se invoca el método adecuado y el servicio de calculadora realiza las operaciones en los datos proporcionados y devuelve el resultado. No almacena ningún estado.

El hecho de no almacenar ningún estado interno hace que esta calculadora de ejemplo sea sencilla. Pero la mayoría de los servicios no son realmente sin estado. En su lugar, externalizan su estado a algún otro almacén. (Por ejemplo, cualquier aplicación web que se base en mantener el estado de sesión en un almacén de respaldo o una memoria caché no es sin estado).

Un ejemplo común de cómo se utilizan los servicios sin estado en Service Fabric es un extremo que expone la API pública para una aplicación web. El servicio front-end se comunica entonces con los servicios con estado para completar una solicitud de usuario. En este caso, las llamadas de los clientes se dirigen a un puerto conocido, como el 80, en el que se escucha el servicio sin estado. Este servicio sin estado recibe la llamada y determina si esta procede de una entidad de confianza, así como a qué servicio va destinada.  Luego, el servicio sin estado reenvía la llamada a la partición correcta del servicio con estado y espera una respuesta. Cuando el servicio sin estado recibe una respuesta, responde al cliente original. Hay un ejemplo de un servicio como este en nuestras muestras de [C#](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started) / [Java](https://github.com/Azure-Samples/service-fabric-java-getting-started/tree/master/Actors/VisualObjectActor/VisualObjectWebService). Esto es solo un ejemplo de este patrón en las muestras; hay otros en otras muestras también.

### <a name="stateful-reliable-services"></a>Servicios fiables con estado
Un servicio con estado es el que debe tener alguna parte del estado coherente y presente para que el servicio funcione. Considere un servicio que calcula constantemente una media acumulada de algún valor basándose en las actualizaciones que recibe. Para ello, debe disponer del conjunto actual de solicitudes entrantes que necesite procesar y del promedio actual. Cualquier servicio que recupera, procesa y almacena la información en un almacén externo (por ejemplo, en la actualidad un almacén de tablas o blobs de Azure) es con estado. Simplemente mantiene su estado en el almacén de estado externo.

Actualmente la mayoría de servicios almacena su estado externamente debido a que el almacén externo es lo que proporciona confiabilidad, disponibilidad, escalabilidad y coherencia para ese estado. En Service Fabric, los servicios no necesitan almacenar su estado externamente. Service Fabric se ocupa de estos requisitos tanto para el código de servicio como para el estado de servicio.

> [!NOTE]
> Linux todavía no es compatible con Reliable Services con estado (para C# o Java).
>

Supongamos que queremos escribir un servicio que procesa imágenes. Para ello, el servicio toma una imagen y la serie de conversiones que se han de realizar en dicha imagen. Luego devuelve un agente de escucha de comunicación (por ejemplo, una WebAPI) que expone una API como `ConvertImage(Image i, IList<Conversion> conversions)`. Cuando recibe una solicitud, el servicio la almacena en una `IReliableQueue` y devuelve algún identificador al cliente para que pueda realizar un seguimiento de la solicitud.

En este servicio, `RunAsync()` podría ser más complejo. El servicio tiene un bucle dentro de su elemento `RunAsync()` que extrae las solicitudes de `IReliableQueue` y realiza las conversiones solicitadas. Los resultados se almacenan en una instancia de `IReliableDictionary`, por lo que, cuando el cliente vuelve, puede recibir sus imágenes convertidas. Para asegurarse de que, incluso si algo no va bien, la imagen no se pierda, este servicio de Reliable Services podría extraerla de la cola, realizar las conversiones y almacenar el resultado en una única transacción. En este caso, el mensaje se quita de la cola y los resultados se almacenan en el diccionario de resultados solo cuando se completan las conversiones. El servicio también podría extraer la imagen de la cola y almacenarla inmediatamente en un almacén remoto. Esto reduce la cantidad de estado que el servicio tiene que administrar, pero aumenta la complejidad, ya que el servicio tiene que mantener los metadatos necesarios para administrar el almacenamiento remoto. Con cualquiera de estos dos métodos, si se produce un error en algún punto intermedio, la solicitud permanece en la cola esperando a ser procesada.

Algo que debe tenerse en cuenta con respecto a este servicio es que parece un servicio .NET normal. La única diferencia es que las estructuras de datos que se van a utilizar (`IReliableQueue` y `IReliableDictionary`) son proporcionadas por Service Fabric, tienen una gran disponibilidad y son altamente confiables y coherentes.

## <a name="when-to-use-reliable-services-apis"></a>Cuándo utilizar las API de Reliable Services
Considere el uso de API de Reliable Services si sus necesidades de servicios de aplicaciones tienen estas características:

* Desea que el código de su servicio (y opcionalmente el estado) tenga una gran disponibilidad y sea altamente confiable.
* Necesita garantías transaccionales en varias unidades de estado (por ejemplo, pedidos y artículos de línea de estos).
* El estado de la aplicación puede modelarse naturalmente como Reliable Dictionaries y Queues.
* El código de las aplicaciones o el estado deben ser de alta disponibilidad con una latencia baja de lecturas y escrituras.
* La aplicación debe controlar la simultaneidad o la granularidad de las operaciones de transacción a través de una o más instancias de Reliable Collections.
* Desea administrar las comunicaciones o controlar el esquema de partición del servicio.
* El código necesita un entorno de tiempo de ejecución de subproceso libre.
* La aplicación debe crear o destruir instancias de Reliable Dictionaries, Queues o servicios completos en tiempo de ejecución de forma dinámica.
* Debe controlar mediante programación la copia de seguridad proporcionada por Service Fabric y restaurar las características del estado de su servicio.
* La aplicación necesita mantener el historial de cambios para sus unidades de estado.
* Desea desarrollar o consumir proveedores de estado personalizados desarrollados por terceros.

## <a name="next-steps"></a>Pasos siguientes
* [Introducción a Reliable Services de Service Fabric de Microsoft Azure](service-fabric-reliable-services-quick-start.md)
* [Uso avanzado de Reliable Services](service-fabric-reliable-services-advanced-usage.md)
* [El modelo de programación de Reliable Actors](service-fabric-reliable-actors-introduction.md)
