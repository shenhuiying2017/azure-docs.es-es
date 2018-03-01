---
title: "Eliminación de un área de trabajo de Azure Log Analytics | Microsoft Docs"
description: "Obtenga información para eliminar el espacio de trabajo de Log Analytics si creó uno en una suscripción personal o reestructure el modelo de espacio de trabajo."
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2017
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 4f455e26f078360f17f8118f8b5b1db5bd75d473
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/21/2018
---
# <a name="delete-an-azure-log-analytics-workspace-with-the-azure-portal"></a>Eliminación de una área de trabajo de Azure Log Analytics con Azure Portal
Este tema muestra cómo usar Azure Portal para eliminar un área de trabajo de Log Analytics que ya no necesite. 

## <a name="to-delete-a-workspace"></a>Para eliminar un área de trabajo 
Cuando elimina un área de trabajo de Log Analytics, todos los datos relacionados con el área de trabajo se eliminan del servicio en un plazo máximo de 30 días.  Debe tener cuidado al eliminar un área de trabajo porque puede haber datos importantes y una configuración que puede afectar negativamente a las operaciones del servicio.  
 
Si es administrador y hay varios usuarios asociados al área de trabajo, se interrumpirá la asociación entre los usuarios y el área de trabajo. Si los usuarios están asociados a otras áreas de trabajo, podrán seguir utilizando Log Analytics con esas otras áreas de trabajo. Sin embargo, si no están asociados a otras áreas de trabajo, deberán crear un área de trabajo para poder usar Log Analytics. 

1. Inicie sesión en el [Portal de Azure](http://portal.azure.com). 
2. En Azure Portal, haga clic en **Todos los servicios**. En la lista de recursos, escriba **Log Analytics**. Cuando comience a escribir, la lista se filtrará en función de la entrada. Seleccione **Log Analytics**.
3. En el panel de suscripciones de Log Analytics, seleccione un área de trabajo y, a continuación, haga clic en **Eliminar** desde la parte superior del panel intermedio.<br><br> ![Opción Eliminar desde el panel de propiedades del área de trabajo](media/log-analytics-manage-del-workspace/log-analytics-delete-workspace.png)<br>  
4. Cuando aparezca de la ventana de mensaje de confirmación que le solicite que confirme la eliminación del área de trabajo, haga clic en **Sí**.<br><br> ![Confirmación de la eliminación del área de trabajo](media/log-analytics-manage-del-workspace/log-analytics-delete-workspace-confirm.png)

