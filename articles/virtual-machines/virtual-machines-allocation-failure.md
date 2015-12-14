<properties
	pageTitle="Solución de problemas de errores de asignación de máquinas virtuales | Microsoft Azure"
	description="Solución de problemas de errores de asignación al crear, reiniciar o cambiar el tamaño de una máquina virtual en Azure"
	services="virtual-machines, azure-resource-manager"
	documentationCenter=""
	authors="jiangchen79"
	manager="felixwu"
	editor=""
	tags="top-support-issue"/>

<tags
	ms.service="virtual-machines"
	ms.workload="na"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/29/2015"
	ms.author="kenazk"/>



# Solución de problemas de errores de asignación al crear, reiniciar o cambiar el tamaño de una máquina virtual en Azure

Cuando se crea una máquina virtual, se reinician las máquinas virtuales detenidas (desasignadas) o se cambia el tamaño de una máquina virtual, Microsoft Azure asigna recursos de proceso a la suscripción. En ocasiones, es posible que reciba errores a realizar estas operaciones incluso antes de llegar a los límites de la suscripción de Azure. En este artículo se explican las causas de algunos de los errores de asignación más comunes y se sugieren posibles soluciones. La información también puede ser útil si tiene pensado realizar la implementación de sus servicios.

Si en cualquier momento necesita más ayuda con este artículo, puede ponerse en contacto con los expertos de Azure en [los foros MSDN Azure o Stack Overflow](http://azure.microsoft.com/support/forums/). Como alternativa, también puede registrar un incidente de soporte técnico de Azure. Vaya al [sitio del soporte técnico de Azure](http://azure.microsoft.com/support/options/) y haga clic en **Obtener soporte técnico**.

La sección "Solución de problemas de errores de asignación comunes" muestra los pasos necesarios para resolver problemas habituales. La sección "Solución de problemas de escenarios de errores de asignación específicos" ofrece los pasos de resolución por mensaje de error específico. Antes de empezar, le ofrecemos alguna información de contexto que explica cómo funciona la asignación y por qué se producen errores de asignación.

## Información de contexto
### Cómo funciona la asignación
Los servidores de los centros de datos de Azure están particionados en clústeres. Normalmente, se intenta una solicitud de asignación en varios clústeres, pero es posible que determinadas restricciones de la solicitud de asignación obliguen a la plataforma de Azure a intentar la solicitud en solo un clúster. En este artículo, haremos referencia a esto como que la solicitud está "anclada a un clúster". En el diagrama número 1 que tiene a continuación, se ilustra el caso de una asignación normal que se intenta en varios clústeres; en el diagrama 2, se ilustra el caso de una asignación que está anclada al clúster 2 porque ahí es donde se hospeda el conjunto de disponibilidad o el servicio en la nube CS\_1. ![Diagrama de asignación](./media/virtual-machines-allocation-failure/Allocation1.png)

### ¿Por qué se producen errores de asignación?
Cuando una solicitud de asignación está anclada a un clúster, existe una posibilidad menor de encontrar recursos libres dado que el grupo de recursos disponible es más pequeño. Además, si la solicitud de asignación está anclada a un clúster pero el tipo de recurso que solicita no se admite en ese clúster, la solicitud dará error aunque el clúster tenga recursos libres. En el diagrama 3 a continuación se ilustra el caso en el que una asignación anclada da error porque el único clúster candidato no tiene recursos libres. En el diagrama 4 se ilustra el caso en el que una asignación anclada da error porque el único clúster candidato no admite el tamaño de la máquina virtual solicitado, a pesar de que el clúster tiene recursos libres. ![Error de asignaciones ancladas](./media/virtual-machines-allocation-failure/Allocation2.png)

## Solución de problemas a errores de asignación comunes en el modelo de implementación clásica

Estos pasos básicos pueden ayudar a resolver muchos errores de asignación en las máquinas virtuales.

- Cambie el tamaño de la máquina virtual por uno diferente.<br> Haga clic en Examinar todo > Máquinas virtuales (clásico) > su máquina virtual > Configuración > **Tamaño**. Para obtener información detallada, consulte [Cambiar el tamaño de la máquina virtual](https://msdn.microsoft.com/library/dn168976.aspx).

- Elimine todas las máquinas virtuales del servicio en la nube y vuelva a crearlas.<br> Haga clic en Examinar todo > Máquinas virtuales (clásico) > su máquina virtual > Eliminar. A continuación, haga clic en Nuevo > Proceso > [Imagen de máquina virtual].

## Solución de problemas a errores de asignación comunes en el modelo del Administrador de recursos

Estos pasos básicos pueden ayudar a resolver muchos errores de asignación en las máquinas virtuales.

- Detenga o desasigne todas las máquinas virtuales que se encuentren en el mismo conjunto de disponibilidad y luego reinicie cada una.<br> Para ello, haga clic en Grupos de recursos > su grupo de recursos > Recursos > su conjunto de disponibilidad > Máquinas virtuales > su máquina virtual > Detener.

	Una vez que todas las máquinas virtuales están detenidas, seleccione la primera máquina virtual y haga clic en Iniciar.

## Solución de problemas de escenarios de errores de asignación específicos en el modelo de implementación clásica
A continuación se presentan los escenarios de asignación comunes que ocasionan que una solicitud de asignación quede anclada. Nos dedicaremos a cada escenario más adelante en este artículo.

- Cambio del tamaño de una máquina virtual o incorporación de máquinas virtuales o instancias de rol adicionales a un servicio en la nube existente
- Reiniciar las máquinas virtuales detenidas o desasignadas: desasignación parcial
- Reiniciar las máquinas virtuales detenidas o desasignadas: desasignación completa
- Implementaciones de ensayo y producción (solo plataforma como servicio)
- Grupo de afinidad: proximidad de la máquina virtual o del servicio
- Red virtual basada en un grupo de afinidad

Cuando reciba un error de asignación, vea si alguno de los escenarios descritos se aplica en su caso. Use el error de asignación que devuelve la plataforma de Azure para identificar el escenario correspondiente. Si la solicitud está anclada, intente quitar algunas de las restricciones de anclaje para abrir su solicitud a más clústeres y aumentar así la posibilidad de que la asignación se realice correctamente.

En general, mientras el error no indique que el tamaño de máquina virtual solicitado no se admite, siempre puede reintentarlo más adelante y dar tiempo a que se liberen suficientes recursos en el clúster para dar cabida a su solicitud. Si el problema es que el tamaño de memoria virtual solicitado no se admite, pruebe con otro diferente; de lo contrario, la única opción es quitar la restricción de anclaje.

Dos escenarios comunes de error están relacionados con el grupo de afinidad. En el pasado, el grupo de afinidad se usaba para proporcionar una estrecha proximidad a máquinas virtuales o instancias de servicio, o bien para permitir la creación de redes virtuales. Con la introducción de la red virtual regional, el grupo de afinidad ya no es necesario para crear una red virtual. Al reducirse la latencia de red en la infraestructura de Azure, ha cambiado la recomendación de usar el grupo de afinidad para la proximidad de máquinas virtuales o servicios.

El diagrama 5 a continuación presenta la taxonomía de los escenarios de asignación (anclados). ![Taxonomía de asignaciones ancladas](./media/virtual-machines-allocation-failure/Allocation3.png)

> [AZURE.NOTE]El error que se muestra en cada escenario de asignación está en forma abreviada. Para ver las cadenas de error detalladas, consulte el [Apéndice](#appendix).

### Escenario de asignación: cambiar el tamaño de una máquina virtual o agregar máquinas virtuales o instancias de rol adicionales a un servicio en la nube existente
**Error**

Upgrade\_VMSizeNotSupported* o GeneralError*

**Causa de anclaje del clúster**

La solicitud para cambiar el tamaño de una máquina virtual o agregar una máquina virtual o una instancia de rol a un servicio en la nube existente se tiene que intentar en el clúster original que hospeda dicho servicio en la nube. La creación de un nuevo servicio en la nube permite a la plataforma de Azure encontrar otro clúster que tenga recursos libres o uno que admita el tamaño de máquina virtual solicitado.

**Solución alternativa**

Si el error es Upgrade\_VMSizeNotSupported *, pruebe con otro tamaño de máquina virtual. Si el uso de un tamaño de máquina virtual diferente no es una opción, pero es aceptable el uso de una dirección IP virtual (VIP) diferente, cree un nuevo servicio en la nube para hospedar la nueva máquina virtual y agréguelo a la red virtual regional donde se ejecutan las máquinas virtuales existentes. Aunque su servicio en la nube no use la red virtual regional, puede crear una nueva red virtual para el nuevo servicio en la nube y luego conectar la [red virtual existente a la nueva red virtual](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Obtenga más información sobre la [red virtual regional](http://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

Si el error es GeneralError*, es probable que el tipo de recurso (por ejemplo, un tamaño determinado de máquina virtual) sea compatible con el clúster pero que este no tenga recursos libres en ese momento. De forma parecida a como se ha indicado anteriormente, intente agregar el recurso de proceso deseado mediante la creación de un nuevo servicio en la nube (tenga en cuenta que el nuevo servicio en la nube tiene que usar una dirección IP virtual diferente) y use la red virtual regional para conectarse a los servicios en la nube.

### Escenario de asignación: reiniciar las máquinas virtuales detenidas (desasignadas), desasignación parcial

**Error**

GeneralError*

**Causa de anclaje del clúster**

La desasignación **parcial** indica que se detuvo (desasignó) una o varias máquinas de un servicio en la nube, pero **no todas**. Al detener (desasignar) una máquina virtual, se liberan los recursos asociados. Por lo tanto, reiniciar esa máquina virtual detenida (desasignada) implica una nueva solicitud de asignación. Reiniciar las máquinas virtuales de un servicio en la nube desasignado parcialmente es equivalente a agregar máquinas virtuales a un servicio en la nube existente, y la solicitud de asignación se debe intentar en el clúster original que hospeda dicho servicio en la nube. La creación de un servicio en la nube diferente permite a la plataforma de Azure encontrar otro clúster que tenga recursos libres o uno que admita el tamaño de máquina virtual solicitado.

**Solución alternativa**

Si es aceptable el uso de una dirección IP virtual diferente, elimine las máquinas virtuales detenidas (desasignadas), pero mantenga los discos asociados, y vuelva agregar las máquinas virtuales mediante un servicio en la nube diferente. Use la red virtual regional para conectarse a los servicios en la nube: 1. Si el servicio en la nube existente usa la red virtual regional, agregue simplemente el nuevo servicio en la nube a la misma red virtual. 2. Si el servicio en la nube existente no usa la red virtual regional, cree una red virtual para el nuevo servicio en la nube y, a continuación, [conecte la red virtual existente a la nueva red virtual](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Obtenga más información sobre la [red virtual regional](http://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

### Escenario de asignación: reiniciar las máquinas virtuales detenidas (desasignadas), desasignación completa
**Error**

GeneralError*

**Causa de anclaje del clúster**

La desasignación **completa** indica que detuvo (desasignó) **todas** las máquinas virtuales de un servicio en la nube. A partir de ahora, las solicitudes de asignación para reiniciar estas máquinas virtuales se deben intentar en el clúster original que hospeda el servicio en la nube. La creación de un nuevo servicio en la nube permite a la plataforma de Azure encontrar otro clúster que tenga recursos libres o uno que admita el tamaño de máquina virtual solicitado.

**Solución alternativa**

Si es aceptable el uso de una dirección IP virtual diferente, elimine las máquinas virtuales originales detenidas (desasignadas), pero mantenga los discos asociados, y elimine el servicio en la nube correspondiente (los recursos de proceso asociados ya se liberaron cuando detuvo [desasignó] las máquinas virtuales). Cree un nuevo servicio en la nube para volver a agregar las máquinas virtuales.

### Escenario de asignación: implementaciones de ensayo y producción (solo plataforma como servicio)
**Error**

New\_General* o New\_VMSizeNotSupported*

**Causa de anclaje del clúster**

La implementación de ensayo y la implementación de producción de un servicio en la nube se hospedan en el mismo clúster. Cuando se agrega la segunda implementación, la solicitud de asignación correspondiente se intenta en el mismo clúster que hospeda la primera implementación.

**Solución alternativa**

Si es aceptable, elimine la primera implementación y el servicio en la nube original y vuelva implementar el servicio en la nube. Esta acción puede conseguir la primera implementación en un clúster que tenga suficientes recursos libres para ajustarse a ambas implementaciones, o en un clúster que admita los tamaños de máquina virtual solicitados.

### Escenario de asignación: grupo de afinidad, proximidad de la máquina virtual o el servicio
**Error**

New\_General* o New\_VMSizeNotSupported*

**Causa de anclaje del clúster**

Cualquier recurso de proceso asignado a un grupo de afinidad está asociado a un clúster. Las nuevas solicitudes de recursos de proceso de ese grupo de afinidad se intentan en el mismo clúster en el que están hospedados los recursos existentes. Esto es así con independencia de que los nuevos recursos se creen mediante un servicio en la nube nuevo o existente.

**Solución alternativa**

Si no es necesario, no use el grupo de afinidad, o bien intente agrupar los recursos de proceso en varios grupos de afinidad de proceso.

### Escenario de asignación: red virtual basada en grupo de afinidad
**Error**

New\_General* o New\_VMSizeNotSupported*

**Causa de anclaje del clúster**

Antes de que se anunciara la red virtual regional, era necesario asociar una red virtual a un grupo de afinidad. Como consecuencia, los recursos de proceso colocados en el grupo de afinidad se regían por las mismas restricciones que se han descrito en la sección anterior "Escenario de asignación: grupo de afinidad, proximidad de máquina virtual o servicio". Es decir, los recursos de proceso estaban asociados a un clúster.

**Solución alternativa**

Si no necesita el grupo de afinidad, cree una red virtual regional para los nuevos recursos que vaya a agregar y, a continuación, [conecte la red virtual existente a la nueva red virtual](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/). Obtenga más información sobre la [red virtual regional](http://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/).

Asimismo, también puede [migrar la red virtual basada en el grupo de afinidad a la red virtual regional](http://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/) y posteriormente intentar agregar de nuevo los recursos deseados.

## Solución de problemas de escenarios de errores de asignación específicos en el modelo de implementación del Administrador de recursos de Azure
A continuación se presentan los escenarios de asignación comunes que ocasionan que una solicitud de asignación quede anclada. Nos dedicaremos a cada escenario más adelante en este artículo.

- Cambio del tamaño de una máquina virtual o incorporación de máquinas virtuales o instancias de rol adicionales a un servicio en la nube existente
- Reiniciar las máquinas virtuales detenidas (desasignadas): desasignación **parcial**
- Reiniciar las máquinas virtuales detenidas (desasignadas): desasignación **completa**

Cuando reciba un error de asignación, vea si alguno de los escenarios descritos se aplica en su caso. Use el error de asignación que devuelve la plataforma de Azure para identificar el escenario correspondiente. Si la solicitud está anclada a un clúster existente, intente quitar algunas de las restricciones de anclaje para abrir su solicitud a más clústeres y aumentar así la posibilidad de que la asignación se realice correctamente.

En general, mientras el error no indique que el tamaño de máquina virtual solicitado no se admite, siempre puede reintentarlo más adelante y dar tiempo a que se liberen suficientes recursos en el clúster para dar cabida a su solicitud. Si el problema tiene que ver con que el tamaño de máquina virtual solicitado no se admite, vea a continuación las posibles soluciones.

### Escenario de asignación: cambiar el tamaño de una máquina virtual o agregar máquinas virtuales adicionales a un conjunto de disponibilidad existente
**Error**

Upgrade\_VMSizeNotSupported* o GeneralError*

**Causa de anclaje del clúster**

La solicitud para cambiar el tamaño de una máquina virtual o agregar una máquina virtual a un conjunto de disponibilidad existente se tiene que intentar en el clúster original que hospeda dicho conjunto. La creación de un nuevo conjunto de disponibilidad permite a la plataforma de Azure encontrar otro clúster que tenga recursos libres o uno que admita el tamaño de máquina virtual solicitado.

**Solución alternativa**

Si el error es Upgrade\_VMSizeNotSupported *, pruebe con otro tamaño de máquina virtual. Si el uso de un tamaño de máquina virtual diferente no es una opción, detenga toda las máquinas virtuales del conjunto de disponibilidad. De esta forma podrá cambiar el tamaño de la máquina virtual, la cual se asignará a un clúster que admita el tamaño deseado.

Si el error es GeneralError*, es probable que el tipo de recurso (por ejemplo, un tamaño determinado de máquina virtual) sea compatible con el clúster pero que este no tenga recursos libres en ese momento. Si la máquina virtual puede formar parte de un conjunto de disponibilidad diferente, cree una nueva máquina virtual en otro conjunto de disponibilidad (en la misma región). Esta nueva máquina virtual se puede agregar luego a la misma red virtual.

### Escenario de asignación: reiniciar las máquinas virtuales detenidas (desasignadas), desasignación parcial
**Error**

GeneralError*

**Causa de anclaje del clúster**

La desasignación **parcial** indica que detuvo (desasignó) una o varias máquinas del conjunto de disponibilidad, pero **no todas**. Al detener (desasignar) una máquina virtual, se liberan los recursos asociados. Por lo tanto, reiniciar esa máquina virtual detenida (desasignada) implica una nueva solicitud de asignación. Reiniciar las máquinas virtuales de un conjunto de disponibilidad desasignado parcialmente es equivalente a agregar máquinas virtuales a un conjunto de disponibilidad existente, y la solicitud de asignación se debe intentar en el clúster original que hospeda dicho conjunto de disponibilidad.

**Solución alternativa**

Pruebe a detener todas las máquinas virtuales del conjunto de disponibilidad antes de reiniciar la primera de ellas. De esta forma se tiene la seguridad de que hay un nuevo intento de asignación en marcha y se puede seleccionar un nuevo clúster que tenga capacidad disponible.

### Escenario de asignación: reiniciar las máquinas virtuales detenidas (desasignadas), desasignación completa
**Error**

GeneralError*

**Causa de anclaje del clúster**

La desasignación **completa** indica que detuvo (desasignó) **todas** las máquinas virtuales de un conjunto de disponibilidad. La solicitud de asignación para reiniciar estas máquinas virtuales se dirigirá a todos los clústeres que admitan el tamaño deseado.

**Solución alternativa**

Pruebe a seleccionar un nuevo tamaño de máquina virtual para asignar. En caso contrario, inténtelo de nuevo más tarde.

## Anexo
### Búsqueda de cadenas de error
**New\_VMSizeNotSupported***

El tamaño de máquina virtual (o la combinación de tamaños de máquina virtual) que se necesita en esta implementación no se puede aprovisionar debido a restricciones en las solicitudes de implementación. Si es posible, intente relajar las restricciones, como los enlaces de red virtual; intente realizar la implementación en un servicio hospedado que no tenga ninguna otra implementación y en un grupo de afinidad diferente o sin grupo de afinidad, o intente realizar la implementación en otra región.

**New\_General***

Error en la asignación; no se pudieron satisfacer las restricciones de la solicitud. La nueva implementación del servicio solicitada está enlazada a un grupo de afinidad, su destino es una red virtual o hay una implementación existente bajo este servicio hospedado. Todas estas condiciones restringen la nueva implementación a recursos específicos de Azure. Inténtelo de nuevo más tarde o pruebe a reducir el tamaño de la máquina virtual o el número de instancias de rol. También puede quitar, si es posible, las restricciones mencionadas o intentar realizar la implementación en otra región.

**Upgrade\_VMSizeNotSupported***

No se puede actualizar la implementación. Es posible que el tamaño de la máquina virtual solicitada XXX no esté disponible entre los recursos que son compatibles con la implementación existente. Inténtelo más tarde, inténtelo con un tamaño de máquina virtual diferente o con menos instancias de rol, o bien cree una implementación en un servicio hospedado vacío con un nuevo grupo de afinidad o sin enlazar ningún grupo de afinidad.

**GeneralError***

Se produjo un error interno en el servidor. Vuelva a realizar la solicitud." o "Error al producir una asignación para el servicio.

## Recursos adicionales
### Póngase en contacto con el servicio de atención al cliente de Azure

Si este artículo no le ayudó a resolver su problema con Azure, puede consultar los foros de Azure en [MSDN y Stack Overflow](http://azure.microsoft.com/support/forums/). También puede presentar un incidente de soporte técnico de Azure sobre su problema. Vaya al sitio del [soporte técnico de Azure](http://azure.microsoft.com/support/options/) y haga clic en Obtener soporte. Para obtener información sobre el uso del soporte técnico de Azure, lea las [Preguntas más frecuentes del soporte técnico de Microsoft Azure](http://azure.microsoft.com/support/faq/).

<!---HONumber=AcomDC_1203_2015-->