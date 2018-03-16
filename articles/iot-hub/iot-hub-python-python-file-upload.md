---
title: Carga de archivos desde dispositivos a Azure IoT Hub con Python | Microsoft Docs
description: "Cómo cargar archivos de un dispositivo a la nube mediante el SDK de dispositivo IoT de Azure para Python. Los archivos cargados se almacenan en un contenedor de blobs de Azure Storage."
services: iot-hub
documentationcenter: python
author: kgremban
manager: timlt
editor: 
ms.assetid: 4759d229-f856-4526-abda-414f8b00a56d
ms.service: iot-hub
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/05/2018
ms.author: kgremban
ms.openlocfilehash: 5939f87684e92e1f95d39ea5bd52b424ca683acc
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/08/2018
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub"></a>Carga de archivos de un dispositivo a la nube con IoT Hub

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Este tutorial describe cómo usar las [funcionalidades de carga de archivos de IoT Hub](iot-hub-devguide-file-upload.md) para cargar un archivo en [Azure Blob Storage](../storage/index.yml). En este tutorial se muestra cómo realizar las siguientes acciones:

- Proporcionar de forma segura un contenedor de almacenamiento para cargar un archivo.
- Usar el cliente de Python para cargar un archivo mediante IoT Hub.

Los tutoriales [Introducción a Iot Hub](iot-hub-node-node-getstarted.md) y [Envío de mensajes de nube a dispositivo con IoT Hub](iot-hub-node-node-c2d.md) muestran cómo usar la funcionalidad básica de mensajería de dispositivo a nube y de nube a dispositivo de IoT Hub. Sin embargo, en algunos casos no se pueden asignar fácilmente los datos de que los dispositivos envían en los mensajes de dispositivo a nube con un tamaño relativamente reducido que acepta Azure IoT Hub. Cuando necesite cargar archivos desde un dispositivo, todavía puede usar la seguridad y confiabilidad de IoT Hub.

> [!NOTE]
> El SDK de Python de IoT Hub solo admite actualmente la carga de archivos basados en caracteres como, por ejemplo, archivos **.txt**.

Al final de este tutorial, ejecutará la aplicación de consola de Python:

* **FileUpload.py**, que carga un archivo en el almacenamiento con el SDK de dispositivo de Python.

> [!NOTE]
> IoT Hub admite muchas plataformas de dispositivos y lenguajes (incluido C, .NET, JavaScript, Python y Java), mediante los SDK de dispositivo IoT de Azure. Consulte el [Centro para desarrolladores de IoT de Azure] para obtener instrucciones paso a paso sobre cómo conectar el dispositivo a Azure IoT Hub.

Para completar este tutorial, necesitará lo siguiente:

* [Python 2.x o 3.x][lnk-python-download]. Asegúrese de usar la instalación de 32 bits o 64 bits en función del programa de instalación. Cuando se le solicite durante la instalación, asegúrese de agregar Python a la variable de entorno específica de la plataforma. Si usa Python 2.x, puede que necesite [instalar o actualizar *pip*, el sistema de administración de paquetes de Python][lnk-install-pip].
* Si utiliza Windows OS, el [paquete redistribuible de Visual C++][lnk-visual-c-redist] permitirá el uso de archivos DLL nativos de Python.
* Una cuenta de Azure activa. (En caso de no tener ninguna, puede crear una [cuenta gratuita](http://azure.microsoft.com/pricing/free-trial/) en tan solo unos minutos).


[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]


## <a name="upload-a-file-from-a-device-app"></a>Carga de un archivo desde una aplicación de dispositivo

En esta sección, creará la aplicación para dispositivos para cargar un archivo en IoT Hub.

1. En el símbolo del sistema, ejecute el siguiente comando para instalar el paquete **azure-iothub-device-client**:

    ```cmd/sh
    pip install azure-iothub-device-client
    ```

1. Con un editor de texto, cree un archivo **FileUpload.py** en la carpeta de trabajo.

1. Agregue las siguientes instrucciones y variables `import` al principio del archivo **FileUpload.py**. Reemplace `deviceConnectionString` por la cadena de conexión del dispositivo de IoT Hub:

    ```python
    import time
    import sys
    import iothub_client
    import os
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError

    CONNECTION_STRING = "{deviceConnectionString}"
    PROTOCOL = IoTHubTransportProvider.HTTP

    FILENAME = 'sample.txt'
    ```

1. Cree una devolución de llamada para la función **upload_blob**:

    ```python
    def blob_upload_conf_callback(result, user_context):
        if str(result) == 'OK':
            print ( "...file uploaded successfully." )
        else:
            print ( "...file upload callback returned: " + str(result) )
    ```

1. Agregue el siguiente código para conectar el cliente y cargar el archivo. Incluya también la rutina `main`:

    ```python
    def iothub_file_upload_sample_run():
        try:
            print ( "IoT Hub file upload sample, press Ctrl-C to exit" )
        
            client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

            client.upload_blob_async(FILENAME, FILENAME, os.path.getsize(FILENAME), blob_upload_conf_callback, 0)
        
            print ( "" )
            print ( "File upload initiated..." )
        
            while True:
                time.sleep(30)

        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )
        except:
            print ( "generic error" )
        
    if __name__ == '__main__':
        print ( "Simulating a file upload using the Azure IoT Hub Device SDK for Python" )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_file_upload_sample_run()
    ```

1. Guarde y cierre el archivo **UploadFile.py**.

1. Copie un archivo de texto de ejemplo en la carpeta de trabajo y cambie su nombre a `sample.txt`.

    > [!NOTE]
    > El SDK de Python de IoT Hub solo admite actualmente la carga de archivos basados en caracteres como, por ejemplo, archivos **.txt**.


## <a name="run-the-application"></a>Ejecución de la aplicación

Ahora está preparado para ejecutar la aplicación.

1. En un símbolo del sistema de la carpeta de trabajo, ejecute el siguiente comando:

    ```cmd/sh
    python FileUpload.py
    ```

1. En la captura de pantalla siguiente se muestra la salida de la aplicación **FileUpload**:

    ![Salida de la aplicación simulated-device](./media/iot-hub-python-python-file-upload/1.png)

1. Puede usar el portal para ver el archivo cargado en el contenedor de almacenamiento que configuró:

    ![Archivo cargado](./media/iot-hub-python-python-file-upload/2.png)


## <a name="next-steps"></a>Pasos siguientes

En este tutorial ha aprendido a usar la funcionalidad de carga de archivos de IoT Hub para simplificar la carga de archivos desde los dispositivos. Puede continuar explorando las características y los escenarios del centro de IoT con los siguientes artículos:

* [Creación de un centro de IoT mediante programación][lnk-create-hub]
* [Introducción al SDK de C][lnk-c-sdk]
* [SDK de IoT de Azure][lnk-sdks]

<!-- Links -->
[Centro para desarrolladores de IoT de Azure]: http://azure.microsoft.com/develop/iot

[lnk-create-hub]: iot-hub-rm-template-powershell.md
[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/