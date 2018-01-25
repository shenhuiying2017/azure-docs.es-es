---
title: "Preparar máquinas para configurar la recuperación ante desastres entre regiones de Azure después de la migración a Azure mediante Site Recovery | Microsoft Docs"
description: "En este artículo se describe cómo preparar máquinas para configurar la recuperación ante desastres entre regiones de Azure después de la migración a Azure mediante Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: ponatara
manager: abhemraj
editor: 
ms.assetid: 9126f5e8-e9ed-4c31-b6b4-bf969c12c184
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2017
ms.author: ponatara
ms.openlocfilehash: 559e64ae3c16ed21bc09ac0c044281aa0a6e43df
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/22/2018
---
# <a name="replicate-azure-vms-to-another-region-after-migration-to-azure-by-using-azure-site-recovery"></a>Replicar máquinas virtuales de Azure en otra región después de la migración a Azure mediante Azure Site Recovery

>[!NOTE]
> La replicación de Azure Site Recovery en máquinas virtuales (VM) de Azure está actualmente en versión preliminar.

## <a name="overview"></a>Información general

Este artículo le ayudará a preparar máquinas virtuales de Azure para la replicación entre dos regiones de Azure después de que dichas máquinas se hayan migrado desde un entorno local a Azure mediante Azure Site Recovery.

## <a name="disaster-recovery-and-compliance"></a>Recuperación ante desastres y cumplimiento normativo
En la actualidad, cada vez más empresas están trasladando sus cargas de trabajo a Azure. Las empresas que trasladan a Azure sus cargas de trabajo de producción locales de máxima importancia se ven obligadas a configurar la recuperación ante desastres para dichas cargas de trabajo con vistas a garantizar el cumplimiento y protegerse contra las interrupciones en una región de Azure.

## <a name="steps-for-preparing-migrated-machines-for-replication"></a>Pasos para preparar para la replicación las máquinas migradas
Para preparar las máquinas migradas con vistas a configurar la replicación en otra región de Azure:

1. Complete la migración.
2. Si es necesario, instale el agente de Azure.
3. Quite Mobility Service.  
4. Reinicie la máquina virtual.

Estos pasos se describen con más detalle en las secciones siguientes.

### <a name="step-1-migrate-workloads-running-on-hyper-v-vms-vmware-vms-and-physical-servers-to-run-on-azure-vms"></a>Paso 1: migrar cargas de trabajo en ejecución en máquinas virtuales de Hyper-V, máquinas virtuales de VMware y servidores físicos, para que pasen a ejecutarse en máquinas virtuales de Azure

Para configurar la replicación y migrar a Azure cargas de trabajo físicas, de Hyper-V y VMware locales, siga los pasos indicados en el artículo [Migración de máquinas virtuales de IaaS de Azure entre regiones de Azure con Azure Site Recovery](site-recovery-migrate-azure-to-azure.md). 

Después de la migración, no es necesario ejecutar una conmutación por error ni eliminarla. En su lugar, seleccione la opción **Completar la migración** para cada máquina que quiera migrar:
1. En **Elementos replicados**, haga clic con el botón derecho en la máquina virtual y después haga clic en **Completar migración**. Haga clic en **Aceptar** para completar el paso. Puede realizar un seguimiento del progreso en las propiedades de la máquina virtual. Para ello, supervise el trabajo Completar la migración en **Trabajos de Azure Site Recovery**.
2. La acción **Completar la migración** termina el proceso de migración, quita la replicación de la máquina y detiene la facturación de la máquina en Site Recovery.

### <a name="step-2-install-the-azure-vm-agent-on-the-virtual-machine"></a>Paso 2: instalar el agente de máquina virtual de Azure en la máquina virtual
El [agente de máquina virtual](../../virtual-machines/windows/agent-user-guide.md) de Azure se debe instalar en la máquina virtual para que la extensión de Site Recovery funcione y proteja la máquina virtual.

>[!IMPORTANT]
>A partir de la versión 9.7.0.0, en las máquinas virtuales Windows, el instalador de Mobility Service también instalará la versión más reciente del agente de máquina virtual de Azure. Tras la migración, la máquina virtual cumple el requisito previo de instalación del agente para usar cualquier extensión de máquina virtual, incluida la extensión de Site Recovery. El agente de máquina virtual de Azure debe instalarse de forma manual únicamente si la versión de Mobility Service instalada en la máquina migrada es 9.6 o anterior.

En la tabla siguiente se proporciona información adicional sobre cómo instalar el agente de máquina virtual y validar que se ha instalado:

| **Operación** | **Windows** | **Linux** |
| --- | --- | --- |
| Instalación del agente de la máquina virtual |Descargue e instale el [MSI del agente](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Para completar la instalación, necesita privilegios de administrador. |Instale el [agente de Linux](../../virtual-machines/linux/agent-user-guide.md) más reciente. Para completar la instalación, necesita privilegios de administrador. Se recomienda instalar el agente desde el repositorio de distribución. *No se recomienda* instalar el agente de máquina virtual Linux directamente desde GitHub.  |
| Validación de la instalación del agente de máquina virtual |1. Vaya a la carpeta C:\WindowsAzure\Packages de la máquina virtual de Azure. El archivo WaAppAgent.exe debe estar ahí. <br>2. Haga clic con el botón derecho en el archivo, desplácese hasta **Propiedades** y seleccione la pestaña **Detalles**. En el campo **Versión del producto**, debe aparecer el valor 2.6.1198.718 o uno superior. |N/D |


### <a name="step-3-remove-the-mobility-service-from-the-migrated-virtual-machine"></a>Paso 3: quitar Mobility Service de la máquina virtual migrada

Si ha migrado máquinas de VMware locales o servidores físicos de Windows o Linux, debe quitar o desinstalar manualmente Mobility Service de la máquina virtual migrada.

>[!IMPORTANT]
>Este paso no es necesario para las máquinas virtuales de Hyper-V migradas a Azure.

#### <a name="uninstall-the-mobility-service-on-a-windows-server-vm"></a>Desinstalar Mobility Service de una máquina virtual de Windows Server
Use uno de los métodos siguientes para desinstalar Mobility Service de un equipo con Windows Server.

##### <a name="uninstall-by-using-the-windows-ui"></a>Desinstalar mediante la interfaz de usuario de Windows
1. En el Panel de Control, seleccione **Programas**.
2. Seleccione **Microsoft Azure Site Recovery Mobility Service/Servidor de destino maestro** y, a continuación, haga clic en**Desinstalar**.

##### <a name="uninstall-at-a-command-prompt"></a>Desinstalación en un símbolo del sistema
1. Abra una ventana de símbolo del sistema como administrador.
2. Para desinstalar Mobility Service, ejecute el comando siguiente:

   ```
   MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
   ```

#### <a name="uninstall-the-mobility-service-on-a-linux-computer"></a>Desinstalar Mobility Service de un equipo con Linux
1. En el servidor Linux, inicie sesión como un usuario **raíz**.
2. En un terminal, vaya a /user/local/ASR.
3. Para desinstalar Mobility Service, ejecute el comando siguiente:

   ```
   uninstall.sh -Y
   ```

### <a name="step-4-restart-the-vm"></a>Paso 4: reiniciar la máquina virtual

Después de desinstalar Mobility Service, reinicie la máquina virtual antes de configurar la replicación en otra región de Azure.


## <a name="next-steps"></a>pasos siguientes
- Empiece a proteger las cargas de trabajo mediante la [replicación de máquinas virtuales de Azure](azure-to-azure-quickstart.md).
- Obtenga más información en las [instrucciones sobre redes para replicar máquinas virtuales de Azure](site-recovery-azure-to-azure-networking-guidance.md).
