---
title: "Validación de la red virtual de Azure para su uso con Azure RemoteApp | Microsoft Docs"
description: "Obtenga información sobre cómo asegurarse de que la red virtual de Azure está lista para usarse con Azure RemoteApp"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: b573ba02-4587-4be5-9821-27bd891a73b2
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 05c8a0ff04293947cec391b6467cc4adddb6a7b0
ms.lasthandoff: 03/31/2017


---
# <a name="validate-the-azure-vnet-to-use-with-azure-remoteapp"></a>Validación de la red virtual de Azure para su uso con Azure RemoteApp
> [!IMPORTANT]
> Azure RemoteApp dejará de estar disponible el 31 de agosto de 2017. Para obtener más información, lea el [anuncio](https://go.microsoft.com/fwlink/?linkid=821148) .
> 
> 

Antes de usar una red virtual de Azure con Azure RemoteApp, debe validar la red virtual. Esto ayuda a evitar problemas con la conectividad.

Para validar la red virtual de Azure, haga lo siguiente:

1. Cree una máquina virtual de Azure dentro de la subred de la red virtual de Azure que quiere usar con Azure RemoteApp.
2. Conéctese a esa máquina virtual mediante la opción **Conectar** del portal de administración.
3. Conecte la máquina virtual al mismo dominio que desea usar con Azure RemoteApp. Si está creando una colección híbrida que se conecta a la red local, una la máquina virtual al dominio local.

Si esto se realiza correctamente, la red virtual de Azure está lista para usarse con RemoteApp.

Para obtener más información sobre el flujo de trabajo completo de la colección híbrida, vea los siguientes artículos:

* [Planeación de la red virtual de Azure RemoteApp](remoteapp-planvnet.md)
* [Creación de una colección híbrida](remoteapp-create-hybrid-deployment.md)
* [Implementación de la colección Azure RemoteApp en la Red virtual de Azure (con compatibilidad para ExpressRoute)](http://blogs.msdn.com/b/rds/archive/2015/04/23/deploy-azure-remoteapp-collection-to-your-azure-virtual-network-with-support-for-expressroute.aspx)


