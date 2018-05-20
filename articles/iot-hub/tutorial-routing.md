---
title: Configuración del enrutamiento de mensajes con Azure IoT Hub (.NET) | Microsoft Docs
description: Configuración del enrutamiento de mensajes con Azure IoT Hub
services: iot-hub
documentationcenter: .net
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: ''
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/01/2018
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 0674ed033f77d7d2eca319d0b1e82171dfa4256d
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-configure-message-routing-with-iot-hub"></a>Tutorial: Configuración del enrutamiento de mensajes con IoT Hub

El enrutamiento de mensajes permite enviar datos de telemetría desde dispositivos de IoT a puntos de conexión integrados compatibles con el centro de eventos o puntos de conexión personalizados como Blob Storage, cola de Service Bus, tema de Service Bus y Event Hubs. Al configurar el enrutamiento de mensajes, puede crear reglas de enrutamiento para personalizar la ruta que coincide con una regla determinada. Una vez configurado, los datos entrantes se enrutan automáticamente a los puntos de conexión mediante IoT Hub. 

En este tutorial, aprenderá a configurar y usar las reglas de enrutamiento con IoT Hub. Enrutará los mensajes des un dispositivo de IoT a uno de los múltiples servicios, entre los que se incluyen Blob Storage y una cola de Service Bus. Los mensajes que envían a la cola de Service Bus los seleccionará una aplicación lógica y se enviarán por correo electrónico. Los mensajes que no tienen el enrutamiento configurado específicamente se envían al punto de conexión predeterminado y se ven en Power BI.

En este tutorial se realizan las siguientes tareas:

> [!div class="checklist"]
> * Mediante la CLI de Azure o PowerShell, configurar los recursos base (una instancia de IoT Hub, una cuenta de almacenamiento, una cola de Service Bus y un dispositivo simulado).
> * Configurar los puntos de conexión y las rutas en IoT Hub para la cuenta de almacenamiento y la cola de Service Bus.
> * Crear una aplicación lógica que se desencadena y se envía un correo electrónico cuando se agrega un mensaje a la cola de Service Bus.
> * Descargar y ejecutar una aplicación que simule que un dispositivo IoT envía mensajes al centro para las diferentes opciones de enrutamiento.
> * Crear una visualización de Power BI para los datos enviados al punto de conexión predeterminado.
> * Vea los resultados...
> * ... en la cola de Service Bus y en los mensajes de correo electrónico.
> * ... en la cuenta de almacenamiento.
> * ... en la visualización de Power BI.

## <a name="prerequisites"></a>requisitos previos

- Una suscripción de Azure. Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

- Instale [Visual Studio para Windows](https://www.visualstudio.com/). 

- Una cuenta de Power BI para realizar el análisis del flujo del punto de conexión predeterminado ([pruebe Power BI de manera gratuita](https://app.powerbi.com/signupredirect?pbi_source=web)).

- Una cuenta de Office 365 para enviar notificaciones por correo electrónico. 

Se necesita la CLI de Azure o Azure PowerShell para realizar los pasos de configuración para este tutorial. 

Para utilizar la CLI de Azure, aunque puede instalarla localmente, se recomienda usar Azure Cloud Shell. Azure Cloud Shell es un shell interactivo gratuito que puede usar para ejecutar scripts de la CLI de Azure. Las herramientas comunes de Azure están preinstaladas y configuradas en Cloud Shell para que las use en su cuenta y no tenga que instalarlas localmente. 

Para usar PowerShell, instálelo localmente con las instrucciones siguientes. 

### <a name="azure-cloud-shell"></a>Azure Cloud Shell

Existen varias maneras de abrir Cloud Shell:

|  |   |
|-----------------------------------------------|---|
| Seleccione **Probarlo** en la esquina superior derecha de un bloque de código. | ![Cloud Shell en este artículo](./media/tutorial-routing/cli-try-it.png) |
| Abra Cloud Shell en el explorador. | [![https://shell.azure.com/bash](./media/tutorial-routing/launchcloudshell.png)](https://shell.azure.com) |
| Seleccione el botón **Cloud Shell** en el menú de la esquina superior derecha de [Azure Portal](https://portal.azure.com). |    ![Cloud Shell en el portal](./media/tutorial-routing/cloud-shell-menu.png) |
|  |  |

### <a name="using-azure-cli-locally"></a>Uso de la CLI de Azure en un entorno local

Si prefiere usar la CLI localmente, en lugar de usar Cloud Shell, debe tener la versión 2.0.30.0 del módulo de la CLI de Azure, o una versión posterior. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0](/cli/azure/install-azure-cli). 

### <a name="using-powershell-locally"></a>Uso de PowerShell en un entorno local

Para realizar este tutorial es necesaria la versión 5.7 del módulo de Azure PowerShell, o cualquier versión posterior. Ejecute `Get-Module -ListAvailable AzureRM` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Install and configure Azure PowerShell](/powershell/azure/install-azurerm-ps) (Instalación y configuración de Azure PowerShell).

## <a name="set-up-resources"></a>Configuración de recursos

Para este tutorial, se necesitan una instancia de IoT Hub, una cuenta de almacenamiento y una cola de Service Bus. Todos estos recursos se pueden crear con la CLI de Azure o Azure PowerShell. Use el mismo grupo de recursos y la misma ubicación para todos los recursos. Al final, puede quitar todos los elementos en un solo paso mediante la eliminación del grupo de recursos.

En las secciones siguientes se describe cómo realizar estos pasos. Siga las instrucciones de la CLI *o* de PowerShell.

1. Cree un [grupo de recursos](../azure-resource-manager/resource-group-overview.md). 

    <!-- When they add the Basic tier, change this to use Basic instead of Standard. -->

2. Creación de una instancia de IoT Hub en el nivel S1. Agregue un grupo de consumidores a la instancia de IoT Hub. El grupo de consumidores lo utiliza Azure Stream Analytics al recuperar datos.

3. Cree una cuenta de almacenamiento V1 estándar con replicación Standard_LRS.

4. Cree un espacio de nombres de Service Bus y una cola. 

5. Cree una identidad de dispositivo para el dispositivo simulado que envíe mensajes al centro. Guarde la clave para la fase de pruebas.

### <a name="azure-cli-instructions"></a>Instrucciones para la CLI de Azure

La manera más fácil de usar este script es copiarlo y pegarlo en Cloud Shell. Suponiendo que ya haya iniciado sesión, el script se ejecutará línea a línea. 

```azurecli-interactive

# This is the IOT Extension for Azure CLI.
# You only need to install this the first time.
# You need it to create the device identity. 
az extension add --name azure-cli-iot-ext

# Set the values for the resource names.
location=westus
resourceGroup=ContosoResources
iotHubConsumerGroup=ContosoConsumers
containerName=contosoresults
iotDeviceName=Contoso-Test-Device 

# These resource names must be globally unique.
# You might need to change these if they are already in use by someone else.
iotHubName=ContosoTestHub 
storageAccountName=contosoresultsstorage 
sbNameSpace=ContosoSBNamespace 
sbQueueName=ContosoSBQueue

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# Create the IoT hub.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --sku S1 --location $location

# Add a consumer group to the IoT hub.
az iot hub consumer-group create --hub-name $iotHubName \
    --name $iotHubConsumerGroup

# Create the storage account to be used as a routing destination.
az storage account create --name $storageAccountName \
    --resource-group $resourceGroup \
    --location $location \
    --sku Standard_LRS

# Get the primary storage account key. 
#    You need this to create the container.
storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroup \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"') 

# See the value of the storage account key.
echo "$storageAccountKey"

# Create the container in the storage account. 
az storage container create --name $containerName \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off 

# Create the Service Bus namespace.
az servicebus namespace create --resource-group $resourceGroup \
    --name $sbNameSpace \
    --location $location
    
# Create the Service Bus queue to be used as a routing destination.
az servicebus queue create --name $sbQueueName \
    --namespace-name $sbNameSpace \
    --resource-group $resourceGroup

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName

# Retrieve the information about the device identity, then copy the primary key to
#   Notepad. You need this to run the device simulation during the testing phase.
az iot hub device-identity show --device-id $iotDeviceName \
    --hub-name $iotHubName

```

### <a name="powershell-instructions"></a>Instrucciones para PowerShell

La forma más fácil de usar este script es abrir [PowerShell ISE](/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise.md), copiar el script en el Portapapeles y, luego, pegar todo el script en la ventana de script. Después puede cambiar los valores de los nombres de recursos (si lo desea) y ejecutar todo el script. 

```azurepowershell-interactive
# Log into Azure account.
Login-AzureRMAccount

# Set the values for the resource names.
$location = "West US"
$resourceGroup = "ContosoResources"
$iotHubConsumerGroup = "ContosoConsumers"
$containerName = "contosoresults"
$iotDeviceName = "Contoso-Test-Device"

# These resource names must be globally unique.
# You might need to change these if they are already in use by someone else.
$iotHubName = "ContosoTestHub"
$storageAccountName = "contosoresultsstorage"
$serviceBusNamespace = "ContosoSBNamespace"
$serviceBusQueueName  = "ContosoSBQueue"

# Create the resource group to be used  
#   for all resources for this tutorial.
New-AzureRmResourceGroup -Name $resourceGroup -Location $location

# Create the IoT hub.
New-AzureRmIotHub -ResourceGroupName $resourceGroup `
    -Name $iotHubName `
    -SkuName "S1" `
    -Location $location `
    -Units 1

# Add a consumer group to the IoT hub for the 'events' endpoint.
Add-AzureRmIotHubEventHubConsumerGroup -ResourceGroupName $resourceGroup `
  -Name $iotHubName `
  -EventHubConsumerGroupName $iotHubConsumerGroup `
  -EventHubEndpointName "events"

# Create the storage account to be used as a routing destination.
# Save the context for the storage account 
#   to be used when creating a container.
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
    -Name $storageAccountName `
    -Location $location `
    -SkuName Standard_LRS `
    -Kind Storage
$storageContext = $storageAccount.Context 

# Create the container in the storage account.
New-AzureStorageContainer -Name $containerName `
    -Context $storageContext

# Create the Service Bus namespace.
New-AzureRmServiceBusNamespace -ResourceGroupName $resourceGroup `
    -Location $location `
    -Name $serviceBusNamespace 

# Create the Service Bus queue to be used as a routing destination.
New-AzureRmServiceBusQueue -ResourceGroupName $resourceGroup `
    -Namespace $serviceBusNamespace `
    -Name $serviceBusQueueName 

```

A continuación, cree una identidad del dispositivo y guarde su clave para su uso posterior. Dicha identidad la usa la aplicación de simulación para enviar mensajes a la instancia de IoT Hub. Esta funcionalidad no está disponible en PowerShell, pero puede crear el dispositivo en [Azure Portal](https://portal.azure.com).

1. Abra [Azure Portal](https://portal.azure.com) e inicie sesión con su cuenta de Azure.

2. Haga clic en **Grupos de recursos** y seleccione el grupo de recursos. En este tutorial se usa **ContosoResources**.

3. En la lista de recursos, haga clic en IoT Hub. En este tutorial se usa **ContosoTestHub**. Seleccione **Dispositivos IoT** en el panel Cuadro.

4. Haga clic en **+ Agregar**. En el panel Agregar dispositivo, rellene el identificador del dispositivo. En este tutorial se usa **Contoso-Test-Device**. Deje en blanco las claves y marque **Generar claves automáticamente**. Asegúrese de que **Connect device to IoT hub** (Conectar dispositivo a IoT Hub) está habilitado. Haga clic en **Save**(Guardar).

   ![Captura de pantalla que muestra la pantalla para agregar dispositivos.](./media/tutorial-routing/add-device.png)

5. Una que ha creado el dispositivo, haga clic en él para ver las claves generadas. Haga clic en el icono Copiar en la clave principal y guárdelo en alguna parte, como el Bloc de notas, para la fase de pruebas del tutorial.

   ![Captura de pantalla que muestra los detalles del dispositivo, incluidas las claves.](./media/tutorial-routing/device-details.png)



## <a name="set-up-message-routing"></a>Configuración del enrutamiento de mensajes

Va a enrutar mensajes a diferentes recursos en función de propiedades que el dispositivo simulado adjunta al mensaje. Los mensajes que no tengan una ruta personalizada se envían al punto de conexión predeterminado (mensajes y eventos). 

|valor |Resultado|
|------|------|
|level="storage" |Se escribe en Azure Storage.|
|level="critical" |Es escribe en una cola de Service Bus. Una aplicación lógica recupera el mensaje de la cola y usa Office 365 para enviarlo por correo electrónico.|
|default |Se muestran estos datos con Power BI.|

### <a name="routing-to-a-storage-account"></a>Enrutamiento en una cuenta de almacenamiento 

Ahora, configure el enrutamiento de la cuenta de almacenamiento. Defina un punto de conexión y, después, configure una ruta para dicho punto. Los mensajes en los que la propiedad **level** esté establecida en **storage** se escriben automáticamente en una cuenta de almacenamiento.

1. En [Azure Portal](https://portal.azure.com), haga clic en **Grupos de recursos** y seleccione un grupo de recursos. En este tutorial se usa **ContosoResources**. Haga cloc en la instancia de IoT Hub en la lista de recursos. En este tutorial se usa **ContosoTestHub**. Haga clic en **Extremos**. En el panel **Extremos**, haga clic en **+ Agregar**. Escriba la siguiente información:

   **Nombre**: escriba el nombre del punto de conexión. En este tutorial se usa **StorageContainer**.
   
   **Tipo de extremo**: seleccione **Contenedor de Azure Storage** en la lista desplegable.

   Haga clic en **Seleccionar un contenedor** para ver la lista de cuentas de almacenamiento. Seleccione su cuenta de almacenamiento. En este tutorial se usa **contosoresultsstorage**. Luego, seleccione el contenedor. En este tutorial se usa **contosoresults**. Haga clic en **Seleccionar**, lo que le devuelve al panel Agregar punto de conexión. 
   
   ![Captura de pantalla que muestra la incorporación de un punto de conexión.](./media/tutorial-routing/add-endpoint-storage-account.png)
   
   Haga clic en **Aceptar** para terminar de agregar el punto de conexión.
   
2. Haga clic en **Rutas** en IoT Hub. Va a crear una regla de enrutamiento que enruta los mensajes al contenedor de almacenamiento que acaba de agregar como punto de conexión. Haga clic en **+Agregar** en la parte superior del panel Rutas. Rellene los campos de la pantalla. 

   **Nombre**: escriba el nombre de la regla de enrutamiento. En este tutorial se usa **StorageRule**.

   **Origen de datos**: seleccione **Mensajes del dispositivo** en la lista desplegable.

   **Extremo**: seleccione el punto de conexión que acaba de configurar. En este tutorial se usa **StorageContainer**. 
   
   **Cadena de consulta**: escriba `level="storage"` como cadena de consulta. 

   ![Captura de pantalla que muestra la creación de una regla de enrutamiento para la cuenta de almacenamiento.](./media/tutorial-routing/create-a-new-routing-rule-storage.png)
   
   Haga clic en **Save**(Guardar). Al terminar, vuelve al panel Rutas, donde puede ver la nueva regla de enrutamiento para el almacenamiento. Cierre el panel Rutas, lo que le devolverá a la página Grupo de recursos.

### <a name="routing-to-a-service-bus-queue"></a>Enrutamiento a una cola de Service Bus. 

Ahora, configure el enrutamiento de la cola de Service Bus. Defina un punto de conexión y, después, configure una ruta para dicho punto. Los mensajes en los que la propiedad **level** está establecida en **critical** se escriben en la cola de Service Bus, lo que desencadena una aplicación lógica, que, posteriormente, envía un correo electrónico con la información. 

1. En la página Grupo de recursos, haga clic en IoT Hub y, después, en **Extremos**. En el panel **Extremos**, haga clic en **+ Agregar**. Escriba la siguiente información.

   **Nombre**: escriba el nombre del punto de conexión. En este tutorial se usa **CriticalQueue**. 

   **Tipo de extremo**: seleccione **Cola de Service Bus** en la lista desplegable.

   **Espacio de nombres de Service Bus**: seleccione el espacio de nombres de Bus de servicio para este tutorial en la lista desplegable. En este tutorial se usa **ContosoSBNamespace**.

   **Cola de Service Bus**: seleccione la cola de Service Bus en la lista desplegable. En este tutorial se usa **contososbqueue**.

   ![Captura de pantalla que muestra la incorporación de un punto de conexión a la cola de Service Bus.](./media/tutorial-routing/add-endpoint-sb-queue.png)

   Haga clic en **Aceptar** para guardar el punto de conexión. Cuando finalice, cierre el panel Extremos. 
    
2. Haga clic en **Rutas** en IoT Hub. Va a crear una regla de enrutamiento que enruta los mensajes a la cola de Service Bus que acaba de agregar como punto de conexión. Haga clic en **+Agregar** en la parte superior del panel Rutas. Rellene los campos de la pantalla. 

   **Nombre**: escriba el nombre de la regla de enrutamiento. En este tutorial se usa **SBQueueRule**. 

   **Origen de datos**: seleccione **Mensajes del dispositivo** en la lista desplegable.

   **Extremo**: seleccione el punto de conexión que acaba de configurar, **CriticalQueue**.

   **Cadena de consulta**: escriba `level="critical"` como cadena de consulta. 

   ![Captura de pantalla que muestra la creación de una regla de enrutamiento para la cola de Service Bus.](./media/tutorial-routing/create-a-new-routing-rule-sbqueue.png)
   
   Haga clic en **Save**(Guardar). Al regresar al panel Rutas, se ven las dos nuevas reglas de enrutamientos, tal como se muestra aquí.

   ![Captura de pantalla que muestra las rutas que acaba de configurar.](./media/tutorial-routing/show-routing-rules-for-hub.png)

   Cierre el panel Rutas, lo que le devolverá a la página Grupo de recursos.

## <a name="create-a-logic-app"></a>Crear una aplicación lógica  

La cola de Service Bus se debe usar para recibir los mensajes designados como críticos. Configure una aplicación lógica que supervise la cola de Service Bus y envíe un correo electrónico cuando se agregue un mensaje a la cola. 

1. En [Azure Portal](https://portal.azure.com), haga clic en **+Crear un recurso**. Escriba **lógica app** en el cuadro de búsqueda y presione en Entrar. En los resultados de la búsqueda que se muestran, seleccione Logic App y, después, haga clic en **Crear** para pasar al panel **Crear lógica aplicación**. Rellene los campos. 

   **Nombre**: este campo es el nombre de la aplicación lógica. En este tutorial se usa **ContosoLogicApp**. 

   **Suscripción**: seleccione su suscripción de Azure.

   **Grupo de recursos**: haga clic en **Usar existente** y seleccione un grupo de recursos. En este tutorial se usa **ContosoResources**. 

   **Ubicación**: use su ubicación. En este tutorial se usa **Oeste de EE. UU.** 

   **Log Analytics**: esta opción debería estar desactivada. 

   ![Captura de pantalla que muestra la pantalla Crear aplicación lógica.](./media/tutorial-routing/create-logic-app.png)

   Haga clic en **Create**(Crear).

4. Ahora, vaya a la aplicación lógica. La manera más fácil de llegar a la aplicación lógica es hacer clic en **Grupos de recursos**, seleccionar un grupo de recursos (en este tutorial se usa **ContosoResources**) y, después, seleccionar la aplicación lógica en la lista de recursos. Aparece la página Diseñador de aplicaciones lógicas (es posible que tenga que desplazarse hacia la derecha para ver la página completa). En la página Diseñador de aplicaciones lógicas, desplácese hacia abajo hasta que vea el icono **Aplicación lógica en blanco +** y haga clic en él. 

5. Se muestra una lista de conectores. Seleccione **Service Bus**. 

   ![Captura de pantalla que muestra la lista de conectores.](./media/tutorial-routing/logic-app-connectors.png)

6. Se muestra una lista de desencadenadores. Después, seleccione el desencadenador **Service Bus - When a message is received in a queue** (Service Bus - Cuando se recibe un mensaje en una cola). 

   ![Captura de pantalla que muestra la lista de desencadenadores de Service Bus.](./media/tutorial-routing/logic-app-triggers.png)

6. En la siguiente pantalla, rellene el campo Nombre de la conexión. En este tutorial se usa **ContosoConnection**. 

   ![Captura de pantalla que muestra la configuración de la conexión para la cola de Service Bus.](./media/tutorial-routing/logic-app-define-connection.png)

   Haga clic en el espacio de nombre de Service Bus. En este tutorial se usa **ContosoSBNamespace**. Cuando se selecciona el espacio de nombres, el portal consulta el espacio de nombres de Service Bus para recuperar las claves. Seleccione **RootManageSharedAccessKey** y haga clic en **Crear**. 
   
   ![Captura de pantalla que muestra la finalización d la configuración de la conexión.](./media/tutorial-routing/logic-app-finish-connection.png)

7. En la siguiente pantalla, seleccione el nombre de la cola (en este tutorial se usa **contososbqueue**) en la lista desplegable. En los restantes campos puede dejar los valores predeterminados. 

   ![Captura de pantalla que muestra las opciones de la cola.](./media/tutorial-routing/logic-app-queue-options.png)

7. Ahora, configure la acción para enviar un correo electrónico cuando se recibe un mensaje en la cola. En el Diseñador de aplicaciones lógicas, haga clic en **+Nuevo paso** para agregar una acción y, después, haga clic en **Agregar una acción**. En el panel **Elegir una acción**, busque y haga clic en **Office 365 Outlook**. En la pantalla de desencadenadores, seleccione **Office 365 Outlook - Enviar un correo electrónico**.  

   ![Captura de pantalla que muestra las opciones de Office365.](./media/tutorial-routing/logic-app-select-outlook.png)

8. A continuación, inicie sesión con su cuenta de Office 365 para configurar la conexión. Especifique las direcciones de correo electrónico de los destinatarios de los mensajes de correo electrónico. Especifique también el asunto y escriba el mensaje que desea que el destinatario vea en el cuerpo. Para las pruebas, utilice su propia dirección de correo electrónico como destinatario.

   Haga clic en **Agregar contenido dinámico** para mostrar el contenido del mensaje que puede incluir. Seleccione **Contenido** (incluirá el mensaje en el correo electrónico). 

   ![Captura de pantalla que muestra las opciones de correo electrónico de la aplicación lógica.](./media/tutorial-routing/logic-app-send-email.png)

9. Haga clic en **Save**(Guardar). Después, cierre el Diseñador de aplicaciones lógicas.

## <a name="set-up-azure-stream-analytics"></a>Configuración de Azure Stream Analytics

Para ver los datos en una visualización de Power BI, primero es preciso configurar un trabajo de Stream Analytics que los recupere. Recuerde que los únicos mensajes que se envían al punto de conexión predeterminado y que el trabajo de Stream Analytics recuperará para la visualización de Power BI son aquellos en que **level** está establecido en **normal**.

### <a name="create-the-stream-analytics-job"></a>Creación del trabajo de Stream Analytics

1. En [Azure Portal](https://portal.azure.com), haga clic en **Crear un recurso** > **Internet de las cosas** > **Trabajo de Stream Analytics**.

2. Escriba la siguiente información para el trabajo.

   **Nombre del trabajo**: el nombre del trabajo. El nombre debe ser único globalmente. En este tutorial se usa **contosoJob**.

   **Grupo de recursos**: use el mismo grupo de recursos que IoT Hub. En este tutorial se usa **ContosoResources**. 

   **Ubicación**: use la misma ubicación que en el script de instalación. En este tutorial se usa **Oeste de EE. UU.** 

   ![Captura de pantalla que muestra cómo se crea el trabajo de Stream Analytics.](./media/tutorial-routing/stream-analytics-create-job.png)

### <a name="add-an-input-to-the-stream-analytics-job"></a>Adición de una entrada al trabajo de Stream Analytics

1. En **Topología de trabajo**, haga clic en **Entradas**.

2. En el panel **Entradas**, haga clic en **Agregar entrada de flujo** y seleccione IoT Hub. En la pantalla que aparece, rellene los campos siguientes:

   **Alias de entrada**: en este tutorial se usa **contosoinputs**.

   **Suscripción**: seleccione la suscripción.

   **IoT Hub**: seleccione la instancia de IoT Hub. En este tutorial se usa **ContosoTestHub**.

   **Extremo**: seleccione **Mensajería** (si selecciona Supervisión de operaciones, obtendrá los datos de telemetría de IoT Hub, en lugar de los datos que va a enviar). 

   **Nombre de directiva de acceso compartido**: seleccione **iothubowner**. El portal rellena automáticamente el campo Clave de directiva de acceso compartido.

   **Grupo de consumidores**: seleccione el grupo de consumidores que creó anteriormente. En este tutorial se usa **contosoconsumers**.
   
   En cuanto al resto de los campos, acepte los valores predeterminados. 

   ![Captura de pantalla que muestra cómo se configuran las entradas en el trabajo de Stream Analytics.](./media/tutorial-routing/stream-analytics-job-inputs.png)

5. Haga clic en **Save**(Guardar).

### <a name="add-an-output-to-the-stream-analytics-job"></a>Adición de una salida al trabajo de Stream Analytics

1. En **Topología de trabajo**, haga clic en **Salidas**.

2. En el panel **Salidas**, haga clic en **Agregar** y, después, seleccione **Power BI**. En la pantalla que aparece, rellene los campos siguientes:

   **Alias de salida**: el alias único para la salida. En este tutorial se usa **contosooutputs**. 

   **Nombre del conjunto de datos**: nombre del conjunto de datos que se va a usar en Power BI. En este tutorial se usa **contosodataset**. 

   **Nombre de la tabla**: nombre del conjunto de datos que se va a usar en Power BI. En este tutorial se usa **contosotable**.

   En el resto de los campos, acepte los valores predeterminados.

3. Haga clic en **Autorizar** e inicie sesión en su cuenta de Power BI.

   ![Captura de pantalla que muestra cómo se configuran las salidas en el trabajo de Stream Analytics.](./media/tutorial-routing/stream-analytics-job-outputs.png)

4. Haga clic en **Save**(Guardar).

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Configuración de la consulta del trabajo de Stream Analytics

1. En **Topología de trabajo**, haga clic en **Consulta**.

2. Reemplace `[YourInputAlias]` por el alias de entrada del trabajo. En este tutorial se usa **contosoinputs**.

3. Reemplace `[YourOutputAlias]` por el alias de salida del trabajo. En este tutorial se usa **contosooutputs**.

   ![Captura de pantalla que muestra cómo se configura la consulta del trabajo de Stream Analytics.](./media/tutorial-routing/stream-analytics-job-query.png)

4. Haga clic en **Save**(Guardar).

5. Cierre el panel Consulta.

### <a name="run-the-stream-analytics-job"></a>Ejecución del trabajo de Stream Analytics

En el trabajo de Stream Analytics, haga clic en **Iniciar** > **Ahora** > **Iniciar**. Una vez que el trabajo se inicia correctamente, su estado cambia de **Detenido** a **En ejecución**.

Para configurar el informe de Power BI, necesita datos, por lo que deberá configurar Power BI después de crear el dispositivo y ejecutar la aplicación de simulación de dispositivo.

## <a name="run-simulated-device-app"></a>Ejecución de una aplicación de simulación de dispositivo

Anteriormente, en la sección de configuración del script, configuró un dispositivo de simulación, para lo que usó un dispositivo IoT. En esta sección se descarga una aplicación de consola de .NET que simula un dispositivo que envía mensajes de dispositivo a nube a una instancia de IoT Hub. Dicha aplicación envía mensajes para cada uno de los distintos métodos de enrutamiento. 

Descargue la solución para la [simulación de un dispositivo IoT](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Se descarga un repositorio con varias aplicaciones; la solución que buscando está en Tutorials/Routing/SimulatedDevice/.

Haga doble clic en el archivo de la solución (SimulatedDevice.sln) para abrir el código en Visual Studio y, después, abra Program.cs. Sustituya `{iot hub hostname}` por el nombre de host de IoT Hub. El formato del nombre de host de IoT Hub es **{nombre de iot hub} .azure-devices.net**. Para este tutorial, el nombre de host del centro es **ContosoTestHub.azure devices.net**. A continuación, sustituya `{device key}` por la clave de dispositivo que guardó anteriormente al configurar el dispositivo simulado. 

   ```csharp
        static string myDeviceId = "contoso-test-device";
        static string iotHubUri = "ContosoTestHub.azure-devices.net";
        // This is the primary key for the device. This is in the portal. 
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key. 
        static string deviceKey = "{your device key here}";
   ```

## <a name="run-and-test"></a>Ejecución y prueba 

Ejecución de la aplicación de consola. Espere unos minutos. Puede ver los mensajes que se envían en la pantalla de la consola de la aplicación.

La aplicación envía un nuevo mensaje de dispositivo a nube a IoT Hub cada segundo. El mensaje contiene un objeto serializado mediante JSON serializado con el identificador de dispositivo, la temperatura, la humedad y el nivel de mensaje, cuyo valor predeterminado es `normal`. Asigna de forma aleatoria un nivel de `critical` o `storage`, lo que hace que el mensaje se enrute a la cuenta de almacenamiento o a la cola de Service Bus (lo que desencadena que la aplicación lógica envíe un correo electrónico). Las lecturas predeterminadas (`normal`) se mostrarán en el informe de BI que se configura a continuación.

Si todo está configurado correctamente, debería verá los siguientes resultados:

1. Empieza a recibir correos electrónicos acerca de mensajes críticos. 

   ![Captura de pantalla que muestra los mensajes de correo electrónico resultantes.](./media/tutorial-routing/results-in-email.png)

   Esto significa lo siguiente:

   * El enrutamiento a la cola de Service Bus funciona correctamente.
   * La aplicación lógica que recupera el mensaje de la cola de Service Bus funciona correctamente.
   * El conector de aplicación lógica con Outlook funciona correctamente. 

2. En [Azure Portal](https://portal.azure.com), haga clic en **Grupos de recursos** y seleccione un grupo de recursos. En este tutorial se usa **ContosoResources**. Seleccione la cuenta de almacenamiento, haga clic en **Blobs** y, después, seleccione el contenedor. En este tutorial se usa **contosoresults**. Debe ver una carpeta y puede explorar en profundidad los directorios hasta que vea uno o varios archivos. Abra cualquiera de ellos; contienen las entradas enrutadas a la cuenta de almacenamiento. 

   ![Captura de pantalla que muestra los archivos de resultados en el almacenamiento.](./media/tutorial-routing/results-in-storage.png)

Esto significa lo siguiente:

   * El enrutamiento a la cuenta de almacenamiento funciona correctamente.

Con la aplicación en ejecución, configure la visualización de Power BI para ver los mensajes que proceden del enrutamiento predeterminado. 

## <a name="set-up-the-powerbi-visualizations"></a>Configuración de las visualizaciones de Power BI

1. Inicie sesión en su cuenta de [Power BI](https://powerbi.microsoft.com/).

2. Vaya a **Áreas de trabajo** y seleccione la que estableció al crear la salida del trabajo de Stream Analytics. En este tutorial se usa **My Workspace**. 

3. Ahora haga clic en **Conjuntos de datos**.

   Debería ver en la lista el conjunto de datos que especificó en el momento de crear la salida para el trabajo de Stream Analytics. En este tutorial se usa **contosodataset** (la primera vez el conjunto de datos puede tardar entre 5 y 10 minutos en mostrarse).

4. En **ACCIONES**, haga clic en el primer icono para crear un informe.

   ![Captura de pantalla que muestra el área de trabajo de Power BI con el icono de Acciones e informe resaltado.](./media/tutorial-routing/power-bi-actions.png)

5. Cree un gráfico de líneas para mostrar la temperatura en tiempo real en un período determinado.

   a. En la página de creación de informes, agregue un gráfico de líneas, para lo que debe hacer clic en el icono del gráfico de línea.

   ![Captura de pantalla que muestra las visualizaciones y los campos.](./media/tutorial-routing/power-bi-visualizations-and-fields.png)

   b. En el panel **Campos**, expanda la tabla que especificó en el momento de crear la salida para el trabajo de Stream Analytics. En este tutorial se usa **contosotable**.

   c. Arrastre **EventEnqueuedUtcTime** (Hora UTC de evento en cola) al **Eje** en el panel **Visualizaciones**.

   d. Arrastre **temperature** (temperatura) a **Valores**.

   Se ha creado un gráfico de líneas. El eje x muestra la fecha y hora en la zona horaria UTC. El eje y muestra la temperatura del sensor.

7. Cree otro gráfico de líneas para mostrar la humedad en tiempo real en un período determinado. Para configurar el segundo gráfico, siga los mismos pasos y coloque **EventEnqueuedUtcTime** en el eje x y **humidity** en el eje y.

   ![Captura de pantalla que muestra el informe final de Power con los dos gráficos.](./media/tutorial-routing/power-bi-report.png)

8. Haga clic en **Guardar** para guardar el informe.

Debería poder ver los datos en ambos gráficos. Esto significa lo siguiente:

   * El enrutamiento al punto de conexión predeterminado funciona correctamente.
   * El trabajo de Azure Stream Analytics se transmite correctamente.
   * La visualización de Power BI está configurada correctamente.

Puede actualizar los gráficos para ver los datos más recientes. Para ello, debe hacer clic en el botón Actualizar, que se encuentra en la parte superior de la ventana de Power BI. 

## <a name="clean-up-resources"></a>Limpieza de recursos 

Si desea quitar todos los recursos que ha creado, elimine el grupo de recursos. Esta acción elimina también todos los recursos del grupo. En este caso, quita la instancia de IoT Hub, el espacio de nombres y la cola de Service Bus, la aplicación lógica, la cuenta de almacenamiento y el propio grupo de recursos. 

### <a name="clean-up-resources-in-the-powerbi-visualization"></a>Limpieza de recursos en la visualización de Power BI

Inicie sesión en su cuenta de [Power BI](https://powerbi.microsoft.com/). Vaya a su área de trabajo. En este tutorial se usa **My Workspace**. Para quitar la visualización de Power BI, vaya a Conjuntos de datos y haga clic en el icono de la papelera para eliminar el conjunto de datos. En este tutorial se usa **contosodataset** Al quitar el conjunto de datos, se quita también el informe.

### <a name="clean-up-resources-using-azure-cli"></a>Limpieza de recursos mediante la CLI de Azure

Para quitar el grupo de recursos, use el comando [az group delete](https://docs.microsoft.com/en-us/cli/azure/group?view=azure-cli-latest#az-group-delete).

```azurecli-interactive
az group delete --name $resourceGroup
```
### <a name="clean-up-resources-using-powershell"></a>Limpieza de recursos mediante PowerShell

Para quitar el grupo de recursos, use el comando [Remove-AzureRmResourceGroup](https://docs.microsoft.com/en-us/powershell/module/azurerm.resources/remove-azurermresourcegroup). $resourceGroup se estableció en **ContosoIoTRG1** al principio de este tutorial.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name $resourceGroup
```


## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido a utilizar el enrutamiento de mensajes para enrutar los mensajes de IoT Hub a diferentes destinos mediante la realización de las tareas siguientes.  

> [!div class="checklist"]
> * Mediante la CLI de Azure o PowerShell, configurar los recursos base (una instancia de IoT Hub, una cuenta de almacenamiento, una cola de Service Bus y un dispositivo simulado).
> * Configurar los puntos de conexión y las rutas en IoT Hub para la cuenta de almacenamiento y la cola de Service Bus.
> * Crear una aplicación lógica que se desencadena y se envía un correo electrónico cuando se agrega un mensaje a la cola de Service Bus.
> * Descargar y ejecutar una aplicación que simule que un dispositivo IoT envía mensajes al centro para las diferentes opciones de enrutamiento.
> * Crear una visualización de Power BI para los datos enviados al punto de conexión predeterminado.
> * Vea los resultados...
> * ... en la cola de Service Bus y en los mensajes de correo electrónico.
> * ... en la cuenta de almacenamiento.
> * ... en la visualización de Power BI.

En el siguiente tutorial aprender a administrar el estado de un dispositivo IoT. 

> [!div class="nextstepaction"]
[Introducción a los dispositivos gemelos de Azure IoT Hub](iot-hub-node-node-twin-getstarted.md)

 <!--  [Manage the state of a device](./tutorial-manage-state.md) -->