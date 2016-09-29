<properties
	pageTitle="Directrices para máquinas virtuales Linux | Microsoft Azure"
	description="Obtenga información sobre las directrices clave de diseño e implementación para implementar máquinas virtuales Linux en Azure."
	documentationCenter=""
	services="virtual-machines-linux"
	authors="iainfoulds"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/08/2016"
	ms.author="iainfou"/>

# Directrices para máquinas virtuales

[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-intro](../../includes/virtual-machines-linux-infrastructure-guidelines-intro.md)]

Este artículo se centra en describir los pasos de planeamiento necesarios para crear y administrar máquinas virtuales (VM) en su entorno de Azure.

## Directrices de implementación para las máquinas virtuales
Decisiones:

- ¿Cuántas máquinas virtuales necesita para sus diversos niveles de aplicación y los componentes de su infraestructura?
- ¿Qué recursos de CPU y memoria necesita cada máquina virtual y cuáles son los requisitos de almacenamiento?

Tareas:

- Defina las cargas de trabajo de la aplicación y los recursos que requerirá la máquina virtual.
- Alinee las demandas de recursos de cada máquina virtual con el tipo de almacenamiento y el tamaño de VM adecuados.
- Defina sus grupos de recursos para los diferentes niveles y los componentes de su infraestructura.
- Defina la convención de nomenclatura de la máquina virtual.
- Cree sus máquinas virtuales con la CLI de Azure, el portal web o plantillas de Resource Manager.

## Máquinas virtuales

Es probable que uno de los componentes principales de su entorno de Azure sean las máquinas virtuales. Aquí es donde ejecutará sus aplicaciones, bases de datos, servicios de autenticación, etc.

Es importante conocer los [diferentes tamaños de máquina virtual](virtual-machines-linux-sizes.md) para cambiar correctamente el tamaño de su entorno desde una perspectiva de costos y rendimiento. Si sus máquinas virtuales no tienen una cantidad adecuada de núcleos de CPU o memoria, el rendimiento de su aplicación se verá afectado independientemente de su diseño y desarrollo. Revise las cargas de trabajo sugeridas para cada serie de máquinas virtuales como punto de partida al decidir el tamaño de VM que debe usar para cada componente de su infraestructura. Puede [cambiar el tamaño de una máquina virtual](virtual-machines-linux-change-vm-size.md) después de la implementación.

El almacenamiento desempeña un papel clave en el rendimiento de la máquina virtual. Puede usar Standard Storage que utilizan los discos giratorios habituales, o bien Premium Storage para altas cargas de trabajo de E/S y el máximo rendimiento que utilizan los discos SSD. Al igual que ocurre con el tamaño de VM, existen consideraciones sobre el costo en lo referente a la selección del medio de almacenamiento. Puede leer el [artículo de las directrices de infraestructura de almacenamiento](virtual-machines-linux-infrastructure-storage-solutions-guidelines.md) para saber cómo diseñar el almacenamiento adecuado para que el rendimiento de sus máquinas virtuales sea óptimo.


## Grupos de recursos
Componentes como las máquinas virtuales se agrupan de forma lógica para facilitar la administración y el mantenimiento mediante los [grupos de recursos de Azure](../resource-group-overview.md). Al usar los grupos de recursos, puede crear, administrar y supervisar todos los recursos que conforman una aplicación determinada. También puede implementar [controles de acceso basados en rol](../active-directory/role-based-access-control-what-is.md) para conceder acceso a otros usuarios de su equipo solo a los recursos que necesiten. Tómese su tiempo para planear sus grupos de recursos y asignaciones de roles. Existen distintos enfoques para diseñar e implementar realmente grupos de recursos, de modo que asegúrese de leer el [artículo de las directrices de grupos de recursos](virtual-machines-linux-infrastructure-resource-groups-guidelines.md) para saber cuál es la mejor forma de crear sus máquinas virtuales.


## Plantillas 
Puede crear plantillas, definidas por archivos JSON declarativos, para crear sus máquinas virtuales. Normalmente, las plantillas también crearán el almacenamiento, las redes, las interfaces de red o el direccionamiento IP necesarios junto con las propias máquinas virtuales. Puede usar plantillas para crear entornos coherentes y reproducibles con fines de desarrollo y prueba para replicar fácilmente entornos de producción y viceversa. Puede leer más sobre cómo [crear y usar plantillas](../resource-group-overview.md#template-deployment) a fin de aprender a usarlas para crear e implementar sus máquinas virtuales.


## Pasos siguientes
[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-next-steps](../../includes/virtual-machines-linux-infrastructure-guidelines-next-steps.md)]

<!---HONumber=AcomDC_0914_2016-->