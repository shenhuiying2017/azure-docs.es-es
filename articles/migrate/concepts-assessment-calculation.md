---
title: "Cálculos de evaluación de Azure Migrate | Microsoft Docs"
description: "En este artículo se proporciona una introducción a los cálculos de evaluación del servicio Azure Migrate."
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: raynew
ms.openlocfilehash: b8075f0e1149a6fc5194347fc34e2a16d5eb2ffc
ms.sourcegitcommit: d6984ef8cc057423ff81efb4645af9d0b902f843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/05/2018
---
# <a name="assessment-calculations"></a>Cálculos de evaluación

[Azure Migrate](migrate-overview.md) evalúa las cargas de trabajo locales para su migración a Azure. En este artículo se proporciona información sobre cómo se calculan las evaluaciones.



## <a name="overview"></a>Información general

Una evaluación de Azure Migrate tiene tres etapas. La evaluación comienza con un análisis de idoneidad, seguido de las estimaciones de ajuste de tamaño según el rendimiento y, por último, la estimación del costo mensual. Una máquina solo se mueve a una fase posterior si aprueba la anterior. Por ejemplo, si no supera la comprobación de idoneidad de Azure, se marca como no adecuada para Azure, y el ajuste de tamaño y los costos no se calculan. 


## <a name="azure-suitability-analysis"></a>Análisis de idoneidad de Azure

Las máquinas que quiera migrar a Azure deben cumplir las limitaciones y los requisitos de Azure. Por ejemplo, si un disco de máquina virtual local tiene más de 4 TB, no se puede hospedar en Azure. Las comprobaciones de cumplimiento de Azure se resumen en la tabla siguiente. 

**Comprobación** | **Detalles**
--- | ---
**Tipo de arranque** | El tipo de arranque del disco de sistema operativo invitado debe ser BIOS y no UEFI.
**Núcleos** | El número de núcleos de las máquinas debe ser igual o menor que el número máximo de núcleos (32) admitidos en una máquina virtual de Azure.<br/><br/> Si el historial de rendimiento está disponible, Azure Migrate tiene en cuenta los núcleos utilizados para la comparación. Si se especifica un factor de confort en la configuración de evaluación, el número de núcleos utilizados se multiplica por dicho factor de confort.<br/><br/> Si no hay ningún historial de rendimiento, Azure Migrate usa los núcleos asignados sin aplicar el factor de confort.
**Memoria** | El tamaño de memoria de la máquina debe ser igual o menor que la memoria máxima (448 GB) permitida en una máquina virtual de Azure. <br/><br/> Si hay algún historial de rendimiento disponible, Azure Migrate tiene en cuenta la memoria utilizada para la comparación. Si se especifica un factor de confort, la memoria utilizada se multiplica por dicho factor de confort.<br/><br/> Si no hay ningún historial, se utiliza la memoria asignada sin aplicar el factor de confort.<br/><br/> 
**Windows Server 2003-2008 R2** | Compatibilidad con 32 bits y 64 bits.<br/><br/> Azure solo proporciona compatibilidad de mejor esfuerzo.
**Windows Server 2008 R2 con todos los Service Pack** | Compatibilidad con 64 bits.<br/><br/> Azure proporciona compatibilidad completa.
**Windows Server 2012 y todos los Service Pack** | Compatibilidad con 64 bits.<br/><br/> Azure proporciona compatibilidad completa.
**Windows Server 2012 R2 y todos los Service Pack** | Compatibilidad con 64 bits.<br/><br/> Azure proporciona compatibilidad completa.
**Windows Server 2016 y todos los Service Pack** | Compatibilidad con 64 bits.<br/><br/> Azure proporciona compatibilidad completa.
**Cliente de Windows 7 y versiones posteriores** | Compatibilidad con 64 bits.<br/><br/> Azure solo proporciona compatibilidad con la suscripción de Visual Studio.
**Linux** | Compatibilidad con 64 bits.<br/><br/> Azure proporciona compatibilidad completa con estos [sistemas operativos](../virtual-machines/linux/endorsed-distros.md).
**Disco de almacenamiento** | El tamaño asignado de un disco debe ser de 4 TB (4096 GB) o menos.<br/><br/> El número de discos conectados a la máquina debe ser 65 o menos, incluido el disco de sistema operativo. 
**Redes** | Una máquina debe tener 32 NIC o menos conectadas.


## <a name="performance-based-sizing"></a>Ajuste de tamaño según el rendimiento

Cuando una máquina se marca como adecuada para Azure, Azure Migrate le asigna un tamaño de máquina virtual en Azure con los siguientes criterios:

- **Comprobación de almacenamiento**: Azure Migrate intenta asignar cada disco conectado a la máquina a un disco en Azure:
    - Azure Migrate multiplica la E/S por segundo (IOPS) por el factor de confort. También multiplica el rendimiento (en MB/s) de cada disco por el factor de confort. De este modo, se obtiene el rendimiento y el IOPS de disco efectivo. En función de esto, Azure Migrate asigna el disco a un disco estándar o premium de Azure.
      - Si el servicio no puede encontrar un disco con el rendimiento y el IOPS necesarios, marca la máquina como no adecuada para Azure.
      - Si encuentra un conjunto de discos adecuados, Azure Migrate selecciona aquellos que admiten el método de redundancia de almacenamiento y la ubicación especificada en la configuración de evaluación.
      - Si hay varios discos aptos, selecciona el que tiene el costo más bajo.
- **Rendimiento de disco de almacenamiento**: [obtenga más información](../azure-subscription-service-limits.md#storage-limits) sobre los límites de Azure por disco y máquina virtual.
- **Tipo de disco**: Azure Migrate solo admite discos administrados.
- **Comprobación de la red**: Azure Migrate trata de encontrar una máquina virtual de Azure que pueda admitir el número de NIC de la máquina local.
    - Para ello, agrega los datos transmitidos por segundo (MB/s) de la máquina (salida de red) a todas las NIC y aplica el factor de confort al número agregado. Este número si se utiliza para buscar una máquina virtual de Azure que pueda cumplir el rendimiento de red requerido.
    - Azure Migrate toma la configuración de red de la máquina virtual y da por supuesto que se trata de una red fuera del centro de datos.
    - Si no hay datos de rendimiento de red disponibles, solo se tiene en cuenta el número de NIC para ajustar el tamaño de la máquina virtual.
- **Proceso de comprobación**: cuando se calculan los requisitos de almacenamiento y red, Azure Migrate tiene en cuenta los requisitos de proceso:
    - Si los datos de rendimiento están disponibles para la máquina virtual, examina la memoria y los núcleos utilizados, y aplica el factor de confort. En función de ese número, trata de encontrar un tamaño de máquina virtual adecuado en Azure.
    - Si no encuentra ningún tamaño adecuado, la máquina se marca como no adecuada para Azure.
    - Si encuentra un tamaño adecuado, Azure Migrate aplica los cálculos de red y almacenamiento. Después, aplica la configuración de ubicación y plan de tarifa para realizar la recomendación final de tamaño de máquina virtual.


## <a name="monthly-cost-estimation"></a>Estimación del costo mensual

Una vez completadas las recomendaciones de ajuste de tamaño, Azure Migrate calcula los costos de almacenamiento y proceso posteriores a la migración.

- **Costo de proceso**: con el tamaño recomendado de máquina virtual de Azure, Azure Migrate usa Billing API para calcular el costo mensual de la máquina virtual. El cálculo tiene en cuenta la configuración de moneda, ubicación, Software Assurance y sistema operativo. Para calcular el costo de proceso mensual total, agrega el costo a todas las máquinas.
- **Costo de almacenamiento**: el costo de almacenamiento mensual de una máquina se calcula sumando el costo mensual de todos los discos conectados a la máquina. Azure Migrate calcula los costos de almacenamiento mensuales totales agregando los costos de almacenamiento de todas las máquinas. Actualmente, el cálculo no tiene en cuenta las ofertas especificadas en la configuración de evaluación.

Los costos se muestran en la moneda especificada en la configuración de evaluación. 


## <a name="next-steps"></a>pasos siguientes

[Configuración de una evaluación para máquinas virtuales VMware locales](tutorial-assessment-vmware.md)
