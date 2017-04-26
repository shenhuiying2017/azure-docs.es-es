---
title: "Mantenimiento de reinicio de máquina virtual para máquinas virtuales Linux en Azure | Microsoft Docs"
description: "Mantenimiento de reinicio de máquina virtual para máquinas virtuales Linux."
services: virtual-machines-linux
documentationcenter: 
author: 
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: eb4b92d8-be0f-44f6-a6c3-f8f7efab09fe
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/27/2017
ms.author: 
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: c02b2878462afb107c81317c9ea0a317cb9ce838
ms.lasthandoff: 04/18/2017


---

# <a name="vm-restarting-maintenance"></a>Mantenimiento de reinicio de máquina virtual

Hay pocos casos en que las máquinas virtuales se reinicien debido a un mantenimiento planeado de la infraestructura subyacente. Al repercutir en la disponibilidad de las máquinas virtuales hospedadas en Azure, los siguientes están disponibles para su uso:

-   Notificación enviada al menos 30 días antes de la repercusión.

-   Visibilidad en las ventanas de mantenimiento por cada máquina virtual.

-   Flexibilidad y control a la hora de establecer la hora exacta para el mantenimiento que afecte a las máquinas virtuales.

El mantenimiento de Microsoft Azure se programa en iteraciones. Las iteraciones iniciales tienen un ámbito más pequeño para reducir el riesgo que conlleva la implementación de nuevas capacidades y correcciones. Las iteraciones posteriores pueden abarcar varias regiones (nunca del mismo par de regiones). Se incluye una máquina virtual en una iteración de mantenimiento única. Si se anula una iteración, las máquinas virtuales restantes se incluyen en otra iteración futura.

La iteración de mantenimiento planeado tiene dos fases: ventana de mantenimiento preferente y ventana de mantenimiento programado.

La **ventana de mantenimiento preferente** proporciona la flexibilidad necesaria para iniciar el mantenimiento en las máquinas virtuales. Esto permite determinar cuándo se ven afectadas las máquinas virtuales, la secuencia de la actualización y el tiempo entre el mantenimiento de cada máquina virtual. Puede consultar a cada máquina virtual para ver si tiene previsto algún mantenimiento y comprobar el resultado de la última solicitud de mantenimiento iniciada.

La **ventana de mantenimiento programado** es cuando Azure ha programado el mantenimiento de las máquinas virtuales. Durante esta ventana de tiempo, que sigue a la ventana de mantenimiento preferente, puede consultar sobre la ventana de mantenimiento, pero ya no podrá organizarlo.

## <a name="availability-considerations-during-planned-maintenance"></a>Consideraciones sobre disponibilidad durante el mantenimiento planeado 

### <a name="paired-regions"></a>Regiones emparejadas

Cada región de Azure se empareja con otra región de la misma zona geográfica, que juntas forman un emparejamiento regional. Al ejecutar el mantenimiento, Azure solo actualizará las instancias de máquina virtual en una sola región de su pareja. Por ejemplo, al actualizar las máquinas virtuales de la zona centro-norte de EE. UU., Azure no actualizará las máquinas virtuales de centro-sur de EE. UU. al mismo tiempo. Se programarán a una hora independiente, lo que permitirá la conmutación por error o el equilibrio de carga entre regiones. Sin embargo, otras regiones como Europa del Norte pueden estar en mantenimiento al mismo tiempo que el Este de EE. UU.
Más información sobre [Regiones emparejadas de Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

### <a name="single-instance-vms-vs-availability-set-or-vm-scale-set"></a>Máquinas virtuales de instancia única frente a conjunto de disponibilidad o conjunto de escalado de VM

Al implementar una carga de trabajo mediante máquinas virtuales en Azure, puede crear las máquinas virtuales en un conjunto de disponibilidad para proporcionar una alta disponibilidad a la aplicación. Esta configuración garantiza que durante un corte de suministro o un evento de mantenimiento, al menos haya una máquina virtual disponible.

En un conjunto de disponibilidad, las máquinas virtuales individuales se reparten entre un máximo de 20 dominios de actualización. Durante el mantenimiento planeado, solo un dominio de actualización se ve afectado en un momento dado. El orden de los dominios de actualización que se ven afectados puede no ser secuencial durante el mantenimiento planeado. En el caso de las máquinas virtuales de instancia única (que no forman parte del conjunto de disponibilidad), no hay ninguna manera de predecir ni determinar qué y cuántas máquinas virtuales se ven afectadas.

Los conjuntos de escalado de máquinas virtuales son un recurso informático de Azure que permite implementar y administrar un conjunto de máquinas virtuales idénticas como un recurso único.
El conjunto de escalado proporciona garantías similares a un conjunto de disponibilidad establecido en forma de dominios de actualización. 

Para más información sobre la configuración de máquinas virtuales para ofrecer alta disponibilidad, vea [*Administración de la disponibilidad de las máquinas virtuales Windows*](manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="scheduled-events"></a>Eventos programados

Azure Metadata Service le permite descubrir información sobre una máquina virtual hospedada en Azure. Eventos programados, una de las categorías expuestas, muestra información relacionada con próximos eventos (por ejemplo, un reinicio) para que la aplicación se pueda preparar y limitar la interrupción.

Para más información sobre los eventos programados, vea [Azure Metadata Service: eventos programados](../virtual-machines-scheduled-events.md).

## <a name="maintenance-discovery-and-notifications"></a>Detección y notificaciones de mantenimiento

La programación de mantenimiento es visible para los clientes en el nivel de las máquinas virtuales individuales. Puede usar Azure Portal, API, PowerShell o CLI para consultar sobre las ventanas de mantenimiento preferente y programado. Además, cuente con recibir una notificación (correo electrónico) en el caso de que una o varias de sus máquinas virtuales se vean afectadas durante el proceso.

Las fases de mantenimiento programado y de mantenimiento preferente comienzan con una notificación. Cuente con recibir una única notificación por suscripción de Azure. La notificación se envía al administrador y al coadministrador de la suscripción de forma predeterminada. También puede configurar los destinatarios de la notificación de mantenimiento.

### <a name="view-the-maintenance-window-in-the-portal"></a>Ver la ventana de mantenimiento en el portal 

Puede usar Azure Portal y buscar máquinas virtuales con mantenimiento programado.

1.  Inicie sesión en el Portal de Azure.

2.  Haga clic y abra la hoja **Máquinas virtuales**.

3.  Haga clic en el botón **Columnas** para abrir la lista de columnas disponibles para elegir.

4.  Seleccione y agregue las columnas **Ventana de mantenimiento**. Se abren las ventanas de mantenimiento de las máquinas virtuales con mantenimiento programado. Una vez que se completa o se anula el mantenimiento, la ventana de mantenimiento desaparece.

### <a name="query-maintenance-details-using-the-azure-api"></a>Consultar detalles de mantenimiento mediante la API de Azure

Use la [API obtener información de máquina virtual](https://docs.microsoft.com/rest/api/compute/virtualmachines/virtualmachines-get) y busque la vista de instancia para detectar los detalles de mantenimiento en una máquina virtual individual. La respuesta incluye los siguientes elementos:

  - isCustomerInitiatedMaintenanceAllowed: indica si puede iniciar ahora la reimplementación preferente en la máquina virtual.

  - preMaintenanceWindowStartTime: hora de inicio de la ventana de mantenimiento preferente.

  - preMaintenanceWindowEndTime: hora de finalización de la ventana de mantenimiento preferente. Después de esta hora ya no podrá iniciar el mantenimiento en esta máquina virtual.
    
  - maintenanceWindowStartTime: hora de inicio de la ventana de mantenimiento programado a la que se verá afectada la máquina virtual.

  - maintenanceWindowEndTime: hora de finalización de la ventana de mantenimiento programado.
  
  - lastOperationResultCode: resultado de la última operación de reimplementación de mantenimiento.
 
  - lastOperationMessage: mensaje que describe el resultado de la última operación de reimplementación de mantenimiento.


## <a name="pre-emptive-redeploy"></a>Reimplementación preferente

La acción de reimplementación preferente ofrece la flexibilidad de controlar la hora a la que se aplica el mantenimiento a las máquinas virtuales en Azure. El mantenimiento planeado comienza con una ventana de mantenimiento preferente donde puede decidir la hora exacta de reinicio de cada una de las máquinas virtuales. Estos son casos de uso en los que esta funcionalidad resulta útil:

-   Se tiene que coordinar el mantenimiento con el cliente final.

-   La ventana de mantenimiento programado que ofrece Azure no es suficiente.
    Es posible que la ventana se produzca el día de la semana con más ajetreo o sea demasiado larga.

-   En el caso de las aplicaciones de varias instancias o varios niveles, se necesita suficiente tiempo entre dos máquinas virtuales o hay que seguir una secuencia determinada.

Cuando se llama a una reimplementación preferente en una máquina virtual, se mueve la máquina virtual a un nodo ya actualizado y luego se vuelve a conectar, conservando todas las opciones de configuración y los recursos asociados. Mientras se hace, se pierde el disco temporal y se actualizan las direcciones IP dinámicas asociadas con la interfaz de red virtual.

La reimplementación preferente se habilita una vez por máquina virtual. Si se produce un error durante el proceso, se anula la operación, la máquina virtual no se ve afectada y se excluye de la iteración de mantenimiento planeado. Más adelante se le ofrecerá una nueva programación y una nueva oportunidad de programar y ordenar el impacto en las máquinas virtuales.

