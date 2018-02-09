---
title: "Creación de una puerta de enlace de aplicaciones con un firewall de aplicaciones web mediante Azure Portal| Microsoft Docs"
description: Aprenda a crear una puerta de enlace de aplicaciones con un firewall de aplicaciones web mediante Azure Portal.
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/26/2018
ms.author: davidmu
ms.openlocfilehash: d2b8fc65e6cd03f61151dbae66bb89821cdab13b
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/29/2018
---
# <a name="create-an-application-gateway-with-a-web-application-firewall-using-the-azure-portal"></a>Creación de una puerta de enlace de aplicaciones con un firewall de aplicaciones web mediante Azure Portal

Puede usar Azure Portal para crear una [puerta de enlace de aplicaciones](application-gateway-introduction.md) con un [firewall de aplicaciones web](application-gateway-web-application-firewall-overview.md) (WAF). WAF usa reglas de [OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) para proteger la aplicación. Estas reglas incluyen protección frente a ataques, como la inyección de SQL, ataques de scripts entre sitios y apropiaciones de sesión.

En este artículo, aprenderá a:

> [!div class="checklist"]
> * Crear una puerta de enlace de aplicaciones con WAF habilitado
> * Crear las máquinas virtuales que se utilizan como servidores back-end
> * Crear una cuenta de almacenamiento y configurar los diagnósticos

![Ejemplo de firewall de aplicaciones web](./media/application-gateway-web-application-firewall-portal/scenario-waf.png)

## <a name="log-in-to-azure"></a>Inicie sesión en Azure.

Inicie sesión en Azure Portal en: [http://portal.azure.com](http://portal.azure.com).

## <a name="create-an-application-gateway"></a>Creación de una puerta de enlace de aplicaciones

Se necesita una red virtual para la comunicación entre los recursos que se crean. En este ejemplo se crean dos subredes: una para la puerta de enlace de aplicaciones y la otra para los servidores back-end. Puede crear una red virtual a la vez que crea la puerta de enlace de aplicaciones.

1. Haga clic en **Nuevo** en la esquina superior izquierda de Azure Portal.
2. Seleccione **Redes** y **Application Gateway** en la lista de destacados.
3. Especifique estos valores para la puerta de enlace de aplicaciones:

    - *myAppGateway*: como nombre de la puerta de enlace de aplicaciones.
    - *myResourceGroupAG*: para el nuevo grupo de recursos.
    - Seleccione *WAF* para el nivel de la puerta de enlace de aplicaciones.

    ![Creación de una nueva puerta de enlace de aplicaciones](./media/application-gateway-web-application-firewall-portal/application-gateway-create.png)

4. Acepte los valores predeterminados para las demás opciones y haga clic en **Aceptar**.
5. Haga clic en **Elegir una red virtual**, luego en **Crear nueva** y, después, especifique estos valores para la red virtual:

    - *myVNet*: como nombre de la red virtual.
    - *10.0.0.0/16*: como espacio de direcciones de la red virtual.
    - *myAGSubnet*: como nombre de subred.
    - *10.0.0.0/24*: como espacio de direcciones de la subred.

    ![Creación de una red virtual](./media/application-gateway-web-application-firewall-portal/application-gateway-vnet.png)

6. Haga clic en **Aceptar** para crear la red virtual y la subred.
7. Haga clic en **Elegir una dirección IP pública** y en **Crear nueva** y, a continuación, escriba el nombre de la dirección IP pública. En este ejemplo, la dirección IP pública se llama *myAGPublicIPAddress*. Acepte los valores predeterminados para las demás opciones y haga clic en **Aceptar**.
8. Acepte los valores predeterminados para la configuración del agente de escucha, deje el firewall de aplicaciones web deshabilitado y, a continuación, haga clic en **Aceptar**.
9. Revise la configuración en la página de resumen y, a continuación, haga clic en **Aceptar** para crear los recursos de red y la puerta de enlace de aplicaciones. La creación de la puerta de enlace de aplicaciones puede tardar varios minutos, espere a que finalice correctamente la implementación antes de pasar a la sección siguiente.

### <a name="add-a-subnet"></a>Incorporación de una subred

1. Haga clic en **Todos los recursos** en el menú izquierdo y, después, haga clic en **myVNet** en la lista de recursos.
2. Haga clic en **Subredes** y, a continuación, haga clic en **Subred**.

    ![Creación de una subred](./media/application-gateway-web-application-firewall-portal/application-gateway-subnet.png)

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

    ![Instalación de la extensión personalizada](./media/application-gateway-web-application-firewall-portal/application-gateway-extension.png)

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

1. Haga clic en **Todos los recursos** y en **myAppGateway**.
2. Haga clic en **Grupos de back-end**. Con la puerta de enlace de aplicaciones se crea un grupo predeterminado. Haga clic en **appGateayBackendPool**.
3. Haga clic en **Agregar destino** para agregar las máquinas virtuales que creó en el grupo de servidores back-end.

    ![Incorporación de servidores back-end](./media/application-gateway-web-application-firewall-portal/application-gateway-backend.png)

4. Haga clic en **Save**(Guardar).

## <a name="create-a-storage-account-and-configure-diagnostics"></a>Creación de una cuenta de almacenamiento y configuración de diagnósticos

## <a name="create-a-storage-account"></a>Crear una cuenta de almacenamiento

En este tutorial, la puerta de enlace de aplicaciones usa una cuenta de almacenamiento para almacenar datos con fines de detección y prevención. También puede usar Log Analytics o una instancia de Event Hubs para registrar los datos.

1. Haga clic en **Nuevo** en la esquina superior izquierda de Azure Portal.
2. Seleccione **Almacenamiento** y, a continuación, seleccione **Cuenta de almacenamiento: blob, archivo, tabla, cola**.
3. Escriba el nombre de la cuenta de almacenamiento, seleccione **Usar existente** para el grupo de recursos y, a continuación, seleccione **myResourceGroupAG**. En este ejemplo, el nombre de la cuenta de almacenamiento es *myagstore1*. Acepte los valores predeterminados para las demás opciones y haga clic en **Crear**.

## <a name="configure-diagnostics"></a>Configuración de diagnóstico

Configure los diagnósticos para registrar datos en los registros ApplicationGatewayAccessLog, ApplicationGatewayPerformanceLog y ApplicationGatewayFirewallLog.

1. En el menú izquierdo, haga clic en **Todos los recursos** y, a continuación, seleccione *myAppGateway*.
2. En Supervisión, haga clic en **Registros de diagnóstico**.
3. Haga clic en **Agregar configuración de diagnóstico**.
4. Escriba *myDiagnosticsSettings* como el nombre de la configuración de diagnóstico.
5. Seleccione **Archivar en una cuenta de almacenamiento** y, a continuación, haga clic en **Configurar** para seleccionar la cuenta de almacenamiento *myagstore1* que creó anteriormente.
6. Seleccione los registros de la puerta de enlace de aplicaciones que se van a recopilar y conservar.
7. Haga clic en **Save**(Guardar).

    ![Configuración de diagnóstico](./media/application-gateway-web-application-firewall-portal/application-gateway-diagnostics.png)

## <a name="test-the-application-gateway"></a>Prueba de la puerta de enlace de aplicaciones

1. Busque la dirección IP pública de la puerta de enlace de aplicaciones en la pantalla de información general. Haga clic en **Todos los recursos** y en **myAGPublicIPAddress**.

    ![Registro de la dirección IP pública de la puerta de enlace de aplicaciones](./media/application-gateway-web-application-firewall-portal/application-gateway-record-ag-address.png)

2. Copie la dirección IP pública y péguela en la barra de direcciones del explorador.

    ![Prueba de la puerta de enlace de aplicaciones](./media/application-gateway-web-application-firewall-portal/application-gateway-iistest.png)

## <a name="next-steps"></a>pasos siguientes

En este artículo, ha aprendido cómo:

> [!div class="checklist"]
> * Crear una puerta de enlace de aplicaciones con WAF habilitado
> * Crear las máquinas virtuales que se utilizan como servidores back-end
> * Crear una cuenta de almacenamiento y configurar los diagnósticos

Para más información acerca de las puertas de enlace de aplicaciones y sus recursos asociados, vaya a los artículos de procedimientos.