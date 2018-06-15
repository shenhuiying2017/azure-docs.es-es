---
title: Preparación del servidor de Hyper-V local para la recuperación ante desastres de máquinas virtuales de Hyper-V en Azure | Microsoft Docs
description: Obtenga información sobre cómo preparar las máquinas virtuales de Hyper-V locales no administradas por System Center VMM para la recuperación ante desastres en Azure con el servicio Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 03/15/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 7e0219a662483ef123bdc2889a43dd3d93d23ac2
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2018
ms.locfileid: "31413235"
---
# <a name="prepare-on-premises-hyper-v-servers-for-disaster-recovery-to-azure"></a>Preparación de servidores de Hyper-V locales para la recuperación ante desastres en Azure

En este tutorial se muestra cómo preparar la infraestructura de Hyper-V local cuando se desea replicar las máquinas virtuales de Hyper-V en Azure para la recuperación ante desastres. System Center Virtual Machine Manager (VMM) puede administrar los hosts de Hyper-V, pero no es obligatorio.  En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Revisar los requisitos de Hyper-V y, si corresponde, los requisitos de VMM.
> * Preparar VMM si corresponde.
> * Comprobar el acceso a través de Internet a las ubicaciones de Azure.
> * Preparar las máquinas virtuales para que pueda acceder a ellas después de realizar la conmutación por error en Azure.

Este es el segundo tutorial de la serie. Asegúrese de que [ha configurado los componentes de Azure](tutorial-prepare-azure.md) tal como se describe en el tutorial anterior.



## <a name="review-requirements-and-prerequisites"></a>Revisión de requisitos y requisitos previos

Asegúrese de que los hosts y las máquinas virtuales de VMware cumplen los requisitos.

1. [Verifique](hyper-v-azure-support-matrix.md#on-premises-servers) los requisitos del servidor local.
2. [Compruebe los requisitos](hyper-v-azure-support-matrix.md#replicated-vms) de las máquinas virtuales de Hyper-V que desea replicar en Azure.
3. Compruebe las [redes](hyper-v-azure-support-matrix.md#hyper-v-network-configuration) del host de Hyper-V, así como la compatibilidad del [almacenamiento](hyper-v-azure-support-matrix.md#hyper-v-host-storage) invitado y de host en los hosts de Hyper-V locales.
4. Compruebe lo que es compatible para [redes Azure](hyper-v-azure-support-matrix.md#azure-vm-network-configuration-after-failover), [almacenamiento](hyper-v-azure-support-matrix.md#azure-storage), y [proceso](hyper-v-azure-support-matrix.md#azure-compute-features), después de la conmutación por error.
5. Las máquinas virtuales locales que replique en Azure tienen que cumplir los [requisitos de máquina virtual de Azure](hyper-v-azure-support-matrix.md#azure-vm-requirements).


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

1. En este tutorial, la configuración más sencilla es para que los hosts de Hyper-V y el servidor VMM tengan acceso directo a Internet sin proxys. 
2. Asegúrese de que los hosts de Hyper-V, y el servidor VMM si procede, puedan acceder a estas direcciones URL: 

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]
    
3. Si controla el acceso por la dirección IP, asegúrese de que:
    - Las reglas de firewall basadas en la dirección IP se puedan conectar a [intervalos IP de centro de datos de Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) y al puerto HTTPS (443).
    - Permita los intervalos de direcciones IP correspondientes a la región de Azure de su suscripción y del oeste de EE. UU. (se usan para Access Control y para Identity Management).


## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Preparación para la conexión a las máquinas virtuales de Azure después de la conmutación por error

Durante una conmutación por error, es posible que quiera conectarse a la red local replicada.

Para conectarse a máquinas virtuales Windows mediante RDP después de la conmutación por error, conceda permisos de acceso como se indica a continuación:

1. Para obtener acceso a través de Internet, habilite RDP en la máquina virtual local antes de la conmutación por error. Asegúrese de que se hayan agregado las reglas de TCP y UDP para el perfil **Público**, y que RDP se permite en **Firewall de Windows** > **Aplicaciones permitidas** para todos los perfiles.
2. Para obtener acceso a través de VPN de sitio a sitio, habilite RDP en la máquina local. RDP debe permitirse en **Firewall de Windows** -> **Aplicaciones y características permitidas** para redes de **dominio y privadas**.
   Compruebe que la directiva SAN del sistema operativo está establecida en **OnlineAll**. [Más información](https://support.microsoft.com/kb/3031135). No debe haber actualizaciones de Windows pendientes en la máquina virtual cuando se desencadene una conmutación por error. Si hay, no podrá iniciar sesión en la máquina virtual hasta que se complete la actualización.
3. En la máquina virtual de Microsoft Azure después de la conmutación por error, compruebe los **Diagnósticos de arranque** para ver una captura de pantalla de la máquina virtual. Si no puede conectarse, compruebe que se está ejecutando la máquina virtual y revise estas [sugerencias de solución de problemas](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

Después de la conmutación por error, puede acceder a las máquinas virtuales de Azure con la misma dirección IP replicada en la máquina virtual local, o bien con una dirección IP distinta. [Obtenga más información](concepts-on-premises-to-azure-networking.md) sobre la configuración de las direcciones IP para la conmutación por error.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Configuración de la recuperación ante desastres en Azure para máquinas virtuales de Hyper-V](tutorial-hyper-v-to-azure.md)
> [Configuración de la recuperación ante desastres en Azure para máquinas virtuales de Hyper-V en nubes de VMM](tutorial-hyper-v-vmm-to-azure.md)
