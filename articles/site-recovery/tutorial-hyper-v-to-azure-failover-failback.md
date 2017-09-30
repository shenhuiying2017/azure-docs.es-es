---
title: "Conmutación por error y conmutación por recuperación de máquinas virtuales de Hyper-V replicadas en Azure con Site Recovery | Microsoft Docs"
description: "Obtenga información sobre cómo conmutar por error máquinas virtuales de Hyper-V en Azure y conmutar por recuperación en el sitio local con Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 72065d01-ed0f-430e-b117-a5885cecd1db
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/15/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: e1cc21661450a983c25b24fe2a6228e26ceecec6
ms.contentlocale: es-es
ms.lasthandoff: 09/25/2017

---

# <a name="fail-over-and-fail-back-hyper-v-vms-replicated-to-azure"></a>Conmutar por error y conmutar por recuperación máquinas virtuales de Hyper-V replicadas en Azure

El servicio [Azure Site Recovery](site-recovery-overview.md) administra y coordina la replicación, la conmutación por error y la conmutación por recuperación de máquinas locales y máquinas virtuales (VM) de Azure.

En este tutorial se describe cómo conmutar por error una máquina virtual de Hyper-V en Azure. Después de conmutar por error, conmute por recuperación a su sitio local cuando esté disponible. En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Conmutar por error máquinas virtuales de Hyper-V del entorno local a Azure
> * Conmutar por recuperación de Azure al entorno local, y empezar a replicar en Azure de nuevo

## <a name="overview"></a>Información general
La conmutación por error y la conmutación por recuperación constan de dos fases:

1. **Conmutación por error a Azure**: se conmutan por error las máquinas del sitio local a Azure.
2. **Conmutación por recuperación de Azure al entorno local**: se ejecuta una conmutación por error planeada de Azure al entorno local. Después de la conmutación por error planeada, habilite la replicación inversa para empezar a replicar de nuevo del entorno local a Azure. 


## <a name="fail-over-to-azure"></a>Conmutación por error a Azure

### <a name="failover-prerequisites"></a>Requisitos previos de conmutación por error

Para completar la conmutación por error:

- Asegúrese de que ha obtenido los [detalles de la recuperación ante desastres](tutorial-dr-drill-azure.md) para comprobar que todo funciona según lo previsto.
- Si quiere, puede prepararse para conectarse a las máquinas virtuales de Azure antes de conmutar por error.
- Compruebe las propiedades de la máquina virtual antes de ejecutar la conmutación por error.

#### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Preparación para la conexión a las máquinas virtuales de Azure después de la conmutación por error

Si quiere conectarse a máquinas virtuales de Azure mediante RDP después de la conmutación por error, haga lo siguiente:

##### <a name="azure-vms-running-windows"></a>Máquinas virtuales de Azure que ejecutan Windows

1. Para obtener acceso a máquinas virtuales de Azure a través de Internet, habilite RDP en la máquina virtual local antes de la conmutación por error. Asegúrese de que se hayan agregado las reglas de TCP y UDP para el perfil **Público**, y que RDP se permite en **Firewall de Windows** > **Aplicaciones permitidas** para todos los perfiles.
2. Para obtener acceso a través de VPN de sitio a sitio, habilite RDP en la máquina local. RDP debe permitirse en **Firewall de Windows** -> **Aplicaciones y características permitidas** para redes de **dominio y privadas**. Compruebe que la directiva SAN del sistema operativo está establecida en **OnlineAll**. [Más información](https://support.microsoft.com/kb/3031135). No debe haber actualizaciones de Windows pendientes en la máquina virtual cuando se desencadene una conmutación por error. Si hay, no podrá iniciar sesión en la máquina virtual hasta que se complete la actualización. 
3. En la máquina virtual de Microsoft Azure después de la conmutación por error, compruebe los **Diagnósticos de arranque** para ver una captura de pantalla de la máquina virtual. Si no puede conectarse, compruebe que se está ejecutando la máquina virtual y revise estas [sugerencias de solución de problemas](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).


##### <a name="azure-vms-running-linux"></a>Máquinas virtuales de Azure que ejecutan Linux

1. En el equipo local antes de la conmutación por error, compruebe que el servicio de Secure Shell está configurado para iniciarse automáticamente en el arranque del sistema. Compruebe que las reglas de firewall permiten una conexión SSH.
2. En la máquina virtual de Azure después de la conmutación por error, permita las conexiones entrantes al puerto SSH para las reglas del grupo de seguridad de red de la máquina virtual conmutada por error y para la subred de Azure a la que esta se conecta.  [Agregue una dirección IP pública](site-recovery-monitoring-and-troubleshooting.md#adding-a-public-ip-on-a-resource-manager-virtual-machine) para la máquina virtual. Puede comprobar los **Diagnósticos de arranque** para ver una captura de pantalla de la máquina virtual.


#### <a name="verify-vm-properties"></a>Comprobar las propiedades de la máquina virtual

Compruebe las propiedades de la máquina virtual y asegúrese de que la máquina virtual cumpla con los [requisitos de Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).

1. En **Elementos protegidos**, haga clic en **Elementos replicados** > VM.
2. En el panel **Elemento replicado**, puede ver un resumen de la información de la máquina virtual, el estado de mantenimiento y los puntos de recuperación disponibles más recientes. Haga clic en **Propiedades** para ver más detalles.
3. En **Proceso y red**, puede modificar el nombre de Azure, el grupo de recursos, el tamaño de destino, el [conjunto de disponibilidad](../virtual-machines/windows/tutorial-availability-sets.md) y la [configuración de discos administrados](#managed-disk-considerations).
4. Puede ver y modificar la configuración de red, incluida la red o subred en la que se va a ubicar la máquina virtual de Azure después de la conmutación por error y la dirección IP que se le va a asignar.
5. En **Discos** puede ver información sobre los discos de datos y el sistema operativo de la máquina virtual.


### <a name="run-a-failover-from-on-premises-to-azure"></a>Ejecutar una conmutación por error del entorno local a Azure

Puede ejecutar una conmutación por error normal o planeada para máquinas virtuales de Hyper-V.

- Use una conmutación por error normal para las interrupciones inesperadas. Al ejecutar esta conmutación por error, crea una máquina virtual de Azure y la enciende. Ejecute la conmutación por error en un punto de recuperación concreto. Puede producirse una pérdida de datos, en función del punto de recuperación que use.
- Puede usar una conmutación por error planeada para el mantenimiento o durante una interrupción esperada. Esta opción no produce la pérdida de datos. Cuando se desencadena una conmutación por error planeada, las máquinas virtuales de origen se apagan. Los datos no sincronizados se sincronizan y se desencadena la conmutación por error.

En este procedimiento se describe cómo se ejecuta una conmutación por error normal.


1. En **Configuración** > **Elementos replicados**, haga clic en VM > **Conmutación por error**.
2. En **Conmutación por error**, seleccione un **punto de recuperación** en el que realizar la conmutación por error. Puede seleccionar una de las siguientes opciones:
    - **Último** (valor predeterminado): esta opción procesa primero todos los datos enviados a Site Recovery. Ofrece el objetivo de punto de recuperación (RPO) mínimo, ya que la máquina virtual de Azure creada después de la conmutación por error tiene todos los datos replicados en Site Recovery al desencadenarse la conmutación por error.
    - **Procesado más recientemente**: con esta opción se realiza una conmutación por error de la máquina virtual al último punto de recuperación procesado por Site Recovery. Esta opción proporciona un objetivo de tiempo de recuperación (RTO) bajo, ya que no se invierte tiempo en el procesamiento de datos sin procesar.
    - **Más reciente coherente con la aplicación**: esta opción conmuta por error la máquina virtual al punto de recuperación más reciente coherente con la aplicación que haya procesado Site Recovery. 
    - **Personalizado**: especifique un punto de recuperación.
3. Si realiza la conmutación por error a Azure de máquinas virtuales de Hyper-V en nubes de System Center VMM y está habilitado el cifrado de datos en la nube, seleccione en **Clave de cifrado** el certificado emitido al habilitarse el cifrado de datos durante la instalación del proveedor en el servidor VMM.
4. Seleccione **Shut down machine before beginning failover** (Apagar la máquina antes de comenzar la conmutación por error) si desea que Site Recovery intente apagar las máquinas virtuales de origen antes de desencadenar la conmutación por error. Antes de desencadenar la conmutación por error, Site Recovery también intentará sincronizar los datos locales que aún no se han enviado a Azure. Tenga en cuenta que la conmutación por error continúa aunque se produzca un error en el apagado. Puede seguir el progreso de la conmutación por error en la página **Trabajos**.
5. Si está preparado para conectarse a la máquina virtual de Azure, hágalo para validarla después de la conmutación por error.
6. Después de la comprobación, **confirme** la conmutación por error. Este proceso eliminará todos los puntos de recuperación disponibles.

> [!WARNING]
> **No cancele una conmutación por error en curso**: antes de iniciar la conmutación por error, se detiene la replicación de la máquina virtual. Si se cancela una conmutación por error en curso, la conmutación por error se detiene, pero no replica la máquina virtual de nuevo.  


## <a name="fail-back-from-azure-to-on-premises"></a>Conmutación por recuperación de Azure al entorno local

### <a name="prerequisites-for-failback"></a>Requisitos previos para la conmutación por recuperación

Para completar la conmutación por recuperación, asegúrese de que el servidor VMM o Hyper-V del sitio principal está conectado a Site Recovery.


### <a name="run-a-failback-and-reprotect-on-premises-vms"></a>Ejecutar una conmutación por recuperación y volver a proteger las máquinas virtuales locales

Realice la conmutación por error de Azure al sitio local y empiece a replicar las máquinas virtuales del sitio local a Azure.

1. En **Configuración** > **Elementos replicados**, haga clic en VM > **Conmutación por error planeada**.
2. En **Confirmar conmutación por error planeada**, compruebe la dirección de la conmutación por error (de Azure) y seleccione las ubicaciones de origen y de destino. 
3. En **Sincronización de datos**, especifique cómo quiere realizar la sincronización:
    - **Sincronizar datos antes de la conmutación por error (sincronizar solo cambios incrementales)**: esta opción reduce al mínimo el tiempo de inactividad de las máquinas virtuales, ya que realiza la sincronización sin apagarlas. Hace lo siguiente:
        1. Toma una instantánea de la máquina virtual de Azure y la copia en el host de Hyper-V local. La máquina virtual sigue ejecutándose en Azure.
        2. Apaga la máquina virtual de Azure, de modo que no se produzca ningún cambio nuevo. El último conjunto de cambios incrementales se transfiere al servidor local y se inicia la máquina virtual local.
    - **Sincronizar datos solo durante la conmutación por error (descarga completa)**: use esta opción si ha estado trabajando en Azure durante mucho tiempo. Esta opción es más rápida, ya que esperamos que se realicen varios cambios en el disco y no queremos invertir tiempo en los cálculos de suma de comprobación. Esta opción realiza una descarga de disco. También es útil cuando se ha eliminado la máquina virtual local.
4. Si realiza la conmutación por error a Azure de máquinas virtuales de Hyper-V en nubes de System Center VMM y está habilitado el cifrado de datos en la nube, seleccione en **Clave de cifrado** el certificado emitido al habilitarse el cifrado de datos durante la instalación del proveedor en el servidor VMM.
5. Inicie la conmutación por error. Puede seguir el progreso de la conmutación por error en la pestaña **Trabajos** .
6. Si ha seleccionado la opción de sincronizar los datos antes de la conmutación por error, una vez que la sincronización de datos inicial haya finalizado y esté listo para apagar las máquinas virtuales de Azure, haga clic en **Trabajos** > nombre del trabajo de conmutación por error planeado > **Completar conmutación por error**. La máquina virtual de Azure se apagará, se transferirán los cambios más recientes en el entorno local y se iniciará la máquina virtual local.
7. Inicie sesión en la máquina virtual local para comprobar que está disponible según lo previsto.
8. La máquina virtual local se encuentra ahora en un estado de confirmación pendiente. Haga clic en **Confirmar** para confirmar la conmutación por error. Esto elimina las máquinas virtuales de Azure y sus discos, y prepara la máquina virtual local para la replicación inversa.
9. Para iniciar la replicación de la máquina virtual local en Azure, habilite la **replicación inversa**.


> [!NOTE]
> La replicación inversa solo replica los cambios que se han producido desde que se ha apagado la máquina virtual de Azure, y solo se envían los cambios incrementales.


