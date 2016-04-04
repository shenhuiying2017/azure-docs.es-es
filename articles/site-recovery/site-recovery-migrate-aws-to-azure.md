<properties
	pageTitle="Migrar máquinas virtuales de Windows de Amazon Web Services a Azure con Site Recovery | Microsoft Azure"
	description="Este artículo describe cómo usar Azure Site Recovery para migrar máquinas virtuales de Windows que se están ejecutando en Amazon Web Services (AWS) en Azure."
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="backup-recovery"
	ms.date="03/16/2016"
	ms.author="raynew"/>

#  Migrar máquinas virtuales de Windows en Amazon Web Services (AWS) a Azure con Azure Site Recovery

## Información general

En este artículo se describe cómo usar la recuperación del sitio para migrar las instancias de Windows que se ejecutan en AWS a Azure. Antes de empezar, tenga en cuenta lo siguiente:

- Solo puede migrar en este momento. Esto significa que puede conmutar por error de AWS a Azure, pero no puede volver a conmutar por recuperación.
- Este artículo resume y usa muchos de los pasos que se describen al completo en [Replicación de máquinas virtuales de VMware y servidores físicos en Azure](site-recovery-vmware-to-azure-classic.md), que proporciona las instrucciones mejoradas más recientes para la configuración de la replicación. Le sugerimos que siga este artículo para obtener instrucciones detalladas al migrar.
- **Ya no debe usar** las instrucciones que se describen en este [artículo heredado](site-recovery-vmware-to-azure-classic-legacy.md).

Publique cualquier comentario o pregunta que tenga en la parte inferior de este artículo, o bien en el [foro de Servicios de recuperación de Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## Requisitos previos

Esto es lo necesita antes de empezar:

- **Servidor de administración**: una máquina virtual local que ejecuta Windows Server 2012 R2 que actúa como servidor de administración. Instalar los componentes de Site Recovery (incluido el servidor de configuración y el servidor de procesos) en este servidor. Obtenga más información en [Consideraciones sobre el servidor de administración](site-recovery-vmware-to-azure-classic.md#management-server-considerations) y [Requisitos previos locales](site-recovery-vmware-to-azure-classic.md#on-premises-prerequisites).
- **Instancias de máquina virtual EC2**: las instancias que desea migrar y, a continuación, proteger.

## Pasos de implementación

1. [Creación de un almacén](site-recovery-vmware-to-azure-classic.md#step-1-create-a-vault)
2. [Implemente un servidor de administración](site-recovery-vmware-to-azure-classic.md#Step-5-install-the-management-server). 
3. Una vez implementado el servidor de administración, compruebe que se puede comunicar con las instancias de EC2 que desea migrar.
4. [Cree un grupo de protección](site-recovery-vmware-to-azure-classic.md#step-8-create-a-protection-group). Un grupo de protección contiene las máquinas protegidas que comparten la misma configuración de replicación. Especifica la configuración de replicación de un grupo y esta configuración se aplicará a todas las máquinas que agregue al grupo. 
5. [Instale el servicio de movilidad](site-recovery-vmware-to-azure-classic.md#step-9-install-the-mobility-service). Cada máquina virtual que desea proteger necesita tener instalado el servicio de Movilidad. Este servicio envía datos al servidor de procesos. El servicio de Movilidad puede instalarse manualmente o insertarse e instalarse automáticamente mediante el servidor de procesos cuando está habilitada la protección para la máquina virtual. Las reglas de firewall de instancias de EC2 que desea migrar deben configurarse para permitir la instalación de inserción de este servicio. El grupo de seguridad para las instancias de EC2 debe tener las siguientes reglas:

	![reglas de firewall](./media/site-recovery-migrate-aws-to-azure/migrate-firewall.png)

6. [Habilite la protección para las máquinas](site-recovery-vmware-to-azure-classic.md#step-10-enable-protection-for-a-machine). Agregue las máquinas que quiere proteger al grupo de replicación.

	![habilitar protección](./media/site-recovery-migrate-aws-to-azure/migrate-add-machines.png)

7. Puede detectar las instancias EC2 que desea migrar a Azure usando la dirección IP privada de la instancia que se puede obtener desde la consola EC2. Puede agregar la dirección IP privada de cada instancia al grupo de protección.

	![habilitar protección](./media/site-recovery-migrate-aws-to-azure/migrate-machine-ip.png)

	Después de agregar una máquina al grupo, se habilitará la protección y se ejecutará la replicación inicial según la configuración del grupo de protección.

8. [Ejecución de una conmutación por error no planeada](site-recovery-failover.md#run-an-unplanned-failover). Una vez completada la replicación inicial puede ejecutar una conmutación por error no planeada desde AWS a Azure para cada máquina virtual. Si lo desea, puede crear un plan de recuperación y una conmutación por error no planeada para migrar varias máquinas virtuales desde AWS a Azure. [Obtenga más información](site-recovery-create-recovery-plans.md) sobre los planes de recuperación.
		
## Pasos siguientes

Obtenga más información sobre otros escenarios de replicación en [¿Qué es Azure Site Recovery?](site-recovery-overview.md)

<!---HONumber=AcomDC_0323_2016-->