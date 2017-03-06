---
title: "Herramientas de línea de comandos multiplataforma basadas en Azure Resource Manager | Microsoft Docs"
description: "Aprenda a usar las nuevas herramientas de línea de comandos multiplataforma basadas en Azure Resource Manager para administrar sus aplicaciones web de Azure."
services: app-service\web
documentationcenter: 
author: ahmedelnably
manager: stefsch
editor: 
ms.assetid: d415b195-4262-416f-b59f-7e1aef200054
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2016
ms.author: aelnably
translationtype: Human Translation
ms.sourcegitcommit: 5ea7095e12b6194556d3cd0baa43ccfed1e087ee
ms.openlocfilehash: 7a03e1417617453c43edcc3787da10d171359757
ms.lasthandoff: 02/27/2017


---
# <a name="using-azure-resource-manager-based-xplat-cli-for-azure-app-service"></a>Uso de la CLI de XPlat basada en Azure Resource Manager para Azure App Service
> [!div class="op_single_selector"]
> * [CLI de Azure](app-service-web-app-azure-resource-manager-xplat-cli.md)
> * [Azure PowerShell](app-service-web-app-azure-resource-manager-powershell.md)

Con el lanzamiento de la versión 0.10.5 de las herramientas de línea de comandos multiplataforma de Microsoft Azure, se han agregado nuevos comandos. Estos comandos ofrecen la posibilidad de usar comandos de PowerShell basados en Azure Resource Manager para administrar App Service.

Para más información sobre cómo administrar grupos de recursos, consulte [Uso de la CLI de Azure para administrar los recursos y grupos de recursos de Azure](../azure-resource-manager/xplat-cli-azure-resource-manager.md). 

> [!NOTE] 
> Además, pruebe la [CLI de Azure 2.0](https://github.com/Azure/azure-cli), una CLI de última generación escrita en Python para el modelo de implementación de administración de recursos.
>
>

## <a name="managing-app-service-plans"></a>Administración de planes del Servicio de aplicaciones
### <a name="create-an-app-service-plan"></a>Creación de un plan del Servicio de aplicaciones
Para crear un plan de App Service, use el comando **azure appserviceplan create**.

Estas son las descripciones de los distintos parámetros:

* **--resource-group**: grupo de recursos que incluye el plan de App Service reciñen creado.
* **--name**: nombre del plan de App Service.
* **--location**: ubicación del plan de App Service.
* **--sku**:  la sku de precios deseada; las opciones son: F1 (Gratis). D1 (Compartida). B1 (Básico inferior), B2 (Básico medio) y B3 (Básico superior). S1 (Estándar inferior), S2 (Estándar medio) y S3 (Estándar superior). P1 (Premium inferior), P2 (Premium medio) y P3 (Premium superior).)
* **--instances**: el número de trabajos en el plan de App Service (el valor predeterminado es 1).

Ejemplo para usar este cmdlet:

    azure appserviceplan create --name ContosoAppServicePlan --location "South Central US" --resource-group ContosoAzureResourceGroup --sku P1 --instances 10

#### <a name="create-a-linux-app-service-plan"></a>Creación de un plan de App Service de Linux

Con el mismo comando **azure appserviceplan create**, con el parámetro adicional **--islinux true**. Tenga en cuenta las restricciones y las regiones que se describen en [Introducción a App Service en Linux](app-service-linux-intro.md).

### <a name="list-existing-app-service-plans"></a>Enumeración de planes del Servicio de aplicaciones existentes
Para enumerar los planes de App Service existentes, use el cmdlet **azure appserviceplan list**.

Para enumerar todos los planes del Servicio de aplicaciones de un grupo de recursos específico, use:

    azure appserviceplan list --resource-group ContosoAzureResourceGroup

Para obtener un plan de App Service específico, use el cmdlet **azure appserviceplan show**.

    azure appserviceplan show --name ContosoAppServicePlan --resource-group southeastasia

### <a name="configure-an-existing-app-service-plan"></a>Configuración de un plan del Servicio de aplicaciones existente
Para cambiar la configuración de un plan de App Service existente, use el cmdlet **azure appserviceplan config**. Puede cambiar la sku y el número de trabajos. 

    azure appserviceplan config --name ContosoAppServicePlan --resource-group ContosoAzureResourceGroup --sku S1 --instances 9

#### <a name="scaling-an-app-service-plan"></a>Escalado de un plan del Servicio de aplicaciones
Para escalar un plan del Servicio de aplicaciones existente, use:

    azure appserviceplan config --name ContosoAppServicePlan --resource-group ContosoAzureResourceGroup --instances 9

#### <a name="changing-the-sku-of-an-app-service-plan"></a>Cambio de la SKU de un plan de App Service
Para cambiar la sku de un plan de App Service, use:

    azure appserviceplan config --name ContosoAppServicePlan --resource-group ContosoAzureResourceGroup --sku S1


### <a name="delete-an-existing-app-service-plan"></a>Eliminación de un plan del Servicio de aplicaciones
Para eliminar un plan de App Service existente, primero se deben mover o eliminar todas las aplicaciones asignadas. A continuación, mediante el comando **azure webapp delete**, puede eliminar el plan de App Service.

    azure appserviceplan delete --name ContosoAppServicePlan --resource-group southeastasia

## <a name="managing-app-service-apps"></a>Administración de aplicaciones de App Service
### <a name="create-a-web-app"></a>Creación de una aplicación web
Para crear una aplicación web, use el comando **azure webapp create**.

Estas son las descripciones de los distintos parámetros:

* **--name**: nombre de la aplicación web.
* **--plan**: nombre del plan usado para hospedar la aplicación web.
* **--resource-group**: grupo de recursos que hospeda el plan de App Service.
* **--location**: la ubicación de la aplicación web.

Ejemplo para usar este cmdlet:

    azure webapp create --name ContosoWebApp --resource-group ContosoAzureResourceGroup --plan ContosoAppServicePlan --location "South Central US"

### <a name="delete-an-existing-app"></a>Eliminación de una aplicación existente
Para eliminar una aplicación existente, puede usar el comando **zure webapp delete**. Debe especificar el nombre de la aplicación y el nombre del grupo de recursos.

    azure webapp delete --name ContosoWebApp --resource-group ContosoAzureResourceGroup

### <a name="list-existing-apps"></a>Lista de aplicaciones existentes
Para enumerar las aplicaciones existentes, use el comando **azure webapp list**.

Para enumerar todas los aplicaciones de un grupo de recursos específico, use:

    azure webapp list --resource-group ContosoAzureResourceGroup

Para obtener una aplicación específica, use el comando **azure webapp show**.

    azure webapp show --name ContosoWebApp --resource-group ContosoAzureResourceGroup

### <a name="configure-an-existing-app"></a>Configuración de una aplicación existente
Para cambiar los valores y las configuraciones de una aplicación existente, use el comando **azure webapp config set**.

Ejemplo (1): Cambio de la versión de php de una aplicación 

    azure webapp config set --name ContosoWebApp --resource-group ContosoAzureResourceGroup --phpversion 5.6

Ejemplo (2): Adición o cambio de la configuración de una aplicación

    webapp config appsettings set --name ContosoWebApp --resource-group ContosoAzureResourceGroup appsetting1=appsetting1value,appsetting2=appsetting2value

Para saber qué otra configuración se puede cambiar, use el comando **azure webapp config set -h**.

### <a name="change-the-state-of-an-existing-app"></a>Cambio del estado de una aplicación existente
#### <a name="restart-an-app"></a>Reinicio de una aplicación
Para reiniciar una aplicación, debe especificar el nombre y grupo de recursos de la aplicación.

    azure webapp restart --name ContosoWebApp --resource-group ContosoAzureResourceGroup

#### <a name="stop-an-app"></a>Detener una aplicación
Para detener una aplicación, debe especificar el nombre y grupo de recursos de la aplicación.

    azure webapp stop --name ContosoWebApp --resource-group ContosoAzureResourceGroup

#### <a name="start-an-app"></a>Inicio de una aplicación
Para iniciar una aplicación, debe especificar el nombre y grupo de recursos de la aplicación.

    azure webapp start --name ContosoWebApp --resource-group ContosoAzureResourceGroup

### <a name="manage-publishing-profiles-of-an-app"></a>Administración de perfiles de publicación de una aplicación
Cada aplicación tiene un perfil de publicación que puede usarse para publicar el código.

#### <a name="get-publishing-profile"></a>Obtención de un perfil de publicación
Para obtener el perfil de publicación de una aplicación, use:

    azure webapp publishingprofile --name ContosoWebApp --resource-group ContosoAzureResourceGroup

Este comando devuelve el nombre de usuario y la contraseña del perfil de publicación a la línea de comandos.

### <a name="manage-app-hostnames"></a>Administración de nombres de host de aplicaciones
Para administrar los enlaces de nombre de host de la aplicación, use el comando **azure webapp config hostnames**.  

#### <a name="list-hostname-bindings"></a>Enumeración de enlaces de nombre de host
Para obtener los enlaces de nombre de host actuales de una aplicación, use:

    azure webapp config hostnames list --name ContosoWebApp --resource-group ContosoAzureResourceGroup

#### <a name="add-hostname-bindings"></a>Adición de enlaces de nombre de host
Para agregar enlaces de nombre de host a una aplicación, use:

    azure webapp config hostnames add --name ContosoWebApp --resource-group ContosoAzureResourceGroup --hostname www.contoso.com

#### <a name="delete-hostname-bindings"></a>Eliminación de enlaces de nombre de host
Para eliminar enlaces de nombre de host, use:

    azure webapp config hostnames delete --name ContosoWebApp --resource-group ContosoAzureResourceGroup --hostname www.contoso.com

## <a name="next-steps"></a>Pasos siguientes
* Para aprender sobre la compatibilidad con la CLI de Azure Resource Manager, consulte [Uso de la CLI de Azure para administrar los recursos y grupos de recursos de Azure.](../azure-resource-manager/xplat-cli-azure-resource-manager.md)
* Para más información sobre cómo administrar App Service con PowerShell, consulte [Uso de PowerShell basado en Azure Resource Manager para administrar aplicaciones web de Azure.](app-service-web-app-azure-resource-manager-powershell.md)
* Para más información sobre Azure App Service, consulte [Introducción a App Service en Linux](app-service-linux-intro.md).

