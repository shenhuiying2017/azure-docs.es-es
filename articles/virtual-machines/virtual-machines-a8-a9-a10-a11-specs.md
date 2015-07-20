<properties
 pageTitle="Acerca de las instancias A8, A9, A10 y A11 | Microsoft Azure"
 description="Obtenga información general y consideraciones sobre el uso de las instancias de proceso intensivo A8, A9, A10 y A11 de Azure."
 services="virtual-machines, cloud-services"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""/>
<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="infrastructure-services"
 ms.date="07/08/2015"
 ms.author="danlep"/>

# Sobre las instancias informáticas intensivas A8, A9, A10 y A11

Este artículo proporciona información general y consideraciones sobre el uso de las instancias de Azure A8, A9, A10 y A11, también conocidas como instancias de *cálculo intensivo*. Entre las características clave de estas instancias se incluyen:

* **Hardware de alto rendimiento**: el hardware de centro de datos de Azure que ejecuta estas instancias está diseñado y optimizado para el proceso y para aplicaciones de red intensiva, incluidas las aplicaciones de clúster de proceso de alto rendimiento (HCP), el modelado y las simulaciones.

* **Conexión de red RDMA para aplicaciones MPI**: cuando se configura con los controladores de red necesarios, las instancias A8 y A9 pueden comunicarse con otras instancias de A8 y A9 a través de una red de baja latencia y alto rendimiento en Azure basadas en tecnología de acceso a memoria directa remota (RDMA). Esta característica puede mejorar el rendimiento de las aplicaciones que usan las implementaciones de la interfaz de transferencia de mensajes de Linux o Windows compatibles.

* **Compatibilidad con clústeres de HPC de Windows y Linux**: implemente la administración del clúster y el software de programación de trabajos en las instancias A8, A9, A10 y A11 en Azure para crear un clúster HPC independiente o para agregar más capacidad a un clúster local. Como otros tamaños de máquina virtual de Azure, las instancias A8, A9, A10 y A11 admiten imágenes del sistema operativo Windows Server y Linux estándar o personalizadas o plantillas del administrador de recursos en máquinas virtuales de Azure (IaaS) o versiones de SO invitado de Azure en servicios en la nube (PaaS, solamente para Windows Server).

>[AZURE.NOTE]Las instancias A10 y A11 tienen las mismas optimizaciones y especificaciones de rendimiento que las instancias A8 y A9. Sin embargo, no incluyen el acceso a la red RDMA en Azure. Están diseñadas para aplicaciones HPC que no requieren comunicación constante y de baja latencia entre nodos, también conocidas como aplicaciones paramétricas o embarazosamente paralelas. Y cuando se ejecutan cargas de trabajo distintas de aplicaciones MPI, las instancias A8 y A9 no tienen acceso a la red RDMA y son funcionalmente equivalentes a las instancias A10 y A11.


## Especificaciones

### CPU y memoria

Las instancias de proceso intensivo Azure A8, A9, A10 y A11 cuentan con CPU de alta velocidad y varios núcleos y grandes cantidades de memoria, como se muestra en la tabla siguiente.

Tamaño | CPU | Memoria
------------- | ----------- | ----------------
A8 y A10 | Intel® Xeon® E5-2670<br/>8 núcleos a 2,6 GHz | DDR3-1600 MHz<br/>56 GB
A9 y A11 | Intel® Xeon® E5-2670<br/>16 núcleos a 2,6 GHz | DDR3-1600 MHz<br/>112 GB


>[AZURE.NOTE]Detalles adicionales de procesador, incluidas las extensiones del conjunto de instrucciones compatibles, se encuentran en el sitio web Intel.com. Para las capacidades de almacenamiento de máquina virtual y los detalles del disco, consulte [Tamaños de máquinas virtuales](virtual-machines-size-specs.md).

### Adaptadores de red

Las instancias A8 y A9 tienen dos adaptadores de red que se conectan a las siguientes redes back-end de Azure.


Red | Descripción
-------- | -----------
Ethernet de 10 Gbps | Se conecta a los servicios de Azure (por ejemplo, almacenamiento y red virtual) y a Internet
Back-end de 32 Gbps, compatible con RDMA | Permite una baja latencia y comunicación de aplicaciones de alto rendimiento entre las instancias de un servicio de nube único o un conjunto de disponibilidad. Reservado solo para el tráfico MPI.


>[AZURE.IMPORTANT]En las máquinas virtuales A8 y A9 que disponen de Linux en IaaS, actualmente el acceso a la red RDMA solo está habilitado a través de aplicaciones que usan Azure Linux RDMA y la Intel MPI Library 5.0 en SUSE Linux Enterprise Server 12. En instancias de A8 y A9 con Windows Server en IaaS o PaaS, actualmente el acceso a la red RDMA está habilitado solamente a través de las aplicaciones que usan la interfaz Microsoft Network Direct mediante Microsoft MPI (MS-MPI). Consulte [Acceso a la red RDMA](#access-the-rdma-network) en este artículo para obtener información acerca de los requisitos adicionales.

Las instancias A10 y A11 tienen un único adaptador de red Ethernet de 10 Gbps que se conecta a servicios de Azure e Internet.

## Consideraciones para la suscripción de Azure

* **Cuenta de azure**: si desea implementar más de un pequeño número de instancias de proceso intensivo, considere la posibilidad de utilizar una suscripción de pago por uso u otras opciones de compra. También puede utilizar su suscripción a MSDN. Consulte [Ventajas de Azure para suscriptores de MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Si utiliza una [evaluación gratuita de Azure](http://azure.microsoft.com/pricing/free-trial/), solo podrá usar un número limitado de núcleos de proceso de Azure.

* **Cuota de núcleos**: es posible que tenga que aumentar la cuota de núcleos de su suscripción de Azure desde el valor predeterminado de 20 núcleos, que no es suficiente para muchos escenarios con instancias de 8 o 16 núcleos. Para las pruebas iniciales, puede considerar la posibilidad de solicitar un aumento de cuota hasta 100 núcleos. Para ello, abra una incidencia de soporte técnico gratuita como se muestra en [Descripción de los límites y aumentos de Azure](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/).

>[AZURE.NOTE]Las cuotas de Azure son límites de crédito, no garantías de capacidad. Solamente se le cobrarán los núcleos que utilice.

* **Grupo de afinidad**: actualmente no se recomienda un grupo de afinidad para la mayoría de las implementaciones nuevas. Sin embargo, tenga en cuenta que si usa un grupo de afinidad que contiene instancias de tamaños diferentes A8 - A11, no podrá usarlo también para las instancias A8 - A11 y viceversa.

* **Red virtual**: no se necesita una red virtual de Azure para usar instancias de proceso intensivo. Sin embargo, puede que necesite al menos una red virtual Azure basada en la nube para muchos escenarios IaaS o una conexión de sitio a sitio si necesita tener acceso a recursos locales como un servidor de licencias de aplicaciones. Necesitará crear una nueva red virtual (regional) antes de implementar las instancias. No se admite la adición de una máquina virtual A8, A9, A10 o A11 a una red virtual en un grupo de afinidad. Para obtener más información, consulte [Crear una red Virtual](https://msdn.microsoft.com/library/azure/dn631643.aspx) y [Configurar una red virtual con una conexión VPN de sitio a sitio](../vpn-gateway/vpn-gateway-site-to-site-create.md).

* **Servicio en la nube o conjunto de disponibilidad**: para conectarse a través de la red RDMA, deben implementarse las instancias A8 y A9 en el mismo servicio de nube (para escenarios de IaaS con máquinas virtuales de Linux o máquinas virtuales de Windows en la administración de servicios de Azure, o en escenarios de PaaS con Windows Server) o el mismo conjunto de disponibilidad (para máquinas virtuales Linux o Windows en el Administrador de recursos de Azure).

## Consideraciones sobre el uso de HPC Pack

### Consideraciones sobre el HPC Pack y Linux

[HPC Pack](https://technet.microsoft.com/library/jj899572.aspx) es la solución gratuita de administración de trabajos y clústeres HPC de Microsoft para Windows. Comenzando desde HPC Pack 2012 R2 Update 2, HPC Pack admite la ejecución de varias distribuciones de Linux en nodos de cálculo implementados en máquinas virtuales Azure, administradas por un nodo principal de Windows Server. Con la última versión de HPC Pack puede implementar un clúster basado en Linux que puede ejecutar aplicaciones MPI que accedan a la red RDMA en Azure. Para obtener más información, consulte la [documentación de HPC Pack](http://go.microsoft.com/fwlink/?LinkId=617894).

### Consideraciones sobre HPC Pack y Linux

HPC Pack no es necesario para usar las instancias A8, A9, A10 y A11 con Windows Server, pero es una herramienta recomendada para crear clústeres de Windows HPC en Azure. En el caso de las instancias de A8 y A9, HPC Pack es la forma más eficaz de ejecutar aplicaciones de MPI de Windows que tienen acceso a la red RDMA en Azure. HPC Pack incluye un entorno de tiempo de ejecución para la implementación de Microsoft de la interfaz de transferencia de mensajes para Windows.

Para que obtener más información y listas de comprobación para implementar y usar instancias de proceso intensivo en escenarios de IaaS y PaaS con HPC Pack en Windows Server, consulte [Instancias de proceso intensivo A8 y A9: inicio rápido con HPC Pack](https://msdn.microsoft.com/library/azure/dn594431.aspx).

## Acceso a la red RDMA

### Acceso desde máquinas virtuales Linux A8 y A9

En un único servicio en la nube o en conjunto de disponibilidad, las instancias A8 y A9 pueden tener acceso a la red RDMA de Azure al ejecutar aplicaciones MPI que usan controladores Linux RDMA para comunicarse entre instancias. En este momento, Azure Linux RDMA solo se admite con [Intel MPI Library 5.0](https://software.intel.com/es-es/intel-mpi-library/).

>[AZURE.NOTE]Actualmente los controladores Azure Linux RDMA no están disponibles para instalarse a través de extensiones de controladores. Solamente están disponibles mediante la imagen SLES 12 habilitada para RDMA desde Azure Marketplace.

Consulte la siguiente tabla para obtener información sobre los requisitos previos para las aplicaciones de Linux MPI para acceder a la red RDMA en clústeres de nodos de cálculo (IaaS).

Requisito previo | Máquinas virtuales (IaaS)
------------ | -------------
Sistema operativos | Imagen HPC de SUSE Linux Enterprise Server 12 (SLES 12) de Azure Marketplace
MPI | Intel MPI Library 5.0

### Acceso desde instancias Windows A8 y A9

En un único servicio en la nube o conjunto de disponibilidad, las instancias A8 y A9 pueden tener acceso a la red RDMA de Azure al ejecutar aplicaciones MPI que usan la interfaz Microsoft Network Direct para comunicarse entre instancias. En este momento Network Direct solo es compatible con MS-MPI de Microsoft para Windows. Las instancias A10 y A11 no incluyen el acceso a la red RDMA.

Consulte la siguiente tabla para obtener información acerca de los requisitos previos para que las aplicaciones de MPI tengan acceso a la red RDMA en las implementaciones de máquina virtual (IaaS) y de servicio en la nube (PaaS) de las instancias A8 o A9. Para obtener información de escenarios de implementación típicos, consulte [Instancias de proceso intensivo A8 y A9: Inicio rápido con HPC Pack](https://msdn.microsoft.com/library/azure/dn594431.aspx).


Requisito previo | Máquinas virtuales (IaaS) | Servicios en la nube (PaaS)
---------- | ------------ | -------------
Sistema operativos | Windows Server 2012 R2 o máquinas virtuales Windows Server 2012 | Familia de SO invitado Windows Server 2012 R2, Windows Server 2012 o Windows Server 2008 R2.
MPI | MS-MPI 2012 R2 o versiones posteriores, ya sean independientes o instaladas a través de HPC Pack 2012 R2 o versiones posteriores | MS-MPI 2012 R2 o versiones posteriores, ya sean instaladas a través de HPC Pack 2012 R2 o versiones posteriores


>[AZURE.NOTE]Para escenarios de IaaS, es necesario agregar la [extensión HpcVmDrivers](https://msdn.microsoft.com/library/azure/dn690126.aspx) a las máquinas virtuales para instalar los controladores de Windows necesarios para la conectividad RDMA.


## Aspectos adicionales que debe conocer

* Los tamaños de máquina virtual A8-A11 solo están disponibles en el nivel de precios estándar.

* No se puede cambiar el tamaño de una VM de Azure existente al tamaño A8, A9, A10 o A11.

* Las instancias a8, A9, A10 y A11 actualmente no se pueden implementar mediante un servicio en la nube que forma parte de un grupo de afinidad existente. Del mismo modo, no se puede utilizar un grupo de afinidad con un servicio de nube que contiene las instancias A8, A9, A10 y A11 para implementaciones de otros tamaños de instancia. Si intenta realizar estas implementaciones verá un mensaje de error similar a `Azure deployment failure (Compute.OverconstrainedAllocationRequest): The VM size (or combination of VM sizes) required by this deployment cannot be provisioned due to deployment request constraints.`


## Pasos siguientes

* Para obtener más información sobre la disponibilidad y precios de las instancias A8, A9, A10 y A11, consulte [Precios de máquinas virtuales](http://azure.microsoft.com/pricing/details/virtual-machines/) y [Precios de servicios en la nube](http://azure.microsoft.com/pricing/details/cloud-services/).
* Para empezar a implementar y usar instancias A8 y A9 con HPC Pack en Windows, consulte [Instancias de proceso intensivo A8 y A9: inicio rápido con HPC Pack](https://msdn.microsoft.com/library/azure/dn594431.aspx) y [Ejecutar aplicaciones de MPI en instancias A8 y A9](https://msdn.microsoft.com/library/azure/dn592104.aspx).

<!---HONumber=July15_HO2-->