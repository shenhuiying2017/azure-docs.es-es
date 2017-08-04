---
title: "Eliminación del almacén de Site Recovery"
description: "Aprenda a eliminar un almacén de Azure Site Recovery, en función del escenario de Site Recovery."
service: site-recovery
documentationcenter: 
author: rajani-janaki-ram
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/04/2017
ms.author: rajani-janaki-ram
ms.translationtype: HT
ms.sourcegitcommit: 8021f8641ff3f009104082093143ec8eb087279e
ms.openlocfilehash: b95b9defa0a037f7d7d3ef36b99bc7c53c751050
ms.contentlocale: es-es
ms.lasthandoff: 07/21/2017

---
# <a name="delete-a-site-recovery-vault"></a>Eliminación del almacén de Site Recovery
Las dependencias pueden evitar la eliminación de un almacén de Azure Site Recovery. Las acciones que debe llevar a cabo varían según el escenario de Site Recovery: VMware a Azure, Hyper-V (con y sin System Center Virtual Machine Manager) a Azure y Azure Backup. Para eliminar un almacén de Azure Backup, consulte el artículo sobre la [eliminación de un almacén de Azure Backup](../backup/backup-azure-delete-vault.md).

>[!Important]
>Si va a probar el producto y no le importa perder datos, puede usar el método de eliminación forzada para eliminar el almacén y todas sus dependencias rápidamente.

> El comando de PowerShell eliminará todo el contenido del almacén y la acción es irreversible.

## <a name="use-powershell-to-force-delete-the-vault"></a>Uso de PowerShell para forzar la eliminación del almacén 

Para eliminar el almacén de Site Recovery aunque haya elementos protegidos, use estos comandos:

    Login-AzureRmAccount

    Select-AzureRmSubscription -SubscriptionName "XXXXX"

    $vault = Get-AzureRmSiteRecoveryVault -Name "vaultname"

    Remove-AzureRmSiteRecoveryVault -Vault $vault


## <a name="delete-a-site-recovery-vault"></a>Eliminación del almacén de Site Recovery 
Para eliminar el almacén, siga los pasos recomendados para su escenario.

### <a name="vmware-vms-to-azure"></a>Máquinas virtuales de VMware en Azure

1. Siga los pasos descritos en el artículo sobre la [deshabilitación de la protección para VMware](site-recovery-manage-registration-and-protection.md##disable-protection-for-a-vmware-vm-or-physical-server) para eliminar todas las máquinas virtuales protegidas.

2. Siga los pasos descritos en [Eliminación de una directiva de aplicación](site-recovery-setup-replication-settings-vmware.md##delete-a-replication-policy) para eliminar todas las directivas de replicación.

3. Siga los pasos descritos en el artículo de [eliminación de vCenter](site-recovery-vmware-to-azure-manage-vCenter.md##delete-a-vcenter-in-azure-site-recovery) para eliminar las referencias a vCenter.

4. Siga los pasos descritos en el artículo de [retirada de un servidor de configuración](site-recovery-vmware-to-azure-manage-configuration-server.md##decommissioning-a-configuration-server) para eliminar el servidor de configuración.

5. Elimine el almacén.


### <a name="hyper-v-vms-with-virtual-machine-manager-to-azure"></a>Máquinas virtuales de Hyper-V (con Virtual Machine Manager) a Azure
1. Eliminar todas las máquinas virtuales protegidas siguiendo los pasos descritos en [Disable protection for a VMware VM or physical server](site-recovery-manage-registration-and-protection.md##disable-protection-for-a-vmware-vm-or-physical-server) (Deshabilitar la protección de un servidor físico o una máquina virtual de VMware).

2. Siga los pasos descritos en [Eliminación de una directiva de aplicación](site-recovery-setup-replication-settings-vmware.md##delete-a-replication-policy) para eliminar todas las directivas de replicación.

3.  Siga los pasos del artículo sobre la [anulación del registro de un servidor VMM conectado](site-recovery-manage-registration-and-protection.md##unregister-a-connected-vmm-server) para eliminar las referencias a los servidores Virtual Machine Manager.

4.  Elimine el almacén.

### <a name="hyper-v-vms-without-virtual-machine-manager-to-azure"></a>Máquinas virtuales de Hyper-V (sin Virtual Machine Manager) a Azure
1. Eliminar todas las máquinas virtuales protegidas siguiendo los pasos descritos en [Disable protection for a VMware VM or physical server](site-recovery-manage-registration-and-protection.md##disable-protection-for-a-vmware-vm-or-physical-server) (Deshabilitar la protección de un servidor físico o una máquina virtual de VMware).

2. Siga los pasos descritos en [Eliminación de una directiva de aplicación](site-recovery-setup-replication-settings-vmware.md##delete-a-replication-policy) para eliminar todas las directivas de replicación.

3. Siga los pasos del artículo sobre la [anulación del registro de un host de Hyper-V](/site-recovery-manage-registration-and-protection.md##unregister-a-hyper-v-host-in-a-hyper-v-site) para eliminar las referencias a los servidores Hyper-V.

4. Elimine el sitio de Hyper-V.

5. Elimine el almacén.

