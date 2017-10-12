---
title: "Conexión a Azure Stack | Microsoft Docs"
description: "Obtenga información acerca de cómo conectar a Azure Stack"
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 3cebbfa6-819a-41e3-9f1b-14ca0a2aaba3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/22/2017
ms.author: sngun
ms.openlocfilehash: 914f2e5d10aa341cea5eba8c24c7c37610e6b626
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="connect-to-azure-stack"></a>Conexión a Azure Stack

Para administrar recursos, debe conectarse a Azure Stack Development Kit. Este tema detalla los pasos necesarios para conectar con el kit de desarrollo. Puede utilizar cualquiera de las siguientes opciones de conexión:

* [Escritorio remoto](#connect-with-remote-desktop): permite a un solo usuario simultáneo conectarse rápidamente al kit de desarrollo.
* [Red privada virtual (VPN)](#connect-with-vpn): permite que varios usuarios simultáneos se conecten desde clientes fuera de la infraestructura de Azure Stack (requiere configuración).

## <a name="connect-to-azure-stack-with-remote-desktop"></a>Conexión a Azure Stack con Escritorio remoto
Con una conexión a Escritorio remoto, un único usuario simultáneo puede trabajar con el portal para administrar los recursos.

1. Abra una conexión a Escritorio remoto y conecte con el kit de desarrollo. Escriba **AzureStack\AzureStackAdmin** como nombre de usuario y la contraseña de administrador que proporcionó durante la instalación de Azure Stack.  

2. En el equipo del kit de desarrollo, abra el Administrador del servidor, haga clic en **Servidor local**, desactive la opción de seguridad mejorada de Internet Explorer y, a continuación, cierre el Administrador del servidor.

3. Para abrir el portal, vaya a (https://portal.local.azurestack.external/) e inicie sesión con las credenciales de usuario.


## <a name="connect-to-azure-stack-with-vpn"></a>Conexión a Azure Stack con VPN

Puede establecer un túnel de conexión de red privada virtual (VPN) a Azure Stack Development Kit. A través de la conexión VPN, puede tener acceso al portal de administrador, al portal de usuario y a las herramientas instaladas localmente, como Visual Studio y PowerShell, para administrar los recursos de Azure Stack. Se admite la conectividad VPN en implementaciones basadas en Azure Active Directory (AAD) y en los Servicios de federación de Active Directory (AD FS). Las conexiones VPN permiten que varios clientes puedan conectarse a Azure Stack al mismo tiempo. 

> [!NOTE] 
> Esta conexión VPN no proporciona conectividad a las máquinas virtuales de infraestructura de Azure Stack. 

### <a name="prerequisites"></a>Requisitos previos

* Instale [Azure PowerShell compatible con Azure Stack](azure-stack-powershell-install.md) en el equipo local.  
* Descargue las [herramientas necesarias para trabajar con Azure Stack](azure-stack-powershell-download.md). 

### <a name="configure-vpn-connectivity"></a>Configuración de la conectividad VPN

Para crear una conexión VPN con el kit de desarrollo, abra una sesión de PowerShell con privilegios elevados desde el equipo local basado en Windows y ejecute el script siguiente (asegúrese de actualizar los valores de dirección IP y contraseña para su entorno):

```PowerShell 
# Configure winrm if it's not already configured
winrm quickconfig  

Set-ExecutionPolicy RemoteSigned

# Import the Connect module
Import-Module .\Connect\AzureStack.Connect.psm1 

# Add the development kit computer’s host IP address & certificate authority (CA) to the list of trusted hosts. Make sure to update the the IP address and password values for your environment. 

$hostIP = "<Azure Stack host IP address>"

$Password = ConvertTo-SecureString `
  "<Administrator password provided when deploying Azure Stack>" `
  -AsPlainText `
  -Force

Set-Item wsman:\localhost\Client\TrustedHosts `
  -Value $hostIP `
  -Concatenate

# Create a VPN connection entry for the local user
Add-AzsVpnConnection `
  -ServerAddress $hostIP `
  -Password $Password

```

Si la configuración se realiza correctamente, debería ver **azurestack** en la lista de conexiones VPN.

![Conexiones de red](media/azure-stack-connect-azure-stack/image3.png)  

### <a name="connect-to-azure-stack"></a>Conexión a Azure Stack

Conéctese a la instancia de Azure Stack mediante cualquiera de los dos métodos siguientes:  

* Mediante el uso del comando `Connect-AzsVpn `: 
    
  ```PowerShell
  Connect-AzsVpn `
    -Password $Password
  ```

  Cuando se le solicite, confíe en el host de Azure Stack e instale el certificado de **AzureStackCertificateAuthority** en el almacén de certificados del equipo local. (el símbolo del sistema podría aparecer detrás de la ventana de sesión de PowerShell). 

* En el equipo local, abra **Configuración de red** > **VPN** > haga clic en **azurestack** > **Conectar**. En el símbolo del sistema de inicio de sesión, escriba el nombre de usuario (AzureStack\AzureStackAdmin) y la contraseña.

### <a name="test-the-vpn-connectivity"></a>Prueba de la conectividad VPN

Para probar la conexión del portal, abra un explorador de Internet y navegue hasta el portal de usuario (https://portal.local.azurestack.external/), inicie sesión y cree recursos.  

## <a name="next-steps"></a>Pasos siguientes



