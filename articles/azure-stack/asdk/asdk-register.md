---
title: Registro del Kit de desarrollo de Azure Stack en Azure | Microsoft Docs
description: Describe cómo registrar Azure Stack en Azure para habilitar la redifusión de Marketplace y los informes de uso.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/17/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: eb1f939f76c3528f05a9002b6365359fb6599aa2
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/20/2018
ms.locfileid: "34357731"
---
# <a name="azure-stack-registration"></a>Registro de Azure Stack
La instalación del Kit de desarrollo de Azure Stack se puede registrar en Azure para descargar elementos de Marketplace de Azure y configurar informes de datos comerciales para Microsoft. Es necesario que el registro admita todas funcionalidades de Azure Stack, incluida la redifusión de Marketplace. Dicho registro es aconsejable, porque permite probar una funcionalidad importante de Azure Stack, como es la redifusión de Marketplace y los informes de uso. Después de registrar Azure Stack, se informa del uso para el comercio de Azure. Puede verlo en la suscripción que usó para el registro. Sin embargo, a los usuarios del ASDK no se les cobra ningún uso que comuniquen.

Si no registra su ASDK, puede que vea la alerta de advertencia **Activación necesaria** que le informa que debe registrar el Kit de desarrollo de Azure Stack. Este comportamiento es normal.

## <a name="register-azure-stack-with-azure"></a>Registro de Azure Stack con Azure 
Siga estos pasos para registrar el ASDK en Azure.

> [!NOTE]
> Todos estos pasos se deben ejecutar desde un equipo que tenga acceso al punto de conexión con privilegios. En el caso del Kit de desarrollo de Azure Stack, este es el equipo host del kit de desarrollo. 

Antes de usar estos pasos para registrar el ASDK en Azure, asegúrese de que ha instalado Azure Stack PowerShell y ha descargado las herramientas de Azure Stack como se describe en el artículo de [configuración posterior a la implementación](asdk-post-deploy.md). 

1. Abra una consola de PowerShell como administrador.  

2. Ejecute los siguientes comandos de PowerShell para registrar la instalación de ASDK en Azure (tendrá que iniciar sesión tanto en su suscripción a Azure como en la instalación de ASDK local):

    ```PowerShell
    # Add the Azure cloud subscription environment name. Supported environment names are AzureCloud or, if using a China Azure Subscription, AzureChinaCloud.
    Add-AzureRmAccount -EnvironmentName "AzureCloud"

    # Register the Azure Stack resource provider in your Azure subscription
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack

    #Import the registration module that was downloaded with the GitHub tools
    Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

    #Register Azure Stack
    $AzureContext = Get-AzureRmContext
    $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
    Set-AzsRegistration `
        -PrivilegedEndpointCredential $CloudAdminCred `
        -PrivilegedEndpoint AzS-ERCS01 `
        -BillingModel Development

3. When the script completes, you should see this message: **Your environment is now registered and activated using the provided parameters.**

    ![](media/asdk-register/1.PNG) 

## Verify the registration was successful
Follow these steps to verify that the ASDK registration with Azure was successful.

1. Sign in to the [Azure Stack administration portal](https://adminportal.local.azurestack.external).

2. Click **Marketplace Management** > **Add from Azure**.

    ![](media/asdk-register/2.PNG) 

3. If you see a list of items available from Azure, your activation was successful.

    ![](media/asdk-register/3.PNG) 

## Next steps
[Add an Azure Stack marketplace item](asdk-marketplace-item.md)
