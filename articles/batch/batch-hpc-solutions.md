<properties
   pageTitle="Soluciones HPC y Lote en la nube | Microsoft Azure"
   description="Presenta escenarios (Big Compute) de informática de alto rendimiento y de ejecución por lotes y opciones de solución de Azure"
   services="batch, virtual-machines, cloud-services"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""/>

<tags
   ms.service="batch"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="big-compute"
   ms.date="07/01/2015"
   ms.author="danlep"/>

# Soluciones HPC y de ejecución por lotes

En este artículo se presentan soluciones de Azure de informática de alto rendimiento (HPC) y de ejecución por lotes también denominadas *Big Compute*. Aunque el artículo está destinado principalmente a los encargados de la toma de decisiones técnicas, los directores de TI y los proveedores de software independientes, otros profesionales de TI y desarrolladores pueden usarlo para familiarizarse con estas soluciones.

Las organizaciones tienen problemas informáticos a gran escala como el diseño de ingeniería y el análisis, la representación de imágenes, los modelos complejos, las simulaciones de Monte Carlo y los cálculos de riesgos financieros. Para ayudar a las organizaciones a resolver estos problemas y tomar decisiones con los recursos, las escalas y programaciones que necesitan, Azure ofrece capacidades y servicios de proceso escalables a petición. Con Azure, las organizaciones pueden:

* Crear soluciones híbridas, ampliando un clúster de HPC local para las descargas de cargas de trabajo máximas a la nube

* Ejecutar cargas de trabajo de clúster HPC completamente en Azure mediante conjuntos de herramientas y aplicaciones existentes

* Usar un servicio administrado y escalable de Azure como [lote](http://azure.microsoft.com/documentation/services/batch/) para ejecutar cargas de trabajo de proceso intensivo sin tener que implementar y administrar la infraestructura de proceso

Azure también ofrece herramientas de desarrollo y servicios para las organizaciones y los proveedores de software para crear soluciones Big Compute personalizadas e integrales.


## Segundo plano: aplicaciones HPC y de ejecución por lotes

A diferencia de las aplicaciones web y de muchas aplicaciones de línea de negocio, las aplicaciones HPC y de ejecución por lotes tienen un inicio y una finalización definidos y pueden ejecutarse de manera programada o a petición, a veces durante horas o más tiempo. La mayoría se dividen en dos categorías principales: *intrínsecamente paralelas* (en ocasiones denominada "embarazosamente paralelas", porque los problemas que solucionan se prestan a ejecutarse en paralelo en varios procesadores o equipos) y *estrechamente acopladas*. Para obtener más información acerca de estos tipos de aplicaciones, consulte la tabla siguiente. Algunas soluciones de Azure funcionan mejor para un tipo u otro.

>[AZURE.NOTE]En las soluciones HPC y de ejecución por lotes, una instancia en ejecución de una aplicación normalmente se denomina un *trabajo* y cada trabajo podría dividirse en *tareas*. Y los recursos de proceso en clúster para la aplicación se denominan a menudo *nodos de ejecución*.

Escriba | Características | Ejemplos
------------- | ----------- | ---------------
**Intrínsecamente paralelos**<br/><br/>![Intrínsecamente paralelos][parallel] |• Los equipos individuales ejecutan la lógica de la aplicación de forma independiente<br/><br/> • Agregar equipos permite que la aplicación escale y reduzca el tiempo de cálculo<br/><br/>• La aplicación consta de archivos ejecutables independientes o se divide en un grupo de servicios invocados por un cliente (una arquitectura orientada a servicios o SOA, aplicación) |• Modelado de riesgos financieros<br/><br/>• Representación y procesamiento de imágenes<br/><br/>• Codificación y transcodificación multimedia<br/><br/>• Simulaciones de Monte Carlo<br/><br/>• Pruebas de Software
**Estrechamente acoplados**<br/><br/>![Estrechamente acoplados][coupled] |• La aplicación requiere la interactuación de los nodos de ejecución o el intercambio de resultados intermedios<br/><br/>• Los nodos de ejecución pueden comunicarse mediante la Interfaz de Paso de Mensajes (MPI) (MPI), un protocolo de comunicación común para la informática paralela<br/><br/>• La aplicación es sensible a la latencia de red y al ancho de banda<br/><br/>• El rendimiento de la aplicación se puede mejorar mediante la infraestructura de proceso compatible con tecnologías de red de alta velocidad como InfiniBand y acceso directo a memoria remoto (RDMA) |• Modelado de depósitos de petróleo y gas •<br/><br/>• Diseño de ingeniería y análisis, como la dinámica de fluidos computacionales<br/><br/>• Simulaciones físicas como choques de automóviles y reacciones nucleares<br/><br/>• Previsión meteorológica de

### Consideraciones para la ejecución de aplicaciones HPC y de ejecución en lotes en la nube

Puede migrar fácilmente muchas aplicaciones que están diseñadas para ejecutarse en clústeres HPC locales a Azure o a un entorno híbrido (entre entornos). Sin embargo, puede haber algunas limitaciones o consideraciones, entre las que se incluyen:


* **Disponibilidad ininterrumpida de los recursos en la nube**: según el tipo de recursos de proceso en la nube seleccionados para la solución, es posible que no pueda disfrutar de disponibilidad continua del equipo durante la ejecución de un trabajo. Señalización de comprobación de progreso y control de estado son técnicas comunes para controlar posibles errores transitorios, y resultan más necesarias al aprovechar los recursos en la nube.


* **Acceso a datos**: las técnicas de acceso a datos normalmente disponibles en un clúster de red empresarial, por ejemplo, NFS, es posible que requieran una configuración especial en la nube, o puede que deban adoptar prácticas de acceso de datos y patrones diferentes para la nube.

* **Movimiento de datos**: para las aplicaciones que procesan grandes cantidades de datos, es necesario idear estrategias para mover los datos al almacenamiento en la nube y para procesar recursos, y es posible que necesite considerar la conexión a redes entre locales de alta velocidad como [Azure ExpressRoute](http://azure.microsoft.com/services/expressroute/). Considere también las limitaciones legales, normativas o de directivas para almacenar o acceder a esos datos.


* **Licencias**: póngase en contacto con el proveedor de cualquier aplicación comercial para obtener información acerca de las licencias u otras restricciones para la ejecución en la nube. No todos los proveedores ofrecen licencias de pago por uso. Es posible que deba planear un servidor de licencias en la nube para su solución o una conexión a un servidor de licencias local.


### ¿Big Compute o Big Data?

Las líneas divisorias entre aplicaciones Big Compute y Big Data no siempre están claras y es posible que algunas aplicaciones tengan las características de ambas. Ambas incluyen cálculos a gran escala, normalmente en clústeres de equipos que se ejecutan de forma local, en la nube o en ambas ubicaciones. Pero los enfoques de las soluciones y las herramientas de soporte técnico pueden diferir.

• **Big Compute** suele implicar a las aplicaciones que se basan en la capacidad y la memoria de la CPU, como simulaciones de ingeniería, modelado de riesgos financieros y representación digital. Es posible que los clústeres que están detrás de una solución Big Compute incluyan equipos con procesadores de varios núcleos especializados para realizar cálculos sin formato y hardware especializado de alta velocidad para conectar los equipos.

• **Big Data** soluciona problemas de análisis de datos que implican grandes cantidades de datos que no pueden administrarse mediante un único equipo o sistema de administración de datos, como grandes volúmenes de registros web o de otros datos de inteligencia empresarial. Big Data tiene a confiar más en la capacidad del disco y el rendimiento de E/S que en la potencia de la CPU, y una solución Big Data usa a menudo herramientas especializadas como Apache Hadoop para administrar el clúster y efectuar una partición de los datos. (Para obtener información acerca de Azure HDInsight y otras soluciones Azure Hadoop, consulte[Hadoop](http://azure.microsoft.com/solutions/hadoop/)).

## Administración de recursos y programación de trabajos

La ejecución de las aplicaciones Lote y HPC normalmente incluye un *Administrador de clústeres* y un *programador de trabajos* para ayudar a administrar los recursos de proceso en clúster y asignarlos a las aplicaciones que ejecutan los trabajos. Estas funciones pueden realizarse mediante herramientas independientes o una herramienta integrada.

* **Administrador de clústeres**: aprovisiona, publica y administra recursos de proceso (o nodos de ejecución). Dependiendo de la herramienta, es posible que un administrador de clústeres automatice la instalación de imágenes de sistema operativo y aplicaciones en nodos de ejecución, escalar los recursos de proceso en función de la demanda y supervisar el rendimiento de los nodos.

* **Programador de trabajos**: especifica los recursos (por ejemplo, los procesadores o la memoria) que necesita una aplicación y las condiciones que determinan cuándo se ejecutará. Un programador de trabajos mantiene una cola de trabajos y les asigna recursos en función de una prioridad asignada u otras características.

Las herramientas de agrupación en clústeres y de programación de trabajos en clústeres basados en Windows y Linux, o aquellas que se desarrollaron en forma independiente, pueden migrarse correctamente a Azure. Por ejemplo,[Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) es la solución gratuita de clúster de proceso de Microsoft para equipos basados en Windows y Windows Server. Para reducir la necesidad de recursos de proceso locales específicos, puede ampliar un clúster de HPC Pack para usar los nodos de ejecución de Azure a petición o implementar un clúster completamente en máquinas virtuales de Azure.

### Flujos de trabajo de Big Compute

Herramientas de administración de clústeres y de programación de trabajos ayudan a lograr un flujo de trabajo de Big Compute con pasos de predicción. Dependiendo de la solución, puede omitir algunos pasos de la siguiente lista o introducir herramientas adicionales personalizadas. Los flujos de trabajo intensivos en datos pueden implicar pasos previos y posteriores al procesamiento de datos adicionales (no mostrados).

1. **Aprovisionar**: preparar el entorno de proceso con la infraestructura necesaria, recursos de proceso y almacenamiento para ejecutar aplicaciones

2. **Fase**: mover datos de entrada y aplicaciones al entorno de proceso

3. **Programación**: configurar trabajos y tareas y asignar los recursos de proceso cuando los recursos estén disponibles

4. **Supervisar**: proporcionar información de estado sobre el progreso de los trabajos y tareas; controlar errores o excepciones

5. **Finalizar**: devolver resultados y procesar posteriormente los datos de salida si es necesario

## Servicios de Azure para Big Compute

Azure tiene una amplia gama de servicios de proceso, datos, redes y relacionados que puede usar para soluciones y flujos de trabajo Big Compute. Para obtener información detallada acerca de cada uno de estos servicios, consulte la documentación de los servicios de Azure. Consulte [Escenarios de la solución](#solution-scenarios) en este artículo para obtener algunos enfoques comunes con aplicaciones HPC y Lote.

>[AZURE.NOTE]Con frecuencia se introducen nuevos servicios en la plataforma Azure que podrían ser útiles para su escenario. El uso de los servicios de vista previa solo se recomienda para implementaciones de prueba o de prueba de concepto, no para cargas de trabajo de producción. Si tiene alguna pregunta, póngase en contacto con un [asociado de Azure](https://pinpoint.microsoft.com/en-US/search?keyword=azure) o envíe un correo electrónico a *bigcompute@microsoft.com*.

### Servicios de proceso

Los Servicios proceso de Azure son el núcleo de una solución Big Compute. Los servicios de proceso de la siguiente tabla se usan con frecuencia y ofrecen ventajas para diferentes escenarios. En un nivel básico, estos servicios ofrecen modos distintos para que las aplicaciones se ejecuten en instancias de proceso basadas en máquinas virtuales que Azure proporciona mediante tecnología Windows Server Hyper-V. Dependiendo del servicio, estas instancias pueden ejecutar una amplia variedad de herramientas y sistemas operativos Linux y Windows estándar y personalizados. Azure ofrece [diversos tamaños de instancia](../virtual-machines/virtual-machines-sizes-specs.md) con diferentes configuraciones de núcleos de CPU, memoria, capacidad de disco y otras características. Según sus necesidades puede escalar las instancias a miles de núcleos y, a continuación, reducirlas cuando se necesiten menos recursos.

>[AZURE.NOTE]Puede aprovechar las instancias A8-A11 para mejorar el rendimiento de algunas cargas de proceso intensivo, incluidas las aplicaciones MPI paralelas que requieren una red de aplicaciones de baja latencia y alto rendimiento. Consulte [Sobre las instancias informáticas intensivas A8, A9, A10 y A11](../virtual-machines/virtual-machines-a8-a9-a10-a11-specs.md).

Servicio | Descripción
------------- | -----------
**[Servicios en la nube](http://azure.microsoft.com/documentation/services/cloud-services)**<br/><br/> |• Puede ejecutar aplicaciones de Big Compute en instancias de rol de trabajo, que son máquinas virtuales que ejecutan una versión de Windows Server y están administradas completamente por Azure<br/><br/>• Habilite aplicaciones escalables y fiables con baja sobrecarga administrativa, que se ejecutan en un modelo de plataforma como servicio (PaaS)<br/><br/>• Es posible que requieran herramientas adicionales o desarrollo para la integración con soluciones de clúster HPC locales
**[Máquinas virtuales](http://azure.microsoft.com/documentation/services/virtual-machines)**<br/><br/> |• Proporcionan una infraestructura de proceso como servicio (IaaS) mediante la tecnología Microsoft Hyper-V<br/><br/>• Permiten aprovisionar y administrar de manera flexible equipos en la nube persistentes desde imágenes de Windows Server o Linux estándar o imágenes y discos de datos proporcionados por usted o de [Azure Marketplace](https://azure.microsoft.com/marketplace/)<br/><br/>• Ejecutan herramientas y aplicaciones de clúster de proceso locales completamente en la nube
**[Lote](http://azure.microsoft.com/documentation/services/batch)**<br/><br/> |• Ejecuta cargas de trabajo en lotes y paralelas a gran escala, como la representación de imágenes y la codificación y transcodificación multimedia en un servicio totalmente administrado<br/><br/>• Proporciona la programación de trabajos y el escalamiento automático de un grupo de máquinas virtuales administrado<br/><br/>• Permite a los programadores crear y ejecutar aplicaciones como un servicio o habilitar aplicaciones existentes para la nube<br/>

### Servicios de almacenamiento

Normalmente, una solución Big Compute funciona en un conjunto de datos de entrada y genera datos para sus resultados. Entre algunos de los servicios de almacenamiento de Azure que se usan en muchas soluciones Big Compute se incluyen los siguientes:

* [Almacenamiento de blobs, tablas y en cola](http://azure.microsoft.com/documentation/services/storage): administre grandes cantidades de datos no estructurados, datos NoSQL y mensajes de flujo de trabajo y comunicación, respectivamente. Por ejemplo, puede usar el almacenamiento de blobs para grandes conjuntos de datos técnicos o las imágenes de entrada o archivos multimedia que su aplicación procesa. Puede usar colas para la comunicación asincrónica en una solución. Consulte [introducción al Almacenamiento de Microsoft Azure](../storage/storage-introduction.md) para obtener más información acerca de estas soluciones de almacenamiento.

* [Almacenamiento de archivos de Azure](http://azure.microsoft.com/services/storage/files/): comparte archivos comunes y datos en Azure mediante el protocolo SMB estándar, que es necesario para algunas soluciones de clúster HPC.

### Servicios de datos y análisis

Algunos escenarios Big Compute implican flujos de datos a gran escala o generan datos que necesitan un procesamiento o análisis posterior. Para ello, Azure ofrece una serie de servicios de datos y análisis, entre los que se incluyen:

* [Factoría de datos](http://azure.microsoft.com/documentation/services/data-factory): crea flujos de trabajo controlados por datos (canalizaciones) que unen, agregan y transforman datos con origen en almacenes de datos locales, basados en la nube y de Internet.

* [Base de datos SQL](http://azure.microsoft.com/documentation/services/sql-database): proporciona las características clave de un sistema de administración de base de datos relacional de Microsoft SQL Server en un servicio de plataforma administrada.

* [HDInsight](http://azure.microsoft.com/documentation/services/hdinsight): implementa y aprovisiona clústeres Apache Hadoop basados en Windows Server o Linux en la nube para administrar, analizar e informar sobre big data con alta confiabilidad y disponibilidad.

* [Aprendizaje automático](http://azure.microsoft.com/documentation/services/machine-learning): le ayuda a crear, probar, operar y administrar soluciones de análisis predictivas en un servicio de plataforma totalmente administrada.

### Servicios adicionales

Es posible que la solución Big Compute deba incluir otros servicios de infraestructura y plataforma de Azure para conectarse a recursos locales o de otros entornos. Algunos ejemplos son:

* [Red virtual](http://azure.microsoft.com/documentation/services/virtual-network): crea una sección aislada lógicamente en Azure para conectarse a recursos de Azure en su centro de datos local o en un único equipo cliente con IPSec; permite que las aplicaciones Big Compute tengan acceso a datos locales, servicios de Active Directory y servidores de licencias

* [ExpressRoute](http://azure.microsoft.com/documentation/services/expressroute): crea una conexión privada entre los centros de datos de Microsoft y la infraestructura local o en un entorno de ubicación compartida, con una mayor seguridad, más confiabilidad, velocidades más rápidas y latencias más bajas que las de las conexiones normales a través de Internet.

* [Bus de servicio](http://azure.microsoft.com/documentation/services/service-bus): proporciona varios mecanismos para que las aplicaciones se comuniquen o intercambien datos, independientemente de si están ubicadas en Azure, en otra plataforma en la nube o en un centro de datos.

## Escenarios de solución

Los siguientes son escenarios comunes para ejecutar cargas de trabajo de Big Compute en Azure mediante el aprovechamiento de soluciones de clúster HPC existentes, servicios de Azure o una combinación de ambos.

>[AZURE.NOTE]Para las organizaciones con cargas de trabajo de proceso intensivo que no están adaptadas a las herramientas de clúster HPC estándar o que no se migran directamente a los servicios de Azure, Azure proporciona a los desarrolladores y asociados un conjunto completo de capacidades de proceso, servicios, opciones de arquitectura y herramientas de desarrollo. Azure puede admitir flujos de trabajo Big Compute personalizados que permiten escalar a miles de núcleos de proceso.

### Escenario 1. Introducir un clúster HPC local en Azure

**¿Cuando seleccionaría esta opción?**: puede que ya tenga un clúster HPC local ejecutando sus cargas de trabajo de proceso intensivo, pero necesita recursos de proceso adicionales para períodos de máxima actividad, como la generación de informes de fin de mes o proyectos especiales. En lugar de adquirir, implementar y administrar hardware y software adicional que es posible que esté inactivo la mayor parte del tiempo, puede usar Azure para agregar capacidad de proceso a petición al clúster existente.

Por ejemplo, si su clúster HPC local existente se genera con [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029), puede agregar recursos de proceso adicionales en forma de instancias de rol de trabajo de Azure que se ejecutan en un servicio en la nube. Consulte la siguiente figura. Para obtener más información e instrucciones paso a paso, consulte [Introducción en Azure con Microsoft HPC Pack](https://technet.microsoft.com/library/gg481749.aspx).

![Ráfaga de clúster][burst_cluster]

>[AZURE.NOTE]Si desea minimizar el impacto del clúster de HPC Pack, podría reducir el clúster local a tan solo el nodo principal de HPC Pack. A continuación, agregue todos los recursos de proceso a petición en Azure. Para obtener un tutorial que le guíe en este escenario, consulte [Configuración de un clúster híbrido con Microsoft HPC Pack](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md).

Esta solución híbrida aprovecha la inversión existente en un clúster local, pero permite escalar la infraestructura local fija para cargas de trabajo típicas (que no se produzcan en horas punta). Si necesita tener acceso a un almacén de datos o servidor de licencias local, puede configurar una red virtual Azure para conectar el clúster local a Azure.

### Escenario 2. Crear un clúster HPC completamente en Azure

**¿Cuándo se debe seleccionar esta opción?**: es posible que ya haya efectuado una notable inversión en un clúster HPC de Windows o Linux local que incluya herramientas de administración y programación y aplicaciones personalizadas. Es posible que necesite capacidad de clúster adicional para ejecutar sus aplicaciones y conjuntos de herramientas existentes, pero no desee invertir en infraestructura local adicional o modificar las aplicaciones. O bien es posible que deba crear un clúster nuevo para un período de tiempo corto o largo, pero no desee pagar el costo de adquisición, mantenimiento y funcionamiento, ni asignar el espacio necesario para instalarlo e implementarlo.

Puede usar herramientas de automatización de Azure para crear un clúster HPC en máquinas virtuales de Azure para crear la capacidad que necesita. Dependiendo de las máquinas virtuales que implemente, puede ejecutar una amplia variedad de cargas de trabajo HPC y paralelas, incluido aplicaciones MPI estrechamente acopladas.

>[AZURE.NOTE]Consulte al proveedor de la solución de clúster local y aplicaciones si hay requisitos adicionales y prácticas recomendadas para ejecutar en una nube pública que proporcione infraestructura como servicio (IaaS).

Por ejemplo, puede crear un clúster HPC basado en Windows Server con [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) en máquinas virtuales de servicios de infraestructura de Azure (IaaS) para ejecutar las cargas de trabajo, tal y como se muestra en la siguiente ilustración simplificada. Un usuario del clúster puede enviar un trabajo de forma segura al clúster en la nube a través de herramientas de envío de trabajos de HPC Pack estándar que se ejecutan en un equipo cliente. Consulte [Microsoft HPC Pack en máquinas virtuales de Azure](https://msdn.microsoft.com/library/azure/dn518135.aspx) para obtener detalles y opciones de implementación.

![Clúster en IaaS][iaas_cluster]

**Implementación automatizada**: para implementar un gran número de máquinas virtuales de Windows Server o Linux, puede usar imágenes de máquina virtual estándar o personalizadas y herramientas de automatización de Azure como la [interfaz de línea de comandos de Azure](../xplat-cli.md) o [Azure PowerShell](../powershell-install-configure.md). Algunos ejemplos son:

* Para implementar un clúster de HPC Pack en servicios de infraestructura de Azure, puede ejecutar un [script de Azure PowerShell](https://msdn.microsoft.com/library/azure/dn864734.aspx)desde un equipo cliente; el script usa una imagen de máquina virtual Windows Server con HPC Pack preinstalado. También puede usar una [plantilla de inicio rápido](https://azure.microsoft.com/documentation/templates/create-hpc-cluster/)con Azure PowerShell o la CLI de Azure para implementar un clúster de HPC Pack.

* Puede usar una [plantilla de inicio rápido](https://azure.microsoft.com/documentation/templates/slurm/) con Azure PowerShell o la CLI de Azure para implementar un clúster de Linux que ejecute el administrador de cargas de trabajo de código abierto [SLURM](https://computing.llnl.gov/linux/slurm/).

Colocar un clúster HPC entero en la nube puede tener ventajas claras.

* Una organización puede ejecutar trabajos de HPC sin necesidad de adquirir y administrar hardware local adicional y puede controlar el tamaño del clúster para usar los recursos de proceso eficazmente.

* Muchas aplicaciones de clúster local pueden ejecutarse sin cambios o con modificaciones menores en un clúster basado en la nube.

* En comparación con una solución híbrida que extiende un clúster local en la nube, ejecutar una aplicación completamente en la nube puede simplificar el acceso a datos. En lugar de dividir los datos entre las instalaciones en la nube y locales, o hacer que una parte de la aplicación acceda a los datos de forma remota, todos los datos de aplicación pueden almacenarse en la nube.

* Algunos proveedores de software optimizan sus aplicaciones para ejecutarse en clústeres basados en la nube. Por ejemplo, mediante la implementación del [servidor de cálculo distribuido por MATLAB](http://www.mathworks.com/products/distriben/), desde MathWorks, en un clúster de HPC Pack en máquinas virtuales de Azure, puede ejecutar trabajos de MATLAB paralelos completamente con recursos de proceso basados en la nube.

### Escenario 3. Escalar horizontalmente una aplicación paralela en Azure

**¿Cuando se debe seleccionar esta opción?**: puede estar ejecutando una aplicación de proceso intensivo como una simulación de Monte Carlo, representación de animaciones o transcodificación de medios en estaciones de trabajo locales o en un clúster pequeño. No conviene administrar los recursos de proceso o un programador de trabajos; en su lugar, conviene centrarse en la ejecución de la aplicación de manera eficaz para resolver sus problemas empresariales. ¿O puede descargar la aplicación de proceso intensivo o una aplicación de terceros para que se ejecute por completo como un servicio en la nube.

Dependiendo de la carga de trabajo, puede aprovechar un servicio Big Compute existente en Azure, hospedado por Microsoft u otro proveedor de servicios, para simplificar la administración de la infraestructura y la aplicación de la solución. Algunos servicios hospedan aplicaciones específicas para clientes de sectores concretos. Algunos servicios se conectan a aplicaciones locales, lo que permite una solución híbrida. Otros, como [Servicios multimedia de Azure](http://azure.microsoft.com/documentation/services/media-services) son servicios de plataforma específicos.

Para habilitar fácilmente para la nube y escalar horizontalmente una aplicación que ejecute en la actualidad, [Lote](http://azure.microsoft.com/documentation/services/batch) proporciona API para programar trabajos y administrar recursos de proceso de un servicio. Lote administra la implementación y el ajuste automático de las máquinas virtuales, la programación de trabajos, la recuperación ante desastres, el movimiento de datos, la administración de dependencias, la implementación de aplicaciones y el resto de instalaciones necesarias para ejecutar trabajos en la nube. Actualmente, Lote es ideal para aplicaciones intrínsecamente paralelas, como la representación de imágenes, el modelado de riesgos financieros y las simulaciones de Monte Carlo que se ejecutan en recursos de proceso Windows Server.

Consulte la figura siguiente para obtener información acerca de un flujo de trabajo típico que un programador puede crear mediante Lote.

![Azure Batch][batch_proc]

1. Cargue archivos de entrada (por ejemplo, datos de origen o imágenes, archivos .exe o de script de aplicación requeridos y sus dependencias) en Almacenamiento de Azure.

2. Cree un servicio de Lote que:

    a. Implemente un grupo de máquinas virtuales de Azure idénticas para ejecutar la aplicación, análogas a nodos de ejecución de un clúster HPC. El programador especifica su tamaño, el sistema operativo que ejecutan y otras propiedades como si el grupo puede ajustarse automáticamente. Cuando se ejecuta una tarea, se le asigna automáticamente una máquina virtual de este grupo.

    b. Cree un trabajo para ejecutar la aplicación. El desarrollador puede especificar una programación y la prioridad del trabajo o el trabajo puede ejecutarse a petición.

    c. Divida el trabajo en tareas. Cada tarea es básicamente una línea de comandos que se ejecuta en una de las máquinas virtuales del grupo para procesar la información de uno de los archivos de datos cargados en el almacenamiento.

3. Ejecute el servicio y supervise los resultados.


## Pasos siguientes

* Consulte [Recursos técnicos para HPC y Lote](big-compute-resources.md)para encontrar orientación técnica para su solución.

* Para los anuncios más recientes, vea el [blog del equipo de Microsoft HPC y Batch](http://blogs.technet.com/b/windowshpc/) y el [blog de Azure](http://azure.microsoft.com/blog/tag/hpc/).

<!--Image references-->
[parallel]: ./media/batch-hpc-solutions/parallel.png
[coupled]: ./media/batch-hpc-solutions/coupled.png
[iaas_cluster]: ./media/batch-hpc-solutions/iaas_cluster.png
[burst_cluster]: ./media/batch-hpc-solutions/burst_cluster.png
[batch_proc]: ./media/batch-hpc-solutions/batch_proc.png

<!---HONumber=July15_HO5-->