---
title: Azure Site Recovery Deployment Planner en el escenario de Hyper-V a Azure | Microsoft Docs
description: "En este artículo se describe el análisis del informe generado por Azure Site Recovery Deployment Planner en el escenario de Hyper-V a Azure."
services: site-recovery
documentationcenter: 
author: nsoneji
manager: garavd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 12/02/2017
ms.author: nisoneji
ms.openlocfilehash: 9340fe48c1da874d6c0cf02c026e5dec6ddabbe7
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/15/2017
---
# <a name="analyze-the-azure-site-recovery-deployment-planner-report"></a>Análisis del informe de Azure Site Recovery Deployment Planner
El informe de Microsoft Excel generado contiene las siguientes hojas:

## <a name="on-premises-summary"></a>Resumen local
La hoja de cálculo Resumen local proporciona información general del entorno de Hyper-V en el que se han generado perfiles ![Resumen local](media/site-recovery-hyper-v-deployment-planner-analyze-report/on-premises-summary-h2a.png)

**Fecha de inicio** y **Fecha de finalización**: las fechas de inicio y finalización de los datos de generación de perfiles que se tienen en cuenta para la generación de informes. De forma predeterminada, la fecha de inicio es la fecha en que comienza la generación de perfiles, mientras que la de finalización es la fecha en la que se detiene. Esta se puede especificar mediante los valores "StartDate" y "EndDate" si el informe se genera con estos parámetros.

**Número total de días de generación de perfiles**: el número total de días de generación de perfiles comprendido entre las fechas de inicio y de finalización para el que se genera el informe.

**Number of compatible virtual machines**: número total de máquinas virtuales compatibles para el que se calculan el ancho de banda de red necesario, el número de cuentas de almacenamiento requeridas y los núcleos de Microsoft Azure.

**Total number of disks across all compatible virtual machines**: número total de discos entre todas las máquinas virtuales compatibles.

**Número medio de discos por máquina virtual compatible**: el número medio de discos calculado en todas las máquinas virtuales compatibles.

**Tamaño medio de disco (GB)**: el tamaño medio de disco calculado en todas las máquinas virtuales compatibles.

**RPO deseado (en minutos)**: el objetivo de punto de recuperación predeterminado o el valor pasado para el parámetro "DesiredRPO" en el momento de la generación de informes para calcular el ancho de banda necesario.

**Ancho de banda deseado (Mbps)**: el valor que ha pasado para el parámetro "Bandwidth" en el momento de la generación de informes para calcular el RPO factible.

**Actividad de datos normal observada por día (GB)**: el valor medio de la actividad de datos observada en todos los días de generación de perfiles.

## <a name="recommendations"></a>Recomendaciones  
La hoja de recomendaciones del informe de Hyper-V a Azure tiene los siguientes detalles según el RPO deseado seleccionado:

![Recomendaciones para el informe de Hyper-V a Azure](media/site-recovery-hyper-v-deployment-planner-analyze-report/Recommendations-h2a.png)

### <a name="profile-data"></a>Generación de perfiles de datos
![Generación de perfiles de datos](media/site-recovery-hyper-v-deployment-planner-analyze-report/profile-data-h2a.png)

**Período de datos de generación de perfiles**: el período durante el que se ejecutó la generación de perfiles. De forma predeterminada, la herramienta incluye todos los datos para los que se han generado perfiles en el cálculo. Si ha usado la opción StartDate (fecha inicial) y EndDate (fecha final) en la generación del informe, se genera el informe para el período específico. 

**Number of Hyper-V servers profiled**: número de servidores de Hyper-V para el que se genera el informe de sus máquinas virtuales. Haga clic en el número para ver el nombre de los servidores de Hyper-V. Se abre la hoja On-premises Storage Requirement (Requisitos de almacenamiento local), en la que se enumeran todos los servidores junto con sus requisitos de almacenamiento.    

**RPO deseado**: el objetivo de punto de recuperación para la implementación. De manera predeterminada, se calcula el ancho de banda de red necesario para los valores de RPO de 15, 30 y 60 minutos. En función de la selección, los valores afectados se actualizan en la hoja. Si ha usado el parámetro DesiredRPOinMin al generar el informe, ese valor se muestra en el resultado de RPO deseado.

### <a name="profiling-overview"></a>Información general sobre la generación de perfiles
![Información general sobre la generación de perfiles](media/site-recovery-hyper-v-deployment-planner-analyze-report/profiling-overview-h2a.png)

**Total de máquinas virtuales de las que se ha generado el perfil**: El número total de las máquinas virtuales cuyos datos de generación de perfiles están disponibles. Si VMListFile contiene los nombres de las máquinas virtuales de las que no se ha generado perfil, dichas máquinas virtuales no se tienen en cuenta en la generación de informes y se excluyen del número total de máquinas virtuales de las que se ha generado perfil.

**Compatible Virtual Machines**: número de máquinas virtuales que se pueden proteger en Azure mediante Azure Site Recovery. Es el número total de máquinas virtuales compatibles para el que se calculan el ancho de banda de red, el número de cuentas de almacenamiento y el número de núcleos de Azure. Los detalles de todas las máquinas virtuales compatibles están disponibles en la sección "Máquinas virtuales compatibles".

**Incompatible Virtual Machines**: número de máquinas virtuales para las que se ha generado el perfil y no son compatibles para la protección con Azure Site Recovery. Los motivos de dicha incompatibilidad se indican en la sección "Máquinas virtuales no compatibles". Si VMListFile contiene los nombres de las máquinas virtuales cuyo perfil no se ha generado, dichas máquinas se excluyen del número de máquinas virtuales no compatibles. Estas máquinas aparecen como "Datos no encontrados" al final de la sección "Máquinas virtuales no compatibles".

**RPO deseado**: el objetivo del punto de recuperación deseado, en minutos. El informe se genera para tres valores de RPO: 15 (valor predeterminado), 30 y 60 minutos. La recomendación que se hace sobre el ancho de banda en el informe se cambia en función de la selección que se realice en la lista desplegable de Desired RPO (RPO deseado) de la parte superior derecha de la hoja. Si el informe se ha generado mediante el parámetro -DesiredRPO con un valor personalizado, dicho valor personalizado se muestra como valor predeterminado en la lista desplegable Desired RPO (RPO deseado).

### <a name="required-network-bandwidth-mbps"></a>Ancho de banda de red requerido (Mbps)
![Ancho de banda de red requerido](media/site-recovery-hyper-v-deployment-planner-analyze-report/required-network-bandwidth-h2a.png)

**To meet RPO 100 percent of the time**: el ancho de banda recomendado, en Mbps, que se debe asignar para cumplir el RPO deseado en todo momento. Esta cantidad de ancho de banda debe estar dedicada a la replicación diferencial de estado estable de todas las máquinas virtuales compatibles para evitar infracciones de RPO.

**Para cubrir el RPO en el 90 % de las ocasiones**: a causa de los precios del ancho de banda o por cualquier otra razón, si no puede establecer el ancho de banda necesario para cubrir el RPO deseado en todo momento, puede elegir usar un valor de ancho de banda inferior que le permita satisfacer el RPO deseado en el 90 % de las ocasiones. Para conocer las implicaciones de establecer un ancho de banda inferior, el informe proporciona el análisis de hipótesis del número previsible de infracciones de RPO y su duración.

**Achieved Throughput**: rendimiento del servidor en el que ha ejecutado el comando GetThroughput para la región de Azure en la que se encuentra la cuenta de almacenamiento. Este número de rendimiento indica el nivel estimado que se puede lograr al proteger las máquinas virtuales compatibles mediante Azure Site Recovery, siempre que las características de red y de almacenamiento del servidor de Hyper-V sean las mismas que las del servidor en el que se ha ejecutado la herramienta.

Para todas las implementaciones de Azure Site Recovery que se realicen en la empresa, se recomienda usar [ExpressRoute](https://aka.ms/expressroute).

### <a name="required-storage-accounts"></a>Cuentas de almacenamiento requeridas
El siguiente gráfico muestra el número total de cuentas de almacenamiento (Estándar y Premium) que se requieren para proteger todas las máquinas virtuales compatibles. Para saber qué cuenta de almacenamiento se debe usar para cada máquina virtual, consulte la sección "selección de ubicación de almacenamiento de máquina virtual".

![Cuentas de almacenamiento requeridas](media/site-recovery-hyper-v-deployment-planner-analyze-report/required-storage-accounts-h2a.png)

### <a name="required-number-of-azure-cores"></a>Número de núcleos de Azure requeridos
Este resultado es el número total de núcleos que se deben configurar antes de realizar una conmutación por error real o de prueba de todas las máquinas virtuales compatibles. Si hay muy pocos núcleos disponibles en la suscripción, Azure Site Recovery no puede crear las máquinas virtuales en el momento de realizar la conmutación por error real o de prueba.
![Required number of Azure cores](media/site-recovery-hyper-v-deployment-planner-analyze-report/required-number-of-azure-cores-h2a.png)


### <a name="additional-on-premises-storage-requirement"></a>Requisitos de almacenamiento local adicionales

El almacenamiento disponible total necesario en los servidores de Hyper-V para la replicación inicial correcta y la replicación diferencial para asegurarse de que la replicación de las máquinas virtuales no provocará ningún tiempo de inactividad no deseado para las aplicaciones en producción. Los detalles de cada requisito de volumen están disponibles en los [requisitos de almacenamiento local](#on-premises-storage-requirement). 

Para información sobre por qué es necesario espacio disponible para la replicación, consulte la sección [Requisitos de almacenamiento local](#why-do-i-need-free-space-on-the-hyper-v-server-for-the-replication).

![Requisitos de almacenamiento local y frecuencia de copia](media/site-recovery-hyper-v-deployment-planner-analyze-report/on-premises-storage-and-copy-frequency-h2a.png)

### <a name="maximum-copy-frequency"></a>Frecuencia de copia máxima
Debe establecerse la frecuencia de copia máxima recomendada para la replicación para lograr el RPO deseado. El valor predeterminado es 5 minutos. Puede establecer una frecuencia de copia de 30 segundos para lograr un mejor RPO.

### <a name="what-if-analysis"></a>Análisis de hipótesis
![What-if-analysis](media/site-recovery-hyper-v-deployment-planner-analyze-report/what-if-analysis-h2a.png) Este análisis indica el número de infracciones que pueden producirse durante el período de generación de perfiles si se establece un ancho de banda menor para cumplir el RPO deseado solo en el 90 % de las ocasiones. Pueden producirse una o varias infracciones de RPO en cualquier día determinado. El gráfico muestra el RPO máximo del día. En función de este análisis, puede decidir si el número de infracciones de RPO a lo largo de todos los días y el límite máximo de RPO por día es aceptable para el ancho de banda menor especificado. Si es aceptable, puede asignar un ancho de banda menor para la replicación. En caso contrario, asigne un ancho de banda mayor como se sugirió anteriormente para satisfacer el RPO deseado en el 100 % de las ocasiones. 

### <a name="recommendation-for-successful-initial-replication"></a>Recomendación para una replicación inicial correcta
En esta sección se recomienda el número de lotes en el que se protegerán las máquinas virtuales y el ancho de banda mínimo necesario para completar la replicación inicial (IR) correctamente. 

![Procesamiento por lotes de IR](media/site-recovery-hyper-v-deployment-planner-analyze-report/ir-batching-h2a.png)

La máquina virtual debe ser protegida en el orden de lote especificado. Cada lote tiene una lista específica de máquinas virtuales. Se deben proteger las maquinas virtuales del Lote 1 antes que las del Lote 2. Se deben proteger las maquinas virtuales del Lote 2 antes que las del Lote 3 y así sucesivamente. Una vez completada la replicación inicial de las máquinas virtuales del Lote 1, puede habilitar la replicación de las máquinas virtuales del Lote 2. De modo similar, una vez completada la replicación inicial de las máquinas virtuales del Lote 2, puede habilitar la replicación de las máquinas virtuales del Lote 3 y así sucesivamente. Si no se sigue el orden de los lotes, podría no haber ancho de banda suficiente para la replicación inicial disponible para las máquinas virtuales, que se protegen más adelante. El resultado es que no se podrá completar la replicación inicial de las máquinas virtuales o que pocas máquinas virtuales protegidas pueden entrar en modo de resincronización. La hoja Procesamiento por lotes de IR para el RPO seleccionado contiene información detallada sobre las máquinas virtuales que se incluyen en cada lote.

Aquí, el gráfico muestra la distribución del ancho de banda para la replicación inicial y la replicación diferencial entre los lotes en el orden de lote especificado. Cuando se protegen las máquinas virtuales del primer lote, está disponible el ancho de banda completo para la replicación inicial. Una vez que la replicación inicial ha finalizado para el primer lote, será necesaria una parte del ancho de banda para la replicación diferencial. El ancho de banda restante estará disponible para la replicación inicial de las máquinas virtuales del segundo lote. La barra del Lote 2 muestra el ancho de banda necesario para la replicación diferencial de las máquinas virtuales del Lote 1 y el ancho de banda disponible para la replicación inicial de las máquinas virtuales del Lote 2. De forma similar, la barra del Lote 3 muestra el ancho de banda necesario para la replicación diferencial de los lotes anteriores (las máquinas virtuales del Lote 1 y el Lote 2) y el ancho de banda disponible para la replicación inicial del Lote 3 y así sucesivamente.  Una vez que la replicación inicial de todos los lotes ha finalizado, la última barra muestra el ancho de banda necesario para la replicación diferencial de todas las máquinas virtuales protegidas. 

**¿Por qué se necesita el procesamiento por lotes de la replicación inicial?**
El tiempo de finalización de la replicación inicial está en función del tamaño de los discos de las máquinas virtuales, del espacio de disco usado y del rendimiento de red disponible. Los detalles están disponibles en la hoja Procesamiento por lotes de IR para el RPO seleccionado.

### <a name="cost-estimation"></a>Estimación de costos
En el gráfico siguiente se muestra la vista de resumen del costo total estimado de la recuperación ante desastres en Azure de la región de destino elegida y en la moneda que ha especificado para generar el informe.
![Cost estimation summary](media/site-recovery-hyper-v-deployment-planner-analyze-report/cost-estimation-summary-h2a.png) El resumen le ayuda a comprender lo que debe pagar por los servicios de almacenamiento, proceso, red y licencia cuando protege todas las máquinas virtuales compatibles en Azure mediante Azure Site Recovery. El costo se calcula para las máquinas virtuales compatibles y no para todas las máquinas virtuales para las que se han generado perfiles.  
 
Puede ver el costo mensual o anual. Obtenga más información sobre las [regiones de destino admitidas](./site-recovery-hyper-v-deployment-planner-cost-estimation.md#supported-target-regions) y las [monedas admitidas](./site-recovery-hyper-v-deployment-planner-cost-estimation.md#supported-currencies).

**Cost by components**: el costo total de la recuperación ante desastres se divide entre cuatro componentes: Compute, Storage, Network y el costo de la licencia de Azure Site Recovery. El costo se calcula en función del consumo en el que se incurrirá durante la replicación y en el transcurso del simulacro de recuperación ante desastres por los servicios de proceso, almacenamiento (premium y estándar), circuito ExpressRoute o VPN configurados entre el sitio local y Azure, y la licencia de Azure Site Recovery.

**Cost by states**: el costo total de recuperación ante desastres se clasifica según dos estados diferentes: replicación y simulacro de recuperación ante desastres. 

**Replication cost**: costo en el que se incurrirá durante la replicación. Incluye el costo de almacenamiento, red y licencia de Azure Site Recovery. 

**DR-Drill cost** (Costo del simulacro de recuperación ante desastres): costo en el que se incurrirá durante las conmutaciones por error de prueba. Azure Site Recovery pone en marcha las máquinas virtuales durante la conmutación por error de prueba. El costo del simulacro de recuperación ante desastres incluye el costo de los servicios de proceso y almacenamiento de las máquinas virtuales en ejecución. 

**Azure storage cost per Month/Year**: muestra el costo total en el que se incurrirá por el almacenamiento premium y estándar por la replicación y el simulacro de recuperación ante desastres.
Puede ver el análisis detallado de los costos por máquina virtual en la hoja [Cost Estimation](site-recovery-hyper-v-deployment-planner-cost-estimation.md) (Estimación de costos).

### <a name="growth-factor-and-percentile-values-used"></a>Factor de crecimiento y valores de percentil utilizados
Esta sección de la parte inferior de la hoja muestra el valor del percentil que se utiliza en todos los contadores de rendimiento de las máquinas virtuales de las que se ha generado el perfil (el valor predeterminado es el percentil 95) y el factor de crecimiento (el valor predeterminado es el 30 %) que se usa en todos los cálculos.

![Factor de crecimiento y valores de percentil utilizados](media/site-recovery-hyper-v-deployment-planner-run/growth-factor-max-percentile-value.png)

## <a name="recommendations-with-available-bandwidth-as-input"></a>Recomendaciones relacionadas con el ancho de banda disponible como entrada
![Introducción a la generación de perfiles con el ancho de banda de entrada](media/site-recovery-hyper-v-deployment-planner-analyze-report/profiling-overview-bandwidth-input-h2a.png)

Puede darse el caso de que sepa que no puede establecer un ancho de banda de más de x Mbps para la replicación de Azure Site Recovery. La herramienta le permite especificar el ancho de banda disponible (mediante el parámetro -Bandwidth durante la generación de informes) y obtener el RPO factible en minutos. Con este valor de RPO factible, puede decidir si necesita aprovisionar ancho de banda adicional o si está conforme con tener una solución de recuperación ante desastres con este RPO.

![RPO factible](media/site-recovery-hyper-v-deployment-planner-analyze-report/achivable-rpo-h2a.PNG)

## <a name="vm-storage-placement-recommendation"></a>Recomendación de selección de ubicación de almacenamiento de máquina virtual 

![VM-Selección de ubicación de almacenamiento](media/site-recovery-hyper-v-deployment-planner-analyze-report/vm-storage-placement-h2a.png)

**Tipo de almacenamiento en disco**: cuenta de Standard o Premium Storage, que se usa para replicar todas las máquinas virtuales correspondientes que se mencionan en la columna **Máquinas virtuales que se colocan**.

**Prefijo sugerido**: el prefijo de tres caracteres sugerido que se puede usar para asignar un nombre a la cuenta de almacenamiento. Puede usar su propio prefijo, pero el que sugiere la herramienta sigue la [convención de nomenclatura de particiones de las cuentas de almacenamiento](https://aka.ms/storage-performance-checklist).

**Nombre de cuenta sugerido**: el nombre de la cuenta de almacenamiento después de incluir el prefijo sugerido. Reemplace el nombre entre corchetes angulares (< y >) por una entrada personalizada.

**Cuenta de almacenamiento del registro**: todos los registros de la replicación se almacenan en una cuenta de almacenamiento Estándar. En el caso de las máquinas virtuales que se replican en una cuenta de almacenamiento Premium, configure una cuenta de almacenamiento Estándar adicional para el almacenamiento de registros. Varias cuentas de almacenamiento de replicación Premium puede usar una única cuenta de almacenamiento de registros Estándar. Las máquinas virtuales que se replican en las cuentas de almacenamiento Estándar usan la misma cuenta de almacenamiento para los registros.

**Nombre de cuenta de registros sugerido**: el nombre de la cuenta de registros de almacenamiento después de incluir el prefijo sugerido. Reemplace el nombre entre corchetes angulares (< y >) por una entrada personalizada.

**Resumen de la selección de ubicación**: un resumen de la carga total de máquinas virtuales en la cuenta de almacenamiento en el momento de la replicación y la conmutación por error real o de prueba. Incluye el número total de máquinas virtuales asignadas a la cuenta de almacenamiento, el total de IOPS de lectura/escritura en todas las máquinas virtuales colocadas en esta cuenta de almacenamiento, el total de IOPS de escritura (replicación), el tamaño total configurado en todos los discos y el número total de discos.

**Máquinas virtuales que se colocan**: una lista de todas las máquinas virtuales que se deben colocar en la cuenta de almacenamiento especificada para que tanto el rendimiento como el uso sean óptimos.

## <a name="compatible-vms"></a>Máquinas virtuales compatibles
El informe de Microsoft Excel generado por Azure Site Recovery Deployment Planner proporciona todos los detalles de las máquinas virtuales compatibles en la hoja "Máquinas virtuales compatibles".

![Máquinas virtuales compatibles](media/site-recovery-hyper-v-deployment-planner-analyze-report/compatible-vms-h2a.png)

**VM Name**: el nombre de la máquina virtual que se utiliza en VMListFile cuando se genera un informe. Esta columna también muestra los discos (discos duros virtuales) que están conectados a las máquinas virtuales. Los nombres incluyen los nombres de host de Hyper-V donde se colocaron las máquinas virtuales cuando la herramienta los detectó durante el período de generación de perfiles.

**Compatibilidad de máquina virtual**: los valores son **Sí** y **Sí**\*. **Sí**\* es para las instancias en que la máquina virtual es una opción para [Azure Premium Storage](https://aka.ms/premium-storage-workload). En este caso, la renovación elevada de generación de perfiles o el disco IOPS encaja en un tamaño de disco Premium mayor que el tamaño asignado al disco. La cuenta de almacenamiento decide a qué tipo de disco de almacenamiento Premium se asigna un disco, en función de su tamaño. 
* Menos de 128 GB es P10.
* De 128 GB a 256 GB es P15
* De 256 GB a 512 GB es P20.
* De 512 GB a 1024 GB es P30.
* De 1025 GB a 2048 GB es P40.
* De 2049 GB a 4095 GB es P50.

Por ejemplo, si las características de carga de trabajo de un disco lo colocan en la categoría P20 o P30, pero el tamaño lo asigna a un tipo de disco de almacenamiento Premium inferior, la herramienta marca la máquina virtual como **Sí**\*. La herramienta también recomienda que cambie el tamaño del disco de origen para poder encuadrarlo en el tipo de disco de almacenamiento Premium recomendado o que cambie el tipo de disco de destino después de la conmutación por error.

**Tipo de almacenamiento**: Estándar o Premium.

**Prefijo sugerido**: el prefijo de tres caracteres de la cuenta de almacenamiento.

**Cuenta de almacenamiento**: el nombre que utiliza el prefijo sugerido para la cuenta de almacenamiento.

**Peak R/W IOPS (with Growth Factor)**: el valor máximo de IOPS de lectura y escritura en carga de trabajo en el disco (el percentil 95 es el predeterminado) incluido el factor de crecimiento futuro (el valor predeterminado es el 30 %). Tenga en cuenta que el total de IOPS de lectura y escritura de una máquina virtual no son siempre la suma de las IOPS de lectura y escritura de los discos individuales de la máquina virtual, ya que las IOPS de lectura y escritura máximas de la máquina virtual son el máximo de la suma de las IOPS de lectura y escritura sus discos individuales durante cada minuto del período de generación de perfiles.

**Actividad de datos en MB/s (con factor de crecimiento)**: el máximo índice de actividad en el disco (el percentil 95 es el predeterminado), incluido el factor de crecimiento futuro (el valor predeterminado es el 30 %). Tenga en cuenta que el total de actividad de datos de la máquina virtual no siempre es la suma de la actividad de datos de los discos individuales de la máquina virtual, ya que la actividad de datos máxima de la máquina virtual es el máximo de la suma de las actividades de los discos individuales durante cada minuto del período de generación de perfiles.

**Tamaño de la máquina virtual de Azure**: el tamaño ideal de la máquina virtual de Azure Cloud Services asignada para esta máquina virtual local. La asignación basada en la memoria, el número de núcleos de discos/núcleos/NIC y las IOPS de lectura y escritura de la máquina virtual local. La recomendación es usar siempre el menor tamaño de máquina virtual de Azure que cumpla todas las características de la máquina virtual local.

**Number of Disks**: el número total de discos (discos duros virtuales) de la máquina virtual.

**Disk size (GB)**: el tamaño total de todos los discos de la máquina virtual. La herramienta también muestra el tamaño de los discos individuales de la máquina virtual.

**Núcleos**: el número de núcleos de CPU de la máquina virtual.

**Memoria (en MB)**: la memoria RAM de la máquina virtual.

**NIC**: el número de NIC de la máquina virtual.

**Boot Type**: tipo de arranque de la máquina virtual. Puede ser BIOS o EFI.

## <a name="incompatible-vms"></a>Máquinas virtuales no compatibles
El informe de Microsoft Excel generado por Azure Site Recovery Deployment Planner proporciona todos los detalles de las máquinas virtuales no compatibles en la hoja "Incompatible VMs" (Máquinas virtuales no compatibles).

![Máquinas virtuales no compatibles](media/site-recovery-hyper-v-deployment-planner-analyze-report/incompatible-vms-h2a.png)

**VM Name**: el nombre de la máquina virtual que se utiliza en VMListFile cuando se genera un informe. Esta columna también muestra los discos (discos duros virtuales) que están conectados a las máquinas virtuales. Los nombres incluyen los nombres de host de Hyper-V donde se colocaron las máquinas virtuales cuando la herramienta los detectó durante el período de generación de perfiles.

**VM Compatibility**: indica el motivo por el que una máquina virtual dada no es compatible con Azure Site Recovery. Se describen las razones de cada disco incompatible de la máquina virtual, que, en función de los [límites de almacenamiento](https://aka.ms/azure-storage-scalbility-performance) publicados, pueden ser cualesquiera de las siguientes:

* El tamaño del disco es superior a 4095 GB. Azure Storage no admite actualmente discos de datos cuyo tamaño supere 4095 GB.

* El disco del sistema operativo es mayor de 2047 GB para una máquina virtual de Generación 1 (tipo de inicio del BIOS).  Azure Site Recovery no admite un tamaño de disco del sistema operativo mayor de 2047 GB para máquinas virtuales de Generación 1.

* El disco del sistema operativo es mayor de 300 GB para una máquina virtual de Generación 2 (tipo de inicio EFI). Azure Site Recovery no admite un tamaño de disco del sistema operativo mayor de 300 GB para máquinas virtuales de Generación 2.

* El nombre de máquina virtual contiene alguno de los siguientes caracteres no admitidos "" [] '.  La herramienta no puede obtener datos de generación de perfiles de las máquinas virtuales que tienen alguno de estos caracteres en sus nombres. 

* El disco duro virtual es compartido por dos o más máquinas virtuales. Azure no admite máquinas virtuales con disco duro virtual compartido.

* No se admite la máquina virtual con canal de fibra virtual. Azure Site Recovery no admite máquinas virtuales con canal de fibra virtual.

* El clúster de Hyper-V no contiene un agente de replicación. Azure Site Recovery no admite una máquina virtual en un clúster de Hyper-V si el agente de réplica de Hyper-V no está configurado para el clúster.

* La máquina virtual no es de alta disponibilidad. Azure Site Recovery no admite una máquina virtual de nodo de clúster de Hyper-V cuyos discos duros virtuales se almacenan en el disco local en lugar de en el disco de clúster. 

* El tamaño total de la máquina virtual (replicación + TFO) supera el límite de tamaño de la cuenta de almacenamiento Premium que se admite (35 TB). Esta incompatibilidad se produce normalmente cuando uno de los discos de la máquina virtual tiene una característica de rendimiento que supera los límites de almacenamiento estándar de Azure o de Azure Site Recovery. Una instancia de este tipo coloca la máquina virtual en la zona de almacenamiento premium. Sin embargo, el tamaño máximo que se admite de una cuenta de almacenamiento Premium es de 35 TB y una sola máquina virtual protegida no se puede proteger en varias cuentas de almacenamiento. Tenga también en cuenta que si se realiza una conmutación por error de prueba en una máquina virtual protegida, si el disco no administrado está configurado para la conmutación por error de prueba, esta se ejecuta en la misma cuenta de almacenamiento en la que se lleva a cabo la replicación. En este caso, se requiere la misma cantidad de espacio adicional de almacenamiento que para la replicación. Se asegura que el progreso de la replicación y la conmutación por error de prueba sean correctos en paralelo. Cuando el disco administrado está configurado para la conmutación por error de prueba, no debe tenerse en cuenta ningún espacio adicional para la máquina virtual de conmutación por error de prueba.

* El valor de IOPS de origen supera el límite que admite el almacenamiento, 7500 por disco.

* El valor de IOPS de origen supera el límite que admite el almacenamiento, 80 000 por máquina virtual.

* La actividad de datos media de la máquina virtual de origen supera el límite que admite Azure Site Recovery, 10 MB/s para el tamaño medio de E/S de disco.

* El valor de IOPS de escritura efectiva medio de la máquina virtual de origen supera el límite admitido de Azure Site Recovery de 840.

* El almacenamiento de instantáneas estimado supera el límite admitido de 10 TB.

**Peak R/W IOPS (with Growth Factor)**: el valor máximo de IOPS en carga de trabajo en el disco (el percentil 95 es el predeterminado) incluido el factor de crecimiento futuro (el valor predeterminado es el 30 %). Tenga en cuenta que el total de IOPS de lectura y escritura de la máquina virtual no es siempre la suma de las IOPS de lectura y escritura de los discos individuales de la máquina virtual, ya que las IOPS de lectura y escritura máximas de la máquina virtual es el máximo de la suma de las IOPS de lectura y escritura sus discos individuales durante cada minuto del período de generación de perfiles.

**Actividad de datos en MB/s (con factor de crecimiento)**: el máximo índice de actividad en el disco (el percentil 95 es el predeterminado), incluido el factor de crecimiento futuro (el valor predeterminado es el 30 %). Tenga en cuenta que el total de actividad de datos de la máquina virtual no siempre es la suma de la actividad de datos de los discos individuales de la máquina virtual, ya que la actividad de datos máxima de la máquina virtual es el máximo de la suma de las actividades de los discos individuales durante cada minuto del período de generación de perfiles.

**Number of Disks**: el número total de discos duros virtuales de la máquina virtual.

**Tamaño de disco (en GB)** : el tamaño total configurado de todos los discos de la máquina virtual. La herramienta también muestra el tamaño de los discos individuales de la máquina virtual.

**Núcleos**: el número de núcleos de CPU de la máquina virtual.

**Memoria (en MB)**: la cantidad de memoria RAM de la máquina virtual.

**NIC**: el número de NIC de la máquina virtual.

**Boot Type**: tipo de arranque de la máquina virtual. Puede ser BIOS o EFI.

## <a name="azure-site-recovery-limits"></a>Límites de Azure Site Recovery
En la tabla siguiente se proporcionan los límites de Azure Site Recovery. Estos límites se basan en nuestras pruebas, pero no pueden cubrir todas las combinaciones de E/S posibles de la aplicación. Los resultados reales pueden variar en función de la combinación de E/S de la aplicación. Para unos mejores resultados, incluso después del planeamiento de la implementación, es aconsejable probar siempre la aplicación de forma exhaustiva mediante una conmutación por error de prueba para obtener una imagen real del rendimiento de la aplicación.
 
**Destino de almacenamiento de la replicación** | **Tamaño medio de E/S de la máquina virtual de origen** |**Actividad de datos media de la máquina virtual de origen** | **Actividad de datos total de la máquina virtual de origen por día**
---|---|---|---
Standard storage | 8 KB | 2 MB/s por máquina virtual | 168 GB por máquina virtual
Premium Storage | 8 KB  | 5 MB/s por máquina virtual | 421 GB por máquina virtual
Premium Storage | 16 KB o superior| 10 MB/s por máquina virtual | 842 GB por máquina virtual

Estos límites son los números promedio si la superposición de E/S es del 30 %. Azure Site Recovery es capaz de administrar un mayor rendimiento en función de la relación de superposición, los tamaños más grandes de escritura y el comportamiento real de E/S de la carga de trabajo. Los números anteriores asumen un trabajo pendiente típico de aproximadamente cinco minutos. Es decir, una vez que se cargan los datos, se procesan y se crea un punto de recuperación en menos de cinco minutos.

## <a name="on-premises-storage-requirement"></a>Requisitos de almacenamiento local

La hoja de cálculo proporciona los requisitos de espacio de almacenamiento libre total para cada volumen de los servidores de Hyper-V (donde residen los discos duros virtuales) para la replicación inicial correcta y la replicación diferencial. Antes de habilitar la replicación, agregue el espacio de almacenamiento necesario en los volúmenes para asegurarse de que la replicación no provocará ningún tiempo de inactividad no deseado en las aplicaciones de producción. 

Azure Site Recovery Deployment Planner identifica los requisitos de espacio de almacenamiento óptimos según el tamaño de los discos duros virtuales y el ancho de banda de red utilizado para la replicación.

![Requisitos de almacenamiento local](media/site-recovery-hyper-v-deployment-planner-analyze-report/on-premises-storage-requirement-h2a.png)

### <a name="why-do-i-need-free-space-on-the-hyper-v-server-for-the-replication"></a>¿Por qué necesito espacio libre en el servidor de Hyper-V para la replicación?
* Cuando se habilita la replicación de una máquina virtual, Azure Site Recovery toma una instantánea de cada disco duro virtual de la máquina virtual para la replicación inicial (IR). Mientras que la replicación inicial está en progreso, la aplicación escribe nuevos cambios en los discos. Azure Site Recovery realiza un seguimiento de estos cambios diferenciales en los archivos de registro, que requieren espacio de almacenamiento adicional.  Hasta que se completa la replicación inicial, los archivos de registro se almacenan localmente. Si no hay suficiente espacio para los archivos de registro y la instantánea (AVHDX), la replicación pasará al modo de resincronización y nunca se completará la replicación. En el peor de los casos, se necesita un espacio libre adicional del 100% del tamaño del disco duro virtual para la replicación inicial.
* Una vez que la replicación inicial se ha completado, se inicia la replicación diferencial. Azure Site Recovery realiza un seguimiento de estos cambios diferenciales en los archivos de registro, que se almacenan en el volumen donde residen los discos duros virtuales de la máquina virtual. Estos archivos de registro se replican en Azure con una frecuencia de copia configurada. Según el ancho de banda de red disponible, la replicación de los archivos de registro en Azure tarda algún tiempo en realizarse. Si no hay suficiente espacio libre para almacenar los archivos de registro, se pondrá en pausa la replicación y el estado de replicación de la máquina virtual pasa a resincronización necesaria.
* Si el ancho de banda de red no es suficiente para insertar los archivos de registro en Azure, los archivos de registro son apilados en el volumen. En el peor escenario, cuando aumenta el tamaño de los archivos de registro al 50% del tamaño del disco duro virtual, la replicación de la máquina virtual pasa a resincronización necesaria. En el peor de los casos, se necesita un espacio libre adicional del 50 % del tamaño del disco duro virtual para la replicación diferencial.

**Hyper-V host**: lista de servidores de Hyper-V para los que se ha generado perfil. Si un servidor forma parte de un clúster de Hyper-V, se agrupan todos los nodos del clúster.

**Volume (VHD path)**: cada volumen de un host de Hyper-V en el que están presentes los archivos VHD o VHDX. 

**Free space available (GB)**: el espacio libre disponible en el volumen.

**Total storage space required on the volume (GB)**: el espacio de almacenamiento libre total requerido en el volumen para la replicación inicial correcta y la replicación diferencial. 

**Total additional storage to be provisioned on the volume for successful replication**: recomienda el espacio total adicional que debe aprovisionarse en el volumen para la replicación inicial correcta y la replicación diferencial.

## <a name="initial-replication-batching"></a>Procesamiento por lotes de la replicación inicial 

### <a name="why-do-i-need-initial-replication-ir-batching"></a>¿Por qué se necesita el procesamiento por lotes de la replicación inicial (IR)?
Si se protegen todas las máquinas virtuales al mismo tiempo, el requisito de almacenamiento libre sería mucho mayor y si no hay suficiente espacio de almacenamiento, la replicación de las máquinas virtuales entrará en modo de resincronización. Además, el requisito de ancho de banda de red sería mucho más alto para completar la replicación inicial de todas las máquinas virtuales juntas correctamente. 

### <a name="initial-replication-batching-for-a-selected-rpo"></a>Procesamiento por lotes de la replicación inicial para un RPO seleccionado
Esta hoja de cálculo proporciona la vista de detalle de cada lote para la replicación inicial (IR). Para cada RPO, se crean una hoja de procesamiento por lotes de replicación inicial independiente. 

Una vez que ha seguido la recomendación de requisitos de almacenamiento local para cada volumen, la información principal que necesita para replicar es la lista de máquinas virtuales que se pueden proteger en paralelo. Estas máquinas virtuales se agrupan juntas en un lote. Puede haber varios lotes. Debe proteger las máquinas virtuales en el orden de lote especificado. En primer lugar, proteja las máquinas virtuales del Lote 1 y cuando se haya completado la replicación inicial, proteja las máquinas virtuales del Lote 2 y así sucesivamente. Puede obtener la lista de lotes y máquinas virtuales correspondientes en esta hoja. 

![Detalles del procesamiento por lotes de IR 1](media/site-recovery-hyper-v-deployment-planner-analyze-report/ir-batching-for-rpo-h2a.png)
![Detalles del procesamiento por lotes de IR 2](media/site-recovery-hyper-v-deployment-planner-analyze-report/ir-batching-for-rpo2-h2a.png)

### <a name="each-batch-provides-the-following-information"></a>Cada lote proporciona la siguiente información 
**Hyper-V host**: el host de Hyper-V de la máquina virtual que se protegerá.
Máquina virtual: la máquina virtual que se protegerá. 

**Comments**: si es necesaria cualquier acción para un volumen específico de una máquina virtual, aquí se proporciona el comentario.  Por ejemplo, si no hay suficiente espacio libre en un volumen, reflejaría Agregar almacenamiento adicional para proteger esta máquina virtual en el comentario.

**Volume (VHD path)**: el nombre del volumen donde residen los discos duros virtuales de la máquina virtual. Espacio libre disponible en el volumen (GB): el espacio en disco disponible en el volumen para la máquina virtual. Al calcular el espacio libre disponible en los volúmenes, se considera el espacio en disco usado para la replicación diferencial por las máquinas virtuales de los lotes anteriores cuyos discos duros virtuales se encuentran en el mismo volumen.  

Por ejemplo, VM1, VM2 y VM3 residen en el volumen E:\VHDpath. Antes de la réplica, el espacio libre en el volumen es de 500 GB. VM1 forma parte del Lote 1, VM2 forma parte del Lote 2 y VM3 forma parte del Lote 3.  Para VM1, el espacio libre disponible es de 500 GB. Para VM2, el espacio libre disponible sería 500 menos el espacio en disco necesario para la replicación diferencial de VM1.  Suponiendo que VM1 requiere 300 GB de espacio para la replicación diferencial, el espacio libre disponible para VM2 sería 500 GB – 300 GB = 200 GB.  De forma similar, VM2 requiere 300 GB para la replicación diferencial. El espacio libre disponible para VM3 sería 200 GB - 300 GB = -100 GB.

**Storage required on the volume for initial replication (GB)**: espacio de almacenamiento libre requerido en el volumen para la replicación inicial de la máquina virtual.

**Storage required on the volume for delta replication (GB)**: espacio de almacenamiento libre requerido en el volumen para la replicación diferencial de la máquina virtual.

**Additional storage required based on deficit to avoid replication failure (GB)**: espacio de almacenamiento adicional requerido en el volumen para la máquina virtual.  Es el valor máximo de los requisitos de espacio de almacenamiento de la replicación inicial y la replicación diferencial menos el espacio libre disponible en el volumen.

**Minimum bandwidth required for initial replication (Mbps)**: ancho de banda mínimo necesario para la replicación inicial de la máquina virtual.

**Minimum bandwidth required for delta replication (Mbps)**: ancho de banda mínimo necesario para la replicación diferencial de la máquina virtual

### <a name="network-utilization-details-for-each-batch"></a>Detalles de uso de red para cada lote 
Cada tabla de lotes proporciona un resumen del uso de red del lote.

**Bandwidth available for Batch**: el ancho de banda disponible para el lote después de considerar el ancho de banda de la replicación diferencial del lote anterior.

**Approximate bandwidth available for initial replication of batch**: ancho de banda disponible para la replicación inicial de las máquinas virtuales del lote. 

**Approximate bandwidth consumed for delta replication of batch**: ancho de banda necesario para la replicación diferencial de las máquinas virtuales del lote. 

**Estimated Initial Replication time for Batch (HH:MM)**: tiempo de la replicación inicial estimado en horas:minutos.

## <a name="cost-estimation"></a>Estimación de costos
Obtenga más información sobre la [estimación de costos](site-recovery-hyper-v-deployment-planner-cost-estimation.md). 

## <a name="next-steps"></a>Pasos siguientes
Obtenga más información sobre la [estimación de costos](site-recovery-hyper-v-deployment-planner-cost-estimation.md).