---
title: "Migración de máquinas virtuales de IaaS de Azure entre regiones de Azure | Microsoft Docs"
description: "Use Azure Site Recovery para migrar máquinas virtuales de IaaS de Azure de una región de Azure a otra."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: tysonn
ms.assetid: 8a29e0d9-0010-4739-972f-02b8bdf360f6
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: b0fb6e2b86aa0a47b7250face90be8ab2d06b78e
ms.contentlocale: es-es
ms.lasthandoff: 03/15/2017


---
# <a name="migrate-azure-iaas-virtual-machines-between-azure-regions-with-azure-site-recovery"></a>Migración de máquinas virtuales de IaaS de Azure entre regiones de Azure con Azure Site Recovery
## <a name="overview"></a>Información general
Bienvenido a Azure Site Recovery. Utilice este artículo si quiere migrar máquinas virtuales de Azure de una región de Azure a otra. Antes de comenzar, tenga en cuenta lo siguiente:

* Azure tiene dos modelos de implementación diferentes para crear y utilizar recursos: Azure Resource Manager y el portal clásico. Azure también tiene dos portales: el Portal de Azure clásico que admite el modelo de implementación clásico y el Portal de Azure que es compatible con ambos modelos de implementación. Los pasos básicos para la migración son los mismos, con independencia de que vaya a configurar Site Recovery en Resource Manager o en el portal clásico. Sin embargo, las instrucciones de la interfaz de usuario y las capturas de pantalla del artículo son pertinentes para el Portal de Azure.
* **Actualmente, solo puede efectuar la migración de una región a otra. Puede conmutar por error las máquinas virtuales de una región de Azure a otra, pero no podrá volver a realizar este proceso con estas máquinas más adelante.**
* Las instrucciones de migración de este artículo se basan en las directrices para la replicación de una máquina física en Azure. Incluye vínculos a los pasos de [Replicación en Azure de máquinas físicas y máquinas virtuales de VMware con Azure Site Recovery con la utilización del Portal de Azure](site-recovery-vmware-to-azure.md), donde se describe cómo replicar un servidor físico en el Portal de Azure.
* Si está configurando Site Recovery en el portal clásico, siga las instrucciones detalladas de [este artículo](site-recovery-vmware-to-azure-classic.md).

Publique cualquier comentario o pregunta que tenga en la parte inferior de este artículo, o bien en el [foro de Servicios de recuperación de Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="prerequisites"></a>Requisitos previos
Requisitos para realizar esta implementación:

* **Servidor de configuración**: una máquina virtual local que ejecuta Windows Server 2012 R2 y que actúa como el servidor de configuración. Instale los demás componentes de Site Recovery (incluido el servidor de procesos y el servidor de destino principal) en esta máquina virtual. Obtenga más información en [Arquitectura del escenario](site-recovery-components.md#vmware-to-azure) y [Requisitos previos del servidor de configuración/servidor de procesos de escalado horizontal](site-recovery-vmware-to-azure.md#prerequisites).
* **Máquinas virtuales IaaS**: las máquinas virtuales que desea migrar. Migrará estas máquinas virtuales tratándolas como máquinas físicas.

## <a name="deployment-steps"></a>Pasos de implementación
En esta sección se describen los pasos de implementación en el nuevo Portal de Azure.

1. [Cree un almacén](site-recovery-vmware-to-azure.md#create-a-recovery-services-vault).
2. [Implemente un servidor de configuración](site-recovery-vmware-to-azure.md#prepare-the-configuration-server).
3. Cuando haya implementado el servidor de administración, compruebe que se puede comunicar con las máquinas virtuales que quiere migrar.
4. [Configure las opciones de replicación](site-recovery-vmware-to-azure.md#set-up-replication-settings). Cree una directiva de replicación y asígnela al servidor de configuración.
5. [Instale el servicio de Movilidad](site-recovery-vmware-to-azure.md#prepare-vms-for-replication). Cada máquina virtual que desea proteger necesita tener instalado el servicio de Movilidad. Este servicio envía datos al servidor de procesos. El servicio de Movilidad puede instalarse manualmente o insertarse e instalarse automáticamente mediante el servidor de procesos cuando está habilitada la protección para la máquina virtual. Las reglas de firewall de las máquinas virtuales que quiera migrar deben configurarse para permitir la instalación de inserción de este servicio.
6. [Habilite la replicación](site-recovery-vmware-to-azure.md#enable-replication). Habilite la replicación para las máquinas virtuales que desee migrar. Puede detectar las máquinas virtuales de IaaS que desea migrar a Azure usando la dirección IP privada de las máquinas virtuales. Busque esta dirección en el panel de la máquina virtual de Azure. Cuando habilite la replicación, establezca el tipo de máquina para las VM como máquinas físicas.
7. [ Ejecute una conmutación por error no planeada](site-recovery-failover.md). Una vez completada la replicación inicial, puede ejecutar una conmutación por error no planeada desde una región de Azure a otra. Si lo desea, puede crear un plan de recuperación y una conmutación por error no planeada para migrar varias máquinas virtuales entre las regiones. [Obtenga más información](site-recovery-create-recovery-plans.md) sobre los planes de recuperación.

## <a name="next-steps"></a>Pasos siguientes
Obtenga más información sobre otros escenarios de replicación en [¿Qué es Site Recovery?](site-recovery-overview.md)

