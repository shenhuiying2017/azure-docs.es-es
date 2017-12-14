---
title: "Administración de perfiles de la versión de la API de Azure Stack | Microsoft Docs"
description: "Aprenda los perfiles de la versión de la API en Azure Stack."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 6B749785-DCF5-4AD8-B808-982E7C6BBA0E
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2017
ms.author: mabrigg
ms.openlocfilehash: d86a54ea9e165269131eb961df7f74703f0ec6ff
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="manage-api-version-profiles-in-azure-stack"></a>Administre los perfiles de la versión de API en Azure Stack

La característica de la API de los perfiles de versión de Azure App Service proporciona una manera de administrar las diferencias de versión entre Azure y Azure Stack. Un perfil de la versión de la API es un conjunto de módulos de PowerShell AzureRM con versiones específicas de la API. Cada plataforma de la nube tiene un conjunto de perfiles de versión de API compatibles. Por ejemplo, Azure Stack es compatible con una versión de perfil con fecha específica **2017-03-09-perfil**, y Azure es compatible con la *última* versión de perfil de la API. Cuando se instala un perfil, se instalan los módulos de PowerShell AzureRM que se corresponden con el perfil especificado.

## <a name="install-the-powershell-module-required-to-use-api-version-profiles"></a>Instale el módulo de PowerShell necesario para usar perfiles de la versión de la API

El módulo **AzureRM.Bootstrapper** que está disponible a través de la Galería de PowerShell proporciona cmdlets de PowerShell que son necesarios para trabajar con perfiles de la versión de la API. Use el siguiente cmdlet para instalar el módulo **AzureRM.Bootstrapper**:

```PowerShell
Install-Module -Name AzureRm.BootStrapper
```
El módulo **AzureRM.Bootstrapper** es una versión preliminar, por lo que los detalles y las funcionalidades están sujetos a cambios. Para descargar e instalar la versión más reciente de este módulo desde la Galería de PowerShell, ejecute el siguiente cmdlet:

```PowerShell
Update-Module -Name "AzureRm.BootStrapper"
```

## <a name="install-a-profile"></a>Instalar un perfil

Use el cmdlet **Install-AzureRmProfile** con el perfil de versión **2017-03-09-perfil** de la API para instalar los módulos de AzureRM que necesita Azure Stack. 

>[!NOTE]
>Los módulos de administrador de la nube de Azure Stack no están instalados con este perfil de la versión de la API. Los módulos de administrador deben instalarse por separado según se especificó en el paso 3 del artículo [Instalación de PowerShell para Azure Stack](azure-stack-powershell-install.md).

```PowerShell 
Install-AzureRMProfile -Profile 2017-03-09-profile
```
## <a name="install-and-import-modules-in-a-profile"></a>Instalar e importar módulos en un perfil

Use el cmdlet de **AzureRmProfile Use** para instalar e importar módulos que están asociados a un perfil de la versión de la API. Puede importar un solo perfil de la versión de la API en una sesión de PowerShell. Para importar un perfil de versión de la API diferente, debe abrir una nueva sesión de PowerShell. El cmdlet **Use-AzureRMProfile** ejecuta las siguientes tareas:  
1. Comprueba si están instalados los módulos de PowerShell asociados al perfil de versión de la API especificado en el ámbito actual.  
2. Descarga e instala los módulos si no están ya instalados.   
3. Importa los módulos en la sesión actual de PowerShell. 

```PowerShell
# Installs and imports the specified API version profile into the current PowerShell session.
Use-AzureRmProfile -Profile 2017-03-09-profile -Scope CurrentUser

# Installs and imports the specified API version profile into the current PowerShell session without any prompts.
Use-AzureRmProfile -Profile 2017-03-09-profile -Scope CurrentUser -Force
```

Para instalar e importar los módulos seleccionados de AzureRM de un perfil de la versión de la API, ejecute el cmdlet **Use-AzureRMProfile** con el parámetro *Module*:

```PowerShell
# Installs and imports the Compute, Storage, and Network modules from the specified API version profile into your current PowerShell session.
Use-AzureRmProfile -Profile 2017-03-09-profile -Module AzureRM.Compute, AzureRM.Storage, AzureRM.Network
```

## <a name="get-the-installed-profiles"></a>Obtener los perfiles instalados

Use el cmdlet **Get AzureRmProfile** para obtener una lista disponible de perfiles de versión de la API: 

```PowerShell
# Lists all API version profiles provided by the AzureRM.BootStrapper module.
Get-AzureRmProfile -ListAvailable 

# Lists the API version profiles that are installed on your machine.
Get-AzureRmProfile
```
## <a name="update-profiles"></a>Administrar perfiles

Use el cmdlet **Update-AzureRmProfile** para actualizar los módulos de un perfil de versión de la API a la última versión de los módulos que están disponibles en la Galería de PowerShell. Se recomienda ejecutar el cmdlet **Update-AzureRmProfile** cmdlet en una nueva sesión de PowerShell para evitar conflictos al importar módulos. El cmdlet **Update-AzureRmProfile** ejecuta las siguientes tareas:

1. Comprueba si están instaladas las últimas versiones de los módulos en el perfil determinado de versión de la API para el ámbito actual.  
2. Solicita que instale los módulos si aún no están instalados.  
3. Instala e importa los módulos actualizados en la sesión actual de PowerShell.  

```PowerShell
Update-AzureRmProfile -Profile 2017-03-09-profile
```

Para quitar las versiones de los módulos instaladas anteriormente antes de actualizar a la última versión disponible, use el cmdlet **Update-AzureRmProfile** junto con el parámetro *-RemovePreviousVersions*:

```PowerShell 
Update-AzureRmProfile -Profile 2017-03-09-profile -RemovePreviousVersions
```

Este cmdlet ejecuta las siguientes tareas:  

1. Comprueba si están instaladas las últimas versiones de los módulos en el perfil determinado de versión de la API para el ámbito actual.  
2. Quita las versiones anteriores de módulos con el perfil de versión de API actual y en la sesión actual de PowerShell.  
3. Solicita que instale la última versión de los módulos.  
4. Instala e importa los módulos actualizados en la sesión actual de PowerShell.  
 
## <a name="uninstall-profiles"></a>Desinstalar perfiles

Use el cmdlet **Uninstall-AzureRmProfile** para desinstalar el perfil de versión de API especificado:

```PowerShell 
Uninstall-AzureRmProfile -Profile 2017-03-09-profile
```

## <a name="next-steps"></a>Pasos siguientes
* [Install PowerShell for Azure Stack](azure-stack-powershell-install.md) (Instalación de PowerShell para Azure Stack)
* [Configuración del entorno de PowerShell del usuario de Azure Stack](azure-stack-powershell-configure-user.md)  
