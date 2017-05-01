---
title: "Conexión a Azure Analysis Services con Excel | Microsoft Docs"
description: Aprenda a conectarse a un servidor de Azure Analysis Services mediante Excel.
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 04/12/2017
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: a83e313413d87179b89e78d2b90a08a9bcc92fa1
ms.lasthandoff: 04/17/2017


---
# <a name="connect-with-excel"></a>Conexión con Excel

Una vez que se ha creado un servidor en Azure y se ha implementado en él un modelo tabular, está preparado para conectarse y comenzar a explorar los datos. 


## <a name="connect-in-excel"></a>Conexión en Excel

La conexión a un servidor en Excel se admite mediante Obtener datos en Excel 2016 o Power Query en versiones anteriores. No se admite la conexión mediante el Asistente para importar tablas en Power Pivot. 

**Conexión en Excel 2016**

1. En Excel 2016, en la cinta de opciones **Datos**, haga clic en **Obtener datos externos** > **De otros orígenes** > **Desde Analysis Services**.

2. En el Asistente para la conexión de datos, en **Nombre del servidor**, pegue el nombre del servidor desde el Portapapeles. Luego, en **Credenciales de inicio de sesión**, seleccione **Utilizar el nombre de usuario y la contraseña siguientes** y escriba el nombre de usuario de la organización (por ejemplo, nancy@adventureworks.com) y la contraseña.

    ![Conexión desde el inicio de sesión de Excel](./media/analysis-services-connect-excel/aas-connect-excel-logon.png)

3. En **Seleccionar base de datos y tabla**, seleccione la base de datos y el modelo o la perspectiva y haga clic en **Finalizar**.
   
    ![Conexión desde el modelo de selección de Excel](./media/analysis-services-connect-excel/aas-connect-excel-select.png)


## <a name="see-also"></a>Consulte también
[Bibliotecas de cliente](analysis-services-data-providers.md)   
[Administración del servidor](analysis-services-manage.md)     



