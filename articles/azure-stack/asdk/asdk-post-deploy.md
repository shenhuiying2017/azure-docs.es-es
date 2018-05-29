---
title: Configuraciones después de la implementación del Kit de desarrollo de Azure Stack (ASDK) | Microsoft Docs
description: Describe los cambios de configuración que se recomiendan realizar después de instalar el Kit de desarrollo de Azure Stack (ASDK).
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 4b58f3496b25e4fc04761b9df6e27f8313b35fe9
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2018
ms.locfileid: "33204639"
---
# <a name="post-asdk-installation-configuration-tasks"></a>Tareas de configuración posteriores a la instalación de ASDK
Después de [instalar el Kit de desarrollo de Azure Stack](asdk-install.md), es aconsejable realizar algunos cambios en la configuración. 

## <a name="install-azure-stack-powershell"></a>Instalación de PowerShell de Azure Stack 
Los módulos de Azure PowerShell compatibles con Azure Stack se requieren para trabajar con Azure Stack.

Los comandos de PowerShell para Azure Stack se instalan a través de la Galería de PowerShell. Para registrar el repositorio PSGallery, abra una sesión de PowerShell con privilegios elevados y ejecute el siguiente comando:

``` Powershell
Set-PSRepository `
  -Name "PSGallery" `
  -InstallationPolicy Trusted
```

 Los módulos AzureRM compatibles con Azure Stack se instalan a través de perfiles de la versión de API. Azure Stack requiere el perfil de la versión de API 2017-03-09-profile, que está disponible al instalar el módulo AzureRM.Bootstrapper. 
 
 El módulo de Azure Stack PowerShell más reciente se puede instalar con o sin conectividad a internet en el equipo host de ASDK:

> [!IMPORTANT]
> Antes de instalar la versión requerida, asegúrese de [desinstalar los módulos de Azure PowerShell existentes](.\.\azure-stack-powershell-install.md#uninstall-existing-versions-of-powershell).

- **Con conexión a internet** desde el equipo host de ASDK. Ejecute el siguiente script de PowerShell para instalar estos módulos en la instalación del kit de desarrollo:

  ``` PowerShell
  # Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet 
  Install-Module `
    -Name AzureRm.BootStrapper

  # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
  Use-AzureRmProfile `
    -Profile 2017-03-09-profile -Force

  Install-Module `
    -Name AzureStack `
    -RequiredVersion 1.2.11
  ```
  Si la instalación es correcta, los módulos AzureRM y AzureStack se muestran en la salida.

- **Sin conexión a internet** desde el equipo host de ASDK. En un escenario sin conexión, primero hay que descargar los módulos de PowerShell en un equipo que tenga conexión a Internet con los siguientes comandos de PowerShell:

  ```PowerShell
  $Path = "<Path that is used to save the packages>"

  Save-Package `
    -ProviderName NuGet `
    -Source https://www.powershellgallery.com/api/v2 `
    -Name AzureRM `
    -Path $Path `
    -Force `
    -RequiredVersion 1.2.11

  Save-Package `
    -ProviderName NuGet `
    -Source https://www.powershellgallery.com/api/v2 `
    -Name AzureStack `
    -Path $Path `
    -Force `
    -RequiredVersion 1.2.11
  ```
  Después, se copian los paquetes descargados al equipo de ASDK y, seguidamente, se registra esta ubicación como el repositorio predeterminado y se instalan los módulos AzureRM y AzureStack desde este repositorio:

    ```PowerShell
    $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
    $RepoName = "MyNuGetSource"

    Register-PSRepository `
      -Name $RepoName `
      -SourceLocation $SourceLocation `
      -InstallationPolicy Trusted

    Install-Module AzureRM `
      -Repository $RepoName

    Install-Module AzureStack `
      -Repository $RepoName
    ```

## <a name="download-the-azure-stack-tools"></a>Descarga de las herramientas de Azure Stack
[AzureStack-Tools](https://github.com/Azure/AzureStack-Tools) es un repositorio de GitHub que hospeda módulos de PowerShell para administrar e implementar recursos en Azure Stack. Para obtener estas herramientas, clone el repositorio de GitHub o descargue la carpeta AzureStack-Tools mediante la ejecución del script siguiente:

  ```PowerShell
  # Change directory to the root directory. 
  cd \

  # Enforce usage of TLSv1.2 to download the Azure Stack tools archive from GitHub
  [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
  invoke-webrequest `
    https://github.com/Azure/AzureStack-Tools/archive/master.zip `
    -OutFile master.zip

  # Expand the downloaded files.
  expand-archive master.zip `
    -DestinationPath . `
    -Force

  # Change to the tools directory.
  cd AzureStack-Tools-master
  ```

## <a name="validate-the-asdk-installation"></a>Validación de la instalación de ASDK
Para asegurarse de que ASDK se ha implementado correctamente, puede usar el cmdlet Test-AzureStack siguiendo estos pasos:

1. Inicie sesión como AzureStack\CloudAdmin en el equipo host de ASDK.
2. Abra PowerShell como administrador (no ISE de PowerShell).
3. Ejecute: `Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint`
4. Ejecute: `Test-AzureStack`

Las pruebas tardarán algunos minutos en completarse. Si la instalación no se realizó correctamente, el resultado sería similar a este:

![test-azurestack](media/asdk-post-deploy/test-azurestack.png)

Si se produjo un error, siga los pasos de la solución de problemas para obtener ayuda.

## <a name="activate-the-administrator-and-tenant-portals"></a>Activación de los portales de administrador y de inquilino
Después de las implementaciones que usa Azure AD, debe activar los portales de administrador y de inquilino de Azure Stack. Esta activación concede los permisos correctos al portal de Azure Stack y al de Azure Resource Manager (permisos que se muestran en la página de consentimiento) para todos los usuarios del directorio.

- En el portal de administrador, vaya a https://adminportal.local.azurestack.external/guest/signup, lea la información y haga clic en **Aceptar**. Después de aceptar, puede agregar los administradores de servicios que no sean también administradores de inquilinos de directorio.

- En el portal del inquilino, vaya a https://portal.local.azurestack.external/guest/signup, lea la información y haga clic en **Aceptar**. Después de aceptar, los usuarios del directorio pueden iniciar sesión en el portal de inquilino. 

> [!NOTE] 
> Si no se activan los portales, solo el administrador del directorio puede iniciar sesión y usar los portales. Si otro usuario inicia sesión, verá un error que indica que el administrador no ha concedido permisos a otros usuarios. Cuando el administrador no pertenece de forma nativa al directorio en el que está registrado Azure Stack, el directorio de este se debe anexar a la dirección URL de activación. Por ejemplo, si Azure Stack está registrado en fabrikam.onmicrosoft.com y el usuario administrador es admin@contoso.com, vaya a https://portal.local.azurestack.external/guest/signup/fabrikam.onmicrosoft.com para activar el portal. 

## <a name="reset-the-password-expiration-policy"></a>Restablecimiento de la directiva de expiración de contraseñas 
Para asegurarse de que la contraseña del host del kit de desarrollo no expire antes de que termine el período de evaluación, siga estos pasos después de implementar ASDK.

### <a name="to-change-the-password-expiration-policy-from-powershell"></a>Para cambiar la directiva de caducidad de contraseña desde Powershell:
En una consola de Powershell con privilegios elevados, ejecute el comando:

```powershell
Set-ADDefaultDomainPasswordPolicy -MaxPasswordAge 180.00:00:00 -Identity azurestack.local
```

### <a name="to-change-the-password-expiration-policy-manually"></a>Para cambiar la directiva de caducidad de contraseña manualmente:
1. En el host del kit de desarrollo, abra **Administración de directivas de grupo** (GPMC.MMC) y vaya a **Administración de directivas de grupo** – **Bosque: azurestack.local** – **Dominios**: **azurestack.local**.
2. Haga clic con el botón derecho en **Directiva predeterminada de dominio**  y, después, haga clic en **Editar**.
3. En el Editor de administración de directivas de grupo, vaya a **Configuración del equipo** – **Directivas** – **Configuración de Windows** – **Configuración de seguridad** – **Directivas de cuenta** – **Directiva de contraseñas**.
4. En el panel derecho, haga doble clic en **Vigencia máxima de la contraseña**.
5. En el cuadro de diálogo **Maximum password age Properties** (Propiedades de Vigencia máxima de la contraseña), cambie el valor **Password will expire in** (La contraseña expirará en) a **180** y, después, haga clic en **Aceptar**.

![Consola de administración de directivas de grupo](media/asdk-post-deploy/gpmc.png)


## <a name="next-steps"></a>Pasos siguientes
[Registro del Kit de desarrollo de Azure Stack en Azure](asdk-register.md)
