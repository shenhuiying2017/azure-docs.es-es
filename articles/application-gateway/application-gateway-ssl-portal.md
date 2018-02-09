---
title: "Creación de una puerta de enlace de aplicaciones con terminación SSL- Azure Portal | Microsoft Docs"
description: "Aprenda a crear una puerta de enlace de aplicaciones y a agregar un certificado para la terminación SSL mediante Azure Portal."
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
ms.openlocfilehash: daab3ada5ef0cc20883130e4c12b1dc3570e63b1
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/29/2018
---
# <a name="create-an-application-gateway-with-ssl-termination-using-the-azure-portal"></a>Creación de una puerta de enlace de aplicaciones con terminación SSL mediante de Azure Portal

Puede usar Azure Portal para crear una [puerta de enlace de aplicaciones](application-gateway-introduction.md) con un certificado para terminación SSL que use máquinas virtuales para servidores back-end.

En este artículo, aprenderá a:

> [!div class="checklist"]
> * Creación de un certificado autofirmado
> * Crear una puerta de enlace de aplicaciones con el certificado
> * Crear las máquinas virtuales que se utilizan como servidores back-end

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="log-in-to-azure"></a>Inicie sesión en Azure.

Inicie sesión en Azure Portal en: [http://portal.azure.com](http://portal.azure.com).

## <a name="create-a-self-signed-certificate"></a>Creación de un certificado autofirmado

En esta sección, utilice [New-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) para crear un certificado autofirmado que carga en Azure Portal al crear el agente de escucha para la puerta de enlace de aplicaciones.

En el equipo local, abra una ventana de Windows PowerShell como administrador. Ejecute el siguiente comando para crear el certificado:

```powershell
New-SelfSignedCertificate \
  -certstorelocation cert:\localmachine\my \
  -dnsname www.contoso.com
```

Debería ver algo parecido a esta respuesta:

```
PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\my

Thumbprint                                Subject
----------                                -------
E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630  CN=www.contoso.com

Use [Export-PfxCertificate](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate) with the Thumbprint that was returned to export a pfx file from the certificate:
```

```powershell
$pwd = ConvertTo-SecureString -String "Azure123456!" -Force -AsPlainText
Export-PfxCertificate \
  -cert cert:\localMachine\my\E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630 \
  -FilePath c:\appgwcert.pfx \
  -Password $pwd
```

## <a name="create-an-application-gateway"></a>Creación de una puerta de enlace de aplicaciones

Se necesita una red virtual para la comunicación entre los recursos que se crean. En este ejemplo se crean dos subredes: una para la puerta de enlace de aplicaciones y la otra para los servidores back-end. Puede crear una red virtual a la vez que crea la puerta de enlace de aplicaciones.

1. Haga clic en **Nuevo** en la esquina superior izquierda de Azure Portal.
2. Seleccione **Redes** y **Application Gateway** en la lista de destacados.
3. Escriba *myAppGateway* para el nombre de la puerta de enlace de aplicaciones y *myResourceGroupAG* para el nuevo grupo de recursos.
4. Acepte los valores predeterminados para las demás opciones y haga clic en **Aceptar**.
5. Haga clic en **Elegir una red virtual**, luego en **Crear nueva** y, después, especifique estos valores para la red virtual:

    - *myVNet*: como nombre de la red virtual.
    - *10.0.0.0/16*: como espacio de direcciones de la red virtual.
    - *myAGSubnet*: como nombre de subred.
    - *10.0.0.0/24*: como espacio de direcciones de la subred.

    ![Creación de una red virtual](./media/application-gateway-ssl-portal/application-gateway-vnet.png)

6. Haga clic en **Aceptar** para crear la red virtual y la subred.
7. Haga clic en **Elegir una dirección IP pública** y en **Crear nueva** y, a continuación, escriba el nombre de la dirección IP pública. En este ejemplo, la dirección IP pública se llama *myAGPublicIPAddress*. Acepte los valores predeterminados para las demás opciones y haga clic en **Aceptar**.
8. Haga clic en **HTTPS** para el protocolo del agente de escucha y asegúrese de que el puerto esté definido como **443**.
9. Haga clic en el icono de la carpeta y busque el certificado *appgwcert.pfx* que creó anteriormente para cargarlo.
10. Escriba *mycert1* para el nombre del certificado y *Azure123456!* para la contraseña y, después, haga clic en **Aceptar**.

    ![Creación de una nueva puerta de enlace de aplicaciones](./media/application-gateway-ssl-portal/application-gateway-create.png)

11. Revise la configuración en la página de resumen y, a continuación, haga clic en **Aceptar** para crear los recursos de red y la puerta de enlace de aplicaciones. La creación de la puerta de enlace de aplicaciones puede tardar varios minutos, espere a que finalice correctamente la implementación antes de pasar a la sección siguiente.

### <a name="add-a-subnet"></a>Incorporación de una subred

1. Haga clic en **Todos los recursos** en el menú izquierdo y, después, haga clic en **myVNet** en la lista de recursos.
2. Haga clic en **Subredes** y en **Subred**.

    ![Creación de una subred](./media/application-gateway-ssl-portal/application-gateway-subnet.png)

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

    ![Instalación de la extensión personalizada](./media/application-gateway-ssl-portal/application-gateway-extension.png)

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
4. Haga clic en **Grupos de back-end**. Con la puerta de enlace de aplicaciones se crea un grupo predeterminado. Haga clic en **appGateayBackendPool**.
5. Haga clic en **Agregar destino** para agregar las máquinas virtuales que creó en el grupo de servidores back-end.

    ![Incorporación de servidores back-end](./media/application-gateway-ssl-portal/application-gateway-backend.png)

6. Haga clic en **Save**(Guardar).

## <a name="test-the-application-gateway"></a>Prueba de la puerta de enlace de aplicaciones

1. Haga clic en **Todos los recursos** y, a continuación, haga clic en **myAGPublicIPAddress**.

    ![Registro de la dirección IP pública de la puerta de enlace de aplicaciones](./media/application-gateway-ssl-portal/application-gateway-ag-address.png)

2. Copie la dirección IP pública y péguela en la barra de direcciones del explorador. Para aceptar la advertencia de seguridad si usó un certificado autofirmado, seleccione Detalles y, a continuación, Acceder a la página web:

    ![Advertencia de seguridad](./media/application-gateway-ssl-portal/application-gateway-secure.png)

    El sitio web IIS protegido se muestra ahora como en el ejemplo siguiente:

    ![Prueba de la dirección URL base en la puerta de enlace de aplicaciones](./media/application-gateway-ssl-portal/application-gateway-iistest.png)

## <a name="next-steps"></a>Pasos siguientes

En este tutorial aprendió lo siguiente:

> [!div class="checklist"]
> * Creación de un certificado autofirmado
> * Crear una puerta de enlace de aplicaciones con el certificado
> * Crear las máquinas virtuales que se utilizan como servidores back-end

Para más información acerca de las puertas de enlace de aplicaciones y sus recursos asociados, vaya a los artículos de procedimientos.