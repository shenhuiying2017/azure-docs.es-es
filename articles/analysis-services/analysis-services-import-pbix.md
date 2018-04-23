---
title: Importación de un archivo de Power BI Desktop en Azure Analysis Services | Microsoft Docs
description: Describe cómo importar un archivo de Power BI Desktop (pbix) mediante Azure Portal.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 35bf2ba85017de43788f802b6244d61ed2bb62df
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2018
---
# <a name="import-a-power-bi-desktop-file"></a>Importación de un archivo de Power BI Desktop

Puede crear un nuevo modelo en el sistema autónomo (AS)* de Azure importando un archivo de Power BI Desktop (pbix). Se importan metadatos del modelo, datos en caché y conexiones a origen de datos. Los informes y las visualizaciones no se importan.

**Restricciones**   
- El modelo de pbix puede conectarse solo a orígenes de datos de Azure SQL Database y Azure SQL Data Warehouse. 
- El modelo pbix no puede tener conexiones en vivo o de DirectQuery. 
- Se puede producir un error en la importación si el modelo de datos pbix contiene metadatos que no se admiten en Analysis Services.

## <a name="to-import-from-pbix"></a>Para importar desde pbix

1. En la opción **Información general** > **Web designer** (Diseñador web) del servidor, haga clic en **Abrir**.

    ![Creación de un modelo en Azure Portal](./media/analysis-services-create-model-portal/aas-create-portal-overview-wd.png)

2. En **Web designer** > **Modelos**, haga clic en **+ Agregar**.

    ![Creación de un modelo en Azure Portal](./media/analysis-services-create-model-portal/aas-create-portal-models.png)

3. En **Nuevo modelo**, escriba un nombre de modelo y, a continuación, seleccione el archivo de Power BI Desktop.

    ![Cuadro de diálogo Nuevo modelo en Azure Portal](./media/analysis-services-import-pbix/aas-import-pbix-new-model.png)

4. En **Importar**, encuentre y seleccione su archivo.

     ![Cuadro de diálogo Connect (Conexión) en Azure Portal](./media/analysis-services-import-pbix/aas-import-pbix-select-file.png)

## <a name="see-also"></a>Otras referencias

[Creación de un modelo en Azure Portal](analysis-services-create-model-portal.md)   
[Conexión a Azure Analysis Services](analysis-services-connect.md)  
