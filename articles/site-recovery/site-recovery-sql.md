---
title: "Replicación de aplicaciones con SQL Server y Azure Site Recovery | Microsoft Docs"
description: "En este artículo se describe cómo replicar SQL Server mediante Azure Site Recovery, una de las funcionalidades de recuperación ante desastres de SQL Server."
services: site-recovery
documentationcenter: 
author: prateek9us
manager: gauravd
editor: 
ms.assetid: 9126f5e8-e9ed-4c31-b6b4-bf969c12c184
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2017
ms.author: pratshar
ms.openlocfilehash: e53f60979e01a0eabe118d3ae6457a61bd4b0ded
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2017
---
# <a name="protect-sql-server-using-sql-server-disaster-recovery-and-azure-site-recovery"></a>Proteger SQL Server con la recuperación ante desastres de SQL Server y Azure Site Recovery

En este artículo se describe cómo proteger el back-end de SQL Server de una aplicación con una combinación de tecnologías de continuidad empresarial y recuperación ante desastres (BCDR) de SQL Server y [Azure Site Recovery](site-recovery-overview.md).

Antes de empezar, asegúrese de que comprende las funcionalidades de recuperación ante desastres de SQL Server, incluidos los clústeres de conmutación por error, los grupos de disponibilidad AlwaysOn, la creación de reflejo de la base de datos y el trasvase de registros.


## <a name="sql-server-deployments"></a>Implementaciones de SQL Server

Muchas cargas de trabajo usan SQL Server como base que, además, se puede integrar con aplicaciones como SharePoint, Dynamics y SAP para implementar servicios de datos.  SQL Server se puede implementar de varias maneras:

* **Servidor SQL independiente**: SQL Server y todas las bases de datos se hospedan en una sola máquina (física o virtual). Cuando se virtualiza, la agrupación en clústeres de host se utiliza para conseguir una elevada disponibilidad local. No se implementa alta disponibilidad de nivel de invitado.
* **Instancias de agrupación en clústeres de conmutación por error de SQL Server**: dos o más nodos que ejecutan instancias de SQL Server con discos compartidos se configuran en un clúster de conmutación por error de Windows. Si un nodo está inactivo, el clúster puede conmutar por error SQL Server en otra instancia. Esta configuración normalmente se usa para implementar la alta disponibilidad en un sitio principal. Esta implementación no protege frente a errores o una interrupción en la capa de almacenamiento compartido. Un disco compartido se puede implementar con iSCSI, canal de fibra o VHDx compartido.
* **Grupos de disponibilidad AlwaysOn de SQL**: se configuran dos o más nodos en un clúster no compartido, con bases de datos SQL Server configuradas en un grupo de disponibilidad, con replicación sincrónica y conmutación por error automática.

 En este artículo se aprovechan las siguientes tecnologías nativas de recuperación ante desastres de SQL para recuperar bases datos en un sitio de remoto:

* Grupos de disponibilidad AlwayOn de SQL para proponer recuperación ante desastres para ediciones empresariales de SQL 2012 o 2014.
* Reflejo de base de datos SQL en modo de alta seguridad para SQL Server Standard Edition (cualquier versión) o SQL Server 2008 R2.

## <a name="site-recovery-support"></a>Compatibilidad de Site Recovery

### <a name="supported-scenarios"></a>Escenarios admitidos
Site Recovery puede proteger SQL Server como se resume en la tabla.

**Escenario** | **En un sitio secundario** | **En Azure**
--- | --- | ---
**Hyper-V** | Sí | Sí
**VMware** | Sí | Sí
**Servidor físico** | Sí | Sí

### <a name="supported-sql-server-versions"></a>Versiones admitidas de SQL Server
Se admiten estas versiones de SQL Server en los escenarios admitidos:

* SQL Server 2016 Enterprise y Standard
* SQL Server 2014 Enterprise y Standard
* SQL Server 2012 Enterprise y Standard
* SQL Server 2008 R2 Enterprise y Standard

### <a name="supported-sql-server-integration"></a>Integración de SQL Server admitida

Site Recovery se puede integrar con las tecnologías nativas de SQL Server BCDR resumidas en la tabla para proporcionar una solución de recuperación ante desastres.

**Característica** | **Detalles** | **SQL Server** |
--- | --- | ---
**Grupos de disponibilidad AlwaysOn** | Se ejecutan varias instancias independientes de SQL Server, cada una en un clúster de conmutación por error que tiene varios nodos.<br/><br/>Las bases de datos se pueden agrupar en grupos de conmutación por error que se puede copiar (reflejar) en instancias de SQL Server para que no se necesite ningún almacenamiento compartido.<br/><br/>Proporciona recuperación ante desastres entre un sitio principal y uno o más sitios secundarios. Dos nodos pueden configurarse en un clúster no compartido con Bases de datos de SQL Server configurado en un grupo de disponibilidad con replicación sincrónica y conmutación por error automática. | SQL Server 2014 y 2012 Enterprise Edition
**Clústeres de conmutación por error (FCI AlwaysOn)** | SQL Server aprovecha la agrupación en clústeres de conmutación por error de Windows para conseguir alta disponibilidad de las cargas de trabajo locales de SQL Server.<br/><br/>Los nodos que ejecutan instancias de SQL Server con discos compartidos se configuran en un clúster de conmutación por error. Si una instancia está inactiva, el clúster conmuta por error a una diferente.<br/><br/>El clúster no protege frente a errores o interrupciones en el almacenamiento compartido. El disco compartido se puede implementar con iSCSI, canal de fibra o VHDX compartido. | SQL Server Enterprise Edition<br/><br/>SQL Server Standard Edition (limitada a solo dos nodos)
**Creación de un reflejo de la base de datos (modo de alta seguridad)** | Protege una sola base de datos en una única copia secundaria. Disponible en modos de replicación de seguridad alta (sincrónica) y de alto rendimiento (asincrónica). No requiere un clúster de conmutación por error. | SQL Server 2008 R2<br/><br/>Todas las ediciones de SQL Server Enterprise
**SQL Server independiente** | SQL Server y la base de datos se hospedan en un único servidor (físico o virtual). Los clústeres de host se utilizan para lograr alta disponibilidad, si el servidor virtual. Sin alta disponibilidad de nivel de invitado. | Edición Enterprise o Standard

## <a name="deployment-recommendations"></a>Recomendaciones de implementación

En la siguiente tabla se resumen nuestras recomendaciones para integrar las tecnologías de SQL Server BCDR con Site Recovery.

| **Versión** | **Edición** | **Implementación** | **De local a local** | **De local a Azure** |
| --- | --- | --- | --- | --- |
| SQL Server 2014 o 2012 |Enterprise |Instancia de clúster de conmutación por error |Grupos de disponibilidad AlwaysOn |Grupos de disponibilidad AlwaysOn |
|| Enterprise |Grupos de disponibilidad AlwaysOn para alta disponibilidad |Grupos de disponibilidad AlwaysOn |Grupos de disponibilidad AlwaysOn | |
|| Standard |Instancia de clúster de conmutación por error (FCI) |Replicación de Site Recovery con un reflejo local |Replicación de Site Recovery con un reflejo local | |
|| Enterprise o Standard |Independiente |Replicación de Site Recovery |Replicación de Site Recovery | |
| SQL Server 2008 R2 o 2008 |Enterprise o Standard |Instancia de clúster de conmutación por error (FCI) |Replicación de Site Recovery con un reflejo local |Replicación de Site Recovery con un reflejo local |
|| Enterprise o Standard |Independiente |Replicación de Site Recovery |Replicación de Site Recovery | |
| SQL Server (cualquier versión) |Enterprise o Standard |Instancia de clúster de conmutación por error: aplicación de DTC |Replicación de Site Recovery |No compatible |

## <a name="deployment-prerequisites"></a>Requisitos previos de implementación

* La implementación local de SQL Server ejecuta una versión compatible de SQL Server. Normalmente, también necesitará Active Directory para SQL Server.
* Requisitos para el escenario que desea implementar. Obtenga más información sobre los requisitos de compatibilidad para la [replicación en Azure](site-recovery-support-matrix-to-azure.md) y la replicación [local](site-recovery-support-matrix.md), y los [requisitos previos de implementación](site-recovery-prereq.md).
* Para configurar la recuperación de Azure, ejecute la herramienta [Evaluación de preparación de máquinas virtuales de Azure](http://www.microsoft.com/download/details.aspx?id=40898) en sus máquinas virtuales de SQL Server para asegurarse de que son compatibles con Azure y Site Recovery.

## <a name="set-up-active-directory"></a>Configuración de Active Directory

Configure Active Directory en el sitio de recuperación secundario para que SQL Server se ejecute correctamente.

* **Empresas pequeñas**: con un pequeño número de aplicaciones y un solo controlador de dominio para el sitio local, si desea conmutar por error todo el sitio, se recomienda utilizar la replicación de Site Recovery para replicar el controlador de dominio en el centro de datos secundario o en Azure.
* **Empresas medianas y grandes**: si tiene un gran número de aplicaciones, un bosque de Active Directory y desea que la conmutación por error se realice por aplicaciones o cargas de trabajo, se recomienda configurar un controlador de dominio adicional en el centro de datos secundario o en Azure. Si usa grupos de disponibilidad AlwaysOn para recuperar en un sitio remoto, se recomienda configurar otro controlador de dominio adicional en el sitio secundario o en Azure, que se utilizará para la instancia de SQL Server recuperada.

Las instrucciones de este artículo suponen que un controlador de dominio está disponible en la ubicación secundaria. [Más información](site-recovery-active-directory.md) sobre la protección de Active Directory con Site Recovery.


## <a name="integrate-with-sql-server-always-on-for-replication-to-azure"></a>Integración con SQL Server AlwaysOn para la replicación en Azure

Esto es lo que debe hacer:

1. Importe scripts en su cuenta de Azure Automation. Esta cuenta contiene los scripts para conmutar por error grupos de disponibilidad de SQL en una [máquina virtual de Resource Manager](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAG.ps1) y una [máquina virtual clásica](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAGClassic.ps1).

    [![Implementación en Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)


1. Agregue ASR-SQL-FailoverAG como acción previa del primer grupo del plan de recuperación.

1. Siga las instrucciones disponibles en el script para crear una variable de automatización para proporcionar el nombre de los grupos de disponibilidad.

### <a name="steps-to-do-a-test-failover"></a>Pasos para realizar una conmutación por error de prueba

SQL AlwaysOn no admite de forma nativa la conmutación por error de prueba. Por consiguiente, es recomendable que:

1. Configure [Azure Backup](../backup/backup-azure-vms.md) en la máquina virtual que hospeda la réplica del grupo de disponibilidad en Azure.

1. Antes de desencadenar la conmutación por error del plan de recuperación, recupere la máquina virtual a partir de la copia de seguridad realizada en el paso anterior.

    ![Restauración de datos de Azure Backup ](./media/site-recovery-sql/restore-from-backup.png)

1. [Fuerce un quórum](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/force-a-wsfc-cluster-to-start-without-a-quorum#PowerShellProcedure) en la máquina virtual restaurada de la copia de seguridad.

1. Actualice la IP del agente de escucha a una dirección IP disponible en la red de conmutación por error de prueba.

    ![Actualización de la dirección IP del agente de escucha](./media/site-recovery-sql/update-listener-ip.png)

1. Conecte el agente de escucha.

    ![Conexión del agente de escucha](./media/site-recovery-sql/bring-listener-online.png)

1. Cree un equilibrador de carga con una dirección IP creada en el grupo de direcciones IP de front-end correspondiente a cada agente de escucha de grupo de disponibilidad y con la máquina virtual SQL agregada en el grupo de back-end.

     ![Creación del equilibrador de carga: grupo de direcciones IP de front-end ](./media/site-recovery-sql/create-load-balancer1.png)

    ![Creación del equilibrador de carga: grupo de direcciones IP de back-end ](./media/site-recovery-sql/create-load-balancer2.png)

1. Realice una conmutación por error del plan de recuperación.

### <a name="steps-to-do-a-failover"></a>Pasos para realizar una conmutación por error

Una vez que ha agregado el script en el plan de recuperación y ha validado el plan de recuperación mediante una conmutación por error de prueba, puede realizar una conmutación por error del plan de recuperación.


## <a name="integrate-with-sql-server-always-on-for-replication-to-a-secondary-on-premises-site"></a>Integración con SQL Server Always On para la replicación en un sitio local secundario

Si el servidor SQL Server utiliza grupos de disponibilidad para alta disponibilidad (o un FCI), se recomienda utilizar también grupos de disponibilidad en el sitio de recuperación. Tenga en cuenta que esto es aplicable a aplicaciones que no utilizan transacciones distribuidas.

1. [Configure bases de datos](https://msdn.microsoft.com/library/hh213078.aspx) en grupos de disponibilidad.
1. Cree una red virtual en el sitio secundario.
1. Configure una conexión VPN de sitio a sitio entre la red virtual y el sitio principal.
1. Cree una máquina virtual en el sitio de recuperación e instale SQL Server en ella.
1. Amplíe los grupos de disponibilidad AlwaysOn existentes a la nueva VM con SQL Server. Configure esta instancia de SQL Server como una copia de réplica asincrónica.
1. Cree un agente de escucha del grupo de disponibilidad o actualice el agente de escucha existente para incluir la máquina virtual de réplica asincrónica.
1. Asegúrese de que la granja de aplicaciones está configurada con el agente de escucha. Si realiza la configuración mediante el nombre del servidor de la base de datos, actualícelo para utilizar el agente de escucha para que no tenga que volver a configurar después de la conmutación por error.

Para las aplicaciones que usan transacciones distribuidas, le recomendamos implementar Site Recovery con la [replicación entre sitios de VMware o servidores físicos](site-recovery-vmware-to-vmware.md).

### <a name="recovery-plan-considerations"></a>Consideraciones del plan de recuperación
1. Agregue este script de ejemplo a la biblioteca de VMM en los sitios principales y secundarios.

        Param(
        [string]$SQLAvailabilityGroupPath
        )
        import-module sqlps
        Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force

1. Al crear un plan de recuperación para la aplicación, agregue una acción previa al paso de script Group-1, que invoca el script para realizar la conmutación por error de grupos de disponibilidad.

## <a name="protect-a-standalone-sql-server"></a>Protección de un servidor SQL Server independiente

En este escenario, se recomienda que utilice la replicación de Site Recovery para proteger la máquina de SQL Server. Los pasos exactos dependerán de si SQL Server está en una máquina virtual o en un servidor físico y si desea replicar en Azure o en un sitio local secundario. Más información acerca de [escenarios de Site Recovery](site-recovery-overview.md).

## <a name="protect-a-sql-server-cluster-standard-editionwindows-server-2008-r2"></a>Protección de un clúster de SQL Server (Standard Edition o Windows Server 2008 R2)

Para un clúster que ejecuta SQL Server Standard Edition o SQL Server 2008 R2, se recomienda utilizar la replicación de Site Recovery para proteger SQL Server.

### <a name="on-premises-to-on-premises"></a>De local a local

* Si la aplicación usa transacciones distribuidas, se recomienda implementar [Site Recovery con la replicación de SAN](site-recovery-vmm-san.md) en caso de un entorno de Hyper-V y [VMware/servidor físico a VMware](site-recovery-vmware-to-vmware.md) si se trata de un entorno de VMware.
* Para las aplicaciones que no sean DTC, use el enfoque anterior para recuperar el clúster como un servidor independiente mediante el uso de un reflejo de la base de datos local de seguridad alta.

### <a name="on-premises-to-azure"></a>De local a Azure

Site Recovery no proporciona la compatibilidad con clústeres invitados al replicar en Azure. SQL Server tampoco proporciona una solución de recuperación ante desastres de bajo costo para la edición Standard. En este escenario, se recomienda proteger el clúster de SQL Server local en un servidor SQL Server independiente y recuperarlo en Azure.

1. Configure una instancia de SQL Server independiente adicional en el sitio local.
1. Configure esta instancia para actuar como un reflejo para las bases de datos que desea proteger. Configure el reflejo en modo de alta seguridad.
1. Configure Site Recovery en el sitio local para [Hyper-V](site-recovery-hyper-v-site-to-azure.md) o para [máquinas virtuales de VMware o servidores físicos](site-recovery-vmware-to-azure-classic.md).
1. Utilice la replicación de Site Recovery para replicar la nueva instancia de SQL Server en Azure. Como es una copia de alta seguridad de reflejo, se sincronizará con el clúster principal, pero se puede replicar en Azure con la replicación de Site Recovery.


![Clúster estándar](./media/site-recovery-sql/standalone-cluster-local.png)

### <a name="failback-considerations"></a>Consideraciones de la conmutación por recuperación

Para los clústeres de SQL Server Standard, la conmutación por recuperación después de una conmutación por error no planeada requiere realizar una copia de seguridad de SQL Server y una restauración de la instancia reflejada en el clúster original y, a continuación, restablecer el reflejo.

## <a name="next-steps"></a>Pasos siguientes
[Obtenga información](site-recovery-components.md) acerca de la arquitectura de Site Recovery.
