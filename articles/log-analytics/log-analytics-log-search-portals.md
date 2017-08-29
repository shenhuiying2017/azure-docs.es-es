---
title: "Portales para la creación y edición de consultas de registros en Azure Log Analytics | Microsoft Docs"
description: "En este artículo se describen los portales que puede utilizar en Azure Log Analytics para crear y editar búsquedas de registros."
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
ms.date: 07/25/2017
ms.author: bwren
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: d878dae95d449bd4da58e8dad4daccaf07f98f66
ms.contentlocale: es-es
ms.lasthandoff: 08/21/2017

---
# <a name="portals-for-creating-and-editing-log-queries-in-azure-log-analytics"></a>Portales para la creación y edición de consultas de registros en Azure Log Analytics

> [!NOTE]
> En este artículo se describen los portales de Azure Log Analytics que usan el nuevo lenguaje de consulta.  Puede obtener más información sobre el nuevo lenguaje y conocer el procedimiento para actualizar el área de trabajo en [Upgrade your Azure Log Analytics workspace to new log search](log-analytics-log-search-upgrade.md) (Actualización del área de trabajo de Azure Log Analytics al nuevo registro de búsquedas)///.  
>
> Si todavía no se ha actualizado el área de trabajo al nuevo lenguaje de consulta, vaya a [Búsqueda de datos mediante búsquedas de registros en Log Analytics](log-analytics-log-searches.md) para obtener información sobre la versión actual del portal de búsqueda de registros.

Puede usar búsquedas de registros en diversos lugares de Azure Log Analytics para recuperar datos del área de trabajo.  Para crear y editar realmente consultas además de trabajar interactivamente con los datos devueltos, tiene dos opciones que se describen a continuación.  

## <a name="log-search-portal"></a>Portal de búsqueda de registros
El portal de búsqueda de registros es accesible desde Azure Portal o desde el portal de OMS.  Es adecuado para la creación de consultas básicas que se pueden crear en una sola línea.  El portal de búsqueda de registros se puede utilizar sin necesidad de iniciar un portal externo y se puede usar para realizar una serie de funciones con búsquedas de registros, incluida la creación de reglas de alertas, la creación de grupos de equipos y la exportación de los resultados de la consulta.  

El portal de búsqueda de registros proporciona varias características para poder editar la consulta sin tener un conocimiento completo del lenguaje de consulta.  Puede ver un resumen de estas características en [Creación de búsquedas de registros en Azure Log Analytics mediante el portal correspondiente](log-analytics-log-search-log-search-portal.md).


![Portal de búsqueda de registros](media/log-analytics-log-search-portals/log-search-portal.png)

## <a name="advanced-analytics-portal"></a>Portal de análisis avanzado
El portal de análisis avanzado es un portal dedicado que proporciona funcionalidades avanzadas que no están disponibles en el portal de búsqueda de registros.  Las características incluyen la capacidad para editar una consulta en varias líneas, ejecutar código de forma selectiva, Intellisense sensible al contexto y análisis inteligente.  El portal de análisis avanzado es más adecuado para diseñar consultas más complejas que se guardan como una búsqueda de registros o que se copian y pegan en otros elementos de Log Analytics.  Inicie el portal de análisis avanzado desde un vínculo del portal de búsqueda de registros.

![Portal de análisis avanzado](media/log-analytics-log-search-portals/advanced-analytics-portal.png)


Debido a sus características avanzadas, normalmente usará el portal de análisis avanzado como la herramienta principal para crear y editar consultas.  Una vez que haya determinado que la consulta funciona según lo previsto, cópiela y péguela en otra parte como, por ejemplo, en el portal de búsqueda de registros o el Diseñador de vistas.  Dado que el portal de análisis avanzado admite consultas de varias líneas, debe tener en cuenta lo siguiente cuando se copia una consulta de este portal.

- Los comentarios deben quitarse de la consulta antes de copiarla y pegarla en otra ubicación.  Puede hacer un comentario a una línea mediante la inclusión de dos barras diagonales (//) delante.  Si pega una consulta de varias líneas en una sola línea, se quitan los saltos de línea.  Si se incluyen los comentarios, todos los caracteres después del primer comentario se consideran parte del comentario.


## <a name="next-steps"></a>Pasos siguientes

- Vea un tutorial sobre el uso del [portal de búsqueda de registros](log-analytics-log-search-log-search-portal.md) o el [portal de análisis avanzado](https://docs.loganalytics.io/docs/Learn/Tutorials/Getting-started-with-the-Analytics-portal) para crear consultas.
- Consulte un [tutorial sobre cómo escribir consultas](https://docs.loganalytics.io/docs/Learn/Tutorials/Getting-started-with-queries) mediante el nuevo lenguaje de consulta.

