---
title: "Configuración de una red virtual en Azure DevTest Labs | Microsoft Docs"
description: "Aprenda a configurar una red virtual existente y la subred y a usarlas en una máquina virtual con Azure DevTest Labs."
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: 6cda99c2-b87e-4047-90a0-5df10d8e9e14
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2017
ms.author: v-craic
ms.openlocfilehash: 21daa8ff756ee30c6d454d49af7db20afe488c47
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/02/2018
---
# <a name="configure-a-virtual-network-in-azure-devtest-labs"></a>Configuración de una red virtual en Azure DevTest Labs
Como se explica en el artículo [Adición de una máquina virtual a un laboratorio](devtest-lab-add-vm.md), al crear una máquina virtual en un laboratorio, puede especificar una red virtual configurada. Por ejemplo, puede que necesite acceder a los recursos de la red corporativa desde las máquinas virtuales mediante la red virtual que se ha configurado con ExpressRoute o VPN de sitio a sitio.

En este artículo se explica cómo agregar la red virtual existente a la configuración de Virtual Network del laboratorio de modo que esté disponible para seleccionarse al crear las máquinas virtuales.

## <a name="configure-a-virtual-network-for-a-lab-using-the-azure-portal"></a>Configuración de una red virtual para un laboratorio mediante el Portal de Azure
Los pasos siguientes le guiarán en el proceso de agregar una red virtual existente (y la subred) a un laboratorio para que esta se pueda utilizar al crear una máquina virtual en el mismo laboratorio. 

1. Inicie sesión en el [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Seleccione **Más servicios** y, luego, **DevTest Labs** en la lista.
1. En la lista de laboratorios, seleccione el laboratorio que desee. 
1. En el panel principal del laboratorio, seleccione **Configuración y directivas**.

    ![Acceso a la configuración y directivas del laboratorio](./media/devtest-lab-configure-vnet/policies-menu.png)
1. En la sección **RECURSOS EXTERNOS**, seleccione **Redes virtuales**. Verá una lista de redes virtuales que se han configurado para el laboratorio actual, así como la red virtual predeterminada creada para el laboratorio. 
1. Seleccione **+Agregar**.
   
    ![Incorporación de una red virtual existente al laboratorio](./media/devtest-lab-configure-vnet/lab-settings-vnet-add.png)
1. En el panel **Red virtual**, elija **[Seleccionar red virtual]**.
   
    ![Selección de una red virtual existente](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet1.png)
1. En el panel **Elegir red virtual**, seleccione la red virtual deseada. Aparece una lista que muestra todas las redes virtuales que están en la misma región de la suscripción que el laboratorio.
1. Después de seleccionar una red virtual, volverá al panel **Red virtual**. Seleccione la subred en la lista en la parte inferior.

    ![Lista de subredes](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet2.png)
    
    Aparece el panel de la subred de laboratorio.

    ![Panel de la subred de laboratorio](./media/devtest-lab-configure-vnet/lab-subnet.png)
     
   - Especifique un **nombre de subred de laboratorio**.
   - Para permitir que una subred se utilice en la creación de máquinas virtuales de laboratorio, seleccione **Use in virtual machine creation** (Usar en la creación de máquinas virtuales).
   - Para habilitar una [dirección IP pública compartida](devtest-lab-shared-ip.md), seleccione **Habilitar IP pública compartida**.
   - Para permitir direcciones IP públicas en una subred, seleccione **Allow public IP creation** (Permitir IP pública).
   - En el campo **Maximum virtual machines per user** (Número máximo de máquinas virtuales por usuario), especifique el número máximo de máquinas virtuales por usuario para cada subred. Si quiere un número ilimitado de máquinas virtuales, deje este campo en blanco.
1. Seleccione **Aceptar** para cerrar el panel de subred de laboratorio.
1. Seleccione **Guardar** para cerrar el panel de red virtual.

Ahora que está configurada la red virtual, se puede seleccionar al crear una nueva máquina virtual. Para ver cómo crear una máquina virtual y especificar una red virtual, consulte el artículo [Incorporación de una máquina virtual a un laboratorio](devtest-lab-add-vm.md). 

La [Documentación de Virtual Network](https://docs.microsoft.com/azure/virtual-network) de Azure proporciona más información sobre cómo usar las redes virtuales, incluido cómo configurar y administrar una red virtual y conectarse a la red local.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>pasos siguientes
Después de agregar las redes virtuales deseadas al laboratorio, el paso siguiente consiste en [agregar una máquina virtual al laboratorio](devtest-lab-add-vm.md).

