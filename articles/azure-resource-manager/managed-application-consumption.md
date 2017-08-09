---
title: "Uso de una aplicación administrada de Azure | Microsoft Docs"
description: "Describe cómo crear una aplicación administrada de Azure a partir de archivos publicados."
services: azure-resource-manager
author: ravbhatnagar
manager: rjmax
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 05/17/2017
ms.author: gauravbh; tomfitz
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 8745db3abe6ddec6da1f92da6b7cf8bf82b96d79
ms.contentlocale: es-es
ms.lasthandoff: 07/21/2017

---
# <a name="consume-a-service-catalog-managed-application"></a>Uso de una aplicación administrada del catálogo de servicios

Tal y como se describe en el artículo de [introducción a las aplicaciones administradas](managed-application-overview.md), hay dos escenarios en la experiencia completa. Uno es el publicador o el ISV que desea crear una aplicación administrada para que la utilicen los usuarios. El segundo es el usuario final de la aplicación administrada. Este artículo trata el segundo escenario y explica cómo un usuario final puede utilizar una aplicación administrada proporcionada por fabricantes independientes de software.

Actualmente, puede utilizar la CLI de Azure o Azure Portal para usar una aplicación administrada. 

## <a name="create-the-managed-application-using-cli"></a>Creación de una aplicación administrada con la CLI 

Debe obtener el identificador de definición de aplicación para la aplicación que desea utilizar.

Hay dos maneras diferentes de crear una aplicación administrada mediante la CLI de Azure. Una es usar el comando normal de implementación de plantilla y, el otro, es usar un nuevo comando que se proporcionan solo para este propósito.

### <a name="create-using-template-deployment-command"></a>Creación mediante el comando de implementación de plantilla

Implemente el archivo applianceMainTemplate.json que el proveedor creó.

Cree dos grupos de recursos. El primero es donde se crea el recurso de aplicación (Microsoft.Solutions/appliances). El segundo contiene todos los recursos definidos en mainTemplate.json. El ISV administra este grupo de recursos.

```azurecli
az group create --name mainResourceGroup --location westcentralus    
az group create --name managedResourceGroup --location westcentralus
```

> [!NOTE]
> Use `westcentralus` como la ubicación del grupo de recursos.
>


Después, use el comando siguiente para implementar el archivo applianceMainTemplate.json en mainResourceGroup:

```azurecli
az group deployment create --name managedAppDeployment --resourceGroup mainResourceGroup --templateUri  
```

Cuando se ejecuta la plantilla anterior, le pide los valores de los parámetros que se definen en la plantilla. Además de los parámetros necesarios para el aprovisionamiento de recursos en una plantilla, necesita los valores de dos parámetros clave:

- managedResourceGroupId: identificador del grupo de recursos donde se crean los recursos definidos en applianceMainTemplate.json. El identificador tiene el formato `/subscriptions/{subscriptionId}/resourceGroups/{resoureGroupName}`. En el ejemplo anterior, es el identificador de `managedResourceGroup`.
- applianceDefinitionId: identificador del recurso de definición de aplicación administrada. Este valor es proporcionado por el ISV. 

> [!NOTE] 
> El ISV debe conceder acceso al grupo de recursos donde se crea el recurso de definición de aplicación. El recurso de definición de aplicación se crea en la suscripción del ISV. Por lo tanto, un usuario, grupo de usuarios o aplicación en el inquilino del cliente necesita acceso de lectura a este recurso. 

Una vez completada la implementación correctamente, verá el recurso de aplicación creado en **mainResourceGroup**. el recurso storageAccount se crea en **managedResourceGroup**.

### <a name="create-the-managed-application-using-create-command"></a>Creación de una aplicación administrada con el comando create

Puede usar el comando `az managedapp create` para crear una aplicación administrada a partir de la definición de aplicación administrada. 

```azurecli
az managedapp create --name ravtestappliance401 --location "westcentralus" 
    --kind "Servicecatalog" --resource-group "ravApplianceCustRG401" 
    --managedapp-definition-id "/subscriptions/{guid}/resourceGroups/ravApplianceDefRG401/providers/Microsoft.Solutions/applianceDefinitions/ravtestAppDef401" 
    --managed-rg-id "/subscriptions/{guid}/resourceGroups/ravApplianceCustManagedRG401" 
    --parameters "{\"storageAccountName\": {\"value\": \"ravappliancedemostore1\"}}" 
    --debug
```

**appliance-definition-Id**: identificador del recurso de definición de aplicación creado en el paso anterior. Para obtener este identificador, ejecute el siguiente comando:

```azurecli
az appliance definition show -n ravtestAppDef1 -g ravApplianceRG2
```

Este comando devuelve la definición de dispositivo. Se necesita el valor de la propiedad **Id**.

**managed-rg-id**: nombre del grupo de recursos donde se crean todos los recursos definidos en applianceMainTemplate.json. Este grupo de recursos es el grupo de recursos administrados por el publicador. Si no existe, se crea.

**resource-group**: grupo de recursos donde se crea el recurso de dispositivo. El recurso Microsoft.Solutions/appliance reside en este grupo de recursos. 

**parameters**: parámetros que son necesarios para los recursos definidos en applianceMainTemplate.json.

## <a name="create-the-managed-application-using-portal"></a>Creación de una aplicación administrada con Azure Portal

Azure Portal también permite utilizar aplicaciones administradas publicadas por el ISV. Siga estos pasos:

Seleccione la aplicación administrada del catálogo de servicios en la hoja Crear en Azure Portal:

![](./media/managed-application-consumption/create-service-catalog-managed-application.png)

A continuación, verá la lista de ofertas de distintos ISV o asociados. Seleccione la que le gustaría crear y haga clic en "Crear".

![](./media/managed-application-consumption/select-offer.png)

Después de hacer clic en Crear, proporcione los parámetros necesarios para aprovisionar los recursos en la hoja que se abre. 

![](./media/managed-application-consumption/input-parameters.png)

Después de proporcionar los valores, haga clic en Aceptar. La plantilla se valida con las entradas que proporcionó. Si la validación es correcta, se inicia la implementación de la plantilla. Una vez finalizada la implementación, los recursos adecuados definidos en la plantilla se aprovisionan en el grupo de recursos administrados que proporcionó.

## <a name="known-issues"></a>Problemas conocidos

Esta versión preliminar presenta los siguientes problemas:

* Si recibe un error 500 Error interno del servidor durante la creación del dispositivo, probablemente es un problema intermitente. Si experimenta este problema, vuelva a intentar la operación.
* Se necesita un nuevo grupo de recursos para el grupo de recursos administrados. Utilizar un grupo de recursos existente produce un error de implementación.
* El grupo de recursos que contiene el recurso Microsoft.Solutions/appliances debe crearse en **westcentralus**.

## <a name="next-steps"></a>Pasos siguientes

* Para una introducción a las aplicaciones administradas, consulte la [introducción a las aplicaciones administradas de Azure](managed-application-overview.md).
* Para información sobre cómo publicar una aplicación administrada del catálogo de servicios, consulte [Creación y publicación de una aplicación administrada del catálogo de servicios](managed-application-publishing.md).
* Para información sobre cómo publicar aplicaciones administradas en Marketplace, consulte [Aplicaciones administradas de Azure en Marketplace](managed-application-author-marketplace.md).
* Para información sobre cómo usar una aplicación administrada de Marketplace, consulte [Uso de aplicaciones administradas de Azure en Marketplace](managed-application-consume-marketplace.md).
