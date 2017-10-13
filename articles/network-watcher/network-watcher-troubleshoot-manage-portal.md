---
title: "Solución de problemas de conexiones y puerta de enlace de Azure Virtual Network: PowerShell | Microsoft Docs"
description: "En esta página se explica cómo usar Azure Network Watcher para solucionar problemas con el cmdlet de PowerShell"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: f6f0a813-38b6-4a1f-8cfc-1dfdf979f595
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: jdial
ms.openlocfilehash: c3fa22bd599026b0838b134e26062d9837df703e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-virtual-network-gateway-and-connections-using-azure-network-watcher-powershell"></a>Solución de problemas de las conexiones y la puerta de enlace de Virtual Network mediante PowerShell de Azure Network Watcher

> [!div class="op_single_selector"]
> - [Portal](network-watcher-troubleshoot-manage-portal.md)
> - [PowerShell](network-watcher-troubleshoot-manage-powershell.md)
> - [CLI 1.0](network-watcher-troubleshoot-manage-cli-nodejs.md)
> - [CLI 2.0](network-watcher-troubleshoot-manage-cli.md)
> - [API DE REST](network-watcher-troubleshoot-manage-rest.md)

Network Watcher proporciona numerosas funcionalidades con relación a los recursos de red de Azure. Una de estas funcionalidades es la solución de problemas de recursos. Se puede llamar a la solución de problemas de recursos mediante el portal, PowerShell, la CLI o la API de REST. Cuando se llama a Network Watcher, este inspecciona el estado de una puerta de enlace de Virtual Network o de una conexión y devuelve sus conclusiones.

## <a name="before-you-begin"></a>Antes de empezar

En este escenario, se da por hecho que ya ha seguido los pasos descritos en [Create an Azure Network Watcher instance](network-watcher-create.md) (Creación de una instancia de Azure Network Watcher) para crear una instancia de Network Watcher.

Para obtener una lista de los tipos de puerta de enlace compatibles, vea el artículo sobre los [tipos de puerta de enlace compatibles](network-watcher-troubleshoot-overview.md#supported-gateway-types).

## <a name="overview"></a>Información general

La solución de problemas de recursos permite solucionar los problemas que surgen con las puertas de enlace y las conexiones de Virtual Network. Cuando se envía una solicitud para solucionar problemas de recursos, se consultan y se inspeccionan los registros. Una vez finalizada la inspección, se devuelven los resultados. Las solicitudes para solucionar problemas de recursos son de larga ejecución, y podrían tardar varios minutos en devolver un resultado. Los registros de solución de problemas se almacenan en un contenedor en una cuenta de almacenamiento especificada.

## <a name="troubleshoot-a-gateway-or-connection"></a>Solución de problemas de una puerta de enlace o conexión

1. Navegue hasta [Azure Portal](https://portal.azure.com) y haga clic en **Redes** > **Network Watcher** > **Diagnóstico de VPN**
2. Seleccione una **suscripción**, un **grupo de recursos** y una **ubicación**.
3. La solución de problemas de recursos devuelve datos sobre el mantenimiento del recurso. También guarda los registros pertinentes en una cuenta de almacenamiento para su revisión. Haga clic en **Cuenta de Storage** para seleccionar una.
4. En la hoja **Cuentas de Storage**, seleccione una existente o haga clic en **+ Cuenta de Storage** para crear una nueva.
5. En la hoja **Contenedores**, elija uno existente o haga clic en **+ Contenedor** para crear uno nuevo. Cuando termine, haga clic en **Seleccionar**
6. Seleccione los recursos de puerta de enlace y conexión para solucionar problemas y haga clic en **Start Troubleshooting** (Iniciar solución de problemas)

Si se seleccionan varios recursos, la solución de problemas se ejecuta de manera simultánea en los recursos de puerta de enlace. No se puede ejecutar en una conexión y su puerta de enlace asociada al mismo tiempo. Se recomienda solucionar primero los problemas de las puertas de enlace, puesto que solucionar problemas de conexión es un proceso más largo. Mientras se ejecuta el diagnóstico de VPN en un recurso, la columna **ESTADO DE SOLUCIÓN DE PROBLEMAS** mostrará un estado **En ejecución**

Cuando finalice, la columna de estado cambia a **Healthy** (Correcto) **Unhealthy** (Incorrecto).

![finalización de la solución de problemas][2]

## <a name="understanding-the-results"></a>Descripción de los resultados

En la sección de **detalles** de la ventana, la pestaña **Estado** muestra la última ejecución de solución de problemas en el recurso seleccionado. Los resultados del diagnóstico más reciente se muestran xx minutos después de la última ejecución.

|Propiedad  |Descripción  |
|---------|---------|
|Recurso     | Un vínculo al recurso.        |
|Ruta de acceso de almacenamiento     |  Ruta de acceso a la cuenta de almacenamiento y el contenedor que contienen los registros (si durante la ejecución se generó alguno). Esta configuración no se conserva una vez que sale del portal.        |
|Resumen     | Resumen del mantenimiento de recursos.        |
|Detalles     | Información detallada sobre el mantenimiento de recursos.        |
|Última ejecución     | La hora en que se ejecutó por última vez la solución de problemas.        |


La pestaña **Acción** ofrece una guía general sobre cómo resolver el problema. Si se puede realizar una acción para el problema, se proporciona un vínculo con instrucciones adicionales. Si no hay instrucciones adicionales, la respuesta proporciona la dirección URL para abrir un caso de soporte técnico.  Para más información acerca de las propiedades de la respuesta y lo que incluye, consulte la [introducción a la solución de problemas en Network Watcher](network-watcher-troubleshoot-overview.md)


## <a name="next-steps"></a>Pasos siguientes

Si se cambió la configuración y la conectividad de VPN se ha detenido, consulte [Administración de grupos de seguridad de red](../virtual-network/virtual-network-manage-nsg-arm-portal.md) para realizar un seguimiento de los grupos de seguridad de red y las reglas de seguridad que pueden estar afectados.


[2]: ./media/network-watcher-troubleshoot-manage-portal/2.png
