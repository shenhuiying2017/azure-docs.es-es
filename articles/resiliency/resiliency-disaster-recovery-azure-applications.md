<properties
   pageTitle="Recuperación ante desastres para aplicaciones de Azure | Microsoft Azure"
   description="Información general técnica y detallada sobre cómo diseñar aplicaciones para recuperación ante desastres en Microsoft Azure."
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

#Recuperación ante desastres para aplicaciones generadas en Microsoft Azure

Mientras que la alta disponibilidad está relacionada con la administración de errores temporales, la recuperación ante desastres (DR) lo está con la pérdida de funcionalidad de la aplicación. Por ejemplo, piense en un escenario en el que una región deja de funcionar. En ese caso, es preciso que tenga un plan para ejecutar la aplicación o acceder a los datos fuera de la región de Azure. La ejecución de este plan implica a las personas, los procesos y las aplicaciones auxiliares que permiten que funcione el sistema. Los propietarios de negocios y tecnología, que definen el modo operativo del sistema para un desastre, determinan también el nivel de funcionalidad del servicio durante un desastre. El nivel de funcionalidad puede adoptar diversas formas: total falta de disponibilidad, disponibilidad parcial (funcionalidad degradada o procesamiento diferido) o disponibilidad total.

##Características de recuperación ante desastres de Azure

Al igual que sucede con las consideraciones de disponibilidad, Azure tiene [manuales técnicos de resistencia ](./resiliency-technical-guidance.md) diseñados para dar soporte técnico para la recuperación ante desastres. También existe una relación entre algunas de las características de disponibilidad de Azure y la recuperación ante desastres. Por ejemplo, la administración de roles en dominios de error aumenta la disponibilidad de una aplicación. Sin esa administración, un error de hardware no controlado se convertiría en un escenario de "desastre". Por consiguiente, la aplicación correcta de las características y estrategias de disponibilidad es una parte importante a la hora de que una aplicación se haya creado a prueba de desastres. Sin embargo, este artículo va más allá de los problemas de disponibilidad general, ya que trata eventos de desastres más graves (y menos frecuentes).

##Regiones con varios centros de datos

Azure mantiene centros de datos en muchas regiones del mundo. Esta infraestructura da soporte a varios escenarios de recuperación ante desastres, como la replicación geográfica que proporciona el sistema de Almacenamiento de Azure a regiones secundarias. También significa que se puede implementar fácil y económicamente un servicio en la nube en varias ubicaciones de todo el mundo. Compare esto con el costo y la dificultad que supone ejecutar sus propios centros de datos en varias regiones. La implementación de datos y servicios en varias regiones contribuye a proteger la aplicación frente a interrupciones importantes en una sola región.

##Administrador de tráfico de Azure

Si se produce un error en una región concreta, el tráfico debe redirigirse a los servicios o implementaciones de otra región. Este enrutamiento se puede realizar manualmente, pero es más eficaz utilizar un proceso automatizado. El Administrador de tráfico de Azure está diseñado para esta tarea. Puede usarse para administrar automáticamente la conmutación por error del tráfico de los usuarios a otra región, en caso de que se produzca un error en la región principal. Dado que la administración del tráfico es una parte importante de la estrategia global, es importante comprender los conceptos básicos del Administrador de tráfico.

En el diagrama siguiente, los usuarios se conectan a una dirección URL especificada para el Administrador de tráfico (__http://myATMURL.trafficmanager.net__) que resume las direcciones URL de los sitios reales (__http://app1URL.cloudapp.net__ y \_\_http://app2URL.cloudapp.net__). En función de cómo se configuren los criterios de enrutamiento de los usuarios, se les enviará al sitio real correcto cuando la directiva lo establezca. Las opciones de la directiva son: round-robin, rendimiento o conmutación por error. Para este artículo, solo nos centraremos en la opción de conmutación por error.

![Enrutamiento mediante el Administrador de tráfico de Azure](./media/resiliency-disaster-recovery-azure-applications/routing-using-azure-traffic-manager.png)

Cuando se configura el Administrador de tráfico, se proporciona un nuevo prefijo de DNS del Administrador de tráfico. Este es el prefijo de la dirección URL que proporcionará a los usuarios para que accedan al servicio. El Administrador de tráfico ahora abstrae el equilibrio de carga a un nivel superior, no en el nivel de la región. El DNS del Administrador de tráfico se asigna a un CNAME en todas las implementaciones que administra.

En el Administrador de tráfico, especifique la prioridad de las implementaciones a las que se enrutarán los usuarios cuando se produce un error. El Administrador de tráfico supervisa los puntos de conexión de las implementaciones y anota cuándo se produce un error en la implementación principal. En el error, el Administrador de tráfico analizará la lista prioritaria de las implementaciones y enrutará los usuarios a la siguiente de dicha lista.

Aunque el Administrador de tráfico decide adónde debe ir en una conmutación por error, usted puede decidir si su dominio de conmutación por error está inactivo o activo mientras no está en modo de conmutación por error. Esa funcionalidad no tiene nada que ver con el Administrador de tráfico de Azure. El Administrador de tráfico detecta un error en el sitio principal y cambia al sitio de conmutación por error. El Administrador de tráfico realiza el cambio, independientemente de si dicho sitio sirve actualmente a los usuarios o no.

Para más información sobre el funcionamiento del Administrador de tráfico de Azure, consulte:

 * [Información general sobre el Administrador de tráfico](../traffic-manager/traffic-manager-overview.md)
 * [Configuración del método de enrutamiento de conmutación por error](../traffic-manager/traffic-manager-configure-failover-routing-method.md)

##Escenarios de desastre de Azure

Las siguientes secciones tratan varios tipos de escenarios de desastre. Las interrupciones del servicio a nivel de toda la región no son la única causa de errores en toda la aplicación. Un diseño deficiente o errores de administración también pueden provocar interrupciones. Es importante tener en cuenta las posibles causas de un error durante las fases de prueba y de diseño del plan de recuperación. Un buen plan saca provecho a las características de Azure y las aumenta con estrategias específicas de la aplicación. La respuesta elegida viene determinada por la importancia de la aplicación, el objetivo de punto de recuperación (RPO) y el objetivo de tiempo de recuperación (RTO).

###Error de la aplicación

El Administrador de tráfico de Azure controla automáticamente los errores generados por el software subyacente o el sistema operativo de la máquina virtual host. Azure crea una nueva instancia del rol en un servidor que funciona y la agrega a la rotación del equilibrador de carga. Si el número de instancias de rol es mayor que uno, Azure desplaza el procesamiento a las otras instancias de rol en ejecución mientras se reemplaza el nodo con error.

Hay errores graves de la aplicación que aparecen, independientemente de que haya errores de hardware o del sistema operativo. La aplicación podría dejar de funcionar debido a las excepciones catastróficas causadas por una lógica defectuosa o por problemas en la integridad de los datos. Debe incorporar suficiente telemetría al código para que un sistema de supervisión pueda detectar condiciones de error y enviar las notificaciones pertinentes a un administrador de aplicaciones. Un administrador con conocimiento completo de los procesos de recuperación ante desastres puede tomar la decisión de invocar un proceso de conmutación por error. Como alternativa, el administrador puede simplemente aceptar una interrupción de la disponibilidad para resolver los errores críticos.

###Datos dañados

Azure almacena automáticamente los datos de Base de datos SQL de Azure y Almacenamiento de Azure tres veces en distintos dominios de error de la misma región. Si utiliza la replicación geográfica, los datos se almacenan tres veces más en una región distinta. Sin embargo, si los usuarios o la aplicación dañan los datos de la copia principal, los datos se replican rápidamente en las restantes copias. Lamentablemente, esto genera tres copias de datos dañados.

Para administrar el posible daño de los datos, tiene dos opciones. En primer lugar, puede administrar una estrategia de copia de seguridad personalizada. Puede almacenar las copias de seguridad en Azure o de forma local, en función de los requisitos empresariales o las regulaciones gubernamentales. Otra posibilidad es utilizar la nueva opción de restauración a un momento dado para recuperar una base de datos SQL. Para más información, consulte la sección sobre las [estrategias de datos para la recuperación ante desastres](#data-strategies-for-disaster-recovery).

###Interrupción de la red

Cuando no se puede acceder a algunas partes de la red de Azure, es posible que no pueda obtener acceso a una aplicación o a los datos. Si una o varias instancias de rol no están disponibles debido a problemas en la red, Azure usa las restantes instancias disponibles de la aplicación. Si la aplicación no puede acceder a los datos debido a una interrupción en la red de Azure, puede ejecutarse localmente en modo degradado mediante el uso de los datos almacenados en la caché. Para la ejecución en modo degradado en la aplicación, es preciso diseñar la estrategia de recuperación ante desastres. En algunas aplicaciones, esto puede no resultar práctico.

Otra opción es almacenar los datos en una ubicación alternativa hasta que se restaure la conectividad. Si el modo degradado no es una opción, las restantes opciones son el tiempo de inactividad de la aplicación o la conmutación por error a otra región. El diseño de una aplicación que se ejecuta en modo degradado es mucho más una decisión empresarial que técnica. Esto se explica más detalladamente en la sección sobre [funcionalidad de aplicación degradada](#degraded-application-functionality).

###Error de un servicio dependiente

Azure proporciona muchos servicios que pueden experimentar tiempos de inactividad periódicamente. Por ejemplo, piense en [Caché en Redis de Azure](https://azure.microsoft.com/services/cache/). El servicio multiinquilino proporciona funcionalidades de almacenamiento en caché a su aplicación. Es importante tener en cuenta lo que ocurre en la aplicación si el servicio dependiente no está disponible. En muchos sentidos, este escenario es similar al escenario de interrupción de la red. Sin embargo, si se considera cada servicio de forma independiente, se consiguen mejoras potenciales en el plan global.

Caché en Redis de Azure proporciona almacenamiento en caché a una aplicación desde la implementación del servicio en la nube, lo que proporciona beneficios en la recuperación ante desastres. En primer lugar, el servicio se ejecuta en roles locales de cara a la implementación. Por consiguiente, tiene mayor capacidad para supervisar y administrar el estado de la memoria caché como parte de los procesos de administración global del servicio en la nube. Este tipo de almacenamiento en caché también expone nuevas características. Una de estas nuevas características es la alta disponibilidad de los datos almacenados en la caché. Esto ayuda a conservar los datos almacenados en la memoria caché en caso de que se produzca un error en un nodo individual, mediante el mantenimiento de copias duplicadas en otros nodos.

Tenga en cuenta que la alta disponibilidad reduce el rendimiento y aumenta la latencia debido a la actualización de la copia secundaria en las escrituras. También dobla la cantidad de memoria que se utiliza para cada elemento, algo que debe tener en cuenta. Este ejemplo concreto muestra que cada servicio dependiente puede tener funcionalidades que mejoran la disponibilidad general y resistencia a errores catastróficos.

Con cada servicio dependiente, debe comprender las implicaciones de una interrupción del servicio. En el ejemplo del almacenamiento en la caché, es posible acceder a los datos directamente desde una base de datos hasta que se restaure la memoria caché. Esto sería un modo degradado, en términos de rendimiento, pero proporcionaría la funcionalidad completa con respecto a los datos.

###Interrupción del servicio en toda la región

Los anteriores han sido principalmente errores que pueden administrarse en la misma región de Azure. Sin embargo, también debe prepararse para la posibilidad de que se produzca una interrupción del servicio en toda la región. Si se produce una interrupción del servicio en toda la región, las copias redundantes locales de los datos no estarán disponibles. Si ha habilitado la replicación geográfica, hay tres copias adicionales de los blobs y las tablas en una región distinta. Si Microsoft declara la región perdida, Azure reasignará todas las entradas de DNS a la región con replicación geográfica.

>[AZURE.NOTE] Tenga en cuenta que no tiene ningún control sobre este proceso y que solo se producirá si se produce una interrupción del servicio en toda la región. Por este motivo, debe confiar en otras estrategias de copia de seguridad específicas de la aplicación para lograr el máximo nivel de disponibilidad. Para más información, consulte la sección sobre las [estrategias de datos para la recuperación ante desastres](#data-strategies-for-disaster-recovery).

###Interrupción de un servicio en todo Azure

En el planeamiento de desastres, es preciso tener en cuenta toda la gama de posibles desastres. Una de las interrupciones de servicio más graves implicaría a todas las regiones de Azure de manera simultánea. Al igual que con otras interrupciones de servicio, puede decidir asumir el riesgo de una inactividad temporal del evento. Las interrupciones de servicio generalizadas que abarcan regiones deberían ser mucho menos habituales que las aisladas que afectan a servicios dependientes o regiones individuales.

Sin embargo, en algunas aplicaciones críticas, puede decidir que también debe haber un plan de copia de seguridad para este escenario. El plan de este evento puede incluir la conmutación por error de servicios en una [nube alternativa](#alternative-cloud) o en una [solución híbrida local y en la nube](#hybrid-on-premises-and-cloud-solution).

###Funcionalidad de aplicación degradada

Una aplicación bien diseñada suele utilizar una colección de módulos que se comunican entre sí a través de la implementación de patrones de intercambio de información de acoplamiento flexible. Las aplicaciones compatibles con recuperación ante desastres requieren la separación de tareas en el nivel de módulo. De este modo, se evita que la interrupción de un servicio dependiente desactive toda la aplicación. Por ejemplo, piense en una aplicación de comercio electrónico para la empresa Y. Los módulos siguientes podrían constituir la aplicación:

 * __Catálogo de productos__: permite a los usuarios examinar los productos.
 * __Carro de la compra__: permite a los usuarios agregar productos al carro de la compra o quitarlos de él.
 * __Estado del pedido__: muestra el estado de envío de los pedidos de los usuarios.
 * __Envío del pedido__: finaliza la sesión de compra enviando el pedido con el pago.
 * __Procesamiento del pedido__: valida la integridad de los datos del pedido y realiza la comprobación de la disponibilidad de la cantidad solicitada.

Cuando deja de funcionar una dependencia de un módulo de esta aplicación, ¿cómo funciona el módulo hasta que se recupera esa parte? Un sistema bien elaborado implementa los límites de aislamiento mediante la separación de tareas tanto en el tiempo de diseño como en el tiempo de ejecución. Todos los errores se pueden clasificar como recuperables y no recuperables. Los errores no recuperables harán que el módulo deje de funcionar, pero los errores recuperables se pueden mitigar a través de ciertas alternativas. Como se ha explicado en la sección de alta disponibilidad, puede ocultar algunos problemas a los usuarios mediante el control de los errores y la toma de acciones alternativas. Durante una interrupción del servicio más grave, la aplicación podría estar completamente no disponible. Sin embargo, una tercera opción es seguir prestando servicio a los usuarios en modo degradado.

Por ejemplo, si la base de datos para hospedar los pedidos deja de funcionar, el módulo Procesamiento del pedido pierde su capacidad para procesar transacciones de ventas. En función de la arquitectura, puede que sea difícil o imposible que las partes de Envío del pedido y Procesamiento del pedido de la aplicación sigan funcionando. Si la aplicación no está diseñada para controlar este escenario, toda ella puede quedarse sin conexión.

Sin embargo, en este mismo escenario, es posible que los datos de los productos se almacenen en otra ubicación. En ese caso, el módulo Catálogo de productos puede utilizarse para ver los productos. En modo degradado, la aplicación sigue estando disponible para los usuarios de la funcionalidad disponible, como ver el catálogo de productos. Sin embargo, otras partes de la aplicación no están disponibles, como los pedidos o las consultas al inventario.

Otra variación de modo degradado se centra en el rendimiento, en lugar de en las funcionalidades. Por ejemplo, piense en un escenario donde el catálogo de productos se haya almacenado en la memoria caché con Caché en Redis de Azure. Si el almacenamiento en caché deja de estar disponible, es posible que la aplicación vaya directamente al almacenamiento del servidor para recuperar la información del catálogo de productos. Pero este acceso puede ser más lento que la versión en la memoria caché. Por este motivo, el rendimiento de la aplicación se degrada hasta que el servicio de almacenamiento en caché esté completamente restaurado.

Decidir qué parte de una aplicación seguirá funcionando en modo degradado es una decisión tanto empresarial como técnica. La aplicación también debe decidir cómo informar a los usuarios de los problemas temporales. En este ejemplo, la aplicación puede permitir la visualización de productos, e incluso su incorporación a un carro de la compra. Sin embargo, cuando el usuario intenta realizar una compra, la aplicación notifica al usuario que el módulo de ventas está temporalmente inaccesible. No es lo ideal para el cliente, pero evita una interrupción del servicio en toda la aplicación.

##Estrategias de datos para la recuperación ante desastres

El control correcto de los datos es el área más difícil en cualquier plan de recuperación ante desastres. La restauración de los datos es también la parte del proceso de recuperación que suele durar más tiempo. Varias opciones de los modos de degradación plantean retos difíciles para la recuperación de datos cuando se produce un error y la coherencia después de un error.

Uno de los factores es la necesidad de restaurar o mantener una copia de los datos de la aplicación. Estos datos se usarán como referencia y para transacciones en un sitio secundario. Una configuración local requiere un proceso de planeamiento costoso y largo para implementar una estrategia de recuperación ante desastres en varias regiones. Afortunadamente, la mayoría de los proveedores de nube, incluido Azure, permiten que las aplicaciones se implementen fácilmente en varias regiones. Estas regiones están distribuidas geográficamente de tal forma que la interrupción del servicio en varias regiones sea muy poco habitual. La estrategia para el control de datos entre regiones es uno de los factores que contribuyen el éxito de cualquier plan de recuperación ante desastres.

En Las secciones siguientes se describen las técnicas de recuperación ante desastres relacionadas con las copias de seguridad de datos, los datos de referencia y los datos transaccionales.

###Copia de seguridad y restauración

Copias de seguridad periódicas de los datos de la aplicación pueden dar soporte a algunos escenarios de recuperación ante desastres. Recursos de almacenamiento diferentes requieren técnicas diferentes.

En los niveles Basic, Standard y Premium de Base de datos SQL, se puede sacar provecho de la función de restauración a un momento dado para recuperar una base de datos. Para más información, consulte [Información general: continuidad del negocio en la nube y recuperación ante desastres con la Base de datos SQL](../sql-database/sql-database-business-continuity.md). Otra opción es usar la replicación geográfica activa para Base de datos SQL. Con esta función se replican automáticamente los cambios de la base de datos en bases de datos secundarias de la misma región de Azure, o incluso de una región distinta. Esto proporciona una posible alternativa a algunas de las técnicas de sincronización de datos más manuales que se presentan en este artículo. Para más información, consulte [Información general: Replicación geográfica activa para Base de datos SQL de Azure](../sql-database/sql-database-geo-replication-overview.md).

También puede utilizar un enfoque más manual para realizar copias de seguridad y restauraciones. Utilice el comando DATABASE COPY para crear una copia de la base de datos. Debe utilizar este comando para obtener una copia de seguridad con coherencia transaccional. También puede usar el servicio de importación y exportación de Base de datos SQL de Azure. Esto permite exportar bases de datos a archivos BACPAC que se almacenan en el Almacenamiento de blobs de Azure.

La redundancia integrada de Almacenamiento de Azure crea dos réplicas del archivo de copia de seguridad en la misma región. Sin embargo, la frecuencia de ejecución del proceso de copia de seguridad determina el RPO, que es la cantidad de datos que se pueden perder en escenarios de desastres. Por ejemplo, suponga que realiza una copia de seguridad a la hora en punto y se produce un desastre dos minutos antes de la hora en punto. Perderá los 58 minutos de datos que se produjeron después de la última copia de seguridad. Además, para protegerse contra una interrupción del servicio en toda una región, debe copiar los archivos BACPAC en otra región. A continuación, tiene la opción de restaurar dichas copias de seguridad en la otra región. Para más información, consulte [Información general: continuidad del negocio en la nube y recuperación ante desastres con la Base de datos SQL](../sql-database/sql-database-business-continuity.md).

En el caso de Almacenamiento de Azure, puede desarrollar su propio proceso de copia de seguridad personalizado, o bien utilizar una de las muchas herramientas de copia de seguridad de terceros. Tenga en cuenta que la mayoría de los diseños de aplicaciones presentan complejidades adicionales donde los recursos de almacenamiento se hacen referencia entre sí. Por ejemplo, piense en una Base de datos SQL que tenga una columna vinculada a un blob de Almacenamiento de Azure. Si las copias de seguridad no se realizan simultáneamente, la base de datos puede tener el puntero a un blob del que no se había realizado una copia de seguridad antes del error. La aplicación o el plan de recuperación ante desastres debe implementar procesos que controlen esta incoherencia tras una recuperación.

###Patrón de datos de referencia para la recuperación ante desastres

Los datos de referencia son de solo lectura y admiten la funcionalidad de la aplicación. Normalmente no cambian con frecuencia. Aunque la copia de seguridad y restauración es un método para controlar las interrupciones del servicio en toda una región, el RTO es relativamente largo. Al implementar la aplicación en una región secundaria, algunas estrategias pueden mejorar el RTO de los datos de referencia.

Dado que los datos de referencia cambian con poca frecuencia, el RTO se puede mejorar manteniendo una copia permanente de los datos de referencia en la región secundaria. Esto elimina el tiempo requerido para restaurar copias de seguridad en caso de desastre. Para cumplir los requisitos de recuperación ante desastres en varias regiones, es preciso implementar la aplicación y los datos de referencia juntos en varias regiones. Como se ha mencionado en [Patrón de datos de referencia para la alta disponibilidad](./resiliency-high-availability-azure-applications.md#reference-data-pattern-for-high-availability), puede implementar datos de referencia al propio rol, a un almacenamiento externo o una combinación de ambos.

El modelo de implementación de datos de referencia en los nodos de proceso cumple implícitamente los requisitos de recuperación ante desastres. La implementación de datos de referencia en Base de datos SQL requiere que se implemente una copia de los datos de referencia en cada región. La misma estrategia se aplica a Almacenamiento de Azure. Debe implementar una copia de todos los datos de referencia almacenados en Almacenamiento de Azure en las regiones principal y secundaria.

![Publicación de datos de referencia en las regiones principal y secundaria](./media/resiliency-disaster-recovery-azure-applications/reference-data-publication-to-both-primary-and-secondary-regions.png)

Debe implementar sus propias rutinas de copia de seguridad específicas de la aplicación para todos los datos, incluidos los datos de referencia. Las copias con replicas geográficas entre regiones solo se utilizan en una interrupción del servicio en toda una región. Para evitar un tiempo de inactividad prolongado, implemente las partes críticas de los datos de la aplicación en la región secundaria. Para obtener un ejemplo de esta topología, consulte el [modelo activo-pasivo](#active-passive).

###Patrón de datos transaccionales para la recuperación ante desastres

La implementación de una estrategia del modo de desastres totalmente funcional requiere la replicación asincrónica de los datos transaccionales en la región secundaria. Las ventanas de tiempo prácticas en las que puede producirse la replicación determinarán las características RPO de la aplicación. En la ventana de la replicación se pueden recuperar los datos perdidos de la región principal. También puede combinarlos con los de la región secundaria más adelante.

Los siguientes ejemplos de arquitectura proporcionan algunas ideas de distintas formas de controlar los datos transaccionales en un escenario de conmutación por error. Es importante tener en cuenta que estos ejemplos no son exhaustivos. Por ejemplo, las ubicaciones de almacenamiento intermedias, como las colas, pueden reemplazarse por Base de datos SQL de Azure. Las propias colas pueden ser colas de Almacenamiento de Azure o del Bus de servicio (consulte [Colas de Bus de servicio y colas de Azure: comparación y diferencias](../service-bus/service-bus-azure-and-service-bus-queues-compared-contrasted.md)). Los destinos de almacenamiento del servidor también pueden variar, como las tablas de Azure, en lugar de Base de datos SQL. Además, pueden insertarse roles de trabajo como intermediarios en varios pasos. Lo importante es no emular estas arquitecturas exactamente, sino considerar diversas alternativas en la recuperación de datos transaccionales y los módulos relacionados.

####Replicación de datos transaccionales en preparación para la recuperación ante desastres

Considere una aplicación que utiliza las colas de Almacenamiento de Azure para contener datos transaccionales. Esto permite que los roles de trabajo procesen dichos datos transaccionales en la base de datos de servidor en una arquitectura desacoplada. Esto requiere que las transacciones utilicen alguna forma de caché temporal si los roles de front-end requieren la consulta inmediata de dichos datos. En función del nivel de tolerancia de la pérdida de datos, puede elegir replicar las colas, la base de datos o todos los recursos de almacenamiento. Con solo la replicación de la base de datos, si la región principal deja de funcionar, puede recuperar los datos de las colas cuando dicha región vuelva a funcionar.

El siguiente diagrama muestra una arquitectura en la que la base de datos de servidor está sincronizada entre regiones.

![Replicación de datos transaccionales en preparación para la recuperación ante desastres](./media/resiliency-disaster-recovery-azure-applications/replicate-transactional-data-in-preparation-for-disaster-recovery.png)

El mayor desafío a la hora de implementar esta arquitectura es la estrategia de replicación entre regiones. El servicio SQL Data Sync de Azure permite este tipo de replicación. Sin embargo, dicho servicio existe solo en versión preliminar y no se recomienda en entornos de producción. Para más información, consulte [Información general: continuidad del negocio en la nube y recuperación ante desastres con la Base de datos SQL](../sql-database/sql-database-business-continuity.md). Para las aplicaciones de producción, debe invertir en una solución de terceros o crear su propia lógica de replicación en el código. En función de la arquitectura, la replicación puede ser bidireccional, que también es más compleja.

Una posible implementación podría hacer uso de la cola intermedia del ejemplo anterior. El rol de trabajo que procesa los datos en el destino de almacenamiento final puede realizar el cambio en las regiones principal y secundaria. Estas tareas no son triviales y una guía completa para el código de replicación está fuera del ámbito de este artículo. El punto importante es que una gran parte de su tiempo y pruebas deben centrarse en cómo replicar los datos en la región secundaria. Las pruebas y procesamientos adicionales pueden contribuir a garantizar que los procesos de recuperación y de conmutación por error controlen correctamente todas las posibles incoherencias de los datos o transacciones duplicadas.

>[AZURE.NOTE] La mayor parte de este documento se centra en Plataforma como servicio (PaaS). Sin embargo, las opciones de replicación y disponibilidad adicionales para las aplicaciones híbridas utilizan máquinas virtuales de Azure. Estas aplicaciones híbridas usan Infraestructura como servicio (IaaS) para hospedar SQL Server en máquinas virtuales de Azure. Esto permite enfoques tradicionales de disponibilidad en SQL Server, como grupos de disponibilidad AlwaysOn o trasvase de registros. Algunas técnicas, como AlwaysOn, solo funcionan entre instancias de SQL Server locales y máquinas virtuales de Azure. Para más información, consulte [Alta disponibilidad y recuperación ante desastres para SQL Server en máquinas virtuales de Azure](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md).

####Modo de aplicación degradada para la captura de transacciones

Considere una segunda arquitectura que opere en modo degradado. La aplicación de la región secundaria desactiva toda la funcionalidad, como informes, inteligencia empresarial (BI) o colas de drenaje. Solo acepta los tipos más importantes de flujos de trabajo transaccionales de acuerdo con los requisitos empresariales. El sistema captura las transacciones y las escribe en las colas. El sistema puede posponer el procesamiento de los datos durante la fase inicial de la interrupción del servicio. Si el sistema de la región principal se reactiva en la ventana de tiempo esperado, los roles de trabajo de la región principal pueden purgar las colas. Este proceso elimina la necesidad de combinar bases de datos. Si la interrupción del servicio de la región principal va más allá de la ventana tolerable, la aplicación puede empezar a procesar las colas.

En este escenario, la base de datos de la región secundaria contiene datos transaccionales incrementales que se deben combinar después de que la principal se reactive. El diagrama siguiente muestra esta estrategia para almacenar temporalmente datos transaccionales hasta que se restaure la región principal.

![Modo de aplicación degradada para la captura de transacciones](./media/resiliency-disaster-recovery-azure-applications/degraded-application-mode-for-transaction-capture.png)

Para más información acerca de las técnicas de administración de datos para aplicaciones de Azure resistentes, consulte [Failsafe: Guidance for Resilient Cloud Architectures](https://channel9.msdn.com/Series/FailSafe) (Failsafe: Guía para las arquitecturas resistentes en la nube).

##Topologías de implementación para recuperación ante desastres

Debe preparar aplicaciones críticas para la posibilidad de que se produzca una interrupción del servicio en toda una región. Para ello, es preciso incorporar al planeamiento operativo una estrategia de implementación en varias regiones.

Las implementaciones en varias regiones pueden implicar procesos profesionales de TI para publicar la aplicación y los datos de referencia en la región secundaria después de un desastre. Si la aplicación requiere una conmutación por error instantánea, el proceso de implementación puede implicar una configuración activa/activa o activa/pasiva. Este tipo de implementación tiene instancias existentes de la aplicación que se ejecutan en la región alternativa. Una herramienta de enrutamiento, como el Administrador de tráfico de Azure, proporciona servicios de equilibrio de carga a nivel de DNS. Puede detectar interrupciones del servicio y enrutar a los usuarios a distintas regiones cuando sea necesario.

Una parte de una correcta recuperación ante desastres de Azure es el diseño de dicha recuperación en la solución desde el principio. La nube proporciona opciones adicionales para recuperarse de errores durante un desastre que no pueden ofrecer los proveedores de hospedaje tradicionales. En concreto, permite asignar recursos de forma rápida y dinámica a otra región. Por lo tanto, no tendrá que pagar mucho por los recursos inactivos mientras espera que se produzca un error.

En las secciones siguientes se tratan las diferentes topologías de implementación existentes para la recuperación ante desastres. Normalmente, hay un aumento del costo o la complejidad si se desea mayor disponibilidad.

###Implementación en una sola región

Una implementación en una sola región no es realmente una topología de recuperación ante desastres, pero supone un contraste con respecto a las restantes arquitecturas. Las implementaciones en una sola región son comunes para las aplicaciones de Azure. Sin embargo, este tipo de implementaciones no suponen una verdadera competencia para un plan de recuperación ante desastres.

El siguiente diagrama muestra una aplicación que se ejecuta en una sola región de Azure. El Administrador de tráfico de Azure y el uso de dominios de error y de actualización aumentan la disponibilidad de la aplicación dentro de la región.

![Implementación en una sola región](./media/resiliency-disaster-recovery-azure-applications/single-region-deployment.png)

Aquí es evidente que la base de datos es un único punto de error. Aunque Azure replica los datos a través de distintos dominios de error de manera interna, todo esto se produce en la misma región. La aplicación no puede resistir un error catastrófico. Si la región deja de funcionar, todos los dominios de error dejan de hacerlo también, lo que incluye todas las instancias de servicio y los recursos de almacenamiento.

Debe idear un plan para implementar su aplicación en varias regiones, salvo en el caso de las aplicaciones menos críticas. Al plantearse qué topología de implementación va a utilizar, también debe tener en cuenta las restricciones de costo y el RTO.

Examinemos varios patrones concretos para admitir la conmutación por error en diferentes regiones. Todos estos ejemplos utilizan dos regiones para describir el proceso.

###Reimplementación en una región de Azure secundaria

En el patrón de reimplementación en una región secundaria, solo la región principal tiene aplicaciones y bases de datos en ejecución. La región secundaria no está configurada para una conmutación por error automática. Por consiguiente, si se produce un desastre, será preciso poner en marcha todas las partes del servicio en la nueva región. Esto incluye la carga de un servicio en la nube en Azure, la implementación del servicio en la nube, la restauración de los datos y el cambio del DNS para reenrutar el tráfico.

Aunque esta es la más asequible de todas las opciones para varias regiones, también es la que tiene las peores características de RTO. En este modelo, las copias de seguridad de base de datos y el paquete de servicio se almacenan localmente o en la instancia de Almacenamiento de blobs de Azure de la región secundaria. Sin embargo, antes de reanudar el funcionamiento es preciso implementar un nuevo servicio y restaurar los datos. Aunque se automatice totalmente la transferencia de datos desde el almacenamiento de copias de seguridad, la puesta en marcha del nuevo entorno de base de datos consume mucho tiempo. La parte más cara del proceso de restauración es el movimiento de datos desde el almacenamiento en disco de copias de seguridad a la base de datos vacía de la región secundaria. Sin embargo, esta operación debe realizarse para poner la base de datos nueva en estado operativo, ya que no se ha replicado.

Lo mejor es almacenar los paquetes de servicio en el Almacenamiento de blobs de la región secundaria. De esta forma se elimina la necesidad de cargar el paquete en Azure, que es lo que ocurre cuando la implementación se realiza desde una máquina de desarrollo local. Con los scripts de PowerShell es posible implementar rápidamente los paquetes de servicio en un servicio en la nube nuevo desde el Almacenamiento de blobs.

Esta opción solo es práctica para las aplicaciones no críticas que pueden tolerar un RTO elevado. Por ejemplo, puede funcionar en una aplicación que pueda estar inactiva durante varias horas, pero que deba volver en ejecución en menos de 24 horas.

![Reimplementación en una región de Azure secundaria](./media/resiliency-disaster-recovery-azure-applications/redeploy-to-a-secondary-azure-region.png)

###Activo-pasivo

El patrón activo-pasivo es la opción favorita de muchas empresas, ya que proporciona mejoras en el RTO con un aumento relativamente pequeño del costo, con respecto al patrón de reimplementación. En este escenario, de nuevo hay una región principal y una región secundaria de Azure. Todo el tráfico se dirige a la implementación activa de la región principal. La región secundaria está mejor preparada para la recuperación ante desastres, ya que la base de datos se ejecuta en ambas regiones. Además, hay un mecanismo de sincronización en vigor entre ellas. Este enfoque en espera puede implicar dos variaciones: un enfoque de solo la base de datos o una implementación completa en la región secundaria.

####Solo base de datos

En la primera variación del patrón activo-pasivo, la región principal es la única en que se implementa una aplicación de servicio en la nube. Sin embargo, a diferencia del patrón de reimplementación, ambas regiones se sincronizan con el contenido de la base de datos. Para más información, consulte la sección [Patrón de datos transaccionales para la recuperación ante desastres](#transactional-data-pattern-for-disaster-recovery). Cuando se produce un desastre, hay menos requisitos de activación. Inicie la aplicación de la región secundaria, cambie las cadenas de conexión a la base de datos nueva y cambiar las entradas DNS para reenrutar el tráfico.

Al igual que en el patrón de reimplementación, para agilizar la implementación, los paquetes de servicio deben haberse almacenado previamente en Almacenamiento de blobs de Azure en la región secundaria. A diferencia del patrón de reimplementación, no se incurre en la mayoría de la sobrecarga que requieren las operaciones de restauración de la base de datos. La base de datos está lista y en ejecución. Esto ahorra mucho tiempo, lo que hace que este sea el patrón de recuperación ante desastres más asequible. También es el modelo de recuperación ante desastres más usado.

![Activo-pasivo (solo base de datos)](./media/resiliency-disaster-recovery-azure-applications/active-passive-database-only.png)

####Réplica completa

En la segunda variación del patrón activo-pasivo, tanto la región principal como la secundaria tienen una implementación completa. Esta implementación incluye los servicios en la nube y una base de datos sincronizada. Sin embargo, la región principal es la única que controla activamente las solicitudes de red de los usuarios. La región secundaria no pasa a ser la activa hasta que la región principal experimenta una interrupción del servicio. En ese caso, todas las solicitudes de red nuevas se enrutan a la región secundaria. El Administrador de tráfico de Azure puede administrar esta conmutación por error de forma automática.

La conmutación por error se produce más rápidamente que la variación de solo la base de datos porque los servicios ya están implementados. Este patrón proporciona un RTO muy bajo. La región de conmutación por error secundaria debe estar lista para empezar a funcionar inmediatamente después que se produzca un error en la región principal.

Junto con un tiempo de respuesta menor, este patrón tiene la ventaja de preasignar e implementar servicios de copia de seguridad. No es preciso preocuparse de que una región no tenga el espacio suficiente para asignar nuevas instancias en caso de desastre. Esto es importante si la región secundaria de Azure está cerca de alcanzar su capacidad máxima. No hay ninguna garantía (contrato de nivel de servicio) de poder implementar al instante varios servicios en la nube en cualquier región.

Para reducir al máximo el tiempo de respuesta con este modelo, es preciso tener una escala similar (número de instancias de rol) en las regiones principal y secundaria. A pesar de las ventajas, es costoso pagar por instancias de proceso que no se utilizan, por lo que es posible que esta opción no sea la más prudente desde un punto de vista financiero. Por este motivo, es más habitual utilizar una versión ligeramente reducida de los servicios en la nube en la región secundaria. A partir de ahí, se podrá realizar rápidamente una conmutación por error y escalar horizontalmente la implementación secundaria, si fuera necesario. El proceso de conmutación por error se debe automatizar con el fin de que, una vez que no se pueda acceder a la región principal, active las instancias adicionales en función de la carga. Esto puede implicar el uso de un mecanismo de escalado automático, como los [conjuntos de escala de máquinas virtuales](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md).

El siguiente diagrama muestra el modelo en el que las regiones principal y secundaria contienen un servicio en la nube totalmente implementado en un patrón activo-pasivo.

![Activo-pasivo (réplica completa)](./media/resiliency-disaster-recovery-azure-applications/active-passive-full-replica.png)

###Activo-activo

A estas alturas, es probable que ya haya descubierto la evolución de los patrones: al reducir el RTO, aumentan los costos y la complejidad. La solución activo-activo rompe esta tendencia con respecto al costo.

En un patrón activo-activo, tanto los servicios en la nube como la base de datos están totalmente implementados en ambas regiones. A diferencia del modelo activo-pasivo, ambas regiones reciben tráfico de usuarios. Esta opción es la que tiene el menor tiempo de recuperación. Los servicios ya se han escalado para controlar una parte de la carga en cada región. El DNS ya está habilitado para usar la región secundaria. Hay mayor complejidad a la hora de determinar cómo enrutar los usuarios a la región adecuada. Se puede usar la programación round robin. Es más probable que determinados usuarios utilicen la región específica en la que reside la copia principal de sus datos.

En caso de conmutación por error, sencillamente deshabilite el DNS en la región principal. Esto enruta todo el tráfico a la región secundaria.

Incluso en este modelo, hay algunas variaciones. Por ejemplo, el siguiente diagrama muestra un modelo en el que la región principal posee la copia maestra de la base de datos. Los servicios en la nube de ambas regiones escriben en dicha base de datos principal. La implementación de la región secundaria puede leer tanto de la base de datos principal como de la replicada. En este ejemplo, la replicación es unidireccional.

![Activo-activo](./media/resiliency-disaster-recovery-azure-applications/active-active.png)

La arquitectura de activo-activo del diagrama anterior tiene una desventaja. La segunda región debe tener acceso a la base de datos de la primera región, ya que es ahí donde reside la copia maestra. El rendimiento disminuye considerablemente cuando se accede a los datos desde fuera de una región. En las llamadas a la base de datos entre regiones, es preciso considerar algún tipo de estrategia de procesamiento por lotes para mejorar el rendimiento de estas llamadas. Para más información, consulte [Uso del procesamiento por lotes para mejorar el rendimiento de las aplicaciones de Base de datos SQL](../sql-database/sql-database-use-batching-to-improve-performance.md).

Una arquitectura alternativa podría implicar que cada región accediera directamente a su propia base de datos. En este modelo, se necesita cierto tipo de replicación bidireccional para sincronizar las bases de datos en cada región.

En el patrón activo-activo, es posible que no se necesiten tantas instancias en la región principal como en el patrón activo-pasivo. Si en una arquitectura de activo-pasivo hay diez instancias en la región principal, es posible que solo se necesiten cinco en cada región en una arquitectura de activo-activo. Ahora ambas regiones comparten la carga. Esto puede suponer un ahorro de costos sobre el patrón activo-pasivo si se mantiene una espera semiactiva en la región pasiva con diez instancias en espera de conmutación por error.

Tenga en cuenta que hasta que se restaure la región principal, la región secundaria puede recibir una sobrecarga repentina de nuevos usuarios. Si había 10 000 usuarios en cada servidor al producirse la interrupción del servicio en la región principal, la región secundaria tiene que administrar repentinamente 20 000 usuarios. Las reglas de supervisión de la región secundaria deben detectar este incremento y duplicar las instancias de la región secundaria. Para más información, consulte la sección sobre [detección de errores](#failure-detection).

##Solución híbrida, local y en la nube

Una estrategia adicional para la recuperación ante desastres es diseñar una aplicación híbrida que se ejecute de manera local y en la nube. En función de la aplicación, la región principal puede estar en cualquier ubicación. Piense en las arquitecturas anteriores e imagine las regiones principal o secundaria como una ubicación local.

Estas arquitecturas híbridas plantean algunos desafíos. En primer lugar, la mayor parte de este artículo ha abordado patrones de arquitectura de PaaS. Las aplicaciones típicas de PaaS en Azure se basan en construcciones específicas de Azure como roles, servicios en la nube y el Administrador de tráfico. Para crear una solución local para este tipo de aplicación de PaaS se requeriría una arquitectura muy distinta, algo que puede que no sea factible desde una perspectiva de costos o administración.

Sin embargo, una solución híbrida para la recuperación ante desastres plantea menos desafíos para las arquitecturas tradicionales que simplemente se han movido a la nube. Esto sucede en las arquitecturas que utilizan IaaS. Las aplicaciones de IaaS utilizan máquinas virtuales en la nube que pueden tener equivalentes directos en el sistema local. También puede usar redes virtuales para conectar máquinas en la nube con recursos de red locales. Esto abre varias posibilidades que no son impensables con las aplicaciones solo PaaS. Por ejemplo, SQL Server puede sacar provecho de soluciones de recuperación ante desastres, como los grupos de disponibilidad AlwaysOn y la creación de reflejo de base de datos. Para más información, consulte [Alta disponibilidad y recuperación ante desastres para SQL Server en máquinas virtuales de Azure](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md).

Las soluciones de IaaS también proporcionan una ruta más sencilla para que las aplicaciones locales utilicen Azure como opción de conmutación por error. Puede haber una aplicación totalmente funcional en una región local existente. Sin embargo, ¿qué ocurre si faltan los recursos necesarios para mantener una región geográficamente independiente para la conmutación por error? Se pueden usar máquinas virtuales y redes virtuales para que la aplicación se ejecute en Azure. En tal caso, defina procesos que sincronicen los datos con la nube. En ese momento, la implementación de Azure se convierte en la región secundaria que se utilizará para la conmutación por error. La región principal sigue siendo la aplicación local. Para más información acerca de las arquitecturas de IaaS y sus funcionalidades, consulte la [documentación de las máquinas virtuales](https://azure.microsoft.com/documentation/services/virtual-machines/).

##Nube alternativa

Hay situaciones en que ni siquiera la solidez de Microsoft Cloud puede satisfacer las reglas de cumplimiento o directivas internas que requiere su organización. Incluso la mejor preparación y diseño para implementar sistemas de copia de seguridad durante un desastre no son suficientes si se produce una interrupción global del servicio de un proveedor de servicios en la nube.

Deseará comparar los requisitos de disponibilidad con el costo y la complejidad del aumento de disponibilidad. Realice un análisis de riesgos y defina el RTO y el RPO de la solución. Si la aplicación no puede tolerar ningún tiempo de inactividad, tendría sentido considerar la posibilidad de utilizar otra solución en la nube. A menos que todo Internet deje de funcionar simultáneamente, es posible que aún haya otra solución en la nube si no se pudiera acceder a Azure a nivel global.

Al igual que sucede en el escenario híbrido, las implementaciones de conmutación por error en las arquitecturas de recuperación ante desastres anteriores también pueden existir en otra solución en la nube. Los sitios de recuperación ante desastres en la nube alternativos solo deben utilizarse para aquellas soluciones cuyo RTO permita muy poco tiempo de inactividad, o ninguno. Tenga en cuenta que una solución que utilice un sitio de recuperación ante desastres fuera de Azure requerirá más trabajo de configuración, desarrollo, implementación y mantenimiento. También es más difícil implementar los procedimientos recomendados en una arquitectura entre nubes. Aunque las plataformas en la nube tienen conceptos de alto nivel similares, las API y las arquitecturas son diferentes.

Si decide dividir la recuperación ante desastres entre varias plataformas, tendría sentido incluir capas de abstracción en el diseño de la solución. Si lo hace, no será preciso que desarrolle y mantenga dos versiones diferentes de la misma aplicación para plataformas en la nube diferentes en caso de desastre. Como sucede en el escenario híbrido, el uso de Máquinas virtuales de Azure o el servicio Contenedor de Azure puede ser más sencillo en estos casos que el empleo de diseños de PaaS específicos de la nube.

##Automatización

Algunos de los patrones que acabamos de describir requieren una activación rápida de las implementaciones sin conexión, así como la restauración de determinadas partes de un sistema. La automatización, o scripting, admite la capacidad de activar recursos a petición e implementar soluciones rápidamente. En este documento, la automatización relacionada con la recuperación ante desastres equivale a [Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx), pero la [API de REST de administración de servicios](https://msdn.microsoft.com/library/azure/ee460799.aspx) también es una opción.

El desarrollo de scripts ayuda a administrar las parte de la recuperación ante desastres que Azure no controla de forma transparente. Esto tiene la ventaja de generar resultados coherentes en todo momento, lo que reduce al mínimo la posibilidad de errores humanos. Los scripts predefinidos de recuperación ante desastres también reducen el tiempo para necesario para recompilar un sistema, y las partes que lo constituyen, en medio de un desastre. No es buena idea intentar averiguar manualmente cómo restaurar su sitio mientras está inactivo y perder dinero a cada minuto.

Tras crear los scripts, pruébelos varias veces de principio a fin. Después de comprobar su funcionalidad básica, asegúrese de probarlos en una [simulación de desastre](#disaster-simulation). Esto ayuda a detectar defectos en los scripts o procesos.

Un procedimiento recomendado con automatización es crear un repositorio de scripts de PowerShell o de interfaz de la línea de comandos (CLI) para recuperación ante desastres de Azure. Márquelos y clasifíquelos claramente para facilitar su búsqueda. Designe una persona para que se encargue de administrar el repositorio y el control de versiones de los scripts. Documéntelos perfectamente con explicaciones de los parámetros y ejemplos de uso de los scripts. Asegúrese de que esta documentación está sincronizada con las implementaciones de Azure. Esto subraya la importancia de que haya una persona responsable de todas las partes del repositorio.

##Detección de errores

Para controlar correctamente los problemas de disponibilidad y recuperación ante desastres, es preciso ser capaz de detectar y diagnosticar los errores. Se debe realizar una supervisión avanzada tanto del servidor como de la implementación para poder saber rápidamente en qué momento dejan de funcionar repentinamente un sistema, o cualquiera de sus componentes. Una parte de este trabajo pueden realizarla las herramientas de supervisión que examinan el estado general del servicio en la nube y sus dependencias. Una herramienta de Microsoft es [System Center 2016](https://www.microsoft.com/es-ES/server-cloud/products/system-center-2016/). Hay herramientas de terceros que también pueden proporcionar funcionalidades de supervisión. La mayoría de las soluciones de supervisión principales hacen un seguimiento de los contadores de rendimiento clave y de la disponibilidad del servicio.

Aunque estas herramientas son vitales, no reemplazan la necesidad de planear la detección de errores ni la generación de informes en un servicio en la nube. Debe planear el uso correcto de Diagnósticos de Azure. Los contadores de rendimiento personalizados o las entradas del registro de eventos también pueden formar parte de la estrategia global. Todo esto proporciona más datos durante los errores y, en consecuencia, agiliza el diagnóstico del problema y la restauración de todas las capacidades. También proporciona métricas adicionales que las herramientas de supervisión pueden usar determinar el estado de cualquier aplicación. Para más información, consulte [Habilitación de diagnósticos de Azure en servicios en la nube de Azure](../cloud-services/cloud-services-dotnet-diagnostics.md). Para una explicación de cómo planear un "modelo de estado" global, consulte [Failsafe: Guidance for Resilient Cloud Architectures](https://channel9.msdn.com/Series/FailSafe) (Failsafe: Guía para las arquitecturas resistentes en la nube).

##Simulación de desastre

Las pruebas de simulación implican la creación de situaciones reales a pequeña escala en el lugar de trabajo real para observar cómo reaccionan los miembros del equipo. Las simulaciones también muestran la eficacia de la descripción de las soluciones del plan de recuperación. Las simulaciones se deben llevar a cabo de tal forma que los escenarios creados no interrumpan el negocio real, pero puedan considerarse situaciones reales.

Considere la posibilidad de incluir algún tipo de "panel de control" en la aplicación para simular manualmente problemas de disponibilidad. Por ejemplo, a través de una conmutación suave, desencadene excepciones de acceso a la base de datos en un módulo de pedidos, que hacen que no funcione correctamente. Se pueden adoptar otros enfoques ligeros similares para otros módulos en el nivel de interfaz de red.

La simulación resalta todos los problemas que no se hayan abordado correctamente. Los escenarios simulados deben ser completamente controlables, lo que significa que, aunque parezca que el plan de recuperación no funciona, debe ser posible devolver la situación a un estado normal sin provocar daños importantes. También es importante que informar a la dirección de alto nivel de cómo y cuándo se van a realizar los ejercicios de simulación. Este plan debe incluir información sobre el tiempo o los recursos que pueden quedar improductivos durante la prueba de simulación. Cuando someta el plan de recuperación ante desastres a una prueba, también es importante que defina cómo se medirá el éxito.

Hay otras técnicas que puede usar para probar los planes de recuperación ante desastres. Sin embargo, la mayor parte de ellas son solo versiones ligeramente modificadas de estas técnicas básicas. El motivo principal que subyace a estas pruebas es evaluar lo factible y viable que es el plan de recuperación. Las pruebas de recuperación ante desastres se centran en los detalles para detectar posibles fallos en el plan de recuperación básico.

##Pasos siguientes

Este artículo forma parte de una serie enfocada a la [recuperación ante desastres y la alta disponibilidad para aplicaciones creadas en Microsoft Azure](./resiliency-disaster-recovery-high-availability-azure-applications.md). El anterior artículo de esta serie es [Alta disponibilidad para aplicaciones creadas en Microsoft Azure](./resiliency-high-availability-azure-applications.md).

<!---HONumber=AcomDC_0824_2016-->