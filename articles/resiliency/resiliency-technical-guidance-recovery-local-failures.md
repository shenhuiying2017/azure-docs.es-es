<properties
   pageTitle="Guía técnica sobre recuperación ante errores locales en Azure | Microsoft Azure"
   description="Artículo para entender y diseñar aplicaciones resistentes, con alta disponibilidad y con tolerancia a errores y para planear la recuperación ante desastres centrada en errores locales dentro de Azure."
   services=""
   documentationCenter="na"
   authors="adamglick"
   manager="hongfeig"
   editor=""/>

<tags
   ms.service="resiliency"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/13/2016"
   ms.author="patw;jroth;aglick"/>

#Guía técnica sobre resistencia en Azure: recuperación ante errores locales en Azure

Hay dos amenazas principales para la disponibilidad de las aplicaciones: los errores en los dispositivos, como la unidad y los servidores, y el agotamiento de los recursos críticos, tales como los procesos en condiciones de picos de carga. Azure ofrece una combinación de administración de recursos, elasticidad, equilibrio de carga y creación de particiones para permitir una alta disponibilidad en estas circunstancias. Algunas de estas características se realizan automáticamente para todos los servicios en la nube; sin embargo, en algunos casos el desarrollador de aplicaciones debe realizar algún trabajo adicional para beneficiarse de ellas.

##Servicios en la nube
Todos los servicios en la nube hospedados por Azure son colecciones de uno o varios roles web o de trabajo. Se pueden ejecutar una o más instancias de un rol determinado simultáneamente. El número de instancias se determina mediante la configuración. Las instancias de rol se supervisan y administran mediante un componente denominado controlador de tejido (FC). El controlador de tejido detecta y responde automáticamente ante los errores del hardware y del software.

  * Cada instancia de rol se ejecuta en su propia máquina virtual (VM) y se comunica con el controlador de tejido a través de un agente invitado (GA). El agente invitado recopila métricas de los recursos y del nodo, entre las que se incluye el uso, estado, registros, uso de recursos, excepciones y condiciones de error de la máquina virtual. El controlador de tejido consulta al agente invitado a intervalos configurables y reinicia la máquina virtual si este no puede responder.
  * En caso de error del hardware, el controlador de tejido asociado mueve todas las instancias de rol afectadas a un nuevo nodo de hardware y vuelve a configurar la red para distribuir el tráfico hacia allí.

Para beneficiarse de estas características, los desarrolladores deben garantizar que todos los roles de servicio impiden almacenar el estado en las instancias de rol. En su lugar, se debe acceder a todos los datos persistentes desde un almacenamiento duradero, como los servicios de Almacenamiento de Azure o Base de datos SQL de Azure. Esto permite que todos los roles controlen las solicitudes. También significa que las instancias de rol pueden dejar de funcionar en un momento dado sin que ello genere incoherencias en el estado transitorio o persistente del servicio.

El requisito para almacenar estados de forma externa a los roles tiene varias implicaciones. Implica, por ejemplo, que todos los cambios relacionados con una tabla de Almacenamiento de Azure deben cambiarse, si es posible, en una única transacción de grupos de entidades. Por supuesto, no siempre es posible realizar todos los cambios en una sola transacción. Debe tener especial cuidado para asegurarse de que los errores de instancias de rol no causen problemas cuando interrumpan operaciones de ejecución prolongada que abarcan dos o más actualizaciones del estado persistente del servicio. Si otro rol vuelve a intentar esta operación, debe anticiparse y controlar aquella situación en la que el trabajo se completó parcialmente.

Por ejemplo, en un servicio que crea particiones de datos en varios almacenes, si un rol de trabajo deja de funcionar durante la reubicación de una partición, esta reubicación puede que no se complete, o puede que un rol de trabajo diferente tenga que repetirla desde el principio, lo cual podría provocar datos huérfanos o dañados. Para evitar problemas, las operaciones de ejecución prolongada deben ser idempotentes (es decir, repetibles sin efectos secundarios) o reiniciables incrementalmente (es decir, podrán continuar desde el punto de error más reciente).

  * Para ser idempotente, una operación de ejecución prolongada debe tener el mismo efecto independientemente de cuántas veces se ejecuta, incluso si la ejecución se interrumpe.
  * Para ser reiniciable incrementalmente, una operación de ejecución prolongada debe constar de una secuencia de operaciones atómicas menores y registrar su progreso en un almacenamiento duradero, para que cada invocación posterior comience desde el punto en que se detuvo su predecesora.

Por último, se deben invocar varias veces las operaciones de ejecución prolongada hasta que tengan éxito. Por ejemplo, una operación de aprovisionamiento puede permanecer en una cola de Azure y un rol de trabajo podrá quitarla de allí solo cuando la operación se haya ejecutado correctamente. La recolección de elementos no utilizados podría ser necesaria para limpiar los datos creados por las operaciones interrumpidas.

###Elasticidad
El número inicial de instancias que se ejecutan para cada rol se determina en la configuración del mismo. Los administradores deben configurar inicialmente cada uno de los roles para que se ejecuten con dos o más instancias según la carga esperada. Pero las instancias de rol pueden escalarse fácilmente de forma vertical u horizontal a medida que cambian los patrones de uso. Esto puede hacerse manualmente en el Portal de Azure o puede automatizarse mediante Windows PowerShell, Service Management API o herramientas de terceros. Aquí puede encontrar información sobre [Cómo escalar automáticamente un servicio en la nube](../cloud-services/cloud-services-how-to-scale.md).

###Creación de particiones
El controlador de tejido de Azure usa dos tipos de particiones: los dominios de actualización y los dominios de error.

  * Un dominio de actualización se utiliza para actualizar instancias de rol de un servicio en grupos. Azure implementa las instancias de servicio en varios dominios de actualización. Para obtener una actualización en contexto, el controlador de tejido desactiva todas las instancias en un dominio de actualización, las actualiza y, a continuación, las reinicia antes de pasar al siguiente dominio de actualización. Este método evita que todo el servicio se quede sin disponibilidad durante el proceso de actualización.
  * Un dominio de error define los posibles puntos de error de hardware o de red. Para todos aquellos roles con más de una instancia, el controlador de tejido garantiza que estas instancias se distribuyan entre varios dominios de error, para evitar que errores aislados de hardware interrumpan el servicio. Toda exposición a errores en el servidor y en el clúster en Azure se rige por los dominios de error.

Según el [Acuerdo de Nivel de Servicio de Azure](https://azure.microsoft.com/support/legal/sla/), Microsoft garantiza que, si se implementan dos o más instancias de rol web en distintos dominios de error y de actualización, tendrán conectividad externa al menos un 99,95 % de las veces. A diferencia de los dominios de actualización, no hay ninguna manera de controlar el número de dominios de error. Azure asigna automáticamente los dominios de error y distribuye las instancias de rol entre ellos. Al menos las dos primeras instancias de cada rol se colocan en diferentes dominios de error y de actualización para garantizar que cualquier rol con al menos dos instancias cumpla el Acuerdo de Nivel de Servicio. Esto se representa en el diagrama siguiente.

![Aislamiento de los dominios de error (vista simplificada)](./media/resiliency-technical-guidance-recovery-local-failures/partitioning-1.png "Aislamiento de los dominios de error: vista simplificada")

###Equilibrio de carga
Todo el tráfico entrante a un rol web pasa a través de un equilibrador de carga sin estado, que distribuye las solicitudes de clientes entre las instancias de rol. Las instancias de rol individuales no tienen direcciones IP públicas y no son directamente direccionables desde Internet. Los roles web no tienen estado, por lo que se pueden enrutar las solicitudes de clientes a cualquier instancia de rol. Se produce un evento [StatusCheck](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.statuscheck.aspx) cada 15 segundos. Esto puede utilizarse para indicar si el rol está listo para recibir tráfico o si está ocupado y debe quitarse de la rotación del equilibrador de carga.

##Máquinas virtuales
Las Máquinas virtuales de Azure difieren de roles de proceso de PaaS en varios aspectos relacionados con la alta disponibilidad. En algunos casos, deberá realizar tareas adicionales para garantizar una alta disponibilidad.

###Durabilidad del disco
A diferencia de las instancias de rol de PaaS, los datos almacenados en unidades de máquinas virtuales son persistentes, incluso si se reasigna la máquina virtual. Las Máquinas virtuales de Azure usan discos de máquina virtual que existen como blobs en el Almacenamiento de Azure. Debido a las características de disponibilidad del Almacenamiento de Azure, los datos almacenados en unidades de una máquina virtual también presentan una alta disponibilidad. Tenga en cuenta que la unidad D: es la excepción a esta regla. La unidad D: es realmente un almacenamiento físico en el servidor en bastidor que hospeda la máquina virtual y sus datos se perderán si esta se recicla. La unidad D: solo está pensada como almacenamiento temporal.

###Creación de particiones
Azure entiende de manera nativa los niveles de una aplicación PaaS (rol web y rol de trabajo) y, por tanto, puede distribuirlos correctamente en dominios de error y de actualización. En cambio, los niveles de las aplicaciones IaaS se deben definir manualmente mediante conjuntos de disponibilidad. Los conjuntos de disponibilidad son necesarios para un Acuerdo de Nivel de Servicio en IaaS.

![Conjuntos de disponibilidad para Máquinas virtuales de Microsoft Azure](./media/resiliency-technical-guidance-recovery-local-failures/partitioning-2.png "Conjuntos de disponibilidad para Máquinas virtuales de Microsoft Azure")

En el diagrama anterior, el nivel de Internet Information Server (IIS) (que funciona como nivel de aplicación web) y el nivel de SQL (que funciona como nivel de datos), se asignan a distintos conjuntos de disponibilidad. Esto garantiza que todas las instancias de cada nivel tienen redundancia de hardware mediante su distribución entre los dominios de error y que estas no se quitarán durante una actualización.

###Equilibrio de carga
Si hay que distribuir el tráfico entre las máquinas virtuales, tendrá que agruparlas en un servicio en la nube y equilibrar la carga mediante un punto de conexión TCP o UDP específico. Para obtener más información, consulte [Equilibrio de carga de máquinas virtuales](../virtual-machines/virtual-machines-linux-load-balance.md). Si las máquinas virtuales reciben entradas de otro origen como, por ejemplo, un mecanismo de cola, no será necesario un equilibrador de carga. El equilibrador de carga utiliza una comprobación básica de estado para determinar si se debe enviar tráfico al nodo. También puede crear sus propios sondeos para implementar métricas de estado específicas para la aplicación que determinen si la máquina virtual debe recibir tráfico.

##Almacenamiento
Almacenamiento de Azure es el servicio de datos duradero más importante en Azure, que proporciona almacenamiento de blobs, tablas, colas y de disco de máquina virtual. Utiliza una combinación de replicación y administración de recursos para proporcionar alta disponibilidad en un solo centro de datos. El Acuerdo de Nivel de Servicio del Almacenamiento de Azure garantiza que al menos durante el 99,9 % del tiempo, las solicitudes correctamente formateadas para agregar, actualizar, leer y eliminar datos se procesarán satisfactoriamente y que las cuentas de almacenamiento tendrán conectividad a la puerta de enlace de Internet.

###Replicación
La durabilidad de los datos de Almacenamiento de Azure se logra manteniendo varias copias de todos los datos en diferentes unidades ubicadas en subsistemas de almacenamiento físico totalmente independientes dentro de la región. Los datos se replican de forma sincrónica y todas las copias se confirman antes de completar la operación de escritura. Almacenamiento de Azure es muy coherente, lo cual significa que se garantiza que las operaciones de lectura reflejan las operaciones de escritura más recientes. Además, las copias de los datos se examinan continuamente para detectar y reparar la degradación de bits, una amenaza para la integridad de los datos almacenados que a menudo se pasa por alto. Los servicios se pueden beneficiar de la replicación solo con usar el Almacenamiento de Azure. El desarrollador del servicio no necesita realizar ningún trabajo adicional para la recuperación ante un error local.

###Administración de recursos
Las cuentas de almacenamiento creadas después del 7 de junio de 2012 pueden crecer hasta 200 TB (el máximo anterior era de 100 TB). Si se necesita espacio adicional, las aplicaciones se deberán diseñar para aprovechar varias cuentas de almacenamiento.

###Discos de máquinas virtuales
Un disco de máquina virtual se almacena como un blob en páginas en el Almacenamiento de Azure, lo cual le otorga las mismas propiedades de durabilidad y escalabilidad que al almacenamiento de blobs. Este diseño hace que los datos de un disco de máquina virtual sean persistentes incluso si se produce un error en el servidor que ejecuta la máquina virtual y se debe reiniciar dicha máquina en otro servidor.

##Base de datos

###Base de datos SQL
Base de datos SQL de Microsoft Azure proporciona una base datos como servicio, lo cual permite a las aplicaciones aprovisionar, insertar datos y realizar consultas rápidamente en las bases de datos relacionales. Proporciona muchas de las conocidas características y funcionalidades de SQL Server, al tiempo que reduce la carga de hardware, la configuración, la aplicación de revisiones y la resistencia.

>[AZURE.NOTE]Base de datos SQL de Azure no proporciona una paridad exacta de características con SQL Server y está pensada para satisfacer un conjunto diferente de requisitos adecuados exclusivamente a aplicaciones en la nube (escala elástica, base de datos como servicio para reducir costos de mantenimiento, etc). Para más información, consulte [Selección de una opción de SQL Server en la nube: Base de datos (PaaS) SQL de Azure o SQL Server en máquinas virtuales de Azure (IaaS)](../sql-database/data-management-azure-sql-database-and-sql-server-iaas.md).

####Replicación
Base de datos SQL de Azure proporciona resistencia integrada a errores en el nivel de nodo. Todas las operaciones de escritura en una base de datos se replican automáticamente en dos o más nodos en segundo plano mediante una técnica de confirmación de cuórum (el principal y al menos uno de los secundarios deben confirmar que la actividad se ha escrito en el registro de transacciones antes de que la transacción se considere correcta y se devuelva). En caso de error del nodo, la base de datos automáticamente conmuta por error a una de las réplicas secundarias. Esto produce una interrupción de la conexión transitoria para las aplicaciones cliente. Por este motivo, todos los clientes de Base de datos SQL de Microsoft Azure deben implementar alguna forma de control de conexiones transitorias. Para más información, consulte [Using the Transient Fault Handling Application Block with SQL Azure](https://msdn.microsoft.com/library/hh680899.aspx) (Uso del bloque de aplicaciones de control de errores transitorios con SQL Azure).

####Administración de recursos
Cada base de datos, cuando se crea, se configura con un límite de tamaño superior. El tamaño máximo actualmente disponible es de 150 GB. Cuando una base de datos alcanza el límite superior de tamaño, rechaza comandos INSERT o UPDATE adicionales (la consulta y eliminación de datos aún es posible).

Base de datos SQL de Microsoft Azure usa un tejido para administrar los recursos dentro de una base de datos. No obstante, en lugar de un controlador de tejido, usa una topología en anillo para detectar errores. Cada réplica de un clúster tiene dos vecinos y es responsable de detectar cuándo estos se bloquean. Cuando una réplica deja de funcionar, sus vecinos desencadenan un agente de reconfiguración (RA) para volver a crearla en otro equipo. Se proporciona limitación del motor para garantizar que un servidor lógico no usará demasiados recursos de un equipo ni superará los límites físicos del mismo.

###Elasticidad
Si la aplicación requiere más de los 150 GB de límite de la base de datos, deberá implementar un enfoque de escalado horizontal. El escalado horizontal con Base de datos SQL de Microsoft Azure se realiza mediante la creación manual de particiones, también llamada particionamiento, de los datos entre varias bases de datos SQL de Azure. Este enfoque de escalado horizontal ofrece la oportunidad de conseguir un crecimiento del costo casi lineal de la escala. El crecimiento elástico o la capacidad a petición pueden aumentar según sea necesario, lo cual implicará un aumento de los costos, ya que las bases de datos se facturan según el tamaño real promedio usado cada día, y no según el tamaño máximo posible.

##SQL Server en máquinas virtuales
Si instala SQL Server en Máquinas virtuales de Azure (versión 2014 o posterior), podrá aprovecharse de las características de disponibilidad tradicionales de SQL Server, como los grupos de disponibilidad AlwaysOn o la creación de reflejo de base de datos. Tenga en cuenta que las máquinas virtuales de Azure, el almacenamiento y las redes tienen características operativas diferentes de las de una infraestructura TI local y no virtualizada. Para implementar correctamente una solución de alta disponibilidad y recuperación ante desastres (HA/DR) de SQL Server en Azure es necesario conocer estas diferencias y diseñar la solución que mejor se adapta a ellas.

###Nodos de alta disponibilidad en un conjunto de disponibilidad
Cuando implementa una solución de alta disponibilidad, el conjunto de disponibilidad de Azure le permite colocar los nodos de alta disponibilidad en dominios de error y de actualización independientes. Para ser más precisos, "conjunto de disponibilidad" es un concepto de Azure. Se trata de un procedimiento recomendado que debe seguir para asegurarse de que las bases de datos son realmente de alta disponibilidad, tanto si utiliza grupos de disponibilidad AlwaysOn o creación de reflejo de base de datos, como si no lo hace. Si no sigue esta recomendación, es posible que crea erróneamente que el sistema tiene una alta disponibilidad, pero, en realidad, todos los nodos pueden dar error simultáneamente si están colocados en el mismo dominio de error del centro de datos de Azure. Esta recomendación no es aplicable al trasvase de registros, ya que, al tratarse de una característica de recuperación ante desastres, debe asegurarse de que los servidores se ejecutan en ubicaciones de centros de datos de Azure independientes (regiones). Por definición, estas ubicaciones de centros de datos son dominios de error independientes.

Para que las máquinas virtuales de Azure se coloquen en el mismo conjunto de disponibilidad, debe implementarlas en el mismo servicio en la nube. Tenga en cuenta que solo las máquinas virtuales del mismo servicio en la nube puede participar en el mismo conjunto de disponibilidad. Además, las máquinas virtuales deben estar en la misma red virtual para asegurarse de que conservan sus direcciones IP incluso después de la recuperación del servicio, lo cual evita los tiempos de actualización de DNS.

###Exclusiva de Azure: soluciones de alta disponibilidad
Puede tener una solución de alta disponibilidad para sus bases de datos de SQL Server en Azure con grupos de disponibilidad AlwaysOn o creación de reflejo de la base de datos.

El siguiente diagrama muestra la arquitectura de los grupos de disponibilidad AlwaysOn que se ejecutan en Máquinas virtuales de Azure. Este diagrama se realizó desde el artículo en el que se analizó en profundidad este tema, titulado [Alta disponibilidad y recuperación ante desastres para SQL Server en máquinas virtuales de Azure](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md).

![Grupos de disponibilidad de AlwaysOn en Microsoft Azure](./media/resiliency-technical-guidance-recovery-local-failures/high_availability_solutions-1.png "Grupos de disponibilidad de AlwaysOn en Microsoft Azure")

También puede aprovisionar automáticamente una implementación de grupos de disponibilidad AlwaysOn de principio a fin en máquinas virtuales de Azure mediante la plantilla de AlwaysOn del Portal de Microsoft Azure. Para obtener más información, consulte [Oferta de AlwaysOn de SQL Server en la Galería del Portal de Microsoft Azure](https://blogs.technet.microsoft.com/dataplatforminsider/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery/).

El diagrama siguiente muestra el uso de creación de reflejo de base de datos en Máquinas virtuales de Azure. La información también se extrajo del artículo [Alta disponibilidad y recuperación ante desastres para SQL Server en máquinas virtuales de Azure](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md).

![Creación de reflejo de base de datos en Microsoft Azure](./media/resiliency-technical-guidance-recovery-local-failures/high_availability_solutions-2.png "Creación de reflejo de base de datos en Microsoft Azure")

>[AZURE.NOTE]En ambas arquitecturas se requiere un controlador de dominio. Sin embargo, con la creación de reflejo de base de datos es posible utilizar certificados de servidor para eliminar la necesidad de un controlador de dominio.

##Otros servicios de la plataforma de Azure
Los Servicios en la nube de Azure están integrados en Azure, por lo que se benefician de las funcionalidades de la plataforma anteriormente descritas para recuperarse ante errores locales. En algunos casos, hay determinadas acciones que puede tomar para aumentar la disponibilidad para su escenario concreto.

###Bus de servicio
Para mitigar una interrupción temporal del Bus de servicio de Azure, considere la posibilidad de crear una cola duradera en el lado de cliente. Con ello, utilizará temporalmente un mecanismo de almacenamiento local alternativo para almacenar los mensajes que no se puedan agregar a la cola del Bus de servicio. La aplicación puede decidir cómo controlar los mensajes almacenados temporalmente una vez restaurado el servicio. Para más información, consulte [Procedimientos recomendados para mejorar el rendimiento mediante la mensajería asincrónica del Bus de servicio](../service-bus/service-bus-performance-improvements.md). Para más información, consulte la sección [Bus de servicio (recuperación ante desastres)](./resiliency-technical-guidance-recovery-loss-azure-region.md#service-bus).

###Servicios móviles
Hay dos consideraciones que debe tener en cuenta sobre la disponibilidad de los Servicios móviles de Azure. En primer lugar, realice copias de seguridad de la Base de datos SQL de Azure asociada con el servicio móvil. Realice también una copia de seguridad de los scripts del servicio móvil. Para más información, consulte [Recuperación del servicio móvil en caso de un desastre](../mobile-services/mobile-services-disaster-recovery.md). Si los Servicios móviles experimentan una interrupción temporal, es posible que deba usar un centro de datos de Azure alternativo de forma transitoria. Para más información, consulte la sección [Servicios móviles (recuperación ante desastres)](./resiliency-technical-guidance-recovery-loss-azure-region.md#mobile-services).

###HDInsight
Los datos asociados a HDInsight se almacenan de forma predeterminada en el Almacenamiento de blobs de Azure, que tiene las propiedades de alta disponibilidad y durabilidad especificadas por el Almacenamiento de Azure. El procesamiento de varios nodos asociado a los trabajos de MapReduce de Hadoop se realiza en un sistema de archivos distribuido transitorio de Hadoop (HDFS) que se aprovisiona cuando es necesario mediante HDInsight. Los resultados de un trabajo de MapReduce también se almacenan de forma predeterminada en el Almacenamiento de blobs de Azure, de forma que los datos procesados sean duraderos y tengan una alta disponibilidad una vez desaprovisionado el clúster de Hadoop. Para más información, consulte la sección [HDInsight (recuperación ante desastres)](./resiliency-technical-guidance-recovery-loss-azure-region.md#hdinsight).

##Listas de comprobación: Errores locales
 
##Lista de comprobación de los Servicios en la nube
  1. Revise la sección [Servicios en la nube](#cloud-services) de este documento.
  2. Configure al menos dos instancias para cada rol.
  3. Conserve el estado en un almacenamiento durable, no en las instancias de rol
  4. Controle correctamente el evento StatusCheck.
  5. Ajuste los cambios relacionados en las transacciones cuando sea posible.
  6. Compruebe que las tareas de rol de trabajo son idempotentes y reiniciables.
  7. Continúe invocando las operaciones hasta que se completen satisfactoriamente.
  8. Considere estrategias de escalado automático.

##Lista de comprobación de máquinas virtuales
  1. Revise la sección [Máquinas virtuales](#virtual-machines) de este documento.
  2. No utilice la unidad D: para almacenamiento persistente.
  3. Agrupe las máquinas de un nivel de servicio en un conjunto de disponibilidad.
  4. Configure el equilibrio de carga y sondeos opcionales.
 
##Lista de comprobación de almacenamiento
  1. Revise la sección [Almacenamiento](#storage) de este documento.
  2. Utilice varias cuentas de almacenamiento cuando los datos o el ancho de banda superen las cuotas.

##Lista de comprobación de Base de datos SQL
  1. Revise la sección [Base de datos SQL](#sql-database) de este documento.
  2. Implemente una directiva de reintento para controlar los errores transitorios.
  3. Utilice el particionamiento o sharding como estrategia de escalado horizontal.
  
##Lista de comprobación de SQL Server en Máquinas virtuales
  1. Revise la sección [SQL Server en Máquinas virtuales](#sql-server-on-virtual-machines) de este documento.
  2. Siga las recomendaciones anteriores para máquinas virtuales.
  3. Use las características de alta disponibilidad de SQL Server, como AlwaysOn.
  
##Lista de comprobación del Bus de servicio
  1. Revise la sección [Bus de servicio](#service-bus) de este documento.
  2. Considere la creación de una cola duradera en el lado del cliente como una copia de seguridad.

##Lista de comprobación de HDInsight
  1. Revise la sección [HDInsight](#hdinsight) de este documento.
  2. No se requieren pasos adicionales de disponibilidad para errores locales.
 
##Pasos siguientes
Este artículo forma parte de una serie que se centra en la [Guía técnica sobre resistencia en Azure](./resiliency-technical-guidance.md). El siguiente artículo de esta serie se centra en la [recuperación ante una interrupción del servicio en toda la región](./resiliency-technical-guidance-recovery-loss-azure-region.md).

<!---HONumber=AcomDC_0525_2016-->