---
title: "Conexión a una máquina virtual de Windows Server | Microsoft Docs"
description: "Aprenda a conectarse a una máquina virtual de Windows y a iniciar sesión en ella mediante el Portal de Azure y el modelo de implementación de Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: ef62b02e-bf35-468d-b4c3-71b63fe7f409
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/01/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 00f6b2e60c20eb27771d9d54df63f930ee88a55a
ms.openlocfilehash: 7427c8126ab73a851bc696d4925366b3b714616d
ms.lasthandoff: 03/02/2017


---
# <a name="how-to-connect-and-log-on-to-an-azure-virtual-machine-running-windows"></a>Conexión a una máquina virtual de Azure donde se ejecuta Windows e inicio de sesión en ella
Para iniciar una sesión de Escritorio remoto (RDP) desde un escritorio de Windows, usará el botón **Conectar** de Azure Portal. En primer lugar, conéctese a la máquina virtual y luego inicie sesión.

Si intenta conectarse a una máquina virtual Windows desde un equipo Mac, debe instalar un cliente de RDP para Mac como [Escritorio remoto de Microsoft](https://itunes.apple.com/app/microsoft-remote-desktop/id715768417).

## <a name="connect-to-the-virtual-machine"></a>Conexión a la máquina virtual
1. Si aún no lo ha hecho, inicie sesión en el [Portal de Azure](https://portal.azure.com/).
2. En el menú Centro, haga clic en **Máquinas virtuales**.
3. Seleccione la máquina virtual en la lista.
4. En la hoja de la máquina virtual, haga clic en **Conectar**.
   
    ![Captura de pantalla del Portal de Azure que muestra cómo conectarse a la máquina virtual.](./media/virtual-machines-windows-connect-logon/connect.png)
   
   > [!TIP]
   > Si el botón **Conectar** del portal está atenuado y no está conectado a Azure a través de una conexión [Express Route](../expressroute/expressroute-introduction.md) o [VPN de sitio a sitio](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md), deberá crear y asignar a la máquina virtual una dirección IP pública antes de poder usar RDP. Aquí puede leer más sobre [direcciones IP públicas en Azure](../virtual-network/virtual-network-ip-addresses-overview-arm.md).
   > 
   > 

## <a name="log-on-to-the-virtual-machine"></a>Iniciar sesión en la nueva máquina virtual
[!INCLUDE [virtual-machines-log-on-win-server](../../includes/virtual-machines-log-on-win-server.md)]

## <a name="next-steps"></a>Pasos siguientes
Si surgen problemas al intentar conectarse, consulte [Solución de problemas de conexiones del Escritorio remoto](virtual-machines-windows-troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). En este artículo se le guiará a través del diagnóstico y la resolución de problemas comunes.


