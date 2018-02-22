---
title: "Módulo SQL de Azure IoT Edge | Microsoft Docs"
description: "Almacene datos en el perímetro con módulos de SQL de Microsoft, con Azure Functions para dar formato a los datos."
services: iot-edge
keywords: 
author: ebertrams
manager: timlt
ms.author: kgremban, ebertrams
ms.date: 02/07/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: f7ff069a2536d0138be8cbb32eefba342e1e9275
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/09/2018
---
# <a name="store-data-at-the-edge-with-sql-server-databases"></a>Almacenamiento de datos en el perímetro con bases de datos de SQL Server

Use dispositivos perimetrales de Azure IoT Edge para almacenar los datos que se generan en el perímetro. Los dispositivos con conexiones intermitentes a Internet pueden mantener sus propias bases de datos y notificar los cambios a la nube solo cuando se conectan. Los dispositivos que se han programado para enviar únicamente los datos importantes a la nube, pueden guardar el resto de los datos para cargas masivas normales. Una vez en la nube, los datos estructurados pueden compartirse con otros servicios de Azure, por ejemplo para crear un modelo de aprendizaje automático. 

Este artículo proporciona instrucciones para implementar una base de datos de SQL Server en un dispositivo IoT Edge. Azure Functions, ejecutándose en el dispositivo IoT Edge, estructura los datos de entrada y a continuación los envía a la base de datos. Los pasos descritos en este artículo también pueden aplicarse a otras bases de datos que funcionan en contenedores, como MySQL o PostgreSQL. 

## <a name="prerequisites"></a>requisitos previos 

Antes de empezar con las instrucciones del presente artículo, debe completar los siguientes tutoriales:
* Implementación de Azure IoT Edge en un dispositivo simulado en [Windows](tutorial-simulate-device-windows.md) o [Linux](tutorial-simulate-device-linux.md)
* [Implementación de Azure Functions como módulo de IoT Edge](tutorial-deploy-function.md)

Los artículos siguientes no son necesarios para completar correctamente este tutorial, pero pueden proporcionar un contexto útil:
* [Ejecutar la imagen de contenedor de SQL Server 2017 con Docker](https://docs.microsoft.com/sql/linux/quickstart-install-connect-docker)
* [Uso de Visual Studio Code para desarrollar y depurar Azure Functions en Azure IoT Edge](how-to-vscode-develop-azure-function.md)

Después de completar los tutoriales necesarios, debe tener todos los requisitos previos listos en su máquina: 
* Una instancia de Azure IoT Hub activa.
* Un dispositivo IoT Edge con al menos 2 GB de RAM y una unidad de disco de 2 GB.
* [Visual Studio Code](https://code.visualstudio.com/). 
* [Extensión de Azure IoT Edge para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). 
* [Extensión de C# para Visual Studio Code (con tecnología de OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) 
* [Docker](https://docs.docker.com/engine/installation/)
* [SDK de .NET Core 2.0](https://www.microsoft.com/net/core#windowscmd). 
* [Python 2.7](https://www.python.org/downloads/)
* [Script de control de IoT Edge](https://pypi.python.org/pypi/azure-iot-edge-runtime-ctl)
* Plantilla de AzureIoTEdgeFunction (`dotnet new -i Microsoft.Azure.IoT.Edge.Function`)
* Un centro de IoT activo con al menos un dispositivo de IoT Edge

Tanto los contenedores de Windows como los de Linux en arquitecturas de procesador x64 sirven para este tutorial. SQL Server no es compatible con procesadores ARM.

## <a name="deploy-a-sql-server-container"></a>Implementación de un contenedor de SQL Server

En esta sección, agregará una base de datos MS-SQL al dispositivo IoT Edge simulado. Use la imagen de contenedor de Docker de SQL Server 2017 disponible en [Windows](https://hub.docker.com/r/microsoft/mssql-server-windows-developer/) y [Linux](https://hub.docker.com/r/microsoft/mssql-server-linux/). 

### <a name="deploy-sql-server-2017"></a>Implementación de SQL Server 2017

De forma predeterminada, el código de esta sección crea un contenedor con la edición de desarrollador gratuita de SQL Server 2017. Si desea ejecutar las ediciones de producción en su lugar, consulte [Ejecute las imágenes del contenedor de producción](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-docker#production) para obtener información detallada. 

En el paso 3, se agregan opciones de creación al contenedor de SQL Server, estas son importantes para establecer variables de entorno y almacenamiento persistente. Las [variables de entorno](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-environment-variables) configuradas aceptan el contrato de licencia de usuario final y definen una contraseña. El [almacenamiento persistente](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-docker#persist) se configura mediante [montajes](https://docs.docker.com/storage/volumes/). Los montajes crean el contenedor de SQL Server 2017 con un contenedor de volumen *sqlvolume* adjuntado de forma que los datos persisten incluso si se elimina el contenedor. 

1. Abra el archivo `deployment.json` en Visual Studio Code. 
2. Reemplace la sección **modules** con el siguiente código: 

   ```json
   "modules": {
          "filterFunction": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "localhost:5000/filterfunction:latest",
              "createOptions": "{}"
            }
          },
          "tempSensor": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview",
              "createOptions": "{}"
            }
          },
          "sql": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "",
              "createOptions": ""
             }
          }
        }
      }
   ```

3. Según el sistema operativo que esté ejecutando, actualice la configuración del módulo de SQL con el código siguiente: 

   * Windows:

      ```json
      "image": "microsoft/mssql-server-windows-developer",
      "createOptions": "{\r\n\t"Env": [\r\n\t\t"ACCEPT_EULA=Y",\r\n\t\t"sa_password=Strong!Passw0rd"\r\n\t],\r\n\t"HostConfig": {\r\n\t\t"Mounts": [{\r\n\t\t\t"Target": "C:\\mssql",\r\n\t\t\t"Source": "sqlVolume",\r\n\t\t\t"Type": "volume"\r\n\t\t}],\r\n\t\t"PortBindings": {\r\n\t\t\t"1433/tcp": [{\r\n\t\t\t\t"HostPort": "1401"\r\n\t\t\t}]\r\n\t\t}\r\n\t}\r\n}"
      ```

   * Linux:

      ```json
      "image": "microsoft/mssql-server-linux:2017-latest",
      "createOptions": "{\r\n\t"Env": [\r\n\t\t"ACCEPT_EULA=Y",\r\n\t\t"MSSQL_SA_PASSWORD=Strong!Passw0rd"\r\n\t],\r\n\t"HostConfig": {\r\n\t\t"Mounts": [{\r\n\t\t\t"Target": "/var/opt/mssql",\r\n\t\t\t"Source": "sqlVolume",\r\n\t\t\t"Type": "volume"\r\n\t\t}],\r\n\t\t"PortBindings": {\r\n\t\t\t"1433/tcp": [{\r\n\t\t\t\t"HostPort": "1401"\r\n\t\t\t}]\r\n\t\t}\r\n\t}\r\n}"
      ```

4. Guarde el archivo. 
5. En la paleta de comandos de Visual Studio Code, seleccione **Edge: Create deployment for Edge device** (Edge: crear implementación para dispositivo de Edge). 
6. Seleccione el identificador del dispositivo de IoT Edge.
7. Seleccione el archivo `deployment.json` que ha actualizado. En la ventana de salida, verá las salidas correspondientes a la implementación. 
8. Para iniciar el entorno de tiempo de ejecución de Edge en la paleta de comandos, seleccione **Edge: Start Edge** (Edge: iniciar Edge).

>[!TIP]
>Cada vez que cree un contenedor de SQL Server en un entorno de producción, debería [cambiar la contraseña de administrador del sistema predeterminada](https://docs.microsoft.com/sql/linux/quickstart-install-connect-docker.md#change-the-sa-password).

## <a name="create-the-sql-database"></a>Creación de la base de datos SQL

Esta sección le guiará en el proceso de configuración de la base de datos SQL para almacenar los datos de temperatura recibidos de los sensores conectados al dispositivo IoT Edge. Si está usando un dispositivo simulado, estos datos proceden del contenedor *tempSensor*. 

En una herramienta de línea de comandos, conéctese a la base de datos: 

* Windows
   ```cmd
   Docker exec -it sql cmd
   ```

* Linux    
   ```cmd
   Docker exec -it sql 'bash'
   ```

Abra la herramienta de comando SQL: 

* Windows
   ```cmd
   sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
   ```

* Linux
   ```cmd
   /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
   ```

Cree la base de datos: 

* Windows
   ```sql
   CREATE DATABASE MeasurementsDB
   ON
   (NAME = MeasurementsDB, FILENAME = 'C:\mssql\measurementsdb.mdf')
   GO
   ```

* Linux
   ```sql
   CREATE DATABASE MeasurementsDB
   ON
   (NAME = MeasurementsDB, FILENAME = '/var/opt/mssql/measurementsdb.mdf')
   GO
   ```

Defina la tabla: 

   ```sql
   CREATE TABLE MeasurementsDB.dbo.TemperatureMeasurements (measurementTime DATETIME2, location NVARCHAR(50), temperature FLOAT)
   GO
   ```

Puede personalizar el archivo de Docker de SQL Server para configurar automáticamente SQL Server para que se implemente en varios dispositivos IoT Edge. Para más información, consulte el [proyecto de demostración de contenedor de Microsoft SQL Server](https://github.com/twright-msft/mssql-node-docker-demo-app).

## <a name="understand-the-sql-connection"></a>Información sobre la conexión SQL

En otros tutoriales, se utiliza rutas para permitir que los contenedores se comuniquen, mientras permanecen aislados entre sí. Cuando se trabaja con una base de datos de SQL Server, sin embargo, es necesaria una relación más cercana. 

IoT Edge crea automáticamente un puente (Linux) o una red NAT (Windows) cuando se inicia. La red se llama **azure-iot-edge**. Si alguna vez tiene que depurar esta conexión, puede buscar sus propiedades en la línea de comandos:

* Windows

   ```cmd
   docker network inspect azure-iot-edge
   ```

* Linux

   ```cmd
   sudo docker network inspect azure-iot-edge
   ```

IoT Edge también puede resolver el DNS de un nombre de contenedor a través de Docker, por lo que no tiene que hacer referencia a la base de datos de SQL Server mediante su dirección IP. 

Por ejemplo, aquí está la cadena de conexión que se usa en la sección siguiente: 

   ```csharp
   Data Source=tcp:sql,1433;Initial Catalog=MeasurementsDB;User Id=SA;Password=Strong!Passw0rd;TrustServerCertificate=False;Connection Timeout=30;
   ```

Puede ver que la cadena de conexión hace referencia al contenedor por su nombre, **sql**. Si ha cambiado el nombre del módulo para que sea otra cosa, actualice también esta cadena de conexión. De lo contrario, continúe en siguiente sección. 

## <a name="update-your-azure-function"></a>Actualice su función de Azure
Para enviar los datos a la base de datos, actualice la función de Azure FilterFunction que creó en el tutorial anterior. Modifique este archivo para que estructure los datos recibidos por los sensores y los almacene en una tabla SQL. 

1. En Visual Studio Code, abra la carpeta FilterFunction. 
2. Reemplace el archivo run.csx con el siguiente código que incluye la cadena de conexión de SQL de la sección anterior: 

   ```csharp
   #r "Microsoft.Azure.Devices.Client"
   #r "Newtonsoft.Json"
   #r "System.Data.SqlClient"

   using System.IO;
   using Microsoft.Azure.Devices.Client;
   using Newtonsoft.Json;
   using Sql = System.Data.SqlClient;
   using System.Threading.Tasks;

   // Filter messages based on the temperature value in the body of the message and the temperature threshold value.
   public static async Task Run(Message messageReceived, IAsyncCollector<Message> output, TraceWriter log)
   {
       const int temperatureThreshold = 25;
       byte[] messageBytes = messageReceived.GetBytes();
       var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

       if (!string.IsNullOrEmpty(messageString))
       {
           // Get the body of the message and deserialize it
           var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

           //Store the data in SQL db
           const string str = "Data Source=tcp:sql,1433;Initial Catalog=MeasurementsDB;User Id=SA;Password=Strong!Passw0rd;TrustServerCertificate=False;Connection Timeout=30;";
           using (Sql.SqlConnection conn = new Sql.SqlConnection(str))
           {
           conn.Open();
           var insertMachineTemperature = "INSERT INTO MeasurementsDB.dbo.TemperatureMeasurements VALUES (CONVERT(DATETIME2,'" + messageBody.timeCreated + "', 127), 'machine', " + messageBody.machine.temperature + ");";
           var insertAmbientTemperature = "INSERT INTO MeasurementsDB.dbo.TemperatureMeasurements VALUES (CONVERT(DATETIME2,'" + messageBody.timeCreated + "', 127), 'ambient', " + messageBody.ambient.temperature + ");"; 
               using (Sql.SqlCommand cmd = new Sql.SqlCommand(insertMachineTemperature + "\n" + insertAmbientTemperature, conn))
               {
               //Execute the command and log the # rows affected.
               var rows = await cmd.ExecuteNonQueryAsync();
               log.Info($"{rows} rows were updated");
               }
           }

           if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
           {
               // Send the message to the output as the temperature value is greater than the threshold
               var filteredMessage = new Message(messageBytes);
               // Copy the properties of the original message into the new Message object
               foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
               {
                   filteredMessage.Properties.Add(prop.Key, prop.Value);
               }
               // Add a new property to the message to indicate it is an alert
               filteredMessage.Properties.Add("MessageType", "Alert");
               // Send the message        
               await output.AddAsync(filteredMessage);
               log.Info("Received and transferred a message with temperature above the threshold");
           }
       }
   }

   //Define the expected schema for the body of incoming messages
   class MessageBody
   {
       public Machine machine {get;set;}
       public Ambient ambient {get; set;}
       public string timeCreated {get; set;}
   }
   class Machine
   {
      public double temperature {get; set;}
      public double pressure {get; set;}         
   }
   class Ambient
   {
      public double temperature {get; set;}
      public int humidity {get; set;}         
   }
   ```

## <a name="update-your-container-image"></a>Actualización de una imagen de contenedor

Para aplicar los cambios realizados, actualice la imagen de contenedor, publíquela y reinicie IoT Edge.

1. En Visual Studio Code, expanda la carpeta **Docker**. 
2. En función de la plataforma que está usando, expanda la carpeta **windows-nano** o **linux-x64**. 
3. Haga clic con el botón derecho en el archivo **Dockerfile** y seleccione **Build IoT Edge module Docker image** (Generar imagen de Docker del módulo de IoT Edge).
4. Vaya a la carpeta de proyecto **FilterFunction** y haga clic en **Select Folder as EXE_DIR** (Seleccionar carpeta como EXE_DIR).
5. En el cuadro de texto emergente de la parte superior de la ventana de VS Code, escriba el nombre de la imagen. Por ejemplo, `<your container registry address>/filterfunction:latest`. Si va a realizar la implementación en el registro local, el nombre debe ser: `<localhost:5000/filterfunction:latest>`.
6. En la paleta de comandos de Visual Studio Code seleccione **Edge: Push IoT Edge module Docker image** (Edge: insertar la imagen de Docker del módulo de IoT Edge). 
7. En el cuadro de texto emergente, escriba el mismo nombre de imagen. 
8. En la paleta de comandos de Visual Studio Code seleccione **Edge: Restart Edge** (Edge: reiniciar Edge).

## <a name="view-the-local-data"></a>Visualización de los datos locales

Una vez que se reinicien los contenedores, los datos recibidos de los sensores de temperatura se almacenan en una base de datos de SQL Server 2017 local en el dispositivo IoT Edge. 

En una herramienta de línea de comandos, conéctese a la base de datos: 

* Windows
   ```cmd
   Docker exec -it sql cmd
   ```

* Linux    
   ```cmd
   Docker exec -it sql 'bash'
   ```

Abra la herramienta de comando SQL: 

* Windows
   ```cmd
   sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
   ```

* Linux
   ```cmd
   /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
   ```

Visualice los datos: 

   ```sql
   Select * FROM MeasurementsDB.dbo.TemperatureMeasurements
   GO
   ```

## <a name="next-steps"></a>pasos siguientes

* Aprenda a [configurar SQL Server 2017 imágenes del contenedor en Docker](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-docker).

* Visite el [repositorio de GitHub mssql-docker](https://github.com/Microsoft/mssql-docker) para recursos, comentarios y problemas conocidos.