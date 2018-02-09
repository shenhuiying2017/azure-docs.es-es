---
title: "Creación de una puerta de enlace de aplicaciones con reglas de enrutamiento basadas en rutas de dirección URL con Azure Portal | Microsoft Docs"
description: "Aprenda a crear reglas de enrutamiento basadas en rutas de dirección URL para una puerta de enlace de aplicaciones y un conjunto de escalado de máquinas virtuales mediante Azure Portal."
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
ms.openlocfilehash: eb07b1811b017f71a003be26522e6b213a300321
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/29/2018
---
# <a name="create-an-application-gateway-with-path-based-routing-rules-using-the-azure-portal"></a>Creación de una puerta de enlace de aplicaciones con reglas de enrutamiento basadas en rutas de dirección URL con Azure Portal

Puede usar Azure Portal para configurar [reglas de enrutamiento basadas en rutas de dirección URL](application-gateway-url-route-overview.md) cuando se crea una [puerta de enlace de aplicaciones](application-gateway-introduction.md). En este tutorial, creará grupos de back-end mediante el uso de máquinas virtuales. A continuación, creará reglas de enrutamiento que garantizan que el tráfico web llega a los servidores adecuados de los grupos.

En este artículo, aprenderá a:

> [!div class="checklist"]
> * Creación de una puerta de enlace de aplicaciones
> * Crear máquinas virtuales para servidores back-end
> * Crear grupos de back-end con los servidores back-end
> * Crear un agente de escucha de back-end
> * Crear una regla de enrutamiento basada en la ruta de acceso

![Ejemplo de enrutamiento de direcciones URL](./media/application-gateway-create-url-route-portal/scenario.png)

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

    ![Creación de una nueva puerta de enlace de aplicaciones](./media/application-gateway-create-url-route-portal/application-gateway-create.png)

4. Acepte los valores predeterminados para las demás opciones y haga clic en **Aceptar**.
5. Haga clic en **Elegir una red virtual**, luego en **Crear nueva** y, después, especifique estos valores para la red virtual:

    - *myVNet*: como nombre de la red virtual.
    - *10.0.0.0/16*: como espacio de direcciones de la red virtual.
    - *myAGSubnet*: como nombre de subred.
    - *10.0.0.0/24*: como espacio de direcciones de la subred.

    ![Creación de una red virtual](./media/application-gateway-create-url-route-portal/application-gateway-vnet.png)

6. Haga clic en **Aceptar** para crear la red virtual y la subred.
7. Haga clic en **Elegir una dirección IP pública** y en **Crear nueva** y, a continuación, escriba el nombre de la dirección IP pública. En este ejemplo, la dirección IP pública se llama *myAGPublicIPAddress*. Acepte los valores predeterminados para las demás opciones y haga clic en **Aceptar**.
8. Acepte los valores predeterminados para la configuración del agente de escucha, deje el firewall de aplicaciones web deshabilitado y haga clic en **Aceptar**.
9. Revise la configuración en la página de resumen y, a continuación, haga clic en **Aceptar** para crear los recursos de red y la puerta de enlace de aplicaciones. La creación de la puerta de enlace de aplicaciones puede tardar varios minutos, espere a que finalice correctamente la implementación antes de pasar a la sección siguiente.

### <a name="add-a-subnet"></a>Incorporación de una subred

1. Haga clic en **Todos los recursos** en el menú izquierdo y, después, haga clic en **myVNet** en la lista de recursos.
2. Haga clic en **Subredes** y, a continuación, haga clic en **Subred**.

    ![Creación de una subred](./media/application-gateway-create-url-route-portal/application-gateway-subnet.png)

3. Escriba *myBackendSubnet* como nombre de la subred y, a continuación, haga clic en **Aceptar**.

## <a name="create-virtual-machines"></a>Creación de máquinas virtuales

En este ejemplo, se crean tres máquinas virtuales que se usarán como servidores back-end para la puerta de enlace de aplicaciones. También se instala IIS en las máquinas virtuales para comprobar que la puerta de enlace de aplicaciones se ha creado correctamente.

1. Haga clic en **Nuevo**.
2. Haga clic en **Compute** y, después, seleccione **Windows Server 2016 Datacenter** en la lista de destacados.
3. Especifique estos valores para la máquina virtual:

    - *myVM1*: para el nombre de la máquina virtual.
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

    ![Instalación de la extensión personalizada](./media/application-gateway-create-url-route-portal/application-gateway-extension.png)

2. Ejecute el siguiente comando para instalar IIS en la máquina virtual: 

    ```azurepowershell-interactive
    $publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/davidmu1/samplescripts/master/appgatewayurl.ps1");  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }
    Set-AzureRmVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -Location eastus `
      -ExtensionName IIS `
      -VMName myVM1 `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -Settings $publicSettings
    ```

3. Cree dos máquinas virtuales más e instale IIS siguiendo los pasos que acaba de finalizar. Escriba los nombres de *myVM2* y *myVM3* para los nombres y los valores de VMName en Set-AzureRmVMExtension.

## <a name="create-backend-pools-with-the-virtual-machines"></a>Creación de grupos de servidores back-end con las máquinas virtuales

1. Haga clic en **Todos los recursos** y, a continuación, haga clic en **myAppGateway**.
2. Haga clic en **Grupos de back-end**. Con la puerta de enlace de aplicaciones se crea un grupo predeterminado. Haga clic en **appGateayBackendPool**.
3. Haga clic en **Agregar destino** para agregar *myVM1* a appGatewayBackendPool.

    ![Incorporación de servidores back-end](./media/application-gateway-create-url-route-portal/application-gateway-backend.png)

4. Haga clic en **Save**(Guardar).
5. Haga clic en **Grupos de back-end** y, a continuación, haga clic en **Agregar**.
6. Escriba un nombre para *imagesBackendPool* y agregue *myVM2* con **Agregar destino**.
7. Haga clic en **OK**.
8. Haga clic de nuevo en **Agregar** para agregar otro grupo de servidores back-end con el nombre *videoBackendPool* y agregue *myVM3* a dicho grupo.

## <a name="create-a-backend-listener"></a>Crear un agente de escucha de back-end

1. Haga clic en **Agentes de escucha** y, a continuación, haga clic en **Básico**.
2. Escriba *myBackendListener* para el nombre, *myFrontendPort* para el nombre del puerto de front-end y, a continuación, *8080* como el puerto para el agente de escucha.
3. Haga clic en **OK**.

## <a name="create-a-path-based-routing-rule"></a>Crear una regla de enrutamiento basada en la ruta de acceso

1. Haga clic en **Reglas** y, a continuación, haga clic en **Basada en ruta de acceso**.
2. Escriba *rule2* para el nombre.
3. Escriba *Images* para el nombre de la primera ruta de acceso. Escriba */images /** para la ruta de acceso. Seleccione **imagesBackendPool** para el grupo de servidores back-end.
4. Escriba *Video* para el nombre de la segunda ruta de acceso. Escriba */video/** para la ruta de acceso. Seleccione **videoBackendPool** para el grupo de servidores back-end.

    ![Creación de una regla basada en ruta de acceso](./media/application-gateway-create-url-route-portal/application-gateway-route-rule.png)

5. Haga clic en **OK**.

## <a name="test-the-application-gateway"></a>Prueba de la puerta de enlace de aplicaciones

1. Haga clic en **Todos los recursos** y, a continuación, haga clic en **myAGPublicIPAddress**.

    ![Registro de la dirección IP pública de la puerta de enlace de aplicaciones](./media/application-gateway-create-url-route-portal/application-gateway-record-ag-address.png)

2. Copie la dirección IP pública y péguela en la barra de direcciones del explorador. Por ejemplo, http://http: / / 40.121.222.19.

    ![Prueba de la dirección URL base en la puerta de enlace de aplicaciones](./media/application-gateway-create-url-route-portal/application-gateway-iistest.png)

3. Cambie la dirección URL a http://&lt;dirección-ip&gt;:8080/video/test.htm, sustituyendo &lt;dirección-ip&gt; por su dirección IP y verá algo similar al ejemplo siguiente:

    ![Prueba de la dirección URL de imágenes en la puerta de enlace de aplicaciones](./media/application-gateway-create-url-route-portal/application-gateway-iistest-images.png)

4. Cambie la dirección URL a http://&lt;dirección-ip&gt;:8080/video/test.htm, sustituyendo &lt;dirección-ip&gt; por su dirección IP y verá algo similar al ejemplo siguiente:

    ![Prueba de la dirección URL de vídeo en la puerta de enlace de aplicaciones](./media/application-gateway-create-url-route-portal/application-gateway-iistest-video.png)

## <a name="next-steps"></a>pasos siguientes

En este artículo, ha aprendido cómo

> [!div class="checklist"]
> * Creación de una puerta de enlace de aplicaciones
> * Crear máquinas virtuales para servidores back-end
> * Crear grupos de back-end con los servidores back-end
> * Crear un agente de escucha de back-end
> * Crear una regla de enrutamiento basada en la ruta de acceso

Para más información acerca de las puertas de enlace de aplicaciones y sus recursos asociados, vaya a los artículos de procedimientos.