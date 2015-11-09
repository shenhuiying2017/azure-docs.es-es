<properties 
   pageTitle="Diseño de soluciones de nube para la recuperación ante desastres mediante la replicación geográfica de la Base de datos SQL"
   description="Aprenda a diseñar la solución de nube para recuperación ante desastres eligiendo el modelo correcto de conmutación por error."
   services="sql-database"
   documentationCenter="" 
   authors="anosov1960" 
   manager="jeffreyg" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="10/07/2015"
   ms.author="sashan"/>

# Diseño de aplicaciones de nube para la continuidad de negocio mediante replicación geográfica

Hay varias maneras en las que puede aprovechar las ventajas de la replicación geográfica en la base de datos SQL al diseñar su aplicación para la continuidad empresarial. Los factores que conforman el diseño incluyen la topología de implementación de aplicación, el contrato de nivel de servicio que tiene como objetivo, la latencia y los costos del tráfico. En este artículo observaremos los patrones comunes de aplicación y comentaremos las ventajas y desventajas de cada opción.

## Patrón de diseño 1: implementación activa-pasiva para la recuperación ante desastres con base de datos colocalizada

Esta opción es mejor para las aplicaciones con las siguientes características:

+ instancia activa de una única región de Azure
+ fuerte dependencia de acceso de lectura y escritura (RW) a los datos 
+ la conectividad entre regiones entre la lógica de aplicación y la base de datos no es aceptable debido al costo del tráfico y la latencia    

En este caso la topología de implementación de la aplicación está optimizada para el tratamiento de desastres regionales cuando se ven afectados todos los componentes de aplicación y es necesario conmutar por error como una unidad. Para la redundancia geográfica tanto la lógica de aplicación como la base de datos se replican en otra región, pero no se usan para la carga de trabajo de aplicación en las condiciones normales. La aplicación en la región secundaria debe configurarse para usar una cadena de conexión SQL a la base de datos secundaria. El Administrador de tráfico se configura para usar [el método de enrutamiento de conmutación por error](traffic-manager-configure-failover-load-balancing.md).

> [AZURE.NOTE] [Azure traffic manager](traffic-manager-overview.md) se usa en este artículo únicamente con fines ilustrativos. Puede usar cualquier solución de equilibrio de carga que admita el método de enrutamiento de conmutación por error.
 
Además de las instancias de la aplicación principal, debe considerar la implementación de una pequeña [aplicación de rol de trabajo](cloud-services-choose-me.md#tellmecs) que supervise la base de datos principal mediante la emisión de comandos periódicos T-SQL de solo lectura (RO). Puede usarlo para activar automáticamente la conmutación por error, para generar una alerta en la consola de administración de la aplicación, o para ambas acciones. Para asegurarse de que la supervisión no está afectada por las interrupciones que afecten a toda la región, debe implementar las instancias de la aplicación de supervisión en cada región y conectarlas a la base de datos en otra región, pero solo la instancia en la región secundaria tiene que estar activa.

> [AZURE.NOTE]Si está usando la [replicación geográfica activa](https://msdn.microsoft.com/library/azure/dn741339.aspx) puede tener ambas aplicaciones de supervisión activas y sondear la bases de datos principal y secundaria. Este último puede usarse para detectar un error en la región secundaria y alertar cuando la aplicación no está protegida.

El diagrama siguiente muestra esta configuración antes de una interrupción.

![En la Ilustración 1](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern1-1.png)

Después de una interrupción en la región principal, la aplicación de supervisión detecta que la base de datos principal no está accesible y registra una alerta. Dependiendo del contrato de nivel de servicios de su aplicación puede decidir cuántos sondeos de supervisión consecutivos deberán producir un error antes de que se declare una interrupción de la base de datos. Para lograr la conmutación por error coordinada de la base de datos y el extremo de la aplicación, debe hacer que la aplicación de supervisión realice los pasos siguientes:

1. [actualice el estado del extremo principal](https://msdn.microsoft.com/library/hh758250.aspx) para desencadenar la conmutación por error de extremo
2. llame a la base de datos secundaria para [iniciar la conmutación por error de base de datos](https://msdn.microsoft.com/library/azure/dn509573.aspx) 

Después de la conmutación por error, la aplicación procesará las solicitudes de usuario en la región secundaria, pero permanecerá colocalizada con la base de datos porque la base de datos principal se encuentra ahora en la región secundaria. Esto se ilustra en el diagrama siguiente. En todos los diagramas las líneas sólidas indican conexiones activas, mientras que las líneas de puntos indican conexiones suspendidas y las señales de stop indican activadores de acción.


![Ilustración 2](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern1-2.png)

Si se produce una interrupción en la región secundaria, se suspende el vínculo de replicación entre la base de datos principal y la secundaria, y la aplicación de supervisión registrará una alerta de que la base de datos principal está expuesta. Esto no afectará al rendimiento de la aplicación pero funcionará expuesta y, por tanto, con un riesgo más alto en caso de que ambas regiones tengan un error en sucesión.

> [AZURE.NOTE]Solo se recomienda las configuraciones de implementación con una sola región de recuperación ante desastres. Esto es porque la mayoría de las ubicaciones geográficas de Azure tienen dos regiones. Estas configuraciones no protegerán la aplicación de un error grave de ambas regiones. En el caso poco probable de que este error se produjese, puede recuperar sus bases de datos en una tercera región mediante una [operación de restauración geográfica](sql-database-disaster-recovery.md#recovery-using-geo-restore).
 
Una vez que se reduce la interrupción, la base de datos secundaria se sincronizará automáticamente con la principal. Durante la sincronización el rendimiento de la principal podría verse afectado ligeramente dependiendo de la cantidad de datos que haya que sincronizar. El siguiente diagrama ilustra una interrupción en la región secundaria.

![Ilustración 3](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern1-3.png)


Las **ventajas** clave de este patrón de diseño son:

+ la cadena de conexión de SQL se configura durante la implementación de aplicaciones en cada región y no cambia después de la conmutación por error
+ el rendimiento de la aplicación no se ve afectado por la conmutación por error ya que la aplicación y la base de datos siempre están colocalizadas, es decir tienen una ubicación conjunta 

El principal **inconveniente** es que la instancia de aplicación redundante en la región secundaria solo se usa para la recuperación ante desastres.

## Patrón de diseño 2: implementación activa-activa para el equilibrio de carga de aplicación
Esta opción es mejor para las aplicaciones con las siguientes características:

+ proporción alta de lecturas en relación a las escrituras en la base de datos
+ la latencia de escritura de la base de datos no afecta a la experiencia del usuario final  
+ la lógica de solo lectura se puede separar de la lógica de lectura y escritura mediante el uso de una cadena de conexión distinta 
+ la lógica de solo lectura no dependen de que los datos estén totalmente sincronizados con las actualizaciones más recientes  

Si sus aplicaciones tienen estas características, el equilibrio de carga de las conexiones de usuario final en varias instancias de la aplicación en diferentes regiones puede mejorar el rendimiento y la experiencia del usuario final. Para lograr que cada región tenga una instancia activa de la aplicación con la lógica de lectura y escritura (RW) conectada a la base de datos principal en la región principal. La lógica de solo lectura (RO) debe estar conectada a una base de datos secundaria en la misma región que la instancia de aplicación. El administrador de tráfico debe configurarse para usar [enrutamiento Round Robin](traffic-manager-configure-round-robin-load-balancing.md) o [enrutamiento del rendimiento](traffic-manager-configure-performance-load-balancing.md) con [la supervisión de extremo](traffic-manager-monitoring.md) habilitada para cada instancia de la aplicación.

Como en el patrón nº 1, debe considerar la posibilidad de implementar una aplicación de supervisión similar. Pero a diferencia del patrón nº 1 no será responsable de desencadenar la conmutación por error de extremo.

> [AZURE.NOTE]Mientras que este patrón usa más de una base de datos secundaria, solo uno de las secundarias se usará para la conmutación por error por los motivos que se indicaron anteriormente. Puesto que este patrón requiere acceso de solo lectura a la base de datos secundaria, necesita [replicación geográfica activa](https://msdn.microsoft.com/library/azure/dn741339.aspx).

El Administrador de tráfico tiene que configurarse para el enrutamiento de rendimiento para dirigir las conexiones de usuario a la instancia de aplicación que esté más cerca de la ubicación geográfica del usuario. El diagrama siguiente muestra esta configuración antes de una interrupción.![Ilustración 4](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern2-1.png)

Si se detecta una interrupción de la base de datos en la región principal se inicia la conmutación por error de la base de datos principal a una de las regiones secundarias, lo que cambiará la ubicación de la base de datos principal. El Administrador de tráfico excluirá automáticamente el extremo sin conexión de la tabla de enrutamiento, pero continuará el enrutamiento del tráfico de usuario final a las instancias restantes en línea. Dado que la base de datos principal está ahora en una región distinta, todas las instancias en línea tienen que cambiar su cadena de conexión de SQL de lectura y escritura para conectarse con el nuevo elemento principal. Es importante que realice este cambio antes de iniciar la conmutación por error de la base de datos. Las cadenas de conexión de SQL de solo lectura deben permanecer sin cambios ya que siempre señalan a la base de datos en la misma región. Los pasos de conmutación por error son:

1. cambie las cadenas de conexión de SQL de lectura y escritura para que apunte al nuevo elemento principal
2. llame a la base de datos secundaria designada para [iniciar la conmutación por error de la base de datos](https://msdn.microsoft.com/library/azure/dn509573.aspx) 

El siguiente diagrama ilustra la nueva configuración después de la conmutación por error. ![Ilustración 5.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern2-2.png)

En el caso de una interrupción en una de las regiones secundarias, el Administrador de tráfico quitará automáticamente de la tabla de enrutamiento el extremo sin conexión en esa región. Se suspenderá el canal de replicación para la base de datos secundaria en dicha región. Dado que el resto de las regiones recibirán tráfico de usuario adicional, el rendimiento de la aplicación puede verse afectado durante la interrupción. Una vez que se reduce la interrupción, la base de datos secundaria en la región impactada se sincronizará automáticamente con la principal. Durante la sincronización el rendimiento de la principal podría verse afectado ligeramente dependiendo de la cantidad de datos que haya que sincronizar. El siguiente diagrama ilustra una interrupción en una de las regiones secundarias.

![Ilustración 6.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern2-3.png)

La **ventaja** clave de este patrón de diseño, es que se puede escalar la carga de trabajo de la aplicación a través de varios elementos secundarios para lograr el rendimiento óptimo del usuario final. Los **inconvenientes** de esta opción son:

+ las conexiones de lectura y escritura entre las instancias de la aplicación y la base de datos varían en latencia y costo
+ el rendimiento de la aplicación se ve afectado durante la interrupción
+ las instancias de aplicación son necesarias para cambiar dinámicamente la cadena de conexión de SQL después de la conmutación por error de la base de datos.  

> [AZURE.NOTE]Un enfoque similar puede usarse para descargar las cargas de trabajo especializadas, como trabajos de elaboración de informes, herramientas de inteligencia empresarial o copias de seguridad. Normalmente estas cargas de trabajo consumen una cantidad de recursos importante de la base de datos, por tanto, se recomienda designar una de las bases de datos secundarias para ellas que tenga un nivel de rendimiento que coincide con la carga de trabajo anticipada.

## Patrón de diseño 3: implementación activa-pasiva para la conservación de datos  
Esta opción es mejor para las aplicaciones con las siguientes características:

+ cualquier pérdida de datos supone un gran riesgo para la empresa, la conmutación por error de la base de datos solo puede usarse como último recurso.
+ la aplicación puede funcionar en "modo de solo lectura" durante un período de tiempo 

En este patrón de la aplicación cambia al modo de solo lectura cuando se conecta a la base de datos secundaria. La lógica de aplicación en la región principal está colocalizada con la base de datos principal y funciona en modo de lectura y escritura (RW), la lógica de aplicación en la región secundaria comparte ubicación con la base de datos secundaria y está lista para funcionar en modo de solo lectura (RO). El Administrador de tráfico debe configurarse para usar el [enrutamiento de conmutación por error](traffic-manager-configure-failover-load-balancing.md) con la [supervisión del extremo](traffic-manager-monitoring.md) habilitada para ambas instancias de aplicación.

El siguiente diagrama muestra esta configuración antes de una interrupción.![Ilustración 7.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern3-1.png)

Cuando el Administrador de tráfico detecta un error de conectividad en la región principal cambiará automáticamente el tráfico de usuario a la instancia de aplicación en la región secundaria. Con este patrón, es importante que **no** inicie la conmutación por error de la base de datos tras la detección de la interrupción. La aplicación de la región secundaria está activada y funciona en modo de solo lectura con la base de datos secundaria. Esto se ilustra en el diagrama siguiente

![Ilustración 8.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern3-2.png)

Una vez que se reduce la interrupción de la región principal, el Administrador de tráfico detectará la restauración de conectividad en la región principal y cambiará el tráfico de usuario a la instancia de aplicación en la región principal. Esa instancia de aplicación se reanuda y funciona en modo de lectura y escritura con la base de datos principal.

> [AZURE.NOTE]Puesto que este patrón requiere un acceso de solo lectura a la base de datos secundaria, necesitará la [replicación geográfica activa](https://msdn.microsoft.com/library/azure/dn741339.aspx).

En el caso de una interrupción en la región secundaria el Administrador de tráfico marcará el extremo de la aplicación en la región principal como degradado y se suspenderá el canal de replicación. Sin embargo no afectará al rendimiento de la apliación durante la interrupción. Una vez que se reduce la interrupción, la base de datos secundaria se sincronizará inmediatamente con la principal. Durante la sincronización el rendimiento de la principal podría verse afectado ligeramente dependiendo de la cantidad de datos que haya que sincronizar.

![Ilustración 8.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern3-3.png)

Este patrón de diseño tiene varias **ventajas**:

+ evita la pérdida de datos durante las interrupciones temporales
+ no requiere la implementación de una aplicación de supervisión ya que es el Administrador de tráfico el que desencadena la recuperación. 
+ el tiempo de inactividad solo depende de la rapidez del Administrador de tráfico en detectar el error de conectividad, que se puede configurar.

Los **inconvenientes** son:

+ la aplicación tiene que poder operar en modo de solo lectura
+ es necesario cambiar dinámicamente a este modo cuando la conexión es a una base de datos de solo lectura
+ la reanudación de las operaciones de lectura y escritura requieren la recuperación de la región principal, lo que significa que el acceso completo a los datos puede estar deshabilitado durante horas o incluso días. 

> [AZURE.NOTE]En el caso de una interrupción del servicio permanente en la región, tendrá que activar manualmente la conmutación por error de base de datos y aceptar la pérdida de datos. La aplicación será funcional en la región secundaria con acceso de lectura y escritura a la base de datos.

## Resumen

Su estrategia específica de recuperación ante desastres puede combinar o ampliar estos patrones para satisfacer mejor las necesidades de su aplicación. Como se mencionó anteriormente, la estrategia que elija se ha de basar en el contrato de nivel de servicios que desee ofrecer a sus clientes y en la topología de implementación de la aplicación. Para guiarle en su decisión, la siguiente tabla compara las opciones en función de la pérdida de datos estimada u objetivo de punto de recuperación (RPO) y el tiempo de recuperación calculado (ERT).

| Patrón | RPO | ERT 
| :--- |:--- | :--- 
| Implementación activa-pasiva para la recuperación ante desastres con acceso a base de datos colocalizada | Acceso de lectura y escritura < 5 s | Tiempo de detección de errores + llamada de API de conmutación por error + prueba de verificación de aplicación 
| Implementación activa-activa para el equilibrio de carga de aplicación | Acceso de lectura y escritura < 5 s | Tiempo de detección de errores + llamada de API de conmutación por error + cadena de conexión SQL + cambio de prueba de verificación de aplicación
| Implementación activa-pasiva para la conservación de datos | Acceso de solo lectura < 5 s acceso de lectura y escritura = cero | Acceso de solo lectura = tiempo de detección de errores de conectividad + prueba de comprobación de la aplicación <br>Acceso de lectura y escritura = tiempo para mitigar la interrupción 

<!---HONumber=Nov15_HO1-->