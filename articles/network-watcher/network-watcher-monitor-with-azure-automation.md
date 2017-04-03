---

title: "Supervisión de las puertas de enlace de VPN con la solución de problemas de Azure Network Watcher | Documentos de Microsoft"
description: "En este artículo se describe cómo diagnosticar la conectividad local con Azure Automation y Network Watcher"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: b4802009a8512cb4dcb49602545c7a31969e0a25
ms.openlocfilehash: 9a6f42e9b7b737e9316dcc1ff39ea532c4b923c5
ms.lasthandoff: 03/29/2017


---

# <a name="monitor-vpn-gateways-with-network-watcher-troubleshooting"></a>Supervisión de las puertas de enlace de VPN con la solución de problemas de Network Watcher

Obtener información detallada sobre el rendimiento de la red es fundamental para proporcionar servicios de confianza a los clientes. Por lo tanto, es fundamental detectar rápidamente las condiciones de interrupción de la red y tomar medidas correctivas para mitigar los efectos de la condición de interrupción. Azure Automation permite implementar y ejecutar una tarea de forma programática a través de runbooks. El uso de Azure Automation crea una receta perfecta para realizar la supervisión continua y proactiva de la red y crear alertas.

## <a name="scenario"></a>Escenario

El escenario en la siguiente imagen es una aplicación de varios niveles, con conectividad local establecida mediante una puerta de enlace de VPN y un túnel. Asegurarse de que VPN Gateway está activo y ejecutándose es fundamental para el correcto rendimiento de aplicaciones.

Se crea un runbook con un script para comprobar el estado de la conexión de túnel de VPN, usando la API de solución de problemas de recursos para comprobar el estado del túnel de conexión. Si el estado no es correcto, se envía a los administradores un desencadenador de correo electrónico.

![Escenario de ejemplo][scenario]

En este escenario:

- Creará un runbook que llama al cmdlet `Start-AzureRmNetworkWatcherResourceTroubleshooting` para solucionar los problemas de estado de la conexión
- Vinculará una programación al runbook

## <a name="before-you-begin"></a>Antes de empezar

Antes de comenzar con este escenario, tiene que cumplir los siguientes requisitos previos:

- Tener una cuenta de Azure Automation en Azure.
- Tener un conjunto de credenciales configuradas en Azure Automation. Obtenga más información en el artículo sobre [seguridad en Azure Automation](../automation/automation-security-overview.md)
- Tener un servidor SMTP válido (Office 365, correo electrónico local u otro) y las credenciales definidas en Azure Automation
- Tener una puerta de enlace de Virtual Network configurada en Azure.

> [!NOTE]
> La infraestructura que se muestra en la imagen anterior tiene fines meramente ilustrativos y no se creó con los pasos que se incluyen en este artículo.

### <a name="create-the-runbook"></a>Creación del runbook

El primer paso para configurar el ejemplo es crear el runbook. Este ejemplo utiliza una cuenta de ejecución. Para obtener información acerca de las cuentas de ejecución, visite [Autenticación de Runbooks con una cuenta de ejecución de Azure](../automation/automation-sec-configure-azure-runas-account.md#create-an-automation-account-from-the-azure-portal)

### <a name="step-1"></a>Paso 1

Navegue a Azure Automation en [Azure Portal](https://portal.azure.com) y haga clic en **Runbooks**

![Información general sobre las cuentas de Automation][1]

### <a name="step-2"></a>Paso 2

Haga clic en **Agregar un runbook** para iniciar el proceso de creación del runbook.

![Hoja de runbooks][2]

### <a name="step-3"></a>Paso 3

En **Creación rápida**, haga clic en **Crear un runbook nuevo** para crear el runbook.

![Hoja para agregar una hoja de runbook][3]

### <a name="step-4"></a>Paso 4

En este paso, se le da un nombre al runbook, en el ejemplo se llama **VPNGatewayStatus Get**. Es importante darle al runbook un nombre descriptivo y se recomienda que el nombre siga los estándares de nomenclatura de PowerShell. El tipo de runbook para este ejemplo es **PowerShell**, las otras opciones son Gráfico, Flujo de trabajo de PowerShell y Flujo de trabajo de PowerShell gráfico.

![Hoja de runbook][4]

### <a name="step-5"></a>Paso 5

En este paso se crea el runbook, el ejemplo de código siguiente proporciona todo el código necesario para el ejemplo. Los elementos en el código que contienen \<value\> tienen que reemplazarse con los valores de la suscripción.

Use el código siguiente y haga clic en **Guardar**

```PowerShell
# Get credentials for Office 365 account
$MyCredential = "Office 365 account"
$Cred = Get-AutomationPSCredential -Name $MyCredential

# Get the connection "AzureRunAsConnection "
$connectionName = "AzureRunAsConnection"
$servicePrincipalConnection=Get-AutomationConnection -Name $connectionName
$subscriptionId = "<subscription id>"
"Logging in to Azure..."
Add-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $servicePrincipalConnection.TenantId `
    -ApplicationId $servicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint
"Setting context to a specific subscription"
Set-AzureRmContext -SubscriptionId $subscriptionId

$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" }
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName
$connection = Get-AzureRmVirtualNetworkGatewayConnection -Name "2to3" -ResourceGroupName "testrg"
$sa = New-AzureRmStorageAccount -Name "contosoexamplesa" -SKU "Standard_LRS" -ResourceGroupName "testrg" -Location "WestCentralUS"
$result = Start-AzureRmNetworkWatcherResourceTroubleshooting -NetworkWatcher $networkWatcher -TargetResourceId $connection.Id -StorageId $sa.Id -StoragePath "$($sa.PrimaryEndpoints.Blob)logs"


if($result.code -ne "Healthy")
    {
        $Body = "Connection for ${vpnconnectionName} is: $($result.code). View the logs at $($sa.PrimaryEndpoints.Blob)logs to learn more."
        $subject = "${connectionname} Status"
        Send-MailMessage `
        -To 'admin@contoso.com' `
        -Subject $subject `
        -Body $Body `
        -UseSsl `
        -Port 587 `
        -SmtpServer 'smtp.office365.com' `
        -From "${$username}" `
        -BodyAsHtml `
        -Credential $Cred
    }
else
    {
    Write-Output ("Connection Status is: $($result.connectionStatus)")
    }
```

![Paso 5][5]

### <a name="step-6"></a>Paso 6

Una vez guardado el runbook, tiene que haber una programación vinculada al mismo para automatizar su inicio. Para iniciar el proceso, haga clic en **Programación**.

![Paso 6][6]

## <a name="link-a-schedule-to-the-runbook"></a>Vinculará una programación al runbook

Tiene que crear una nueva programación. Haga clic en **Vincular una programación a su runbook**.

![Paso 7][7]

### <a name="step-1"></a>Paso 1

En la hoja **Programación**, haga clic en **Crear una programación nueva**

![Paso 8][8]

### <a name="step-2"></a>Paso 2

En la hoja **Nueva programación** rellene la información de programación. La lista siguiente enumera los valores que se pueden establecer:

- **Nombre**: el nombre descriptivo de la programación.
- **Description**: descripción de la programación.
- **Se inicia el**: este valor es una combinación de fecha, hora y zona horaria que componen el horario en el que se desencadena la programación.
- **Periodicidad**: este valor determina la repetición de programaciones.  Los valores posibles son **Una vez** o **Periódica**.
- **Repetir cada**: el intervalo de periodicidad de la programación en horas, días, semanas o meses.
- **Configurar expiración**: el valor determina si la programación debería expirar o no. Se puede establecer en **Sí** o **No**. Si se elige Sí es necesario proporcionar una fecha y hora válidas.

> [!NOTE]
> Si necesita que un runbook se ejecute con más frecuencia que cada hora, tiene que crear varias programaciones en intervalos diferentes (es decir, 15, 30, 45 minutos después de la hora)

![Paso 9:][9]

### <a name="step-3"></a>Paso 3

Haga clic en Guardar para guardar la programación del runbook.

![Paso 10][10]

## <a name="next-steps"></a>Pasos siguientes

Ahora que comprende mejor cómo integrar la solución de problemas de Network Watcher con Azure Automation, aprenda a desencadenar capturas de paquetes en las alertas de máquina virtual consultando el artículo en el que se describe cómo [crear una captura de paquetes desencadenada mediante alertas con Azure Network Watcher](network-watcher-alert-triggered-packet-capture.md).

<!-- images -->
[scenario]: ./media/network-watcher-monitor-with-azure-automation/scenario.png
[1]: ./media/network-watcher-monitor-with-azure-automation/figure1.png
[2]: ./media/network-watcher-monitor-with-azure-automation/figure2.png
[3]: ./media/network-watcher-monitor-with-azure-automation/figure3.png
[4]: ./media/network-watcher-monitor-with-azure-automation/figure4.png
[5]: ./media/network-watcher-monitor-with-azure-automation/figure5.png
[6]: ./media/network-watcher-monitor-with-azure-automation/figure6.png
[7]: ./media/network-watcher-monitor-with-azure-automation/figure7.png
[8]: ./media/network-watcher-monitor-with-azure-automation/figure8.png
[9]: ./media/network-watcher-monitor-with-azure-automation/figure9.png
[10]: ./media/network-watcher-monitor-with-azure-automation/figure10.png

