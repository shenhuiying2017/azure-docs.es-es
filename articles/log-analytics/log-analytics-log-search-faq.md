---
title: "Preguntas frecuentes sobre la nueva búsqueda de registros de Log Analytics | Microsoft Docs"
description: "Este artículo ofrece respuesta a las preguntas más frecuentes sobre la actualización de Log Analytics al nuevo lenguaje de consulta."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/27/2017
ms.author: bwren
ms.translationtype: HT
ms.sourcegitcommit: 7456da29aa07372156f2b9c08ab83626dab7cc45
ms.openlocfilehash: d7bd0d780c265cc15ad09a73ede8c5a886005e37
ms.contentlocale: es-es
ms.lasthandoff: 08/28/2017

---

# <a name="log-analytics-new-log-search-faq-and-known-issues"></a>Problemas conocidos y preguntas frecuentes sobre la nueva búsqueda de registros de Log Analytics

Este artículo incluye problemas conocidos y preguntas frecuentes sobre la actualización de [Log Analytics al nuevo lenguaje de consulta](log-analytics-log-search-upgrade.md).  Debe leer el artículo completo antes de tomar la decisión de actualizar el área de trabajo.


## <a name="alerts"></a>Alertas

### <a name="question-i-have-a-lot-of-alert-rules-do-i-need-to-create-them-again-in-the-new-language-after-i-upgrade"></a>Pregunta: Tengo una gran cantidad de reglas de alertas. ¿Es necesario crearlas de nuevo en el nuevo lenguaje después de actualizar?  
No, las reglas de alertas se convierten automáticamente al nuevo lenguaje de búsqueda durante la actualización.  


## <a name="computer-groups"></a>Grupos de equipos

### <a name="question-im-getting-errors-when-trying-to-use-computer-groups--has-their-syntax-changed"></a>Pregunta: Obtengo errores al intentar usar grupos de equipos.  ¿Se ha modificado su sintaxis?
Sí, la sintaxis para usar grupos de equipos cambian cuando se actualiza el área de trabajo.  Consulte [Grupos de equipos en búsquedas de registros en Log Analytics](log-analytics-computer-groups.md) para obtener información detallada.

### <a name="known-issue-groups-imported-from-active-directory"></a>Problema conocido: Grupos importados desde Active Directory
Actualmente no puede crear una consulta que use un grupo de equipos importados desde Active Directory.  Como solución temporal hasta que se corrija este problema, cree un grupo de equipos con el grupo importado desde Active Directory y después use el grupo nuevo en la consulta.

A continuación se indica una consulta de ejemplo para crear un grupo de equipos que incluye un grupo importado de Active Directory:

    ComputerGroup | where GroupSource == "ActiveDirectory" and Group == "AD Group Name" and TimeGenerated >= ago(24h) | distinct Computer


## <a name="dashboards"></a>Paneles

### <a name="question-can-i-still-use-dashboards-in-an-upgraded-workspace"></a>Pregunta: ¿Puedo usar paneles aún en un área de trabajo actualizada?
Puede continuar usando todos los elementos agregados a **Mi panel** antes de la actualización del área de trabajo, pero no puede editarlos ni agregar otros nuevos.  También puede continuar creando y editando vistas con el [Diseñador de vistas](log-analytics-view-designer.md) y crear paneles en Azure Portal.


## <a name="log-searches"></a>Búsqueda de registros

### <a name="question-i-have-saved-searches-outside-of-my-upgraded-workspace-can-i-convert-them-to-the-new-search-language-automatically"></a>Pregunta: He guardado búsquedas fuera de mi área de trabajo actualizada. ¿Puedo convertirlas automáticamente al nuevo lenguaje de consulta?
Puede usar la herramienta del convertidor de lenguaje en la página de búsqueda de registros para convertir cada una de ellas.  No hay ningún método para convertir automáticamente varias búsquedas sin actualizar el área de trabajo.

### <a name="question-why-are-my-query-results-not-sorted"></a>Pregunta: ¿Por qué los resultados de la consulta no están ordenados?
Los resultados no se ordenan de forma predeterminada en el nuevo lenguaje de consulta.  Use el [operador sort](https://go.microsoft.com/fwlink/?linkid=856079) para ordenar los resultados en función de una o varias propiedades.

### <a name="known-issue-search-results-in-a-list-may-include-properties-with-no-data"></a>Problema conocido: Los resultados de la búsqueda de una lista pueden incluir propiedades sin datos
Los resultados de la búsqueda de registros de una lista pueden mostrar propiedades sin datos.  Antes de la actualización, estas propiedades no se incluirían.  Este problema se corregirá para que no se muestren las propiedades vacías.

### <a name="known-issue-selecting-a-value-in-a-chart-doesnt-display-detailed-results"></a>Problema conocido: Al seleccionar el valor de un gráfico, no se muestran resultados detallados
Antes de la actualización, cuando se seleccionaba un valor en un gráfico, se devolvía una lista detallada de los registros que coincidían con el valor seleccionado.  Después de la actualización, solo se devuelve la única línea resumida.  Actualmente se está investigando este problema.

## <a name="log-search-api"></a>API de búsqueda de registros

### <a name="question-does-the-log-search-api-get-updated-after-i-upgrade"></a>Pregunta: ¿También se actualiza la API de búsqueda de registros?
La [API de búsqueda de registros](log-analytics-log-search-api.md) aún no se ha actualizado al nuevo lenguaje de búsqueda.  Continúe usando el lenguaje de consulta heredado con esta API, incluso después de actualizar el área de trabajo.  La documentación actualizada estará disponible para la API de búsqueda de registros en cuanto esta se actualice.


## <a name="portals"></a>Portales

### <a name="question-should-i-use-the-new-advanced-analytics-portal-or-keep-using-the-log-search-portal"></a>Pregunta: ¿Debo usar el nuevo portal de análisis avanzado o continuar con el portal de búsqueda de registros?
Puede ver una comparación de los dos portales en [Portales para la creación y edición de consultas de registros en Azure Log Analytics](log-analytics-log-search-portals.md).  Cada uno tiene distintas ventajas, para que pueda elegir el que mejor satisfaga sus necesidades.  Es común escribir consultas en el portal de análisis avanzado y copiarlas en otros lugares, como el Diseñador de vistas.  Debe leer sobre las [cuestiones que debe tener en cuenta](log-analytics-log-search-portals.md#advanced-analytics-portal) para ejecutar lo anterior.


## <a name="power-bi"></a>Power BI

### <a name="question-does-anything-change-with-powerbi-integration"></a>Pregunta: ¿Hay algún cambio en la integración con Power BI?
Sí.  Una vez que se haya actualizado el área de trabajo, el proceso de exportación de datos de Log Analytics a Power BI dejará de funcionar.  Se deshabilitarán las programaciones existentes creadas antes de actualizar.  Después de la actualización, Azure Log Analytics usa la misma plataforma que Application Insights y el usuario utilizará el mismo proceso para exportar las consultas de Log Analytics a Power BI que se describe en [el proceso de exportación de consultas de Application Insights a Power BI](../application-insights/app-insights-export-power-bi.md#export-analytics-queries).

### <a name="known-issue-power-bi-request-size-limit"></a>Problema conocido: Límite de tamaño de solicitud de Power BI
Actualmente hay un límite de tamaño de 8 MB para una consulta de Log Analytics que se puede exportar a Power BI.  Este límite se a aumentará próximamente.


##<a name="powershell-cmdlets"></a>Cmdlets de PowerShell

### <a name="question-does-the-log-search-powershell-cmdlet-get-updated-after-i-upgrade"></a>Pregunta: ¿También se actualiza el cmdlet de PowerShell para búsqueda de registros?
El cmdlet [Get-AzureRmOperationalInsightsSearchResults](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/Get-AzureRmOperationalInsightsSearchResults) aún no se ha actualizado al nuevo lenguaje de búsqueda.  Continúe usando el lenguaje de consulta heredado con este cmdlet, incluso después de actualizar el área de trabajo.  La documentación actualizada estará disponible para el cmdlet en cuanto este se actualice.


## <a name="resource-manager-templates"></a>Plantillas de Resource Manager

### <a name="question-can-i-create-an-upgraded-workspace-with-a-resource-manager-template"></a>Pregunta: ¿Puedo crear un área de trabajo actualizada con una plantilla de Resource Manager?
Sí.  Debe usar una versión de API de 2017-03-15-preview e incluir una sección **features** en la plantilla, como en el ejemplo siguiente.

    "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "apiVersion": "2017-03-15-preview",
            "name": "[parameters('workspaceName')]",
            "location": "[parameters('workspaceRegion')]",
            "properties": {
                "sku": {
                    "name": "Free"
                },
                "features": {
                    "legacy": 0,
                    "searchVersion": 1
                }
            }
        }
    ],



## <a name="solutions"></a>Soluciones

### <a name="question-will-my-solutions-continue-to-work"></a>Pregunta: ¿Mis soluciones continuarán funcionando?
Todas las soluciones continuarán funcionando en un área de trabajo actualizada, aunque su rendimiento mejorará si se migran al nuevo lenguaje de consulta.  Existen problemas conocidos con algunas soluciones existentes que se describen en esta sección.

### <a name="known-issue-capacity-and-performance-solution"></a>Problema conocido: Solución Capacity and Performance
Algunas de las partes de la vista [Capacity and Performance](log-analytics-capacity.md) pueden estar vacías.  Una corrección para este problema estará disponible pronto.

### <a name="known-issue-device-health-solution"></a>Problema conocido: Solución Estado de dispositivos
La [solución Estado de dispositivos](https://docs.microsoft.com/windows/deployment/update/device-health-monitor) no recopilará datos en un área de trabajo actualizada.  Una corrección para este problema estará disponible pronto.

### <a name="known-issue-application-insights-connector"></a>Problema conocido: Application Insights Connector
Las perspectivas de la [solución Application Insights Connector](log-analytics-app-insights-connector.md) no se admiten actualmente en un área de trabajo actualizada.  En estos momentos se está analizando una corrección para este problema.

## <a name="upgrade-process"></a>Proceso de actualización

### <a name="question-i-have-several-workspaces-can-i-upgrade-all-workspaces-at-the-same-time"></a>Pregunta: Tengo varias áreas de trabajo. ¿Puedo actualizar todas las áreas de trabajo al mismo tiempo?  
No.  La actualización se aplica a una sola área de trabajo cada vez. Actualmente no hay ninguna manera de actualizar muchas áreas de trabajo a la vez. Tenga en cuenta que otros usuarios del área de trabajo actualizada también se verán afectados.  

### <a name="question-will-existing-log-data-collected-in-my-workspace-be-modified-if-i-upgrade"></a>Pregunta: ¿Se modificarán los datos de los registros existentes recopilados en mi área de trabajo si la actualizo?  
No. La actualización no afecta a los datos de los registros disponibles para las búsquedas en el área de trabajo. Las búsquedas, alertas y vistas guardadas se convertirán al nuevo lenguaje de búsqueda automáticamente.  

### <a name="question-what-happens-if-i-dont-upgrade-my-workspace"></a>Pregunta: ¿Qué sucede si no actualizo mi área de trabajo?  
La búsqueda de registros heredada dejará de utilizarse en los próximos meses. Las áreas de trabajo que no se hayan actualizado en esa fecha se actualizarán automáticamente.

### <a name="question-i-didnt-choose-to-upgrade-but-my-workspace-has-been-upgraded-anyway-what-happened"></a>Pregunta: Yo no elegí actualizar y, sin embargo, el área de trabajo se ha actualizado igualmente. ¿Qué ha ocurrido?  
Otro administrador de esta área de trabajo la ha actualizado. Tenga en cuenta que todas las áreas de trabajo se actualizarán automáticamente cuando el nuevo lenguaje tenga disponibilidad general.  

### <a name="question-i-have-upgraded-by-mistake-and-now-need-to-cancel-it-and-restore-everything-back-what-should-i-do"></a>Pregunta: He actualizado por error y ahora necesito cancelar la actualización y restaurarlo todo de nuevo. ¿Qué debo hacer?  
No se preocupe.  Se crea una instantánea del área de trabajo antes de la actualización, con lo cual puede restaurarla si es necesario. Aunque debe tener en cuenta que las búsquedas, alertas o vistas que guardó después de la actualización se perderán.  Para restaurar el entorno del área de trabajo, siga el procedimiento descrito en [¿Se puede revertir la actualización?](log-analytics-log-search-upgrade.md#can-i-go-back-after-i-upgrade).


## <a name="views"></a>Vistas

### <a name="question-how-do-i-create-a-new-view-with-view-designer"></a>Pregunta: ¿Cómo puedo crear una vista con el Diseñador de vistas?
Antes de la actualización, podría crear una vista con el Diseñador de vistas de algún elemento del panel principal.  Cuando se actualiza el área de trabajo, este elemento se elimina.  Puede crear una vista con el Diseñador de vistas en el portal de OMS; para ello, haga clic en el botón + de color verde en el menú de la izquierda.

### <a name="known-issue-see-all-option-for-line-charts-in-views-doesnt-result-in-a-line-chart"></a>Problema conocido: La opción Ver todo para los gráficos de líneas de las vistas no genera un gráfico de líneas
Al hacer clic en la opción *Ver todo* en la parte inferior de una parte del gráfico de líneas de una vista, se le presentará una tabla.  Antes de la actualización, se le presentaría un gráfico de líneas.  Este problema se está analizando para una posible modificación.


## <a name="next-steps"></a>Pasos siguientes

- Obtenga más información sobre la [actualización del área de trabajo al nuevo lenguaje de consulta de Log Analytics](log-analytics-log-search-upgrade.md).

