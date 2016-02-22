<properties
	pageTitle="Crear un Centro de Iot con una plantilla del Administrador de recursos | Microsoft Azure"
	description="Siga este tutorial para empezar a usar las plantillas del Administrador de recursos para crear un Centro de IoT con un programa de C# o con PowerShell."
	services="iot-hub"
	documentationCenter=".net"
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="dotnet"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="11/23/2015"
     ms.author="dobett"/>

# Tutorial: Crear un centro de IoT con un programa de C#

[AZURE.INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## Introducción

Puede usar el Administrador de recursos de Azure para crear y administrar los centros de IoT de Azure mediante programación. En este tutorial se muestra cómo usar una plantilla del administrador de recursos para crear un centro de IoT desde un programa de C#.

> [AZURE.NOTE] Azure tiene dos modelos de implementación diferentes para crear y trabajar con recursos: [el Administrador de recursos y el clásico](../resource-manager-deployment-model.md). Este artículo trata sobre el uso del modelo de implementación del Administrador de recursos.

Para completar este tutorial, necesitará lo siguiente:

- Microsoft Visual Studio 2015.
- Una cuenta de Azure activa. <br/>En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure][lnk-free-trial].
- [Microsoft Azure PowerShell 1.0][lnk-powershell-install] o versiones posteriores.

> [AZURE.TIP] En este artículo se describe cómo crear un nuevo Centro de IoT con un programa de C# y una plantilla ARM. También puede usar la [plantilla ARM](#submit-a-template-to-create-an-iot-hub) de este artículo con un script de PowerShell en lugar de un programa de C#. El artículo [Uso de Azure PowerShell con Administrador de recursos de Azure][lnk-powershell-arm] describe cómo escribir un script de PowerShell que usa una plantilla ARM para crear un recurso de Azure como, por ejemplo, un Centro de IoT.

[AZURE.INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## Preparar su proyecto de Visual Studio

1. En Visual Studio, cree un nuevo proyecto de Windows de Visual C# con la plantilla de proyecto **Aplicación de consola**. Asigne al proyecto el nombre **CreateIoTHub**.

2. En el Explorador de soluciones, haga clic con el botón secundario en su proyecto y luego haga clic en **Administrar paquetes de NuGet**.

3. En el Administrador de paquetes de NuGet, busque **Microsoft.Azure.Management.Resources**. Seleccione la versión **2.18.11-preview**. Haga clic en **Instalar**, en **Revisar cambios**, haga clic en **Aceptar** y, luego, en **Acepto** para aceptar las licencias.

4. En el Administrador de paquetes de NuGet, busque **Microsoft.IdentityModel.Clients.ActiveDirectory**. Seleccione la versión **2.19.208020213**. Haga clic en **Instalar**, en **Revisar cambios**, haga clic en **Aceptar** y, luego, en **Acepto** para aceptar la licencia.

5. En el Administrador de paquetes de NuGet, busque **Microsoft.Azure.Common**. Seleccione la versión **2.1.0**. Haga clic en **Instalar**, en **Revisar cambios**, haga clic en **Aceptar** y, luego, en **Acepto** para aceptar las licencias.

6. En Program.cs, reemplace las instrucciones **using** existentes por las siguientes:

    ```
    using System;
    using System.IO;
    using System.Net;
    using System.Net.Http.Headers;
    using Microsoft.Azure;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.Resources.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```
    
7. En Program.cs, agregue las siguientes variables estáticas reemplazando los valores de marcador de posición. Realizó una nota de **ApplicationId**, **SubscriptionId**, **TenantId** y **Password** anteriormente en este tutorial. El **Nombre de grupo de recursos** es el nombre del grupo de recursos que usará al crear el Centro de IoT, puede ser un grupo de recursos existente u otro nuevo. El **nombre del Centro de IoT** es el nombre del Centro de IoT que creará, como **MyIoTHub**. El **Nombre de la implementación** es un nombre para la implementación, como **Deployment\_01**.

    ```
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";
    
    static string rgName = "{Resource group name}";
    static string iotHubName = "{IoT Hub name}";
    static string deploymentName = "{Deployment name}";
    ```

[AZURE.INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## Enviar una plantilla para crear un centro de IoT

Use una plantilla de JSON para crear un nuevo centro de IoT en el grupo de recursos. También puede usar la plantilla para realizar cambios en un centro de IoT existente.

1. En el Explorador de soluciones, haga clic con el botón secundario en su proyecto y haga clic en **Agregar** y luego en **Nuevo elemento**. Agregue un nuevo archivo JSON denominado **template.json** a su proyecto.

2. En el Explorador de soluciones, seleccione **template.json** y, luego, en **Propiedades**, establezca **Copiar en el directorio de salida** en **Copiar siempre**.

3. Reemplace el contenido de **template.json** por la siguiente definición de recursos para agregar un nuevo centro de IoT estándar a la región **Este de EE.UU.**:

    ```
    {
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",

    "resources": [
      {
        "apiVersion": "2015-08-15-preview",
        "type": "Microsoft.Devices/Iothubs",
        "name": "[IotHubName]",
        "location": "East US",
        "sku": {
          "name": "S1",
          "tier": "Standard",
          "capacity": 1
        },
        "properties": {
          "location": "East US"
        }
      }
    ]
    }
    ```

4. Agregue el método siguiente a Program.cs:
    
    ```
    static bool CreateIoTHub(ResourceManagementClient client)
    {
        
    }
    ```

5. Agregue el código siguiente al método **CreateIoTHub** para cargar el archivo de plantilla, agregue el nombre del centro de IoT y envíe la plantilla al Administrador de recursos de Azure:

    ```
    string template = File.ReadAllText("template.json");
    template = template.Replace("[IotHubName]", iotHubName);
    var createResponse = client.Deployments.CreateOrUpdateAsync(
      rgName,
      deploymentName,
      new Deployment()
      {
        Properties = new DeploymentProperties
        {
          Mode = DeploymentMode.Incremental,
          Template = template
        }
      }).Result;
    ```

6. Agregue el código siguiente al método **CreateIoTHub** que espera hasta que la implementación se complete correctamente:

    ```
    string state = createResponse.Deployment.Properties.ProvisioningState;
    while (state != "Succeeded" && state != "Failed")
    {
      var getResponse = client.Deployments.GetAsync(
        rgName,
        deploymentName).Result;

      state = getResponse.Deployment.Properties.ProvisioningState;
      Console.WriteLine("Deployment state: {0}", state);
    }

    if (state != "Succeeded")
    {
      Console.WriteLine("Failed to create iothub");
      return false;
    }
    return true;
    ```

[AZURE.INCLUDE [iot-hub-retrieve-keys](../../includes/iot-hub-retrieve-keys.md)]

## Completar y ejecutar la aplicación

Ahora puede completar la aplicación llamando a los métodos **CreateIoTHub** y **ShowIoTHubKeys** antes de compilarla y ejecutarla.

1. Agregue el siguiente código al final del método **Main**:

    ```
    if (CreateIoTHub(client))
        ShowIoTHubKeys(client, token.AccessToken);
    Console.ReadLine();
    ```
    
2. Haga clic en **Compilar** y luego en **Compilar solución**. Corrija los errores.

3. Haga clic en **Depurar** y luego en **Iniciar depuración** para ejecutar la aplicación. La ejecución de la implementación puede tardar varios minutos en completarse.

4. Para comprobar que su aplicación ha agregado el nuevo centro de IoT, visite el [portal][lnk-azure-portal] y vea la lista de recursos o use el cmdlet de PowerShell **Get-AzureRmResource**.

> [AZURE.NOTE] Esta aplicación de ejemplo agrega un centro de IoT estándar S1 por el que se le cobrará. Puede eliminar el centro de IoT a través del [portal][lnk-azure-portal] o mediante el cmdlet **Remove-AzureRmResource** de PowerShell cuando haya terminado.

## Pasos siguientes

- Explore las capacidades de la [API de REST del proveedor de recursos del centro de IoT][lnk-rest-api].
- Lea la [Información general de Administrador de recursos de Azure][lnk-azure-rm-overview] para más información sobre las capacidades del Administrador de recursos de Azure.

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-powershell-install]: https://azure.microsoft.com/es-ES/blog/azps-1-0-pre/
[lnk-rest-api]: https://msdn.microsoft.com/library/mt589014.aspx
[lnk-azure-rm-overview]: ./resource-group-overview.md
[lnk-powershell-arm]: ./powershell-azure-resource-manager.md

<!---HONumber=AcomDC_0211_2016-->