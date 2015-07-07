<properties
 pageTitle="Acerca de las instancias A8, A9, A10 y A11 | Microsoft Azure"
 description="Este artículo proporciona información general y consideraciones sobre el uso de las instancias de proceso intensivo A8, A9, A10 y A11 de Azure."
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
 ms.date="06/09/2015"
 ms.author="danlep"/>

# Sobre las instancias informáticas intensivas A8, A9, A10 y A11

Este artículo proporciona información general y consideraciones sobre el uso de las instancias de Azure A8, A9, A10 y A11, también conocidas como instancias de *cálculo intensivo*. Entre las características clave que distinguen a estas instancias se incluyen:

* **Hardware de alto rendimiento**: el hardware de centro de datos de Azure que ejecuta estas instancias está diseñado y optimizado para el proceso y para aplicaciones de red intensiva, incluidas las aplicaciones de clúster de proceso de alto rendimiento (HCP), el modelado y las simulaciones.

* **Conexión de red RDMA**: las instancias A8 y A9 pueden comunicarse a través de una red de baja latencia y alto rendimiento en Azure basadas en tecnología de acceso a memoria directa remota (RDMA). Esta característica puede mejorar el rendimiento de aplicaciones paralelas de interfaz de paso de mensajes (MPI). (El acceso a red RDMA actualmente solo se admite para los servicios de nube y las máquinas virtuales basadas en Windows Server).

>[AZURE.NOTE]Las instancias A10 y A11 tienen las mismas optimizaciones y especificaciones de rendimiento que las instancias A8 y A9. Sin embargo, no incluyen el acceso a la red RDMA en Azure. Están diseñadas para aplicaciones HPC que no requieren comunicación constante y de baja latencia entre nodos, también conocidas como aplicaciones paramétricas o embarazosamente paralelas.

Como [otros tamaños de máquina virtual de Azure](virtual-machines-size-specs.md), las instancias A8, A9, A10 y A11 admiten imágenes del sistema operativo Windows Server y Linux estándar o personalizadas en máquinas virtuales de Azure (IaaS) o versiones de SO invitado de Azure en servicios en la nube (PaaS).

## Especificaciones

### CPU y memoria

Las instancias de proceso intensivo Azure A8, A9, A10 y A11 cuentan con CPU de alta velocidad y varios núcleos y grandes cantidades de memoria, como se muestra en la tabla siguiente.

Tamaño | CPU | Memoria
------------- | ----------- | ----------------
A8 y A10 | Intel® Xeon® E5-2670<br/>8 núcleos a 2,6 GHz | DDR3-1600 MHz<br/>56 GB
A9 y A11 | Intel® Xeon® E5-2670<br/>16 núcleos a 2,6 GHz | DDR3-1600 MHz<br/>112 GB


>[AZURE.NOTE]Detalles adicionales de procesador, incluidas las extensiones del conjunto de instrucciones compatibles, se encuentran en el sitio web Intel.com.


### Adaptadores de red

Las instancias A8 y A9 tienen dos adaptadores de red que se conectan a las siguientes redes back-end de Azure.


Red | Descripción
-------- | -----------
Ethernet de 10 Gbps | Se conecta a los servicios de Azure (por ejemplo, almacenamiento y red virtual) y a Internet
Back-end de 32 Gbps, compatible con RDMA | Permite una baja latencia y comunicación de aplicaciones de alto rendimiento entre las instancias de un servicio de nube único


>[AZURE.IMPORTANT]Actualmente, el acceso a la red RDMA solo está habilitado a través de las aplicaciones que utilizan la interfaz Microsoft Network Direct. Consulte [Acceso a la red RDMA](#access-the-RDMA-network) en este artículo.


Las instancias A10 y A11 tienen un único adaptador de red Ethernet de 10 Gbps que se conecta a servicios de Azure e Internet.

## Consideraciones para la suscripción de Azure

* **Cuenta de azure**: si desea implementar más de un pequeño número de instancias de proceso intensivo, considere la posibilidad de utilizar una suscripción de pago por uso u otras opciones de compra. También puede utilizar su suscripción a MSDN. Consulte [Ventajas de Azure para suscriptores de MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Si utiliza una [evaluación gratuita de Azure](http://azure.microsoft.com/pricing/free-trial/), solo podrá usar un número limitado de núcleos de proceso de Azure.

* **Cuota de núcleos**: es posible que tenga que aumentar la cuota de núcleos de su suscripción de Azure desde el valor predeterminado de 20 núcleos, que no es suficiente para muchos escenarios con instancias de 8 o 16 núcleos. Para las pruebas iniciales, puede considerar la posibilidad de solicitar un aumento de cuota hasta 100 núcleos. Para ello, abra una incidencia de soporte técnico gratuita como se muestra en [Descripción de los límites y aumentos de Azure](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/).

>[AZURE.NOTE]Las cuotas de Azure son límites de crédito, no garantías de capacidad. Solamente se le cobrarán los núcleos que utilice.

* **Grupo de afinidad**: un grupo de afinidad de Azure puede ayudar a optimizar el rendimiento mediante la agrupación de servicios o máquinas virtuales en el mismo centro de datos de Azure. Para agrupar las instancias de proceso intensivo, recomendamos que cree un nuevo grupo de afinidad en una región donde tenga previsto implementar las instancias. Como práctica recomendada, utilice solo el grupo de afinidad para instancias de proceso intensivo, no para instancias de otros tamaños.

* **Red virtual**: no se necesita una red virtual de Azure para usar instancias de proceso intensivo. Sin embargo, puede que necesite al menos una red virtual Azure basada en la nube para muchos escenarios IaaS o una conexión de sitio a sitio si necesita tener acceso a recursos locales. Necesitará crear una nueva red virtual (regional) antes de implementar las instancias. No se admite la adición de una máquina virtual A8, A9, A10 o A11 a una red virtual en un grupo de afinidad. Para obtener más información, consulte [Crear una red Virtual](https://msdn.microsoft.com/library/azure/dn631643.aspx) y [Configurar una red virtual con una conexión VPN de sitio a sitio](../vpn-gateway/vpn-gateway-site-to-site-create.md).

## Consideraciones sobre el uso de HPC Pack

[HPC Pack](https://technet.microsoft.com/library/cc514029), solución gratuita de Microsoft de administración de trabajos y clústeres HPC, no es necesaria para poder utilizar las instancias A8, A9, A10 y A11, pero es una herramienta recomendada para crear clústeres de Windows HPC en Azure. En el caso de las instancias de A8 y A9, HPC Pack es la forma más eficaz de ejecutar aplicaciones de MPI de Windows que tienen acceso a la red RDMA en Azure. HPC Pack incluye un entorno de tiempo de ejecución para la implementación de Microsoft de la interfaz de transferencia de mensajes (MS-MPI) para Windows.

Para que obtener más información y listas de comprobación para utilizar instancias de proceso intensivo con HPC Pack, consulte [Instancias de proceso intensivo A8 y A9: inicio rápido con HPC Pack](https://msdn.microsoft.com/library/azure/dn594431.aspx).

## Acceso a la red RDMA

En un único servicio en la nube, las instancias A8 y A9 pueden tener acceso a la red RDMA de Azure al ejecutar aplicaciones MPI que usan la interfaz Microsoft Network Direct para comunicarse entre instancias. En este momento Network Direct solo es compatible con MS-MPI de Microsoft para Windows. Las instancias A10 y A11 no incluyen el acceso a la red RDMA.

>[AZURE.NOTE]Al igual que otras instancias de Azure, las instancias A8 y A9 pueden ejecutar cargas de trabajo que no sean aplicaciones MPI mediante sus núcleos de CPU disponibles, memoria y espacio en disco. Sin embargo, en estos casos, las instancias no se conectan a la red RDMA y son funcionalmente equivalentes a las instancias A10 y A11.


Consulte la siguiente tabla para obtener información acerca de los requisitos previos para que las aplicaciones de MPI tengan acceso a la red RDMA en las implementaciones de máquina virtual (IaaS) y de servicio en la nube (PaaS) de las instancias A8 o A9. Para obtener información de escenarios de implementación típicos, consulte [Instancias de proceso intensivo A8 y A9: Inicio rápido con HPC Pack](https://msdn.microsoft.com/library/azure/dn594431.aspx).


Requisito previo | Máquinas virtuales (IaaS) | Servicios en la nube (PaaS)
---------- | ------------ | -------------
Sistema operativos | Windows Server 2012 R2 o máquinas virtuales Windows Server 2012 | Windows Server 2012 R2 o familia del sistema operativo invitado Windows Server 2008 R2
MPI | MS-MPI 2012 R2 o versiones posteriores, ya sean independientes o instaladas a través de HPC Pack 2012 R2 o versiones posteriores | MS-MPI 2012 R2 o versiones posteriores, ya sean instaladas a través de HPC Pack 2012 R2 o versiones posteriores


>[AZURE.NOTE]Para escenarios de IaaS, es necesario agregar la [extensión HpcVmDrivers](https://msdn.microsoft.com/library/azure/dn690126.aspx) a las máquinas virtuales para instalar los controladores de Windows necesarios para la conectividad RDMA.


## Aspectos adicionales que debe conocer

* No se puede cambiar el tamaño de una VM de Azure existente al tamaño A8, A9, A10 o A11.

* Las instancias a8, A9, A10 y A11 actualmente no se pueden implementar mediante un servicio en la nube que forma parte de un grupo de afinidad existente. Del mismo modo, no se puede utilizar un grupo de afinidad con un servicio de nube que contiene las instancias A8, A9, A10 y A11 para implementaciones de otros tamaños de instancia. Si intenta realizar estas implementaciones verá un mensaje de error similar a `Azure deployment failure (Compute.OverconstrainedAllocationRequest): The VM size (or combination of VM sizes) required by this deployment cannot be provisioned due to deployment request constraints.`


## Pasos siguientes

* Para obtener más información sobre la disponibilidad y precios de las instancias A8, A9, A10 y A11, consulte [Precios de máquinas virtuales](http://azure.microsoft.com/pricing/details/virtual-machines/) y [Precios de servicios en la nube](http://azure.microsoft.com/pricing/details/cloud-services/).
* Para empezar a implementar y usar instancias A8 y A9 con HPC Pack, consulte [Instancias de proceso intensivo A8 y A9: inicio rápido con HPC Pack](https://msdn.microsoft.com/library/azure/dn594431.aspx) y [Ejecutar aplicaciones de MPI en instancias A8 y A9](https://msdn.microsoft.com/library/azure/dn592104.aspx).
 

<!---HONumber=62-->