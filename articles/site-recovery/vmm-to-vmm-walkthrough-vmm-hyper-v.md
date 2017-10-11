---
title: "Configuración de VMM y Hyper-V para la replicación en un sitio secundario con Azure Site Recovery | Microsoft Docs"
description: "Describe cómo configurar servidores de System Center VMM y hosts de Hyper-V para la replicación en un sitio de VMM secundario."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: d0389e3b-3737-496c-bda6-77152264dd98
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2017
ms.author: raynew
ms.openlocfilehash: 73bd1790c56ac52166f638de2e80c2a2cfb87e53
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="step-4-set-up-vmm-and-hyper-v-for-hyper-v-vm-replication-to-a-secondary-site"></a>Paso 4: Configuración de VMM y Hyper-V para la replicación de máquinas virtuales de Hyper-V en un sitio secundario 

Una vez que se haya preparado para las redes, configure servidores de System Center Virtual Machine Manager (VMM) y hosts de Hyper-V para la replicación de máquinas virtuales de Hyper-V en un sitio secundario mediante [Azure Site Recovery](site-recovery-overview.md) en Azure Portal. 

Publique cualquier comentario que tenga en la parte inferior de este artículo, o bien en el [foro de Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).



## <a name="prepare-vmm-servers"></a>Preparar los servidores VMM 

Para prepararse para la implementación:


1. Asegúrese de que los servidores VMM cumplen los [requisitos de soporte técnico](site-recovery-support-matrix-to-sec-site.md#on-premises-servers) y los [requisitos previos de implementación](vmm-to-vmm-walkthrough-prerequisites.md).
2. Asegúrese de que los servidores VMM están conectados a Internet y tienen acceso a estas direcciones URL.
    
    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]
    
    - Si tiene reglas de firewall basadas en direcciones IP, asegúrese de que permitan la comunicación con Azure.
    - Permita los [intervalos IP del centro de datos de Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) y el puerto HTTPS (443).
    - Permita los intervalos de direcciones IP correspondientes a la región de Azure de su suscripción y del oeste de EE. UU. (se usan para Access Control y para Identity Management).
3. Asegúrese de que el servidor VMM está [preparado para la asignación de red](vmm-to-vmm-walkthrough-network.md#prepare-for-network-mapping).


## <a name="prepare-hyper-v-hostsclusters"></a>Preparar los hosts o clústeres de Hyper-V

1. Asegúrese de que los hosts o clústeres de Hyper-V cumplen los [requisitos de soporte técnico](site-recovery-support-matrix-to-sec-site.md#on-premises-servers) y los [requisitos previos de implementación](vmm-to-vmm-walkthrough-prerequisites.md).
2. Compruebe los requisitos para las [máquinas virtuales de Hyper-V](site-recovery-support-matrix-to-sec-site.md#support-for-replicated-machine-os-versions).
3. Compruebe los requisitos de [red](site-recovery-support-matrix-to-sec-site.md#network-configuration) y [almacenamiento](site-recovery-support-matrix-to-sec-site.md#storage).
4. Asegúrese de que los hosts de Hyper-V están conectados a Internet y tienen acceso a estas direcciones URL.
    
    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]
    
    - Si tiene reglas de firewall basadas en direcciones IP, asegúrese de que permitan la comunicación con Azure.
    - Permita los [intervalos IP del centro de datos de Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) y el puerto HTTPS (443).
    - Permita los intervalos de direcciones IP correspondientes a la región de Azure de su suscripción y del oeste de EE. UU. (se usan para Access Control y para Identity Management).

## <a name="prepare-for-single-server-deployment"></a>Preparación para la implementación de un solo servidor


Si solo tiene un servidor VMM, puede replicar las máquinas virtuales de hosts de Hyper-V de la nube VMM en [Azure](hyper-v-site-walkthrough-overview.md) o en una nube VMM secundaria, como se describe en este documento. Se recomienda la primera opción porque la replicación entre nubes no es perfecta.

Si desea replicar entre nubes, puede replicar con un único servidor VMM independiente o con un solo servidor VMM implementado en un clúster de Windows extendido.

### <a name="replicate-with-a-standalone-vmm-server"></a>Replicar con un servidor VMM independiente

En este escenario, se implementa un solo servidor VMM como máquina virtual en el sitio principal y se replica esta última en uno secundario mediante Site Recovery y Réplica de Hyper-V.

1. **Configurar VMM en una máquina virtual Hyper-V**. Se recomienda colocar la instancia de SQL Server que usa VMM en la misma máquina virtual. Esto permite ahorrar tiempo, ya que solo se tiene que crear una máquina virtual. Si quiere usar una instancia remota de SQL Server y se produce una interrupción, necesita recuperar esa instancia para poder recuperar VMM.
2. **Asegúrese de que el servidor VMM tenga al menos dos nubes configuradas**. Una nube contendrá las máquinas virtuales que desee replicar y la otra nube actuará como la ubicación secundaria. La nube que contenga las máquinas virtuales que quiere proteger debe cumplir una serie de [requisitos previos](#prerequisites).
3. Configure Site Recovery tal como se describe en este artículo. Cree y registre el servidor VMM en un almacén, configure una directiva de replicación y habilite la replicación. Los nombres de VMM de origen y de destino serán iguales. Especifique que la replicación inicial tendrá lugar a través de la red.
4. Al configurar la asignación de red, se asigna la red de VM para la nube principal a la red de VM para la nube secundaria.
5. En la consola de administrador de Hyper-V, habilite la réplica de Hyper-V en el host de Hyper-V que contiene la máquina virtual de VMM y habilite la replicación en la máquina virtual. Asegúrese de no agregar la máquina virtual VMM a las nubes que están protegidas por Site Recovery para garantizar que la configuración de la réplica de Hyper-V no la invalide Site Recovery.
6. Si crea planes de recuperación para la conmutación por error, se usa el mismo servidor VMM de origen y de destino.
7. En una interrupción completa, se conmuta por error y se recupera de la siguiente forma:

   1. Ejecute una conmutación por error no planeada en la consola de administrador de Hyper-V del sitio secundario para conmutar la máquina virtual VMM principal al sitio secundario.
   2. Compruebe que la máquina virtual VMM esté en funcionamiento y, en el almacén, ejecute una conmutación por error no planeada para conmutar por error las máquinas virtuales de las nubes principales a las secundarias. Confirme la conmutación por error y seleccione un punto de recuperación alternativo, si es necesario.
   3. Una vez completada la conmutación por error no planeada, se podrá acceder a todos los recursos de nuevo desde el sitio primario.
   4. Cuando el sitio principal está disponible de nuevo, en la consola de administrador de Hyper-V en el sitio secundario, habilite la replicación inversa para la máquina virtual VMM. Esto inicia la replicación de la máquina virtual del sitio secundario al principal.
   5. Ejecute una conmutación por error planeada en la consola de administrador de Hyper-V del sitio secundario para conmutar la máquina virtual VMM al sitio principal. Confirme la conmutación por error. Después, habilite la replicación inversa para que la máquina virtual VMM se vuelva a replicar del sitio principal al secundario.
   6. En el almacén de Servicios de recuperación, habilite la replicación inversa para las máquinas virtuales de carga de trabajo e iniciar la replicación del sitio secundario al principal.
   7. En el almacén de Servicios de recuperación, ejecute una conmutación por error planeada para conmutar las máquinas virtuales de carga de trabajo al sitio principal. Confirme la conmutación por error para finalizarla. A continuación, habilite la replicación inversa para iniciar la replicación de las máquinas virtuales de carga de trabajo del sitio principal al secundario.

### <a name="replicate-with-a-stretched-vmm-cluster"></a>Replicar con un clúster de VMM estirado

En lugar de implementar un servidor VMM independiente como máquina virtual que se replica en un sitio secundario, puede hacer que VMM esté altamente disponible mediante su implementación como máquina virtual en un clúster de conmutación por error de Windows. De esta forma, se garantiza la resistencia de las cargas de trabajo y la protección frente a los errores del hardware. Para efectuar una implementación con Site Recovery, la VM de VMM debe implementarse en un clúster extendido en sitios separados geográficamente. Para ello, siga estos pasos:

1. Instale VMM en una máquina virtual en un clúster de conmutación por error de Windows y seleccione la opción de ejecución del servidor de alta disponibilidad durante la instalación.
2. La instancia de SQL Server usada por VMM se debe replicar con grupos de disponibilidad AlwaysOn de SQL Server para que haya una réplica de la base de datos en el sitio secundario.
3. Siga las instrucciones de este artículo para crear un almacén, registrar el servidor y configurar la protección. Debe registrar cada servidor VMM del clúster en el almacén de Servicios de recuperación. Para ello, instale el proveedor en un nodo activo y registre el servidor VMM. Después, instale el proveedor en los demás nodos.
4. Si se produce alguna interrupción, el servidor VMM y su base de datos de SQL Server correspondiente se conmutan por error y se accede a ellos desde el sitio secundario.



## <a name="next-steps"></a>Pasos siguientes

Vaya a [Paso 5: Configuración de un almacén](vmm-to-vmm-walkthrough-create-vault.md).