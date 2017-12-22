---
title: "Creación de una máquina virtual con una dirección IP pública estática (Azure Portal) | Microsoft Docs"
description: "Obtenga información sobre cómo crear una máquina virtual con una dirección IP pública estática mediante Azure Portal."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
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
ms.openlocfilehash: 233e4eea8439320c1c7446e2c2b2e9d379351a3e
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="create-a-vm-with-a-static-public-ip-address-using-the-azure-portal"></a>Creación de una máquina virtual con una dirección IP pública estática mediante Azure Portal

> [!div class="op_single_selector"]
> * [Azure Portal](virtual-network-deploy-static-pip-arm-portal.md)
> * [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
> * [CLI de Azure](virtual-network-deploy-static-pip-arm-cli.md)
> * [Plantilla](virtual-network-deploy-static-pip-arm-template.md)
> * [PowerShell (clásico)](virtual-networks-reserved-public-ip.md)

[!INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

> [!NOTE]
> Azure tiene dos modelos de implementación diferentes para crear recursos y trabajar con ellos: [Resource Manager y el clásico](../resource-manager-deployment-model.md). En este artículo se describe el uso del modelo de implementación de Resource Manager, recomendado por Microsoft para la mayoría de las nuevas implementaciones en lugar del modelo de implementación clásica.

[!INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

## <a name="create-a-vm-with-a-static-public-ip"></a>Creación de una máquina virtual con una IP pública estática

Para crear una máquina virtual con una dirección IP pública estática en Azure Portal, complete los siguientes pasos:

1. Desde un explorador, vaya al [Portal de Azure](https://portal.azure.com) y, si fuera necesario, inicie sesión con su cuenta de Azure.
2. En la esquina superior izquierda del portal, haga clic en **Nuevo**>>**Compute**>**Windows Server 2012 R2 Datacenter**.
3. En la lista **Seleccionar un modelo de implementación**, seleccione **Resource Manager** y haga clic en **Crear**.
4. En la hoja **Datos básicos**, escriba la información de la máquina virtual como se muestra a continuación y haga clic en **Aceptar**.
   
    ![Portal de Azure: conceptos básicos](./media/virtual-network-deploy-static-pip-arm-portal/figure1.png)
5. En la hoja **Elegir un tamaño**, haga clic en **A1 estándar** como se muestra a continuación y en **Seleccionar**.
   
    ![Portal de Azure: elegir un tamaño](./media/virtual-network-deploy-static-pip-arm-portal/figure2.png)
6. En la hoja **Configuración**, haga clic en **Dirección IP pública** y, en la hoja **Crear dirección IP pública**, en **Asignación**, haga clic en **Estática** como se muestra a continuación. A continuación, haga clic en **Aceptar**.
   
    ![Portal de Azure: crear la dirección IP pública](./media/virtual-network-deploy-static-pip-arm-portal/figure3.png)
7. En la hoja **Configuración**, haga clic en **Aceptar**.
8. Revise la hoja **Resumen**, como se muestra a continuación, y haga clic en **Aceptar**.
   
    ![Portal de Azure: crear la dirección IP pública](./media/virtual-network-deploy-static-pip-arm-portal/figure4.png)
9. Observe el nuevo icono en el panel.
   
    ![Portal de Azure: crear la dirección IP pública](./media/virtual-network-deploy-static-pip-arm-portal/figure5.png)
10. Una vez creada la máquina virtual, la hoja **Configuración** se muestra, tal como se indica a continuación.
    
    ![Portal de Azure: crear la dirección IP pública](./media/virtual-network-deploy-static-pip-arm-portal/figure6.png)

