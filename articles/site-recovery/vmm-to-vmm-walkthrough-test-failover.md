---
title: "Ejecución de una conmutación por error de prueba para la replicación de máquinas virtuales de Hyper-V en un sitio secundario con Azure Site Recovery | Microsoft Docs"
description: "Describe cómo ejecutar una conmutación por error de prueba para la replicación de máquinas virtuales de Hyper-V en un sitio secundario de System Center VMM con Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: a3fd11ce-65a1-4308-8435-45cf954353ef
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2017
ms.author: raynew
ms.openlocfilehash: 23d235d326273e7ec59feee6588a39f685401e52
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="step-10-run-a-test-failover-for-hyper-v-replication-to-a-secondary-site"></a>Paso 10: Ejecución de una conmutación por error de prueba para la replicación en un sitio secundario


Después de habilitar la replicación de máquinas virtuales de Hyper-V con [Azure Site Recovery](site-recovery-overview.md), use este artículo para ejecutar una conmutación por error de prueba. Una conmutación por error de prueba comprueba que la replicación funciona, sin afectar a su entorno de producción. 


Publique cualquier comentario que tenga en la parte inferior de este artículo, o bien en el [foro de Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="before-you-start"></a>Antes de comenzar

- Cuando se desencadena una conmutación por error de prueba, puede especificar la red a la que se conectarán las máquinas virtuales de réplica de prueba. Puede [obtener más información](site-recovery-test-failover-vmm-to-vmm.md#network-options-in-site-recovery) sobre las opciones de red.
- Le recomendamos que no seleccione una red que haya seleccionado durante la asignación de red.
- En las instrucciones de este artículo se describe cómo conmutar por error una única máquina virtual. Obtenga información sobre cómo [crear planes de recuperación](site-recovery-create-recovery-plans.md) si quiere conmutar por error varias máquinas virtuales juntas.
- Obtenga información sobre las [limitaciones para las conmutaciones por error de prueba](site-recovery-test-failover-vmm-to-vmm.md#things-to-note).
- La dirección IP que se asigna a una máquina virtual durante la conmutación por error de prueba es la misma que obtendría la máquina virtual al realizar una conmutación por error planeada o no planeada (siempre que la dirección IP esté disponible en la red de conmutación por error de prueba). Si la dirección IP no está disponible en la red de conmutación por error de prueba, la máquina virtual recibe otra dirección IP que esté disponible en la red de conmutación por error de prueba.
- Si quiere hacer una conmutación por error de prueba en la red de producción, para comprobar completamente la máquina de conectividad de red de un extremo a otro, tenga en cuenta lo siguiente:
    - La máquina virtual principal debe estar apagada cuando realice la conmutación por error de prueba. Si no lo hace, dos máquinas virtuales con la misma identidad se ejecutarán en la misma red al mismo tiempo. 
    - Si realiza cambios en las máquinas virtuales de prueba, estos se perderán al limpiar la conmutación por error de prueba. Estos cambios no se replican en la máquina virtual principal.
    - Las pruebas en una red de producción conllevan un tiempo fuera de servicio para las cargas de trabajo de producción. Pida a los usuarios que no usen la aplicación durante la exploración de la recuperación ante desastres.  


## <a name="run-a-test-failover-for-a-vm"></a>Ejecutar una prueba de conmutación por error para una máquina virtual

1. Para conmutar por error una sola máquina virtual, en **Elementos replicados**, haga clic en la máquina virtual > **Conmutación por error de prueba**.
2. En **Conmutación por error de prueba**, especifique cómo se conectarán las máquinas virtuales de prueba a redes después de la conmutación por error de prueba. 
3. Haga clic en **Aceptar** para iniciar la conmutación por error. Realizar el seguimiento del progreso en la pestaña **Trabajos** .
5. Cuando haya terminado la conmutación por error, compruebe que las máquinas virtuales de prueba se inician correctamente.
6. Cuando haya terminado, haga clic en **Cleanup test failover** (Limpieza de conmutación por error de prueba) en el plan de recuperación.
7. En **Notas**, registre y guarde las observaciones asociadas a la conmutación por error de prueba. En este paso se eliminan las máquinas virtuales y las redes que se crearon durante la conmutación por error de prueba.


## <a name="next-steps"></a>Pasos siguientes

Después de probar la implementación, obtenga más información sobre otros tipos de [conmutación por error](site-recovery-failover.md).
