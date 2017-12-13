---
title: "Estimación de la capacidad de replicación en Azure | Microsoft Docs"
description: "Use este artículo para hacer estimaciones cuando se realizan replicaciones con Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 0a1cd8eb-a8f7-4228-ab84-9449e0b2887b
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/28/2017
ms.author: nisoneji
ms.openlocfilehash: f504888aac9e8d97e974fb5bec0a12a8ede39c76
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/05/2017
---
La nueva versión mejorada de [Azure Site Recovery Deployment Planner para Hyper-V en Azure](site-recovery-hyper-v-deployment-planner.md) está disponible ahora y reemplaza a la antigua herramienta. Use la herramienta nueva para el planeamiento de la implementación. Proporciona las siguientes directrices:
* Evaluación de la idoneidad de las máquinas virtuales en función del número de discos, el tamaño de estos, las IOPS, la actividad de datos y algunas otras características de las máquinas virtuales.
* Necesidad de ancho de banda de red frente a evaluación de RPO.
* Requisitos de infraestructura de Azure.
* Requisitos de la infraestructura local.
* Guía para el procesamiento por lotes de la replicación inicial.
* Costo estimado total de la recuperación ante desastres en Azure.

# <a name="plan-capacity-for-protecting-hyper-v-vms-with-site-recovery"></a>Planeamiento de la capacidad para proteger las máquinas virtuales de Hyper-V con Site Recovery

La herramienta Azure Site Recovery Capacity Planner le ayuda a determinar los requisitos de capacidad al replicar máquinas virtuales de Hyper-V con Azure Site Recovery.

Use Site Recovery Capacity Planner para analizar el entorno de origen y las cargas de trabajo, calcular las necesidades de ancho de banda, los recursos de servidor que necesita en la ubicación de origen y los recursos (máquinas virtuales, almacenamiento, etc.) que necesitará en su ubicación de destino.

Puede ejecutar la herramienta de dos modos distintos:

* **Planeación rápida**: ejecute la herramienta en este modo para obtener las proyecciones de la red y del servidor según el promedio de las máquinas virtuales, discos, almacenamiento y tasa de cambio.
* **Planeación detallada**: ejecute la herramienta en este modo y proporcione detalles de cada carga de trabajo en el nivel de la máquina virtual. Analice la compatibilidad de la máquina virtual y obtenga proyecciones de red y del servidor.

## <a name="before-you-start"></a>Antes de comenzar


1. Recopile información sobre su entorno, incluidas las máquinas virtuales, discos por máquina virtual y almacenamiento por disco.
2. Identifique la tasa de cambio (renovación) diaria para los datos replicados. Para ello, descargue la [herramienta de planeamiento de la capacidad de Hyper-V](https://www.microsoft.com/download/details.aspx?id=39057) y obtendrá la tasa de cambio. [Más información](site-recovery-capacity-planning-for-hyper-v-replication.md) sobre esta herramienta. Se recomienda ejecutar esta herramienta durante una semana para capturar los promedios.
   

## <a name="run-the-quick-planner"></a>Ejecución de Quick Planner
1. Descargue la herramienta [Capacity Planner de Azure Site Recovery](http://aka.ms/asr-capacity-planner-excel) y ábrala. Como debe ejecutar macros, seleccione las opciones de permitir la edición y de habilitar el contenido cuando se le solicite.
2. En **Select a planner type** (Seleccione un tipo de planeador), seleccione **Quick Planner** (Planificador rápido) en el cuadro de lista.

   ![Introducción](./media/site-recovery-capacity-planner/getting-started.png)
3. En la hoja de cálculo **Capacity Planner** , escriba la información que necesite. Debe rellenar todos los campos rodeados por un círculo rojo de la captura de pantalla siguiente.

   * En **Select your scenario** (Seleccionar escenario) elija **Hyper-V to Azure** (Hyper-V a Azure) o **VMware/Physical to Azure** (VMWare/Físico a Azure).
   * En **Average daily data change rate (%)** [Tasa media diaria de cambio de datos (%)], agregue la información que ha recopilado con la [herramienta de planeamiento de capacidad de Hyper-V](site-recovery-capacity-planning-for-hyper-v-replication.md) o con [Azure Site Recovery Deployment Planner](./site-recovery-deployment-planner.md).  
   * La configuración de **compresión** no se utiliza al replicar máquinas virtuales de Hyper-V en Azure. Para la compresión, use un dispositivo de terceros como Riverbed.
   * En **Entradas de retención**, especifique durante cuánto tiempo hay que conservar las réplicas, en horas.
   * En **Number of hours in which initial replication for the batch of virtual machines should complete** (Número de horas en las que debe completarse la replicación inicial para el lote de máquinas virtuales) y **Number of virtual machines per initial replication batch** (Número de máquinas virtuales por lote de replicación inicial) hay que especificar la configuración que se ha usado para procesar los requisitos de replicación iniciales.  Cuando se implementa Site Recovery, se debe cargar el conjunto de datos inicial completo.

   ![Entradas](./media/site-recovery-capacity-planner/inputs.png)
4. Después de haber especificado los valores para el entorno de origen, la salida mostrada incluye:

   * **Ancho de banda necesario para la replicación diferencial** (MB/s). El ancho de banda de red necesario para la replicación diferencial se calcula según la tasa media diaria de cambio de datos.
   * **Ancho de banda necesario para la replicación inicial** (MB/s). El ancho de banda de red para la replicación inicial se calcula según los valores de replicación inicial establecidos.
   * **Almacenamiento necesario (en GB)** es el almacenamiento total requerido en Azure.
   * **IOPS totales en cuentas de almacenamiento estándar** se calcula en función de un tamaño de unidad IOPS de 8 K en las cuentas de almacenamiento estándar totales.  Para Quick Planner, el número se calcula en función de todos los discos de máquinas virtuales de origen y la tasa de cambio de los datos diarios. Para Detailed Planner el número se calcula en función del número total de máquinas virtuales que se asignan a las máquinas virtuales estándar de Azure y a la tasa de cambio de los datos en dichas máquinas virtuales.
   * **Número de cuentas de almacenamiento estándar** proporciona el número total de cuentas de almacenamiento estándar necesarias para proteger las máquinas virtuales. Una cuenta de almacenamiento estándar puede contener hasta 20 000 IOPS en todas las máquinas virtuales de un almacenamiento estándar y admite un máximo de 500 IOPS por disco.
   * **Número de discos blob necesarios** proporciona el número de discos que se crearán en el almacenamiento de Azure.
   * **Número de cuentas de almacenamiento premium necesarias** proporciona el número total de cuentas de almacenamiento premium necesarias para proteger las máquinas virtuales. Una máquina virtual de origen con una IOPS elevada (más de 20000) necesita una cuenta de Premium Storage. Una cuenta de almacenamiento premium puede contener hasta 80 000 IOPS.
   * **IOPS totales en cuentas de almacenamiento premium** se calcula en función de un tamaño de unidad IOPS de 256 K en las cuentas de almacenamiento premium totales.  Para Quick Planner, el número se calcula en función de todos los discos de máquinas virtuales de origen y la tasa de cambio de los datos diarios. Para Detailed Planner el número se calcula en función del número total de máquinas virtuales que se asignan a las máquinas virtuales premium de Azure (serie DS y GS) y a la tasa de cambio de los datos en dichas máquinas virtuales.
   * **Número de servidores de configuración necesarios** muestra cuántos servidores de configuración son necesarios para la implementación.
   * **Número de servidores de procesos adicionales necesarios** muestra si se requieren servidores de procesos adicionales, además del servidor de proceso configurado en el servidor de configuración de forma predeterminada.
   * **100 % de almacenamiento adicional en origen** muestra si se necesita almacenamiento adicional en la ubicación de origen.

   ![Salida](./media/site-recovery-capacity-planner/output.png)

## <a name="run-the-detailed-planner"></a>Ejecución de Detailed Planner

1. Descargue la herramienta [Capacity Planner de Azure Site Recovery](http://aka.ms/asr-capacity-planner-excel) y ábrala. Como debe ejecutar macros, seleccione las opciones de permitir la edición y de habilitar el contenido cuando se le solicite.
2. En **Seleccionar un tipo de planificador**, elija **Planificador detallado** en el cuadro de lista.

   ![Introducción](./media/site-recovery-capacity-planner/getting-started-2.png)
3. En la hoja de cálculo **Workload Qualification** , escriba la información necesaria. Debe rellenar todos los campos marcados.

   * En **Núcleos de procesador**, especifique el número total de núcleos de un servidor de origen.
   * En **Asignación de memoria en MB**, especifique el tamaño de la RAM de un servidor de origen.
   * El valor **Número de NIC** especifica el número de adaptadores de red de un servidor de origen.
   * En **Almacenamiento total (en GB)**, especifique el tamaño total del almacenamiento de la máquina virtual. Por ejemplo, si el servidor de origen tiene 3 discos con 500 GB cada uno, el tamaño de almacenamiento total es de 1500 GB.
   * En **Número de discos conectados**, especifique el número total de discos de un servidor de origen.
   * En **Utilización de la capacidad de disco**, especifique el promedio de uso.
   * En **Tasa de cambios diaria (%)**, especifique la tasa de cambios de datos diaria de un servidor de origen.
   * En **Tamaño de asignación de Azure**, escriba el tamaño de la máquina virtual de Azure que desea asignar. Si no quiere hacerlo manualmente, haga clic en **Compute IaaS VMs** (Calcular máquinas virtuales IaaS). Si especifica un valor de configuración manual y hace clic en Procesar máquinas virtuales de IaaS, puede que se sobrescriba la configuración manual, ya que el proceso identifica automáticamente la mejor coincidencia de tamaño de máquina virtual de Azure.

   ![Workload Qualification](./media/site-recovery-capacity-planner/workload-qualification.png)
4. Al hacer clic en **Procesar máquinas virtuales de IaaS** :

   * Se validan las entradas obligatorias.
   * Se calculan las IOPS y se sugiere la mejor coincidencia de tamaño de máquina virtual de Azure para cada máquina virtual apta para la replicación en Azure. Se produce un error si no se detecta un tamaño adecuado para la máquina virtual de Azure. Por ejemplo, si el número de discos conectados es 65, se produce un error ya que el tamaño máximo de una máquina virtual de Azure es 64.
   * Sugiere una cuenta de almacenamiento que puede usar para una máquina virtual de Azure.
   * Calcula el número total de cuentas de almacenamiento estándar y de cuentas de almacenamiento premium que son necesarias para la carga de trabajo. Desplácese hacia abajo para ver el tipo de almacenamiento de Azure y la cuenta de almacenamiento que puede usar para un servidor de origen.
   * Completa y ordena el resto de la tabla basándose en el tipo de almacenamiento necesario (estándar o premium) asignado a una máquina virtual y el número de discos conectados. En todas las máquinas virtuales que cumplen los requisitos de Azure, la columna **Is VM qualified?** (¿Está calificada la máquina virtual?) muestra **Yes** (Sí). Si no se puede realizar una copia de seguridad de la máquina virtual en Azure, se muestra un error.

Las columnas AA hasta AE muestran los resultados y proporcionan información de cada máquina virtual.

![Workload Qualification](./media/site-recovery-capacity-planner/workload-qualification-2.png)

### <a name="example"></a>Ejemplo
Como ejemplo, para seis máquinas virtuales con los valores que se muestran en la tabla, la herramienta calcula y asigna la mejor coincidencia de máquina virtual de Azure y los requisitos de almacenamiento de Azure.

![Workload Qualification](./media/site-recovery-capacity-planner/workload-qualification-3.png)

* En el resultado del ejemplo, tenga en cuenta lo siguiente:

  * La primera columna es una columna de validación para las máquinas virtuales, discos y renovación.
  * Se requieren dos cuentas de almacenamiento estándar y una cuenta de almacenamiento premium para las cinco máquinas virtuales.
  * VM3 no cumple los requisitos para la protección porque uno o más discos tienen más de 1 TB.
  * VM1 y VM2 pueden usar la primera cuenta de almacenamiento estándar
  * VM4 puede usar la segunda cuenta de almacenamiento estándar
  * VM5 y VM6 necesitan una cuenta de Premium Storage y ambas pueden usar una única cuenta.

    > [!NOTE]
    > Las IOPS de almacenamiento estándar y premium se calculan en el nivel de máquina virtual y no en el nivel de disco. Una máquina virtual estándar puede controlar hasta 500 IOPS por disco. Si las IOPS de un disco son más de 500, necesitará Premium Storage. Sin embargo, si las IOPS de un disco son más de 500, pero las IOPS de todos los discos de máquina virtual están dentro de los límites admitidos para máquinas virtuales de Azure estándar (tamaño de máquina virtual, número de discos, número de adaptadores, CPU, memoria), el planificador elige una máquina virtual estándar en lugar de la serie DS o GS. El usuario debe actualizar manualmente la asignación de la celda de tamaño de Azure con la máquina virtual de serie DS o GS correspondiente.


Cuando toda la información esté definida, haga clic en **Enviar datos a la herramienta del planificador** para abrir la herramienta**Capacity Planner**. Las cargas de trabajo se resaltan para mostrar si cumplen los requisitos para la protección.

### <a name="submit-data-in-the-capacity-planner"></a>Enviar datos en Capacity Planner
1. Cuando se abre la hoja de datos **Capacity Planner** , esta se rellena en función de la configuración que haya especificado. La palabra «Workload» aparece en la celda **Origen de entradas de infraestructura** para mostrar la entrada de la hoja de cálculo **Workload Qualification**.
2. Si desea realizar cambios, deberá modificar la hoja de cálculo **Workload Qualification** y hacer clic de nuevo en **Enviar datos a la herramienta del planificador**.  

   ![Capacity Planner](./media/site-recovery-capacity-planner/capacity-planner.png)

## <a name="next-steps"></a>Pasos siguientes

[Aprenda a ejecutar](site-recovery-capacity-planning-for-hyper-v-replication.md) la herramienta Capacity Planner.
