---
title: "Preparación del servidor de Hyper-V local para la recuperación ante desastres de máquinas virtuales de Hyper-V en Azure | Microsoft Docs"
description: "Obtenga información sobre cómo preparar las máquinas virtuales de Hyper-V locales no administradas por System Center VMM para la recuperación ante desastres en Azure con el servicio Azure Site Recovery."
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 12/31/2017
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 6e2837341e16f5077cfff18d4a34c097c165ef09
ms.sourcegitcommit: 9ea2edae5dbb4a104322135bef957ba6e9aeecde
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/03/2018
---
# <a name="prepare-on-premises-hyper-v-servers-for-disaster-recovery-to-azure"></a>Preparación de servidores de Hyper-V locales para la recuperación ante desastres en Azure

En este tutorial se muestra cómo preparar la infraestructura de Hyper-V local cuando se desea replicar las máquinas virtuales de Hyper-V en Azure para la recuperación ante desastres. System Center Virtual Machine Manager (VMM) puede administrar los hosts de Hyper-V, pero no es obligatorio.  En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Revisar los requisitos de Hyper-V y, si corresponde, los requisitos de VMM.
> * Preparar VMM si corresponde.
> * Comprobar el acceso a través de Internet a las ubicaciones de Azure.
> * Preparar las máquinas virtuales para que pueda acceder a ellas después de realizar la conmutación por error en Azure.

Este es el segundo tutorial de la serie. Asegúrese de que [ha configurado los componentes de Azure](tutorial-prepare-azure.md) tal como se describe en el tutorial anterior.



## <a name="review-server-requirements"></a>Revisión de los requisitos del servidor

Asegúrese de que los hosts de Hyper-V cumplan con los requisitos siguientes. Si administra hosts en las nubes de System Center Virtual Machine Manager (VMM), compruebe los requisitos de VMM.


**Componente** | **Hyper-V administrado por VMM** | **Hyper-V sin VMM**
--- | --- | ---
**Sistema operativo host de Hyper-V** | Windows Server 2016, 2012 R2 | N/D
**VMM** | VMM 2012, VMM 2012 R2 | N/D


## <a name="review-hyper-v-vm-requirements"></a>Revisión de los requisitos de la máquina virtual de Hyper-V

Asegúrese de que la máquina virtual cumple con los requisitos que aparecen resumidos en la tabla.

**Requisito de la máquina virtual** | **Detalles**
--- | ---
**Sistema operativo invitado** | Cualquier sistema operativo invitado [compatible con Azure](https://technet.microsoft.com/library/cc794868.aspx).
**Requisitos de Azure** | Las máquinas virtuales de Hyper-V locales deben cumplir con los requisitos de la máquina virtual de Azure (site-recovery-support-matrix-to-azure.md).

## <a name="prepare-vmm-optional"></a>Preparación de VMM (opcional)

Si VMM administra los hosts de Hyper-V, debe preparar el servidor VMM local. 

- Asegúrese de que el servidor VMM tiene al menos una nube, con uno o más grupos host. El host de Hyper-V en el que se ejecutan las máquinas virtuales debe encontrarse en la nube.
- Prepare el servidor VMM para la asignación de red.

### <a name="prepare-vmm-for-network-mapping"></a>Preparar VMM para la asignación de red

Si usa VMM, la [asignación de red](site-recovery-network-mapping.md) asigna entre redes de máquinas virtuales de VMM locales y redes virtuales de Azure. La asignación garantiza que las máquinas virtuales de Azure se conecten a la red adecuada cuando se crean después de la conmutación por error.

Para preparar VMM para la asignación de red, siga estos pasos:

1. Asegúrese de que tiene una [red lógica de VMM](https://docs.microsoft.com/system-center/vmm/network-logical) que esté asociada a la nube en la que se encuentran los hosts de Hyper-V.
2. Asegúrese de que tiene una [red de máquinas virtuales](https://docs.microsoft.com/system-center/vmm/network-virtual) vinculada a la red lógica.
3. En VMM, conecte las máquinas virtuales a la red de VM.

## <a name="verify-internet-access"></a>Comprobación del acceso a Internet

1. En este tutorial, la configuración más sencilla es para los hosts de Hyper-V y el servidor de VMM, si corresponde, para tener acceso directo a Internet sin usar proxy. 
2. Asegúrese de que los hosts de Hyper-V, y el servidor VMM si procede, puedan acceder a estas direcciones URL: 

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]
    
3. Asegúrese de que:
    - Todas las reglas de firewall basadas en direcciones IP deben permitir la comunicación con Azure.
    - Permita los [intervalos IP del centro de datos de Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) y el puerto HTTPS (443).
    - Permita los intervalos de direcciones IP correspondientes a la región de Azure de su suscripción y del oeste de EE. UU. (se usan para Access Control y para Identity Management).


## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Preparación para la conexión a las máquinas virtuales de Azure después de la conmutación por error

Durante una conmutación por error, es posible que quiera conectarse a la red local replicada.

Para conectarse a máquinas virtuales Windows mediante RDP después de la conmutación por error, haga lo siguiente:

1. Para obtener acceso a través de Internet, habilite RDP en la máquina virtual local antes de la conmutación por error. Asegúrese de que se hayan agregado las reglas de TCP y UDP para el perfil **Público**, y que RDP se permite en **Firewall de Windows** > **Aplicaciones permitidas** para todos los perfiles.
2. Para obtener acceso a través de VPN de sitio a sitio, habilite RDP en la máquina local. RDP debe permitirse en **Firewall de Windows** -> **Aplicaciones y características permitidas** para redes de **dominio y privadas**.
   Compruebe que la directiva SAN del sistema operativo está establecida en **OnlineAll**. [Más información](https://support.microsoft.com/kb/3031135). No debe haber actualizaciones de Windows pendientes en la máquina virtual cuando se desencadene una conmutación por error. Si hay, no podrá iniciar sesión en la máquina virtual hasta que se complete la actualización.
3. En la máquina virtual de Microsoft Azure después de la conmutación por error, compruebe los **Diagnósticos de arranque** para ver una captura de pantalla de la máquina virtual. Si no puede conectarse, compruebe que se está ejecutando la máquina virtual y revise estas [sugerencias de solución de problemas](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).


## <a name="next-steps"></a>pasos siguientes

> [!div class="nextstepaction"]
> [Configuración de la recuperación ante desastres en Azure para máquinas virtuales de Hyper-V](tutorial-hyper-v-to-azure.md)
> [Configuración de la recuperación ante desastres en Azure para máquinas virtuales de Hyper-V en nubes de VMM](tutorial-hyper-v-vmm-to-azure.md)
