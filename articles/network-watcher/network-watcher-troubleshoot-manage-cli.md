---
title: "Solución de problemas de puerta de enlace y conexiones de Azure Virtual Network con la CLI de Azure | Microsoft Docs"
description: "En esta página se explica cómo usar Azure Network Watcher para solucionar problemas con CLI de Azure"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: 2838bc61-b182-4da8-8533-27db8fdbd177
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 1d0136b044f6049e59fa09d824cf244cac703c45
ms.openlocfilehash: 30475d710dc00ff06683dcb963e9fdfdd762735b
ms.lasthandoff: 02/23/2017


---

# <a name="troubleshoot-virtual-network-gateway-and-connections-using-azure-network-watcher-azure-cli"></a>Solución de problemas de puerta de enlace y conexiones de red virtual mediante la CLI de Azure de Azure Network Watcher

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-troubleshoot-manage-powershell.md)
> - [CLI](network-watcher-troubleshoot-manage-cli.md)
> - [API de REST](network-watcher-troubleshoot-manage-rest.md)

Network Watcher proporciona numerosas funcionalidades con relación a los recursos de red de Azure. Una de estas funcionalidades es la solución de problemas de recursos. Se puede llamar a la solución de problemas de recursos mediante PowerShell, la CLI o la API de REST. Cuando se llama a Network Watcher, este inspecciona el estado de una puerta de enlace o de una conexión de Virtual Network y devuelve sus conclusiones.

## <a name="before-you-begin"></a>Antes de empezar

En este escenario, se da por hecho que ya ha seguido los pasos descritos en [Creación de una instancia de Network Watcher](network-watcher-create.md) para crear un monitor de red.

## <a name="overview"></a>Información general

La solución de problemas de recursos permite solucionar los problemas que surgen con las puertas de enlace y las conexiones de Virtual Network. Cuando se envía una solicitud para solucionar problemas de recursos, se consultan y se inspeccionan los registros. Una vez finalizada la inspección, se devuelven los resultados. Las solicitudes para solucionar problemas de recursos son de larga ejecución, y podrían tardar varios minutos en devolver un resultado. Los registros de solución de problemas se almacenan en un contenedor en una cuenta de almacenamiento especificada.

## <a name="retrieve-a-virtual-network-gateway-connection"></a>Recuperación de una conexión de puerta de enlace de Virtual Network

En este ejemplo, la solución de problemas de recursos se va a ejecutar en una conexión. También puede pasarla una puerta de enlace de Virtual Network. El cmdlet siguiente enumera las conexiones de VPN en un grupo de recursos.

```azurecli
azure network vpn-connection list -g resourceGroupName
```

También puede ejecutar el comando para ver las conexiones de una suscripción.

```azurecli
azure network vpn-connection list -s subscription
```

Cuando tenga el nombre de la cuenta de almacenamiento, puede ejecutar este comando para obtener su identificador de recurso:

```azurecli
azure network vpn-connection show -g resourceGroupName -n connectionName
```

## <a name="create-a-storage-account"></a>Crear una cuenta de almacenamiento

La solución de problemas de recursos devuelve datos sobre el estado de mantenimiento del recurso; también guarda registros en una cuenta de almacenamiento para su revisión. En este paso, se crea una cuenta de almacenamiento; si ya existe una cuenta de almacenamiento, puede usarla.

```azurecli
azure storage account create -n storageAccountName -l location -g resourceGroupName
```

## <a name="run-network-watcher-resource-troubleshooting"></a>Ejecución de la solución de problemas de recursos Network Watcher

Los problemas de recursos se solucionan con el cmdlet `network watcher troubleshoot`. Pasamos al cmdlet el grupo de recursos, el nombre de la instancia de Network Watcher, el identificador de la conexión, el identificador de la cuenta de almacenamiento, y la ruta de acceso al blob donde se van a almacenar los resultados de la solución de problemas.

```azurecli
azure network watcher -g resourceGroupName -n networkWatcherName -t connectionId -i storageId -p storagePath
```

Después de ejecutar el cmdlet, Network Watcher revisa los recursos para comprobar el estado. Devuelve los resultados al shell y almacena los registros de los resultados en la cuenta de almacenamiento especificada.

## <a name="understanding-the-results"></a>Descripción de los resultados

El texto de la acción ofrece instrucciones generales sobre cómo resolver el problema. Si se puede realizar una acción para el problema, se proporciona un vínculo con instrucciones adicionales. Si no hay instrucciones adicionales, la respuesta proporciona la dirección URL para abrir un caso de soporte técnico.  Para más información acerca de las propiedades de la respuesta y lo que incluye, consulte la [introducción a la solución de problemas en Network Watcher](network-watcher-troubleshoot-overview.md).

Para más instrucciones acerca de cómo descargar archivos desde cuentas de almacenamiento de Azure, consulte [Introducción a Azure Blob Storage mediante .NET](../storage/storage-dotnet-how-to-use-blobs.md). Otra herramienta que se puede utilizar es el Explorador de Storage. Encontrará más información acerca del Explorador de Storage en el siguiente vínculo: [Explorador de Storage](http://storageexplorer.com/).

## <a name="next-steps"></a>Pasos siguientes

Si se cambió la configuración y la conectividad de VPN se ha detenido, consulte [Administración de grupos de seguridad de red mediante el portal](../virtual-network/virtual-network-manage-nsg-arm-portal.md) para realizar un seguimiento de los grupos de seguridad de red y las reglas de seguridad que pueden estar afectados.

