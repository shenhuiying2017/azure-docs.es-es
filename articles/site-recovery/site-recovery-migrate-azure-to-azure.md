<properties
	pageTitle="Migrar máquinas virtuales de IaaS de Azure de una región de Azure a otra con Site Recovery | Microsoft Azure"
	description="Use Azure Site Recovery para migrar máquinas virtuales de IaaS de Azure de una región de Azure a otra."
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor="tysonn"/>

<tags
	ms.service="site-recovery"
	ms.workload="backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/16/2016"
	ms.author="raynew"/>

#  Migración de máquinas virtuales de IaaS de Azure entre regiones de Azure con Azure Site Recovery

## Información general

En este artículo se describe cómo usar Site Recovery para migrar máquinas virtuales de Azure entre regiones de Azure. Antes de comenzar, tenga en cuenta lo siguiente:

- Solo puede realizar la migración en este momento. Es decir, puede conmutar por error las máquinas virtuales de una región de Azure a otra, pero no podrá volver a realizar este proceso con estas máquinas más adelante.
- En este artículo se resumen y emplean muchos de los pasos que se describen al completo en [Replicación de máquinas virtuales de VMware y servidores físicos en Azure](site-recovery-vmware-to-azure-classic.md), que proporciona las instrucciones mejoradas más recientes para configurar la replicación. Le recomendamos que siga este artículo para obtener instrucciones detalladas cuando realice la migración.
- **Ya no debe usar** las instrucciones que se describen en este [artículo heredado](site-recovery-vmware-to-azure-classic-legacy.md).

Publique cualquier comentario o pregunta que tenga en la parte inferior de este artículo, o bien en el [foro de Servicios de recuperación de Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## Requisitos previos

Requisitos para realizar esta implementación:

- **Servidor de administración**: una máquina virtual que ejecute Windows Server 2012 R2 y que actúe como servidor de administración. Instalar los componentes de Site Recovery (incluido el servidor de configuración y el servidor de procesos) en este servidor. Obtenga más información en las [consideraciones sobre el servidor de administración](site-recovery-vmware-to-azure-classic.md#management-server-considerations) y los [requisitos previos de origen](site-recovery-vmware-to-azure-classic.md#on-premises-prerequisites).
- **Máquinas virtuales IaaS**: las máquinas virtuales que desea migrar.

## Pasos de implementación

1. [Cree un almacén](site-recovery-vmware-to-azure-classic.md#step-1-create-a-vault).
2. [Implemente un servidor de administración](site-recovery-vmware-to-azure-classic.md#Step-5-install-the-management-server).
3. Cuando haya implementado el servidor de administración, valide que se puede comunicar con las máquinas virtuales que quiere migrar.
4. [Cree un grupo de protección](site-recovery-vmware-to-azure-classic.md#step-8-create-a-protection-group). Un grupo de protección contiene las máquinas protegidas que comparten la misma configuración de replicación. Especifica la configuración de replicación de un grupo y esta configuración se aplicará a todas las máquinas que agregue al grupo.
5. [Instale el servicio de movilidad](site-recovery-vmware-to-azure-classic.md#step-9-install-the-mobility-service). Cada máquina virtual que desea proteger necesita tener instalado el servicio de Movilidad. Este servicio envía datos al servidor de procesos. El servicio de Movilidad puede instalarse manualmente o insertarse e instalarse automáticamente mediante el servidor de procesos cuando está habilitada la protección para la máquina virtual. Tenga en cuenta que las reglas de firewall de las máquinas virtuales de IaaS que quiere migrar deben estar configuradas para permitir la instalación de inserción de este servicio.
6. [Habilite la protección para las máquinas](site-recovery-vmware-to-azure-classic.md#step-10-enable-protection-for-a-machine). Agregue las máquinas que quiere proteger al grupo de replicación. 
7. Puede detectar las máquinas virtuales de IaaS que desea migrar a Azure usando la dirección IP privada de las máquinas virtuales. Busque esta dirección en el panel de la máquina virtual de Azure.
8. En la pestaña del grupo de protección que ha creado, haga clic en **Agregar máquinas** > **Máquinas físicas**.

	![Detección de EC2](./media/site-recovery-migrate-azure-to-azure/migrate-add-machines.png)

9. Especifique la dirección IP privada de la máquina virtual.

	![Detección de EC2](./media/site-recovery-migrate-azure-to-azure/migrate-machine-ip.png)
	
	Después de agregar una máquina al grupo, se habilitará la protección y se ejecutará la replicación inicial según la configuración del grupo de protección.

10. [Ejecute una conmutación por error no planeada](site-recovery-failover.md#run-an-unplanned-failover). Una vez completada la replicación inicial puede ejecutar una conmutación por error no planeada desde una región de Azure a otra. Si lo desea, puede crear un plan de recuperación y una conmutación por error no planeada para migrar varias máquinas virtuales entre las regiones. [Obtenga más información](site-recovery-create-recovery-plans.md) sobre los planes de recuperación.
		
## Pasos siguientes

Obtenga más información sobre otros escenarios de replicación en [¿Qué es Azure Site Recovery?](site-recovery-overview.md)

<!---HONumber=AcomDC_0518_2016-->