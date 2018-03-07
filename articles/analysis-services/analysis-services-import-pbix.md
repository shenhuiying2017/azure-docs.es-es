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
ms.openlocfilehash: 43eab587a1e5209069a248f1e2e1f57af158a2b8
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="import-a-power-bi-desktop-file"></a>Importación de un archivo de Power BI Desktop

Puede crear un nuevo modelo en el sistema autónomo (AS)* de Azure importando un archivo de Power BI Desktop (pbix). Se importan metadatos del modelo, datos en caché y conexiones a origen de datos. Los informes y las visualizaciones no se importan. Una vez en su servidor, los cambios en el modelo pueden realizarse actualizando y volviendo a importar el pbix, usando la característica del diseñador web (versión preliminar) o utilizando SQL Server Management Studio (SSMS). Los modelos importados no se pueden abrir ni exportar a Visual Studio.

> [!NOTE]
> Si su modelo pbix se conecta a orígenes de datos locales, una [puerta de enlace local](analysis-services-gateway.md) se debe configurar para su servidor.

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
