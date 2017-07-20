---
title: "Creación, modificación o eliminación de direcciones IP públicas de Azure | Microsoft Docs"
description: "Aprenda a crear, modificar o eliminar direcciones IP públicas."
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
ms.date: 05/05/2017
ms.author: jdial
ms.translationtype: Human Translation
ms.sourcegitcommit: 7948c99b7b60d77a927743c7869d74147634ddbf
ms.openlocfilehash: 47237fe499cd9244266487cd97f6be0d2cb2e977
ms.contentlocale: es-es
ms.lasthandoff: 06/20/2017


---

# <a name="create-change-or-delete-public-ip-addresses"></a>Creación, modificación o eliminación de direcciones IP públicas

Información acerca de las direcciones IP públicas y sobre cómo crearlas, cambiarlas y eliminarlas. Una dirección IP pública es un recurso con sus propios valores de configuración. La asignación de una dirección IP pública a otros recursos de Azure habilita:
- La conectividad entrante a Internet para recursos como Azure Virtual Machines, los conjuntos de escalado de máquinas virtuales de Azure, Azure VPN Gateway y Azure Load Balancer con conexión a Internet.
- La conectividad saliente a Internet distinta de las direcciones de red traducidas (NAT). Por ejemplo, una máquina virtual enviar una comunicación a Internet sin tener una dirección IP pública asignada, pero su dirección es la dirección de red traducida de Azure. Para más información acerca de las conexiones salientes de recursos de Azure, consulte el artículo de [descripción de las conexiones salientes](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

En este artículo se explica cómo trabajar con direcciones IP públicas implementadas a través del modelo de implementación de Azure Resource Manager.

## <a name="before"></a>Antes de empezar

Antes de llevar a cabo ningún paso de las secciones de este artículo, realice las siguientes tareas:

- Revise el artículo sobre los [límites de Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) para conocer los límites de las direcciones IP públicas.
- Inicie sesión en el portal de Azure, la interfaz de la línea de comandos (CLI) o Azure PowerShell con una cuenta de Azure. Si todavía no tiene una cuenta de Azure, regístrese para obtener una [cuenta de evaluación gratuita](https://azure.microsoft.com/free).
- Si va a usar comandos de PowerShell para realizar las tareas de este artículo, instale y configure Azure PowerShell mediante los pasos del artículo de [instalación y configuración de Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json). Asegúrese de que tiene instalada la versión más reciente de los commandlets de Azure PowerShell. Para obtener ayuda con los comandos de PowerShell, con ejemplos, escriba `get-help <command> -full`.
- Si va a usar comandos de la interfaz de la línea de comandos (CLI) de Azure para realizar las tareas de este artículo, instale y configure la CLI de Azure mediante los pasos del artículo de [instalación y configuración de la CLI de Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Asegúrese de que tiene instalada la versión más reciente de la CLI de Azure. Para obtener ayuda con los comandos de la CLI, escriba `az <command> --help`.

Las direcciones IP públicas tienen un precio simbólico. Para ver los precios, consulte la página [Precios de las direcciones IP](https://azure.microsoft.com/pricing/details/ip-addresses). 

## <a name="create"></a>Creación de una dirección IP pública

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta que, como mínimo, tenga asignados los permisos del rol de colaborador de red para la suscripción. Lea el artículo [Roles integrados para el control de acceso basado en roles de Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) para más información sobre la asignación de roles y permisos a las cuentas.
2. En el cuadro que contiene el texto *Buscar recursos*, en la parte superior de Azure Portal, escriba *Dirección IP pública*. Haga clic en **Direcciones IP públicas** cuando aparezca en los resultados de búsqueda.
3. Haga clic en **+ Agregar** en la hoja **Dirección IP pública** que aparece.
4. Escriba o seleccione los siguientes valores de configuración en la hoja **Crear dirección IP pública** que aparece y haga clic en **Crear**:

    |Configuración|¿Necesario?|Detalles|
    |---|---|---|
    |Nombre|Sí|El nombre debe ser único dentro del grupo de recursos que seleccione.|
    |Asignación de dirección IP|Sí|**Dinámico:** asigna las direcciones dinámicas solo después de asociar la dirección IP pública a una NIC conectada a una máquina virtual que se inicie por primera vez. Las direcciones dinámicas pueden cambiar si la máquina virtual a la cual está conectada la NIC está detenida (desasignada). La dirección sigue siendo la misma si la máquina virtual se reinicia o se detiene (pero no está desasignada). **Estático:** las direcciones estáticas se asignan cuando se crea la dirección IP pública. Las direcciones estáticas no cambian, aunque la máquina virtual se detenga (estado desasignado). La dirección solo se libera cuando se elimina la NIC. Una vez creada la NIC, puede cambiar el método de asignación.|
    |Tiempo de espera de inactividad (minutos)|No|Cantidad de minutos para mantener una conexión TCP o HTTP abierta sin que dependa del envío de mensajes de mantenimiento de los clientes.|
    |Etiqueta de nombre DNS|No|Debe ser único dentro de la ubicación de Azure en la que cree el nombre (entre todas las suscripciones y todos los clientes). El servicio DNS público de Azure registra automáticamente el nombre y la dirección IP para que pueda conectarse a un recurso con el nombre. Azure anexa *location.cloudapp.azure.com* (donde location es la ubicación seleccionada) al nombre que proporcione para crear el nombre DNS completo.|
    |La suscripción|Sí|Debe existir en la misma [suscripción](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) que el recurso al cual desee asociar la dirección IP pública.|
    |Grupos de recursos|Sí|Puede existir en la misma suscripción que el [grupo de recursos](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) al cual desee asociar la dirección IP pública o en otra diferente.|
    |La ubicación|Sí|Debe existir en la misma [ubicación](https://azure.microsoft.com/regions), a la que también se hace referencia como región, que el recurso al que desea asociar la dirección IP pública.|

**Comandos**

|Herramienta|Comando|
|---|---|
|CLI|[az network public-ip-create](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress)|

## <a name="change"></a>Visualización, cambio de la configuración o eliminación de una dirección IP pública

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta que, como mínimo, tenga asignados los permisos del rol de colaborador de red para la suscripción. Lea el artículo [Roles integrados para el control de acceso basado en roles de Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) para más información sobre la asignación de roles y permisos a las cuentas.
2. En el cuadro que contiene el texto *Buscar recursos*, en la parte superior de Azure Portal, escriba *Dirección IP pública*. Haga clic en **Direcciones IP públicas** cuando aparezca en los resultados de búsqueda.
3. En la hoja **Direcciones IP públicas** que aparece, haga clic en el nombre de la dirección IP pública cuya configuración desee ver, cambiar o eliminar.
4. En la hoja que aparece para la dirección IP pública, complete una de las siguientes opciones en función de si desea eliminar o cambiar la dirección IP pública.
    - **Visualización**: la sección Información general de la hoja muestra la configuración clave de la dirección IP pública, como la interfaz de red a la que está asociada (si la dirección está asociada a una interfaz de red).
    - **Eliminación**: para eliminar la dirección IP pública, haga clic en **Eliminar** en la sección **Información general** de la hoja. Si la dirección está actualmente asociada a una configuración de IP, no se puede eliminar. Si la dirección está asociada actualmente con una configuración, haga clic en **Desasociar** para desasociar la dirección de la configuración de IP.
    - **Cambio**: haga clic en **Configuración**. Cambie la configuración según se indica en el paso 4 de la sección [Crear una dirección IP pública](#create) de este artículo. Para cambiar la asignación de estática a dinámica, primero debe desasociar la dirección IP pública de la configuración de IP a que está asociada. A continuación, puede cambiar el método de asignación a dinámico y hacer clic en **Asociar** para asociar la dirección IP dirección a la misma configuración de IP, una diferente o dejarla desasociada. Para desasociar una dirección IP pública, en la sección **Información general**, haga clic en **Desasociar**.

>[!WARNING]
>Al cambiar el método de asignación de estático a dinámico, perderá la dirección IP que se asignó a la dirección IP pública. Aunque los servidores DNS públicos de Azure mantienen una asignación entre las direcciones estáticas o dinámicas y cualquier etiqueta de nombre DNS (si se han definido), las direcciones IP dinámicas pueden cambiar cuando se inicia la máquina virtual desde el estado detenido (desasignado). Para evitar que la dirección cambie, asigne una dirección IP estática.

**Comandos**

|Herramienta|Comando|
|---|---|
|CLI|[az network public-ip-list](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#list) para mostrar las direcciones IP públicas, [az network public-ip-show](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#show) para mostrar la configuración, [az network public-ip update](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#update) para actualizar, [az network public-ip delete](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#delete) para eliminar|
|PowerShell|[Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress?toc=%2fazure%2fvirtual-network%2ftoc.json) para recuperar un objeto de dirección IP pública y ver su configuración, [Set-AzureRmPublicIpAddress](/powershell/resourcemanager/azurerm.network/set-azurermpublicipaddress?toc=%2fazure%2fvirtual-network%2ftoc.json) para actualizar la configuración, [Remove-AzureRmPublicIpAddress](/powershell/module/azurerm.network/remove-azurermpublicipaddress) para eliminar|

## <a name="next-steps"></a>Pasos siguientes
Asignar direcciones IP públicas al crear los siguientes recursos de Azure:

- Máquinas virtuales [Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure Load Balancer accesible desde Internet](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Introducción a Puerta de enlace de aplicaciones](../application-gateway/application-gateway-create-gateway-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Conexión de sitio a sitio con Azure VPN Gateway](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Conjunto de escalado de máquinas virtuales de Azure](../virtual-machine-scale-sets/virtual-machine-scale-sets-portal-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

