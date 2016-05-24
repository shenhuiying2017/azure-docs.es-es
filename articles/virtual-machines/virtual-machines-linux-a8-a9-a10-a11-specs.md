<properties
 pageTitle="Acerca de las instancias A8 - A11 y Linux | Microsoft Azure"
 description="Obtenga información general y algunas consideraciones sobre el uso de los tamaños de proceso intensivo A8, A9, A10 y A11 para las máquinas virtuales de Linux."
 services="virtual-machines-linux"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management"/>
<tags
ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="infrastructure-services"
 ms.date="05/09/2016"
 ms.author="danlep"/>

# Sobre las instancias de proceso intensivo A8, A9, A10 y A11 

Aquí se proporciona información general y algunas consideraciones sobre el uso de las instancias de Azure A8, A9, A10 y A11, también conocidas como instancias de *proceso intensivo*. Este artículo se centra en el uso de estas instancias para máquinas virtuales con Linux. Este artículo también está disponible para [máquinas virtuales con Windows](virtual-machines-windows-a8-a9-a10-a11-specs.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

[AZURE.INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## Acceso a la red RDMA

En un servicio en la nube o conjunto de disponibilidad individuales, los clústeres de máquinas virtuales con Linux de tamaño A8 y A9 que ejecutan una de las siguientes distribuciones de Linux HPC compatibles y una implementación de MPI compatible puede acceder a la red RDMA de Azure para ejecutar aplicaciones Linux MPI. Para conocer las opciones de implementación y los pasos de una configuración de ejemplo, consulte [Configuración de un clúster de Linux RDMA para ejecutar aplicaciones MPI](virtual-machines-linux-classic-rdma-cluster.md).

* **Distribuciones**: SUSE Linux Enterprise Server (SLES) 12 para HPC, SLES 12 para HPC (Premium), HPC 7.1 basado en CentOS o HPC 6.5 basado en CentOS, implementado desde la imagen de Azure Marketplace

* **MPI**: Biblioteca MPI Intel 5.x

    >[AZURE.NOTE] Intel MPI 5.1.3.181 ya está instalado en las imágenes de HPC basadas en CentOS HPC de Marketplace. Para utilizar Intel MPI en máquinas virtuales HPC de SLES 12, debe instalarlo por separado.

En la actualidad, los controladores Azure Linux RDMA solo se instalan al implementar imágenes HPC de SLES 12 y HPC de CentOS con RDMA habilitado desde Azure Marketplace. Los controladores no se pueden instalar en otras máquinas virtuales con Linux que se implementen.

>[AZURE.NOTE]En las imágenes HPC basadas en CentOS desde Marketplace, las actualizaciones del núcleo están deshabilitadas en el archivo de configuración **yum**. Esto se debe a que los controladores Linux RDMA se distribuyen en forma de paquete RPM y sus actualizaciones de estos podrían no funcionar si se actualiza el kernel.


## Actualizaciones de controladores RDMA para SLES 12
Después de crear una máquina virtual basada en una imagen HPC de SLES 12, es preciso actualizar los controladores RDMA en las máquinas virtuales para obtener la conectividad de red RDMA.

>[AZURE.IMPORTANT]Este paso es **requiere** para las implementaciones de máquinas virtuales HPC de SLES 12 en todas las regiones de Azure. Este paso no es necesario si ha implementado una máquina virtual HPC 7.1 o HPC 6.5 basada en CentOS.

Antes de actualizar los controladores, detenga todos los procesos **zypper** o cualquier proceso que bloquee las bases de datos de repositorio de SUSE en la máquina virtual. De lo contrario, puede que los controladores no se actualicen correctamente.

Para actualizar los controladores Linux RDMA de cada máquina virtual, ejecute uno de los siguientes conjuntos de comandos de la CLI de Azure desde el equipo cliente.

**Para una máquina virtual HPC de SLES 12 aprovisionada en el modelo de implementación clásico**

```
azure config mode asm

azure vm extension set <vm-name> RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1
```

**Para una máquina virtual HPC de SLES 12 aprovisionada en el modelo de implementación de Resource Manager**

```
azure config mode arm

azure vm extension set <resource-group> <vm-name> RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1
```

>[AZURE.NOTE]Los instaladores pueden tardar un tiempo en instalarse y el comando no devolverá resultado. Tras la actualización, la VM se reiniciará y estará lista para su uso en unos minutos.

### Scripts de ejemplo para las actualizaciones de controladores

Si tiene un clúster de máquinas virtuales HPC de SLES 12, puede crear scripts de la actualización del controlador en todos los nodos del clúster. Por ejemplo, el siguiente script actualiza los controladores en un clúster de ocho nodos.

```

#!/bin/bash -x

# Define a prefix naming scheme for compute nodes, e.g., cluster11, cluster12, etc.

vmname=cluster

# Plug in appropriate numbers in the for loop below.

for (( i=11; i<19; i++ )); do

# For VMs created in the classic deployment model use the following command in your script.

azure vm extension set $vmname$i RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1

# For VMs created in the Resource Manager deployment model, use the following command in your script.

# azure vm extension set <resource-group> $vmname$i RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1

done

```


## Consideraciones sobre el HPC Pack y Linux

[HPC Pack](https://technet.microsoft.com/library/jj899572.aspx) es la solución gratuita de administración de trabajos y clústeres HPC de Microsoft. Las últimas versiones de HPC Pack 2012 R2 admiten varias distribuciones de Linux en nodos de ejecución implementados en máquinas virtuales de Azure, administradas por un nodo principal de Windows Server. Los nodos de cálculo de Linux implementados en las máquinas virtuales A8 o A9 y que ejecutan una implementación de MPI compatible pueden ejecutar aplicaciones de MPI que tienen acceso a la red RDMA. Para comenzar, consulte [Introducción a los nodos de proceso de Linux en un clúster de HPC Pack en Azure](virtual-machines-linux-classic-hpcpack-cluster.md).

## Consideraciones sobre la topología de red

* En las máquinas virtuales con Linux de tamaño A8 y A9 de Azure, Eth1 se reserva para el tráfico de red RDMA. No cambie ninguna configuración Eth1 ni ninguna información del archivo de configuración que haga referencia a esta red. Eth0 se reserva para el tráfico de red regular de Azure.

* En Azure, no se admite IP sobre Infiniband (IB). Solo se admite RDMA sobre IB.


## Pasos siguientes

* Para más información sobre la disponibilidad y los precios de las instancias A8, A9, A10 y A11, consulte [Precios de Máquinas virtuales](https://azure.microsoft.com/pricing/details/virtual-machines/#Linux).

* Para más información sobre las capacidades de almacenamiento y los detalles del disco, consulte [Tamaños de máquinas virtuales](virtual-machines-linux-sizes.md).

* Para empezar a implementar y usar las instancias A8 y A9 con RDMA en Linux, consulte [Configuración de un clúster de Linux RDMA para ejecutar aplicaciones MPI](virtual-machines-linux-classic-rdma-cluster.md).

<!---HONumber=AcomDC_0511_2016-->