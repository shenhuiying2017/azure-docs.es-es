---
title: Desinstalación de componentes de Trabajos de base de datos elástica
description: Aprenda a desinstalar los componentes de Trabajos de Elastic Database mediante Azure Portal de PowerShell.
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: f5d0579cbb5f787ce08e2a2cea58d3c39a967970
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/20/2018
---
# <a name="uninstall-elastic-database-jobs-components"></a>Desinstalación de componentes de trabajos de Elastic Database.
Los componentes de **Trabajos de Elastic Database** se pueden desinstalar mediante Azure Portal o PowerShell.

## <a name="uninstall-elastic-database-jobs-components-using-the-azure-portal"></a>Desinstalación de componentes de Trabajos de base de datos elástica mediante Azure Portal
1. Abra el [Azure Portal](https://portal.azure.com/).
2. Navegue hasta la suscripción que contiene componentes de **trabajos de Elastic Database** , es decir, la suscripción en la que se instalaron los componentes de trabajos de Elastic Database.
3. Haga clic en **Examinar** y luego en **Grupos de recursos**.
4. Seleccione el grupo de recursos denominado "__ElasticDatabaseJob".
5. Elimine el grupo de recursos.

## <a name="uninstall--elastic-database-jobs-components-using-powershell"></a>Desinstalación de componentes de trabajos de Elastic Database mediante PowerShell
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
Para reinstalar trabajos de Elastic Database, vea [Instalación del servicio de trabajos de Elastic Database](sql-database-elastic-jobs-service-installation.md)

Si desea obtener más información sobre trabajos de Elastic Database, vea [Información general de trabajos de Elastic Database](sql-database-elastic-jobs-overview.md).

<!--Image references-->


