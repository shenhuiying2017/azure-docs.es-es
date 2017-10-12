---
title: "Creación de un plan en Azure Stack | Microsoft Docs"
description: "Como administrador de la nube, cree un plan que permita a los suscriptores aprovisionar máquinas virtuales."
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 3dc92e5c-c004-49db-9a94-783f1f798b98
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 7/10/2017
ms.author: erikje
ms.openlocfilehash: 30759dca746fd7fd02653556cb105f419f5bf854
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-plan-in-azure-stack"></a>Creación de un plan en Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Azure Stack Development Kit*

Los [planes](azure-stack-key-features.md) son agrupaciones de uno o varios servicios. Como proveedor puede crear planes y ofrecérselos a sus usuarios. A su vez, los usuarios se suscriben a las ofertas para usar los planes y los servicios que incluyen. Este ejemplo muestra cómo crear un plan que incluya a los proveedores de recursos de proceso, de red y de almacenamiento. Este plan ofrece a los suscriptores la capacidad de aprovisionar máquinas virtuales.

1. Inicie sesión en el portal de administrador de Azure Stack (https://adminportal.local.azurestack.external). Escriba las credenciales de la cuenta que creó en el paso 5 de la sección [Ejecución del script de PowerShell](azure-stack-run-powershell-script.md).

2. Para crear un plan y una oferta a la que los usuarios puedan suscribirse, haga clic en **Nuevo** > **Ofertas y planes de inquilino** > **Plan**.

   ![](media/azure-stack-create-plan/image01.png)
3. En la hoja **Nuevo plan**, rellene **Nombre para mostrar** y **Nombre de recurso**. El nombre para mostrar es el nombre descriptivo del plan que ven los usuarios. Solo el administrador puede ver el nombre del recurso. Es el nombre que usan los administradores para trabajar con el plan como un recurso de Administrador de recursos de Azure.

   ![](media/azure-stack-create-plan/image02.png)
4. Cree un nuevo **Grupo de recursos** o seleccione uno existente, como contenedor para el plan.

   ![](media/azure-stack-create-plan/image02a.png)
5. Haga clic en **Servicios**, seleccione **Microsoft.Compute**, **Microsoft.Network** y **Microsoft.Storage** y, a continuación, haga clic en **Seleccionar**.

   ![](media/azure-stack-create-plan/image03.png)
6. Haga clic en **Cuotas**, haga clic en **Microsoft.Storage (local)** y, a continuación, seleccione la cuota predeterminada o haga clic en **Crear nueva cuota** para personalizar la cuota.

   ![](media/azure-stack-create-plan/image04.png)
7. Si va a crear una nueva cuota, escriba un nombre para la cuota > establezca los valores de la cuota > haga clic en **Aceptar** > haga clic en el nombre de la nueva cuota.

   ![](media/azure-stack-create-plan/image06.png)
8. Haga clic en **Microsoft.Network (local)** y, a continuación, seleccione la cuota predeterminada o haga clic en **Crear nueva cuota** para personalizar la cuota.

    ![](media/azure-stack-create-plan/image07.png)
9. Si va a crear una nueva cuota, escriba un nombre para la cuota > establezca los valores de la cuota > haga clic en **Aceptar** > haga clic en el nombre de la nueva cuota.

    ![](media/azure-stack-create-plan/image08.png)
10. Haga clic en **Microsoft.Compute (local)** y, a continuación, seleccione la cuota predeterminada o haga clic en **Crear nueva cuota** para personalizar la cuota.

    ![](media/azure-stack-create-plan/image09.png)
11. Si va a crear una nueva cuota, escriba un nombre para la cuota > establezca los valores de la cuota > haga clic en **Aceptar** > haga clic en el nombre de la nueva cuota.

    ![](media/azure-stack-create-plan/image10.png)
12. En la hoja **Cuotas**, haga clic en **Aceptar** y, a continuación, en la hoja **Nuevo plan** y haga clic en **Crear** para crear el plan.

    ![](media/azure-stack-create-plan/image11.png)
13. Para ver el nuevo plan, haga clic en **Todos los recursos**, a continuación, busque el plan y haga clic en su nombre.

    ![](media/azure-stack-create-plan/image12.png)

### <a name="next-steps"></a>Pasos siguientes
[Creación de una oferta](azure-stack-create-offer.md)
