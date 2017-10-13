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
ms.date: 09/14/2017
ms.author: zivr
ms.openlocfilehash: 90129bee5771534b01ac12473c6cfed77840ae20
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
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
Get-AzureRmVM -ResourceGroupName rgName --Status
```

La siguiente función de PowerShell toma el identificador de la suscripción e imprime una lista de máquinas virtuales que están programadas para su mantenimiento.

```powershell

function MaintenanceIterator
{
    Select-AzureRmSubscription -SubscriptionId $args[0]

    $rgList= Get-AzureRmResourceGroup 

    for ($rgIdx=0; $rgIdx -lt $rgList.Length ; $rgIdx++)
    {
        $rg = $rgList[$rgIdx]
        $vmList = Get-AzureRMVM -ResourceGroupName $rg.ResourceGroupName 
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
 

## <a name="next-steps"></a>Pasos siguientes

Obtenga información acerca de cómo puede registrarse para eventos de mantenimiento desde la máquina virtual con [Eventos programados](scheduled-events.md).