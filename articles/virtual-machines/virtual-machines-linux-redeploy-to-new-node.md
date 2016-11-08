---
title: Nueva implementación de máquinas virtuales Linux | Microsoft Docs
description: Se describe cómo volver a implementar máquinas virtuales Linux para solucionar problemas de conexión SSH.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
tags: azure-resource-manager,top-support-issue

ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/19/2016
ms.author: iainfou

---
# Nueva implementación de la máquina virtual en un nuevo nodo de Azure
Si ha estado teniendo dificultades para solucionar los problemas de acceso de SSH o de las aplicaciones a una máquina virtual (VM) de Azure, puede que le ayude volverla a implementar. Cuando se vuelve a implementar una máquina virtual, se mueve a otro nodo dentro de la infraestructura de Azure y después se vuelve a conectar, conservando todas las opciones de configuración y los recursos asociados. En este artículo se muestra cómo volver a implementar una máquina virtual con la CLI de Azure o el Portal de Azure.

> [!NOTE]
> Después de volver a implementar una máquina virtual, se perderá el disco temporal y se actualizarán las direcciones IP dinámicas asociadas con la interfaz de red virtual.
> 
> 

## Uso de la CLI de Azure
Asegúrese de que tiene instalada la [CLI de Azure más reciente](../xplat-cli-install.md) en su equipo y que usa el modo de implementación de Resource Manager (`azure config mode arm`).

Para volver a implementar la máquina virtual, utilice el siguiente comando de la CLI de Azure:

```bash
azure vm redeploy --resourcegroup <resourcegroup> --vm-name <vmname> 
```

Puede ver el estado del cambio de máquina virtual mientras recorre el proceso de nueva implementación. El elemento `PowerState` de la máquina virtual pasará de En ejecución a Actualizando, luego Iniciando y finalmente En ejecución mientras se pasa por el proceso de nueva implementación en otro host. Compruebe el estado de las máquinas virtuales dentro de un grupo de recursos con:

```bash
azure vm list -g <resourcegroup>
```


[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## Pasos siguientes
Puede encontrar ayuda específica sobre la [solución de problemas de las conexiones SSH](virtual-machines-linux-troubleshoot-ssh-connection.md) o [pasos detallados para solucionar problemas de SSH](virtual-machines-linux-detailed-troubleshoot-ssh-connection.md) si tiene problemas para conectarse a la máquina virtual. También puede leer sobre la [solución de problemas de aplicaciones](virtual-machines-linux-troubleshoot-app-connection.md) si no se puede acceder a una aplicación que se ejecuta en la máquina virtual.

<!---HONumber=AcomDC_0921_2016-->