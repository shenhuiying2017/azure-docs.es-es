<properties
   pageTitle="Big Compute: Recursos técnicos para informática de alto rendimiento (HPC) y computación por lotes | Microsoft Azure"
   description="Enumera los recursos técnicos para ayudarle a ejecutar cargas de trabajo de HPC, por lotes y en paralelo a gran escala en Azure."
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
   ms.date="07/28/2015"
   ms.author="danlep"/>

# Big Compute en Azure: Recursos técnicos para informática de alto rendimiento (HPC) y computación por lotes
Se trata de una guía sobre recursos técnicos que le ayudará a ejecutar cargas de trabajo de HPC, por lotes y en paralelo a gran escala en Azure. Amplíe sus cargas de trabajo de HPC o de lotes existentes a la nube de Azure, o cree nuevas soluciones de Big Compute en Azure mediante una gama de los servicios de Azure.

## Opciones de soluciones

Obtenga información sobre las opciones de Big Compute en Azure y elija el enfoque correcto para sus necesidades empresariales y de carga de trabajo.

* [Soluciones HPC y Lote](batch-hpc-solutions.md)

* [Vídeo: Big Compute en la nube con Azure y HPC](http://azure.microsoft.com/documentation/videos/teched-europe-2014-big-compute-in-the-cloud-with-high-performance-computing-on-azure/)


## Azure Batch

[Batch](http://azure.microsoft.com/services/batch/) es un servicio de plataforma que facilita el proceso de habilitar para la nube sus aplicaciones y de ejecutar trabajos sin configurar y administrar un clúster y un programador de trabajos. Use el SDK para integrar aplicaciones cliente con Azure Batch a través de una variedad de lenguajes, almacenar datos en Azure y crear canalizaciones de ejecución de trabajos.

* [Documentación](http://azure.microsoft.com/documentation/services/batch/)

* [Referencia de API](https://msdn.microsoft.com/library/azure/dn820177.aspx)

* [Tutorial: Introducción a la biblioteca de Lote de Azure para .NET](batch-dotnet-get-started.md)

* [Foro de Lote](https://social.msdn.microsoft.com/Forums/home?forum=azurebatch)

* [Vídeos de Lote](https://azure.microsoft.com/documentation/videos/index/?services=batch)

## Soluciones de clúster de HPC

Implemente o amplíe su clúster de Windows o Linux HPC existente para ejecutar sus cargas de trabajo intensivas de proceso.

### Microsoft HPC Pack

HPC Pack es la solución HPC gratuita de Microsoft que se basa en las tecnologías de Microsoft Azure y Windows Server.

* [Descarga de HPC Pack 2012 R2 Update 2](https://www.microsoft.com/es-es/download/details.aspx?id=47755)

* [Documentación](https://technet.microsoft.com/library/jj899572.aspx)

* **Imágenes de VM de Azure**

    * [HPC Pack en Windows Server 2012 R2](http://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/)

    * [Nodo de proceso de HPC Pack en Windows Server 2012 R2](http://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodeonwindowsserver2012r2/)

    * [Nodo de proceso de HPC Pack con Excel en Windows Server 2012 R2](http://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodewithexcelonwindowsserver2012r2/)

* **Scripts y plantillas de inicio rápido de Azure**

    * [Creación de un clúster de HPC](https://azure.microsoft.com/documentation/templates/create-hpc-cluster/)

    * [Creación de un clúster de HPC con una imagen de nodo de cálculo personalizada](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-custom-image/)

    * [Script de PowerShell de implementación de IaaS de HPC Pack](https://www.microsoft.com/download/details.aspx?id=44949)

* **Tutoriales de Azure**

    * [Tutorial: Introducción a los nodos de proceso de Linux en un clúster de HPC Pack en Azure](../virtual-machines/virtual-machines-linux-cluster-hpcpack.md)

    * [Tutorial: Introducción a un clúster de HPC Pack en Azure para ejecutar cargas de trabajo de Excel y SOA](../virtual-machines/virtual-machines-excel-cluster-hpcpack.md)

    * [Tutorial: Configuración de un clúster híbrido con HPC Pack en Azure](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)

    * [HPC Pack en máquinas virtuales de Azure](https://msdn.microsoft.com/library/azure/dn518135.aspx)

    * [Irrumpir en Azure con HPC Pack](https://technet.microsoft.com/library/gg481749.aspx)


* [Foros de Windows HPC](https://social.microsoft.com/Forums/home?category=windowshpc)

### Soluciones de clúster de Linux y OSS

Use estas plantillas de inicio rápido de Azure para implementar clústeres de HPC de Linux con herramientas de código abierto.

* [Establecimiento de un clúster SLURM](http://azure.microsoft.com/documentation/templates/slurm/) y [una entrada de blog](http://blogs.technet.com/b/windowshpc/archive/2015/06/06/deploy-a-slurm-cluster-on-azure.aspx)

* [Establecimiento de un clúster de par](http://azure.microsoft.com/documentation/templates/torque-cluster/)

## Microsoft MPI

[Microsoft MPI](https://msdn.microsoft.com/library/bb524831.aspx) (MS-MPI) es una implementación de Microsoft del estándar de la interfaz de especificación de mensajes para desarrollar y ejecutar aplicaciones en paralelo en la plataforma de Windows.


* [Descargar MS-MPI](http://go.microsoft.com/FWLink/p/?LinkID=389556)

* [Referencia de MS-MPI](https://msdn.microsoft.com/library/dn473458.aspx)

* [Foro de MPI](https://social.microsoft.com/Forums/home?forum=windowshpcmpi)


## Instancias intensivas de proceso

Azure ofrece un [intervalo de tamaños](../virtual-machines/virtual-machines-size-specs.md), que incluye las [instancias A8, A9, A10 y A11](../virtual-machines/virtual-machines-a8-a9-a10-a11-specs.md) de proceso intensivo, para ejecutar las cargas de trabajo de HPC de Linux y Windows.

* [Configuración de un clúster de Linux RDMA para ejecutar aplicaciones MPI](../virtual-machines/virtual-machines-linux-cluster-rdma.md)

* [Instancias A8 y A9: inicio rápido con HPC Pack](https://msdn.microsoft.com/library/azure/dn594431.aspx)

* [Ejecución de aplicaciones MPI en instancias A8 y A9](https://msdn.microsoft.com/library/azure/dn592104.aspx)

## Proyectos de arquitectura

* En [Large-Scale Computing - Financial Services](http://go.microsoft.com/fwlink/?LinkId=536378) (PDF) se muestra cómo poner operativos y organizar los análisis de datos y los cálculos a gran escala en la nube para administración de riesgos, generación de informes y simulaciones.

## Ejemplos y demos

* [Ejemplos de código de Azure Batch](https://github.com/Azure/azure-batch-samples)

* [Ejemplo de combinación de aplicaciones de Batch](https://github.com/Azure/azure-batch-apps-blender) y [entrada de blog](http://azure.microsoft.com/blog/2015/01/26/blender-on-azure-batch/)

## Servicios de Azure relacionados

* [Factoría de datos](http://azure.microsoft.com/documentation/services/data-factory/)

* [Aprendizaje automático](http://azure.microsoft.com/documentation/services/machine-learning/)

* [HDInsight](http://azure.microsoft.com/documentation/services/hdinsight/)

* [Máquinas virtuales](http://azure.microsoft.com/documentation/services/virtual-machines/)

* [Servicios en la nube](http://azure.microsoft.com/documentation/services/cloud-services/)

* [Servicios multimedia](http://azure.microsoft.com/documentation/services/media-services/)



## Pasos siguientes

* Para los anuncios más recientes, vea el [blog del equipo de Microsoft HPC y Batch](http://blogs.technet.com/b/windowshpc/) y el [blog de Azure](http://azure.microsoft.com/blog/tag/hpc/).
* Vea también [Novedades de Batch](http://azure.microsoft.com/updates/?service=batch) o suscríbase a la [fuente RSS](http://azure.microsoft.com/updates/feed/?service=batch).

<!---HONumber=August15_HO6-->