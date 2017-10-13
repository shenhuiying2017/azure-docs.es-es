---
title: "Configuración de una cadena de conexión para Azure Storage | Microsoft Docs"
description: "Configure una cadena de conexión para una cuenta de Azure Storage. Una cadena de conexión contiene la información necesaria para autenticar el acceso a una cuenta de almacenamiento desde una aplicación en tiempo de ejecución."
services: storage
documentationcenter: 
author: tamram
manager: timlt
editor: tysonn
ms.assetid: ecb0acb5-90a9-4eb2-93e6-e9860eda5e53
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: tamram
ms.openlocfilehash: 192799cb44dc9a56c65a6414c1267c506252fe29
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="configure-azure-storage-connection-strings"></a>Configuración de las cadenas de conexión de Azure Storage

Una cadena de conexión incluye la información de autenticación que requiere una aplicación para acceder a los datos de una cuenta de Azure Storage en tiempo de ejecución. Las cadenas de conexión se pueden configurar para:

* Conectarse al emulador de Azure Storage.
* Acceder a la cuenta de Azure Storage.
* Acceder a recursos especificados en Azure a través de una firma de acceso compartido (SAS).

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

## <a name="storing-your-connection-string"></a>Almacenamiento de la cadena de conexión
La aplicación necesita acceder a la cadena de conexión en tiempo de ejecución para autenticar las solicitudes realizadas a Azure Storage. Tiene varias opciones para almacenar una cadena de conexión:

* Una aplicación que se ejecuta en el escritorio o en un dispositivo puede almacenar la cadena de conexión en un archivo **app.config** o **web.config**. Agregue la cadena de conexión a la sección **AppSettings** en estos archivos.
* Una aplicación que se ejecuta en un servicio en la nube de Azure puede almacenar la cadena de conexión en el [archivo de esquema de configuración de servicio de Azure (.cscfg)](https://msdn.microsoft.com/library/ee758710.aspx). Agregue la cadena de conexión a la sección **ConfigurationSettings** del archivo de configuración del servicio.
* La cadena de conexión se puede usar directamente en el código. Sin embargo, es aconsejable que en la mayoría de los escenarios se recomienda almacenar la cadena de configuración en un archivo de configuración.

El almacenamiento de la cadena de conexión en un archivo de configuración facilita la actualización de la cadena de conexión para que alterne entre el emulador de Storage y una cuenta de Azure Storage en la nube. Solo necesitará editar la cadena de conexión para apuntar al entorno de destino.

Puede usar el [Administrador de configuración de Microsoft Azure](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/) para acceder a la cadena de conexión en tiempo de ejecución independientemente de dónde se ejecute la aplicación.

## <a name="create-a-connection-string-for-the-storage-emulator"></a>Creación de una cadena de conexión para el emulador de Storage
[!INCLUDE [storage-emulator-connection-string-include](../../../includes/storage-emulator-connection-string-include.md)]

Para más información acerca del emulador de Storage, consulte [Uso del emulador de Azure Storage para desarrollo y pruebas](storage-use-emulator.md).

## <a name="create-a-connection-string-for-an-azure-storage-account"></a>Creación de una cadena de conexión para una cuenta de Azure Storage
Para crear una cadena de conexión para una cuenta de Azure Storage, use el siguiente formato. Indique si desea conectarse a la cuenta de almacenamiento a través de HTTPS (recomendado) o HTTP, reemplace `myAccountName` por el nombre de la cuenta de almacenamiento y reemplace `myAccountKey` por la clave de acceso a la cuenta:

`DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey`

Por ejemplo, la cadena de conexión podría ser similar a la siguiente:

`DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=<account-key>`

Aunque Azure Storage admite HTTP y HTTPS en una cadena de conexión, *se recomienda encarecidamente utilizar HTTPS*.

> [!TIP]
> Las cadenas de conexión de la cuenta de almacenamiento se pueden encontrar en [Azure Portal](https://portal.azure.com). Navegue a **CONFIGURACIÓN** > **Claves de acceso** en la hoja del menú de la cuenta de almacenamiento para ver las cadenas de conexión de las claves de acceso principal y secundaria.
>

## <a name="create-a-connection-string-using-a-shared-access-signature"></a>Creación de una cadena de conexión con una firma de acceso compartido
[!INCLUDE [storage-use-sas-in-connection-string-include](../../../includes/storage-use-sas-in-connection-string-include.md)]

## <a name="create-a-connection-string-for-an-explicit-storage-endpoint"></a>Creación de una cadena de conexión para un punto de conexión de Storage explícito
En una cadena de conexión se pueden especificar puntos de conexión de servicio explícitos, en lugar de usar los predeterminados. Para crear una cadena de conexión que especifique un punto de conexión explícito, especifique el punto de conexión de servicio completo para cada servicio, incluida la especificación de protocolo (HTTPS (recomendado) o HTTP) con el formato siguiente:

```
DefaultEndpointsProtocol=[http|https];
BlobEndpoint=myBlobEndpoint;
FileEndpoint=myFileEndpoint;
QueueEndpoint=myQueueEndpoint;
TableEndpoint=myTableEndpoint;
AccountName=myAccountName;
AccountKey=myAccountKey
```

Un escenario en el que puede desear especificar un punto de conexión explícito es cuando ha asignado un punto de conexión de Blob Storage a un [dominio personalizado](../blobs/storage-custom-domain-name.md). En ese caso, puede especificar un punto de conexión personalizado para Blob Storage en la cadena de conexión. También puede especificar los puntos de conexión predeterminados para los demás servicios, en caso de que la aplicación los use.

Este es un ejemplo de una cadena de conexión que especifica un punto de conexión explícito para Blob service:

```
# Blob endpoint only
DefaultEndpointsProtocol=https;
BlobEndpoint=http://www.mydomain.com;
AccountName=storagesample;
AccountKey=<account-key>
```

Este ejemplo especifica puntos de conexión explícitos para todos los servicios, lo que incluye un dominio personalizado para Blob service:

```
# All service endpoints
DefaultEndpointsProtocol=https;
BlobEndpoint=http://www.mydomain.com;
FileEndpoint=https://myaccount.file.core.windows.net;
QueueEndpoint=https://myaccount.queue.core.windows.net;
TableEndpoint=https://myaccount.table.core.windows.net;
AccountName=storagesample;
AccountKey=<account-key>
```

Los valores del punto de conexión de una cadena de conexión se usan para construir los identificadores URI de solicitud para los servicios de Storage e indicar el formato de los identificadores URI que se devuelven al código.

Si ha asignado un punto de conexión de Storage a un dominio personalizado y omite dicho punto en una cadena de conexión, no podrá usarla para acceder a los datos de dicho servicio desde el código.

> [!IMPORTANT]
> Los valores del punto de conexión de servicio de las cadenas de conexión deben ser identificadores URI con el formato correcto, entre los que se incluyen `https://` (recomendado) o `http://`. Dado que Azure Storage aún no admite HTTPS en dominios personalizados, *debe* especificar `http://` para los identificadores URI de todos los puntos de conexión que apunten a un dominio personalizado.
>

### <a name="create-a-connection-string-with-an-endpoint-suffix"></a>Creación de una cadena de conexión con el sufijo de un punto de conexión
Para crear una cadena de conexión para un servicio de Storage en regiones o instancias con sufijos de punto de conexión diferentes, como Azure China o Azure Government, utilice el siguiente formato de cadena de conexión. Indique si desea conectarse a la cuenta de almacenamiento a través de HTTP (recomendado) o HTTPS, reemplace `myAccountName` por el nombre de la cuenta de almacenamiento, reemplace `myAccountKey` por la clave de acceso a la cuenta y reemplace `mySuffix` por el sufijo del identificador URI:

```
DefaultEndpointsProtocol=[http|https];
AccountName=myAccountName;
AccountKey=myAccountKey;
EndpointSuffix=mySuffix;
```

Este es un ejemplo de cadena de conexión para los servicios de Storage en Azure China:

```
DefaultEndpointsProtocol=https;
AccountName=storagesample;
AccountKey=<account-key>;
EndpointSuffix=core.chinacloudapi.cn;
```

## <a name="parsing-a-connection-string"></a>Análisis de una cadena de conexión
[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

## <a name="next-steps"></a>Pasos siguientes
* [Uso del emulador de Azure Storage para desarrollo y pruebas](storage-use-emulator.md)
* [Exploradores de Azure Storage](storage-explorers.md)
* [Uso de Firmas de acceso compartido (SAS)](storage-dotnet-shared-access-signature-part-1.md)

