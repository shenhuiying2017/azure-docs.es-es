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
ms.date: 11/02/2016
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 147c27d12b87379ff02b871ceabd8154e53b6c1a
ms.openlocfilehash: f189e19b5b843d6a7d056c8230bc06d85e568202


---
# <a name="create-an-analysis-services-server"></a>Creación de un servidor de Analysis Services
Este artículo le guiará a través de la creación de un nuevo recurso de servidor de Analysis Services en la suscripción de Azure.

## <a name="before-you-begin"></a>Antes de empezar
Para empezar, necesitará lo siguiente:

* **Suscripción de Azure**: visite [Evaluación gratuita de Azure](https://azure.microsoft.com/offers/ms-azr-0044p/) para crear una cuenta.
* **Azure Active Directory**: la suscripción debe estar asociada a un inquilino de Azure Active Directory. Además, debe estar conectado en Azure con una cuenta en ese Azure Active Directory. No se admiten cuentas de Microsoft. Para obtener más información, consulte [autenticación de usuario](analysis-services-overview.md#secure).
* **Grupo de recursos**: use un grupo de recursos que ya tenga o [cree uno nuevo](../azure-resource-manager/resource-group-overview.md).

> [!NOTE]
> La creación de un servidor de Analysis Services podría dar lugar a un nuevo servicio facturable. Para obtener más información, consulte los [precios de Analysis Services](https://azure.microsoft.com/pricing/details/analysis-services/).
> 
> 

## <a name="create-an-analysis-services-server"></a>Creación de un servidor de Analysis Services
1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. Haga clic en **+ Nuevo** > **Inteligencia y análisis** > **Analysis Services**.
3. En la hoja de **Analysis Services**, rellene los campos obligatorios y, a continuación, presione **Crear**.
   
    ![Crear un servidor](./media/analysis-services-create-server/aas-create-server-blade.png)
   
   * **Nombre del servidor**: escriba un nombre único que se pueda utilizar para hacer referencia al servidor.
   * **Suscripción**: seleccione la suscripción al que este servidor se puede facturar.
   * **Grupos de recursos**: son contenedores diseñados para ayudarle a administrar una colección de recursos de Azure. Para más información, consulte [grupos de recursos](../azure-resource-manager/resource-group-overview.md).
   * **Ubicación**: esta ubicación del centro de datos de Azure hospeda el servidor. Elija una ubicación más cercana a su base de usuarios más grande.
   * **Plan de tarifa**: seleccione un plan de tarifa. Se admiten modelos tabulares de hasta 100 GB. Siempre puede cambiar el plan de tarifa más adelante.
4. Haga clic en **Crear**.

La creación normalmente tarda menos de un minuto, a menudo solo unos pocos segundos. Si seleccionó **Add to Portal** (Agregar al portal), desplácese hasta el portal para ver el nuevo servidor. O bien, vaya a **Más servicios** > **Analysis Services** para ver si el servidor está listo. Si no aparece, actualice la lista.

 ![Panel](./media/analysis-services-create-server/aas-create-server-dashboard.png)

## <a name="automate-server-creation"></a>Automatización de la creación de servidores
Puede automatizar el aprovisionamiento de servidores en marcha con archivos de plantilla de Azure Resource Manager. Vea este práctico vídeo para obtener más información.

>[!VIDEO https://channel9.msdn.com/series/Azure-Analysis-Services/AzureAnalysisServicesAutomation/player]
>
>


## <a name="next-steps"></a>Pasos siguientes
Una vez que ha creado el servidor, puede [implementar un modelo](analysis-services-deploy.md) en él mediante SSDT o con SSMS.

Si un modelo que se implemente en el servidor se conecta a orígenes de datos locales, deberá instalar una [puerta de enlace de datos local](analysis-services-gateway.md) en un equipo de la red.




<!--HONumber=Jan17_HO1-->


