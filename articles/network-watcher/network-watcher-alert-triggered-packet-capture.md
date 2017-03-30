---
title: "Uso de la captura de paquetes para realizar la supervisión proactiva de redes con Azure Functions | Microsoft Docs"
description: "En este artículo se describe cómo crear una captura de paquetes desencadenada mediante alertas con Azure Network Watcher."
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: 75e6e7c4-b3ba-4173-8815-b00d7d824e11
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 5f31b24a0d46b9d557a55b3c9d0cd7748ecb9c33
ms.lasthandoff: 03/21/2017


---
# <a name="use-packet-capture-to-do-proactive-network-monitoring-with-azure-functions"></a>Uso de la captura de paquetes para realizar la supervisión proactiva de redes con Azure Functions

La captura de paquetes de Network Watcher crea sesiones de captura para realizar el seguimiento del tráfico dentro y fuera de una máquina virtual. El archivo de captura puede tener un filtro que se define para realizar el seguimiento solo del tráfico que se quiere supervisar. Estos datos se almacenan luego en un blob de almacenamiento o de forma local en la máquina invitada. Esta funcionalidad se puede iniciar de forma remota desde otros escenarios de automatización, como Azure Functions. La captura de paquetes ofrece la funcionalidad de ejecutar capturas proactivas en función de las anomalías de red definidas. Otros usos son la recopilación de estadísticas de red, la obtención de información sobre las intrusiones de red y la depuración de las comunicaciones cliente-servidor, entre otros.

Los recursos implementados en Azure se ejecutan las 24 horas, los 7 días de la semana. Usted o su personal no pueden supervisar activamente el estado de todos los recursos de manera ininterrumpida. ¿Qué ocurre si un problema se produce a las 2 a.m.?

Con Network Watcher, la característica de alertas y Functions del ecosistema de Azure, puede responder de manera proactiva a los problemas de su red con los datos y las herramientas para resolverlos.

## <a name="before-you-begin"></a>Antes de empezar

En este ejemplo, la máquina virtual está enviando más segmentos TCP de lo habitual, y le gustaría recibir alertas al respecto. Los segmentos TCP se usan como ejemplo, pero podría usar cualquier condición de alerta. Al recibir una alerta, quiere tener datos de nivel de paquete para comprender por qué la comunicación ha aumentado y así poder realizar los pasos para devolver la máquina a la comunicación normal.
En este escenario se supone que tiene una instancia existente de Network Watcher y que se va a usar un grupo de recursos con una máquina virtual válida.

[!INCLUDE [network-watcher-preview](../../includes/network-watcher-public-preview-notice.md)]

## <a name="scenario"></a>Escenario

Para automatizar este proceso, crearemos y conectaremos una alerta en nuestra máquina virtual para que se active cuando se produzca el incidente y una función de Azure para llamar en Network Watcher.

Este escenario:

* Creará una función de Azure que inicie una captura de paquetes.
* Creará una regla de alerta en una máquina virtual.
* Configurará la regla de alerta para llamar a la función de Azure.

## <a name="creating-an-azure-function-and-overview"></a>Creación de una función de Azure e información general

El primer paso es crear una función de Azure para procesar la alerta y crear una captura de paquetes. 

En la lista siguiente se muestra información general sobre el flujo de trabajo que tiene lugar.

1. Se desencadena una alerta en la máquina virtual.
1. La alerta llama a la función de Azure a través de un webhook.
1. La función de Azure procesa la alerta y se inicia una sesión de captura de paquetes de Network Watcher.
1. La captura de paquetes se ejecuta en la máquina virtual y recopila el tráfico. 
1. El archivo de captura se carga en una cuenta de almacenamiento para su revisión y diagnóstico. 

La creación de una función de Azure se puede realizar en el portal siguiendo las instrucciones del artículo [Creación de su primera función de Azure](../azure-functions/functions-create-first-azure-function.md). En este ejemplo, elegimos una función de tipo HttpTrigger-PowerShell. Para este ejemplo se requieren personalizaciones, que se explican en los siguientes pasos:

![ejemplo de functions][functions1]

> [!NOTE]
> La plantilla de PowerShell es experimental y no es totalmente compatible.

## <a name="adding-modules"></a>Incorporación de módulos

Para usar cmdlets Network Watcher, de PowerShell, el último módulo de PowerShell debe cargarse en Function App.

1. Una vez instalados los últimos módulos de Azure PowerShell en su equipo local, ejecute el siguiente comando de PowerShell:

    ```powershell
    (Get-Module AzureRM.Network).Path
    ```

    De este modo, obtendrá la ruta de acceso local de los módulos de Azure PowerShell. Estas carpetas se usan en un paso posterior. A continuación, se muestran los módulos usados:

    * AzureRM.Network

    * AzureRM.Profile

    * AzureRM.Resources

    ![carpetas de powershell][functions5]

1. Vaya a **Configuración de Function App** > **Ir al Editor de App Service**.

    ![kudu de functions][functions2]

1. Haga clic con el botón derecho en la carpeta AlertPacketCapturePowershell y cree una carpeta llamada **azuremodules**. Siga creando subcarpetas para cada uno de los módulos requeridos.

    ![kudu de functions][functions3]

    * AzureRM.Network

    * AzureRM.Profile

    * AzureRM.Resources

1. Haga clic con el botón derecho en la subcarpeta **AzureRM.Network** y haga clic en **Cargar archivos**. Vaya a la ubicación donde se encuentran instalados sus módulos de Azure y, en la carpeta AzureRM.Network local, seleccione todos los archivos de esta y haga clic en **Aceptar**.  Repita estos pasos para AzureRM.Profile y AzureRM.Resources.

    ![cargar archivos][functions6]

1. Cuando haya finalizado, cada carpeta debe tener los archivos del módulo de PowerShell del equipo local.

    ![archivos de powershell][functions7]

## <a name="authentication"></a>Autenticación

Para usar los cmdlets de PowerShell, debe autenticarse. La autenticación debe configurarse en Function App. Para ello, se configuran las variables de entorno y es necesario cargar en Function App un archivo de clave cifrada.

> [!note]
> Este escenario proporciona solo un ejemplo de cómo para implementar la autenticación con Azure Functions, hay otras maneras de hacerlo.

### <a name="encrypted-credentials"></a>Credenciales cifradas

El siguiente script de PowerShell crea un archivo de clave llamado **PassEncryptKey.key** y proporciona una versión cifrada de la contraseña suministrada.  Esta contraseña es la misma que se define para la aplicación de Azure AD usada para la autenticación.

```powershell
#variables
$keypath = "C:\temp\PassEncryptKey.key"
$AESKey = New-Object Byte[] 32
$Password = "<insert a password here>"

#keys
[Security.Cryptography.RNGCryptoServiceProvider]::Create().GetBytes($AESKey) 
Set-Content $keypath $AESKey

#get encrypted password
$secPw = ConvertTo-SecureString -AsPlainText $Password -Force
$AESKey = Get-content $KeyPath
$Encryptedpassword = $secPw | ConvertFrom-SecureString -Key $AESKey
$Encryptedpassword
```

En el Editor de App Service de Function App, cree una carpeta llamada **keys** en **AlertPacketCapturePowerShell** y cargue el archivo **PassEncryptKey.key** creado por el ejemplo anterior de PowerShell.

![clave de functions][functions8]

### <a name="retrieving-values-for-environment-variables"></a>Recuperación de valores para variables de entorno

La configuración final requerida consiste en instalar las variables de entorno necesarias para tener acceso a los valores de la autenticación. A continuación, se muestra una lista de las variables de entorno que se crean.

* AzureClientID

* AzureTenant

* AzureCredPassword


#### <a name="azureclientid"></a>AzureClientID

El id. de cliente es el id. de aplicación de una aplicación de Azure Active Directory.

1. Si aún no cuenta con una aplicación que pueda usar, ejecute el siguiente ejemplo para crear una.

    ```powershell
    $app = New-AzureRmADApplication -DisplayName "ExampleAutomationAccount_MF" -HomePage "https://exampleapp.com" -IdentifierUris "https://exampleapp1.com/ExampleFunctionsAccount" -Password "<same password as defined earlier>"
    New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId
    Start-Sleep 15
    New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $app.ApplicationId
    ```

   > [!NOTE]
   > La contraseña utilizada al crearse la aplicación debe ser la misma que se creó anteriormente al guardarse el archivo de clave.

1. En Azure Portal, vaya a **Suscripciones** > Elija la suscripción que usará > **Control de acceso (IAM)**.

    ![iam de functions][functions9]

1. Elija la cuenta que usará y haga clic en Propiedades. Copie el id. de aplicación.

    ![id. de aplicación de functions][functions10]

#### <a name="azuretenant"></a>AzureTenant

El id. de inquilino se obtiene ejecutando el ejemplo siguiente de PowerShell:

```powershell
(Get-AzureRmSubscription -SubscriptionName "<subscriptionName>").TenantId
```

#### <a name="azurecredpassword"></a>AzureCredPassword

El valor de la variable de entorno AzureCredPassword es el valor resultante de la ejecución del ejemplo siguiente de PowerShell. Se trata del mismo ejemplo mostrado en la sección **Credenciales cifradas** anterior. El valor necesario es el resultado de la variable `$Encryptedpassword`.  Esta es la contraseña de entidad de servicio que ciframos mediante el script de PowerShell.

```powershell
#variables
$keypath = "C:\temp\PassEncryptKey.key"
$AESKey = New-Object Byte[] 32
$Password = "<insert a password here>"

#keys
[Security.Cryptography.RNGCryptoServiceProvider]::Create().GetBytes($AESKey) 
Set-Content $keypath $AESKey

#get encrypted password
$secPw = ConvertTo-SecureString -AsPlainText $Password -Force
$AESKey = Get-content $KeyPath
$Encryptedpassword = $secPw | ConvertFrom-SecureString -Key $AESKey
$Encryptedpassword
```

### <a name="storing-the-environment-variables"></a>Almacenamiento de las variables de entorno

1. Vaya a Function App, haga clic en **Configuración de Function App** > **Configurar las opciones de la aplicación**

    ![configurar las opciones de la aplicación][functions11]

1. Agregue las variables de entorno y sus valores a la configuración de la aplicación y haga clic en **Guardar**

    ![configuración de aplicaciones][functions12]

## <a name="processing-the-alert-and-starting-a-packet-capture-session"></a>Procesamiento de la alerta e inicio de una sesión de captura de paquetes

Ahora es el momento de realizar llamadas en Network Watcher dentro de la función de Azure. La implementación de esta función es diferente, según los requisitos. Sin embargo, el flujo general del código es el siguiente:

1. Procesar los parámetros de entrada.
2. Consultar las capturas de paquetes existentes para comprobar los límites y resolver los conflictos de nombres.
3. Crear una captura de paquetes con los parámetros adecuados.
4. Sondear la captura de paquetes periódicamente hasta que finalice.
5. Notificar al usuario que la sesión de captura de paquetes ha finalizado.

El ejemplo siguiente es con PowerShell, que se puede usar en la función de Azure. Hay valores que deben reemplazarse por subscriptionId, resourceGroupName y storageAccountName.

```powershell
#Import Azure PowerShell modules required to make calls to Network Watcher
Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\AzureRM.Profile\AzureRM.Profile.psd1" -Global
Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\AzureRM.Network\AzureRM.Network.psd1" -Global
Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\AzureRM.Resources\AzureRM.Resources.psd1" -Global

#Process Alert Request Body
$requestBody = Get-Content $req -Raw | ConvertFrom-Json

#Storage Account Id to save captures in
$storageaccountid = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}"

#Packet Capture Vars
$packetcapturename = "PSAzureFunction"
$packetCaptureLimit = 10
$packetCaptureDuration = 10

#Credentials
$tenant = $env:AzureTenant
$pw = $env:AzureCredPassword
$clientid = $env:AzureClientId
$keypath = "D:\home\site\wwwroot\AlertPacketCapturePowerShell\keys\PassEncryptKey.key"

#Authentication
$secpassword = $pw | ConvertTo-SecureString -Key (Get-Content $keypath)
$credential = New-Object System.Management.Automation.PSCredential ($clientid, $secpassword)
Add-AzureRMAccount -ServicePrincipal -Tenant $tenant -Credential $credential #-WarningAction SilentlyContinue | out-null


#Get the VM that fired the Alert
if($requestBody.context.resourceType -eq "Microsoft.Compute/virtualMachines")
{
    Write-Output ("Subscription ID: {0}" -f $requestBody.context.subscriptionId)
    Write-Output ("Resource Group:  {0}" -f $requestBody.context.resourceGroupName)
    Write-Output ("Resource Name:  {0}" -f $requestBody.context.resourceName)
    Write-Output ("Resource Type:  {0}" -f $requestBody.context.resourceType)

    #Get the Network Watcher in the VM's Region
    $nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq $requestBody.context.resourceRegion}
    $networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName

    #Get existing packetCaptures
    $packetCaptures = Get-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher

    #Remove existing packet capture created by the function if it exists
    $packetCaptures | %{if($_.Name -eq $packetCaptureName)
    { 
        Remove-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName $packetCaptureName
    }}

    #Initiate Packet Capture on the VM that fired the alert
    if ((Get-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher).Count -lt $packetCaptureLimit){
        echo "Initiating Packet Capture"
        New-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -TargetVirtualMachineId $requestBody.context.resourceId -PacketCaptureName $packetCaptureName -StorageAccountId $storageaccountid -TimeLimitInSeconds $packetCaptureDuration
        Out-File -Encoding Ascii -FilePath $res -inputObject "Packet Capture created on ${requestBody.context.resourceID}"
    }
} 
``` 

Cuando haya creado la función, debe configurar la alerta para que llame a la dirección URL asociada a la función. Para obtener este valor, haga clic en **</> Obtención de dirección URL de la función** 

![búsqueda de la dirección url 1 de la función][functions13]

Copie la dirección URL de la función de su instancia de Function App.

![búsqueda de la dirección url 2 de la función][2]

Si necesita propiedades personalizadas en la carga útil de la solicitud POST del webhook, consulte [Configuración de un webhook en una alerta de métrica de Azure](../monitoring-and-diagnostics/insights-webhooks-alerts.md).

## <a name="configure-an-alert-on-a-vm"></a>Configuración de una alerta en una máquina virtual

Se pueden configurar alertas para que avisen a los usuarios cuando una métrica determinada supere un umbral que se le ha asignado. En este ejemplo, la alerta se encuentra en los segmentos TCP enviados, pero se puede activar para muchas otras métricas. En este ejemplo, se configura una alerta para llamar a un webhook que llama a la función.

### <a name="create-the-alert-rule"></a>Creación de la regla de alerta

Vaya a una máquina virtual existente y agregue una regla de alerta. Se puede encontrar documentación más detallada sobre la configuración de alertas en [Creación de alertas en Azure Monitor para servicios de Azure - Azure Portal](../monitoring-and-diagnostics/insights-alerts-portal.md). 

![agregar regla de alerta de vm a una máquina virtual][1]

> [!NOTE]
> Algunas métricas no están habilitadas de forma predeterminada. Para encontrar más información sobre cómo habilitar la adición de métricas, visite [Habilitación de supervisión y diagnóstico](../monitoring-and-diagnostics/insights-how-to-use-diagnostics.md).

Por último, pegue la dirección URL del paso anterior en el cuadro de texto de webhook de la alerta. Haga clic en **Aceptar** para guardar la regla de alerta.

![pegar la dirección url en la regla de alerta][3]

## <a name="downloading-and-viewing-the-capture-file"></a>Descarga y visualización del archivo de captura

Si guarda la captura en una cuenta de almacenamiento, se puede descargar el archivo de captura a través del portal o mediante programación. Si el archivo de captura se almacena localmente, para recuperarlo se debe iniciar sesión en la máquina virtual. 

Para más instrucciones acerca de cómo descargar archivos desde cuentas de Azure Storage, consulte [Introducción a Azure Blob Storage mediante .NET](../storage/storage-dotnet-how-to-use-blobs.md). Otra herramienta que se puede utilizar es el Explorador de Storage. Encontrará más información acerca del Explorador de Storage en el siguiente vínculo: [Explorador de Storage](http://storageexplorer.com/).

Cuando se descarga la captura, puede verla con cualquier herramienta que pueda leer archivos **cap**. A continuación se muestran vínculos a dos de estas herramientas:

[Analizador de mensajes de Microsoft](https://technet.microsoft.com/en-us/library/jj649776.aspx).  
[WireShark](https://www.wireshark.org/)  

## <a name="next-steps"></a>Pasos siguientes

Para saber cómo ver las capturas de paquetes, visite [Análisis de capturas de paquetes con Wireshark](network-watcher-alert-triggered-packet-capture.md)

[1]: ./media/network-watcher-alert-triggered-packet-capture/figure1.png
[2]: ./media/network-watcher-alert-triggered-packet-capture/figure2.png
[3]: ./media/network-watcher-alert-triggered-packet-capture/figure3.png
[functions1]:./media/network-watcher-alert-triggered-packet-capture/functions1.png
[functions2]:./media/network-watcher-alert-triggered-packet-capture/functions2.png
[functions3]:./media/network-watcher-alert-triggered-packet-capture/functions3.png
[functions4]:./media/network-watcher-alert-triggered-packet-capture/functions4.png
[functions5]:./media/network-watcher-alert-triggered-packet-capture/functions5.png
[functions6]:./media/network-watcher-alert-triggered-packet-capture/functions6.png
[functions7]:./media/network-watcher-alert-triggered-packet-capture/functions7.png
[functions8]:./media/network-watcher-alert-triggered-packet-capture/functions8.png
[functions9]:./media/network-watcher-alert-triggered-packet-capture/functions9.png
[functions10]:./media/network-watcher-alert-triggered-packet-capture/functions10.png
[functions11]:./media/network-watcher-alert-triggered-packet-capture/functions11.png
[functions12]:./media/network-watcher-alert-triggered-packet-capture/functions12.png
[functions13]:./media/network-watcher-alert-triggered-packet-capture/functions13.png
