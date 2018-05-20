---
title: archivo de inclusión
description: archivo de inclusión
services: iot-hub
author: chrissie926
manager: timlt
ms.service: iot-hub
ms.topic: include
ms.date: 04/26/2018
ms.author: menchi
ms.custom: include file
ms.openlocfilehash: a94a68d238a731388d8b13bd962b0db1007c5ca4
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/03/2018
---
## <a name="create-a-module-identity"></a>Creación de una identidad de módulo

En esta sección se crea una aplicación de consola .NET que crea una identidad de dispositivo y otra de módulo en el registro de identidades de la instancia de IoT Hub. No se puede conectar un dispositivo o un módulo a IoT Hub a menos que tenga una entrada en el registro de identidades. Para más información, consulte la sección sobre el registro de la identidad de la [guía para desarrolladores de IoT Hub][lnk-devguide-identity]. Cuando se ejecuta esta aplicación de consola, genera una clave y un identificador únicos para el dispositivo y el módulo. El dispositivo y el módulo usan estos valores para identificarse al enviar mensajes del dispositivo a la nube a IoT Hub. Los identificadores distinguen mayúsculas de minúsculas.


1. **Cree un proyecto de Visual Studio**: en Visual Studio, agregue un proyecto de escritorio clásico de Windows de Visual C# a una solución nueva mediante la plantilla de proyecto **Aplicación de consola (.NET Framework)**. Asegúrese de que la versión de .NET Framework sea 4.6.1 o una posterior. Llame **CreateIdentities** al proyecto e **IoTHubGetStarted** a la solución.

    ![Creación de una solución de Visual Studio][11]

2. **Instale el SDK de servicio .NET de Azure IoT Hub V1.16.0-preview-001**: la identidad de módulo y el módulo gemelo se encuentran en versión preliminar pública. Solo está disponible en los SDK de servicio de la versión preliminar de IoT Hub. En Visual Studio, abra Herramientas > Administrador de paquetes NuGet > Administrar paquetes NuGet para la solución. Busque Microsoft.Azure.Devices. Asegúrese de que ha marcado la casilla de inclusión de la versión preliminar. Seleccione la versión 1.16.0-preview-001 e instálela. Ahora tendrá acceso a todas las características del módulo. 

    ![Instalación del SDK de servicio .NET de Azure IoT Hub V1.16.0-preview-001][10]

3. Agregue las siguientes instrucciones `using` al principio del archivo **Program.cs** :

    ```csharp
    using Microsoft.Azure.Devices;
    using Microsoft.Azure.Devices.Common.Exceptions;
    ```

4. Agregue los campos siguientes a la clase **Program** . Sustituya el valor de marcador de posición por la cadena de conexión de IoT Hub para el centro que creó en la sección anterior.

    ```csharp
    const string connectionString = "<replace_with_iothub_connection_string>";
    const string deviceID = "myFirstDevice";
    const string moduleID = "myFirstModule";
    ```

5. Agregue los métodos siguientes a la clase **Program**:

    ```csharp
    private static async Task AddDeviceAsync()
    {
        RegistryManager registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        Device device;

        try
        {
            device = await registryManager.AddDeviceAsync(new Device(deviceID));
        }
        catch (DeviceAlreadyExistsException)
        {
            device = await registryManager.GetDeviceAsync(deviceID);
            }

            Console.WriteLine("Generated device key: {0}", device.Authentication.SymmetricKey.PrimaryKey);
    }

    private static async Task AddModuleAsync()
    {
        RegistryManager registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        Module module;

        try
        {
            module = await registryManager.AddModuleAsync(new Module(deviceID, moduleID));
        }
        catch (ModuleAlreadyExistsException)
        {
            module = await registryManager.GetModuleAsync(deviceID, moduleID);
        }

        Console.WriteLine("Generated module key: {0}", module.Authentication.SymmetricKey.PrimaryKey);
    }
    ```

    El método AddDeviceAsync() crea una identidad de dispositivo con el identificador **myFirstDevice**. (Si el identificador de dispositivo ya existe en el Registro de identidad, el código simplemente recupera la información existente del dispositivo). A continuación, la aplicación muestra la clave principal de esa identidad. Esta clave se usará en la aplicación de dispositivo simulado para conectarse a IoT Hub.

    El método AddModuleAsync() crea una identidad de módulo con el identificador **myFirstModule** en el dispositivo **myFirstDevice**. (Si el identificador de módulo ya existe en el Registro de identidad, el código simplemente recupera la información existente del módulo). A continuación, la aplicación muestra la clave principal de esa identidad. Esta clave se usará en la aplicación de módulo simulado para conectarse a IoT Hub.

[!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

6. Ejecute la aplicación y anote la clave del dispositivo y del módulo.

> [!NOTE]
> El registro de identidades de IoT Hub solo almacena identidades de dispositivos y módulos para permitir el acceso seguro a IoT Hub. El registro de identidades almacena los identificadores y las claves de dispositivo para usarlas como credenciales de seguridad. El registro de identidades también almacena una marca de habilitado o deshabilitado de cada dispositivo que se puede usar para deshabilitar el acceso a dicho dispositivo. Si la aplicación necesita almacenar otros metadatos específicos del dispositivo, debe usar un almacén específico de la aplicación. No hay marcas de habilitado/deshabilitado para las identidades de módulo. Consulte la [guía para desarrolladores de IoT Hub][lnk-devguide-identity] para más información.

<!-- Images. -->
[10]: ./media/iot-hub-get-started-create-module-identity-csharp/install-sdk.png
[11]: ./media/iot-hub-get-started-create-module-identity-csharp/create-identities-csharp1.JPG

<!-- Links -->
[lnk-devguide-identity]: ../articles/iot-hub/iot-hub-devguide-identity-registry.md
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
