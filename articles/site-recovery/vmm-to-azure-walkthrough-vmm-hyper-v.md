---
title: "Preparar System Center VMM para la replicación de Hyper-V en Azure | Microsoft Docs"
description: "Describe cómo preparar el servidor de System Center VMM para la replicación de Hyper-V en Azure mediante Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: afcd81ae-d192-4013-a0af-3dac45b3c7e9
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/23/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: 74b75232b4b1c14dbb81151cdab5856a1e4da28c
ms.openlocfilehash: ec118ed837dbf140083b3ae1e4ecd41c81562018
ms.contentlocale: es-es
ms.lasthandoff: 07/26/2017

---

# <a name="step-6-prepare-vmm-servers-and-hyper-v-hosts-for-hyper-v-replication-to-azure"></a>Paso 6: Preparación de servidores VMM y de hosts de Hyper-V para la replicación de Hyper-V en Azure

Después de configurar los [componentes de Azure](vmm-to-azure-walkthrough-prepare-azure.md) para la implementación, siga las instrucciones de este artículo para preparar los servidores VMM locales y los hosts de Hyper-V para interactuar con Azure Site Recovery.

Cuando haya terminado de leer este artículo, publique cualquier comentario o pregunta que tenga en la parte inferior de este artículo, o bien en el [foro de Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="prepare-vmm-servers"></a>Preparar los servidores VMM

- Necesita al menos un servidor VMM que cumpla los requisitos de compatibilidad para la replicación de Site Recovery (site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers).
- Asegúrese de haber preparado el servidor VMM para la [asignación de red](vmm-to-azure-walkthrough-network.md#network-mapping-for-replication-to-azure).
- Asegúrese de que el servidor VMM puede obtener acceso a estas direcciones URL:

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]
    
- Si tiene reglas de firewall basadas en direcciones IP, asegúrese de que permitan la comunicación con Azure.
- Permita los [intervalos IP del centro de datos de Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) y el puerto HTTPS (443).
- Permita los intervalos de direcciones IP correspondientes a la región de Azure de su suscripción y del oeste de EE. UU. (se usan para Access Control y para Identity Management).

Durante la implementación de Site Recovery debe descargar el proveedor de Site Recovery e instalarlo en todos los servidores VMM. El servidor VMM se registra en el almacén de Recovery Services.




## <a name="next-steps"></a>Pasos siguientes

Vaya al [paso 7: Creación de un almacén](vmm-to-azure-walkthrough-create-vault.md)


