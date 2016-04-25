<properties
	pageTitle="Configuración del tamaño, administración y supervisión de grupos de bases de datos elásticas"
	description="Obtenga más información sobre cómo usar la inteligencia integrada de la Base de datos SQL y el Portal de Azure con el objetivo de administrar y supervisar un grupo de bases de datos elásticas escalables, así como identificar su tamaño más adecuado, con el objetivo de optimizar el rendimiento de las bases de datos y administrar los costos."
	keywords=""
	services="sql-database"
	documentationCenter=""
	authors="sidneyh"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="04/01/2016"
	ms.author="sidneyh"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Configuración del tamaño, administración y supervisión de grupos de bases de datos elásticas con el Portal de Azure

> [AZURE.SELECTOR]
- [Portal de Azure](sql-database-elastic-pool-manage-portal.md)
- [C#](sql-database-elastic-pool-manage-csharp.md)
- [PowerShell](sql-database-elastic-pool-manage-powershell.md)
- [T-SQL](sql-database-elastic-pool-manage-tsql.md)


En este artículo se describe cómo usar el Portal de Azure para supervisar y administrar un grupo de bases de datos elásticas y las bases de datos del grupo, así como identificar su tamaño correcto. Base de datos SQL es una base de datos inteligente que analiza la telemetría del historial de uso y recomienda de forma anticipada un grupo para las bases de datos cuando resulte más rentable. También puede agregar y quitar bases de datos antes de confirmar los cambios y ver el impacto de estos en el almacenamiento y el rendimiento del grupo.

Para seguir los pasos de este artículo, necesitará bases de datos y un grupo. Si ya tiene bases de datos, consulte el artículo sobre la [creación de un grupo](sql-database-elastic-pool-create-portal.md), y el [tutorial sobre bases de datos SQL](sql-database-get-started) en caso contrario.

**Para elegir un grupo con el que trabajar, siga estos pasos:**

- En el [Portal de Azure](https://portal.azure.com), haga clic en **Examinar**, en **Grupos elásticos de SQL** y después haga clic en el grupo de la lista con el que quiere trabajar.

##Supervisión del uso de recursos de un grupo
Tras seleccionar el grupo con el que se va a trabajar, en **Supervisión de grupo elástico**, un gráfico e iconos dinámicos le mostrarán información de uso importante del grupo.

![Supervisión de grupos elásticos](./media/sql-database-elastic-pool-manage-portal/monitor-elastic-pool.png)

**Para cambiar el intervalo de fechas, el tipo de gráfico (de barras o de líneas) o los recursos mostrados, siga estos pasos:**

- Haga clic en **Editar**, elija la configuración que quiera y después haga clic en **Guardar** para actualizar el gráfico.

**Para cambiar los iconos dinámicos, siga estos pasos:**

- Haga clic en **Agregar iconos** y después seleccione los iconos que quiera en la galería de iconos que aparece a la izquierda.

##Adición de una alerta a un grupo de recursos
Puede agregar reglas a los recursos que envían correos electrónicos a personas o cadenas de alerta a puntos de conexión de URL cuando el recurso alcanza un umbral de uso que establezca.

**Para agregar una alerta para cualquier recurso, siga estos pasos:**

1. Haga clic en el gráfico **Uso de recursos** para abrir la hoja **Métrica**, haga clic en **Agregar alerta** y después rellene la información de la hoja **Agregar una regla de alerta** (el campo **Recurso** se establece automáticamente en el grupo con el que está trabajando).
2. Escriba un **Nombre** y una **Descripción** para la alerta que resulten significativos para usted y los destinatarios.
3. Elija una **Métrica** de la que quiera que se generen alertas en la lista.

    El gráfico muestra dinámicamente el uso de recursos relativos a esa métrica a fin de ayudarle a elegir un umbral.

4. Elija una **Condición** (mayor que, menor que, etc.) y un **Umbral**.
5. Haga clic en **Aceptar**.

##Cambio de eDTU por grupo y eDTU de bases de datos
Cuando vea el uso de recursos de un grupo, es posible que descubra que este necesita una configuración de eDTU distinta, o bien que hay bases de datos del grupo que precisan ajustes de eDTU diferentes. Puede cambiar la configuración del grupo en cualquier momento para obtener el mejor equilibrio entre rendimiento y costo. Consulte [Consideraciones de precio y rendimiento](sql-database-elastic-pool-guidance.md) para obtener más información.

**Para cambiar la eDTU del grupo y la eDTU por base de datos, siga estos pasos:**

1. Haga clic en **Configurar grupo** para abrir la hoja **Configurar rendimiento**.

    En **Configuración del grupo de bases de datos elásticas**, un gráfico le muestra la tendencia reciente de eDTU y uso de almacenamiento en porcentaje de capacidad para el grupo.

    ![Uso de recursos de grupos elásticos](./media/sql-database-elastic-pool-manage-portal/resize-pool.png)

2. Haga clic en otra **eDTU del grupo** y verá el costo mensual estimado del cambio que quiera realizar. Además, el gráfico se actualiza para mostrar los valores de uso previsto con la nueva eDTU máxima seleccionada.

    ![Actualización de un grupo y nuevo costo mensual](./media/sql-database-elastic-pool-manage-portal/pool-change-edtu.png)

3. En **Configuración de base de datos elástica**, un gráfico de barras muestra el uso de eDTU de cada base de datos del grupo.

4. Haga clic en **eDTU máx.** para configurar el valor máximo y en **eDTU mín.** para establecer el número mínimo de eDTU de las bases de datos del grupo.

    ![Actualización del eDTU máx. y mín. de una base de datos elástica](./media/sql-database-elastic-pool-manage-portal/change-db-edtuminmax.png)

##Adición y eliminación de bases de datos

Después de crear un grupo, puede agregar bases de datos a él o quitarlas. Solo puede agregar bases de datos en el mismo servidor de SQL Server.

**Para agregar bases de datos, siga estos pasos:**

1. En la hoja del grupo, en **Bases de datos elásticas**, haga clic en el vínculo que muestra el número de bases de datos del grupo.

    ![lista de bases de datos](./media/sql-database-elastic-pool-manage-portal/db-listing.png)

2. En la hoja **Bases de datos elásticas**, haga clic en **Agregar base de datos**, en las bases de datos que quiera agregar y después en el botón **Seleccionar**.

    Ahora, en la hoja **Bases de datos elásticas** se muestra la base de datos que acaba de agregar, con **DTU media** y el uso del almacenamiento como **Tamaño (GB)**, junto con el estado **Pendiente**. Los valores de uso del grupo muestran ahora cuáles serán los **nuevos** valores si guarda los cambios.

    ![grupo recomendado](./media/sql-database-elastic-pool-manage-portal/add-remove-databases.png)

3. Haga clic en **Guardar** y después en **Aceptar** cuando el portal le indique que la solicitud se ha enviado. En la hoja del grupo se muestra el número de bases de datos contenidas en él cuando finaliza la operación.

**Para quitar las bases de datos, siga estos pasos:**

1. En la hoja del grupo, en **Bases de datos elásticas**, haga clic en el vínculo que muestra el número de bases de datos del grupo.

    ![lista de bases de datos](./media/sql-database-elastic-pool-manage-portal/db-listing.png)

2. En la hoja **Bases de datos elásticas**, en la lista de bases de datos del grupo, haga clic en las bases de datos que quiere quitar y después haga clic en **Eliminar bases de datos**.

    Los valores de uso del grupo muestran ahora cuáles serán los **nuevos** valores si guarda los cambios.

3. Haga clic en **Guardar** y después en **Aceptar** cuando el portal le indique que la solicitud se ha enviado. En la hoja del grupo se muestra el número de bases de datos contenidas en él cuando finaliza la operación.

## Creación de una nueva base de datos en un grupo

En la hoja de la base de datos, haga clic en **Crear base de datos**. La base de datos SQL ya está configurada para el servidor y el grupo correctos, así que haga clic para ajustar otras opciones y después seleccione **Aceptar** para crear una nueva base de datos en el grupo.

   ![creación de bases de datos elásticas](./media/sql-database-elastic-pool-portal/create-database.png)

##Creación y administración de trabajos elásticos

Los trabajos elásticos le permiten ejecutar scripts de Transact-SQL con cualquier número de bases de datos en el grupo. Antes de usar los trabajos, instale los componentes de trabajos elásticos y especifique sus credenciales. Para obtener más información, vea [Información general sobre los trabajos de bases de datos elásticas](sql-database-elastic-jobs-overview.md).

## Recursos adicionales

- [Grupo elástico de bases de datos SQL](sql-database-elastic-pool.md)
- [Creación de un grupo de bases de datos elásticas con el portal](sql-database-elastic-pool-create-csharp.md)
- [Creación de un grupo de bases de datos elásticas con PowerShell](sql-database-elastic-pool-create-powershell.md)
- [Creación de un grupo de bases de datos elásticas con C#](sql-database-elastic-pool-create-csharp.md)
- [Consideraciones de precio y rendimiento para grupos de bases de datos elásticas](sql-database-elastic-pool-guidance.md)

<!---HONumber=AcomDC_0413_2016-->