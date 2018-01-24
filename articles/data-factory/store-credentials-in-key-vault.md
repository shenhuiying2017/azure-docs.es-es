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
ms.date: 12/14/2017
ms.author: jingwang
ms.openlocfilehash: 145c2bc0556010389e78e523fde6fd4b9063f930
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/14/2017
---
# <a name="store-credential-in-azure-key-vault"></a>Almacenamiento de credenciales en Azure Key Vault

Puede almacenar las credenciales de los almacenes de datos en una instancia de [Azure Key Vault](../key-vault/key-vault-whatis.md). Azure Data Factory las recupera al ejecutar una actividad que usa el almacén de datos.

Actualmente, solo el [conector de Dynamics](connector-dynamics-crm-office-365.md), el [conector de Salesforce](connector-salesforce.md) y algunos conectores recientemente habilitados admiten esta característica. Habrá más próximamente. En los detalles, puede consultar el tema de cada uno de los conectores. En el caso de los campos secretos que admiten esta característica, verá la siguiente nota en la descripción: "*Puede seleccionar este campo como SecureString para almacenarlo de forma segura en ADF, o almacenar la contraseña en Azure Key Vault y permitir que se copie la extracción de la actividad desde allí al realizar la copia de datos. Obtenga más información sobre el Almacenamiento de credenciales en Key Vault*".

> [!NOTE]
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general, consulte la [documentación de la versión 1 de Data Factory](v1/data-factory-introduction.md).

## <a name="prerequisites"></a>requisitos previos

Esta característica se basa en la identidad de servicio de Data Factory. Obtenga información acerca de cómo funciona en [Identidad de servicio de Data Factory](data-factory-service-identity.md) y asegúrese de que su instancia de Data Factory tenga una asociada.

## <a name="steps"></a>Pasos

Para hacer referencia a una credencial almacenada en Azure Key Vault, deberá seguir estos pasos:

1. [Recupere la identidad de servicio de Data Factory](data-factory-service-identity.md#retrieve-service-identity) copiando el valor del id. de la aplicación de identidad de servicio que se genera con la factoría.
2. Conceda a la identidad de servicio acceso a su instancia de Azure Key Vault. En el almacén de claves -> Control de acceso -> Agregar ->, busque este identificador de aplicación de la identidad de servicio para agregar por lo menos el permiso de **Lector**. De esta forma, la factoría designada puede acceder al secreto del almacén de claves.
3. Cree un servicio vinculado que apunte a su instancia de Azure Key Vault. Consulte [Servicio vinculado de Azure Key Vault](#azure-key-vault-linked-service).
4. Cree un servicio vinculado de almacén de datos, en el que se haga referencia al secreto correspondiente almacenado en el almacén de claves. Consulte [Credencial de referencia almacenada en el almacén de claves](#reference-credential-stored-in-key-vault).

## <a name="azure-key-vault-linked-service"></a>Servicio vinculado de Azure Key Vault

Las siguientes propiedades son compatibles con el servicio vinculado de Azure Key Vault:

| Propiedad | DESCRIPCIÓN | Obligatorio |
|:--- |:--- |:--- |
| Tipo | La propiedad type debe establecerse en **AzureKeyVault**. | Sí |
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

| Propiedad | DESCRIPCIÓN | Obligatorio |
|:--- |:--- |:--- |
| Tipo | La propiedad type del campo debe establecerse en **AzureKeyVaultSecret**. | Sí |
| secretName | El nombre del secreto en Azure Key Vault. | Sí |
| secretVersion | La versión del secreto en Azure Key Vault.<br/>Si no se especifica, siempre se usa la versión más reciente del secreto.<br/>Si se especifica, se usa la versión dada.| Sin  |
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
                "secretName": "<secret name in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
}
```

## <a name="next-steps"></a>pasos siguientes
Consulte los [almacenes de datos compatibles](copy-activity-overview.md#supported-data-stores-and-formats) para ver la lista de almacenes de datos que la actividad de copia de Azure Data Factory admite como orígenes y receptores.