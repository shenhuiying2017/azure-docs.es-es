<properties 
	pageTitle="Consulte Datos de Application Insights en Power BI" 
	description="Use Power BI para supervisar el rendimiento y el uso de la aplicación." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/04/2015" 
	ms.author="awills"/>
 
# Vistas de datos de Application Insights en Power BI

[Microsoft Power BI](https://powerbi.microsoft.com/) presenta los datos en objetos visuales enriquecidos y variados, con la capacidad de reunir información de varios orígenes. Puede transmitir los datos de telemetría sobre el rendimiento y el uso de las aplicaciones web o de dispositivo de Application Insights a Power BI.

![Ejemplo de vista en Power BI de los datos de uso de Application Insights](./media/app-insights-export-power-bi/010.png)

En este artículo, le mostraremos cómo exportar datos de Application Insights y utilizar el Análisis de transmisiones para transmitir los datos a Power BI. El [Análisis de transmisiones](http://azure.microsoft.com/services/stream-analytics/) es un servicio de Azure que vamos a usar como un adaptador.

![Ejemplo de vista en Power BI de los datos de uso de Application Insights](./media/app-insights-export-power-bi/020.png)

## Vídeo

Noam Ben Zeev muestra lo que describimos en este artículo.

> [AZURE.VIDEO export-to-power-bi-from-application-insights]

## Supervisión de aplicaciones con Application Insights

Si aún no lo ha intentado, ahora es el momento de empezar. Application Insights puede supervisar cualquier aplicación web o de dispositivo en una amplia gama de plataformas, entre ellas, Windows, iOS, Android, J2EE y muchas más. [Introducción](app-insights-get-started.md).

## Creación de almacenamiento en Azure

La exportación continua siempre envía los datos a una cuenta de almacenamiento de Azure, por lo que necesitará crear primero el almacenamiento.

1. Cree una cuenta de almacenamiento en su suscripción en el [portal de Azure](https://portal.azure.com).

    ![En el portal de Azure, elija Nuevo, Datos, Almacenamiento.](./media/app-insights-export-power-bi/030.png)

2. Crear un contenedor

    ![En el nuevo almacenamiento, seleccione Contenedores y, a continuación, Agregar.](./media/app-insights-export-power-bi/040.png)

3. Copie la clave de acceso de almacenamiento.

    La necesitará en seguida para configurar la entrada para el servicio de análisis de transmisiones.

    ![En el almacenamiento, abra Configuración, Claves y realice una copia de la clave de acceso principal.](./media/app-insights-export-power-bi/045.png)

## Inicio de la exportación continua al almacenamiento de Azure

La [exportación continua](app-insights-export-telemetry.md) transfiere los datos de Application Insights al almacenamiento de Azure.

1. En el portal de Azure, busque el recurso de Application Insights que ha creado para la aplicación.

    ![Elija Examinar, Application Insights, su aplicación.](./media/app-insights-export-power-bi/050.png)

2. Cree una exportación continua.

    ![Elija Configuración, Exportación continua, Agregar.](./media/app-insights-export-power-bi/060.png)


    Seleccione la cuenta de almacenamiento que creó anteriormente:

    ![Establezca el destino de exportación.](./media/app-insights-export-power-bi/070.png)
    
    Configure los tipos de eventos que desea ver:

    ![Elija los tipos de evento.](./media/app-insights-export-power-bi/080.png)

Ahora deje que los usuarios utilicen la aplicación durante un tiempo. Así, aparecerá la telemetría y verá gráficos estadísticos en el [explorador de métricas](app-insights-metrics-explorer.md) y eventos individuales en la [búsqueda de diagnóstico](app-insights-diagnostic-search.md).

Y, además, exportará los datos en el almacenamiento.

## Creación de una instancia de Análisis de transmisiones de Azure

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

Asegúrese de establecer el formato de fecha como AAAA-MM-DD (con guiones).

El patrón del prefijo de la ruta de acceso especifica cómo busca el Análisis de transmisiones los archivos de entrada en el almacenamiento. Deberá configurarlo para que coincida con el modo en que la Exportación continua almacena los datos. Configúrelo como este caso que se muestra a continuación:

    webapplication27_100000000-0000-0000-0000-000000000000/PageViews/{date}/{time}

En este ejemplo:

* `webapplication27` es el nombre del recurso de Application Insights. 
* `1000...` es la clave de instrumentación que copió del recurso de Application Insights. 
* `PageViews` es el tipo de datos que se van a analizar. Los tipos disponibles dependen del filtro definido en la Exportación continua. Examine los datos exportados para ver los demás tipos disponibles.
* `/{date}/{time}` es un patrón escrito literalmente.

Para obtener el nombre y el valor iKey del recurso de Application Insights, abra Essentials en su página de información general o bien abra la Configuración.

#### Finalización de la instalación inicial

Confirme el formato de serialización:

![Confirme y cierre el asistente.](./media/app-insights-export-power-bi/150.png)

Cierre el asistente y espere a que el programa de instalación finalice.

## Establecimiento de la salida

Ahora seleccione el trabajo y establezca la salida.

![Seleccione el canal nuevo, haga clic en Salidas, Agregar, Power BI](./media/app-insights-export-power-bi/160.png)

Autorice el acceso del Análisis de transmisiones al recurso de Power BI y, a continuación, cree un nombre para la salida y para la tabla y el conjunto de datos de Power BI de destino.

![Invente tres nombres](./media/app-insights-export-power-bi/170.png)

## Definición de la consulta

La consulta controla la traducción de la entrada en la salida.

![Seleccione el trabajo y haga clic en Consultar. Pegue el siguiente ejemplo.](./media/app-insights-export-power-bi/180.png)

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
* Usamos GetElements porque el nombre de evento está en una matriz JSON anidada. A continuación, la opción Select selecciona el nombre de evento, junto con el recuento del número de instancias con dicho nombre en el período de tiempo. La cláusula Group By agrupa los elementos en períodos de tiempo de 1 minuto.

## Ejecución del trabajo

Puede seleccionar una fecha en el pasado desde la que iniciar el trabajo.

![Seleccione el trabajo y haga clic en Consultar. Pegue el siguiente ejemplo.](./media/app-insights-export-power-bi/190.png)

Espere hasta que el trabajo esté en ejecución.

## Visualización de resultados en Power BI

Abra Power BI y seleccione el conjunto de datos y la tabla que ha definido como la salida del trabajo del Análisis de transmisiones.

![En Power BI, seleccione el conjunto de datos y los campos.](./media/app-insights-export-power-bi/200.png)

Ahora puede utilizar este conjunto de datos en los informes y paneles de [Power BI](https://powerbi.microsoft.com).


![En Power BI, seleccione el conjunto de datos y los campos.](./media/app-insights-export-power-bi/210.png)

## Vídeo

Noam Ben Zeev muestra cómo exportar a Power BI.

> [AZURE.VIDEO export-to-power-bi-from-application-insights]

## Material relacionado

* [Exportación continua](app-insights-export-telemetry.md)
* [Application Insights](app-insights-overview.md)
* [Más ejemplos y tutoriales](app-insights-code-samples.md)

<!---HONumber=August15_HO6-->