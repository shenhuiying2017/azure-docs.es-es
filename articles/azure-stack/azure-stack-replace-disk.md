---
title: Reemplazar un disco físico en Azure Stack | Microsoft Docs
description: Se describe el proceso de cómo reemplazar un disco físico en Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 449ae53e-b951-401a-b2c9-17fee2f491f1
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2018
ms.author: mabrigg
ms.openlocfilehash: f168c005c729ae75a5369b80b3dc5eab03ee0243
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/03/2018
ms.locfileid: "30311328"
---
# <a name="replace-a-physical-disk-in-azure-stack"></a>Reemplazar un disco físico en Azure Stack

*Se aplica a: sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

En este artículo se describe el proceso general para reemplazar un disco físico en Azure Stack. Si se produce un error en un disco físico, deberá reemplazarlo tan pronto como sea posible.

Puede usar este procedimiento para sistemas integrados y para implementaciones del kit de desarrollo que tienen discos intercambiables en caliente.

Los pasos para reemplazar el disco varían en función del fabricante del hardware del fabricante de equipos originales (OEM). Consulte la documentación de Field Replaceable Unit (FRU) del proveedor para obtener pasos detallados específicos para su sistema. 

## <a name="review-disk-alert-information"></a>Revisar la información de las alertas de disco
Cuando se produce un error en un disco, recibirá una alerta que indica que se perdió la conectividad a un disco físico. 

 ![Alerta que muestra que se perdió la conectividad al disco físico](media/azure-stack-replace-disk/DiskAlert.png)

Si abre la alerta, la descripción de la misma contiene el nodo de la unidad de escalado y la ubicación exacta del espacio físico del disco que debe reemplazar. Azure Stack también le ayuda a identificar el disco erróneo utilizando las funcionalidades del indicador LED.

 ## <a name="replace-the-disk"></a>Reemplazar el disco

Siga las instrucciones de FRU del fabricante de hardware del OEM para reemplazar el disco actual.

> [!note]
> Reemplace los discos para un nodo de la unidad de escalado de una vez. Espere a que finalicen los trabajos de reparación de los discos antes de pasar al siguiente nodo de la unidad de escalado.

Para evitar el uso de un disco no compatible en un sistema integrado, el sistema bloquea los discos que no son compatibles con el proveedor. Si intenta usar un disco no compatible, una nueva alerta le indica que un disco se ha puesto en cuarentena debido a un modelo no admitido o al firmware.

Después de reemplazar el disco, Azure Stack descubre automáticamente el nuevo disco e inicia el proceso de reparación del disco virtual.  
 
 ## <a name="check-the-status-of-virtual-disk-repair"></a>Comprobar el estado de reparación del disco virtual
 
 Después de reemplazar el disco, puede supervisar el estado de mantenimiento del disco virtual y el progreso del trabajo de reparación con el punto de conexión con privilegios. Siga estos pasos desde cualquier equipo que tenga conectividad de red con el punto de conexión con privilegios.

1. Abra una sesión de Windows PowerShell y conéctese al punto de conexión con privilegios.
    ````PowerShell
        $cred = Get-Credential
        Enter-PSSession -ComputerName <IP_address_of_ERCS>`
          -ConfigurationName PrivilegedEndpoint -Credential $cred
    ```` 
  
2. Ejecute el siguiente comando para ver el mantenimiento del disco virtual:
    ````PowerShell
        Get-VirtualDisk -CimSession s-cluster
    ````
   ![Salida de PowerShell del comando Get-VirtualDisk](media/azure-stack-replace-disk/GetVirtualDiskOutput.png)

3. Ejecute el comando siguiente para ver el estado del trabajo de almacenamiento actual:
    ```PowerShell
        Get-VirtualDisk -CimSession s-cluster | Get-StorageJob
    ````
      ![Salida de PowerShell del comando Get-StorageJob](media/azure-stack-replace-disk/GetStorageJobOutput.png)

## <a name="troubleshoot-virtual-disk-repair"></a>Solución de problemas de la reparación de disco

Si el trabajo de reparación del disco virtual parece que no avanza, ejecute el siguiente comando para reiniciar el trabajo:
  ````PowerShell
        Get-VirtualDisk -CimSession s-cluster | Repair-VirtualDisk
  ```` 
