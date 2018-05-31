---
title: Integración continua e implementación continua de Azure IoT Edge | Microsoft Docs
description: Información general sobre la integración continua y la implementación continua de Azure IoT Edge
services: iot-Edge
documentationcenter: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 4/30/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 2f635a4c02dd8fd2b58598e53662d1a4d82ea611
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/01/2018
ms.locfileid: "32311745"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge---preview"></a>Integración continua e implementación continua en Azure IoT Edge (versión preliminar)
En este tutorial se muestra cómo puede usar las características de integración continua e implementación continua de Visual Studio Team Services (VSTS) y Microsoft Team Foundation Server (TFS) para compilar, probar e implementar aplicaciones de forma rápida y eficiente en su instancia de Azure IoT Edge. 

En este tutorial, aprenderá a:
> [!div class="checklist"]
> * Crear e insertar en el repositorio una solución de IoT Edge de ejemplo que contiene pruebas unitarias
> * Instalar la extensión de Azure IoT Edge para su VSTS
> * Configurar la integración continua (CI) para compilar la solución y ejecutar las pruebas unitarias
> * Configurar la implementación continua (CD) para implementar la solución y ver las respuestas

Tardará 30 minutos en realizar este tutorial.

![CI y CD](./media/how-to-ci-cd/cd.png)

## <a name="create-a-sample-azure-iot-edge-solution-using-visual-studio-code"></a>Creación de una solución de Azure IoT Edge de ejemplo mediante Visual Studio Code

En esta sección, creará una solución de IoT Edge de ejemplo que contendrá pruebas unitarias que puede ejecutar como parte del proceso de compilación. Antes de seguir las instrucciones de esta sección, complete los pasos que se indican en [Desarrollo de una solución de IoT Edge con varios módulos en Visual Studio Code](tutorial-multiple-modules-in-vscode.md).

1. En la paleta de comandos de VS Code, escriba y ejecute el comando **Edge: New IoT Edge solution**. A continuación, seleccione la carpeta del área de trabajo, proporcione el nombre de la solución (el nombre predeterminado es **EdgeSolution**) y cree un módulo de C# (**SampleModule**) como primer módulo de usuario en esta solución. También debe especificar el repositorio de imágenes de Docker para el primer módulo. El repositorio de imágenes predeterminado se basa en un registro de Docker local (`localhost:5000/filtermodule`). Deberá cambiarlo por Azure Container Registry(`<your container registry address>/filtermodule`) o Docker Hub para mejorar la integración continua.

    ![Configuración de ACR](./media/how-to-ci-cd/acr.png)

2. La ventana de Visual Studio Code cargará el área de trabajo de la solución de IoT Edge. Opcionalmente, puede escribir y ejecutar **Edge: Agregar módulo IoT Edge** para agregar más módulos. Hay una carpeta `modules`, una carpeta `.vscode` y un archivo de plantilla de manifiesto de implementación en la carpeta raíz. Todos los códigos del módulo de usuario serán subcarpetas en la carpeta `modules`. `deployment.template.json` es la plantilla del manifiesto de implementación. Algunos de los parámetros de este archivo se analizarán desde `module.json`, que existe en cada carpeta del módulo.

3. Ahora su solución de IoT Edge de ejemplo ya está lista. El módulo predeterminado de C# actúa como módulo de mensajes de canalización. En `deployment.template.json`, verá que esta solución contiene dos módulos. El mensaje se genera a partir del módulo `tempSensor` y se canaliza directamente mediante `FilterModule`, para finalmente enviarse a su centro de IoT. Reemplace el archivo entero **Program.cs** por el contenido siguiente. Para más información sobre este fragmento de código, puede consultar [Crear un proyecto de módulo IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-csharp-module#create-an-iot-edge-module-project).

    ```csharp
    namespace FilterModule
    {
        using System;
        using System.IO;
        using System.Runtime.InteropServices;
        using System.Runtime.Loader;
        using System.Security.Cryptography.X509Certificates;
        using System.Text;
        using System.Threading;
        using System.Threading.Tasks;
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Client.Transport.Mqtt;
        using System.Collections.Generic;     // for KeyValuePair<>
        using Microsoft.Azure.Devices.Shared; // for TwinCollection
        using Newtonsoft.Json;                // for JsonConvert

        public class MessageBody
        {
            public Machine machine { get; set; }
            public Ambient ambient { get; set; }
            public string timeCreated { get; set; }
        }
        public class Machine
        {
            public double temperature { get; set; }
            public double pressure { get; set; }
        }
        public class Ambient
        {
            public double temperature { get; set; }
            public int humidity { get; set; }
        }

        public class Program
        {
            static int counter;
            static int temperatureThreshold { get; set; } = 25;

            static void Main(string[] args)
            {
                // The Edge runtime gives us the connection string we need -- it is injected as an environment variable
                string connectionString = Environment.GetEnvironmentVariable("EdgeHubConnectionString");

                // Cert verification is not yet fully functional when using Windows OS for the container
                bool bypassCertVerification = RuntimeInformation.IsOSPlatform(OSPlatform.Windows);
                if (!bypassCertVerification) InstallCert();
                Init(connectionString, bypassCertVerification).Wait();

                // Wait until the app unloads or is cancelled
                var cts = new CancellationTokenSource();
                AssemblyLoadContext.Default.Unloading += (ctx) => cts.Cancel();
                Console.CancelKeyPress += (sender, cpe) => cts.Cancel();
                WhenCancelled(cts.Token).Wait();
            }

            /// <summary>
            /// Handles cleanup operations when app is cancelled or unloads
            /// </summary>
            public static Task WhenCancelled(CancellationToken cancellationToken)
            {
                var tcs = new TaskCompletionSource<bool>();
                cancellationToken.Register(s => ((TaskCompletionSource<bool>)s).SetResult(true), tcs);
                return tcs.Task;
            }

            /// <summary>
            /// Add certificate in local cert store for use by client for secure connection to IoT Edge runtime
            /// </summary>
            static void InstallCert()
            {
                string certPath = Environment.GetEnvironmentVariable("EdgeModuleCACertificateFile");
                if (string.IsNullOrWhiteSpace(certPath))
                {
                    // We cannot proceed further without a proper cert file
                    Console.WriteLine($"Missing path to certificate collection file: {certPath}");
                    throw new InvalidOperationException("Missing path to certificate file.");
                }
                else if (!File.Exists(certPath))
                {
                    // We cannot proceed further without a proper cert file
                    Console.WriteLine($"Missing path to certificate collection file: {certPath}");
                    throw new InvalidOperationException("Missing certificate file.");
                }
                X509Store store = new X509Store(StoreName.Root, StoreLocation.CurrentUser);
                store.Open(OpenFlags.ReadWrite);
                store.Add(new X509Certificate2(X509Certificate2.CreateFromCertFile(certPath)));
                Console.WriteLine("Added Cert: " + certPath);
                store.Close();
            }
            /// <summary>
            /// Initializes the DeviceClient and sets up the callback to receive
            /// messages containing temperature information
            /// </summary>
            static async Task Init(string connectionString, bool bypassCertVerification = false)
            {
                Console.WriteLine("Connection String {0}", connectionString);

                MqttTransportSettings mqttSetting = new MqttTransportSettings(TransportType.Mqtt_Tcp_Only);
                // During dev you might want to bypass the cert verification. It is highly recommended to verify certs systematically in production
                if (bypassCertVerification)
                {
                    mqttSetting.RemoteCertificateValidationCallback = (sender, certificate, chain, sslPolicyErrors) => true;
                }
                ITransportSettings[] settings = { mqttSetting };

                // Open a connection to the Edge runtime
                DeviceClient ioTHubModuleClient = DeviceClient.CreateFromConnectionString(connectionString, settings);
                await ioTHubModuleClient.OpenAsync();
                Console.WriteLine("IoT Hub module client initialized.");

                // Register callback to be called when a message is received by the module
                // await ioTHubModuleClient.SetImputMessageHandlerAsync("input1", PipeMessage, iotHubModuleClient);

                // Read TemperatureThreshold from Module Twin Desired Properties
                var moduleTwin = await ioTHubModuleClient.GetTwinAsync();
                var moduleTwinCollection = moduleTwin.Properties.Desired;
                try {
                    temperatureThreshold = moduleTwinCollection["TemperatureThreshold"];
                } catch(ArgumentOutOfRangeException) {
                    Console.WriteLine("Proerty TemperatureThreshold not exist");
                }

                // Attach callback for Twin desired properties updates
                await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(onDesiredPropertiesUpdate, null);

                // Register callback to be called when a message is received by the module
                await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
            }

            static Task onDesiredPropertiesUpdate(TwinCollection desiredProperties, object userContext)
            {
                try
                {
                    Console.WriteLine("Desired property change:");
                    Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

                    if (desiredProperties["TemperatureThreshold"] != null)
                        temperatureThreshold = desiredProperties["TemperatureThreshold"];

                }
                catch (AggregateException ex)
                {
                    foreach (Exception exception in ex.InnerExceptions)
                    {
                        Console.WriteLine();
                        Console.WriteLine("Error when receiving desired property: {0}", exception);
                    }
                }
                catch (Exception ex)
                {
                    Console.WriteLine();
                    Console.WriteLine("Error when receiving desired property: {0}", ex.Message);
                }
                return Task.CompletedTask;
            }

            public static Message filter(Message message)
            {
                var counterValue = Interlocked.Increment(ref counter);

                var messageBytes = message.GetBytes();
                var messageString = Encoding.UTF8.GetString(messageBytes);
                Console.WriteLine($"Received message {counterValue}: [{messageString}]");

                // Get message body
                var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

                if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
                {
                    Console.WriteLine($"Machine temperature {messageBody.machine.temperature} " +
                        $"exceeds threshold {temperatureThreshold}");
                    var filteredMessage = new Message(messageBytes);
                    foreach (KeyValuePair<string, string> prop in message.Properties)
                    {
                        filteredMessage.Properties.Add(prop.Key, prop.Value);
                    }

                    filteredMessage.Properties.Add("MessageType", "Alert");
                    return filteredMessage;
                }
                return null;
            }

            static async Task<MessageResponse> FilterMessages(Message message, object userContext)
            {
                try
                {
                    DeviceClient deviceClient = (DeviceClient)userContext;

                    var filteredMessage = filter(message);
                    if (filteredMessage != null)
                    {
                        await deviceClient.SendEventAsync("output1", filteredMessage);
                    }

                    // Indicate that the message treatment is completed
                    return MessageResponse.Completed;
                }
                catch (AggregateException ex)
                {
                    foreach (Exception exception in ex.InnerExceptions)
                    {
                        Console.WriteLine();
                        Console.WriteLine("Error in sample: {0}", exception);
                    }
                    // Indicate that the message treatment is not completed
                    var deviceClient = (DeviceClient)userContext;
                    return MessageResponse.Abandoned;
                }
                catch (Exception ex)
                {
                    Console.WriteLine();
                    Console.WriteLine("Error in sample: {0}", ex.Message);
                    // Indicate that the message treatment is not completed
                    DeviceClient deviceClient = (DeviceClient)userContext;
                    return MessageResponse.Abandoned;
                }
            }
        }
    }
    ```

4. Cree un proyecto de prueba unitaria de .Net Core. En el Explorador de archivos de VS Code, cree una nueva carpeta **tests\FilterModuleTest** en el área de trabajo. A continuación, en el terminal integrado de VS Code (**Ctrl + '**), ejecute los siguientes comandos para crear un proyecto de prueba xunit y agregar la referencia al proyecto **FilterModule**.

    ```cmd
    cd tests\FilterModuleTest
    dotnet new xunit
    dotnet add reference ../../modules/FilterModule/FilterModule.csproj
    ```

    ![Estructura de carpetas](./media/how-to-ci-cd/add-test-project.png)

5. En la carpeta **FilterModuleTest**, actualice el nombre de archivo de **UnitTest1.cs** a **FilterModuleTest.cs**. Seleccione y abra **FilterModuleTest.cs**, y reemplace todo el código por el siguiente fragmento de código, que contiene las pruebas unitarias para el proyecto FilterModule.

    ```csharp
    using Xunit;
    using FilterModule;
    using Newtonsoft.Json;
    using System;
    using System.IO;
    using System.Runtime.InteropServices;
    using System.Runtime.Loader;
    using System.Security.Cryptography.X509Certificates;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Client.Transport.Mqtt;

    namespace FilterModuleTest
    {
        public class FilterModuleTest
        {
            [Fact]
            public void filterLessThanThresholdTest()
            {
                var source = createMessage(25 - 1);
                var result = Program.filter(source);
                Assert.True(result == null);
            }

            [Fact]
            public void filterMoreThanThresholdAlertPropertyTest()
            {
                var source = createMessage(25 + 1);
                var result = Program.filter(source);
                Assert.True(result.Properties["MessageType"] == "Alert");
            }

            [Fact]
            public void filterMoreThanThresholdCopyPropertyTest()
            {
                var source = createMessage(25 + 1);
                source.Properties.Add("customTestKey", "customTestValue");
                var result = Program.filter(source);
                Assert.True(result.Properties["customTestKey"] == "customTestValue");
            }

            private Message createMessage(int temperature)
            {
                var messageBody = createMessageBody(temperature);
                var messageString = JsonConvert.SerializeObject(messageBody);
                var messageBytes = Encoding.UTF8.GetBytes(messageString);
                return new Message(messageBytes);
            }

            private MessageBody createMessageBody(int temperature)
            {
                var messageBody = new MessageBody
                {
                    machine = new Machine
                    {
                        temperature = temperature,
                        pressure = 0
                    },
                    ambient = new Ambient
                    {
                        temperature = 0,
                        humidity = 0
                    },
                    timeCreated = string.Format("{0:O}", DateTime.Now)
                };

                return messageBody;
            }
        }
    }
    ```

6. En el terminal integrado, puede escribir los siguientes comandos para ejecutar pruebas unitarias localmente. 
    ```cmd
    dotnet test
    ```

    ![Prueba unitaria](./media/how-to-ci-cd/unit-test.png)

7. Guarde estos proyectos e insértelos en el repositorio de VSTS o TFS.
    

> [!NOTE]
> Para más información sobre el uso de repositorios de VSTS, consulte [Share your code with Visual Studio and VSTS Git](https://docs.microsoft.com/vsts/git/share-your-code-in-git-vs?view=vsts) (Compartir el código con Visual Studio y Git de VSTS).


## <a name="configure-continuous-integration"></a>Configuración de la integración continua
En esta sección, creará una definición de compilación que está configurada para ejecutarse automáticamente al insertar en el repositorio los cambios realizados en la solución de IoT Edge de ejemplo, y ejecutará automáticamente las pruebas unitarias que contiene.

1. Inicie sesión en su cuenta de VSTS (**https://**_su cuenta_**. visualstudio.com**) y abra el proyecto donde insertó en el repositorio la aplicación de ejemplo.

    ![Inserción del código en el repositorio](./media/how-to-ci-cd/init-project.png)

1. Visite [Azure IoT Edge For VSTS](https://marketplace.visualstudio.com/items?itemName=vsc-iot.iot-edge-build-deploy) en VSTS Marketplace. Haga clic en **Get it free** y siga los pasos del asistente para instalar esta extensión en su cuenta de VSTS o descargarla en su TFS.

    ![Instalación de la extensión](./media/how-to-ci-cd/install-extension.png)

1. En su instancia de VSTS, abra el centro **Compilación &amp; Versión** y, en la pestaña **Compilaciones**, elija **+Nueva definición**. O bien, si ya tiene definiciones de compilación, elija el botón **+Nuevo**. 

    ![Nueva compilación](./media/how-to-ci-cd/add-new-build.png)

1. Si se le solicita, seleccione el tipo de origen **Git de VSTS** y, luego, seleccione el proyecto, el repositorio y la rama donde está ubicado el código. Elija **Continuar**.

    ![Selección de Git de VSTS](./media/how-to-ci-cd/select-vsts-git.png)

1. En la ventana **Seleccione una plantilla** , elija **Proceso vacío** para comenzar con un proceso vacío.

    ![Iniciar vacío](./media/how-to-ci-cd/start-with-empty.png)

1. Haga clic en **+** en el lado derecho de **Phase 1** (Fase 1) para agregar una tarea a la fase. A continuación, busque y seleccione **.Net Core** y haga clic en **Agregar** para agregar esta tarea a la fase.

    ![Prueba de dotnet](./media/how-to-ci-cd/add-dot-net-core.png)

1. Actualice el **nombre para mostrar** con **dotnet test** y, en la lista desplegable, **Comando**, seleccione **test** (probar). Agregue a continuación la **ruta de acceso al proyecto**.

    ```
    tests/FilterModuleTest/*.csproj
    ```

    ![Configuración de dotnet test](./media/how-to-ci-cd/dotnet-test.png)

1. Haga clic en **+** en el lado derecho de **Phase 1** (Fase 1) para agregar una tarea a la fase. A continuación, busque y seleccione **Azure IoT Edge** y haga clic en el botón **Agregar** **dos veces** para agregar estas tareas a la fase.

    ![IoT Edge](./media/how-to-ci-cd/add-azure-iot-edge.png)

1. En la primera tarea de Azure IoT Edge, actualice el **nombre para mostrar** con **Module Build and Push** y, en la lista desplegable **Acción**, seleccione **Build and Push** (Compilar e insertar). En el cuadro de texto **Archivo Module.json**, agregue a continuación la ruta de acceso a él. Luego, elija **Tipo de Container Registry** y asegúrese de que configura y selecciona el mismo registro en el código. Esta tarea compila e inserta todos los módulos en la solución y los publica en el registro de contenedor especificado. 

    ```
    **/module.json
    ```

    ![Compilación e inserción del módulo](./media/how-to-ci-cd/module-build-push.png)

1. En la segunda tarea de Azure IoT Edge, actualice el **nombre para mostrar** con **Deploy to IoT Edge device** y, en la lista desplegable, **Acción**, seleccione **Deploy to IoT Edge device** (Implementar en un dispositivo IoT Edge). Seleccione la suscripción de Azure y escriba el nombre de IoT Hub. Puede especificar un identificador de implementación de IoT Edge y la prioridad de implementación. También puede elegir realizar la implementación en uno o varios dispositivos. En el segundo caso, debe especificar la condición del destino del dispositivo. Por ejemplo, si quiere usar etiquetas de dispositivo como condición, debe actualizar las etiquetas de dispositivo correspondientes antes de la implementación. 

    ![Implementación en Edge](./media/how-to-ci-cd/deploy-to-edge.png)

1. Haga clic en el **proceso** y asegúrese de que **Cola de agentes** sea **Hosted Linux Preview** (Versión preliminar de Linux hospedada).

    ![Configuración](./media/how-to-ci-cd/configure-env.png)

1. Abra la pestaña **Desencadenadores** y active el desencadenador **Integración continua**. Asegúrese de que se incluye la rama que contiene el código.

    ![Desencadenador](./media/how-to-ci-cd/configure-trigger.png)

1. Guarde la nueva definición de compilación y ponga la nueva compilación en cola. Haga clic en el botón **Save & queue** (Guardar y poner en cola).

1. Elija el vínculo a la compilación en la barra de mensajes que aparece. O bien, vaya a la definición de compilación para ver el trabajo de compilación más reciente en cola.

    ![Compilación](./media/how-to-ci-cd/build-def.png)

1. Una vez finalizada la compilación, consulte el resumen de cada tarea y los resultados en el archivo de registro activo. 
    
    ![Complete](./media/how-to-ci-cd/complete.png)

1. Puede regresar a VS Code y comprobar el explorador de dispositivos de IoT Hub. El dispositivo de Edge con el módulo empezará a ejecutarse (asegúrese de que ha agregado las credenciales de registro al entorno de tiempo de ejecución de Edge).

    ![Edge en ejecución](./media/how-to-ci-cd/edge-running.png)

## <a name="continuous-deployment-to-iot-edge-devices"></a>Implementación continua en dispositivos IoT Edge

Para habilitar la implementación continua, básicamente necesitará configurar trabajos de CI con los dispositivos IoT Edge adecuados mediante la habilitación de **Desencadenadores** en las ramas de su proyecto. En la práctica clásica de DevOps, un proyecto contiene dos ramas principales. La rama master debe ser la versión estable del código, y la rama de desarrollo contiene los últimos cambios en el código. Cada desarrollador del equipo debe bifurcar la rama de desarrollo a su propia rama de características al empezar a actualizar el código, lo que significa que todas las confirmaciones se producen en las ramas de características de la rama de desarrollo. Y cada confirmación insertada se debe probar mediante el sistema de CI. Una vez probado totalmente el código localmente, la rama de características se debe combinar con la rama de desarrollo mediante una solicitud de incorporación de cambios. Cuando el código de la rama de desarrollo se prueba mediante el sistema de CI, se puede combinar con una rama master mediante una solicitud de incorporación de cambios.

Por lo tanto, al realizar la implementación en dispositivos IoT Edge, hay tres entornos principales.
- En la rama de características, puede usar el dispositivo IoT Edge simulado en la máquina de desarrollo o realizar la implementación en un dispositivo físico IoT Edge.
- En la rama de desarrollo, debe realizar la implementación en un dispositivo IoT Edge físico.
- En la rama master, los dispositivos IoT Edge de destino deben ser los dispositivos de producción.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial se muestra cómo puede usar las características de integración continua e implementación continua de VSTS o TFS. 

* Puede encontrar información sobre la implementación de IoT Edge en [Descripción de las implementaciones de IoT Edge en un único dispositivo o a escala](module-deployment-monitoring.md).
* Siga los pasos para crear, actualizar o eliminar una implementación en [Implementación y supervisión de módulos de IoT Edge a escala][how-to-deploy-monitor.md].









