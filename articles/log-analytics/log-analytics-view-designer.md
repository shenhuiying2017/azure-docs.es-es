---
title: "Creación de vistas para analizar datos en Log Analytics (OMS) | Microsoft Docs"
description: "El Diseñador de vistas de Log Analytics permite crear vistas personalizadas que se muestran en OMS y Azure Portal y que contienen diferentes visualizaciones de datos del repositorio OMS. En este artículo encontrará información general del Diseñador de vistas, y procedimientos para crear y editar vistas personalizadas."
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
ms.date: 07/17/2017
ms.author: bwren
ms.openlocfilehash: e3c463d749dc4179df58286b9bb75584880a6bc6
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="use-view-designer-to-create-custom-views-in-log-analytics"></a>Uso del Diseñador de vistas para crear vistas personalizadas en Log Analytics
El Diseñador de vistas de [Log Analytics](log-analytics-overview.md) permite crear vistas personalizadas en la consola de OMS que contienen diferentes visualizaciones de datos del repositorio de OMS. En este artículo encontrará información general del Diseñador de vistas, y procedimientos para crear y editar vistas personalizadas.

Estos son otros de los artículos disponibles sobre el Diseñador de vistas:

* [Referencia de los iconos](log-analytics-view-designer-tiles.md): referencia de la configuración de cada uno de los iconos que hay disponibles para usarse en las vistas personalizadas.
* [Referencia de los elementos de visualización](log-analytics-view-designer-parts.md): referencia de la configuración de cada uno de los iconos que hay disponibles para usarse en las vistas personalizadas.

>[!NOTE]
> Si el área de trabajo se ha actualizado al [nuevo lenguaje de consulta de Log Analytics](log-analytics-log-search-upgrade.md), las consultas de todas las vistas se deben escribir en el [nuevo lenguaje de consulta](https://go.microsoft.com/fwlink/?linkid=856078).  Todas las vistas que se crearon antes de actualizar el área de trabajo se convertirán automáticamente.

## <a name="concepts"></a>Conceptos
Las vistas creadas con el Diseñador de vistas contienen los elementos de la siguiente tabla.

| Elemento | Descripción |
|:--- |:--- |
| Icono |Se muestra en el panel principal de información general de Log Analytics.  Incluye un resumen visual de los datos que contiene la vista personalizada.  Los distintos tipos de iconos proporcionan diferentes visualizaciones de los registros del repositorio de OMS.  Haga clic en el icono para abrir la vista personalizada. |
| Vista personalizada |Se muestra cuando el usuario hace clic en el icono.  Contiene uno o varios elementos de visualización. |
| Elementos de visualización |La visualización de los datos del repositorio de OMS se basa en una o varias [búsquedas de registros](log-analytics-log-searches.md).  Muchos de los elementos incluirán un encabezado que proporciona una visualización general y una lista de los principales resultados.  Los distintos tipos de elementos proporcionan diferentes visualizaciones de los registros del repositorio de OMS.  Haga clic en los elementos para realizar una búsqueda de registro que proporcione registros detallados. |

![Información general del Diseñador de vistas](media/log-analytics-view-designer/overview.png)

## <a name="add-view-designer-to-your-workspace"></a>Incorporación del Diseñador de vistas al área de trabajo
Mientras la versión del Diseñador de vistas siga siendo la preliminar, debe agregarlo al área de trabajo seleccionando la opción **Características de vista previa** de la sección **Configuración** del portal OMS.

![Habilitación de la versión preliminar](media/log-analytics-view-designer/preview.png)

## <a name="creating-and-editing-views"></a>Creación y edición de vistas
### <a name="create-a-new-view"></a>Creación de vistas
Abra una nueva vista en el **Diseñador de vistas** haciendo clic en el icono del Diseñador de vistas del panel principal de OMS.

![Icono del Diseñador de vistas](media/log-analytics-view-designer/view-designer-tile.png)

### <a name="edit-an-existing-view"></a>Edición vistas existentes
Para editar una vista existente en el Diseñador de vistas, ábrala haciendo clic en su icono del panel principal de OMS.  Después, haga clic en el botón **Editar** para abrir la vista en el Diseñador de vistas.

![Edición de un icono](media/log-analytics-view-designer/menu-edit.png)

### <a name="clone-an-existing-view"></a>Clonación de vistas existentes
Cuando se clona una vista, se crea una nueva y se abre en el Diseñador de vistas.  La nueva vista tendrá el mismo nombre que la original con la palabra "Copia" agregada al final.  Para clonar una vista existente, ábrala haciendo clic en su icono del panel principal de OMS.  Después, haga clic en el botón **Clonar** para abrir la vista en el Diseñador de vistas.

![Clonación de una vista](media/log-analytics-view-designer/edit-menu-clone.png)

### <a name="delete-an-existing-view"></a>Eliminación de vistas existentes
Para eliminar una vista existente, ábrala haciendo clic en su icono del panel principal de OMS.  Después, haga clic en el botón **Editar** para abrir la vista en el Diseñador de vistas y en **Eliminar vista**.

![Eliminación de una vista](media/log-analytics-view-designer/edit-menu-delete.png)

### <a name="export-an-existing-view"></a>Exportación de vistas existentes
Puede exportar una vista a un archivo JSON, que puede importar a otra área de trabajo o usarse en una [plantilla de Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).  Para exportar una vista existente, ábrala haciendo clic en su icono del panel principal de OMS.  Después, haga clic en el botón **Exportar** para crear un archivo en la carpeta de descargas del explorador.  El nombre del archivo será el de la vista con la extensión *omsview*.

![Exportación de una vista](media/log-analytics-view-designer/edit-menu-export.png)

### <a name="import-an-existing-view"></a>Importación de vistas existentes
Puede importar un archivo *omsview* que haya exportado desde otro grupo de administración.  Para importar una vista existente, cree primero una nueva.  Después, haga clic en el botón **Importar** y seleccione el archivo *omsview*.  La configuración en el archivo se copiará en la vista existente.

![Exportación de una vista](media/log-analytics-view-designer/edit-menu-import.png)

## <a name="working-with-view-designer"></a>Uso del Diseñador de vistas
El Diseñador de vistas tiene tres paneles.  El panel **Diseño** representa la vista personalizada.  Cuando agregue iconos y elementos desde el panel **Control** a **Diseño**, se reflejarán en la vista.  El panel **Propiedades** muestra las propiedades del icono o el elemento seleccionado.

![Ver diseñador](media/log-analytics-view-designer/view-designer-screenshot.png)

### <a name="configure-view-tile"></a>Configuración de iconos de vistas
Una vista personalizada solo puede tener un único icono.  Haga clic en la pestaña **Icono** del panel **Control** para ver el icono actual o seleccionar otra.  El panel **Propiedades** muestra las propiedades del icono actual.  Configure las propiedades de los iconos según la información detallada del artículo [Referencia de los iconos](log-analytics-view-designer-tiles.md) y haga clic en **Aplicar** para guardar los cambios.

### <a name="configure-visualization-parts"></a>Configuración de elementos de visualización
Una vista puede incluir cualquier número de elementos de visualización.  Seleccione la pestaña **Vista** y, luego, un elemento de visualización para agregarlo a la vista.  El panel **Propiedades** muestra las propiedades del elemento seleccionado.  Configure las propiedades de las vistas según la información detallada del artículo [Referencia de los elementos de visualización](log-analytics-view-designer-parts.md) y haga clic en **Aplicar** para guardar los cambios.

### <a name="delete-a-visualization-part"></a>Eliminación de elementos de visualización
Puede quitar un elemento de visualización de la vista haciendo clic en el botón **X** de la esquina superior derecha del elemento.

### <a name="rearrange-visualization-parts"></a>Reorganización de elementos de visualización
Las vistas solo tienen una fila de elementos de visualización.  Los elementos de una vista pueden reorganizarse haciendo clic en ellos y arrastrándolos a una nueva ubicación.

## <a name="next-steps"></a>Pasos siguientes
* Agregue [iconos](log-analytics-view-designer-tiles.md) a la vista personalizada.
* Agregue [elementos de visualización](log-analytics-view-designer-parts.md) a la vista personalizada.
