---
title: "Direcciones IP públicas de Azure | Microsoft Docs"
description: "Aprenda a crear, modificar y eliminar direcciones IP públicas."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: bb71abaf-b2d9-4147-b607-38067a10caf6
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/14/2017
ms.author: jdial
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 9e65a61b2b156611e998f266068ab5e1e306143d
ms.contentlocale: es-es
ms.lasthandoff: 04/27/2017


---

# <a name="public-ip-addresses"></a>Direcciones IP públicas

Información acerca de las direcciones IP públicas y sobre cómo crearlas, cambiarlas y eliminarlas. Una dirección IP pública es un recurso con sus propios valores de configuración. La asignación de una dirección IP pública a otros recursos de Azure habilita:
- La conectividad entrante a Internet para recursos como Azure Virtual Machines, los conjuntos de escalado de máquinas virtuales de Azure, Azure VPN Gateway y Azure Load Balancer con conexión a Internet.
- La conectividad saliente a Internet distinta de las direcciones de red traducidas (NAT). Por ejemplo, una máquina virtual enviar una comunicación a Internet sin tener una dirección IP pública asignada, pero su dirección es la dirección de red traducida de Azure. Para más información acerca de las conexiones salientes de recursos de Azure, consulte el artículo de [descripción de las conexiones salientes](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

En este artículo se explica cómo trabajar con direcciones IP públicas. Este artículo se aplica a los recursos implementados con el modelo de implementación de Azure Resource Manager. Aunque las direcciones IP públicas pueden asignarse a recursos implementados a través del modelo de implementación clásica, las direcciones se aplican de modo distinto al de Resource Manager. Si no está familiarizado con las diferencias entre los dos modelos, consulte el artículo de [descripción de los modelos de implementación de Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

En las secciones restantes de este artículo se enumeran los pasos para completar todas las tareas relacionadas con las direcciones IP públicas. Cada sección contiene:
- Los pasos para completar la tarea en Azure Portal. Para completar los pasos, debe haber iniciado sesión en [Azure Portal](http://portal.azure.com). Regístrese para obtener una [cuenta gratuita](https://azure.microsoft.com/free) si aún no tiene una.
- Comandos para completar la tarea con Azure PowerShell y vínculos a la referencia del comando. Instale y configure PowerShell con los pasos del artículo sobre [cómo instalar y configurar Azure PowerShell](/powershell/azure/overview). Para obtener ayuda con los comandos de PowerShell, con ejemplos, escriba `get-help <command> -full`.
- Comandos para completar la tarea mediante la interfaz de la línea de comandos (CLI) de Azure con vínculos a la referencia del comando. Instale y configure la CLI de Azure siguiendo los pasos del artículo de [instalación y configuración de la CLI de Azure 2.0](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Para obtener ayuda con los comandos de la CLI, escriba `az <command> -h`.

Las direcciones IP públicas tienen un precio simbólico. Para ver los precios, consulte la página [Precios de las direcciones IP](https://azure.microsoft.com/pricing/details/ip-addresses). Existen límites para el número de direcciones IP públicas que se pueden usar dentro de una suscripción. Para ver los límites, consulte el artículo sobre los [límites de Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). 

Complete los pasos descritos en las secciones siguientes para crear, cambiar o eliminar los recursos de dirección IP pública:

## <a name="create"></a>Creación de una dirección IP pública

Para crear una dirección IP pública, complete los pasos siguientes:
1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta que, como mínimo, tenga asignados los permisos del rol de colaborador de red para la suscripción. Lea el artículo [Roles integrados para el control de acceso basado en roles de Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) para más información sobre la asignación de roles y permisos a las cuentas.
2. En el cuadro que contiene el texto *Buscar recursos*, en la parte superior de Azure Portal, escriba *Dirección IP pública*. Haga clic en **Direcciones IP públicas** cuando aparezca en los resultados de búsqueda.
3. Haga clic en **+ Agregar** en la hoja **Dirección IP pública** que aparece.
4. Escriba o seleccione los siguientes valores de configuración en la hoja **Crear dirección IP pública** que aparece y haga clic en **Crear**:

    |**Configuración**|¿Necesario?|**Detalles**|
    |---|---|---|
    |**Name**|Sí|El nombre debe ser único dentro del grupo de recursos que seleccione.|
    |**Asignación de dirección IP**|Sí|**Dinámico:** asigna las direcciones dinámicas solo después de asociar la dirección IP pública a una NIC conectada a una máquina virtual que se inicie por primera vez. Las direcciones dinámicas pueden cambiar si la máquina virtual a la cual está conectada la NIC está detenida (desasignada). La dirección sigue siendo la misma si la máquina virtual se reinicia o se detiene (pero no está desasignada). **Estático:** las direcciones estáticas se asignan cuando se crea la dirección IP pública. Las direcciones estáticas no cambian, aunque la máquina virtual se detenga (estado desasignado). La dirección solo se libera cuando se elimina la NIC. Una vez creada la NIC, puede cambiar el método de asignación.|
    |**Tiempo de espera de inactividad (minutos)**|No|Cantidad de minutos para mantener una conexión TCP o HTTP abierta sin que dependa del envío de mensajes de mantenimiento de los clientes.|
    |**Etiqueta de nombre DNS**|No|Debe ser único dentro de la ubicación de Azure en la que cree el nombre (entre todas las suscripciones y todos los clientes). El servicio DNS público de Azure registra automáticamente el nombre y la dirección IP para que pueda conectarse a un recurso con el nombre. Azure anexa *location.cloudapp.azure.com* (donde location es la ubicación seleccionada) al nombre que proporcione para crear el nombre DNS completo. |
    |**Suscripción**|Sí|Debe existir en la misma suscripción que el recurso al cual desee asociar la dirección IP pública.|
    |**Grupos de recursos**|Sí|Puede existir en la misma suscripción que el grupo de recursos al cual desee asociar la dirección IP pública o en otra.|
    |**Ubicación**|Sí|Debe existir en la misma ubicación que el recurso al cual desee asociar la dirección IP pública.|

|**Herramienta**|**Comando**|
|---|---|
|**CLI**|[az network public-ip-create](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|**PowerShell**|[New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress)|

## <a name="change"></a>Cambio de la configuración o eliminación de una dirección IP pública

Para cambiar o eliminar una dirección IP pública, complete los pasos siguientes:

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta que, como mínimo, tenga asignados los permisos del rol de colaborador de red para la suscripción. Lea el artículo [Roles integrados para el control de acceso basado en roles de Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) para más información sobre la asignación de roles y permisos a las cuentas.
2. En el cuadro que contiene el texto *Buscar recursos*, en la parte superior de Azure Portal, escriba *Dirección IP pública*. Haga clic en **Direcciones IP públicas** cuando aparezca en los resultados de búsqueda.
3. En la hoja **Direcciones IP públicas** que aparece, haga clic en el nombre de la dirección IP pública cuya configuración desee cambiar o eliminar.
4. En la hoja que aparece para la dirección IP pública, complete una de las siguientes opciones en función de si desea eliminar o cambiar la dirección IP pública.
    - **Eliminar:** para eliminar la dirección IP pública, haga clic en **Eliminar** en el la sección **Información general** de la hoja. Si la dirección está actualmente asociada a una configuración de IP, no se puede eliminar. Si la dirección está asociada actualmente con una configuración, haga clic en **Desasociar** para desasociar la dirección de la configuración de IP.
    - **Cambiar:** haga clic en **Configuración**. Cambie la configuración según se indica en el paso 4 de la sección [Crear una dirección IP pública](#create) de este artículo. Para cambiar la asignación de estática a dinámica, primero debe desasociar la dirección IP pública de la configuración de IP a que está asociada. A continuación, puede cambiar el método de asignación a dinámico y hacer clic en **Asociar** para asociar la dirección IP dirección a la misma configuración de IP, una diferente o dejarla desasociada. Para desasociar una dirección IP pública, en la sección **Información general**, haga clic en **Desasociar**.

>[!WARNING]
>Al cambiar el método de asignación de estático a dinámico, perderá la dirección IP que se asignó a la dirección IP pública. Aunque los servidores DNS públicos de Azure mantienen una asignación entre las direcciones estáticas o dinámicas y cualquier etiqueta de nombre DNS (si se han definido), las direcciones IP dinámicas pueden cambiar cuando se inicia la máquina virtual desde el estado detenido (desasignado). Para evitar que la dirección cambie, asigne una dirección IP estática.

|**Herramienta**|**Comando**|
|---|---|
|**CLI**|[az network public-ip update](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#update) para actualizar; [az network public-ip delete](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#delete) para eliminar|
|**PowerShell**|[Set-AzureRmPublicIpAddress](/powershell/resourcemanager/azurerm.network/v3.4.0/set-azurermpublicipaddress?toc=%2fazure%2fvirtual-network%2ftoc.json) para actualizar; [Remove-AzureRmPublicIpAddress](/powershell/module/azurerm.network/remove-azurermpublicipaddress) para eliminar|

## <a name="next-steps"></a>Pasos siguientes
Asignar direcciones IP públicas al crear los siguientes recursos de Azure:

- Máquinas virtuales [Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure Load Balancer accesible desde Internet](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Introducción a Puerta de enlace de aplicaciones](../application-gateway/application-gateway-create-gateway-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Conexión de sitio a sitio con Azure VPN Gateway](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Conjunto de escalado de máquinas virtuales de Azure](../virtual-machine-scale-sets/virtual-machine-scale-sets-portal-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

