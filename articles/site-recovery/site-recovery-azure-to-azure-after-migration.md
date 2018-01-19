---
title: "Configuración de la recuperación ante desastres de máquinas virtuales de Azure después de migrar a Azure con Azure Site Recovery | Microsoft Docs"
description: "En este artículo se describe cómo preparar máquinas para configurar la recuperación ante desastres entre regiones de Azure después de la migración a Azure mediante Azure Site Recovery."
services: site-recovery
author: ponatara
ms.service: site-recovery
ms.topic: article
ms.date: 01/07/2018
ms.author: ponatara
ms.openlocfilehash: c06af21cd6e273b98c004e8bd0e6eac61ba7d644
ms.sourcegitcommit: 828cd4b47fbd7d7d620fbb93a592559256f9d234
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/18/2018
---
# <a name="set-up-disaster-recovery-for-azure-vms-after-migration-to-azure"></a>Configuración de la recuperación ante desastres de máquinas virtuales de Azure tras la migración a Azure 

>[!NOTE]
> La recuperación ante desastres para las máquinas virtuales de Azure con Azure Site Recovery actualmente se encuentra disponible en versión preliminar.

Lea este artículo después de [migrar las máquinas locales a las máquinas virtuales de Azure](tutorial-migrate-on-premises-to-azure.md) mediante el servicio [Site Recovery](site-recovery-overview.md). Este artículo le ayudará a preparar las máquinas virtuales de Azure para configurar la recuperación ante desastres en un región de Azure secundaria mediante Site Recovery.



## <a name="before-you-start"></a>Antes de comenzar

Antes de configurar la recuperación ante desastres, asegúrese de que la migración se haya completado según lo previsto. Para completar una migración correctamente después de una conmutación por error, debe seleccionar la opción **Completar la migración** en cada máquina que desea migrar. 



## <a name="install-the-azure-vm-agent"></a>Instale el agente de máquina virtual de Azure:

El [agente de VM](../virtual-machines/windows/agent-user-guide.md) de Azure debe estar instalado en la máquina virtual, para que Site Recovery pueda replicarlo.


1. Para instalar el agente de VM en máquinas virtuales que ejecutan Windows, descargue y ejecute el [instalador del agente](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Necesita privilegios de administrador en la máquina virtual para completar la instalación.
2. Para instalar el agente de VM en máquinas virtuales que ejecutan Linux, instale la última versión del [agente de Linux](../virtual-machines/linux/agent-user-guide.md). Para completar la instalación, necesita privilegios de administrador. Se recomienda realizar la instalación desde el repositorio de distribución. No se recomienda instalar el agente de máquina virtual Linux directamente desde GitHub. 


## <a name="validate-the-installation-on-windows-vms"></a>Validación de la instalación en máquinas virtuales Windows

1. En la máquina virtual de Azure, en la carpeta C:\WindowsAzure\Package, debería ver el archivo WaAppAgent.exe.
2. Haga clic con el botón derecho en el archivo y, en **Propiedades**, seleccione la pestaña **Detalles**.
3. Verifique que en el campo **Versión del producto** aparece el valor 2.6.1198.718 o una versión posterior.



## <a name="migration-from-vmware-vms-or-physical-servers"></a>Migración de máquinas virtuales de VMware o de servidores físicos

Si migra máquinas virtuales de VMware locales o servidores físicos a Azure, tenga en cuenta que:

- Solo necesita instalar el agente de máquina virtual de Azure si la versión de Mobility Service instalada en la máquina migrada es la 9.6 o posterior.
- En máquinas virtuales Windows que ejecutan la versión 9.7.0.0 de Mobility Service y posteriores, el instalador del servicio instala la última versión disponible del agente de máquina virtual de Azure. Al realizar la migración, estas máquinas virtuales ya cumplen el requisito previo de instalación del agente para usar cualquier extensión de máquina virtual, incluida la extensión de Site Recovery.
- Debe desinstalar manualmente Mobility Service de la máquina virtual de Azure mediante alguno de los métodos siguientes. Reinicie la máquina virtual antes de configurar la replicación.
    - En Windows, en Panel de control > **Agregar o quitar programas**, desinstale **Microsoft Azure Site Recovery Mobility Service/Servidor de destino maestro**. En un símbolo del sistema con privilegios elevados, ejecute:
        ```
        MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
        ```
    - En Linux, inicie sesión como un usuario raíz. En un terminal, vaya a **/user/local/ASR** y ejecute el siguiente comando:
        ```
        uninstall.sh -Y
        ```


## <a name="next-steps"></a>pasos siguientes

[Replicación rápida](azure-to-azure-quickstart.md) de una máquina virtual de Azure a una región secundaria
