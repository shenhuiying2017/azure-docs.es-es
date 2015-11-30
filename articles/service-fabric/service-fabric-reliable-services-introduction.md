<properties
   pageTitle="Información general sobre el modelo de programación de un servicio fiable de Service Fabric"
   description="Obtenga información sobre el modelo de programación de un servicio fiable de Service Fabric y comience a escribir sus propios servicios."
   services="Service-Fabric"
   documentationCenter=".net"
   authors="masnider"
   manager="timlt"
   editor="jessebenson; mani-ramaswamy"/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/17/2015"
   ms.author="masnider;jesseb"/>

# Información general sobre los servicios fiables
Service Fabric simplifica la escritura y la administración de servicios con y sin estado fiables. En este documento se hablará acerca de:

1. El modelo de programación de un servicio fiable para los servicios sin estado y con estado.
2. Las distintas opciones que debe elegir al escribir un servicio fiable.
3. Algunos de los diferentes escenarios y ejemplos de cuándo debe utilizar los servicios fiables y cómo se escriben.

Servicios fiables es uno de los modelos de programación disponibles en Service Fabric. Para obtener más información sobre el modelo de programación de actores fiables, consulte [la introducción](service-fabric-reliable-actors-introduction.md).

En Service Fabric, un servicio se compone de configuración, código de aplicación y opcionalmente de estado.

Service Fabric administra la duración de los servicios desde el aprovisionamiento hasta la implementación a través de la actualización y eliminación mediante la [administración de aplicaciones de Service Fabric](service-fabric-deploy-remove-applications.md).

## ¿Qué son los servicios fiables?
Servicios fiables le ofrece un modelo de programación de nivel superior, sencillo y potente para ayudarle a expresar lo que es importante para su aplicación. Con el modelo de programación de un servicio fiable obtendrá:

1. Para los servicios con estado, el modelo de programación de un servicio fiable permite almacenar de manera coherente y fiable el estado dentro de su servicio mediante las colecciones fiables: un conjunto sencillo de clases de colección de alta disponibilidad que le resultarán familiares a cualquiera que haya usado colecciones de C#. Tradicionalmente, los servicios necesitaban sistemas externos para efectuar la administración de estado fiable. Con las colecciones fiables, puede almacenar el estado junto a su proceso con la misma alta disponibilidad y fiabilidad que cabe esperar de los almacenes externos de alta disponibilidad y con las mejoras de latencia adicional al colocar juntos el proceso y el estado proporcionados.

2. Un modelo simple para ejecutar su propio código que se parece a modelos de programación a los que ya está acostumbrado: el código tiene un punto de entrada bien definido y un ciclo de vida de fácil administración.

3. Un modelo de comunicación acoplable: utilice el transporte que desee, como HTTP con [WebAPI](service-fabric-reliable-services-communication-webapi.md), WebSockets, protocolos TCP personalizados, etc. Servicios fiables proporciona excelentes opciones originales que puede usar o le permite proporcionar las suyas propias.

## ¿Qué hace que los servicios fiables resulten diferentes?
Los servicios fiables de Service Fabric son diferentes de los servicios que haya podido escribir previamente. Service Fabric proporciona confiabilidad, disponibilidad, coherencia y escalabilidad.

+ <u>Confiabilidad</u>: su servicio permanecerá activo incluso en entornos poco confiables en los que es posible que las máquinas fallen o encuentren problemas de red.

+ <u>Disponibilidad</u>: el servicio estará accesible y con capacidad de respuesta (esto no significa que no puede tener los servicios que no se pueden encontrar o a los que no se puede acceder desde fuera).

+ <u>Escalabilidad</u>: los servicios se desacoplan de hardware específico y pueden ampliarse o reducirse según sea necesario mediante la adición o eliminación de hardware o recursos virtuales. Los servicios se particionan fácilmente (especialmente en el caso con estado) para asegurarse de que las partes independientes del servicio pueden escalarse y responder a errores de forma independiente. Finalmente, Service Fabric fomenta que los servicios sean ligeros al permitir que se provisionen miles de servicios en un único proceso, en lugar de requerir o dedicar instancias completas de sistema operativo a una única instancia de una carga de trabajo determinada.

+ <u>Coherencia</u>: esto significa que toda la información almacenada en este servicio puede garantizarse de manera coherente (esto solo se aplica a los servicios con estado; a continuación se facilitará más información acerca de esto)

## Ciclo de vida del servicio
Si el servicio está con estado o sin estado, los servicios fiables proporcionan un ciclo de vida simple que permite conectar rápidamente el código y empezar a trabajar. Realmente solo existen uno o dos métodos que se deben implementar para poner su servicio en funcionamiento.

+ CreateServiceReplicaListeners/CreateServiceInstanceListeners: aquí es donde el servicio define la pila de comunicaciones que quiere usar. La pila de comunicación, como [API web](service-fabric-reliable-services-communication-webapi.md), es lo que define los extremos de escucha para el servicio (cómo lo alcanzarán los clientes), así como de qué manera dichos mensajes que aparecen acaban interactuando con el resto del código de servicio.

+ RunAsync: aquí es donde el servicio ejecuta su lógica empresarial. El token de cancelación que se proporciona es una señal de cuándo debe detenerse ese trabajo. Por ejemplo, si tiene un servicio que necesita extraer mensajes constantemente de una ReliableQueue y procesarlos, aquí sería donde se produciría ese trabajo.

Los eventos principales del ciclo de vida de un servicio fiable son los siguientes:

1. Se construye el objeto de servicio (lo que se deriva de StatelessService o StatefulService).

2. Se llama al método CreateServiceReplicaListeners/CreateServiceInstanceListeners, lo que permite al servicio devolver uno o varios agentes de escucha de comunicación que elija.
  + Tenga en cuenta que esto es opcional, aunque la mayoría de los servicios expondrán algún extremo directamente.

3. Una vez creados los agente de escucha de comunicación, se abre.
  + Los agentes de escucha de comunicación disponen de un método llamado OpenAsync(), al que se llama en este momento y que devuelve la dirección de escucha para el servicio. Si el servicio fiable utiliza uno de los ICommunicationListeners integrados, esto es controlado por usted.

4. Una vez que el agente de escucha de comunicación está abierto, se llama al método RunAsync() del servicio principal.
  + Tenga en cuenta que RunAsync es opcional. Si el servicio realiza todo su trabajo en respuesta a llamadas de usuario solamente, entonces no es necesario que implemente RunAsync().

Cuando se está cerrando el servicio (ya sea cuando se ha eliminado, actualizado o simplemente cuando se esté moviendo desde una ubicación determinada) el orden de llamada es el mismo. Primero se llama a CloseAsync() en los agentes de escucha de comunicación y, a continuación, se cancela el token de cancelación que se pasó a RunAsync().

## Servicios de ejemplo
Tras conocer este modelo de programación, echemos un vistazo rápido a dos servicios diferentes para ver cómo encajan estas piezas.

### Servicios fiables sin estado
Un servicio sin estado es aquel en el que literalmente no se mantiene ningún estado en el servicio, o en el que el estado que se encuentra presente es completamente desechable y no requiere sincronización, replicación, persistencia ni alta disponibilidad.

Por ejemplo, tomemos una calculadora que no tenga memoria y que recibe todos los términos y las operaciones que debe realizar a la vez.

En este caso, el RunAsync() del servicio puede estar vacío, ya que no hay ningún procesamiento de tareas en segundo plano que necesite hacer el servicio. Cuando se cree el servicio de calculadora, devolverá un ICommunicationListener (por ejemplo, [API web](service-fabric-reliable-services-communication-webapi.md)), lo que abre un punto de conexión de escucha en algún puerto. Este extremo de escucha se enlazará con los diferentes métodos (por ejemplo: "Add (n1, n2)") que definen la API pública de la calculadora.

Cuando se realiza una llamada desde un cliente, se invoca el método adecuado y el servicio de calculadora realiza las operaciones en los datos proporcionados y devuelve el resultado. No se almacena ningún estado.

No almacenar ningún estado interno simplifica esta Calculadora de ejemplo. Pero la mayoría de servicios no son realmente sin estado. En su lugar, externalizan su estado a algún otro almacén (por ejemplo, cualquier aplicación web que se basa en mantener el estado de la sesión en un almacén de respaldo o una caché que no es completamente sin estado).

Un ejemplo común de cómo se utilizan los servicios sin estado en Service Fabric es un extremo que expone la API pública para una aplicación web. A continuación, el servicio front-end se comunica con los servicios con estado para completar una solicitud de usuario. En este caso, las llamadas de los clientes se dirigen a un puerto conocido como 80 en el que el servicio sin estado está escuchando. Este servicio sin estado recibe la llamada y determina si la llamada procede de una entidad de confianza, así como a qué servicio va destinado. A continuación, el servicio sin estado reenvía la llamada a la partición correcta del servicio con estado y espera una respuesta. Cuando recibe una respuesta, responde al cliente original.

### Servicios fiables con estado
Un servicio con estado es el que debe tener alguna parte del estado coherente y presente para que el servicio funcione. Considere un servicio que calcule constantemente una media acumulada de algún valor basándose en las actualizaciones que recibe. Para ello, debe disponer del conjunto actual de solicitudes entrantes que necesite para el proceso, así como el promedio actual. Cualquier servicio que recupera, procesa y almacena la información en un almacén externo (por ejemplo, blobs o tablas o de Azure en la actualidad) con estado: simplemente mantiene su estado en el almacén de estado externo.

Actualmente la mayoría de servicios almacena su estado externamente debido a que el almacén externo es lo que proporciona fiabilidad, disponibilidad, escalabilidad y coherencia para ese estado. En Service Fabric, los servicios con estado no se requieren para almacenar su estado externamente debido a que Service Fabric se encarga de estos requisitos para el código de servicio y el estado del servicio.

Supongamos que queremos escribir un servicio que tomó solicitudes de una serie de conversiones que debían realizarse en una imagen y la imagen que necesitaba convertirse. Para este servicio devolvería un agente de escucha de comunicación (supongamos que WebAPI) que abre un puerto de comunicaciones y permite envíos a través de una API como `ConvertImage(Image i, IList<Conversion> conversions)`. En esta API el servicio podría tomar la información y almacenar la solicitud en un ReliableQueue y, a continuación, devolver algún token al cliente, por lo que podría realizar un seguimiento de la solicitud (dado que las solicitudes podrían tardar algún tiempo).

En este servicio RunAsync podría ser más complejo: el servicio tendría un bucle dentro de su RunAsync que extrae las solicitudes de la IReliableQueue, realiza las conversiones enumeradas y almacena los resultados en un IReliableDictionary para que cuando el cliente regrese puedan obtener sus imágenes convertidas. Para asegurarse de que incluso si algo falla la imagen no se pierda, este servicio fiable podría extraer de la cola, realizar las conversiones y almacenar el resultado en una transacción, de modo que el mensaje solo se quite de la cola y los resultados se almacenen en el diccionario de resultados cuando se completan las conversiones. Si algo falla en el medio (por ejemplo, el equipo en el que se está ejecutando esta instancia del código), la solicitud permanece en la cola a la espera de volver a ser procesado.

Una cosa a tener en cuenta acerca de este servicio es que parece un servicio .NET normal, la única diferencia es que las estructuras de datos que se van a usar (IReliableQueue y IReliableDictionary) son proporcionadas por Service Fabric de servicio y, por tanto, se vuelven altamente fiables, disponibles y coherentes.

## Cuándo utilizar las API de servicios fiables
Si cualquiera de las siguientes caracterizan sus necesidades de servicio de aplicación, deberá considerar las API de servicios fiables:

- Debe proporcionar el comportamiento de la aplicación en varias unidades de estado (por ejemplo, pedidos y artículos de líneas de pedido)

- El estado de la aplicación puede modelarse naturalmente como colas y diccionarios confiables

- El estado debe ser de alta disponibilidad con acceso de latencia baja

- La aplicación debe controlar la simultaneidad o la granularidad de las operaciones de transacción a través de una o más colecciones fiables

- Desea administrar las comunicaciones o controlar el esquema de partición del servicio

- El código necesita un entorno de tiempo de ejecución de subprocesamiento libre

- La aplicación necesita crear o destruir diccionarios fiables o colas en tiempo de ejecución de forma dinámica

- Necesita controlar mediante programación la copia de seguridad proporcionada de Service Fabric y restaurar las características del estado de su servicio*

- La aplicación necesita mantener el historial de cambios para sus unidades de estado *

- Desea desarrollar o consumir proveedores de terceros desarrollados de estado personalizado*

> [AZURE.NOTE]*Funciones disponibles en SDK general


## Pasos siguientes
+ [Inicio rápido de servicios fiables](service-fabric-reliable-services-quick-start.md)
+ [Consulte Uso avanzado de servicios fiables](service-fabric-reliable-services-advanced-usage.md)
+ [Lea el modelo de programación de actores fiables](service-fabric-reliable-actors-introduction.md)
 

<!---HONumber=Nov15_HO4-->