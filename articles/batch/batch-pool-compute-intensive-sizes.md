---
title: Uso de VM de Azure de proceso intensivo con Batch | Microsoft Docs
description: "Cómo aprovechar los tamaños de VM compatibles con RDMA o habilitados para GPU en grupos de Azure Batch"
services: batch
documentationcenter: 
author: dlepow
manager: timlt
editor: 
ms.assetid: 
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2017
ms.author: danlep
ms.openlocfilehash: 26cab5ba892d892e035bd94c52cacabd23eebd0c
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/19/2018
---
# <a name="use-rdma-capable-or-gpu-enabled-instances-in-batch-pools"></a>Uso de instancias compatibles con RDMA o habilitadas para GPU en grupos de Batch

Para ejecutar determinados trabajos de Batch, puede aprovechar los tamaños de VM de Azure diseñados para el cálculo a gran escala. Por ejemplo, para ejecutar [cargas de trabajo MPI](batch-mpi.md) de varias instancias, puede elegir tamaños de las series A8, A9 o H que tienen una interfaz de red para acceso directo a memoria remota (RDMA). Estos tamaños se conectan a una red de InfiniBand para la comunicación entre nodos, lo que puede acelerar las aplicaciones MPI. O bien, para aplicaciones CUDA, puede elegir tamaños de la serie N que incluyen tarjetas de unidad de procesamiento gráfico (GPU) de NVIDIA Tesla.

En este artículo se proporcionan instrucciones y ejemplos para usar algunos de los tamaños especializados de Azure en grupos de Batch. Para especificaciones e información preliminar, consulte:

* Tamaños de VM de proceso de alto rendimiento ([Linux](../virtual-machines/linux/sizes-hpc.md) y [Windows](../virtual-machines/windows/sizes-hpc.md)) 

* Tamaños de VM habilitados para GPU ([Linux](../virtual-machines/linux/sizes-gpu.md) y [Windows](../virtual-machines/windows/sizes-gpu.md)) 


## <a name="subscription-and-account-limits"></a>Límites de la cuenta y la suscripción

* **Cuotas**: la [cuota de núcleos dedicada por cuenta de Batch](batch-quota-limit.md#resource-quotas) puede limitar el número o el tipo de nodos que se puede agregar a un grupo de Batch. Es más probable alcanzar una cuota al elegir tamaños de VM compatibles con RDMA, basados en GPU u otros tamaños de VM de varios núcleos. De forma predeterminada, esta cuota es de 20 núcleos. Se aplica una cuota independiente a [VM de baja prioridad](batch-low-pri-vms.md), en caso de que las use. 

Si necesita solicitar un aumento de cuota, abra una [solicitud de soporte técnico al cliente en línea](../azure-supportability/how-to-create-azure-support-request.md) sin cargo alguno.

* **Disponibilidad por regiones**: Las VM de procesos intensivos podrían no estar disponibles en las regiones donde crea las cuentas de Batch. Para comprobar que un tamaño está disponible, vea [Productos disponibles por región](https://azure.microsoft.com/regions/services/).


## <a name="dependencies"></a>Dependencias

Las funcionalidades RDMA y GPU de tamaños de procesos intensivos solo se admiten en determinados sistemas operativos. Dependiendo del sistema operativo, puede que tenga que instalar o configurar un controlador adicional u otro software. En las tablas siguientes se resumen estas dependencias. Consulte los artículos vinculados para más información. Para conocer las opciones para configurar grupos de Batch, consulte la información restante de este artículo.


### <a name="linux-pools---virtual-machine-configuration"></a>Grupos de Linux: configuración de la máquina virtual

| Tamaño | Capacidad | Sistemas operativos | Requisitos de software | Configuración del grupo |
| -------- | -------- | ----- |  -------- | ----- |
| [H16r, H16mr, A8 y A9](../virtual-machines/linux/sizes-hpc.md#rdma-capable-instances) | RDMA | Ubuntu 16.04 LTS,<br/>SUSE Linux Enterprise Server 12 HPC, o<br/>HPC basado en CentOS<br/>(Azure Marketplace) | Intel MPI 5 | Habilitar la comunicación entre nodos y deshabilitar la ejecución de tareas simultáneas |
| [Serie NC*](../virtual-machines/linux/n-series-driver-setup.md#install-cuda-drivers-for-nc-ncv2-and-nd-vms) | GPU NVIDIA Tesla K80 | Ubuntu 16.04 LTS,<br/>Red Hat Enterprise Linux 7.3 o<br/>Basado en CentOS 7.3<br/>(Azure Marketplace) | Controladores de NVIDIA CUDA Toolkit 9.0 | N/D | 
| [Serie NV](../virtual-machines/linux/n-series-driver-setup.md#install-grid-drivers-for-nv-vms) | GPU NVIDIA Tesla M60 | Ubuntu 16.04 LTS,<br/>Red Hat Enterprise Linux 7.3 o<br/>Basado en CentOS 7.3<br/>(Azure Marketplace) | Controladores de NVIDIA GRID 4.3 | N/D |

*La conectividad de RDMA en máquinas virtuales NC24r se admite en 7.3 HPC basados en Ubuntu 16.04 LTS o CentOS (desde Azure Marketplace) con Intel MPI.



### <a name="windows-pools---virtual-machine-configuration"></a>Grupos de Windows: configuración de la máquina virtual

| Tamaño | Capacidad | Sistemas operativos | Requisitos de software | Configuración del grupo |
| -------- | ------ | -------- | -------- | ----- |
| [H16r, H16mr, A8 y A9](../virtual-machines/windows/sizes-hpc.md#rdma-capable-instances) | RDMA | Windows Server 2012 R2 o<br/>Windows Server 2012 (Azure Marketplace) | Microsoft MPI 2012 R2 o posterior, o<br/> Intel MPI 5<br/><br/>Extensión HpcVMDrivers para VM de Azure | Habilitar la comunicación entre nodos y deshabilitar la ejecución de tareas simultáneas |
| [Serie NC*](../virtual-machines/windows/n-series-driver-setup.md) | GPU NVIDIA Tesla K80 | Windows Server 2016 o <br/>Windows Server 2012 R2 (Azure Marketplace) | Controladores de NVIDIA Tesla o de CUDA Toolkit 9.0| N/D | 
| [Serie NV](../virtual-machines/windows/n-series-driver-setup.md) | GPU NVIDIA Tesla M60 | Windows Server 2016 o<br/>Windows Server 2012 R2 (Azure Marketplace) | Controladores de NVIDIA GRID 4.3 | N/D |

*La conectividad de RDMA en máquinas virtuales NC24r se admite en Windows Server 2012 R2 (desde Azure Marketplace) con la extensión HpcVMDrivers y Microsoft MPI o Intel MPI.

### <a name="windows-pools---cloud-services-configuration"></a>Grupos de Windows: configuración de servicios en la nube

> [!NOTE]
> Los tamaños de la serie N no se admiten en grupos de Batch con la configuración de servicios en la nube.
>

| Tamaño | Capacidad | Sistemas operativos | Requisitos de software | Configuración del grupo |
| -------- | ------- | -------- | -------- | ----- |
| [H16r, H16mr, A8 y A9](../virtual-machines/windows/sizes-hpc.md#rdma-capable-instances) | RDMA | Windows Server 2012 R2,<br/>Windows Server 2012 o<br/>Windows Server 2008 R2 (familia del SO invitado) | Microsoft MPI 2012 R2 o posterior, o<br/>Intel MPI 5<br/><br/>Extensión HpcVMDrivers para VM de Azure | Habilitar la comunicación entre nodos y<br/> deshabilitar la ejecución de tareas simultáneas |





## <a name="pool-configuration-options"></a>Opciones de configuración de grupos

Para configurar un tamaño de VM especializado para el grupo de Batch, las herramientas y las API de Batch proporcionan varias opciones para instalar el software o los controladores necesarios, entre las que se incluyen las siguientes:

* [Tarea de inicio](batch-api-basics.md#start-task): Cargue un paquete de instalación como un archivo de recursos en una cuenta de almacenamiento de Azure en la misma región que la cuenta de Batch. Cree una línea de comandos de la tarea de inicio para instalar el archivo de recursos de forma silenciosa cuando se inicie el grupo. Para más información, consulte la [documentación de la API de REST](/rest/api/batchservice/add-a-pool-to-an-account#bk_starttask).

  > [!NOTE] 
  > La tarea de inicio se debe ejecutar con permisos elevados (administrador) y debe esperar a que se realice correctamente.
  >

* [Paquete de aplicación](batch-application-packages.md): Agregue un paquete de instalación comprimido a su cuenta de Batch y configure una referencia de paquete en el grupo. Esta configuración carga y descomprime el paquete en todos los nodos del grupo. Si el paquete es un instalador, cree una línea de comandos de la tarea de inicio para instalar de forma silenciosa la aplicación en todos los nodos del grupo. Si lo desea, instale el paquete cuando una tarea esté programada para ejecutarse en un nodo.

* [Imagen del grupo personalizada](batch-custom-images.md): Cree una imagen de VM de Windows o Linux personalizada que contenga controladores, software u otras configuraciones necesarias para el tamaño de VM. 

* [Batch Shipyard](https://github.com/Azure/batch-shipyard) configura automáticamente GPU y RDMA para trabajar de forma transparente con cargas de trabajo en contenedores en Azure Batch. Batch Shipyard se controla por completo con archivos de configuración. Hay muchas configuraciones de recetas de ejemplo disponibles que habilitan las cargas de trabajo de GPU y RDMA, como la [receta CNTK GPU](https://github.com/Azure/batch-shipyard/tree/master/recipes/CNTK-GPU-OpenMPI), que preconfigura controladores de GPU en VM de la serie N y carga el software Microsoft Cognitive Toolkit como una imagen de Docker.


## <a name="example-microsoft-mpi-on-an-a8-vm-pool"></a>Ejemplo: Microsoft MPI en un grupo de VM A8

Para ejecutar aplicaciones MPI para Windows en un grupo de nodos de A8 de Azure, necesitará instalar una implementación de MPI admitida. Estos son los pasos de ejemplo para instalar [Microsoft MPI](https://msdn.microsoft.com/library/bb524831(v=vs.85).aspx) en un grupo de Windows mediante un paquete de aplicación de Batch.

1. Descargue el [paquete de instalación](http://go.microsoft.com/FWLink/p/?LinkID=389556) (MSMpiSetup.exe) correspondiente a la versión más reciente de Microsoft MPI.
2. Cree un archivo ZIP del paquete.
3. Cargue el paquete en su cuenta de Batch. Para conocer los pasos, consulte las instrucciones de los [paquetes de aplicación](batch-application-packages.md). Especifique un identificador de aplicación (por ejemplo, *MSMPI*) y una versión (por ejemplo, *8.1*). 
4. Mediante las API de Batch o Azure Portal, cree un grupo en la configuración de servicios en la nube con el número de nodos y la escala deseados. En la tabla siguiente se muestra la configuración de ejemplo para configurar MPI en modo desatendido mediante una tarea de inicio:

| Configuración | Valor |
| ---- | ----- | 
| **Tipo de imagen** | Cloud Services |
| **Familia de SO** | Windows Server 2012 R2 (familia 4 de SO) |
| **Tamaño del nodo** | Estándar A8 |
| **Comunicación entre nodos habilitada** | True |
| **Número máximo de tareas por nodo** | 1 |
| **Referencias de paquetes de aplicación** | MSMPI |
| **Tarea de inicio habilitada** | True<br>**Línea de comandos** - `"cmd /c %AZ_BATCH_APP_PACKAGE_MSMPI#8.1%\\MSMpiSetup.exe -unattend -force"`<br/>**Identidad del usuario**: Usuario automático de grupo, administrador<br/>**Esperar operación correcta**: True

## <a name="example-nvidia-tesla-drivers-on-nc-vm-pool"></a>Ejemplo: Controladores de NVIDIA Tesla en el grupo de VM NC

Para ejecutar aplicaciones CUDA en un grupo de nodos NC de Linux, debe instalar CUDA Toolkit 9.0 en los nodos. El kit de herramientas instala los controladores de GPU NVIDIA Tesla necesarios. Estos son los pasos de ejemplo para implementar una imagen personalizada de Ubuntu 16.04 LTS con los controladores de GPU:

1. Implemente una VM NC6 de Azure en la que se ejecute Ubuntu 16.04 LTS. Por ejemplo, puede crear la VM en la región Centro y Sur de EE. UU. Asegúrese de crear la máquina virtual con un disco administrado.
2. Siga los pasos para conectarse a la VM e [instale los controladores CUDA](../virtual-machines/linux/n-series-driver-setup.md#install-cuda-drivers-for-nc-ncv2-and-nd-vms).
3. Desaprovisione el agente de Linux y luego [capture la imagen de la máquina virtual Linux](../virtual-machines/linux/capture-image.md).
4. Cree una cuenta de Batch en una región que admite las máquinas virtuales de NC.
5. Mediante las API de Batch o Azure Portal , cree un grupo [mediante la imagen personalizada](batch-custom-images.md) y con el número de nodos y la escala deseados. En la siguiente tabla se muestra la configuración de grupo de ejemplo de la imagen:

| Configuración | Valor |
| ---- | ---- |
| **Tipo de imagen** | Imagen personalizada |
| **Imagen personalizada** | Nombre de la imagen |
| **SKU de agente del nodo** | batch.node.ubuntu 16.04 |
| **Tamaño del nodo** | Estándar NC6 |



## <a name="next-steps"></a>pasos siguientes

* Para ejecutar trabajos MPI en un grupo de Azure Batch, consulte los ejemplos [Windows](batch-mpi.md) o [Linux](https://blogs.technet.microsoft.com/windowshpc/2016/07/20/introducing-mpi-support-for-linux-on-azure-batch/).

* Para ejemplos de las cargas de trabajo de GPU en Batch, consulte las recetas de [Batch Shipyard](https://github.com/Azure/batch-shipyard/).