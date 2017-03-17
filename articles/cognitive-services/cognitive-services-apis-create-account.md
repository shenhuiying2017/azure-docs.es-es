---
title: "Creación de una cuenta de Cognitive Services APIs en Azure Portal | Microsoft Docs"
description: "Creación de una cuenta de Microsoft Cognitive Services APIs en Azure Portal."
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: b6176bb2-3bb6-4ebf-84d1-3598ee6e01c6
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2017
ms.author: garye;gibattag
translationtype: Human Translation
ms.sourcegitcommit: 1e6ae31b3ef2d9baf578b199233e61936aa3528e
ms.openlocfilehash: a19f56f0b87d992e445034989bd35a827a72f367
ms.lasthandoff: 03/03/2017


---

# <a name="create-a-cognitive-services-apis-account-in-the-azure-portal"></a>Creación de una cuenta de Cognitive Services APIs en Azure Portal

Para usar Microsoft Cognitive Service APIs, primero debe crear una cuenta en Azure Portal.

1. Inicie sesión en el [Portal de Azure](http://portal.azure.com).

2. Haga clic en **+ NUEVO**.

3. Seleccione **Inteligencia y análisis** y, a continuación, **Cognitive Services APIs (versión preliminar)**.

    ![Seleccione Cognitive Services APIs.](media/cognitive-services-apis-create-account/select-cognitive-services-apis.png)

4. En la página **Crear**, proporcione la siguiente información:

    -   **Nombre de cuenta:** nombre de la cuenta. Se recomienda usar un nombre descriptivo, por ejemplo *AFaceAPIAccount*.

    -   **Suscripción:** seleccione una de las suscripciones de Azure disponibles en las que tenga al menos permisos de colaborador.

    -   **Tipo de API:** elija la API de Cognitive Services que desee usar. Para obtener más información sobre las diversas API de Cognitive Services APIs, consulte el sitio de [Cognitive Services](https://azure.microsoft.com/services/cognitive-services/).

    ![Seleccione el tipo de API.](media/cognitive-services-apis-create-account/list-of-apis.png)

    -   **Plan de tarifa:** el costo de la cuenta de Cognitive Services depende del uso real y de las opciones que elija. Para obtener más información sobre los precios de cada API, consulte las [páginas de precios](https://azure.microsoft.com/pricing/details/cognitive-services/).

    -   **Grupo de recursos**: un grupo de recursos es una colección de recursos que comparten el mismo ciclo de vida, los mismos permisos e idénticas directivas. Para obtener más información sobre los grupos de recursos, consulte [Administración de los recursos de Azure a través del Portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).

    -   **Ubicación del grupo de recursos:** esto solo es necesario si la API seleccionada es global (es decir, no está ligada a una ubicación). Sin embargo, si la API es global y no está ligada a una ubicación, debe especificar una ubicación para el grupo de recursos donde residirán los metadatos asociados con la cuenta de la API de Cognitive Services. Esta ubicación no afectará a la disponibilidad en tiempo de ejecución de la cuenta. Para obtener más información sobre el grupo de recursos, consulte [Administración de los recursos de Azure a través del Portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).

    -   **Configuración de la API:** de forma predeterminada, la creación de cuentas está deshabilitada hasta que su [Administrador de cuenta de Azure](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles) la habilite explícitamente.

        Este cambio de configuración se aplicará solo al tipo de API seleccionado actualmente y a la ubicación o a la ubicación del grupo de recursos del panel de la izquierda.

        ![Cree una cuenta de Cognitive Services APIs.](media/cognitive-services-apis-create-account/create-account.png)

        > [!NOTE]
        > Si recibe una notificación que le informa de un error en la actualización de la configuración, esto significa que no inició sesión como [Administrador de cuenta](https://docs.microsoft.com/azure/billing/billing-add-change-azure-subscription-administrator#types-of-azure-admin-accounts). El Administrador de cuenta debe seguir los pasos anteriores para habilitar la creación.
        >
        > ![Mensaje de error al actualizar la configuración](./media/cognitive-services-apis-create-account/updatefailed.png)
        
        En algunos casos, el Administrador de cuenta no puede tener acceso a la suscripción. Si es así, solicite al Administrador de servicios que siga los pasos del documento [Adición o cambio de roles de administrador de Azure que administran la suscripción o servicios](https://docs.microsoft.com/azure/billing/billing-add-change-azure-subscription-administrator).
        
        Para encontrar el Administrador de cuenta o el Administrador de servicios de su suscripción, seleccione su suscripción en [Azure Portal](https://portal.azure.com)y, luego, seleccione __Propiedades__. En la parte inferior de la hoja de propiedades se muestra la información del __Administrador de cuenta__ y del __Administrador de servicios__.
        
        ![Propiedades de la suscripción](./media/cognitive-services-apis-create-account/subscription-properties.png)
    
    Microsoft puede usar los datos que usted envía a Cognitive Services para mejorar sus productos y servicios. Para obtener más información, consulte la [sección Microsoft Cognitive Services](http://www.microsoft.com/Licensing/product-licensing/products.aspx) 
    en los Términos de Online Services.

5. Para anclar la cuenta al panel de Azure Portal, haga clic en **Anclar al panel**.

6. Haga clic en **Crear** para crear la cuenta.

Después de que se haya implementado correctamente la cuenta de Cognitive Services, haga clic en el icono del panel para ver la información de cuenta.

Puede usar la **Dirección URL del extremo** de la sección **Información general** y las claves de la sección **Claves** para empezar a hacer llamadas API en sus aplicaciones.

![Mostrar información de cuenta](media/cognitive-services-apis-create-account/display-account.png)

![Mostrar de claves de cuenta](media/cognitive-services-apis-create-account/account-keys.png)

### <a name="next-steps"></a>Pasos siguientes

- Para obtener más información sobre todos los servicios disponibles de Microsoft Cognitive Services, consulte [Cognitive Services](https://azure.microsoft.com/services/cognitive-services/).

- Para ver las guías de inicio rápido y obtener algunos ejemplos de Cognitive Services APIs, vea:
    - [Introducción a Text Analytics API para detectar opiniones, frases clave, temas e idioma](cognitive-services-text-analytics-quick-start.md)
    - [Guía de inicio rápido para la API de recomendaciones de Cognitive Services](cognitive-services-recommendations-quick-start.md)
