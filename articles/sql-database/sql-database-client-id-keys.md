---
title: "Obtención de valores para la autenticación de aplicaciones: Azure SQL Database | Microsoft Docs"
description: "Cree una entidad de servicio para tener acceso a SQL Database desde el código."
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
tags: 
ms.assetid: b43e43bb-6660-49e6-b069-abde97eb5770
ms.service: sql-database
ms.custom: develop apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Inactive
ms.date: 09/30/2016
ms.author: sstein
ms.openlocfilehash: e76144bcb65da992c6d723d7333b4db8aa1ca488
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/31/2017
---
# <a name="get-the-required-values-for-authenticating-an-application-to-access-sql-database-from-code"></a>Obtención de los valores necesarios para autenticar una aplicación para obtener acceso a SQL Database desde el código
Para crear y administrar SQL Database desde el código debe registrar la aplicación en el dominio de Azure Active Directory (AAD) en la suscripción donde se han creado los recursos de Azure.

## <a name="create-a-service-principal-to-access-resources-from-an-application"></a>Creación de una entidad de servicio para acceder a recursos desde una aplicación
Debe tener la versión más reciente de [Azure PowerShell](https://msdn.microsoft.com/library/mt619274.aspx) instalada y ejecutándose. Para obtener información detallada, vea [Instalación y configuración de Azure PowerShell](/powershell/azureps-cmdlets-docs).

El siguiente script de PowerShell crea la aplicación de Active Directory (AD) y la entidad de servicio que se necesitan para autenticar la aplicación de C#. En la salida del script, se encuentran los valores que se necesitan para el anterior ejemplo de C#. Para ver información detallada, consulte [Uso de Azure PowerShell para crear una entidad de servicio para acceder a recursos](../azure-resource-manager/resource-group-authenticate-service-principal.md).

    # Sign in to Azure.
    Add-AzureRmAccount

    # If you have multiple subscriptions, uncomment and set to the subscription you want to work with.
    #$subscriptionId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}"
    #Set-AzureRmContext -SubscriptionId $subscriptionId

    # Provide these values for your new AAD app.
    # $appName is the display name for your app, must be unique in your directory.
    # $uri does not need to be a real uri.
    # $secret is a password you create.

    $appName = "{app-name}"
    $uri = "http://{app-name}"
    $secret = "{app-password}"

    # Create a AAD app
    $azureAdApplication = New-AzureRmADApplication -DisplayName $appName -HomePage $Uri -IdentifierUris $Uri -Password $secret

    # Create a Service Principal for the app
    $svcprincipal = New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

    # To avoid a PrincipalNotFound error, I pause here for 15 seconds.
    Start-Sleep -s 15

    # If you still get a PrincipalNotFound error, then rerun the following until successful. 
    $roleassignment = New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $azureAdApplication.ApplicationId.Guid


    # Output the values we need for our C# application to successfully authenticate

    Write-Output "Copy these values into the C# sample app"

    Write-Output "_subscriptionId:" (Get-AzureRmContext).Subscription.SubscriptionId
    Write-Output "_tenantId:" (Get-AzureRmContext).Tenant.TenantId
    Write-Output "_applicationId:" $azureAdApplication.ApplicationId.Guid
    Write-Output "_applicationSecret:" $secret




## <a name="see-also"></a>Consulte también
* [Creación de una Base de datos SQL con C#](sql-database-get-started-csharp.md)
* [Conexión a Base de datos SQL mediante autenticación de Azure Active Directory](sql-database-aad-authentication.md)

