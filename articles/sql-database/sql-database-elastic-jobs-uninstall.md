---
title: "Desinstalación de componentes de Trabajos de base de datos elástica"
description: Aprenda a desinstalar los componentes de Trabajos de Elastic Database mediante Azure Portal de PowerShell.
services: sql-database
documentationcenter: 
manager: jhubbard
author: ddove
editor: 
ms.assetid: bfc9d820-edbd-4fca-bfbf-1f339cfcc448
ms.service: sql-database
ms.workload: Inactive
ms.custom: scale out apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: ddove
ms.openlocfilehash: 5e665ee8cc9efacbd31111dc0458ad6096e457c0
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/31/2017
---
# <a name="uninstall-elastic-database-jobs-components"></a>Desinstalación de componentes de Trabajos de base de datos elástica.
Los componentes de **Trabajos de Elastic Database** se pueden desinstalar mediante Azure Portal o PowerShell.

## <a name="uninstall-elastic-database-jobs-components-using-the-azure-portal"></a>Desinstalación de componentes de Trabajos de base de datos elástica mediante el Portal de Azure
1. Abra el [Portal de Azure](https://portal.azure.com/).
2. Navegue hasta la suscripción que contiene componentes de **Trabajos de base de datos elástica** , es decir, la suscripción en la que se instalaron los componentes de Trabajos de base de datos elástica.
3. Haga clic en **Examinar** y luego en **Grupos de recursos**.
4. Seleccione el grupo de recursos denominado "__ElasticDatabaseJob".
5. Elimine el grupo de recursos.

## <a name="uninstall--elastic-database-jobs-components-using-powershell"></a>Desinstalación de componentes de Trabajos de base de datos elástica mediante PowerShell
1. Inicie una ventana de comandos de Microsoft Azure PowerShell y navegue hasta el subdirectorio tools en la carpeta Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x: escriba **cd tools**.
   
     PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*>cd tools
2. Ejecute el script .\UninstallElasticDatabaseJobs.ps1 de PowerShell.
   
     PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>Unblock-File .\UninstallElasticDatabaseJobs.ps1   PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>.\UninstallElasticDatabaseJobs.ps1

O simplemente, ejecute el siguiente script, suponiendo que se usaron valores predeterminados en la instalación de los componentes:

        $ResourceGroupName = "__ElasticDatabaseJob"
        Switch-AzureMode AzureResourceManager

        $resourceGroup = Get-AzureResourceGroup -Name $ResourceGroupName
        if(!$resourceGroup)
        {
            Write-Host "The Azure Resource Group: $ResourceGroupName has already been deleted.  Elastic database job components are uninstalled."
            return
        }

        Write-Host "Removing the Azure Resource Group: $ResourceGroupName.  This may take a few minutes.”
        Remove-AzureResourceGroup -Name $ResourceGroupName -Force
        Write-Host "Completed removing the Azure Resource Group: $ResourceGroupName.  Elastic database job compoennts are now uninstalled."

## <a name="next-steps"></a>Pasos siguientes
Para reinstalar Trabajos de base de datos elástica, vea [Instalación del servicio Trabajos de base de datos elástica](sql-database-elastic-jobs-service-installation.md)

Si desea obtener más información sobre Trabajos de base de datos elástica, vea [Información general de Trabajos de base de datos elástica](sql-database-elastic-jobs-overview.md).

<!--Image references-->


