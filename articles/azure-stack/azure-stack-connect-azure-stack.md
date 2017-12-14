---
title: "Conexión a Azure Stack | Microsoft Docs"
description: Aprenda a conectarse a Azure Stack.
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 3cebbfa6-819a-41e3-9f1b-14ca0a2aaba3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/25/2017
ms.author: mabrigg
ms.openlocfilehash: d9d7beae9ea81f2568377ee3593362871aae98fe
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="connect-to-azure-stack"></a>Conexión a Azure Stack

*Se aplica a: Azure Stack Development Kit*

Para administrar recursos, primero debe conectarse a Azure Stack Development Kit. En este artículo se describen los pasos que debe seguir para conectarse al kit de desarrollo. Puede usar una de las siguientes opciones de conexión:

* [Conexión a Escritorio remoto](#connect-with-remote-desktop) Cuando se conecta mediante Conexión a Escritorio remoto, un usuario puede conectarse rápidamente desde el kit de desarrollo.
* [Red privada virtual (VPN)](#connect-with-vpn). Cuando se conecta mediante una red privada virtual, varios usuarios pueden conectarse a la vez desde los clientes externos a la infraestructura de Azure Stack (se requiere configuración).

<a name="connect-to-azure-stack-with-remote-desktop"></a>
##  <a name="connect-to-azure-stack-by-using-remote-desktop-connection"></a>Conexión a Azure Stack mediante Conexión a Escritorio remoto
Solo un usuario cada vez puede administrar los recursos del portal de operadores o el portal de usuarios mediante Conexión a Escritorio remoto.

1. Abra una Conexión a Escritorio remoto y conéctese al kit de desarrollo. Escriba **AzureStack\AzureStackAdmin** como el nombre de usuario. Use la contraseña del operador que especificó al configurar Azure Stack.  

2. En el equipo del kit de desarrollo, abra el Administrador del servidor. Seleccione **Servidor local**, desactive la casilla **Internet Explorer Enhanced Security** (Seguridad mejorada de Internet Explorer) y luego cierre el Administrador del servidor.

3. Para abrir el [portal de usuarios](azure-stack-key-features.md#portal), vaya a https://portal.local.azurestack.external/. Inicie sesión con las credenciales de usuario. Para abrir el [portal de operadores](azure-stack-key-features.md#portal) de Azure Stack, vaya a https://adminportal.local.azurestack.external/. Inicie sesión con las credenciales de Azure Active Directory (Azure AD) que especificó durante la instalación.

<a name="connect-to-azure-stack-with-vpn"></a>
## <a name="connect-to-azure-stack-by-using-vpn"></a>Conexión a Azure Stack mediante una red privada virtual

Puede establecer una conexión de red privada virtual de túnel dividido a Azure Stack Development Kit. Una conexión de red privada virtual puede usarse para acceder al portal de operadores de Azure Stack, al portal de usuarios y a herramientas instaladas localmente, como Visual Studio y PowerShell, para administrar recursos de Azure Stack. La conectividad VPN se admite en Azure AD y en las implementaciones de Servicios de federación de Active Directory (AD FS). Las conexiones VPN permiten que varios clientes puedan conectarse a Azure Stack al mismo tiempo. 

> [!NOTE] 
> Una conexión VPN no proporciona conectividad a las máquinas virtuales de la infraestructura de Azure Stack. 

### <a name="prerequisites"></a>Requisitos previos

1. Instale [Azure Stack-compatible Azure PowerShell](azure-stack-powershell-install.md) en su equipo local.  
2. Descargue las [herramientas necesarias para trabajar con Azure Stack](azure-stack-powershell-download.md). 

### <a name="set-up-vpn-connectivity"></a>Configuración de la conectividad VPN

Para crear una conexión VPN al kit de desarrollo, abra Windows PowerShell como administrador en el equipo local basado en Windows. A continuación, ejecute el siguiente script (actualice los valores de dirección IP y contraseña para su entorno):

```PowerShell 
# Configure Windows Remote Management (WinRM), if it's not already configured.
winrm quickconfig  

Set-ExecutionPolicy RemoteSigned

# Import the Connect module.
Import-Module .\Connect\AzureStack.Connect.psm1 

# Add the development kit computer’s host IP address and certificate authority (CA) to the list of trusted hosts. Make sure you update the IP address and password values for your environment. 

$hostIP = "<Azure Stack host IP address>"

$Password = ConvertTo-SecureString `
  "<operator's password provided when deploying Azure Stack>" `
  -AsPlainText `
  -Force

Set-Item wsman:\localhost\Client\TrustedHosts `
  -Value $hostIP `
  -Concatenate

# Create a VPN connection entry for the local user.
Add-AzsVpnConnection `
  -ServerAddress $hostIP `
  -Password $Password

```

Si la instalación se realiza correctamente, **azurestack** aparece en la lista de conexiones VPN.

![Conexiones de red](media/azure-stack-connect-azure-stack/image3.png)  

### <a name="connect-to-azure-stack"></a>Conexión a Azure Stack

Conéctese a la instancia de Azure Stack mediante uno los métodos siguientes:  

* Use el comando `Connect-AzsVpn `: 
    
  ```PowerShell
  Connect-AzsVpn `
    -Password $Password
  ```

  Cuando se le solicite, confíe en el host de Azure Stack e instale el certificado de **AzureStackCertificateAuthority** en el almacén de certificados del equipo local. (El símbolo del sistema puede quedar oculto por la ventana de PowerShell). 

* En el equipo local, seleccione **Configuración de red** > **VPN** > **azurestack** > **conectar**. En el símbolo del sistema de inicio de sesión, escriba el nombre de usuario (**AzureStack\AzureStackAdmin**) y la contraseña.

### <a name="test-vpn-connectivity"></a>Probar la conectividad VPN

Para probar la conexión del portal, abra un explorador web y navegue hasta el portal de usuarios (https://portal.local.azurestack.external/) o el portal de operadores (https://adminportal.local.azurestack.external/). Inicie sesión y cree los recursos.  

## <a name="next-steps"></a>Pasos siguientes

[Máquinas virtuales disponibles para los usuarios de Azure Stack](azure-stack-tutorial-tenant-vm.md)

