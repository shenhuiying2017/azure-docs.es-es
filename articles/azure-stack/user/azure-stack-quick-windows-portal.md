---
title: "Guía de inicio rápido de Azure Stack: creación de una máquina virtual Windows"
description: "Guía de inicio rápido de Azure Stack: creación de una máquina virtual Windows con el portal"
services: azure-stack
author: ErikjeMS
manager: byronr
ms.service: azure-stack
ms.topic: quickstart
ms.date: 09/15/2017
ms.author: erikje
ms.custom: mvc
ms.openlocfilehash: 64a623b3f5cb443e0676e7d89d7040837be95737
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/05/2017
---
# <a name="create-a-windows-virtual-machine-with-the-azure-stack-portal"></a>Creación de una máquina virtual Windows con el portal de Azure Stack

Puede crear una máquina virtual Windows mediante el portal de Azure Stack. El portal es una interfaz de usuario basada en explorador donde puede crear, configurar y administrar los recursos.

## <a name="sign-in-to-the-azure-stack-portal"></a>Inicio de sesión en el portal de Azure Stack

Inicie sesión en el portal de Azure Stack. La dirección del portal de Azure Stack depende del producto de Azure Stack al que se vaya a conectar:

* Para Azure Stack Development Kit (ASDK), vaya a https://portal.local.azurestack.external.
* Para un sistema integrado de Azure Stack, vaya a la dirección URL que proporciona su operador de Azure Stack.

## <a name="create-a-virtual-machine"></a>de una máquina virtual

1. Haga clic en **Nuevo** > **Proceso** > **Windows Server 2016 Datacenter Eval** > **Crear**. Si no se muestra la entrada **Windows Server 2016 Datacenter Eval**, póngase en contacto con el operador de Azure Stack. Pídale que la agregue a Marketplace como se explica en el artículo [Add the Windows Server 2016 VM image to the Azure Stack marketplace](../azure-stack-add-default-image.md) (Adición de la imagen de máquina virtual Windows Server 2016 a Azure Stack Marketplace). 
    ![](media/azure-stack-quick-windows-portal/image01.png)
2. En **Aspectos básicos**, escriba un **nombre**, un **nombre de usuario** y una **contraseña**. Elija una **suscripción**. Cree un **grupo de recursos**, o seleccione uno ya existente, seleccione una **ubicación** y luego haga clic en **Aceptar**.

    ![](media/azure-stack-quick-windows-portal/image02.png)
3. En **Elegir un tamaño**, haga clic en **D1 Standard** > **Seleccionar**.
    ![](media/azure-stack-quick-windows-portal/image03.png)
4. En **Configuración**, aceote los valores predeterminados y haga clic en **Aceptar**.
    ![](media/azure-stack-quick-windows-portal/image04.png)
5. En **Resumen**, haga clic en **Aceptar** para crear la máquina virtual. 
    ![](media/azure-stack-quick-windows-portal/image05.png)
6. Para ver la nueva máquina virtual, haga clic en **Todos los recursos** y, a continuación, busque la máquina virtual y haga clic en su nombre.
    ![](media/azure-stack-quick-windows-portal/image06.png)

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no necesite la máquina virtual, elimine el grupo de recursos, la máquina virtual y todos los recursos relacionados. Para ello, seleccione el grupo de recursos de la página de la máquina virtual y haga clic en **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes
En esta guía rápida, ha implementado una máquina virtual Windows sencilla. Para aprender más sobre las máquina virtuales de Azure Stack, continúe con el artículo [Considerations for Virtual Machines in Azure Stack](azure-stack-vm-considerations.md) (Consideraciones sobre Virtual Machines en Azure Stack).
