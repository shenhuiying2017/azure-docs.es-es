---
title: Supervisión y administración de un grupo de bases de datos elásticas con el Portal de Azure | Microsoft Docs
description: Obtenga más información sobre cómo usar la inteligencia integrada de la Base de datos SQL y el Portal de Azure con el objetivo de administrar y supervisar un grupo de bases de datos elásticas escalables, así como identificar su tamaño más adecuado, con el objetivo de optimizar el rendimiento de las bases de datos y administrar los costos.
keywords: ''
services: sql-database
documentationcenter: ''
author: ninarn
manager: jhubbard
editor: cgronlun

ms.service: sql-database
ms.devlang: NA
ms.date: 09/22/2016
ms.author: ninarn
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA

---
# <a name="monitor-and-manage-an-elastic-database-pool-with-the-azure-portal"></a>Supervisión y administración de un grupo de bases de datos elásticas con el Portal de Azure
> [!div class="op_single_selector"]
> * [Portal de Azure](sql-database-elastic-pool-manage-portal.md)
> * [PowerShell](sql-database-elastic-pool-manage-powershell.md)
> * [C#](sql-database-elastic-pool-manage-csharp.md)
> * [T-SQL](sql-database-elastic-pool-manage-tsql.md)
> 
> 

Puede utilizar el Portal de Azure para supervisar y administrar un grupo de bases de datos elásticas y las bases de datos del grupo. En el portal, puede supervisar el uso de un grupo elástico y las bases de datos dentro de ese grupo. También puede realizar un conjunto de cambios en el grupo elástico y enviar todos los cambios a la vez. Estos cambios incluyen agregar o quitar bases de datos, cambiar la configuración del grupo elástico o cambiar la configuración de la base de datos.

El siguiente gráfico muestra un grupo elástico de ejemplo. La vista incluye:

* Los gráficos para supervisar el uso de recursos del grupo elástico y las bases de datos contenidas en el grupo.
* El botón **Configurar grupo** para realizar cambios en el grupo elástico.
* El botón **Crear base de datos** , que crea una base de datos y la agrega al grupo elástico actual.
* Los trabajos elásticos que le ayudarán a administran un gran número de bases de datos mediante la ejecución de scripts de Transact SQL en todas las bases de datos de una lista.

![Vista Grupo][2]

Para trabajar con los pasos de este artículo, necesitará SQL server en Azure con, al menos, una base de datos y un grupo elástico. Si no tiene ningún grupo elástico, consulte [Creación de un nuevo grupo de bases de datos elásticas](sql-database-elastic-pool-create-portal.md); si no tiene ninguna base de datos, consulte el [tutorial de base de datos SQL](sql-database-get-started.md).

## <a name="elastic-pool-monitoring"></a>Supervisión de grupo elástico
Puede ir a un grupo concreto para ver su utilización de recursos. De forma predeterminada, el grupo está configurado para mostrar el almacenamiento y el uso de eDTU durante la última hora. El gráfico puede configurarse para mostrar métricas diferentes en varias ventanas de tiempo.

1. Seleccione un grupo con el que desea trabajar.
2. En **Supervisión de grupo elástico** hay un gráfico con la etiqueta **Uso de recursos**. Haga clic en el gráfico.
   
    ![Supervisión de grupo elástico][3]
   
    Se abre la hoja **Métrica** , donde se muestra una vista detallada de las métricas especificadas en la ventana de tiempo especificada.   
   
    ![Cuadro de métricas][9]

### <a name="to-customize-the-chart-display"></a>Personalización de la visualización del gráfico
Puede editar el gráfico y la hoja de métricas para mostrar otras métricas, como el porcentaje de CPU, el porcentaje de E/S de datos y el porcentaje de E/S de registro usados.

1. En la hoja de métricas, haga clic en **Editar**.
   
    ![Haga clic en Editar.][6]

* En la hoja **Editar gráfico**, seleccione un nuevo intervalo de tiempo (última hora, hoy o última semana) o haga clic en **personalizado** para seleccionar cualquier intervalo de fechas dentro de las últimas dos semanas. Seleccione el tipo de gráfico (de barras o líneas) y, después, seleccione los recursos que se supervisarán.

> Nota: Solo las métricas con la misma unidad de medida se pueden mostrar en el gráfico al mismo tiempo. Por ejemplo, si selecciona el "porcentaje de eDTU", solo podrá seleccionar otras métricas con porcentaje como unidad de medida.
> 
> 

    ![Click edit](./media/sql-database-elastic-pool-manage-portal/edit-chart.png)

* A continuación, haga clic en **Aceptar**.

## <a name="elastic-database-monitoring"></a>Supervisión de la base de datos elástica
También se pueden supervisar bases de datos individuales en caso de un problema potencial.

1. En **Supervisión de base de datos elástica**hay un gráfico que muestra las métricas para cinco bases de datos. De forma predeterminada, el gráfico muestra las 5 bases de datos principales en el grupo por uso promedio de eDTU durante la última hora. Haga clic en el gráfico.
   
    ![Supervisión de grupo elástico][4]
2. Aparece la hoja **Database Resource Utilization** (Uso de recursos de bases de datos). Esto proporciona una vista detallada del uso de las bases de datos en el grupo. Mediante la cuadrícula situada en la parte inferior de la hoja, puede seleccionar las bases de datos del grupo para mostrar su uso en el gráfico (hasta cinco bases de datos). También puede personalizar la ventana de tiempo y métricas que se muestra en el gráfico; para ello, haga clic en **Editar gráfico**.
   
    ![Hoja de utilización de recursos de base de datos][8]

### <a name="to-customize-the-view"></a>Personalización de la vista
1. En la hoja **Database Resource Utilization** (Uso de recursos de bases de datos), haga clic en **Editar gráfico**.
   
    ![Clic en Editar gráfico](./media/sql-database-elastic-pool-manage-portal/db-utilization-blade.png)
2. En la hoja **Editar gráfico**, seleccione un nuevo intervalo de tiempo (última hora o 24 horas) o haga clic en **Personalizado** para seleccionar otro día dentro de las últimas dos semanas que se mostrará.
   
    ![Clic en Personalizado](./media/sql-database-elastic-pool-manage-portal/editchart-date-time.png)
3. Haga clic en la lista desplegable **Compare databases by** (Comparar bases de datos por) para seleccionar otra métrica que se usará al comparar bases de datos.
   
    ![Edición del gráfico](./media/sql-database-elastic-pool-manage-portal/edit-comparison-metric.png)

### <a name="to-select-databases-to-monitor"></a>Selección de las bases de datos que se supervisarán
En la lista de bases de datos en la hoja **Database Resource Utilization** (Uso de recursos de bases de datos) puede encontrar bases de datos específicas consultando las páginas de la lista o escribiendo el nombre de una base de datos. Utilice la casilla para seleccionar la base de datos.

![Búsqueda de las bases de datos que se supervisarán][7]

## <a name="add-an-alert-to-a-pool-resource"></a>Adición de una alerta a un grupo de recursos
Puede agregar reglas a los recursos que envían correos electrónicos a personas o cadenas de alerta a puntos de conexión de URL cuando el recurso alcanza un umbral de uso que establezca.

> [!IMPORTANT]
> La supervisión del uso de recursos de grupos elásticos tiene un retraso de, al menos, 20 minutos. En estos momentos, no se pueden establecer alertas de menos de 30 minutos para grupos elásticos. Es posible que no se desencadenen las alertas establecidas para grupos elásticos con un periodo inferior a 30 minutos (parámetro denominado "WindowSize" en la API de PowerShell). Asegúrese de que las alertas que defina para grupos elásticos utilicen un periodo (WindowSize) de 30 minutos o más.
> 
> 

**Para agregar una alerta para cualquier recurso, siga estos pasos:**

1. Haga clic en el gráfico **Uso de recursos** para abrir la hoja **Métrica**, haga clic en **Agregar alerta** y rellene la información de la hoja **Agregar una regla de alerta** (el campo **Recurso** se establece automáticamente en el grupo con el que está trabajando).
2. Escriba un **nombre** y una **descripción** que identifique la alerta de cara a usted y a los destinatarios.
3. Elija en la lista la **Métrica** para la que quiera que se generen alertas.
   
    El gráfico muestra dinámicamente el uso de recursos relativos a esa métrica a fin de ayudarle a elegir un umbral.
4. Elija una **condición** (mayor que, menor que, etc.) y un **umbral**.
5. Haga clic en **Aceptar**.

## <a name="move-a-database-into-an-elastic-pool"></a>Movimiento de una base de datos a un grupo elástico
Puede agregar o quitar las bases de datos de un grupo existente. Las bases de datos pueden encontrarse en otros grupos. Sin embargo, solo puede agregar bases de datos que estén en el mismo servidor lógico.

1. En la hoja del grupo, en la opción **Bases de datos elásticas**, haga clic en **Configurar grupo**.
   
    ![Haga clic en Configurar grupo.][1]
2. En la hoja **Configurar grupo**, haga clic en **Agregar al grupo**.
   
    ![Haga clic en Agregar para agregar al grupo.](./media/sql-database-elastic-pool-manage-portal/add-to-pool.png)
3. En la hoja **Agregar bases de datos** , seleccione las bases de datos que agregará al grupo. Después, haga clic en **Seleccionar**.
   
    ![Seleccione las bases de datos que desea agregar.](./media/sql-database-elastic-pool-manage-portal/add-databases-pool.png)
   
    La hoja **Configurar grupo** muestra ahora la base de datos que ha seleccionado para agregarla, con el estado establecido como **Pendiente**.
   
    ![Adiciones de grupo pendientes.](./media/sql-database-elastic-pool-manage-portal/pending-additions.png)
4. En la hoja **Configurar grupo**, haga clic en **Guardar**.
   
    ![Haga clic en Guardar](./media/sql-database-elastic-pool-manage-portal/click-save.png)

## <a name="move-a-database-out-of-an-elastic-pool"></a>Movimiento de una base de datos fuera de un grupo elástico
1. En la hoja **Configurar grupo** , seleccione las bases de datos que quitará.
   
    ![lista de bases de datos](./media/sql-database-elastic-pool-manage-portal/select-pools-removal.png)
2. Haga clic en **Quitar del grupo**.
   
    ![lista de bases de datos](./media/sql-database-elastic-pool-manage-portal/click-remove.png)
   
    La hoja **Configurar grupo** muestra ahora la base de datos que ha seleccionado para quitarla, con el estado establecido como **Pendiente**.
   
    ![Adición de base de datos de vista previa y eliminación](./media/sql-database-elastic-pool-manage-portal/pending-removal.png)
3. En la hoja **Configurar grupo**, haga clic en **Guardar**.
   
    ![Haga clic en Guardar](./media/sql-database-elastic-pool-manage-portal/click-save.png)

## <a name="change-performance-settings-of-a-pool"></a>Cambio de la configuración de rendimiento de un grupo
Cuando supervise el uso de recursos de un grupo, es posible que descubra que son necesarios algunos ajustes. Quizás el grupo necesita un cambio en los límites de almacenamiento o rendimiento. Posiblemente desee cambiar la configuración de la base de datos en el grupo. Puede cambiar la configuración del grupo en cualquier momento para obtener el mejor equilibrio entre rendimiento y costo. Consulte [¿Cuándo se debe utilizar un grupo de bases de datos elásticas?](sql-database-elastic-pool-guidance.md) para obtener más información.

**Para cambiar los límites de almacenamiento o eDTU por grupo y las eDTU por base de datos:**

1. Abra la hoja **Configurar grupo** .
   
    En **Configuración del grupo de bases de datos elásticas**, utilice el control deslizante para cambiar la configuración del grupo.
   
    ![Uso de recursos de grupos elásticos](./media/sql-database-elastic-pool-manage-portal/resize-pool.png)
2. Cuando se cambia la configuración, la pantalla muestra el coste estimado mensual del cambio.
   
    ![Actualización de un grupo y nuevo costo mensual](./media/sql-database-elastic-pool-manage-portal/pool-change-edtu.png)

## <a name="create-and-manage-elastic-jobs"></a>Creación y administración de trabajos elásticos
Los trabajos elásticos le permiten ejecutar scripts de Transact-SQL con cualquier número de bases de datos en el grupo. Puede crear tareas o administrar trabajos existentes mediante el portal.

![Creación y administración de trabajos elásticos][5]

Antes de usar los trabajos, instale los componentes de trabajos elásticos y especifique sus credenciales. Para obtener más información, vea [Información general sobre los trabajos de bases de datos elásticas](sql-database-elastic-jobs-overview.md).

Consulte [Escalado horizontal con Base de datos SQL de Azure](sql-database-elastic-scale-introduction.md): use herramientas de bases de datos elásticas para realizar un escalado horizontal, mover los datos, realizar consultas o crear transacciones.

## <a name="additional-resources"></a>Recursos adicionales
* [Grupo elástico de bases de datos SQL](sql-database-elastic-pool.md)
* [Creación de un grupo de bases de datos elásticas con el portal](sql-database-elastic-pool-create-csharp.md)
* [Creación de un grupo de bases de datos elásticas con PowerShell](sql-database-elastic-pool-create-powershell.md)
* [Creación de un grupo de bases de datos elásticas con C#](sql-database-elastic-pool-create-csharp.md)
* [Consideraciones de precio y rendimiento para grupos de bases de datos elásticas](sql-database-elastic-pool-guidance.md)

<!--Image references-->
[1]: ./media/sql-database-elastic-pool-manage-portal/configure-pool.png
[2]: ./media/sql-database-elastic-pool-manage-portal/basic.png
[3]: ./media/sql-database-elastic-pool-manage-portal/basic-2.png
[4]: ./media/sql-database-elastic-pool-manage-portal/basic-3.png
[5]: ./media/sql-database-elastic-pool-manage-portal/elastic-jobs.png
[6]: ./media/sql-database-elastic-pool-manage-portal/edit-metric.png
[7]: ./media/sql-database-elastic-pool-manage-portal/select-dbs.png
[8]: ./media/sql-database-elastic-pool-manage-portal/db-utilization.png
[9]: ./media/sql-database-elastic-pool-manage-portal/metric.png



<!--HONumber=Oct16_HO2-->


