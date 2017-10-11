---
title: "Configurar un almacén para la replicación de Hyper-V (sin System Center VMM) en Azure con Azure Site Recovery | Microsoft Docs"
description: "Resume los pasos necesarios para configurar un almacén para la replicación de Hyper-V en Azure con Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: a936b3e2-0dbe-47ac-a98e-5285d96dc786
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/22/2017
ms.author: raynew
ms.openlocfilehash: 8212ff011633c3a89d3310e828b6d5f1cda6ce3f
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="step-7-set-up-a-vault-for-hyper-v-replication"></a>Paso 7: configuración de un almacén para la replicación de Hyper-V

Este artículo describe cómo configurar un almacén y especificar qué se desea replicar desde la ubicación local en Azure mediante el servicio [Azure Site Recovery](site-recovery-overview.md) en Azure Portal.


Publique cualquier comentario o pregunta en la parte inferior de este artículo, o bien en el [foro de Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="create-a-recovery-services-vault"></a>Creación de un almacén de Servicios de recuperación

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]



## <a name="select-a-protection-goal"></a>Selección de un objetivo de protección

Seleccione aquello que desea replicar y la ubicación donde se va a realizar la replicación.

1. Haga clic en **Almacenes de Recovery Services** > almacén.
2. En el menú de recursos, haga clic en **Site Recovery** > **Preparar la infraestructura** > **Objetivo de protección**.
3. En **Objetivo de protección**, seleccione **En Azure** > **Sí, con Hyper-V**. Seleccione **No** para confirmar que no usa VMM. 

    ![Elegir objetivos](./media/hyper-v-site-walkthrough-create-vault/choose-goals2.png)



## <a name="next-steps"></a>Pasos siguientes

Vaya al [paso 8: configuración de los valores de origen y destino](hyper-v-site-walkthrough-source-target.md)
