<properties
   pageTitle="Big Compute en Azure: recursos técnicos para Batch e informática de alto rendimiento (HPC)"
   description="En este artículo se enumeran los recursos técnicos para ayudarle a ejecutar cargas de trabajo de HPC, por lotes y en paralelo a gran escala en Azure."
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
   ms.date="05/29/2015"
   ms.author="danlep"/>

# Big Compute en Azure: recursos técnicos para Batch e informática de alto rendimiento (HPC)
Se trata de una guía sobre recursos técnicos que le ayudará a ejecutar cargas de trabajo de HPC, por lotes y en paralelo a gran escala en Azure. Amplíe sus cargas de trabajo de HPC o de lotes existentes a la nube de Azure, o cree nuevas soluciones de Big Compute en Azure mediante una gama de los servicios de Azure.

## Opciones de soluciones


Obtenga información sobre las opciones de Big Compute en Azure y elija el enfoque correcto para sus necesidades empresariales y de carga de trabajo.

* [Información general](https://msdn.microsoft.com/library/azure/dn482130.aspx)

* [Vídeo: Big Compute en la nube con Azure y HPC](http://azure.microsoft.com/documentation/videos/teched-europe-2014-big-compute-in-the-cloud-with-high-performance-computing-on-azure/)


## Azure Batch

[Batch](http://azure.microsoft.com/services/batch/) (Preview) es un servicio de plataforma que facilita el proceso de habilitar para la nube sus aplicaciones y de ejecutar trabajos sin configurar y administrar un clúster y un programador de trabajos. Use el SDK para integrar aplicaciones cliente con Azure Batch a través de una variedad de lenguajes, almacenar datos en Azure y crear canalizaciones de ejecución de trabajos.

* Suscríbase a la [Preview](http://azure.microsoft.com/services/preview/)

* [Documentación](http://azure.microsoft.com/documentation/services/batch/)

* [Referencia de API](https://msdn.microsoft.com/library/azure/dn820177.aspx)

* [Foro de Batch](https://social.msdn.microsoft.com/Forums/home?forum=azurebatch)

* [Tutorial: Introducción a la biblioteca de Azure Batch para .NET](batch-dotnet-get-started.md)

* [Vídeos de Lote](https://azure.microsoft.com/documentation/videos/index/?services=batch)

## Soluciones de clúster de HPC

Migre o amplíe su clúster de Windows HPC existente para ejecutar sus cargas de trabajo intensivas de proceso de Azure.

### Microsoft HPC Pack

[HPC Pack](https://technet.microsoft.com/library/cc514029) es el administrador de clústeres gratuito de Microsoft y la solución de programación de trabajos para HPC basada en la nube, híbrida y local.

* [Irrumpir en Azure con HPC Pack](https://technet.microsoft.com/library/gg481749.aspx)

* [HPC Pack en máquinas virtuales de Azure](https://msdn.microsoft.com/library/azure/dn518135.aspx)

* [Tutorial: Configurar un clúster híbrido con HPC Pack en Azure](../cloud-services-setup-hybrid-hpcpack-cluster.md)

* [Foros de Windows HPC](https://social.microsoft.com/Forums/home?category=windowshpc)

## Microsoft MPI

[Microsoft MPI](https://msdn.microsoft.com/library/bb524831.aspx) (MS-MPI) es una implementación de Microsoft del estándar de la interfaz de especificación de mensajes para desarrollar y ejecutar aplicaciones en paralelo en la plataforma de Windows.


* [Descargar MS-MPI](http://go.microsoft.com/FWLink/p/?LinkID=389556)

* [Referencia de MS-MPI](https://msdn.microsoft.com/library/dn473458.aspx)

* [Foro de MPI](https://social.microsoft.com/Forums/home?forum=windowshpcmpi)


## Instancias intensivas de proceso

Azure ofrece un [intervalo de tamaños](https://msdn.microsoft.com/library/azure/dn197896.aspx), incluidas las [instancias A8, A9, A10 y A11](https://msdn.microsoft.com/library/azure/dn689095.aspx), para ejecutar sus cargas de trabajo de Batch y HPC.


* [Instancias A8 y A9: inicio rápido de HPC Pack](https://msdn.microsoft.com/library/azure/dn594431.aspx)

* [Ejecutar aplicaciones MPI en instancias A8 y A9](https://msdn.microsoft.com/library/azure/dn592104.aspx)

## Proyectos de arquitectura

* En [Large-Scale Computing - Financial Services](http://go.microsoft.com/fwlink/?LinkId=536378) (PDF) se muestra cómo hacer operativos y organizar los análisis de datos y los cálculos a gran escala en la nube para la administración de riesgos, informes y simulaciones.

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

<!---HONumber=58_postMigration-->