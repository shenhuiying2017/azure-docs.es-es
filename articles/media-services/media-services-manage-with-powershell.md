---
title: "Administración de cuentas de Servicios multimedia de Azure con PowerShell"
description: Aprenda a administrar las cuentas de Servicios multimedia de Azure con los cmdlets de PowerShell.
author: Juliako
manager: erikre
editor: 
services: media-services
documentationcenter: 
ms.assetid: 17a10c25-d94f-421c-b6bc-ae0958e2ac96
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 2549c876ee35d6a7fa425d43e2f86d24131ca791
ms.openlocfilehash: 3d999d9e27844bc0164cc3572522b9ec022118a1


---
# <a name="manage-azure-media-services-accounts-with-powershell"></a>Administración de cuentas de Servicios multimedia de Azure con PowerShell
> [!div class="op_single_selector"]
> * [Portal](media-services-portal-create-account.md)
> * [PowerShell](media-services-manage-with-powershell.md)
> * [REST](https://docs.microsoft.com/rest/api/media/mediaservice)
> 
> [!NOTE]
> Para poder crear una cuenta de Servicios multimedia de Azure, debe tener una cuenta de Azure. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A8A8397B5" target="_blank">Evaluación gratuita de Azure</a>.
> 
> 

## <a name="overview"></a>Información general
En este artículo se muestran los cmdlets de Azure PowerShell para Servicios multimedia de Azure (AMS) en el marco de trabajo de Azure Resource Manager. Los cmdlets existen en el espacio de nombres **Microsoft.Azure.Commands.Media** .

## <a name="versions"></a>Versiones
**ApiVersion**:   "2015-10-01"

## <a name="new-azurermmediaservice"></a>New-AzureRmMediaService
Crea un servicio multimedia.

### <a name="syntax"></a>Sintaxis
Conjunto de parámetros: StorageAccountIdParamSet

    New-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string> [-Location] <string> [-StorageAccountId] <string> [-Tags <hashtable>]  [<CommonParameters>]

Conjunto de parámetros: StorageAccountsParamSet

    New-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string> [-Location] <string> [-StorageAccounts] <PSStorageAccount[]> [-Tags <hashtable>]  [<CommonParameters>]

### <a name="parameters"></a>Parámetros
**-ResourceGroupName &lt;String&gt;**

Especifica el nombre del grupo de recursos al que pertenece este servicio multimedia.

| Alias | Ninguna |
| --- | --- |
| ¿Necesario? |true |
| ¿Posición? |0 |
| Valor predeterminado |Ninguna |
| ¿Aceptar la entrada de la canalización? |true(ByPropertyName) |
| ¿Aceptar caracteres comodín? |false |

**-AccountName &lt;String&gt;**

Especifica el nombre del servicio multimedia.

| Alias | Nombre |
| --- | --- |
| ¿Necesario? |true |
| ¿Posición? |1 |
| Valor predeterminado |Ninguna |
| ¿Aceptar la entrada de la canalización? |false |
| ¿Aceptar caracteres comodín? |false |

**-Location &lt;String&gt;**

Especifica la ubicación del recurso del servicio multimedia.

| Alias | Ninguna |
| --- | --- |
| ¿Necesario? |true |
| ¿Posición? |2 |
| Valor predeterminado |Ninguna |
| ¿Aceptar la entrada de la canalización? |true(ByPropertyName) |
| ¿Aceptar caracteres comodín? |false |

**-StorageAccountId &lt;String&gt;**

Especifica una cuenta de almacenamiento principal asociada con el servicio multimedia.

* Solo se admite una cuenta de almacenamiento nueva (creada con la API de Resource Manager).
* La cuenta de almacenamiento debe existir y tener la misma ubicación que el servicio multimedia.

| Alias | Ninguna |
| --- | --- |
| ¿Necesario? |true |
| ¿Posición? |3 |
| Valor predeterminado |Ninguna |
| ¿Aceptar la entrada de la canalización? |true(ByPropertyName) |
| Nombre del conjunto de parámetros |StorageAccountIdParamSet |
| ¿Aceptar caracteres comodín? |false |

**-StorageAccounts &lt;PSStorageAccount\[\]&gt;**

Especifica cuentas de almacenamiento asociadas con el servicio multimedia.

* Solo se admite una cuenta de almacenamiento nueva (creada con la API de Resource Manager).
* La cuenta de almacenamiento debe existir y tener la misma ubicación que el servicio multimedia.
* Solo una cuenta de almacenamiento se puede identificar como principal.

| Alias | Ninguna |
| --- | --- |
| ¿Necesario? |true |
| ¿Posición? |3 |
| Valor predeterminado |Ninguna |
| ¿Aceptar la entrada de la canalización? |true(ByPropertyName) |
| Nombre del conjunto de parámetros |StorageAccountsParamSet |
| ¿Aceptar caracteres comodín? |false |

**-Tags &lt;Hashtable&gt;**

Especifica una tabla hash de las etiquetas asociadas con el servicio multimedia.

* Ejemplo: @{"tag1"="value1";"tag2"=:value2"}

| Alias | Ninguna |
| --- | --- |
| ¿Necesario? |false |
| ¿Posición? |con nombre |
| Valor predeterminado |Ninguna |
| ¿Aceptar la entrada de la canalización? |false |
| ¿Aceptar caracteres comodín? |false |

**&lt;CommandParameters&gt;**

Este cmdlet admite los parámetros comunes siguientes: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction y -WarningVariable.

### <a name="inputs"></a>Entradas
El tipo de entrada es el tipo de los objetos que puede canalizar al cmdlet.

### <a name="outputs"></a>Salidas
El tipo de salida es el tipo de los objetos que emite el cmdlet.

## <a name="set-azurermmediaservice"></a>Set-AzureRmMediaService
Actualiza un servicio multimedia.

### <a name="syntax"></a>Sintaxis
    Set-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string> [-Tags <hashtable>] [-StorageAccounts <PSStorageAccount[]>]  [<CommonParameters>]

### <a name="parameters"></a>Parámetros
**-ResourceGroupName &lt;String&gt;**

Especifica el nombre del grupo de recursos al que pertenece este servicio multimedia.

| Alias | Ninguna |
| --- | --- |
| ¿Necesario? |true |
| ¿Posición? |0 |
| Valor predeterminado |Ninguna |
| ¿Aceptar la entrada de la canalización? |true(ByPropertyName) |
| ¿Aceptar caracteres comodín? |false |

**-AccountName &lt;String&gt;**

Especifica el nombre del servicio multimedia.

| Alias | Nombre |
| --- | --- |
| ¿Necesario? |true |
| ¿Posición? |1 |
| Valor predeterminado |Ninguna |
| ¿Aceptar la entrada de la canalización? |true(ByPropertyName) |
| ¿Aceptar caracteres comodín? |False |

**-StorageAccounts &lt;PSStorageAccount\[\]&gt;**

Especifica cuentas de almacenamiento asociadas con el servicio multimedia.

* Solo se admite una cuenta de almacenamiento nueva (creada con la API de Resource Manager).
* La cuenta de almacenamiento debe existir y tener la misma ubicación que el servicio multimedia.
* Solo una cuenta de almacenamiento se puede identificar como principal.

| Alias | Ninguna |
| --- | --- |
| ¿Necesario? |false |
| ¿Posición? |con nombre |
| Valor predeterminado |Ninguna |
| ¿Aceptar la entrada de la canalización? |true(ByPropertyName) |
| Nombre del conjunto de parámetros |StorageAccountsParamSet |
| ¿Aceptar caracteres comodín? |false |

**-Tags &lt;Hashtable&gt;**

Especifica una tabla hash de las etiquetas asociadas con este servicio multimedia.

* Las etiquetas asociadas con el servicio multimedia se reemplazan por el valor que especifica el cliente.

| Alias | Ninguna |
| --- | --- |
| ¿Necesario? |false |
| ¿Posición? |con nombre |
| Valor predeterminado |Ninguna |
| ¿Aceptar la entrada de la canalización? |true(ByPropertyName) |
| ¿Aceptar caracteres comodín? |false |

**&lt;CommandParameters&gt;**

Este cmdlet admite los parámetros comunes siguientes: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction y -WarningVariable.

### <a name="inputs"></a>Entradas
El tipo de entrada es el tipo de los objetos que puede canalizar al cmdlet.

### <a name="outputs"></a>Salidas
El tipo de salida es el tipo de los objetos que emite el cmdlet.

## <a name="remove-azurermmediaservice"></a>Remove-AzureRmMediaService
Quita un servicio multimedia.

### <a name="syntax"></a>Sintaxis
    Remove-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string>  [<CommonParameters>]

### <a name="parameters"></a>Parámetros
**-ResourceGroupName &lt;String&gt;**

Especifica el nombre del grupo de recursos al que pertenece este servicio multimedia.

| Alias | Ninguna |
| --- | --- |
| ¿Necesario? |true |
| ¿Posición? |0 |
| Valor predeterminado |Ninguna |
| ¿Aceptar la entrada de la canalización? |true(ByPropertyName) |
| ¿Aceptar caracteres comodín? |false |

**-AccountName &lt;String&gt;**

Especifica el nombre del servicio multimedia.

| Alias | Ninguna |
| --- | --- |
| ¿Necesario? |true |
| ¿Posición? |2 |
| Valor predeterminado |Ninguna |
| ¿Aceptar la entrada de la canalización? |true(ByPropertyName) |
| ¿Aceptar caracteres comodín? |False |

**&lt;CommandParameters&gt;**

Este cmdlet admite los parámetros comunes siguientes: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction y -WarningVariable.

### <a name="inputs"></a>Entradas
El tipo de entrada es el tipo de los objetos que puede canalizar al cmdlet.

### <a name="outputs"></a>Salidas
El tipo de salida es el tipo de los objetos que emite el cmdlet.

## <a name="get-azurermmediaservice"></a>Get-AzureRmMediaService
Obtiene todos los servicios multimedia de un grupo de recursos o un servicio multimedia con un nombre determinado.

### <a name="syntax"></a>Sintaxis
ParameterSet: ResourceGroupParameterSet

    Get-AzureRmMediaService [-ResourceGroupName] <string>  [<CommonParameters>]    

ParameterSet: AccountNameParameterSet

    Get-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string>  [<CommonParameters>]

### <a name="parameters"></a>Parámetros
**-ResourceGroupName &lt;String&gt;**

Especifica el nombre del grupo de recursos al que pertenece este servicio multimedia.

| Alias | Ninguna |
| --- | --- |
| ¿Necesario? |true |
| ¿Posición? |0 |
| Valor predeterminado |Ninguna |
| ¿Aceptar la entrada de la canalización? |true(ByPropertyName) |
| Nombre del conjunto de parámetros |ResourceGroupParameterSet, AccountNameParameterSet |

¿Aceptar caracteres comodín?   false

**-AccountName &lt;String&gt;**

Especifica el nombre del servicio multimedia.

| Alias | Ninguna |
| --- | --- |
| ¿Necesario? |true |
| ¿Posición? |1 |
| Valor predeterminado |Ninguna |
| ¿Aceptar la entrada de la canalización? |true(ByPropertyName) |
| Nombre del conjunto de parámetros |AccountNameParameterSet |
| ¿Aceptar caracteres comodín? |false |

**&lt;CommandParameters&gt;**

Este cmdlet admite los parámetros comunes siguientes: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction y -WarningVariable.

### <a name="inputs"></a>Entradas
El tipo de entrada es el tipo de los objetos que puede canalizar al cmdlet.

### <a name="outputs"></a>Salidas
El tipo de salida es el tipo de los objetos que emite el cmdlet.

## <a name="get-azurermmediaservicekeys"></a>Get-AzureRmMediaServiceKeys
Obtiene las claves de un servicio multimedia.

### <a name="syntax"></a>Sintaxis
    Get-AzureRmMediaServiceKeys [-ResourceGroupName] <string> [-AccountName] <string>  [<CommonParameters>]

### <a name="parameters"></a>Parámetros
**-ResourceGroupName &lt;String&gt;**

Especifica el nombre del grupo de recursos al que pertenece este servicio multimedia.

| Alias | Ninguna |
| --- | --- |
| ¿Necesario? |true |
| ¿Posición? |0 |
| Valor predeterminado |Ninguna |
| ¿Aceptar la entrada de la canalización? |true(ByPropertyName) |
| ¿Aceptar caracteres comodín? |false |

**-AccountName &lt;String&gt;**

Especifica el nombre del servicio multimedia.

| Alias | Ninguna |
| --- | --- |
| ¿Necesario? |true |
| ¿Posición? |1 |
| Valor predeterminado |Ninguna |
| ¿Aceptar la entrada de la canalización? |true(ByPropertyName) |
| ¿Aceptar caracteres comodín? |false |

**&lt;CommandParameters&gt;**

Este cmdlet admite los parámetros comunes siguientes: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction y -WarningVariable.

### <a name="inputs"></a>Entradas
El tipo de entrada es el tipo de los objetos que puede canalizar al cmdlet.

### <a name="outputs"></a>Salidas
El tipo de salida es el tipo de los objetos que emite el cmdlet.

## <a name="set-azurermmediaservicekey"></a>Set-AzureRmMediaServiceKey
Regenera una clave principal o secundaria de un servicio multimedia.

### <a name="syntax"></a>Sintaxis
    Set-AzureRmMediaServiceKey [-ResourceGroupName] <string> [-AccountName] <string> [-KeyType] <KeyType> {Primary | Secondary}  [<CommonParameters>]

### <a name="parameters"></a>Parámetros
**-ResourceGroupName &lt;String&gt;**

Especifica el nombre del grupo de recursos al que pertenece este servicio multimedia.

| Alias | Ninguna |
| --- | --- |
| ¿Necesario? |true |
| ¿Posición? |0 |
| Valor predeterminado |Ninguna |
| ¿Aceptar la entrada de la canalización? |true(ByPropertyName) |
| ¿Aceptar caracteres comodín? |false |

**-AccountName &lt;String&gt;**

Especifica el nombre del servicio multimedia.

| Alias | Ninguna |
| --- | --- |
| ¿Necesario? |true |
| ¿Posición? |1 |
| Valor predeterminado |Ninguna |
| ¿Aceptar la entrada de la canalización? |true(ByPropertyName) |
| ¿Aceptar caracteres comodín? |false |

**-KeyType &lt;KeyType&gt;**

Especifica el tipo de clave del servicio multimedia.

* Principal o secundaria

| Alias | Ninguna |
| --- | --- |
| ¿Necesario? |true |
| ¿Posición? |2 |
| Valor predeterminado |Ninguna |
| ¿Aceptar la entrada de la canalización? |false |
| ¿Aceptar caracteres comodín? |false |

**&lt;CommandParameters&gt;**

Este cmdlet admite los parámetros comunes siguientes: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction y -WarningVariable.

### <a name="inputs"></a>Entradas
El tipo de entrada es el tipo de los objetos que puede canalizar al cmdlet.

### <a name="outputs"></a>Salidas
El tipo de salida es el tipo de los objetos que emite el cmdlet.

## <a name="sync-azurermmediaservicestoragekeys"></a>Sync-AzureRmMediaServiceStorageKeys
Sincroniza las claves de cuenta de almacenamiento de una cuenta de almacenamiento asociada con el servicio multimedia.

### <a name="syntax"></a>Sintaxis
    Sync-AzureRmMediaServiceStorageKeys [-ResourceGroupName] <string> [-MediaServiceAccountName] <string>    [-StorageAccountId] <string>  [<CommonParameters>]

### <a name="parameters"></a>Parámetros
**-ResourceGroupName &lt;String&gt;**

Especifica el nombre del grupo de recursos al que pertenece este servicio multimedia.

| Alias | Ninguna |
| --- | --- |
| ¿Necesario? |true |
| ¿Posición? |0 |
| Valor predeterminado |Ninguna |
| ¿Aceptar la entrada de la canalización? |true(ByPropertyName) |
| ¿Aceptar caracteres comodín? |false |

**-AccountName &lt;String&gt;**

Especifica el nombre del servicio multimedia.

| Alias | Ninguna |
| --- | --- |
| ¿Necesario? |true |
| ¿Posición? |1 |
| Valor predeterminado |Ninguna |
| ¿Aceptar la entrada de la canalización? |true(ByPropertyName) |
| ¿Aceptar caracteres comodín? |false |

**-StorageAccountId &lt;String&gt;**

Especifica la cuenta de almacenamiento asociada con el servicio multimedia.

| Alias | Id |
| --- | --- |
| ¿Necesario? |true |
| ¿Posición? |2 |
| Valor predeterminado |Ninguna |
| ¿Aceptar la entrada de la canalización? |true(ByPropertyName) |
| ¿Aceptar caracteres comodín? |false |

**&lt;CommandParameters&gt;**

Este cmdlet admite los parámetros comunes siguientes: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction y -WarningVariable.

### <a name="inputs"></a>Entradas
El tipo de entrada es el tipo de los objetos que puede canalizar al cmdlet.

### <a name="outputs"></a>Salidas
El tipo de salida es el tipo de los objetos que emite el cmdlet.

## <a name="next-step"></a>Paso siguiente
Revise las rutas de aprendizaje de Servicios multimedia.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Envío de comentarios
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]




<!--HONumber=Nov16_HO5-->


