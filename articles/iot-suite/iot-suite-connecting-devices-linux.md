<properties
   pageTitle="Conectar un dispositivo mediante C en Linux | Microsoft Azure"
   description="Se describe cómo conectar un dispositivo a la solución de supervisión remota preconfigurada del Conjunto de IoT de Azure mediante una aplicación creada en C que se ejecuta en Linux."
   services=""
   suite="iot-suite"
   documentationCenter="na"
   authors="dominicbetts"
   manager="timlt"
   editor=""/>

<tags
   ms.service="iot-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/14/2016"
   ms.author="dobett"/>


# Conectar el dispositivo a la solución preconfigurada de supervisión remota (Linux)

[AZURE.INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## Compilación y ejecución de una aplicación cliente de C de ejemplo (Linux)

En los siguientes procedimientos se explica cómo crear una aplicación cliente simple sencilla (escrita en C y creada y ejecutada en Ubuntu Linux) que se comunique con la solución preconfigurada de supervisión remota mediante un programa de C que se haya . Para completar estos pasos, necesita un dispositivo que ejecute la versión 15.04 o 15.10 de Ubuntu. Antes de continuar, instale los paquetes de requisitos previos en el dispositivo Ubuntu ejecutando el siguiente comando:

```
sudo apt-get install cmake gcc g++
```

## Instalación de las bibliotecas de cliente en el dispositivo

Las bibliotecas de cliente del Centro de IoT de Azure están disponibles como un paquete que puede instalar en su dispositivo Ubuntu ejecutando el comando **apt-get**. Complete los pasos siguientes para instalar el paquete que contiene la biblioteca del Centro de IoT y los archivos de encabezado de su máquina Ubuntu:

1. Agregue el repositorio AzureIoT a la máquina:

    ```
    sudo add-apt-repository ppa:aziotsdklinux/ppa-azureiot
    sudo apt-get update
    ```

2. Instale el paquete azure-iot-sdk-c-dev:

    ```
    sudo apt-get install -y azure-iot-sdk-c-dev
    ```

## Adición de código para especificar el comportamiento del dispositivo

En la máquina Ubuntu, cree una carpeta llamada "**remote\_monitoring**". En la carpeta **remote\_monitoring**, cree cuatro archivos: **main.c**, **remote\_monitoring.c**, **remote\_monitoring.h** y **CMakeLists.txt**.

Las bibliotecas de cliente del serializador del Centro de IoT utilizan un modelo para especificar el formato de los mensajes que el dispositivo envía al Centro de IoT y los comandos de este último a los que responde dicho dispositivo.

1. En un editor de texto, abra el archivo **remote\_monitoring.c**. Agregue las instrucciones siguientes `#include`:

    ```
    #include "iothubtransportamqp.h"
    #include "schemalib.h"
    #include "iothub_client.h"
    #include "serializer.h"
    #include "schemaserializer.h"
    #include "azure_c_shared_utility/threadapi.h"
    #include "azure_c_shared_utility/platform.h"
    ```

2. Agregue las siguientes declaraciones de variable después de las instrucciones `#include`. Sustituya los valores de marcador de posición [Device Id] y [Device Key] por valores para el dispositivo en el panel de la solución de supervisión remota. Utilice el nombre de host del Centro de IoT en el panel para sustituir [IoTHub Name]. Por ejemplo, si el nombre de host del Centro de IoT es **contoso.azure-devices.net**, sustituya [IoTHub Name] por **contoso**:

    ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "IoTHub Name]";
    static const char* hubSuffix = "azure-devices.net";
    ```

3. Agregue el código siguiente para definir el modelo que permite que el dispositivo se comunique con el Centro de IoT. Este modelo especifica que el dispositivo debe enviar datos de temperatura, temperatura externa, humedad y un id. de dispositivo como telemetría. El dispositivo también envía metadatos sobre sí mismo al Centro de IoT, incluida la lista de comandos que admite el dispositivo. Este dispositivo responde a los comandos **SetTemperature** y **SetHumidity**:

    ```
    // Define the Model
    BEGIN_NAMESPACE(Contoso);

    DECLARE_STRUCT(SystemProperties,
      ascii_char_ptr, DeviceID,
      _Bool, Enabled
    );

    DECLARE_STRUCT(DeviceProperties,
      ascii_char_ptr, DeviceID,
      _Bool, HubEnabledState
    );

    DECLARE_MODEL(Thermostat,

      /* Event data (temperature, external temperature and humidity) */
      WITH_DATA(int, Temperature),
      WITH_DATA(int, ExternalTemperature),
      WITH_DATA(int, Humidity),
      WITH_DATA(ascii_char_ptr, DeviceId),

      /* Device Info - This is command metadata + some extra fields */
      WITH_DATA(ascii_char_ptr, ObjectType),
      WITH_DATA(_Bool, IsSimulatedDevice),
      WITH_DATA(ascii_char_ptr, Version),
      WITH_DATA(DeviceProperties, DeviceProperties),
      WITH_DATA(ascii_char_ptr_no_quotes, Commands),

      /* Commands implemented by the device */
      WITH_ACTION(SetTemperature, int, temperature),
      WITH_ACTION(SetHumidity, int, humidity)
    );

    END_NAMESPACE(Contoso);
    ```

### Adición de código para implementar el comportamiento del dispositivo

Agregue las funciones que se ejecutarán cuando el dispositivo reciba un comando del Centro de IoT y un código para enviar telemetría simulada a dicho Centro.

1. Agregue las siguientes funciones que se ejecutarán cuando el dispositivo recibe los comandos **SetTemperature** y **SetHumidity** definidos en el modelo:

    ```
    EXECUTE_COMMAND_RESULT SetTemperature(Thermostat* thermostat, int temperature)
    {
      (void)printf("Received temperature %d\r\n", temperature);
      thermostat->Temperature = temperature;
      return EXECUTE_COMMAND_SUCCESS;
    }

    EXECUTE_COMMAND_RESULT SetHumidity(Thermostat* thermostat, int humidity)
    {
      (void)printf("Received humidity %d\r\n", humidity);
      thermostat->Humidity = humidity;
      return EXECUTE_COMMAND_SUCCESS;
    }
    ```

2. Agregue la siguiente función que envía un mensaje al Centro de IoT:

    ```
    static void sendMessage(IOTHUB_CLIENT_HANDLE iotHubClientHandle, const unsigned char* buffer, size_t size)
    {
      IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromByteArray(buffer, size);
      if (messageHandle == NULL)
      {
        printf("unable to create a new IoTHubMessage\r\n");
      }
      else
      {
        if (IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, NULL, NULL) != IOTHUB_CLIENT_OK)
        {
          printf("failed to hand over the message to IoTHubClient");
        }
        else
        {
          printf("IoTHubClient accepted the message for delivery\r\n");
        }

        IoTHubMessage_Destroy(messageHandle);
      }
    free((void*)buffer);
    }
    ```

3. Agregue la siguiente función que enlaza la biblioteca de serialización del SDK:

    ```
    static IOTHUBMESSAGE_DISPOSITION_RESULT IoTHubMessage(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
    {
      IOTHUBMESSAGE_DISPOSITION_RESULT result;
      const unsigned char* buffer;
      size_t size;
      if (IoTHubMessage_GetByteArray(message, &buffer, &size) != IOTHUB_MESSAGE_OK)
      {
        printf("unable to IoTHubMessage_GetByteArray\r\n");
        result = EXECUTE_COMMAND_ERROR;
      }
      else
      {
        /*buffer is not zero terminated*/
        char* temp = malloc(size + 1);
        if (temp == NULL)
        {
          printf("failed to malloc\r\n");
          result = EXECUTE_COMMAND_ERROR;
        }
        else
        {
          memcpy(temp, buffer, size);
          temp[size] = '\0';
          EXECUTE_COMMAND_RESULT executeCommandResult = EXECUTE_COMMAND(userContextCallback, temp);
          result =
            (executeCommandResult == EXECUTE_COMMAND_ERROR) ? IOTHUBMESSAGE_ABANDONED :
            (executeCommandResult == EXECUTE_COMMAND_SUCCESS) ? IOTHUBMESSAGE_ACCEPTED :
            IOTHUBMESSAGE_REJECTED;
          free(temp);
        }
      }
      return result;
    }
    ```

4. Agregue la siguiente función para conectarse al Centro de IoT, enviar y recibir mensajes, y desconectarse del Centro. Observe cómo el dispositivo envía metadatos sobre sí mismo, incluidos los comandos que admite, al Centro de IoT en cuanto que se conecta. Esto permite a la solución actualizar el estado del dispositivo a **En ejecución** en el panel:

    ```
    void remote_monitoring_run(void)
    {
      if (platform_init() != 0)
      {
        printf("Failed to initialize the platform.\r\n");
      }
      else
      {
        if (serializer_init(NULL) != SERIALIZER_OK)
        {
          printf("Failed on serializer_init\r\n");
        }
        else
        {
          IOTHUB_CLIENT_CONFIG config;
          IOTHUB_CLIENT_HANDLE iotHubClientHandle;

          config.deviceId = deviceId;
          config.deviceKey = deviceKey;
          config.deviceSasToken = NULL;
          config.iotHubName = hubName;
          config.iotHubSuffix = hubSuffix;
          config.protocol = AMQP_Protocol;
          iotHubClientHandle = IoTHubClient_Create(&config);
          if (iotHubClientHandle == NULL)
          {
            (void)printf("Failed on IoTHubClient_CreateFromConnectionString\r\n");
          }
          else
          {
            Thermostat* thermostat = CREATE_MODEL_INSTANCE(Contoso, Thermostat);
            if (thermostat == NULL)
            {
              (void)printf("Failed on CREATE_MODEL_INSTANCE\r\n");
            }
            else
            {
              STRING_HANDLE commandsMetadata;

              if (IoTHubClient_SetMessageCallback(iotHubClientHandle, IoTHubMessage, thermostat) != IOTHUB_CLIENT_OK)
              {
                printf("unable to IoTHubClient_SetMessageCallback\r\n");
              }
              else
              {

                /* send the device info upon startup so that the cloud app knows
                   what commands are available and the fact that the device is up */
                thermostat->ObjectType = "DeviceInfo";
                thermostat->IsSimulatedDevice = false;
                thermostat->Version = "1.0";
                thermostat->DeviceProperties.HubEnabledState = true;
                thermostat->DeviceProperties.DeviceID = (char*)deviceId;

                commandsMetadata = STRING_new();
                if (commandsMetadata == NULL)
                {
                  (void)printf("Failed on creating string for commands metadata\r\n");
                }
                else
                {
                  /* Serialize the commands metadata as a JSON string before sending */
                  if (SchemaSerializer_SerializeCommandMetadata(GET_MODEL_HANDLE(Contoso, Thermostat), commandsMetadata) != SCHEMA_SERIALIZER_OK)
                  {
                    (void)printf("Failed serializing commands metadata\r\n");
                  }
                  else
                  {
                    unsigned char* buffer;
                    size_t bufferSize;
                    thermostat->Commands = (char*)STRING_c_str(commandsMetadata);

                    /* Here is the actual send of the Device Info */
                    if (SERIALIZE(&buffer, &bufferSize, thermostat->ObjectType, thermostat->Version, thermostat->IsSimulatedDevice, thermostat->DeviceProperties, thermostat->Commands) != IOT_AGENT_OK)
                    {
                      (void)printf("Failed serializing\r\n");
                    }
                    else
                    {
                      sendMessage(iotHubClientHandle, buffer, bufferSize);
                    }

                  }

                  STRING_delete(commandsMetadata);
                }

                thermostat->Temperature = 50;
                thermostat->ExternalTemperature = 55;
                thermostat->Humidity = 50;
                thermostat->DeviceId = (char*)deviceId;

                while (1)
                {
                  unsigned char*buffer;
                  size_t bufferSize;

                  (void)printf("Sending sensor value Temperature = %d, Humidity = %d\r\n", thermostat->Temperature, thermostat->Humidity);

                  if (SERIALIZE(&buffer, &bufferSize, thermostat->DeviceId, thermostat->Temperature, thermostat->Humidity, thermostat->ExternalTemperature) != IOT_AGENT_OK)
                  {
                    (void)printf("Failed sending sensor value\r\n");
                  }
                  else
                  {
                    sendMessage(iotHubClientHandle, buffer, bufferSize);
                  }

                  ThreadAPI_Sleep(1000);
                }
              }

              DESTROY_MODEL_INSTANCE(thermostat);
            }
            IoTHubClient_Destroy(iotHubClientHandle);
          }
          serializer_deinit();
        }
        platform_deinit();
      }
    }
    ```
    
    Como referencia, este es un mensaje de **información del dispositivo** de ejemplo enviado al Centro de IoT durante el inicio:

    ```
    {
      "ObjectType":"DeviceInfo",
      "Version":"1.0",
      "IsSimulatedDevice":false,
      "DeviceProperties":
      {
        "DeviceID":"mydevice01", "HubEnabledState":true
      }, 
      "Commands":
      [
        {"Name":"SetHumidity", "Parameters":[{"Name":"humidity","Type":"double"}]},
        { "Name":"SetTemperature", "Parameters":[{"Name":"temperature","Type":"double"}]}
      ]
    }
    ```
    
    Como referencia, este es un mensaje de **telemetría** de ejemplo enviado al Centro de IoT:

    ```
    {"DeviceId":"mydevice01", "Temperature":50, "Humidity":50, "ExternalTemperature":55}
    ```
    
    Como referencia, este es un **comando** de ejemplo recibido del Centro de IoT:
    
    ```
    {
      "Name":"SetHumidity",
      "MessageId":"2f3d3c75-3b77-4832-80ed-a5bb3e233391",
      "CreatedTime":"2016-03-11T15:09:44.2231295Z",
      "Parameters":{"humidity":23}
    }
    ```

### Adición de código para invocar la función remote\_monitoring\_run

En un editor de texto, abra el archivo **remote\_monitoring.h**. Agregue el siguiente código:

```
void remote_monitoring_run(void);
```

En un editor de texto, abra el archivo **main.c**. Agregue el siguiente código:

```
#include "remote_monitoring.h"

int main(void)
{
    remote_monitoring_run();

    return 0;
}
```

## Uso de CMake para compilar la aplicación cliente

En los pasos siguientes se describe cómo puede utilizar *CMake* para compilar la aplicación cliente.

1. En un editor de texto, abra el archivo **CMakeLists.txt** de la carpeta **remote\_monitoring**.

2. Agregue las siguientes instrucciones para definir cómo compilar la aplicación cliente:

    ```
    cmake_minimum_required(VERSION 2.8.11)

    set(AZUREIOT_INC_FOLDER ".." "/usr/include/azureiot")

    include_directories(${AZUREIOT_INC_FOLDER})

    set(sample_application_c_files
        ./remote_monitoring.c
        ./main.c
    )

    set(sample_application_h_files
        ./remote_monitoring.h
    )

    add_executable(sample_app ${sample_application_c_files} ${sample_application_h_files})

    target_link_libraries(sample_app
        serializer
        iothub_client
        iothub_client_amqp_transport
        aziotsharedutil
        uamqp
        pthread
        curl
        ssl
        crypto
    )
    ```

3. En la carpeta **remote\_monitoring**, cree una otra para almacenar los archivos *make* que genera CMake y vuelva a ejecutar los comandos **cmake** y **make**, tal y como se muestra a continuación:

    ```
    mkdir cmake
    cd cmake
    cmake ../
    make
    ```

4. Ejecute la aplicación cliente y envíe los datos telemetría al Centro de IoT:

    ```
    ./sample_app
    ```

[AZURE.INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

<!---HONumber=AcomDC_0720_2016-->