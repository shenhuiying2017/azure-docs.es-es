---
title: Recursos para cargas de trabajo por lotes y HPC en la nube de Azure | Microsoft Docs
description: "Enumera los recursos técnicos para ayudarle a ejecutar cargas de trabajo por lotes, a gran escala en paralelo y de informática de alto rendimiento (HPC) en Azure."
services: batch, cloud-services, virtual-machines
documentationcenter: 
author: dlepow
manager: timlt
editor: 
ms.assetid: 6f8be911-c841-41ae-88d3-3bcfc029eb7f
ms.service: multiple
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: big-compute
ms.date: 03/17/2017
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: aff11fe3d7e4cea3580f73d54a53d9624e4f3ac9
ms.lasthandoff: 04/03/2017


---
# <a name="big-compute-in-azure-technical-resources-for-batch-and-high-performance-computing"></a>Big Compute en Azure: Recursos técnicos para informática de alto rendimiento y computación por lotes
Esta es una guía de recursos técnicos para ayudarle a ejecutar cargas de trabajo por lotes, a gran escala en paralelo y de informática de alto rendimiento (HPC) en Azure. Amplíe sus cargas de trabajo de HPC o de lotes existentes a la nube de Azure, o cree nuevas soluciones de Big Compute mediante una gama de los servicios de Azure.

## <a name="solutions-options"></a>Opciones de soluciones
Obtenga información sobre las opciones de Big Compute en Azure y elija el enfoque correcto para sus necesidades empresariales y de carga de trabajo.

* [Soluciones HPC y Lote](batch-hpc-solutions.md)
* [Vídeo: Big Compute en la nube con Azure y HPC](https://azure.microsoft.com/documentation/videos/teched-europe-2014-big-compute-in-the-cloud-with-high-performance-computing-on-azure/)

## <a name="azure-batch"></a>Azure Batch
[Lote](https://azure.microsoft.com/services/batch/) es un servicio de plataforma que facilita el proceso de habilitar para la nube sus aplicaciones y de ejecutar trabajos sin configurar y administrar un clúster y un programador de trabajos. Use el SDK para integrar aplicaciones cliente con Azure Batch a través de varios lenguajes, almacenar datos en Azure y crear canalizaciones de ejecución de trabajos.

* [Documentación](https://azure.microsoft.com/documentation/services/batch/)
* Referencia de [.NET](https://msdn.microsoft.com/library/azure/mt348682.aspx), [Python](http://azure-sdk-for-python.readthedocs.io/latest/), [Node.js](http://azure.github.io/azure-sdk-for-node/azure-batch/latest/), [Java](http://azure.github.io/azure-sdk-for-java/) y API de [REST](https://msdn.microsoft.com/library/azure/dn820158.aspx)
* [biblioteca .NET de administración de Lote](https://msdn.microsoft.com/library/mt463120.aspx) 
* Tutoriales: Introducción a la [biblioteca de Azure Batch para .NET](batch-dotnet-get-started.md) y el [cliente Python de Batch](batch-python-tutorial.md)
* [Foro de Batch](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurebatch)
* [Vídeos de Batch](https://azure.microsoft.com/documentation/videos/index/?services=batch)

## <a name="hpc-cluster-solutions"></a>Soluciones de clúster de HPC
Implemente o amplíe su clúster de Windows o Linux HPC existente para ejecutar sus cargas de trabajo intensivas de proceso.  

### <a name="microsoft-hpc-pack"></a>Microsoft HPC Pack
HPC Pack es la solución HPC gratuita de Microsoft que se basa en las tecnologías de Microsoft Azure y Windows Server, capaz de ejecutar cargas de trabajo HPC de Windows y Linux.  

* [Descargar HPC Pack 2016](https://www.microsoft.com/download/details.aspx?id=54507)
* [Descargar HPC Pack 2012 R2 Update 3](https://www.microsoft.com/download/details.aspx?id=49922)
* [Documentación](https://technet.microsoft.com/library/jj899572.aspx)
* Opciones del clúster de HPC Pack de [Linux](../virtual-machines/linux/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) y [Windows](../virtual-machines/windows/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) en Azure 
* [Irrumpir en instancias de trabajo de Azure con HPC Pack](https://technet.microsoft.com/library/gg481749.aspx)
* [Expansión a Azure Batch con HPC Pack](https://technet.microsoft.com/library/mt612877.aspx)
* [Foros de Windows HPC](https://social.microsoft.com/Forums/home?category=windowshpc)

### <a name="linux-and-oss-cluster-solutions"></a>Soluciones de clúster de Linux y OSS
Use estas plantillas de Azure para implementar clústeres de HPC de Linux.

* [Establecimiento de un clúster SLURM](https://azure.microsoft.com/documentation/templates/slurm/) y una [entrada de blog](http://blogs.technet.com/b/windowshpc/archive/2015/06/06/deploy-a-slurm-cluster-on-azure.aspx)
* [Establecimiento de un clúster de par](https://azure.microsoft.com/documentation/templates/torque-cluster/)
* [Plantillas de cuadrícula proceso con PBS Professional](https://github.com/xpillons/azure-hpc/tree/master/Compute-Grid-Infra)

## <a name="hpc-storage"></a>Almacenamiento de HPC
* [Sistemas de archivos en paralelo para el almacenamiento de HPC en Azure](https://blogs.msdn.microsoft.com/azurecat/2017/03/17/parallel-file-systems-for-hpc-storage-on-azure/)
* [Edición de nube de Intel para Software de Lustre - Eval](https://azure.microsoft.com/marketplace/partners/intel/lustre-cloud-edition-evaleval-lustre-2-7/)
* [BeeGFS en la plantilla de CentOS 7.2](https://github.com/smith1511/hpc/tree/master/beegfs-shared-on-centos7.2)




## <a name="microsoft-mpi"></a>Microsoft MPI
[Microsoft MPI](https://msdn.microsoft.com/library/bb524831.aspx) (MS-MPI) es una implementación de Microsoft del estándar de la interfaz de especificación de mensajes para desarrollar y ejecutar aplicaciones en paralelo en la plataforma de Windows.

* [Descargar MS-MPI](http://go.microsoft.com/FWLink/p/?LinkID=389556)
* [Referencia de MS-MPI](https://msdn.microsoft.com/library/dn473458.aspx)
* [Foro de MPI](https://social.microsoft.com/Forums/en-us/home?forum=windowshpcmpi)

## <a name="compute-intensive-instances"></a>Instancias de proceso intensivo
Azure ofrece un [rango de tamaños de VM](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), que incluye instancias de [serie H con un proceso intensivo](../virtual-machines/windows/a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) con capacidad para conectarse a una red RDMA de back-end, para ejecutar cargas de trabajo HPC de Linux y Windows. 

* [Configuración de un clúster de Linux RDMA para ejecutar aplicaciones MPI](../virtual-machines/linux/classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Configuración de un clúster de Windows RDMA con HPC Pack para ejecutar aplicaciones MPI](../virtual-machines/windows/classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

Para cargas de trabajo intensivas de la GPU, eche un vistazo al artículo de [tamaños NC y NV](https://azure.microsoft.com/blog/azure-n-series-general-availability-on-december-1/).

## <a name="samples-and-demos"></a>Ejemplos y demos
* [Ejemplos de código de Lote de Azure para C# y Python](https://github.com/Azure/azure-batch-samples)
* El kit de herramientas [Batch Shipyard](https://azure.github.io/batch-shipyard/) para facilitar la implementación en Azure Batch de cargas de trabajo de estilo lote "dockerizadas".
* El paquete de R[doAzureParallel](http://www.github.com/Azure/doAzureParallel), creado a partir de Azure Batch
* [Versión de prueba de SUSE Linux Enterprise Server para HPC](https://azure.microsoft.com/marketplace/partners/suse/suselinuxenterpriseserver12optimizedforhighperformancecompute/)

## <a name="related-azure-services"></a>Servicios de Azure relacionados
* [Factoría de datos](https://azure.microsoft.com/documentation/services/data-factory/)
* [Aprendizaje automático](https://azure.microsoft.com/documentation/services/machine-learning/)
* [HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/)
* [Máquinas virtuales](https://azure.microsoft.com/documentation/services/virtual-machines/)
* [Conjuntos de escalado de máquina virtual](https://azure.microsoft.com/documentation/services/virtual-machine-scale-sets/)
* [Servicios en la nube](https://azure.microsoft.com/documentation/services/cloud-services/)
* [Servicio de aplicaciones](https://azure.microsoft.com/documentation/services/app-service/)
* [Servicios multimedia](https://azure.microsoft.com/documentation/services/media-services/)
* [Funciones](https://azure.microsoft.com/documentation/services/functions/)

## <a name="architecture-blueprints"></a>Proyectos de arquitectura
* Archivo PDF [HPC and data orchestration using Azure Batch and Azure Data Factory](http://go.microsoft.com/fwlink/?linkid=717686) (Orquestación de HPC y datos mediante Lote de Azure y Data Factory de Azure) y este [artículo](../data-factory/data-factory-data-processing-using-batch.md)

## <a name="industry-solutions"></a>Soluciones del sector
* [Mercados de capital y banca](https://finance.azure.com/)
* [Simulaciones de ingeniería](https://simulation.azure.com/) 

## <a name="customer-stories"></a>Testimonios de clientes
* [ANEO](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=4168) 
* [d3View](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=22088)
* [Ludwig Institute of Cancer Research (Instituto Ludwig de investigación del cáncer)](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=5830)
* [Microsoft Research](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=15634)
* [Milliman](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=14967)
* [Mitsubishi UFJ Securities International](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=26266)
* [Schlumberger](http://azure.microsoft.com/blog/big-compute-for-large-engineering-simulations)
* [Towers Watson](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18222)
* [UberCloud](https://simulation.azure.com/casestudies/Team-182-ABB-UC-Final.pdf)

## <a name="next-steps"></a>Pasos siguientes
* Para los anuncios más recientes, lea el [blog del equipo de Microsoft HPC y Batch](http://blogs.technet.com/b/windowshpc/) y el [blog de Azure](https://azure.microsoft.com/blog/tag/hpc/).
* Vea también [Actualizaciones de Azure](https://azure.microsoft.com/updates/?service=batch) o suscríbase a la [fuente RSS](https://azure.microsoft.com/updates/feed/?service=batch).


