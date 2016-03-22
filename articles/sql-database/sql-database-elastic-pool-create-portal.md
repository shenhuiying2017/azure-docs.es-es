<properties
	pageTitle="Creación de grupos de bases de datos elásticas escalables | Microsoft Azure"
	description="Cómo agregar un grupo de bases de datos elásticas escalables a la configuración de la Base de datos SQL para una administración y un uso compartido de los recursos más sencillos entre varias bases de datos."
	keywords="base de datos escalable,configuración de base de datos"
	services="sql-database"
	documentationCenter=""
	authors="jeffgoll"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="03/16/2016"
	ms.author="jeffreyg"
	ms.workload="data-management"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="NA"/>


# Creación de un grupo de bases de datos elásticas escalables para Bases de datos SQL en el Portal de Azure

> [AZURE.SELECTOR]
- [Portal de Azure](sql-database-elastic-pool-create-portal.md)
- [C#](sql-database-elastic-pool-create-csharp.md)
- [PowerShell](sql-database-elastic-pool-create-powershell.md)

En este artículo se muestra cómo crear un [grupo de bases de datos elásticas](sql-database-elastic-pool.md) escalable con el [Portal de Azure](https://portal.azure.com/). Hay dos maneras de crear un grupo. Puede hacerlo desde cero, si conoce la configuración de grupo que desea, o comenzar con una recomendación del servicio, que tiene una inteligencia integrada que le recomienda una configuración de grupo, si es más rentable en función de la telemetría de uso pasada de la base de datos.

Puede agregar varios grupos a un servidor, pero no puede agregar bases de datos de servidores diferentes al mismo grupo. Para crear un grupo, necesita al menos una base de datos en un servidor V12. Si no la tiene, consulte [Tutorial de Base de datos SQL: creación de una Base de datos SQL en cuestión de minutos con datos de ejemplo y el Portal de Azure](sql-database-get-started.md). Puede crear un grupo con una única base de datos pero los grupos solo son rentables con varias bases de datos. Consulte [Consideraciones de precio y rendimiento para un grupo de bases de datos elásticas](sql-database-elastic-pool-guidance.md).

> [AZURE.NOTE] Los grupos solo están disponibles con servidores V12 de Base de datos SQL. Si tiene una versión anterior, puede [usar PowerShell para actualizar a V12 y crear un grupo](sql-database-upgrade-server-powershell.md) en un solo paso.

##Creación de un nuevo grupo
1. En el [Portal de Azure](http://portal.azure.com/), haga clic en **Servidores SQL Server** y, luego, en el servidor que contiene las bases de datos que desea agregar a un grupo.
2. Haga clic en **Grupo nuevo**.

    ![Adición de un grupo a un servidor](./media/sql-database-elastic-pool-create-portal/new-pool.png)

    **O**

    Haga clic en el mensaje para ver los grupos recomendados según la telemetría de uso histórica de base de datos y, después, en el nivel para ver más detalles y personalizar el grupo. Consulte [Descripción de las recomendaciones de grupos](#understand-pool-recommendations) más adelante en este tema para ver cómo se realiza la recomendación.

    ![grupo recomendado](./media/sql-database-elastic-pool-create-portal/recommended-pool.png)

    Aparecerá la hoja **Grupo de bases de datos elásticas**, que es donde va a configurar el grupo. Si hizo clic en **Grupo nuevo** en el paso anterior, el portal elige un **Grupo Standard** en **Plan de tarifa**, un único **Nombre** para el grupo y una configuración predeterminada para el grupo. Si ha elegido un grupo recomendado, el plan y la configuración recomendados del grupo ya están elegidos pero puede cambiarlos.

    ![Configuración de grupos elásticos](./media/sql-database-elastic-pool-create-portal/configure-elastic-pool.png)

3. Para cambiar el plan de tarifa para el grupo, haga clic en **Plan de tarifa**, luego, en el plan de tarifa que desee y, a continuación, en **Seleccionar**.

    > [AZURE.IMPORTANT] Después de elegir el plan de tarifa y confirmar los cambios haciendo clic en **Aceptar** en el último paso, no podrá cambiar el plan de tarifa del grupo.

4. Haga clic en **Configurar grupo**, donde puede agregar bases de datos y elegir la configuración de recursos para el grupo.
5. Para agregar bases de datos, haga clic en **Agregar base de datos**, luego, en las bases de datos que desee agregar y, a continuación, en el botón **Seleccionar**.

    ![Adición de bases de datos](./media/sql-database-elastic-pool-create-portal/add-databases.png)

    Si está trabajando con bases de datos que tienen suficiente telemetría de uso histórica, el gráfico **Uso estimado de eDTU y GB** y el gráfico de barras **Uso real de eDTU** se actualizan para ayudarle a tomar decisiones de configuración. Además, el servicio puede proporcionar un mensaje de recomendación que le ayuda a ajustar el tamaño correcto del grupo. Consulte [Descripción de las recomendaciones de grupos](#understand-pool-recommendations).

    ![recomendaciones dinámicas](./media/sql-database-elastic-pool-create-portal/dynamic-recommendation.png)

6. Use los controles de la página **Configurar grupo** para explorar la configuración y establecer el grupo según las siguientes directrices:

    ![Configuración de grupos elásticos](./media/sql-database-elastic-pool-create-portal/configure-performance.png)

    | Configuración de rendimiento | Descripción |
    | :--- | :--- |
    | **eDTU del grupo** y **GB del grupo** (según la configuración del grupo)| Las eDTU máximas disponibles y compartidas por todas las bases de datos del grupo. Las eDTU máximas disponibles en un grupo dependen del plan de tarifa (nivel de servicio). La **eDTU del grupo** está relacionada con el almacenamiento disponible para el grupo. Para cada eDTU que asigne al grupo, obtendrá una cantidad fija de almacenamiento de base de datos y viceversa. |
    | **eDTU mín.** (según la configuración de la base de datos)| El número mínimo de eDTU del grupo que se garantiza en una base de datos única en todo momento. Normalmente, la **eDTU mín.** se establece en cualquier valor entre 0 y el promedio de uso histórico de eDTU por base de datos. Se trata de una configuración global que se aplica a todas las bases de datos del grupo. |
    | **eDTU máx.** (según la configuración de la base de datos) | El número máximo de eDTU que puede usar una base de datos única del grupo. Puede establecer este límite máximo hasta la **eDTU del grupo**. Establezca la **eDTU máx.** por base de datos lo suficientemente alta para controlar las ráfagas o picos máximos durante el pico de demanda de la base de datos. Se admite cierto grado de exceso de asignación de recursos, ya que el grupo suele basarse en patrones de uso en frío y caliente de las bases de datos, cuando en realidad los picos de demanda no tienen lugar en todas las bases de datos a la vez. **Ejemplo:** suponga que el pico de utilización de cada base de datos es de 50 DTU y solo el 20 % de las 100 bases de datos del grupo registran simultáneamente un pico de rendimiento. Si el límite máximo de eDTU por base de datos se establece en 50 eDTU, puede asignar al grupo una cantidad cinco veces mayor y establecer la **eDTU del grupo** en 1000. La **eDTU máx.** no es un número garantizado de recursos para una base de datos, sino que es un valor máximo de eDTU que se puede llegar a alcanzar si está disponible. |

    Consulte [Referencia de grupos de bases de datos elásticas de Base de datos SQL](sql-database-elastic-pool-reference.md#edtu-and-storage-limits-for-elastic-pools-and-elastic-databases) para ver más detalles acerca de los límites de cada nivel de servicio y [Consideraciones de precio y rendimiento para un grupo de bases de datos elásticas](sql-database-elastic-pool-guidance.md) para ver instrucciones detalladas sobre el ajuste de tamaño correcto de un grupo.

7. Haga clic en **Seleccionar** al finalizar y, a continuación, en **Aceptar** para crear el grupo.

##Descripción de las recomendaciones de grupos
El servicio Base de datos SQL evalúa el historial de uso y recomienda uno o varios grupos cuando sea más económico que usar bases de datos únicas. Cada recomendación se configura con un subconjunto único de las bases de datos del servidor que mejor se ajustan al grupo. La recomendación de grupo consta de:

- Un plan de tarifa del grupo (Basic, Standard o Premium).
- Las **eDTU del grupo** adecuadas (también denominadas eDTU máx. por grupo)
- Las **eDTU máx.** y **eDTU mín.** por base de datos
- La lista de bases de datos recomendadas para el grupo

El servicio tiene en cuenta los últimos 30 días de telemetría al recomendar grupos. Para que una base de datos se considere candidata para un grupo de bases de datos elásticas, debe tener una existencia mínima de siete días. Las bases de datos que ya están en un grupo de bases de datos elásticas no se consideran candidatas para las recomendaciones de grupos de bases de datos elásticas.

El servicio evalúa las necesidades de recursos y la rentabilidad de mover las bases de datos únicas de cada nivel de servicio a grupos del mismo nivel. Por ejemplo, se evalúan todas las bases de datos Standard en un servidor para que quepan en un bloque de bases de datos elásticas Standard. Esto significa que el servicio no hace recomendaciones entre niveles como, por ejemplo, mover una base de datos Standard a un grupo Premium.

## Recursos adicionales

- [Manage a SQL Database elastic pool with the portal](sql-database-elastic-pool-manage-portal.md) (Administración de un grupo elástico de Base de datos SQL con el portal)
- [Manage a SQL Database elastic pool with PowerShell](sql-database-elastic-pool-manage-powershell.md) (Administración de un grupo elástico de Base de datos SQL con PowerShell)
- [Creación y administración de bases de datos SQL con C#](sql-database-client-library.md)
- [Referencia de bases de datos elásticas](sql-database-elastic-pool-reference.md)

<!---HONumber=AcomDC_0316_2016-->