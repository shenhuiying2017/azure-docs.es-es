---
title: "Introducción a las máquinas virtuales Linux en Azure | Microsoft Docs"
description: "Describe los servicios de proceso, almacenamiento y red de Azure con máquinas virtuales de Linux."
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: rickstercdn
manager: timlt
editor: 
ms.assetid: 7965a80f-ea24-4cc2-bc43-60b574101902
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: overview
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/14/2016
ms.author: rclaus
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: d0c6523b4d90c29781d256b2882b0935b50e2af9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-and-linux"></a>Azure y Linux
Microsoft Azure es una colección cada vez mayor de servicios en la nube, públicos e integrados, que incluyen servicios de análisis, máquinas virtuales, bases de datos, móviles, de red, de almacenamiento y web, ideales para hospedar sus soluciones.  Microsoft Azure proporciona una plataforma informática escalable que permite pagar solo por lo que use, cuando lo desee, sin tener que invertir en hardware local.  Azure está listo cuando tenga que escalar sus soluciones vertical y horizontalmente a cualquier escala que necesite para atender las necesidades de sus clientes.

Si está familiarizado con las distintas características de AWS de Amazon, puede examinar el [documento de asignación de definiciones](https://azure.microsoft.com/campaigns/azure-vs-aws/mapping/)donde se comparan Azure y AWS.

## <a name="regions"></a>Regiones
Los recursos de Microsoft Azure se distribuyen en diversas regiones geográficas de todo el mundo.  Un "region" representa varios centros de datos en una única área geográfica.  Tenemos 34 regiones con disponibilidad general en todo el mundo con 4 regiones anunciadas adicionales. Como seguimos expandiendo nuestra cobertura global, mantenemos una lista actualizada de regiones existentes y anunciadas recientemente.

* [Regiones de Azure](https://azure.microsoft.com/regions/)

## <a name="availability"></a>Disponibilidad
Se anunció un Acuerdo de Nivel de Servicio líder de la industria de máquinas virtuales de una sola instancia del 99,9 % siempre y cuando la máquina virtual se implemente con Premium Storage en todos los discos.  Para que su implementación pueda optar a nuestro Acuerdo de Nivel de Servicio estándar de máquina virtual del 99,95 %, debe implementar dos o más máquinas virtuales que ejecuten la carga de trabajo dentro de un conjunto de disponibilidad. Esto garantizará que las máquinas virtuales estén distribuidas en varios dominios de error en nuestros centros de datos e implementadas en hosts con diferentes tiempos de mantenimiento. En el [SLA de Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_0/) completo se explica la disponibilidad garantizada de Azure como un conjunto.

## <a name="managed-disks"></a>Managed Disks

El servicio Managed Disks controla la creación y administración de las cuentas de almacenamiento de Azure Storage en segundo plano y se asegura de que no tenga que preocuparse de los límites de escalabilidad de la cuenta de almacenamiento. Simplemente especifique el tamaño del disco y el nivel de rendimiento (Estándar o Premium), y Azure crea y administra el disco por usted. Ni siquiera tendrá que preocuparse por el almacenamiento que se va a usar a medida que agrega discos o escala y reduce verticalmente la máquina virtual. Si está creando nuevas máquinas virtuales, [use la CLI de Azure 2.0](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) o Azure Portal para crear máquinas virtuales con sistema operativo administrado y discos de datos. Si tiene máquinas virtuales con discos no administrados, puede [convertir las máquinas virtuales para realizar copias de seguridad con Managed Disks](convert-unmanaged-to-managed-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

También puede administrar sus imágenes personalizadas en una cuenta de almacenamiento por región de Azure y utilizarlas para crear cientos de máquinas virtuales en la misma suscripción. Para obtener más información sobre Managed Disks, consulte [Información general de Managed Disks](../windows/managed-disks-overview.md).

## <a name="azure-virtual-machines--instances"></a>Máquinas virtuales e instancias de Azure
Microsoft Azure permite ejecutar varias de las distribuciones de Linux más populares proporcionadas y mantenidas por diversos asociados.  Encontrará distribuciones como Red Hat Enterprise, CentOS, Debian, Ubuntu, CoreOS, RancherOS y FreeBSD, entre otras, en Azure Marketplace. Trabajamos activamente con distintas comunidades de Linux para agregar aún más tipos a la lista de [distribuciones de Linux aprobadas para Azure](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) .

Si su distribución de Linux favorita no está en la galería, puede usar su propia máquina virtual Linux [creando y actualizando un VHD de Linux en Azure](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Las máquinas virtuales de Azure permiten implementar una amplia gama de soluciones informáticas con agilidad. Puede implementar prácticamente cualquier carga de trabajo y cualquier lenguaje en prácticamente cualquier sistema operativo, ya sea Windows, Linux o uno creado de forma personalizada por alguno de los que integran nuestra lista cada vez mayor de asociados. ¿Todavía no ve lo que busca?  No se preocupe, también puede traer sus propias imágenes desde el entorno local.

## <a name="vm-sizes"></a>Tamaños de máquina virtual
Cuando se implementa una máquina virtual en Azure, se selecciona un tamaño de máquina virtual de nuestras series de tamaños que sea adecuado para la carga de trabajo. El tamaño también afecta a la capacidad de procesamiento, memoria y almacenamiento de la máquina virtual. Se le facturará según la cantidad de tiempo que la máquina virtual se esté ejecutando y consumiendo los recursos asignados. Consulte una lista completa de [tamaños de máquina virtual](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Estas son algunas directrices básicas para seleccionar un tamaño de máquina virtual desde una de nuestras series (A, D, DS, G y GS).
* Las máquinas virtuales de la serie A son las más económicas para empezar con cargas de trabajo ligeras y escenarios de desarrollo y pruebas. Están ampliamente disponibles en todas las regiones y puede conectarse a todos los recursos estándar disponibles para máquinas virtuales, y usarlos.
* Los tamaños de la serie A (A8 - A11) son configuraciones especiales para procesos intensivos adecuadas para aplicaciones de clúster de proceso de alto rendimiento.
* Las máquinas virtuales de la serie D están diseñadas para ejecutar aplicaciones que exigen mayor capacidad de proceso y rendimiento de disco temporal. Las máquinas virtuales de la serie D proporcionan procesadores más rápidos, una mayor proporción de memoria a núcleo y una unidad de estado sólido (SSD) para el disco temporal.
* La serie Dv2 es la versión más reciente de la serie D y cuenta con una CPU más eficaz. La CPU de la serie Dv2 es un 35 % aproximadamente más rápida que la CPU de la serie D. Se basa en el procesador Intel Xeon® E5-2673 v3 (Haskwell) de 2,4 GHz de última generación; y con Intel Turbo Boost Technology 2.0, puede alcanzar los 3,2 GHz. La serie Dv2 tiene las mismas configuraciones de disco y memoria que la serie D.
* Las máquinas virtuales de la serie G ofrecen la mayor cantidad de memoria y se ejecutan en hosts con procesadores de la familia Intel Xeon E5 V3.

Nota: Las máquinas virtuales de las series DS y GS tienen acceso a Premium Storage, nuestro almacenamiento de alto rendimiento, baja latencia y respaldado por SSD para cargas de trabajo con uso intensivo de E/S. Premium Storage está disponible en determinadas regiones. Para obtener información, consulte:

* [Premium Storage: almacenamiento de alto rendimiento para cargas de trabajo de máquina virtual de Azure](../../storage/common/storage-premium-storage.md)

## <a name="automation"></a>Automatización
Para instaurar una cultura de DevOps adecuada, la infraestructura al completa debe ser código.  Cuando toda la infraestructura reside en el código, puede volver a crearse con facilidad (servidores de Phoenix).  Azure funciona con todas las principales herramientas de automatización, como Ansible, Chef, SaltStack y Puppet.  Asimismo, tiene sus propias herramientas de automatización:

* [Plantillas de Azure](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [VMAccess de Azure](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Azure está implementando la compatibilidad con [cloud-init](http://cloud-init.io/) en la mayoría de las distribuciones de Linux que admiten este paquete.  En estos momentos, las máquinas virtuales Ubuntu de Canonical se implementan con cloud-init habilitado de forma predeterminada.  Fedora, CentOS y RHEL de Red Hat admiten cloud-init; sin embargo, las imágenes de Azure que mantiene Red Hat no tienen instalado este paquete.  Para usar cloud-init en un sistema operativo de la familia Red Hat, debe crear una imagen personalizada con cloud-init instalado.

* [Uso de cloud-init en máquinas virtuales con Linux](using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="quotas"></a>Cuotas
Cada suscripción de Azure tiene límites de cuota predeterminados que pueden afectar a la implementación de un gran número de máquinas virtuales en su proyecto. El límite actual por suscripción es 20 máquinas virtuales por región.  Los límites de cuota se pueden elevar rápida y fácilmente presentando una incidencia de soporte técnico solicitando un aumento del límite.  Más información sobre los límites de cuota:

* [Límites de servicio de suscripción de Azure](../../azure-subscription-service-limits.md)

## <a name="partners"></a>Asociados
En Microsoft trabajamos estrechamente con nuestros asociados para garantizar que las imágenes disponibles están actualizadas y optimizadas para los entornos de tiempo de ejecución de Azure.  Para obtener más información sobre nuestros asociados, visite las siguientes páginas de Marketplace.

* Linux en Azure: [distribuciones aprobadas](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* SUSE - [Azure Marketplace - SUSE Linux Enterprise Server](https://azuremarketplace.microsoft.com/en-us/marketplace/apps?search=%27SUSE%27)
* Red Hat: [Azure Marketplace - RedHat Enterprise Linux 7.2](https://azure.microsoft.com/marketplace/partners/redhat/redhatenterpriselinux72/)
* Canonical: [Azure Marketplace - Ubuntu Server 16.04 LTS](https://azure.microsoft.com/marketplace/partners/canonical/ubuntuserver1604lts/)
* Debian: [Azure Marketplace - Debian 8 "Jessie"](https://azure.microsoft.com/marketplace/partners/credativ/debian8/)
* FreeBSD: [Azure Marketplace - FreeBSD 10.3](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd103/)
* CoreOS: [Azure Marketplace - CoreOS (Stable)](https://azure.microsoft.com/marketplace/partners/coreos/coreosstable/)
* RancherOS: [Azure Marketplace - RancherOS](https://azure.microsoft.com/marketplace/partners/rancher/rancheros/)
* Bitnami: [Bitnami Library for Azure](https://azure.bitnami.com/)
* Mesosphere: [Azure Marketplace - Mesosphere DC/OS on Azure](https://azure.microsoft.com/marketplace/partners/mesosphere/dcosdcos/)
* Docker: [Azure Marketplace - Azure Container Service with Docker Swarm](https://azure.microsoft.com/marketplace/partners/microsoft/acsswarms/)
* Jenkins: [Azure Marketplace - CloudBees Jenkins Platform](https://azure.microsoft.com/marketplace/partners/cloudbees/jenkins-platformjenkins-platform/)

## <a name="getting-started-with-linux-on-azure"></a>Introducción a Linux en Azure
Para empezar a usar Azure, necesita una cuenta de Azure, la CLI de Azure instalada y un par de claves SSH públicas y privadas.

### <a name="sign-up-for-an-account"></a>Registro para obtener una cuenta
El primer paso para usar la nube de Azure es suscribirse para obtener una cuenta de Azure.  Para empezar, vaya a la página de [registro para obtener cuentas de Azure](https://azure.microsoft.com/pricing/free-trial/) .

### <a name="install-the-cli"></a>Instalación de la CLI de Azure
Con la nueva cuenta de Azure, puede comenzar inmediatamente a usar Azure Portal, que se trata de un panel de administración web.  Para administrar la nube de Azure a través de la línea de comandos, instale `azure-cli`.  Instale la [CLI de Azure 2.0](/cli/azure/install-azure-cli) en su estación de trabajo Mac o Linux.

### <a name="create-an-ssh-key-pair"></a>Creación de un par de claves SSH
Ya dispone de una cuenta de Azure, del portal web de Azure y de la CLI de Azure.  El siguiente paso consiste en crear un par de claves SSH que se utiliza para acceder mediante SSH a Linux sin utilizar una contraseña.  [Cree claves SSH en Linux y Mac](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para habilitar inicios de sesión sin contraseñas y disfrutar de una mayor seguridad.

### <a name="create-a-vm-using-the-cli"></a>Creación de una máquina virtual con la CLI
Una forma rápida de implementar una máquina virtual sin abandonar el terminal en el que está trabajando es crear una máquina virtual Linux.  Toda la información que se puede especificar en el portal web está disponible a través de un indicador o conmutador de línea de comandos.  

* [Creación de una máquina virtual con Linux mediante la CLI](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="create-a-vm-in-the-portal"></a>Crear una máquina virtual en el portal
Una forma sencilla de crear una máquina virtual Linux en el portal web de Azure es seleccionar las diversas opciones de implementación y hacer clic en ellas.  En lugar de utilizar marcadores o conmutadores de línea de comandos, puede ver un diseño web agradable con varias opciones y configuraciones.  Todos los elementos disponibles a través de la interfaz de línea de comandos también se encuentran en el portal.

* [Creación de una máquina virtual Linux mediante el Portal](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="login-using-ssh-without-a-password"></a>Inicio de sesión mediante SSH sin una contraseña
Ahora, la máquina virtual se está ejecutando en Azure y ya puede iniciar sesión en ella.  Usar contraseñas para iniciar sesión a través de SSH no es un proceso rápido ni seguro.  La forma más segura y rápida de iniciar sesión es utilizar claves SSH.  Al crear una máquina virtual Linux mediante el portal o la CLI, tiene dos opciones de autenticación.  Si elige una contraseña para SSH, Azure configurará la máquina virtual para permitir los inicios de sesión mediante contraseñas.  Si decide utilizar una clave pública SSH, Azure configurará la máquina virtual para permitir que solo se pueda iniciar sesión a través de claves SSH y deshabilitará los inicios de sesión con contraseñas. Para proteger la máquina virtual Linux al permitir que solo se inicie sesión con claves de SSH, utilice la opción de claves públicas SSH durante la creación de las máquinas virtuales en el portal o la CLI.

## <a name="related-azure-components"></a>Componentes de Azure relacionados
## <a name="storage"></a>Almacenamiento
* [Introducción a Microsoft Azure Storage](../../storage/common/storage-introduction.md)
* [Incorporación de un disco a una máquina virtual con Linux mediante la CLI de Azure](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Incorporación de un disco de datos a una máquina virtual con Linux en Azure Portal](attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="networking"></a>Redes
* [Información general sobre redes virtuales](../../virtual-network/virtual-networks-overview.md)
* [Direcciones IP en Azure](../../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* [Apertura de puertos para una máquina virtual con Linux en Azure](nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Creación de un nombre de dominio completo en Azure Portal](portal-create-fqdn.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="containers"></a>Contenedores
* [Máquinas virtuales y contenedores de Azure](containers.md)
* [Presentación del servicio Contenedor de Azure](../../container-service/container-service-intro.md)
* [Implementación de un clúster del servicio Contenedor de Azure](../../container-service/dcos-swarm/container-service-deployment.md)

## <a name="next-steps"></a>Pasos siguientes
Ya tiene una visión general de Linux en Azure.  El siguiente paso consiste en empezar a crear algunas máquinas virtuales.

* [Explore nuestra creciente lista de scripts de ejemplo para tareas comunes mediante la CLI de Azure](cli-samples.md)
