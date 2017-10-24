---
title: "Realización de consultas en áreas de trabajo Azure Log Analytics | Microsoft Docs"
description: "En este artículo se describe cómo se puede consultar en varias áreas de trabajo de su suscripción con ejemplos que puede seguir."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2017
ms.author: magoedte
ms.openlocfilehash: 6b6dc6f472a87178c006301f4f692aeff15e257e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="perform-queries-across-multiple-log-analytics-workspaces"></a>Realización de consultas en varias áreas de trabajo de Log Analytics

Anteriormente, con Azure Log Analytics, solo podía analizar datos desde dentro del área de trabajo actual, lo que limitaba su capacidad de consulta a través de varias áreas de trabajo definidas en la suscripción.  

Ahora puede consultar en varias áreas de trabajo, lo que proporciona una vista de todo el sistema de los datos.  Solo puede realizar este tipo de consulta en el [Advanced Portal](log-analytics-log-search-portals.md#advanced-analytics-portal), no en Azure Portal.  

## <a name="querying-across-log-analytics-workspaces"></a>Consultas en áreas de trabajo de Log Analytics
Para hacer referencia a otra área de trabajo en la consulta, use el identificador *área de trabajo*.  Por ejemplo, la siguiente consulta devuelve recuentos resumidos de actualizaciones requeridas por su clasificación de la tabla Update desde el área de trabajo actual y otra área de trabajo denominada *contosoretail-it*.  


## <a name="identifying-resources"></a>Identificación de recursos
La identificación de un área de trabajo se puede realizar de varias maneras:

* Nombre del recurso:- es un nombre legible del área de trabajo; a veces se denomina *nombre del componente*. 

    `workspace("contosoretail").Update | count`
 
    >[!NOTE]
    >En la identificación de un área de trabajo por su nombre, se da por supuesto que es único en todas las suscripciones accesibles. Si tiene varias aplicaciones con el nombre especificado, la consulta produce un error debido a la ambigüedad. En este caso debe usar uno de los otros identificadores.

* Nombre completo: es el “nombre íntegro” del área de trabajo, compuesto por el nombre de la suscripción, el grupo de recursos y el nombre del componente con este formato: *nombreSuscripción/grupoDeRecursos/nombreDeComponente*. 

    `workspace('contoso/contosoretail/development').requests | count `

    >[!NOTE]
    >Dado que los nombres de suscripción de Azure no son únicos, este identificador podría ser ambiguo. 
    >

* Identificador del área de trabajo: un identificador de área de trabajo es el identificador único e inmutable asignado a cada área de trabajo que se representa como un identificador único global (GUID).

    `workspace("b438b4f6-912a-46d5-9cb1-b44069212ab4").Update | count`

* Identificador de recurso de Azure: identidad única definida por Azure del área de trabajo. Se usa cuando el nombre del recurso es ambiguo.  Para las áreas de trabajo, el formato es: */subscriptions/subscriptionId/resourcegroups/resourceGroup/providers/microsoft.OperationalInsights/workspaces/componentName*.  

    Por ejemplo:
    ``` 
    `workspace("/subscriptions/e427267-5645-4c4e-9c67-3b84b59a6982/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail").Event | count
    ```

## <a name="next-steps"></a>Pasos siguientes

Revise la [referencia sobre búsqueda de registros de Log Analytics](https://docs.loganalytics.io/docs/Language-Reference) para ver todas las opciones de sintaxis de consulta en Log Analytics.    