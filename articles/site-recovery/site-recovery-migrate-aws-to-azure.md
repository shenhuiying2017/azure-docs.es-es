---
title: "Migrar máquinas virtuales de Amazon Web Services a Azure con Site Recovery | Microsoft Docs"
description: "En este artículo se describe cómo migrar máquinas virtuales que se están ejecutando en Amazon Web Services (AWS) en Azure con Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: ddb412fd-32a8-4afa-9e39-738b11b91118
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: backup-recovery
ms.date: 11/01/2016
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 5614c39d914d5ae6fde2de9c0d9941e7b93fc10f
ms.openlocfilehash: c7238d677b315390bc5f53e54ab7dbded2871c5a


---
# <a name="migrate-virtual-machines-in-amazon-web-services-aws-to-azure-with-azure-site-recovery"></a>Migrar máquinas virtuales de Amazon Web Services (AWS) a Azure con Azure Site Recovery
## <a name="overview"></a>Información general
Bienvenido a Azure Site Recovery. Use este artículo para migrar instancias de EC2 que se ejecutan en AWS a Azure con Site Recovery. Antes de comenzar, tenga en cuenta lo siguiente:

* Azure tiene dos modelos de implementación diferentes para crear y utilizar recursos: Azure Resource Manager y el portal clásico. Azure también tiene dos portales: el Portal de Azure clásico que admite el modelo de implementación clásico y el Portal de Azure que es compatible con ambos modelos de implementación. Los pasos básicos para la migración son los mismos si va a configurar Site Recovery en Resource Manager o en el Portal de Azure clásico. Sin embargo, las instrucciones de la interfaz de usuario y las capturas de pantalla de este artículo son relevantes para el portal de Azure.
* **Actualmente solo puede migrar de AWS a Azure. Puede conmutar por error VM de AWS a Azure, pero no puede volver a conmutar por recuperación. No hay ninguna replicación en curso.**
* Las instrucciones de migración de este artículo se basan en las directrices para la replicación de una máquina física en Azure. Incluye vínculos a los pasos de [Replicación en Azure de máquinas físicas y máquinas virtuales de VMware con Azure Site Recovery con la utilización del Portal de Azure](site-recovery-vmware-to-azure.md), donde se describe cómo replicar un servidor físico en el Portal de Azure.
* Si está configurando Site Recovery en el portal clásico, siga las instrucciones detalladas de [este artículo](site-recovery-vmware-to-azure-classic.md). **Ya no debe usar** las instrucciones que se describen en este [artículo heredado](site-recovery-vmware-to-azure-classic-legacy.md).

Publique cualquier comentario o pregunta que tenga en la parte inferior de este artículo, o bien en el [foro de Servicios de recuperación de Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)

## <a name="supported-operating-systems"></a>Sistemas operativos compatibles
Site Recovery puede usarse para migrar instancias de EC2 que se ejecuten en cualquiera de los siguientes sistemas operativos:

### <a name="windows64-bit-only"></a>Windows (solo 64 bits)
* Windows Server 2008 R2 SP1+ (solo controladores de Citrix PV o de AWS PV; **no se admiten las instancias que ejecutan controladores de RedHat PV**)
* Windows Server 2012
* Windows Server 2012 R2

### <a name="linux-64-bit-only"></a>Linux (solo 64 bits)
* Red Hat Enterprise Linux 6.7 (solo instancias virtualizadas de HVM)

## <a name="prerequisites"></a>Requisitos previos
Requisitos para realizar esta implementación

* **Servidor de configuración**: una máquina virtual local que ejecute Windows Server 2012 R2 y que actúe como servidor de configuración. Instale los demás componentes de Site Recovery (incluido el servidor de procesos y el servidor de destino principal) en esta máquina virtual. Obtenga más información en [Arquitectura del escenario](site-recovery-vmware-to-azure.md#scenario-architecture) y [Requisitos previos del servidor de configuración/servidor de procesos de escalado horizontal](site-recovery-vmware-to-azure.md#configuration-server-or-additional-process-server-prerequisites).
* **Instancias de máquina virtual de EC2**: las instancias de EC2 que quiere migrar.

## <a name="deployment-steps"></a>Pasos de implementación
En esta sección se describen los pasos de implementación en el nuevo Portal de Azure. Si necesita estos pasos de implementación para Site Recovery en el portal clásico, consulte [este artículo](site-recovery-vmware-to-azure-classic.md).

1. [Cree un almacén](site-recovery-vmware-to-azure.md#create-a-recovery-services-vault).
2. [Implemente un servidor de configuración](site-recovery-vmware-to-azure.md#step-2-set-up-the-source-environment).
3. Cuando haya implementado el servidor de configuración, valide que se puede comunicar con las máquinas virtuales que quiere migrar.
4. [Configure las opciones de replicación](site-recovery-vmware-to-azure.md#step-4-set-up-replication-settings). Cree una directiva de replicación y asígnela al servidor de configuración.
5. [Instale el servicio de Movilidad](site-recovery-vmware-to-azure.md#step-6-replicate-applications). Cada máquina virtual que desea proteger necesita tener instalado el servicio de Movilidad. Este servicio envía datos al servidor de procesos. El servicio de Movilidad puede instalarse manualmente o insertarse e instalarse automáticamente mediante el servidor de procesos cuando está habilitada la protección para la máquina virtual. Las reglas de firewall de instancias de EC2 que desea migrar deben configurarse para permitir la instalación de inserción de este servicio. El grupo de seguridad para las instancias de EC2 debe tener las siguientes reglas:

    ![reglas de firewall](./media/site-recovery-migrate-aws-to-azure/migrate-firewall.png)
6. [Habilite la replicación](site-recovery-vmware-to-azure.md#enable-replication). Habilite la replicación para las VM que desea migrar. Puede detectar las instancias EC2 con las direcciones IP privada que se pueden obtener desde la consola EC2.
7. [ Ejecute una conmutación por error no planeada](site-recovery-failover.md#run-an-unplanned-failover). Una vez completada la replicación inicial puede ejecutar una conmutación por error no planeada desde AWS a Azure para cada VM. Si lo desea, puede crear un plan de recuperación y una conmutación por error no planeada para migrar varias máquinas virtuales desde AWS a Azure. [Obtenga más información](site-recovery-create-recovery-plans.md) sobre los planes de recuperación.

## <a name="next-steps"></a>Pasos siguientes
Obtenga más información sobre otros escenarios de replicación en [¿Qué es Site Recovery?](site-recovery-overview.md)



<!--HONumber=Nov16_HO3-->


