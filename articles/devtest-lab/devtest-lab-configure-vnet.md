---
title: "Configuración de una red virtual en Azure DevTest Labs | Microsoft Docs"
description: "Aprenda a configurar una red virtual existente y la subred y a usarlas en una máquina virtual con Azure DevTest Labs."
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 6cda99c2-b87e-4047-90a0-5df10d8e9e14
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2017
ms.author: tarcher
ms.openlocfilehash: 19dbb1625f46f8864413dc538a96b2413bc6eea0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="configure-a-virtual-network-in-azure-devtest-labs"></a>Configuración de una red virtual en Azure DevTest Labs
Como se explica en el artículo [Incorporación de una máquina virtual con artefactos a un laboratorio](devtest-lab-add-vm-with-artifacts.md), al crear una máquina virtual en un laboratorio, puede especificar una red virtual configurada. Una situación en la que se podría aplicar esto es si necesita acceder a los recursos de la red corporativa desde las máquinas virtuales mediante la red virtual que se ha configurado con ExpressRoute o VPN de sitio a sitio. En las siguientes secciones se muestra cómo agregar la red virtual existente a la configuración de red virtual del laboratorio de modo que esté disponible para seleccionarse al crear las máquinas virtuales.

## <a name="configure-a-virtual-network-for-a-lab-using-the-azure-portal"></a>Configuración de una red virtual para un laboratorio mediante el Portal de Azure
Los pasos siguientes le guiarán en el proceso de agregar una red virtual existente (y la subred) a un laboratorio para que esta se pueda utilizar al crear una máquina virtual en el mismo laboratorio. 

1. Inicie sesión en el [Portal de Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Seleccione **Más servicios** y, luego, **DevTest Labs** en la lista.
3. En la lista de laboratorios, seleccione el laboratorio que desee. 
4. En la hoja del laboratorio, seleccione **Directivas y configuración**.
5. En la hoja **Configuration and Policies** (Directivas y configuración) del laboratorio, seleccione **Redes virtuales**.
6. En la hoja **Redes virtuales** , verá una lista de redes virtuales que se han configurado para el laboratorio actual, así como la red virtual predeterminada que se crea para el laboratorio. 
7. Seleccione **+Agregar**.
   
    ![Incorporación de una red virtual existente al laboratorio](./media/devtest-lab-configure-vnet/lab-settings-vnet-add.png)
8. En la hoja **Red virtual**, elija **[Seleccionar red virtual]**.
   
    ![Selección de una red virtual existente](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet1.png)
9. En la hoja **Elegir red virtual** , seleccione la red virtual deseada. La hoja muestra todas las redes virtuales que están en la misma región de la suscripción que el laboratorio.  
10. Después de seleccionar una red virtual, se le redirigirá a la **red virtual**. Haga clic en la subred de la lista en la parte inferior de la hoja.

    ![Lista de subredes](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet2.png)
    
    Se muestra la hoja de la subred de laboratorio.

    ![Hoja de la subred de laboratorio](./media/devtest-lab-configure-vnet/lab-subnet.png)

11. Especifique un **nombre de subred de laboratorio**.
12. Para permitir que una subred se utilice en la creación de máquinas virtuales de laboratorio, seleccione **Use in virtual machine creation** (Usar en la creación de máquinas virtuales).
13. Para habilitar una [dirección IP pública compartida](devtest-lab-shared-ip.md), seleccione **Habilitar IP pública compartida**.
14. Para permitir direcciones IP públicas en una subred, seleccione **Allow public IP creation** (Permitir IP pública).
15. En el campo **Maximum virtual machines per user** (Número máximo de máquinas virtuales por usuario), especifique el número máximo de máquinas virtuales por usuario para cada subred. Si quiere un número ilimitado de máquinas virtuales, deje este campo en blanco.
16. Seleccione **Aceptar** para cerrar la hoja de subred de laboratorio.
17. Seleccione **Guardar** para cerrar la hoja de red virtual.
18. Ahora que está configurada la red virtual, se puede seleccionar al crear una nueva máquina virtual. 
    Para ver cómo crear una máquina virtual y especificar una red virtual, consulte el artículo [Incorporación de una máquina virtual con artefactos a un laboratorio](devtest-lab-add-vm-with-artifacts.md). 

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Pasos siguientes
Después de agregar las redes virtuales deseadas al laboratorio, el paso siguiente consiste en [agregar una máquina virtual al laboratorio](devtest-lab-add-vm-with-artifacts.md).

