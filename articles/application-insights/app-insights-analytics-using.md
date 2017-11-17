---
title: "Uso de Analytics: la eficaz herramienta de búsqueda de Azure Application Insights | Microsoft Docs"
description: "Uso de Analytics, la eficaz herramienta de búsqueda de Application Insights. "
services: application-insights
documentationcenter: 
author: danhadari
manager: carmonm
ms.assetid: c3b34430-f592-4c32-b900-e9f50ca096b3
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: danha; mbullwin
ms.openlocfilehash: 0ca5c8b19f4699548a8551ec673e4a067d4e5fad
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/13/2017
---
# <a name="using-analytics-in-application-insights"></a>Uso de Analytics en Application Insights
[Analytics](app-insights-analytics.md) es la eficaz característica de búsqueda de [Application Insights](app-insights-overview.md). En estas páginas se describe el lenguaje de consulta de Log Analytics.

* **[Vea el vídeo de introducción](https://applicationanalytics-media.azureedge.net/home_page_video.mp4)**.
* **[Use una versión de prueba de Analytics en nuestros datos simulados](https://analytics.applicationinsights.io/demo)** si su aplicación aún no envía datos a Application Insights.

## <a name="open-analytics"></a>Apertura de Analytics
En el recurso de inicio de la aplicación de Application Insights, haga clic en Analytics.

![Abra portal.azure.com, abra su recurso de Application Insights y haga clic en Análisis.](./media/app-insights-analytics-using/001.png)

El tutorial en línea le proporciona algunas ideas sobre lo que puede hacer.

Puede encontrar un [paseo más amplio aquí](app-insights-analytics-tour.md).

## <a name="query-your-telemetry"></a>Consulta de la telemetría
### <a name="write-a-query"></a>Escriba una consulta.
![Pantalla del esquema](./media/app-insights-analytics-using/150.png)

Comience con los nombres de cualquiera de las tablas que aparecen a la izquierda (o los operadores [range](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/range-operator) o [union](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/union-operator)). Use `|` para crear una canalización de [operadores](https://docs.loganalytics.io/docs/Learn/References/Useful-operators). 

IntelliSense le indicará tanto los operadores como los elementos de la expresión que se pueden utilizar. Haga clic en el icono de información (o presione Ctrl+Espacio) para obtener una descripción más larga de cada elemento y ejemplos de cómo usarlos.

Consulte [Un paseo por Analytics de Application Insights](app-insights-analytics-tour.md) y [Referencia para Analytics](app-insights-analytics-reference.md).

### <a name="run-a-query"></a>Ejecución de una consulta
![Ejecución de una consulta](./media/app-insights-analytics-using/130.png)

1. Puede utilizar saltos de línea sencillos en las consultas.
2. Coloque el cursor dentro o al final de la consulta que desea ejecutar.
3. Compruebe el intervalo de tiempo de la consulta (puede cambiarla o invalidarla; para ello, debe incluir su propia cláusula [`where...timestamp...`](https://docs.loganalytics.io/docs/Learn/Tutorials/Date-and-time-operations) en la consulta).
3. Haga clic en Ir para ejecutar la consulta.
4. No ponga líneas en blanco en la consulta. Puede mantener varias consultas separadas en una pestaña de consulta; para ello, sepárelas con líneas en blanco. Se ejecuta solo la consulta que tiene el cursor.

### <a name="save-a-query"></a>Almacenamiento de una consulta
![Almacenamiento de una consulta](./media/app-insights-analytics-using/140.png)

1. Guarde el archivo de consulta actual.
2. Abra un archivo de consulta guardado.
3. Cree un archivo de consulta.

## <a name="see-the-details"></a>Visualización de los detalles
Expanda cualquier fila de los resultados para ver la lista completa de sus propiedades. Puede expandir más cualquier propiedad que tenga un valor estructurado: por ejemplo, las dimensiones personalizadas o la pila indicada en una excepción.

![Expansión de una fila](./media/app-insights-analytics-using/070.png)

## <a name="arrange-the-results"></a>Disposición de los resultados
Puede ordenar, filtrar, paginar y agrupar los resultados devueltos desde la consulta.

> [!NOTE]
> Ordenar, agrupar y filtrar en el explorador no vuelve a ejecutar la consulta. Solo vuelve a ordenar los resultados devueltos por la última consulta. 
> 
> Para realizar estas tareas en el servidor antes de que se devuelvan los resultados, escriba la consulta con los operadores [sort](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/sort-operator), [summarize](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/summarize-operator) y [where](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/where-operator).
> 
> 

Elija las columnas que desea ver, arrastre los encabezados de columna para reorganizarlos y cambie el tamaño de las columnas arrastrando sus bordes.

![Organización de columnas](./media/app-insights-analytics-using/030.png)

### <a name="sort-and-filter-items"></a>Ordenación y filtrado de elementos
Haga clic en el encabezado de una columna para ordenar los resultados. Vuelva a hacer clic para ordenar de otra manera, y haga clic una tercera vez para volver a la ordenación original devuelta por la consulta.

Utilice el icono de filtro para restringir la búsqueda.

![Ordenación y filtrado de columnas](./media/app-insights-analytics-using/040.png)

### <a name="group-items"></a>Agrupación de elementos
Para ordenar por más de una columna, use la agrupación. Primero, habilítela y, después, arrastre los encabezados de columna en el espacio por encima de la tabla.

![Grupo](./media/app-insights-analytics-using/060.png)

### <a name="missing-some-results"></a>¿Faltan algunos resultados?

Si cree que no ve todos los resultados que esperaba, puede deberse a los motivos siguientes.

* **Filtro de intervalo de tiempo**. De forma predeterminada, solo verá los resultados de las últimas 24 horas. Hay un filtro automático que limita el intervalo de resultados que se recuperan de las tablas de origen. 

    Sin embargo, dicho filtro se puede cambiar desde el menú desplegable.

    O bien, puede invalidar el intervalo automático mediante la inclusión de su propia [`where  ... timestamp ...` cláusula](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/where-operator) en la consulta. Por ejemplo:

    `requests | where timestamp > ago('2d')`

* **Límite de resultados**. Hay un límite de 10 000 filas en los resultados devueltos desde el portal. Se muestra una advertencia si se sobrepasa el límite. Si esto sucede, al ordenar los resultados de la tabla no siempre mostrará todos los resultados primeros o últimos reales. 

    Es recomendable evitar llegar al límite. Utilice el filtro de intervalo de tiempo u operadores como:

  * [top 100 by timestamp](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/top-operator) 
  * [take 100](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/take-operator)
  * [summarize ](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/summarize-operator) 
  * [where timestamp &gt; ago(3d)](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/where-operator)

(¿Desea más de 10 000 filas? Considere el uso de [Exportación continua](app-insights-export-telemetry.md). Analytics está diseñado para el análisis, no para la recuperación de datos sin procesar.)

## <a name="diagrams"></a>Diagramas
Seleccione el tipo de diagrama que desea:

![Seleccione un tipo de diagrama](./media/app-insights-analytics-using/230.png)

Si tiene varias columnas de los tipos correctos, puede elegir los ejes X e Y, así como una columna de dimensiones para dividir los resultados.

De manera predeterminada, los resultados se muestran en un principio en forma de tabla y el diagrama se selecciona manualmente. Sin embargo, para seleccionar un diagrama se puede usar la [directiva render](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/render-operator) al final de una consulta.

### <a name="analytics-diagnostics"></a>Analytics Diagnostics


En un gráfico de tiempo, si se produce un pico o salto repentino en los datos, es posible que vea un punto en la línea resaltado. Esto indica que Analytics Diagnostics ha identificado una combinación de propiedades que filtran los cambios repentinos. Haga clic en el punto para obtener más detalles del filtro y para ver la versión filtrada. Esto puede ayudar a identificar lo que ha causado el cambio. 

[Más información sobre Analytics Diagnostics](app-insights-analytics-diagnostics.md)


![Analytics Diagnostics](./media/app-insights-analytics-using/analytics-diagnostics.png)

## <a name="pin-to-dashboard"></a>Anclar al panel
Puede anclar un diagrama o una tabla a uno de sus [paneles compartidos](app-insights-dashboards.md) ; simplemente haga clic en la chincheta. 

![Haga clic en la chincheta](./media/app-insights-analytics-using/pin-01.png)

Esto significa que, cuando elabora un panel que le ayude a supervisar el rendimiento o el uso de los servicios web, puede incluir un análisis bastante complejo junto con las demás métricas. 

Puede anclar una tabla al panel, si tiene cuatro o menos columnas. Se muestran solo las siete filas superiores.

### <a name="dashboard-refresh"></a>Actualización del panel
El gráfico anclado al panel se actualiza automáticamente al volver a ejecutar la consulta aproximadamente cada dos horas. También puede hacer clic en el botón Actualizar.

### <a name="automatic-simplifications"></a>Simplificaciones automáticas

Determinadas simplificaciones se aplican a un gráfico cuando se ancla a un panel.

**Restricción de tiempo:** las consultas se limitan de manera automática a los últimos 14 días. El efecto es el mismo que si la consulta incluye `where timestamp > ago(14d)`.

**Restricción de recuento de intervalos:** si se muestra un gráfico que muestra muchos intervalos discretos (normalmente un gráfico de barras), los intervalos menos poblados se agrupan en un solo intervalo "otros". Por ejemplo, esta consulta:

    requests | summarize count_search = count() by client_CountryOrRegion

tiene esta apariencia en Analytics:

![Gráfico con cola larga](./media/app-insights-analytics-using/pin-07.png)

pero cuando se ancla a un panel, la siguiente apariencia:

![Gráfico con intervalos limitados](./media/app-insights-analytics-using/pin-08.png)

## <a name="export-to-excel"></a>Exportación a Excel
Una vez que haya ejecutado una consulta, puede descargar un archivo .csv. Haga clic en **Exportar, Excel**.

## <a name="export-to-power-bi"></a>Exportación a Power BI
Coloque el cursor en una consulta y elija **Exportar, Power BI**.

![Exportación de Analytics a Power BI](./media/app-insights-analytics-using/240.png)

Ejecute la consulta en Power BI. Puede establecerla para que se actualice según una programación.

Con Power BI, puede crear paneles que reúnan datos de una gran variedad de orígenes.

[Más información acerca de la exportación a Power BI](app-insights-export-power-bi.md)

## <a name="deep-link"></a>Vínculo profundo

Obtenga un vínculo en **Export, Share link** (Exportar, compartir vínculo) que puede enviar a otro usuario. Siempre que el usuario tenga [acceso a su grupo de recursos](app-insights-resources-roles-access-control.md), la consulta se abrirá en la interfaz de usuario de Analytics.

(En el vínculo, aparece el texto de consulta después de "?q=", comprimido en gzip y codificado en base 64. Puede escribir código para generar vínculos profundos que se proporcionan a los usuarios. Sin embargo, el método recomendado para ejecutar Analytics desde código es mediante la [API de REST](https://dev.applicationinsights.io/).)


## <a name="automation"></a>Automation

Use la [API de REST de acceso a datos](https://dev.applicationinsights.io/) para ejecutar las consultas de Analytics. [Por ejemplo](https://dev.applicationinsights.io/apiexplorer/query?appId=DEMO_APP&apiKey=DEMO_KEY&query=requests%0A%7C%20where%20timestamp%20%3E%3D%20ago%2824h%29%0A%7C%20count) (con PowerShell):

```PS
curl "https://api.applicationinsights.io/beta/apps/DEMO_APP/query?query=requests%7C%20where%20timestamp%20%3E%3D%20ago(24h)%7C%20count" -H "x-api-key: DEMO_KEY"
```

A diferencia de la interfaz de usuario de Analytics, la API de REST no agrega automáticamente ninguna limitación de marca de tiempo a las consultas. No olvide agregar su propia cláusula de where para evitar la obtención de respuestas muy grandes.



## <a name="import-data"></a>Importar datos

Los datos se pueden importar desde un archivo CSV. Un uso habitual consiste en importar datos estáticos que se pueden combinar con tablas de la telemetría. 

Por ejemplo, si los usuarios autenticados se identifican en la telemetría mediante un alias o un identificador confuso, puede importar una tabla que asigne alias a los nombres reales. Si realiza una combinación en la telemetría de la solicitud, puede identificar a los usuarios por sus nombres reales en los informes de Analytics.

### <a name="define-your-data-schema"></a>Definición de un esquema de datos

1. Haga clic en **Configuración** (en la parte superior izquierda) y, luego, en **Orígenes de datos**. 
2. Agregue un origen de datos siguiendo las instrucciones. Se le solicita que proporcione una muestra de los datos, que se deben incluir al menos diez filas. A continuación, corrija el esquema.

Así se define un origen de datos, que, posteriormente, se puede usar para importar tablas individuales.

### <a name="import-a-table"></a>Importación de una tabla

1. Abra la definición del origen de datos de la lista.
2. Haga clic en "Cargar" y siga las instrucciones para cargar la tabla. Esto implica una llamada a una API de REST, por lo que es fácil de automatizar. 

La tabla ya está disponible para usarla en las consultas de Analytics. Aparecerá en Analytics 

### <a name="use-the-table"></a>Uso de la tabla

Supongamos que la definición del origen de datos se denomina `usermap` y que tiene dos campos, `realName` y `user_AuthenticatedId`. La tabla `requests` también tiene un campo denominado `user_AuthenticatedId`, por lo que resulta sencillo combinarlos:

```AIQL

    requests
    | where notempty(user_AuthenticatedId) | take 10
    | join kind=leftouter ( usermap ) on user_AuthenticatedId 
```
La tabla de solicitudes resultante tiene una columna adicional, `realName`.

### <a name="import-from-logstash"></a>Importación desde LogStash

Si usa [LogStash](https://www.elastic.co/guide/en/logstash/current/getting-started-with-logstash.html), puede utilizar Analytics para consultar los registros. Use el [complemento que canaliza los datos a Analytics](https://github.com/Microsoft/logstash-output-application-insights). 

## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/123/player] 

[!INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]

