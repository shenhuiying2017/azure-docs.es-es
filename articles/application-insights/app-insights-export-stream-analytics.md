---
title: Uso de Stream Analytics para procesar datos exportados de Application Insights | Microsoft Docs
description: Stream Analytics puede transformar, filtrar y enrutar continuamente los datos de que exportan desde Application Insights.
services: application-insights
documentationcenter: 
author: noamben
manager: douge
ms.assetid: 31594221-17bd-4e5e-9534-950f3b022209
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: e6fb4cdbbe8c334245517f2df0292ca172af5c7b


---
# <a name="use-stream-analytics-to-process-exported-data-from-application-insights"></a>Uso de Stream Analytics para procesar datos exportados de Application Insights
[Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) es la herramienta ideal para procesar los datos [exportados desde Application Insights](app-insights-export-telemetry.md). Stream Analytics puede extraer datos de una variedad de orígenes. Puede transformar y filtrar los datos y, después, enrutarlos a una variedad de receptores.

En este ejemplo, vamos a crear un adaptador que toma datos de Application Insights, cambia su nombre y procesa algunos de los campos, y después los canaliza a Power BI.

> [!WARNING]
> Hay formas recomendadas mucho mejores [y sencillas de mostrar datos de Application Insights en Power BI](app-insights-export-power-bi.md). La ruta de acceso que se muestra aquí es solo un ejemplo para mostrar cómo se procesan los datos exportados.
> 
> 

![Diagrama de bloques para exportar a través de SA a PBI](./media/app-insights-export-stream-analytics/020.png)

## <a name="create-storage-in-azure"></a>Creación de almacenamiento en Azure
La exportación continua siempre envía los datos a una cuenta de almacenamiento de Azure, por lo que necesitará crear primero el almacenamiento.

1. Cree una cuenta de almacenamiento "clásica" en su suscripción en el [Portal de Azure](https://portal.azure.com).
   
   ![En el portal de Azure, elija Nuevo, Datos, Almacenamiento.](./media/app-insights-export-stream-analytics/030.png)
2. Crear un contenedor
   
    ![En el nuevo almacenamiento, seleccione Contenedores, haga clic en el icono Contenedores y luego, en Agregar.](./media/app-insights-export-stream-analytics/040.png)
3. Copie la clave de acceso de almacenamiento.
   
    La necesitará en seguida para configurar la entrada para el servicio de análisis de transmisiones.
   
    ![En el almacenamiento, abra Configuración, Claves y realice una copia de la clave de acceso principal.](./media/app-insights-export-stream-analytics/045.png)

## <a name="start-continuous-export-to-azure-storage"></a>Inicio de la exportación continua al almacenamiento de Azure
[exportación continua](app-insights-export-telemetry.md) transfiere los datos de Application Insights al almacenamiento de Azure.

1. En el portal de Azure, busque el recurso de Application Insights que ha creado para la aplicación.
   
    ![Elija Examinar, Application Insights, su aplicación.](./media/app-insights-export-stream-analytics/050.png)
2. Cree una exportación continua.
   
    ![Elija Configuración, Exportación continua, Agregar.](./media/app-insights-export-stream-analytics/060.png)

    Seleccione la cuenta de almacenamiento que creó anteriormente:

    ![Establezca el destino de exportación.](./media/app-insights-export-stream-analytics/070.png)

    Configure los tipos de eventos que desea ver:

    ![Elija los tipos de evento.](./media/app-insights-export-stream-analytics/080.png)

1. Permita que se acumulen algunos datos. Póngase cómo y deje que los usuarios usen su aplicación durante un tiempo. Así, aparecerá la telemetría y verá gráficos estadísticos en el [explorador de métricas](app-insights-metrics-explorer.md) y eventos individuales en la [búsqueda de diagnóstico](app-insights-diagnostic-search.md). 
   
    Y, además, exportará los datos en el almacenamiento. 
2. Inspeccione los datos exportados. En Visual Studio, elija **Ver/Cloud Explorer** y abra Azure/Almacenamiento. (Si no dispone de esta opción de menú, deberá instalar el SDK de Azure: abra el cuadro de diálogo Nuevo proyecto y Visual C#/Nube/Obtener el SDK de Microsoft Azure para. NET.)
   
    ![](./media/app-insights-export-stream-analytics/04-data.png)
   
    Tome nota de la parte común del nombre de la ruta de acceso, que se deriva del nombre de la aplicación y de la clave de instrumentación. 

Los eventos se escriben en archivos de blob en formato JSON. Cada archivo puede contener uno o varios eventos. Así, es probable que queramos leer los datos de eventos y filtrar por los campos que deseemos. Se pueden realizar multitud de acciones con los datos, pero nuestro plan de hoy consiste en usar Stream Analytics para canalizar los datos a Power BI.

## <a name="create-an-azure-stream-analytics-instance"></a>Creación de una instancia de Análisis de transmisiones de Azure
Desde el [Portal de Azure clásico](https://manage.windowsazure.com/), seleccione el servicio Análisis de transmisiones de Azure y cree un nuevo trabajo de Análisis de transmisiones:

![](./media/app-insights-export-stream-analytics/090.png)

![](./media/app-insights-export-stream-analytics/100.png)

Cuando se cree el nuevo trabajo, expanda los detalles:

![](./media/app-insights-export-stream-analytics/110.png)

### <a name="set-blob-location"></a>Establecimiento de la ubicación del blob
Configúrelo de manera que tome los datos del blob de Exportación continua:

![](./media/app-insights-export-stream-analytics/120.png)

Ahora, necesitará la clave de acceso principal de la cuenta de almacenamiento, que anotó anteriormente. Establézcala como la clave de cuenta de almacenamiento.

![](./media/app-insights-export-stream-analytics/130.png)

### <a name="set-path-prefix-pattern"></a>Establecimiento del patrón del prefijo de la ruta de acceso
![](./media/app-insights-export-stream-analytics/140.png)

**Asegúrese de establecer el formato de fecha como AAAA-MM-DD (con guiones).**

El patrón del prefijo de la ruta de acceso especifica dónde busca el Análisis de transmisiones los archivos de entrada en el almacenamiento. Deberá establecerlo para que coincida con el modo en que la Exportación continua almacena los datos. Configúrelo como este caso que se muestra a continuación:

    webapplication27_12345678123412341234123456789abcdef0/PageViews/{date}/{time}

En este ejemplo:

* `webapplication27` es el nombre del recurso de Application Insights, **todo en minúsculas**.
* `1234...` es la clave de instrumentación que copió del recurso de Application Insights, **omitiendo guiones**. 
* `PageViews` es el tipo de datos que quiere analizar. Los tipos disponibles dependen del filtro definido en la Exportación continua. Examine los datos exportados para ver los demás tipos disponibles y vea el [modelo de exportación de datos](app-insights-export-data-model.md).
* `/{date}/{time}` es un patrón escrito literalmente.

> [!NOTE]
> Inspeccione el almacenamiento para asegurarse de que obtiene la ruta de acceso correcta.
> 
> 

### <a name="finish-initial-setup"></a>Finalización de la instalación inicial
Confirme el formato de serialización:

![Confirme y cierre el asistente.](./media/app-insights-export-stream-analytics/150.png)

Cierre el asistente y espere a que el programa de instalación finalice.

> [!TIP]
> Use el comando de ejemplo para descargar algunos datos. Guardarlo como un ejemplo de prueba para depurar la consulta.
> 
> 

## <a name="set-the-output"></a>Establecimiento de la salida
Ahora seleccione el trabajo y establezca la salida.

![Seleccione el canal nuevo, haga clic en Salidas, Agregar, Power BI](./media/app-insights-export-stream-analytics/160.png)

Ofrezca su **cuenta profesional o educativa** para autorizar el Análisis de transmisiones a tener acceso al recurso de Power BI. Luego invente un nombre para la salida y para la tabla y el conjunto de datos de Power BI de destino.

![Invente tres nombres](./media/app-insights-export-stream-analytics/170.png)

## <a name="set-the-query"></a>Definición de la consulta
La consulta controla la traducción de la entrada en la salida.

![Seleccione el trabajo y haga clic en Consultar. Pegue el siguiente ejemplo.](./media/app-insights-export-stream-analytics/180.png)

Use la función de prueba para comprobar que obtiene la salida correcta. Proporciónele los datos de ejemplo que tomó de la página de entradas. 

### <a name="query-to-display-counts-of-events"></a>Consulta para mostrar recuentos de eventos
Pegue esta consulta:

```SQL

    SELECT
      flat.ArrayValue.name,
      count(*)
    INTO
      [pbi-output]
    FROM
      [export-input] A
    OUTER APPLY GetElements(A.[event]) as flat
    GROUP BY TumblingWindow(minute, 1), flat.ArrayValue.name
```

* export-input es el alias que damos a la entrada de transmisiones
* pbi-output es el alias que hemos definido para la salida
* Usamos [OUTER APPLY GetElements](https://msdn.microsoft.com/library/azure/dn706229.aspx) porque el nombre del evento se encuentra en una matriz JSON anidada. A continuación, la opción Select selecciona el nombre de evento, junto con el recuento del número de instancias con dicho nombre en el período de tiempo. La cláusula [Group By](https://msdn.microsoft.com/library/azure/dn835023.aspx) agrupa los elementos en períodos de tiempo de 1 minuto.

### <a name="query-to-display-metric-values"></a>Consulta para mostrar valores de métrica
```SQL

    SELECT
      A.context.data.eventtime,
      avg(CASE WHEN flat.arrayvalue.myMetric.value IS NULL THEN 0 ELSE  flat.arrayvalue.myMetric.value END) as myValue
    INTO
      [pbi-output]
    FROM
      [export-input] A
    OUTER APPLY GetElements(A.context.custom.metrics) as flat
    GROUP BY TumblingWindow(minute, 1), A.context.data.eventtime

``` 

* Esta consulta explora la telemetría de métricas para obtener la hora del evento y el valor de métrica. Los valores de métrica están dentro de una matriz, por eso usamos el patrón OUTER APPLY GetElements para extraer las filas. "myMetric" es el nombre de la métrica en este caso. 

### <a name="query-to-include-values-of-dimension-properties"></a>Consulta para incluir los valores de propiedades de dimensión
```SQL

    WITH flat AS (
    SELECT
      MySource.context.data.eventTime as eventTime,
      InstanceId = MyDimension.ArrayValue.InstanceId.value,
      BusinessUnitId = MyDimension.ArrayValue.BusinessUnitId.value
    FROM MySource
    OUTER APPLY GetArrayElements(MySource.context.custom.dimensions) MyDimension
    )
    SELECT
     eventTime,
     InstanceId,
     BusinessUnitId
    INTO AIOutput
    FROM flat

```

* Esta consulta incluye valores de las propiedades de dimensión sin depender de la fijación de una dimensión determinada en un índice fijo en la matriz de dimensión.

## <a name="run-the-job"></a>Ejecución del trabajo
Puede seleccionar una fecha en el pasado desde la que iniciar el trabajo. 

![Seleccione el trabajo y haga clic en Consultar. Pegue el siguiente ejemplo.](./media/app-insights-export-stream-analytics/190.png)

Espere hasta que el trabajo esté en ejecución.

## <a name="see-results-in-power-bi"></a>Visualización de resultados en Power BI
> [!WARNING]
> Hay formas recomendadas mucho mejores [y sencillas de mostrar datos de Application Insights en Power BI](app-insights-export-power-bi.md). La ruta de acceso que se muestra aquí es solo un ejemplo para mostrar cómo se procesan los datos exportados.
> 
> 

Abra Power BI con su cuenta profesional o educativa y seleccione el conjunto de datos y la tabla que ha definido como la salida del trabajo del Análisis de transmisiones.

![En Power BI, seleccione el conjunto de datos y los campos.](./media/app-insights-export-stream-analytics/200.png)

Ahora puede usar este conjunto de datos en informes y paneles de [Power BI](https://powerbi.microsoft.com).

![En Power BI, seleccione el conjunto de datos y los campos.](./media/app-insights-export-stream-analytics/210.png)

## <a name="no-data"></a>¿No hay datos?
* Compruebe que [establece el formato de fecha](#set-path-prefix-pattern) correctamente en AAAA-MM-DD (con guiones).

## <a name="video"></a>Vídeo
Noam Ben Zeev muestra cómo procesar los datos exportados mediante Stream Analytics.

> [!VIDEO https://channel9.msdn.com/Blogs/Windows-Azure/Export-to-Power-BI-from-Application-Insights/player]
> 
> 

## <a name="next-steps"></a>Pasos siguientes
* [Exportación continua](app-insights-export-telemetry.md)
* [Referencia detallada del modelo de datos para los tipos y valores de propiedad.](app-insights-export-data-model.md)
* [Application Insights](app-insights-overview.md)
* [Más ejemplos y tutoriales](app-insights-code-samples.md)




<!--HONumber=Nov16_HO3-->


