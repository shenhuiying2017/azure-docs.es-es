---
title: "Creación de una puerta de enlace de aplicaciones con Azure Portal | Microsoft Docs"
description: Aprenda a crear una puerta de enlace de aplicaciones mediante Azure Portal.
services: application-gateway
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/25/2018
ms.author: davidmu
ms.openlocfilehash: df9235bc7ff61943de52a0bcc4064bf9fab6636a
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/29/2018
---
# <a name="create-an-application-gateway-using-the-azure-portal"></a>Creación de una puerta de enlace de aplicaciones mediante Azure Portal

Para crear o administrar las puertas de enlace de aplicaciones puede usar Azure Portal. Esta guía de inicio rápido muestra cómo crear recursos de red, servidores back-end y una puerta de enlace de aplicaciones.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="log-in-to-azure"></a>Inicie sesión en Azure.

Inicie sesión en Azure Portal en: [http://portal.azure.com](http://portal.azure.com).

## <a name="create-an-application-gateway"></a>Creación de una puerta de enlace de aplicaciones

Se necesita una red virtual para la comunicación entre los recursos que se crean. En este ejemplo se crean dos subredes: una para la puerta de enlace de aplicaciones y la otra para los servidores back-end. Puede crear una red virtual a la vez que crea la puerta de enlace de aplicaciones.

1. Haga clic en **Nuevo** en la esquina superior izquierda de Azure Portal.
2. Seleccione **Redes** y **Application Gateway** en la lista de destacados.
3. Especifique estos valores para la puerta de enlace de aplicaciones:

    - *myAppGateway*: como nombre de la puerta de enlace de aplicaciones.
    - *myResourceGroupAG*: como nuevo grupo de recursos.

    ![Creación de una nueva puerta de enlace de aplicaciones](./media/application-gateway-create-gateway-portal/application-gateway-create.png)

4. Acepte los valores predeterminados para las demás opciones y haga clic en **Aceptar**.
5. Haga clic en **Elegir una red virtual**, luego en **Crear nueva** y, después, especifique estos valores para la red virtual:

    - *myVNet*: como nombre de la red virtual.
    - *10.0.0.0/16*: como espacio de direcciones de la red virtual.
    - *myAGSubnet*: como nombre de subred.
    - *10.0.0.0/24*: como espacio de direcciones de la subred.

    ![Creación de una red virtual](./media/application-gateway-create-gateway-portal/application-gateway-vnet.png)

6. Haga clic en **Aceptar** para crear la red virtual y la subred.
6. Haga clic en **Elegir una dirección IP pública** y en **Crear nueva** y, a continuación, escriba el nombre de la dirección IP pública. En este ejemplo, la dirección IP pública se llama *myAGPublicIPAddress*. Acepte los valores predeterminados para las demás opciones y haga clic en **Aceptar**.
8. Acepte los valores predeterminados para la configuración del agente de escucha, deje el firewall de aplicaciones web deshabilitado y haga clic en **Aceptar**.
9. Revise la configuración en la página de resumen y haga clic en **Aceptar** para crear la red virtual, la dirección IP pública y la puerta de enlace de aplicaciones. La creación de la puerta de enlace de aplicaciones puede tardar varios minutos, espere a que finalice correctamente la implementación antes de pasar a la sección siguiente.

### <a name="add-a-subnet"></a>Incorporación de una subred

1. Haga clic en **Todos los recursos** en el menú izquierdo y, después, haga clic en **myVNet** en la lista de recursos.
2. Haga clic en **Subredes** y en **Subred**.

    ![Creación de una subred](./media/application-gateway-create-gateway-portal/application-gateway-subnet.png)

3. Escriba *myBackendSubnet* como nombre de la subred y, a continuación, haga clic en **Aceptar**.

## <a name="create-backend-servers"></a>Creación de servidores back-end

En este ejemplo, se crean dos máquinas virtuales que se usarán como servidores back-end para la puerta de enlace de aplicaciones. También se instala IIS en las máquinas virtuales para comprobar que la puerta de enlace de aplicaciones se ha creado correctamente.

### <a name="create-a-virtual-machine"></a>de una máquina virtual

1. Haga clic en **Nuevo**.
2. Haga clic en **Compute** y, después, seleccione **Windows Server 2016 Datacenter** en la lista de destacados.
3. Especifique estos valores para la máquina virtual:

    - *myVM*: como nombre de la máquina virtual.
    - *azureuser*: como nombre del usuario administrador.
    - *Azure123456!* como contraseña.
    - Seleccione **Usar existente** y *myResourceGroupAG*.

4. Haga clic en **OK**.
5. Seleccione **DS1_V2** como tamaño de la máquina virtual y haga clic en **Seleccionar**.
6. Asegúrese de que **myVNet** está seleccionada como red virtual y que la subred es **myBackendSubnet**. 
7. Haga clic en **Deshabilitado** para deshabilitar los diagnósticos de arranque.
8. Haga clic en **Aceptar**, revise la configuración en la página de resumen y haga clic en **Crear**.

### <a name="install-iis"></a>Instalación de IIS

1. Abra el shell interactivo y asegúrese de que está establecido en **PowerShell**.

    ![Instalación de la extensión personalizada](./media/application-gateway-create-gateway-portal/application-gateway-extension.png)

2. Ejecute el siguiente comando para instalar IIS en la máquina virtual: 

    ```azurepowershell-interactive
    Set-AzureRmVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -ExtensionName IIS `
      -VMName myVM `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
      -Location EastUS
    ```

3. Cree una segunda máquina virtual e instale IIS según los pasos que acaba de finalizar. Escriba *myVM2* como nombre y como VMName en Set-AzureRmVMExtension.

### <a name="add-backend-servers"></a>Incorporación de servidores back-end

3. Haga clic en **Todos los recursos** y en **myAppGateway**.
4. Haga clic en **Grupos de back-end**. Con la puerta de enlace de aplicaciones se crea un grupo predeterminado. Haga clic en **appGatewayBackendPool**.
5. Haga clic en **Agregar destino** para agregar las máquinas virtuales que creó en el grupo de servidores back-end.

    ![Incorporación de servidores back-end](./media/application-gateway-create-gateway-portal/application-gateway-backend.png)

6. Haga clic en **Save**(Guardar).

## <a name="test-the-application-gateway"></a>Prueba de la puerta de enlace de aplicaciones

1. Busque la dirección IP pública de la puerta de enlace de aplicaciones en la pantalla de información general. Haga clic en **Todos los recursos** y en **myAGPublicIPAddress**.

    ![Registro de la dirección IP pública de la puerta de enlace de aplicaciones](./media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png)

2. Copie la dirección IP pública y péguela en la barra de direcciones del explorador.

    ![Prueba de la puerta de enlace de aplicaciones](./media/application-gateway-create-gateway-portal/application-gateway-iistest.png)


## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no los necesite, elimine el grupo de recursos, la puerta de enlace de aplicaciones y todos los recursos relacionados. Para ello, seleccione el grupo de recursos que contiene la puerta de enlace de aplicaciones y haga clic en **Eliminar**.

## <a name="next-steps"></a>pasos siguientes

En esta guía de inicio rápido se ha creado un grupo de recursos, recursos de red y servidores back-end. A continuación, se han usado esos recursos para crear una puerta de enlace de aplicaciones. Para más información acerca de las puertas de enlace de aplicaciones y sus recursos asociados, vaya a los artículos de procedimientos.
