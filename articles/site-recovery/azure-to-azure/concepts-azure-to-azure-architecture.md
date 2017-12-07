---
title: "Revise la arquitectura para la replicación de máquinas virtuales de Azure entre regiones de Azure | Microsoft Docs"
description: "En este artículo se proporciona una visión general de los componentes y la arquitectura usados al replicar máquinas virtuales de Azure entre regiones de Azure mediante el servicio de Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/10/2017
ms.author: raynew
ms.openlocfilehash: a449969e627c67660973ccf17123958678dd8844
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/30/2017
---
# <a name="azure-to-azure-replication-architecture"></a>Arquitectura de replicación de Azure en Azure


En este artículo se describen la arquitectura y los procesos que se usan al replicar, conmutar por error y recuperar máquinas virtuales de Azure entre regiones de Azure, mediante el servicio [Azure Site Recovery](../site-recovery-overview.md).

>[!NOTE]
>La replicación de máquinas virtuales de Azure con el servicio de Site Recovery está actualmente en versión preliminar.



## <a name="architectural-components"></a>Componentes de la arquitectura

En el siguiente gráfico se proporciona una visión de alto nivel de un entorno de máquina virtual de Azure en una región específica (en este ejemplo, la ubicación Este de EE. UU.). En un entorno de máquina virtual de Azure:
- Las aplicaciones pueden ejecutarse en máquinas virtuales con discos repartidos entre cuentas de almacenamiento.
- Las máquinas virtuales pueden incluirse en una o más subredes dentro de una red virtual.


**Replicación de Azure en Azure**

![Entorno de cliente](./media/concepts-azure-to-azure-architecture/source-environment.png)

## <a name="replication-process"></a>Proceso de replicación

### <a name="step-1"></a>Paso 1

Cuando se habilita la replicación de maquinas virtuales de Azure, los recursos que se muestran a continuación se crean de forma automática en la región de destino, en función de la configuración de la región de origen. Puede personalizar la configuración de los recursos de destino según sea necesario. 

![Habilitación del proceso de replicación, paso 1](./media/concepts-azure-to-azure-architecture/enable-replication-step-1.png)

**Recurso** | **Detalles**
--- | ---
**Grupo de recursos de destino** | El grupo de recursos a los que pertenecen las máquinas virtuales replicadas después de la conmutación por error.
**Red virtual de destino** | La red virtual en el que se encuentran las máquinas virtuales replicadas después de la conmutación por error. Se crea una asignación de red entre las redes virtuales de origen y de destino y viceversa.
**Cuentas de almacenamiento en caché** | Antes de que los cambios en las máquinas virtuales de origen se repliquen en la cuenta de almacenamiento de destino, se realiza un seguimiento de ellos y se envían a la cuenta de almacenamiento en caché de la ubicación de destino. De esta forma las aplicaciones de producción que se ejecutan en la máquina virtual resultan mínimamente afectadas.
**Cuentas de almacenamiento de destino**  | Las cuentas de almacenamiento de la ubicación de destino en la que se replican los datos.
**Conjuntos de disponibilidad de destino**  | Los conjuntos de disponibilidad en los que se encuentran las máquinas virtuales replicadas tras la conmutación por error.

### <a name="step-2"></a>Paso 2

Cuando la replicación está habilitada, la extensión Mobility Service de Site Recovery se instala automáticamente en la máquina virtual:

1. La máquina virtual está registrada en Site Recovery.

2. La replicación continua está configurada en la máquina virtual. Las escrituras de datos en los discos de máquina virtual se transfieren continuamente a la cuenta de almacenamiento en caché de la ubicación de origen.

   ![Habilitación del proceso de replicación, paso 2](./media/concepts-azure-to-azure-architecture/enable-replication-step-2.png)

  
 Site Recovery no necesita nunca conectividad de entrada a la máquina virtual. Solo se necesita conectividad de salida a las direcciones URL e IP del servicio de Site Recovery, a las direcciones URL o IP de autenticación de Office 365 y a las direcciones IP de la cuenta de almacenamiento en caché.

### <a name="step-3"></a>Paso 3

Una vez que la replicación continua está en curso, las escrituras en disco se transfieren inmediatamente a la cuenta de almacenamiento en caché. Site Recovery procesa los datos y los envía a la cuenta de almacenamiento de destino. Una vez procesados los datos, cada pocos minutos se generan los puntos de recuperación en la cuenta de almacenamiento de destino.

## <a name="failover-process"></a>Proceso de conmutación por error

Cuando se inicia una conmutación por error, las máquinas virtuales se crean en el grupo de recursos de destino, la red virtual de destino, la subred de destino y el conjunto de disponibilidad de destino. Durante una conmutación por error, puede usar cualquier punto de recuperación.

![Proceso de conmutación por error](./media/concepts-azure-to-azure-architecture/failover.png)

## <a name="next-steps"></a>Pasos siguientes

Siga la [guía de inicio rápido](azure-to-azure-quickstart.md) para habilitar la replicación de una máquina virtual de Azure en una región secundaria.

