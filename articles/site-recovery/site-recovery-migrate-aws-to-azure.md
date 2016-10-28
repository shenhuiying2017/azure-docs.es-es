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
	ms.date="08/22/2016"
	ms.author="raynew"/>

#  Migrar máquinas virtuales de Windows en Amazon Web Services (AWS) a Azure con Azure Site Recovery

## Información general

Bienvenido a Azure Site Recovery. Use este artículo para migrar instancias de Windows que se ejecutan en AWS a Azure con Site Recovery. Antes de comenzar, tenga en cuenta lo siguiente:

- Azure tiene dos modelos de implementación diferentes para crear y trabajar con recursos: Azure Resource Manager y el clásico. Azure también tiene dos portales: el Portal de Azure clásico que admite el modelo de implementación clásico y el Portal de Azure que es compatible con ambos modelos de implementación. Los pasos básicos para la migración son los mismos si va a configurar Site Recovery en el Resource Manager o en EL clásico. Sin embargo, las instrucciones de la interfaz de usuario y las capturas de pantalla de este artículo son relevantes para el portal de Azure.
- **Actualmente solo puede migrar de AWS a Azure. Puede conmutar por error VM de AWS a Azure, pero no puede volver a conmutar por recuperación. No hay ninguna replicación en curso.**
- Las instrucciones de migración de este artículo se basan en las directrices para la replicación de una máquina física en Azure. Incluye vínculos a los pasos en [Replicar VM de VMware o servidores físicos en Azure](site-recovery-vmware-to-azure.md), que describe cómo replicar un servidor físico en el Portal de Azure.
- Si está configurando Site Recovery en el portal clásico, siga las instrucciones detalladas de [este artículo](site-recovery-vmware-to-azure-classic.md). **Ya no debe usar** las instrucciones que se describen en este [artículo heredado](site-recovery-vmware-to-azure-classic-legacy.md).

Publique cualquier comentario o pregunta que tenga en la parte inferior de este artículo, o bien en el [foro de Servicios de recuperación de Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)


## Requisitos previos

Requisitos para realizar esta implementación

- **Servidor de configuración**: una máquina virtual local que ejecute Windows Server 2012 R2 y que actúe como servidor de configuración. Instale los otros componentes de Site Recovery (incluido el servidor de proceso y el servidor de destino maestro) también en esta VM. Obtenga más información sobre la [arquitectura del escenario](site-recovery-vmware-to-azure.md#scenario-architecture) y [requisitos previos del servidor de configuración](site-recovery-vmware-to-azure.md#configuration-server-prerequisites).
- **Instancias de VM EC2**: las instancias que ejecutan Windows que desea migrar.

## Pasos de implementación

Esta sección describe los pasos de implementación en el nuevo Portal de Azure. Si necesita estos pasos de implementación para Site Recovery en el Portal clásico, consulte [este artículo](site-recovery-vmware-to-azure-classic.md).

1. [Cree un almacén](site-recovery-vmware-to-azure.md#create-a-recovery-services-vault).
2. [Implemente un servidor de configuración](site-recovery-vmware-to-azure.md#step-2-set-up-the-source-environment).
3. Cuando haya implementado el servidor de configuración, valide que se puede comunicar con las máquinas virtuales que quiere migrar.
4. [Configure las opciones de replicación](site-recovery-vmware-to-azure.md#step-4-set-up-replication-settings). Cree una directiva de replicación y asígnela al servidor de configuración.
5. [Instale Mobility Service](site-recovery-vmware-to-azure.md#step-6-replication-application). Cada máquina virtual que desea proteger necesita tener instalado el servicio de Movilidad. Este servicio envía datos al servidor de procesos. El servicio de Movilidad puede instalarse manualmente o insertarse e instalarse automáticamente mediante el servidor de procesos cuando está habilitada la protección para la máquina virtual. Las reglas de firewall de instancias de EC2 que desea migrar deben configurarse para permitir la instalación de inserción de este servicio. El grupo de seguridad para las instancias de EC2 debe tener las siguientes reglas:

	![reglas de firewall](./media/site-recovery-migrate-aws-to-azure/migrate-firewall.png)

6. [Habilite la replicación](site-recovery-vmware-to-azure.md#enable-replication). Habilite la replicación para las VM que desea migrar. Puede detectar las instancias EC2 con las direcciones IP privada que se pueden obtener desde la consola EC2.
7. [Ejecute una conmutación por error no planeada](site-recovery-failover.md#run-an-unplanned-failover). Una vez completada la replicación inicial puede ejecutar una conmutación por error no planeada desde AWS a Azure para cada VM. Si lo desea, puede crear un plan de recuperación y una conmutación por error no planeada para migrar varias máquinas virtuales desde AWS a Azure. [Obtenga más información](site-recovery-create-recovery-plans.md) sobre los planes de recuperación.

## Pasos siguientes

Obtenga más información sobre otros escenarios de replicación en [¿Qué es Azure Site Recovery?](site-recovery-overview.md)

<!---HONumber=AcomDC_0824_2016-->