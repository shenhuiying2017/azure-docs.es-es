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
ms.date: 08/08/2017
ms.author: bwren
ms.translationtype: HT
ms.sourcegitcommit: f9003c65d1818952c6a019f81080d595791f63bf
ms.openlocfilehash: 2de9a87390da11a034c6cebaa37d4cc89edf3cad
ms.contentlocale: es-es
ms.lasthandoff: 08/09/2017

---

# <a name="log-analytics-new-log-search-frequently-asked-questions"></a>Preguntas frecuentes sobre la nueva búsqueda de registros de Log Analytics

Este artículo ofrece respuesta a las preguntas más frecuentes sobre la actualización de [Log Analytics al nuevo lenguaje de consulta](log-analytics-log-search-upgrade.md).



## <a name="upgrade-process"></a>Proceso de actualización

### <a name="i-have-several-workspaces-can-i-upgrade-all-workspaces-at-the-same-time"></a>Tengo varias áreas de trabajo. ¿Puedo actualizar todas las áreas de trabajo al mismo tiempo?  
R: No.  La actualización se aplica a una sola área de trabajo cada vez. Actualmente no hay ninguna manera de actualizar muchas áreas de trabajo a la vez. Tenga en cuenta que otros usuarios del área de trabajo actualizada también se verán afectados.  

### <a name="will-existing-log-data-collected-in-my-workspace-be-modified-if-i-upgrade"></a>¿Se modificarán los datos de los registros existentes recopilados en mi área de trabajo si la actualizo?  
No. La actualización no afecta a los datos de los registros disponibles para las búsquedas en el área de trabajo. Las búsquedas, alertas y vistas guardadas se convertirán al nuevo lenguaje de búsqueda automáticamente.  

### <a name="what-happens-if-i-dont-upgrade-my-workspace"></a>¿Qué sucede si no actualizo mi área de trabajo?  
La búsqueda de registros heredada dejará de utilizarse en los próximos meses. Las áreas de trabajo que no se hayan actualizado en esa fecha se actualizarán automáticamente.

### <a name="i-didnt-choose-to-upgrade-but-my-workspace-has-been-upgraded-anyway-what-happened"></a>Yo no elegí actualizar y, sin embargo, el área de trabajo se ha actualizado igualmente. ¿Qué ha ocurrido?  
Otro administrador de esta área de trabajo la ha actualizado. Tenga en cuenta que todas las áreas de trabajo se actualizarán automáticamente cuando el nuevo lenguaje tenga disponibilidad general.  

### <a name="i-have-upgraded-by-mistake-and-now-need-to-cancel-it-and-restore-everything-back-what-should-i-do"></a>He actualizado por error y ahora necesito cancelar la actualización y restaurarlo todo de nuevo. ¿Qué debo hacer?  
No se preocupe.  Se crea una instantánea del área de trabajo antes de la actualización, con lo cual puede restaurarla si es necesario. Aunque debe tener en cuenta que las búsquedas, alertas o vistas que guardó después de la actualización se perderán.  Para restaurar el entorno del área de trabajo, siga el procedimiento descrito en [Can I go back after I upgrade?](log-analytics-log-search-upgrade.md#can-i-go-back-after-i-upgrade) (¿Puedo restaurar después de una actualización?).



## <a name="log-searches"></a>Búsqueda de registros

### <a name="i-have-saved-searches-outside-of-my-upgraded-workspace-can-i-convert-them-to-the-new-search-language-automatically"></a>He guardado búsquedas fuera de mi área de trabajo actualizada. ¿Puedo convertirlas automáticamente al nuevo lenguaje de consulta?
Puede usar la herramienta del convertidor de lenguaje en la página de búsqueda de registros para convertir cada una de ellas.  No hay ningún método para convertir automáticamente varias búsquedas sin actualizar el área de trabajo.


## <a name="alerts"></a>Alertas

### <a name="i-have-a-lot-of-alert-rules-do-i-need-to-create-them-again-in-the-new-language-after-i-upgrade"></a>Tengo una gran cantidad de reglas de alertas. ¿Es necesario crearlas de nuevo en el nuevo lenguaje después de actualizar?  
No, las reglas de alertas se convierten automáticamente al nuevo lenguaje de búsqueda durante la actualización.  

## <a name="power-bi"></a>Power BI

### <a name="does-anything-change-with-powerbi-integration"></a>¿Hay algún cambio en la integración con Power BI?
Sí.  Una vez que se haya actualizado el área de trabajo, el proceso de exportación de datos de Log Analytics a Power BI dejará de funcionar.  Se deshabilitarán las programaciones existentes creadas antes de actualizar.  Después de la actualización, Azure Log Analytics usa la misma plataforma que Application Insights y el usuario utilizará el mismo proceso para exportar las consultas de Log Analytics a Power BI que se describe en [el proceso de exportación de consultas de Application Insights a Power BI](../application-insights/app-insights-export-power-bi.md#export-analytics-queries).

## <a name="dashboards"></a>Paneles

### <a name="can-i-still-use-dashboards"></a>¿Puedo continuar usando paneles?
Puede continuar usando los paneles que creó antes de que se actualizara el área de trabajo, pero podrá editarlos ni crear otros nuevos.  Puede continuar creando y editando vistas con el [Diseñador de vistas](log-analytics-view-designer.md). 

## <a name="next-steps"></a>Pasos siguientes

- Obtenga más información sobre la [actualización del área de trabajo al nuevo lenguaje de consulta de Log Analytics](log-analytics-log-search-upgrade.md).

