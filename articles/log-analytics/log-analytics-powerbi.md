---
title: Importación de datos de Azure Log Analytics en Power BI | Microsoft Docs
description: Power BI es un servicio de análisis empresarial basado en la nube de Microsoft que proporciona unos excelentes informes y visualizaciones para el análisis de los distintos conjuntos de datos.  En este artículo se describe cómo configurar e importar datos de Log Analytics en Power BI y cómo configurarlos para que se actualicen automáticamente.
services: log-analytics
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 83edc411-6886-4de1-aadd-33982147b9c3
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/19/2018
ms.author: bwren
ms.openlocfilehash: 725828c2acc5ac4bb53c5e6af14d20578a3d3652
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/28/2018
ms.locfileid: "30236155"
---
# <a name="import-azure-log-analytics-data-into-power-bi"></a>Importación de datos de Azure Log Analytics en Power BI


[Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) es un servicio de análisis empresarial basado en la nube de Microsoft que proporciona unos excelentes informes y visualizaciones para el análisis de los distintos conjuntos de datos.  Puede importar los resultados de una búsqueda de registros de Log Analytics en un conjunto de datos de Power BI para poder beneficiarse de sus características, como la combinación de datos de diferentes orígenes y el uso compartido de informes en los dispositivos web y móviles.

## <a name="overview"></a>Información general
Para importar datos desde un área de trabajo de Log Analytics en Power BI, debe crear un conjunto de datos en Power BI basado en una consulta de búsqueda de registros en Log Analytics.  La consulta se ejecuta cada vez que se actualiza el conjunto de datos.  Después, puede crear informes de Power BI que usen datos del conjunto de datos.  Para crear el conjunto de datos en Power BI, exporte la consulta de Log Analytics al [lenguaje de Power Query (M)](https://msdn.microsoft.com/library/mt807488.aspx).  Después, use esto para crear una consulta en Power BI Desktop y publíquela en Power BI como un conjunto de datos.  A continuación se describen los detalles de este proceso.

![Log Analytics a Power BI](media/log-analytics-powerbi/overview.png)

## <a name="export-query"></a>Exportación de consultas
Empiece por crear una [búsqueda de registros](log-analytics-log-search-new.md) que devuelva los datos de Log Analytics con los que desea rellenar el conjunto de datos de Power BI.  Después, exporte dicha consulta al [lenguaje de Power Query (M)](https://msdn.microsoft.com/library/mt807488.aspx), que es el que Power BI Desktop puede utilizar.

1. Cree la búsqueda de registros en Log Analytics para extraer los datos para el conjunto de datos.
2. Si usa el portal de la búsqueda de registros, haga clic en **Power BI**.  Si usa el portal de Analytics portal, seleccione **Exportar** > **Power BI Query (M)** [Consulta de Power BI (M)].  Ambas opciones exportan la consulta a un archivo de texto con nombre **PowerBIQuery.txt**. 

    ![Exportación de la búsqueda de registros](media/log-analytics-powerbi/export-logsearch.png) ![Exportación de la búsqueda de registros](media/log-analytics-powerbi/export-analytics.png)

3. Abra el archivo de texto y copie su contenido.

## <a name="import-query-into-power-bi-desktop"></a>Importación de consultas en Power BI Desktop
Power BI Desktop es una aplicación de escritorio que le permite crear conjuntos de datos e informes que se pueden publicar en Power BI.  También puede usarlo para crear una consulta mediante el lenguaje de Power Query exportado de Log Analytics. 

1. Instale [Power BI Desktop](https://powerbi.microsoft.com/desktop/) si aún no lo tiene y después abra la aplicación.
2. Seleccione **Obtener datos** > **Consulta en blanco** para abrir una consulta nueva.  Después, seleccione **Editor avanzado** y pegue el contenido del archivo exportado en la consulta. Haga clic en **Done**(Listo).

    ![Consulta de Power BI Desktop](media/log-analytics-powerbi/desktop-new-query.png)

5. La consulta se ejecuta y después se muestran los resultados.  Puede que se le soliciten credenciales para conectarse a Azure.  
6. Escriba un nombre descriptivo para la consulta.  El nombre predeterminado es **Query1**. Haga clic en **Cerrar y aplicar** para agregar el conjunto de datos al informe.

    ![Nombre de Power BI Desktop](media/log-analytics-powerbi/desktop-results.png)



## <a name="publish-to-power-bi"></a>Publicación en Power BI
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



## <a name="next-steps"></a>Pasos siguientes
* Obtenga información sobre de las [búsquedas de registro](log-analytics-log-searches.md) para crear consultas que se puedan exportar a Power BI.
* Obtenga más información sobre [Power BI](http://powerbi.microsoft.com) para generar visualizaciones basadas en exportaciones de Log Analytics.
