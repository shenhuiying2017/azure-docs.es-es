---
title: Instalación de PowerShell para Azure Stack | Microsoft Docs
description: Aprenda a instalar PowerShell para Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 5/18/2018
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: b3c09582f5135655640768bcbcbef91750827bfa
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/20/2018
ms.locfileid: "34358897"
---
# <a name="install-powershell-for-azure-stack"></a>Instalación de PowerShell para Azure Stack

*Se aplica a: sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Los módulos de Azure PowerShell compatibles con Azure Stack se requieren para trabajar con Azure Stack. En esta guía, se le explican los pasos necesarios para instalar PowerShell para Azure Stack.

Este artículo contiene instrucciones detalladas para instalar PowerShell para Azure Stack.

> [!Note]
> En los siguientes pasos se necesita PowerShell 5.0. Para comprobar la versión, ejecute $PSVersionTable.PSVersion y compare la versión **principal**.

Los comandos de PowerShell para Azure Stack se instalan a través de la Galería de PowerShell. Puede utilizar el siguiente procedimiento para validar si PSGallery se ha registrado como un repositorio, abrir una sesión de PowerShell con privilegios elevados y ejecutar el siguiente comando:

```PowerShell  
Get-PSRepository -Name "PSGallery"
```

Si el repositorio no está registrado, abra una sesión de PowerShell con privilegios elevados y ejecute el siguiente comando:

```PowerShell  
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
```
> [!Note]  
> Este paso requiere acceso a Internet. 

## <a name="uninstall-existing-versions-of-powershell"></a>Desinstalación de las versiones existentes de PowerShell

Antes de instalar la versión requerida, asegúrese de desinstalar los módulos de PowerShell de Azure Stack instalados previamente. Puede desinstalarlos mediante uno de los dos métodos siguientes:

 - Para desinstalar los módulos de PowerShell existentes, cierre todas las sesiones de PowerShell activas y ejecute el siguiente comando:

  ```PowerShell
    Uninstall-Module AzureRM.AzureStackAdmin -Force
    Uninstall-Module AzureRM.AzureStackStorage -Force
    Uninstall-Module -Name AzureStack -Force
  ```

 - Elimine todas las carpetas que empiecen con "Azure" de las carpetas `C:\Program Files\WindowsPowerShell\Modules` y `C:\Users\AzureStackAdmin\Documents\WindowsPowerShell\Modules`. Al eliminar estas carpetas se quitan todos los módulos de PowerShell existentes.

En las secciones siguientes se describen los pasos necesarios para instalar PowerShell para Azure Stack. PowerShell puede instalarse en Azure Stack que opere en un escenario conectado, parcialmente conectado o sin conexión.

## <a name="install-powershell-in-a-connected-scenario-with-internet-connectivity"></a>Instalación de PowerShell en un escenario conectado (con conectividad a Internet)

Los módulos AzureRM compatibles con Azure Stack se instalan a través de perfiles de la versión de API. Azure Stack requiere el perfil de la versión de API **2017-03-09-profile**, que está disponible al instalar el módulo AzureRM.Bootstrapper. Para obtener información acerca de los perfiles de la versión de API y los cmdlets proporcionados por ellos, consulte el artículo sobre [administración de perfiles de la versión de API](user/azure-stack-version-profiles.md). Además de los módulos AzureRM, también debe instalar los módulos de PowerShell específicos de Azure Stack. Ejecute el siguiente script de PowerShell para instalar estos módulos en la estación de trabajo de desarrollo:

  ```PowerShell  
# Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet 
Install-Module -Name AzureRm.BootStrapper 

# Install and import the API Version Profile required by Azure Stack into the current PowerShell session. 
Use-AzureRmProfile -Profile 2017-03-09-profile -Force 

# Install Module Version 1.3.0 if Azure Stack is running 1804 at a minimum 
Install-Module -Name AzureStack -RequiredVersion 1.3.0 

# Install Module Version 1.2.11 if Azure Stack is running a lower version than 1804 
Install-Module -Name AzureStack -RequiredVersion 1.2.11 
  ```

Para confirmar la instalación, ejecute el siguiente comando:

```PowerShell  
Get-Module -ListAvailable | where-Object {$_.Name -like "Azs*"}
```

Si la instalación es correcta, los módulos AzureRM y AzureStack se muestran en la salida.

## <a name="install-powershell-in-a-disconnected-or-a-partially-connected-scenario-with-limited-internet-connectivity"></a>Instalación de PowerShell en un escenario desconectado o parcialmente conectado (con conectividad a Internet limitada)

En un escenario desconectado, primero debe descargar los módulos de PowerShell en un equipo que tenga conectividad a Internet y luego transferirlos al Kit de desarrollo de Azure Stack para la instalación.

> [!IMPORTANT]  
> La versión del módulo de PowerShell Azure Stack 1.3.0 incluye una lista de los principales cambios. Para actualizar desde la versión 1.2.11, consulte la [guía de migración](https://aka.ms/azspowershellmigration).

1. Inicie sesión en un equipo que tenga conexión a Internet y use el script siguiente para descargar los paquetes AzureRM y AzureStack en el equipo local:

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
     -RequiredVersion 1.3.0 
   ```

  > [!Important]  
  > Si no ejecuta Azure Stack con la actualización 1804 o superior, cambie el valor del parámetro **requiredversion** a `1.2.11`. 

2. Copie los paquetes descargados a través en un dispositivo USB.

3. Inicie sesión en la estación de trabajo y copie los paquetes desde el dispositivo USB en una ubicación en dicha estación de trabajo.

4. Ahora debe registrar esta ubicación como el repositorio predeterminado e instalar los módulos AzureRM y AzureStack desde este repositorio:

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

## <a name="configure-powershell-to-use-a-proxy-server"></a>Configuración de PowerShell para usar un servidor proxy

En escenarios que requieren un servidor proxy para tener acceso a Internet, primero debe configurar PowerShell para usar un servidor proxy existente.

1. Abra un símbolo del sistema de PowerShell con privilegios elevados.
2. Ejecute los comandos siguientes:

````PowerShell  
  #To use Windows credentials for proxy authentication
  [System.Net.WebRequest]::DefaultWebProxy.Credentials = [System.Net.CredentialCache]::DefaultCredentials

  #Alternatively, to prompt for separate credentials that can be used for #proxy authentication

  [System.Net.WebRequest]::DefaultWebProxy.Credentials = Get-Credential
````

## <a name="next-steps"></a>Pasos siguientes

 - [Descarga de herramientas de Azure Stack desde GitHub](azure-stack-powershell-download.md)
 - [Configuración del entorno de PowerShell del usuario de Azure Stack](user/azure-stack-powershell-configure-user.md)  
 - [Configuración del entorno de PowerShell del operador de Azure Stack](azure-stack-powershell-configure-admin.md) 
 - [Administración de perfiles de la versión de API en Azure Stack](user/azure-stack-version-profiles.md)  
