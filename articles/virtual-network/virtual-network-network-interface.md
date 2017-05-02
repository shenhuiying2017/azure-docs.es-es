---
title: Interfaces de red de Azure | Microsoft Docs
description: "Obtenga información acerca de cómo crear y eliminar interfaces de red (NIC) y asignar direcciones IP públicas y privadas a las NIC. Obtenga información acerca de cómo asociar y desasociar NIC de máquinas virtuales de Azure."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/14/2017
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: c300ba45cd530e5a606786aa7b2b254c2ed32fcd
ms.openlocfilehash: e2d70bbb2af4231a9ba7e4d9a843593ff5d9f7d3
ms.lasthandoff: 04/14/2017


---

# <a name="network-interfaces"></a>Interfaces de red

Obtenga información sobre las interfaces de red (NIC) y cómo trabajar con ellas. Una NIC es la interconexión entre una máquina virtual (VM) de Azure y la red de software subyacente. La siguiente imagen ilustra la funcionalidad que ofrece una NIC:

![Interfaz de red](./media/virtual-network-network-interface/nic.png)

En este artículo se explica cómo trabajar con los conceptos que se muestran en la imagen. Haga clic en cualquiera de los siguientes conceptos para ir directamente a esa sección del artículo:

- [Interfaces de red](#nics): una NIC está conectada a una subred dentro de una red virtual de Azure (VNet). En la imagen, **VM1** tiene asociadas dos NIC y **VM2** tiene asociada una NIC. Ambas NIC están conectadas a la misma red virtual, pero a subredes diferentes. En esta sección se explica cómo enumerar las NIC existentes y cómo crear, cambiar y eliminar NIC.
- [Configuraciones de IP](#ip-configs): cada NIC tiene asociadas una o varias configuraciones de IP. Cada configuración de IP tiene asignada una dirección IP privada. Una configuración de IP puede tener una dirección IP pública. En la imagen, **NIC1** y **NIC3** tienen asociada una configuración de IP cada una, mientras que **NIC2** tiene asociadas dos configuraciones de IP. La configuración de IP asignada a NIC1 y NIC3 tiene asignadas direcciones IP públicas, mientras que ninguna de las configuraciones de IP asignadas a NIC2 tiene asignada una dirección IP pública. En esta sección se explica cómo crear, cambiar y eliminar configuraciones de IP con direcciones IP privadas asignadas con métodos de asignación estático y dinámico. En esta sección también se indican los pasos necesarios para asociar y desasociar direcciones IP públicas hacia y desde una configuración de IP.
- [Grupos de seguridad de red](#nsgs): los grupos de seguridad de red (NSG) contienen una o varias reglas de seguridad entrante o saliente. Las reglas controlan el tipo de tráfico de red que puede entrar y salir de una interfaz de red, una subred o ambos. En la imagen, **NIC1** y **NIC3** tienen asociado un NSG, pero **NIC2** no. En esta sección se indican los pasos necesarios para ver los NSG que se aplican a una NIC, agregar un NSG a una NIC y quitar un NSG de una NIC.
- [Máquinas virtuales](#vms): una máquina virtual tiene asociada al menos una NIC, pero puede haber varias NIC asociadas, según el tamaño de la máquina virtual. Para ver cuántas NIC admite cada tamaño de máquina virtual, consulte los artículos sobre el tamaño de las máquinas virtuales [Windows](../virtual-machines/windows/sizes.md) o [Linux](../virtual-machines/linux/sizes.md). En esta sección se proporcionan los pasos para crear máquinas virtuales con una o varias NIC, así como asociar y desasociar NIC hacia y desde las máquinas virtuales existentes.

Si no está familiarizado con las NIC y las máquinas virtuales de Azure, se recomienda completar el ejercicio que encontrará en [Creación de su primera red virtual de Azure](virtual-network-get-started-vnet-subnet.md) antes de leer este artículo. El ejercicio le ayudará a familiarizarse con las redes virtuales y las máquinas virtuales.

Este artículo se aplica a las redes virtuales y a las NIC creadas con el modelo de implementación de Azure Resource Manager. Microsoft recomienda crear recursos mediante el modelo de implementación de Resource Manager en lugar del modelo de implementación clásico. Si no está familiarizado con las diferencias entre los dos modelos, consulte el artículo de [descripción de los modelos de implementación de Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Las secciones restantes de este artículo indican los pasos para completar todas las tareas relacionadas con las NIC. Cada sección contiene:
- Los pasos para completar la tarea en Azure Portal. Para completar los pasos, debe haber iniciado sesión en [Azure Portal](http://portal.azure.com). Regístrese para obtener una [cuenta gratuita](https://azure.microsoft.com/free) si aún no tiene una.
- Comandos para completar la tarea con Azure PowerShell y vínculos a la referencia del comando. Instale y configure PowerShell con los pasos del artículo sobre [cómo instalar y configurar Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json). Para obtener ayuda con los comandos de PowerShell, con ejemplos, escriba `get-help <command> -full`.
- Comandos para completar la tarea mediante la interfaz de la línea de comandos (CLI) de Azure con vínculos a la referencia del comando. Instale y configure la CLI de Azure siguiendo los pasos del artículo de [instalación y configuración de la CLI de Azure 2.0](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Para obtener ayuda con los comandos de la CLI, escriba `az <command> -h`.

## <a name="nics"></a>Interfaces de red
Complete los pasos descritos en las secciones siguientes para crear, ver, cambiar y eliminar la configuración y las interfaces de red:

### <a name="create-nic"></a>Creación de una interfaz de red

Las NIC se pueden asociar a una máquina virtual o pueden existir por sí mismas. Para más información sobre cómo asociar una NIC a una máquina virtual, lea la sección [Asociación de una NIC a una máquina virtual](#vm-attach-nic) en este artículo.

Para crear una NIC, complete los siguientes pasos:

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta que, como mínimo, tenga asignados los permisos del rol de colaborador de red para la suscripción. Lea el artículo [Roles integrados para el control de acceso basado en roles de Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) para más información sobre la asignación de roles y permisos a las cuentas.
2. En el cuadro que contiene el texto *Buscar recursos*, en la parte superior de Azure Portal, escriba *interfaces de red*. Cuando **interfaces de red** aparezca en los resultados de búsqueda, haga clic en él.
3. En la hoja **Interfaces de red** que aparece, haga clic en **+Agregar**.
4. En la hoja **Crear interfaz de red** que aparece, escriba o seleccione los valores de las siguientes opciones y, después, haga clic en **Crear**:

    |**Configuración**|**¿Necesario?**|**Detalles**|
    |---|---|---|
    |**Name**|Sí|El nombre no se puede cambiar una vez creada la NIC. El nombre debe ser único dentro del grupo de recursos que seleccione. Lea el artículo [Convenciones de nomenclatura](/azure/architecture/best-practices/naming-conventions?toc=%2fazure%2fvirtual-network%2ftoc.json#naming-rules-and-restrictions) para obtener sugerencias de nombres.|
    |**Red virtual**|Sí|Una NIC solo puede conectarse a una red virtual que exista en la misma ubicación y suscripción en la que se encuentre la NIC. La máquina virtual a la que se conecta la NIC debe existir en la misma ubicación y suscripción que la NIC. Si no aparece ninguna red virtual en la lista, debe crear una. Para crear una red virtual, complete los pasos del artículo [Red virtual](virtual-networks-create-vnet-arm-pportal.md). Una vez creada una NIC, no se puede cambiar la red virtual a la que está conectada.|
    |**Subred**|Sí|Seleccione una subred dentro de la red virtual que seleccionó. Puede cambiar la subred a la que está conectada la NIC después de crearla.|
    |**Asignación de direcciones IP privadas**|Sí| El servidor DHCP de Azure asigna una dirección IP privada a la NIC cuando se crea. El servidor DHCP asigna una dirección disponible del intervalo de direcciones de subred definido para la subred a la que se conecta la NIC. **Dinámico:** Azure puede asignar una dirección diferente a una NIC cuando se inicia la máquina virtual a la que está asociada después de estar en estado detenido (desasignado). La dirección sigue siendo la misma si la máquina virtual se reinicia y no estuvo en estado detenido (desasignado). **Estático:** las direcciones estáticas no cambian hasta que se cambian o se elimina la NIC. Una vez creada la NIC, puede cambiar el método de asignación.|
    |**Grupo de seguridad de red**|No|Los grupos de seguridad de red permiten controlar el flujo de tráfico de red hacia y desde una NIC. Consulte el artículo [Grupos de seguridad de red](virtual-networks-nsg.md) para más información sobre los NSG. Puede aplicar un grupo de seguridad de red (NSG), o ninguno, a una NIC. También puede aplicar un NSG, o ninguno, a la subred a la que está conectada la NIC. Cuando se aplica un NSG a una NIC y a la subred a la que está conectada, a veces se producen resultados inesperados. Para solucionar los problemas de los grupos de seguridad de red aplicados a las NIC, lea el artículo [Solución de problemas de los grupos de seguridad de red](virtual-network-nsg-troubleshoot-portal.md#view-effective-security-rules-for-a-network-interface).|
    |**Suscripción**|Sí| La máquina virtual a la que se asocia una NIC y la red virtual a la que se conecta deben encontrarse en la misma suscripción.|
    |**Grupos de recursos**|Sí| La NIC puede existir en el mismo o en diferentes grupos de recursos que la máquina virtual a la que está asociada, o la red virtual a la que está conectada.|
    |**Ubicación**|Sí|La máquina virtual a la que se asocia una NIC y la red virtual a la que se conecta deben encontrarse en la misma ubicación.|

Azure Portal crea una configuración de IP principal llamada **ipconfig1**, con una dirección IP privada dinámica, y la asocia a la NIC que cree. Para más información sobre configuraciones de IP, lea la sección [Configuraciones de IP](#ip-configs) en este artículo. No se puede especificar el nombre de la configuración de IP que el portal crea, asignar una dirección IP privada estática ni asignar una dirección IP pública al crear la NIC. Si crea la NIC con PowerShell o la CLI, puede especificar el nombre de la configuración de IP, una dirección IP estática y asignar una dirección IP pública. Puede cambiar el método de asignación de direcciones IP privadas y si una dirección IP pública se asociará a la NIC una vez creada la NIC. Para cambiar la configuración después de crea una NIC, siga los pasos de la sección [Cambio de una configuración de IP](#change-ip-config), en este artículo.

>[!Note]
> Azure asigna una dirección MAC a la NIC solo después de asociar la NIC a una máquina virtual y después de iniciar la máquina virtual por primera vez. No se puede especificar la dirección MAC que Azure asigna a la NIC. La dirección MAC permanece asignada a la NIC hasta que la NIC se elimina o se cambia la dirección IP privada asignada a la configuración de IP principal de la NIC principal. Para más información sobre configuraciones de IP, lea la sección [Configuraciones de IP](#ip-configs) en este artículo.

|**Herramienta**|**Comando**|
|:---|:---|
|**CLI**|[az network nic create](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|**PowerShell**|[New-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.4.0/new-azurermnetworkinterface/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|

### <a name="view-nics"></a>Visualización y cambio de la configuración y las interfaces de red

Para ver y cambiar la configuración y las interfaces de red, complete los pasos siguientes:

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta que, como mínimo, tenga asignados los permisos del rol de colaborador de red para la suscripción. Lea el artículo [Roles integrados para el control de acceso basado en roles de Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) para más información sobre la asignación de roles y permisos a las cuentas.
2. En el cuadro que contiene el texto *Buscar recursos*, en la parte superior de Azure Portal, escriba *interfaces de red*. Cuando **interfaces de red** aparezca en los resultados de búsqueda, haga clic en él.
3. En la hoja **Interfaces de red** que aparece, haga clic en la NIC que desea ver o cuya configuración desea cambiar.
4. Las siguientes opciones se muestran en la hoja que aparece para la NIC seleccionada:
    - **Información general:** proporciona información acerca de la NIC, como las direcciones IP asignadas, la red virtual o subred a la que la NIC está conectada, y la máquina virtual a la que la NIC está asociada a (si está asociada a una). La siguiente imagen muestra la configuración general de una NIC llamada **mywebserver256**:   ![Información general de la interfaz de red](./media/virtual-network-network-interface/nic-overview.png)
    - **Configuraciones de IP:** una NIC tiene al menos una configuración de IP asignada, pero puede tener varias. Para más información sobre el número máximo de configuraciones de IP que una NIC admite, consulte los [límites de Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Cada configuración de IP tiene asignada una dirección IP privada, y puede tener asociada una dirección IP pública. Para modificar lo que se muestra, complete los pasos de las secciones [Incorporación de una configuración de IP secundaria a una NIC](#create-ip-config), [Cambio de una configuración de IP](#change-ip-config) o [Eliminación de una configuración de IP](#delete-ip-config), en este artículo.
    - **Servidores DNS:** puede especificar a qué servidor DNS asignarán una NIC los servidores DHCP de Azure. Elija entre el servidor DNS interno de Azure o un servidor DNS personalizado. Para modificar lo que se muestra, complete los pasos de la sección [Cambio de la configuración DNS de una NIC](#dns), en este artículo.
    - **Grupo de seguridad de red (NSG):** muestra si hay un NSG asociado a la NIC. Si hay un NSG asociado a la NIC, se muestra el nombre del NSG asociado. Para modificar lo que se muestra, complete los pasos de la sección [Asociación o desasociación de un NSG de una interfaz de red](#associate-nsg), en este artículo.
    - **Propiedades:** muestra las opciones de configuración clave de la NIC, incluida la dirección MAC y la suscripción donde se encuentra. Puede trasladar una NIC a un grupo de recursos o una suscripción distintos, siempre y cuando traslade también todos los recursos relacionados con la NIC. Si la NIC está asociada a una máquina virtual, por ejemplo, también debe trasladar la máquina virtual y todos los recursos adicionales relacionados con ella. Para trasladar una NIC, consulte cómo [trasladar recursos a un nuevo grupo de recursos o suscripción](../azure-resource-manager/resource-group-move-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json#use-portal). Enumera los requisitos previos y cómo trasladar recursos mediante Azure Portal, PowerShell y la CLI de Azure.
    - **Reglas de seguridad eficaces:** las reglas de seguridad se muestran si la NIC está asociada a una máquina virtual en ejecución, y hay un NSG asociado a la NIC, a la subred a la que está conectada, o ambas. Para más información acerca de lo que se muestra, lea el artículo [Solución de problemas de grupos de seguridad de red](virtual-network-nsg-troubleshoot-portal.md#view-effective-security-rules-for-a-network-interface). Consulte el artículo [Grupos de seguridad de red](virtual-networks-nsg.md) para más información sobre los NSG.
    - **Rutas eficaces:** las rutas se muestran si la NIC está conectada a una máquina virtual en ejecución. Las rutas son una combinación de las rutas predeterminadas de Azure, las rutas definidas por el usuario y las rutas BGP que pueda haber en la subred a la que la NIC está conectada. Para más información acerca de lo que se muestra, lea el artículo [Solución de problemas de rutas](virtual-network-routes-troubleshoot-portal.md#view-effective-routes-for-a-network-interface). Para aprender más sobre las rutas definidas por el usuario, lea el artículo [Rutas definidas por el usuario](virtual-networks-udr-overview.md).
    - **Opciones comunes de Azure Resource Manager:** para más información acerca de las opciones de configuración comunes de Azure Resource Manager, lea los artículos [Registro de actividad](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#activity-logs), [Control de acceso (IAM)](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#access-control), [Etiquetas](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#tags), [Bloqueos](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json) y [Script de Automation](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group).

|**Herramienta**|**Comando**|
|---|---|
|**CLI**|[az network nic list](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#list) para ver las NIC en la suscripción; [az network nic show](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#show) para ver la configuración de una NIC|
|**PowerShell**|[Get-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.4.0/get-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) para ver las NIC de la suscripción o la configuración de una NIC|

### <a name="dns"></a>Cambio de la configuración DNS de una NIC

Para cambiar la configuración DNS de una NIC, complete los pasos siguientes. El servidor DHCP de Azure asigna el servidor DNS a la máquina virtual. Para más información sobre las opciones de resolución de nombres de una NIC, lea el artículo [Resolución de nombres de máquinas virtuales](virtual-networks-name-resolution-for-vms-and-role-instances.md).

1. Complete los pasos 1 a 3 de la sección [Visualización y cambio de la configuración y las interfaces de red](#view-nics), en este artículo, para la NIC cuya configuración desea cambiar.
2. En la hoja de la NIC que seleccionó, haga clic en **Servidores DNS**.
3. Haga clic en:
    - **Heredar desde la red virtual (valor predeterminado)**: elija esta opción para heredar la configuración del servidor DNS definida para la red virtual a la que la NIC está conectada. En el nivel de red virtual, se define un servidor DNS personalizado o el servidor DNS proporcionado por Azure. El servidor DNS proporcionado por Azure puede resolver los nombres de los recursos conectados a la misma red virtual, pero no de los recursos conectados a redes virtuales diferentes.
    - **Personalizado**: puede configurar su propio servidor DNS para resolver nombres en diferentes redes virtuales. Escriba la dirección IP del servidor que desea usar como servidor DNS. La dirección del servidor DNS que especifique se asigna solo a esta NIC e invalida la configuración DNS de la red virtual a la que la NIC está conectada.
4. Haga clic en **Guardar**.

|**Herramienta**|**Comando**|
|---|---|
|**CLI**|[az network nic update](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|**PowerShell**|[Set-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.4.0/set-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

### <a name="ip-forwarding"></a>Cambio del reenvío IP de una NIC

El reenvío IP permite que la máquina virtual a la que una NIC está conectada:
- Recibir tráfico de red no destinado a una de las direcciones IP asignadas a cualquiera de las configuraciones de IP asignadas a la NIC.
- Enviar tráfico de red con una dirección IP de origen diferente de la asignada a una de sus configuraciones de IP.

La configuración debe habilitarse para cada NIC asociada a la máquina virtual que recibe el tráfico que la máquina virtual debe reenviar. Una máquina virtual puede reenviar el tráfico tanto si tiene varias como una sola NIC. Aunque el reenvío IP es una configuración de Azure, la máquina virtual también debe ejecutar una aplicación que pueda reenviar el tráfico, como aplicaciones de firewall, de optimización de WAN o de equilibrio de carga. Cuando una máquina virtual está ejecutando aplicaciones de red, la máquina virtual suele denominarse dispositivo virtual de red (NVA). Puede ver una lista de NVA listas para su implementación en [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances). El reenvío IP normalmente se usa con rutas definidas por el usuario. Para aprender más sobre las rutas definidas por el usuario, lea el artículo [Rutas definidas por el usuario](virtual-networks-udr-overview.md).

Para cambiar la configuración de reenvío IP de una NIC, complete los pasos siguientes:

1. Complete los pasos 1 a 3 de la sección [Visualización y cambio de la configuración y las interfaces de red](#view-nics), en este artículo, para la NIC que desea modificar.
2. En la hoja de la NIC que seleccionó, haga clic en Configuraciones de IP.
3. Haga clic en **Habilitado** o **Deshabilitado** (valor predeterminado) para cambiar la configuración.
4. Haga clic en **Guardar**.

|**Herramienta**|**Comando**|
|---|---|
|**CLI**|[az network nic update](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|**PowerShell**|[Set-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.4.0/set-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

### <a name="subnet"></a>Cambio de la subred a la que está conectada una NIC

Puede cambiar la subred a la que está conectada la NIC, pero no la red virtual. Para cambiar la subred, se deben asignar direcciones IP dinámicas privadas a todas las configuraciones de IP asociadas a la NIC. Para cambiar la subred a la que una NIC está conectada, complete los pasos siguientes:

1. Complete los pasos 1 a 3 de la sección [Visualización y cambio de la configuración y las interfaces de red](#view-nics), en este artículo, para la NIC que desea conectar a una subred diferente.
2. En la hoja de la NIC que seleccionó, haga clic en **Configuraciones de IP**. Si las direcciones IP privadas de las configuraciones de IP enumeradas tienen asignada una dirección IP privada con el método estático, debe cambiar al método dinámico siguiendo los pasos que se describen a continuación. Si las direcciones se asignan con el método dinámico, vaya al paso tres:
    - Haga clic en la dirección IP estática de la configuración de IP que desea cambiar en la lista de configuraciones de IP.
    - En la hoja que aparece para la configuración de IP, haga clic en **Dinámica** para el método **Asignación**.
    - Haga clic en **Guardar**.
3. Seleccione la subred a la que desea conectar la NIC en la lista desplegable **Subred**.
4. Haga clic en **Guardar**. Las nuevas direcciones dinámicas se asignan desde el intervalo de direcciones de subred. Si lo desea, después puede asignar las direcciones IP estáticas desde el nuevo intervalo de direcciones de subred.

|**Herramienta**|**Comando**|
|---|---|
|**CLI**|[az network nic ip-config update](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|**PowerShell**|[Set-AzureRmNetworkInterfaceIpConfig](/powershell/resourcemanager/azurerm.network/v3.4.0/set-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|


### <a name="delete-nic"></a>Eliminación de una interfaz de red

Puede eliminar una NIC siempre que no esté asociada a una máquina virtual. Si está asociada a una máquina virtual, primero debe desasociarla para poder eliminarla. Para desasociar una NIC de una máquina virtual, complete los pasos de la sección [Desconexión de una NIC de una máquina virtual](#vm-detach-nic), en este artículo.

1. Complete los pasos 1 y 2 de la sección [Visualización y cambio de la configuración y las interfaces de red](#view-nics), en este artículo, para la NIC que desea eliminar.
2. Haga clic con el botón derecho en la NIC que desea eliminar y haga clic en **Eliminar**.
3. Haga clic en **Sí** para confirmar la eliminación de la NIC.

Cuando se elimina una NIC, se liberan las direcciones MAC o IP asignadas a ella.

|**Herramienta**|**Comando**|
|---|---|
|**CLI**|[az network nic delete](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|**PowerShell**|[Remove-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.1.0/remove-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="ip-configs"></a>Configuraciones de IP
Cada NIC tiene al menos una configuración de IP, llamada configuración **principal**. Una NIC también puede tener asociadas una o varias configuraciones *secundarias*. Hay límites en el número de direcciones IP que se pueden asignar a una NIC. Para más información, lea el artículo acerca de los [límites de Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Cada configuración de IP:
- Tiene asignada una dirección IP privada mediante el método de asignación estático o dinámico. Si una máquina virtual se inicia después de estar en estado detenido (desasignado), las direcciones IP dinámicas pueden cambiar. Las direcciones IP estáticas se liberan de una NIC solo si la NIC se elimina.
- Puede tener asociada una dirección IP pública.

Los servidores DHCP de Azure asignan la dirección IP privada para la configuración de IP principal de la NIC a la NIC dentro del sistema operativo de la máquina virtual.

Asignar varias direcciones IP a una NIC resulta útil en escenarios como:
- Hospede varios sitios web o servicios con direcciones IP y certificados SSL diferentes en un único servidor.
- Una máquina virtual que actúa como una aplicación virtual de red, por ejemplo, un firewall o un equilibrador de carga.
- La capacidad de agregar cualquier dirección IP privada para cualquiera de las NIC a un grupo de servidores de back-end de Azure Load Balancer. En el pasado, solo la dirección IP principal para la NIC principal podía agregarse a un grupo de back-end. Para más información sobre cómo equilibrar la carga de varias configuraciones de IP, lea el artículo [Load balancing multiple IP configurations](../load-balancer/load-balancer-multiple-ip.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Equilibrio de carga de varias configuraciones de IP).

Hay un número limitado de direcciones IP públicas que pueden usarse dentro de una suscripción, y un número limitado de direcciones IP privadas que se pueden asignar a una NIC. Para más información sobre estos límites, lea el artículo sobre los [límites de Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

### <a name="create-ip-config"></a>Incorporación de una configuración de IP secundaria a una NIC

Puede agregar tantas configuraciones de IP como sea necesario a una NIC, dentro de los límites indicados en el artículo sobre los [límites de Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Para agregar una configuración de IP a una NIC, complete los pasos siguientes:

1. Complete los pasos 1 a 3 de la sección [Visualización de la configuración y las interfaces de red](#view-nics), en este artículo, para la NIC a la que desea agregar una configuración de IP.
2. En la hoja de la NIC que seleccionó, haga clic en **Configuraciones de IP**.
3. Haga clic en **+Agregar** en la hoja que se abre para las configuraciones de IP.
4. Especifique lo siguiente y haga clic en **Aceptar** para cerrar la hoja **Agregar configuración de IP**:

    |**Configuración**|**¿Necesario?**|**Detalles**|
    |---|---|---|
    |**Name**|Sí|Debe ser único para la NIC|
    |**Tipo**|Sí|Puesto que va a agregar una configuración de IP a una NIC existente y cada NIC deben tener una configuración de IP principal, la única opción es **Secundaria**.|
    |**Método de asignación de direcciones IP privadas**|Sí|Una dirección **Dinámica** puede cambiar si la máquina virtual se reinicia después de estar en estado detenido (desasignado). Una dirección **Estática** no se libera hasta que se elimina la NIC. Especifique una dirección IP del intervalo de direcciones de subred que no se esté usando actualmente en otra configuración de IP.|
    |**Dirección IP pública**|No|**Deshabilitado:** ningún recurso de dirección IP pública está asociado actualmente a la configuración de IP. **Habilitada:** seleccione una dirección IP pública existente o cree una nueva. Para más información sobre cómo crear una dirección IP pública, lea el artículo [Direcciones IP públicas](virtual-network-public-ip-address.md#create).|
5. Agregue manualmente las direcciones IP privadas secundarias al sistema operativo de la máquina virtual siguiendo las instrucciones del artículo [Asignación de varias direcciones IP a máquinas virtuales](virtual-network-multiple-ip-addresses-portal.md#os-config). No agregue direcciones IP públicas al sistema operativo de la máquina virtual.

|**Herramienta**|**Comando**|
|---|---|
|**CLI**|[az network nic ip-config create](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|**PowerShell**|[Add-AzureRmNetworkInterfaceIpConfig](/powershell/resourcemanager/azurerm.network/v3.4.0/add-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|

### <a name="change-ip-config"></a>Cambio de una configuración de IP

Para cambiar la configuración de direcciones IP públicas y privadas de una configuración de IP principal o secundaria, complete los pasos siguientes:

1. Complete los pasos 1 a 3 de la sección [Visualización de la configuración y las interfaces de red](#view-nics), en este artículo, para la NIC que desea modificar.
2. En la hoja de la NIC que seleccionó, haga clic en **Configuraciones de IP**.
3. Haga clic en la configuración de IP que desea modificar, en la lista de la hoja que se abre para las configuraciones de IP.
4. Cambie la configuración según la información que se proporciona en la sección [Incorporación de una configuración de IP](#create-ip-config), en este artículo; después, haga clic en **Guardar** para cerrar la hoja de la configuración de IP seleccionada.

>[!NOTE]
>Si la NIC principal tiene varias configuraciones de IP y cambia la dirección IP privada de la configuración de IP principal, debe reasignar manualmente todas las direcciones IP secundarias a la NIC en Windows (no es necesario para Linux). Para asignar manualmente las direcciones IP a una NIC en un sistema operativo, lea el artículo [Asignación de varias direcciones IP a máquinas virtuales](virtual-network-multiple-ip-addresses-portal.md#os-config). No agregue direcciones IP públicas al sistema operativo de la máquina virtual.

>[!WARNING]
>Para cambiar la dirección IP privada de una configuración IP secundaria asociada a una NIC secundaria, deben completarse los pasos anteriores solo después de que se detenga la VM y se cancele la asignación de esta.

|**Herramienta**|**Comando**|
|---|---|
|**CLI**|[az network nic ip-config update](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|**PowerShell**|[Set-AzureRMNetworkInterfaceIpConfig](/powershell/resourcemanager/azurerm.network/v3.4.0/set-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|

### <a name="delete-ip-config"></a>Eliminación de una configuración de IP secundaria de una NIC

Complete los pasos siguientes para eliminar una configuración de IP secundaria de una NIC:

1. Complete los pasos 1 a 3 de la sección [Visualización de la configuración y las interfaces de red](#view-nics), en este artículo, para la NIC que desea modificar.
2. En la hoja de la NIC que seleccionó, haga clic en **Configuraciones de IP**.
3. Haga clic con el botón derecho en la configuración IP secundaria que desea eliminar y haga clic en **Eliminar**. Si la configuración tenía asociado un recurso de dirección IP pública, el recurso se desasocia de la configuración de IP, pero no se elimina.
4. Cierre la hoja **Configuraciones de IP**.

|**Herramienta**|**Comando**|
|---|---|
|**CLI**|[az network nic ip-config delete](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|**PowerShell**|[Remove-AzureRmNetworkInterfaceIpConfig](/powershell/resourcemanager/azurerm.network/v3.4.0/remove-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="nsgs"></a>Grupos de seguridad de red
Un grupo de seguridad de red (NSG) contiene una lista de reglas de entrada y salida que permiten o deniegan el tráfico de red hacia una NIC. No se necesita tener una NIC, ni la subred a la que la NIC está conectada, para tener asociado un NSG. Sin embargo, puede asociar un NGS a una NIC, a la subred a la que la NIC está conectada, o ambos. Los NSG con reglas aplicables a todas las NIC conectadas a la subred suelen estar asociadas a subredes. Por lo tanto, podría aplicarse un NSG con reglas más granulares a cada NIC. Para más información sobre los grupos de seguridad de red, consulte el artículo sobre [grupos de seguridad de red](virtual-networks-nsg.md).

### <a name="associate-nsg"></a>Asociación o desasociación de un NSG de una interfaz de red

Para asociar un NSG a una NIC o desasociar un NSG de una NIC, complete los pasos siguientes:

1. Complete los pasos 1 a 3 de la sección [Visualización y cambio de la configuración y las interfaces de red](#view-nics), en este artículo, para la NIC de la que desea asociar o desasociar un NSG.
2. En la hoja de la NIC que seleccionó, haga clic en **Grupo de seguridad de red**. Aparece una hoja con **Editar** en la parte superior. Si no hay ningún NSG asociado actualmente a la NIC, se muestra **Grupo de seguridad de red** *Ninguno*. Si hay un NSG asociado actualmente a una NIC, se muestra **Grupo de seguridad de red** *NSG-Nombre* (donde NSG-Nombre es el nombre del NSG asociado actualmente a la NIC).
3. Haga clic en **Editar**.
4. Haga clic en **Grupo de seguridad de red**. Si no aparece ningún grupo de seguridad de red en la lista, es porque no existe ninguno en su suscripción. Para crear un NSG, complete los pasos del artículo [Grupos de seguridad de red](virtual-networks-create-nsg-arm-pportal.md).
5. En la hoja **Elegir grupo de seguridad de red** que aparece, haga clic en un NSG de la lista para asociarlo a la NIC, o haga clic en **Ninguno** para desasociar un NSG que está asociado actualmente a una NIC.
6. Haga clic en **Guardar**.

|**Herramienta**|**Comando**|
|---|---|
|**CLI**|[az network nic update](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|**PowerShell**|[Set-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/v3.4.0/set-azurermnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="vms"></a>Asociación y desasociación de NIC de una máquina virtual

Puede asociar una NIC existente a una máquina virtual al crearla, o puede asociar una NIC existente a una máquina virtual existente. También puede desasociar una NIC de una máquina virtual existente que tenga al menos dos NIC. Aunque el portal crea una NIC cuando se crea una máquina virtual, no le permite:

- Especificar una NIC existente a la que asociarse al crear la máquina virtual.
- Crear una máquina virtual con varias NIC asociadas.
- Especificar un nombre para la NIC (el portal crea la NIC con un nombre predeterminado).
- Especificar que el método de asignación de direcciones IP privado es estático. El portal asigna automáticamente una dirección IP privada dinámica, aunque puede cambiar el método de asignación después de que el portal cree la NIC.

Puede usar PowerShell o la CLI para crear una máquina virtual o la NIC con todos los atributos anteriores para los que no se puede usar el portal. Antes de completar las tareas de las secciones siguientes, tenga en cuenta las restricciones y los comportamientos siguientes:

- Los diferentes tamaños de máquina virtual admiten un número distinto de NIC. Para obtener más información sobre cuántas NIC admite cada tamaño de máquina virtual, consulte los artículos sobre el tamaño de las máquinas virtuales [Windows](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [Linux](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 
- Anteriormente, solo se podían agregar NIC a VM que admitían varias NIC y que se habían creado con al menos dos NIC. No podía agregar una NIC a una VM creada con una NIC, incluso si el tamaño de la VM admitía varias NIC. Por el contrario, solo se podían quitar NIC de VM con al menos tres NIC conectadas a ella, ya que las VM creadas con al menos dos NIC siempre debían tener al menos dos NIC conectadas. Ninguna de estas restricciones se aplican ya. Ahora puede crear una VM con cualquier número de NIC (hasta el número admitido por el tamaño de la VM) y agregar o quitar cualquier número de NIC, siempre y cuando la VM siempre tenga l menos una NIC asociada. 
- De forma predeterminada, la primera NIC asociada a una máquina virtual se define como la NIC *principal*. Todas las demás NIC asociadas a la máquina virtual son NIC *secundarias*.
- De forma predeterminada, todo el tráfico saliente de la máquina virtual se envía la dirección IP asignada a la configuración de IP principal de la NIC principal. Por supuesto, puede controlar qué dirección IP se usa para el tráfico saliente en el sistema operativo de la máquina virtual.
- En el pasado, era un requisito que todas las máquinas virtuales situadas en el mismo conjunto de disponibilidad debían tener una o varias NIC. Ahora puede haber máquinas virtuales con cualquier número de NIC en el mismo conjunto de disponibilidad. Una máquina virtual solo puede agregarse a un conjunto de disponibilidad cuando se crea. Para más información acerca de los conjuntos de disponibilidad, lea el artículo [Administración de la disponibilidad de las máquinas virtuales Windows en Azure](../virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy).
- Aunque las NIC asociadas a la misma máquina virtual se pueden conectar a subredes diferentes dentro de una red virtual, todas las NIC deben estar conectadas a la misma red virtual.
- Puede agregar cualquier dirección IP de la configuración de IP de cualquier NIC principal o secundaria a un grupo de back-end de Azure Load Balancer. En el pasado, solo la dirección IP principal para la NIC principal podía agregarse a un grupo de back-end.
- La eliminación de una máquina virtual no suprime las NIC asociadas a ella. Cuando se elimina una máquina virtual, las NIC se desasocian de la máquina virtual. Puede asociar las NIC a diferentes máquinas virtuales, o bien eliminarlas.

### <a name="vm-create"></a>Asociación de una o varias NIC al crear una máquina virtual

No se puede asociar una NIC existente a una nueva máquina virtual, ni crear una máquina virtual con varias NIC mediante Azure Portal. Puede usar los siguientes comandos de la CLI de Azure o de PowerShell para asociar una o varias NIC existentes al crear una máquina virtual:

- **CLI:** [az vm create](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json#create)
- **PowerShell:** [New-AzureRmVM](/powershell/resourcemanager/azurerm.compute/v2.5.0/new-azurermvm?toc=%2fazure%2fvirtual-network%2ftoc.json)

### <a name="vm-view-nic"></a> Visualización de las NICs asociadas a una máquina virtual

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta que tenga asignados los roles de propietario, colaborador o colaborador de red para la suscripción. Lea el artículo [Roles integrados para el control de acceso basado en roles de Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) para más información sobre la asignación de roles a las cuentas.
2. En el cuadro que contiene el texto *Buscar recursos*, en la parte superior de Azure Portal, escriba *máquinas virtuales*. Cuando **máquinas virtuales** aparezca en los resultados de búsqueda, haga clic en él.
3. En la hoja **Máquinas virtuales** que aparece, haga clic en el nombre de la máquina virtual para la que desea ver las interfaces de red asociadas.
4. En la hoja **Máquina virtual** que aparece para la máquina virtual seleccionada, haga clic en **Interfaces de red**.

|**Herramienta**|**Comando**|
|---|---|
|**CLI**|[az vm show](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json#show)|
|**PowerShell**|[Get-AzureRmVM](/powershell/resourcemanager/azurerm.compute/v1.3.4/get-azurermvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

### <a name="vm-attach-nic"></a>Asociación de una NIC a una máquina virtual existente

La máquina virtual a la que desea asociar una NIC debe admitir varias NIC y estar en estado detenido (desasignado). No se pueden asociar NIC a una máquina virtual existente mediante Azure Portal. Puede usar los siguientes comandos de la CLI de Azure o de PowerShell para asociar las NIC a las máquinas virtuales:

- **CLI:** [az vm nic add](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#add)
- **PowerShell:** [Add-AzureRmVMNetworkInterface](/powershell/resourcemanager/azurerm.compute/v2.5.0/add-azurermvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)

### <a name="vm-detach-nic"></a>Desasociación de una NIC de una máquina virtual existente

La máquina virtual de la que desea desasociar una NIC debe estar en estado detenido (desasignado) y debe haber al menos dos NIC conectadas. Puede desasociar cualquier NIC, pero la máquina virtual siempre debe tener al menos una NIC asociada. Si desasocia una NIC principal, Azure asigna el atributo primary a la NIC que haya estado más tiempo asociada a la máquina virtual. También elegir cualquier NIC como principal. No se pueden desasociar NIC de una máquina virtual ni establecer el atributo primary de una NIC mediante Azure Portal, aunque puede realizar ambas operaciones con la CLI o PowerShell. Puede usar los siguientes comandos de la CLI de Azure o de PowerShell para desasociar las NIC de las máquinas virtuales:

- **CLI:** [az vm nic remove](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#remove)
- **PowerShell:** [Remove-AzureRMVMNetworkInterface](/powershell/resourcemanager/azurerm.compute/v2.5.0/remove-azurermvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="next-steps"></a>Pasos siguientes
Para crear una máquina virtual con varias NIC o configuraciones de IP mediante scripts, lea los siguientes artículos:

|**Task**|**Herramienta**|
|---|---|
|**Creación de una máquina virtual con varias NIC**|[CLI](virtual-network-deploy-multinic-arm-cli.md) y [PowerShell](virtual-network-deploy-multinic-arm-ps.md)|
|**Creación de una máquina virtual con una sola NIC con varias direcciones IP asignadas**|[CLI](virtual-network-multiple-ip-addresses-cli.md) y [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|

