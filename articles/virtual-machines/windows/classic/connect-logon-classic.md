---
title: Inicio de sesión en una máquina virtual de Azure clásico | Microsoft Docs
description: Use Azure Portal para iniciar sesión en una máquina virtual Windows creada con el modelo de implementación clásica.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: 3c1239ed-07dc-48b8-8b3d-dc8c5f0ab20e
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: cynthn
ms.openlocfilehash: 87ecc65d2d4802ae826f3260b66b26e0bbe414e6
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
ms.locfileid: "34012366"
---
# <a name="log-on-to-a-windows-virtual-machine-using-the-azure-portal"></a>Inicie sesión en una máquina virtual de Windows mediante el Portal de Azure.
En el Portal de Azure, usará el botón **Conectar** para iniciar una sesión de Escritorio remoto e iniciar sesión en una VM de Windows.

¿Desea conectarse a una máquina virtual Linux? Consulte [Inicio de sesión en una máquina virtual Linux](../../linux/mac-create-ssh-keys.md).

<!--
Deleting, but not 100% sure
Learn how to [perform these steps using new Azure portal](../connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
-->

> [!IMPORTANT]
> Azure tiene dos modelos de implementación diferentes para crear recursos y trabajar con ellos: [Resource Manager y el clásico](../../../resource-manager-deployment-model.md). En este artículo se trata el modelo de implementación clásico. Microsoft recomienda que las implementaciones más recientes usen el modelo de Resource Manager. Para más información sobre cómo iniciar sesión en una máquina virtual mediante el modelo de Resource Manager, consulte [aquí](../connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

## <a name="connect-to-the-virtual-machine"></a>Conexión a la máquina virtual
1. Inicie sesión en el Portal de Azure.
2. Haga clic en la máquina virtual a la que quiera acceder. El nombre aparece en el panel **Todos los recursos**.

    ![Virtual-machine-locations](./media/connect-logon/azureportaldashboard.png)

1. Haga clic en el botón **Conectar** en la página de propiedades de la máquina virtual. 
2. En la página **Conexión a la máquina virtual**, mantenga seleccionadas las opciones adecuadas y haga clic en **Descargar archivo RDP**.
2. Abra el archivo RDP que descargó y haga clic en **Conectar** cuando se le solicite. 
2. Aparece una advertencia que indica que el archivo `.rdp` procede de un editor desconocido. Esto es normal. En la ventana de Escritorio remoto, haga clic en **Conectar** para continuar.
   
    ![Captura de pantalla de una advertencia sobre un editor desconocido.](./media/connect-logon/rdp-warn.png)
3. En la ventana **Seguridad de Windows**, seleccione **Más opciones** y, después, **Usar otra cuenta**. Escriba las credenciales de una cuenta en la máquina virtual y haga clic en **Aceptar**.
   
     **Cuenta local** : suele ser el nombre de usuario y la contraseña de la cuenta local que especificó al crear la máquina virtual. En este caso, el dominio es el nombre de la máquina virtual y se escribe como *nombreDeVm*&#92;*nombreDeUsuario*.  
   
    **Máquina virtual unida a dominio**: si la máquina virtual pertenece a un dominio, escriba el nombre de usuario con el formato *Dominio*&amp;#92;*Nombre de usuario*. La cuenta también debe estar en el grupo Administradores o tener privilegios de acceso remoto a la máquina virtual.
   
    **Controlador de dominio** : si la máquina virtual es un controlador de dominio, escriba el nombre de usuario y la contraseña de una cuenta de administrador de dominio para ese dominio.
4. Haga clic en **Sí** para comprobar la identidad de la máquina virtual y finalizar el inicio de sesión.
   
   ![Captura de pantalla que muestra un mensaje sobre la comprobación de la identidad de la máquina virtual.](./media/connect-logon/cert-warning.png)

## <a name="next-steps"></a>Pasos siguientes
* Si el botón **Conectar** está inactivo o tiene otros problemas con la conexión a Escritorio remoto, pruebe a restablecer la configuración. Haga clic en **Restablecer acceso remoto** en el panel de la máquina virtual.

    ![Reset-remote-access](./media/connect-logon/virtualmachine_dashboard_reset_remote_access.png)

* Si experimenta problemas con la contraseña, intente restablecerla. Haga clic en **Restablecer contraseña** en la esquina izquierda del panel de la máquina virtual, debajo de **Soporte y solución de problemas**.

    ![Reset-password](./media/connect-logon/virtualmachine_dashboard_reset_password.png)

Si esas sugerencias no funcionan o no son lo que necesita, consulte [Solución de problemas de conexiones del Escritorio remoto a una máquina virtual de Azure con Windows](../troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). En este artículo se le guiará a través del diagnóstico y la resolución de problemas comunes.
