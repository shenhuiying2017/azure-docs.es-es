<properties
	pageTitle="Creación de grupos de bases de datos elásticas escalables | Microsoft Azure"
	description="Cómo agregar un grupo de bases de datos elásticas escalables a la configuración de la Base de datos SQL para una administración y un uso compartido de los recursos más sencillos entre varias bases de datos."
	keywords="base de datos escalable,configuración de base de datos"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="11/06/2015"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="NA"/>


# Creación de un grupo de bases de datos elásticas escalables para Bases de datos SQL en el Portal de vista previa de Azure

> [AZURE.SELECTOR]
- [Azure preview portal](sql-database-elastic-pool-portal.md)
- [C#](sql-database-elastic-pool-csharp.md)
- [PowerShell](sql-database-elastic-pool-powershell.md)

En este artículo se muestra cómo crear un [grupo de bases de datos elásticas](sql-database-elastic-pool.md) escalable con el Portal de vista previa de Azure. Una configuración de Base de datos SQL con grupos de base de datos elásticas simplifica la administración y el uso compartido de los recursos entre varias bases de datos.

> [AZURE.NOTE]Los grupos de bases de datos elásticas están actualmente en vista previa y solo estarán disponibles en servidores con bases de datos SQL V12. Si tiene un servidor de Base de datos SQL V11, puede [usar PowerShell para actualizar a V12 y crear un grupo](sql-database-upgrade-server.md) en un solo paso.


Antes de comenzar, necesita una base de datos en un servidor de Base de datos SQL V12. Si no la tiene, consulte [Creación de la primera Base de datos SQL de Azure](sql-database-get-started.md) para crear una en menos de cinco minutos. O, si ya tiene un servidor de Base de datos SQL V11, puede [actualizar a V12 en el Portal](sql-database-v12-upgrade.md) y, después, vuelva y siga estas instrucciones para crear un grupo.


## Paso 1: agregue un grupo a un servidor

Cree un grupo de bases de datos elásticas agregando un nuevo grupo a un servidor. Puede agregar varios grupos a un servidor, pero únicamente se puede asociar un solo servidor a cada grupo. Además, es posible agregar a un grupo todas o algunas de las bases de datos de un servidor.


En el [Portal de vista previa de Azure](https://ms.portal.azure.com/), haga clic en **Servidores SQL Server**, haga clic en el servidor que hospeda las bases de datos que desea agregar al grupo y, luego, en **Agregar grupo**.

![Adición de un grupo a un servidor](./media/sql-database-elastic-pool-portal/elastic-pool-add-pool.png)

O bien

Si ve un mensaje que indica que hay un grupo recomendado para un servidor, haga clic en él para revisar y crear fácilmente un grupo optimizado para las bases de datos del servidor. Para obtener más información, consulte [Grupos de bases de datos elásticas recomendados](sql-database-elastic-pool-portal.md#recommended-elastic-database-pools).


![Creación de grupos elásticos][1]


La hoja **Grupo de bases de datos elásticas** brinda opciones para elegir el plan de tarifa, agregar bases de datos y configurar las características de rendimiento del grupo.

> [AZURE.NOTE]Cuando selecciona por primera vez el comando **Agregar grupo**, debe aceptar los términos de la vista previa. Para ello, seleccione **TÉRMINOS DE VISTA PREVIA** y complete la hoja **Términos de vista previa**. Solo debe hacer esto una vez para cada suscripción.

   ![Configuración de grupos elásticos][2]

## Paso 2: elija un plan de tarifa

El plan de tarifa del grupo determina las características disponibles para las bases de datos elásticas del grupo, además de la cantidad máxima de eDTU (eDTU MÁX.) y el almacenamiento (GB) disponibles para cada base de datos. Si desea obtener detalles, consulte [Niveles de servicio](sql-database-service-tiers.md#Service-tiers-for-elastic-database-pools).

>[AZURE.NOTE]Actualmente en la vista previa, no se puede cambiar el nivel de precios de un grupo de bases de datos elásticas una vez creado. Para cambiar el nivel de precios de un grupo de bases de datos elásticas existente, cree un nuevo grupo de bases de datos elásticas en el nivel de precios que quiera y migre las bases de datos elásticas al nuevo grupo.

   ![Nivel de precios][9]


### Recomendaciones sobre el plan de tarifa

El servicio Base de datos SQL evalúa el historial de uso y recomienda uno o más grupos de bases de datos elásticas cuando sea más rentable que usar bases de datos únicas.

Los planes de tarifa marcados con una estrella (![estrella][10]) son los recomendados en función de las cargas de trabajo de las bases de datos.

Si se recomienda más de un nivel de precios, indica que se deben crear varios grupos de bases de datos elásticas. Cada recomendación se configura con un subconjunto único de las bases de datos del servidor que mejor se ajustan al grupo.

Además de simplemente sugerir un nivel de precios para el grupo de bases de datos elásticas, la recomendación de cada grupo contiene lo siguiente:

- Nivel de precios del grupo (Basic, Standard o Premium).
- Cantidad adecuada de unidades eDTU del grupo.
- La configuración de eDTU mín./máx. de las bases de datos elásticas.  
- Lista de bases de datos recomendadas.

El servicio tiene en cuenta los últimos 30 días de telemetría al recomendar grupos de bases de datos elásticas. Para que una base de datos se considere candidata para un grupo de bases de datos elásticas, debe tener una existencia mínima de siete días. Las bases de datos que ya están en un grupo de bases de datos elásticas no se consideran candidatas para las recomendaciones de grupos de bases de datos elásticas.

El servicio evalúa las necesidades de recursos y la rentabilidad de mover las bases de datos únicas de cada nivel de servicio a grupos de bases de datos elásticas del mismo nivel. Por ejemplo, se evalúan todas las bases de datos Standard en un servidor para que quepan en un bloque de bases de datos elásticas Standard. Esto significa que el servicio no hace recomendaciones entre niveles como, por ejemplo, mover una base de datos Standard a un grupo Premium.

>[AZURE.NOTE]Las bases de datos Web y Business se asignan a uno de los nuevos niveles Basic, Standard o Premium según su tamaño de base de datos y el historial de uso. Al asignarlas a los nuevos niveles se recomiendan las bases de datos Web y Business al grupo adecuado.


## Paso 3: agregue bases de datos al grupo

En cualquier momento, puede seleccionar las bases de datos específicas que desea incluir en el grupo. Cuando cree un nuevo grupo, Azure recomendará y marcará las bases de datos más indicadas para su inclusión en el grupo. Puede agregar todas las bases de datos disponibles en el servidor, o bien puede activar o desactivar las bases de datos de la lista inicial como desee.

   ![Adición de bases de datos][5]

Cuando selecciona una base de datos para agregarla a un grupo, deben cumplirse las condiciones siguientes:

- El grupo debe tener espacio para la base de datos (no puede contener el número máximo de bases de datos). Más específicamente, el grupo debe tener suficientes eDTU disponibles para cubrir el número garantizado de eDTU por base de datos (por ejemplo, si el número garantizado de eDTU para el grupo es de 400 y el número garantizado de eDTU para cada base de datos es 10, el número máximo de bases de datos que se permiten en el grupo es de 40 (400 eDTU/10 eDTU garantizadas por base de datos = 40 bases de datos como máximo).
- Las características actuales que utiliza la base de datos deben estar disponibles en el grupo.


## Paso 4: ajuste las características de rendimiento

El rendimiento del grupo se configura estableciendo los parámetros de rendimiento para el grupo y las bases de datos elásticas del grupo. Tenga en cuenta que la **configuración de bases de datos elásticas** se aplica a todas las bases de datos del grupo.

   ![Configuración de grupos elásticos][3]

Existen tres parámetros que puede establecer y que definen el rendimiento del grupo: el número garantizado de eDTU del grupo, así como el eDTU MIN y el eDTU MAX de bases de datos elásticas del grupo. La tabla siguiente describe cada uno de estos parámetros y ofrece algunas indicaciones sobre cómo configurarlos. Para saber la configuración de valor disponible específica, consulte la [referencia de grupo de bases de datos elásticas](sql-database-elastic-pool-reference.md).

| Parámetro de rendimiento | Descripción |
| :--- | :--- |
| **POOL eDTU**: garantía de eDTU para el grupo | El número garantizado de eDTU para el grupo es el número garantizado de eDTU disponibles y compartidas por todas las bases de datos del grupo. <br> El tamaño específico del número garantizado de eDTU para un grupo debe aprovisionarse teniendo en cuenta el uso histórico de eDTU del grupo. Como alternativa, este valor se puede establecer según el número garantizado de DTU por base de datos y el uso de bases de datos activas al mismo tiempo. El número garantizado de DTU para el grupo también guarda relación con la cantidad de almacenamiento disponible para el grupo, ya que por cada DTU que se asigne al grupo se obtiene una cantidad fija de almacenamiento de base de datos. <br> **¿En qué valor debo establecer el número garantizado de eDTU del grupo?** <br>Como mínimo, debe establecer el número garantizado de eDTU del grupo en un valor equivalente al resultado de la operación siguiente: ([n.º de bases de datos] x [promedio de uso de DTU por base de datos]). |
| **eDTU MIN**: número garantizado de eDTU para cada base de datos | El número garantizado de eDTU por base de datos es el número de eDTU que se garantiza en una base de datos única del grupo. Por ejemplo, en grupos Standard, este número garantizado se puede establecer en 0, 10, 20, 50 o 100 eDTU. O bien, puede no proporcionar ningún número garantizado a las bases de datos del grupo (eDTU MIN=0). <br>**¿En qué valor debo establecer el número garantizado de eDTU por base de datos?** <br>Normalmente, el número garantizado de eDTU por base de datos (eDTU MIN) se establece en cualquier valor entre 0 y el ([promedio de uso por base de datos]). El número garantizado de eDTU por base de datos es un ajuste global que establece el número garantizado de eDTU para todas las bases de datos del grupo. |
| **eDTU MAX**: límite de eDTU por base de datos | El valor eDTU MAX por base de datos indica el número máximo de eDTU que puede usar cada una de las bases de datos del grupo. Establezca la capacidad de eDTU por base de datos en un valor lo suficientemente alto como para soportar las fluctuaciones de demanda de las bases de datos. Puede establecer esta capacidad en el límite del sistema, que dependerá del nivel de precios del grupo (1000 eDTU en el caso de Premium). La capacidad específica debe soportar los picos de utilización de las bases de datos del grupo. Se admite un cierto grado de exceso de asignación de recursos, ya que el grupo suele basarse en patrones de uso en frío y caliente de las bases de datos, cuando en realidad los picos de demanda no tienen lugar en todas las bases de datos a la vez.<br> **¿En qué valor debo establecer la capacidad de eDTU por base de datos?** <br> Establezca el valor eDTU MAX o límite de eDTU por base de datos en ([uso máximo de base de datos)]. Por ejemplo, suponga que el pico de utilización de cada base de datos es de 50 DTU y solo el 20% de las 100 bases de datos del grupo registran simultáneamente un pico de rendimiento. Si la capacidad de eDTU de cada base de datos se establece en 50 eDTU, deberá asignar una cantidad de recursos cinco veces mayor y establecer el número garantizado de eDTU para el grupo en 1000 eDTU. También debe tener en cuenta que la capacidad de eDTU no es un número garantizado de recursos para las bases de datos, sino que es un valor máximo que puede llegar a alcanzarse si está disponible. |

## Grupos de bases de datos elásticas recomendados

Vaya a un servidor de Base de datos SQL V12 y es posible que vea un mensaje indicando que existen grupos de bases de datos elásticas recomendados para el servidor.

Al igual que las recomendaciones del nivel de precios para un grupo de bases de datos elásticas, los grupos recomendados están preconfigurados con los siguientes valores ya establecidos:

- Nivel de precios para el grupo.
- Cantidad adecuada de unidades eDTU del grupo.
- Configuración de número mínimo y máximo de eDTU de la base de datos.  
- Lista de bases de datos recomendadas.

### Creación de un grupo recomendado

1. Haga clic en el mensaje para ver una lista de los grupos recomendados:

     ![grupos recomendados][12]

1. Haga clic en un grupo para ver la configuración detallada de la recomendación.
2. Simplemente modifique el nombre del grupo y haga clic en **Aceptar** para crear el grupo. (Los grupos recomendados no se pueden modificar después de la creación).

    ![grupo recomendado][11]


## Agregar y quitar bases de datos del grupo

Una vez creado el grupo, puede agregarle o quitarle bases de datos activando o desactivando las bases de datos en la página **Agregar bases de datos**.

Después de crear un grupo, también puede usar Transact-SQL para crear bases de datos elásticas nuevas en el grupo y mover las bases de datos dentro y fuera de un grupo. Para obtener información detallada, consulte [Referencia de grupos de bases de datos elásticas - Transact-SQL](sql-database-elastic-pool-reference.md#Transact-SQL).*


## Supervisión y administración de un grupo de bases de datos elásticas

Después de crear un grupo de bases de datos elásticas, podrá supervisar y administrar el grupo en el portal examinando la lista de grupos existentes y seleccionando el grupo deseado.

Después de crear un grupo, podrá:

- Seleccione la opción **Configurar grupo** para cambiar la configuración de número de eDTU del grupo y número de eDTU por base de datos.
- Seleccionar **Crear trabajo** y administrar las bases de datos del grupo mediante la creación de trabajos elásticos. Los trabajos elásticos le permiten ejecutar scripts de Transact-SQL con cualquier número de bases de datos en el grupo. Para obtener más información, vea [Información general de los trabajos de bases de datos elásticas](sql-database-elastic-jobs-overview.md).
- Seleccione **Administrar trabajos** para administrar los trabajos elásticos existentes.



![Supervisión de grupos elásticos][8]




![Supervisión de grupos elásticos][4]

Al seleccionar un grupo existente, podrá ver la utilización de recursos del grupo. Haga clic en el gráfico **Utilización de recursos** para abrir la hoja **Métrica**, donde podrá personalizar el gráfico y configurar alertas.


![Utilización de recursos][6]

Haga clic en **Editar gráfico** para agregar parámetros para ver fácilmente los datos de telemetría del grupo.


![Edición de gráficos][7]




## Pasos siguientes
Después de crear un grupo de bases de datos elásticas, puede administrar las bases de datos del grupo creando trabajos elásticos. Los trabajos elásticos facilitan la ejecución de secuencias de comandos de Transact-SQL con cualquier número de bases de datos en el grupo. Para obtener más información, vea [Información general sobre los trabajos de bases de datos elásticas](sql-database-elastic-jobs-overview.md).



## Recursos adicionales

- [Grupo elástico de bases de datos SQL](sql-database-elastic-pool.md)
- [Creación de un grupo elástico de bases de datos SQL con PowerShell](sql-database-elastic-pool-powershell.md)
- [Creación y administración de Base de datos SQL con C#](sql-database-client-library.md)
- [Referencia de bases de datos elásticas](sql-database-elastic-pool-reference.md)


<!--Image references-->
[1]: ./media/sql-database-elastic-pool-portal/new-elastic-pool.png
[2]: ./media/sql-database-elastic-pool-portal/configure-elastic-pool.png
[3]: ./media/sql-database-elastic-pool-portal/configure-performance.png
[4]: ./media/sql-database-elastic-pool-portal/monitor-elastic-pool.png
[5]: ./media/sql-database-elastic-pool-portal/add-databases.png
[6]: ./media/sql-database-elastic-pool-portal/metric.png
[7]: ./media/sql-database-elastic-pool-portal/edit-chart.png
[8]: ./media/sql-database-elastic-pool-portal/configure-pool.png
[9]: ./media/sql-database-elastic-pool-portal/pricing-tier.png
[10]: ./media/sql-database-elastic-pool-portal/star.png
[11]: ./media/sql-database-elastic-pool-portal/recommended-pool.png
[12]: ./media/sql-database-elastic-pool-portal/pools-message.png

<!---HONumber=Nov15_HO4-->