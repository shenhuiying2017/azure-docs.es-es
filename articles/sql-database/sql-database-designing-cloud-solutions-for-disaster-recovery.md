---
title: "Diseño de un servicio de alta disponibilidad con Azure SQL Database | Microsoft Docs"
description: "Obtenga información sobre el diseño de aplicaciones para servicios de alta disponibilidad con Azure SQL Database."
keywords: "recuperación ante desastres en la nube, soluciones de recuperación ante desastres, copia de seguridad de datos de aplicación, planificación de continuidad del negocio"
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
editor: monicar
ms.assetid: e8a346ac-dd08-41e7-9685-46cebca04582
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-management
ms.date: 04/21/2017
ms.author: sashan
ms.translationtype: Human Translation
ms.sourcegitcommit: 8f987d079b8658d591994ce678f4a09239270181
ms.openlocfilehash: 40fe0ae04eb94322356ed19773512e3bc383639c
ms.contentlocale: es-es
ms.lasthandoff: 05/18/2017


---
# <a name="designing-highly-available-services-using-azure-sql-database"></a>Diseño de servicios de alta disponibilidad con Azure SQL Database

Al compilar e implementar servicios de alta disponibilidad en Azure SQL Database, use los [grupos de conmutación por error y la replicación geográfica activa](sql-database-geo-replication-overview.md) para proporcionar resistencia frente a errores regionales e interrupciones graves, así como para habilitar la recuperación rápida en las bases de datos secundarias. Este artículo se centra en los patrones comunes de aplicaciones y trata las ventajas e inconvenientes de cada opción en función de los requisitos de implementación de las aplicaciones, el Acuerdo de Nivel de Servicio objetivo, la latencia de tráfico y los costos. Para saber cómo utilizar la replicación geográfica activa con los grupos elásticos, consulte [Estrategias de recuperación ante desastres para aplicaciones que usan el grupo elástico de Base de datos SQL](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).

## <a name="design-pattern-1-active-passive-deployment-for-cloud-disaster-recovery-with-a-co-located-database"></a>Patrón de diseño 1: implementación activa-pasiva para la recuperación ante desastres en la nube con una base de datos colocada
Esta opción es mejor para las aplicaciones con las siguientes características:

* Instancia activa de una única región de Azure.
* Fuerte dependencia de acceso de lectura y escritura (RW) a los datos.
* La conectividad entre regiones entre la aplicación web y la base de datos no es aceptable debido al costo del tráfico y la latencia.    

En este caso, la topología de implementación de la aplicación está optimizada para el tratamiento de desastres regionales cuando se ven afectados todos los componentes de aplicación y es necesario conmutar por error como una unidad. En el caso de la redundancia geográfica, la lógica de aplicación y la base de datos se replican en otra región, pero no se usan para la carga de trabajo de la aplicación en las condiciones normales. La aplicación en la región secundaria debe configurarse para usar una cadena de conexión SQL a la base de datos secundaria. El Administrador de tráfico se configura para usar [el método de enrutamiento de conmutación por error](../traffic-manager/traffic-manager-configure-failover-routing-method.md).  

> [!NOTE]
> [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) se usa en este artículo únicamente con fines ilustrativos. Puede usar cualquier solución de equilibrio de carga que admita el método de enrutamiento de conmutación por error.    
>

El diagrama siguiente muestra esta configuración antes de una interrupción.

![Configuración de replicación geográfica de SQL Database. Recuperación ante desastres en la nube.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern1-1.png)

Después de una interrupción en la región primaria, el servicio SQL Database detectará que no se puede acceder a la base de datos principal y desencadenará una conmutación por error a la base de datos secundaria en función de los parámetros de la directiva de conmutación por error automática. Dependiendo de su Acuerdo de Nivel de Servicio de aplicación, puede optar por configurar un período de gracia entre la detección de la interrupción y la propia conmutación por error. Al configurar un período de gracia se reduce el riesgo de pérdida de datos a los casos donde la interrupción es grave y la disponibilidad en la región no se puede restaurar rápidamente. Si el administrador de tráfico inicia la conmutación por error del punto de conexión antes de que el grupo de conmutación por error active la conmutación por error de la base de datos, la aplicación web no podrá volverse a conectarse a la base de datos. El intento de la aplicación para volverse a conectar se realiza de forma correcta automáticamente en cuanto la conmutación por error de la base de datos se complete. 

> [!NOTE]
> Para lograr una conmutación por error de la aplicación y las bases de datos totalmente coordinada, debe diseñar su propio método de supervisión y usar la conmutación por error manual de los puntos de conexión de la aplicación web y las bases de datos.
>

Una vez completada la conmutación por error de la base de datos y los puntos de conexión de la aplicación, esta reiniciará el procesamiento de las solicitudes de usuario en la región B y permanecerá colocada con la base de datos porque la base de datos principal está ahora en la región de B. Este escenario se ilustra en el diagrama siguiente. En todos los diagramas, las líneas continuas indican conexiones activas, las líneas de puntos indican conexiones suspendidas y las señales de detención indican desencadenadores de acción.

![Replicación geográfica: conmutación por error a la base de datos secundaria. Copia de seguridad de datos de la aplicación.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern1-2.png)

Si se produce una interrupción en la región secundaria, se suspende el vínculo de replicación entre la base de datos principal y la secundaria pero la conmutación por error no se desencadena porque la base de datos principal no se ve afectada. En este caso, la disponibilidad de la aplicación no cambia, pero la aplicación funciona expuesta y, por tanto, con un riesgo más alto en caso de que ambas regiones tengan un error en cadena.

> [!NOTE]
> Para recuperación ante desastres, se recomienda la configuración con la implementación de aplicaciones limitada a dos regiones. Esto es porque la mayoría de las ubicaciones geográficas de Azure tienen solo dos regiones. Esta configuración no protegerá la aplicación de un error grave simultáneo de ambas regiones.  En el caso poco probable de que este error se produjese, podría recuperar sus bases de datos en una tercera región mediante una [operación de restauración geográfica](sql-database-disaster-recovery.md#recover-using-geo-restore).
>

Una vez que se reduce la interrupción, la base de datos secundaria se volverá a sincronizar automáticamente con la principal. Durante la sincronización, el rendimiento de la principal podría verse afectado ligeramente dependiendo de la cantidad de datos que haya que sincronizar. El siguiente diagrama ilustra una interrupción en la región secundaria.

![Base de datos secundaria sincronizada con la primaria. Recuperación ante desastres en la nube.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern1-3.png)

Las **ventajas** clave de este patrón de diseño son:

* La misma aplicación web se implementa en ambas regiones sin ninguna configuración específica de la región y sin lógica adicional para reaccionar ante la conmutación por error. 
* El rendimiento de la aplicación no se ve afectado por la conmutación por error, ya que la aplicación web y la base de datos siempre están colocadas.

El principal **inconveniente** es que la instancia de aplicación redundante en la región secundaria solo se usa para la recuperación ante desastres.

## <a name="design-pattern-2-active-active-deployment-for-application-load-balancing"></a>Patrón de diseño 2: implementación activa-activa para el equilibrio de carga de aplicación
Esta opción de recuperación ante desastres en la nube es mejor para las aplicaciones con las siguientes características:

* Una proporción alta de lecturas en relación a las escrituras en la base de datos.
* La latencia de lectura de la base de datos es más importante para la experiencia del usuario final que la latencia de escritura. 
* La lógica de solo lectura se puede separar de la lógica de lectura y escritura mediante el uso de una cadena de conexión distinta.
* La lógica de solo lectura no depende de que los datos estén totalmente sincronizados con las actualizaciones más recientes.  

Si sus aplicaciones tienen estas características, equilibrar la carga de las conexiones de usuario final entre varias instancias de la aplicación en diferentes regiones puede mejorar considerablemente la experiencia global del usuario final. Dos de las regiones deben seleccionarse como el par de recuperación ante desastres y el grupo de conmutación por error debe incluir las bases de datos de estas regiones. Para implementar el equilibrio de carga, cada región debe tener una instancia activa de la aplicación con la lógica de lectura y escritura (RW) conectada al punto de conexión del agente de escucha de lectura y escritura del grupo de conmutación por error. Esto garantizará que la conmutación por error se iniciará automáticamente si la base de datos principal se ve afectada por una interrupción del servicio. La lógica de solo lectura (RO) en la aplicación web debe conectarse directamente a la base de datos de dicha región. El administrador de tráfico debe configurarse para usar [enrutamiento del rendimiento](../traffic-manager/traffic-manager-configure-performance-routing-method.md) con la [supervisión de punto de conexión](../traffic-manager/traffic-manager-monitoring.md) habilitada para cada instancia de la aplicación.

Como en el patrón nº 1, debe considerar la posibilidad de implementar una aplicación de supervisión similar. Pero a diferencia del patrón n.º 1, la aplicación de supervisión no será la responsable de desencadenar la conmutación por error del extremo.

> [!NOTE]
> Mientras que este patrón usa más de una base de datos secundaria, solo se usará la base de datos secundaria de la región B para la conmutación por error y debe formar parte del grupo de conmutación por error.
>

El Administrador de tráfico tiene que configurarse para el enrutamiento de rendimiento para dirigir las conexiones de usuario a la instancia de aplicación que esté más cerca de la ubicación geográfica del usuario. El siguiente diagrama muestra esta configuración antes de una interrupción.

![Sin interrupción: enrutamiento de rendimiento a la aplicación más cercana. Replicación geográfica.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern2-1.png)

Si se detecta una interrupción de la base de datos de la región A, el grupo de conmutación por error iniciará automáticamente la conmutación por error de la base de datos principal de la región A a la base de datos secundaria de la región de B. También se actualizará automáticamente el punto de conexión del agente de escucha de lectura y escritura a la región B para que las conexiones de lectura y escritura de la aplicación web no se vean afectadas. El administrador de tráfico excluirá el punto sin conexión de la tabla de enrutamiento, pero continuará enrutando el tráfico de usuario final a las instancias en línea restantes. Las cadenas de conexión de SQL de solo lectura no se verán afectadas, ya que siempre señalan a la base de datos en la misma región. 

El siguiente diagrama ilustra la nueva configuración después de la conmutación por error.

![Configuración después de la conmutación por error. Recuperación ante desastres en la nube.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern2-2.png)

En el caso de una interrupción en una de las regiones secundarias, el administrador de tráfico quitará automáticamente el punto sin conexión de esa región de la tabla de enrutamiento. El canal de replicación se suspenderá para la base de datos secundaria en dicha región. Dado que el resto de las regiones reciben tráfico de usuario adicional en este escenario, el rendimiento de la aplicación resultará afectado durante la interrupción. Una vez que se reduce la interrupción, la base de datos secundaria de la región afectada se sincronizará automáticamente con la principal. Durante la sincronización, el rendimiento de la principal podría verse afectado ligeramente dependiendo de la cantidad de datos que haya que sincronizar. El siguiente diagrama ilustra una interrupción en la región B.

![Interrupción en la región secundaria. Recuperación ante desastres en la nube: replicación geográfica.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern2-3.png)

La principal **ventaja** de este patrón de diseño es que la carga de trabajo de la aplicación se puede escalar a través de varios elementos secundarios para lograr el rendimiento óptimo del usuario final. Los **inconvenientes** de esta opción son:

* las conexiones de lectura y escritura entre las instancias de la aplicación y la base de datos varían en latencia y costo.
* El rendimiento de la aplicación se ve afectado durante la interrupción.

> [!NOTE]
> Un enfoque similar puede usarse para descargar las cargas de trabajo especializadas, como trabajos de elaboración de informes, herramientas de inteligencia empresarial o copias de seguridad. Normalmente estas cargas de trabajo consumen una cantidad de recursos importante de la base de datos. Por tanto, se recomienda designar una de las bases de datos secundarias para ellas que tenga un nivel de rendimiento que coincida con la carga de trabajo anticipada.
>

## <a name="design-pattern-3-active-passive-deployment-for-data-preservation"></a>Patrón de diseño 3: implementación activa-pasiva para la conservación de datos
Esta opción es mejor para las aplicaciones con las siguientes características:

* Cualquier pérdida de datos supone un alto riesgo para la empresa. La conmutación por error de la base de datos solo puede usarse como último recurso si la interrupción es grave.
* La aplicación admite los modos de solo lectura y lectura y escritura de operaciones y puede funcionar en "modo de solo lectura" durante un período de tiempo.

En este modelo, la aplicación cambia al modo de solo lectura cuando las conexiones de lectura y escritura empiezan a tener errores de tiempo de espera agotado. La aplicación web se implementa en ambas regiones e incluyen una conexión con el punto de conexión del agente de escucha de lectura y escritura y otra conexión con el punto de conexión del agente de escucha de solo lectura. El administrador de tráfico debe configurarse para usar el [enrutamiento de conmutación por error](../traffic-manager/traffic-manager-configure-failover-routing-method.md) con la [supervisión de punto de conexión](../traffic-manager/traffic-manager-monitoring.md) habilitada para el punto de conexión de aplicación en cada región.

El siguiente diagrama muestra esta configuración antes de una interrupción.

![Implementación activa-pasiva antes de la conmutación por error. Recuperación ante desastres en la nube.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern3-1.png)

Cuando el administrador de tráfico detecta un error de conectividad con la región A, cambia automáticamente el tráfico de usuario a la instancia de aplicación de la región B. Con este modelo, es importante establecer el período de gracia con pérdida de datos en un valor suficientemente alto, por ejemplo, 24 horas. Esto asegurará que se evita la pérdida de datos si la interrupción se mitiga durante ese período. Cuando la aplicación web de la región B se active, las operaciones de lectura y escritura comenzarán a generar errores. En ese momento, debe cambiar al modo de solo lectura. En este modo, las solicitudes se enrutarán automáticamente a la base de datos secundaria. En el caso de que se produzca un error grave, la interrupción no se mitigará dentro del período de gracia y el grupo de conmutación por error activará la conmutación por error. Después de eso, el agente de escucha de lectura y escritura pasará a estar disponible y las llamadas a él dejarán de dar error. Esto se ilustra en el diagrama siguiente

![Interrupción: aplicación en modo de sólo lectura. Recuperación ante desastres en la nube.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern3-2.png)

Si la interrupción de la región primaria se soluciona dentro del período de gracia, el administrador de tráfico detecta la restauración de la conectividad en la región principal y cambia el tráfico de usuario de nuevo a la instancia de aplicación de la región A. Esa instancia de aplicación se reanuda y funciona en modo de lectura y escritura utilizando la base de datos principal de la región A.

En caso de una interrupción en la región B, el administrador de tráfico detecta el error del punto de conexión de la aplicación en la región B y grupo de conmutación por error cambia el agente de escucha de solo lectura a la región A. Esta interrupción no afecta a la experiencia del usuario final, pero la base de datos principal quedará expuesta durante la interrupción. Esto se ilustra en el diagrama siguiente

![Interrupción: base de datos secundaria. Recuperación ante desastres en la nube.](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern3-3.png)

Una vez mitigada la interrupción, la base de datos secundaria se sincroniza inmediatamente con la principal y el agente de escucha de solo lectura se vuelve a cambiar a la base de datos secundaria de la región B. Durante la sincronización, el rendimiento de la principal podría verse ligeramente afectado dependiendo de la cantidad de datos que se necesite sincronizarse.

Este patrón de diseño tiene varias **ventajas**:

* Evita la pérdida de datos durante las interrupciones temporales.
* El tiempo de inactividad depende solo de la rapidez del Administrador de tráfico para detectar el error de conectividad, que se puede configurar.

La **contrapartida** es:

* La aplicación tiene que poder operar en modo de solo lectura.

> [!NOTE]
> En el caso de una interrupción del servicio permanente en la región, tiene que activar manualmente la conmutación por error de base de datos y aceptar la pérdida de datos. La aplicación será funcional en la región secundaria con acceso de lectura y escritura a la base de datos.
>

## <a name="business-continuity-planning-choose-an-application-design-for-cloud-disaster-recovery"></a>Planificación de continuidad del negocio: elección del diseño de una aplicación para la recuperación ante desastres en la nube
Su estrategia de recuperación ante desastres en la nube puede combinar o ampliar estos patrones para satisfacer mejor las necesidades de su aplicación.  Como se mencionó anteriormente, la estrategia que elija se basa en el acuerdo de nivel de servicio que desee ofrecer a sus clientes y en la topología de implementación de la aplicación. Para guiarle en su decisión, la siguiente tabla compara las opciones en función de la pérdida de datos estimada u objetivo de punto de recuperación (RPO) y el tiempo de recuperación calculado (ERT).

| Patrón | RPO | ERT |
|:--- |:--- |:--- |
| Implementación activa-pasiva para la recuperación ante desastres con acceso a base de datos colocalizada |Acceso de lectura y escritura < 5 s |Tiempo de detección de errores + TTL de DNS |
| Implementación activa-activa para el equilibrio de carga de aplicación |Acceso de lectura y escritura < 5 s |Tiempo de detección de errores + TTL de DNS |
| Implementación activa-pasiva para la conservación de datos |Acceso de solo lectura < 5 s | Acceso de solo lectura = 0 |
||Acceso de lectura y escritura = cero | Acceso de lectura y escritura = Tiempo de detección de errores + Período de gracia con pérdida de datos |
|||

## <a name="next-steps"></a>Pasos siguientes
* Para saber en qué consisten las copias de seguridad automatizadas de Base de datos SQL de Azure, consulte [Información general: copias de seguridad automatizadas de Base de datos SQL](sql-database-automated-backups.md)
* Para obtener una descripción general y los escenarios de la continuidad empresarial, consulte [Continuidad empresarial con Base de datos SQL de Azure](sql-database-business-continuity.md)
* Si quiere saber cómo utilizar las copias de seguridad automatizadas para procesos de recuperación, consulte [Restore a database from the service-initiated backups](sql-database-recovery-using-backups.md)
* Para conocer las opciones de recuperación más rápidas, consulte [Replicación geográfica activa](sql-database-geo-replication-overview.md)  
* Si quiere aprender a utilizar las copias de seguridad automatizadas para procesos de archivado, consulte [Copiar una base de datos SQL de Azure](sql-database-copy.md)
* Para saber cómo utilizar la replicación geográfica activa con los grupos elásticos, consulte [Estrategias de recuperación ante desastres para aplicaciones que usan el grupo elástico de Base de datos SQL](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).

