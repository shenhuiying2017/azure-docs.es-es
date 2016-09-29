<properties
	pageTitle="Directrices de nomenclatura de la infraestructura | Microsoft Azure"
	description="Obtenga información sobre las directrices clave de diseño e implementación para asignar nombres a los servicios de infraestructura de Azure."
	documentationCenter=""
	services="virtual-machines-windows"
	authors="iainfoulds"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/08/2016"
	ms.author="iainfou"/>

# Infrastructure naming guidelines (Directrices de nomenclatura de la infraestructura)

[AZURE.INCLUDE [virtual-machines-windows-infrastructure-guidelines-intro](../../includes/virtual-machines-windows-infrastructure-guidelines-intro.md)]

Este artículo se centra en describir cómo enfocar convenciones de nomenclatura para todos sus diversos recursos de Azure a fin de crear un conjunto de recursos lógico y fácilmente identificable en su entorno.

## Directrices de implementación para las convenciones de nomenclatura

Decisiones:

- ¿Cuáles son sus convenciones de nomenclatura para los recursos de Azure?

Tareas:

- Defina los afijos para usar en sus recursos para mantener la coherencia.
- Defina los nombres de cuentas de almacenamiento una vez especificado el requisito para que sean únicos globalmente.
- Documente la convención de nomenclatura que se va a usar y distribúyala a todas las partes implicadas para garantizar la coherencia entre las implementaciones.

## Convenciones de nomenclatura

Debe existir una buena convención de nomenclatura antes de crear cualquier elemento en Azure. Una convención de nomenclatura garantiza que todos los recursos tienen un nombre predecible, lo que ayuda a reducir la carga administrativa asociada a la administración de esos recursos.

Puede seguir un conjunto específico de convenciones de nomenclatura definidas para toda la organización, o bien para una suscripción o cuenta específica de Azure. Aunque es fácil para las personas de una organización establecer reglas implícitas al trabajar con recursos de Azure, cuando un equipo necesita trabajar en un proyecto en Azure ese modelo no se adapta bien.

Debe acordar por adelantado el conjunto de convenciones de nomenclatura. Existen algunas consideraciones sobre las convenciones de nomenclatura que trascienden los conjuntos de reglas.

## Afijos

A medida que se dispone a definir una convención de nomenclatura, se toma una decisión sobre si el afijo estará:

- Al principio del nombre (prefijo)
- Al final del nombre (sufijo)

Por ejemplo, a continuación se muestran dos nombres posibles para un grupo de recursos que usa el afijo `rg`:

- Rg-WebApp (prefijo)
- WebApp-Rg (sufijo)

Los afijos pueden hacer referencia a distintos aspectos que describen los recursos concretos. En la tabla siguiente se muestran algunos ejemplos que suelen usarse.

| Aspecto | Ejemplos | Notas |
|:-------------------------------------|:-----------------------------------------------------------------------|:-----------------------------------------------------------------------------------------------------------|
| Environment | dev, stg, prod | Según el propósito y el nombre de cada entorno. |
| Ubicación | usw (Oeste de EE. UU.), use (Este de EE. UU. 2) | Según la región del centro de datos o la región de la organización. |
| Componente, servicio o producto de Azure | Rg para grupo de recursos, VNet para red virtual | Según el producto para el que proporciona compatibilidad el recurso. |
| Rol | sql, ora, sp, iis | Según el rol de la máquina virtual. |
| Instance | 01, 02, 03, etc. | Para los recursos que pueden tener más de una instancia. Por ejemplo, servidores web de carga equilibrada en un servicio en la nube. |


Al establecer las convenciones de nomenclatura, asegúrese de que indiquen claramente qué afijos se deben usar para cada tipo de recurso y en qué posición (prefijo o sufijo).

## Fechas

A menudo es importante determinar la fecha de creación a partir del nombre de un recurso. Se recomienda el formato de fecha AAAAMMDD. Este formato garantiza no solo que se registre la fecha completa, sino que dos recursos cuyos nombres difieren solo en la fecha se ordenen alfabética y cronológicamente al mismo tiempo.

## Asignación de nombres a recursos

Defina cada tipo de recurso en la convención de nomenclatura, que debería tener reglas que definan cómo asignar nombres a cada recurso que se cree. Estas reglas se deben aplicar a todos los tipos de recursos, por ejemplo:

- Suscripciones
- Cuentas
- Cuentas de almacenamiento
- Redes virtuales
- Subredes
- Conjuntos de disponibilidad
- Grupos de recursos
- Máquinas virtuales
- Extremos
- Grupos de seguridad de red
- Roles

Para asegurarse de que el nombre proporciona información suficiente para determinar el recurso al que hace referencia, es preciso usar nombres descriptivos.

## Nombres de equipo

Al crear una máquina virtual (VM), Microsoft Azure requiere un nombre de máquina virtual de hasta 15 caracteres que se usa para el nombre de recurso. Azure usará el mismo nombre para el sistema operativo instalado en la máquina virtual. Sin embargo, estos nombres podrían no ser siempre iguales.

En los casos en que una máquina virtual se crea a partir de un archivo de imagen .vhd que ya contiene un sistema operativo, el nombre de la máquina virtual en Azure puede diferir del nombre de equipo del sistema operativo de la máquina virtual. Esta situación se desaconseja, ya que puede agregar cierta dificultad a la administración de máquinas virtuales. Asigne al recurso de la máquina virtual de Azure el mismo nombre que el del equipo que asigne al sistema operativo de dicha máquina virtual.

Se recomienda que el nombre de la máquina virtual de Azure sea el mismo que el del equipo del sistema operativo subyacente.

## Nombres de cuentas de almacenamiento

Las cuentas de almacenamiento tienen reglas especiales que regulan sus nombres. Sólo puede utilizar minúsculas y números. Para obtener más información, vea [Creación de una cuenta de almacenamiento](../storage/storage-create-storage-account.md#create-a-storage-account). Además, el nombre de la cuenta de almacenamiento, en combinación con core.windows.net, debe ser un nombre DNS único y válido globalmente. Por ejemplo, si la cuenta de almacenamiento se llama mystorageaccount, los siguientes nombres DNS deben ser únicos:

- mystorageaccount.blob.core.windows.net
- mystorageaccount.table.core.windows.net
- mystorageaccount.queue.core.windows.net


## Pasos siguientes
[AZURE.INCLUDE [virtual-machines-windows-infrastructure-guidelines-next-steps](../../includes/virtual-machines-windows-infrastructure-guidelines-next-steps.md)]

<!---HONumber=AcomDC_0914_2016-->