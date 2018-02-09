## <a name="create-a-device-identity"></a>Creación de una identidad de dispositivo

En esta sección, creará una aplicación de consola .NET que crea una identidad de dispositivo en el registro de identidades de su instancia de IoT Hub. No se puede conectar un dispositivo a IoT Hub a menos que tenga una entrada en el registro de identidades. Para más información, consulte la sección sobre el registro de la identidad de la [guía para desarrolladores de IoT Hub][lnk-devguide-identity]. Cuando se ejecuta esta aplicación de consola, genera una clave y un identificador del dispositivo únicos. El dispositivo usa estos valores para identificarse cuando envía mensajes del dispositivo a la nube a IoT Hub. Los identificadores de dispositivos distinguen mayúsculas de minúsculas.

1. En Visual Studio, agregue un proyecto de escritorio clásico de Windows de Visual C# a una nueva solución mediante la plantilla de proyecto **Aplicación de consola (.NET Framework)**. Asegúrese de que la versión de .NET Framework sea 4.5.1 o una posterior. Llame **CreateDeviceIdentity** al proyecto e **IoTHubGetStarted** a la solución.

    ![Nuevo proyecto de escritorio clásico de Windows de Visual C#][10]

1. En el Explorador de soluciones, haga clic con el botón derecho en el proyecto **CreateDeviceIdentity** y, a continuación, seleccione **Administrar paquetes NuGet**.

1. En la ventana **Administrador de paquetes NuGet**, seleccione **Examinar**, busque **microsoft.azure.devices**, seleccione **Instalar** para instalar el paquete **Microsoft.Azure.Devices** y acepte los términos de uso. Este procedimiento permite descargar, instalar y agregar una referencia al [paquete NuGet del SDK de dispositivo de IoT de Azure][lnk-nuget-service-sdk] y sus dependencias.

    ![Ventana del Administrador de paquetes NuGet][11]

1. Agregue las siguientes instrucciones `using` al principio del archivo **Program.cs** :

    ```csharp
    using Microsoft.Azure.Devices;
    using Microsoft.Azure.Devices.Common.Exceptions;
    ```

1. Agregue los campos siguientes a la clase **Program** . Sustituya el valor de marcador de posición por la cadena de conexión de IoT Hub para el centro que creó en la sección anterior.

    ```csharp
    static RegistryManager registryManager;
    static string connectionString = "{iot hub connection string}";
    ```

1. Agregue el método siguiente a la clase **Program** :

    ```csharp
    private static async Task AddDeviceAsync()
    {
      string deviceId = "myFirstDevice";
      Device device;
      try
      {
          device = await registryManager.AddDeviceAsync(new Device(deviceId));
      }
      catch (DeviceAlreadyExistsException)
      {
          device = await registryManager.GetDeviceAsync(deviceId);
      }
      Console.WriteLine("Generated device key: {0}", device.Authentication.SymmetricKey.PrimaryKey);
    }
    ```

    Este método crea una identidad de dispositivo con el identificador **myFirstDevice**. (Si el identificador de dispositivo ya existe en el Registro de identidad, el código simplemente recupera la información existente del dispositivo). A continuación, la aplicación muestra la clave principal de esa identidad. Esta clave se usará en la aplicación de dispositivo simulado para conectarse a IoT Hub.
[!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

1. Por último, agregue las líneas siguientes al método **Main** :

    ```csharp
    registryManager = RegistryManager.CreateFromConnectionString(connectionString);
    AddDeviceAsync().Wait();
    Console.ReadLine();
    ```

1. Ejecute la aplicación y anote la clave del dispositivo.

    ![Clave de dispositivo generada por la aplicación][12]

> [!NOTE]
> El registro de identidades de IoT Hub solo almacena identidades de dispositivos para permitir el acceso seguro a IoT Hub. El registro de identidades almacena los identificadores y las claves de dispositivo para usarlas como credenciales de seguridad. El registro de identidades también almacena una marca de habilitado o deshabilitado de cada dispositivo que se puede usar para deshabilitar el acceso a dicho dispositivo. Si la aplicación necesita almacenar otros metadatos específicos del dispositivo, debe usar un almacén específico de la aplicación. Consulte la [guía para desarrolladores de IoT Hub][lnk-devguide-identity] para más información.

<!-- Images. -->
[10]: ./media/iot-hub-get-started-create-device-identity-csharp/create-identity-csharp1.png
[11]: ./media/iot-hub-get-started-create-device-identity-csharp/create-identity-csharp2.png
[12]: ./media/iot-hub-get-started-create-device-identity-csharp/create-identity-csharp3.png

<!-- Links -->
[lnk-devguide-identity]: ../articles/iot-hub/iot-hub-devguide-identity-registry.md
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
