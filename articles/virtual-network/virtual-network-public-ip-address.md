---
title: "Creación, modificación o eliminación de una dirección IP pública de Azure | Microsoft Docs"
description: "Obtenga información sobre cómo crear, cambiar o eliminar una dirección IP pública."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: bb71abaf-b2d9-4147-b607-38067a10caf6
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: jdial
ms.openlocfilehash: e52dc76608a83d446ccc8503d17445a8d6a61ae4
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/07/2017
---
# <a name="create-change-or-delete-a-public-ip-address"></a>Creación, modificación o eliminación de una dirección IP pública

Obtenga información sobre una dirección IP pública y cómo crearla, modificarla y eliminarla. Una dirección IP pública es un recurso con sus propios valores de configuración. La asignación de una dirección IP pública a otros recursos de Azure habilita:
- La conectividad entrante a Internet para recursos como Azure Virtual Machines, los conjuntos de escalado de máquinas virtuales de Azure, Azure VPN Gateway, Puertas de enlace de aplicaciones y Azure Load Balancer con conexión a Internet. Los recursos de Azure no pueden recibir comunicación entrante proveniente de Internet si no tienen asignada una dirección IP pública. Si bien algunos de los recursos de Azure son inherentemente accesibles a través de direcciones IP públicas, otros deben tener asignadas direcciones IP públicas para ser accesibles desde Internet.
- La conectividad saliente a Internet usa una dirección IP predecible. Por ejemplo, una máquina virtual puede establecer una comunicación saliente a Internet sin tener asignada una dirección IP pública, pero su dirección es una dirección de red que Azure traduce a una dirección pública impredecible. Asignar una dirección IP pública a un recurso le permite saber cuál es la dirección IP que se usa para la conexión saliente. Si bien la dirección es predecible, puede cambiar en función del método de asignación que se elija. Para más información, consulte [Creación de una dirección IP pública](#create-a-public-ip-address). Para más información acerca de las conexiones salientes de recursos de Azure, consulte el artículo de [descripción de las conexiones salientes](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="before-you-begin"></a>Antes de empezar

Antes de llevar a cabo ningún paso de las secciones de este artículo, realice las siguientes tareas:

- Revise el artículo sobre los [límites de Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) para conocer los límites de las direcciones IP públicas.
- Inicie sesión con una cuenta de Azure en [Azure Portal](https://portal.azure.com), la interfaz de la línea de comandos (CLI) de Azure o Azure PowerShell. Si todavía no tiene una cuenta de Azure, regístrese para obtener una [cuenta de evaluación gratuita](https://azure.microsoft.com/free).
- Si usa comandos de PowerShell para completar las tareas de este artículo, [instale y configure Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json). Asegúrese de que tiene instalada la versión más reciente de los commandlets de Azure PowerShell. Para obtener ayuda con los comandos de PowerShell, con ejemplos, escriba `get-help <command> -full`.
- Si usa comandos de la interfaz de la línea de comandos (CLI) de Azure para completar las tareas de este artículo, [instale y configure la CLI de Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Asegúrese de que tiene instalada la versión más reciente de la CLI de Azure. Para obtener ayuda con los comandos de la CLI, escriba `az <command> --help`. En lugar de instalar la CLI y sus requisitos previos, puede usar Azure Cloud Shell. Azure Cloud Shell es un shell de Bash gratuito que se puede ejecutar directamente en Azure Portal. Tiene la CLI de Azure preinstalada y configurada para utilizar con su cuenta. Para usar Cloud Shell, haga clic en el botón Cloud Shell **>_** que se encuentra en la parte superior del [portal](https://portal.azure.com).

Las direcciones IP públicas tienen un precio simbólico. Para ver los precios, consulte la página [Precios de las direcciones IP](https://azure.microsoft.com/pricing/details/ip-addresses). 

## <a name="create-a-public-ip-address"></a>Crear una dirección IP pública

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta que, como mínimo, tenga asignados los permisos del rol de colaborador de red para la suscripción. Lea el artículo [Roles integrados para el control de acceso basado en roles de Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) para más información sobre la asignación de roles y permisos a las cuentas.
2. En el cuadro que contiene el texto *Buscar recursos*, en la parte superior de Azure Portal, escriba *Dirección IP pública*. Haga clic en **Direcciones IP públicas** cuando aparezca en los resultados de búsqueda.
3. Haga clic en **+ Agregar** en la hoja **Dirección IP pública** que aparece.
4. Escriba o seleccione los siguientes valores de configuración en la hoja **Crear dirección IP pública** que aparece y haga clic en **Crear**:

    |Configuración|¿Necesario?|Detalles|
    |---|---|---|
    |SKU|Sí|Todas las direcciones IP públicas creadas antes de la introducción de SKU son direcciones IP públicas de SKU **básica**.  No se puede cambiar la SKU después de crear la dirección IP pública. Una máquina virtual independiente, las máquinas virtuales dentro de un conjunto de disponibilidad o los conjuntos de escalado de máquinas virtuales pueden usar SKU básicas o estándar.  No se permite la mezcla de SKU entre máquinas virtuales dentro de conjuntos de disponibilidad o conjuntos de escalado. SKU **básico**: si va a crear una dirección IP pública en una región que admite zonas de disponibilidad, el ajuste **Zona disponibilidad** se establece en *Ninguno* de forma predeterminada. Puede seleccionar una zona de disponibilidad para garantizar una zona específica para la dirección IP pública. SKU **estándar** SKU: una dirección IP pública de SKU estándar se puede asociar a una máquina virtual o una front-end de equilibrador de carga. Si va a crear una dirección IP pública en una región que admite zonas de disponibilidad, el ajuste **Zona disponibilidad** se establece en *Redundancia de zona* de forma predeterminada. Para obtener información sobre las zonas de disponibilidad, consulte el ajuste **Zona de disponibilidad**. La SKU estándar es necesaria si se asocia la dirección a un equilibrador de carga estándar. Para obtener más información sobre equilibradores de carga estándar, consulte [Azure load balancer standard SKU](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (SLU estándar para Azure Load Balancer). La SKU estándar se encuentra en versión preliminar. Antes de crear una dirección IP pública de SKU estándar, primero debe completar los pasos descritos de la sección [Registro para la versión preliminar de la SKU estándar](#register-for-the-standard-sku-preview) y crear la dirección IP pública en una ubicación (región) admitida. Para una lista de ubicaciones admitidas, consulte [Region availability](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#region-availability) (Disponibilidad de regiones) y supervise la página [Actualizaciones de Azure](https://azure.microsoft.com/updates/?product=virtual-network) para conocer la compatibilidad con regiones adicionales. Cuando asigna una dirección IP pública de SKU estándar a la interfaz de red de una máquina virtual, debe permitir explícitamente el tráfico previsto con un [grupo de seguridad de red](security-overview.md#network-security-groups). Para evitar que se produzca un error en la comunicación con el recurso, debe crear un grupo de seguridad de red, asociarlo y permitir explícitamente el tráfico deseado.|
    |Nombre|Sí|El nombre debe ser único dentro del grupo de recursos que seleccione.|
    |Versión de la dirección IP|Sí| Seleccione IPv4 o IPv6. Si bien las direcciones IPv4 públicas se pueden asignar a varios recursos de Azure, una dirección IP pública IPv6 solo se puede asignar a un equilibrador de carga accesible desde Internet. El equilibrador de carga puede equilibrar la carga del tráfico IPv6 a máquinas virtuales de Azure. Más información sobre el [equilibrio de carga del tráfico IPv6 a máquinas virtuales](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Si seleccionó la **SKU estándar**, no tiene la opción de seleccionar *IPv6*. Solo puede crear una dirección IPv4 cuando se usa la **SKU estándar**.|
    |Asignación de dirección IP|Sí|**Dinámica:** las direcciones dinámicas solo se asignan después de asociar la dirección IP pública a una interfaz de red asociada a una máquina virtual y que se inicie por primera vez. Las direcciones dinámicas pueden cambiar si la máquina virtual a la cual está asociada la interfaz de red está detenida (desasignada). La dirección sigue siendo la misma si la máquina virtual se reinicia o se detiene (pero no está desasignada). **Estático:** las direcciones estáticas se asignan cuando se crea la dirección IP pública. Las direcciones estáticas no cambian, aunque la máquina virtual se detenga (estado desasignado). La dirección solo se libera cuando se elimina la interfaz de red. Una vez creada la interfaz de red, puede cambiar el método de asignación. Si selecciona *IPv6* como la **versión de dirección IP**, el único método de asignación disponible es *Dinámica*. Si selecciona *estándar* para **SKU**, el método de asignación es *Estático*.|
    |Tiempo de espera de inactividad (minutos)|No|Cantidad de minutos para mantener una conexión TCP o HTTP abierta sin que dependa del envío de mensajes de mantenimiento de los clientes. Si selecciona IPv6 como la **versión de dirección IP**, no se puede cambiar este valor. |
    |Etiqueta de nombre DNS|No|Debe ser único dentro de la ubicación de Azure en la que cree el nombre (entre todas las suscripciones y todos los clientes). Azure registra automáticamente el nombre y la dirección IP en el DNS para que pueda conectarse a un recurso con el nombre. Azure anexa una subred predeterminada, como *location.cloudapp.azure.com* (donde location es la ubicación seleccionada), al nombre que proporcione para crear el nombre DNS completo. Si elige crear ambas versiones de direcciones, el mismo nombre DNS se asigna tanto a la dirección IPv4 como a la IPv6. El DNS predeterminado de Azure contiene registros de nombres AAAA tanto de IPv4 como de IPv6 y responde con ambos registros cuando se busca el nombre DNS. El cliente elige con qué dirección (IPv4 o IPv6) comunicarse. En lugar de usar la etiqueta de nombre DNS con el sufijo predeterminado, o además de ello, puede usar el servicio Azure DNS para configurar un nombre DNS con un sufijo personalizado que se resuelva en la dirección IP pública. Para obtener más información, vea los detalles relativos al [uso de Azure DNS con una dirección IP pública de Azure](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address).|
    |Creación de una dirección IPv6 (o IPv4)|No| Se muestra IPv6 o IPv4, dependiendo de lo que selecciona en la **versión de dirección IP**. Por ejemplo, si selecciona **IPv4** en la **versión de dirección IP**, aquí se muestra **IPv6**. Si selecciona *Estándar* para **SKU**, no tiene la opción de crear una dirección IPv6.
    |Nombre (solo es visible si activó la casilla **Crear una dirección IPv6 (o IPv4)**)|Sí, si selecciona la casilla **Crear una dirección IPv6** (o IPv4).|El nombre debe ser distinto del nombre que escribe para el **nombre** de esta lista. Si elige crear tanto una dirección IPv4 como IPv6, el portal crea dos recursos de direcciones IP públicas independientes, uno con cada versión de dirección IP asignada.|
    |Asignación de direcciones IP (solo está visible si activó la casilla **Crear una dirección IPv6 (o IPv4)**)|Sí, si selecciona la casilla **Crear una dirección IPv6** (o IPv4).|Si la casilla indica **Crear una dirección IPv4**, puede seleccionar un método de asignación. Si la casilla indica **Crear una dirección IPv6**, no puede seleccionar un método de asignación, porque debe ser **Dinámico**.|
    |La suscripción|Sí|Debe existir en la misma [suscripción](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) que el recurso al cual desee asociar la dirección IP pública.|
    |Grupos de recursos|Sí|Puede existir en la misma suscripción que el [grupo de recursos](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) al cual desee asociar la dirección IP pública o en otra diferente.|
    |La ubicación|Sí|Debe existir en la misma [ubicación](https://azure.microsoft.com/regions), a la que también se hace referencia como región, que el recurso al que desea asociar la dirección IP pública.|
    |Zona de disponibilidad| No | Este ajuste solo aparece si selecciona una ubicación admitida. Para una lista de ubicaciones admitidas, consulte [Introducción a las zonas de disponibilidad](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Las zonas de disponibilidad están actualmente en versión preliminar. Antes de seleccionar una opción con zona o redundancia de zona, primero debe completar los pasos descritos en el [registro para la versión preliminar de zonas de disponibilidad](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#get-started-with-the-availability-zones-preview). Si seleccionó la SKU **básica**, la opción *Ninguna* se selecciona automáticamente. Si prefiere garantizar una zona específica, puede seleccionarla. Cualquiera de las opciones no tiene redundancia de zona. Si seleccionó la SKU **estándar**: la redundancia de zona se selecciona automáticamente y hace que la ruta de acceso de datos sea resistente a errores de zona. Si prefiere garantizar una zona específica, que no sea resistente a errores de zona, puede seleccionarla.
  

**Comandos**

Si bien el portal proporciona la opción de crear dos recursos de direcciones IP públicas (una IPv4 y una IPv6), los comandos de PowerShell y la CLI siguientes crean un recurso con una dirección para una versión de dirección IP o la otra. Si desea dos recursos de direcciones IP públicas, uno para cada versión de dirección IP, debe ejecutar dos veces el comando y especificar distintos nombres y versiones para los recursos de direcciones IP públicas. 

|Herramienta|Comando|
|---|---|
|CLI|[az network public-ip create](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress)|

## <a name="view-change-settings-for-or-delete-a-public-ip-address"></a>Visualización, cambio de la configuración o eliminación de una dirección IP pública

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta que, como mínimo, tenga asignados los permisos del rol de colaborador de red para la suscripción. Lea el artículo [Roles integrados para el control de acceso basado en roles de Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) para más información sobre la asignación de roles y permisos a las cuentas.
2. En el cuadro que contiene el texto *Buscar recursos*, en la parte superior de Azure Portal, escriba *Dirección IP pública*. Haga clic en **Direcciones IP públicas** cuando aparezca en los resultados de búsqueda.
3. En la hoja **Direcciones IP públicas** que aparece, haga clic en el nombre de la dirección IP pública cuya configuración desee ver, cambiar o eliminar.
4. En la hoja que aparece para la dirección IP pública, complete una de las siguientes opciones en función de si desea ver, eliminar o cambiar la dirección IP pública.
    - **Visualización**: la sección **Información general** de la hoja muestra la configuración clave de la dirección IP pública, como la interfaz de red a la que está asociada (si la dirección está asociada a una interfaz de red). El portal no muestra la versión de la dirección (IPv4 o IPv6). Para ver información sobre la versión, use el comando de la CLI o PowerShell para ver la dirección IP pública. Si la versión de la dirección IP es IPv6, ni el portal, ni PowerShell ni la CLI muestran la dirección asignada. 
    - **Eliminación**: para eliminar la dirección IP pública, haga clic en **Eliminar** en la sección **Información general** de la hoja. Si la dirección está actualmente asociada a una configuración de IP, no se puede eliminar. Si la dirección está asociada actualmente con una configuración, haga clic en **Desasociar** para desasociar la dirección de la configuración de IP.
    - **Cambio**: haga clic en **Configuración**. Cambie la configuración según se indica en el paso 4 de la sección [Crear una dirección IP pública](#create-a-public-ip-address) de este artículo. Para cambiar la asignación de una dirección IPv4 de estática a dinámica, primero debe desasociar la dirección IPv4 pública de la configuración de IP a que está asociada. A continuación, puede cambiar el método de asignación a dinámico y hacer clic en **Asociar** para asociar la dirección IP dirección a la misma configuración de IP, una diferente o dejarla desasociada. Para desasociar una dirección IP pública, en la sección **Información general**, haga clic en **Desasociar**.

>[!WARNING]
>Al cambiar el método de asignación de estático a dinámico, perderá la dirección IP que se asignó a la dirección IP pública. Aunque los servidores DNS públicos de Azure mantienen una asignación entre las direcciones estáticas o dinámicas y cualquier etiqueta de nombre DNS (si se han definido), las direcciones IP dinámicas pueden cambiar cuando se inicia la máquina virtual desde el estado detenido (desasignado). Para evitar que la dirección cambie, asigne una dirección IP estática.

**Comandos**

|Herramienta|Comando|
|---|---|
|CLI|[az network public-ip-list](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#list) para mostrar las direcciones IP públicas, [az network public-ip-show](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#show) para mostrar la configuración, [az network public-ip update](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#update) para actualizar, [az network public-ip delete](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#delete) para eliminar|
|PowerShell|[Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress?toc=%2fazure%2fvirtual-network%2ftoc.json) para recuperar un objeto de dirección IP pública y ver su configuración, [Set-AzureRmPublicIpAddress](/powershell/resourcemanager/azurerm.network/set-azurermpublicipaddress?toc=%2fazure%2fvirtual-network%2ftoc.json) para actualizar la configuración, [Remove-AzureRmPublicIpAddress](/powershell/module/azurerm.network/remove-azurermpublicipaddress) para eliminar|

## <a name="register-for-the-standard-sku-preview"></a>Registro para la versión preliminar de la SKU estándar

> [!NOTE]
> Las características que se encuentran en versión preliminar pueden no tener el mismo nivel de disponibilidad y confiabilidad que las características que se encuentran en la versión de disponibilidad general. Las características de la versión preliminar no se admiten, pueden tener funcionalidades limitadas y pueden no estar disponibles en todas las ubicaciones de Azure. 

Para poder crear una dirección IP pública de SKU estándar, primero debe registrarse para la versión preliminar. Complete los pasos siguientes para registrarse para la versión preliminar:

1. Instale y configure [Azure PowerShell](/powershell/azure/install-azurerm-ps).
2. Ejecute el comando `Get-Module -ListAvailable AzureRM` para ver qué versión del módulo AzureRM ha instalado. Debe tener la versión 4.4.0 o superior instalada. Si no lo hace, puede instalar la versión más reciente de la [Galería de PowerShell](https://www.powershellgallery.com/packages/AzureRM).
3. Inicie sesión en Azure con el comando `login-azurermaccount`.
4. Escriba el siguiente comando para registrarse para la versión preliminar:
   
    ```powershell
    Register-AzureRmProviderFeature -FeatureName AllowLBPreview -ProviderNamespace Microsoft.Network
    ```

5. Escriba el siguiente comando para confirmar que está registrado para la versión preliminar:

    ```powershell
    Get-AzureRmProviderFeature -FeatureName AllowLBPreview -ProviderNamespace Microsoft.Network
    ```

## <a name="next-steps"></a>Pasos siguientes
Asignar direcciones IP públicas al crear los siguientes recursos de Azure:

- Máquinas virtuales [Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure Load Balancer accesible desde Internet](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Introducción a Puerta de enlace de aplicaciones](../application-gateway/application-gateway-create-gateway-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Conexión de sitio a sitio con Azure VPN Gateway](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Conjunto de escalado de máquinas virtuales de Azure](../virtual-machine-scale-sets/virtual-machine-scale-sets-portal-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
