---
title: "Creación de un servidor de Analysis Services en Azure | Microsoft Docs"
description: Aprenda a crear una instancia de servidor de Analysis Services en Azure.
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 7f560216-8a9a-4d06-852e-48cf24deab19
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 08/15/2017
ms.author: owend
ms.openlocfilehash: 95b367e7cd74405088190c1fe19cf92990759d90
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="create-an-azure-analysis-services-server-in-azure-portal"></a>Creación de un servidor de Azure Analysis Services en Azure Portal
Este artículo lo guiará por la creación de un recurso de servidor de Analysis Services en la suscripción de Azure.

## <a name="before-you-begin"></a>Antes de empezar
Para completar este inicio rápido necesita instalar:

* **Suscripción de Azure**: visite [Evaluación gratuita de Azure](https://azure.microsoft.com/offers/ms-azr-0044p/) para crear una cuenta.
* **Azure Active Directory**: la suscripción debe estar asociada a un inquilino de Azure Active Directory. Además, debe estar conectado en Azure con una cuenta en ese Azure Active Directory. No se admiten cuentas de Microsoft. Para más información, consulte [Permisos de usuario y autenticación](analysis-services-manage-users.md).
* **Grupo de recursos**: use un grupo de recursos que ya tenga o [cree uno nuevo](../azure-resource-manager/resource-group-overview.md).

> [!NOTE]
> La creación de un servidor puede dar lugar a un nuevo servicio facturable. Para obtener más información, consulte los [precios de Analysis Services](https://azure.microsoft.com/pricing/details/analysis-services/).
> 
> 

## <a name="to-create-a-server-in-azure-portal"></a>Creación de un servidor de Azure Portal
1. Inicie sesión en [Azure Portal](https://portal.azure.com).  
2. Haga clic en **+ Nuevo** > **Datos y análisis** > **Analysis Services**.
3. En la hoja de **Analysis Services**, rellene los campos obligatorios y, a continuación, presione **Crear**.
   
    ![Crear un servidor](./media/analysis-services-create-server/aas-create-server-blade.png)
   
   * **Nombre del servidor**: escriba un nombre único que se pueda utilizar para hacer referencia al servidor.
   * **Suscripción**: seleccione la suscripción al que este servidor se puede facturar.
   * **Grupos de recursos**: son contenedores diseñados para ayudarlo a administrar una colección de recursos de Azure. Para más información, consulte [grupos de recursos](../azure-resource-manager/resource-group-overview.md).
   * **Ubicación**: esta ubicación del centro de datos de Azure hospeda el servidor. Elija una ubicación más cercana a su base de usuarios más grande.
   * **Plan de tarifa**: seleccione un plan de tarifa. Se admiten modelos tabulares de hasta 400 GB. Para obtener más información, consulte los [precios de Azure Analysis Services](https://azure.microsoft.com/pricing/details/analysis-services/).
4. Haga clic en **Crear**.

La creación normalmente tarda menos de un minuto, a menudo solo unos pocos segundos. Si seleccionó **Add to Portal** (Agregar al portal), desplácese hasta el portal para ver el nuevo servidor. O bien, vaya a **Más servicios** > **Analysis Services** para ver si el servidor está listo.

 ![Panel](./media/analysis-services-create-server/aas-create-server-dashboard.png)


## <a name="next-steps"></a>Pasos siguientes
Una vez creado el servidor, puede [implementar un modelo](analysis-services-deploy.md) en él mediante SSDT o con SSMS.

Si un modelo que se implemente en el servidor se conecta a orígenes de datos locales, deberá instalar una [puerta de enlace de datos local](analysis-services-gateway.md) en un equipo de la red.

