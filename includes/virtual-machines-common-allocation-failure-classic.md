---
title: archivo de inclusión
description: archivo de inclusión
services: virtual-machines-windows, azure-resource-manager
author: genlin
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 04/14/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: f403e060859df6d1de96a3c0d478d57df2677eee
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/18/2018
ms.locfileid: "31531062"
---
A continuación se presentan los escenarios de asignación comunes que ocasionan que una solicitud de asignación quede anclada. Nos dedicaremos a cada escenario más adelante en este artículo.

- Cambio del tamaño de una VM o incorporación de VM o instancias de rol a un servicio en la nube existente.
- Reinicio de las VM detenidas (desasignadas) parcialmente.
- Reinicio de las VM detenidas (desasignadas) completamente.
- Implementaciones de ensayo o producción (solo plataforma como servicio).
- Grupo de afinidad (proximidad de la VM o el servicio).
- Red virtual basada en el grupo de afinidad

Cuando reciba un error de asignación, compruebe si alguno de los escenarios indicados se aplica en su caso. Use el error de asignación que devuelve la plataforma de Azure para identificar el escenario correspondiente. Si la solicitud está anclada, quite algunas de las restricciones de anclaje para abrir su solicitud a más clústeres y aumente así la posibilidad de que la asignación se realice correctamente.
En general, si el error no indica que "el tamaño de VM solicitado no se admite", siempre puede volver a intentarlo posteriormente. Esto se debe a que pueden haberse liberado recursos suficientes en el clúster para adaptarse a su solicitud. Si el problema tiene que ver con que el tamaño de la VM solicitado no se admite, pruebe con una VM diferente. De lo contrario, la única opción es quitar la restricción anclada.

Dos escenarios comunes de error están relacionados con los grupos de afinidad. Antes, un grupo de afinidad se usaba para proporcionar una estrecha proximidad a las VM y a las instancias de servicio, o bien para permitir la creación de una red virtual. Con la introducción de las redes virtuales regionales, los grupos de afinidad ya no son necesarios para crear una red virtual. Al reducirse la latencia de red en la infraestructura de Azure, ha cambiado la recomendación de usar grupos de afinidad para la proximidad de las VM o servicios.

En el diagrama siguiente se presenta la taxonomía de los escenarios de asignación (anclados). 

![Taxonomía de asignaciones ancladas](./media/virtual-machines-common-allocation-failure/Allocation3.png)

## <a name="resize-a-vm-or-add-vms-or-role-instances-to-an-existing-cloud-service"></a>Cambio del tamaño de una VM o incorporación de VM o instancias de rol a un servicio en la nube existente.
**Error**

Upgrade_VMSizeNotSupported o GeneralError

**Causa de anclaje del clúster**

Las solicitudes para cambiar el tamaño de una VM o agregar una VM o una instancia de rol a un servicio en la nube existente se tienen que intentar realizar en el clúster original que hospeda dicho servicio en la nube. La creación de un nuevo servicio en la nube permite que la plataforma de Azure encuentre otro clúster que tenga recursos libres o uno que admita el tamaño de VM solicitado.

**Solución alternativa**

Si el error es Upgrade_VMSizeNotSupported *, pruebe con otro tamaño de máquina virtual. Si el uso de un tamaño de VM diferente no es una opción, pero es aceptable el uso de una dirección IP virtual (VIP) distinta, cree un nuevo servicio en la nube para hospedar la nueva VM y agréguelo a la red virtual regional donde se ejecutan las VM existentes. Aunque su servicio en la nube no use una red virtual regional, puede crear una nueva para el nuevo servicio en la nube y, después, conectar la [red virtual existente a la nueva red virtual](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Consulte más información sobre las [redes virtuales regionales](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

Si el error es GeneralError*, es probable que el tipo de recurso (por ejemplo, un tamaño determinado de VM) sea compatible con el clúster pero que este no tenga recursos libres en ese momento. De forma parecida a como se ha indicado anteriormente, agregue el recurso de proceso deseado mediante la creación de un nuevo servicio en la nube (tenga en cuenta que el nuevo servicio en la nube tiene que usar otra dirección IP virtual) y use la red virtual regional para conectarse a los servicios en la nube.

## <a name="restart-partially-stopped-deallocated-vms"></a>Reinicio de las VM detenidas (desasignadas) parcialmente.
**Error**

GeneralError*

**Causa de anclaje del clúster**

La desasignación parcial indica que se detuvieron (desasignaron) una o varias VM de un servicio en la nube, pero no todas. Al detener (desasignar) una VM, se liberan los recursos asociados. Por lo tanto, reiniciar esa VM detenida (desasignada) implica una nueva solicitud de asignación. Reiniciar las VM en un servicio en la nube desasignado parcialmente es equivalente a agregarlas a un servicio en la nube existente. Las solicitud de asignación se debe intentar realizar en el clúster original que hospeda el servicio en la nube. La creación de otro servicio en la nube permite a la plataforma de Azure encontrar otro clúster que tenga recursos libres o uno que admita el tamaño de máquina virtual solicitado.

**Solución alternativa**

Si es aceptable el uso de una dirección IP virtual diferente, elimine las VM detenidas (desasignadas), pero mantenga los discos asociados, y vuelva agregar las VM mediante un servicio en la nube diferente. Use una red virtual regional para conectarse a los servicios en la nube:

* Si el servicio en la nube existente usa una red virtual regional, agregue el nuevo servicio en la nube a la misma red virtual.
* Si el servicio en la nube no usa una red virtual regional, cree una nueva para el nuevo servicio en la nube y, seguidamente, [conecte la red virtual existente a la nueva red virtual](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Consulte más información sobre las [redes virtuales regionales](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

## <a name="restart-fully-stopped-deallocated-vms"></a>Reinicio de las VM detenidas (desasignadas) completamente.
**Error**

GeneralError*

**Causa de anclaje del clúster**

La desasignación completa indica que detuvo (desasignó) todas las VM de un servicio en la nube. Las solicitudes de asignación para reiniciar estas VM se deben intentar realizar en el clúster original que hospeda el servicio en la nube. La creación de un nuevo servicio en la nube permite que la plataforma de Azure encuentre otro clúster que tenga recursos libres o uno que admita el tamaño de VM solicitado.

**Solución alternativa**

Si es aceptable el uso de una dirección IP virtual diferente, elimine las VM originales detenidas (desasignadas), pero mantenga los discos asociados, y elimine el servicio en la nube correspondiente (los recursos de proceso asociados ya se liberaron cuando detuvo [desasignó] las VM). Cree un nuevo servicio en la nube para volver a agregar las VM.

## <a name="stagingproduction-deployments-platform-as-a-service-only"></a>Implementaciones de ensayo o producción (solo plataforma como servicio).
**Error**

New_General* o New_VMSizeNotSupported\*

**Causa de anclaje del clúster**

Las implementaciones de ensayo y de producción de un servicio en la nube se hospedan en el mismo clúster. Cuando se agrega la segunda implementación, la solicitud de asignación correspondiente se intenta en el mismo clúster que hospeda la primera implementación.

**Solución alternativa**

Elimine la primera implementación y el servicio en la nube original y vuelva implementar el servicio en la nube. Esta acción puede conseguir la primera implementación en un clúster que tenga suficientes recursos libres para ajustarse a ambas implementaciones, o bien en un clúster que admita los tamaños de VM solicitados.

## <a name="affinity-group-vmservice-proximity"></a>Grupo de afinidad (proximidad de la VM o el servicio).
**Error**

New_General* o New_VMSizeNotSupported\*

**Causa de anclaje del clúster**

Cualquier recurso de proceso asignado a un grupo de afinidad está asociado a un clúster. Las nuevas solicitudes de recursos de proceso de ese grupo de afinidad se intentan llevar a cabo en el mismo clúster en el que están hospedados los recursos existentes. Esto es así con independencia de que los nuevos recursos se creen mediante un servicio en la nube nuevo o existente.

**Solución alternativa**

Si no es necesario un grupo de afinidad, no lo use, o bien intente agrupar los recursos de proceso en varios grupos de afinidad.

## <a name="affinity-group-based-virtual-network"></a>Red virtual basada en un grupo de afinidad
**Error**

New_General* o New_VMSizeNotSupported\*

**Causa de anclaje del clúster**

Antes de que se presentaran las redes virtuales regionales, era necesario asociar una red virtual a un grupo de afinidad. Como consecuencia, los recursos de proceso colocados en un grupo de afinidad se rigen por las mismas restricciones que se han descrito en la sección anterior "Escenario de asignación: grupo de afinidad (proximidad de la VM o el servicio)". Es decir, los recursos de proceso están asociados a un clúster.

**Solución alternativa**

Si no necesita ningún grupo de afinidad, cree una red virtual regional para los recursos nuevos que vaya a agregar y, luego, [conecte la red virtual existente a la nueva red virtual](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Consulte más información sobre las [redes virtuales regionales](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

También puede [migrar la red virtual basada en un grupo de afinidad a una red virtual regional](https://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/)y, después, volver a agregar los recursos deseados.
