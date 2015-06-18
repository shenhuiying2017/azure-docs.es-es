<properties 
	pageTitle="Creación y administración de un grupo de bases de datos SQL elásticas (vista previa)" 
	description="Creación de un único grupo de recursos para compartir en un grupo de bases de datos SQL de Azure" 
	services="sql-database" 
	documentationCenter="" 
	authors="stevestein" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="04/29/2015" 
	ms.author="sstein" 
	ms.workload="data-management" 
	ms.topic="article" 
	ms.tgt_pltfrm="NA"/>


# Creación y administración de un grupo de bases de datos SQL elásticas

> [AZURE.SELECTOR]
- [Azure portal](sql-database-elastic-pool-portal.md)
- [PowerShell](sql-database-elastic-pool-powershell.md)

Este artículo muestra cómo crear un grupo elástico con el [Portal de Azure](https://portal.azure.com).

Los grupos elásticos simplifican el proceso de creación, mantenimiento y administración del rendimiento y el coste de numerosas bases de datos.
 

> [AZURE.NOTE]Los grupos elásticos están actualmente en vista previa y solo estarán disponibles en servidores con bases de datos SQL V12.

 


## Requisitos previos

Para crear un grupo elástico necesita lo siguiente:

- Una suscripción de Azure. Si necesita una suscripción a Azure, simplemente haga clic en la opción PRUEBA GRATUITA situada en la parte superior de esta página y, a continuación, vuelva para finalizar este artículo.
- Un servidor de base de datos SQL de Azure versión 12. Si no tiene un servidor V12, siga los pasos siguientes para crear uno: [Creación de la primera base de datos SQL de Azure](sql-database-get-started.md).



## Creación de un grupo elástico

Para crear un grupo elástico, agregue un nuevo grupo a un servidor. Puede agregar varios grupos a un servidor, pero solo se puede asociar 1 servidor a cada grupo. Además, es posible agregar a un grupo todas o algunas de las bases de datos de un servidor.


1.	Seleccione un servidor de base de datos SQL V12 que contenga las bases de datos que desea agregar al grupo.
2.	Cree el grupo seleccionando **Agregar grupo** en la parte superior de la hoja **SQL Server**.

   ![Creación de grupos elásticos][1]

## Configuración de un grupo elástico

Configure el grupo estableciendo el nivel de precios, agregando las bases de datos y configurando las características de rendimiento del grupo.

*Cuando se selecciona el comando **Agregar grupo**, debe aceptar los términos de la vista previa seleccionando el comando **TÉRMINOS DE VISTA PREVIA** y completando la hoja **Términos de vista previa**. Solo debe aceptar los términos una vez para cada suscripción.*

   ![Configuración de grupos elásticos][2]


### Nivel de precios

El nivel de precios de un grupo elástico se parece al nivel de servicio de la base de datos SQL. El nivel de precios determina las características disponibles para las bases de datos elásticas del grupo, así como el número máximo de DTU (DTU MÁX.) y el almacenamiento (en GB) disponible para cada base de datos.

> [AZURE.NOTE]La vista previa está actualmente está limitada al nivel de precios **Standard**.

| Nivel de precios | Número máximo de DTU por base de datos |
| :--- | :--- |
| Standard | Número máximo de 100 DTU por base de datos |

### Adición de bases de datos

En cualquier momento, puede seleccionar las bases de datos específicas que desea incluir en el grupo. Cuando cree un nuevo grupo, Azure recomendará y marcará las bases de datos más indicadas para su inclusión en el grupo. Puede agregar todas las bases de datos disponibles en el servidor, o bien puede activar o desactivar las bases de datos de la lista inicial como desee.

   ![Adición de bases de datos][5]

Cuando selecciona una base de datos para agregarla a un grupo, deben cumplirse las condiciones siguientes:

- El grupo debe tener espacio para la base de datos (no puede contener el número máximo de bases de datos). Más específicamente, el grupo debe tener suficiente DTU disponibles para cubrir el número de DTU por base de datos garantizado (por ejemplo, si el número de DTU garantizado para el grupo es de 400 y se garantizan 10 DTU, el número máximo de bases de datos que se permiten en el grupo es de 40 (400 DTU/10 DTU garantizados por base de datos = 40 bases de datos como máximo).
- Las características actuales que utiliza la base de datos deben estar disponibles en el grupo. 


### Configuración del rendimiento

El rendimiento del grupo se configura estableciendo los parámetros de rendimiento para el grupo y las bases de datos elásticas del grupo. Tenga en cuenta que la **Configuración de bases de datos elásticas** se aplican a todas las bases de datos del grupo.

   ![Configuración de grupos elásticos][3]

Existen tres parámetros que puede establecer y que definen el rendimiento del grupo: el número de DTU garantizados para el grupo, el número mínimo de DTU y el número máximo de DTU de las bases de datos flexibles del grupo. La tabla siguiente describe cada uno de estos parámetros y ofrece algunas indicaciones sobre cómo configurarlos.

| Parámetro de rendimiento | Descripción |
| :--- | :--- |
| **POOL DTU/GB**: número de DTU garantizados para el grupo. | El número de DTU garantizados para el grupo es el número garantizado de DTU disponibles y compartidos en el grupo. <br> Actualmente, puede establecer este valor en 200, 400, 800 o 1200. <br> El número específico de DTU garantizados para un grupo debe aprovisionarse teniendo en cuenta el historial de uso de DTU del grupo. De manera alternativa, este valor se puede establecer según la cantidad de DTU garantizados por base de datos según el uso de bases de datos activas al mismo tiempo. El número de DTU garantizados del grupo también guarda relación con la cantidad de almacenamiento disponible para el grupo, ya que por cada DTU asignado al grupo se obtiene 1 GB de almacenamiento de base de datos (1 DTU = 1 GB de almacenamiento). <br> **¿Con qué valor debo establecer el número de DTU garantizados del grupo?** <br>Como mínimo, debe establecer el número de DTU garantizados del grupo con un valor equivalente a la operación siguiente: [n.º de bases de datos] x [promedio de uso de DTU por base de datos]. |
| **DTU MIN**: número de DTU garantizados para cada base de datos | El número mínimo de DTU garantizado por base de datos es el número de DTU que tiene garantizado cada base de datos del grupo. Actualmente, se puede establecer este valor en 0, 10, 20 o 50 DTU. O bien, puede elegir no ofrecer ningún número de DTU garantizado a las bases de datos del grupo (DTU MIN = 0). <br>**¿Con qué valor debo establecer el número de DTU garantizados para cada base de datos?** <br> Como mínimo, debe establecer el número de DTU garantizados de cada base de datos (DTU MIN) con un valor equivalente al [promedio de utilización de cada base de datos]. El número de DTU garantizados por cada base de datos es un ajuste global que establece el número de DTU garantizados de todas las bases de datos del grupo. |
| **DTU MAX**: capacidad de DTU por cada base de datos | El valor DTU MAX de cada base de datos es el número máximo de DTU que puede tener cada una de las bases de datos del grupo. Establezca la capacidad de DTU de cada base de datos con un valor lo suficientemente alto como para soportar los altos y bajos de demanda de las bases de datos. Puede establecer la capacidad con el valor límite del sistema, que dependerá del nivel de precios del grupo (100 DTU para el nivel Standard). La capacidad específica debe soportar los picos de utilización de las bases de datos del grupo. Se admite un cierto grado de exceso de asignación de recursos, ya que el grupo suele basarse en patrones de uso en frío y caliente de las bases de datos, cuando en realidad los picos de demanda no tienen lugar en todas las bases de datos a la vez.<br> **¿Con qué valor debo establecer la capacidad de DTU para cada base de datos?** <br> Establezca el valor de DTU MAX o de capacidad de DTU por cada base de datos con un valor equivalente a [al pico de demanda de las bases de datos]. Por ejemplo, suponga que el pico de utilización de cada base de datos es de 50 DTU y solo el 20% de las 100 bases de datos del grupo registran simultáneamente un pico de rendimiento. Si la capacidad de DTU de cada base de datos se establece en 50 DTU, deberá asignar una cantidad de recursos 5 veces mayor y establecer el número de DTU garantizados para el grupo en 1000 DTU. También debe tener en cuenta que la capacidad de DTU no es una garantía de recursos para las bases de datos, sino que es un valor máximo que puede llegar a alcanzarse si está disponible. |


## Adición de bases de datos a un grupo elástico

Una vez creado el grupo, puede agregar o quitar bases de datos dentro y fuera del grupo.


## Supervisión y administración de un grupo elástico

Después de crear el grupo elástico, podrá supervisar y administrar el grupo en el portal examinando la lista de grupos existentes y seleccionando el grupo deseado.

Después de crear un grupo, podrá:

- Seleccionar la opción **Configurar grupo** para cambiar el número de DTU del grupo y el número de DTU por cada base de datos.
- Seleccionar **Crear trabajo** y administrar las bases de datos del grupo mediante la creación de trabajos elásticos. Los trabajos elásticos facilitan la ejecución de secuencias de comandos de T-SQL con cualquier número de bases de datos en el grupo. Para obtener más información, vea [Información general de los trabajos de bases de datos elásticas](sql-database-elastic-jobs-overview.md).
- Seleccione **Administrar trabajos** para administrar los trabajos elásticos existentes.



![Supervisión de grupos elásticos][8]




![Supervisión de grupos elásticos][4]

Al seleccionar un grupo existente, podrá ver la utilización de recursos del grupo. Haga clic en el gráfico **Utilización de recursos** para abrir la hoja **Métrica**, donde podrá personalizar el gráfico y configurar alertas.


![Utilización de recursos][6]

Haga clic en **Editar gráfico** para agregar parámetros para ver fácilmente los datos de telemetría del grupo.


![Edición de gráficos][7]







## Pasos siguientes
Tras la creación de un grupo flexible, puede administrar las bases de datos del grupo mediante la creación de trabajos elásticos. Los trabajos elásticos facilitan la ejecución de secuencias de comandos de T-SQL con cualquier número de bases de datos en el grupo.

Para obtener más información, vea [Información general sobre los trabajos de bases de datos elásticas](sql-database-elastic-jobs-overview.md).



## Temas relacionados

- [Grupo elástico de bases de datos SQL](sql-database-elastic-pool.md)
- [Creación de un grupo elástico de bases de datos SQL con PowerShell](sql-database-elastic-pool-powershell.md)
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

<!---HONumber=58--> 