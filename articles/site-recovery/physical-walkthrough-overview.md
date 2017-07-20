---
title: "Replicar servidores físicos locales en Azure con Azure Site Recovery | Microsoft Docs"
description: "Proporciona información general sobre los pasos para la replicación de cargas de trabajo que se ejecutan en servidores físicos locales de Windows o Linux en Azure con el servicio Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 20122f01-929a-4675-b85b-a9b99d2618bc
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: 0a09b35e98dc0b2f5283c2a707a3a2b8ac9a39f2
ms.contentlocale: es-es
ms.lasthandoff: 06/29/2017


---
# <a name="replicate-physical-servers-to-azure-with-site-recovery"></a>Replicar servidores físicos en Azure con Site Recovery

En este artículo se proporciona información general sobre los pasos necesarios para replicar servidores físicos locales de Windows o Linux en Azure mediante el servicio [Azure Site Recovery](site-recovery-overview.md) de Azure Portal.


## <a name="step-1-review-architecture-and-prerequisites"></a>Paso 1: Revisar la arquitectura y los requisitos previos

Antes de empezar la implementación, revise la arquitectura del escenario y asegúrese de comprender todos los componentes que necesita para configurar la implementación.

Vaya a [Paso 1: Revisar la arquitectura](physical-walkthrough-architecture.md)


## <a name="step-2-review-prerequisites"></a>Paso 2: Revisar los requisitos previos

Asegúrese de satisfacer los requisitos previos de cada componente de la implementación:

- **Requisitos previos de Azure**: necesita una cuenta de Microsoft Azure, redes de Azure y cuentas de almacenamiento.
- **Componentes locales de Site Recovery**: necesita un equipo con componentes locales de Site Recovery.
- **Máquinas replicadas**: los servidores que quiera replicar deben cumplir los requisitos locales y de Azure.

Vaya a [Paso 2: Revisar los requisitos previos y las limitaciones](physical-walkthrough-prerequisites.md)

## <a name="step-3-plan-capacity"></a>Paso 3: Planear la capacidad

Si está realizando una implementación completa, debe averiguar qué recursos de replicación necesita. Si está realizando una configuración rápida para probar el entorno, puede omitir este paso.

Vaya a [Paso 3: Planear la capacidad](physical-walkthrough-capacity.md)

## <a name="step-4-plan-networking"></a>Paso 4: Planear las redes

Debe planear las redes para asegurarse de que las máquinas virtuales de Azure estén conectadas a redes después de que se produzca la conmutación por error y de que tengan la dirección IP correcta.

Vaya a [Paso 4: Planear las redes](physical-walkthrough-network.md)

##  <a name="step-5-prepare-azure-resources"></a>Paso 5: Preparar los recursos de Azure

Antes de empezar hay que configurar las redes y el almacenamiento de Azure. 

Vaya a [Paso 5: Preparar Azure](physical-walkthrough-prepare-azure.md)


## <a name="step-6-set-up-a-vault"></a>Paso 6: Configurar un almacén

Hay que configurar un almacén de Recovery Services para organizar y administrar la replicación. Al configurar el almacén, es necesario especificar lo que se quiere replicar y en dónde.

Vaya a [Paso 6: Configurar un almacén](physical-walkthrough-create-vault.md)

## <a name="step-7-configure-source-and-target-settings"></a>Paso 7: Configurar los valores de origen y destino

Hay que configurar los valores del sitio de origen y destino (Azure). La configuración de origen incluye la ejecución de la instalación unificada para instalar los componentes locales de Site Recovery.

Vaya a [Paso 7: Configurar los valores de origen y destino](physical-walkthrough-source-target.md)

## <a name="step-8-set-up-a-replication-policy"></a>Paso 8: Configurar una directiva de replicación

Tiene que configurar una directiva para especificar cómo se deben replicar los servidores físicos.

Vaya a [Paso 8: Configurar una directiva de replicación](physical-walkthrough-replication.md)

## <a name="step-9-install-the-mobility-service"></a>Paso 9: Instalación del servicio de movilidad

Tiene que instalar el Servicio de movilidad en cada servidor que quiera replicar. Hay varias maneras de configurar el servicio, con instalación de inserción o extracción.

Vaya a [Paso 9: Instalar el Servicio de movilidad](physical-walkthrough-install-mobility.md)

## <a name="step-10-enable-replication"></a>Paso 10: Habilitar la replicación

Una vez que el Servicio de movilidad se está ejecutando en un servidor, puede habilitar su replicación. Después de la habilitación, se produce la replicación inicial de la máquina virtual.

Vaya a [Paso 10: Habilitar la replicación](physical-walkthrough-enable-replication.md)

## <a name="step-11-run-a-test-failover"></a>Paso 11: Ejecutar una conmutación por error de prueba

Una vez que termina la replicación inicial y que se está ejecutando la replicación diferencial, puede ejecutar una conmutación por error de prueba para asegurarse de que todo funciona según lo previsto.

Vaya a [Paso 11: Ejecutar una conmutación por error de prueba](physical-walkthrough-test-failover.md)


