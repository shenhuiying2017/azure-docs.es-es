---
title: "Conmutación por recuperación en Azure Site Recovery para las máquinas virtuales de Hyper-V | Microsoft Docs"
description: "Azure Site Recovery coordina la replicación, la conmutación por error y la recuperación de máquinas virtuales y servidores físicos. Información acerca de la conmutación por recuperación de Azure a un centro de datos local."
services: site-recovery
documentationcenter: 
author: ruturaj
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 08/11/2017
ms.author: ruturajd
ms.openlocfilehash: 7f478a61ee448d2d18b3ac7bc0a579b6e341c30d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="failback-in-site-recovery-for-hyper-v-virtual-machines"></a>Conmutación por recuperación en Site Recovery para máquinas virtuales de Hyper-V

En este artículo se describe cómo se realiza la conmutación por recuperación en máquinas virtuales protegidas con Site Recovery.

## <a name="prerequisites"></a>Requisitos previos
1. Asegúrese de que el servidor del sitio principal de VMM/Hyper-V está conectado.
2. Debe haber realizado la acción **Confirmar** en la máquina virtual.

## <a name="why-is-there-no-button-called-failback"></a>¿Por qué no hay ningún botón llamado conmutación por recuperación?
En el portal, no hay ninguna acción explícita que se denomine conmutación por recuperación. Se trata de un paso para volver al sitio principal. Por definición, la conmutación por error se realiza en las máquinas virtuales desde el sitio principal (local) para la recuperación (en Azure) y en la conmutación por recuperación, la recuperación de las máquinas virtuales se realiza en el sitio principal.

Al iniciar una conmutación por error, la hoja le informa sobre la dirección del trabajo. Si la dirección es de Azure a local, es una conmutación por recuperación.

## <a name="why-is-there-only-a-planned-failover-gesture-to-failback"></a>¿Por qué hay solo una acción de conmutación por error planeada para la conmutación por recuperación?
Azure es un entorno de alta disponibilidad y las máquinas virtuales estarán siempre disponibles. La conmutación por recuperación es una actividad planeada que necesita un tiempo breve de inactividad para que las cargas de trabajo empiecen a ejecutarse de nuevo de forma local. No se prevé la pérdida de datos. Por lo tanto, hay solo una conmutación por error planeada, que desactivará las máquinas virtuales de Azure, descargará los cambios más recientes y garantizará que no se pierden datos.

## <a name="do-i-need-a-process-server-in-azure-to-failback-to-hyper-v"></a>¿Se necesita un servidor de proceso en Azure para realizar la conmutación por recuperación en Hyper-v?
No, solo se requiere un servidor de proceso cuando está protegiendo máquinas virtuales de VMware. No es necesario implementar componentes adicionales cuando se proteja o realice la conmutación por recuperación de máquinas virtuales de Hyper-v.

## <a name="initiate-failback"></a>Inicio de la conmutación por recuperación
Después de la conmutación por error de la ubicación principal a la secundaria, las máquinas virtuales replicadas no están protegidas por Site Recovery y la ubicación secundaria actúa como ubicación principal. Siga estos procedimientos para realizar la conmutación por recuperación al sitio principal original. En este procedimiento se describe cómo ejecutar una conmutación por error planeada para un plan de recuperación. También puede ejecutar la conmutación por error para una única máquina virtual en la pestaña **Máquinas virtuales** .

1. Seleccione **Recovery Plans** > *nombreDePlanDeRecuperación*. Haga clic en **Conmutación por error** > **Planned Conmutación por error**.
2. En la página **Confirmar conmutación por error planeada**, elija las ubicaciones de origen y de destino. Tenga en cuenta la dirección de la conmutación por error. Si la conmutación por error desde la ubicación principal ha funcionado como se esperaba y todas las máquinas virtuales están en la ubicación secundaria, este dato es solo informativo.
3. Si realiza la conmutación por recuperación desde Azure, seleccione la configuración en **Sincronización de datos**:

   * **Sincronizar datos antes de la conmutación por error (sincronizar solo cambios incrementales)**: esta opción reduce al mínimo el tiempo de inactividad de las máquinas virtuales, ya que realiza la sincronización sin apagarlas. Hace lo siguiente:
     * Fase 1: realiza una instantánea de la máquina virtual en Azure y la copia en el host de Hyper-V local. El equipo continúa ejecutándose en Azure.
     * Fase 2: apaga la máquina virtual en Azure para que no se realice ningún nuevo cambio allí. El último conjunto de cambios incrementales se transfiere al servidor local y se inicia la máquina virtual local.

    - **Sincronizar datos solo durante la conmutación por error (descarga completa)**: utilice esta opción si ha estado trabajando en Azure durante mucho tiempo. Esta opción es más rápida, ya que se prevé que la mayoría del disco ha cambiado y no queremos dedicar tiempo al cálculo de la suma de comprobación. Realiza una descarga del disco. También es útil cuando se ha eliminado la máquina virtual local.

    >[!NOTE]
    >Se recomienda utilizar esta opción si ha estado trabajando con Azure durante un tiempo (un mes o más) o se ha eliminado la máquina virtual local. Esta opción no realiza cálculos de suma de comprobación.


4. Si en la nube está habilitado el cifrado de datos, en **Clave de cifrado** seleccione el certificado que se emitió al habilitarlo durante la instalación del proveedor en el servidor de VMM.
5. Inicie la conmutación por error. Puede seguir el progreso de la conmutación por error en la pestaña **Trabajos** .
6. Si seleccionó la opción de sincronizar los datos antes de la conmutación por error, una vez que la sincronización de datos inicial haya finalizado y esté listo para apagar las máquinas virtuales en Azure, haga clic en **Trabajos**, en el nombre del trabajo de conmutación por error planeado y en **Completar conmutación por error**. La máquina de Azure se apagará, se transferirán los cambios más recientes a la máquina virtual local y esta se iniciará.
7. Ahora puede iniciar sesión en la máquina virtual para confirmar que está disponible como se esperaba.
8. La máquina virtual está en un estado pendiente de confirmación. Haga clic en **Confirmar** para confirmar la conmutación por error.
9. Para completar la conmutación por recuperación, haga clic en **Replicación inversa** con el fin de comenzar a proteger la máquina virtual en el sitio principal.

## <a name="failback-to-an-alternate-location"></a>Conmutación por recuperación a una ubicación alternativa
Si ha implementado la protección entre un [sitio de Hyper-V y Azure](site-recovery-hyper-v-site-to-azure.md) , tiene la capacidad de realizar la conmutación por recuperación de Azure a una ubicación local alternativa. Esto es útil si necesita configurar nuevo hardware local. Así es cómo debe hacerlo.

1. Si configura nuevo hardware, instale Windows Server 2012 R2 y el rol de Hyper-V en el servidor.
2. Cree un conmutador de red virtual con el mismo nombre que tenía en el servidor original.
3. Seleccione **Elementos protegidos** -> **Grupo de protección** -> <ProtectionGroupName> -> <VirtualMachineName> en el que desea realizar la conmutación por recuperación y seleccione **Conmutación por error planeada**.
4. En **Confirmar conmutación por error planeada** select **Crear máquina virtual local si no existe**.
5. En **Nombre de host** , seleccione el nuevo servidor host de Hyper-V en el que desea incluir la máquina virtual.
6. En Sincronización de datos, se recomienda seleccionar la opción **Sincronizar los datos antes de la conmutación por error**. Así se reduce el tiempo de inactividad de las máquinas virtuales, ya que la sincronización se realiza sin apagarlas. Hace lo siguiente:

   * Fase 1: realiza una instantánea de la máquina virtual en Azure y la copia en el host de Hyper-V local. El equipo continúa ejecutándose en Azure.
   * Fase 2: apaga la máquina virtual en Azure para que no se realice ningún nuevo cambio allí. El último conjunto de cambios se transfiere al servidor local y se inicia la máquina virtual local.
7. Haga clic en la marca de verificación para iniciar la conmutación por error (conmutación por recuperación).
8. Después de que finalice la sincronización inicial y esté listo para apagar la máquina virtual en Azure, haga clic en **Trabajos** > <planned failover job> > **Completar conmutación por error**. Esta opción apaga la máquina de Azure, transfiere los últimos cambios a la máquina virtual local y la inicia.
9. Puede iniciar sesión en la máquina virtual local para comprobar que todo funciona según lo esperado. A continuación, haga clic en **Confirmar** para finalizar la conmutación por error.
10. Haga clic en **Replicación inversa** para comenzar a proteger la máquina virtual local.

    > [!NOTE]
    > Si cancela el trabajo de conmutación por recuperación durante el paso de sincronización de datos, la máquina virtual local tendrá un estado dañado. Esto se debe a que la sincronización de datos copia los datos más recientes de los discos de la máquina virtual de Azure a los discos de datos locales y puede que los datos de los discos no sean coherentes hasta que la sincronización se complete. Si se cancela la sincronización de datos, es posible que la máquina virtual local no arranque. Vuelva a desencadenar la conmutación por error para completar la sincronización de datos.

## <a name="time-taken-to-failback"></a>Tiempo que se tarda en realizar la conmutación por recuperación
El tiempo que se tarda en realizar la sincronización de datos y arrancar la máquina virtual depende de varios factores. Para tener una idea del tiempo que se tarda, se explica lo que ocurre durante la sincronización de datos.

La sincronización de datos realiza una instantánea de los discos de la máquina virtual e inicia la comprobación bloque por bloque y calcula su suma de comprobación. Esta suma de comprobación calculada se envía a un entorno local para compararlo con la suma de comprobación local del mismo bloque. En el caso de que las sumas de comprobación coincidan, el bloque de datos no se transfiere. Si no coinciden, el bloque de datos se transfiere al entorno local. Este tiempo de transferencia depende del ancho de banda disponible. La velocidad de la suma de comprobación es de algunos GB por minuto. 

Para acelerar la descarga de datos, puede configurar el agente de MARS para utilizar más subprocesos para equiparar la descarga. Vea [este documento](https://support.microsoft.com/en-us/help/3056159/how-to-manage-on-premises-to-azure-protection-network-bandwidth-usage) sobre cómo cambiar los subprocesos de descarga en el agente.


## <a name="next-steps"></a>Pasos siguientes

Una vez haya completado el trabajo de conmutación por recuperación, realice la acción **Confirmar** en la máquina virtual. La confirmación elimina la máquina virtual de Azure y sus discos, y la prepara para volver a protegerla.

Después de **Confirmar**, puede iniciar la *Replicación inversa*. Esto iniciará la protección de la máquina virtual del entorno local a Azure. Tenga en cuenta que esto solo replicará los cambios, ya que la máquina virtual se ha desactivado en Azure y solo envía los cambios incrementales.
