---
title: "Ejecución de una conmutación por recuperación en un sitio principal para máquinas virtuales de Hyper-V | Microsoft Docs"
description: "Azure Site Recovery coordina la replicación, la conmutación por error y la recuperación de máquinas virtuales y servidores físicos. Información acerca de la conmutación por recuperación de Azure a un centro de datos local."
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 02/14/2018
ms.author: rajanaki
ms.openlocfilehash: d344174ffa290b55386918ae19e2f792bb801a8a
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/22/2018
---
# <a name="run-a-failback-for-hyper-v-vms"></a>Ejecución de una conmutación por recuperación para máquinas virtuales de Hyper-V

En este artículo se describe cómo se realiza la conmutación por recuperación de máquinas virtuales de Hyper-V protegidas por Site Recovery.

## <a name="prerequisites"></a>requisitos previos
1. Asegúrese de leer los detalles sobre los [diferentes tipos de conmutación por recuperación](concepts-types-of-failback.md) y las advertencias correspondientes.
1. Asegúrese de que el servidor del sitio principal de VMM o el servidor de host Hyper-V está conectado a Azure.
2. Debe haber realizado la acción **Confirmar** en la máquina virtual.

## <a name="perform-failback"></a>Realización de una conmutación por recuperación
Después de la conmutación por error de la ubicación principal a la secundaria, las máquinas virtuales replicadas no están protegidas por Site Recovery y la ubicación secundaria actúa como ubicación principal. Para que las VM conmuten por recuperación en un plan de recuperación, ejecute una conmutación por error planeada desde el sitio secundario en el sitio primario del modo siguiente. 
1. Seleccione **Recovery Plans** > *nombreDePlanDeRecuperación*. Haga clic en **Conmutación por error** > **Planned Conmutación por error**.
2. En la página **Confirmar conmutación por error planeada**, elija las ubicaciones de origen y de destino. Tenga en cuenta la dirección de la conmutación por error. Si la conmutación por error desde la ubicación principal ha funcionado como se esperaba y todas las máquinas virtuales están en la ubicación secundaria, este dato es solo informativo.
3. Si realiza la conmutación por recuperación desde Azure, seleccione la configuración en **Sincronización de datos**:
    - **Sincronizar datos antes de la conmutación por error (sincronizar solo cambios incrementales)**: esta opción reduce al mínimo el tiempo de inactividad de las máquinas virtuales, ya que realiza la sincronización sin apagarlas. Realiza los pasos siguientes:
        - Fase 1: realiza una instantánea de la máquina virtual en Azure y la copia en el host de Hyper-V local. El equipo continúa ejecutándose en Azure.
        - Fase 2: apaga la máquina virtual en Azure para que no se realice ningún nuevo cambio allí. El último conjunto de cambios incrementales se transfiere al servidor local y se inicia la máquina virtual local.

    - **Sincronizar datos solo durante la conmutación por error (descarga completa)**: esta opción es más rápida.
        - Esta opción es más rápida, ya que se prevé que la mayoría del disco ha cambiado y no queremos dedicar tiempo al cálculo de la suma de comprobación. Realiza una descarga del disco. También es útil cuando se ha eliminado la máquina virtual local.
        - Se recomienda utilizar esta opción si ha estado trabajando con Azure durante un tiempo (un mes o más) o se ha eliminado la máquina virtual local. Esta opción no realiza cálculos de suma de comprobación.


4. Si en la nube está habilitado el cifrado de datos, en **Clave de cifrado** seleccione el certificado que se emitió al habilitarlo durante la instalación del proveedor en el servidor de VMM.
5. Inicie la conmutación por error. Puede seguir el progreso de la conmutación por error en la pestaña **Trabajos** .
6. Si seleccionó la opción de sincronizar los datos antes de la conmutación por error, una vez que la sincronización de datos inicial haya finalizado y esté listo para apagar las máquinas virtuales en Azure, haga clic en **Trabajos**, en el nombre del trabajo de conmutación por error planeado y en **Completar conmutación por error**. La máquina de Azure se apagará, se transferirán los cambios más recientes a la máquina virtual local y esta se iniciará.
7. Ahora puede iniciar sesión en la máquina virtual para confirmar que está disponible como se esperaba.
8. La máquina virtual está en un estado pendiente de confirmación. Haga clic en **Confirmar** para confirmar la conmutación por error.
9. Para completar la conmutación por recuperación, haga clic en **Replicación inversa** con el fin de comenzar a proteger la máquina virtual en el sitio principal.


Siga estos procedimientos para realizar la conmutación por recuperación al sitio principal original. En este procedimiento se describe cómo ejecutar una conmutación por error planeada para un plan de recuperación. También puede ejecutar la conmutación por error para una única máquina virtual en la pestaña **Máquinas virtuales** .


## <a name="failback-to-an-alternate-location-in-hyper-v-environment"></a>Conmutación por recuperación en una ubicación alternativa en el entorno de Hyper-V
Si ha implementado la protección entre un [sitio de Hyper-V y Azure](site-recovery-hyper-v-site-to-azure.md) , tiene la capacidad de realizar la conmutación por recuperación de Azure a una ubicación local alternativa. Esto es útil si necesita configurar nuevo hardware local. Así es cómo debe hacerlo.

1. Si configura nuevo hardware, instale Windows Server 2012 R2 y el rol de Hyper-V en el servidor.
2. Cree un conmutador de red virtual con el mismo nombre que tenía en el servidor original.
3. Seleccione **Elementos protegidos** -> **Grupo de protección** -> <ProtectionGroupName> -> <VirtualMachineName> en el que desea realizar la conmutación por recuperación y seleccione **Conmutación por error planeada**.
4. En **Confirmar conmutación por error planeada** select **Crear máquina virtual local si no existe**.
5. En Nombre de host,** seleccione el nuevo servidor host de Hyper-V en el que desea incluir la máquina virtual.
6. En Sincronización de datos, se recomienda seleccionar la opción **Sincronizar los datos antes de la conmutación por error**. Así se reduce el tiempo de inactividad de las máquinas virtuales, ya que la sincronización se realiza sin apagarlas. Hace lo siguiente:

    - Fase 1: realiza una instantánea de la máquina virtual en Azure y la copia en el host de Hyper-V local. El equipo continúa ejecutándose en Azure.
    - Fase 2: apaga la máquina virtual en Azure para que no se realice ningún nuevo cambio allí. El último conjunto de cambios se transfiere al servidor local y se inicia la máquina virtual local.
    
7. Haga clic en la marca de verificación para iniciar la conmutación por error (conmutación por recuperación).
8. Después de que finalice la sincronización inicial y esté listo para apagar la máquina virtual en Azure, haga clic en **Trabajos** > <planned failover job> > **Completar conmutación por error**. Esta opción apaga la máquina de Azure, transfiere los últimos cambios a la máquina virtual local y la inicia.
9. Puede iniciar sesión en la máquina virtual local para comprobar que todo funciona según lo esperado. A continuación, haga clic en **Confirmar** para finalizar la conmutación por error. La confirmación elimina la máquina virtual de Azure y sus discos, y la prepara para volver a protegerla.
10. Haga clic en **Replicación inversa** para comenzar a proteger la máquina virtual local.

    > [!NOTE]
    > Si cancela el trabajo de conmutación por recuperación durante el paso de sincronización de datos, la máquina virtual local tendrá un estado dañado. Esto se debe a que la sincronización de datos copia los datos más recientes de los discos de la máquina virtual de Azure a los discos de datos locales y puede que los datos de los discos no sean coherentes hasta que la sincronización se complete. Si se cancela la sincronización de datos, es posible que la máquina virtual local no arranque. Vuelva a desencadenar la conmutación por error para completar la sincronización de datos.


## <a name="why-is-there-no-button-called-failback"></a>¿Por qué no hay ningún botón llamado conmutación por recuperación?
En el portal, no hay ninguna acción explícita que se denomine conmutación por recuperación. Se trata de un paso para volver al sitio principal. Por definición, la conmutación por recuperación es cuando conmuta por error las máquinas virtuales del sitio de recuperación al sitio principal.

Cuando se inicia una conmutación por error, la hoja le informa sobre la dirección en la que van a moverse las máquinas virtuales, si la dirección es de Azure a local, es una conmutación por recuperación.

## <a name="why-is-there-only-a-planned-failover-gesture-to-failback"></a>¿Por qué hay solo una acción de conmutación por error planeada para la conmutación por recuperación?
Azure es un entorno de alta disponibilidad y las máquinas virtuales están siempre disponibles. La conmutación por recuperación es una actividad planeada que necesita un tiempo breve de inactividad para que las cargas de trabajo empiecen a ejecutarse de nuevo de forma local. No se prevé la pérdida de datos. Por lo tanto, hay solo una conmutación por error planeada, que desactivará las máquinas virtuales de Azure, descargará los cambios más recientes y garantizará que no se pierden datos.

## <a name="do-i-need-a-process-server-in-azure-to-failback-to-hyper-v"></a>¿Se necesita un servidor de proceso en Azure para realizar la conmutación por recuperación en Hyper-v?
No, solo se requiere un servidor de proceso cuando está protegiendo máquinas virtuales de VMware. No es necesario implementar componentes adicionales cuando se proteja o realice la conmutación por recuperación de máquinas virtuales de Hyper-v.


## <a name="time-taken-to-failback"></a>Tiempo que se tarda en realizar la conmutación por recuperación
El tiempo que se tarda en realizar la sincronización de datos y arrancar la máquina virtual depende de varios factores. Para tener una idea del tiempo que se tarda, se explica lo que ocurre durante la sincronización de datos.

La sincronización de datos realiza una instantánea de los discos de la máquina virtual e inicia la comprobación bloque por bloque y calcula su suma de comprobación. Esta suma de comprobación calculada se envía a un entorno local para compararlo con la suma de comprobación local del mismo bloque. En el caso de que las sumas de comprobación coincidan, el bloque de datos no se transfiere. Si no coinciden, el bloque de datos se transfiere al entorno local. Este tiempo de transferencia depende del ancho de banda disponible. La velocidad de la suma de comprobación es de algunos GB por minuto. 

Para acelerar la descarga de datos, puede configurar el agente de MARS para utilizar más subprocesos para equiparar la descarga. Vea [este documento](https://support.microsoft.com/en-us/help/3056159/how-to-manage-on-premises-to-azure-protection-network-bandwidth-usage) sobre cómo cambiar los subprocesos de descarga en el agente.


## <a name="next-steps"></a>Pasos siguientes

Después de **Confirmar**, puede iniciar la *Replicación inversa*. Esto inicia la protección de la máquina virtual del entorno local a Azure. Esto solo replicará los cambios, ya que la máquina virtual se ha desactivado en Azure y solo envía los cambios incrementales.
