---
title: "¿Cómo funciona la replicación de máquinas virtuales de Azure entre regiones de Azure en Azure Site Recovery?  | Microsoft Docs"
description: "En este artículo se proporciona una visión general de los componentes y la arquitectura usados al replicar máquinas virtuales de Azure entre regiones de Azure mediante el servicio de Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: sujayt
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 08/31/2017
ms.author: sujayt
ms.openlocfilehash: 7a3e1eb129db530295cbf28bbce1dbe48575d50d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="how-does-azure-vm-replication-work-in-site-recovery"></a>¿Cómo funciona la replicación de máquinas virtuales de Azure en Site Recovery?


En este artículo se describen los componentes y los procesos implicados en la replicación y la recuperación de máquinas virtuales (VM) de Azure de una región a otra mediante el servicio de [Azure Site Recovery](site-recovery-overview.md).

>[!NOTE]
>La replicación de máquinas virtuales de Azure con el servicio de Site Recovery está actualmente en versión preliminar.

Publique cualquier comentario en la parte inferior de este artículo, o bien en el [foro de Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="architectural-components"></a>Componentes de la arquitectura

En el siguiente diagrama se proporciona una visión de alto nivel de un entorno de máquina virtual de Azure en una región específica (en este ejemplo, la ubicación Este de EE. UU.). En un entorno de máquina virtual de Azure:
- Las aplicaciones pueden ejecutarse en máquinas virtuales con discos repartidos entre cuentas de almacenamiento.
- Las máquinas virtuales pueden incluirse en una o más subredes dentro de una red virtual.

![Entorno de cliente](./media/site-recovery-azure-to-azure-architecture/source-environment.png)

Aprenda sobre los requisitos previos de implementación y los requisitos de la [matriz de compatibilidad](site-recovery-support-matrix-azure-to-azure.md).

## <a name="replication-process"></a>Proceso de replicación

### <a name="step-1"></a>Paso 1

Cuando se habilita la replicación de máquinas virtuales de Azure en Azure Portal, se crean automáticamente en la región de destino los recursos que se muestran en el siguiente diagrama y tabla. De forma predeterminada, los recursos se crean en función de la configuración de la región de origen. Puede personalizar la configuración de destino según sea necesario. [Más información](site-recovery-replicate-azure-to-azure.md).

![Habilitación del proceso de replicación, paso 1](./media/site-recovery-azure-to-azure-architecture/enable-replication-step-1.png)

**Recurso** | **Detalles**
--- | ---
**Grupo de recursos de destino** | El grupo de recursos a los que pertenecen las máquinas virtuales replicadas después de la conmutación por error.
**Red virtual de destino** | La red virtual en el que se encuentran las máquinas virtuales replicadas después de la conmutación por error. Se crea una asignación de red entre las redes virtuales de origen y de destino y viceversa.
**Cuentas de almacenamiento en caché** | Antes de que los cambios en las máquinas virtuales de origen se repliquen en la cuenta de almacenamiento de destino, se realiza un seguimiento de ellos y se envían a la cuenta de almacenamiento en caché de la ubicación de destino. De esta forma las aplicaciones de producción que se ejecutan en la máquina virtual resultan mínimamente afectadas.
**Cuentas de almacenamiento de destino**  | Las cuentas de almacenamiento de la ubicación de destino en la que se replican los datos.
**Conjuntos de disponibilidad de destino**  | Los conjuntos de disponibilidad en los que se encuentran las máquinas virtuales replicadas tras la conmutación por error.

### <a name="step-2"></a>Paso 2

Cuando la replicación está habilitada, la extensión Mobility Service de Site Recovery se instala automáticamente en la máquina virtual. Ocurre lo siguiente:

1. La máquina virtual está registrada en Site Recovery.

2. La replicación continua está configurada en la máquina virtual. Las escrituras de datos en los discos de máquina virtual se transfieren continuamente a la cuenta de almacenamiento en caché de la ubicación de origen.

   ![Habilitación del proceso de replicación, paso 2](./media/site-recovery-azure-to-azure-architecture/enable-replication-step-2.png)

   >[!IMPORTANT]
   > Site Recovery no necesita nunca conectividad de entrada a la máquina virtual. La máquina virtual solo necesita conectividad de salida a las direcciones URL e IP del servicio de Site Recovery, a las direcciones URL o IP de autenticación de Office 365 y a las direcciones IP de la cuenta de almacenamiento en caché. Para más información, consulte el artículo [Networking guidance for replicating Azure virtual machines](site-recovery-azure-to-azure-networking-guidance.md) (Instrucciones de redes para la replicación de máquinas virtuales de Azure).

## <a name="continuous-replication-process"></a>Proceso de replicación continua

Una vez que la replicación continua está funcionando, las escrituras en disco se transfieren inmediatamente a la cuenta de almacenamiento en caché. Site Recovery procesa los datos y los envía a la cuenta de almacenamiento de destino. Una vez procesados los datos, cada pocos minutos se generan los puntos de recuperación en la cuenta de almacenamiento de destino.

## <a name="failover-process"></a>Proceso de conmutación por error

Cuando se inicia una conmutación por error, las máquinas virtuales se crean en el grupo de recursos de destino, la red virtual de destino, la subred de destino y el conjunto de disponibilidad de destino. Durante una conmutación por error, puede usar cualquier punto de recuperación.

![Proceso de conmutación por error](./media/site-recovery-azure-to-azure-architecture/failover.png)

## <a name="next-steps"></a>Pasos siguientes

- Aprenda sobre las [redes](site-recovery-azure-to-azure-networking-guidance.md) para la replicación de máquinas virtuales de Azure.
- Siga un tutorial para [replicar máquinas virtuales de Azure.](site-recovery-azure-to-azure.md)
