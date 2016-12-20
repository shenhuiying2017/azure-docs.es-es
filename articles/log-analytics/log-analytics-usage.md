---
title: "Análisis del uso de datos en Log Analytics | Microsoft Docs"
description: "Puede emplear el panel Uso en Log Analytics para ver la cantidad de datos que se envían al servicio OMS."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: jwhit
editor: 
ms.assetid: 74d0adcb-4dc2-425e-8b62-c65537cef270
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/14/2016
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 668cde23cb717fcad52fd7823561d10735e6df1b


---
# <a name="analyze-data-usage-in-log-analytics"></a>Análisis del uso de datos en Log Analytics
Log Analytics recopila datos y los envía al servicio OMS periódicamente.  Puede usar el panel **Uso de Log Analytics** para ver la cantidad de datos que se envían al servicio OMS. Además, el panel muestra la cantidad de datos que las soluciones envían y la frecuencia con que los servidores envían datos.

> [!NOTE]
> Si tiene una cuenta gratuita, dispone de un límite diario de 500 MB de datos para el servicio OMS. Si alcanza este límite, se detiene el análisis de los datos y se reanuda al comenzar el día siguiente. En ese caso, debe volver a enviar aquellos datos que OMS no haya aceptado o procesado.

Si ha superado su límite de uso diario o está cerca de él, tiene la posibilidad de quitar una solución para reducir la cantidad de datos que envía al servicio OMS. Para obtener más información sobre como quitar soluciones, consulte [Add Log Analytics solutions from the Solutions Gallery](log-analytics-add-solutions.md)(Incorporación de soluciones de Log Analytics desde la galería de soluciones).

![panel de uso](./media/log-analytics-usage/usage-dashboard01.png)

El panel **Uso de Log Analytics** muestra la siguiente información:

- Volumen de datos
    - Data volume over time (Volumen de datos con el tiempo), en función del ámbito temporal actual
    - Data volume by solution (Volumen de datos por solución)
    - Data not associated with a computer (Datos no asociados a un equipo)
- Equipos
    - Computers sending data (Equipos que envían datos)
    - Computers with no data in last 24 hours (Equipos sin datos en las últimas 24 horas)
- Offerings (Ofertas)
    - Insight and Analytics nodes (Nodos Insight y Analytics)
    - Automation and Control nodes (Nodos Automation y Control)
    - Security nodes (Nodos de seguridad)
- Rendimiento
    - Time taken to collect and index data (Tiempo dedicado a recopilar e indexar datos)
- Lista de consultas

## <a name="to-work-with-usage-data"></a>Para trabajar con datos de uso, siga estos pasos:
1. Si aún no lo ha hecho, inicie sesión en [Azure Portal](https://portal.azure.com) mediante su suscripción de Azure.
2. En el menú **central**, haga clic en **Más servicios** y, en la lista de recursos, escriba **Log Analytics**. Cuando comience a escribir, la lista se filtrará en función de la entrada. Haga clic en **Log Analytics**.  
    ![Menú central de Azure](./media/log-analytics-usage/hub.png)
3. El panel **Log Analytics** muestra una lista de las áreas de trabajo. Seleccione un área de trabajo.
4. En el panel del *área de trabajo*, haga clic en **Uso de Log Analytics**.
5. En el panel **Uso de Log Analytics**, haga clic en **Tiempo: Últimas 24 horas** para cambiar el intervalo de tiempo.  
    ![Intervalo de tiempo](./media/log-analytics-usage/time.png)
6. Vea las hojas de categoría de uso que muestren áreas que le interesen. Elija una hoja y haga clic en un elemento en ella para ver más detalles en [Búsqueda de registros](log-analytics-log-searches.md).  
    ![Hoja de uso de datos de ejemplo](./media/log-analytics-usage/blade.png)
7. En el panel Búsqueda de registros, revise los resultados devueltos por la búsqueda.  
    ![Búsqueda de registros de uso de ejemplo](./media/log-analytics-usage/usage-log-search.png)


## <a name="next-steps"></a>Pasos siguientes
* Consulte [Búsquedas de registros en Log Analytics](log-analytics-log-searches.md) para ver la información detallada que las soluciones y características recopilan y envían a OMS.



<!--HONumber=Dec16_HO2-->


