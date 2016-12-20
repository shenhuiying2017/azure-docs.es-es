---
title: Soluciones de Batch y de HPC en la nube | Microsoft Docs
description: "Más información acerca de los escenarios de ejecución por lotes y de informática de alto rendimiento (HPC y Big Compute), y opciones de soluciones de Azure"
services: batch, virtual-machines, cloud-services
documentationcenter: 
author: dlepow
manager: timlt
editor: 
ms.assetid: aab5401d-2baf-4cf2-bf20-ad224de33888
ms.service: batch
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: big-compute
ms.date: 07/27/2016
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: 87f8b1651ea429663c621a64d7f668a175fa675f


---
# <a name="batch-and-hpc-solutions-in-the-azure-cloud"></a>Soluciones de lote y HPC en la nube de Azure
Azure ofrece soluciones en la nube eficientes y escalables para la ejecución por lotes y la informática de alto rendimiento (HPC), que también se denomina *Big Compute*. Obtenga información aquí sobre cargas de trabajo Big Compute y servicios de Azure para admitirlas, o bien vaya directamente a [escenarios de solución](#scenarios) más adelante en este artículo. Este artículo está destinado principalmente a los encargados de la toma de decisiones técnicas, los administradores de TI y los proveedores de software independientes, aunque otros profesionales de TI y desarrolladores pueden usarlo para familiarizarse con estas soluciones.

Las organizaciones tienen problemas informáticos a gran escala: el diseño y análisis de ingeniería, la representación de imágenes, los modelos complejos, las simulaciones de Monte Carlo, los cálculos de riesgos financieros, etc. Azure ayuda a las organizaciones a solucionar estos problemas con los recursos, la escala y la programación que necesitan. Con Azure, las organizaciones pueden:

* Crear soluciones híbridas, ampliando un clúster de HPC local para las descargas de cargas de trabajo máximas a la nube
* Ejecución de cargas de trabajo y herramientas de clúster HPC completamente en Azure
* Usar un servicio administrado y escalable de Azure como [Lote](https://azure.microsoft.com/documentation/services/batch/) para ejecutar cargas de trabajo de proceso intensivo sin tener que implementar y administrar la infraestructura de proceso

Aunque fuera del ámbito de este artículo, Azure también proporciona a los desarrolladores y partners un conjunto completo de funcionalidades, opciones de arquitectura y herramientas de desarrollo para crear flujos de trabajo Big Compute a gran escala y personalizados. Y un ecosistema de partners creciente está listo para ayudarle a aumentar la productividad de las cargas de trabajo Big Compute en la nube de Azure.

## <a name="batch-and-hpc-applications"></a>Aplicaciones HPC y de ejecución por lotes
A diferencia de las aplicaciones web y de muchas aplicaciones de línea de negocio, las aplicaciones HPC y de ejecución por lotes tienen un inicio y una finalización definidos y pueden ejecutarse de manera programada o a petición, a veces durante horas o más tiempo. La mayoría se dividen en dos categorías principales: *intrínsecamente paralelas* (en ocasiones denominadas "embarazosamente paralelas", porque los problemas que solucionan se prestan a ejecutarse en paralelo en varios procesadores o equipos) y *estrechamente acopladas*. Para obtener más información acerca de estos tipos de aplicaciones, consulte la tabla siguiente. Algunas soluciones de Azure funcionan mejor para un tipo u otro.

> [!NOTE]
> En las soluciones de HPC y de Batch, una instancia en ejecución de una aplicación se suele llamar *trabajo* y cada trabajo podría dividirse en *tareas*. Y los recursos de proceso en clúster para la aplicación se denominan a menudo *nodos de ejecución*.
> 
> 

| Escriba | Características | Ejemplos |
| --- | --- | --- |
| **Intrínsecamente paralelos**<br/><br/>![Intrínsecamente paralelos][parallel] |• Los equipos individuales ejecutan la lógica de la aplicación de forma independiente<br/><br/> • La incorporación de equipos permite que la aplicación escale y reduzca el tiempo de cálculo<br/><br/>• La aplicación consta de archivos ejecutables independientes o se divide en un grupo de servicios invocados por un cliente (una arquitectura orientada a servicios o SOA, aplicación) |• Modelado de riesgos financieros<br/><br/>• Representación y procesamiento de imágenes<br/><br/>• Codificación y transcodificación multimedia<br/><br/>• Simulaciones Monte Carlo<br/><br/>• Pruebas de Software |
| **Tightly coupled**<br/><br/>![Tightly coupled][coupled] |• La aplicación requiere la Interactuación de los nodos de ejecución o el intercambio de resultados intermedios<br/><br/>• Los nodos de proceso pueden comunicarse mediante la interfaz de paso de mensajes (MPI), un protocolo de comunicación común para el procesamiento en paralelo<br/><br/>• La aplicación es sensible al ancho de banda y la latencia de red<br/><br/>• El rendimiento de la aplicación se puede mejorar mediante tecnologías de redes de alta velocidad como InfiniBand y acceso directo a memoria remoto (RDMA) |• Modelado de depósitos de petróleo y gas •<br/><br/>• Diseño de ingeniería y análisis, como dinámica de fluidos computacional<br/><br/>• Simulaciones físicas como choques de automóviles y reacciones nucleares<br/><br/>• Previsión meteorológica de |

### <a name="considerations-for-running-batch-and-hpc-applications-in-the-cloud"></a>Consideraciones para la ejecución de aplicaciones HPC y de ejecución en lotes en la nube
Puede migrar fácilmente muchas aplicaciones que están diseñadas para ejecutarse en clústeres HPC locales a Azure o a un entorno híbrido (entre entornos). Sin embargo, puede haber algunas limitaciones o consideraciones, entre las que se incluyen:

* **Disponibilidad de los recursos en la nube** : en función del tipo de recursos del proceso en la nube que use, es posible que no pueda disfrutar de disponibilidad continua de la máquina mientras se ejecuta un trabajo. El control de estado y la señalización de control de progreso son técnicas comunes que suelen usarse para tratar posibles errores transitorios y más necesarias cuando se usan recursos en la nube.
* **Acceso a datos** : las técnicas de acceso a datos disponibles habitualmente en los clústeres de empresa, como NFS, pueden requerir una configuración especial en la nube. O bien, es posible que haya que adoptar prácticas y patrones de acceso a los datos diferentes para la nube.
* **Movimiento de datos** : para las aplicaciones que procesan grandes cantidades de datos, es preciso implementar estrategias para mover los datos al almacenamiento en la nube y para procesar recursos. Es posible que necesite la conexión de redes entre instalaciones de alta velocidad como [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/). Considere también las limitaciones legales, normativas o de directivas para almacenar o acceder a esos datos.
* **Licencias** : póngase en contacto con el proveedor de cualquier aplicación comercial para obtener información acerca de las licencias u otras restricciones para la ejecución en la nube. No todos los proveedores ofrecen licencias de pago por uso. Es posible que deba planear un servidor de licencias en la nube para su solución o conectarse a un servidor de licencias local.

### <a name="big-compute-or-big-data"></a>¿Big Compute o Big Data?
La línea divisoria entre aplicaciones Big Compute y de macrodatos no siempre está clara y es posible que algunas aplicaciones tengan características de ambas. Ambas incluyen cálculos a gran escala, normalmente en clústeres de equipos. Pero los enfoques de las soluciones y las herramientas de soporte técnico pueden diferir.

• **Big Compute** suele implicar a las aplicaciones que se basan en la capacidad y la memoria de la CPU, como simulaciones de ingeniería, modelado de riesgos financieros y representación digital. La infraestructura de una solución de Big Compute puede incluir equipos con procesadores de núcleo múltiple especializados que realizan cálculos básicos y hardware de red de alta velocidad especializado para conectar los equipos.

• Los **macrodatos** solucionan problemas de análisis de datos que implican grandes cantidades de datos que no se pueden administrar mediante un único equipo o sistema de administración de bases de datos. Entre los ejemplos que pueden encontrarse se incluyen grandes volúmenes de registros web o de otros datos de inteligencia empresarial. Los macrodatos tiende a confiar más en la capacidad de disco y en el rendimiento de E/S que en la potencia de la CPU. También hay herramientas de macrodatos especializadas, como Apache Hadoop, para administrar el clúster y particionar los datos. (Para obtener información acerca de Azure HDInsight y otras soluciones Azure Hadoop, consulte [Hadoop](https://azure.microsoft.com/solutions/hadoop/)).

## <a name="compute-management-and-job-scheduling"></a>Administración de procesos y programación de trabajos
La ejecución de las aplicaciones Batch y HPC normalmente incluye un *administrador de clústeres* y un *programador de trabajos* para ayudar a administrar los recursos de proceso en clúster y asignarlos a las aplicaciones que ejecutan los trabajos. Estas funciones pueden realizarse mediante herramientas independientes o una herramienta o servicio integrado.

* **Administrador de clústeres** : aprovisiona, publica y administra recursos de proceso (o nodos de ejecución). Un administrador de clústeres podría automatizar la instalación de imágenes de sistema operativo y aplicaciones en nodos de ejecución, escalar los recursos de proceso en función de la demanda y supervisar el rendimiento de los nodos.
* **Programador de trabajos** : especifica los recursos (como los procesadores o la memoria) que necesita una aplicación y las condiciones que determinan cuándo se ejecuta. Un programador de trabajos mantiene una cola de trabajos y les asigna recursos en función de una prioridad asignada u otras características.

Las herramientas de agrupación en clústeres y programación de trabajos para clústeres basados en Windows y Linux pueden migrarse correctamente a Azure. Por ejemplo, [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029)(solución de clúster de proceso gratis de Microsoft para cargas de trabajo HPC de Windows y Linux) ofrece varias opciones para ejecución en Azure. También puede compilar clústeres de Linux para ejecutar herramientas de código abierto como Torque y SLURM. También puede aportar soluciones comerciales en malla a Azure, como [TIBCO DataSynapse GridServer](http://www.tibco.com/company/news/releases/2016/tibco-to-accelerate-cloud-adoption-of-banking-and-capital-markets-customers-via-microsoft-collaboration), [IBM Platform Symphony](http://www-01.ibm.com/support/docview.wss?uid=isg3T1023592) y [Univa Grid Engine](http://www.univa.com/products/grid-engine).

Como se muestra en las secciones siguientes, también pueden sacar partido de los servicios de Azure para administrar recursos de proceso y programar trabajos sin (o además de) las herramientas de administración de clústeres tradicionales.

## <a name="scenarios"></a>Escenarios
Aquí se muestran tres escenarios comunes para ejecutar cargas de trabajo de Big Compute en Azure mediante el uso de soluciones de clúster de HPC existentes, servicios de Azure o una combinación de ambos. Se enumeran consideraciones clave para elegir cada escenario pero no son exhaustivas. Posteriormente en este artículo, puede obtener más información sobre los servicios de Azure disponibles que puede usar en la solución.

| Escenario | ¿Por qué elegirlo? |
| --- | --- | --- |
| **Expansión de clúster de HPC a Azure**<br/><br/>[![Cluster burst][burst_cluster]](./media/batch-hpc-solutions/burst_cluster.png) <br/><br/> Más información:<br/>[Expansión a instancias de trabajo de Azure con HPC Pack](https://technet.microsoft.com/library/gg481749.aspx)<br/><br/>• [Configuración de un clúster de proceso híbrido con HPC Pack](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)<br/><br/>• [Expansión a Azure Batch con HPC Pack](https://technet.microsoft.com/library/mt612877.aspx)<br/><br/> |• Combine su [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) u otro clúster local con recursos de Azure adicionales en una solución híbrida.<br/><br/>• Amplíe las cargas de trabajo de Big Compute para la ejecución en instancias de máquina virtual de plataforma como servicio (PaaS) (actualmente, solo Windows Server).<br/><br/>• Acceda a un almacén de datos o a un servidor de licencias local mediante una red virtual de Azure opcional. |
| **Creación de un clúster de HPC completamente en Azure**<br/><br/>[![Cluster in IaaS][iaas_cluster]](./media/batch-hpc-solutions/iaas_cluster.png)<br/><br/>Más información:<br/>• [Soluciones de clúster de HPC en Azure](big-compute-resources.md)<br/><br/> |• Implemente de forma rápida y coherente las aplicaciones y herramientas de clúster en máquinas virtuales de infraestructura como servicio (IaaS) con Windows o Linux estándar o personalizadas.<br/><br/>• Ejecute diversas cargas de trabajo de Big Compute con la solución de su elección para la programación de trabajos.<br/><br/>• Use servicios de Azure adicionales, que incluyan las redes y el almacenamiento, para crear soluciones completas basadas en la nube. |
| **Escalado horizontal de una aplicación paralela en Azure**<br/><br/>[![Azure Batch][batch_proc]](./media/batch-hpc-solutions/batch_proc.png)<br/><br/>Más información:<br/>• [Conceptos básicos de Azure Batch](batch-technical-overview.md)<br/><br/>• [Introducción a la biblioteca .NET de Azure Batch](batch-dotnet-get-started.md) |• Desarrolle con [Azure Batch](https://azure.microsoft.com/documentation/services/batch/) para escalar horizontalmente diversas cargas de trabajo de Big Compute de forma que se ejecuten en grupos de máquinas virtuales Windows o Linux.<br/><br/>• Use un servicio de la plataforma Azure para administrar la implementación y el escalado automático de máquinas virtuales, la programación de trabajos, la recuperación ante desastres, el movimiento de datos, la administración de dependencias y la implementación de aplicaciones. |

## <a name="azure-services-for-big-compute"></a>Servicios de Azure para Big Compute
Aquí hay más información sobre el proceso, los datos, las redes y los servicios relacionados que puede combinar en las soluciones y los flujos de trabajo de Big Compute. Para obtener información detallada acerca de los servicios de Azure, consulte la [documentación](https://azure.microsoft.com/documentation/)de dichos servicios. Los [escenarios](#scenarios) anteriores de este artículo muestran solo algunas de las formas en que se pueden utilizar estos servicios.

> [!NOTE]
> Azure presenta nuevos servicios con cierta frecuencia que podrían ser útiles para su escenario. Si tiene alguna pregunta, póngase en contacto con un [asociado de Azure](https://pinpoint.microsoft.com/en-US/search?keyword=azure) o envíe un correo electrónico a *bigcompute@microsoft.com*.
> 
> 

### <a name="compute-services"></a>Servicios de proceso
Servicios de proceso de Azure son el núcleo de una solución Big Compute y los diversos servicios de proceso ofrecen ventajas para diferentes escenarios. En un nivel básico, estos servicios ofrecen modos distintos para que las aplicaciones se ejecuten en instancias de proceso basadas en máquinas virtuales que Azure proporciona mediante tecnología Windows Server Hyper-V. Estas instancias pueden ejecutar herramientas y sistemas operativos Linux y Windows estándar y personalizados. Azure permite elegir entre varios [tamaños de instancia](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) con diferentes configuraciones de núcleos de CPU, memoria, capacidad de disco y otras características. En función de sus necesidades puede escalar las instancias a miles de núcleos y, luego, reducirlas verticalmente cuando necesite menos recursos.

> [!NOTE]
> Saque provecho de las instancias de Azure con un proceso intensivo para mejorar el rendimiento y la escalabilidad de las cargas de trabajo de HPC, incluidas las aplicaciones MPI paralelas que requieren una red de aplicaciones de baja latencia y alto rendimiento. Consulte [Acerca de las máquinas virtuales de la serie H y A de procesos intensivos](../virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).  
> 
> 

| Servicio | Description |
| --- | --- |
| **[Máquinas virtuales](https://azure.microsoft.com/documentation/services/virtual-machines/)**<br/><br/> |• Proporcionan infraestructura de proceso como servicio (IaaS) mediante la tecnología de Microsoft Hyper-V<br/><br/>• Permiten aprovisionar de forma flexible y administrar equipos en la nube persistentes desde imágenes estándar de Windows Server o Linux de [Azure Marketplace](https://azure.microsoft.com/marketplace/) o desde las imágenes y los discos de datos que proporcione<br/><br/>• Se pueden implementar y administrar como [conjuntos de escalado de máquinas virtuales](https://azure.microsoft.com/documentation/services/virtual-machine-scale-sets/) para crear servicios a gran escala a partir de máquinas virtuales idénticas, con escalado automático para aumentar o disminuir la capacidad automáticamente<br/><br/>• Ejecutan localmente aplicaciones y herramientas de clúster de proceso completamente en la nube<br/><br/> |
| **[Cloud services](https://azure.microsoft.com/documentation/services/cloud-services/)**<br/><br/> |• Puede ejecutar aplicaciones de Big Compute en instancias de rol de trabajo, que son máquinas virtuales que ejecutan una versión de Windows Server y están administradas completamente por Azure<br/><br/>• Hacen posibles las aplicaciones escalables y fiables con baja carga administrativa, que se ejecutan en un modelo de plataforma como servicio (PaaS)<br/><br/>• Es posible que requieran herramientas adicionales o desarrollo para la integración de soluciones de clúster HPC locales  |
| **[Lote](https://azure.microsoft.com/documentation/services/batch/)**<br/><br/> |• Ejecuta cargas de trabajo en lotes y paralelas a gran escala en un servicio totalmente administrado.<br/><br/>• Proporciona programación de trabajos y escalado automático de un grupo administrado de máquinas virtuales<br/><br/>• Permite que los desarrolladores compilen y ejecuten aplicaciones como servicio o habiliten aplicaciones existentes para la nube<br/> |

### <a name="storage-services"></a>Servicios de almacenamiento
Normalmente, una solución Big Compute funciona en un conjunto de datos de entrada y genera datos para sus resultados. Entre algunos de los servicios de almacenamiento de Azure que se usan en soluciones Big Compute se incluyen los siguientes:

* [Almacenamiento de blobs, tablas y colas](https://azure.microsoft.com/documentation/services/storage/) : administre grandes cantidades de datos no estructurados, datos NoSQL y mensajes de flujo de trabajo y comunicación, respectivamente. Por ejemplo, puede usar el Almacenamiento de blobs para grandes conjuntos de datos técnicos o para las imágenes de entrada o los archivos multimedia que su aplicación procesa. Puede usar colas para la comunicación asincrónica en una solución. Consulte [Introducción a Almacenamiento de Microsoft Azure](../storage/storage-introduction.md).
* [Almacenamiento de archivos de Azure](https://azure.microsoft.com/services/storage/files/) : comparte archivos comunes y datos en Azure mediante el protocolo SMB estándar, que se necesita para algunas soluciones de clúster HPC.
* [Data Lake Store](https://azure.microsoft.com/services/data-lake-store/) : proporciona un sistema de archivos distribuido de Apache Hadoop a hiperescala para la nube, algo que es útil para realizar análisis por lotes, interactivos y en tiempo real.

### <a name="data-and-analysis-services"></a>Servicios de datos y análisis
Algunos escenarios Big Compute implican flujos de datos a gran escala o generan datos que necesitan un procesamiento o análisis posterior. Azure ofrece varios servicios de datos y análisis, entre los que se incluyen:

* [Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) : crea flujos de trabajo controlados por datos (canalizaciones) que unen, agregan y transforman datos de almacenes de datos locales, basados en la nube y de Internet.
* [Base de datos SQL](https://azure.microsoft.com/documentation/services/sql-database/) : proporciona las características clave de un sistema de administración de base de datos relacionales de Microsoft SQL Server en un servicio administrado.
* [HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/) : implementa y aprovisiona clústeres de Apache Hadoop basados en Windows Server o Linux en la nube para administrar, analizar y crear informes de macrodatos.
* [Aprendizaje automático](https://azure.microsoft.com/documentation/services/machine-learning/) : le ayuda a crear, probar, operar y administrar soluciones de análisis predictivo en un servicio totalmente administrado.

### <a name="additional-services"></a>Servicios adicionales
Es posible que la solución Big Compute necesite otros servicios de Azure para conectarse a recursos locales o de otros entornos. Algunos ejemplos son:

* [Red virtual](https://azure.microsoft.com/documentation/services/virtual-network/) : crea una sección aislada lógicamente en Azure para conectar los recursos de Azure entre sí o con un centro de datos local. Con una red virtual entre locales, las aplicaciones de Big Compute pueden acceder a datos locales, servicios de Active Directory y servidores de licencias
* [ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) : crea una conexión privada entre los centros de datos de Microsoft y la infraestructura local o en un entorno de colocalización. ExpressRoute proporciona más confiabilidad, velocidad y seguridad, y una menor latencia que las conexiones a Internet típicas.
* [Bus de servicio](https://azure.microsoft.com/documentation/services/service-bus/) : proporciona varios mecanismos para que las aplicaciones se comuniquen o intercambien datos, independientemente de si están ubicadas en Azure, en otra plataforma en la nube o en un centro de datos.

## <a name="next-steps"></a>Pasos siguientes
* Consulte [Big Compute en Azure: Recursos técnicos para informática de alto rendimiento (HPC) y computación por lotes](big-compute-resources.md) para encontrar instrucciones técnicas para crear una solución.
* Converse sobre las opciones de Azure con partners, como  Cycle Computing y UberCloud.
* Consulte más información sobre soluciones Big Compute de Azure proporcionadas por [Towers Watson](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18222), [Altair](https://azure.microsoft.com/blog/availability-of-altair-radioss-rdma-on-microsoft-azure/), [ANSYS](https://azure.microsoft.com/blog/ansys-cfd-and-microsoft-azure-perform-the-best-hpc-scalability-in-the-cloud/) y [d3VIEW](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=22088).
* Para los anuncios más recientes, lea el [blog del equipo de Microsoft HPC y Batch](http://blogs.technet.com/b/windowshpc/), y el [blog de Azure](https://azure.microsoft.com/blog/tag/hpc/).

<!--Image references-->
[parallel]: ./media/batch-hpc-solutions/parallel.png
[coupled]: ./media/batch-hpc-solutions/coupled.png
[iaas_cluster]: ./media/batch-hpc-solutions/iaas_cluster.png
[burst_cluster]: ./media/batch-hpc-solutions/burst_cluster.png
[batch_proc]: ./media/batch-hpc-solutions/batch_proc.png



<!--HONumber=Nov16_HO2-->


