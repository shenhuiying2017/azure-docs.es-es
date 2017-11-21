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
ms.date: 11/02/2017
ms.author: bwren
ms.openlocfilehash: 1ec815a12cea98228dd4b7ac7361fe5e3554b5d3
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2017
---
# <a name="log-analytics-new-log-search-faq-and-known-issues"></a>Problemas conocidos y preguntas frecuentes sobre la nueva búsqueda de registros de Log Analytics

Este artículo incluye problemas conocidos y preguntas frecuentes sobre la actualización de [Log Analytics al nuevo lenguaje de consulta](log-analytics-log-search-upgrade.md).  Debe leer el artículo completo antes de tomar la decisión de actualizar el área de trabajo.


## <a name="alerts"></a>Alertas

### <a name="question-i-have-a-lot-of-alert-rules-do-i-need-to-create-them-again-in-the-new-language-after-i-upgrade"></a>Pregunta: Tengo una gran cantidad de reglas de alertas. ¿Es necesario crearlas de nuevo en el nuevo lenguaje después de actualizar?  
No, las reglas de alertas se convierten automáticamente al nuevo lenguaje de búsqueda durante la actualización.  

### <a name="question-i-have-alert-rules-with-webhook-and-runbook-actions-will-these-continue-to-work-when-i-upgrade"></a>Pregunta: Tengo reglas de alertas con acciones de webhook y runbook. ¿Seguirán funcionando cuando actualice?

No, hay algunos cambios en las acciones de webhook y runbook que podrían requerir que haga cambios en el modo en que procesa la carga. Hemos realizado estos cambios para normalizar los distintos formatos de salida y reducir el tamaño de la carga. Los detalles acerca de estos formatos se encuentran en [Adición de acciones a reglas de alerta en Log Analytics](log-analytics-alerts-actions.md).


## <a name="computer-groups"></a>Grupos de equipos

### <a name="question-im-getting-errors-when-trying-to-use-computer-groups--has-their-syntax-changed"></a>Pregunta: Obtengo errores al intentar usar grupos de equipos.  ¿Se ha modificado su sintaxis?
Sí, la sintaxis para usar grupos de equipos cambian cuando se actualiza el área de trabajo.  Consulte [Grupos de equipos en búsquedas de registros en Log Analytics](log-analytics-computer-groups.md) para obtener información detallada.


## <a name="dashboards"></a>Paneles

### <a name="question-can-i-still-use-dashboards-in-an-upgraded-workspace"></a>Pregunta: ¿Puedo usar paneles aún en un área de trabajo actualizada?
Con la actualización, se da comienzo al proceso por el cual **Mi panel** va a quedar en desuso.  Puede continuar usando todos los elementos agregados al panel antes de la actualización del área de trabajo, pero no puede editarlos ni agregar otros nuevos.  También puede continuar creando y editando vistas con el [Diseñador de vistas](log-analytics-view-designer.md), que tiene un conjunto de características más completo, y crear paneles en Azure Portal.


## <a name="log-searches"></a>Búsqueda de registros

### <a name="question-i-have-saved-searches-outside-of-my-upgraded-workspace-can-i-convert-them-to-the-new-search-language-automatically"></a>Pregunta: He guardado búsquedas fuera de mi área de trabajo actualizada. ¿Puedo convertirlas automáticamente al nuevo lenguaje de consulta?
Puede usar la herramienta del convertidor de lenguaje en la página de búsqueda de registros para convertir cada una de ellas.  No hay ningún método para convertir automáticamente varias búsquedas sin actualizar el área de trabajo.

### <a name="question-why-are-my-query-results-not-sorted"></a>Pregunta: ¿Por qué los resultados de la consulta no están ordenados?
Los resultados no se ordenan de forma predeterminada en el nuevo lenguaje de consulta.  Use el [operador sort](https://go.microsoft.com/fwlink/?linkid=856079) para ordenar los resultados en función de una o varias propiedades.

### <a name="question-where-did-the-metrics-view-go-after-i-upgraded"></a>Pregunta: ¿Dónde está la vista de métricas después de la actualización?
La vista de métricas ofrecía una representación gráfica de los datos de rendimiento a partir de una búsqueda de registros.  Esta vista ya no está disponible después de la actualización.  Puede usar el [operador de representación](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/render-operator) para dar formato al resultado de una consulta en un gráfico de tiempo.

### <a name="question-where-did-minify-go-after-i-upgraded"></a>Pregunta: ¿Dónde está la opción de minificación después de la actualización?
La minificación es una característica que proporciona una vista resumida de los resultados de la búsqueda.  Después de la actualización, la opción de minificación ya no aparece en el portal de búsqueda de registros.  Puede obtener una funcionalidad similar con el nuevo lenguaje de búsqueda si usa [reduce](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/reduce-operator) o [autocluster_v2](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/evaluate-operator/autocluster). 

    Event
    | where TimeGenerated > ago(10h)
    | reduce by RenderedDescription

    Event
    | where TimeGenerated > ago(10h)
    | evaluate autocluster_v2()



## <a name="log-search-api"></a>API de búsqueda de registros

### <a name="question-does-the-log-search-api-get-updated-after-i-upgrade"></a>Pregunta: ¿También se actualiza la API de búsqueda de registros?
La [API de búsqueda de registros](log-analytics-log-search-api.md) heredada dejará de funcionar cuando se actualice el área de trabajo.  Consulte [API de REST de Azure Log Analytics](https://dev.loganalytics.io/) para más información sobre la nueva API.


## <a name="portals"></a>Portales

### <a name="question-should-i-use-the-new-advanced-analytics-portal-or-keep-using-the-log-search-portal"></a>Pregunta: ¿Debo usar el nuevo portal de análisis avanzado o continuar con el portal de búsqueda de registros?
Puede ver una comparación de los dos portales en [Portales para la creación y edición de consultas de registros en Azure Log Analytics](log-analytics-log-search-portals.md).  Cada uno tiene distintas ventajas, para que pueda elegir el que mejor satisfaga sus necesidades.  Es común escribir consultas en el portal de análisis avanzado y copiarlas en otros lugares, como el Diseñador de vistas.  Debe leer sobre las [cuestiones que debe tener en cuenta](log-analytics-log-search-portals.md#advanced-analytics-portal) para ejecutar lo anterior.


### <a name="question--after-upgrade-i-get-an-error-trying-to-run-queries-and-am-also-seeing-errors-in-my-views"></a>Pregunta: Después de actualizar, recibo un error al tratar de ejecutar consultas, y también veo errores en mis vistas.

El explorador requiere acceso a las direcciones siguientes para ejecutar las consultas de Log Analytics después de la actualización.  Si el explorador tiene acceso a Azure Portal a través de un firewall, debe habilitar el acceso a estas direcciones.

| Identificador URI | IP | Puertos |
|:---|:---|:---|
| portal.loganalytics.io | Dinámica | 80 443 |
| api.loganalytics.io    | Dinámica | 80 443 |
| docs.loganalytics.io   | Dinámica | 80 443 |



## <a name="power-bi"></a>Power BI

### <a name="question-does-anything-change-with-powerbi-integration"></a>Pregunta: ¿Hay algún cambio en la integración con Power BI?
Sí.  Una vez que se haya actualizado el área de trabajo, el proceso de exportación de datos de Log Analytics a Power BI dejará de funcionar.  Se deshabilitarán las programaciones existentes creadas antes de actualizar.  

Después de la actualización, Azure Log Analytics usa la misma plataforma que Application Insights y el usuario utilizará el mismo proceso para exportar las consultas de Log Analytics a Power BI que se describe en [el proceso de exportación de consultas de Application Insights a Power BI](../application-insights/app-insights-export-power-bi.md#export-analytics-queries).  La exportación a Power BI ahora llama directamente al punto de conexión de API. Esto le permite obtener hasta 500.000 filas o 64.000.000 bytes de datos, exportar consultas largas y personalizar el tiempo de espera de la consulta (el tiempo de espera predeterminado es 3 minutos, y el tiempo de espera máximo es de 10 minutos).

## <a name="powershell-cmdlets"></a>Cmdlets de PowerShell

### <a name="question-does-the-log-search-powershell-cmdlet-get-updated-after-i-upgrade"></a>Pregunta: ¿También se actualiza el cmdlet de PowerShell para búsqueda de registros?
El cmdlet [Get-AzureRmOperationalInsightsSearchResults](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/Get-AzureRmOperationalInsightsSearchResults) dejará de utilizarse una vez completada la actualización de todas las áreas de trabajo.  Use el [cmdlet Invoke-LogAnalyticsQuery](https://dev.loganalytics.io/documentation/Tools/PowerShell-Cmdlets) para realizar búsquedas de registros en áreas de trabajo actualizadas.




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

### <a name="known-issue-perspectives-in-application-insights-connector"></a>Problema conocido: perspectivas en Application Insights Connector
Las perspectivas de la [solución Application Insights Connector](log-analytics-app-insights-connector.md) ya no se admiten en la solución de conector de Application Insights.  Puede utilizar el Diseñador de vistas para crear vistas personalizadas con datos de Application Insights.

### <a name="known-issue-backup-solution"></a>Problema conocido: Solución de copia de seguridad
La solución de copia de seguridad no puede recopilar datos si se instaló antes de actualizar un área de trabajo. Desinstale la solución y, a continuación, instale la versión más reciente.  La nueva versión de la solución no es compatible con los almacenes de Backup clásicos, por lo que también debe actualizar a almacenes de Recovery Services para seguir usando la solución.

## <a name="upgrade-process"></a>Proceso de actualización

### <a name="question-i-have-several-workspaces-can-i-upgrade-all-workspaces-at-the-same-time"></a>Pregunta: Tengo varias áreas de trabajo. ¿Puedo actualizar todas las áreas de trabajo al mismo tiempo?  
No.  La actualización se aplica a una sola área de trabajo cada vez. Actualmente no hay ninguna manera de actualizar muchas áreas de trabajo a la vez. Tenga en cuenta que otros usuarios del área de trabajo actualizada también se verán afectados.  

### <a name="question-will-existing-log-data-collected-in-my-workspace-be-modified-if-i-upgrade"></a>Pregunta: ¿Se modificarán los datos de los registros existentes recopilados en mi área de trabajo si la actualizo?  
No. La actualización no afecta a los datos de los registros disponibles para las búsquedas en el área de trabajo. Las búsquedas, alertas y vistas guardadas se convertirán al nuevo lenguaje de búsqueda automáticamente.  

### <a name="question-what-happens-if-i-dont-upgrade-my-workspace"></a>Pregunta: ¿Qué sucede si no actualizo mi área de trabajo?  
La búsqueda de registros heredada dejará de utilizarse en los próximos meses. Las áreas de trabajo que no se hayan actualizado en esa fecha se actualizarán automáticamente.

### <a name="question-can-i-revert-back-after-i-upgrade"></a>Pregunta: ¿Es posible revertir después de actualizar?
Antes de la disponibilidad general, podía revertir el área de trabajo después de actualizar.  Ahora que el nuevo lenguaje alcanzó la disponibilidad general, se ha suprimido esta funcionalidad a medida que empezamos a retirar la plataforma anterior.



## <a name="views"></a>Vistas

### <a name="question-how-do-i-create-a-new-view-with-view-designer"></a>Pregunta: ¿Cómo puedo crear una vista con el Diseñador de vistas?
Antes de la actualización, podría crear una vista con el Diseñador de vistas de algún elemento del panel principal.  Cuando se actualiza el área de trabajo, este elemento se elimina.  Puede crear una vista con el Diseñador de vistas en el portal de OMS; para ello, haga clic en el botón + de color verde en el menú de la izquierda.


## <a name="next-steps"></a>Pasos siguientes

- Obtenga más información sobre la [actualización del área de trabajo al nuevo lenguaje de consulta de Log Analytics](log-analytics-log-search-upgrade.md).
