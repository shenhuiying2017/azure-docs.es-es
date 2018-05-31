---
title: Conexión a una máquina virtual de Windows Server | Microsoft Docs
description: Aprenda a conectarse a una máquina virtual de Windows y a iniciar sesión en ella mediante el Portal de Azure y el modelo de implementación de Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ef62b02e-bf35-468d-b4c3-71b63fe7f409
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 04/11/2018
ms.author: cynthn
ms.openlocfilehash: 7c495a74ccbcad3ee18147726742ee59b65f1c0e
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
ms.locfileid: "34012648"
---
# <a name="how-to-connect-and-log-on-to-an-azure-virtual-machine-running-windows"></a>Conexión a una máquina virtual de Azure donde se ejecuta Windows e inicio de sesión en ella
Para iniciar una sesión de Escritorio remoto (RDP) desde un escritorio de Windows, usará el botón **Conectar** de Azure Portal. En primer lugar, conéctese a la máquina virtual y luego inicie sesión.

Si intenta conectarse a una máquina virtual Windows desde un equipo Mac, debe instalar un cliente de RDP para Mac como [Escritorio remoto de Microsoft](https://itunes.apple.com/app/microsoft-remote-desktop/id715768417).

## <a name="connect-to-the-virtual-machine"></a>Conexión a la máquina virtual
1. Si aún no lo ha hecho, inicie sesión en el [Azure Portal](https://portal.azure.com/).
2. En el menú de la izquierda, haga clic en **Máquinas virtuales**.
3. Seleccione la máquina virtual en la lista.
4. En la parte superior de la página de la máquina virtual, haga clic en ![Imagen del botón Conectar.](./media/connect-logon/connect.png) .
2. En la página **Conexión a la máquina virtual**, seleccione las opciones adecuadas y haga clic en **Descargar archivo RDP**.
2. Abra el archivo RDP que descargó y haga clic en **Conectar** cuando se le solicite. 
2. Aparece una advertencia que indica que el archivo `.rdp` procede de un editor desconocido. Esto es normal. En la ventana de Escritorio remoto, haga clic en **Conectar** para continuar.
   
    ![Captura de pantalla de una advertencia sobre un editor desconocido.](./media/connect-logon/rdp-warn.png)
3. En la ventana **Seguridad de Windows**, seleccione **Más opciones** y, después, **Usar otra cuenta**. Escriba las credenciales de una cuenta en la máquina virtual y haga clic en **Aceptar**.
   
     **Cuenta local** : suele ser el nombre de usuario y la contraseña de la cuenta local que especificó al crear la máquina virtual. En este caso, el dominio es el nombre de la máquina virtual y se escribe como *nombreDeVm*&#92;*nombreDeUsuario*.  
   
    **Máquina virtual unida a dominio**: si la máquina virtual pertenece a un dominio, escriba el nombre de usuario con el formato *Dominio*&amp;#92;*Nombre de usuario*. La cuenta también debe estar en el grupo Administradores o tener privilegios de acceso remoto a la máquina virtual.
   
    **Controlador de dominio** : si la máquina virtual es un controlador de dominio, escriba el nombre de usuario y la contraseña de una cuenta de administrador de dominio para ese dominio.
4. Haga clic en **Sí** para comprobar la identidad de la máquina virtual y finalizar el inicio de sesión.
   
   ![Captura de pantalla que muestra un mensaje sobre la comprobación de la identidad de la máquina virtual.](./media/connect-logon/cert-warning.png)


   > [!TIP]
   > Si el botón **Conectar** del portal está atenuado y no está conectado a Azure a través de una conexión [Express Route](../../expressroute/expressroute-introduction.md) o [VPN de sitio a sitio](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md), deberá crear y asignar a la máquina virtual una dirección IP pública antes de poder usar RDP. Aquí puede leer más sobre [direcciones IP públicas en Azure](../../virtual-network/virtual-network-ip-addresses-overview-arm.md).
   > 
   > 


## <a name="next-steps"></a>Pasos siguientes
Si surgen problemas al intentar conectarse, consulte [Solución de problemas de conexiones del Escritorio remoto](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). En este artículo se le guiará a través del diagnóstico y la resolución de problemas comunes.

