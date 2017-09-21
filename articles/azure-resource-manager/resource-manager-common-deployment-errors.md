---
title: "Solución de errores comunes de implementación de Azure | Microsoft Docs"
description: "Describe cómo solucionar errores comunes al implementar recursos en Azure con Azure Resource Manager."
services: azure-resource-manager
documentationcenter: 
tags: top-support-issue
author: tfitzmac
manager: timlt
editor: tysonn
keywords: "error de implementación, implementación de Azure, implementar en Azure"
ms.assetid: c002a9be-4de5-4963-bd14-b54aa3d8fa59
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: support-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/17/2017
ms.author: tomfitz
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: 54db95fd50e5d61d54d0c03a8589cfe8292c012c
ms.contentlocale: es-es
ms.lasthandoff: 09/14/2017

---
# <a name="troubleshoot-common-azure-deployment-errors-with-azure-resource-manager"></a>Solución de errores comunes de implementación de Azure con Azure Resource Manager
En este tema se describe cómo resolver algunos errores comunes con los que puede encontrarse al realizar una implementación de Azure.

En este tema se describen los códigos de error siguientes:

* [AccountNameInvalid](#accountnameinvalid)
* [Error de autorización](#authorization-failed)
* [BadRequest](#badrequest)
* [DeploymentFailed](#deploymentfailed)
* [DisallowedOperation](#disallowedoperation)
* [InvalidContentLink](#invalidcontentlink)
* [InvalidTemplate](#invalidtemplate)
* [MissingSubscriptionRegistration](#noregisteredproviderfound)
* [NotFound](#notfound)
* [NoRegisteredProviderFound](#noregisteredproviderfound)
* [OperationNotAllowed](#quotaexceeded)
* [ParentResourceNotFound](#parentresourcenotfound)
* [QuotaExceeded](#quotaexceeded)
* [RequestDisallowedByPolicy](#requestdisallowedbypolicy)
* [ResourceNotFound](#notfound)
* [SkuNotAvailable](#skunotavailable)
* [StorageAccountAlreadyExists](#storagenamenotunique)
* [StorageAccountAlreadyTaken](#storagenamenotunique)

## <a name="deploymentfailed"></a>DeploymentFailed

Este código de error indica un error de implementación general, pero no es el código de error que necesita para empezar a solucionar problemas. El código de error que realmente le ayuda a resolver el problema suele estar un nivel por debajo de este error. Por ejemplo, la siguiente imagen muestra el código de error **RequestDisallowedByPolicy** que se encuentra debajo del error de implementación.

![Visualización del código de error](./media/resource-manager-common-deployment-errors/error-code.png)

## <a name="skunotavailable"></a>SkuNotAvailable

Al implementar un recurso (normalmente una máquina virtual), puede recibir el siguiente código y mensaje de error:

```
Code: SkuNotAvailable
Message: The requested tier for resource '<resource>' is currently not available in location '<location>' 
for subscription '<subscriptionID>'. Please try another tier or deploy to a different location.
```

Recibirá este error si la SKU del recurso que ha seleccionado (como, por ejemplo, el tamaño de máquina virtual) no está disponible para la ubicación seleccionada. Para resolver este problema, debe determinar qué SKU están disponibles en una región. Puede usar PowerShell, el portal o una operación REST para buscar las SKU disponibles.

- Para PowerShell, use [Get-AzureRmComputeResourceSku](/powershell/module/azurerm.compute/get-azurermcomputeresourcesku) y filtre por ubicación. Debe tener la versión más reciente de PowerShell para que funcione este comando.

  ```powershell
  Get-AzureRmComputeResourceSku | where {$_.Locations.Contains("southcentralus")}
  ```

  Los resultados incluyen una lista de SKU para la ubicación y las restricciones de esas SKU.

  ```powershell
  ResourceType                Name      Locations Restriction                      Capability Value
  ------------                ----      --------- -----------                      ---------- -----
  availabilitySets         Classic southcentralus             MaximumPlatformFaultDomainCount     3
  availabilitySets         Aligned southcentralus             MaximumPlatformFaultDomainCount     3
  virtualMachines      Standard_A0 southcentralus
  virtualMachines      Standard_A1 southcentralus
  virtualMachines      Standard_A2 southcentralus
  ```

- En la CLI de Azure, use el comando `az vm list-skus`. Después, puede usar `grep` o una utilidad similar para filtrar la salida.

  ```
  az vm list-skus --output table
  ResourceType      Locations           Name                    Capabilities                       Tier      Size           Restrictions
  ----------------  ------------------  ----------------------  ---------------------------------  --------  -------------  ---------------------------
  availabilitySets  eastus              Classic                 MaximumPlatformFaultDomainCount=3
  avilabilitySets  eastus              Aligned                 MaximumPlatformFaultDomainCount=3
  availabilitySets  eastus2             Classic                 MaximumPlatformFaultDomainCount=3
  availabilitySets  eastus2             Aligned                 MaximumPlatformFaultDomainCount=3
  availabilitySets  westus              Classic                 MaximumPlatformFaultDomainCount=3
  availabilitySets  westus              Aligned                 MaximumPlatformFaultDomainCount=3
  availabilitySets  centralus           Classic                 MaximumPlatformFaultDomainCount=3
  availabilitySets  centralus           Aligned                 MaximumPlatformFaultDomainCount=3
  ```

- Para usar el [portal](https://portal.azure.com), inicie sesión en el portal y agregue un recurso mediante la interfaz. A medida que establezca los valores, verá las SKU disponibles para ese recurso. No tiene que completar la implementación.

    ![SKU disponibles](./media/resource-manager-common-deployment-errors/view-sku.png)

- Para usar la API de REST para máquinas virtuales, envíe la solicitud siguiente:

  ```HTTP 
  GET
  https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/skus?api-version=2016-03-30
  ```

  Se devuelven las SKU y las regiones disponibles en el formato siguiente:

  ```json
  {
    "value": [
      {
        "resourceType": "virtualMachines",
        "name": "Standard_A0",
        "tier": "Standard",
        "size": "A0",
        "locations": [
          "eastus"
        ],
        "restrictions": []
      },
      {
        "resourceType": "virtualMachines",
        "name": "Standard_A1",
        "tier": "Standard",
        "size": "A1",
        "locations": [
          "eastus"
        ],
        "restrictions": []
      },
      ...
    ]
  }    
  ```

Si no puede encontrar una SKU adecuada en esa región ni en ninguna región alternativa que satisfaga las necesidades de su negocio, envíe una [solicitud de SKU](https://aka.ms/skurestriction) al soporte técnico de Azure.

## <a name="disallowedoperation"></a>DisallowedOperation

```
Code: DisallowedOperation
Message: The current subscription type is not permitted to perform operations on any provider 
namespace. Please use a different subscription.
```

Si recibe este error es que está usando una suscripción a la que no se le permite acceder a ningún servicio de Azure, excepto a Azure Active Directory. Puede tener este tipo de suscripción si necesita acceder al portal clásico, pero no se le permitirá implementar recursos. Para resolver este problema, debe usar una suscripción que tenga permiso para implementar recursos.  

Para ver las suscripciones disponibles con PowerShell, use:

```powershell
Get-AzureRmSubscription
```

Y, para establecer la suscripción actual, use:

```powershell
Set-AzureRmContext -SubscriptionName {subscription-name}
```

Para ver las suscripciones disponibles con la CLI de Azure 2.0, use:

```azurecli
az account list
```

Y, para establecer la suscripción actual, use:

```azurecli
az account set --subscription {subscription-name}
```

## <a name="invalidtemplate"></a>InvalidTemplate
Este error puede tener distintos orígenes.

- Error de sintaxis

   Si recibe un mensaje de error que indica que la plantilla no superó la validación, puede que esta tenga un problema de sintaxis.

  ```
  Code=InvalidTemplate
  Message=Deployment template validation failed
  ```

   Es fácil cometer este error porque las expresiones de plantilla pueden ser complejas. Por ejemplo, la siguiente asignación de nombre de una cuenta de almacenamiento contiene un conjunto de corchetes, tres funciones, tres conjuntos de paréntesis, un conjunto de comillas simples y una propiedad:

  ```json
  "name": "[concat('storage', uniqueString(resourceGroup().id))]",
  ```

   Si no proporciona toda la sintaxis de coincidencia, la plantilla generará un valor que será diferente del que esperaba.

   Si recibe este tipo de error, revise cuidadosamente la sintaxis de las expresiones. Considere la posibilidad de utilizar un editor JSON como [Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) o [Visual Studio Code](resource-manager-vs-code.md) que puede avisarlo de los errores de sintaxis.

- Longitudes de segmentos incorrectas

   Cuando el nombre del recurso no tiene el formato correcto, se produce otro error de plantilla no válida.

  ```
  Code=InvalidTemplate
  Message=Deployment template validation failed: 'The template resource {resource-name}'
  for type {resource-type} has incorrect segment lengths.
  ```

   Un recurso de nivel de raíz debe tener un segmento menos en el nombre que en el tipo de recurso. Cada segmento se distingue por una barra diagonal. En el ejemplo siguiente, el tipo tiene 2 segmentos y el nombre, 1, por lo que es un **nombre válido**.

  ```json
  {
    "type": "Microsoft.Web/serverfarms",
    "name": "myHostingPlanName",
    ...
  }
  ```

   Pero el ejemplo siguiente **no es un nombre válido** porque el nombre tiene el mismo número de segmentos que el tipo.

  ```json
  {
    "type": "Microsoft.Web/serverfarms",
    "name": "appPlan/myHostingPlanName",
    ...
  }
  ```

   Para los recursos secundarios, el tipo y el nombre deben tener el mismo número de segmentos. Este número de segmentos tiene sentido, ya que el tipo y el nombre completo del elemento secundario incluyen el tipo y el nombre del elemento primario. Por lo tanto, el nombre completo sigue teniendo un segmento menos que el tipo completo.

  ```json
  "resources": [
      {
          "type": "Microsoft.KeyVault/vaults",
          "name": "contosokeyvault",
          ...
          "resources": [
              {
                  "type": "secrets",
                  "name": "appPassword",
                  ...
              }
          ]
      }
  ]
  ```

   Obtener los segmentos correctos puede resultar complicado con los tipos de Resource Manager que se aplican en los proveedores de recursos. Por ejemplo, para aplicar un bloqueo de recurso a un sitio web, el tipo debe tener 4 segmentos. Por lo tanto, el nombre tiene 3 segmentos:

  ```json
  {
      "type": "Microsoft.Web/sites/providers/locks",
      "name": "[concat(variables('siteName'),'/Microsoft.Authorization/MySiteLock')]",
      ...
  }
  ```

- Copy index is not expected (Índice de copia no esperado)

   Este error **InvalidTemplate** se produce cuando se ha aplicado el elemento **copy** a una parte de la plantilla que no lo admite. Solo se puede aplicar el elemento copy a un tipo de recurso. No se puede aplicar el elemento copy a una propiedad de un tipo de recurso. Por ejemplo, puede aplicar el elemento copy a una máquina virtual, pero no puede aplicarlo a los discos del sistema operativo de una máquina virtual. En algunos casos, puede convertir un recurso secundario en un recurso primario para crear un bucle del elemento copy. Para más información sobre cómo usar este elemento, consulte [Creación de varias instancias de recursos en Azure Resource Manager](resource-group-create-multiple.md).

- El parámetro no es válido

   Si la plantilla especifica los valores permitidos de un parámetro y proporciona un valor que no es uno de esos, recibirá un mensaje similar al siguiente error:

  ```
  Code=InvalidTemplate;
  Message=Deployment template validation failed: 'The provided value {parameter value}
  for the template parameter {parameter name} is not valid. The parameter value is not
  part of the allowed values
  ``` 

   Compruebe los valores permitidos de la plantilla y proporcione uno durante la implementación.

- Se detectó una dependencia circular

   Este error se recibe cuando los recursos dependen unos de otros de una forma que impide que se inicie la implementación. Una combinación de interdependencias hace que dos o más recursos esperen otros recursos que también están esperando. Por ejemplo, resource1 depende de resource3, resource2 depende de resource1 y resource3 depende de resource2. Para resolver este problema, normalmente se eliminan las dependencias innecesarias. 

<a id="notfound" />
### <a name="notfound-and-resourcenotfound"></a>NotFound y ResourceNotFound
Cuando la plantilla incluye el nombre de un recurso que no se puede resolver, recibirá un error similar al siguiente:

```
Code=NotFound;
Message=Cannot find ServerFarm with name exampleplan.
```

Si está tratando de implementar el recurso que falta en la plantilla, compruebe si tiene que agregar una dependencia. Cuando es posible, Resource Manager optimiza la implementación mediante la creación de recursos en paralelo. Si un recurso se debe implementar después de otro recurso, debe usar el elemento **dependsOn** en la plantilla para crear una dependencia en el otro recurso. Por ejemplo, al implementar una aplicación web, debe existir el plan de Servicio de aplicaciones. Si no ha especificado que la aplicación web dependa del plan de App Service, Resource Manager creará ambos recursos al mismo tiempo. Recibirá un error que indica que no se encuentra el recurso del plan de App Service, porque aún no existe cuando se trata de establecer una propiedad en la aplicación web. Este error se puede evitar estableciendo la dependencia en la aplicación web.

```json
{
  "apiVersion": "2015-08-01",
  "type": "Microsoft.Web/sites",
  "dependsOn": [
    "[variables('hostingPlanName')]"
  ],
  ...
}
```

Para obtener sugerencias sobre cómo solucionar los errores de dependencia, consulte [Comprobación de la secuencia de implementación](#check-deployment-sequence).

También verá este error cuando el recurso se encuentra en otro grupo de recursos que donde se está implementando. En ese caso, use la [función resourceId](resource-group-template-functions-resource.md#resourceid) para obtener el nombre completo del recurso.

```json
"properties": {
    "name": "[parameters('siteName')]",
    "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
}
```

Si trata de usar las funciones [reference](resource-group-template-functions-resource.md#reference) o [listKeys](resource-group-template-functions-resource.md#listkeys) con un recurso que no se puede resolver, recibirá el error siguiente:

```
Code=ResourceNotFound;
Message=The Resource 'Microsoft.Storage/storageAccounts/{storage name}' under resource
group {resource group name} was not found.
```

Busque una expresión que incluya la función **reference**. Compruebe que los valores de parámetro son correctos.

## <a name="parentresourcenotfound"></a>ParentResourceNotFound

Cuando un recurso es un elemento primario de otro recurso, el primario debe existir antes de crear el secundario. Si aún no existe, recibirá el error siguiente:

```
Code=ParentResourceNotFound;
Message=Can not perform requested operation on nested resource. Parent resource 'exampleserver' not found."
```

El nombre del recurso secundario incluye el nombre primario. Por ejemplo, se podría definir una base de datos SQL como:

```json
{
  "type": "Microsoft.Sql/servers/databases",
  "name": "[concat(variables('databaseServerName'), '/', parameters('databaseName'))]",
  ...
```

Sin embargo, si no especifica una dependencia del recurso primario, el secundario puede implementarse antes del primario. Para resolver este error, incluya una dependencia.

```json
"dependsOn": [
    "[variables('databaseServerName')]"
]
```

<a id="storagenamenotunique" />

## <a name="storageaccountalreadyexists-and-storageaccountalreadytaken"></a>StorageAccountAlreadyExists y StorageAccountAlreadyTaken
Para las cuentas de almacenamiento, debe proporcionar un nombre al recurso que sea único en Azure. Si no lo hace, recibirá un error como el siguiente:

```
Code=StorageAccountAlreadyTaken
Message=The storage account named mystorage is already taken.
```

Puede crear un nombre único concatenando la convención de nomenclatura con el resultado de la función [uniqueString](resource-group-template-functions-string.md#uniquestring) .

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
"type": "Microsoft.Storage/storageAccounts",
```

Si implementa una cuenta de almacenamiento con el mismo nombre que una que ya hay en la suscripción, pero proporciona una ubicación diferente, recibirá un error que indica que la cuenta de almacenamiento ya se encuentra en otra ubicación. Elimine la cuenta de almacenamiento que ya existe o proporcione la misma ubicación que la de la cuenta de almacenamiento.

## <a name="accountnameinvalid"></a>AccountNameInvalid
Verá el error **AccountNameInvalid** al tratar de proporcionar a una cuenta de almacenamiento un nombre que incluya caracteres prohibidos. Los nombres de cuentas de almacenamiento deben tener entre 3 y 24 caracteres, y usar solo números y letras minúsculas. La función [uniqueString](resource-group-template-functions-string.md#uniquestring) devuelve 13 caracteres. Si concatena un prefijo con el resultado **uniqueString**, proporcione un prefijo que tenga 11 caracteres o menos.

## <a name="badrequest"></a>BadRequest

Puede encontrar un estado BadRequest al proporcionar un valor no válido para una propiedad. Por ejemplo, si proporciona un valor incorrecto de SKU para una cuenta de almacenamiento, se produce un error en la implementación. Para determinar valores válidos para la propiedad, examine la [API de REST](/rest/api) del tipo de recurso que se va a implementar.

<a id="noregisteredproviderfound" />

## <a name="noregisteredproviderfound-and-missingsubscriptionregistration"></a>NoRegisteredProviderFound y MissingSubscriptionRegistration
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

Recibirá estos errores por uno de estos tres motivos:

1. El proveedor de recursos no se ha registrado para la suscripción
2. No se permite esta versión de API para el tipo de recurso
3. No se permite esta ubicación para el tipo de recurso

El mensaje de error debería proporcionarle sugerencias con respecto a las versiones de API y a las ubicaciones admitidas. Puede cambiar la plantilla a uno de los valores sugeridos. La mayoría de los proveedores se registran automáticamente mediante el Portal de Azure o la interfaz de línea de comandos que esté utilizando; pero no ocurre con todos. Si no ha utilizado un proveedor de recursos determinado antes, debe registrar dicho proveedor. Puede obtener más información sobre los proveedores de recursos a través de PowerShell o la CLI de Azure.

**Portal**

Puede ver el estado de registro y registrar un espacio de nombres de proveedor de recursos a través del portal.

1. Para la suscripción, seleccione **Proveedores de recursos**.

   ![seleccionar proveedores de recursos](./media/resource-manager-common-deployment-errors/select-resource-provider.png)

2. Examine la lista de proveedores de recursos y, si es necesario, seleccione el vínculo **Registrar** para registrar el proveedor de recursos del tipo que está intentando implementar.

   ![Enumeración de proveedores de recursos](./media/resource-manager-common-deployment-errors/list-resource-providers.png)

**PowerShell**

Para ver el estado de su registro, use **Get-AzureRmResourceProvider**.

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

**CLI de Azure**

Para ver si el proveedor está registrado, utilice el comando `azure provider list` .

```azurecli
az provider list
```

Para registrar un proveedor de recursos, use el comando `azure provider register` y especifique el *espacio de nombres* que desea registrar.

```azurecli
az provider register --namespace Microsoft.Cdn
```

Para ver las ubicaciones y las versiones de API admitidas para un tipo de recursos, use:

```azurecli
az provider show -n Microsoft.Web --query "resourceTypes[?resourceType=='sites'].locations"
```

<a id="quotaexceeded" />

## <a name="quotaexceeded-and-operationnotallowed"></a>QuotaExceeded y OperationNotAllowed
Podría tener problemas cuando una implementación supera una cuota, lo que podría suceder por grupo de recursos, suscripciones, cuentas y otros ámbitos. Por ejemplo, la suscripción puede configurarse para limitar el número de núcleos para una región. Si trata de implementar una máquina virtual con más núcleos que los permitidos, recibirá un error que indica que se ha superado la cuota.
Para obtener información completa de las cuotas, consulte [Límites, cuotas y restricciones de suscripción y servicios de Microsoft Azure](../azure-subscription-service-limits.md).

Para examinar las cuotas de núcleos de su suscripción, puede usar el comando `azure vm list-usage` en la CLI de Azure. En el siguiente ejemplo se muestra que la cuota de núcleos para una cuenta de evaluación gratuita es 4:

```azurecli
az vm list-usage --location "South Central US"
```

Que devuelve:

```azurecli
[
  {
    "currentValue": 0,
    "limit": 2000,
    "name": {
      "localizedValue": "Availability Sets",
      "value": "availabilitySets"
    }
  },
  ...
]
```

Si implementa una plantilla que crea más de 4 núcleos en la región oeste de EE. UU., obtendrá un error de implementación similar al siguiente:

```
Code=OperationNotAllowed
Message=Operation results in exceeding quota limits of Core.
Maximum allowed: 4, Current in use: 4, Additional requested: 2.
```

O bien en PowerShell, puede emplear el cmdlet **Get-AzureRmVMUsage** .

```powershell
Get-AzureRmVMUsage
```

Que devuelve:

```powershell
...
CurrentValue : 0
Limit        : 4
Name         : {
                 "value": "cores",
                 "localizedValue": "Total Regional Cores"
               }
Unit         : null
...
```

En estos casos, debe ir al portal y archivar un problema de soporte técnico para aumentar su cuota para la región en la que desea realizar la implementación.

> [!NOTE]
> Recuerde que para los grupos de recursos, la cuota para cada región individual, no para toda la suscripción. Si necesita implementar 30 núcleos en el oeste de Estados Unidos, debe pedir 30 núcleos de administrador de recursos en el oeste de Estados Unidos. Si necesita implementar 30 núcleos en cualquiera de las regiones para las que tiene acceso, debe pedir 30 núcleos de Resource Manager en todas las regiones.
>
>

## <a name="invalidcontentlink"></a>InvalidContentLink
Si recibe el mensaje de error:

```
Code=InvalidContentLink
Message=Unable to download deployment content from ...
```

Probablemente ha tratado de agregar un vínculo a una plantilla anidada que no está disponible. Compruebe el URI proporcionado para la plantilla anidada. Si la plantilla se encuentra en una cuenta de almacenamiento, asegúrese de que puede accederse al URI. Debe transmitir un token SAS. Para más información, consulte [Uso de plantillas vinculadas con el Administrador de recursos de Azure](resource-group-linked-templates.md).

## <a name="requestdisallowedbypolicy"></a>RequestDisallowedByPolicy
Recibirá este error cuando la suscripción incluye una directiva de recursos que impide una acción que está tratando de realizar durante la implementación. En el mensaje de error, busque el identificador de directiva.

```
Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'
```

En **PowerShell**, proporcione ese identificador de directiva como el parámetro **Id** para recuperar los detalles de la directiva que bloqueó la implementación.

```powershell
(Get-AzureRmPolicyDefinition -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition").Properties.policyRule | ConvertTo-Json
```

En la **CLI de Azure**, proporcione el nombre de la definición de directiva:

```azurecli
az policy definition show --name regionPolicyAssignment
```

Para más información, consulte los siguientes artículos.

- [Error RequestDisallowedByPolicy](resource-manager-policy-requestdisallowedbypolicy-error.md)
- [Uso de directivas para administrar los recursos y controlar el acceso](resource-manager-policy.md)

## <a name="authorization-failed"></a>Error de autorización
Puede recibir un error durante la implementación porque la cuenta o la entidad de servicio que intenta implementar los recursos no tiene acceso para realizar esas acciones. Azure Active Directory permite al usuario o al administrador controlar qué identidades pueden acceder a qué recursos con un alto grado de precisión. Por ejemplo, si su cuenta se asigna al rol Lector, no podrá crear recursos. En ese caso, debería ver un mensaje de error que indica que hubo un error de autorización.

Para más información sobre el control de acceso basado en roles, consulte [Control de acceso basado en roles de Azure](../active-directory/role-based-access-control-configure.md).


## <a name="next-steps"></a>Pasos siguientes
* Para más información sobre las acciones de auditoría, consulte [Operaciones de auditoría con Resource Manager](resource-group-audit.md).
* Si desea conocer más detalles sobre las acciones que permiten determinar los errores durante la implementación, consulte [Visualización de operaciones de implementación con el Portal de Azure](resource-manager-deployment-operations.md).

