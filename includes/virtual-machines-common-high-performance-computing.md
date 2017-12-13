Las organizaciones tienen necesidades informáticas a gran escala. Las cargas de trabajo de Big Compute incluyen el diseño y análisis de ingeniería, los cálculos de riesgos financieros, la representación de imágenes, los modelos complejos, las simulaciones de Monte Carlo, etc. 

Use la nube de Azure para ejecutar eficazmente cargas de trabajo de Linux y Windows de proceso intensivo que abarcan desde trabajos por lotes en paralelo hasta las simulaciones de informática de alto rendimiento habituales. Ejecute sus cargas de trabajo de informática de alto rendimiento y por lotes en la infraestructura de Azure mediante la elección de los servicios de proceso, los administradores de soluciones en malla, las soluciones de Marketplace y las aplicaciones hospedadas por el proveedor (SaaS). Azure proporciona soluciones flexibles para distribuir el trabajo y escalar a miles de máquinas virtuales o núcleos y, posteriormente, reducir verticalmente cuando se necesitan menos recursos. 



## <a name="solution-options"></a>Opciones de solución



* **Soluciones de implementación personal**
    * Configure su propio entorno de clúster en máquinas virtuales de Azure o en [conjuntos de escalado de máquinas virtuales](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md). 
    * Migre mediante "lift and shift" un clúster local o implemente un nuevo clúster en Azure para obtener capacidad adicional. 
    * Use las plantillas de Azure Resource Manager para implementar los [administradores de carga de trabajo](#workload-managers) principales, la infraestructura y las [aplicaciones](#hpc-applications). 
    * Elija los [tamaños de las máquinas virtuales de HPC y GPU](#hpc-and-gpu-sizes) que incluyan hardware especializado y conexiones de red para cargas de trabajo MPI o GPU. 
    * Agregue [almacenamiento de alto rendimiento](#hpc-storage) para cargas de trabajo intensivas de E/S.
* **Soluciones híbridas**
    * Ampliación de la solución local para descargar cargas de trabajo máximas ("ráfagas") en la infraestructura de Azure
    * Use el proceso en la nube a petición con el [administrador de cargas de trabajo](#workload-manager) ya existente.
    * Aproveche las ventajas de los [tamaños de las máquinas virtuales de HPC y GPU](#hpc-and-gpu-sizes) para las cargas de trabajo de MPI o GPU.
* **Soluciones de Big Compute como servicio**
    * Desarrolle soluciones y flujos de trabajo personalizados de Big Compute mediante [Azure Batch](#azure-batch) y otros [servicios de Azure](#related-azure-services) relacionados.
    * Ejecute soluciones de ingeniería y simulación habilitadas para Azure de proveedores como [Altair](http://www.altair.com/), [Rescale](https://www.rescale.com/azure/) y [Cycle Computing](https://cyclecomputing.com/) (que ahora [se han unido a Microsoft](https://blogs.microsoft.com/blog/2017/08/15/microsoft-acquires-cycle-computing-accelerate-big-computing-cloud/)).
* **Soluciones de Marketplace**
    * Utilice la escala de [aplicaciones HPC](#hpc-applications) y las [soluciones](#marketplace-solutions) que se ofrecen en [Azure Marketplace](https://azuremarketplace.microsoft.com/). 
    


Las secciones siguientes ofrecen más información sobre tecnologías auxiliares y vínculos a instrucciones.



## <a name="marketplace-solutions"></a>Soluciones de Marketplace

Visite [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) para obtener imágenes de máquinas virtuales Windows y Linux, y soluciones diseñadas para HPC. Algunos ejemplos son:

* [HPC basado en RogueWave CentOS](https://azuremarketplace.microsoft.com/marketplace/apps/RogueWave.CentOSbased73HPC?tab=Overview)
* [SUSE Linux Enterprise Server para HPC](https://azure.microsoft.com/marketplace/partners/suse/suselinuxenterpriseserver12optimizedforhighperformancecompute/)
*  [TIBCO Grid Server Engine](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/tibco-software.gridserverlinuxengine?tab=Overview)
* [Máquinas virtuales de ciencia de datos de Azure para Windows y Linux](../articles/machine-learning/machine-learning-data-science-virtual-machine-overview.md)
* [D3View](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/xfinityinc.d3view-v5?tab=Overview)
* [UberCloud](https://azure.microsoft.com/search/marketplace/?q=ubercloud)
* [Intel Cloud Edition for Lustre](https://azuremarketplace.microsoft.com/marketplace/apps/intel.lustre-cloud-edition-eval?tab=Overview)


 
## <a name="hpc-applications"></a>Aplicaciones HPC

Ejecute aplicaciones HPC personalizadas o comerciales en Azure. Se realizan pruebas comparativas en varios ejemplos de esta sección para realizar un escalado eficaz con máquinas virtuales o nodos de proceso adicionales. Visite [Azure Marketplace](https://marketplace.azure.com) en busca de soluciones que estén listas para su implementación.

> [!NOTE]
> Póngase en contacto con el proveedor de cualquier aplicación comercial para obtener información acerca de las licencias u otras restricciones para la ejecución en la nube. No todos los proveedores ofrecen licencias de pago por uso. Es posible que necesite un servidor de licencias en la nube para su solución o conectarse a un servidor de licencias local.

### <a name="engineering-applications"></a>Aplicaciones de ingeniería


* [Altair RADIOSS](https://azure.microsoft.com/blog/availability-of-altair-radioss-rdma-on-microsoft-azure/)
* [ANSYS CFD](https://azure.microsoft.com/blog/ansys-cfd-and-microsoft-azure-perform-the-best-hpc-scalability-in-the-cloud/)
* [MATLAB Distributed Computing Server](../articles/virtual-machines/windows/matlab-mdcs-cluster.md)
* [StarCCM+](https://blogs.msdn.microsoft.com/azurecat/2017/07/07/run-star-ccm-in-an-azure-hpc-cluster/)
* [OpenFOAM](https://simulation.azure.com/casestudies/Team-182-ABB-UC-Final.pdf)



### <a name="graphics-and-rendering"></a>Gráficos y representación

* [Autodesk Maya, 3ds Max y Arnold](../articles/batch/batch-rendering-service.md) en Azure Batch (versión preliminar)

### <a name="ai-and-deep-learning"></a>Inteligencia artificial y aprendizaje profundo

* [Batch AI](../articles/batch-ai/overview.md): entrenamiento de modelos de aprendizaje profundo
* [Microsoft Cognitive Toolkit](https://docs.microsoft.com/cognitive-toolkit/cntk-on-azure)
* [Máquina virtual de aprendizaje profundo](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.dsvm-deep-learning)
* [Batch Shipyard recipes for deep learning](https://github.com/Azure/batch-shipyard/tree/master/recipes#deeplearning)






## <a name="hpc-and-gpu-vm-sizes"></a>Tamaños de máquinas virtuales de HPC y GPU
Azure ofrece varios tamaños para las máquinas virtuales [Linux](../articles/virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) y [Windows](../articles/virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), incluidos los tamaños diseñados para cargas de trabajo de proceso intensivo. Por ejemplo, las máquinas virtuales H16r y H16mr pueden conectarse a una red RDMA de back-end de alto rendimiento. Esta red de nube puede mejorar el rendimiento de las aplicaciones paralelas estrechamente vinculadas que se ejecutan en [Microsoft MPI](https://msdn.microsoft.com/library/bb524831.aspx) o Intel MPI. 

Las máquinas virtuales de la serie N ofrecen GPU de NVIDIA diseñadas para aplicaciones de proceso intensivo o de uso intensivo de gráficos, como las de aprendizaje y visualización de inteligencia artificial (AI). 

Más información:

* Tamaños de máquina virtual para procesos de alto rendimiento en [Linux](../articles/virtual-machines/linux/sizes-hpc.md) y [Windows](../articles/virtual-machines/windows/sizes-hpc.md) 
* Tamaños de máquina virtual habilitados para GPU para [Linux](../articles/virtual-machines/linux/sizes-gpu.md) y [Windows](../articles/virtual-machines/windows/sizes-gpu.md) 

Obtenga información sobre cómo:

* [Configuración de un clúster de Linux RDMA para ejecutar aplicaciones MPI](../articles/virtual-machines/linux/classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Configuración de un clúster de Windows RDMA con HPC Pack para ejecutar aplicaciones MPI](../articles/virtual-machines/windows/classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Usar máquinas virtuales para procesos intensivos en grupos de Batch](../articles/batch/batch-pool-compute-intensive-sizes.md)



## <a name="azure-batch"></a>Azure Batch
[Batch](../articles/batch/batch-technical-overview.md) es un servicio de plataforma para ejecutar aplicaciones en paralelo a gran escala y de informática de alto rendimiento (HPC) de manera eficaz en la nube. Azure Batch programa el trabajo de proceso intensivo para que se ejecute en un grupo administrado de máquinas virtuales y puede escalar automáticamente los recursos de proceso para satisfacer las necesidades de sus trabajos. 

Los proveedores o desarrolladores SaaS pueden usar los SDK y las herramientas de Batch para integrar las aplicaciones de HPC o las cargas de trabajo de los contenedores con Azure, almacenar provisionalmente datos en Azure y crear canalizaciones de ejecución de trabajos. 

Obtenga información sobre cómo:

* [Empezar a desarrollar con Batch](../articles/batch/batch-dotnet-get-started.md)
* [Usar ejemplos de código de Azure Batch](https://github.com/Azure/azure-batch-samples)
* [Uso de máquinas virtuales de prioridad baja con Batch](../articles/batch/batch-low-pri-vms.md)
* [Ejecutar cargas de trabajo de HPC en contenedores con Batch Shipyard](https://github.com/Azure/batch-shipyard)
* [Usar el lenguaje R con Batch](https://github.com/Azure/doAzureParallel)

## <a name="workload-managers"></a>Administradores de cargas de trabajo

Los siguientes son ejemplos de administradores de clústeres y cargas de trabajo que se pueden ejecutar en la infraestructura de Azure. Cree clústeres independientes en máquinas virtuales de Azure o dé el salto a máquinas virtuales de Azure desde un clúster local. 
* [TIBCO DataSynapse GridServer](https://azure.microsoft.com/blog/tibco-datasynapse-comes-to-the-azure-marketplace/) 
* [Bright Cluster Manager](http://www.brightcomputing.com/technology-partners/microsoft)
* [IBM Spectrum Symphony y Symphony LSF](https://azure.microsoft.com/blog/ibm-and-microsoft-azure-support-spectrum-symphony-and-spectrum-lsf/)
* [PBS Pro](http://pbspro.org)
* [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029(v=ws.11).aspx): consulte varias opciones de ejecución en máquinas virtuales [Windows](../articles/virtual-machines/windows/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) y [Linux](../articles/virtual-machines/linux/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 



## <a name="hpc-storage"></a>Almacenamiento de HPC

Las cargas de trabajo a gran escala de Batch y HPC presentan una demanda de almacenamiento y acceso de datos que superan las capacidades de los sistemas de archivos en la nube tradicionales. Implemente soluciones de sistema de archivos en paralelo en Azure como [Lustre](http://lustre.org/) y [BeeGFS](http://www.beegfs.com/content/).

Más información:

* [Sistemas de archivos en paralelo para el almacenamiento de HPC en Azure](https://blogs.msdn.microsoft.com/azurecat/2017/03/17/parallel-file-systems-for-hpc-storage-on-azure/)


## <a name="related-azure-services"></a>Servicios de Azure relacionados

Las máquinas virtuales de Azure, los conjuntos de escalado de máquinas virtuales, Batch y los servicios de proceso relacionados son el fundamento de muchas soluciones de HPC para Azure. Sin embargo, la solución puede aprovechar las ventajas de muchos servicios de Azure relacionados. Aquí se muestra una lista parcial:

### <a name="storage"></a>Storage

* [Blob Storage, Table Storage y Queue Storage](../articles/storage/storage-introduction.md)
* [Almacenamiento de archivos](../articles/storage/storage-files-introduction.md)

### <a name="data-and-analytics"></a>Datos y análisis
* [HDInsight](../articles/hdinsight/hadoop/apache-hadoop-introduction.md) para clústeres de Hadoop en Azure
* [Factoría de datos](../articles/data-factory/introduction.md)
* [Data Lake Store](../articles/data-lake-store/data-lake-store-overview.md)
* [Machine Learning](../articles/machine-learning/machine-learning-what-is-machine-learning.md)
* [SQL Database](../articles/sql-database/sql-database-technical-overview.md)

### <a name="networking"></a>Redes
* [Virtual Network](../articles/virtual-network/virtual-networks-overview.md)
* [ExpressRoute](../articles/expressroute/expressroute-introduction.md)

### <a name="containers"></a>Contenedores
* [Servicio Contenedor](../articles/container-service/dcos-swarm/container-service-intro.md)
* [Container Registry](../articles/container-registry/container-registry-intro.md)



## <a name="customer-stories"></a>Testimonios de clientes

Estos son ejemplos de clientes que han resuelto problemas empresariales con las soluciones de HPC de Azure:

* [ANEO](https://customers.microsoft.com/story/it-provider-finds-highly-scalable-cloud-based-hpc-redu) 
* [AXA Global P&amp;C](https://customers.microsoft.com/story/axa-global-p-and-c)
* [Axioma](https://customers.microsoft.com/story/axioma-delivers-fintechs-first-born-in-the-cloud-multi-asset-class-enterprise-risk-solution)
* [d3View](https://customers.microsoft.com/story/big-data-solution-provider-adopts-new-cloud-gains-thou)
* [Hymans Robertson](https://customers.microsoft.com/story/hymans-robertson)
* [MetLife](https://enterprise.microsoft.com/en-us/customer-story/industries/insurance/metlife/)
* [Microsoft Research](https://customers.microsoft.com/doclink/fast-lmm-and-windows-azure-put-genetics-research-on-fa)
* [Milliman](https://customers.microsoft.com/story/actuarial-firm-works-to-transform-insurance-industry-w)
* [Mitsubishi UFJ Securities International](https://customers.microsoft.com/story/powering-risk-compute-grids-in-the-cloud)
* [Schlumberger](http://azure.microsoft.com/blog/big-compute-for-large-engineering-simulations)
* [Towers Watson](https://customers.microsoft.com/story/insurance-tech-provider-delivers-disruptive-solutions)


## <a name="next-steps"></a>Pasos siguientes
* Obtenga más información sobre las soluciones de Big Compute para [simulación de ingeniería](https://simulation.azure.com/), [representación](https://simulation.azure.com/), [los mercados de capital y banca](https://finance.azure.com/) y [genómica](https://enterprise.microsoft.com/en-us/industries/health/genomics/).
* Para los anuncios más recientes, lea el [blog del equipo de Microsoft HPC y Batch](http://blogs.technet.com/b/windowshpc/) y el [blog de Azure](https://azure.microsoft.com/blog/tag/hpc/).

* Uso del servicio administrado y escalable Azure [Batch](https://azure.microsoft.com/services/batch/) para ejecutar cargas de trabajo de proceso intensivo, sin tener que administrar la infraestructura subyacente [Más información](https://azure.microsoft.com/en-us/solutions/architecture/hpc-big-compute-saas/)



