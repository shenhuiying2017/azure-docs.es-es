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
ms.date: 02/26/2017
ms.author: jingwang
ms.openlocfilehash: 51318a6bb4f88759984531b005c65712b6817ec0
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="store-credential-in-azure-key-vault"></a>Almacenamiento de credenciales en Azure Key Vault

Puede almacenar las credenciales de los almacenes de datos y los procesos en una instancia de [Azure Key Vault](../key-vault/key-vault-whatis.md). Azure Data Factory las recupera al ejecutar una actividad que usa el almacén de datos o el proceso.

Actualmente, todos los tipos de actividad, excepto la actividad personalizada, admiten esta característica. En cuanto a la configuración de conectores concretos, revise la sección "Propiedades del servicio vinculado" de [cada tema de conector](copy-activity-overview.md#supported-data-stores-and-formats) para obtener más información.

> [!NOTE]
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general, consulte la [documentación de la versión 1 de Data Factory](v1/data-factory-introduction.md).

## <a name="prerequisites"></a>requisitos previos

Esta característica se basa en la identidad de servicio de Data Factory. Obtenga información acerca de cómo funciona en [Identidad de servicio de Data Factory](data-factory-service-identity.md) y asegúrese de que su instancia de Data Factory tenga una asociada.

## <a name="steps"></a>Pasos

Para hacer referencia a una credencial almacenada en Azure Key Vault, deberá seguir estos pasos:

1. **[Recuperación de la identidad de servicio de Data Factory](data-factory-service-identity.md#retrieve-service-identity)**: copie el valor de "ID. DE LA APLICACIÓN DE IDENTIDAD DE SERVICIO" que se generó junto con la factoría.
2. **Concesión del acceso de identidad de servicio a Azure Key Vault**: en el almacén de claves -> Directivas de acceso -> Agregar nueva ->, busque este identificador de la aplicación de identidad de servicio para conceder el permiso **Get** en el menú desplegable Permisos de secretos. De esta forma, la factoría designada puede acceder al secreto del almacén de claves.
3. **Creación de un servicio vinculado que apunte a Azure Key Vault.** Consulte [Servicio vinculado de Azure Key Vault](#azure-key-vault-linked-service).
4. **Creación de un servicio vinculado a un almacén de datos en el que se hace referencia al secreto correspondiente guardado en el almacén de claves.** Consulte [Secreto de referencia almacenado en el almacén de claves](#reference-secret-stored-in-key-vault).

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

## <a name="reference-secret-stored-in-key-vault"></a>Secreto de referencia almacenado en el almacén de claves

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