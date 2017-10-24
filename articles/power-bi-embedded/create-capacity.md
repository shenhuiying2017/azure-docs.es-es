---
title: "Creación de una capacidad de Power BI Embedded en Azure Portal | Microsoft Docs"
description: "Este artículo lo guía por el proceso de creación de una capacidad de Power BI Embedded en Microsoft Azure."
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/28/2017
ms.author: asaxton
ms.openlocfilehash: 1902e5c18cd7083ceeda79e6b9e779e4baaf175a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="create-power-bi-embedded-capacity-in-the-azure-portal"></a>Creación de una capacidad de Power BI Embedded en Azure Portal

Este artículo lo guía por el proceso de creación de una capacidad de Power BI Embedded en Microsoft Azure. Además, simplifica las funcionalidades de Power BI, ya que los ayudan a agregar rápidamente sorprendentes objetos visuales, informes y paneles a sus aplicaciones.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar.

> [!VIDEO https://www.youtube.com/embed/aXrvFfg_iSk]

## <a name="before-you-begin"></a>Antes de empezar

Para completar este inicio rápido necesita instalar:

* **Suscripción de Azure**: visite [Evaluación gratuita de Azure](https://azure.microsoft.com/free/) para crear una cuenta.
* **Azure Active Directory**: la suscripción debe estar asociada a un inquilino de Azure Active Directory (AAD). Además, ***debe estar conectado a Azure con una cuenta en ese inquilino***. No se admiten cuentas de Microsoft. Para obtener más información, consulte Permisos de usuario y autenticación.
* **Inquilino de Power BI**: debe haber registrada, al menos, una cuenta en su inquilino de AAD para Power BI.
* **Grupo de recursos**: use un grupo de recursos que ya tenga o [cree uno](../azure-resource-manager/resource-group-overview.md).

## <a name="create-a-capacity"></a>Creación de una capacidad

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).

2. Seleccione **+ (Nuevo)** > **Datos y análisis**.

3. En el cuadro de búsqueda, busque *Power BI Embedded*.

4. En Power BI Embedded, seleccione **Crear**.

5. Rellene la información necesaria y haga clic en **Crear**.

    ![Campos para rellenar con el fin de crear una capacidad](media/create-capacity/azure-portal-create-power-bi-embedded.png)

    |Configuración |Descripción |
    |---------|---------|
    |**Nombre del recurso**|Nombre para identificar la capacidad. El nombre del recurso se muestra en el portal de administración de Power BI, además de en Azure Portal.|
    |**Suscripción**|La suscripción en la que le gustaría crear la capacidad.|
    |**Grupos de recursos**|El grupo de recursos que contiene esta nueva capacidad. Elija un grupo de recursos o cree uno. Para más información, consulte [Información general de Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).|
    |**Administrador de capacidad de Power BI**|Los administradores de capacidad de Power BI pueden ver la capacidad en el portal de administración de Power BI, así como conceder permisos de asignación a otros usuarios. De forma predeterminada, el administrador de capacidad es su cuenta. El administrador de capacidad debe encontrarse en el inquilino de Power BI.|
    |**Ubicación**|La ubicación en la que se hospedará Power BI para su inquilino. Esta configuración se resuelve automáticamente; no se puede seleccionar otra.|
    |**Plan de tarifa**|Seleccione la SKU (número de núcleo virtual y tamaño de memoria) que satisfaga sus necesidades.  Para ver los detalles, consulte [Precios de Power BI Embedded](https://azure.microsoft.com/pricing/details/power-bi-embedded/).|

6. Seleccione **Crear**.

El proceso de creación normalmente tarda menos de un minuto; a menudo, solo unos segundos. Si seleccionó **Anclar al panel**, acceda al panel para ver la nueva capacidad. También puede ir a **Más servicios** > **Power BI Embedded** para ver si la capacidad está lista.

![Panel de Azure Portal con la capacidad de Power BI Embedded](media/create-capacity/azure-portal-dashboard.png)

## <a name="next-steps"></a>Pasos siguientes

Si quiere usar la nueva capacidad de Power BI Embedded, vaya al portal de administración de Power BI para asignar áreas de trabajo. Para obtener más información, consulte [Administración de la capacidad en Power BI Premium y Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-admin-premium-manage/).

Si no necesita usar esta capacidad, páusela para detener la facturación. Para obtener más información, consulte [Pausa e inicio de la capacidad de Power BI Embedded en Azure Portal](pause-start.md).

Para empezar a insertar contenido de Power BI en una aplicación, consulte [Procedimiento para insertar paneles, informes e iconos de Power BI](https://powerbi.microsoft.com/documentation/powerbi-developer-embedding-content/).

¿Tiene más preguntas? [Pruebe a plantearlas en la Comunidad de Power BI](http://community.powerbi.com/)