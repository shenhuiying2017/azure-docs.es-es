<properties
	pageTitle="Planeación de la capacidad para la protección de la máquina virtual y del servidor físico en Site Recovery | Microsoft Azure"
	description="Azure Site Recovery coordina la replicación, la conmutación por error y la recuperación de máquinas virtuales ubicadas localmente en Azure o en un sitio local secundario." 
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
	ms.workload="storage-backup-recovery" 
	ms.date="12/07/2015" 
	ms.author="raynew"/>

# Planeación de la capacidad para la protección de la máquina virtual y del servidor físico en Azure Site Recovery

La herramienta Capacity Planner le ayuda a determinar los requisitos de capacidad para proteger máquinas virtuales de Hyper-V, máquinas virtuales de VMware y servidores físicos de Windows/Linux con Azure Site Recovery.


## Información general

Use la herramienta para analizar el entorno de origen y las cargas de trabajo y para averiguar las necesidades de ancho de banda, los recursos de servidor que necesita en la ubicación de origen y los recursos (máquinas virtuales y almacenamiento, etc.) que necesitará en su ubicación de destino.

Puede ejecutar la herramienta de dos modos distintos:

- **Planeación rápida**: ejecute la herramienta en este modo para obtener las proyecciones de la red y del servidor según el promedio de las máquinas virtuales, discos, almacenamiento y tasa de cambio.
- **Planeación detallada**: ejecute la herramienta en este modo y proporcione detalles de cada carga de trabajo en el nivel de la máquina virtual. Analice la compatibilidad de la máquina virtual y obtenga proyecciones de red y del servidor.

## Antes de comenzar

Antes de ejecutar la herramienta:

1. Recopile información sobre su entorno, incluidas las máquinas virtuales, discos por máquina virtual y almacenamiento por disco.
2. Identifique la tasa de cambio (renovación) diaria para los datos replicados. Para ello, siga estos pasos:

	- Si está replicando máquinas virtuales de Hyper-V, descargue la [herramienta de planeación de capacidad de Hyper-V](https://www.microsoft.com/download/details.aspx?id=39057) para obtener la tasa de cambio. [Más información](site-recovery-capacity-planning-for-hyper-v-replication.md) sobre esta herramienta. Se recomienda ejecutar esta herramienta durante una semana para capturar los promedios.
	- Si está replicando máquinas virtuales de VMware, use [vSphere Replication Capacity Planning Appliance](https://labs.vmware.com/flings/vsphere-replication-capacity-planning-appliance) para calcular la tasa de renovación.
	- Si está replicando servidores físicos, debe realizar los cálculos manualmente.

## Ejecución de Quick Planner
1.	Descargue y abra la herramienta [Capacity Planner de Azure Site Recovery](http://aka.ms/asr-capacity-planner-excel). Como debe ejecutar macros, seleccione las opciones de permitir la edición y de habilitar el contenido cuando se le solicite. 
2.	En **Select a planner type** (Seleccione un tipo de planeador), seleccione **Quick Planner** (Planificador rápido) en el cuadro de lista.

	![Introducción](./media/site-recovery-capacity-planner/getting-started.png)

3.	En la hoja de cálculo **Capacity Planner**, escriba la información que necesite. Debe rellenar todos los campos rodeados por un círculo rojo de la captura de pantalla siguiente.

	- En **Select your scenario** (Seleccionar escenario) elija **Hyper-V to Azure** (Hyper-V a Azure) o **VMware/Physical to Azure** (VMWare/Físico a Azure).
	- En **Average daily data change rate** (Tasa de cambio de datos de promedio diario), agregue la información que ha recopilado con la [herramienta de planeación de capacidad de Hyper-V](site-recovery-capacity-planning-for-hyper-v-replication.md) o la [aplicación de planeación de capacidad de vSphere](https://labs.vmware.com/flings/vsphere-replication-capacity-planning-appliance).  
	- La configuración **Compression** (Compresión) solo se aplica a la compresión ofrecida al replicar máquinas virtuales de VMware o servidores físicos en Azure. Hemos calculado como mínimo un 30 %. Modifique la configuración según sea necesario. Para la replicación de máquinas virtuales de Hyper-V a la compresión de Azure puede usar un dispositivo de terceros, como Riverbed. 
	-  En **Number of retention** (Número de retención) especifique por cuánto tiempo hay que conservar las réplicas. Si está replicando VMware o servidores físicos, especifique un valor en días. Si está replicando Hyper-V, especifique el tiempo en horas.
	-  En **Number of hours in which initial replication for the batch of virtual machines should complete** (Número de horas en las que debe completarse la replicación inicial para el lote de máquinas virtuales) y **Number of virtual machines per initial replication batch** (Número de máquinas virtuales por lote de replicación inicial) hay que especificar la configuración que se ha usado para procesar los requisitos de replicación iniciales. Cuando se implementa Site Recovery, se debe cargar el conjunto de datos inicial completo. 

	![Entradas](./media/site-recovery-capacity-planner/inputs.png)

2.	Después de haber especificado los valores para el entorno de origen, la salida mostrada incluye:

	- Network bandwidth requirements (Requisitos de ancho de banda de red):
		- **Bandwidth required for delta replication** (Mbps) (Ancho de banda necesario para la replicación delta (Mbps)) Esto se calcula según la tasa de cambio de datos promedio diaria.
		- **Bandwidth required for initial replication** (Mbps) (Ancho de banda necesario para la replicación inicial (Mbps)) Esto se calcula con los valores de replicación iniciales especificados. 
	- Requisitos de Azure: proporciona información detallada sobre el almacenamiento, IOPS, cuentas de almacenamiento y discos necesarios en Azure.
		- **Storage required (in GBs)** (Almacenamiento necesario (en GB)) es el almacenamiento total requerido en Azure.
		- **Total IOPS on standard storage accounts** (IOPS total en cuentas de almacenamiento estándar) se calcula en función del tamaño de la unidad IOPS de 8 K en las cuentas de almacenamiento estándar totales. Para Quick Planner, el número se calcula en función de todos los discos de máquinas virtuales de origen y la tasa de cambio de los datos diarios. Para Detailed Planner el número se calcula en función del número total de máquinas virtuales que se asignan a las máquinas virtuales estándar de Azure y a la tasa de cambio de los datos en dichas máquinas virtuales. 
		- **Number of standard storage accounts** (Número de cuentas de almacenamiento estándar) proporciona el número total de cuentas de almacenamiento estándar necesarias para proteger las máquinas virtuales. Tenga en cuenta que una cuenta de almacenamiento estándar puede contener hasta 20 000 IOPS en todas las máquinas virtuales de un almacenamiento estándar y admite un máximo de 500 IOPS por disco. 
		- **Number of blob disks required** (Número de discos de blob necesarios) proporciona el número de discos que se crearán en el almacenamiento de Azure.
		- **Number of premium storage accounts required** (Número de cuentas de almacenamiento premium necesarias) proporciona el número total de cuentas de almacenamiento premium necesarias para proteger las máquinas virtuales. Tenga en cuenta que una máquina virtual de origen con una IOPS elevada (más de 20 000) necesita una cuenta de almacenamiento premium. Una cuenta de almacenamiento premium puede contener hasta 80 000 IOPS.
		- **Total IOPS on premium storage accounts** (IOPS totales en cuentas de almacenamiento premium) se calcula en función del tamaño de la unidad IOPS de 256 K en las cuentas de almacenamiento premium totales. Para Quick Planner, el número se calcula en función de todos los discos de máquinas virtuales de origen y la tasa de cambio de los datos diarios. Para Detailed Planner el número se calcula en función del número total de máquinas virtuales que se asignan a las máquinas virtuales premium de Azure (serie DS y GS) y a la tasa de cambio de los datos en dichas máquinas virtuales. 

	- Other infrastructure requirements (Otros requisitos de infraestructura):
		- Incluye requisitos adicionales para la replicación de VMware/servidor físico en Azure, incluidos los servidores de configuración y proceso.
		- Número de servidores de proceso adicionales necesarios: muestra si se necesitan servidores de proceso adicionales. Es decir, además del servidor de proceso que está configurado en el servidor de configuración de forma predeterminada. N
		- Incluye los requisitos adicionales de la replicación Hyper-V a Azure, como el almacenamiento adicional necesario en el origen.
			
	![Salida](./media/site-recovery-capacity-planner/output.png)
 
## Ejecución de Detailed Planner


1.	Descargue y abra la herramienta [Capacity Planner de Azure Site Recovery](http://aka.ms/asr-capacity-planner-excel). Como debe ejecutar macros, seleccione las opciones de permitir la edición y de habilitar el contenido cuando se le solicite. 
2.	En **Select a planner type** (Seleccione un tipo de planeador), seleccione **Detailed Planner** (Planeador detallado) en el cuadro de lista.

	![Introducción](./media/site-recovery-capacity-planner/getting-started-2.png)

3.	En la hoja de cálculo **Calificación de infraestructura**, escriba la información necesaria. Debe rellenar todos los campos marcados.

	- En **Processor cores** (Núcleos del procesador), especifique el número total de núcleos en un servidor de origen.
	- En **Memory Allocation in MB** (Asignación de memoria en MB), especifique el tamaño de la RAM de un servidor de origen. 
	- **Number of NICs** (Número de NIC) especifica el número de adaptadores de red en un servidor de origen. 
	-  En **Total storage (in GB)** (Almacenamiento total (en GB)) especifique el tamaño total del almacenamiento de la máquina virtual. Por ejemplo, si el servidor de origen tiene tres discos con 500 GB cada uno, el tamaño de almacenamiento total es de 1500 GB.
	-  En **Number of disks attached** (Número de discos conectados) especifique el número total de discos de un servidor de origen.
	-  En **Disk capacity utilization** (Utilización de la capacidad de disco) especifique una utilización promedio.
	-  En **Daily Change rate (%)** (Tasa de cambios diaria (%)) especifique la tasa de cambios de datos diaria de un servidor de origen.
	-  En **Mapping Azure size** (Tamaño de Azure de asignación) escriba el tamaño de la máquina virtual de Azure que desea asignar. Si no desea hacerlo manualmente, haga clic en **Compute IaaS VMs** (Procesar máquinas virtuales de IaaS).

4.	Si hace clic en **Compute IaaS VMs**, esto es lo que hace:

	- Valida las entradas y obtiene a la mejor coincidencia de máquina virtual de Azure para cada máquina virtual que sea elegible para la replicación en Azure. Se produce un error si no se detecta un tamaño adecuado para la máquina virtual de Azure. Por ejemplo, si el número de discos conectados es 65, se produce un error ya que el tamaño máximo de una máquina virtual de Azure es 64.
	- Sugiere una cuenta de almacenamiento que puede usar para una máquina virtual de Azure.
	- Calcula el número total de cuentas de almacenamiento estándar y de cuentas de almacenamiento premium que son necesarias para la carga de trabajo. Desplácese hacia abajo a la derecha para ver el tipo de almacenamiento de Azure y la cuenta de almacenamiento que se puede usar para un servidor de origen.
	- Completa y ordena el resto de la tabla basándose en el tipo de almacenamiento necesario (estándar o premium) asignado a una máquina virtual y el número de discos conectados. En todas las máquinas virtuales que cumplen los requisitos de copia de seguridad en Azure, la columna A (Is VM qualified?) (¿Está calificada la máquina virtual?) muestra Yes (Sí). Si no se puede realizar una copia de seguridad de la máquina virtual en Azure, se muestra un error.

	![Calificación de la carga de trabajo](./media/site-recovery-capacity-planner/workload-qualification.png)


- **Ejemplo**: para las seis máquinas virtuales con los valores mostrados en la tabla, la herramienta calcula y asigna la mejor coincidencia de máquina virtual de Azure y los requisitos de almacenamiento de Azure.

	![Calificación de la carga de trabajo](./media/site-recovery-capacity-planner/workload-qualification-2.png)

- En la salida del ejemplo, observe lo siguiente:
	- Se requieren dos cuentas de almacenamiento estándar y una cuenta de almacenamiento premium para las cinco máquinas virtuales. 
	-  VM3 no cumple los requisitos para la protección porque uno o más discos tienen más de 1 TB.
	-  VM1 y VM2 pueden usar la primera cuenta de almacenamiento estándar
	-  VM4 puede usar la segunda cuenta de almacenamiento estándar
	-  VM5 y VM6 necesitan una cuenta de almacenamiento premium y ambas pueden usar una única cuenta.

	![Calificación de la carga de trabajo](./media/site-recovery-capacity-planner/workload-qualification-3.png)

	>[AZURE.NOTE]La IOPS de almacenamiento se calcula en el nivel de máquina virtual y no en el nivel de disco. Si uno de los discos de una IOPS de máquina virtual de origen es mayor que 500, pero el número total de IOPS de la máquina virtual está dentro del rango de la máquina virtual de Azure estándar admitida (y todos los demás valores como número de discos, número de tarjetas NIC, número de núcleos de CPU, tamaño de la memoria se encuentran dentro de un límite de VM estándar), la herramienta toma una máquina virtual estándar en lugar de almacenamiento premium. Debe actualizar manualmente la asignación de la celda de tamaño de Azure con la máquina virtual de serie DS o GS correspondiente.

	>[AZURE.NOTE]La primera columna es una columna de validación para las máquinas virtuales, discos y renovación. La IOPS de almacenamiento se calcula en el nivel de máquina virtual y no en el nivel de disco. Si uno de los discos de una IOPS de máquina virtual de origen es mayor que 500, pero el número total de IOPS de la máquina virtual está dentro del rango de la máquina virtual de Azure estándar admitida (y todos los demás valores como número de discos, número de tarjetas NIC, número de núcleos de CPU, tamaño de la memoria se encuentran dentro de un límite de VM estándar), la herramienta toma una máquina virtual estándar en lugar de almacenamiento premium. Debe actualizar manualmente la asignación de la celda de tamaño de Azure con la máquina virtual de serie DS o GS correspondiente.

5. Cuando toda la información esté definida, haga clic en **Submit data to the planner tool** (Enviar datos a la herramienta del planificador) para abrir la herramienta **Capacity Planner**. Las cargas de trabajo se resaltan para mostrar si son elegibles para protección o no.


### Ejecución de la herramienta Capacity Planner

1.	Rellene la hoja de cálculo **Capacity Planner**. La palabra 'Workload' aparece en la celda Infra inputs source ( Origen de entradas de infraestructura) para mostrar la entrada de la hoja de trabajo **Workload Qualification** (Calificación de infraestructura).  
2.	Modifique la configuración que desee cambiar y haga clic en **Submit data to the planner tool** (Enviar datos a la herramienta del planificador). 

	![Capacity Planner](./media/site-recovery-capacity-planner/capacity-planner.png)

<!---HONumber=AcomDC_1210_2015-->
