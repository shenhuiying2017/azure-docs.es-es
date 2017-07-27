---
title: "Eliminación del almacén de Recovery Services"
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
ms.translationtype: Human Translation
ms.sourcegitcommit: bb794ba3b78881c967f0bb8687b1f70e5dd69c71
ms.openlocfilehash: 32fcab0c9e4665d07691dc3792bdee90fb01fe66
ms.contentlocale: es-es
ms.lasthandoff: 07/06/2017


---
# <a name="delete-recovery-services-vault"></a>Eliminación del almacén de Recovery Services
Las dependencias impiden que se elimine el almacén de Recovery Services y las acciones que debe realizar varían según el tipo de escenario de Azure Site Recovery: VMWare a Azure, Hyper-V (con y sin VMM) a Azure y Azure Backup. Para eliminar un almacén usado en Azure Backup, revise [este](../backup/backup-azure-delete-vault.md) vínculo.

>[!Important]
>Si va a probar el producto y desea eliminar rápidamente el almacén y no le importa perder datos, puede usar el método de eliminación forzada para quitar el almacén y todas sus dependencias.

> Tenga en cuenta que el comando de PowerShell eliminará todo el contenido del almacén y que es una acción irreversible

## <a name="force-delete-vault-using-powershell"></a>Eliminación forzada del almacén mediante PowerShell

Siga estos pasos para eliminar el almacén de Site Recovery incluso si hay elementos protegidos

    Login-AzureRmAccount

    Select-AzureRmSubscription -SubscriptionName "XXXXX"

    $vault = Get-AzureRmSiteRecoveryVault -Name "vaultname"

    Remove-AzureRmSiteRecoveryVault -Vault $vault



Siga los pasos recomendados (en el orden establecido) para su escenario a fin de eliminar el almacén

## <a name="delete-vault-used-in-site-recovery-for-protecting-vmware-vms-to-azure"></a>Eliminación del almacén, que se usa en Site Recovery para proteger las máquinas virtuales de VMWare en Azure:
1. Asegúrese de que se eliminen todas las máquinas virtuales protegidas, consulte [cómo hacerlo](site-recovery-manage-registration-and-protection.md##disable-protection-for-a-vmware-vm-or-physical-server).
2.  Asegúrese de que se eliminen todas las directivas de replicación, consulte [cómo hacerlo](site-recovery-setup-replication-settings-vmware.md##delete-a-replication-policy).
3.  Asegúrese de que se eliminen las referencias a vCenter, consulte [cómo hacerlo](site-recovery-vmware-to-azure-manage-vCenter.md##delete-a-vcenter-in-azure-site-recovery).
4. Asegúrese de que se elimine el servidor de configuración, consulte [cómo hacerlo](site-recovery-vmware-to-azure-manage-configuration-server.md##decommissioning-a-configuration-server).
5. Ahora intente eliminar el almacén.


## <a name="delete-vault-used-in-site-recovery-for-protecting-hyper-v-vms-with-vmm-to-azure"></a>Eliminación del almacén, que se usa en Site Recovery para proteger las máquinas virtuales de Hyper-V (con VMM) en Azure:
1.  Asegúrese de que se eliminen todas las máquinas virtuales protegidas, consulte [cómo hacerlo](site-recovery-manage-registration-and-protection.md##disable-protection-for-a-vmware-vm-or-physical-server).
- Asegúrese de que se eliminen todas las directivas de replicación, consulte [cómo hacerlo](site-recovery-setup-replication-settings-vmware.md##delete-a-replication-policy).
-   Elimine las referencias a servidores VMM, consulte [cómo hacerlo](site-recovery-manage-registration-and-protection.md##unregister-a-connected-vmm-server)
-   Ahora intente eliminar el almacén.

## <a name="delete-vault-used-in-site-recovery--for-protecting-hyper-v-vms-without-vmm-to-azure"></a>Eliminación del almacén, que se usa en Site Recovery para proteger las máquinas virtuales de Hyper-V (sin VMM) en Azure:
1. Asegúrese de que se eliminen todas las máquinas virtuales protegidas, consulte [cómo hacerlo](site-recovery-manage-registration-and-protection.md##disable-protection-for-a-vmware-vm-or-physical-server).
- Asegúrese de que se eliminen todas las directivas de replicación, consulte [cómo hacerlo](site-recovery-setup-replication-settings-vmware.md##delete-a-replication-policy).
-   Elimine las referencias a servidores Hyper-V, consulte [cómo hacerlo](/site-recovery-manage-registration-and-protection.md##unregister-a-hyper-v-host-in-a-hyper-v-site).
-   Elimine el sitio de Hyper-V.
-   Ahora intente eliminar el almacén.

