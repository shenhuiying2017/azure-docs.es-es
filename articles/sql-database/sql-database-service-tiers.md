<properties
   pageTitle="Niveles de servicio de Base de datos SQL"
   description="Compare las características de rendimiento y de continuidad del negocio de los niveles de servicio de Base de datos SQL de Azure para encontrar el equilibrio adecuado entre costo y funcionalidad a medida que escalan a petición sin tiempo de inactividad."
   services="sql-database"
   documentationCenter=""
   authors="shontnew"
   manager="jeffreyg"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="09/11/2015"
   ms.author="shkurhek"/>

# Niveles de servicio de Base de datos SQL

## Información general
[Base de datos SQL de Azure](sql-database-technical-overview.md) proporciona varios niveles de servicio para administrar los diferentes tipos de cargas de trabajo. El usuario tiene la opción de crear una base de datos única con características y precios definidos. O bien, puede crear varias bases de datos en un grupo de bases de datos elásticas. En ambos casos, los niveles son: **Basic**, **Standard** y **Premium**. Pero las características de estas capas varían en función de si se va a crear una base de datos individual o una base de datos dentro de un grupo de bases de datos elásticas. Este artículo proporciona información general de los niveles de servicio en ambos contextos.

## Niveles de servicio
Los niveles de servicio Basic, Standard y Premium tienen un SLAde tiempo de actividad del 99,99% y ofrecen un rendimiento predecible, opciones de continuidad del negocio flexibles, características de seguridad y facturación por hora. La tabla siguiente proporciona ejemplos de los niveles más adecuados para las cargas de trabajo de las diferentes aplicaciones.

| Nivel de servicio | Cargas de trabajo de destino |
|---|---|
| **Básica** | Ideal para bases de datos pequeñas, que suelen admitir una sola operación activa en un momento dado. Entre los ejemplos se incluyen las bases de datos usadas para desarrollo, pruebas o aplicaciones a pequeña escala que se usan con poca frecuencia. |
| **Standard** | La opción predilecta para la mayoría de aplicaciones en la nube, admite varias consultas simultáneas. Entre los ejemplos se incluyen aplicaciones web o para grupos de trabajo. |
| **Premium** | Diseñado para altos volúmenes de transacciones, admite un gran número de usuarios simultáneos y requiere el máximo nivel de capacidades de continuidad empresarial. Entre los ejemplos se incluyen bases de datos que admiten aplicaciones críticas. |

>[AZURE.NOTE]Retirada de las ediciones Web y Business. Descubra cómo [actualizar las ediciones Web y Business](sql-database-upgrade-new-service-tiers.md). Si piensa seguir usando las ediciones Web y Business, lea [P+F de Sunset](http://azure.microsoft.com/pricing/details/sql-database/web-business/).

### Niveles de servicio para bases de datos únicas
Para las bases de datos únicas hay varios niveles de rendimiento dentro de cada nivel de servicio, tiene la flexibilidad de elegir el nivel que mejor se adapte a las exigencias de la carga de trabajo. Si necesita escalar vertical u horizontalmente, puede cambiar fácilmente los niveles de la base de datos en el Portal de Azure sin que haya tiempo de inactividad en la aplicación. Para obtener información más detallada, consulte [Modificación de niveles de servicio y de rendimiento de la base de datos](sql-database-scale-up.md).

Las características de rendimiento que se enumeran aquí se aplican a las bases de datos creadas con [Base de datos SQL V12](sql-database-v12-whats-new.md).

[AZURE.INCLUDE [Tabla de niveles de servicio de datos de la Base de datos SQL](../../includes/sql-database-service-tiers-table.md)]

### Niveles de servicio para grupos de bases de datos elásticas
Además de crear y escalar una base de datos única, también es posible administrar varias bases de datos dentro de un [grupo de bases de datos elásticas](sql-database-elastic-pool.md). Todas las bases de datos de un grupo de bases de datos elásticas comparten un conjunto común de recursos. Las características de rendimiento se miden por *Unidades de transacción de bases de datos elásticas* (eDTU). Como ocurre con las bases de datos únicas, los grupos de bases de datos elásticas tienen tres niveles de rendimiento: **Basic**, **Standard** y **Premium**. En el caso de las bases de datos elásticas, estos tres niveles también definen los límites de rendimiento global y varias características.

Los grupos de bases de datos elásticas permiten que estas bases de datos compartan y consuman recursos de DTU sin que sea preciso de asignar un nivel de rendimiento específicos a las bases de datos del grupo. Por ejemplo, una base de datos de un grupo Standard puede usar desde 0 eDTU al número máximo de eDTU de la base de datos (ya sean los 100 eDTU definidos por el nivel de servicio o un número personalizado que se configure). Esto permite que varias bases de datos con diferentes cargas de trabajo usen de forma eficaz los recursos de eDTU disponibles para todo el grupo.

En la tabla siguiente se describen las características de los niveles de servicio de los grupos de bases de datos elásticas.

[AZURE.INCLUDE [Tabla de niveles de servicio de Base de datos SQL para bases de datos elásticas](../../includes/sql-database-service-tiers-table-elastic-db-pools.md)]

>[AZURE.NOTE]Todas las bases de datos de un grupo también se ajustan a las características de base de datos única de ese nivel. Por ejemplo, el grupo Basic tiene un límite de número máximo de sesiones por grupo de 2400 – 28800, pero una base de datos individual de dicho grupo tiene un límite de base de datos de 300 sesiones (el límite de una base de datos Basic especificado en la sección anterior).

## Descripción de las DTU

[AZURE.INCLUDE [Descripción de DTU de base de datos SQL](../../includes/sql-database-understanding-dtus.md)]

## Supervisión del rendimiento
La supervisión del rendimiento de una base de datos SQL comienza con la supervisión del uso de recursos, en relación con el nivel de rendimiento elegido para la base de datos. Estos datos relevantes se exponen de las siguientes formas:

1.	En el Portal de administración de Microsoft Azure.

2.	En las vistas de administración dinámica de la base de datos de usuario y en la base de datos maestra del servidor que contiene la base de datos de usuario.

En el [Portal de vista previa de Azure](https://portal.azure.com/), se puede supervisar el uso de una base de datos única; para ello, se debe seleccionar la base de datos y hacer clic en el gráfico **Supervisión**. Se abrirá una ventana **Métrica** que se puede cambiar haciendo clic en el botón **Editar gráfico**. Agregue las siguientes métricas:

- Porcentaje de CPU
- Porcentaje de DTU
- Porcentaje de E/S de datos
- Porcentaje de almacenamiento

Una vez agregadas estas métricas, aún se pueden ver en el gráfico **Supervisión** con más detalles en el ventana **Métrica**. Las cuatro métricas de cuatro muestran el porcentaje de uso medio, en relación con la **DTU** de la base de datos.

![supervisión de nivel de servicio](./media/sql-database-service-tiers/sqldb_service_tier_monitoring.png)

También se pueden configurar alertas en las métricas de rendimiento. Haga clic en el botón **Agregar alerta** botón de la ventana **Métrica**. Siga el asistente para configurar la alerta. Tiene la opción de que se genere una alerta si la métrica supera un umbral determinado, o si no llega a él.

Por ejemplo, si espera que crezca la carga de trabajo de una base de datos, puede configurar una alerta por correo electrónico cada vez que la base de datos alcance el 80% en cualquiera de las métricas de rendimiento. Esto se puede usar como advertencia prematura para saber cuándo puede tener que cambiar al nivel de rendimiento inmediatamente superior.

Las métricas de rendimiento también pueden ayudarle a determinar si puede cambiar a un nivel de rendimiento inferior. Suponga que usa una base de datos Estándar S2 y todas las métricas de rendimiento muestran que, de media, la base de datos no usa más del 10% en ningún momento. Es probable que la base de datos funcione bien en Estándar S1. Sin embargo, tenga en cuenta las cargas de trabajo que tienen picos o fluctúan antes de tomar la decisión de cambiar a un nivel de rendimiento inferior.

Las mismas métricas que se exponen en el portal también están disponibles a través de las vistas del sistema: [sys.resource\_stats](https://msdn.microsoft.com/library/dn269979.aspx) en la base de datos maestra lógica del servidor y [sys.dm\_db\_resource\_stats](https://msdn.microsoft.com/library/dn800981.aspx) en la base de datos de usuario (**sys.dm\_db\_resource\_stats** se crea en cada base de datos de usuario Basic, Standard y Premium. Las bases de datos Web y Business Edition devuelven un conjunto de resultados vacío). Use **sys.resource\_stats** si necesita supervisar datos menos pormenorizados en un periodo mayor. Use **sys.dm\_db\_resource\_stats** si necesita supervisar datos más pormenorizados en un período menor. Para obtener más información, consulte [Guía de rendimiento de Base de datos SQL de Azure](https://msdn.microsoft.com/library/azure/dn369873.aspx).

En los grupos de bases de datos elásticas, puede supervisar las bases de datos individuales del grupo con las técnicas descritas en esta sección. Sin embargo, también puede supervisar el grupo en conjunto. Para obtener información, consulte [Supervisión y administración de un grupo de bases de datos elásticas](sql-database-elastic-pool-portal.md#monitor-and-manage-an-elastic-database-pool).

## Pasos siguientes
Para obtener más información sobre los precios de estos niveles, consulte [Precios de Base de datos SQL](http://azure.microsoft.com/pricing/details/sql-database/).

Si está interesado en la administración de varias bases de datos como un grupo, consulte [Grupos de bases de datos elásticas](sql-database-elastic-pool-guidance.md) en [Consideraciones de precio y rendimiento para un grupo de bases de datos elásticas](sql-database-elastic-pool-guidance.md).

Ahora que conoce los niveles de Base de datos SQL, pruébelos con una [evaluación gratuita](http://azure.microsoft.com/pricing/free-trial/) y aprenda a [crear su primera base de datos SQL](sql-database-get-started.md).
 

<!---HONumber=Oct15_HO1-->