---
title: Almacenamiento de credenciales en Azure Key Vault | Microsoft Docs
description: "Aprenda a almacenar credenciales para almacenes de datos usados en una instancia de Azure Key Vault que Azure Data Factory pueda recuperar automáticamente en tiempo de ejecución."
services: data-factory
author: spelluru
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2017
ms.author: spelluru
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: fc8b4f1ecf664c7db5ab2e535245dee90415fa65
ms.contentlocale: es-es
ms.lasthandoff: 09/25/2017

---

# <a name="store-credential-in-azure-key-vault"></a>Almacenamiento de credenciales en Azure Key Vault

Puede almacenar las credenciales de los almacenes de datos en una instancia de [Azure Key Vault](../key-vault/key-vault-whatis.md). Azure Data Factory las recupera al ejecutar una actividad que usa el almacén de datos.

> [!NOTE]
> Actualmente, solo el [conector de Dynamics](connector-dynamics-crm-office-365.md) admite esta característica. 

## <a name="steps"></a>Pasos

Al crear una factoría de datos, se crea también una identidad de servicio. La identidad de servicio es una aplicación administrada registrada en Azure Active Directory y representa esta factoría de datos específica. Puede encontrar la información de la identidad de servicio en Azure Portal -> su factoría de datos -> Propiedades: 

- ID. DE LA IDENTIDAD DE SERVICIO
- INQUILINO DE LA IDENTIDAD DE SERVICIO
- ID. DE LA APLICACIÓN DE IDENTIDAD DE SERVICIO

Para hacer referencia a una credencial almacenada en Azure Key Vault, deberá seguir estos pasos:

1. Copie el "ID. DE LA APLICACIÓN DE IDENTIDAD DE SERVICIO" generado junto con la factoría de datos.
2. Conceda a la identidad de servicio acceso a su instancia de Azure Key Vault. En el almacén de claves -> Control de acceso -> Agregar ->, busque este identificador de aplicación de la identidad de servicio para agregar permiso de lectura. De esta forma, la factoría designada puede acceder al secreto del almacén de claves.
3. Cree un servicio vinculado que apunte a su instancia de Azure Key Vault. Consulte [Servicio vinculado de Azure Key Vault](#azure-key-vault-linked-service).
4. Cree un servicio vinculado de almacén de datos, en el que se haga referencia al secreto correspondiente almacenado en el almacén de claves. Consulte [Credencial de referencia almacenada en el almacén de claves](#reference-credential-stored-in-key-vault).

## <a name="azure-key-vault-linked-service"></a>Servicio vinculado de Azure Key Vault

Las siguientes propiedades son compatibles con el servicio vinculado de Azure Key Vault:

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad type debe establecerse en **AzureKeyVault**. | Sí |
| baseUrl | Especifique la dirección URL de Azure Key Vault (nombre DNS). | Sí |

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

## <a name="next-steps"></a>Pasos siguientes
Consulte los [almacenes de datos compatibles](copy-activity-overview.md##supported-data-stores-and-formats) para ver la lista de almacenes de datos que la actividad de copia de Azure Data Factory admite como orígenes y receptores.
