---
title: Identidad de servicio de Azure Data Factory | Microsoft Docs
description: "Obtenga información acerca de la identidad de servicio de la factoría de datos en Azure Data Factory."
services: data-factory
author: linda33wj
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: jingwang
ms.openlocfilehash: aad93abd6e7bdf75e6f3b4fcd02b433a1d301ebc
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2018
---
# <a name="azure-data-factory-service-identity"></a>Identidad de servicio de Azure Data Factory

Este artículo le ayudará a comprender qué es la identidad de servicio de Data Factory y cómo funciona.

> [!NOTE]
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general, consulte la [documentación de la versión 1 de Data Factory](v1/data-factory-introduction.md).

## <a name="overview"></a>Información general

Al crear una factoría de datos, se puede crear también una identidad de servicio. La identidad de servicio es una aplicación administrada registrada en Azure Active Directory y representa esta factoría de datos específica.

La identidad de servicio de Data Factory beneficia a las dos características siguientes:

- [Almacenamiento de credenciales en Azure Key Vault](store-credentials-in-key-vault.md), en cuyo caso se utiliza la identidad de servicio de Data Factory para la autenticación de Azure Key Vault.
- [Copiar datos desde/hacia Azure Data Lake Store](connector-azure-data-lake-store.md), en cuyo caso, la identidad de servicio de Data Factory se puede utilizar como uno de los tipos de autenticación admitidos de Azure Data Lake Store.

## <a name="generate-service-identity"></a>Generación de la identidad de servicio

La identidad de servicio de Data Factory se genera de la manera siguiente:

- Cuando se crea una factoría de datos mediante **Azure Portal o PowerShell**, la identidad de servicio siempre se crea automáticamente desde la versión preliminar pública de ADF V2.
- Cuando crea una factoría de datos mediante **SDK**, la identidad de servicio se creará solo si especifica "Identity = new FactoryIdentity()" en el objeto de la factoría para la creación. Vea el ejemplo que aparece en el [Inicio rápido de .NET: Crear una factoría de datos](quickstart-create-data-factory-dot-net.md#create-a-data-factory).
- Cuando crea una factoría de datos mediante la **API de REST**, la identidad de servicio solo se creará si especifica la sección "identity" en el cuerpo de la solicitud. Vea el ejemplo que aparece en el [Inicio rápido de REST: Crear una factoría de datos](quickstart-create-data-factory-rest-api.md#create-a-data-factory).

Si observa que la factoría de datos no tiene una identidad de servicio asociada después de seguir la instrucción de [recuperación de la identidad de servicio](#retrieve-service-identity), puede generar explícitamente una si actualiza la factoría de datos con el iniciador de identidades mediante programación:

- [Generar una identidad de servicio con PowerShell](#generate-service-identity-using-powershell)
- [Generar una identidad de servicio con la API de REST](#generate-service-identity-using-rest-api)
- [Generar una identidad de servicio con el SDK](#generate-service-identity-using-sdk)

>[!NOTE]
>- La identidad de servicio no se puede modificar. La actualización de una factoría de datos que ya tiene una identidad de servicio no tendrá ningún efecto, ya que la identidad de servicio se mantiene sin cambios.
>- Si actualiza una factoría de datos que ya tiene una identidad de servicio, sin especificar el parámetro "identity" en el objeto de la factoría o sin especificar la sección "identity" en el cuerpo de la solicitud de REST, obtendrá un error.
>- Cuando se elimina una factoría de datos, se eliminará también la identidad de servicio asociada.

### <a name="generate-service-identity-using-powershell"></a>Generar una identidad de servicio con PowerShell

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

### <a name="generate-service-identity-using-rest-api"></a>Generar una identidad de servicio con la API de REST

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

### <a name="generate-service-identity-using-sdk"></a>Generar una identidad de servicio con el SDK

Llame a la función create_or_update de la factoría de datos con Identity=new FactoryIdentity(). Código de ejemplo mediante .NET:

```csharp
Factory dataFactory = new Factory
{
    Location = <region>,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
```

## <a name="retrieve-service-identity"></a>Recuperación de la identidad de servicio

Puede recuperar la identidad de servicio desde Azure Portal o mediante programación. Las secciones siguientes le muestran algunos ejemplos.

>[!TIP]
> Si no ve la identidad de servicio, [genérela](#generate-service-identity) mediante la actualización de la factoría.

### <a name="retrieve-service-identity-using-azure-portal"></a>Recuperar la identidad de servicio mediante Azure Portal

Puede encontrar la información de la identidad de servicio en Azure Portal -> su factoría de datos -> Configuración ->Propiedades:

- ID. DE LA IDENTIDAD DE SERVICIO
- INQUILINO DE LA IDENTIDAD DE SERVICIO
- **ID. DE LA APLICACIÓN DE IDENTIDAD DE SERVICIO** > copie este valor

![Recuperación de la identidad de servicio](media/data-factory-service-identity/retrieve-service-identity-portal.png)

### <a name="retrieve-service-identity-using-powershell"></a>Recuperar una identidad de servicio con PowerShell

El Id. de entidad de seguridad de la identidad de servicio y el Id. del inquilino se devolverán cuando obtenga una factoría de datos específica como se indica a continuación:

```powershell
PS C:\WINDOWS\system32> (Get-AzureRmDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName>).Identity

PrincipalId                          TenantId
-----------                          --------
765ad4ab-XXXX-XXXX-XXXX-51ed985819dc 72f988bf-XXXX-XXXX-XXXX-2d7cd011db47
```

Copie el identificador de entidad de seguridad y, luego, ejecute el comando de Azure Active Directory siguiente con el Id. de entidad de seguridad como parámetro para obtener el valor de **ApplicationId** que se usará para conceder acceso:

```powershell
PS C:\WINDOWS\system32> Get-AzureRmADServicePrincipal -ObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc

ServicePrincipalNames : {76f668b3-XXXX-XXXX-XXXX-1b3348c75e02, https://identity.azure.net/P86P8g6nt1QxfPJx22om8MOooMf/Ag0Qf/nnREppHkU=}
ApplicationId         : 76f668b3-XXXX-XXXX-XXXX-1b3348c75e02
DisplayName           : ADFV2DemoFactory
Id                    : 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
Type                  : ServicePrincipal
```

## <a name="next-steps"></a>pasos siguientes
Consulte los siguientes temas que presentan cuándo y cómo se utiliza la identidad de servicio de Data Factory:

- [Almacenamiento de credenciales en Azure Key Vault](store-credentials-in-key-vault.md)
- [Copia de datos con Azure Data Lake Store como origen o destino mediante Azure Data Factory](connector-azure-data-lake-store.md)

Consulte el tema de [información general sobre MSI](~/articles/active-directory/msi-overview.md) para obtener más información sobre la identidad de servicio administrada en la que se basa la identidad de servicio de factoría de datos. 