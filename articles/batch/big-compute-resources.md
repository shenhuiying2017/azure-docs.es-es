<properties
   pageTitle="Recursos para cargas de trabajo de lotes y HPC en la nube | Microsoft Azure"
   description="Enumera los recursos técnicos para ayudarle a ejecutar cargas de trabajo por lotes, a gran escala en paralelo y de informática de alto rendimiento (HPC) en Azure."
   services="batch, cloud-services, virtual-machines"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""/>

<tags
   ms.service="multiple"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="big-compute"
   ms.date="09/22/2016"
   ms.author="danlep"/>

# Big Compute en Azure: Recursos técnicos para informática de alto rendimiento y computación por lotes 
Esta es una guía de recursos técnicos para ayudarle a ejecutar cargas de trabajo por lotes, a gran escala en paralelo y de informática de alto rendimiento (HPC) en Azure. Amplíe sus cargas de trabajo de HPC o de lotes existentes a la nube de Azure, o cree nuevas soluciones de Big Compute mediante una gama de los servicios de Azure.

## Opciones de soluciones

Obtenga información sobre las opciones de Big Compute en Azure y elija el enfoque correcto para sus necesidades empresariales y de carga de trabajo.

* [Soluciones HPC y Lote](batch-hpc-solutions.md)

* [Vídeo: Big Compute en la nube con Azure y HPC](https://azure.microsoft.com/documentation/videos/teched-europe-2014-big-compute-in-the-cloud-with-high-performance-computing-on-azure/)


## Azure Batch

[Lote](https://azure.microsoft.com/services/batch/) es un servicio de plataforma que facilita el proceso de habilitar para la nube sus aplicaciones y de ejecutar trabajos sin configurar y administrar un clúster y un programador de trabajos. Use el SDK para integrar aplicaciones cliente con Azure Batch a través de varios lenguajes, almacenar datos en Azure y crear canalizaciones de ejecución de trabajos.

* [Documentación](https://azure.microsoft.com/documentation/services/batch/)

* Referencia de [.NET](https://msdn.microsoft.com/library/azure/mt348682.aspx), [Python](http://azure-sdk-for-python.readthedocs.io/latest/), [Node.js](http://azure.github.io/azure-sdk-for-node/azure-batch/latest/), [Java](http://azure.github.io/azure-sdk-for-java/) y de API de [REST](https://msdn.microsoft.com/library/azure/dn820158.aspx)

* Referencia de la [biblioteca .NET de administración de Lote](https://msdn.microsoft.com/library/mt463120.aspx)

* Tutoriales: Introducción a la [biblioteca de Lote de Azure para .NET](batch-dotnet-get-started.md) y el [cliente Python de Lote](batch-python-tutorial.md)

* [Foro de Lote](https://social.msdn.microsoft.com/Forums/es-ES/home?forum=azurebatch)

* [Vídeos de Lote](https://azure.microsoft.com/documentation/videos/index/?services=batch)

## Soluciones de clúster de HPC

Implemente o amplíe su clúster de Windows o Linux HPC existente para ejecutar sus cargas de trabajo intensivas de proceso.

### Microsoft HPC Pack

HPC Pack es la solución HPC gratuita de Microsoft que se basa en las tecnologías de Microsoft Azure y Windows Server, capaz de ejecutar cargas de trabajo HPC de Windows y Linux.

* [Descargar HPC Pack 2012 R2 Update 3](https://www.microsoft.com/download/details.aspx?id=49922)

* [Documentación](https://technet.microsoft.com/library/jj899572.aspx)


* Opciones de clúster de HPC Pack de [Linux](../virtual-machines/virtual-machines-linux-hpcpack-cluster-options.md) y [Windows](../virtual-machines/virtual-machines-windows-hpcpack-cluster-options.md) en Azure

* [Irrumpir en instancias de trabajo de Azure con HPC Pack](https://technet.microsoft.com/library/gg481749.aspx)

* [Irrumpir en Lote de Azure con HPC Pack](https://technet.microsoft.com/library/mt612877.aspx)


* [Foros de Windows HPC](https://social.microsoft.com/Forums/home?category=windowshpc)

### Soluciones de clúster de Linux y OSS

Use estas plantillas de Azure para implementar clústeres de HPC de Linux.

* [Establecimiento de un clúster SLURM](https://azure.microsoft.com/documentation/templates/slurm/) y [una entrada de blog](http://blogs.technet.com/b/windowshpc/archive/2015/06/06/deploy-a-slurm-cluster-on-azure.aspx)

* [Establecimiento de un clúster de par](https://azure.microsoft.com/documentation/templates/torque-cluster/)

* [Edición de nube de Intel para Software de Lustre - Eval](https://azure.microsoft.com/marketplace/partners/intel/lustre-cloud-edition-evaleval-lustre-2-7/)

## Microsoft MPI

[Microsoft MPI](https://msdn.microsoft.com/library/bb524831.aspx) (MS-MPI) es una implementación de Microsoft del estándar de la interfaz de especificación de mensajes para desarrollar y ejecutar aplicaciones en paralelo en la plataforma de Windows. La versión más reciente es MS-MPI v7.


* [Descargar MS-MPI](http://go.microsoft.com/FWLink/p/?LinkID=389556)

* [Referencia de MS-MPI](https://msdn.microsoft.com/library/dn473458.aspx)

* [Foro de MPI](https://social.microsoft.com/Forums/es-ES/home?forum=windowshpcmpi)

## Instancias de proceso intensivo

Azure ofrece un [intervalo de tamaños de VM](../virtual-machines/virtual-machines-windows-sizes.md), que incluye instancias de [proceso intensivo](../virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md) con capacidad para conectarse con una red RDMA de back-end, para ejecutar cargas de trabajo HPC de Linux y Windows.


* [Configuración de un clúster de Linux RDMA para ejecutar aplicaciones MPI](../virtual-machines/virtual-machines-linux-classic-rdma-cluster.md)

* [Configurar un clúster de Windows RDMA con Microsoft HPC Pack para ejecutar aplicaciones MPI](../virtual-machines/virtual-machines-windows-classic-hpcpack-rdma-cluster.md)



## Ejemplos y demos

* [Ejemplos de código de Lote de Azure para C# y Python](https://github.com/Azure/azure-batch-samples)

* Kit de herramientas de [Batch Shipyard](https://azure.github.io/batch-shipyard/) para facilitar la implementación sencilla de cargas de trabajo de acoplamiento en estilo de lote

* [Versión de prueba de SUSE Linux Enterprise Server para HPC](https://azure.microsoft.com/marketplace/partners/suse/suselinuxenterpriseserver12optimizedforhighperformancecompute/)

## Servicios de Azure relacionados

* [Factoría de datos](https://azure.microsoft.com/documentation/services/data-factory/)

* [Aprendizaje automático](https://azure.microsoft.com/documentation/services/machine-learning/)

* [HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/)

* [Máquinas virtuales](https://azure.microsoft.com/documentation/services/virtual-machines/)

* [Conjuntos de escalado de máquina virtual](https://azure.microsoft.com/documentation/services/virtual-machine-scale-sets/)

* [Servicios en la nube](https://azure.microsoft.com/documentation/services/cloud-services/)

* [Servicio de aplicaciones](https://azure.microsoft.com/documentation/services/app-service/)

* [Servicios multimedia](https://azure.microsoft.com/documentation/services/media-services/)

* [Funciones](https://azure.microsoft.com/documentation/services/functions/)

## Proyectos de arquitectura

* Archivo PDF [HPC and data orchestration using Azure Batch and Azure Data Factory](http://go.microsoft.com/fwlink/?linkid=717686) (Orquestación de HPC y datos mediante Lote de Azure y Data Factory de Azure) y este [artículo](../data-factory/data-factory-data-processing-using-batch.md)

## Soluciones del sector

* [Mercados de capital y banca](https://finance.azure.com/)

* [Simulaciones de ingeniería](https://simulation.azure.com/)

## Testimonios de clientes

* [ANEO](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=4168)

* [d3View](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=22088)

* [Ludwig Institute of Cancer Research (Instituto Ludwig de investigación del cáncer)](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=5830)

* [Microsoft Research](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=15634)

* [Milliman](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=14967)

* [Mitsubishi UFJ Securities International](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=26266)

* [Schlumberger](http://azure.microsoft.com/blog/big-compute-for-large-engineering-simulations)

* [Towers Watson](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18222)

* [UberCloud](https://simulation.azure.com/casestudies/Team-182-ABB-UC-Final.pdf)



## Pasos siguientes

* Para los anuncios más recientes, vea el [blog del equipo de Microsoft HPC y Batch](http://blogs.technet.com/b/windowshpc/) y el [blog de Azure](https://azure.microsoft.com/blog/tag/hpc/).
* Vea también [Novedades de Batch](https://azure.microsoft.com/updates/?service=batch) o suscríbase a la [fuente RSS](https://azure.microsoft.com/updates/feed/?service=batch).

<!---HONumber=AcomDC_0928_2016-->