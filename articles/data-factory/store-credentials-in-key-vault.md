---
title: Almacenamiento de credenciales en Azure Key Vault | Microsoft Docs
description: "Aprenda a almacenar credenciales para almacenes de datos usados en una instancia de Azure Key Vault que Azure Data Factory pueda recuperar automáticamente en tiempo de ejecución."
services: data-factory
author: linda33wj
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: jingwang
ms.openlocfilehash: cb4545dcd41fe9a2c73bc86096843b717d61a785
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="store-credential-in-azure-key-vault"></a>Almacenamiento de credenciales en Azure Key Vault

Puede almacenar las credenciales de los almacenes de datos en una instancia de [Azure Key Vault](../key-vault/key-vault-whatis.md). Azure Data Factory las recupera al ejecutar una actividad que usa el almacén de datos.

> [!NOTE]
> Actualmente, solo el [conector de Dynamics](connector-dynamics-crm-office-365.md) admite esta característica.

## <a name="steps"></a>Pasos

Al crear una factoría de datos, se puede crear también una identidad de servicio. La identidad de servicio es una aplicación administrada registrada en Azure Active Directory y representa esta factoría de datos específica.

- Cuando crea una factoría de datos mediante **Azure Portal o PowerShell**, la identidad de servicio siempre se crea automáticamente desde la versión pública preliminar.
- Cuando crea una factoría de datos mediante **SDK**, la identidad de servicio se creará solo si especifica "Identity = new FactoryIdentity()" en el objeto de la factoría para la creación. Vea el ejemplo que aparece en el [inicio rápido de .NET: creación de factoría de datos](quickstart-create-data-factory-dot-net.md#create-a-data-factory).
- Cuando crea una factoría de datos mediante la **API de REST**, la identidad de servicio solo se creará si especifica la sección "identity" en el cuerpo de la solicitud. Vea el ejemplo que aparece en el [inicio rápido de REST: creación de factoría de datos](quickstart-create-data-factory-rest-api.md#create-a-data-factory).

Para hacer referencia a una credencial almacenada en Azure Key Vault, deberá seguir estos pasos:

1. Copie el "ID. DE LA APLICACIÓN DE IDENTIDAD DE SERVICIO" generado junto con la factoría de datos. Consulte [Recuperación de la identidad de servicio](#retrieve-service-identity).
2. Conceda a la identidad de servicio acceso a su instancia de Azure Key Vault. En el almacén de claves -> Control de acceso -> Agregar ->, busque este identificador de aplicación de la identidad de servicio para agregar permiso de lectura. De esta forma, la factoría designada puede acceder al secreto del almacén de claves.
3. Cree un servicio vinculado que apunte a su instancia de Azure Key Vault. Consulte [Servicio vinculado de Azure Key Vault](#azure-key-vault-linked-service).
4. Cree un servicio vinculado de almacén de datos, en el que se haga referencia al secreto correspondiente almacenado en el almacén de claves. Consulte [Credencial de referencia almacenada en el almacén de claves](#reference-credential-stored-in-key-vault).

## <a name="retrieve-service-identity"></a>Recuperación de la identidad de servicio

Puede recuperar la identidad de servicio desde Azure Portal o mediante programación. Las secciones siguientes le muestran algunos ejemplos.

>[!TIP]
> Si no ve la identidad de servicio, [genérela](#generate-service-identity) mediante la actualización de la factoría.

### <a name="using-azure-portal"></a>Uso de Azure Portal

Puede encontrar la información de la identidad de servicio en Azure Portal -> su factoría de datos -> Configuración ->Propiedades:

- ID. DE LA IDENTIDAD DE SERVICIO
- INQUILINO DE LA IDENTIDAD DE SERVICIO
- **ID. DE LA APLICACIÓN DE IDENTIDAD DE SERVICIO** > copie este valor para conceder acceso a Key Vault.

![Recuperación de la identidad de servicio](media/store-credentials-in-key-vault/retrieve-service-identity-portal.png)

### <a name="using-powershell"></a>Con PowerShell

El Id. de entidad de seguridad de la identidad de servicio y el Id. del inquilino se devolverán cuando obtenga una factoría de datos específica como se indica a continuación:

```powershell
PS C:\WINDOWS\system32> (Get-AzureRmDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName>).Identity

PrincipalId                          TenantId
-----------                          --------
765ad4ab-XXXX-XXXX-XXXX-51ed985819dc 72f988bf-XXXX-XXXX-XXXX-2d7cd011db47
```

Copie el identificador de entidad de seguridad y luego ejecute el comando de Azure Active Directory siguiente con el Id. de entidad de seguridad como parámetro para obtener el **ApplicationId** que se usará para conceder acceso a Key Vault.

```powershell
PS C:\WINDOWS\system32> Get-AzureRmADServicePrincipal -ObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc

ServicePrincipalNames : {76f668b3-XXXX-XXXX-XXXX-1b3348c75e02, https://identity.azure.net/P86P8g6nt1QxfPJx22om8MOooMf/Ag0Qf/nnREppHkU=}
ApplicationId         : 76f668b3-XXXX-XXXX-XXXX-1b3348c75e02
DisplayName           : ADFV2DemoFactory
Id                    : 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
Type                  : ServicePrincipal
```

## <a name="azure-key-vault-linked-service"></a>Servicio vinculado de Azure Key Vault

Las siguientes propiedades son compatibles con el servicio vinculado de Azure Key Vault:

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad type debe establecerse en **AzureKeyVault**. | Sí |
| baseUrl | Especifique la dirección URL de Azure Key Vault. | Sí |

**Ejemplo:**

```json
{
    "name": "AzureKeyVaultLinkedService",
    "properties": {
        "type": "AzureKeyVault",
        "typeProperties": {
        "baseUrl": "https://<azureKeyVaultName>.vault.azure.net"
        }
    }
}
```

## <a name="reference-credential-stored-in-key-vault"></a>Credencial de referencia almacenada en el almacén de claves

Al configurar un campo en un servicio vinculado que hace referencia a un secreto del almacén de claves, se admiten las siguientes propiedades:

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad type del campo debe establecerse en **AzureKeyVaultSecret**. | Sí |
| secretName | El nombre del secreto en Azure Key Vault. | Sí |
| secretVersion | La versión del secreto en Azure Key Vault.<br/>Si no se especifica, siempre se usa la versión más reciente del secreto.<br/>Si se especifica, se usa la versión dada.| No |
| store | Hace referencia a un servicio vinculado de Azure Key Vault que se usa para almacenar las credenciales. | Sí |

**Ejemplo: (consulte la sección "Contraseña")**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "typeProperties": {
            "deploymentType": "<>",
            "organizationName": "<>",
            "authenticationType": "<>",
            "username": "<>",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "mySecret",
                "store":{
                    "linkedServiceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
}
```

## <a name="generate-service-identity"></a>Generación de la identidad de servicio

Si observa que la factoría de datos no tiene una identidad de servicio asociada después de seguir la instrucción de [recuperación de la identidad de servicio](#retrieve-service-identity), puede generar una si actualiza la factoría de datos con el iniciador de identidades mediante programación.

> [!NOTE]
> - **La identidad de servicio no se puede cambiar**. La actualización de una factoría de datos que ya tiene una identidad de servicio no tendrá ningún efecto, ya que la identidad de servicio se mantendrá sin cambios.
> - **La identidad de servicio no se puede eliminar**. Si actualiza una factoría de datos que ya tiene una identidad de servicio, sin especificar el parámetro "identity" en el objeto de la factoría o sin especificar la sección "identity" en el cuerpo de la solicitud de REST, obtendrá un error.

Las secciones siguientes muestran algunos ejemplos sobre la generación de identidades de servicio para la factoría si estas no existen.

### <a name="using-powershell"></a>Con PowerShell

Llame al comando **Set-AzureRmDataFactoryV2** de nuevo y verá que los campos "Identity" se han generado recientemente:

```powershell
PS C:\WINDOWS\system32> Set-AzureRmDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName> -Location <region>

DataFactoryName   : ADFV2DemoFactory
DataFactoryId     : /subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory
ResourceGroupName : <resourceGroupName>
Location          : East US
Tags              : {}
Identity          : Microsoft.Azure.Management.DataFactory.Models.FactoryIdentity
ProvisioningState : Succeeded
```

### <a name="using-rest-api"></a>Uso de la API de REST

Llame a la siguiente API con la sección "identity" en el cuerpo de la solicitud:

```
PATCH https://management.azure.com/subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<data factory name>?api-version=2017-09-01-preview
```

**Cuerpo de la solicitud**: agregue "identity": { "type": "SystemAssigned" }.

```json
{
    "name": "<dataFactoryName>",
    "location": "<region>",
    "properties": {},
    "identity": {
        "type": "SystemAssigned"
    }
}
```

**Respuesta**: la identidad de servicio se crea automáticamente y la sección "identity" se rellena en consecuencia.

```json
{
    "name": "ADFV2DemoFactory",
    "tags": {},
    "properties": {
        "provisioningState": "Succeeded",
        "loggingStorageAccountKey": "**********",
        "createTime": "2017-09-26T04:10:01.1135678Z",
        "version": "2017-09-01-preview"
    },
    "identity": {
        "type": "SystemAssigned",
        "principalId": "765ad4ab-XXXX-XXXX-XXXX-51ed985819dc",
        "tenantId": "72f988bf-XXXX-XXXX-XXXX-2d7cd011db47"
    },
    "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory",
    "type": "Microsoft.DataFactory/factories",
    "location": "EastUS"
}
```

### <a name="using-sdk"></a>Uso de SDK

Llame a la función create_or_update de la factoría de datos con Identity=new FactoryIdentity(). Código de ejemplo mediante .NET:

```csharp
Factory dataFactory = new Factory
{
    Location = <region>,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
```

## <a name="next-steps"></a>Pasos siguientes
Consulte los [almacenes de datos compatibles](copy-activity-overview.md##supported-data-stores-and-formats) para ver la lista de almacenes de datos que la actividad de copia de Azure Data Factory admite como orígenes y receptores.