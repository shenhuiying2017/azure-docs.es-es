<properties
   pageTitle="Exportación de datos de Log Analytics a Power BI | Microsoft Azure"
   description="Power BI es un servicio de análisis empresarial basado en la nube de Microsoft que proporciona unos excelentes informes y visualizaciones para el análisis de los distintos conjuntos de datos. Log Analytics puede exportar continuamente datos desde el repositorio de OMS a Power BI con lo que podrá aprovechar sus herramientas de análisis y visualizaciones. En este artículo se explica cómo configurar las consultas en Log Analytics para que se exporten a Power BI automáticamente a intervalos regulares."
   services="log-analytics"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="log-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/11/2016"
   ms.author="bwren" />

# Exportación de datos de Log Analytics a Power BI

[Power BI](https://powerbi.microsoft.com/es-ES/documentation/powerbi-service-get-started/) es un servicio de análisis empresarial basado en la nube de Microsoft que proporciona unos excelentes informes y visualizaciones para el análisis de los distintos conjuntos de datos. Log Analytics puede exportar automáticamente datos desde el repositorio de OMS a Power BI con lo que podrá aprovechar sus herramientas de análisis y visualizaciones.

Si configura Power BI con Log Analytics, podrá crear consultas de registro que exporten los resultados a los conjuntos de datos correspondientes de Power BI. La consulta y la exportación se continuarán ejecutándo automáticamente según la programación que defina para mantener el conjunto de datos actualizado con los últimos datos recopilados por Log Analytics.

![Log Analytics a Power BI](media/log-analytics-powerbi/overview.png)

## Programaciones de Power BI

Una *programación de Power BI* incluye una búsqueda de registros que exporta un conjunto de datos desde el repositorio de OMS al conjunto de datos correspondiente de Power BI y una programación que define la frecuencia de ejecución de esta búsqueda para mantener el conjunto de datos actualizado.

Los campos del conjunto de datos se corresponderán con las propiedades de los registros devueltos por la búsqueda de registros. Si la búsqueda devuelve registros de diferentes tipos, el conjunto de datos incluirá todas las propiedades de cada uno de los tipos incluidos.

> [AZURE.NOTE] Se recomienda usar una consulta de búsqueda de registros que devuelva datos sin procesar en lugar de realizar cualquier consolidación mediante comandos como [Measure](log-analytics-search-reference.md#measure). Puede realizar cualquier cálculo y adición en Power BI a partir de los datos sin procesar.

## Conexión del área de trabajo de OMS a Power BI

Para poder exportar desde Log Analytics a Power BI, debe conectar el área de trabajo de OMS a la cuenta de Power BI mediante el siguiente procedimiento.

1. En la consola de OMS, haga clic en el icono **Configuración**.
2. Seleccione **Cuentas**.
3. En la sección **Información del área de trabajo**, haga clic en **Connect to Power BI Account** (Conectarse a una cuenta de Power BI).
4. Escriba las credenciales de la cuenta de Power BI.

## Creación de una programación de Power BI

Cree una programación de Power BI para cada conjunto de datos mediante el siguiente procedimiento.

1. En la consola de OMS, haga clic en el icono **Búsqueda de registros**.
2. Escriba una consulta nueva o seleccione una búsqueda guardada que devuelva los datos que desea exportar a **Power BI**.  
3. Haga clic en el botón **Power BI** situado en la parte superior de la página para abrir el cuadro de diálogo **Power BI**.
4. Proporcione la información en la tabla siguiente y haga clic en **Guardar**.

| Propiedad | Descripción |
|:--|:--|
| Nombre | Nombre para identificar la programación cuando consulte la lista de programaciones de Power BI. |
| Búsqueda guardada | La búsqueda de registros que desea ejecutar. Puede seleccionar la consulta actual u otra búsqueda guardada existente en el cuadro desplegable. |
| Schedule | La frecuencia con la que se ejecutará la búsqueda guardada y la exportación al conjunto de datos de Power BI. El valor debe estar comprendido entre 15 minutos y 24 horas. |
| Nombre del conjunto de datos | El nombre del conjunto de datos de Power BI. Se creará si no existe y se actualizará si ya existe. |

## Visualización y eliminación de programaciones de Power BI

Observe la lista de programaciones de Power BI existente mediante el siguiente procedimiento.

1. En la consola de OMS, haga clic en el icono **Configuración**.
2. Seleccione **Power BI**.

Además de los detalles de la programación, se muestra el número de veces que se ha ejecutado la programación durante la semana pasada y el estado de la última sincronización. Si se produjeron errores durante la sincronización, puede hacer clic en el vínculo para ejecutar una búsqueda de registros para encontrar aquellos registros con los detalles del error.

Puede quitar una programación haciendo clic en la **X** en **Quitar columna**. Puede deshabilitar una programación seleccionando **Desactivar**. Para modificar una programación debe eliminarla y volverla a crear con la nueva configuración.

![Programaciones de Power BI](media/log-analytics-powerbi/schedules.png)

## Tutorial de ejemplo
La siguiente sección le guiará a través de un ejemplo de creación de una programación de Power BI y la utilización de su conjunto de datos para crear un informe sencillo. En este ejemplo, todos los datos de rendimiento de un conjunto de equipos se exporta a Power BI y, a continuación, se crea un gráfico de líneas para mostrar la utilización del procesador.

### Creación de la búsqueda de registros
Empezaremos creando una búsqueda de registros para los datos que desea enviar al conjunto de datos. En este ejemplo, utilizaremos una consulta que devuelve todos los datos de rendimiento de los equipos cuyo nombre empieza por *srv*.

![Programaciones de Power BI](media/log-analytics-powerbi/walkthrough-query.png)

### Creación de una búsqueda de Power BI
Haga clic en el botón **Power BI** para que se abra el cuadro de diálogo correspondiente y proporcione la información necesaria. Queremos que esta búsqueda se ejecute una vez cada hora y que se cree un conjunto de datos denominado *Contoso Perf*. Dado que ya se ha abierto la búsqueda que crea los datos que queremos, conservaremos el valor predeterminado de *Usar consulta de búsqueda actual* para **Búsqueda guardada**.

![Búsqueda de Power BI](media/log-analytics-powerbi/walkthrough-schedule.png)

### Comprobación de una búsqueda de Power BI
Para comprobar que se creó correctamente la programación, consulte la lista de búsquedas de Power BI en el icono **Configuración** del panel de OMS. Espere unos minutos y actualice esta vista hasta que se informe de que se ha ejecutado la sincronización.

![Búsqueda de Power BI](media/log-analytics-powerbi/walkthrough-schedules.png)

### Comprobación del conjunto de datos de Power BI
Inicie sesión en nuestra cuenta en [powerbi.microsoft.com](http://powerbi.microsoft.com/) y desplácese a **Conjuntos de datos** situados en la parte inferior del panel izquierdo. Se puede ver que el conjunto de datos denominado *Contoso Perf* aparece, lo cual indica que la exportación se ha ejecutado correctamente.

![Conjunto de datos de Power BI](media/log-analytics-powerbi/walkthrough-datasets.png)

### Creación de un informe basado en el conjunto de datos
Seleccione el conjunto de datos **Contoso Perf** y, luego, haga clic en **Resultados** en el panel **Campos** de la derecha para ver los campos que forman parte de este conjunto de datos. Para crear un gráfico de líneas que muestre la utilización del procesador por parte de cada equipo, se realizarán los siguientes pasos.

1. Seleccione la visualización del gráfico de líneas.
2. Arrastre **ObjectName** a **Report level filter** (Filtro de nivel de informes) y seleccione **Procesador**.
3. Arrastre **CounterName** a **Report level filter** (Filtro de nivel de informes) y seleccione **% de tiempo de procesador**.
4. Arrastre **CounterValue** a **Valores**.
5. Arrastre **Computer** a **Leyenda**.
6. Arrastre **TimeGenerated** a **Eje**.

Aparecerá el gráfico de líneas resultante con los datos de nuestro conjunto de datos.

![Gráfico de líneas de Power BI](media/log-analytics-powerbi/walkthrough-linegraph.png)

### Guardado del informe
Para guardar el informe, haga clic en el botón Guardar situado en la parte superior de la pantalla y luego compruebe que aparezca en la sección de informes del panel izquierdo.

![Informes de Power BI](media/log-analytics-powerbi/walkthrough-report.png)

## Pasos siguientes

- Obtenga información sobre de las [búsquedas de registro](log-analytics-log-searches.md) para crear consultas que se puedan exportar a Power BI.
- Obtenga más información sobre [Power BI](powerbi.microsoft.com) para generar visualizaciones basadas en exportaciones de Log Analytics.

<!---HONumber=AcomDC_0518_2016-->