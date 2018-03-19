---
title: "Registrar la suscripción de Azure con Administración de costos de Azure | Microsoft Docs"
description: "Use la suscripción de Azure para registrarse en Azure Cost Management."
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 01/30/2018
ms.topic: quickstart
ms.custom: mvc
ms.service: cost-management
manager: carmonm
ms.openlocfilehash: b7c1fee3276d98b6b7c611a09133804c1253384a
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="register-an-individual-azure-subscription-and-view-cost-data"></a>Registrar una suscripción individual de Azure y ver los datos de costo

Puede usar la suscripción de Azure para registrarse en Azure Cost Management. Este registro le proporciona acceso al portal de Cloudyn. En esta guía de inicio rápido se detalla el proceso de registro necesario para crear una suscripción de evaluación de Cloudyn e iniciar sesión en el portal de Cloudyn. También se muestra cómo empezar a ver inmediatamente los datos de costo.

## <a name="log-in-to-azure"></a>Inicio de sesión en Azure

- Inicie sesión en Azure Portal: http://portal.azure.com/.

## <a name="create-a-trial-registration"></a>Crear un registro de evaluación

1. En Azure Portal, haga clic en **Administración de costos + facturación** en la lista de servicios.
2. En **Overview** (Información general), haga clic en **Administración de costos**.  
    ![Página Administración de costos](./media/quick-register-azure-sub/cost-mgt-billing-service.png)
3. En la página **Administración de costos**, haga clic en **Go to Cost Management** (Ir a Administración de costos) para abrir la página de registro de Cloudyn en una ventana nueva.
4. En la página de registro de evaluación del portal de Cloudyn, escriba el nombre de la compañía y, después, seleccione **Azure Individual Subscription Owner** (Propietario de suscripción individual de Azure). A continuación, haga clic en **Siguiente**. El nombre de la cuenta y el identificador del inquilino se agregarán automáticamente al formulario.  
    ![registro de prueba](./media/quick-register-azure-sub/trial-reg-ind.png)
5. Seleccione el **nombre y el identificador de la oferta** asociados a la suscripción. Si no está seguro de cuál es el identificador de tasa de su suscripción, puede consultar la factura de Azure para buscar el **identificador de oferta**.
6. Acepte los términos de uso, valide la información y, después, haga clic en **Siguiente**.
7. En la página **Gather additional data**(Recopilar datos adicionales), haga clic en **Siguiente** para permitir que Cloudyn recopile los datos de recursos de Azure. Los datos recopilados incluyen datos de uso, de rendimiento, de facturación y de etiquetas de las suscripciones.  
    ![recopilar datos adicionales](./media/quick-register-azure-sub/gather-additional.png)
8. El explorador le lleva a la página de inicio de sesión de Cloudyn. Inicie sesión con las credenciales de la suscripción de Azure.
9. Haga clic en **Go to Cloudyn** (Ir a Cloudyn) para abrir el portal de Cloudyn y, después, en la página **Administración de cuentas**, debería ver la información de la cuenta de la suscripción de Azure.  
    ![Administración de cuentas](./media/quick-register-azure-sub/accounts-mgt.png)

Para ver un vídeo tutorial sobre cómo registrar su suscripción de Azure, consulte [Finding your Directory GUID and Rate ID for use in Azure Cost Management by Cloudyn](https://youtu.be/PaRjnyaNGMI) (Búsqueda del GUID de directorio y del identificador de tasa para usarlos en Azure Cost Management de Cloudyn).

[!INCLUDE [cost-management-create-account-view-data](../../includes/cost-management-create-account-view-data.md)]

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido, se usa la información de su suscripción de Azure para registrarse en Administración de costos. También se inicia sesión en el portal de Cloudyn para que pueda empezar a visualizar los datos de costo. Para más información sobre Azure Cost Management, continúe con el tutorial de Cost Management.

> [!div class="nextstepaction"]
> [Revisión del uso y los costos](./tutorial-review-usage.md)
