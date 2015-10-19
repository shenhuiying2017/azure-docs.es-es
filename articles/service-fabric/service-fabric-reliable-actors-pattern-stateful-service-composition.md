
<properties
   pageTitle="Patrón de diseño de composición de servicio con estado de Actores confiables"
   description="Patrón de diseño de Actores confiables de Service Fabric que usa actores con estado para mantener el estado entre llamadas de servicio, así como almacenar en la memoria caché los resultados de servicio anteriores. El estado puede ser persistente o temporal."
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

# Patrón de diseño de Actores confiables: composición de servicio con estado
Los desarrolladores se pasaron la última década y media en crear servicios sin estado de N capas en la empresa. Crearon servicios sobre bases de datos, servicios de orden superior sobre otros servicios y motores de orquestación y software intermedio orientado a mensajes para coordinar esos servicios. A medida que evolucionaron las cargas de trabajo de los usuarios, ya fuera solicitando más interactividad o escalado, la arquitectura orientada a servicios sin estado empezó a mostrar sus puntos débiles.

## El método antiguo: servicios de SOA
Aunque los servicios de SOA se escalaban horizontalmente sin problemas gracias a su naturaleza sin estado, creaban un cuello de botella en la capa de almacenamiento: simultaneidad y rendimiento. El acceso al almacenamiento se hizo cada vez más costoso. Como una práctica común, la mayoría de desarrolladores introdujo el almacenamiento en memoria caché a su solución para reducir la demanda de almacenamiento, pero esa solución también tenía inconvenientes: otra capa que administrar, acceso simultáneo a la memoria caché, limitaciones y cambios semánticos y, finalmente, la coherencia. Como se describió anteriormente en el patrón de memoria caché inteligente, el modelo de actor virtual ofrece una solución perfecta para esto.

Algunos desarrolladores intentaron resolver el problema mediante la replicación de su capa de almacenamiento. Sin embargo, este enfoque no escalaba bien y se topaba rápidamente con límites de CAP. El segundo problema ha evolucionado en torno a los requerimientos cambiantes; tanto los usuarios finales como las empresas necesitan servicios interactivos: respuestas a las solicitudes en milisegundos, en lugar de segundos, como norma. Para responder, los desarrolladores comenzaron a crear servicios de fachada encima de otros servicios, en algunos casos decenas de servicios para crear servicios centrados en el usuario. Sin embargo, la creación de varios servicios de bajada pronto mostró problemas de latencia.

Una vez más, los desarrolladores recurrieron a las memorias caché y los almacenes de objeto en memoria, en algunos casos con implementaciones diferentes para cumplir los requisitos de rendimiento. Empezaron a crear de procesos de trabajo de back-end para crear la memoria caché periódicamente y así minimizar la costosa población de memoria caché a petición. Por último, empezaron a deconstruir sus cargas de trabajo para aislar las operaciones asincrónicas de las sincrónicas para tener más espacio para que las operaciones interactivas reaccionaran a los cambios de estado, lo que es especialmente difícil en SOA.

Introdujeron más capas, como las colas y los trabajadores, que agregaron más complejidad a sus soluciones. Básicamente, los desarrolladores empezaron a buscar soluciones para crear "servicios con estado", es decir, colocar el "estado" junto al "comportamiento de servicio" para abordar las experiencias interactivas centradas en el usuario. Y aquí es donde entra actores de Service Fabric de Azure como una capa de composición de servicio, no como un sustituto de estos servicios.

En el diagrama siguiente se muestra esto:

![][1]

## Mejor solución con actores
En el caso de la composición de servicios, los actores pueden ser con o sin estado.

* Los actores sin estado se puede usar como servidores proxy para los servicios subyacentes. Estos actores se pueden escalar dinámicamente en el clúster de Service Fabric de Azure y pueden almacenar en memoria caché cierta información relacionada con el servicio, por ejemplo, su extremo cuando se detecta.
* El actor con estado puede mantener el estado entre llamadas de servicio, así como almacenar en memoria caché los resultados de servicio anteriores. El estado puede ser persistente o temporal.

Este patrón también es aplicable en muchos escenarios; en la mayoría de casos, los actores deben realizar llamadas externas para invocar una operación en un servicio determinado. Se ilustrará con un ejemplo de uso de aplicaciones modernas de comercio electrónico. Estas aplicaciones se basan en servicios que ofrecen diversas funcionalidades, como la administración de perfiles de usuario, recomendaciones, administración de la cesta de la compra, administración de la lista de deseos, compras y muchos más.

La mayoría de desarrolladores quiere adoptar un enfoque centrado en el usuario para su arquitectura, muy similar a los que desarrollan experiencias sociales, dado que las experiencias de comercio electrónico principalmente giran en torno a los usuarios y los productos. Normalmente, esto se consigue enviando una fachada de servicios, probablemente compatibles con la memoria caché por motivos de rendimiento.

Ahora hablemos sobre un enfoque basado en actor. Un actor de usuario puede representar tanto el comportamiento del usuario (exploración del catálogo, preferencia por un producto, la incorporación de un elemento a la cesta de la compra, la recomendación de un producto a un amigo), como el estado compuesto (su perfil, los elementos de la cesta, la lista de elementos recomendados por sus amigos, su historial de compras, la ubicación geográfica actual, etc.).

## Uso de actores con estado
En primer lugar, echemos un vistazo a un ejemplo donde el actor de usuario necesita rellenar su estado a partir de varios servicios. No vamos a ofrecer un ejemplo de código para este, porque todo lo que se ha indicado para el modelo de memoria caché inteligente también es aplicable aquí. Se puede activar el actor de usuario en el momento de inicio de sesión y rellenarlo con suficientes datos de servicios de back-end. Por supuesto, como hemos visto en muchas ocasiones anteriormente en este artículo, el estado completo y parcial puede rellenarse previamente a petición, en un temporizador o una mezcla de ambos y almacenarse en memoria caché en el actor. En este ejemplo, el perfil y lista de deseos se muestran a continuación:

![][2]

Por ejemplo podemos rellenar previamente el estado de usuario frecuente y conseguir que esté listo cuando inicie sesión o rellenarlo en el momento de inicio de sesión para los usuarios que visitan el servicio cada mes. Hemos visto estos patrones en la sección de la memoria caché inteligente.

Cuando el usuario 23 inicia sesión, si aún no está activado, el actor de usuario (23) se activa y recupera la información de perfil del usuario pertinente y la lista de deseos de los servicios back-end. El actor de usuario probablemente almacenará en memoria caché la información de las llamadas posteriores. Y si, por ejemplo, es necesario agregar un elemento a la lista de deseos, se puede realizar escritura por detrás o escritura a través como se explicó anteriormente. En segundo lugar, echemos un vistazo a un ejemplo donde el usuario hace clic en el botón "me gusta" de un producto. Esta acción puede requerir varias invocaciones a varios servicios, como se muestra a continuación: enviar un "me gusta" al servicio de catálogo, desencadenar el siguiente conjunto de recomendaciones y quizás publicar una actualización en una red social.

Esto se ilustra a continuación:

![][3]

## Cómo pueden ayudar la composición de los actores y la comunicación asincrónica
De hecho, los actores de Service Fabric de Azure destacan cuando se quieren componer operaciones de estilo de solicitud y respuesta junto con operaciones asincrónicas. Por ejemplo, aunque "Me gusta el producto" coloca inmediatamente el elemento en cuestión en lista de deseos del usuario, su publicación en las redes sociales y el desencadenamiento del siguiente conjunto de recomendaciones pueden ser operaciones asincrónicas mediante los búferes y temporizadores.

Otro beneficio principal del uso de un actor de usuario con servicios es que los actores ofrecen un lugar lógico para el estado almacenado en memoria caché y, lo más importante, reacciona ante los cambios en su estado de forma asincrónica. Se trata de un escenario especialmente complicado con servicios sin estado. Por ejemplo, un usuario lleva a cabo una serie de acciones, quizá parte del "viaje de un usuario". Estos eventos se pueden capturar en tiempo real en el actor y se puede ensamblar un flujo, que se puede consultar en tiempo de evento o de forma asincrónica en un temporizador para cambiar el comportamiento del actor.

En ese momento, los puristas de SOA sin duda se habrán dado cuenta de que no son servicios en el sentido de actores como extremos expuestos en un protocolo independiente del lenguaje. Actores de Service Fabric de Azure no es un componente de interoperabilidad ni una plataforma para la interoperación del servicio. Sin embargo, realmente no hay nada que impida pensar en términos de la granularidad de los servicios de tipo SOA al modelar los actores o en la separación del modelado de los aspectos de la misma manera. Estos servicios se conocen como "microservicios." Del mismo modo, no hay absolutamente nada que impida colocar un extremo REST o un extremo SOAP como una capa de interoperabilidad delante de actores de Service Fabric de Azure.

La composición del servicio con estado también se aplica a los flujos de trabajo y no solo a los escenarios transaccionales, como el comercio electrónico. Service Fabric de Azure está diseñado como un motor de flujo de trabajo y orquestación para que se pueda usar para modelar flujos de trabajo que impliquen interacciones de servicio y mantener el estado de esas interacciones.

Se aprecian inconvenientes del "servicio sin estado" en la creación de servicios escalables para ofrecer experiencias dinámicas. Actores de Service Fabric de Azure, principalmente al reunir estado y comportamiento, ayuda a que los desarrolladores creen experiencias interactivas y escalables sobre sus inversiones existentes.


## Pasos siguientes
[Patrón: memoria caché inteligente](service-fabric-reliable-actors-pattern-smart-cache.md)

[Patrón: gráficos y redes distribuidas](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)

[Patrón: gobernanza de recursos](service-fabric-reliable-actors-pattern-resource-governance.md)

[Patrón: Internet de las cosas](service-fabric-reliable-actors-pattern-internet-of-things.md)

[Patrón: procesamiento distribuido](service-fabric-reliable-actors-pattern-distributed-computation.md)

[Algunos antipatrones](service-fabric-reliable-actors-anti-patterns.md)

[Introducción a los actores de Service Fabric](service-fabric-reliable-actors-introduction.md)


<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-stateful-service-composition/stateful-service-composition-1.png
[2]: ./media/service-fabric-reliable-actors-pattern-stateful-service-composition/stateful-service-composition-2.png
[3]: ./media/service-fabric-reliable-actors-pattern-stateful-service-composition/stateful-service-composition-3.png

<!---HONumber=Oct15_HO2-->