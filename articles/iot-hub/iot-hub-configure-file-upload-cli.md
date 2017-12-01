---
title: "Configuración de carga de archivos a IoT Hub con CLI de Azure (az.py) | Documentos de Microsoft"
description: "Configuración de cargas de archivos a Azure IoT Hub mediante la CLI de Azure 2.0 (az.py) multiplataforma."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 915f1597-272d-4fd4-8c5b-a0ccb1df0d91
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: 6b100e65aba604fd8becb02c3a205b3348872bc4
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/18/2017
---
# <a name="configure-iot-hub-file-uploads-using-azure-cli"></a>Configuración de cargas de archivos de IoT Hub mediante la CLI de Azure

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

Para usar la [funcionalidad de carga de archivos en IoT Hub][lnk-upload], primero debe asociar una cuenta de Azure Storage con IoT Hub. Puede usar una cuenta de almacenamiento existente o crear una nueva.

Para completar este tutorial, necesitará lo siguiente:

* Una cuenta de Azure activa. Si no tiene ninguna, puede crear una [cuenta gratuita][lnk-free-trial] en tan solo unos minutos.
* [CLI de Azure 2.0][lnk-CLI-install].
* Un centro de Azure IoT. Si no tiene ningún IoT Hub, puede usar el [comando][lnk-cli-create-iothub] `az iot hub create` para crear uno o usar el portal para [Crear un IoT Hub][lnk-portal-hub].
* Una cuenta de Azure Storage. Si no tiene ninguna cuenta de Azure Storage, puede usar la [CLI de Azure 2.0: Administrar cuentas de almacenamiento][lnk-manage-storage] para crear una o usar el portal para [Crear una cuenta de almacenamiento][lnk-portal-storage].

## <a name="sign-in-and-set-your-azure-account"></a>Inicio de sesión y configuración de la cuenta de Azure

Inicie sesión en la cuenta de Azure y seleccione su suscripción.

1. En el símbolo del sistema, ejecute el [comando de inicio de sesión][lnk-login-command]:

    ```azurecli
    az login
    ```

    Siga las instrucciones para realizar la autenticación mediante el código e inicie sesión en la cuenta de Azure a través de un explorador web.

1. Si tiene varias suscripciones de Azure, iniciar sesión en Azure le concede acceso a todas las cuentas de Azure asociadas con las credenciales. Use el siguiente [comando para mostrar las cuentas de Azure][lnk-az-account-command] que tiene disponibles para su uso:

    ```azurecli
    az account list
    ```

    Use el siguiente comando para seleccionar la suscripción que desea usar para ejecutar los comandos que crearán la instancia de IoT Hub. Puede usar el nombre de la suscripción o el identificador de la salida del comando anterior:

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

## <a name="retrieve-your-storage-account-details"></a>Recuperación de los detalles de la cuenta de almacenamiento

En los siguientes pasos se supone que ha creado la cuenta de almacenamiento mediante el modelo de implementación de **Resource Manager**, y no el modelo **clásico**.

Para configurar cargas de archivos desde sus dispositivos, necesitará la cadena de conexión de una cuenta de almacenamiento de Azure. Esta cuenta debe encontrarse en la misma suscripción que IoT Hub. También necesitará el nombre de un contenedor de blobs de la cuenta de almacenamiento. Use el siguiente comando para recuperar las claves de la cuenta de almacenamiento:

```azurecli
az storage account show-connection-string --name {your storage account name} --resource-group {your storage account resource group}
```

Anote el valor de **connectionString**. La necesitará en los pasos siguientes.

Puede usar un contenedor de blobs existente para sus cargas de archivos o crear uno nuevo:

* Para mostrar los contenedores de blobs existentes en su cuenta de almacenamiento, use el siguiente comando:

    ```azurecli
    az storage container list --connection-string "{your storage account connection string}"
    ```

* Para crear un contenedor de blobs en su cuenta de almacenamiento, use el siguiente comando:

    ```azurecli
    az storage container create --name {container name} --connection-string "{your storage account connection string}"
    ```

## <a name="file-upload"></a>Carga de archivos

Ahora puede configurar el centro de IoT para habilitar la [funcionalidad de carga de archivos] [lnk-upload] con los datos de su cuenta de almacenamiento.

La configuración requiere los siguientes valores:

**Contenedor de almacenamiento:**: un contenedor de blobs en una cuenta de Azure Storage en la suscripción actual para asociar con IoT Hub. En la sección anterior, recuperó la información necesaria de la cuenta de almacenamiento. IoT Hub genera automáticamente identificadores URI de SAS con permisos de escritura en este contenedor de blobs para los dispositivos que se utilizarán cuando se carguen archivos.

**Receive notifications for uploaded files** (Recibir notificaciones para archivos cargados): habilite o deshabilite las notificaciones de carga de archivos.

**SAS TTL**(TTL SAS): este valor es el periodo de vida de los URI de SAS que IoT Hub devuelve al dispositivo. De forma predeterminada, está establecido en una hora.

**File notification settings default TTL**(TTL predeterminado de configuración de notificación de archivos): el periodo de vida de una notificación de carga de archivos antes de que caduque. De forma predeterminada, está establecido en un día.

**File notification maximum delivery count**(Número máximo de entregas de notificaciones de archivo): el número de veces que IoT Hub tratará de entregar una notificación de carga de archivos. De forma predeterminada, está establecido en 10.

Use los siguientes comandos del CLI de Azure para configurar la carga de archivos en IoT Hub:

En un uso de shell de Bash:

```azurecli
az iot hub update --name {your iot hub name} --set properties.storageEndpoints.'$default'.connectionString="{your storage account connection string}"
az iot hub update --name {your iot hub name} --set properties.storageEndpoints.'$default'.containerName="{your storage container name}"
az iot hub update --name {your iot hub name} --set properties.storageEndpoints.'$default'.sasTtlAsIso8601=PT1H0M0S

az iot hub update --name {your iot hub name} --set properties.enableFileUploadNotifications=true
az iot hub update --name {your iot hub name} --set properties.messagingEndpoints.fileNotifications.maxDeliveryCount=10
az iot hub update --name {your iot hub name} --set properties.messagingEndpoints.fileNotifications.ttlAsIso8601=PT1H0M0S
```

En un uso de símbolo del sistema de Windows:

```azurecli
az iot hub update --name {your iot hub name} --set "properties.storageEndpoints.$default.connectionString="{your storage account connection string}""
az iot hub update --name {your iot hub name} --set "properties.storageEndpoints.$default.containerName="{your storage container name}""
az iot hub update --name {your iot hub name} --set "properties.storageEndpoints.$default.sasTtlAsIso8601=PT1H0M0S"

az iot hub update --name {your iot hub name} --set properties.enableFileUploadNotifications=true
az iot hub update --name {your iot hub name} --set properties.messagingEndpoints.fileNotifications.maxDeliveryCount=10
az iot hub update --name {your iot hub name} --set properties.messagingEndpoints.fileNotifications.ttlAsIso8601=PT1H0M0S
```

Puede revisar la configuración de carga de archivos en IoT Hub con el siguiente comando:

```azurecli
az iot hub show --name {your iot hub name}
```

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre las funcionalidades de carga de archivos de IoT Hub, consulte [Carga de archivos desde un dispositivo][lnk-upload].

Siga estos vínculos para más información sobre la administración de Azure IoT Hub:

* [Administración masiva de dispositivos de IoT][lnk-bulk]
* [Métricas de IoT Hub][lnk-metrics]
* [Supervisión de operaciones][lnk-monitor]

Para explorar aún más las funcionalidades de IoT Hub, consulte:

* [Guía para desarrolladores de IoT Hub][lnk-devguide]
* [Implementación de Azure IoT Edge en un dispositivo simulado en Linux: versión preliminar][lnk-iotedge]
* [Protección total de la solución de IoT][lnk-securing]

[13]: ./media/iot-hub-configure-file-upload/file-upload-settings.png
[14]: ./media/iot-hub-configure-file-upload/file-upload-container-selection.png
[15]: ./media/iot-hub-configure-file-upload/file-upload-selected-container.png

[lnk-upload]: iot-hub-devguide-file-upload.md

[lnk-bulk]: iot-hub-bulk-identity-mgmt.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-securing]: iot-hub-security-ground-up.md


[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-CLI-install]: https://docs.microsoft.com/cli/azure/install-az-cli2
[lnk-login-command]: https://docs.microsoft.com/cli/azure/get-started-with-az-cli2
[lnk-az-account-command]: https://docs.microsoft.com/cli/azure/account
[lnk-az-register-command]: https://docs.microsoft.com/cli/azure/provider
[lnk-az-addcomponent-command]: https://docs.microsoft.com/cli/azure/component
[lnk-az-resource-command]: https://docs.microsoft.com/cli/azure/resource
[lnk-az-iot-command]: https://docs.microsoft.com/cli/azure/iot
[lnk-iot-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-manage-storage]:../storage/common/storage-azure-cli.md#manage-storage-accounts
[lnk-portal-storage]:../storage/common/storage-create-storage-account.md
[lnk-cli-create-iothub]: https://docs.microsoft.com/cli/azure/iot/hub#az_iot_hub_create