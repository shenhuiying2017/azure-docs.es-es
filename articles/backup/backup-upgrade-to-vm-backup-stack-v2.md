---
title: Actualizar al modelo de implementación de Azure Resource Manager para la pila de copia de seguridad de Azure VM | Microsoft Docs
description: Proceso de actualización y preguntas más frecuentes de la pila de copia de seguridad de VM, modelo de implementación de Resource Manager
services: backup, virtual-machines
documentationcenter: ''
author: trinadhk
manager: vijayts
tags: azure-resource-manager, virtual-machine-backup
ms.assetid: ''
ms.service: backup, virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: storage-backup-recovery
ms.date: 03/08/2018
ms.author: trinadhk, sogup
ms.openlocfilehash: 1e5515486afac5a6d84a35bca33f55ae98e287d3
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
ms.locfileid: "33764866"
---
# <a name="upgrade-to-the-azure-resource-manager-deployment-model-for-azure-vm-backup-stack"></a>Actualizar al modelo de implementación de Azure Resource Manager para la pila de copia de seguridad de Azure VM
El modelo de implementación de Resource Manager para la actualización a la pila de copia de seguridad de máquina virtual (VM) proporciona las siguientes mejoras de características:
* Posibilidad de ver instantáneas tomadas como parte de un trabajo de copia de seguridad que está disponible para la recuperación sin tener que esperar a que finalice la transferencia de datos. Reduce el tiempo de espera para la copia de instantáneas en el almacén antes de desencadenar la restauración. Además, esta posibilidad elimina el requisito de almacenamiento adicional para la copia de seguridad de máquinas virtuales Premium, a excepción de la primera copia de seguridad.  

* Reducción de los tiempos de copia de seguridad y restauración al conservarse las instantáneas en local durante siete días.

* Compatibilidad con tamaños de disco de hasta 4 TB.

* Posibilidad de usar cuentas de almacenamiento originales de una máquina virtual no administrada al restaurar. Esta posibilidad existe aun cuando la máquina virtual tenga discos distribuidos entre cuentas de almacenamiento. Las restauraciones son más rápidas en una amplia variedad de configuraciones de máquina virtual.
    > [!NOTE] 
    > Esta posibilidad no es lo mismo que reemplazar la máquina virtual original. 
    >

## <a name="whats-changing-in-the-new-stack"></a>¿Qué es lo que cambia en la nueva pila?
En la actualidad, el trabajo de copia de seguridad consta de dos fases:
1.  Toma de una instantánea de máquina virtual. 
2.  Transferencia de una instantánea de máquina virtual a Azure Backup Vault. 

Un punto de recuperación se considera creado solo después de que se terminan las fases 1 y 2. Como parte de la nueva pila, se crea un punto de recuperación en cuanto finaliza la instantánea. También puede restaurar desde este punto de recuperación mediante el mismo flujo de restauración. Este punto de recuperación se puede identificar en Azure Portal al usar "instantánea" como tipo de punto de recuperación. Una vez que se ha transferido la instantánea al almacén, el tipo de punto de recuperación cambia a "instantánea y almacén". 

![Trabajo de copia de seguridad de la pila de copia de seguridad de VM, modelo de implementación de Resource Manager, almacenamiento y almacén](./media/backup-azure-vms/instant-rp-flow.jpg) 

De forma predeterminada, las instantáneas se conservan durante siete días. Esta característica permite que la restauración finalice más rápido a partir de estas instantáneas. Reduce el tiempo necesario para volver a copiar los datos desde el almacén en la cuenta de almacenamiento del cliente. 

## <a name="considerations-before-upgrade"></a>Consideraciones antes de la actualización
* La actualización de la pila de copia de seguridad de VM es unidireccional. Por eso, todas las copias de seguridad se encuadran en este flujo. Dado que se habilita en el nivel de suscripción, todas las máquinas virtuales se encuadran en este flujo. Todas las nuevas adiciones de características se basan en la misma pila. La posibilidad de controlar esta actualización en el nivel de directiva estará disponible en futuras versiones.

* Las instantáneas se almacenan en local para acelerar la creación de puntos de recuperación y la propia restauración. Por lo tanto, se ven costos de almacenamiento correspondientes a las instantáneas durante el período de siete días.

* Las instantáneas incrementales se almacenan como blobs en páginas. A todos los clientes que usan discos no administrados se les cobra por los siete días que las instantáneas están almacenadas en la cuenta de almacenamiento local del cliente. Según el modelo de precios actual, no hay ningún costo para los clientes con discos administrados.

* Si realiza una restauración desde un punto de recuperación de instantáneas en una máquina virtual Premium, ve que se usa una ubicación de almacenamiento temporal mientras se crea la máquina virtual como parte de la restauración.

* En las cuentas de almacenamiento Premium, las instantáneas tomadas para una recuperación instantánea ocupan 10 TB de espacio asignado.

## <a name="upgrade"></a>Actualizar
### <a name="the-azure-portal"></a>El Portal de Azure
Si usa Azure Portal, ve una notificación en el panel del almacén. Esta notificación está relacionada con la compatibilidad con discos grandes y las mejoras de velocidad de copia de seguridad y restauración.

![Trabajo de copia de seguridad de la pila de copia de seguridad de VM, modelo de implementación de Resource Manager, notificación de compatibilidad](./media/backup-azure-vms/instant-rp-banner.png) 

Para abrir una pantalla para actualizarse a la nueva pila, seleccione el banner. 

![Trabajo de copia de seguridad de la pila de copia de seguridad de VM, modelo de implementación de Resource Manager, actualización](./media/backup-azure-vms/instant-rp.png) 

### <a name="powershell"></a>PowerShell
Ejecute los siguientes cmdlets desde un terminal de PowerShell con privilegios elevados:
1.  Inicio de sesión en la cuenta de Azure 

    ```
    PS C:> Connect-AzureRmAccount
    ```

2.  Seleccione la suscripción que desea registrar para la versión preliminar:

    ```
    PS C:>  Get-AzureRmSubscription –SubscriptionName "Subscription Name" | Select-AzureRmSubscription
    ```

3.  Registre esta suscripción para la versión preliminar privada:

    ```
    PS C:>  Register-AzureRmProviderFeature -FeatureName "InstantBackupandRecovery" –ProviderNamespace Microsoft.RecoveryServices
    ```

## <a name="verify-that-the-upgrade-is-finished"></a>Compruebe que la actualización ha finalizado
Desde un terminal de PowerShell con privilegios elevados, ejecute el siguiente cmdlet:

```
Get-AzureRmProviderFeature -FeatureName "InstantBackupandRecovery" –ProviderNamespace Microsoft.RecoveryServices
```

Si indica "Registrado", la suscripción se ha actualizado a la pila de copia de seguridad de VM del modelo de implementación de Resource Manager.
