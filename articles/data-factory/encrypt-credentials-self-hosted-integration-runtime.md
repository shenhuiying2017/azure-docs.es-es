---
title: Cifrado de credenciales en Azure Data Factory | Microsoft Docs
description: "Aprenda a cifrar y almacenar credenciales para sus almacenes de datos locales en una máquina con Integration Runtime autohospedado."
services: data-factory
documentationcenter: 
author: nabhishek
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: abnarain
ms.openlocfilehash: 0f42d971fcf21d0f719468a8c10ff637f2e523f9
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2018
---
# <a name="encrypt-credentials-for-on-premises-data-stores-in-azure-data-factory"></a>Cifrado de credenciales de almacenes de datos locales en Azure Data Factory
Puede cifrar y almacenar las credenciales de los almacenes de datos locales (servicios vinculados con información confidencial) en una máquina con Integration Runtime autohospedado. 

Se pasa un archivo de definición JSON con credenciales al cmdlet <br/>[**New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential**](https://docs.microsoft.com/powershell/module/azurerm.datafactoryv2/New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential?view=azurermps-4.4.0) para generar un archivo de definición JSON de salida con las credenciales cifradas. A continuación, se usa la definición de JSON actualizada para crear los servicios vinculados.

> [!NOTE]
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general, vea la [documentación de Data Factory versión 1](v1/data-factory-introduction.md).

## <a name="author-sql-server-linked-service"></a>Creación del servicio vinculado de SQL Server
Cree un archivo JSON denominado **SqlServerLinkedService.json** en cualquier carpeta con el siguiente contenido:  

Reemplace `<servername>`, `<databasename>`, `<username>` y `<password>` por los valores de SQL Server antes de guardar el archivo. Y, reemplace `<integration runtime name>` por el nombre de su instancia de Integration Runtime. 

```json
{
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=<servername>;Database=<databasename>;User ID=<username>;Password=<password>;Timeout=60"
            }
        },
        "connectVia": {
            "type": "integrationRuntimeReference",
            "referenceName": "<integration runtime name>"
        },
        "name": "SqlServerLinkedService"
    }
}
```

## <a name="encrypt-credentials"></a>Cifrado de las credenciales
Para cifrar los datos confidenciales de la carga de JSON en una instancia de Integration Runtime autohospedado, ejecute **New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential** y pase dicha carga. Este cmdlet garantiza que las credenciales se cifran utilizando DPAPI y se almacenan en el nodo de Integration Runtime autohospedado de manera local. La carga de salida se puede redirigir a otro archivo JSON (en este caso, "encryptedLinkedService.json"), que contiene las credenciales cifradas.

```powershell
New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "SqlServerLinkedService" -DefinitionFile ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
```

## <a name="use-the-json-with-encrypted-credentials"></a>Uso de JSON con credenciales cifradas
Ahora, use el archivo JSON de salida del comando anterior que contiene la credencial cifrada para configurar **SqlServerLinkedService**.

```powershell
Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -DefinitionFile ".\encryptedSqlServerLinkedService.json" 
```

## <a name="next-steps"></a>pasos siguientes
Para más información sobre las consideraciones de seguridad en el movimiento de datos, consulte [Consideraciones sobre la seguridad del movimiento de datos](data-movement-security-considerations.md).

