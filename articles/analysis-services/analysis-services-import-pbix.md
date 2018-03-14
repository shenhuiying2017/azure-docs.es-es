---
title: "Importación de un archivo de Power BI Desktop en Azure Analysis Services | Microsoft Docs"
description: "Describe cómo importar un archivo de Power BI Desktop (pbix) mediante Azure Portal."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 02/26/2018
ms.author: owend
ms.openlocfilehash: e0be0c69b501d7e93c65bcf23d4dd1b6bfa89caf
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/02/2018
---
# <a name="import-a-power-bi-desktop-file"></a>Importación de un archivo de Power BI Desktop

Puede crear un nuevo modelo en el sistema autónomo (AS)* de Azure importando un archivo de Power BI Desktop (pbix). Se importan metadatos del modelo, datos en caché y conexiones a origen de datos. Los informes y las visualizaciones no se importan.

**Restricciones**   
- El modelo pbix debe conectar únicamente con los [orígenes de datos admitidos de Analysis Services](analysis-services-datasource.md). 
- El modelo pbix no puede tener conexiones en vivo o de DirectQuery. 
- Si el modelo pbix se conecta a orígenes de datos locales, se debe configurar una [puerta de enlace local](analysis-services-gateway.md) para el servidor de Analysis Services.
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
