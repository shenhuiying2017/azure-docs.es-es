<properties
	pageTitle="Capacity Planner de Site Recovery | Microsoft Azure" 
	description="Azure Site Recovery coordina la replicación, la conmutación por error y la recuperación de máquinas virtuales ubicadas localmente en Azure o en un sitio local secundario." 
	services="site-recovery" 
	documentationCenter="" 
	authors="prateek9us" 
	manager="abhiag" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery" 
	ms.date="11/27/2015" 
	ms.author="pratshar"/>

# Herramienta Capacity Planner de Site Recovery

En este documento se explica el uso de herramienta de planeación de la capacidad de Microsoft ASR, que proporciona orientación sobre los recursos que se deben aprovisionar en operaciones de recuperación de sitio sin problemas. La herramienta de planeación de capacidad se puede usar para analizar el entorno de origen (cargas de trabajo), los requisitos de ancho de banda, los requisitos de los recursos (máquinas virtuales, almacenamiento) en el destino y cualquier recurso de servidor adicional que sea necesario en el origen (SC VMM, servidores de configuración, servidores de proceso, etc.). Descargar la herramienta [Capacity Planner de Azure Site Recovery](http://aka.ms/asr-capacity-planner-excel)
 
Hay dos modos en los que se puede usar el programador de capacidad:
 
- **Planeación rápida**: obtenga las proyecciones de la red y del servidor según el promedio de las máquinas virtuales, discos, almacenamiento y tasa de cambio. 
- **Planeación detallada**: proporcione los detalles de cada carga de trabajo en el nivel de máquina virtual. Analice la compatibilidad en el nivel de máquina virtual y también obtenga las proyecciones de la red y de los servidores.
     
Este documento supone que el usuario está familiarizado con Azure Site Recovery. Consulte [Información general sobre Azure Site Recovery](site-recovery-overview.md)

## Introducción
###Requisitos previos
En función del modo de la herramienta de planeación que se quiera usar, cambiarán los detalles necesarios para continuar. Además de los detalles de infraestructura, como máquinas virtuales, discos por máquina virtual, almacenamiento por disco, hay otros detalles necesarios. La clave principal es la tasa de cambio o la tasa de renovación. Si el entorno de origen es Hyper-V, use la [herramienta de planeación de capacidad de Hyper-V](https://www.microsoft.com/en-in/download/details.aspx?id=39057) para obtener la tasa de renovación. Consulte la instrucción para usar la [herramienta de planeación de capacidad de Hyper-V](site-recovery-capacity-planning-for-hyper-v-replication.md). Para VMWare, use la [aplicación de planeación de capacidad de VMware](https://labs.vmware.com/flings/vsphere-replication-capacity-planning-appliance)

##Planeación rápida
1.	Abra el archivo **ASR Capacity Planner.xlsm**. Se requiere la ejecución de macros. Por lo tanto, **"permita la edición"** y **"habilite el contenido"** cuando se le solicite. 
1.	Seleccione **Quick Planner** (Planeador rápido) en el cuadro de lista. Se abrirá otra hoja de cálculo denominada **Capacity Planner**.

	![Introducción](./media/site-recovery-capacity-planner/getting-started.png)

1.	En la hoja de cálculo "Capacity Planner", escriba las entradas que necesite. Todos los campos rodeados de un círculo son campos de entrada obligatorios.
	1.	El cuadro de lista Select your scenario (Seleccionar el escenario) es un cuadro de lista que se puede usar para cambiar el entorno de origen entre "Hyper-V to Azure" (Hyper-V a Azure) y "VMware/Physical to Azure" (VMware/Physical a Azure).
	1. 	Average Daily data change rate (Tasa de cambio de datos de promedio diario) debe medirse. En entornos de Hyper-V, puede usarse la herramienta de planeación de capacidad de Hyper-V. En el caso de VMWare, se puede usar la herramienta de planeación de capacidad de VMWare. Normalmente, se recomienda ejecutar la herramienta durante al menos una semana para que se puedan capturar los picos y promediarse. 
	1. 	Compression (Compresión): se trata de la compresión que ofrece ASR en el escenario VMWare/Physical to Azure. En Hyper-V to Azure, esto puede lograrse mediante una aplicación de terceros, como Riverbed. 
	1. En el escenario VMWare/Physical to Azure, el valor de Number of retention (Número de retención) debe indicarse en días. En el escenario de Hyper-V, este valor se introduce en horas. 
	1. Las dos últimas entradas se usan para calcular la replicación inicial (IR). Cuando se implementa la configuración de ASR, se debe cargar el conjunto de datos inicial completo. Number of hours in which initial replication for the batch of virtual machines should complete (Número de horas en las que debe completarse la replicación inicial para el lote de máquinas virtuales) y Number of virtual machines per initial replication batch (Número de máquinas virtuales por lote de replicación inicial): son los valores deseados para tomarse como entradas. Por otro lado, estos números se pueden modificar para ajustarse el ancho de banda existente. 

	![Entradas](./media/site-recovery-capacity-planner/inputs.png)

1. Una vez especificados los detalles del entorno de origen, la salida mostrada incluye lo siguiente:
	1.	Requisitos de ancho de banda de red
		1. Bandwidth required for delta replication (in Megabits/sec) (Ancho de banda necesario para la replicación delta (en Megabits/s)). Esto se calcula según la tasa de cambio de datos promedio diaria. 
		1. También se presenta el valor de Bandwidth required for initial replication (In Megabits/sec) (Ancho de banda necesario para la replicación inicial (en Megabits por segundo)). Este valor se calcula en función de las entradas de la replicación inicial presentadas (las dos últimas filas) en las entradas. 
	1.	Requisitos de Azure
		1. 	En esta sección se detalla el almacenamiento, el IOPS, las cuentas de almacenamiento y los discos necesarios en Azure. 
	1. 	Otros requisitos de infraestructura 
		1. Cualquier requisito adicional del escenario VMware/Physical to Azure, como los requisitos de servidores de configuración y de servidores de proceso. 
		1. 	Cualquier requisitos adicional del escenario Hyper-V to Azure, como el almacenamiento adicional necesario en el origen.
			
	![Salida](./media/site-recovery-capacity-planner/output.png)
 
##Planeación detallada

1.	Abra el archivo **ASR Capacity Planner.xlsm**. Se requiere la ejecución de macros. Por lo tanto, **"permita la edición"** y **"habilite el contenido"** cuando se le solicite. 
1.	Seleccione **Detailed Planner** (Planeador detallado) en el cuadro de lista. Se abrirá otra hoja de cálculo denominada **Workload Qualification**.

	![Introducción](./media/site-recovery-capacity-planner/getting-started-2.png)


1.	En la hoja de cálculo Workload Qualification, escriba las entradas según sea necesario. Todas las columnas marcadas en rojo son campos obligatorios. Las otras columnas son opcionales.
	1.	Process Cores (Núcleos de proceso): proporcione el número total de núcleos de un servidor de origen.
	1. Memory Allocation in MB (Asignación de memoria en MB): proporcione el tamaño de la RAM de un servidor de origen.
	1.	Number of NICs (Número de tarjetas NIC): proporcione el número de tarjetas NIC de un servidor de origen.
	1. Total Storage (in GB) (Almacenamiento total (en GB)): indique el tamaño total del almacenamiento de la máquina virtual. Por ejemplo, si el servidor de origen tiene tres discos cada uno con 500 GB, el tamaño de almacenamiento total es 1500 GB.
	1. Number of disks attached (Número de discos conectados): proporcione el número total de discos de un servidor de origen.
	1. Disk capacity utilization (Utilización de la capacidad de disco): proporcione una utilización promedio. 
	1. Daily Change rate(%) (Tasa de cambios diaria (%)): proporcione la tasa de cambios de datos diaria de un servidor de origen.
	1. Mapping Azure size (Asignación de tamaño de Azure): puede especificar el tamaño de máquina virtual de Azure que desea asignar o use el botón Compute IaaS VMs (Procesar máquinas virtuales de IaaS) para calcular la mejor coincidencia posible. 

	![Calificación de la carga de trabajo](./media/site-recovery-capacity-planner/workload-qualification.png)
 

1. Si hace clic en el botón **Compute IaaS VMs**, se validan las entradas anteriores y se sugiere la mejor coincidencia de máquina virtual de Azure posible. Si no encuentra el tamaño adecuado de máquina virtual de Azure para un servidor de origen, se produce un error en el servidor. Por ejemplo, cuando el valor del numero de discos conectados para una máquina virtual de origen es 65, se produce un error ya que el número máximo de discos que se pueden conectar a la máquina virtual de Azure de mayor tamaño es 64.


La opción **Compute IaaS VMs** también calcula si una máquina virtual necesita una cuenta de almacenamiento estándar de Azure o una cuenta de almacenamiento premium de Azure. También sugiere cuántas cuentas de almacenamiento estándar y cuántas cuentas de almacenamiento premium son necesarias para la carga de trabajo. Desplácese hacia la derecha para ver el tipo de almacenamiento de Azure y la cuenta de almacenamiento que puede usar para un servidor de origen.
 
**Ejemplo**: para cinco máquinas virtuales con los siguientes valores, la herramienta calcula y asigna la mejor coincidencia de la máquina virtual de tamaño de Azure y sugiere si la máquina virtual necesita almacenamiento estándar o almacenamiento premium.

![Calificación de la carga de trabajo](./media/site-recovery-capacity-planner/workload-qualification-2.png)

En el ejemplo se requieren dos cuentas de almacenamiento estándar y una cuenta de almacenamiento premium para las cinco de máquinas virtuales. VM1 y VM2 pueden usar la primera cuenta de almacenamiento estándar y VM3 puede usar la segunda cuenta de almacenamiento estándar. VM4 y VM5 necesitan una cuenta de almacenamiento premium y se pueden alojar en una única cuenta de almacenamiento premium.

![Calificación de la carga de trabajo](./media/site-recovery-capacity-planner/workload-qualification-3.png)


>[AZURE.NOTE]IOPS se calcula a nivel de máquina virtual y no a nivel de disco. Si uno de los discos de un origen de IOPS de máquina virtual de origen es > 500, pero el número total de IOPS de la máquina virtual está dentro de la máquina virtual de Azure estándar admitida y todos los demás valores (número de discos, número de tarjetas NIC, número de núcleos de CPU, tamaño de la memoria) que se encuentran dentro de un límite de VM estándar, la herramienta toma una máquina virtual estándar en lugar de almacenamiento premium. El usuario debe actualizar manualmente la asignación de la celda de tamaño de Azure con la máquina virtual de serie DS o GS correspondiente.


1.	La primera columna es una columna de validación para las máquinas virtuales, discos y renovación. 
1.	Una vez reunidos todos los detalles, presione el botón **Submit data to the planner tool** (Enviar datos a la herramienta de planeación) en la parte superior. Se abrirá la hoja de cálculo **Capacity Planner** con los promedios rellenados automáticamente, tal como se muestra en la ilustración siguiente. 
1.	Esta acción también resaltará las cargas de trabajo que son elegibles para la protección.


###Capacity Planner

1.	En la hoja de cálculo **Capacity Planner**, la primera fila **Workload** (Carga de trabajo) de Infra Inputs source (Origen de entrada de infraestructura) sugiere que la información de entrada se rellena desde la hoja de cálculo **Workload Qualification** (Calificación de la carga de trabajo).  
1.	Siempre que se requieran cambios, realice los cambios necesarios en la hoja de cálculo **Workload Qualification** y presione el botón **Submit data to the planner tool**. 

	![Capacity Planner](./media/site-recovery-capacity-planner/capacity-planner.png)

<!---HONumber=AcomDC_1203_2015-->