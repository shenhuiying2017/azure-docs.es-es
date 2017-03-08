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
ms.sourcegitcommit: c33b69dbdccd2a894b494175dec2bd423af478b1
ms.openlocfilehash: 61909f588077f3bbdbaf368db1a9138a3b28b117
ms.lasthandoff: 03/02/2017


---
# <a name="migrate-virtual-machines-in-amazon-web-services-aws-to-azure-with-azure-site-recovery"></a>Migrar máquinas virtuales de Amazon Web Services (AWS) a Azure con Azure Site Recovery

En este artículo se describe cómo migrar instancias de AWS Windows a máquinas virtuales de Azure con el servicio [Azure Site Recovery](site-recovery-overview.md).

La migración es realmente una conmutación por error de AWS a Azure. No podrá conmutar por recuperación máquinas en AWS y no habrá ninguna replicación en curso. En este artículo se describen los pasos necesarios para realizar la migración en Azure Portal; se basan en las instrucciones para [replicar una máquina física en Azure](site-recovery-vmware-to-azure.md).

Publique cualquier comentario o pregunta que tenga en la parte inferior de este artículo, o bien en el [foro de Servicios de recuperación de Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)

## <a name="supported-operating-systems"></a>Sistemas operativos compatibles

Site Recovery puede usarse para migrar instancias de EC2 que se ejecuten en cualquiera de los siguientes sistemas operativos:

- Windows (solo&64; bits)
    - Windows Server 2008 R2 SP1 + (solo controladores de Citrix PV o de AWS PV). **No se admiten las instancias que ejecutan controladores de RedHat PV**) Windows Server 2012 Windows Server 2012 R2
- Linux (solo&64; bits)
    - Red Hat Enterprise Linux 6.7 (solo instancias virtualizadas de HVM)

## <a name="prerequisites"></a>Requisitos previos

Requisitos para realizar esta implementación

* **Servidor de configuración**: una máquina virtual de Amazon EC2 que ejecute Windows Server 2012 R2 implementada como servidor de configuración. De forma predeterminada, los demás componentes de Azure Site Recovery (el servidor de procesos y el servidor de destino maestro) se instalan al implementar el servidor de configuración. En este artículo se describen los pasos necesarios para realizar la migración en Azure Portal; se basan en las instrucciones para [obtener más información](site-recovery-components.md#vmware-replication-to-azure).

* **Instancias de EC2**: las instancias de máquinas virtuales Amazon EC2 que quiere migrar.

## <a name="deployment-steps"></a>Pasos de implementación

1. Creación de un almacén de Servicios de recuperación

2. El grupo de seguridad para las instancias de EC2 debe tener las siguientes reglas configuradas: ![Reglas](./media/site-recovery-migrate-aws-to-azure/migration_pic1.png)

3. En la misma nube privada virtual de Amazon que las instancias de EC2, implemente un servidor de configuración ASR. Consulte los requisitos previos de VMWare y los requisitos físicos de Azure para conocer los requisitos de implementación del servidor de configuración ![DeployCS](./media/site-recovery-migrate-aws-to-azure/migration_pic2.png)

4.    Una vez implementado el servidor de configuración en AWS y después de registrarlo con el almacén de Recovery Services, debe aparecer el servidor de configuración y el de procesos en la infraestructura de Site Recovery como se muestra a continuación: ![CSinVault](./media/site-recovery-migrate-aws-to-azure/migration_pic3.png)
  >[!NOTE]
  >Puede que tarde hasta 15 minutos en aparecer el servidor de configuración y el de procesos
  >

5. Cuando haya implementado el servidor de configuración, valide que se puede comunicar con las máquinas virtuales que quiere migrar.

6. [Configuración de opciones de replicación](site-recovery-setup-replication-settings-vmware.md)

7. Habilite la replicación: Habilite la replicación para las máquinas virtuales que desea migrar. Puede detectar las instancias EC2 con las direcciones IP privada que se pueden obtener desde la consola EC2.
![SelectVM](./media/site-recovery-migrate-aws-to-azure/migration_pic4.png)
8. Una vez que se protegieron las instancias de EC2 y se ha completado la replicación en Azure, [ejecute una conmutación por error de prueba](site-recovery-test-failover-to-azure.md) para validar el rendimiento de su aplicación en Azure ![TFI](./media/site-recovery-migrate-aws-to-azure/migration_pic5.png)

9. Ejecute una conmutación por error desde AWS a Azure para cada máquina virtual. Si lo desea, puede crear un plan de recuperación y ejecutar una conmutación por error para migrar varias máquinas virtuales desde AWS a Azure. [Obtenga más información](site-recovery-create-recovery-plans.md) sobre los planes de recuperación.

10. Para la migración, no es necesario ejecutar una conmutación por error. En su lugar, seleccione la opción Completar migración de cada máquina que desea migrar. La acción Completar migración termina el proceso de migración, quita la replicación de la máquina y se detiene la facturación de la máquina en Site Recovery.![Migrar](./media/site-recovery-migrate-aws-to-azure/migration_pic6.png)

