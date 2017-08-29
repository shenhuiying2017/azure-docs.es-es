---
title: Requerir transferencia segura en Azure Storage | Microsoft Docs
description: "Obtenga información acerca de la característica \"Requerir transferencia segura\" de Azure Storage y cómo habilitarla."
services: storage
documentationcenter: na
author: fhryo-msft
manager: Jason.Hogg
editor: fhryo-msft
ms.assetid: 
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 06/20/2017
ms.author: fryu
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: bc5b7fc79869c632db96958f17aaf953a5fd3b19
ms.contentlocale: es-es
ms.lasthandoff: 08/21/2017

---
# <a name="require-secure-transfer"></a>Requerir transferencia segura

La opción "Se requiere transferencia segura" mejora la seguridad de su cuenta de almacenamiento, ya que solo permite enviar solicitudes a la cuenta de almacenamiento desde conexiones seguras. Por ejemplo, al llamar a las API de REST para acceder a una cuenta de almacenamiento, es preciso conectarse mediante HTTPS. Si "Se requiere transferencia segura" está habilitada, se rechazan todas las solicitudes en que se use HTTP.

Si se usa el servicio Azure Files, se produce un error en todas las conexiones sin cifrado cuando la opción "Se requiere transferencia segura" está habilitada. Aquí se incluyen los escenarios que usan SMB 2.1 y SMB 3.0 sin cifrado, y algunas versiones del cliente SMB de Linux. 

De forma predeterminada, la opción "Se requiere transferencia segura" está deshabilitada.

> [!NOTE]
> Dado que Azure Storage no admite HTTPS para los nombres de dominio personalizados, esta opción no se aplica cuando se utiliza un nombre de dominio personalizado.

## <a name="enable-secure-transfer-required-in-the-azure-portal"></a>Habilitación de "Se requiere transferencia segura" en Azure Portal

La opción de "Se requiere transferencia segura" se puede habilitar cuando se crea una cuenta de almacenamiento en [Azure Portal](https://portal.azure.com)y para las cuentas de almacenamiento existentes.

### <a name="require-secure-transfer-when-you-create-a-storage-account"></a>Solicitud de transferencia segura al crear una cuenta de almacenamiento

1. Abra la hoja **Crear cuenta de almacenamiento** en Azure Portal.
1. En **Se requiere transferencia segura**, seleccione **Habilitado**.

  ![captura de pantalla](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_1.png)

### <a name="require-secure-transfer-for-an-existing-storage-account"></a>Solicitud de transferencia segura en una cuenta de almacenamiento existente

1. Seleccione una cuenta de almacenamiento existente en Azure Portal.
1. Seleccione **Configuración** en **CONFIGURACIÓN** en la hoja del menú de la cuenta de almacenamiento.
1. En **Se requiere transferencia segura**, seleccione **Habilitado**.

  ![captura de pantalla](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_2.png)

## <a name="enable-secure-transfer-required-programmatically"></a>Habilitación de "Se requiere transferencia segura" mediante programación

El nombre del valor es _supportsHttpsTrafficOnly_ en las propiedades de la cuenta de almacenamiento. Puede habilitar la configuración "Se requiere transferencia segura" con la API de REST, herramientas o bibliotecas:

* **API de REST** (versión: 2016-12-01): [paquete comercial](https://docs.microsoft.com/en-us/rest/api/storagerp/storageaccounts)
* **PowerShell** (versión: 4.1.0): [paquete comercial](https://docs.microsoft.com/en-us/powershell/module/azurerm.storage/set-azurermstorageaccount?view=azurermps-4.1.0)
* **CLI** (versión: 2.0.11): [paquete comercial](https://pypi.python.org/pypi/azure-cli-storage/2.0.11)
* **NodeJS** (versión: 1.1.0): [paquete comercial](https://www.npmjs.com/package/azure-arm-storage/)
* **SDK de .NET** (versión: 6.3.0): [paquete comercial](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/6.3.0-preview)
* **SDK de Python** (versión: 1.1.0): [paquete comercial](https://pypi.python.org/pypi/azure-mgmt-storage/1.1.0)
* **SDK de Ruby** (versión: 0.11.0): [paquete comercial](https://rubygems.org/gems/azure_mgmt_storage)

### <a name="enable-secure-transfer-required-setting-with-rest-api"></a>Habilitación de la configuración "Se requiere transferencia segura" con la API de REST

Para simplificar las pruebas con la API de REST, puede usar [ArmClient](https://github.com/projectkudu/ARMClient) para llamar desde la línea de comandos.

 Puede utilizar la línea de comandos siguiente para comprobar la configuración con la API de REST:

```
# Login Azure and proceed with your credentials
> armclient login

> armclient GET  /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{accountName}?api-version=2016-12-01
```

En la respuesta, puede encontrar el valor _supportsHttpsTrafficOnly_. Sample:

```Json
{
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{accountName}",
  "kind": "Storage",
  ...
  "properties": {
    ...
    "supportsHttpsTrafficOnly": false
  },
  "type": "Microsoft.Storage/storageAccounts"
}
```

Puede utilizar la línea de comandos siguiente para habilitar la configuración con la API de REST:

```
# Login Azure and proceed with your credentials
> armclient login

> armclient PUT /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{accountName}?api-version=2016-12-01 < Input.json
```
Ejemplo de Input.json:
```Json
{
  "location": "westus",
  "properties": {
    "supportsHttpsTrafficOnly": true
  }
}
```

## <a name="next-steps"></a>Pasos siguientes
Azure Storage proporciona un completo conjunto de funcionalidades de seguridad que, conjuntamente, permiten a los desarrolladores compilar aplicaciones seguras. Para obtener más detalles, visite la [Guía de seguridad para almacenamiento](storage-security-guide.md).

