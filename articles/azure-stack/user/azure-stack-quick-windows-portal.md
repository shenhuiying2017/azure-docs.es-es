---
title: 'Guía de inicio rápido de Azure Stack: creación de una máquina virtual Windows'
description: 'Guía de inicio rápido de Azure Stack: creación de una máquina virtual Windows con el portal'
services: azure-stack
author: brenduns
manager: femila
ms.service: azure-stack
ms.topic: quickstart
ms.date: 04/23/2018
ms.author: brenduns
ms.reviewer: ''
ms.custom: mvc
ms.openlocfilehash: 5776fc472483018eb2c9e4f8962d0b1e8bce8081
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
ms.locfileid: "32155588"
---
# <a name="quickstart-create-a-windows-server-virtual-machine-with-the-azure-stack-portal"></a>Guía de inicio rápido: Creación de una máquina virtual Windows Server con el portal de Azure Stack

*Se aplica a: sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Puede crear una máquina virtual Windows Server 2016 mediante el portal de Azure Stack. Siga los pasos descritos en este artículo para crear y usar una máquina virtual.

## <a name="sign-in-to-the-azure-stack-portal"></a>Inicio de sesión en el portal de Azure Stack

Inicie sesión en el portal de Azure Stack. La dirección del portal de Azure Stack depende del producto de Azure Stack al que se vaya a conectar:

* Para obtener el Kit de desarrollo de Azure Stack (ASDK), vaya a: https://portal.local.azurestack.external.
* Para un sistema integrado de Azure Stack, vaya a la dirección URL que proporciona su operador de Azure Stack.

## <a name="create-a-virtual-machine"></a>de una máquina virtual

1. Haga clic en **Nuevo** > **Proceso** > **Windows Server 2016 Datacenter Eval** > **Crear**. Si no se muestra la entrada **Windows Server 2016 Datacenter Eval**, póngase en contacto con el operador de Azure Stack. Pídale que la agregue a Marketplace como se explica en el artículo [Add the Windows Server 2016 VM image to the Azure Stack marketplace](../azure-stack-add-default-image.md) (Adición de la imagen de máquina virtual Windows Server 2016 a Azure Stack Marketplace).

    ![Pasos para crear una máquina virtual Windows en el portal](media/azure-stack-quick-windows-portal/image01.png)
2. En **Aspectos básicos**, escriba un **nombre**, un **nombre de usuario** y una **contraseña**. Elija una **suscripción**. Cree un **grupo de recursos**, o seleccione uno ya existente, seleccione una **ubicación** y luego haga clic en **Aceptar**.

    ![Configuración básica](media/azure-stack-quick-windows-portal/image02.png)
3. En **Elegir un tamaño**, haga clic en **D1 Standard** > **Seleccionar**.
    ![Elección del tamaño de máquina virtual](media/azure-stack-quick-windows-portal/image03.png)
4. En **Configuración**, aceote los valores predeterminados y haga clic en **Aceptar**.
    ![Configuración de la máquina virtual](media/azure-stack-quick-windows-portal/image04.png)
5. En **Resumen**, haga clic en **Aceptar** para crear la máquina virtual.
    ![Visualización del resumen y creación de máquina virtual](media/azure-stack-quick-windows-portal/image05.png)
6. Para ver la nueva máquina virtual, haga clic en **Todos los recursos**, busque el nombre de la máquina virtual y, a continuación, haga clic en su nombre en los resultados de la búsqueda.
    ![Visualización de la máquina virtual](media/azure-stack-quick-windows-portal/image06.png)

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando haya terminado de usarla, elimine la máquina virtual y sus recursos. Para ello, seleccione el grupo de recursos en la página de la máquina virtual y haga clic en **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido ha implementado una máquina virtual básica con Windows Server. Para aprender más sobre las máquina virtuales de Azure Stack, continúe con el artículo [Considerations for Virtual Machines in Azure Stack](azure-stack-vm-considerations.md) (Consideraciones acerca de máquinas virtuales de Azure Stack).
