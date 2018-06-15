---
title: 'Guía de inicio rápido: dirigir el tráfico web con Azure Application Gateway: Azure Portal | Microsoft Docs'
description: Obtenga información acerca de cómo utilizar Azure Portal para crear una instancia de Azure Application Gateway que dirija el tráfico web a las máquinas virtuales de un grupo de back-end.
services: application-gateway
author: vhorne
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: quickstart
ms.workload: infrastructure-services
ms.date: 02/14/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: effda81d8755486a65472eb546c6b8688aea0a3b
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2018
ms.locfileid: "33205982"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-portal"></a>Guía de inicio rápido: dirigir el tráfico web con Azure Application Gateway: Azure Portal

Con Azure Application Gateway, puede dirigir el tráfico web de la aplicación a recursos específicos mediante la asignación de agentes de escucha a los puertos, la creación de reglas y la adición de recursos a un grupo de back-end.

En esta guía de inicio rápido se muestra cómo utilizar Azure Portal para crear rápidamente la puerta de enlace de aplicaciones con dos máquinas virtuales en el grupo de back-end. A continuación, se prueba para asegurarse de que funciona correctamente.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="log-in-to-azure"></a>Inicio de sesión en Azure

Inicie sesión en Azure Portal en [http://portal.azure.com](http://portal.azure.com)

## <a name="create-an-application-gateway"></a>Creación de una puerta de enlace de aplicaciones

Debe crear una red virtual para que la puerta de enlace de aplicaciones pueda comunicarse con otros recursos. Puede crear una red virtual a la vez que crea la puerta de enlace de aplicaciones. En este ejemplo, se crean dos subredes: una para la puerta de enlace de aplicaciones y la otra para las máquinas virtuales. 

1. Haga clic en **Crear un recurso** en la esquina superior izquierda de Azure Portal.
2. Seleccione **Redes** y **Application Gateway** en la lista de destacados.
3. Especifique estos valores para la puerta de enlace de aplicaciones:

    - *myAppGateway*: como nombre de la puerta de enlace de aplicaciones.
    - *myResourceGroupAG*: como nuevo grupo de recursos.

    ![Creación de una nueva puerta de enlace de aplicaciones](./media/quick-create-portal/application-gateway-create.png)

4. Acepte los valores predeterminados para las demás opciones y haga clic en **Aceptar**.
5. Haga clic en **Elegir una red virtual** > **Crear nueva** y, a continuación, especifique estos valores para la red virtual:

    - *myVNet*: como nombre de la red virtual.
    - *10.0.0.0/16*: como espacio de direcciones de la red virtual.
    - *myAGSubnet*: como nombre de subred.
    - *10.0.0.0/24*: como espacio de direcciones de la subred.

    ![Creación de una red virtual](./media/quick-create-portal/application-gateway-vnet.png)

6. Haga clic en **Aceptar** para crear la red virtual y la subred.
6. Haga clic en **Elegir una dirección IP pública** > **Crear nueva** y, a continuación, escriba el nombre de la dirección IP pública. En este ejemplo, la dirección IP pública se llama *myAGPublicIPAddress*. Acepte los valores predeterminados para las demás opciones y haga clic en **Aceptar**.
8. Acepte los valores predeterminados para la configuración del agente de escucha, deje el firewall de aplicaciones web deshabilitado y haga clic en **Aceptar**.
9. Revise la configuración en la página de resumen y haga clic en **Aceptar** para crear la red virtual, la dirección IP pública y la puerta de enlace de aplicaciones. La creación de la puerta de enlace de aplicaciones puede tardar hasta 30 minutos. Espere a que finalice correctamente la implementación antes de pasar a la sección siguiente.

### <a name="add-a-subnet"></a>Incorporación de una subred

1. Haga clic en **Todos los recursos** en el menú izquierdo y, después, haga clic en **myVNet** en la lista de recursos.
2. Haga clic en **Subredes** > **Subred**.

    ![Creación de una subred](./media/quick-create-portal/application-gateway-subnet.png)

3. Escriba *myBackendSubnet* como nombre de la subred y, a continuación, haga clic en **Aceptar**.

## <a name="create-backend-servers"></a>Creación de servidores back-end

En este ejemplo, se crean dos máquinas virtuales que se usarán como servidores back-end para la puerta de enlace de aplicaciones. 

### <a name="create-a-virtual-machine"></a>de una máquina virtual

1. Haga clic en **Nuevo**.
2. Haga clic en **Compute** y, a continuación, seleccione **Windows Server 2016 Datacenter** en la lista de destacados.
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

También se instala IIS en las máquinas virtuales para comprobar que la puerta de enlace de aplicaciones se ha creado correctamente.

1. Abra el shell interactivo y asegúrese de que está establecido en **PowerShell**.

    ![Instalación de la extensión personalizada](./media/quick-create-portal/application-gateway-extension.png)

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

Después de crear las máquinas virtuales, debe agregarlas al grupo de back-end en la puerta de enlace de aplicaciones.

1. Haga clic en **Todos los recursos** > **myAppGateway**.
2. Haga clic en **Grupos de back-end**. Con la puerta de enlace de aplicaciones se crea un grupo predeterminado. Haga clic en **appGatewayBackendPool**.
3. Haga clic en **Agregar destino** > **Máquina virtual** y, a continuación, seleccione *myVM*. Seleccione **Agregar destino** > **Máquina virtual** y, a continuación, seleccione *myVM2*.

    ![Incorporación de servidores back-end](./media/quick-create-portal/application-gateway-backend.png)

4. Haga clic en **Save**(Guardar).

## <a name="test-the-application-gateway"></a>Prueba de la puerta de enlace de aplicaciones

No es necesario instalar IIS para crear la puerta de enlace de aplicaciones, pero se instaló en esta guía de inicio rápido para comprobar si la puerta de enlace de aplicaciones se creó correctamente.

1. Busque la dirección IP pública de la puerta de enlace de aplicaciones en la pantalla de información general. Haga clic en **Todos los recursos** > **myAGPublicIPAddress**.

    ![Registro de la dirección IP pública de la puerta de enlace de aplicaciones](./media/quick-create-portal/application-gateway-record-ag-address.png)

2. Copie la dirección IP pública y péguela en la barra de direcciones del explorador.

    ![Prueba de la puerta de enlace de aplicaciones](./media/quick-create-portal/application-gateway-iistest.png)

Al actualizar el explorador, verá aparecer el nombre de la otra VM.

## <a name="clean-up-resources"></a>Limpieza de recursos

Explore los recursos que se crearon con la puerta de enlace de aplicaciones y, a continuación, cuando ya no sean necesarios, puede eliminar el grupo de recursos, la puerta de enlace de aplicaciones y todos los recursos relacionados. Para ello, seleccione el grupo de recursos que contiene la puerta de enlace de aplicaciones y haga clic en **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Administrar el tráfico web con Application Gateway mediante la CLI de Azure](./tutorial-manage-web-traffic-cli.md)
