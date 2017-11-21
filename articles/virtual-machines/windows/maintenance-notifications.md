---
title: "Control de las notificaciones de mantenimiento de máquinas virtuales Windows en Azure | Microsoft Docs"
description: "Ver las notificaciones de mantenimiento de máquinas virtuales Windows que se ejecutan en Azure e iniciar el mantenimiento de autoservicio."
services: virtual-machines-windows
documentationcenter: 
author: zivraf
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/26/2017
ms.author: zivr
ms.openlocfilehash: 80c029866f3d28712be823692f3bf4ce6e210405
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/09/2017
---
# <a name="handling-planned-maintenance-notifications-for-windows-virtual-machines"></a>Control de las notificaciones de mantenimiento planeado de máquinas virtuales Windows

Azure realiza periódicamente actualizaciones para mejorar la confiabilidad, el rendimiento y la seguridad de la infraestructura host de las máquinas virtuales. Las actualizaciones son cambios como la aplicación de revisiones en el entorno de hospedaje o la actualización y la retirada de hardware. La mayoría de estas actualizaciones se realizan sin afectar a las máquinas virtuales hospedadas. Sin embargo, hay casos en los que las actualizaciones tienen un impacto:

- Si el mantenimiento no requiere un reinicio, Azure usa la migración en contexto para pausar la máquina virtual mientras se actualiza el host.

- Si el mantenimiento requiere un reinicio, el usuario recibe un aviso de cuándo está programado el mantenimiento. En estos casos, se le asigna también un período de tiempo donde puede iniciar el mantenimiento a la hora que le sea más conveniente.


El mantenimiento planeado que requiere un reinicio se programa en olas. Cada ola tiene un ámbito diferente (regiones).

- Una ola comienza con una notificación a los clientes. De forma predeterminada, la notificación se envía al propietario de la suscripción y a los copropietarios. Puede agregar más destinatarios y opciones de mensajes como correo electrónico, SMS y Webhooks a las notificaciones.  
- Poco después de la notificación, se establece una ventana de autoservicio. Durante este período, puede averiguar qué máquinas virtuales se incluyen en esta ola e iniciar el mantenimiento mediante una reimplementación proactiva. 
- A continuación de la ventana de autoservicio, comienza la ventana de mantenimiento programado. En este momento, Azure programa y aplica el mantenimiento necesario para la máquina virtual. 

El objetivo de tener dos ventanas es proporcionar tiempo suficiente para que pueda iniciar el mantenimiento y reiniciar la máquina virtual sabiendo cuando iniciará Azure el mantenimiento automáticamente.


Puede usar Azure Portal, PowerShell, la API de REST y la CLI para consultar las ventanas de mantenimiento de las máquinas virtuales e iniciar el mantenimiento de autoservicio.

 > [!NOTE]
 > Si intenta iniciar el mantenimiento y se produce un error, Azure marca la máquina virtual como **omitida** y no se reinicia durante la ventana de mantenimiento programado. En su lugar, le llegará un contacto posterior con una nueva programación. 


[!INCLUDE [virtual-machines-common-maintenance-notifications](../../../includes/virtual-machines-common-maintenance-notifications.md)]

## <a name="check-maintenance-status-using-powershell"></a>Comprobación del estado de mantenimiento con PowerShell

También puede usar Azure Powershell para ver cuándo está programado el mantenimiento de las máquinas virtuales. La información de mantenimiento planeado está con el cmdlet [Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm) cuando se usa el parámetro `-status`.
 
La información de mantenimiento se devuelve solo si hay mantenimiento planeado. Si no hay ningún mantenimiento programado que afecte a la máquina virtual, el cmdlet no devuelve ninguna información de mantenimiento. 

```powershell
Get-AzureRmVM -ResourceGroupName rgName -Name vmName -Status
```

Las siguientes propiedades se devuelven en MaintenanceRedeployStatus: 
| Valor | Descripción   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Indica si puede iniciar el mantenimiento en la máquina virtual en este momento ||
| PreMaintenanceWindowStartTime         | El comienzo de la ventana de autoservicio de mantenimiento en la que puede iniciar el mantenimiento en la máquina virtual ||
| PreMaintenanceWindowEndTime           | El final de la ventana de autoservicio de mantenimiento en la que puede iniciar el mantenimiento en la máquina virtual ||
| MaintenanceWindowStartTime            | El comienzo de la ventana de mantenimiento programado en la que puede iniciar el mantenimiento en la máquina virtual ||
| MaintenanceWindowEndTime              | El final de la ventana de mantenimiento programado en la que puede iniciar el mantenimiento en la máquina virtual ||
| LastOperationResultCode               | El resultado del último intento de iniciar el mantenimiento en la máquina virtual ||



También puede obtener el estado de mantenimiento de todas las máquinas virtuales en un grupo de recursos mediante el uso de [Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm) sin especificar una máquina virtual.
 
```powershell
Get-AzureRmVM -ResourceGroupName rgName -Status
```

La siguiente función de PowerShell toma el identificador de la suscripción e imprime una lista de máquinas virtuales que están programadas para su mantenimiento.

```powershell

function MaintenanceIterator
{
    Select-AzureRmSubscription -SubscriptionId $args[0]

    $rgList= Get-AzureRmResourceGroup 

    for ($rgIdx=0; $rgIdx -lt $rgList.Length ; $rgIdx++)
    {
        $rg = $rgList[$rgIdx]        $vmList = Get-AzureRMVM -ResourceGroupName $rg.ResourceGroupName 
        for ($vmIdx=0; $vmIdx -lt $vmList.Length ; $vmIdx++)
        {
            $vm = $vmList[$vmIdx]
            $vmDetails = Get-AzureRMVM -ResourceGroupName $rg.ResourceGroupName -Name $vm.Name -Status
              if ($vmDetails.MaintenanceRedeployStatus )
            {
                Write-Output "VM: $($vmDetails.Name)  IsCustomerInitiatedMaintenanceAllowed: $($vmDetails.MaintenanceRedeployStatus.IsCustomerInitiatedMaintenanceAllowed) $($vmDetails.MaintenanceRedeployStatus.LastOperationMessage)"               
            }
          }
    }
}

```

### <a name="start-maintenance-on-your-vm-using-powershell"></a>Inicio del mantenimiento en la máquina virtual mediante PowerShell

Con la información de la función de la sección anterior, lo siguiente inicia el mantenimiento en una máquina virtual si **IsCustomerInitiatedMaintenanceAllowed** está establecido en true.

```powershell
Restart-AzureRmVM -PerformMaintenance -name $vm.Name -ResourceGroupName $rg.ResourceGroupName 
```

## <a name="classic-deployments"></a>Implementaciones clásicas

Si todavía tiene máquinas virtuales heredadas que se han implementado según el modelo de implementación clásico, puede usar PowerShell para consultar las máquinas virtuales e iniciar el mantenimiento.

Para obtener el estado de mantenimiento de una máquina virtual, escriba lo siguiente:

```
Get-AzureVM -ServiceName <Service name> -Name <VM name>
```

Para iniciar el mantenimiento de la máquina virtual clásica, escriba lo siguiente:

```
Restart-AzureVM -InitiateMaintenance -ServiceName <service name> -Name <VM name>
```


## <a name="faq"></a>P+F


**P: ¿Por qué hay que reiniciar las máquinas virtuales ahora?**

**R:** Aunque la mayoría de las actualizaciones de la plataforma Azure no afectan a la disponibilidad de la máquina virtual, hay casos en los que es imposible evitar reiniciar las máquinas virtuales hospedadas en Azure. Hemos acumulado varios cambios que requieren el reinicio de nuestros servidores, lo que conllevará el reinicio de las máquinas virtuales.

**P: Si sigo las recomendaciones para lograr una alta disponibilidad mediante un conjunto de disponibilidad, ¿estoy seguro?**

**R:**Las máquinas virtuales implementadas en un conjunto de disponibilidad o en conjuntos de escalado de máquinas virtuales tienen la noción de dominios de actualización (UD). Al realizar mantenimiento, Azure respeta la restricción de UD, por lo que no reiniciará máquinas virtuales de diferentes UD (del mismo conjunto de disponibilidad).  Azure también espera al menos 30 minutos antes de pasar al siguiente grupo de máquinas virtuales. 

Para más información acerca de la alta disponibilidad, consulte Administración de la disponibilidad de las máquinas virtuales Windows en Azure o Administración de la disponibilidad de las máquinas virtuales Linux en Azure.

**P: Tengo la recuperación ante desastres establecida en otra región. ¿Estoy seguro?**

**R:** Cada región de Azure se empareja con otra región de la misma zona geográfica (por ejemplo, EE. UU., Europa o Asia). Las actualizaciones planeadas de Azure se implementan una a una en regiones emparejadas para minimizar el tiempo de inactividad y el riesgo de interrupción de la aplicación. Durante el mantenimiento planeado, Azure puede programar una ventana similar para que los usuarios inicie el mantenimiento; sin embargo, la ventana de mantenimiento programada será diferente entre las regiones emparejadas.  

Para más información acerca de las regiones de Azure, consulte Regiones y disponibilidad de máquinas virtuales en Azure.  Aquí se puede ver la lista completa de pares regionales.

**P: ¿Cómo recibo notificaciones acerca del mantenimiento planeado?**

**R:** Una oleada de mantenimiento planeado se inicia mediante el establecimiento de una programación en una o varias regiones de Azure. Poco después, se envía una notificación por correo electrónico a los propietarios de las suscripciones (un correo electrónico por suscripción). Mediante Alertas de registro de actividad se pueden configurar más canales y destinatarios de esta notificación. En caso de que implemente una máquina virtual en una región en la que ya se ha programado un mantenimiento planeado, no recibirá la notificación, sino que tendrá que comprobar el estado de mantenimiento de la máquina virtual.

**P: No veo ninguna indicación del mantenimiento planeado en el portal, Powershell o la CLI, ¿cuál es el problema?**

**R:** La información relacionada con el mantenimiento planeado está disponible durante una oleada de mantenimiento planeado solo para las máquinas virtuales que se va a verse afectadas por ella. En otras palabras, si no ve datos, es posible que la oleada de mantenimiento se haya completado (o que no haya empezado) o que la máquina virtual ya esté alojada en un servidor actualizado.

**P: ¿Debo iniciar el mantenimiento en mi máquina virtual?**

**R:** En general, las cargas de trabajo que se implementan en un servicio en la nube, el conjunto de disponibilidad o el conjunto de escalado de máquinas virtuales, son resistentes al mantenimiento planeado.  Durante el mantenimiento planeado, solo un dominio de actualización se ve afectado en un momento dado. Tenga en cuenta que el orden de los dominios de actualización afectados no siempre ocurre de manera secuencial.

Puede que desee iniciar a realizar usted mismo el mantenimiento en los casos siguientes:
- La aplicación se ejecuta en una única máquina virtual y todo el mantenimiento durante debe realizarse fuera de las horas de trabajo
- Necesita coordinar la hora del mantenimiento como parte de su SLA
- Necesita más de 30 minutos entre cada reinicio de máquinas virtuales, incluso dentro de un conjunto de disponibilidad.
- Desea dar de baja toda la aplicación (varios niveles y varios dominios de actualización) para completar el mantenimiento más rápidamente.

**P: ¿Existe alguna una manera de saber exactamente cuándo se verá afectada mi máquina virtual?**

**R:** Al establecer la programación, definimos una ventana de tiempo de varios días. Sin embargo, se desconoce la secuencia exacta de los servidores (y las máquinas virtuales) en esta ventana. Los clientes que deseen conocer el tiempo exacto de sus máquinas virtuales pueden usar eventos programados y realizar consultas desde la máquina virtual y recibir una notificación de 10 minutos antes de que se reinicie la máquina virtual.
  
**P: ¿Cuánto tiempo tardará en reiniciar mi máquina virtual?**

**R:** En función del tamaño de la máquina virtual, el reinicio podría tardar varios minutos. Tenga en cuenta que si usa servicios en la nube, conjuntos de escalado o un conjunto de disponibilidad, se le proporcionarán 30 minutos entre cada grupo de máquinas virtuales (UD). 

**P: ¿Cuál será la experiencia en el caso de los servicios en la nube, conjuntos de escalado y Service Fabric?**

**R:** Aunque estas plataformas resultan afectadas por el mantenimiento planeado, se considera que los clientes que las usan están seguros, ya que solo resultarán afectadas en cualquier momento las máquinas virtuales de un dominio de actualización (UD) individual.  

**P: He recibido un correo electrónico acerca de la retirada de hardware, ¿es lo mismo que el mantenimiento planeado?**

**R:** Aunque la retirada de hardware es un evento de mantenimiento planeado, este caso de uso aún no se ha incorporado a la nueva experiencia.  Suponemos que los clientes pueden sentir confusión si reciben dos mensajes de correo electrónico similares acerca de dos oleadas de mantenimiento planeado diferentes.

**P: No veo información de mantenimiento en mis máquinas virtuales. ¿A qué se debe?**

**R:** Hay varios motivos por los que no ve información de mantenimiento en sus máquinas virtuales:
1.  Utiliza una suscripción marcada como interna de Microsoft.
2.  Las máquinas virtuales no están programadas para su mantenimiento. Es posible que la oleada de mantenimiento haya finalizado, se haya cancelado o se haya modificado para que las máquinas virtuales dejen de verse afectadas por ella.
3.  La columna de "mantenimiento" no se ha agregado a la vista de la lista de máquinas virtuales. Aunque hemos agregado esta columna a la vista predeterminada, los clientes que se han configurado para ver las columnas no predeterminadas deben agregar manualmente la columna de **mantenimiento** a la vista de lista de máquinas virtuales.

**P: Está programado que se realice un segundo mantenimiento programado de mi máquina virtual. ¿Por qué?**

**R:** Hay varios casos de uso en los que verá que el mantenimiento de su máquina virtual está programado después de que haya completado el ciclo de mantenimiento-reimplementación:
1.  Hemos cancelado la oleada de mantenimiento y la hemos reiniciado con otra carga útil. Es posible que hayamos detectado una carga útil con errores y que necesitemos implementar una carga adicional.
2.  El *servicio de una máquina virtual se ha reparado* en otro nodo debido a un error de hardware
3.  Ha seleccionado detener (desasignar) y reiniciar la máquina virtual
4.  El **apagado automático** se ha activado en la máquina virtual


## <a name="next-steps"></a>Pasos siguientes

Obtenga información acerca de cómo puede registrarse para eventos de mantenimiento desde la máquina virtual con [Eventos programados](scheduled-events.md).
