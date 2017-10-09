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
ms.date: 09/25/2017
ms.author: abnarain
ms.translationtype: HT
ms.sourcegitcommit: a6bba6b3b924564fe7ae16fa1265dd4d93bd6b94
ms.openlocfilehash: 8363187b7c8492e9386715324f87e70d7d8615af
ms.contentlocale: es-es
ms.lasthandoff: 09/28/2017

---

# <a name="encrypt-credentials-for-on-premises-data-stores-in-azure-data-factory"></a>Cifrado de credenciales de almacenes de datos locales en Azure Data Factory
Puede cifrar y almacenar las credenciales de los almacenes de datos locales (servicios vinculados con información confidencial) en una máquina con Integration Runtime autohospedado. 

Se pasa un archivo de definición JSON con credenciales al cmdlet <br/>[**New-AzureRmDataFactoryV2LinkedServiceEncryptCredential**](https://docs.microsoft.com/powershell/module/azurerm.datafactoryv2/New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential?view=azurermps-4.4.0) para generar un archivo de definición JSON de salida con las credenciales cifradas. A continuación, se usa la definición de JSON actualizada para crear los servicios vinculados.

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
Para cifrar los datos confidenciales de la carga de JSON en una instancia de Integration Runtime autohospedado, ejecute **New-AzureRmDataFactoryV2LinkedServiceEncryptCredential** y pase dicha carga. Este cmdlet garantiza que las credenciales se cifran utilizando DPAPI y se almacenan en el nodo de Integration Runtime autohospedado de manera local. La carga de salida se puede redirigir a otro archivo JSON (en este caso, "encryptedLinkedService.json"), que contiene las credenciales cifradas.

```powershell
New-AzureRmDataFactoryV2LinkedServiceEncryptCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "SqlServerLinkedService" -DefinitionFile ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
```

## <a name="use-the-json-with-encrypted-credentials"></a>Uso de JSON con credenciales cifradas
Ahora, use el archivo JSON de salida del comando anterior que contiene la credencial cifrada para configurar **SqlServerLinkedService**.

```powershell
Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -DefinitionFile ".\encryptedSqlServerLinkedService.json" 
```

## <a name="next-steps"></a>Pasos siguientes
Para más información sobre las consideraciones de seguridad en el movimiento de datos, consulte [Consideraciones sobre la seguridad del movimiento de datos](data-movement-security-considerations.md).


