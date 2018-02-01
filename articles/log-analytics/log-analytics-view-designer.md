---
title: "Creación de vistas para analizar datos en Azure Log Analytics | Microsoft Docs"
description: "El Diseñador de vistas de Log Analytics permite crear vistas personalizadas que se muestran en Azure Portal y que contienen diferentes visualizaciones de datos del área de trabajo de Log Analytics. En este artículo encontrará información general del Diseñador de vistas, y procedimientos para crear y editar vistas personalizadas."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: 
ms.assetid: ce41dc30-e568-43c1-97fa-81e5997c946a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/18/2018
ms.author: bwren
ms.openlocfilehash: a84f40503c1b9778c496461ebbf6864f99bd1c4b
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/19/2018
---
# <a name="use-view-designer-to-create-custom-views-in-log-analytics"></a>Uso del Diseñador de vistas para crear vistas personalizadas en Log Analytics
El Diseñador de vistas de [Log Analytics](log-analytics-overview.md) permite crear vistas personalizadas en Azure Portal que contienen diferentes visualizaciones de datos en el espacio de trabajo de Log Analytics. En este artículo encontrará información general del Diseñador de vistas, y procedimientos para crear y editar vistas personalizadas.

Estos son otros de los artículos disponibles sobre el Diseñador de vistas:

* [Referencia de los iconos](log-analytics-view-designer-tiles.md): referencia de la configuración de cada uno de los iconos que hay disponibles para usarse en las vistas personalizadas.
* [Referencia de los elementos de visualización](log-analytics-view-designer-parts.md): referencia de la configuración de cada uno de los iconos que hay disponibles para usarse en las vistas personalizadas.

>[!NOTE]
> Si el área de trabajo se ha actualizado al [nuevo lenguaje de consulta de Log Analytics](log-analytics-log-search-upgrade.md), las consultas de todas las vistas se deben escribir en el [nuevo lenguaje de consulta](https://go.microsoft.com/fwlink/?linkid=856078).  Todas las vistas que se crearon antes de actualizar el área de trabajo se convertirán automáticamente.

## <a name="concepts"></a>Conceptos
Las vistas aparecen en la página de **información general** del área de trabajo de Log Analytics en Azure Portal.  El icono de cada vista personalizada se mostrará en orden alfabético con los iconos de las soluciones instaladas en la misma área de trabajo.

![Página de información general](media/log-analytics-view-designer/overview-page.png)

Las vistas creadas con el Diseñador de vistas contienen los elementos de la siguiente tabla.

| Elemento | DESCRIPCIÓN |
|:--- |:--- |
| Icono |Aparece en la página de información general del área de trabajo de Log Analytics.  Incluye un resumen visual de los datos que contiene la vista personalizada.  Los distintos tipos de iconos proporcionan diferentes visualizaciones de los registros.  Haga clic en el icono para abrir la vista personalizada. |
| Vista personalizada |Se muestra cuando el usuario hace clic en el icono.  Contiene uno o varios elementos de visualización. |
| Elementos de visualización |La visualización de los datos del área de trabajo de Log Analytics se basa en una o varias [búsquedas de registros](log-analytics-log-searches.md).  Muchos de los elementos incluirán un encabezado que proporciona una visualización general y una lista de los principales resultados.  Los distintos tipos de elementos proporcionan diferentes visualizaciones de los registros del área de trabajo de Log Analytics.  Haga clic en los elementos para realizar una búsqueda de registro que proporcione registros detallados. |


## <a name="work-with-an-existing-view"></a>Uso de una vista existente
Cuando abre una vista creada con el Diseñador de vistas, tendrá un menú con las opciones que aparecen en la tabla siguiente.

![Menú de información general](media/log-analytics-view-designer/overview-menu.png)


| Opción | DESCRIPCIÓN |
|:--|:--|
| Actualizar   | Actualice la vista con los datos más recientes. | 
| Análisis | Abra el [portal de análisis avanzado](log-analytics-log-search-portals.md#advanced-analytics-portal) para analizar los datos con las búsquedas de registros.(log-analytics-log-search-portals.md#advanced-analytics-portal). |
| Filtrar    | Establezca un filtro de tiempo para los datos incluidos en la vista. |
| Edit      | Abra la vista en el Diseñador de vistas para editar el contenido y la configuración.   |
| Clon     | Cree una vista nueva y ábrala en el Diseñador de vistas.  La vista nueva tiene el mismo nombre que la original con la palabra "Copia" agregada al final. |


## <a name="create-a-new-view"></a>Creación de vistas
Cree una vista en el **Diseñador de vistas** con un clic en el icono del Diseñador de vistas en la página de información general del área de trabajo de Log Analytics en Azure Portal.

![Icono del Diseñador de vistas](media/log-analytics-view-designer/view-designer-tile.png)


## <a name="working-with-view-designer"></a>Uso del Diseñador de vistas
Trabajará con el Diseñador de vistas ya sea que esté creando una vista nueva o editando una existente.  

El Diseñador de vistas tiene tres paneles.  El panel **Diseño** contiene la vista personalizada que crea o edita.  Puede agregar iconos y elementos desde el panel **Control** al panel **Diseño**.  El panel **Propiedades** muestra las propiedades del icono o el elemento seleccionado.

![Ver diseñador](media/log-analytics-view-designer/view-designer-screenshot.png)

### <a name="configure-view-tile"></a>Configuración de iconos de vistas
Una vista personalizada solo puede tener un único icono.  Haga clic en la pestaña **Icono** del panel **Control** para ver el icono actual o seleccionar otra.  El panel **Propiedades** muestra las propiedades del icono actual.  Configure las propiedades de los iconos según la información detallada del artículo [Referencia de los iconos](log-analytics-view-designer-tiles.md) y haga clic en **Aplicar** para guardar los cambios.

### <a name="configure-visualization-parts"></a>Configuración de elementos de visualización
Una vista puede incluir cualquier número de elementos de visualización.  Seleccione la pestaña **Vista** y, luego, un elemento de visualización para agregarlo a la vista.  El panel **Propiedades** muestra las propiedades del elemento seleccionado.  Configure las propiedades de las vistas según la información detallada del artículo [Referencia de los elementos de visualización](log-analytics-view-designer-parts.md) y haga clic en **Aplicar** para guardar los cambios.

Las vistas solo tienen una fila de elementos de visualización.  Los elementos de una vista pueden reorganizarse haciendo clic en ellos y arrastrándolos a una nueva ubicación.

Puede quitar un elemento de visualización de la vista haciendo clic en el botón **X** de la esquina superior derecha del elemento.


### <a name="menu-options"></a>Opciones del menú
Cuando trabaja con una vista en el modo de edición, tiene las opciones de menú que aparecen en la tabla siguiente.

![Menú Edición](media/log-analytics-view-designer/edit-menu.png)

| Opción | DESCRIPCIÓN |
|:--|:--|
| Save        | Guarde los cambios y cierre la vista. |
| Cancelar      | Descarte los cambios y cierre la vista. |
| Eliminar vista | Elimine la vista. |
| Exportación      | Exporte la vista a una [plantilla de Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) que pueda importar en otra área de trabajo.  El nombre del archivo será el de la vista con la extensión *omsview*. |
| Importar      | Importe un archivo *omsview* que haya exportado de otra área de trabajo.  Con esto se sobrescribirá la configuración de la vista existente. |
| Clon       | Cree una vista nueva y ábrala en el Diseñador de vistas.  La vista nueva tiene el mismo nombre que la original con la palabra "Copia" agregada al final. |
| Publicar     | Exporte la vista a un archivo JSON que se pueda insertar en una [solución de administración](../operations-management-suite/operations-management-suite-solutions-resources-views.md).  El nombre del archivo será el nombre de la vista con la extensión *json*. Se crea un segundo archivo con la extensión *resjson* que incluye los valores de los recursos definidos en el archivo JSON.

## <a name="next-steps"></a>pasos siguientes
* Agregue [iconos](log-analytics-view-designer-tiles.md) a la vista personalizada.
* Agregue [elementos de visualización](log-analytics-view-designer-parts.md) a la vista personalizada.
