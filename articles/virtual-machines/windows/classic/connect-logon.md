---
title: "Inicio de sesión en una máquina virtual de Azure clásico | Microsoft Docs"
description: "Use Azure Portal para iniciar sesión en una máquina virtual Windows creada con el modelo de implementación clásica."
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
ms.date: 05/30/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: 07584294e4ae592a026c0d5890686eaf0b99431f
ms.openlocfilehash: 43d54de7e875de9212c23c49ad0539bf2272a312
ms.contentlocale: es-es
ms.lasthandoff: 06/01/2017

---
# <a name="log-on-to-a-windows-virtual-machine-using-the-azure-portal"></a>Inicie sesión en una máquina virtual de Windows mediante el Portal de Azure.
En el Portal de Azure, usará el botón **Conectar** para iniciar una sesión de Escritorio remoto e iniciar sesión en una VM de Windows.

¿Desea conectarse a una máquina virtual Linux? Consulte [Inicio de sesión en una máquina virtual Linux](../../linux/mac-create-ssh-keys.md).

<!--
Deleting, but not 100% sure
Learn how to [perform these steps using new Azure portal](../connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
-->

> [!IMPORTANT]
> Azure tiene dos modelos de implementación diferentes para crear recursos y trabajar con ellos: [Resource Manager y el clásico](../../../resource-manager-deployment-model.md). En este artículo se trata el modelo de implementación clásico. Microsoft recomienda que las implementaciones más recientes usen el modelo del Administrador de recursos. Para más información sobre cómo iniciar sesión en una máquina virtual mediante el modelo de Resource Manager, consulte [aquí](../connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="connect-to-the-virtual-machine"></a>Conexión a la máquina virtual
1. Inicie sesión en el Portal de Azure.
2. Haga clic en la máquina virtual a la que quiera acceder. El nombre aparece en el panel **Todos los recursos**.

    ![Virtual-machine-locations](./media/connect-logon/azureportaldashboard.png)

3. Haga clic en **Conectar** en la barra de comandos encima del panel de la máquina virtual.

    ![Icono de Conectar de la máquina virtual](./media/connect-logon/virtualmachine_dashboard_connect.png)

<!-- Don't know if this still applies
     I think we can zap this.
> [!TIP]
> If the **Connect** button isn't available, see the troubleshooting tips at the end of this article.
>
>
-->

## <a name="log-on-to-the-virtual-machine"></a>Iniciar sesión en la nueva máquina virtual
[!INCLUDE [virtual-machines-log-on-win-server](../../../../includes/virtual-machines-log-on-win-server.md)]

## <a name="next-steps"></a>Pasos siguientes
* Si el botón **Conectar** está inactivo o tiene otros problemas con la conexión a Escritorio remoto, pruebe a restablecer la configuración. Haga clic en **Restablecer acceso remoto** en el panel de la máquina virtual.

    ![Reset-remote-access](./media/connect-logon/virtualmachine_dashboard_reset_remote_access.png)

* Si experimenta problemas con la contraseña, intente restablecerla. Haga clic en **Restablecer contraseña** en la esquina izquierda del panel de la máquina virtual, debajo de **Soporte y solución de problemas**.

    ![Reset-password](./media/connect-logon/virtualmachine_dashboard_reset_password.png)

Si esas sugerencias no funcionan o no son lo que necesita, consulte [Solución de problemas de conexiones del Escritorio remoto a una máquina virtual de Azure con Windows](../troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). En este artículo se le guiará a través del diagnóstico y la resolución de problemas comunes.

