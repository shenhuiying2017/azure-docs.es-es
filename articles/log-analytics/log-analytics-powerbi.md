---
title: "Importación de datos de Azure Log Analytics en Power BI | Microsoft Docs"
description: "Power BI es un servicio de análisis empresarial basado en la nube de Microsoft que proporciona unos excelentes informes y visualizaciones para el análisis de los distintos conjuntos de datos.  En este artículo se describe cómo configurar la importación de datos de Log Analytics en Power BI y cómo configurarlos para que se actualicen automáticamente."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 83edc411-6886-4de1-aadd-33982147b9c3
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/27/2017
ms.author: bwren
ms.openlocfilehash: 163ac33af43a8cb7a23742f6336efca5fe7c4b4e
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/28/2017
---
# <a name="import-azure-log-analytics-data-into-power-bi"></a>Importación de datos de Azure Log Analytics en Power BI


[Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) es un servicio de análisis empresarial basado en la nube de Microsoft que proporciona unos excelentes informes y visualizaciones para el análisis de los distintos conjuntos de datos.  Puede importar los resultados de una búsqueda de registros de Log Analytics en un conjunto de datos de Power BI para poder beneficiarse de sus características, como la combinación de datos de diferentes orígenes y el uso compartido de informes en los dispositivos web y móviles.

En este artículo se proporcionan detalles sobre cómo importar datos de Log Analytics en Power BI y cómo programarlos para que se actualicen automáticamente.  Se incluyen diferentes procesos para un área de trabajo [actualizada](#upgraded-workspace) y otra [heredada](#legacy-workspace).

## <a name="upgraded-workspace"></a>Área de trabajo actualizada


Para importar datos desde un [área de trabajo actualizada de Log Analytics](log-analytics-log-search-upgrade.md) en Power BI, debe crear un conjunto de datos en Power BI basado en una consulta de búsqueda de registros en Log Analytics.  La consulta se ejecuta cada vez que se actualiza el conjunto de datos.  Después, puede crear informes de Power BI que usen datos del conjunto de datos.  Para crear el conjunto de datos en Power BI, exporte la consulta de Log Analytics al [lenguaje de Power Query (M)](https://msdn.microsoft.com/library/mt807488.aspx).  Después, use esto para crear una consulta en Power BI Desktop y publíquela en Power BI como un conjunto de datos.  A continuación se describen los detalles de este proceso.

![Log Analytics a Power BI](media/log-analytics-powerbi/overview.png)

### <a name="export-query"></a>Exportación de consultas
Empiece por crear una [búsqueda de registros](log-analytics-log-search-new.md) que devuelva los datos de Log Analytics con los que desea rellenar el conjunto de datos de Power BI.  Después, exporte dicha consulta al [lenguaje de Power Query (M)](https://msdn.microsoft.com/library/mt807488.aspx), que es el que Power BI Desktop puede utilizar.

1. Cree la búsqueda de registros en Log Analytics para extraer los datos para el conjunto de datos.
2. Si usa el portal de la búsqueda de registros, haga clic en **Power BI**.  Si usa el portal de Analytics portal, seleccione **Exportar** > **Power BI Query (M)** [Consulta de Power BI (M)].  Ambas opciones exportan la consulta a un archivo de texto con nombre **PowerBIQuery.txt**. 

    ![Exportación de la búsqueda de registros](media/log-analytics-powerbi/export-logsearch.png) ![Exportación de la búsqueda de registros](media/log-analytics-powerbi/export-analytics.png)

3. Abra el archivo de texto y copie su contenido.

### <a name="import-query-into-power-bi-desktop"></a>Importación de consultas en Power BI Desktop
Power BI Desktop es una aplicación de escritorio que le permite crear conjuntos de datos e informes que se pueden publicar en Power BI.  También puede usarlo para crear una consulta mediante el lenguaje de Power Query exportado de Log Analytics. 

1. Instale [Power BI Desktop](https://powerbi.microsoft.com/desktop/) si aún no lo tiene y después abra la aplicación.
2. Seleccione **Obtener datos** > **Consulta en blanco** para abrir una consulta nueva.  Después, seleccione **Editor avanzado** y pegue el contenido del archivo exportado en la consulta. Haga clic en **Done**(Listo).

    ![Consulta de Power BI Desktop](media/log-analytics-powerbi/desktop-new-query.png)

5. La consulta se ejecuta y después se muestran los resultados.  Puede que se le soliciten credenciales para conectarse a Azure.  
6. Escriba un nombre descriptivo para la consulta.  El nombre predeterminado es **Query1**. Haga clic en **Cerrar y aplicar** para agregar el conjunto de datos al informe.

    ![Nombre de Power BI Desktop](media/log-analytics-powerbi/desktop-results.png)



### <a name="publish-to-power-bi"></a>Publicación en Power BI
Al publicar en Power BI, se crean un conjunto de datos y un informe.  Si crea un informe en Power BI Desktop, este se publicará con sus datos.  De lo contrario, se creará un informe en blanco.  Puede modificar el informe en Power BI o crear uno basado en el conjunto de datos.

8. Cree un informe basado en sus datos.  Use la [documentación de Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-report-view) si no está familiarizado con esta aplicación.  Cuando esté listo para realizar el envío a Power BI, haga clic en **Publicar**.  Cuando se le pida, seleccione un destino en la cuenta de Power BI.  A menos que tenga un destino específico en mente, use **Mi área de trabajo**.

    ![Publicación en Power BI Desktop](media/log-analytics-powerbi/desktop-publish.png)

3. Una vez completada la publicación, haga clic en **Abrir en Power BI** para abrir Power BI con el nuevo conjunto de datos.


### <a name="configure-scheduled-refresh"></a>Configuración de la actualización programada
El conjunto de datos creado en Power BI tendrá los mismos datos que vio anteriormente en Power BI Desktop.  Debe actualizar el conjunto de datos periódicamente para volver a ejecutar la consulta y rellenarlo con los últimos datos de Log Analytics.  

1. Haga clic en el área de trabajo donde se cargó el informe y seleccione el menú **Conjuntos de datos**. Seleccione el menú contextual junto a su nuevo conjunto de datos y seleccione **Configuración**. En **Credenciales de origen de datos** debe aparecer un mensaje que indica que las credenciales no son válidas.  Esto se debe a que aún no ha proporcionado las credenciales para el conjunto de datos que se deben usar cuando se actualizan los datos.  Haga clic en **Editar credenciales** y especifique las credenciales con acceso a Log Analytics.

    ![Programación de Power BI](media/log-analytics-powerbi/powerbi-schedule.png)

5. En **Actualización programada**, active la opción **Mantener los datos actualizados**.  También puede cambiar la **frecuencia de actualización** y una o varias horas específicas para ejecutar la actualización.

    ![Actualización de Power BI](media/log-analytics-powerbi/powerbi-schedule-refresh.png)

## <a name="legacy-workspace"></a>Área de trabajo heredada
Si configura Power BI con un [área de trabajo heredada de Log Analytics](log-analytics-powerbi.md), cree consultas de registro que exporten los resultados a los conjuntos de datos correspondientes de Power BI.  La consulta y la exportación se continuarán ejecutándo automáticamente según la programación que defina para mantener el conjunto de datos actualizado con los últimos datos recopilados por Log Analytics.

![Log Analytics a Power BI](media/log-analytics-powerbi/overview-legacy.png)

### <a name="power-bi-schedules"></a>Programaciones de Power BI
Una *programación de Power BI* incluye una búsqueda de registros que exporta un conjunto de datos desde el repositorio de OMS al conjunto de datos correspondiente de Power BI y una programación que define la frecuencia de ejecución de esta búsqueda para mantener el conjunto de datos actualizado.

Los campos del conjunto de datos se corresponderán con las propiedades de los registros devueltos por la búsqueda de registros.  Si la búsqueda devuelve registros de diferentes tipos, el conjunto de datos incluirá todas las propiedades de cada uno de los tipos incluidos.  

### <a name="connecting-oms-workspace-to-power-bi"></a>Conexión del área de trabajo de OMS a Power BI
Para poder exportar desde Log Analytics a Power BI, debe conectar el área de trabajo de OMS a la cuenta de Power BI mediante el siguiente procedimiento.  

1. En la consola de OMS, haga clic en el icono **Configuración** .
2. Seleccione **Cuentas**.
3. En la sección **Información del área de trabajo**, haga clic en **Connect to Power BI Account** (Conectarse a una cuenta de Power BI).
4. Escriba las credenciales de la cuenta de Power BI.

### <a name="create-a-power-bi-schedule"></a>Creación de una programación de Power BI
Cree una programación de Power BI para cada conjunto de datos mediante el siguiente procedimiento.

1. En la consola de OMS, haga clic en el icono **Búsqueda de registros** .
2. Escriba una consulta nueva o seleccione una búsqueda guardada que devuelva los datos que desea exportar a **Power BI**.  
3. Haga clic en el botón **Power BI** situado en la parte superior de la página para abrir el cuadro de diálogo **Power BI**.
4. Proporcione la información en la tabla siguiente y haga clic en **Guardar**.

| Propiedad | Descripción |
|:--- |:--- |
| Nombre |Nombre para identificar la programación cuando consulte la lista de programaciones de Power BI. |
| Búsqueda guardada |La búsqueda de registros que desea ejecutar.  Puede seleccionar la consulta actual u otra búsqueda guardada existente en el cuadro desplegable. |
| Schedule |La frecuencia con la que se ejecutará la búsqueda guardada y la exportación al conjunto de datos de Power BI.  El valor debe estar comprendido entre 15 minutos y 24 horas. |
| Nombre del conjunto de datos |El nombre del conjunto de datos de Power BI.  Se creará si no existe y se actualizará si ya existe. |

### <a name="viewing-and-removing-power-bi-schedules"></a>Visualización y eliminación de programaciones de Power BI
Observe la lista de programaciones de Power BI existente mediante el siguiente procedimiento.

1. En la consola de OMS, haga clic en el icono **Configuración** .
2. Seleccione **Power BI**.

Además de los detalles de la programación, se muestra el número de veces que se ha ejecutado la programación durante la semana pasada y el estado de la última sincronización.  Si se produjeron errores durante la sincronización, puede hacer clic en el vínculo para ejecutar una búsqueda de registros para encontrar aquellos registros con los detalles del error.

Puede quitar una programación haciendo clic en la **X** en **Quitar columna**.  Puede deshabilitar una programación seleccionando **Desactivar**.  Para modificar una programación debe eliminarla y volverla a crear con la nueva configuración.

![Programaciones de Power BI](media/log-analytics-powerbi/schedules.png)

### <a name="sample-walkthrough"></a>Tutorial de ejemplo
La siguiente sección le guiará a través de un ejemplo de creación de una programación de Power BI y la utilización de su conjunto de datos para crear un informe sencillo.  En este ejemplo, todos los datos de rendimiento de un conjunto de equipos se exporta a Power BI y, a continuación, se crea un gráfico de líneas para mostrar la utilización del procesador.

#### <a name="create-log-search"></a>Creación de la búsqueda de registros
Empezaremos creando una búsqueda de registros para los datos que desea enviar al conjunto de datos.  En este ejemplo, utilizaremos una consulta que devuelve todos los datos de rendimiento de los equipos cuyo nombre empieza por *srv*.  

![Programaciones de Power BI](media/log-analytics-powerbi/walkthrough-query.png)

#### <a name="create-power-bi-search"></a>Creación de una búsqueda de Power BI
Haga clic en el botón **Power BI** para que se abra el cuadro de diálogo correspondiente y proporcione la información necesaria.  Queremos que esta búsqueda se ejecute una vez cada hora y que se cree un conjunto de datos denominado *Contoso Perf*.  Dado que ya se ha abierto la búsqueda que crea los datos que queremos, conservaremos el valor predeterminado de *Usar consulta de búsqueda actual* para **Búsqueda guardada**.

![Búsqueda de Power BI](media/log-analytics-powerbi/walkthrough-schedule.png)

#### <a name="verify-power-bi-search"></a>Comprobación de una búsqueda de Power BI
Para comprobar que se creó correctamente la programación, consulte la lista de búsquedas de Power BI en el icono **Configuración** del panel de OMS.  Espere unos minutos y actualice esta vista hasta que se informe de que se ha ejecutado la sincronización.  Normalmente, podrá programar el conjunto de datos para que se actualice automáticamente.

![Búsqueda de Power BI](media/log-analytics-powerbi/walkthrough-schedules.png)

#### <a name="verify-the-dataset-in-power-bi"></a>Comprobación del conjunto de datos de Power BI
Inicie sesión en nuestra cuenta en [powerbi.microsoft.com](http://powerbi.microsoft.com/) y desplácese a **Conjuntos de datos** situados en la parte inferior del panel izquierdo.  Se puede ver que el conjunto de datos denominado *Contoso Perf* aparece, lo cual indica que la exportación se ha ejecutado correctamente.

![Conjunto de datos de Power BI](media/log-analytics-powerbi/walkthrough-datasets.png)

#### <a name="create-report-based-on-dataset"></a>Creación de un informe basado en el conjunto de datos
Seleccione el conjunto de datos **Contoso Perf** y, luego, haga clic en **Resultados** en el panel **Campos** de la derecha para ver los campos que forman parte de este conjunto de datos.  Para crear un gráfico de líneas que muestre la utilización del procesador por parte de cada equipo, se realizarán los siguientes pasos.

1. Seleccione la visualización del gráfico de líneas.
2. Arrastre **ObjectName** a **Report level filter** (Filtro de nivel de informes) y seleccione **Procesador**.
3. Arrastre **CounterName** a **Report level filter** (Filtro de nivel de informes) y seleccione **% de tiempo de procesador**.
4. Arrastre **CounterValue** a **Valores**.
5. Arrastre **Computer** a **Leyenda**.
6. Arrastre **TimeGenerated** a **Eje**.

Aparecerá el gráfico de líneas resultante con los datos de nuestro conjunto de datos.

![Gráfico de líneas de Power BI](media/log-analytics-powerbi/walkthrough-linegraph.png)

#### <a name="save-the-report"></a>Guardado del informe
Para guardar el informe, haga clic en el botón Guardar situado en la parte superior de la pantalla y luego compruebe que aparezca en la sección de informes del panel izquierdo.

![Informes de Power BI](media/log-analytics-powerbi/walkthrough-report.png)



## <a name="next-steps"></a>Pasos siguientes
* Obtenga información sobre de las [búsquedas de registro](log-analytics-log-searches.md) para crear consultas que se puedan exportar a Power BI.
* Obtenga más información sobre [Power BI](http://powerbi.microsoft.com) para generar visualizaciones basadas en exportaciones de Log Analytics.
