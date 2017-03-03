---
title: "Azure Portal: Creación y administración de un grupo elástico de SQL Database | Microsoft Docs"
description: "Aprenda a usar la inteligencia integrada de Azure Portal y SQL Database para administrar y supervisar un grupo elástico escalable, así como para identificar su tamaño más adecuado, con el objetivo de optimizar el rendimiento de las bases de datos y administrar los costes."
keywords: 
services: sql-database
documentationcenter: 
author: ninarn
manager: jhubbard
editor: cgronlun
ms.assetid: 3dc9b7a3-4b10-423a-8e44-9174aca5cf3d
ms.service: sql-database
ms.custom: multiple databases
ms.devlang: NA
ms.date: 11/17/2016
ms.author: ninarn
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: dbf337a27c43fc6c91f1b061a1938c5471dd36a4
ms.openlocfilehash: 52cc3c74e05dc3934e0536dea02b4870f6ed86c2
ms.lasthandoff: 02/16/2017


---
# <a name="create-and-manage-an-elastic-pool-with-the-azure-portal"></a>Creación y administración de un grupo elástico con Azure Portal
En este tema se muestra cómo crear y administrar [grupos elásticos](sql-database-elastic-pool.md) escalables con Azure Portal. También puede crear y administrar un grupo elástico de Azure en [PowerShell](sql-database-elastic-pool-manage-powershell.md), la API de REST o [C#][Creación y administración de un grupo elástico con C#](sql-database-elastic-pool-manage-csharp.md). También puede crear y mover bases de datos dentro y fuera de los grupos elásticos mediante [Transact-SQL](sql-database-elastic-pool-manage-tsql.md).

## <a name="create-an-elastic-pool"></a>Creación de un grupo elástico 

Hay dos maneras de crear un grupo elástico. Puede partir de cero si conoce la configuración deseada del grupo, o comenzar con una recomendación del servicio. Base de datos SQL es una base de datos inteligente que recomienda la configuración de grupo elástico más rentable, en función de los datos de telemetría de uso pasados de las bases de datos.

Puede crear varios grupos a un servidor, pero no puede agregar bases de datos de servidores diferentes al mismo grupo. 

> [!NOTE]
> Los grupos elásticos están disponibles con carácter general (GA) en todas las regiones de Azure excepto oeste de la India, donde actualmente se encuentran en versión preliminar.  La disponibilidad general de grupos elásticos en esta región se producirá tan pronto como sea posible.
>
>

### <a name="step-1-create-an-elastic-pool"></a>Paso 1: Crear un grupo elástico

Crear un grupo elástico a partir de una hoja de **servidor** que ya existe en el portal es la forma más sencilla de mover bases de datos existentes a un grupo elástico.

> [!NOTE]
> También puede crear un grupo elástico si busca **grupo elástico de SQL** en **Marketplace** o hace clic en **+Agregar** en la hoja para examinar **Grupos elásticos de SQL**. Puede especificar un servidor nuevo o existente por medio de este flujo de trabajo de aprovisionamiento de grupo.
>
>

1. En [Azure Portal](http://portal.azure.com/), haga clic en **Más servicios** **>** **Servidores SQL Server** y, luego, en el servidor que contiene las bases de datos que desea agregar a un grupo elástico.
2. Haga clic en **Grupo nuevo**.

    ![Adición de un grupo a un servidor](./media/sql-database-elastic-pool-create-portal/new-pool.png)

    **O**

    Quizá vea un mensaje que indica que existen grupos elásticos recomendados para el servidor (solo V12). Haga clic en el mensaje para ver los grupos recomendados según la telemetría de uso histórica de base de datos y, después, en el nivel para ver más detalles y personalizar el grupo. Consulte [Descripción de las recomendaciones de grupos](#understand-pool-recommendations) más adelante en este tema para ver cómo se realiza la recomendación.

    ![grupo recomendado](./media/sql-database-elastic-pool-create-portal/recommended-pool.png)

3. Aparece la hoja del **grupo elástico**, que es donde va a especificar la configuración del grupo. Si hizo clic en **Grupo nuevo** en el paso anterior, el plan de tarifa es **Estándar** de forma predeterminada y aún no hay bases de datos seleccionadas. Puede crear un grupo vacío o especificar un conjunto de bases de datos existentes de ese servidor para moverlas al grupo. Si va a crear un grupo recomendado, ya estarán llenos el plan de tarifa recomendado, la configuración de rendimiento y la lista de bases de datos, aunque todavía puede hacer cambios.

    ![Configuración de grupos elásticos](./media/sql-database-elastic-pool-create-portal/configure-elastic-pool.png)

4. Especifique un nombre para el grupo elástico o deje el valor predeterminado.

### <a name="step-2-choose-a-pricing-tier"></a>Paso 2: Elegir un plan de tarifa

El plan de tarifa del grupo determina las características disponibles para las bases de datos elásticas del grupo, además de la cantidad máxima de eDTU (eDTU MÁX.) y el almacenamiento (GB) disponibles para cada base de datos. Para más detalles, consulte Niveles de servicio.

Para cambiar el plan de tarifa del grupo, haga clic en **Plan de tarifa**, en el plan de tarifa que prefiera y en **Seleccionar**.

> [!IMPORTANT]
> Después de elegirlo y hacer clic en **Aceptar** en el último paso para confirmar los cambios, no podrá cambiar el plan de tarifa del grupo. Para cambiar el plan de tarifa de un grupo elástico existente, cree un grupo elástico en el plan de tarifa que quiera y migre las bases de datos al nuevo grupo.
>
>

![Seleccione un nivel de precios.](./media/sql-database-elastic-pool-create-portal/pricing-tier.png)

### <a name="step-3-configure-the-pool"></a>Paso 3: Configurar el grupo

Después de establecer el plan de tarifa, haga clic en Configurar grupo donde agregar bases de datos, establezca las eDTU y el almacenamiento (GB del grupo) y el lugar en que se establecen las eDTU mínima y máxima para las bases de datos elásticas del grupo.

1. Haga clic en **Configurar grupo**
2. Seleccione las bases de datos que desea agregar al grupo. Este paso es opcional al crear el grupo. Se pueden agregar bases de datos una vez creado el grupo.
    Para agregar bases de datos, haga clic en **Agregar base de datos**, en las bases de datos que quiera agregar y en el botón **Seleccionar**.

    ![Agregar bases de datos](./media/sql-database-elastic-pool-create-portal/add-databases.png)

    Si está trabajando con bases de datos que tienen suficiente telemetría de historial de uso, el gráfico **Estimated eDTU and GB usage** (Uso estimado de eDTU y GB) y el gráfico de barras **Actual eDTU usage** (Uso real de eDTU) se actualizan para ayudarle a tomar decisiones de configuración. Además, el servicio puede proporcionar un mensaje de recomendación que le ayuda a ajustar el tamaño correcto del grupo. Consulte [Recomendaciones dinámicas](#dynamic-recommendations).

3. Use los controles de la página **Configurar grupo** para explorar las opciones y configurar el grupo. Consulte los [límites de los grupos elásticos](sql-database-elastic-pool.md#edtu-and-storage-limits-for-elastic-pools) para ver más información sobre los límites de cada nivel de servicio y las [consideraciones sobre precios y rendimiento para los grupos elásticos](sql-database-elastic-pool-guidance.md) para ver instrucciones detalladas sobre el ajuste de tamaño correcto de un grupo elástico. Para más información sobre la configuración del grupo, consulte las [propiedades del grupo elástico](sql-database-elastic-pool.md#elastic-pool-properties).

    ![Configuración de grupos elásticos](./media/sql-database-elastic-pool-create-portal/configure-performance.png)

4. Haga clic en **Seleccionar** in the **Configure Pool** después de cambiar la configuración.
5. Haga clic en **Aceptar** para crear el grupo.


## <a name="understand-elastic-pool-recommendations"></a>Descripción de las recomendaciones de grupos elásticos

El servicio Base de datos SQL evalúa el historial de uso y recomienda uno o varios grupos cuando sea más económico que usar bases de datos únicas. Cada recomendación se configura con un subconjunto único de las bases de datos del servidor que mejor se ajustan al grupo.

![grupo recomendado](./media/sql-database-elastic-pool-create-portal/recommended-pool.png)  

La recomendación de grupo consta de:

- Un plan de tarifa del grupo (Basic, Standard o Premium).
- Las **eDTU del grupo** adecuadas (también denominadas eDTU máx. por grupo)
- Las **eDTU máx.** y **eDTU mín.** por base de datos
- La lista de bases de datos recomendadas para el grupo

> ![IMPORTANTE] El servicio tiene en cuenta los últimos 30 días de telemetría al recomendar grupos. Para que una base de datos se considere una candidata para un grupo elástico, debe tener una existencia mínima de 7 días. Las bases de datos que ya están en un grupo elástico no se consideran candidatas para las recomendaciones de grupos elásticos.
>

El servicio evalúa las necesidades de recursos y la rentabilidad de mover las bases de datos únicas de cada nivel de servicio a grupos del mismo nivel. Por ejemplo, se evalúan todas las bases de datos Standard en un servidor para que quepan en un bloque de bases de datos elásticas Standard. Esto significa que el servicio no hace recomendaciones entre niveles como, por ejemplo, mover una base de datos Standard a un grupo Premium.

Después de agregar las bases de datos al grupo, las recomendaciones se generarán dinámicamente basándose en el historial de uso de las bases de datos que se han seleccionado. Estas recomendaciones se muestran en el gráfico de uso de eDTU y GB, y en un mensaje emergente de recomendación en la parte superior de la hoja **Configurar grupo**. Estas recomendaciones están pensadas para ayudarle a crear un grupo elástico optimizado para sus bases de datos concretas.

![Recomendaciones dinámicas](./media/sql-database-elastic-pool-create-portal/dynamic-recommendation.png)

## <a name="manage-and-monitor-an-elastic-pool"></a>Administración y supervisión de un grupo elástico

Puede utilizar Azure Portal para supervisar y administrar un grupo elástico y las bases de datos de este. En el portal, puede supervisar el uso de un grupo elástico y las bases de datos dentro de ese grupo. También puede realizar un conjunto de cambios en el grupo elástico y enviar todos los cambios a la vez. Estos cambios incluyen agregar o quitar bases de datos, cambiar la configuración del grupo elástico o cambiar la configuración de la base de datos.

El siguiente gráfico muestra un grupo elástico de ejemplo. La vista incluye:

*  Los gráficos para supervisar el uso de recursos del grupo elástico y las bases de datos contenidas en el grupo.
*  El botón **Configurar grupo** para realizar cambios en el grupo elástico.
*  El botón **Crear base de datos** que crea una base de datos y la agrega al grupo elástico actual.
*  Los trabajos elásticos que le ayudarán a administran un gran número de bases de datos mediante la ejecución de scripts de Transact SQL en todas las bases de datos de una lista.

![Vista Grupo][2]

Puede ir a un grupo concreto para ver su utilización de recursos. De forma predeterminada, el grupo está configurado para mostrar el almacenamiento y el uso de eDTU durante la última hora. El gráfico puede configurarse para mostrar métricas diferentes en varias ventanas de tiempo.

1. Seleccione un grupo elástico con el que trabajar.
2. En **Supervisión de grupo elástico** hay un gráfico con la etiqueta **Uso de recursos**. Haga clic en el gráfico.

    ![Supervisión de grupo elástico][3]

    Se abre la hoja **Métrica**, donde se muestra una vista detallada de las métricas especificadas en la ventana de tiempo especificada.   

    ![Cuadro de métricas][9]

### <a name="to-customize-the-chart-display"></a>Personalización de la visualización del gráfico

Puede editar el gráfico y la hoja de métricas para mostrar otras métricas, como el porcentaje de CPU, el porcentaje de E/S de datos y el porcentaje de E/S de registro usados.

1. En la hoja de métricas, haga clic en **Editar**.

    ![Haga clic en Editar.][6]

2. En la hoja **Editar gráfico**, seleccione un intervalo de tiempo (última hora, hoy o última semana) o haga clic en **personalizado** para seleccionar cualquier intervalo de fechas dentro de las últimas dos semanas. Seleccione el tipo de gráfico (de barras o líneas) y, después, seleccione los recursos que se supervisarán.

   > [!Note]
   > Solo las métricas con la misma unidad de medida se pueden mostrar en el gráfico al mismo tiempo. Por ejemplo, si selecciona el "porcentaje de eDTU", solo podrá seleccionar otras métricas con porcentaje como unidad de medida.
   >

    ![Haga clic en Editar.](./media/sql-database-elastic-pool-manage-portal/edit-chart.png)

    

3. y, a continuación, haga clic en **Aceptar**.

## <a name="manage-and-monitor-databases-in-an-elastic-pool"></a>Administración y supervisión de bases de datos en un grupo elástico

También se pueden supervisar bases de datos individuales en caso de un problema potencial.

1. En **Supervisión de base de datos elástica**hay un gráfico que muestra las métricas para cinco bases de datos. De forma predeterminada, el gráfico muestra las 5 bases de datos principales en el grupo por uso promedio de eDTU durante la última hora. Haga clic en el gráfico.

    ![Supervisión de grupo elástico][4]

2. Aparece la hoja **Database Resource Utilization** (Uso de recursos de bases de datos). Esto proporciona una vista detallada del uso de las bases de datos en el grupo. Mediante la cuadrícula situada en la parte inferior de la hoja, puede seleccionar las bases de datos del grupo para mostrar su uso en el gráfico (hasta cinco bases de datos). También puede personalizar la ventana de tiempo y métricas que se muestra en el gráfico; para ello, haga clic en **Editar gráfico**.

    ![Hoja de utilización de recursos de base de datos][8]

### <a name="to-customize-the-view"></a>Personalización de la vista

1. En la hoja **Database Resource Utilization** (Uso de recursos de bases de datos), haga clic en **Editar gráfico**.

    ![Clic en Editar gráfico](./media/sql-database-elastic-pool-manage-portal/db-utilization-blade.png)

2. En la hoja **Editar gráfico**, seleccione un intervalo de tiempo (última hora o 24 horas) o haga clic en **Personalizado** para seleccionar otro día dentro de las últimas dos semanas que se mostrará.

    ![Clic en Personalizado](./media/sql-database-elastic-pool-manage-portal/editchart-date-time.png)

3. Haga clic en la lista desplegable **Compare databases by** (Comparar bases de datos por) para seleccionar otra métrica que se usará al comparar bases de datos.

    ![Edición del gráfico](./media/sql-database-elastic-pool-manage-portal/edit-comparison-metric.png)

### <a name="to-select-databases-to-monitor"></a>Selección de las bases de datos que se supervisarán

En la lista de bases de datos en la hoja **Database Resource Utilization** (Uso de recursos de bases de datos) puede encontrar bases de datos específicas consultando las páginas de la lista o escribiendo el nombre de una base de datos. Utilice la casilla para seleccionar la base de datos.

![Búsqueda de las bases de datos que se supervisarán][7]


## <a name="add-an-alert-to-an-elastic-pool-resource"></a>Adición de una alerta a un recurso de grupos elásticos

Puede agregar reglas a un grupo elástico que envía correos electrónicos a personas o cadenas de alerta a puntos de conexión de URL cuando el grupo elástico alcanza un umbral de uso que establezca.

> [!IMPORTANT]
> La supervisión del uso de recursos de grupos elásticos tiene un retraso de, al menos, 20 minutos. En estos momentos, no se pueden establecer alertas de menos de 30 minutos para grupos elásticos. Es posible que no se desencadenen las alertas establecidas para grupos elásticos con un periodo inferior a 30 minutos (parámetro denominado "WindowSize" en la API de PowerShell). Asegúrese de que las alertas que defina para grupos elásticos utilicen un período (WindowSize) de 30 minutos o más.
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

3. En la hoja **Configurar grupo**, haga clic en **Guardar**.

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

## <a name="change-performance-settings-of-an-elastic-pool"></a>Cambio de la configuración de rendimiento de un grupo elástico

Cuando supervise el uso de recursos de un grupo elástico, es posible que descubra que son necesarios algunos ajustes. Quizás el grupo necesita un cambio en los límites de almacenamiento o rendimiento. Posiblemente desee cambiar la configuración de la base de datos en el grupo. Puede cambiar la configuración del grupo en cualquier momento para obtener el mejor equilibrio entre rendimiento y costo. Consulte [¿Cuándo se debe utilizar un grupo elástico?](sql-database-elastic-pool-guidance.md) para más información.

Para cambiar los límites de almacenamiento o eDTU por grupo y las eDTU por base de datos:

1. Abra la hoja **Configurar grupo** .

    En **Configuración de grupo elástico**, utilice el control deslizante para cambiar la configuración del grupo.

    ![Uso de recursos de grupos elásticos](./media/sql-database-elastic-pool-manage-portal/resize-pool.png)

2. Cuando se cambia la configuración, la pantalla muestra el coste estimado mensual del cambio.

    ![Actualización de un grupo elástico y nuevo coste mensual](./media/sql-database-elastic-pool-manage-portal/pool-change-edtu.png)

## <a name="latency-of-elastic-pool-operations"></a>Latencia de las operaciones de grupos elásticos
* El cambio del número mínimo de eDTU por base de datos o del máximo de eDTU por base de datos suele completarse en cinco minutos o menos.
* El cambio de eDTU por grupo depende de la cantidad total de espacio que usen todas las bases de datos del grupo. Los cambios tienen un duración media de 90 minutos o menos por cada 100 GB. Por ejemplo, si el espacio total que usan todas las bases de datos del grupo es de 200 GB, la latencia esperada para cambiar las eDTU de grupo por grupo es de 3 horas o menos.

## <a name="next-steps"></a>Pasos siguientes

- Para entender qué es un grupo elástico, consulte [Grupo elástico de SQL Database](sql-database-elastic-pool.md).
- Para una guía sobre cómo usar grupos elásticos, consulte las [consideraciones de precio y rendimiento para grupos elásticos](sql-database-elastic-pool-guidance.md).
- Para usar trabajos elásticos para ejecutar scripts de Transact-SQL con cualquier número de bases de datos en el grupo, consulte [Introducción a los trabajos elásticos](sql-database-elastic-jobs-overview.md).
- Para consultar en cualquier número de bases de datos del grupo, consulte [Introducción a las consultas elásticas](sql-database-elastic-query-overview.md).
- Para más información sobre las transacciones de cualquier número de bases de datos del grupo, consulte [Transacciones elásticas](sql-database-elastic-transactions-overview.md).


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

