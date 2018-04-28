---
title: Actualización a la versión 2 de la pila de copia de seguridad de máquinas virtuales de Azure | Microsoft Docs
description: Actualización del proceso y preguntas frecuentes sobre la versión 2 de la pila de copia de seguridad de máquinas virtuales
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
ms.openlocfilehash: 7e092dc1448a45277e01b1a8c6d2bc0e2a8a22a3
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/19/2018
---
# <a name="upgrade-to-vm-backup-stack-v2"></a>Actualización a la versión 2 de la pila de copia de seguridad de máquinas virtuales
La actualización de la versión 2 de la pila de copia de seguridad de máquinas virtuales (VM) proporciona las siguientes mejoras de características:
* Posibilidad de ver la instantánea tomada como parte del trabajo de copia de seguridad que estará disponible para la recuperación sin tener que esperar a que finalice la transferencia de datos.
Como resultado, se reduce la espera en la instantánea que se va a copiar en el almacén antes de que se desencadene la restauración. Además, se elimina el requisito de almacenamiento adicional para la copia de seguridad de máquinas virtuales premium, a excepción de la primera copia de seguridad.  

* Reducción de los tiempos de copia de seguridad al conservarse las instantáneas localmente durante 7 días. 

* Compatibilidad con tamaños de disco de hasta 4 TB.  

* Posibilidad de usar cuentas de almacenamiento originales (incluso cuando la máquina virtual tiene discos que se distribuyen entre las cuentas de almacenamiento) cuando se realiza la restauración de una máquina virtual no administrada. Como resultado, las restauraciones serán más rápidas en una amplia variedad de configuraciones de máquina virtual. 
    > [!NOTE] 
    > Si bien, no es lo mismo que reemplazar la máquina virtual original. 
    > 
    >

## <a name="what-is-changing-in-the-new-stack"></a>¿Qué es lo que cambia en la nueva pila?
En la actualidad, el trabajo de copia de seguridad consta de dos fases:
1.  Toma de la instantánea de máquina virtual. 
2.  Transferencia de las instantáneas de máquina virtual al almacén de Azure Backup. 

Un punto de recuperación se considera creado solo después de que se terminan las fases 1 y 2. Como parte de la nueva pila, se crea un punto de recuperación en cuanto se completa la instantánea. También puede restaurar desde este punto de recuperación mediante el mismo flujo de restauración. Este punto de recuperación se puede identificar en Azure Portal usando "instantánea" como tipo de punto de recuperación. Una vez que se transfiere la instantánea al almacén, el tipo de punto de recuperación cambia a "instantánea y almacén". 

![Trabajo de copia de seguridad en la versión 2 de la pila de copia de seguridad de máquinas virtuales](./media/backup-azure-vms/instant-rp-flow.jpg) 

De forma predeterminada, las instantáneas se conservan durante siete días. Como consecuencia, la restauración a partir de estas instantáneas se puede realizar de forma más rápida ya que se reduce el tiempo necesario para volver a copiar los datos desde el almacén hasta la cuenta de almacenamiento del cliente. 

## <a name="considerations-before-upgrade"></a>Consideraciones antes de la actualización
* Esta es una actualización unidireccional de la pila de copia de seguridad de máquinas virtuales. Por lo tanto, todas las futuras copias de seguridad siguen este flujo. Puesto que **está habilitada en el nivel de suscripción, todas las máquinas virtuales siguen este flujo**. Todas las nuevas adiciones de características se basan en la misma pila. La posibilidad de controlar esta actualización en el nivel de directiva estará disponible en futuras versiones. 
* En máquinas virtuales con discos premium, asegúrese de que durante la primera copia de seguridad, y hasta que finalice, haya disponible espacio de almacenamiento equivalente al tamaño de la máquina virtual en la cuenta de almacenamiento. 
* Dado que las instantáneas se almacenan localmente para favorecer la creación de puntos de recuperación y también para acelerar la restauración, verá los costos de almacenamiento correspondientes a las instantáneas durante el período de siete días.
* Las instantáneas incrementales se almacenan como blobs en páginas. A todos los clientes que usen discos no administrados se les cobrará por las instantáneas de siete días que se almacenen en la cuenta de almacenamiento local del cliente. Según el modelo de precios actual, no hay ningún costo para los clientes con Managed Disks.
* Si va a realizar una restauración desde un punto de recuperación de Snapshot en una máquina virtual Premium, verá que se usa una ubicación de almacenamiento temporal mientras se crea la máquina virtual como parte de la restauración. 
* En caso de cuentas de Premium Storage, las instantáneas tomadas para la recuperación instantánea ocuparán el espacio de 10 TB asignado a la cuenta Premium Storage.

## <a name="how-to-upgrade"></a>Instrucciones de actualización
### <a name="the-azure-portal"></a>El Portal de Azure
Si usa Azure Portal, verá una notificación en el panel del almacén relacionada con la compatibilidad con discos de gran tamaño y con las mejoras en la velocidad de la copia de seguridad y la restauración.

![Trabajo de copia de seguridad en la versión 2 de la pila de copia de seguridad de máquinas virtuales](./media/backup-azure-vms/instant-rp-banner.png) 

Para abrir una pantalla para actualizarse a la nueva pila, seleccione el banner. 

![Trabajo de copia de seguridad en la versión 2 de la pila de copia de seguridad de máquinas virtuales](./media/backup-azure-vms/instant-rp.png) 

### <a name="powershell"></a>PowerShell
Ejecute los siguientes cmdlets desde un terminal de PowerShell con privilegios elevados:
1.  Inicie sesión en la cuenta de Azure. 

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

## <a name="verify-whether-the-upgrade-is-complete"></a>Compruebe si la actualización ha finalizado.
Desde un terminal de PowerShell con privilegios elevados, ejecute el siguiente cmdlet:

```
Get-AzureRmProviderFeature -FeatureName "InstantBackupandRecovery" –ProviderNamespace Microsoft.RecoveryServices
```

Si pone Registrado, su suscripción se actualiza a la versión 2 de la pila de copia de seguridad de máquinas virtuales. 



