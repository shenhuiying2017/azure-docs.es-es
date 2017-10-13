---
title: "Ejecución de una exploración de recuperación ante desastres en el sitio local secundario con Azure Site Recovery | Microsoft Docs"
description: "Aprenda a ejecutar una exploración de recuperación ante desastres en el sitio local secundario con Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 445878e2-6682-49ba-914d-4c6824ab08a6
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/18/2017
ms.author: raynew
ms.openlocfilehash: 8737c2a22bd729cfc15d5448e1ec0becef643fd5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="run-a-disaster-recovery-drill-for-hyper-v-vms-to-your-secondary-on-premises-site"></a>Ejecución de una exploración de recuperación ante desastres para máquinas virtuales de Hyper-V en el sitio local secundario

El servicio [Azure Site Recovery](site-recovery-overview.md) contribuye a su estrategia de recuperación ante desastres mediante la administración y la coordinación de la replicación, la conmutación por error y la conmutación por recuperación de máquinas locales y máquinas virtuales (VM) de Azure.

En este tutorial se muestra cómo ejecutar una exploración de recuperación ante desastres para VM de Hyper-V en el sitio local secundario. Las VM de Hyper-V deben administrarse en una nube privada de System Center Virtual Machine Manager (VMM). Mediante una exploración se valida su estrategia de replicación sin ocasionar ninguna pérdida de datos ni tiempo de inactividad, y sin afectar al entorno de producción. En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Preparar los requisitos previos para la conmutación por error de prueba
> * Ejecutar una conmutación por error de prueba para una sola máquina


## <a name="prerequisites"></a>Requisitos previos

- Puede ejecutar una conmutación por error de prueba con un par de opciones para las redes en el sitio secundario. Puede ejecutar la conmutación por error sin una red, con una red existente o bien permitir que Site Recovery cree una red de prueba automáticamente. 
**Si quiere usar una red de producción existente para la conmutación por error de prueba:**
    - La máquina virtual principal debe estar apagada cuando realice la conmutación por error de prueba. Si no lo está, se ejecutarán al mismo tiempo dos VM con la misma identidad en la misma red. 
    - Si realiza cambios en las VM de prueba, estos se perderán al borrar la conmutación por error de prueba. Estos cambios no se replican en la VM principal.
    - Las pruebas en una red de producción conllevan un tiempo de inactividad para las cargas de trabajo de producción. Pida a los usuarios que no usen aplicaciones relacionadas mientras se realiza la exploración de recuperación ante desastres. 
- La red de prueba de réplica no tiene que coincidir con el tipo de red lógica de VMM usado para la conmutación por error de prueba. En cambio, algunas combinaciones no funcionan. Por ejemplo, si la réplica usa DHCP y el aislamiento basado en VLAN, no se puede usar la virtualización de red de Windows para la red de conmutación por error de prueba, porque necesita grupos de direcciones IP. 
- Para la conmutación por error, le recomendamos que no use la red que seleccionó para la asignación de red.


## <a name="run-a-test-failover-for-a-vm"></a>Ejecutar una prueba de conmutación por error para una máquina virtual

1. En **Elementos replicados**, haga clic en la máquina virtual > **Conmutación por error de prueba**.
2. En **Conmutación por error de prueba**, especifique cómo se conectarán las máquinas virtuales de prueba a redes después de la conmutación por error de prueba. Para este tutorial, se recomienda dejar que Site Recovery cree automáticamente una red de prueba. [Más información](site-recovery-test-failover-vmm-to-vmm.md#network-options-in-site-recovery).
3. Haga clic en **Aceptar** para iniciar la conmutación por error. Realizar el seguimiento del progreso en la pestaña **Trabajos** .
4. Cuando haya terminado la conmutación por error, compruebe que las máquinas virtuales de prueba se inician correctamente.
5. Cuando termine, haga clic en **Limpiar conmutación por error de prueba**. De esta forma se eliminan las VM de prueba, así como cualquier red creada durante la conmutación por error de prueba.
6. En **Notas**, registre y guarde las observaciones asociadas a la conmutación por error de prueba. 


## <a name="next-steps"></a>Pasos siguientes

[Ejecutar una conmutación por error de producción](tutorial-vmm-to-vmm-failover-failback.md)






