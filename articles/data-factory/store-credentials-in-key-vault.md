---
title: Almacenamiento de credenciales en Azure Key Vault | Microsoft Docs
description: Aprenda a almacenar credenciales para almacenes de datos usados en una instancia de Azure Key Vault que Azure Data Factory pueda recuperar automáticamente en tiempo de ejecución.
services: data-factory
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2017
ms.author: jingwang
ms.openlocfilehash: 4a8c96bf9124feede2e5a28beb791636784dcad7
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
ms.locfileid: "32166283"
---
# <a name="store-credential-in-azure-key-vault"></a>Almacenamiento de credenciales en Azure Key Vault

Puede almacenar las credenciales de los almacenes de datos y los procesos en una instancia de [Azure Key Vault](../key-vault/key-vault-whatis.md). Azure Data Factory las recupera al ejecutar una actividad que usa el almacén de datos o el proceso.

Actualmente, todos los tipos de actividad, excepto la actividad personalizada, admiten esta característica. En cuanto a la configuración de conectores concretos, revise la sección "Propiedades del servicio vinculado" de [cada tema de conector](copy-activity-overview.md#supported-data-stores-and-formats) para obtener más información.

> [!NOTE]
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general, consulte la [documentación de la versión 1 de Data Factory](v1/data-factory-introduction.md).

## <a name="prerequisites"></a>requisitos previos

Esta característica se basa en la identidad de servicio de Data Factory. Obtenga información acerca de cómo funciona en [Identidad de servicio de Data Factory](data-factory-service-identity.md) y asegúrese de que su instancia de Data Factory tenga una asociada.

>[!TIP]
>En Azure Key Vault, cuando cree un secreto,  **ponga todo el valor de una propiedad secreta que solicite el servicio vinculado ADF (por ejemplo, cadena de conexión/contraseña/clave de entidad de servicio/etc)**. Por ejemplo, para el servicio vinculado de Azure Storage, ponga `DefaultEndpointsProtocol=http;AccountName=myAccount;AccountKey=myKey;` como secreto de AKV, luego haga referencia en el campo "connectionString" de ADF; para el servicio vinculado de Dynamics, ponga `myPassword` como secreto de AKV, luego haga referencia en el campo "password" de ADF. Consulte los artículos de cada conector/proceso para conocer los detalles de propiedad admitidos.

## <a name="steps"></a>Pasos

Para hacer referencia a una credencial almacenada en Azure Key Vault, deberá seguir estos pasos:

1. **Recupere la identidad de servicio de Data Factory** copiando el valor del id. de la aplicación de identidad de servicio que se genera con la factoría. Si usa la IU de creación de ADF, el identificador de identidad del servicio se mostrará en la ventana de creación del servicio vinculado de Azure Key Vault; también puede recuperarlo de Azure Portal, consulte [Recuperación de la identidad de servicio](data-factory-service-identity.md#retrieve-service-identity).
2. **Concesión del acceso de identidad de servicio a Azure Key Vault**: en el almacén de claves -> Directivas de acceso -> Agregar nueva ->, busque este identificador de la aplicación de identidad de servicio para conceder el permiso **Get** en el menú desplegable Permisos de secretos. De esta forma, la factoría designada puede acceder al secreto del almacén de claves.
3. **Creación de un servicio vinculado que apunte a Azure Key Vault.** Consulte [Servicio vinculado de Azure Key Vault](#azure-key-vault-linked-service).
4. **Creación de un servicio vinculado a un almacén de datos en el que se hace referencia al secreto correspondiente guardado en el almacén de claves.** Consulte [Secreto de referencia almacenado en el almacén de claves](#reference-secret-stored-in-key-vault).

## <a name="azure-key-vault-linked-service"></a>Servicio vinculado de Azure Key Vault

Las siguientes propiedades son compatibles con el servicio vinculado de Azure Key Vault:

| Propiedad | DESCRIPCIÓN | Obligatorio |
|:--- |:--- |:--- |
| Tipo | La propiedad type debe establecerse en **AzureKeyVault**. | Sí |
| baseUrl | Especifique la dirección URL de Azure Key Vault. | Sí |

**Uso de la IU de creación:**

Haga clic en **Conexiones** -> **Servicios vinculados** -> **+ Nuevo** -> busque "Azure Key Vault":

![Búsqueda de AKV](media/store-credentials-in-key-vault/search-akv.png)

Seleccione el almacén de Azure Key Vault aprovisionado en el que se almacenan las credenciales. Puede hacer una **conexión de prueba** para asegurarse de que su conexión de AKV es válida. 

![Configuración de AKV](media/store-credentials-in-key-vault/configure-akv.png)

**Ejemplo JSON:**

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

**Uso de la IU de creación:**

Seleccione **Azure Key Vault** para campos secretos al crear la conexión al almacén de datos/proceso. Seleccione el servicio vinculado de Azure Key Vault aprovisionado y proporcione el **nombre del secreto**. También puede proporcionar una versión del secreto. 

![Configuración de secreto de AKV](media/store-credentials-in-key-vault/configure-akv-secret.png)

**Ejemplo de JSON: (consulte la sección "Contraseña")**

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

## <a name="next-steps"></a>Pasos siguientes
Consulte los [almacenes de datos compatibles](copy-activity-overview.md#supported-data-stores-and-formats) para ver la lista de almacenes de datos que la actividad de copia de Azure Data Factory admite como orígenes y receptores.