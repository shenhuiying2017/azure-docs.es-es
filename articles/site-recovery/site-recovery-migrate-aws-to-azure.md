---
title: "Migración de máquinas virtuales de AWS a Azure| Microsoft Docs"
description: "En este artículo se describe cómo migrar máquinas virtuales que se están ejecutando en Amazon Web Services (AWS) en Azure con Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: bsiva
manager: jwhit
editor: 
ms.assetid: ddb412fd-32a8-4afa-9e39-738b11b91118
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: backup-recovery
ms.date: 02/12/2017
ms.author: bsiva
translationtype: Human Translation
ms.sourcegitcommit: 5031f64ffcd34b6287a3ecd87dd027c2bc7c716f
ms.openlocfilehash: 9d0d0ba4ca5966b39ce62ea8296d48e5930c9782


---
# <a name="migrate-virtual-machines-in-amazon-web-services-aws-to-azure-with-azure-site-recovery"></a>Migrar máquinas virtuales de Amazon Web Services (AWS) a Azure con Azure Site Recovery

En este artículo se describe cómo migrar instancias de AWS Windows a máquinas virtuales de Azure con el servicio [Azure Site Recovery](site-recovery-overview.md).

La migración es realmente una conmutación por error de AWS a Azure. No podrá conmutar por recuperación máquinas y no habrá ninguna replicación en curso. En este artículo se describen los pasos necesarios para realizar la migración en Azure Portal; se basan en las instrucciones para [replicar una máquina física en Azure](site-recovery-vmware-to-azure.md).

Publique cualquier comentario o pregunta que tenga en la parte inferior de este artículo, o bien en el [foro de Servicios de recuperación de Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)

## <a name="supported-operating-systems"></a>Sistemas operativos compatibles

Site Recovery puede usarse para migrar instancias de EC2 que se ejecuten en cualquiera de los siguientes sistemas operativos:

- Windows (solo&64; bits)
    - Windows Server 2008 R2 SP1 + (solo controladores de Citrix PV o de AWS PV). **No se admiten las instancias que ejecutan controladores de RedHat PV**) Windows Server 2012 Windows Server 2012 R2
- Linux (solo&64; bits)
    - Red Hat Enterprise Linux 6.7 (solo instancias virtualizadas de HVM)

## <a name="prerequisites"></a>Requisitos previos

Requisitos para realizar esta implementación

* **Servidor de configuración**: una máquina virtual local que ejecute Windows Server 2012 R2 implementada como servidor de configuración. De forma predeterminada, los demás componentes de Site Recovery (el servidor de procesos y el servidor de destino maestro) se instalan al implementar el servidor de configuración. [Más información](site-recovery-components.md#replicate-vmware-vmsphysical-servers-to-azure)
* **Instancias de EC2**: las instancias de máquinas virtuales EC2 que quiere migrar.

## <a name="deployment-steps"></a>Pasos de implementación

1. Creación de un almacén
2. Implementación del servidor de configuración
3. Cuando haya implementado el servidor de configuración, valide que se puede comunicar con las máquinas virtuales que quiere migrar.
4. Configure las opciones de replicación.
5. Instale Mobility Service. Cada máquina virtual que desea proteger necesita tener instalado el servicio de Movilidad. Este servicio envía datos al servidor de procesos. El servicio de Movilidad puede instalarse manualmente o insertarse e instalarse automáticamente mediante el servidor de procesos cuando está habilitada la protección para la máquina virtual. Las reglas de firewall de instancias de EC2 que desea migrar deben configurarse para permitir la instalación de inserción de este servicio. El grupo de seguridad para las instancias de EC2 debe tener las siguientes reglas:

    ![reglas de firewall](./media/site-recovery-migrate-aws-to-azure/migrate-firewall.png)
6. Habilite la replicación. Habilite la replicación para las VM que desea migrar. Puede detectar las instancias EC2 con las direcciones IP privada que se pueden obtener desde la consola EC2.
7. Ejecute una conmutación por error no planeada. Una vez completada la replicación inicial puede ejecutar una conmutación por error no planeada desde AWS a Azure para cada VM. Si lo desea, puede crear un plan de recuperación y una conmutación por error no planeada para migrar varias máquinas virtuales desde AWS a Azure. [Obtenga más información](site-recovery-create-recovery-plans.md) sobre los planes de recuperación.

Obtenga instrucciones detalladas para los [pasos de implementación](site-recovery-vmware-to-azure.md) y ejecutar una [conmutación por error no planeada](site-recovery-failover.md#run-an-unplanned-failover).



<!--HONumber=Feb17_HO2-->


