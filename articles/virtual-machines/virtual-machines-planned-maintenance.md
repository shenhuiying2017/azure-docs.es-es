<properties
	pageTitle="Planeación del mantenimiento de máquinas virtuales de Azure | Microsoft Azure"
	description="Entienda qué es el mantenimiento planeado de Azure y cómo afecta a sus máquinas virtuales que se ejecutan en Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="kenazk"
	manager="timlt"
	editor=""
	tags="azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/23/2015"
	ms.author="kenazk"/>


# Planeación del mantenimiento de máquinas virtuales de Azure

## Por qué Azure realiza mantenimiento planeado

Microsoft Azure realiza periódicamente actualizaciones en todo el planeta para mejorar la fiabilidad, el rendimiento y la seguridad de infraestructuras host que subyacen bajo las máquinas virtuales. Muchas de esas actualizaciones se realizan sin que afecten a las máquinas virtuales o a los servicios en la nube, incluidas las actualizaciones de conservación de memoria.

Sin embargo, algunas de estas actualizaciones requieren que las máquinas virtuales se reinicien para aplicar a la infraestructura las actualizaciones requeridas. Las máquinas virtuales se cierran mientras arreglamos la infraestructura y después las máquinas virtuales se reinician.

Tenga en cuenta que hay dos tipos de mantenimiento que pueden afectar a la disponibilidad de las máquinas virtuales: planeado y no planeado. En esta página se describe cómo Microsoft Azure realiza el mantenimiento planeado. Para obtener más información acerca del mantenimiento planeado, consulte [Mantenimiento planeado frente a mantenimiento no planeado](virtual-machines-manage-availability.md).

## Actualizaciones de conservación de memoria

En el caso de una clase de actualizaciones en Microsoft Azure, los clientes no verán afectadas sus máquinas virtuales en ejecución. Muchas de estas actualizaciones son componentes o servicios que se pueden actualizar sin interferir con la instancia en ejecución. Algunas de estas actualizaciones son actualizaciones de la infraestructura de la plataforma en el sistema operativo host, que se pueden aplicar sin necesidad de un reinicio completo de las máquinas virtuales.

Estas actualizaciones se realizan con la tecnología que permite la migración en vivo (una actualización de "conservación de memoria"). Al actualizar, la máquina virtual se pone en un estado de "pausa" y conserva la memoria RAM, mientras que el sistema operativo host subyacente recibe las actualizaciones y revisiones necesarias. La máquina virtual se reanudará en menos de 30 segundos después de haberse puesto en pausa. Una vez reanudada, se sincronizará automáticamente el reloj de la máquina virtual.

No todas las actualizaciones pueden implementarse con este mecanismo, pero gracias a su breve período de pausa, implementar las actualizaciones de este modo reduce en gran medida el impacto en las máquinas virtuales.

Se aplican actualizaciones de instancias múltiples (parar máquinas virtuales en un conjunto de disponibilidad) a un dominio de actualización a la vez.

## Configuraciones de máquinas virtuales

Hay dos tipos de configuraciones de máquinas virtuales: instancias múltiples y una sola instancia. En una configuración de instancias múltiples, las máquinas virtuales similares se colocan en un conjunto de disponibilidad.

La configuración de instancias múltiples proporciona redundancia y se recomienda para garantizar la disponibilidad de la aplicación. Todas las máquinas virtuales del conjunto de disponibilidad deben ser prácticamente idénticas y prestar el mismo servicio a la aplicación.

Para obtener más información sobre la configuración de sus máquinas virtuales para ofrecer alta disponibilidad, consulte [Administración de la disponibilidad de las máquinas virtuales](virtual-machines-manage-availability.md).

Por el contrario, se usa una configuración de una sola instancia se usa para máquinas virtuales independientes no colocadas en un conjunto de disponibilidad. Por sí mismas, estas máquinas virtuales no tienen derecho al contrato de nivel de servicio (SLA) que requiere la implementación de dos o más máquinas virtuales en el mismo conjunto de disponibilidad.

Para obtener más información sobre los contratos de nivel de servicio, consulte la sección "Servicios en la nube, máquinas virtuales y red virtual" de [Contratos de nivel de servicio](http://azure.microsoft.com/support/legal/sla/).


## Actualizaciones de una configuración de instancias múltiples

Durante el mantenimiento planeado, la plataforma Azure actualizará primero el conjunto de máquinas virtuales que se hospedan en una configuración de instancias múltiples. Esto provoca un reinicio de estas máquinas virtuales.

En una configuración de instancias múltiples, las máquinas virtuales se actualizan de forma que conservan la disponibilidad durante todo el proceso, suponiendo que cada máquina realiza una función similar a las otras que forman parte del conjunto.

La plataforma Azure subyacente asigna a cada máquina virtual del conjunto de disponibilidad un dominio de actualización y un dominio de error. Cada dominio de actualización es un grupo de máquinas virtuales que se reiniciará en la misma ventana de tiempo. Cada dominio de error es un grupo de máquinas virtuales que comparten una fuente de alimentación común y un conmutador de red.

Para obtener más información acerca de dominios de actualización y dominios de error, consulte [Configure varias máquinas virtuales en un conjunto de disponibilidad para la redundancia](virtual-machines-manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy).

Para evitar que los dominios de actualización se queden sin conexión al mismo tiempo, al realizar el mantenimiento, se cierra cada máquina virtual en un dominio de actualización, se aplica la actualización a los equipos host, se reinician las máquinas virtuales y se pasa al siguiente dominio de actualización. El evento de mantenimiento planeado termina cuando todos los dominios de actualización se han actualizado.

El orden en el que los dominios de actualización se reinician no puede llevarse a cabo secuencialmente durante un mantenimiento planeado, sino que solamente se reiniciará un dominio de actualización simultáneamente. Actualmente, Azure ofrece una notificación de mantenimiento planeado para máquinas virtuales con 48 horas de antelación en la configuración de instancias múltiples.

Después de restaurar una máquina virtual, aquí se muestra un ejemplo de lo que puede mostrar el Visor de eventos de Windows:

<!--Image reference-->
![][image2]

## Actualizaciones de una configuración de una sola instancia

Una vez completadas las actualizaciones de instancias múltiples, Azure realizará la actualización de las máquinas virtuales de una sola instancia. Esta actualización también provoca el reinicio de las máquinas virtuales que no se ejecutan en conjuntos de disponibilidad.

Tenga en cuenta que, aunque solamente tenga una instancia ejecutándose en un conjunto de disponibilidad, la plataforma Azure seguirá tratándola como una actualización de instancias múltiples.

Las máquinas virtuales con una configuración de una sola instancia se actualizan cerrándolas, aplicando la actualización a la máquina host y reiniciando las máquinas virtuales. Estas actualizaciones se ejecutan en todas las máquinas virtuales de una región en una sola ventana de mantenimiento.

Este evento de mantenimiento planeado afectará a la disponibilidad de la aplicación para este tipo de configuración de máquina virtual. Azure ofrece una notificación de mantenimiento planeado para máquinas virtuales con 1 semana de antelación en la configuración de una instancia.

### Notificación por correo electrónico

Solo en el caso de las configuraciones de máquinas virtuales de una instancia o de instancias múltiples, Azure envía con antelación una comunicación por correo electrónico para avisarle del próximo mantenimiento planeado (1 semana de antelación para una instancia y 48 horas de antelación para instancias múltiples). Este correo electrónico se enviará a las cuentas de correo electrónico del administrador y coadministrador de cuenta proporcionadas en la suscripción. A continuación se muestra un ejemplo de este tipo de correo electrónico:

<!--Image reference-->
![][image1]

## Pares de región

Azure organiza un conjunto de pares de región. Azure no implementará ninguna actualización en regiones emparejadas simultáneamente durante un mantenimiento planeado de máquinas virtuales con configuraciones de una instancia.

Consulte la tabla siguiente para obtener información sobre los pares de región actuales:

Región 1 | Región 2
:----- | ------:
Centro-Norte de EE. UU | Centro-Sur de EE. UU
Este de EE. UU. | Oeste de EE. UU.
Este de EE. UU. - 2 | Central EE. UU.:
Europa del Norte | Europa occidental
Sudeste de Asia | Asia oriental
Este de China | Norte de China
Este de Japón | Oeste de Japón
Sur de Brasil | Centro-Sur de EE. UU
Sudeste de Australia | Australia Oriental
Gobierno de EE. UU. - Iowa | Gobierno de EE. UU. - Virginia

Por ejemplo, durante un mantenimiento planeado, Azure no implementará ninguna actualización en el Oeste de EE. UU. si el Este de EE. UU. está en mantenimiento al mismo tiempo. Sin embargo, otras regiones como Europa del Norte pueden estar en mantenimiento al mismo tiempo que el Este de EE. UU.

<!--Anchors-->
[image1]: ./media/virtual-machines-planned-maintenance/vmplanned1.png
[image2]: ./media/virtual-machines-planned-maintenance/EventViewerPostReboot.png
[image3]: ./media/virtual-machines-planned-maintenance/RegionPairs.PNG


<!--Link references-->
[Virtual Machines Manage Availability]: virtual-machines-windows-tutorial.md
[Understand planned versus unplanned maintenance]: virtual-machines-manage-availability.md#Understand-planned-versus-unplanned-maintenance/

<!---HONumber=Sept15_HO4-->