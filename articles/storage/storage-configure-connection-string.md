---
title: "Configurar una cadena de conexión a Azure Storage | Microsoft Docs"
description: "Configuración de una cadena de conexión para una cuenta de Almacenamiento de Azure Una cadena de conexión incluye la información necesaria para autenticar el acceso a una cuenta de almacenamiento desde la aplicación en tiempo real."
services: storage
documentationcenter: 
author: mmacy
manager: timlt
editor: tysonn
ms.assetid: ecb0acb5-90a9-4eb2-93e6-e9860eda5e53
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2016
ms.author: marsma
translationtype: Human Translation
ms.sourcegitcommit: 1a3c754bf2f2b73d0bf72cbf48b906d8085eaef1
ms.openlocfilehash: f4410c10ce66d50b64307e364e64a3367b9397f9


---
# <a name="configure-azure-storage-connection-strings"></a>Configuración de las cadenas de conexión de Almacenamiento de Azure
## <a name="overview"></a>Información general
Una cadena de conexión incluye la información de autenticación necesaria para obtener acceso a los datos de una cuenta de almacenamiento de Azure desde la aplicación en tiempo de ejecución. Puede configurar una cadena de conexión de las maneras siguientes:

* Conéctese al emulador de almacenamiento de Azure.
* Acceda a la cuenta de almacenamiento en Azure.
* Obtenga acceso a recursos especificados en Azure a través de una firma de acceso compartido (SAS).

[!INCLUDE [storage-account-key-note-include](../../includes/storage-account-key-note-include.md)]

## <a name="storing-your-connection-string"></a>Almacenamiento de la cadena de conexión
La aplicación necesitaría obtener acceso a la cadena de conexión en tiempo de ejecución para autenticar las solicitudes realizadas en Almacenamiento de Azure. Tiene diversas opciones para almacenar la cadena de conexión:

* Para una aplicación que se ejecuta en el escritorio o en un dispositivo, puede almacenar la cadena de conexión en un archivo **app.config** o **web.config**. Agregue la cadena de conexión a la sección **AppSettings** .
* Para una aplicación que se ejecute en un servicio en la nube de Azure, puede guardar la cadena de conexión en el [archivo de esquema de configuración de servicio de Azure (.cscfg)](https://msdn.microsoft.com/library/ee758710.aspx). Agregue la cadena de conexión a la sección **ConfigurationSettings** del archivo de configuración del servicio.
* También puede utilizar la cadena de conexión directamente en el código. Sin embargo, para la mayoría de los escenarios, recomendamos que almacene su cadena de configuración en un archivo de configuración.

Almacenar la cadena de conexión dentro de un archivo de configuración facilita la actualización de la cadena de conexión para cambiar entre el emulador de almacenamiento y una cuenta de almacenamiento de Azure en la nube. Solo necesitará editar la cadena de conexión para apuntar al entorno de destino.

Puede usar la clase [Administrador de configuración de Microsoft Azure](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/) para obtener acceso a la cadena de conexión en tiempo de ejecución independientemente de dónde se ejecute la aplicación.

## <a name="create-a-connection-string-to-the-storage-emulator"></a>Creación de una cadena de conexión para el emulador de almacenamiento
[!INCLUDE [storage-emulator-connection-string-include](../../includes/storage-emulator-connection-string-include.md)]

Consulte [Uso de Emulador de almacenamiento de Azure para desarrollo y pruebas](storage-use-emulator.md) para obtener más información sobre el emulador de almacenamiento.

## <a name="create-a-connection-string-to-an-azure-storage-account"></a>Creación de una cadena de conexión para una cuenta de Almacenamiento de Azure
Para crear una cadena de conexión para su cuenta de Almacenamiento de Azure, use el formato de cadena de conexión que aparece a continuación. Indique si desea conectarse a la cuenta de almacenamiento con HTTP o HTTPS (recomendado), reemplace *myAccountName* con el nombre de la cuenta de almacenamiento y reemplace *myAccountKey* con la clave de acceso a la cuenta:

`DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey`

Por ejemplo, la cadena de conexión tendrá un aspecto similar a la siguiente cadena de conexión de ejemplo:

`DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=<account-key>`

> [!NOTE]
> Almacenamiento de Azure admite HTTP y HTTPS en una cadena de conexión; sin embargo, se recomienda encarecidamente utilizar HTTPS.
>
>

## <a name="create-a-connection-string-using-a-shared-access-signature"></a>Creación de una cadena de conexión con una firma de acceso compartido
[!INCLUDE [storage-use-sas-in-connection-string-include](../../includes/storage-use-sas-in-connection-string-include.md)]

## <a name="creating-a-connection-string-to-an-explicit-storage-endpoint"></a>Creación de una cadena de conexión para un extremo de almacenamiento explícito
Puede especificar de manera explícita los puntos de conexión de servicio en la cadena de conexión en lugar de usar los puntos de conexión predeterminados. Para crear una cadena de conexión que especifique un punto de conexión explícito, especifique el punto de conexión de servicio completo para cada servicio, incluida la especificación de protocolo (HTTPS (recomendado) o HTTP) con el formato siguiente:

```
DefaultEndpointsProtocol=[http|https];
BlobEndpoint=myBlobEndpoint;
QueueEndpoint=myQueueEndpoint;
TableEndpoint=myTableEndpoint;
FileEndpoint=myFileEndpoint;
AccountName=myAccountName;
AccountKey=myAccountKey
```

Un escenario donde puede que desee especificar un punto de conexión explícito es si ha asignado el punto de conexión de almacenamiento de blobs a un dominio personalizado. En ese caso, puede especificar el punto de conexión personalizado para Blob Storage en la cadena de conexión y, opcionalmente, especificar los puntos de conexión predeterminados para el otro servicio si la aplicación los utiliza.

Aquí se muestran ejemplos de cadenas de conexión válidas que especifican un punto de conexión explícito para el servicio BLOB:

```
# Blob endpoint only
DefaultEndpointsProtocol=https;
BlobEndpoint=www.mydomain.com;
AccountName=storagesample;
AccountKey=account-key

# All service endpoints
DefaultEndpointsProtocol=https;
BlobEndpoint=www.mydomain.com;
FileEndpoint=myaccount.file.core.windows.net;
QueueEndpoint=myaccount.queue.core.windows.net;
TableEndpoint=myaccount;
AccountName=storagesample;
AccountKey=account-key
```

El valor final que aparece en la cadena de conexión se utiliza para construir los URI de solicitud para el servicio Blob e indica el formato de los URI que se devuelven para el código.

Tenga en cuenta que si opta por omitir un punto de conexión de servicio de la cadena de conexión, no podrá usar esa cadena de conexión para obtener acceso a los datos en ese servicio desde el código.

### <a name="creating-a-connection-string-with-an-endpoint-suffix"></a>Creación de una cadena de conexión con un sufijo de extremo
Para crear una cadena de conexión para un servicio de almacenamiento en regiones o instancias con distintos sufijos de extremo, como para Azure China o Azure Governance, utilice el siguiente formato de cadena de conexión. Indique si desea conectarse a la cuenta de almacenamiento con HTTP o HTTPS, reemplace *myAccountName* con el nombre de la cuenta de almacenamiento y reemplace *myAccountKey* con la clave de acceso a la cuenta, y reemplace *mySuffix* con el sufijo del URI:

```
DefaultEndpointsProtocol=[http|https];
AccountName=myAccountName;
AccountKey=myAccountKey;
EndpointSuffix=mySuffix;
```

Por ejemplo, la cadena de conexión debe tener un aspecto similar a la siguiente cadena de conexión:

```
DefaultEndpointsProtocol=https;
AccountName=storagesample;
AccountKey=<account-key>;
EndpointSuffix=core.chinacloudapi.cn;
```

## <a name="parsing-a-connection-string"></a>Análisis de una cadena de conexión
[!INCLUDE [storage-cloud-configuration-manager-include](../../includes/storage-cloud-configuration-manager-include.md)]

## <a name="next-steps"></a>Pasos siguientes
* [Uso del emulador de almacenamiento de Azure para desarrollo y pruebas](storage-use-emulator.md)
* [Exploradores de almacenamiento de Azure](storage-explorers.md)
* [Uso de Firmas de acceso compartido (SAS)](storage-dotnet-shared-access-signature-part-1.md)




<!--HONumber=Dec16_HO2-->


