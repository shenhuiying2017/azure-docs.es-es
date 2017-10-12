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
ms.date: 08/23/2017
ms.author: gauravbh; tomfitz
ms.openlocfilehash: ed8fbaf2a4546c8e31eeced11cd0b5627fd62c0c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="consume-an-internal-managed-application"></a>Uso de una aplicación administrada interna

Puede usar [aplicaciones administradas](managed-application-overview.md) de Azure que están diseñadas para los miembros de su organización. Por ejemplo, puede seleccionar aplicaciones administradas desde el departamento de TI que garantizan el cumplimiento normativo de los estándares de la organización. Estas aplicaciones administradas están disponibles a través del catálogo de servicios y no en Azure Marketplace.

Antes de continuar con este artículo, debe tener una aplicación administrada disponible en el catálogo de servicios de su suscripción. Si nadie de la organización ha creado todavía una aplicación administrada, consulte [Publicación de una aplicación administrada para uso interno](managed-application-publishing.md).

Actualmente, puede utilizar la CLI de Azure o Azure Portal para usar una aplicación administrada.

## <a name="create-the-managed-application-by-using-the-portal"></a>Creación de una aplicación administrada con Azure Portal

Para implementar una aplicación administrada a través del portal, siga estos pasos:

1. Vaya a Azure Portal. Búsqueda de una **aplicación administrada del catálogo de servicios**.

   ![Aplicación administrada del catálogo de servicios](./media/managed-application-consumption/create-service-catalog-managed-application.png)

1. Seleccione la aplicación administrada que desea crear a partir de la lista de soluciones disponibles. Seleccione **Crear**.

   ![Selección de aplicaciones administradas](./media/managed-application-consumption/select-offer.png)

1. Proporcione valores para los parámetros necesarios para aprovisionar los recursos. Seleccione **Centro occidental de EE. UU.** como ubicación. Seleccione **Aceptar**.

   ![Parámetros de aplicaciones administradas](./media/managed-application-consumption/input-parameters.png)

1. La plantilla valida los valores que proporcionó. Si la validación es correcta, seleccione **Aceptar** para iniciar la implementación.

   ![Validación de aplicaciones administradas](./media/managed-application-consumption/validation.png)

Una vez finalizada la implementación, los recursos adecuados definidos en la plantilla se aprovisionan en el grupo de recursos administrados que proporcionó.

## <a name="create-the-managed-application-by-using-azure-cli"></a>Creación de una aplicación administrada con la CLI de Azure

Hay dos maneras de crear una aplicación administrada mediante la CLI de Azure:

* Use el comando para crear aplicaciones administradas.
* Usar el comando normal de implementación de plantilla.

### <a name="use-the-template-deployment-command"></a>Uso del comando normal de implementación de plantilla

Implemente el archivo applianceMainTemplate.json que el proveedor creó.

Después, cree dos grupos de recursos. El primero es donde se crea el recurso de la aplicación administrada: Microsoft.Solutions/appliances. El segundo contiene todos los recursos definidos en mainTemplate.json. El ISV administra este grupo de recursos.

```azurecli
az group create --name mainResourceGroup --location westcentralus
az group create --name managedResourceGroup --location westcentralus
```

> [!NOTE]
> Use `westcentralus` como la ubicación del grupo de recursos.
>

Para implementar el archivo applianceMainTemplate.json en mainResourceGroup, use el comando siguiente:

```azurecli
az group deployment create --name managedAppDeployment --resourceGroup mainResourceGroup --templateUri
```

Cuando se ejecuta la plantilla anterior, le pide los valores de los parámetros que se definen en la plantilla. Además de los parámetros necesarios para el aprovisionamiento de recursos en una plantilla, necesita los valores de dos parámetros clave:

- **managedResourceGroupId**: identificador del grupo de recursos que contiene los recursos definidos en applianceMainTemplate.json. El identificador tiene el formato `/subscriptions/{subscriptionId}/resourceGroups/{resoureGroupName}`. En el ejemplo anterior, es el identificador de `managedResourceGroup`.
- **applianceDefinitionId**: identificador del recurso de definición de la aplicación administrada. Este valor es proporcionado por el ISV.

> [!NOTE]
> El publicador debe conceder acceso al grupo de recursos que contiene la definición de la aplicación administrada. El recurso de definición se crea en la suscripción del publicador. Por lo tanto, un usuario, grupo de usuarios o aplicación en el inquilino del cliente necesita acceso de lectura a este recurso.

Una vez completada la implementación correctamente, verá que la aplicación administrada se ha creado en mainResourceGroup. El recurso storageAccount se crea en managedResourceGroup.

### <a name="use-the-create-command"></a>Uso del comando create

Puede usar el comando `az managedapp create` para crear una aplicación administrada a partir de la definición de aplicación administrada.

```azurecli
az managedapp create --name ravtestappliance401 --location "westcentralus"
    --kind "Servicecatalog" --resource-group "ravApplianceCustRG401"
    --managedapp-definition-id "/subscriptions/{guid}/resourceGroups/ravApplianceDefRG401/providers/Microsoft.Solutions/applianceDefinitions/ravtestAppDef401"
    --managed-rg-id "/subscriptions/{guid}/resourceGroups/ravApplianceCustManagedRG401"
    --parameters "{\"storageAccountName\": {\"value\": \"ravappliancedemostore1\"}}"
    --debug
```

* **appliance-definition-Id**: identificador del recurso de definición de la aplicación administrada creado en el paso anterior. Para obtener este identificador, ejecute el siguiente comando:

  ```azurecli
  az appliance definition show -n ravtestAppDef1 -g ravApplianceRG2
  ```

  Este comando devuelve la definición de la aplicación administrada. Se necesita el valor de la propiedad ID.

* **managed-rg-id**: nombre del grupo de recursos que contiene los recursos definidos en applianceMainTemplate.json. Este es el grupo de recursos administrados. Es administrado por el publicador. Si no existe, se crea.
* **resource-group**: grupo de recursos donde se crea el recurso de la aplicación administrada. El recurso Microsoft.Solutions/appliance reside en este grupo de recursos.
* **parameters**: parámetros que son necesarios para los recursos definidos en applianceMainTemplate.json.

## <a name="known-issues"></a>Problemas conocidos

Esta versión preliminar presenta los siguientes problemas:

* Aparece un error de servidor interno 500 durante la creación de la aplicación administrada. Si experimenta este problema, es probable que sea intermitente. Vuelva a intentar la operación.
* Se necesita un nuevo grupo de recursos para el grupo de recursos administrados. Si usa un grupo de recursos existente, se produce un error de implementación.
* El grupo de recursos que contiene el recurso Microsoft.Solutions/appliances debe crearse en la ubicación **westcentralus**.

## <a name="next-steps"></a>Pasos siguientes

* Para una introducción a las aplicaciones administradas, consulte [Introducción a las aplicaciones administradas](managed-application-overview.md).
* Para información sobre cómo publicar una aplicación administrada del catálogo de servicios, consulte [Creación y publicación de una aplicación administrada del catálogo de servicios](managed-application-publishing.md).
* Para información sobre cómo publicar aplicaciones administradas en Azure Marketplace, consulte [Aplicaciones administradas de Azure en Marketplace](managed-application-author-marketplace.md).
* Para información sobre cómo usar una aplicación administrada de Marketplace, consulte [Uso de aplicaciones administradas de Azure en Marketplace](managed-application-consume-marketplace.md).
