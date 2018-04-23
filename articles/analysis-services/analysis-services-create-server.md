---
title: Creación de un servidor de Analysis Services en Azure | Microsoft Docs
description: Aprenda a crear una instancia de servidor de Analysis Services en Azure.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: c910416524f149c785aae299d576ca5c521abc6d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2018
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

## <a name="to-create-a-server-in-the-azure-portal"></a>Creación de un servidor en Azure Portal
1. Inicie sesión en el [Azure Portal](https://portal.azure.com).  
2. Haga clic en **+ Nuevo** > **Datos y análisis** > **Analysis Services**.
3. En la hoja de **Analysis Services**, rellene los campos obligatorios y, a continuación, presione **Crear**.
   
    ![Crear un servidor](./media/analysis-services-create-server/aas-create-server-blade.png)
   
   * **Nombre del servidor**: escriba un nombre único que se pueda utilizar para hacer referencia al servidor.
   * **Suscripción**: seleccione la suscripción al que este servidor se puede facturar.
   * **Grupos de recursos**: son contenedores diseñados para ayudarlo a administrar una colección de recursos de Azure. Para más información, consulte [grupos de recursos](../azure-resource-manager/resource-group-overview.md).
   * **Ubicación**: esta ubicación del centro de datos de Azure hospeda el servidor. Elija una ubicación más cercana a su base de usuarios más grande.
   * **Plan de tarifa**: seleccione un plan de tarifa. Se admiten modelos tabulares de hasta 400 GB. Para obtener más información, consulte los [precios de Azure Analysis Services](https://azure.microsoft.com/pricing/details/analysis-services/).
4. Haga clic en **Create**(Crear).

La creación normalmente tarda menos de un minuto, a menudo solo unos pocos segundos. Si seleccionó **Add to Portal** (Agregar al portal), desplácese hasta el portal para ver el nuevo servidor. O bien, vaya a **Todos los servicios** > **Analysis Services** para ver si el servidor está listo.

 ![panel](./media/analysis-services-create-server/aas-create-server-dashboard.png)


## <a name="next-steps"></a>Pasos siguientes
Una vez creado el servidor, puede [implementar un modelo](analysis-services-deploy.md) en él mediante SSDT o con SSMS.

Si un modelo que se implemente en el servidor se conecta a orígenes de datos locales, deberá instalar una [puerta de enlace de datos local](analysis-services-gateway.md) en un equipo de la red.

