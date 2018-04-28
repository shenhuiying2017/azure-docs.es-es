---
title: Creación de una máquina virtual con una dirección IP pública estática (Azure Portal) | Microsoft Docs
description: Obtenga información sobre cómo crear una máquina virtual con una dirección IP pública estática mediante Azure Portal.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: e9546bcc-f300-428f-b94a-056c5bd29035
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 50ae4d6e8c275db16f811a2a1a063eda441f150b
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/18/2018
---
# <a name="create-a-vm-with-a-static-public-ip-address-using-the-azure-portal"></a>Creación de una máquina virtual con una dirección IP pública estática mediante Azure Portal

> [!div class="op_single_selector"]
> * [Azure Portal](virtual-network-deploy-static-pip-arm-portal.md)
> * [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
> * [CLI de Azure](virtual-network-deploy-static-pip-arm-cli.md)
> * [PowerShell (clásico)](virtual-networks-reserved-public-ip.md)

[!INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

> [!NOTE]
> Azure tiene dos modelos de implementación diferentes para crear recursos y trabajar con ellos: [Resource Manager y el clásico](../resource-manager-deployment-model.md). En este artículo se describe el uso del modelo de implementación de Resource Manager, recomendado por Microsoft para la mayoría de las nuevas implementaciones en lugar del modelo de implementación clásica.

[!INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

## <a name="create-a-vm-with-a-static-public-ip"></a>Creación de una máquina virtual con una IP pública estática

Para crear una máquina virtual con una dirección IP pública estática en Azure Portal, complete los siguientes pasos:

1. Desde un explorador, vaya [Azure Portal](https://portal.azure.com) y, si fuera necesario, inicie sesión con su cuenta de Azure.
2. En la esquina superior izquierda del portal, haga clic en **Crear un recurso**>>**Proceso**>**Windows Server 2012 R2 Datacenter**.
3. En la lista **Seleccionar un modelo de implementación**, seleccione **Resource Manager** y haga clic en **Crear**.
4. En el panel **Básico**, escriba la información de la máquina virtual de la manera siguiente y, luego, haga clic en **Aceptar**.
   
    ![Portal de Azure: conceptos básicos](./media/virtual-network-deploy-static-pip-arm-portal/figure1.png)
5. En el panel **Elegir un tamaño**, haga clic en **A1 Estándar** y, después, en **Seleccionar**.
   
    ![Portal de Azure: elegir un tamaño](./media/virtual-network-deploy-static-pip-arm-portal/figure2.png)
6. En el panel **Configuración**, haga clic en **Dirección IP pública** y, en el panel **Crear dirección IP pública**, en **Asignación**, haga clic en **Estática** como se muestra a continuación. A continuación, haga clic en **Aceptar**.
   
    ![Portal de Azure: crear la dirección IP pública](./media/virtual-network-deploy-static-pip-arm-portal/figure3.png)
7. En el panel **Configuración**, haga clic en **Aceptar**.
8. Revise el panel **Resumen**, como se indica a continuación y, a continuación, haga clic en **Aceptar**.
   
    ![Portal de Azure: crear la dirección IP pública](./media/virtual-network-deploy-static-pip-arm-portal/figure4.png)
9. Observe el nuevo icono en el panel.
   
    ![Portal de Azure: crear la dirección IP pública](./media/virtual-network-deploy-static-pip-arm-portal/figure5.png)
10. Una vez creada la máquina virtual, se muestra el panel **Configuración** de la manera siguiente:
    
    ![Portal de Azure: crear la dirección IP pública](./media/virtual-network-deploy-static-pip-arm-portal/figure6.png)

## <a name="set-ip-addresses-within-the-operating-system"></a>Configuración de direcciones IP en el sistema operativo

No asigne manualmente la dirección IP pública asignada a una máquina virtual de Azure en el sistema operativo de la máquina virtual. Se recomienda no asignar estáticamente la dirección IP privada asignada a la máquina virtual de Azure en el sistema operativo de una máquina virtual, a menos que sea necesario, como al [asignar varias direcciones IP a una máquina virtual Windows](virtual-network-multiple-ip-addresses-portal.md). Al establecer manualmente la dirección IP privada en el sistema operativo, asegúrese de que sea la misma que la asignada a la [interfaz de red](virtual-network-network-interface-addresses.md#change-ip-address-settings) de Azure, de lo contrario, perderá la conectividad a la máquina virtual. Más información sobre la configuración de la [dirección IP privada](virtual-network-network-interface-addresses.md#private).

## <a name="next-steps"></a>Pasos siguientes

Cualquier tráfico de red puede fluir hacia la máquina virtual creada en este artículo y desde ella. Puede definir reglas de seguridad entrantes y salientes en un grupo de seguridad de red que limite el tráfico que puede fluir hacia la interfaz de red, la subred o ambas y desde ellas. Para más información acerca de los grupos de seguridad de red, consulte [Seguridad de las redes](security-overview.md).