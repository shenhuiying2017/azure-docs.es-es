---
title: "Inicio de sesión en una máquina virtual de Azure clásico | Microsoft Docs"
description: "Use el Portal de Azure clásico para iniciar sesión en una máquina virtual de Windows creada con el modelo de implementación clásica."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 3c1239ed-07dc-48b8-8b3d-dc8c5f0ab20e
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 07/28/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: f6537e4ebac76b9f3328223ee30647885ee15d3e
ms.openlocfilehash: f8fb116f14f771947f321f59d9880df4cdfe6859


---
# <a name="log-on-to-a-windows-virtual-machine-using-the-azure-classic-portal"></a>Inicio de sesión en una máquina virtual Windows mediante el Portal de Azure clásico
En el Portal de Azure clásico, usará el botón **Conectar** para iniciar una sesión de Escritorio remoto e iniciar sesión en una máquina virtual de Windows.

¿Desea conectarse a una máquina virtual Linux? Consulte [Inicio de sesión en una máquina virtual Linux](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Aprenda cómo [realizar estos pasos con el nuevo Portal de Azure](virtual-machines-windows-connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!IMPORTANT] 
> Azure tiene dos modelos de implementación diferentes para crear recursos y trabajar con ellos: [Resource Manager y el clásico](../azure-resource-manager/resource-manager-deployment-model.md). En este artículo se trata el modelo de implementación clásico. Microsoft recomienda que las implementaciones más recientes usen el modelo del Administrador de recursos. Para más información sobre cómo iniciar sesión en una máquina virtual mediante el modelo de Resource Manager, consulte [aquí](virtual-machines-windows-connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="video-walkthrough"></a>Tutorial en vídeo
A continuación se facilita una guía detallada en vídeo de los pasos de este tutorial. También se tratan los extremos y los puertos públicos y privados utilizados para conectarse a una VM de Windows en Azure.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Documentation-Shorts/Logging-On-To-VM-Running-Windows-Server-on-Azure/player]



## <a name="connect-to-the-virtual-machine"></a>Conexión a la máquina virtual
1. Inicie sesión en el portal clásico de Azure.
2. Haga clic en **Máquinas virtuales**y después seleccione la máquina virtual.
3. En la barra de comandos situada en la parte inferior de la página, haga clic en **Conectar**.
   
    ![Iniciar sesión en la nueva máquina virtual](./media/virtual-machines-windows-classic-connect-logon/connectwindows.png)

> [!TIP]
> Si el botón **Conectar** no está disponible, consulte las sugerencias de solución de problemas al final de este artículo.
> 
> 

## <a name="log-on-to-the-virtual-machine"></a>Iniciar sesión en la nueva máquina virtual
[!INCLUDE [virtual-machines-log-on-win-server](../../includes/virtual-machines-log-on-win-server.md)]

## <a name="next-steps"></a>Pasos siguientes
* Si el botón **Conectar** está inactivo o tiene otros problemas con la conexión a Escritorio remoto, pruebe a restablecer la configuración. Desde el panel de la máquina virtual, en **Vista rápida**, haga clic en **Restablecer configuración remota**.
* Si experimenta problemas con la contraseña, intente restablecerla. Desde el panel de la máquina virtual, en **Vista rápida**, haga clic en **Restablecer contraseña**.

Si esas sugerencias no funcionan o no son lo que necesita, consulte [Solución de problemas de conexiones del Escritorio remoto a una máquina virtual de Azure con Windows](virtual-machines-windows-troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). En este artículo se le guiará a través del diagnóstico y la resolución de problemas comunes.




<!--HONumber=Dec16_HO1-->


