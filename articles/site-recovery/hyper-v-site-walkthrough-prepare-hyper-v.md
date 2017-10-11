---
title: "Preparar los hosts de Hyper-V (sin System Center VMM) para la replicación en Azure | Microsoft Docs"
description: "Describe cómo preparar los hosts de Hyper-V para la replicación en Azure con Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 0f204e24-8d78-4076-95c5-8137d1be9c01
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/22/2017
ms.author: raynew
ms.openlocfilehash: f9bcaa8e55be6e8fddaf88ebc3f18f5dbb2811e4
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="step-6-prepare-hyper-v-hosts-for-replication-to-azure"></a>Paso 6: preparación de los hosts de Hyper-V para la replicación en Azure

Siga las instrucciones de este artículo para preparar los hosts de Hyper-V local para interactuar con Azure Site Recovery.

Cuando haya terminado de leer este artículo, publique cualquier comentario o pregunta que tenga en la parte inferior de este artículo, o bien en el [foro de Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="prepare-hosts"></a>Preparar los hosts

- Asegúrese de que los hosts de Hyper-V cumplen los [requisitos previos](site-recovery-prereq.md#disaster-recovery-of-hyper-v-vms-to-azure-no-vmm).
- Asegúrese de que los hosts pueden acceder a las direcciones URL necesarias:

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]
    
- Si tiene reglas de firewall basadas en direcciones IP, asegúrese de que permitan la comunicación con Azure.
- Permita los [intervalos IP del centro de datos de Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) y el puerto HTTPS (443).
- Permita los intervalos de direcciones IP correspondientes a la región de Azure de su suscripción y del oeste de EE. UU. (se usan para Access Control y para Identity Management).

Durante la implementación de Site Recovery, se agregan hosts de Hyper-V que contienen las máquinas virtuales que desea replicar en un sitio de Hyper-V. El proveedor de Site Recovery y el agente de Recovery Services se instalan en cada host. Se registra el sitio de Hyper-V en el almacén de Recovery Services.

## <a name="next-steps"></a>Pasos siguientes

Vaya al [paso 7: Creación de un almacén](hyper-v-site-walkthrough-create-vault.md)

