---
title: "Creación de un módulo de Azure IoT Edge con C# | Microsoft Docs"
description: "Este tutorial muestra cómo escribir un módulo convertidor de datos BLE usando los paquetes de NuGet de Azure Iot Edge más recientes, Visual Studio Code y C#."
services: iot-hub
author: jeffreyCline
manager: timlt
keywords: "azure, iot, tutorial, módulo, nuget, vscode, csharp, edge"
ms.service: iot-hub
ms.devlang: csharp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2017
ms.author: jcline
ms.openlocfilehash: 7175ffc8de2c043593d61143b402484d33e4a8cc
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="create-an-azure-iot-edge-module-with-cx23"></a>Creación de un módulo de Azure IoT Edge con C&#x23;

Este tutorial muestra cómo crear un módulo para `Azure IoT Edge` con `Visual Studio Code` y `C#`.

En este tutorial, se tratará la configuración del entorno y cómo escribir un módulo convertidor de datos [BLE](https://en.wikipedia.org/wiki/Bluetooth_Low_Energy) usando la versión más reciente de los paquetes `Azure IoT Edge NuGet`. 

>[!NOTE]
Este tutorial utiliza el `.NET Core SDK`, que admite la compatibilidad multiplataforma. El tutorial siguiente se escribe utilizando el sistema operativo `Windows 10`. Algunos de los comandos en este tutorial pueden ser diferentes en función de su `development environment`. 

## <a name="prerequisites"></a>Requisitos previos

En esta sección, se configura el entorno para el desarrollo de un módulo de `Azure IoT Edge`. Se aplica a los sistemas operativos **Windows de 64 bits** y **Linux de 64 bits (Ubuntu y Debian 8)**.

Se requiere el software siguiente:

- [Cliente de GIT](https://git-scm.com/downloads)
- [SDK de .NET Core](https://www.microsoft.com/net/core#windowscmd)
- [código de Visual Studio](https://code.visualstudio.com/)

No es necesario clonar el repositorio para este ejemplo, sin embargo, todo el código de ejemplo que se utiliza en este tutorial se encuentra en el repositorio siguiente:

- `git clone https://github.com/Azure-Samples/iot-edge-samples.git`.
- `cd iot-edge-samples/dotnetcore/simulated_ble`

## <a name="getting-started"></a>Introducción

1. Instalar `.NET Core SDK`.
2. Instalar `Visual Studio Code` y `C# extension` desde Visual Studio Code Marketplace.

Vea este [vídeo tutorial rápido](https://channel9.msdn.com/Blogs/dotnet/Get-started-VSCode-Csharp-NET-Core-Windows) acerca de cómo empezar a usar `Visual Studio Code` y `.NET Core SDK`.

## <a name="creating-the-azure-iot-edge-converter-module"></a>Creación de un módulo convertidor de Azure IoT Edge

1. Inicializar un nuevo proyecto de biblioteca de clase de C# `.NET Core`:
    - Abra una ventana de símbolo del sistema (`Windows + R` -> `cmd` -> `enter`).
    - Navegue hasta la carpeta donde desea crear el proyecto `C#`.
    - Escriba **dotnet new classlib -o IoTEdgeConverterModule -f netstandard1.3**. 
    - Este comando crea una clase vacía denominada `Class1.cs` en el directorio de proyectos.
2. Navegue hasta la carpeta donde se acaba de crear el proyecto de biblioteca de clases escribiendo **cd IoTEdgeConverterModule**.
3. Abra el proyecto en `Visual Studio Code` escribiendo **code .**.
4. Una vez se abre el proyecto en `Visual Studio Code`, haga clic en **IoTEdgeConverterModule.csproj** para abrir el archivo como se muestra en la siguiente imagen:

    ![Ventana de edición de Visual Studio Code](media/iot-hub-iot-edge-create-module/vscode-edit-csproj.png)

5. Inserte el blob `XML` que se muestra en el siguiente fragmento de código entre la etiqueta de cierre de `PropertyGroup` y la etiqueta de cierre de `Project`; la línea seis en la imagen anterior y guarde el archivo presionando `Ctrl`  +  `S`.

   ```xml
     <ItemGroup>
       <PackageReference Include="Microsoft.Azure.Devices.Gateway.Module.NetStandard" Version="1.0.5" />
       <PackageReference Include="System.Threading.Thread" Version="4.3.0" />
       <PackageReference Include="Newtonsoft.Json" Version="10.0.2" />
     </ItemGroup> 
   ```

6. Una vez guardado el archivo `.csproj`, `Visual Studio Code` debe solicitarle mediante un cuadro de diálogo `unresolved dependencies` tal como se muestra en la siguiente imagen: 

    ![Cuadro de diálogo de restauración de dependencias de Visual Studio Code](media/iot-hub-iot-edge-create-module/vscode-restore.png)

    a) Haga clic en `Restore` para restaurar todas las referencias en el archivo del proyecto `.csproj` incluida la recién agregada `PackageReferences`. 

    b) `Visual Studio Code` crea automáticamente el archivo `project.assets.json` en la carpeta `obj` del proyecto. Este archivo contiene información sobre las dependencias del proyecto para agilizar las restauraciones posteriores.
 
    >[!NOTE]
    `.NET Core Tools` ahora, están basadas en MSBuild. Lo que significa que se crea un archivo de proyecto `.csproj` en lugar de un `project.json`.

    - Si `Visual Studio Code` no le solicita su confirmación, puede hacerse manualmente. Abra la ventana de terminal integrada de `Visual Studio Code` presionando `Ctrl`  +  `backtick` o mediante los menús `View`  ->  `Integrated Terminal`.
    - En la ventana `Integrated Terminal`, escriba **dotnet restore**.
    
7. Cambie el nombre del archivo `Class1.cs` a `BleConverterModule.cs`. 

    a) Para cambiar el nombre del archivo haga clic en el archivo y, a continuación, presione la tecla `F2`.
    
    b) Escriba el nuevo nombre **BleConverterModule**, tal como se muestra en la siguiente imagen:

    ![Cambio de nombre de una clase en Visual Studio Code](media/iot-hub-iot-edge-create-module/vscode-rename.png)

8. Reemplace el código existente en el archivo `BleConverterModule.cs` copiando y pegando el siguiente fragmento de código en su archivo `BleConverterModule.cs`.

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Globalization;
   using System.Linq;
   using System.Text;
   using System.Threading;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Gateway;
   using Newtonsoft.Json;
   
   namespace IoTEdgeConverterModule
   {
       public class BleConverterModule : IGatewayModule, IGatewayModuleStart
       {
           private Broker broker;
           private string configuration;
           private int messageCount;

           public void Create(Broker broker, byte[] configuration)
           {
               this.broker = broker;
               this.configuration = System.Text.Encoding.UTF8.GetString(configuration);
           }
   
           public void Start()
           {
           }

           public void Destroy()
           {
           }

           public void Receive(Message received_message)
           {
               string recMsg = Encoding.UTF8.GetString(received_message.Content, 0, received_message.Content.Length);
               BleData receivedData = JsonConvert.DeserializeObject<BleData>(recMsg);

               float temperature = float.Parse(receivedData.Temperature, CultureInfo.InvariantCulture.NumberFormat); 
               Dictionary<string, string> receivedProperties = received_message.Properties;
            
               Dictionary<string, string> properties = new Dictionary<string, string>();
               properties.Add("source", receivedProperties["source"]);
               properties.Add("macAddress", receivedProperties["macAddress"]);
               properties.Add("temperatureAlert", temperature > 30 ? "true" : "false");
   
               String content = String.Format("{0} \"deviceId\": \"Intel NUC Gateway\", \"messageId\": {1}, \"temperature\": {2} {3}", "{", ++this.messageCount, temperature, "}");
               Message messageToPublish = new Message(content, properties);
   
               this.broker.Publish(messageToPublish);
           }
       }
   }
   ```

9. Guarde el archivo presionando `Ctrl`  +  `S`.

10. Cree un nuevo archivo denominado `Untitled-1` presionando las teclas `Ctrl`  +  `N` tal como se muestra en la siguiente imagen:

    ![Nuevo archivo en Visual Studio Code](media/iot-hub-iot-edge-create-module/vscode-new-file.png)

11. Para deserializar el objeto `JSON` que se recibe desde el dispositivo simulado `BLE`, copie el código siguiente en la ventana del editor de código del archivo `Untitled-1`. 

   ```csharp
   using System;
   using Newtonsoft.Json;

   namespace IoTEdgeConverterModule
   {
       public class BleData
       {
           [JsonProperty(PropertyName = "temperature")]
           public string Temperature { get; set; }
       }
   }
   ```

12. Guarde el archivo como `BleData.cs` presionando las teclas `Ctrl`  +  `Shift`  +  `S`.
    - En el cuadro de diálogo Guardar como, en el menú desplegable `Save as Type`, seleccione `C# (*.cs;*.csx)` tal como se muestra en la siguiente imagen:

    ![Cuadro de diálogo Guardar como de Visual Studio Code](media/iot-hub-iot-edge-create-module/vscode-save-as.png)

13. Cree un nuevo archivo denominado `Untitled-1` presionando las teclas `Ctrl`  +  `N`.

14. Copie y pegue el siguiente fragmento de código en el archivo `Untitled-1`. Esta clase es un módulo de `Azure IoT Edge` que se utiliza para dar salida a los datos recibidos desde `BleConverterModule`.

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Linq;
   using System.Text;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Gateway;
   using Newtonsoft.Json;
   
   namespace PrinterModule
   {
       public class DotNetPrinterModule : IGatewayModule
       {
           private string configuration;
           public void Create(Broker broker, byte[] configuration)
           {
               this.configuration = System.Text.Encoding.UTF8.GetString(configuration);
           }
   
           public void Destroy()
           {
           }
   
           public void Receive(Message received_message)
           {
               string recMsg = System.Text.Encoding.UTF8.GetString(received_message.Content, 0, received_message.Content.Length);
               Dictionary<string, string> receivedProperties = received_message.Properties;
               
               BleConverterData receivedData = JsonConvert.DeserializeObject<BleConverterData>(recMsg);
   
               Console.WriteLine();
               Console.WriteLine("Module           : [DotNetPrinterModule]");
               Console.WriteLine("received_message : {0}", recMsg);
   
               if(received_message.Properties["source"] == "bleTelemetry")
               {
                   Console.WriteLine("Source           : {0}", receivedProperties["source"]);
                   Console.WriteLine("MAC address      : {0}", receivedProperties["macAddress"]);
                   Console.WriteLine("Temperature Alert: {0}", receivedProperties["temperatureAlert"]);
                   Console.WriteLine("Deserialized Obj : [BleConverterData]");
                   Console.WriteLine("  DeviceId       : {0}", receivedData.DeviceId);
                   Console.WriteLine("  MessageId      : {0}", receivedData.MessageId);
                   Console.WriteLine("  Temperature    : {0}", receivedData.Temperature);
               }
   
               Console.WriteLine();
           }
       }
   }
   ```

15. Guarde el archivo como `DotNetPrinterModule.cs` presionando `Ctrl` + `Shift` + `S`.
    - En el cuadro de diálogo Guardar como, en el menú desplegable `Save as Type`, seleccione `C# (*.cs;*.csx)`.

16. Cree un nuevo archivo denominado `Untitled-1` presionando las teclas `Ctrl`  +  `N`.

17. Para deserializar el objeto `JSON` que se recibe desde el módulo `BleConverterModule`, copie y pegue el siguiente fragmento de código en el archivo `Untitled-1`. 

   ```csharp
   using System;
   using Newtonsoft.Json;

   namespace PrinterModule
   {
       public class BleConverterData
       {
           [JsonProperty(PropertyName = "deviceId")]
           public string DeviceId { get; set; }
   
           [JsonProperty(PropertyName = "messageId")]
           public string MessageId { get; set; }
   
           [JsonProperty(PropertyName = "temperature")]
           public string Temperature { get; set; }
       }
   }
   ```

18. Guarde el archivo como `BleConverterData.cs` presionando `Ctrl` + `Shift` + `S`.
    - En el cuadro de diálogo Guardar como, en el menú desplegable `Save as Type`, seleccione `C# (*.cs;*.csx)`.

19. Cree un nuevo archivo denominado `Untitled-1` presionando las teclas `Ctrl`  +  `N`.

20. Copie y pegue el siguiente fragmento de código en el archivo `Untitled-1`.

   ```json
   {
       "loaders": [
           {
               "type": "dotnetcore",
               "name": "dotnetcore",
               "configuration": {
                   "binding.path": "dotnetcore.dll",
                   "binding.coreclrpath": "C:\\Program Files\\dotnet\\shared\\Microsoft.NETCore.App\\1.1.1\\coreclr.dll",
                   "binding.trustedplatformassemblieslocation": "C:\\Program Files\\dotnet\\shared\\Microsoft.NETCore.App\\1.1.1\\"
               }
           }
       ],
          "modules": [
           {
               "name": "simulated_device",
               "loader": {
                   "name": "native",
                   "entrypoint": {
                       "module.path": "simulated_device.dll"
                   }
               },
               "args": {
                   "macAddress": "01:02:03:03:02:01",
                   "messagePeriod": 500
               }
           },
           {
               "name": "ble_converter_module",
               "loader": {
                   "name": "dotnetcore",
                   "entrypoint": {
                       "assembly.name": "IoTEdgeConverterModule",
                       "entry.type": "IoTEdgeConverterModule.BleConverterModule"
                   }
               },
               "args": ""
           },
           {
               "name": "printer_module",
               "loader": {
                   "name": "dotnetcore",
                   "entrypoint": {
                       "assembly.name": "IoTEdgeConverterModule",
                       "entry.type": "PrinterModule.DotNetPrinterModule"
                   }
               },
               "args": ""
           }
       ],
       "links": [
           {
               "source": "simulated_device", "sink": "ble_converter_module"
           },
           {
               "source": "ble_converter_module", "sink": "printer_module"
           }
   ]
   }
   ```

21. Guarde el archivo como `gw-config.json` presionando `Ctrl` + `Shift` + `S`.
    - En el cuadro de diálogo Guardar como, en el menú desplegable `Save as Type`, seleccione `JSON (*.json;*.bowerrc;*.jshintrc;*.jscsrc;*.eslintrc;*.babelrc;*webmanifest)`.

22. Para habilitar la copia del archivo de configuración en el directorio de salida, actualice `IoTEdgeConverterModule.csproj` con el objeto blob XML siguiente:

   ```xml
     <ItemGroup>
       <None Update="gw-config.json" CopyToOutputDirectory="PreserveNewest" />
     </ItemGroup>
   ```
    
   - El archivo `IoTEdgeConverterModule.csproj` actualizado debería ser similar a la siguiente imagen:

    ![Archivo .csproj actualizado en Visual Studio Code](media/iot-hub-iot-edge-create-module/vscode-update-csproj.png)

23. Cree un nuevo archivo denominado `Untitled-1` presionando las teclas `Ctrl`  +  `N`.

24. Copie y pegue el siguiente fragmento de código en el archivo `Untitled-1`.

   ```powershell
   Copy-Item -Path $env:userprofile\.nuget\packages\microsoft.azure.devices.gateway.native.windows.x64\1.1.3\runtimes\win-x64\native\* -Destination .\bin\Debug\netstandard1.3
   Copy-Item -Path $env:userprofile\.nuget\packages\system.runtime.serialization.formatters\4.3.0\lib\netstandard1.4\* -Destination .\bin\Debug\netstandard1.3
   Copy-Item -Path $env:userprofile\.nuget\packages\system.runtime.serialization.primitives\4.3.0\lib\netstandard1.3\* -Destination .\bin\Debug\netstandard1.3
   Copy-Item -Path $env:userprofile\.nuget\packages\newtonsoft.json\10.0.2\lib\netstandard1.3\* -Destination .\bin\Debug\netstandard1.3
   Copy-Item -Path $env:userprofile\.nuget\packages\system.componentmodel.typeconverter\4.3.0\lib\netstandard1.5\* -Destination .\bin\Debug\netstandard1.3
   Copy-Item -Path $env:userprofile\.nuget\packages\system.collections.nongeneric\4.3.0\lib\netstandard1.3\* -Destination .\bin\Debug\netstandard1.3
   Copy-Item -Path $env:userprofile\.nuget\packages\system.collections.specialized\4.3.0\lib\netstandard1.3\* -Destination .\bin\Debug\netstandard1.3
   ```

25. Guarde el archivo como `binplace.ps1` presionando `Ctrl` + `Shift` + `S`.
    - En el cuadro de diálogo Guardar como, en el menú desplegable `Save as Type`, seleccione `PowerShell (*.ps1;*.psm1;*.psd1;*.pssc;*.psrc)`.

26. Compile el proyecto presionando las teclas `Ctrl` + `Shift` + `B`. Al compilar el proyecto por primera vez, `Visual Studio Code` muestra un cuadro de diálogo `No build task defined.` tal como se muestra en la siguiente imagen:

    ![Cuadro de diálogo Tarea de compilación de Visual Studio Code](media/iot-hub-iot-edge-create-module/vscode-build-task.png)

    a) Haga clic en el botón `Configure Build Task`.

    b) En el menú desplegable del cuadro de diálogo `Select a Task Runner`. Seleccione `.NET Core` tal como se muestra en la siguiente imagen: 

    ![Cuadro de diálogo seleccionar una tarea de Visual Studio Code](media/iot-hub-iot-edge-create-module/vscode-build-task-runner.png)

    c) Si hace clic en el elemento `.NET Core`, se crea el archivo `tasks.json` en el directorio `.vscode` y se abre el archivo en la ventana `code editor`. No es necesario modificar este archivo, puede cerrar la pestaña.

27.  Abra la ventana de terminal integrada de `Visual Studio Code` presionando las teclas `Ctrl`  +  `backtick` o mediante los menús `View`  ->  `Integrated Terminal` y escriba **.\binplace.ps1** en el símbolo del sistema de `PowerShell`. Este comando copia todas las dependencias en el directorio de salida.

28. Navegue hasta el directorio de salida del proyecto en la ventana `Integrated Terminal` escribiendo **cd .\bin\Debug\netstandard1.3**.

29. Ejecute el proyecto de ejemplo escribiendo **. \gw.exe gw-config.json** en la ventana `Integrated Terminal`. 
    - Si ha seguido todos los pasos de este tutorial, ahora debe tener en ejecución el proyecto de ejemplo `Azure IoT Edge BLE Data Converter Module` tal como se muestra en la siguiente imagen:
    
        ![Ejemplo de dispositivo simulado en ejecución en Visual Studio Code](media/iot-hub-iot-edge-create-module/vscode-run.png)
    
    - Si desea finalizar la aplicación, presione la tecla `<Enter>`.

>[!IMPORTANT]
No se recomienda usar `Ctrl`  +  `C` para terminar la aplicación de puerta de enlace de `IoT Edge` (es decir, **gw.exe**). Esta acción puede hacer que el proceso finalice de forma anómala.

