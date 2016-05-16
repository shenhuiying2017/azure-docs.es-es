<properties
	pageTitle="Supervisión y administración de un grupo de bases de datos elásticas con el Portal de Azure | Microsoft Azure"
	description="Obtenga más información sobre cómo usar la inteligencia integrada de la Base de datos SQL y el Portal de Azure con el objetivo de administrar y supervisar un grupo de bases de datos elásticas escalables, así como identificar su tamaño más adecuado, con el objetivo de optimizar el rendimiento de las bases de datos y administrar los costos."
	keywords=""
	services="sql-database"
	documentationCenter=""
	authors="ninarn"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="05/02/2016"
	ms.author="ninarn"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Supervisión y administración de un grupo de bases de datos elásticas con el Portal de Azure

> [AZURE.SELECTOR]
- [Portal de Azure](sql-database-elastic-pool-manage-portal.md)
- [PowerShell](sql-database-elastic-pool-manage-powershell.md)
- [C#](sql-database-elastic-pool-manage-csharp.md)
- [T-SQL](sql-database-elastic-pool-manage-tsql.md)


Puede utilizar el portal de Azure para supervisar, administrar y configurar un grupo de bases de datos elásticas y las bases de datos del grupo. Base de datos SQL tiene inteligencia integrada que analiza el uso histórico de todas las bases de datos de un servidor (con independencia de que estén en grupos o no) y recomienda un grupo de bases de datos cuando sea más rentable.

El portal permite cambiar la configuración de grupos y bases de datos, obtener una vista previa de los cambios y, después, confirmar todos los cambios al mismo tiempo. Puede obtener una vista previa de los cambios, como agregar y quitar bases de datos. También se mostrará el posible impacto en el rendimiento y en los precios.

Para seguir los pasos de este artículo, necesitará algunas bases de datos y un grupo. Si ya tiene bases de datos, consulte [Creación de un grupo de bases de datos elásticas con el Portal de Azure](sql-database-elastic-pool-create-portal.md); si no dispone de una base de datos, consulte [Tutorial de Base de datos SQL: creación de una Base de datos SQL en cuestión de minutos con datos de ejemplo y el Portal de Azure](sql-database-get-started.md).

## Seleccione un grupo con el que desea trabajar.

1. En el [Portal de Azure](https://portal.azure.com), haga clic en **Examinar**.
2. Haga clic en **Grupos elásticos de SQL**.
3. En la lista, haga clic en el grupo con el que desea trabajar.

## Movimiento de una base de datos a un grupo elástico

Puede agregar o quitar las bases de datos de un grupo existente. Las bases de datos pueden encontrarse en otros grupos. Sin embargo, solo puede agregar bases de datos que estén en el mismo servidor lógico.

1. En la hoja del grupo, en la opción **Bases de datos elásticas**, haga clic en **Configurar grupo**.

    ![Haga clic en Configurar grupo.][1]

2. En la hoja **Configurar grupo**, haga clic en **Add to pool** (Agregar al grupo).

	![Haga clic en Agregar al grupo.](./media/sql-database-elastic-pool-manage-portal/add-to-pool.png)

	
3. En la hoja **Agregar bases de datos**, seleccione las bases de datos que agregará al grupo. Después, haga clic en **Seleccionar**.

	![Seleccione las bases de datos que desea agregar.](./media/sql-database-elastic-pool-manage-portal/add-databases-pool.png)

    La hoja **Configurar grupo** muestra ahora, con el estado **Pendiente**, la base de datos que acaba de agregar.

    ![Adiciones de grupo pendientes.](./media/sql-database-elastic-pool-manage-portal/pending-additions.png)

3. En la hoja "Configurar grupo", haga clic en **Guardar**.

    ![Haga clic en Guardar](./media/sql-database-elastic-pool-manage-portal/click-save.png)

## Movimiento de una base de datos fuera de un grupo elástico

1. En la hoja **Configurar grupo**, seleccione las base de datos que quitará.

    ![lista de bases de datos](./media/sql-database-elastic-pool-manage-portal/select-pools-removal.png)

2. Haga clic en **Quitar del grupo**.

    ![lista de bases de datos](./media/sql-database-elastic-pool-manage-portal/remove-from-pool.png)

	Las bases de datos seleccionadas aparecerán en la interfaz de usuario como "databases selected to be removed" (bases de datos seleccionadas para quitarse).


## Supervisión del uso de recursos de un grupo


1. Seleccione un grupo con el que desea trabajar.
2. En **Supervisión de grupo elástico**, un gráfico e iconos dinámicos mostrarán información de uso importante del grupo.

![Supervisión de grupos elásticos](./media/sql-database-elastic-pool-manage-portal/monitor-elastic-pool.png)

**Para cambiar el gráfico y la visualización, siga estos pasos:**

- Haga clic en **Editar**.

	![Haga clic en Editar.](./media/sql-database-elastic-pool-manage-portal/edit-resource-utlization.png)

- En la hoja **Editar gráfico**, seleccione un nuevo intervalo de tiempo (última hora, hoy o última semana) o haga clic en **personalizado** para establecer un intervalo de tiempo diferente. Seleccione el tipo de gráfico (de barras o líneas) y, después, seleccione los recursos que se supervisarán.

	![Haga clic en Editar.](./media/sql-database-elastic-pool-manage-portal/edit-chart.png)

- A continuación, haga clic en **Aceptar**.


## Adición de una alerta a un grupo de recursos

Puede agregar reglas a los recursos que envían correos electrónicos a personas o cadenas de alerta a puntos de conexión de URL cuando el recurso alcanza un umbral de uso que establezca.

**Para agregar una alerta para cualquier recurso, siga estos pasos:**

1. Haga clic en el gráfico **Uso de recursos** para abrir la hoja **Métrica**, haga clic en **Agregar alerta** y, después, rellene la información de la hoja **Agregar una regla de alerta** (el campo **Recurso** se establece automáticamente en el grupo con el que está trabajando).
2. Escriba un **nombre** y una **descripción** para la alerta que resulten significativos para usted y los destinatarios.
3. Elija una **métrica** de la que quiera que se generen alertas en la lista.

    El gráfico muestra dinámicamente el uso de recursos relativos a esa métrica a fin de ayudarle a elegir un umbral.

4. Elija una **condición** (mayor que, menor que, etc.) y un **umbral**.
5. Haga clic en **Aceptar**.

## Cambio de la configuración de rendimiento de un grupo

Cuando supervise el uso de recursos de un grupo, es posible que descubra que el grupo necesita una eDTU más. También puede percatarse de que las bases de datos individuales del grupo necesitan una configuración de eDTU diferente. Puede cambiar la configuración del grupo en cualquier momento para obtener el mejor equilibrio entre rendimiento y costo. Consulte [¿Cuándo se debe utilizar un grupo de bases de datos elásticas?](sql-database-elastic-pool-guidance.md) para obtener más información.

**Para cambiar las eDTU por grupo y por base de datos, siga estos pasos:**

1. Abra la hoja **Configurar grupo**.

    En **Configuración del grupo de bases de datos elásticas**, utilice los controles deslizantes para cambiar la configuración del grupo.

    ![Uso de recursos de grupos elásticos](./media/sql-database-elastic-pool-manage-portal/resize-pool.png)

2. Cuando se cambia la configuración, la pantalla muestra el coste estimado mensual del cambio.

    ![Actualización de un grupo y nuevo costo mensual](./media/sql-database-elastic-pool-manage-portal/pool-change-edtu.png)

## Vista previa de las acciones en las bases de datos

Puede obtener una vista previa de la adición o eliminación de bases de datos antes de confirmar la acción en la hoja **Configurar grupo**:

![Adición de base de datos de vista previa y eliminación](./media/sql-database-elastic-pool-manage-portal/pools-tab.png).


## Creación y administración de trabajos elásticos

Los trabajos elásticos le permiten ejecutar scripts de Transact-SQL con cualquier número de bases de datos en el grupo. Antes de usar los trabajos, instale los componentes de trabajos elásticos y especifique sus credenciales. Para obtener más información, vea [Información general sobre los trabajos de bases de datos elásticas](sql-database-elastic-jobs-overview.md).

Consulte [Escalado horizontal con Base de datos SQL de Azure](sql-database-elastic-scale-introduction.md): use herramientas de bases de datos elásticas para realizar un escalado horizontal, mover los datos, realizar consultas o crear transacciones.


## Recursos adicionales

- [Grupo elástico de bases de datos SQL](sql-database-elastic-pool.md)
- [Creación de un grupo de bases de datos elásticas con el portal](sql-database-elastic-pool-create-csharp.md)
- [Creación de un grupo de bases de datos elásticas con PowerShell](sql-database-elastic-pool-create-powershell.md)
- [Creación de un grupo de bases de datos elásticas con C#](sql-database-elastic-pool-create-csharp.md)
- [Consideraciones de precio y rendimiento para grupos de bases de datos elásticas](sql-database-elastic-pool-guidance.md)


<!--Image references-->
[1]: ./media/sql-database-elastic-pool-manage-portal/configure-pool.png

<!---HONumber=AcomDC_0504_2016-->