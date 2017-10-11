---
title: "Réplica de máquinas virtuales de Azure entre regiones de Azure | Microsoft Docs"
description: "Resume los pasos necesarios para replicar máquinas virtuales de Azure entre regiones de Azure con el servicio de Azure Site Recovery en Azure portal"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmon
editor: 
ms.assetid: 6dd36239-4363-4538-bf80-a18e71b8ec67
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2017
ms.author: raynew
ms.openlocfilehash: 9258613161a61e36b1d0c5796d5763c916d66859
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="replicate-azure-vms-between-regions-with-azure-site-recovery"></a>Replicación de máquinas virtuales de Azure entre regiones con Azure Site Recovery

>Este artículo proporciona información general sobre los pasos necesarios para replicar Azure Virtual Machines (VM) en una región de Azure en máquinas virtuales de Azure situadas en una región distinta. 

>[!NOTE]
>
> La replicación de la máquina virtual de Azure actualmente se encuentra en versión preliminar.

Cualquier comentario o pregunta que tenga puede publicarlo en la parte inferior de este artículo, o bien en el [foro de Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="step-1-review-architecture"></a>Paso 1: Revisión de la arquitectura

Antes de empezar la implementación, revise la arquitectura del escenario y los componentes que necesita implementar.

Vaya al [paso 1: revisión de la arquitectura](azure-to-azure-walkthrough-architecture.md)


## <a name="step-2-review-prerequisites"></a>Paso 2: revisión de los requisitos previos

Compruebe que tiene los requisitos previos de Azure en sitios, incluidos una suscripción, redes virtuales, cuentas de almacenamiento y requisitos de la máquina virtual.

Vaya a [Paso 2: Comprobación de los requisitos previos y las limitaciones](azure-to-azure-walkthrough-prerequisites.md)


## <a name="step-3-plan-networking"></a>Paso 3: Planeamiento de redes

Compruebe que la conectividad de salida está configurada en máquinas virtuales de Azure que desee replicar y que se configuran las conexiones desde el entorno local.

Vaya al [paso 4: planeamiento de las redes](azure-to-azure-walkthrough-network.md)



## <a name="step-4-create-a-vault"></a>Paso 4: Creación de un almacén 

Debe configurar un almacén de Recovery Services para organizar y administrar la replicación y especificar la región de origen.

Vaya al [Paso 4: Creación de un almacén](azure-to-azure-walkthrough-vault.md)


## <a name="step-5-enable-replication"></a>Paso 5: Habilitamiento de la replicación


Para habilitar la replicación, configure opciones de ubicación de destino y una directiva de replicación y seleccione las máquinas virtuales de Azure que desea replicar. Después de habilitarla, se produce la replicación inicial de la máquina virtual.

Vaya a [Paso 5: Habilitación de la replicación](azure-to-azure-walkthrough-enable-replication.md)


## <a name="step-6-run-a-test-failover"></a>Paso 6: Ejecución de una conmutación por error de prueba

Una vez que termina la replicación inicial y que se está ejecutando la replicación diferencial, puede ejecutar una conmutación por error de prueba para asegurarse de que todo funciona según lo previsto.

Vaya a [Paso 6: Ejecución de una conmutación por error de prueba](azure-to-azure-walkthrough-test-failover.md)


