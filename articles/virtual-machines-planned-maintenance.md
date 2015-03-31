<properties 
	pageTitle="Planeación del mantenimiento de máquinas virtuales de Azure" 
	description="Entienda qué es el mantenimiento planeado de Azure y cómo afecta a sus máquinas virtuales que se ejecutan en Azure." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="kenazk" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-multiple" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/26/2014" 
	ms.author="kenazk"/>


# Planeación del mantenimiento de máquinas virtuales de Azure

## Por qué Azure realiza mantenimiento planeado
<p> Microsoft Azure realiza periódicamente actualizaciones en todo el planeta para mejorar la fiabilidad, el rendimiento y la seguridad de infraestructuras host que subyacen bajo máquinas virtuales. Muchas de esas actualizaciones se realizan sin ningún impacto en máquinas virtuales o servicios en la nube. Sin embargo, algunas de estas actualizaciones requieren que la máquina virtual se reinicie para aplicar las actualizaciones requeridas a la infraestructura. La máquina virtual se cerrará mientras arreglamos la infraestructura y después las máquinas virtuales se reiniciarán. Tenga en cuenta que hay dos tipos de mantenimiento que pueden afectar a la disponibilidad de la máquina virtual: planeado y no planeado. En esta página se describe cómo Microsoft Azure realiza el mantenimiento planeado. Para obtener más información acerca del mantenimiento planeado, consulte[Entender el mantenimiento planeado frente al no planeado].

<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->

* [Configuraciones de máquinas virtuales]  
* [Actualización de instancias múltiples]
* [Actualización de una sola instancia]
* [Notificación por correo electrónico]


## Configuraciones de máquinas virtuales
Hay dos tipos de configuraciones de máquinas virtuales: instancias múltiples y una sola instancia.  Las máquinas virtuales de una sola instancia se configuran colocando máquinas virtuales idénticas en el conjunto de disponibilidad. La configuración de instancias múltiples proporciona redundancia y se recomienda para garantizar la disponibilidad de la aplicación. Todas las máquinas virtuales del conjunto de disponibilidad deben ser prácticamente idénticas y prestar el mismo servicio a la aplicación. Para obtener más informacións sobre la configuración de las máquinas virtuales para alta disponibilidad, consulte<a href="http://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability/">"Administración de la disponibilidad de las máquinas virtuales</a>". 

En cambio, las máquinas virtuales de una sola instancia son máquinas virtuales independientes que no se colocan en un conjunto de disponibilidad. Por sí mismas, las máquinas virtuales de una sola instancia no tienen derecho al contrato de nivel de servicio (SLA) que requiere la implementación de dos o más máquinas virtuales en el mismo conjunto de disponibilidad. Para obtener más información sobre los contratos de nivel de servicio, consulte la sección "Servicios en la nube, máquinas virtuales y red virtual" de [Contratos de nivel de servicio](http://azure.microsoft.com/support/legal/sla/).


## Actualización de instancias múltiples
Durante el mantenimiento planeado, la plataforma Azure actualizará primero el conjunto de máquinas host que hospedan el conjunto de máquinas virtuales en una configuración de instancias múltiples, lo que provocará el reinicio de estas máquinas virtuales. En lo que respecta las máquinas virtuales en una configuración de instancias múltiples, dichas máquinas se actualizan de forma que conservan la disponibilidad durante todo el proceso, suponiendo que cada máquina realiza una función similar a las otras que forman parte del conjunto. La plataforma Azure subyacente asigna a cada máquina virtual del conjunto de disponibilidad un dominio de actualización (UD) y un dominio de error (FD). Cada dominio de actualización es un grupo de máquinas virtuales que se reiniciará en la misma ventana de tiempo. Cada dominio de error es un grupo de máquinas virtuales que comparten una fuente de alimentación común y un conmutador de red. 

Para obtener más información sobre UD y FD, consulte "<a href="http://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability/#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy">Configure varias máquinas virtuales en un conjunto de disponibilidad para la redundancia</a>".

Microsoft Azure garantiza que ningún evento de mantenimiento planeado provocará que las máquinas virtuales de dos dominios de actualización diferentes se queden sin conexión al mismo tiempo. El mantenimiento se realiza cerrando cada máquina virtual, aplicando la actualización a las máquinas host, reiniciando la máquina virtual y siguiendo con el siguiente dominio de actualización. El evento de mantenimiento planeado termina cuando todos los dominios de actualización se han actualizado. El orden en el que los dominios de actualización se reinician no puede llevarse a cabo secuencialmente durante un mantenimiento planeado, sino que solamente un dominio de actualización se reiniciará simultáneamente. Actualmente, no se proporciona ninguna notificación de mantenimiento planeado por adelantado para máquinas virtuales en la configuración de instancias múltiples.

Después de restaurar la máquina virtual, aquí se muestra un ejemplo de lo que puede mostrar el Visor de eventos de Windows:

<!--Image reference-->
![][image2]

## Actualización de una sola instancia
Una vez completadas las actualizaciones de instancias múltiples, Azure realizará la actualización en el conjunto de máquinas host que hospedan máquinas virtuales de una sola instancia. Esta actualización también provocará el reinicio de las máquinas virtuales que no se ejecutan en conjuntos de disponibilidad. Tenga en cuenta que, aunque solamente tenga una instancia ejecutándose en un conjunto de disponibilidad, la plataforma Azure seguirá tratándola como instancia múltiple. Las máquinas virtuales con una configuración de una sola instancia se actualizan cerrándolas, aplicando la actualización a la máquina host y reiniciando la máquina virtual. Estas actualizaciones se ejecutan en todas las máquinas virtuales de una región en una sola ventana de mantenimiento. Este evento de mantenimiento planeado afectará a la disponibilidad de la aplicación para este tipo de configuración de máquina virtual. 
 
### Notificación por correo electrónico
Solamente para máquinas virtuales con una configuración de una sola instancia, Azure enviará una comunicación por correo electrónico al menos una vez a la semana por adelantado para informarle del mantenimiento planeado próximo. Este correo electrónico se enviará a la cuenta de correo electrónico principal proporcionada por la suscripción. A continuación se muestra un ejemplo de este tipo de correo electrónico:

<!--Image reference-->
![][image1]

<!--Anchors-->
[Por qué Azure realiza mantenimiento planeado]: #why-azure-performs-planned-maintenance
[Configuraciones de máquinas virtuales]: #virtual-machine-configurations
[Actualización de instancias múltiples]: #multi-instance-update
[Actualización de una sola instancia]: #single-instance-update
[Notificación por correo electrónico]: #email-notification
[image1]: ./media/virtual-machines-planned-maintenance/vmplanned1.png
[image2]: ./media/virtual-machines-planned-maintenance/EventViewerPostReboot.png


<!--Link references-->
[Administración de la disponibilidad de las máquinas virtuales]: ../virtual-machines-windows-tutorial/
[Entender el mantenimiento planeado frente al no planeado]: ../virtual-machines-manage-availability/#Understand-planned-versus-unplanned-maintenance/ 

<!--HONumber=47-->
