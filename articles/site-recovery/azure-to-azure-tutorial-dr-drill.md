---
title: "Ejecución de una exploración de recuperación ante desastres en máquinas virtuales de Azure en una región secundaria de Azure con Azure Site Recovery (versión preliminar)"
description: "Aprenda a ejecutar una exploración de recuperación ante desastres en máquinas virtuales de Azure en una región de Azure secundaria mediante el servicio Azure Site Recovery."
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 5bcd3d64714951508d984c17326e845ae4842670
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/01/2017
---
# <a name="run-a-disaster-recovery-drill-for-azure-vms-to-a-secondary-azure-region-preview"></a>Ejecución de una exploración de recuperación ante desastres en máquinas virtuales de Azure en una región secundaria de Azure (versión preliminar)

El servicio [Azure Site Recovery](site-recovery-overview.md) contribuye a la estrategia de recuperación ante desastres y continuidad empresarial (BCDR) al mantener sus aplicaciones empresariales al día y disponibles durante interrupciones planeadas y no planeadas. Azure Site Recovery administra y coordina la recuperación ante desastres de máquinas locales y máquinas virtuales de Azure, lo que incluye la replicación, la conmutación por error y la recuperación.

En este tutorial se muestra cómo ejecutar una exploración de recuperación ante desastres en una máquina virtual de Azure, desde una región de Azure a otra, con una conmutación por error de prueba. Mediante una exploración se valida su estrategia de replicación sin ocasionar ninguna pérdida de datos ni tiempo de inactividad, y sin afectar al entorno de producción. En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Comprobar los requisitos previos
> * Ejecutar una conmutación por error de prueba en una sola máquina virtual

## <a name="prerequisites"></a>Requisitos previos

- Antes de ejecutar una conmutación por error de prueba, se recomienda que compruebe las propiedades de la máquina virtual para asegurarse de que todo se ajusta a lo esperado.  Acceda a las propiedades de la máquina virtual en **Elementos replicados**. En la hoja **Información esencial** se detalla la configuración y el estado de las máquinas.
- Se recomienda usar una red de máquina virtual de Azure independiente en la conmutación por error de prueba en lugar de la red predeterminada que se configuró cuando habilitó la replicación.


## <a name="run-a-test-failover"></a>Ejecución de una conmutación por error de prueba

1. En **Configuración** > **Elementos replicados**, haga clic en la VM > icono **+Probar conmutación por error**.

2. En **Conmutación por error**, seleccione el punto de recuperación que usar para la conmutación por error:

   - **Procesado más recientemente**: error de la VM en el último punto de recuperación procesado por el servicio Site Recovery. Se muestra la marca de tiempo. Con esta opción, no se emplea tiempo en el procesamiento de datos, por lo que se proporciona un objetivo de tiempo de recuperación (RTO) bajo.
   - **Más reciente coherente con la aplicación**: esta opción conmuta por error todas las VM en el punto de recuperación más reciente coherente con la aplicación. Se muestra la marca de tiempo.
   - **Personalizado**: seleccione un punto de recuperación.

3. Seleccione la red virtual de Azure de destino a la se conectarán las VM de Azure en la región secundaria después de que se produzca la conmutación por error.

4. Para iniciar la conmutación por error, haga clic en **Aceptar**. Para realizar el seguimiento del progreso, haga clic en la máquina virtual para abrir sus propiedades. También puede hacer clic en el trabajo **Conmutación por error de prueba** en el nombre del almacén > **Configuración** > **Trabajos** > **Trabajos de Site Recovery**.
5. Una vez finalizada la conmutación por error, la VM de Azure de réplica aparece en Azure Portal > **Virtual Machines**. Asegúrese de que la máquina virtual está en funcionamiento, tiene el tamaño adecuado y está conectada a la red apropiada.
6. Para eliminar las máquinas virtuales que se crearon durante la conmutación por error de prueba, haga clic en **Cleanup test failover** (Limpiar conmutación por error de prueba) en el artículo replicado o el plan de recuperación. En **Notas**, registre y guarde las observaciones asociadas a la conmutación por error de prueba.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Ejecutar una conmutación por error de producción](azure-to-azure-tutorial-failover-failback.md)
