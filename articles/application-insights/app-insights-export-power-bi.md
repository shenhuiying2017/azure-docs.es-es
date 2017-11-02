---
title: "Exportación a Power BI desde Application Insights | Microsoft Docs"
description: Las consultas de Analytics se pueden mostrar en Power BI.
services: application-insights
documentationcenter: 
author: noamben
manager: carmonm
ms.assetid: 7f13ea66-09dc-450f-b8f9-f40fdad239f2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: mbullwin
ms.openlocfilehash: fe708b14fac971d18d95fd1619907023ec35af89
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/01/2017
---
# <a name="feed-power-bi-from-application-insights"></a>Alimentación de Power BI desde Application Insights
[Power BI](http://www.powerbi.com/) es un conjunto de herramientas de análisis de negocios que pueden ayudar a analizar datos y compartir conocimientos. Cada dispositivo cuenta con paneles que incluyen gran cantidad de datos. Puede combinar datos de varios orígenes, incluidas las consultas de Analytics en [Azure Application Insights](app-insights-overview.md).

Hay tres métodos recomendados para exportar datos de Application Insights a Power BI. Se pueden utilizar por separado o conjuntamente.

* [**Adaptador de Power BI**](#power-pi-adapter): configure un panel completo de telemetría desde la aplicación. El conjunto de gráficos está predefinido, pero puede agregar sus propias consultas de cualquier otro origen.
* [**Exportar consultas de Analytics**](#export-analytics-queries): escriba las consultas que quiera con Analytics y expórtelas a Power BI. Puede colocar esta consulta en un panel junto con otros datos.
* [**Exportación continua y Stream Analytics**](app-insights-export-stream-analytics.md): esto implica más trabajo de configuración. Es útil si desea conservar los datos durante largos períodos. De lo contrario, se recomiendan los otros métodos.

## <a name="power-bi-adapter"></a>Adaptador de Power BI
Este método crea un panel completo de telemetría. El conjunto de datos inicial está predefinido, pero puede agregar más datos a él.

### <a name="get-the-adapter"></a>Obtención del adaptador
1. Inicie sesión en [Power BI](https://app.powerbi.com/).
2. Abra **Obtener datos**, **Servicios**, **Application Insights**
   
    ![Obtención de un origen de datos de Application Insights](./media/app-insights-export-power-bi/power-bi-adapter.png)
3. Proporcione los detalles de su recurso de Application Insights.
   
    ![Obtención de un origen de datos de Application Insights](./media/app-insights-export-power-bi/azure-subscription-resource-group-name.png)
4. Espere un minuto o dos para que se importen los datos.
   
    ![Adaptador de Power BI](./media/app-insights-export-power-bi/010.png)

Puede modificar el panel, combinando los gráficos de Application Insights con los de otras fuentes y con consultas de Analytics. Tiene a su disposición una galería de visualización en la que puede obtener más gráficos; y cada gráfico tiene a su vez parámetros que puede configurar.

Después de la importación inicial, el panel y los informes seguirán actualizándose a diario. Puede controlar la programación de la actualización en el conjunto de datos.

## <a name="export-analytics-queries"></a>Exportación de consultas de Analytics
Esta ruta le permite escribir las consultas de Analytics que quiera y, después, exportarlas a un panel de Power BI. (Puede agregar al panel creado por el adaptador).

### <a name="one-time-install-power-bi-desktop"></a>Una vez: instalar Power BI Desktop
Para importar la consulta de Application Insights, utilice la versión de escritorio de Power BI. Después, puede publicarla en la web o en el área de trabajo de nube de Power BI. 

Instale [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/).

### <a name="export-an-analytics-query"></a>Exportación de una consulta de Analytics
1. [Abra Analytics y escriba la consulta](app-insights-analytics-tour.md).
2. Pruebe y refine la consulta hasta que esté satisfecho con los resultados.

   **Asegúrese de que la consulta se ejecuta correctamente en Analytics antes de exportarla.**
3. En el menú **Exportar**, elija **Power BI (M)**. Guarde el archivo de texto.
   
    ![Exportación de la consulta de Power BI](./media/app-insights-export-power-bi/analytics-export-power-bi.png)
4. En Power BI Desktop, seleccione **Obtener datos, Consulta en blanco** y, después, en el editor de consultas en **Ver**, seleccione **Editor de consultas avanzadas**.

    Pegue el script del lenguaje M exportado en el editor de consultas avanzadas.

    ![Editor de consultas avanzadas](./media/app-insights-export-power-bi/power-bi-import-analytics-query.png)

1. Es posible que deba proporcionar credenciales para permitir que Power BI acceda a Azure. Utilice la cuenta de organización para iniciar sesión con su cuenta Microsoft.
   
    ![Proporcionar las credenciales de Azure para habilitar Power BI para que ejecute la consulta de Application Insights](./media/app-insights-export-power-bi/power-bi-import-sign-in.png)

    (Si tiene que comprobar las credenciales, use el comando del menú Configuración de origen de datos en el Editor de consultas. Tenga cuidado al especificar las credenciales que use para Azure ya que pueden ser diferentes de las credenciales de Power BI).
2. Elija una visualización para la consulta y seleccione los campos para el eje x, eje y y la dimensión de segmentación.
   
    ![Seleccionar la visualización](./media/app-insights-export-power-bi/power-bi-analytics-visualize.png)
3. Publique el informe en el área de trabajo de nube de Power BI. Desde allí, puede incluir una versión sincronizada en otras páginas web.
   
    ![Seleccionar la visualización](./media/app-insights-export-power-bi/publish-power-bi.png)
4. Actualice el informe manualmente a intervalos o configure una actualización programada en la página Opciones.

## <a name="troubleshooting"></a>Solución de problemas

### <a name="401-or-403-unauthorized"></a>401 o 403 No autorizado 
Esto puede ocurrir si no se ha actualizado su token de actualización. Pruebe estos pasos para asegurarse de que todavía tiene acceso. Si tiene acceso pero la actualización de las credenciales no funciona, abra una incidencia de soporte técnico.

1. Inicie sesión en Azure Portal y asegúrese de que pueda acceder al recurso
2. Intente actualizar las credenciales en el panel

### <a name="502-bad-gateway"></a>Puerta de enlace incorrecta 502
Esto se debe normalmente a una consulta de Analytics que devuelve demasiados datos. También debe intentar usar un intervalo de tiempo más reducido o usar las funciones [ago](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-analytics-reference#ago) o [startofweek/startofmonth](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-analytics-reference#startofweek) para que [proyect](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-analytics-reference#project-operator) recupere solo los campos que necesite.

Si la reducción del conjunto de datos que procede de la consulta de Analytics no se ajusta a sus requisitos, debe considerar el uso de la [API](https://dev.applicationinsights.io/documentation/overview) para extraer un conjunto de datos más grande. Las siguientes instrucciones le muestran cómo convertir la exportación de M-Query para usar la API.

1. Cree una [clave de API](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID).
2. Actualice el script M de Power BI que exportó desde Analytics; para ello, reemplace la dirección URL de ARM por la API AI (ver el ejemplo siguiente).
   * Reemplace **https://management.azure.com/subscriptions/...**
   * por **https://api.applicationinsights.io/beta/apps/...**
3. Por último, actualice las credenciales al modo básico y use su clave de API.
  

**Script existente**
 ```
 Source = Json.Document(Web.Contents("https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups//providers/microsoft.insights/components//api/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```
**Script actualizado**
 ```
 Source = Json.Document(Web.Contents("https://api.applicationinsights.io/beta/apps/<APPLICATION_ID>/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```

## <a name="about-sampling"></a>Acerca del muestreo
Si la aplicación envía muchos datos, la característica de muestreo adaptativo puede operar y enviar solamente un porcentaje de los datos de telemetría. Esto se aplica igualmente si ha configurado el muestreo manualmente en el SDK o en la recopilación. [Obtenga más información sobre el muestreo.](app-insights-sampling.md)


## <a name="next-steps"></a>Pasos siguientes
* [Power BI: más información](http://www.powerbi.com/learning/)
* [Tutorial de Analytics](app-insights-analytics-tour.md)

