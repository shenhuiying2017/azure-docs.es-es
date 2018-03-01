---
title: "Conmutación por error y por recuperación de máquinas virtuales de Hyper-V replicadas a un centro de datos secundario con Site Recovery | Microsoft Docs"
description: "Aprenda a conmutar por error máquinas virtuales de Hyper-V a su sitio local secundario y a conmutar por recuperación al sitio primario, con Azure Site Recovery."
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 02/12/2018
ms.author: raynew
ms.openlocfilehash: 3740ec9917499f6a1e87905befe86598a18f68e6
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/21/2018
---
# <a name="fail-over-and-fail-back-hyper-v-vms-replicated-to-your-secondary-on-premises-site"></a>Conmutación por error y por recuperación de máquinas virtuales de Hyper-V replicadas a su sitio local secundario

El servicio [Azure Site Recovery](site-recovery-overview.md) administra y orquesta la replicación, la conmutación por error y la conmutación por recuperación de máquinas locales y máquinas virtuales (VM) de Azure.

En este artículo se describe cómo conmutar por error una máquina virtual administrada de Hyper-V en una nube de System Center Virtual Machine Manager (VMM) a un sitio secundario de VMM. Después de que ha conmutado por error, conmutará por recuperación a su sitio local cuando esté disponible. En este artículo, aprenderá a:

> [!div class="checklist"]
> * Conmutar por error de una máquina virtual de Hyper-V desde una nube de VMM principal a una nube de VMM secundaria
> * Volver a proteger del sitio secundario al primario, y conmutar por recuperación
> * Opcionalmente, comenzar a replicar de nuevo desde el sitio primario al secundario.

## <a name="failover-and-failback"></a>Conmutación por error y conmutación por recuperación

La conmutación por error y por recuperación consta de tres etapas:

1. **Conmutación por error al sitio secundario**: las máquinas conmutan por error del sitio primario al secundario.
2. **Conmutación por recuperación desde el sitio secundario**: las máquinas virtuales se replican desde el sitio secundario al primario y ejecutan una conmutación por error planeada a la conmutación por recuperación.
3. Después de la conmutación por error planeada, tiene la opción de comenzar a replicar desde el sitio primario al sitio secundario.


## <a name="prerequisites"></a>requisitos previos

- Asegúrese de que ha completado una [exploración en profundidad de recuperación ante desastres](hyper-v-vmm-test-failover.md) para comprobar que todo funciona según lo previsto.
- Para realizar la conmutación por recuperación, asegúrese de que los servidores VMM principal y secundario están conectados a Site Recovery.



## <a name="run-a-failover-from-primary-to-secondary"></a>Ejecución de una conmutación por error de primario a secundario

Puede ejecutar una conmutación por error planeada o normal para las máquinas virtuales de Hyper-V.

- Use una conmutación por error normal para interrupciones inesperadas. Al ejecutar esta conmutación por error, Site Recovery crea una máquina virtual en el sitio secundario y lo enciende. La conmutación por error se ejecuta contra un punto de recuperación específico. Dependiendo del punto de recuperación que use, puede producirse pérdida de datos.
- Puede usar una conmutación por error planeada para el mantenimiento o durante una interrupción esperada. Esta opción no proporciona pérdida de datos. Cuando se desencadena una conmutación por error planeada, las máquinas virtuales de origen se apagan. Los datos no sincronizados se sincronizan y se desencadena la conmutación por error. 
- 
En este procedimiento se describe cómo se ejecuta una conmutación por error normal.


1. En **Configuración** > **Elementos replicados**, haga clic en VM > **Conmutación por error**.
2. En **Conmutación por error**, seleccione un **punto de recuperación** en el que realizar la conmutación por error. Puede seleccionar una de las siguientes opciones:
    - **Último** (valor predeterminado): esta opción procesa primero todos los datos enviados a Site Recovery. Ofrece el objetivo de punto de recuperación (RPO) mínimo, ya que la máquina virtual de réplica creada después de la conmutación por error tiene todos los datos replicados en Site Recovery al desencadenarse la conmutación por error.
    - **Procesado más recientemente**: con esta opción se realiza una conmutación por error de la máquina virtual al último punto de recuperación procesado por Site Recovery. Esta opción proporciona un objetivo de tiempo de recuperación (RTO) bajo, ya que no se invierte tiempo en el procesamiento de datos sin procesar.
    - **Más reciente coherente con la aplicación**: esta opción conmuta por error la máquina virtual al punto de recuperación más reciente coherente con la aplicación que haya procesado Site Recovery. 
3. La clave de cifrado no es importante en este escenario.
4. Seleccione **Apague la máquina antes de comenzar con la conmutación por error** si desea que Site Recovery intente apagar las máquinas virtuales de origen antes de desencadenar la conmutación por error. Antes de desencadenar la conmutación por error, Site Recovery también intentará sincronizar los datos locales que aún no se han enviado al sitio secundario. Tenga en cuenta que la conmutación por error continúa aunque se produzca un error en el apagado. Puede seguir el progreso de la conmutación por error en la página **Trabajos**.
5. Ahora debería poder ver la máquina virtual en la nube de VMM secundaria.
6. Después de comprobar la máquina virtual, **confirme** la conmutación por error. Esta acción elimina todos los puntos de recuperación disponibles.

> [!WARNING]
> **No cancele una conmutación por error en curso**: antes de iniciar la conmutación por error, se detiene la replicación de la máquina virtual. Si se cancela una conmutación por error en curso, la conmutación por error se detiene, pero no se replica la máquina virtual de nuevo.  


## <a name="reprotect-and-fail-back"></a>Volver a proteger y conmutar por recuperación

Empiece a replicar desde el sitio secundario a la réplica principal y conmuten por recuperación al sitio primario. Después de que las máquinas virtuales se ejecutan de nuevo en el sitio primario, puede volver a reproducirlas en el sitio secundario.  

1. En **Configuración** > **Elementos replicados**, haga clic en la máquina virtual y habilite **Replicación inversa**. La máquina virtual comienza a replicarse de nuevo en el sitio primario.
2. Haga clic en la máquina virtual > **Conmutación por error planeada**.
3. En **Confirmar conmutación por error planeada**, compruebe la dirección de conmutación por error (desde la nube de VMM secundaria) y seleccione las ubicaciones de origen y de destino. 
4. En **Sincronización de datos**, especifique cómo desea sincronizar:
    - **Sincronizar datos antes de la conmutación por error (sincronizar solo cambios incrementales)**: esta opción reduce al mínimo el tiempo de inactividad de las máquinas virtuales, ya que realiza la sincronización sin apagarlas. Así es cómo funciona:
        - Toma una instantánea de la máquina virtual de réplica y la copia en el host de Hyper-V principal. La máquina virtual de réplica se sigue ejecutando.
        - Apaga la máquina virtual de réplica para ahí no se produzca ningún cambio nuevo. El conjunto final de los cambios incrementales se transfiere al sitio primario, y se inicia en él la máquina virtual.
    - **Sincronizar datos solo durante la conmutación por error (descarga completa)**: utilice esta opción si lleva mucho tiempo trabajando en el sitio secundario. Esta opción es más rápida, porque se esperan varios cambios de disco y no se invierte tiempo en cálculos de suma de comprobación. Esta opción realiza una descarga de disco. También es útil cuando se ha eliminado la máquina virtual principal.
5. La clave de cifrado no es importante en este escenario.
6. Inicie la conmutación por error. Puede seguir el progreso de la conmutación por error en la pestaña **Trabajos** .
7. Si ha decidido sincronizar los datos antes de la conmutación por error, una vez que se haya realizado la sincronización inicial de los datos y esté preparado para apagar la máquina virtual de réplica del sitio secundario, haga clic en **Trabajos** > nombre del trabajo de conmutación por error planeada > **Completar conmutación por error**. Esta acción apaga la máquina virtual secundaria, transfiere los cambios más recientes al sitio primario e inicia la máquina virtual principal.
8. En la nube de VMM principal, compruebe que la máquina virtual está disponible.
9. La máquina virtual principal está ahora en un estado de confirmación pendiente. Haga clic en **Confirmar** para confirmar la conmutación por error.
10. Si quiere volver a replicar la máquina virtual principal al sitio secundario, habilite **Replicación inversa**.


> [!NOTE]
> La replicación inversa solo replica los cambios que se han producido desde que se apagó la máquina virtual de réplica, y solo se envían los cambios incrementales.

## <a name="next-steps"></a>pasos siguientes
[Revise el paso](hyper-v-vmm-disaster-recovery.md) para la replicación de máquinas virtuales de Hyper-V a un sitio secundario.
