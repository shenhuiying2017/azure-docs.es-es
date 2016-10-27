<properties
   pageTitle="Alta disponibilidad para aplicaciones creadas en Microsoft Azure | Microsoft Azure"
   description="Información general técnica y detallada sobre cómo diseñar y crear aplicaciones para alta disponibilidad en Microsoft Azure."
   services=""
   documentationCenter="na"
   authors="adamglick"
   manager="saladki"
   editor=""/>

<tags
   ms.service="resiliency"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/18/2016"
   ms.author="aglick"/>


#<a name="high-availability-for-applications-built-on-microsoft-azure"></a>Alta disponibilidad para aplicaciones creadas en Microsoft Azure

Una aplicación de alta disponibilidad asume las fluctuaciones en la disponibilidad y la carga, así como los errores temporales en los servicios dependientes y el hardware. La aplicación continúa funcionando en un nivel de respuesta sistémico y de usuario aceptable según lo definido por los requisitos empresariales o en los Acuerdos de Nivel de Servicio (SLA) de la aplicación.

##<a name="azure-high-availability-features"></a>Características de alta disponibilidad de Azure

Azure integra en su plataforma muchas características que admiten aplicaciones de alta disponibilidad. En esta sección se describen algunas de esas características clave. Para ver un análisis más exhaustivo de la plataforma, consulte [Guía técnica sobre resistencia en Azure](./resiliency-technical-guidance.md).

###<a name="fabric-controller"></a>Controlador de tejido

El controlador de tejido de Azure se encarga del aprovisionamiento y el control de la condición de las instancias de proceso de Azure. El controlador de tejido comprueba el estado del hardware y el software de las instancias de máquinas host e invitadas. Cuando detecta un error, aplica los SLA reubicando automáticamente las instancias de máquina virtual. El concepto de dominios de error y de actualización apoya también el SLA de procesos.

Cuando se implementan varias instancias de rol de servicios en la nube, Azure las implementa en distintos dominios de error. Un límite de dominio de error es básicamente un bastidor de hardware diferente en la misma región. Los dominios de error reducen la probabilidad de que un error de hardware localizado interrumpa el servicio de una aplicación. No se puede administrar el número de dominios de error que se asignan a los roles web o de trabajo. El controlador de tejido usa recursos dedicados que son independientes de las aplicaciones hospedadas en Azure. Su tiempo de actividad es del 100 % porque actúa como núcleo del sistema de Azure. Supervisa y administra las instancias de rol en los dominios de error.

En el siguiente diagrama, se muestran los recursos compartidos de Azure que implementa y administra el controlador de tejido en distintos dominios de error.

![Vista simplificada del aislamiento de los dominios de error](./media/resiliency-high-availability-azure-applications/fault-domain-isolation.png)

Los dominios de actualización son similares a los dominios de error en su función, aunque admiten actualizaciones en lugar de errores. Un dominio de actualización es una unidad lógica de separación de instancias que determina qué instancias de un servicio determinado se actualizarán en un momento dado. De forma predeterminada, para la implementación del servicio hospedado, se definen cinco dominios de actualización. Aun así, puede cambiar ese valor en el archivo de definición de servicio. Por ejemplo, suponga que tiene ocho instancias del rol web. Habrá dos instancias en tres dominios de actualización y dos instancias en un dominio de actualización. Azure define la secuencia de actualización, pero se basa en el número de dominios de actualización. Para más información sobre los dominios de actualización, consulte [Actualización de un servicio en la nube](../cloud-services/cloud-services-update-azure-service.md).

###<a name="features-in-other-services"></a>Características de otros servicios

Además de estas características de plataforma que admiten una gran disponibilidad de procesos, Azure inserta características de alta disponibilidad en sus demás servicios. Por ejemplo, Almacenamiento de Azure mantiene tres réplicas de todos los datos de los blobs, tablas y cola. También permite la opción de replicación geográfica para almacenar copias de seguridad de blobs y tablas en una región secundaria. La red de entrega de contenido de Azure permite que los blobs se almacenen en caché en todo el mundo tanto por redundancia como por escalabilidad. Base de datos SQL de Azure también mantiene varias réplicas.

Además de la serie de artículos de [Guía técnica sobre resistencia en Azure](https://aka.ms/bctechguide), consulte el artículo [Best Practices for Designing Large-Scale Services on Windows Azure](https://azure.microsoft.com/blog/best-practices-for-designing-large-scale-services-on-windows-azure/) (Procedimientos recomendados para el diseño de servicios a gran escala en Azure Cloud Services). Estos artículos profundizan en el análisis de las características de disponibilidad de la plataforma Azure.

Aunque Azure proporciona varias características que admiten la alta disponibilidad, es importante conocer sus limitaciones:

- Para los procesos, Azure garantiza que los roles estarán disponibles y en funcionamiento, pero no sabe si la aplicación está en ejecución o sobrecargada.
- Para Base de datos SQL de Azure, los datos se replican de forma sincrónica dentro de la región. Puede elegir la replicación geográfica activa, que permite hasta cuatro copias adicionales de la base de datos en la misma región (o en regiones diferentes). Estas réplicas de base de datos no son copias de seguridad de un momento dado. Las bases de datos SQL proporcionan funcionalidades de copia de seguridad de un momento dado. Para más información acerca de las funcionalidades de copia de seguridad de un momento dado de Base de datos SQL, consulte [Azure SQL Database Point in Time Restore](https://azure.microsoft.com/blog/azure-sql-database-point-in-time-restore/)(Restauración a un momento dado de Base de datos SQL de Azure).
- Para Almacenamiento de Azure, los datos de blobs y tablas se replican de forma predeterminada en una región alternativa. Sin embargo, no puede acceder a las réplicas hasta que Microsoft conmute por error al sitio alternativo. Una conmutación por error de región solo se produce en caso de interrupción prolongada del servicio en toda la región y no existe ningún SLA para el tiempo de conmutación por error geográfica. También es importante tener en cuenta que los daños en los datos se propagan rápidamente a las réplicas.

Por estas razones, debe complementar las características de disponibilidad de la plataforma con otras específicas de la aplicación. Entre estas últimas características, se incluye la de instantáneas de blob, que crea copias de seguridad de datos de blob en un momento dado.

###<a name="availability-sets-for-azure-virtual-machines"></a>Conjuntos de disponibilidad para Máquinas virtuales de Azure

La mayor parte de este artículo se centra en los servicios en la nube, que usan un modelo de plataforma como servicio (PaaS). Sin embargo, también existen características de disponibilidad específicas para Máquinas virtuales de Azure, que utilizan un modelo de infraestructura como servicio (IaaS). Para lograr la alta disponibilidad con Máquinas virtuales, debe usar conjuntos de disponibilidad. Un conjunto de disponibilidad realiza una función similar a los dominios de error y de actualización. Dentro de un conjunto de disponibilidad, Azure coloca las máquinas virtuales de forma que se impida que los errores de hardware localizados y las actividades de mantenimiento dejen inactivas todas las máquinas de ese grupo. Los conjuntos de disponibilidad son necesarios para lograr el SLA de Azure para la disponibilidad de Máquinas virtuales.

En el siguiente diagrama, se proporciona una representación de dos conjuntos de disponibilidad que agrupan máquinas virtuales web y SQL Server, respectivamente.

![Conjuntos de disponibilidad para Máquinas virtuales de Azure](./media/resiliency-high-availability-azure-applications/availability-set-for-azure-virtual-machines.png)

>[AZURE.NOTE] En el diagrama anterior, SQL Server está instalado y ejecutándose en las máquinas virtuales. Esto difiere de la explicación anterior sobre Base de datos SQL de Azure, que proporciona una base de datos como servicio administrado.

##<a name="application-strategies-for-high-availability"></a>Estrategias de aplicación para la alta disponibilidad

La mayoría de las estrategias de aplicación para la alta disponibilidad conllevan redundancia o la eliminación de las dependencias físicas entre los componentes de la aplicación. El diseño de la aplicación debería admitir la tolerancia a errores durante el tiempo de inactividad esporádico de Azure o de servicios de terceros. En las secciones siguientes, se describen patrones de aplicación para aumentar la disponibilidad de los servicios en la nube.

###<a name="asynchronous-communication-and-durable-queues"></a>Comunicación asincrónica y colas duraderas

Considere la comunicación asincrónica entre servicios con acoplamiento flexible para aumentar la disponibilidad en las aplicaciones de Azure. En este patrón, escriba mensajes en colas de almacenamiento o del Bus de servicio de Azure para procesarlos después. Cuando se escribe el mensaje en la cola, se devuelve el control inmediatamente al remitente del mensaje. Otra capa de la aplicación controla el procesamiento de mensajes, normalmente implementado como rol de trabajo. Si el rol de trabajo deja de funcionar, los mensajes se acumulan en la cola hasta que se restaure el servicio de procesamiento. Siempre que la cola esté disponible, no existe dependencia directa entre el remitente de front-end y el procesador de mensajes. Esto elimina la necesidad de las llamadas sincrónicas a servicios que pueden crear un cuello de botella de rendimiento en las aplicaciones distribuidas.

Otra variación usa Almacenamiento de Azure (blobs, tablas, colas) o colas del Bus de servicio como ubicación de conmutación por error para las llamadas a la base de datos con errores. Por ejemplo, una llamada sincrónica dentro de una aplicación a otro servicio (como Base de datos SQL de Azure) produce un error repetidamente. Es posible que pueda serializar esos datos en almacenamiento duradero. Más adelante, cuando el servicio o la base de datos vuelva a estar en línea, la aplicación puede enviar de nuevo la solicitud desde el almacenamiento. La diferencia en este modelo es que la ubicación intermedia no es una parte constante del flujo de trabajo de aplicación, sino que solo se usa en escenarios de error.

En ambos escenarios, la comunicación asincrónica y el almacenamiento intermedio impiden que un servicio back-end inactivo haga que toda la aplicación deje de funcionar. Las colas actúan como intermediario lógico. Para más orientación sobre cómo elegir el servicio de cola correcto, consulte [Colas de Bus de servicio y colas de Azure: comparación y diferencias](../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md).

###<a name="fault-detection-and-retry-logic"></a>Detección de errores y lógica de reintento

Un punto clave en el diseño de una aplicación de alta disponibilidad es utilizar lógica de reintento en el código para controlar correctamente un servicio que no funciona de forma temporal. El bloque de aplicación de control de errores transitorios ( [Transient Fault Handling Application Block](https://msdn.microsoft.com/library/hh680934.aspx)), desarrollado por el equipo de modelos y prácticas empresariales de Microsoft, ayuda a los desarrolladores de aplicaciones en este proceso. La palabra "transitorios" se refiere a una condición temporal de duración relativamente corta. En el contexto de este artículo, el control de errores transitorios forma parte del desarrollo de una aplicación de alta disponibilidad. Algunos ejemplos de condiciones transitorias son errores de red intermitentes y la pérdida de conexiones de bases de datos.

El bloque de aplicaciones de control de errores transitorios es una forma simplificada de controlar los errores en el código de forma correcta. Puede usarlo para mejorar la disponibilidad de las aplicaciones mediante la adición de lógica de control de errores transitorios sólida. En la mayoría de los casos, la lógica de reintento resuelve la breve interrupción y vuelve a conectar al remitente y al destinatario tras uno o varios intentos infructuosos. Cuando un reintento funciona, esto suele pasar inadvertido a los usuarios de la aplicación.

Los desarrolladores disponen de tres opciones para administrar la lógica de reintento: incremental, de intervalo fijo y exponencial. Con la opción incremental, se espera más antes de cada reintento de un modo lineal creciente (por ejemplo, 1, 2, 3 y 4 segundos). Con la opción de intervalo fijo, se espera el mismo tiempo entre cada reintento (por ejemplo, 2 segundos). Como opción más aleatoria, el retroceso exponencial espera más tiempo entre reintentos. Sin embargo, muestra un comportamiento exponencial (por ejemplo, 2, 4, 8 y 16 segundos).

La estrategia resumida en su código es:

1. Definir la estrategia y la directiva de reintento.
1. Intentar la operación que podría producir un error transitorio.
1. Si se produce un error transitorio, invocar la directiva de reintento.
1. Si se produce un error en todos los reintentos, detectar una excepción final.

Pruebe su lógica de reintento con errores simulados para asegurarse de que los reintentos en operaciones sucesivas no produzcan un retraso prolongado inesperado. Hágalo antes de decidir si se producirá un error en la tarea completa.

###<a name="reference-data-pattern-for-high-availability"></a>Patrón de datos de referencia para la alta disponibilidad

Los datos de referencia son los datos de solo lectura de una aplicación. Proporcionan el contexto empresarial en que la aplicación genera datos transaccionales en el transcurso de una operación empresarial. Los datos transaccionales son una función de un momento dado de los datos de referencia. Por lo tanto, su integridad depende de la instantánea de los datos de referencia que se tomara en el momento de la transacción. Aunque es una definición algo imprecisa, bastará para nuestro propósito aquí.

Los datos de referencia en el contexto de una aplicación son necesarios para el funcionamiento de esta. Las aplicaciones respectivas crean y mantienen datos de referencia; los sistemas de administración de datos maestros (MDM) suelen realizan esta función. Estos sistemas se encargan del ciclo de vida de los datos de referencia. Algunos ejemplos de datos de referencia son un catálogo de productos, un maestro de empleados, un maestro de piezas o un maestro de equipos. Los datos de referencia también se pueden originar fuera de la organización; por ejemplo, códigos postales o tipos impositivos. Las estrategias para aumentar la disponibilidad de los datos de referencia suelen ser menos complicadas que las de los datos transaccionales. La ventaja de los datos de referencia es que, por lo general, son inmutables.

Puede hacer que los roles web y de trabajo de Azure consuman datos de referencia autónomos en tiempo de ejecución mediante la implementación de dichos datos junto con la aplicación. Si el tamaño del almacenamiento local permite esa implementación, se trata del estado ideal. Las bases de datos insertadas (SQL, NoSQL) o los archivos XML implementados en un sistema de archivos local contribuirán a la autonomía de las unidades de escalado de procesos de Azure. Sin embargo, debe poseer un mecanismo para actualizar los datos en cada rol sin necesidad de volver a implementarlos. Para ello, coloque las actualizaciones de los datos de referencia en un punto de conexión del almacenamiento en la nube (por ejemplo, el almacenamiento de blobs de Azure o Base de datos SQL). Agregue código a cada rol que descarga las actualizaciones de datos en los nodos de proceso cuando se inicia el rol. Como alternativa, agregue código que permita a un administrador realizar una descarga forzada en las instancias de rol.

Para aumentar la disponibilidad, los roles también deben contener un conjunto de los datos de referencia por si el almacenamiento está inactivo. Esto permite que los roles se inicien con un conjunto básico de datos de referencia hasta que el recurso de almacenamiento esté disponible para las actualizaciones.

![Alta disponibilidad de aplicaciones mediante nodos de proceso autónomos](./media/resiliency-high-availability-azure-applications/application-high-availability-through-autonomous-compute-nodes.png)

Una consideración para este patrón es la velocidad de implementación e inicio para los roles. Si va a implementar o descargar grandes cantidades de datos de referencia durante el inicio, puede aumentar el tiempo necesario para poner en marcha nuevas implementaciones o instancias de rol. Podría tratarse de una concesión aceptable a cambio de contar con la autonomía de disponer inmediatamente de los datos de referencia en cada rol, en lugar de depender de servicios de almacenamiento externos.

###<a name="transactional-data-pattern-for-high-availability"></a>Patrón de datos transaccionales para la alta disponibilidad

Los datos transaccionales son aquellos generados por la aplicación en un contexto empresarial. Se trata de una combinación del conjunto de procesos de negocio implementados por la aplicación y de los datos de referencia en que se sustentan. Algunos ejemplos de datos transaccionales son los pedidos, los avisos de envío avanzados, las facturas y las oportunidades de CRM. Los datos transaccionales generados de esta forma se introducirán en sistemas externos para mantener un registro o para procesarlos más adelante.

Tenga en cuenta que los datos de referencia pueden cambiar dentro de los sistemas encargados de ellos. Por este motivo, los datos transaccionales deben guardar el contexto de los datos de referencia en un momento dado, de forma que tengan dependencias externas mínimas para su coherencia semántica. Por ejemplo, piense en la eliminación de un producto del catálogo unos meses después de que se haya entregado un pedido. El procedimiento recomendado es insertar tanto contexto de datos de referencia como sea posible en la transacción. Esto conserva la semántica asociada a la transacción, incluso si los datos de referencia cambian una vez capturada la transacción.

Como se ha mencionado, las arquitecturas que usan el acoplamiento flexible y la comunicación asincrónica se prestan a niveles más altos de disponibilidad. Esto también es válido para los datos transaccionales, aunque la implementación es más compleja. Normalmente, las nociones transaccionales tradicionales confían en la base de datos para garantizar la transacción. Cuando se incluyen capas intermedias, el código de la aplicación debe administrar correctamente los datos en diversas capas para garantizar una coherencia y una durabilidad suficientes.

La secuencia siguiente describe un flujo de trabajo que separa la captura de los datos transaccionales de su procesamiento:

1. Nodo de proceso web: se presentan los datos de referencia.
1. Almacenamiento externo: se guardan los datos transaccionales intermedios.
1. Nodo de proceso web: se completa la transacción de usuario final.
1. Nodo de proceso web: se envían los datos transaccionales completados junto con el contexto de los datos de referencia a un almacenamiento duradero temporal cuya respuesta predecible está garantizada.
1. Nodo de proceso web: se señala al usuario final la finalización de la transacción.
1. Nodo de proceso en segundo plano: se extraen los datos transaccionales, se siguen procesando si es necesario, y se envían a su ubicación final de almacenamiento en el sistema actual.

El siguiente diagrama muestra una posible implementación de este diseño en un servicio en la nube hospedado por Azure.

![Alta disponibilidad a través del acoplamiento flexible](./media/resiliency-disaster-recovery-high-availability-azure-applications/application-high-availability-through-loose-coupling.png)

Las flechas discontinuas del diagrama anterior indican un procesamiento asincrónico. El rol web de front-end desconoce este procesamiento asincrónico. Esto lleva al almacenamiento de la transacción en su destino final con referencia al sistema actual. A consecuencia de la latencia aportada por este modelo asincrónico, los datos transaccionales no están disponibles de inmediato para la consulta. Por lo tanto, cada unidad de datos transaccionales necesita guardarse en caché o en una sesión de usuario para satisfacer los requisitos inmediatos de la interfaz de usuario.

Por consiguiente, el rol web es autónomo del resto de la infraestructura. Su perfil de disponibilidad es una combinación del rol web y la cola de Azure, y no de toda la infraestructura. Además de la alta disponibilidad, este enfoque permite que el rol web se escale de forma horizontal, independientemente del almacenamiento back-end. Este modelo de alta disponibilidad puede afectar a la vertiente económica de las operaciones. Los componentes adicionales, como las colas de Azure y los roles de trabajo, pueden afectar al costo por uso mensual.

Tenga en cuenta que el diagrama anterior muestra una implementación de este enfoque desacoplado de datos transaccionales, pero existen muchas otras posibilidades. En la siguiente lista, se proporcionan algunas alternativas:

 * Se puede colocar un rol de trabajo entre el rol web y la cola de almacenamiento.
 * Se puede usar una cola del Bus de servicio en lugar de una cola de Almacenamiento de Azure.
 * El destino final podría ser Almacenamiento de Azure o un proveedor de base de datos diferente.
 * Se puede utilizar Caché de Azure en la capa web para proporcionar los requisitos inmediatos de almacenamiento en caché después de la transacción.

###<a name="scalability-patterns"></a>Patrones de escalabilidad

Es importante tener en cuenta que la escalabilidad del servicio en la nube afecta directamente a la disponibilidad. Si una carga mayor hace que el servicio no responda, la impresión del usuario es que la aplicación no está funcionando. Siga los procedimientos recomendados para la escalabilidad en función de la carga esperada de la aplicación y sus expectativas futuras. La máxima escala incluye muchas consideraciones, como el uso de una única cuenta de almacenamiento o varias, el uso compartido entre varias bases de datos y las estrategias de almacenamiento en caché. Para una visión detallada de estos patrones, consulte [Best Practices for Designing Large-Scale Services on Windows Azure](https://azure.microsoft.com/blog/best-practices-for-designing-large-scale-services-on-windows-azure/)(Procedimientos recomendados para el diseño de servicios a gran escala en Windows Azure).

##<a name="next-steps"></a>Pasos siguientes

Este artículo forma parte de una serie enfocada a la [recuperación ante desastres y la alta disponibilidad para aplicaciones creadas en Microsoft Azure](./resiliency-disaster-recovery-high-availability-azure-applications.md). El siguiente artículo de esta serie es [Recuperación ante desastres para aplicaciones generadas en Microsoft Azure](./resiliency-disaster-recovery-azure-applications.md).



<!--HONumber=Oct16_HO2-->


