---
title: "Requisitos previos para replicación de servidor físico en Azure con Azure Site Recovery | Microsoft Docs"
description: "Resume los pasos necesarios para configurar un almacén para la replicación de servidores físicos en Azure con Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: d99f2605-f417-4995-be77-5323136b814f
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.openlocfilehash: deb5ad0495edc969b374795eeb2698326dd4ff4d
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="step-6-set-up-a-vault-for-physical-server-replication-to-azure"></a>Paso 6: Configuración de un almacén para la replicación de servidor físico en Azure


En este artículo se describe cómo configurar un almacén. Cree un almacén y especifique qué se desea replicar desde la ubicación local en Azure mediante el servicio [Azure Site Recovery](site-recovery-overview.md) en Azure Portal.


Publique cualquier comentario o pregunta en la parte inferior de este artículo, o bien en el [foro de Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).




## <a name="create-a-recovery-services-vault"></a>Creación de un almacén de Servicios de recuperación

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

## <a name="select-a-protection-goal"></a>Selección de un objetivo de protección

Seleccione aquello que desea replicar y la ubicación donde se va a realizar la replicación.

1. Haga clic en **Almacenes de Recovery Services** > almacén.
2. En el menú de recursos, haga clic en **Site Recovery** > **Preparar la infraestructura** > **Objetivo de protección**.
3. En **Objetivo de protección**, seleccione **En Azure** > **No virtualizado/Otro**.


## <a name="next-steps"></a>Pasos siguientes

Vaya a [Paso 7: Configuración de los valores de origen y destino](physical-walkthrough-source-target.md)
