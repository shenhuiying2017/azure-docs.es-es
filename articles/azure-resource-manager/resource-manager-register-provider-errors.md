---
title: Errores del registro de proveedor de recursos de Azure | Microsoft Docs
description: "Describe cómo resolver errores de registro de proveedor de recursos de Azure."
services: azure-resource-manager,azure-portal
documentationcenter: 
author: tfitzmac
manager: timlt
editor: 
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: support-article
ms.date: 09/13/2017
ms.author: tomfitz
ms.openlocfilehash: d6a99917e732a3439a31cafa5608348694014054
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="resolve-errors-for-resource-provider-registration"></a>Resolución de errores del registro del proveedor de recursos

Este artículo describe los errores que pueden producirse al usar un proveedor de recursos que no ha usado anteriormente en la suscripción.

## <a name="symptom"></a>Síntoma

Al implementar recursos, puede recibir el siguiente código y mensaje de error:

```
Code: NoRegisteredProviderFound
Message: No registered resource provider found for location {location}
and API version {api-version} for type {resource-type}.
```

O bien, puede recibir un mensaje similar que indica:

```
Code: MissingSubscriptionRegistration
Message: The subscription is not registered to use namespace {resource-provider-namespace}
```

## <a name="cause"></a>Causa

Recibirá estos errores por uno de estos tres motivos:

1. El proveedor de recursos no se ha registrado para la suscripción
1. No se permite esta versión de API para el tipo de recurso
1. No se permite esta ubicación para el tipo de recurso

## <a name="solution"></a>Solución

El mensaje de error debería proporcionarle sugerencias con respecto a las versiones de API y a las ubicaciones admitidas. Puede cambiar la plantilla a uno de los valores sugeridos. La mayoría de los proveedores se registran automáticamente mediante el Portal de Azure o la interfaz de línea de comandos que esté utilizando; pero no ocurre con todos. Si no ha utilizado un proveedor de recursos determinado antes, debe registrar dicho proveedor. Puede obtener más información sobre los proveedores de recursos a través de PowerShell o la CLI de Azure.

### <a name="solution-1"></a>Solución 1

Para PowerShell, use **Get-AzureRmResourceProvider** para ver su estado de registro.

```powershell
Get-AzureRmResourceProvider -ListAvailable
```

Para registrar un proveedor, use **Register-AzureRmResourceProvider** e indique el nombre del proveedor de recursos que desea registrar.

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Cdn
```

Para conocer las ubicaciones admitidas para un tipo determinado de recurso, use:

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
```

Para conocer las versiones de API admitidas para un tipo determinado de recurso, use:

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
```

### <a name="solution-2"></a>Solución 2

**CLI de Azure**

Para ver si el proveedor está registrado, utilice el comando `az provider list` .

```azurecli-interactive
az provider list
```

Para registrar un proveedor de recursos, use el comando `az provider register` y especifique el *espacio de nombres* que desea registrar.

```azurecli-interactive
az provider register --namespace Microsoft.Cdn
```

Para ver las ubicaciones y las versiones de API admitidas para un tipo de recursos, use:

```azurecli-interactive
az provider show -n Microsoft.Web --query "resourceTypes[?resourceType=='sites'].locations"
```

### <a name="solution-3"></a>Solución 3

Puede ver el estado de registro y registrar un espacio de nombres de proveedor de recursos a través del portal.

1. Para la suscripción, seleccione **Proveedores de recursos**.

   ![seleccionar proveedores de recursos](./media/resource-manager-register-provider-errors/select-resource-provider.png)

1. Examine la lista de proveedores de recursos y, si es necesario, seleccione el vínculo **Registrar** para registrar el proveedor de recursos del tipo que está intentando implementar.

   ![Enumeración de proveedores de recursos](./media/resource-manager-register-provider-errors/list-resource-providers.png)
