---
title: Restablecimiento de la interfaz de red para VM de Microsoft Azure| Microsoft Docs
description: "Muestra cómo restablecer la interfaz de red para VM de Microsoft Azure"
services: virtual-machines-windows, azure-resource-manager
documentationcenter: 
author: genlin
manager: willchen
editor: 
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: 6bf5c991e8a96cfdcbad971e0f2ea2dfd01f2893
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/03/2018
---
# <a name="how-to-reset-network-interface-for-azure-windows-vm"></a>Restablecimiento de la interfaz de red para VM de Microsoft Azure 

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

No puede conectarse a la máquina virtual (VM) de Microsoft Azure después de deshabilitar la interfaz de red (NIC) predeterminada o de establecer manualmente un IP estático para la NIC. Este artículo muestra cómo restablecer la interfaz de red para la máquina virtual de Microsoft Azure, que resolverá el problema de conexión remota.

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]
## <a name="reset-network-interface"></a>Restablecimiento de la interfaz de red

### <a name="for-classic-vms"></a>Para máquinas virtuales clásicas

Para restablecer la interfaz de red, siga estos pasos:

1.  Vaya a [Azure Portal]( https://ms.portal.azure.com).
2.  Seleccione **Máquinas virtuales (clásico)**.
3.  Seleccione la máquina virtual afectada.
4.  Seleccione **Direcciones IP**.
5.  Si la **Asignación de IP privada** no es  **Estática**, cámbiela a **Estática**.
6.  Cambie la **Dirección IP** a otra dirección IP disponible en la subred.
7.  Seleccione Guardar.
8.  La máquina virtual se reiniciará para inicializar la nueva NIC en el sistema.
9.  Intente utilizar RDP en la máquina. Si la operación se realiza correctamente, puede volver a la dirección IP privada original si lo desea. De lo contrario, manténgala. 

### <a name="for-vms-deployed-in-resource-group-model"></a>Para máquinas virtuales implementadas en el módulo de grupo de recursos

1.  Vaya a [Azure Portal]( https://ms.portal.azure.com).
2.  Seleccione la máquina virtual afectada.
3.  Seleccione **Interfaces de red**.
4.  Seleccione la interfaz de red asociada a la máquina.
5.  Seleccione **Configuraciones IP**.
6.  Seleccione la IP. 
7.  Si la **Asignación de IP privada** no es  **Estática**, cámbiela a **Estática**.
8.  Cambie la **Dirección IP** a otra dirección IP disponible en la subred.
9. La máquina virtual se reiniciará para inicializar la nueva NIC en el sistema.
10. Intente utilizar RDP en la máquina. Si la operación se realiza correctamente, puede volver a la dirección IP privada original si lo desea. De lo contrario, manténgala. 

## <a name="delete-the-unavailable-nics"></a>Eliminación de las NIC no disponibles
Cuando pueda utilizar el escritorio remoto en la máquina, debe eliminar las NIC antiguas para evitar el posible problema:

1.  Abra el Administrador de dispositivos.
2.  Seleccione **Vista** > **Mostrar dispositivos ocultos**.
3.  Seleccione **Adaptadores de red**. 
4.  Compruebe que los adaptadores tengan el nombre de "Adaptador de red Microsoft Hyper-V".
5.  Puede que vea un adaptador no disponible atenuado. Haga clic con el botón derecho en el adaptador y, a continuación, seleccione Desinstalar.

    ![la imagen de la NIC](media/reset-network-interface/nicpage.png)

    > [!NOTE]
    > Solo desinstale los adaptadores no disponibles con el nombre "Adaptador de red de Microsoft Hyper-V". Si desinstala alguno de los adaptadores ocultos, podrían producirse problemas adicionales.
    >
    >

6.  Ahora deben borrarse todos los adaptadores no disponibles del sistema.
