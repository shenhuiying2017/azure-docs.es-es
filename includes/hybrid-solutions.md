# Service Bus

Si el software se ejecuta en la nube o de manera local, tiene que interactuar a menudo con otro software. Azure ofrece el bus de servicio para realizar esa tarea. En este artículo se examina esta tecnología y se describe qué es y los motivos que pueden impulsarle a usarla.

## Tabla de contenido

-   [Elementos fundamentales del bus de servicio][]
-   [Colas][]
-   [Temas][]
-   [Relés][]

## <a name="fundamentals"></a>Elementos fundamentales del bus de servicio

Las distintas situaciones requieren distintos estilos de comunicación. A veces, permitir a las aplicaciones enviar y recibir mensajes mediante una simple cola es la mejor solución. En otras situaciones, una cola ordinaria no es suficiente y es mejor una cola con un mecanismo de publicación y suscripción. En otros casos, lo realmente necesario es una conexión entre las aplicaciones y no se requieren colas. El bus de servicio ofrece las tres opciones, por lo que sus aplicaciones pueden interactuar de varias maneras diferentes.

El bus de servicio es un servicio en la nube multiinquilino, lo que significa que varios usuarios comparten el servicio. Cada usuario, como un desarrollador de aplicaciones, crea un *espacio de nombres* y, a continuación, define los mecanismos de comunicación que necesita dentro de ese espacio de nombres. La [ilustración 1][] muestra su apariencia.

<a name="Fig1"></a>![Diagrama del bus de servicio de Azure][]

**Ilustración 1: El bus de servicio proporciona un servicio multiinquilino para conectar aplicaciones a través de la nube.**

Dentro de un espacio de nombres puede usar uno o más ejemplos de los tres mecanismos de comunicación diferentes, que se conectan a las aplicaciones de forma distinta. Las opciones son las siguientes:

-   *Colas*, que permiten una comunicación unidireccional. Cada cola funciona como un intermediario (al que se le llama a veces *agente*) que almacena los mensajes enviados hasta que se reciben.
-   *Temas*, que proporcionan una comunicación unidireccional mediante *suscripciones*. Como en las colas, los temas funcionan como un agente, pero permiten a cada suscripción visualizar solo los mensajes que cumplen con criterios específicos.
-   *Relés*, que permiten una comunicación bidireccional. A diferencia de las colas y los temas, los relés no almacenan mensajes que se encuentran en proceso; no son un agente. Simplemente los transmiten a la aplicación de destino.

Cuando crea una cola, un tema o un relé, les pone un nombre. Cuando se combina con el nombre del espacio de nombres, se crea un identificador exclusivo para el objeto. Las aplicaciones pueden proporcionar ese nombre al bus de servicio y, a continuación, usar esa cola, tema o relé para comunicarse con los demás.

Para usar cualquiera de estos objetos, las aplicaciones de Windows pueden usar Windows Communication Foundation (WCF). Para las colas y los temas, las aplicaciones de Windows también pueden usar una API de mensajería definida por el bus de servicio. Asimismo, se puede obtener acceso a las colas y los temas a través de HTTP. Además, para que sean más fáciles de usar desde aplicaciones que no sean de Windows, Microsoft proporciona SDK para Java, Node.js y otros lenguajes.

Es importante comprender que aunque el bus de servicio se ejecute en la nube (es decir, en los centros de datos de Azure de Microsoft), las aplicaciones que usa pueden ejecutarse desde cualquier lugar. Puede usar el bus de servicio para conectarse a aplicaciones que se ejecutan, por ejemplo, en Azure o aplicaciones que se ejecutan dentro de su propio centro de datos. También puede usarlo para conectarse a una aplicación que se ejecuta en Azure o a otra plataforma en la nube con una aplicación local o con tabletas y teléfonos. Es posible incluso conectar aparatos domésticos, sensores y otros dispositivos a una aplicación central o entre ellos. El bus de servicio es un mecanismo de comunicación genérico en la nube al que puede obtenerse acceso desde prácticamente cualquier lugar. La forma en la que lo use depende de las tareas que tengan que realizar sus aplicaciones.

## <a name="queues"></a>Colas

Supongamos que decide conectar dos aplicaciones con una cola de bus de servicio. La [ilustración 2][] muestra esa situación.

<a name="Fig2"></a>![Diagrama de las colas del bus de servicio][]

**Ilustración 2: Las colas del bus de servicio proporcionan una cola asincrónica unidireccional.**

El proceso es simple: un usuario envía un mensaje a la cola del bus de servicio y un receptor recoge ese mensaje más tarde. Es posible que una cola tenga único receptor, como se muestra en la [ilustración 2][], o bien que varias aplicaciones puedan leer desde la misma cola. En esa última situación, los mensajes los lee normalmente solo un receptor; las colas no proporcionan un servicio multidifusión.

Cada mensaje dispone de dos partes: un conjunto de propiedades, cada una con un par clave-valor, y un cuerpo de mensaje binario. La forma en la que se usen depende de las tareas que intente realizar la aplicación. Por ejemplo, una aplicación que envía un mensaje sobre una venta reciente podría incluir las propiedades *Vendedor="Ava"* y *Cantidad=10.000*. El cuerpo del mensaje puede contener una imagen escaneada del contrato firmado de la venta o, si no la hubiera, puede aparecer vacío.

El receptor puede leer los mensajes de la cola del bus de servicio de dos formas distintas. La primera opción, denominada ReceiveAndDelete, quita un mensaje de la cola y lo elimina de inmediato. Esto es sencillo, pero si se produce un error por parte del receptor antes de que finalice el procesamiento del mensaje, este se perderá. Puesto que se quita de la cola, ningún otro receptor podrá obtener acceso a él.

La segunda opción, PeekLock, se ha creado para solucionar este problema. Como ocurre con ReceiveAndDelete, una lectura de PeekLock quita un mensaje de la lista. Sin embargo, no elimina el mensaje. En este caso, bloquea el mensaje, por lo que se oculta a los demás receptores. A continuación, espera a que se produzca uno de los tres eventos:

-   Si el receptor procesa el mensaje correctamente, invoca Completo, y la cola elimina el mensaje.
-   Si el receptor decide que no puede procesar el mensaje correctamente, invoca Abandonar. La cola quita el bloqueo del mensaje y se pone a disposición de otros receptores.
-   Si el receptor no invoca ninguna de esas opciones en un período configurable (60 segundos de forma predeterminada), la cola presupone que se ha producido un error con el receptor. En ese caso, se comporta como si el receptor hubiera invocado Abandonar, por lo que el mensaje estaría disponible para otros receptores.

Tenga en cuenta que puede ocurrir esto: el mismo mensaje puede enviarse dos veces, quizás a dos receptores distintos. Las aplicaciones que usan colas de bus de servicio deben estar preparadas para ello. Para facilitar la detección duplicada, cada mensaje dispone de una propiedad MessageID exclusiva que no se modifica de forma predeterminada independientemente del número de veces que se lea un mensaje en una cola.

Las colas son útiles en determinadas situaciones. Permiten a las aplicaciones comunicarse incluso cuando no se estén ejecutando a la vez. Esto es especialmente útil con aplicaciones móviles y por lotes. Una cola con varios receptores también proporciona un equilibrio de carga automático, ya que los mensajes enviados se difunden a través de esos receptores.

## <a name="topics"></a>Temas

Aunque son útiles, las colas no siempre son la solución adecuada. A veces, los temas de bus de servicio son mejores. La [ilustración 3][] muestra esa perspectiva.

<a name="Fig3"></a>![Diagrama de las suscripciones y temas del bus de servicio][]

**Ilustración 3: Según el filtro que especifica una aplicación de suscripción, puede recibir algunos mensajes, o todos, enviados a un tema del bus de servicio.**

Un tema es similar en muchos aspectos a una cola. Los remitentes envían mensajes a un tema de la misma forma en la que envían mensajes a una cola, y esos mensajes tienen la misma apariencia que las colas. La gran diferencia es que los temas permiten a cada aplicación de recepción crear su propia suscripción mediante la definición de un *filtro*. El suscriptor verá, por lo tanto, solo los mensajes que coincidan con ese filtro. Por ejemplo, la [ilustración 3][] muestra un remitente y un tema con tres suscriptores, cada uno con su propio filtro:

-   El suscriptor 1 recibe solo mensajes que contienen la propiedad *Vendedor="Ava"*.
-   El suscriptor 2 recibe mensajes que contienen la propiedad *Vendedor="Ruby"* o contienen una propiedad *Cantidad* cuyo valor es superior a 100.000. Quizás Ruby es la directora de ventas y por eso quiere ver sus propias ventas y todas las grandes ventas independientemente de quien las haya realizado.
-   El suscriptor 3 ha establecido su filtro en *Verdadero*, lo que significa que recibe todos los mensajes. Esta aplicación puede ser la responsable del mantenimiento, por ejemplo, de una pista de auditoría, y por eso necesita verlo todo.

Como ocurre con las colas, los suscriptores a un tema pueden leer los mensajes utilizando ReceiveAndDelete o PeekLock. Sin embargo, a diferencia de las colas, varios suscriptores pueden recibir un mensaje único enviado a un tema. Este enfoque, que se denomina normalmente *publicar y suscribir*, es útil cuando varias aplicaciones están interesadas en los mismos mensajes. Si se define el filtro adecuado, cada suscriptor puede pulsar solo la parte de la secuencia de mensaje que necesita ver.

## <a name="relays"></a>Relés

Tanto las colas como los temas proporcionan una comunicación asincrónica unidireccional a través de un agente. El tráfico fluye en una única dirección y no existe una conexión directa entre los remitentes y los receptores. Sin embargo, ¿qué ocurre si no es eso lo que quiere? Supongamos que sus aplicaciones necesitan enviar y recibir, o que quizás quiere un vínculo directo entre ellas y no dispone de sitio para almacenar los mensajes entre las dos. Para solucionar problemas como este, el bus de servicio proporciona relés, como se muestra en la [ilustración 4][].

<a name="Fig4"></a>![Diagrama del relé del bus de servicio][]

**Ilustración 4: El relé del bus de servicio proporciona una comunicación sincrónica bidireccional entre aplicaciones.**

La pregunta obvia que se plantea acerca de los relés es la siguiente: ¿en qué situaciones usaría uno? Incluso si no necesito colas, ¿por qué hacer que las aplicaciones se comuniquen a través de un servicio en la nube en lugar de interactuar directamente? La respuesta es que hablar directamente puede ser más complicado de lo que se piensa.

Supongamos que desea conectar dos aplicaciones locales que se ejecutan dentro de centros de datos corporativos. Cada una de esas aplicaciones se encuentran protegidas por un firewall y cada centro de datos usa probablemente la traducción de direcciones de red (NAT). El firewall bloquea los datos entrantes en casi todos los puertos y NAT implica que la máquina en la que se ejecuta cada aplicación no dispone probablemente de una dirección IP fija. Si no se cuenta con ayuda adicional, conectar esas aplicaciones mediante una conexión pública a Internet es complicado.

Un relé de bus de servicio proporciona esa ayuda. Para comunicarse de forma bidireccional a través de un relé, cada aplicación establece una conexión TCP saliente con el bus de servicio y, a continuación, la mantiene abierta. Todas las comunicaciones entre las dos aplicaciones se llevarán a cabo a través de esas conexiones. Puesto que cada conexión se estableció desde dentro del centro de datos, el firewall permitirá el tráfico entrante en los datos de aplicaciones enviados a través del relé sin abrir los puertos nuevos. Este enfoque también evita el problema de NAT, puesto que cada aplicación cuenta con un extremo coherente a través de la comunicación. Las aplicaciones pueden evitar los problemas que podrían complicar la comunicación mediante el intercambio de datos a través del relé.

Para usar relés de bus de servicio, las aplicaciones se basan en Windows Communication Foundation (WCF). El bus de servicio proporciona enlaces a WCF que facilitan la interactuación de las aplicaciones de Windows a través de los relés. Las aplicaciones que ya usan WCF pueden normalmente especificar solo uno de esos enlaces y después comunicarse con los demás a través de un relé. Sin embargo, a diferencia de las colas y los temas, el uso cuando sea posible de relés de aplicaciones que no sean de Windows requiere algún tipo de esfuerzo de programación; no se proporcionan bibliotecas estándar.

Al contrario de lo que ocurre con las colas y los temas, las aplicaciones no crean relés explícitamente. En este caso, cuando una aplicación que desea recibir mensajes establece una conexión TCP con el bus de servicio, se crea un relé automáticamente. Cuando se abandona la conexión, el relé se elimina. Para que una aplicación encuentre un relé creado por un agente de escucha específico, el bus de servicio proporciona un registro que permite ubicar un relé específico por nombre.

Los relés son la solución adecuada cuando necesita una comunicación directa. Piense, por ejemplo, en un sistema de reserva de una aerolínea que se ejecute en un centro de datos local y al que deba obtenerse acceso a través de mostradores de facturación, dispositivos móviles y otros equipos. Las aplicaciones que se ejecuten en todos esos sistemas podrían confiar en los relés del bus de servicio en la nube para comunicarse independientemente del lugar en el que se estén ejecutando.

La conexión de las aplicaciones ha formado siempre parte de la creación de soluciones completas y es complicado que el problema desaparezca. Al proporcionar tecnologías basadas en la nube para realizar esto a través de colas, temas y relés, Service Bus pretende facilitar esta función fundamental y ofrecer una disponibilidad más amplia.

  [Elementos fundamentales del bus de servicio]: #fundamentals
  [Colas]: #queues
  [Temas]: #topics
  [Relés]: #relays
  [ilustración 1]: #Fig1
  [Diagrama del bus de servicio de Azure]: ./media/hybrid-solutions/SvcBus_01_architecture.png
  [ilustración 2]: #Fig2
  [Diagrama de las colas del bus de servicio]: ./media/hybrid-solutions/SvcBus_02_queues.png
  [ilustración 3]: #Fig3
  [Diagrama de las suscripciones y temas del bus de servicio]: ./media/hybrid-solutions/SvcBus_03_topicsandsubscriptions.png
  [ilustración 4]: #Fig4
  [Diagrama del relé del bus de servicio]: ./media/hybrid-solutions/SvcBus_04_relay.png
