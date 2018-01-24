---
title: "Ejecutar una exploración en profundidad de una recuperación ante desastres de máquinas locales en Azure con Azure Site Recovery | Microsoft Docs"
description: "Obtener información sobre la ejecución de la exploración en profundidad de una recuperación ante desastres desde máquinas locales en Azure con Azure Site Recovery"
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 12/31/2017
ms.author: raynew
ms.openlocfilehash: f7dc5e2df95a64685a8b70d25e839c371d4fc2de
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/02/2018
---
# <a name="run-a-disaster-recovery-drill-to-azure"></a>Ejecución de un simulacro de recuperación ante desastres
 en Azure

En este tutorial se muestra cómo ejecutar una exploración en profundidad de una recuperación ante desastres para una máquina local en Azure, mediante una conmutación por error de prueba. Una exploración en profundidad valida su estrategia de replicación sin pérdida de datos. En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Configurar una red aislada para la conmutación por error de prueba
> * Preparación para la conexión a la máquina virtual de Azure después de una conmutación por error
> * Ejecutar una conmutación por error de prueba para una sola máquina

Este es el cuarto tutorial de una serie. En este tutorial se da por hecho que ya ha realizado las tareas de los tutoriales anteriores.

1. [Preparación de Azure](tutorial-prepare-azure.md)
2. [Preparación de instancias locales de VMware](tutorial-prepare-on-premises-vmware.md)
3. [Configuración de la recuperación ante desastres](tutorial-vmware-to-azure.md)

## <a name="verify-vm-properties"></a>Verificación de las propiedades de la máquina virtual

Antes de ejecutar una conmutación por error de prueba, compruebe las propiedades de la máquina virtual y asegúrese de que la máquina virtual cumpla con los [requisitos de Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).

1. En **Elementos protegidos**, haga clic en **Elementos replicados** > VM.
2. En el panel **Elemento replicado**, puede ver un resumen de la información de la máquina virtual, el estado de mantenimiento y los puntos de recuperación disponibles más recientes. Haga clic en **Propiedades** para ver más detalles.
3. En **Proceso y red**, puede modificar el nombre de Azure, el grupo de recursos, el tamaño de destino, el [conjunto de disponibilidad](../virtual-machines/windows/tutorial-availability-sets.md) y la configuración de discos administrados.
   
      >[!NOTE]
      La conmutación por recuperación en máquinas Hyper-V locales desde máquinas virtuales de Azure con discos administrados no se admite actualmente. Solo debe usar la opción de discos administrados para la conmutación por error si planea migrar máquinas virtuales locales a Azure, sin realizar una conmutación por recuperación.
   
4. Puede ver y modificar la configuración de red, incluida la red o subred en la que se va a ubicar la máquina virtual de Azure después de la conmutación por error y la dirección IP que se le va a asignar.
5. En **Discos** puede ver información sobre los discos de datos y el sistema operativo de la máquina virtual.

## <a name="run-a-test-failover-for-a-single-vm"></a>Ejecutar una conmutación por error de prueba en una sola máquina virtual

Cuando se ejecuta una conmutación por error de prueba, ocurre lo siguiente:

1. Se ejecuta una comprobación de requisitos previos para garantizar que se dan todas las condiciones necesarias para la conmutación por error.
2. La conmutación por error procesa los datos, de modo que se pueda crear una máquina virtual de Azure. Si se selecciona el último punto de recuperación, se crea un punto de recuperación a partir de los datos.
3. Se crea una VM de Azure mediante los datos procesados en el paso anterior.

Ejecute la conmutación por error de prueba de la manera siguiente:

1. En **Configuración** > **Elementos replicados**, haga clic en la VM > **+Probar conmutación por error**.
2. Seleccione el punto de recuperación **Procesado más recientemente** para este tutorial. Se realiza una conmutación por error de la máquina virtual al último punto en el tiempo disponible. Se muestra la marca de tiempo. Con esta opción, no se empleó tiempo en el procesamiento de datos, por lo que se proporciona un objetivo de tiempo de recuperación (RTO) bajo.
3. En **Probar conmutación por error**, seleccione la red de Azure de destino a la que se conectarán las máquinas virtuales de Azure después de la conmutación por error.
4. Haga clic en **Aceptar** para iniciar la conmutación por error. Para realizar el seguimiento del progreso, haga clic en la máquina virtual para abrir sus propiedades. También puede hacer clic en el trabajo **Conmutación por error de prueba** en el nombre del almacén > **Configuración** > **Trabajos** >
   **Trabajos de Site Recovery**.
5. Una vez finalizada la conmutación por error, la VM de Azure de réplica aparece en Azure Portal > **Virtual Machines**. Compruebe que la VM tiene el tamaño adecuado, que está conectada a la red correspondiente y que se está ejecutando.
6. Ahora debería poder conectarse a la VM replicada en Azure.
7. Para eliminar máquinas virtuales de Azure que se crearon durante la conmutación por error de prueba, haga clic en **Limpieza de conmutación por error de prueba** en la máquina virtual. En **Notas**, registre y guarde las observaciones asociadas a la conmutación por error de prueba.

En algunos escenarios, la conmutación por error requiere un procesamiento adicional que tarda aproximadamente de ocho a diez minutos en completarse. Puede observar tiempos de conmutación por error de prueba más largos en las máquinas de VMware Linux, las máquinas virtuales de VMware que no tienen el servicio DHCP habilitado y las máquinas virtuales de VMware que no tienen los siguientes controladores de arranque: storvsc, vmbus, storflt, intelide, atapi.

## <a name="next-steps"></a>pasos siguientes

> [!div class="nextstepaction"]
> [Ejecución de una conmutación por error y una conmutación por recuperación entre máquinas de VMware locales](tutorial-vmware-to-azure-failover-failback.md).
