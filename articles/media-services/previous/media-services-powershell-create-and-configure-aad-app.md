---
title: Uso de PowerShell para crear una aplicación de Azure AD para acceder a la API de Azure Media Services | Microsoft Docs
description: Obtenga información sobre cómo usar PowerShell para crear una aplicación de Azure Active Directory (Azure AD) y configurarla para acceder a la API de Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/17/2017
ms.author: juliako
ms.openlocfilehash: 36f00c2b794730ae8191238c516cfedf871851bd
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
ms.locfileid: "33785794"
---
# <a name="use-powershell-to-create-an-azure-ad-app-to-use-with-the-azure-media-services-api"></a>Uso de PowerShell para crear una aplicación de Azure AD para usarla con la API de Azure Media Services

Obtenga información sobre cómo usar un script de PowerShell para crear una aplicación de Azure Active Directory (Azure AD) y la entidad de servicio para acceder a recursos de Azure Media Services.  

## <a name="prerequisites"></a>requisitos previos

- Una cuenta de Azure. Si no tiene cuenta, comience con una [evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Una cuenta de Media Services. Para más información, vea [Creación de una cuenta de Azure Media Services mediante Azure Portal](media-services-portal-create-account.md).
- Azure PowerShell (versión 0.8.8 o una versión posterior) Para más información, vea [Uso de Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).
- Cmdlets de Azure Resource Manager.  

## <a name="create-an-azure-ad-app-by-using-powershell"></a>Creación de una aplicación de Azure AD mediante PowerShell  

```powershell
Connect-AzureRmAccount
Import-Module AzureRM.Resources
Set-AzureRmContext -SubscriptionId $SubscriptionId
$ServicePrincipal = New-AzureRMADServicePrincipal -DisplayName $ApplicationDisplayName -Password $Password

Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id 
$NewRole = $null
$Scope = "/subscriptions/your subscription id/resourceGroups/userresourcegroup/providers/microsoft.media/mediaservices/your media account"

$Retries = 0;While ($NewRole -eq $null -and $Retries -le 6)
{
    # Sleep here for a few seconds to allow the service principal application to become active (usually, it will take only a couple of seconds)
    Sleep 15
    New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $ServicePrincipal.ApplicationId -Scope $Scope | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzureRMRoleAssignment -ServicePrincipalName $ServicePrincipal.ApplicationId -ErrorAction SilentlyContinue
    $Retries++;
}
```

Para más información, consulte los siguientes artículos.

- [Uso de Azure PowerShell para crear a una entidad de servicio para acceder a recursos](../../azure-resource-manager/resource-group-authenticate-service-principal.md)
- [Administración del control de acceso basado en rol con Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
- [Configuración manual de aplicaciones de demonio mediante certificados](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential/blob/master/Manual-Configuration-Steps.md#add-the-certificate-as-a-key-for-the-todolistdaemonwithcert-application-in-azure-ad)

## <a name="next-steps"></a>Pasos siguientes

Empiece a trabajar en la [carga de archivos en la cuenta](media-services-portal-upload-files.md).
