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
ms.openlocfilehash: 24d89b617c347bc9443b437c92cb034acb3e05cb
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/08/2018
---
Cuando se crea una máquina virtual (VM), se reinician las detenidas (desasignadas) o se les cambia el tamaño, Microsoft Azure asigna recursos de proceso a la suscripción. Invertimos continuamente en infraestructura y características adicionales para asegurarnos de que todos los tipos de máquina virtual están disponibles para respaldar la demanda de los clientes. Sin embargo, en ocasiones, puede experimentar errores de asignación de recursos debido al crecimiento sin precedentes de la demanda de los servicios de Azure en determinadas regiones. Este problema puede producirse al intentar crear o iniciar máquinas virtuales en una región mientras estas muestran los siguientes código de error y mensaje:

**Código de error**: AllocationFailed o ZonalAllocationFailed

**Mensaje de error**: "Allocation failed. We do not have sufficient capacity for the requested VM size in this region. Read more about improving likelihood of allocation success at http://aka.ms/allocation-guidance" (Error en la asignación. No tenemos capacidad suficiente para el tamaño de VM solicitado en esta región. Lea más información sobre cómo mejorar la probabilidad de éxito de asignación en http://aka.ms/allocation-guidance")

En este artículo se explican las causas de algunos de los errores de asignación más comunes y se sugieren posibles soluciones.

Si su problema con Azure no se trata en este artículo, visite los [foros de Azure en MSDN y Stack Overflow](https://azure.microsoft.com/support/forums/). Puede publicar su problema en ellos o en @AzureSupport en Twitter. También puede presentar una solicitud de soporte técnico de Azure; para ello, seleccione Obtener soporte técnico en el sitio de [soporte técnico de Azure](https://azure.microsoft.com/support/options/).

Hasta que su tipo de VM preferido esté disponible en su región preferida, se recomienda a los clientes que tengan problemas de implementación que consideren las instrucciones de la siguiente tabla como solución alternativa temporal. 

Identifique la situación que guarde más parecido con su caso y, a continuación, vuelva a intentar solicitar la asignación mediante la solución alternativa recomendada correspondiente para aumentar la probabilidad de éxito de asignación. También puede volver a intentarlo más tarde. Esto se debe a que pueden haberse liberado recursos suficientes en el clúster, región o zona para adaptarse a su solicitud. 


## <a name="resize-a-vm-or-add-vms-to-an-existing-availability-set"></a>Cambio del tamaño de una VM o incorporación de VM a un conjunto de disponibilidad existente.

### <a name="cause"></a>Causa

La solicitud para cambiar el tamaño de una VM o agregarla a un conjunto de disponibilidad existente se tiene que intentar en el clúster original que hospeda dicho conjunto. El tamaño de VM solicitado es compatible con el clúster, pero es posible que el clúster no tenga capacidad suficiente actualmente. 

### <a name="workaround"></a>Solución alternativa

Si la VM puede formar parte de un conjunto de disponibilidad diferente, cree una VM en otro conjunto de disponibilidad (en la misma región). Esta nueva VM se puede agregar luego a la misma red virtual.

Detenga (desasigne) todas las máquinas virtuales que estén en el mismo conjunto de disponibilidad y, luego, reinícielas.
Para detenerlas: haga clic en Grupos de recursos > [su grupo de recursos] > Recursos > [su conjunto de disponibilidad] > Virtual Machines > [su máquina virtual] > Detener.
Una vez detenidas todas las máquinas virtuales, seleccione la primera y, a continuación, haga clic en Iniciar.
De esta forma se garantiza que hay un nuevo intento de asignación en marcha y que se puede seleccionar un nuevo clúster que tenga capacidad suficiente.

## <a name="restart-partially-stopped-deallocated-vms"></a>Reinicio de las VM detenidas (desasignadas) parcialmente.

### <a name="cause"></a>Causa

La desasignación parcial indica que se detuvieron (desasignaron) una o varias VM de un conjunto de disponibilidad, pero no todas. Al desasignar una VM, se liberan los recursos asociados. Reiniciar las VM en un conjunto de disponibilidad desasignado parcialmente es lo mismo que agregarlas a un conjunto de disponibilidad existente. Por lo tanto, se debe intentar solicitar la asignación en el clúster original donde se hospeda el conjunto de disponibilidad existente que puede no tener capacidad suficiente.

### <a name="workaround"></a>Solución alternativa

Detenga (desasigne) todas las máquinas virtuales que estén en el mismo conjunto de disponibilidad y, luego, reinícielas.
Para detenerlas: haga clic en Grupos de recursos > [su grupo de recursos] > Recursos > [su conjunto de disponibilidad] > Virtual Machines > [su máquina virtual] > Detener.
Una vez detenidas todas las máquinas virtuales, seleccione la primera y, a continuación, haga clic en Iniciar.
De esta forma se garantizará que hay un nuevo intento de asignación en marcha y que se puede seleccionar un nuevo clúster que tenga capacidad disponible.

## <a name="restart-fully-stopped-deallocated-vms"></a>Reinicio de las VM detenidas (desasignadas) completamente.

### <a name="cause"></a>Causa

La desasignación completa indica que detuvo (desasignó) todas las VM de un conjunto de disponibilidad. La solicitud de asignación para reiniciar estas VM se dirigirá a todos los clústeres que admitan el tamaño deseado dentro de la zona o región. Cambie su solicitud de asignación por las recomendaciones de este artículo y vuelva a intentar la solicitud para mejorar la posibilidad de éxito de asignación. 

### <a name="workaround"></a>Solución alternativa

Si usa series o tamaños de VM anteriores, como Dv1, DSv1, Av1, D15v2 o DS15v2, considere la posibilidad de migrar a versiones más recientes. Consulte estas recomendaciones para tamaños de VM específicos.
Si no tiene la opción de usar otro tamaño de VM, intente implementar en otra región dentro de la misma geoárea. Para más información sobre los tamaños de VM disponibles en cada región en https://aka.ms/azure-regions

Si usa zonas de disponibilidad, pruebe otra zona de la región que pueda tener capacidad disponible para el tamaño de VM solicitado.

Si su solicitud de asignación es grande (más de 500 núcleos), consulta las instrucciones en las siguientes secciones para dividir la solicitud en implementaciones más pequeñas.

## <a name="allocation-failures-for-older-vm-sizes-av1-dv1-dsv1-d15v2-ds15v2-etc"></a>Errores de asignación para tamaños de VM anteriores (Av1, Dv1, DSv1, D15v2, DS15v2, etc.).

A medida que ampliamos la infraestructura de Azure, implementamos hardware de nueva generación diseñado para admitir los tipos de máquina virtual más recientes. Algunas de las máquinas virtuales de serie anteriores no se ejecutan en nuestra infraestructura de última generación. Por este motivo, en ocasiones, los clientes pueden experimentar errores de asignación para estas SKU heredadas. Para evitar este problema, animamos a los clientes que usan máquinas virtuales de serie heredadas a que consideren la posibilidad de trasladarse a las máquinas virtuales más recientes equivalentes por las siguientes recomendaciones: estas máquinas virtuales están optimizadas para el hardware más reciente y le permitirán aprovechar mejores precios y rendimiento. 

|Tamaño o serie de VM heredados|Tamaño o serie de VM más recientes recomendados|Más información|
|----------------------|----------------------------|--------------------|
|Serie Av1|[Serie Av2](../articles/virtual-machines/windows/sizes-general.md#av2-series)|https://azure.microsoft.com/blog/new-av2-series-vm-sizes/
|Serie Dv1 o DSv1 (D1 a D5)|[Serie Dv3 o DSv3](../articles/virtual-machines/windows/sizes-general.md#dsv3-series-sup1sup)|https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/
|Serie Dv1 o DSv1 (D11 a D14)|[Serie Ev3 o ESv3](../articles/virtual-machines/windows/sizes-memory.md#ev3-series)|
|D15v2 o DS15v2|Si usa el modelo de implementación de Resource Manager para aprovechar los tamaños de VM más grandes, considere la posibilidad de trasladarse a D16v3/DS16v3 o D32v3/DS32v3. Estos están diseñados para ejecutarse en el hardware de última generación. Si usa el modelo de implementación de Resource Manager para asegurarse de que su máquina virtual está aislada para hardware dedicado a un solo cliente, considere la posibilidad de trasladarse a los nuevos tamaños de VM aislados, E64i_v3 or E64is_v3, diseñados para ejecutarse en el hardware de última generación. |https://azure.microsoft.com/blog/new-isolated-vm-sizes-now-available/

## <a name="allocation-failures-for-large-deployments-more-than-500-cores"></a>Errores de asignación para grandes implementaciones (más de 500 núcleos)

Reduzca el número de instancias del tamaño de VM solicitado y, a continuación, vuelva a intentar la operación de implementación. Además, para implementaciones más grandes, es posible que desee evaluar [conjuntos de escalado de máquinas virtuales de Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/). El número de instancias de máquina virtual puede aumentar o disminuir de forma automática en respuesta a la demanda o una programación definida y cuenta con una posibilidad de éxito de asignación mayor porque las implementaciones se pueden distribuir en varios clústeres. 

## <a name="background-information"></a>Información de contexto
### <a name="how-allocation-works"></a>Cómo funciona la asignación
Los servidores de los centros de datos de Azure están particionados en clústeres. Normalmente, se intenta una solicitud de asignación en varios clústeres, pero es posible que determinadas restricciones de la solicitud de asignación obliguen a la plataforma de Azure a intentar la solicitud en solo un clúster. En este artículo, nos referiremos a dicha solicitud como "anclada a un clúster". En el diagrama número 1 que puede ver a continuación, se ilustra el caso de una asignación normal que se intenta en varios clústeres; En el diagrama 2 se ilustra el caso de una asignación que está anclada al clúster 2, porque ahí es donde se hospeda el servicio en la nube CS_1 o el conjunto de disponibilidad.
![Diagrama de asignación](./media/virtual-machines-common-allocation-failure/Allocation1.png)

### <a name="why-allocation-failures-happen"></a>¿Por qué se producen errores de asignación?
Cuando una solicitud de asignación está anclada a un clúster, existe una posibilidad menor de encontrar recursos libres dado que el grupo de recursos disponible es más pequeño. Además, si la solicitud de asignación está anclada a un clúster pero el tipo de recurso que solicita no se admite en ese clúster, la solicitud dará error aunque el clúster tenga recursos libres. En el diagrama 3 siguiente se ilustra el caso en el que una asignación anclada da error porque el único clúster candidato no tiene recursos libres. En el diagrama 4 se ilustra el caso en el que una asignación anclada da error porque el único clúster candidato no admite el tamaño de VM solicitado, a pesar de que el clúster tiene recursos libres.

![Error de asignaciones ancladas](./media/virtual-machines-common-allocation-failure/Allocation2.png)


