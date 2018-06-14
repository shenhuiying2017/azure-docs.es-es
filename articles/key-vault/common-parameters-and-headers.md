---
title: Parámetros y encabezados comunes
description: Los parámetros y encabezados comunes a todas las operaciones que puede realizar relacionadas con los recursos de Key Vault.
services: key-vault
documentationcenter: ''
author: lleonard-msft
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: a715d13ca9-d6e8-4e54-ac5e-0ed9400fb15b15d13ca9-d6e8-4e54-ac5e-0ed9400fb15b
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: alleonar
ms.openlocfilehash: ead1ac550c9b7c489edefd35d5672a9955e78255
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
ms.locfileid: "34011938"
---
# <a name="common-parameters-and-headers"></a>Parámetros y encabezados comunes

La siguiente información es común a todas las operaciones que puede realizar relacionadas con los recursos de Key Vault:

- Reemplace `{api-version}` por la versión de api del URI.
- Reemplace `{subscription-id}` por el identificador de suscripción del URI.
- Sustituya `{resource-group-name}` por el grupo de recursos. Para más información, consulte el artículo Uso de grupos de recursos para administrar los recursos de Azure.
- Reemplace `{vault-name}` por el nombre del almacén de claves del URI.
- Establezca el encabezado Content-Type en application/json.
- Establezca el encabezado Authorization en un token web de JSON que se obtiene de Azure Active Directory (AAD). Par más información, consulte [Solicitudes de autenticación de Azure Resource Manager](authentication-requests-and-responses.md).

## <a name="common-error-response"></a>Respuestas de errores habituales
El servicio usará códigos de estado HTTP para indicar el éxito o el error. Además, los errores contienen una respuesta en el formato siguiente:

   {  
     "error": {  
     "code": "BadRequest",  
     "message": "The key vault sku is invalid."  
     }  
   }  

|Nombre del elemento | Escriba | DESCRIPCIÓN |
|---|---|---|
| código | string | El tipo de error que se produjo.|
| Mensaje | string | Una descripción de lo que produjo el error. |



## <a name="see-also"></a>Otras referencias
 [Referencia de la API REST de Azure Key Vault](/rest/api/keyvault/)
