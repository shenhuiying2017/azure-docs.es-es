
<properties
   pageTitle="Patrón de composición de servicio con estado | Microsoft Azure"
   description="Patrón de diseño de Reliable Actors de Service Fabric que usa actores con estado para mantener el estado entre llamadas de servicio y para almacenar en la memoria caché los resultados de servicio anteriores."
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
   ms.date="08/05/2015"
   ms.author="vturecek"/>

# Patrón de diseño de Reliable Actors: composición de servicio con estado

Los desarrolladores se pasaron la última década y media en crear servicios sin estado de N capas en la empresa. Crearon servicios sobre bases de datos. Crearon servicios de orden superior sobre otros servicios. Y luego crearon motores de orquestación y software intermedio orientado a mensajes para coordinar esos servicios. A medida que evolucionaron las cargas de trabajo de los usuarios, para satisfacer demandas de mayor interactividad o escalado, la arquitectura orientada a servicios (SOA) sin estado empezó a mostrar sus puntos débiles.

## El método anterior: servicios de SOA

Aunque los servicios de SOA se escalaban horizontalmente sin problemas gracias a su naturaleza sin estado, creaban un cuello de botella en la capa de almacenamiento: simultaneidad y rendimiento. Por esto resultaba cada vez más caro acceder al almacenamiento. Como una práctica común, la mayoría de los desarrolladores introdujeron el almacenamiento en caché en su solución para reducir la demanda de almacenamiento. Sin embargo, esta solución tenía sus inconvenientes. Requería otra capa para administrar, acceso simultáneo a la memoria caché, limitaciones semánticas y cambios y coherencia. Como se describió [en el patrón de memoria caché inteligente](service-fabric-reliable-actors-pattern-smart-cache.md), el modelo de actor virtual ofrece una solución perfecta para estos problemas. Algunos desarrolladores intentan resolver los problemas de SOA mediante la replicación de la capa de almacenamiento. Este enfoque no se escala bien, aunque alcanza rápidamente los límites del Protocolo de alerta común.

El segundo problema ha evolucionado en torno a requisitos cambiantes. Los usuarios y las empresas exigen servicios interactivos que puedan responder a las solicitudes en milisegundos, en lugar de en segundos, como es la norma. Para satisfacer esta demanda, los desarrolladores empezaron a crear servicios de fachada sobre otros servicios. En algunos casos, se han incorporado decenas de servicios de fachada para crear servicios orientados al usuario. Pero la adición de varios servicios de bajada crea rápidamente problemas de latencia.

Los desarrolladores también han recurrido a memorias caché y a almacenes de objetos en memoria. En algunos casos, estos han utilizado diferentes implementaciones para satisfacer los requisitos de rendimiento. En este enfoque, los desarrolladores normalmente crean procesos de trabajo back-end para generar la caché periódicamente. Esto minimiza la población de caché costosa a petición. A continuación, deconstruyen sus cargas de trabajo para aislar las operaciones asincrónicas de las sincrónicas. Esto consigue más espacio para que las operaciones interactivas reaccionen a los cambios de estado, algo que es especialmente difícil en SOA.

A menudo también presentan más capas, como las colas y los trabajadores. Pueden agregar más complejidad a sus soluciones.

Básicamente, los desarrolladores han empezado a buscar soluciones para crear "servicios con estado" que colocan el "estado" junto al "comportamiento de servicio" para abordar las experiencias interactivas centradas en el usuario. Aquí es donde se introduce el modelo de Reliable Actors de Azure Service Fabric como una capa de composición de servicio, no como una sustitución de estos servicios.

En el diagrama siguiente se muestra este caso:

![Reliable Actors, composición de servicio y persistencia de estado][1]

## Implementación de soluciones mejoradas con actores

Para la composición de servicios, los actores pueden ser con o sin estado.

* Los actores sin estado se pueden usar como servidores proxy para los servicios subyacentes. Estos actores se pueden escalar dinámicamente en el clúster de Service Fabric y pueden almacenar en memoria caché cierta información relacionada con el servicio. Por ejemplo, su punto de conexión cuando se detecta.
* El actor con estado puede mantener el estado entre llamadas de servicio, así como almacenar en memoria caché los resultados de servicio anteriores. El estado puede ser persistente o temporal.

Este patrón es aplicable en muchos escenarios. En la mayoría de los casos, un actor necesita realizar una llamada externa para invocar una operación en un servicio concreto. Vamos a ilustrar esto con la utilización de un ejemplo de una aplicación moderna de comercio electrónico. Estas aplicaciones se basan en servicios que ofrecen diversas funcionalidades, como la administración de perfiles de usuario, recomendaciones, administración de la cesta de la compra, administración de la lista de deseos y compras.

La mayoría de los desarrolladores de comercio electrónico intentan adoptar un enfoque centrado en el usuario para su arquitectura, que sea similar al desarrollo de experiencias sociales. Esto es porque las experiencias de comercio electrónico también giran principalmente alrededor de los usuarios y los productos. Las soluciones de desarrolladores normalmente se consiguen con el envío de una fachada de servicios que, por motivos de rendimiento, probablemente son compatibles con una caché.

Compare esto con un enfoque basado en actores. Un actor de usuario puede representar el comportamiento del usuario (como explorar el catálogo, indicar preferencias por un producto, agregar un artículo a la cesta de la compra o recomendar un producto a un amigo). Pero también puede representar el estado compuesto del usuario, como el perfil del usuario, los artículos de la cesta de la compra, los artículos recomendados por amigos, el historial de compra del usuario y la geolocalización actual del usuario.

## Rellenar el estado mediante actores con estado

En primer lugar, echemos un vistazo a un ejemplo donde un actor de usuario necesita rellenar su estado a partir de varios servicios. No vamos a ofrecer un ejemplo de código para este, porque todo lo que se ha indicado [para el modelo de memoria caché inteligente](service-fabric-reliable-actors-pattern-smart-cache.md) también es aplicable aquí. Un actor de usuario se puede activar en el inicio de sesión y se rellena con suficientes datos de servicios back-end. El estado completo y parcial también se puede rellenar previamente a petición, en un temporizador o con ambos métodos, y esto se puede almacenar en caché en el actor. En este ejemplo, los servicios **Perfil** y **Lista de deseos** se ilustran a continuación:

![Servicios de perfil y lista de deseos][2]

Los desarrolladores pueden rellenar previamente el estado de los usuarios frecuentes para que esté listo cuando inician sesión. Los desarrolladores también pueden rellenar el estado en el momento del inicio de sesión para los usuarios que visitan el servicio cada mes. También puede ver estos patrones en la sección de la memoria caché inteligente.

Cuando el usuario 23 (como se muestra en la imagen anterior) inicia sesión, el actor de usuario (23) se activa, en caso de que aún no se haya activado. El actor de usuario luego captura la información de perfil de usuario pertinente y la lista de deseos de servicios back-end. El actor de usuario probablemente también almacenará en memoria caché la información de las llamadas posteriores. Si, por ejemplo, se necesita agregar un artículo a la lista de deseos, esto se puede conseguir con la escritura a través o por detrás, como ya se ha indicado.

Vamos a analizar un ejemplo en el que un usuario hace clic en el botón **Me gusta** para indicar que le gusta un producto. Esta acción puede requerir varias invocaciones de muchos servicios. Estas acciones pueden incluir enviar un "Me gusta" al servicio del catálogo, desencadenar el siguiente conjunto de recomendaciones y publicar una actualización en una red social.

Esto se ilustra a continuación:

![Servicios para indicar que un producto gusta, lista de deseos, perfil y catálogo][3]

## Uso de actores en composiciones y comunicaciones asincrónicas
El modelo de programación de Reliable Actors de Service Fabric destaca cuando un desarrollador desea componer operaciones del estilo solicitud/respuesta junto con operaciones asincrónicas. Por ejemplo, aunque cuando un usuario indica que le gusta un producto, este último pasa inmediatamente a la lista de deseos, su publicación en las redes sociales y el desencadenamiento del siguiente conjunto de operaciones de recomendaciones pueden realizarse de manera asincrónica con la utilización de búferes y temporizadores.

Otra ventaja clave que ofrece utilizar un actor de usuario para los servicios es que el actor ofrece una ubicación natural para el estado almacenado en memoria caché. Lo más importante, el actor también responde a los cambios de su estado de forma asincrónica. Se trata de un escenario especialmente complicado con servicios sin estado. Por ejemplo, un usuario puede realizar una serie de acciones como parte de un “viaje de usuario”, y estos eventos pueden capturarse en tiempo real en el actor. Luego se puede ensamblar una secuencia que se puede consultar en el momento del evento o de manera asincrónica en un temporizador para cambiar el comportamiento del actor.

En ese momento, los puristas de SOA sin duda se habrán dado cuenta de que no son servicios en el sentido de actores como puntos de conexión expuestos en un protocolo independiente del lenguaje. El modelo de Reliable Actors de Service Fabric no es un componente de interoperabilidad ni una plataforma para la interoperación del servicio. Sin embargo, no hay nada que impida pensar en términos de la granularidad de los servicios de tipo SOA cuando modelan los actores o en la separación de los aspectos de la misma manera. Estos servicios se conocen como microservicios. Tampoco hay nada que impida que los desarrolladores coloquen un punto de conexión REST o SOAP como una capa de interoperación delante de Reliable Actors de Service Fabric.

Además, la composición del servicio con estado también se aplica a los flujos de trabajo y no solo a los escenarios transaccionales, como el comercio electrónico. Service Fabric está diseñado como un motor de flujo de trabajo u orquestación. Se puede usar para modelar flujos de trabajo que conllevan interacciones de servicio y mantienen el estado de estas interacciones.

Puede observar los inconvenientes del "servicio sin estado" en la creación de servicios escalables para ofrecer experiencias dinámicas. Al combinar el estado y el comportamiento, el modelo de programación de Reliable Actors de Service Fabric ayuda a los desarrolladores a crear experiencias escalables e interactivas sobre sus inversiones existentes.


## Pasos siguientes

[Patrón: memoria caché inteligente](service-fabric-reliable-actors-pattern-smart-cache.md)

[Patrón: gráficos y redes distribuidos](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)

[Patrón: gobernanza de recursos](service-fabric-reliable-actors-pattern-resource-governance.md)

[Patrón: Internet de las cosas](service-fabric-reliable-actors-pattern-internet-of-things.md)

[Patrón: procesamiento distribuido](service-fabric-reliable-actors-pattern-distributed-computation.md)

[Algunos antipatrones](service-fabric-reliable-actors-anti-patterns.md)

[Introducción a Actores confiables de Service Fabric.](service-fabric-reliable-actors-introduction.md)


<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-stateful-service-composition/stateful-service-composition-1.png
[2]: ./media/service-fabric-reliable-actors-pattern-stateful-service-composition/stateful-service-composition-2.png
[3]: ./media/service-fabric-reliable-actors-pattern-stateful-service-composition/stateful-service-composition-3.png

<!---HONumber=AcomDC_0121_2016-->