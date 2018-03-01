---
title: "Cálculos de evaluación de Azure Migrate | Microsoft Docs"
description: "En este artículo se proporciona una introducción a los cálculos de evaluación del servicio Azure Migrate."
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 06/02/2017
ms.author: raynew
ms.openlocfilehash: db09ff30ff9f3852e84162b8400572e76515230f
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/21/2018
---
# <a name="assessment-calculations"></a>Cálculos de evaluación

[Azure Migrate](migrate-overview.md) evalúa las cargas de trabajo locales para su migración a Azure. En este artículo se proporciona información sobre cómo se calculan las evaluaciones.


## <a name="overview"></a>Información general

Una evaluación de Azure Migrate tiene tres etapas. La valoración comienza con un análisis de idoneidad, seguido de las estimaciones de ajuste de tamaño y, por último, la estimación del costo mensual. Una máquina solo se mueve a una fase posterior si aprueba la anterior. Por ejemplo, si no supera la comprobación de idoneidad de Azure, se marca como no adecuada para Azure, y el ajuste de tamaño y los costos no se calculan. 


## <a name="azure-suitability-analysis"></a>Análisis de idoneidad de Azure

No todas las máquinas son adecuadas para ejecutarse en la nube dado que esta tiene sus propios requisitos y limitaciones. Azure Migrate evalúa si las máquinas son adecuadas para la migración a Azure y clasifica las máquinas en una de las siguientes categorías:
- **Preparada para Azure**: la máquina se puede migrar como está a Azure sin realizar ningún cambio. Se iniciará en Azure con compatibilidad completa con Azure.
- **Condicionalmente preparada para Azure**: la máquina puede iniciarse en Azure, pero no tendrá compatibilidad completa con Azure. Por ejemplo, una máquina con una versión anterior del sistema operativo Windows Server no se admite en Azure. Antes de migrar estas máquinas a Azure y seguir la guía de soluciones sugeridas en la valoración, debe tener cuidado para resolver los problemas de preparación antes de realizar la migración.
- **No preparada para Azure**: la máquina no se inicia en Azure. Por ejemplo, si una máquina local tiene asociado un disco con un tamaño superior a 4 TB, no se puede hospedar en Azure. Debe seguir las instrucciones de soluciones sugeridas en la valoración para solucionar el problema de preparación antes de migrar a Azure. La estimación del tamaño adecuado y del costo no se realiza en máquinas marcadas como no preparadas para Azure.
- **Preparación desconocida**: Azure Migrate no pudo encontrar la preparación de la máquina por falta de datos disponibles en vCenter Server. 

Azure Migrate revisa las propiedades de la máquina y el sistema operativo invitado para identificar la preparación para Azure de la máquina local.

### <a name="machine-properties"></a>Propiedades de la máquina
Azure Migrate Azure revisa las siguientes propiedades de la máquina virtual local para identificar si se puede ejecutar una máquina virtual en Azure.
 
**Propiedad** | **Detalles** | **Estado de la preparación para Azure**
--- | --- | ---
**Tipo de arranque** | Azure admite máquinas virtuales con el tipo de arranque de BIOS y no UEFI. | Condicionalmente preparada para Azure, si el tipo de arranque es UEFI. 
**Núcleos** | El número de núcleos de las máquinas debe ser igual o menor que el número máximo de núcleos (32) admitidos en una máquina virtual de Azure.<br/><br/> Si el historial de rendimiento está disponible, Azure Migrate tiene en cuenta los núcleos utilizados para la comparación. Si se especifica un factor de confort en la configuración de evaluación, el número de núcleos utilizados se multiplica por dicho factor de confort.<br/><br/> Si no hay ningún historial de rendimiento, Azure Migrate usa los núcleos asignados sin aplicar el factor de confort. | No está preparada si el número de núcleos es mayor que 32. 
**Memoria** | El tamaño de memoria de la máquina debe ser igual o menor que la memoria máxima (448 GB) permitida en una máquina virtual de Azure. <br/><br/> Si hay algún historial de rendimiento disponible, Azure Migrate tiene en cuenta la memoria utilizada para la comparación. Si se especifica un factor de confort, la memoria utilizada se multiplica por dicho factor de confort.<br/><br/> Si no hay ningún historial, se utiliza la memoria asignada sin aplicar el factor de confort.<br/><br/> | No está preparada si el tamaño de memoria es superior a 448 GB.
**Disco de almacenamiento** | El tamaño asignado de un disco debe ser de 4 TB (4096 GB) o menos.<br/><br/> El número de discos conectados a la máquina debe ser 65 o menos, incluido el disco de sistema operativo. | No está preparada si cualquier disco tiene un tamaño mayor de 4 TB o si hay más de 65 discos asociados a la máquina. 
**Redes** | Una máquina debe tener 32 NIC o menos conectadas. | No está preparada si la máquina tiene más de 32 NIC.

### <a name="guest-operating-system"></a>Sistema operativo invitado 
Además de las propiedades de la máquina virtual, Azure Migrate también examina el sistema operativo invitado de la máquina virtual local para identificar si se puede ejecutar en Azure.

> [!NOTE]
> Azure Migrate tiene en cuenta el sistema operativo especificado en vCenter Server para realizar el siguiente análisis. Puesto que la detección realizada por Azure Migrate está basada en el dispositivo, no hay manera de comprobar si el sistema operativo que se ejecuta dentro de la máquina virtual es el mismo que el especificado en vCenter Server.

Azure Migrate emplea la siguiente lógica para identificar la preparación de Azure de la máquina virtual en función del sistema operativo.

**Sistema operativo** | **Detalles** | **Estado de la preparación para Azure**
--- | --- | ---
Windows Server 2016 y todos los Service Pack | Azure proporciona compatibilidad completa. | Preparado para Azure
Windows Server 2012 R2 y todos los Service Pack | Azure proporciona compatibilidad completa. | Preparado para Azure
Windows Server 2012 y todos los Service Pack | Azure proporciona compatibilidad completa. | Preparado para Azure
Windows Server 2008 R2 con todos los Service Pack | Azure proporciona compatibilidad completa.| Preparado para Azure
Windows Server 2003-2008 R2 | El sistema operativo alcanzó la fecha de finalización del soporte técnico y necesita un [contrato de soporte técnico personalizado (CSA)](https://aka.ms/WSosstatement) para obtener soporte técnico en Azure. | Condicionalmente preparada para Azure, considere la posibilidad de actualizar el sistema operativo antes de migrar a Azure.
Windows 2000, 98, 95, NT, 3.1, MS-DOS | Estos sistemas operativos han alcanzado la fecha de finalización del soporte técnico, la máquina pude arrancar en Azure, pero Azure no proporciona ningún soporte técnico para el sistema operativo. | Condicionalmente preparada para Azure, se recomienda actualizar el sistema operativo antes de migrar a Azure.
Cliente de Windows 7, 8 y 10 | Azure solo proporciona compatibilidad con la suscripción de Visual Studio. | Condicionalmente preparada para Azure
Windows Vista, XP Professional | Estos sistemas operativos han alcanzado la fecha de finalización del soporte técnico, la máquina pude arrancar en Azure, pero Azure no proporciona ningún soporte técnico para el sistema operativo. | Condicionalmente preparada para Azure, se recomienda actualizar el sistema operativo antes de migrar a Azure.
Linux | Azure aprueba estos [sistemas operativos Linux](../virtual-machines/linux/endorsed-distros.md). Otros sistemas operativos Linux pueden arrancar en Azure, pero se recomienda actualizar el sistema operativo a una versión aprobada antes de migrar a Azure. | Preparada para Azure si la versión está aprobada.<br/><br/>Condicionalmente preparada si la versión no se ha aprobado.
Otros sistemas operativos<br/><br/> Por ejemplo,  Oracle Solaris, Apple Mac OS etc., FreeBSD, etc. | Azure no aprueba estos sistemas operativos. La máquina puede arrancar en Azure, pero Azure no proporciona ningún sistema operativo. | Condicionalmente preparada para Azure, se recomienda instalar un sistema operativo compatible antes de migrar a Azure.  
Sistema operativo especificado como *otro* en vCenter Server. | Azure Migrate no puede identificar el sistema operativo en este caso. | Preparación desconocida. Asegúrese de que el sistema operativo que se ejecuta dentro de la máquina virtual se admite en Azure. 
Sistemas operativos de 32 bits | La máquina puede arrancar en Azure, pero Azure no puede proporcionar soporte técnico completo. | Condicionalmente preparada para Azure, considere la posibilidad de actualizar el sistema operativo de la máquina de un sistema operativo de 32 bits a uno de 64 bits antes de migrar a Azure.

## <a name="sizing"></a>Ajuste de tamaño

Después de que una máquina se marca como preparada para Azure, Azure Migrate cambia el tamaño de la máquina virtual y sus discos para Azure. Si el criterio de ajuste de tamaño especificado en las propiedades de valoración es realizar el ajuste de tamaño basado en el rendimiento, Azure Migrate tiene en cuenta el historial de rendimiento de la máquina para identificar un tamaño de memoria virtual en Azure. Este método es útil en escenarios donde ha asignado en exceso la máquina virtual local, pero el uso es bajo, y le gustaría ajustar correctamente el tamaño de las máquinas virtuales en Azure para ahorrar costos.

> [!NOTE]
> Azure Migrate recopila el historial de rendimiento de las máquinas virtuales locales desde vCenter Server. Para garantizar la precisión en el ajuste de tamaño, asegúrese de que la configuración de estadísticas en vCenter Server está establecida en el nivel 3 y espere al menos un día antes de empezar la detección de las máquinas virtuales locales. Si la configuración de estadísticas en vCenter Server está por debajo del nivel 3, no se recopilan datos de rendimiento de disco y red. 

Si no desea considerar el historial de rendimiento y quiere usar la máquina virtual en Azure tal y como está, puede especificar el criterio de ajuste de tamaño *como local* y Azure Migrate ajustará entonces el tamaño de las máquinas virtuales en función de la configuración local sin tener en cuenta los datos de uso.

### <a name="performance-based-sizing"></a>Ajuste de tamaño según el rendimiento

Para realizar el ajuste de tamaño basado en el rendimiento, Azure Migrate comienza con los discos asociados a la máquina virtual, después los adaptadores de red y, a continuación, asigna una máquina virtual de Azure en función de requisitos de proceso de la máquina virtual local. 
 
- **Discos**: Azure Migrate intenta asignar cada disco asociado a la máquina a un disco en Azure. 
    
    > [!NOTE]
    > Azure Migrate admite solo discos administrados para la valoración.
    
    - Para obtener la E/S de disco por segundo (IOPS) y el rendimiento (MBps) efectivos, Azure Migrate multiplica el valor de IOPS del disco y el rendimiento por el factor de confort. Según los valores efectivos de IOPS y rendimiento, Azure Migrate identifica si el disco debe asignarse a un disco estándar o premium de Azure.
    - Si Azure Migrate no puede encontrar un disco con los valores de rendimiento e IOPS necesarios, marca la máquina como no adecuada para Azure. [Más información](../azure-subscription-service-limits.md#storage-limits) sobre los límites de Azure por disco y máquina virtual.
    - Si encuentra un conjunto de discos adecuados, Azure Migrate selecciona aquellos que admiten el método de redundancia de almacenamiento y la ubicación especificada en la configuración de evaluación.
    - Si hay varios discos aptos, selecciona el que tiene el costo más bajo.
    - Si no hay datos de rendimiento disponibles para los discos, todos los discos se asignan a discos estándar en Azure.

- **Adaptadores de red**: Azure Migrate intenta encontrar una máquina virtual de Azure que pueda admitir el número de adaptadores de red asociados a la máquina local y el rendimiento que requieren estos adaptadores de red.
    - Para obtener el rendimiento de red efectivo de la máquina virtual local, Azure Migrate agrega los datos transmitidos por segundo (MBps) fuera de la máquina (salida de red), en todos los adaptadores de red, y aplica el factor de confort. Este número se utiliza para buscar una máquina virtual de Azure que pueda cumplir el rendimiento de red requerido.
    - Junto con el rendimiento de la red, también tiene en cuenta si la máquina virtual de Azure puede admitir el número de adaptadores de red necesarios.
    - Si no hay datos de rendimiento de red disponibles, solo se tiene en cuenta el número de adaptadores de red para ajustar el tamaño de la máquina virtual.

- **Tamaño de máquina virtual**: después de que se calculan los requisitos de red y almacenamiento, Azure Migrate tiene en cuenta los requisitos de CPU y memoria para encontrar un tamaño adecuado de máquina virtual en Azure.
    - Azure Migrate examina los núcleos y la memoria utilizados y aplica el factor de confort para obtener los núcleos y la memoria efectivos. En función de ese número, trata de encontrar un tamaño de máquina virtual adecuado en Azure.
    - Si no encuentra ningún tamaño adecuado, la máquina se marca como no adecuada para Azure.
    - Si encuentra un tamaño adecuado, Azure Migrate aplica los cálculos de red y almacenamiento. Después, aplica la configuración de ubicación y plan de tarifa para realizar la recomendación final de tamaño de máquina virtual.
    - Si hay varios tamaños de máquina virtual de Azure adecuados, se recomienda el que tiene el costo más bajo.

### <a name="as-on-premises-sizing"></a>Ajuste de tamaño como local
Si el criterio de ajuste de tamaño es *como local*, Azure Migrate no tiene en cuenta el historial de rendimiento de las máquinas virtuales y asigna las máquinas virtuales y los discos en función del tamaño asignado en el entorno local.
- **Almacenamiento**: para cada disco, se recomienda un disco estándar en Azure con el mismo tamaño que el disco local.
- **Red**: para cada adaptador de red, se recomienda un adaptador de red en Azure.
- **Proceso**: Azure Migrate examina el número de núcleos y tamaño de la memoria de la máquina virtual local y recomienda una máquina virtual de Azure con la misma configuración. Si hay varios tamaños de máquina virtual de Azure adecuados, se recomienda el que tiene el costo más bajo.
 
### <a name="confidence-rating"></a>Clasificación de confianza

Cada valoración de Azure Migrate está asociada con una clasificación de confianza que va de 1 a 5 estrellas (siendo 1 estrella la más baja y 5 estrellas la más alta). La clasificación de confianza se asigna a una valoración que se basa en la disponibilidad de puntos de datos necesarios para calcular tal valoración. Le ayuda a calcular la confiabilidad de las recomendaciones de tamaño que proporciona Azure Migrate. 

La clasificación de confianza es útil cuando está realizando el *ajuste de tamaño basado en el rendimiento* dado que puede que no estén disponibles todos los puntos de datos. Para el *ajuste de tamaño como local*, la clasificación de confianza siempre es de 5 estrellas, puesto que Azure Migrate tiene todos los datos que necesita para ajustar el tamaño de la máquina virtual. 

Para ajustar el tamaño basado en el rendimiento, Azure Migrate necesita los datos de uso de la CPU y la memoria. Para cada disco asociado a la máquina virtual, se necesita el valor de IOPS de lectura o escritura y el rendimiento para realizar el ajuste de tamaño basado en el rendimiento. De forma similar, para cada adaptador de red asociado a la máquina virtual, Azure Migrate necesita la entrada o la salida de red para realizar el ajuste de tamaño basado en el rendimiento. Si alguno de los números de uso anteriores no está disponible en vCenter Server, la recomendación de tamaño que realiza Azure Migrate podría no ser de confianza. Según el porcentaje de puntos de datos disponibles, se proporciona la clasificación de confianza para la valoración:

   **Disponibilidad de puntos de datos** | **Clasificación de confianza**
   --- | ---
   0 % - 20 % | 1 estrella
   21 % - 40 % | 2 estrellas
   41 % - 60 % | 3 estrellas
   61 % - 80 % | 4 estrellas
   81 % - 100 % | 5 estrellas

Debido a uno de los siguientes motivos, puede que las valoraciones no tengan todos los puntos de datos disponibles:
- La configuración de estadísticas en vCenter Server no está establecida en el nivel 3 y la valoración tiene como criterio de tamaño el ajuste de tamaño basado en el rendimiento. Si la configuración de estadísticas en vCenter Server es inferior al nivel 3, no se recopilan los datos de rendimiento de disco y de red de vCenter Server. En este caso, la recomendación que proporciona Azure Migrate de disco y de red se basa solamente en lo que se asignó en local. Para el almacenamiento, Azure Migrate recomienda discos estándar ya que no hay ninguna manera de identificar si el disco tiene una IOPS o un rendimiento altos y necesita discos premium.
- La configuración de estadísticas en vCenter Server se estableció en el nivel 3 durante un corto período, antes de iniciar la detección. Por ejemplo, si hoy cambia el nivel de configuración de estadísticas a 3 y mañana inicia la detección mediante el dispositivo recopilador (una vez pasadas 24 horas), si está creando una valoración durante un día, tendrá todos los puntos de datos. Pero si cambia la duración del rendimiento a un mes en las propiedades de la valoración, la clasificación de confianza descenderá dado que el disco y los datos de rendimiento de la red durante el último mes no están disponibles. Si quisiera considerar los datos de rendimiento del último mes, se recomienda que mantenga la configuración de las estadísticas de vCenter Server en el nivel 3 durante un mes antes de iniciar la detección. 
- Se apagaron algunas máquinas virtuales en el período durante el que se calcula la valoración. Si se han apagado todas las máquinas virtuales durante un tiempo, vCenter Server no tendrá los datos de rendimiento de ese período. 
- Algunas máquinas virtuales se crearon en algún momento del período durante el cual se calcula la valoración. Por ejemplo, si va a crear una valoración para el historial de rendimiento del último mes, pero algunas máquinas virtuales se crearon en el entorno hace solo una semana. En tales casos, el historial de rendimiento de las nuevas máquinas virtuales no permanecerá durante toda la duración.

> [!NOTE]
> Si la clasificación de confianza de cualquier valoración es inferior a 3 estrellas, recomendamos que cambie el nivel de configuración de las estadísticas de vCenter Server a 3, espere la duración que desee considerar para la valoración (un día, una semana o un mes) y, a continuación, realice la detección y la valoración. Si no se puede realizar lo anterior, el ajuste de tamaño basado en el rendimiento podría no ser de confianza y se recomienda cambiar a *como local* cambiando las propiedades de la valoración.

## <a name="monthly-cost-estimation"></a>Estimación del costo mensual

Una vez completadas las recomendaciones de ajuste de tamaño, Azure Migrate calcula los costos de almacenamiento y proceso posteriores a la migración.

- **Costo de proceso**: con el tamaño recomendado de máquina virtual de Azure, Azure Migrate usa Billing API para calcular el costo mensual de la máquina virtual. El cálculo tiene en cuenta la configuración de moneda, ubicación, Software Assurance y sistema operativo. Para calcular el costo de proceso mensual total, agrega el costo a todas las máquinas.
- **Costo de almacenamiento**: el costo de almacenamiento mensual de una máquina se calcula sumando el costo mensual de todos los discos conectados a la máquina. Azure Migrate calcula los costos de almacenamiento mensuales totales agregando los costos de almacenamiento de todas las máquinas. Actualmente, el cálculo no tiene en cuenta las ofertas especificadas en la configuración de evaluación.

Los costos se muestran en la moneda especificada en la configuración de evaluación. 


## <a name="next-steps"></a>pasos siguientes

[Creación de una valoración para máquinas virtuales VMware locales](tutorial-assessment-vmware.md)
