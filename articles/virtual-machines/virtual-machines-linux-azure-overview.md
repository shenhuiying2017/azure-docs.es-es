 <properties
   pageTitle="Azure y Linux | Microsoft Azure"
   description="Describe los servicios de proceso, almacenamiento y red de Azure con máquinas virtuales de Linux."
   services="virtual-machines-linux"
   documentationCenter="virtual-machines-linux"
   authors="rickstercdn"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="02/01/2016"
   ms.author="rclaus"/>

# Azure y Linux
Microsoft Azure es una colección cada vez mayor de servicios en la nube, públicos e integrados, que incluyen servicios de análisis, Máquinas virtuales, de bases de datos, móviles, de red, de almacenamiento y web, ideales para hospedar sus soluciones. Microsoft Azure proporciona una plataforma informática escalable que permite pagar solo por lo que use, cuando lo desee, sin tener que invertir en hardware local. Azure está listo cuando tenga que escalar sus soluciones vertical y horizontalmente a cualquier escala que necesite para atender las necesidades de sus clientes.
 
## Máquinas virtuales de Azure
Máquinas virtuales de Azure permite implementar una amplia gama de soluciones informáticas con agilidad. Implemente una máquina virtual de Windows o Linux desde la galería de imágenes o una máquina virtual personalizada creada por uno de los asociados de nuestra creciente lista. Puede implementar prácticamente cualquier carga de trabajo y cualquier lenguaje en casi cualquier sistema operativo. ¿Todavía no ve lo que busca? No se preocupe, también puede traer sus propias imágenes desde el entorno local.
 
## Introducción a Linux en Microsoft Azure

Use Máquinas virtuales, Almacenamiento y Redes de Microsoft Azure para disponer de "infraestructura como servicio" en Internet para su necesidades informáticas de Linux. Para empezar a trabajar con Linux en Azure, va a necesitar:

1. Una cuenta de evaluación gratuita. [Obtener una](https://azure.microsoft.com/pricing/free-trial/).
2. La interfaz de la línea de comandos de Azure para Linux, Mac y Windows (la CLI de Azure). [Instalar](../xplat-cli-install.md).
3. Una máquina virtual de Linux. [Crearla](virtual-machines-linux-quick-create-cli.md).
4. Obtenga más información acerca de Linux y Azure, incluido cómo optar al contrato de nivel de servicio (SLA). **Lea este documento, aunque no le gusten nada los documentos legales**.

## Logística: regiones, distribuciones, disponibilidad, tamaños de máquinas virtuales y cuotas
### Regiones
Los recursos de Microsoft Azure se distribuyen en diversas regiones geográficas de todo el mundo. Un "region" representa varios centros de datos en una única área geográfica. A partir del 1 de enero de 2016, esto incluye: 8 en América, 2 en Europa, 6 en Asia Pacífico, 2 en China continental y 3 en India. Si lo desea, puede consultar la lista completa de todas las regiones de Azure, existentes y de próxima incorporación, **[aquí](https://azure.microsoft.com/regions/)**.

### Distribuciones
Microsoft Azure permite ejecutar varias de las distribuciones de Linux más populares proporcionadas y mantenidas por diversos asociados. Encontrará distribuciones como CentOS, Debian, Red Hat Enterprise, Ubuntu, FreeBSD, entre otras, en Azure Marketplace. Trabajamos activamente con distintas comunidades de Linux para agregar aun más tipos a la lista de distribuciones aprobadas. **[Consulte las distribuciones actuales](virtual-machines-linux-endorsed-distros.md)** Si su distribución de Linux favorita no está en la galería, siga las instrucciones de **[esta página](virtual-machines-linux-create-upload-generic.md)** para "traer su propia máquina virtual de Linux".

## Disponibilidad y el SLA de Microsoft Azure
Para que su implementación pueda optar a nuestro contrato de nivel de servicio de máquina virtual 99,95, debe implementar dos o más máquinas virtuales que ejecuten la carga de trabajo dentro de un conjunto de disponibilidad. Esto garantizará que las máquinas virtuales estén distribuidas en varios dominios de error en nuestros centros de datos e implementadas en hosts con diferentes tiempos de mantenimiento. Para obtener información completa sobre nuestro SLA, consúltelo **[aquí en línea](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_0/)**.

## Tamaños de máquina virtual y cuotas
Cuando se implementa una máquina virtual en Azure, se selecciona un tamaño de máquina virtual, dentro de nuestras series de tamaños, que sea adecuada para la carga de trabajo. El tamaño también afecta a la capacidad de procesamiento, memoria y almacenamiento de la máquina virtual. Se le facturará según la cantidad de tiempo que la máquina virtual se esté ejecutando y consumiendo los recursos asignados. Para obtener una lista completa, consulte el siguiente artículo en [Sizes for Linux virtual machines in Azure](virtual-machines-linux-sizes.md) (Tamaños de máquinas virtuales de Linux en Azure).

Estas son algunas directrices básicas para seleccionar un tamaño de máquina virtual desde una de nuestras series (A, D, DS, G y GS).

* Las máquinas virtuales de la serie A son las más económicas para empezar con cargas de trabajo ligeras y escenarios de desarrollo y pruebas. Están ampliamente disponibles en todas las regiones y puede conectarse a todos los recursos estándar disponibles para máquinas virtuales, y usarlos.
* Los tamaños de la serie A (A8 - A11) son configuraciones especiales para procesos intensivos adecuadas para aplicaciones de clúster de proceso de alto rendimiento.
* Las máquinas virtuales de la serie D están diseñadas para ejecutar aplicaciones que exigen mayor capacidad de proceso y rendimiento de disco temporal. Las máquinas virtuales de la serie D proporcionan procesadores más rápidos, una mayor proporción de memoria a núcleo y una unidad de estado sólido (SSD) para el disco temporal. 
* La serie Dv2 es la versión más reciente de la serie D y cuenta con una CPU más eficaz. La CPU de la serie Dv2 es un 35 % aproximadamente más rápida que la CPU de la serie D. Se basa en el procesador Intel Xeon® E5-2673 v3 (Haswell) de 2,4 GHz de la última generación; y con Intel Turbo Boost Technology 2.0, puede alcanzar los 3,2 GHz. La serie Dv2 tiene las mismas configuraciones de disco y memoria que la serie D.
* Las máquinas virtuales de la serie G ofrecen la mayor cantidad de memoria y se ejecutan en hosts con procesadores de la familia Intel Xeon E5 V3.

Nota: Las máquinas virtuales de las series DS y GS tienen acceso a Almacenamiento premium, nuestro almacenamiento de alto rendimiento, baja latencia y respaldado por SSD para cargas de trabajo con uso intensivo de E/S. Almacenamiento premium está disponible en determinadas regiones. Para más información, consulte **[Almacenamiento Premium: Almacenamiento de alto rendimiento para cargas de trabajo de máquina virtual de Azure](../storage/storage-premium-storage-preview-portal.md)**.

Cada suscripción de Azure tiene límites de cuota predeterminados que pueden afectar a la implementación de un gran número de máquinas virtuales en su proyecto. El límite actual por suscripción es 20 máquinas virtuales por región. Para aumentar estos límites de cuota, envíe una incidencia de soporte técnico y solicite un aumento del límite. Para más información sobre los límites de cuota, consulte **[Límites, cuotas y restricciones de suscripción y servicios de Microsoft Azure](../azure-subscription-service-limits.md)**.

## Pasos siguientes

Una cuenta de evaluación gratuita. **[Obtenga una.](https://azure.microsoft.com/pricing/free-trial/)** Si ya tiene una, **[instale la CLI de Azure.](../xplat-cli-install.md)** para probarla. Si ya lo ha hecho, [vaya a crear la máquina virtual de Linux ahora](virtual-machines-linux-quick-create-cli.md).

<!---HONumber=AcomDC_0323_2016-->