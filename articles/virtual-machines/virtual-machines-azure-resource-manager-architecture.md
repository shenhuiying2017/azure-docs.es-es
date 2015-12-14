<properties
   pageTitle="Arquitectura de administrador de recursos de Azure | Microsoft Azure"
   description="Obtenga información acerca de la arquitectura del Administrador de recursos y las relaciones entre el proceso, la red y los proveedores de recursos de almacenamiento."
   services="virtual-machines"
   documentationCenter=""
   authors="davidmu1"
   manager="timlt"
   editor=""
   tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/01/2015"
	ms.author="davidmu"/>

# Arquitectura de administrador de recursos de Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementación clásica.



Este artículo proporciona una visión general de las arquitecturas de administración de servicios y administrador de recursos para crear aplicaciones y cargas de trabajo basadas en infraestructuras.

## Arquitectura para la administración de servicios

Antes de describir la arquitectura del Administrador de recursos de Azure y los distintos proveedores de recursos, revisemos la arquitectura que existe actualmente para la Administración de servicios de Azure. En Administración de servicios de Azure, los recurss de cálculo, almacenamiento o para hospedar máquinas virtuales son proporcionados por:

- Un servicio de nube requerido que actúa como contenedor para hospedar máquinas virtuales (cálculo). Las máquinas virtuales se proporcionan automáticamente con una tarjeta de interfaz de red (NIC) y una dirección IP asignada por Azure. Además, el servicio de nube contiene una instancia de equilibrador de carga externa, una dirección IP pública y extremos predeterminados para permitir un escritorio remoto y tráfico de PowerShell remoto para máquinas virtuales basadas en Windows y tráfico de Secure Shell (SSH) para máquinas virtuales basadas en Linux.
- Una cuenta de almacenamiento necesaria que almacena discos duros virtuales para una máquina virtual, incluido el sistema operativo, los discos de datos temporales y adicionales (almacenamiento).
- Una red virtual opcional que actúa como un contenedor adicional, en el que se puede crear una estructura de subredes y designar la subred en la que se encuentra la máquina virtual (red).

Aquí se encuentran los componentes y sus relaciones para la administración de servicios de Azure.

![](./media/virtual-machines-azure-resource-manager-architecture/arm_arch1.png)

## Arquitectura de administrador de recursos

Para el Administrador de recursos de Azure, los proveedores de recursos admiten los recursos individuales para crear máquinas virtuales que funcionen en la configuración que necesita. Para las máquinas virtuales, hay tres proveedores de recursos principales:

- Proveedor de recursos de proceso (CRP): admite instancias de máquinas virtuales y conjuntos de disponibilidad opcional.
- Proveedor de recursos de almacenamiento (SRP): admite cuentas de almacenamiento requeridas que almacenan los discos duros virtuales para máquinas virtuales, incluido su sistema operativo y discos de datos adicionales.
- Proveedor de recursos de red (NRP): admite NIC requeridos, direcciones IP de máquinas virtuales y subredes de redes virtuales y equilibradores de carga opcionales, direcciones IP de equilibradores de carga y grupos de seguridad de red.

Además, hay relaciones entre los recursos de los proveedores de recursos:

- Una máquina virtual depende de una cuenta de almacenamiento específica definida en el SRP para almacenar sus discos en el almacenamiento de blobs (obligatorio).
- Una máquina virtual hace referencia a una NIC específica definida en el NRP (obligatorio) y un conjunto de disponibilidad definido en el CRP (opcional).
- Una NIC hace referencia a la dirección IP asignada a la máquina virtual (necesaria), la subred de la red virtual para la máquina virtual (necesaria) y a un grupo de seguridad de red (opcional).
- Una subred dentro de una red virtual hace referencia a un grupo de seguridad de red (opcional).
- Una instancia de equilibrador de carga hace referencia al grupo de backend de direcciones IP que incluye la NIC de una máquina virtual (opcional) y hace referencia a una dirección IP pública o privada del equilibrador de carga (opcional).

![](./media/virtual-machines-azure-resource-manager-architecture/arm_arch2.png)

La creación de componentes de recursos permite una mayor flexibilidad al configurar la infraestructura para una carga de trabajo de TI hospedada en Azure. Las plantillas de Administrador de recursos de Azure sacan partido de esta flexibilidad para crear el conjunto de recursos dependientes necesarios para una configuración concreta. Al ejecutar una plantilla, el Administrador de recursos garantiza que los recursos de una configuración se creen en el orden correcto para conservar las dependencias y las referencias. Por ejemplo, el Administrador de recursos no creará la NIC para una máquina virtual hasta que se haya creado la red virtual con una subred y una dirección IP (el grupo de seguridad de red es opcional).

Un grupo de recursos es un contenedor lógico que contiene los recursos relacionados para una aplicación, que puede constar de varias máquinas virtuales, NIC, direcciones IP, equilibradores de carga, subredes y grupos de seguridad de red. Por ejemplo, puede administrar todos los recursos de la aplicación como una unidad única de administración. Puede crear, actualizar y eliminar todos juntos. A continuación se facilita una aplicación de ejemplo implementada en un único grupo de recursos.

![](./media/virtual-machines-azure-resource-manager-architecture/arm_arch3.png)

La aplicación consta de:

- Dos máquinas virtuales que usan la misma cuenta de almacenamiento, se encuentran en el mismo conjunto de disponibilidad y en la misma subred de una red virtual.
- Una sola NIC y dirección IP de máquina virtual para cada máquina virtual.
- Un equilibrador de carga externo que distribuye el tráfico de Internet a las NIC de las dos máquinas virtuales.

Todos estos recursos de esta aplicación se administran a través del grupo de recursos único que los contiene.

También puede ver la creación de componentes y relaciones de dependencia entre recursos cuando se crea una máquina virtual basada en administrador de recursos con Azure PowerShell o la CLI de Azure. Para poder ejecutar el comando que crea la máquina virtual, debe crear un grupo de recursos, una cuenta de almacenamiento, una red virtual con una subred y una NIC con una dirección IP. Para obtener más información, consulte [Creación de una máquina virtual de Windows con el Administrador de recursos de Azure y PowerShell](virtual-machines-ps-create-preconfigure-windows-resource-manager-vms.md)

## Pasos siguientes

[Implementación y administración de máquinas virtuales con plantillas del Administrador de recursos de Azure y CLI de Azure](virtual-machines-deploy-rmtemplates-azure-cli.md)

[Implementación y administración de Máquinas virtuales de Azure mediante el Administrador de recursos de plantillas y PowerShell](virtual-machines-deploy-rmtemplates-powershell.md)

## Recursos adicionales

[Proceso, red y proveedores de almacenamiento de Azure en el Administrador de recursos de Azure](virtual-machines-azurerm-versus-azuresm.md)

[Información general del Administrador de recursos de Azure](resource-group-overview.md)

<!---HONumber=AcomDC_1203_2015-->