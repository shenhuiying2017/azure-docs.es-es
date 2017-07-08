---
title: Comandos de PowerShell basados en Azure Resource Manager para aplicaciones web de Azure | Microsoft Docs
description: Aprenda a usar los nuevos comandos de PowerShell basados en Azure Resource Manager para administrar aplicaciones web de Azure.
services: app-service\web
documentationcenter: 
author: ahmedelnably
manager: stefsch
editor: 
ms.assetid: 4231fbba-61e5-4f92-b958-531c066fb87f
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2016
ms.author: aelnably
ms.translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 8d574f051a327ba0409e6f25a5886af673d3d5e0
ms.contentlocale: es-es
ms.lasthandoff: 07/06/2017


---
# <a name="using-azure-resource-manager-based-powershell-to-manage-azure-web-apps"></a>Uso de PowerShell basado en Azure Resource Manager para administrar aplicaciones web de Azure
> [!div class="op_single_selector"]
> * [CLI de Azure](app-service-web-app-azure-resource-manager-xplat-cli.md)
> * [Azure PowerShell](app-service-web-app-azure-resource-manager-powershell.md)
> 
> 

En la versión 1.0.0 de Microsoft Azure PowerShell se han agregado nuevos comandos que proporcionan al usuario la capacidad de utilizar comandos de PowerShell basados en Azure Resource Manager para administrar aplicaciones web.

Para obtener información acerca de cómo administrar grupos de recursos, consulte [Uso de Azure PowerShell con Azure Resource Manager](../powershell-azure-resource-manager.md). 

Para más información acerca de la lista completa de parámetros y opciones de los cmdlets de PowerShell, consulte la [referencia completa de los cmdlets de PowerShell basados en Azure Resource Manager de aplicaciones web](https://msdn.microsoft.com/library/mt619237.aspx)

## <a name="managing-app-service-plans"></a>Administración de planes del Servicio de aplicaciones
### <a name="create-an-app-service-plan"></a>Creación de un plan del Servicio de aplicaciones
Para crear un plan de App Service, utilice el cmdlet **New-AzureRmAppServicePlan**.

Estas son las descripciones de los distintos parámetros:

* **Name**: el nombre del plan del Servicio de aplicaciones.
* **Location**: ubicación del plan del servicio.
* **ResourceGroupName**: grupo de recursos que incluye el plan del Servicio de aplicaciones recién creado.
* **Tier**: el plan de tarifa deseado (el valor predeterminado es Gratis y las restantes opciones son Compartido, Básico, Estándar y Premium).
* **WorkerSize**: el tamaño de los trabajos (el valor predeterminado es Pequeño si en el parámetro Tier se ha especificado el valor Básico, Estándar o Premium. Otras opciones son Mediano y Grande.)
* **NumberofWorkers**: el número de trabajos en el plan del Servicio de aplicaciones (el valor predeterminado es 1). 

Ejemplo para usar este cmdlet:

    New-AzureRmAppServicePlan -Name ContosoAppServicePlan -Location "South Central US" -ResourceGroupName ContosoAzureResourceGroup -Tier Premium -WorkerSize Large -NumberofWorkers 10

### <a name="create-an-app-service-plan-in-an-app-service-environment"></a>Creación de un plan del Servicio de aplicaciones en un entorno del Servicio de aplicaciones
Para crear un plan de App Service en App Service Environment, use el mismo comando **New-AzureRmAppServicePlan** con parámetros adicionales para especificar el nombre del ASE y el nombre del grupo de recursos del ASE.

Ejemplo para usar este cmdlet:

    New-AzureRmAppServicePlan -Name ContosoAppServicePlan -Location "South Central US" -ResourceGroupName ContosoAzureResourceGroup -AseName constosoASE -AseResourceGroupName contosoASERG -Tier Premium -WorkerSize Large -NumberofWorkers 10

Para obtener más información acerca del entorno del Servicio de aplicaciones, consulte [Introducción al entorno del Servicio de aplicaciones](app-service-app-service-environment-intro.md)

### <a name="list-existing-app-service-plans"></a>Enumeración de planes del Servicio de aplicaciones existentes
Para enumerar los planes del Servicio de aplicaciones existentes, use el cmdlet **Get-AzureRmAppServicePlan** .

Para enumerar todos los planes del Servicio de aplicaciones de su suscripción, use: 

    Get-AzureRmAppServicePlan

Para enumerar todos los planes del Servicio de aplicaciones de un grupo de recursos específico, use:

    Get-AzureRmAppServicePlan -ResourceGroupname ContosoAzureResourceGroup

Para obtener un plan del Servicio de aplicaciones específico, use:

    Get-AzureRmAppServicePlan -Name ContosoAppServicePlan


### <a name="configure-an-existing-app-service-plan"></a>Configuración de un plan del Servicio de aplicaciones existente
Para cambiar la configuración de un plan del Servicio de aplicaciones existente, use el cmdlet **Set-AzureRmAppServicePlan** . Puede cambiar el nivel, el tamaño del trabajo y el número de trabajos 

    Set-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -Tier Standard -WorkerSize Medium -NumberofWorkers 9

#### <a name="scaling-an-app-service-plan"></a>Escalado de un plan del Servicio de aplicaciones
Para escalar un plan del Servicio de aplicaciones existente, use:

    Set-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -NumberofWorkers 9

#### <a name="changing-the-worker-size-of-an-app-service-plan"></a>Cambio del tamaño de los trabajos de un plan del Servicio de aplicaciones
Para cambiar el tamaño de los trabajos de un plan del Servicio de aplicaciones existente, use:

    Set-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -WorkerSize Medium

#### <a name="changing-the-tier-of-an-app-service-plan"></a>Cambio del nivel de un plan del Servicio de aplicaciones
Para cambiar el nivel de un plan del Servicio de aplicaciones existente, use:

    Set-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -Tier Standard

### <a name="delete-an-existing-app-service-plan"></a>Eliminación de un plan del Servicio de aplicaciones
Para eliminar un plan de App Service existente, primero se deben mover o eliminar todas las aplicaciones web asignadas. A continuación, mediante el cmdlet **Remove-AzureRmAppServicePlan** puede eliminar el plan de App Service.

    Remove-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup

## <a name="managing-app-service-web-apps"></a>Administración de aplicaciones web del Servicio de aplicaciones
### <a name="create-a-web-app"></a>Creación de una aplicación web
Para crear una aplicación web, use el cmdlet **New-AzureRmWebApp**.

Estas son las descripciones de los distintos parámetros:

* **Name**: nombre de la aplicación web.
* **AppServicePlan**: nombre del plan del servicio que se utiliza para hospedar la aplicación web.
* **ResourceGroupName**: grupo de recursos que hospeda el plan del Servicio de aplicaciones.
* **Location**: la ubicación de la aplicación web.

Ejemplo para usar este cmdlet:

    New-AzureRmWebApp -Name ContosoWebApp -AppServicePlan ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -Location "South Central US"

### <a name="create-a-web-app-in-an-app-service-environment"></a>Creación de una aplicación web en App Service Environment
Creación de una aplicación web en App Service Environment (ASE). Use el mismo comando **New-AzureRmWebApp** con parámetros adicionales para especificar el nombre del ASE y el nombre del grupo de recursos al que pertenece el ASE.

    New-AzureRmWebApp -Name ContosoWebApp -AppServicePlan ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -Location "South Central US"  -ASEName ContosoASEName -ASEResourceGroupName ContosoASEResourceGroupName

Para obtener más información acerca del entorno del Servicio de aplicaciones, consulte [Introducción al entorno del Servicio de aplicaciones](app-service-app-service-environment-intro.md)

### <a name="delete-an-existing-web-app"></a>Eliminación de una aplicación web existente
Para eliminar una aplicación web existente, puede utilizar el cmdlet **Remove-AzureRmWebApp** , pero debe especificar el nombre de la aplicación web y el nombre del grupo de recursos.

    Remove-AzureRmWebApp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup


### <a name="list-existing-web-apps"></a>Lista de aplicaciones web existentes
Para enumerar las aplicaciones web existentes, use el cmdlet **Get-AzureRmWebApp** .

Para enumerar todas las aplicaciones web de su suscripción, use:

    Get-AzureRmWebApp

Para enumerar todas los aplicaciones web de un grupo de recursos específico, use:

    Get-AzureRmWebApp -ResourceGroupname ContosoAzureResourceGroup

Para obtener una aplicación web específica, use:

    Get-AzureRmWebApp -Name ContosoWebApp

### <a name="configure-an-existing-web-app"></a>Configuración de una aplicación web existente
Para cambiar los valores y la configuración de una aplicación web existente, use el cmdlet **Set-AzureRmWebApp** . Para obtener la lista completa de parámetros, consulte el [vínculo de referencia de cmdlet](https://msdn.microsoft.com/library/mt652487.aspx)

Ejemplo (1): utilice este cmdlet para cambiar las cadenas de conexión

    $connectionstrings = @{ ContosoConn1 = @{ Type = “MySql”; Value = “MySqlConn”}; ContosoConn2 = @{ Type = “SQLAzure”; Value = “SQLAzureConn”} }
    Set-AzureRmWebApp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup -ConnectionStrings $connectionstrings

Ejemplo (2): Adición o cambio de la configuración de una aplicación

    $appsettings = @{appsetting1 = "appsetting1value"; appsetting2 = "appsetting2value"}
    Set-AzureRmWebApp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup -AppSettings $appsettings


Ejemplo (3): establezca que la aplicación web se ejecute en modo de 64 bits

    Set-AzureRmWebApp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup -Use32BitWorkerProcess $False

### <a name="change-the-state-of-an-existing-web-app"></a>Cambio del estado de una aplicación web existente
#### <a name="restart-a-web-app"></a>Reinicio de una aplicación web
Para reiniciar una aplicación web, debe especificar el nombre y grupo de recursos de la aplicación web.

    Restart-AzureRmWebapp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup

#### <a name="stop-a-web-app"></a>Detención de una aplicación web
Para detener una aplicación web, debe especificar el nombre y grupo de recursos de la aplicación web.

    Stop-AzureRmWebapp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup

#### <a name="start-a-web-app"></a>Inicio de una aplicación web
Para iniciar una aplicación web, debe especificar el nombre y grupo de recursos de la aplicación web.

    Start-AzureRmWebapp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup

### <a name="manage-web-app-publishing-profiles"></a>Administración de perfiles de publicación de aplicaciones web
Cada aplicación web tiene un perfil de publicación que se puede utilizar para publicar las aplicaciones y en dichos perfiles se pueden ejecutar varias operaciones.

#### <a name="get-publishing-profile"></a>Obtención de un perfil de publicación
Para obtener el perfil de publicación de una aplicación web, use:

    Get-AzureRmWebAppPublishingProfile -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup -OutputFile .\publishingprofile.txt

Este comando reproduce el perfil de publicación en la línea de comandos y enviará el perfil de publicación a un archivo de texto.

#### <a name="reset-publishing-profile"></a>Restablecimiento de un perfil de publicación
Para restablecer tanto la contraseña de publicación para FTP como la implementación web de una aplicación web, use:

    Reset-AzureRmWebAppPublishingProfile -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup

### <a name="manage-web-app-certificates"></a>Administración de certificados de aplicaciones web
Para obtener información acerca de cómo administrar certificados de aplicaciones web, consulte [Enlace de certificados SSL con Servicio de aplicaciones de Azure mediante PowerShell](app-service-web-app-powershell-ssl-binding.md)

### <a name="next-steps"></a>Pasos siguientes
* Para obtener información sobre la compatibilidad de PowerShell con Azure Resource Manager, consulte [Uso de Azure PowerShell con Azure Resource Manager](../powershell-azure-resource-manager.md)
* Para obtener información acerca de los entornos del Servicio de aplicaciones, consulte [Introducción al entorno del Servicio de aplicaciones](app-service-app-service-environment-intro.md)
* Para obtener más información acerca de cómo administrar los certificados SSL del Servicio de aplicaciones mediante PowerShell, consulte [Enlace de certificados SSL con Servicio de aplicaciones de Azure mediante PowerShell](app-service-web-app-powershell-ssl-binding.md)
* Para obtener más información acerca de la lista completa de los cmdlets de PowerShell basados en Azure Resource Manager para Aplicaciones web de Azure, consulte la [referencia de los cmdlets de PowerShell basados en Azure Resource Manager de aplicaciones web](https://msdn.microsoft.com/library/mt619237.aspx)
* * Para más información acerca de cómo administrar App Service mediante una CLI, consulte [Using Azure Resource Manager-Based XPlat CLI for Azure Web App](app-service-web-app-azure-resource-manager-xplat-cli.md) (Uso de una CLI multiplataforma basada en Azure Resource Manager para aplicaciones web de Azure).


