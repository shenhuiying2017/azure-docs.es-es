---
title: Exportación a Power BI desde Application Insights
description: Artículos
services: application-insights
documentationcenter: ''
author: noamben
manager: douge

ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/05/2016
ms.author: awills

---
# Alimentación de Power BI desde Application Insights
[Power BI](http://www.powerbi.com/) es un conjunto de herramientas de análisis de negocios que sirve para analizar datos y compartir conocimientos. Cada dispositivo cuenta con paneles que incluyen gran cantidad de datos. Puede combinar datos de varios orígenes, incluido [Application Insights de Visual Studio](app-insights-overview.md).

Para comenzar, consulte [Paquete de contenido de Application Insights para Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-application-insights/).

Obtendrá un panel inicial que puede personalizar, combinando los gráficos de Application Insights con los de otras fuentes. Tiene a su disposición una galería de visualización en la que puede obtener más gráficos; y cada gráfico tiene a su vez parámetros que puede configurar.

![](./media/app-insights-export-power-bi/010.png)

Después de la importación inicial, el panel y los informes seguirán actualizándose a diario. Puede controlar la programación de la actualización en el conjunto de datos.

**Muestreo.** Si la aplicación envía una gran cantidad de datos y usa el SDK de Application Insights para ASP.NET versión 2.0.0-beta3 o posterior, la característica de muestreo adaptativo puede operar y enviar solamente un porcentaje de los datos de telemetría. Esto se aplica igualmente si ha configurado el muestreo manualmente en el SDK o en la recopilación. [Obtenga más información sobre el muestreo.](app-insights-sampling.md)

## Formas alternativas para ver datos de Application Insights
* Los [paneles de Azure que contienen gráficos de Application Insights](app-insights-dashboards.md) pueden ser la opción más adecuada si no necesita mostrar datos que no son de Azure. Por ejemplo, si desea configurar un panel de gráficos de Application Insights que supervisen los diferentes componentes de un sistema, quizás junto con algunos monitores de servicios de Azure, la mejor opción es un panel de Azure. Se actualiza con más frecuencia de manera predeterminada. 
* La [exportación continua](app-insights-export-telemetry.md) copia los datos entrantes en Almacenamiento de Azure, donde puede moverlos y procesarlos como desee.
* El [análisis](app-insights-analytics.md) le permite realizar consultas complejas en los datos sin procesar conservados por Application Insights.

## Creación de su propio adaptador de Power BI mediante Análisis de transmisiones
El paquete de contenido de Power BI para Application Insights muestra un práctico subconjunto de telemetría de su aplicación que probablemente será suficiente para sus necesidades. Sin embargo, si desea obtener una gama más amplia de telemetría, o si desea calcular algunos datos a partir de la telemetría sin procesar, puede crear su propio adaptador mediante el servicio Análisis de transmisiones de Azure.

En este esquema, exportaremos datos desde Application Insights a Almacenamiento de Azure. [Análisis de transmisiones](https://azure.microsoft.com/services/stream-analytics/) extraerá los datos desde ahí, cambiará el nombre de algunos de los campos y los procesará, y canalizará los datos en Power BI. Análisis de transmisiones es un servicio que puede filtrar, agregar y realizar cálculos en una secuencia continua de datos.

![Diagrama de bloques para exportar a través de SA a PBI](./media/app-insights-export-power-bi/020.png)

> [!TIP]
> **No es necesario seguir el procedimiento que se explica en el resto de este artículo** (mediante Análisis de transmisiones) para ver los datos de Application Insights en Power BI. Hay una manera mucho más fácil de hacerlo: [utilice el adaptador gratuito](https://powerbi.microsoft.com/documentation/powerbi-content-pack-application-insights/). Siga el resto de este artículo solo si ese adaptador no proporciona todos los datos que desea, o si desea definir sus propias agregaciones o funciones en los datos.
> 
> 

### Creación de almacenamiento en Azure
La exportación continua siempre envía los datos a una cuenta de almacenamiento de Azure, por lo que necesitará crear primero el almacenamiento.

1. ¿Ha probado el [Paquete de contenido de Application Insights para Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-application-insights/)? Si esto es suficiente para sus necesidades, no necesita hacer nada más en el resto de este artículo.
2. Cree una cuenta de almacenamiento "clásica" en su suscripción en el [Portal de Azure](https://portal.azure.com).
   
   ![En el portal de Azure, elija Nuevo, Datos, Almacenamiento.](./media/app-insights-export-power-bi/030.png)
3. Crear un contenedor
   
    ![En el nuevo almacenamiento, seleccione Contenedores, haga clic en el icono Contenedores y luego, en Agregar.](./media/app-insights-export-power-bi/040.png)
4. Copie la clave de acceso de almacenamiento.
   
    La necesitará en seguida para configurar la entrada para el servicio de análisis de transmisiones.
   
    ![En el almacenamiento, abra Configuración, Claves y realice una copia de la clave de acceso principal.](./media/app-insights-export-power-bi/045.png)

### Inicio de la exportación continua al almacenamiento de Azure
La [Exportación continua](app-insights-export-telemetry.md) transfiere los datos de Application Insights al almacenamiento de Azure.

1. En el portal de Azure, busque el recurso de Application Insights que ha creado para la aplicación.
   
    ![Elija Examinar, Application Insights, su aplicación.](./media/app-insights-export-power-bi/050.png)
2. Cree una exportación continua.
   
    ![Elija Configuración, Exportación continua, Agregar.](./media/app-insights-export-power-bi/060.png)

    Seleccione la cuenta de almacenamiento que creó anteriormente:

    ![Establezca el destino de exportación.](./media/app-insights-export-power-bi/070.png)

    Configure los tipos de eventos que desea ver:

    ![Elija los tipos de evento.](./media/app-insights-export-power-bi/080.png)

1. Permita que se acumulen algunos datos. Póngase cómo y deje que los usuarios usen su aplicación durante un tiempo. Así, aparecerá la telemetría y verá gráficos estadísticos en el [explorador de métricas](app-insights-metrics-explorer.md) y eventos individuales en la [búsqueda de diagnóstico](app-insights-diagnostic-search.md).
   
    Y, además, exportará los datos en el almacenamiento.
2. Inspeccione los datos exportados. En Visual Studio, elija **Ver/Cloud Explorer** y abra Azure/Almacenamiento. (Si no dispone de esta opción de menú, deberá instalar el SDK de Azure: abra el cuadro de diálogo Nuevo proyecto y Visual C#/Nube/Obtener el SDK de Microsoft Azure para. NET.)
   
    ![](./media/app-insights-export-power-bi/04-data.png)
   
    Tome nota de la parte común del nombre de la ruta de acceso, que se deriva del nombre de la aplicación y de la clave de instrumentación.

Los eventos se escriben en archivos de blob en formato JSON. Cada archivo puede contener uno o varios eventos. Así, es probable que queramos leer los datos de eventos y filtrar por los campos que deseemos. Se pueden realizar multitud de acciones con los datos, pero nuestro plan de hoy consiste en usar Stream Analytics para canalizar los datos a Power BI.

### Creación de una instancia de Análisis de transmisiones de Azure
Desde el [Portal de Azure clásico](https://manage.windowsazure.com/), seleccione el servicio Análisis de transmisiones de Azure y cree un nuevo trabajo de Análisis de transmisiones:

![](./media/app-insights-export-power-bi/090.png)

![](./media/app-insights-export-power-bi/100.png)

Cuando se cree el nuevo trabajo, expanda los detalles:

![](./media/app-insights-export-power-bi/110.png)

#### Establecimiento de la ubicación del blob
Configúrelo de manera que tome los datos del blob de Exportación continua:

![](./media/app-insights-export-power-bi/120.png)

Ahora, necesitará la clave de acceso principal de la cuenta de almacenamiento, que anotó anteriormente. Establézcala como la clave de cuenta de almacenamiento.

![](./media/app-insights-export-power-bi/130.png)

#### Establecimiento del patrón del prefijo de la ruta de acceso
![](./media/app-insights-export-power-bi/140.png)

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

#### Finalización de la instalación inicial
Confirme el formato de serialización:

![Confirme y cierre el asistente.](./media/app-insights-export-power-bi/150.png)

Cierre el asistente y espere a que el programa de instalación finalice.

> [!TIP]
> Use el comando de ejemplo para descargar algunos datos. Guardarlo como un ejemplo de prueba para depurar la consulta.
> 
> 

### Establecimiento de la salida
Ahora seleccione el trabajo y establezca la salida.

![Seleccione el canal nuevo, haga clic en Salidas, Agregar, Power BI](./media/app-insights-export-power-bi/160.png)

Ofrezca su **cuenta profesional o educativa** para autorizar el Análisis de transmisiones a tener acceso al recurso de Power BI. Luego invente un nombre para la salida y para la tabla y el conjunto de datos de Power BI de destino.

![Invente tres nombres](./media/app-insights-export-power-bi/170.png)

### Definición de la consulta
La consulta controla la traducción de la entrada en la salida.

![Seleccione el trabajo y haga clic en Consultar. Pegue el siguiente ejemplo.](./media/app-insights-export-power-bi/180.png)

Use la función de prueba para comprobar que obtiene la salida correcta. Proporciónele los datos de ejemplo que tomó de la página de entradas.

#### Consulta para mostrar recuentos de eventos
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

#### Consulta para mostrar valores de métrica
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

#### Consulta para incluir los valores de propiedades de dimensión
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

### Ejecución del trabajo
Puede seleccionar una fecha en el pasado desde la que iniciar el trabajo.

![Seleccione el trabajo y haga clic en Consultar. Pegue el siguiente ejemplo.](./media/app-insights-export-power-bi/190.png)

Espere hasta que el trabajo esté en ejecución.

### Visualización de resultados en Power BI
Abra Power BI con su cuenta profesional o educativa y seleccione el conjunto de datos y la tabla que ha definido como la salida del trabajo del Análisis de transmisiones.

![En Power BI, seleccione el conjunto de datos y los campos.](./media/app-insights-export-power-bi/200.png)

Ahora puede usar este conjunto de datos en informes y paneles de [Power BI](https://powerbi.microsoft.com).

![En Power BI, seleccione el conjunto de datos y los campos.](./media/app-insights-export-power-bi/210.png)

### ¿No hay datos?
* Compruebe que [establece el formato de fecha](#set-path-prefix-pattern) correctamente en AAAA-MM-DD (con guiones).

### Vídeo
Noam Ben Zeev muestra cómo exportar a Power BI.

> [!VIDEO https://channel9.msdn.com/Blogs/Windows-Azure/Export-to-Power-BI-from-Application-Insights/player]
> 
> 

## Material relacionado
* [Exportación continua](app-insights-export-telemetry.md)
* [Referencia detallada del modelo de datos para los tipos y valores de propiedad.](app-insights-export-data-model.md)
* [Application Insights](app-insights-overview.md)
* [Más ejemplos y tutoriales](app-insights-code-samples.md)

<!---HONumber=AcomDC_0420_2016-->