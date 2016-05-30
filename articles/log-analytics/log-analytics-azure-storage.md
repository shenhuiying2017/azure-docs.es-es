<properties
	pageTitle="Conexión de Almacenamiento de Azure con Log Analytics | Microsoft Azure"
	description="Log Analytics usa datos de los servidores de la infraestructura local o de nube. Puede recopilar datos de equipo del almacenamiento de Azure cuando son generados por Diagnósticos de Azure."
	services="log-analytics"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="log-analytics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/28/2016"
	ms.author="banders"/>

# Conexión de Almacenamiento de Azure con Log Analytics

Log Analytics usa datos de los servidores de la infraestructura local o de nube. Puede recopilar datos de equipo del almacenamiento de Azure cuando son generados por Diagnósticos de Azure.

Con los datos recopilados del almacenamiento de Azure, puede buscar rápidamente en los registros de eventos y de IIS de servicios en la nube y máquinas virtuales. También puede obtener información adicional de las máquinas virtuales instalando el agente de supervisión de Microsoft.

Las soluciones de evaluación de actualizaciones, seguimiento de cambios y evaluación de SQL trabajan todas con Microsoft Monitoring Agent para proporcionar información más detallada sobre las máquinas virtuales. Si todavía no lo ha hecho, puede [agregar soluciones de Log Analytics desde la galería de soluciones](log-analytics-add-solutions.md) cuando inicie sesión en el [portal de OMS](https://www.microsoft.com/oms/).

Para las máquinas virtuales de Azure, existen dos formas sencillas de habilitar la recopilación de datos basada en agente:

- En el Portal de administración de Microsoft Azure
- Uso de PowerShell

Cuando se usa una recopilación basada en agente para los datos de registro, debe configurar qué registros se van a recopilar en la página de configuración de administración de registros del [portal de OMS](https://www.microsoft.com/oms/).

>[AZURE.NOTE] Si configuró OMS para indexar los datos de registro mediante Diagnósticos de Azure y configura el agente para recopilar registros, los mismos registros se indexarán dos veces. Ambos orígenes de datos se le cobrarán a la tarifa normal. Si tiene instalado el agente, debe recopilar datos de registro mediante el agente y no indexar los registros recopilados por Diagnósticos de Azure.

## Portal de Microsoft Azure

Puede instalar el agente para OMS y conectar la máquina virtual de Azure donde se ejecuta mediante el [Portal de Azure](https://portal.azure.com).

### Para instalar el agente para OMS y conectar la máquina virtual con un área de trabajo

1.	Inicie sesión en el [Portal de Azure](http://portal.azure.com).
2.	Busque **Log Analytics (OMS)** y selecciónelo.
3.	En la lista de áreas de trabajo de OMS, seleccione el área donde desea que se conecte la máquina virtual de Azure. ![áreas de trabajo de oms](./media/log-analytics-azure-storage/oms-connect-azure-01.png)
4.	En **Log analytics management** (Administración de Log Analytics), haga clic en **Máquinas virtuales**. ![máquinas virtuales](./media/log-analytics-azure-storage/oms-connect-azure-02.png)
5.	En la lista de **máquinas virtuales**, seleccione la máquina virtual donde desea instalar el agente. El **estado de la conexión de OMS** de la máquina virtual indicará que **no está conectado**. ![VM no conectada](./media/log-analytics-azure-storage/oms-connect-azure-03.png)
6.	En los detalles de la máquina virtual, haga clic en **Conectar**. El agente se instala y configura automáticamente para el área de trabajo de OMS, pero el proceso puede demorar unos minutos en completarse. ![conectar VM](./media/log-analytics-azure-storage/oms-connect-azure-04.png)
7.	Cuando el agente esté instalado y conectado, verá que el estado de la **conexión de OMS** se actualizó para mostrar **This workspace** (Esta área de trabajo). ![conectada](./media/log-analytics-azure-storage/oms-connect-azure-05.png)

>[AZURE.NOTE] El [agente de VM de Azure](../virtual-machines/virtual-machines-windows-extensions-features.md) debe estar instalado para instalar automáticamente el agente para OMS. Si tiene una máquina virtual de Azure Resource Manager, no aparecerá en la lista y se deberá usar PowerShell o crear una plantilla de ARM para instalar el agente.

Algunas posibles razones por las que hay máquinas virtuales que no aparecen en el Portal de administración de Azure son las siguientes:

- Otra área de trabajo de OMS ya administra la máquina virtual.
- La máquina virtual es una máquina virtual de ARM, lo que no se admite en este momento.

## PowerShell

Si prefiere el scripting para realizar cambios en máquinas virtuales de Azure, puede habilitar al agente de supervisión de Microsoft mediante PowerShell.

Microsoft Monitoring Agent es una [extensión de máquina virtual de Azure](../virtual-machines/virtual-machines-windows-extensions-features.md) y se puede administrar con PowerShell, tal y como se muestra en los ejemplos siguientes.

En el caso de las máquinas virtuales de Azure clásicas, use el siguiente ejemplo de PowerShell:

```
Add-AzureAccount

$workspaceId="enter workspace here"
$workspaceKey="enter workspace key here"
$hostedService="enter hosted service here"

$vm = Get-AzureVM –ServiceName $hostedService
Set-AzureVMExtension -VM $vm -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionName 'MicrosoftMonitoringAgent' -Version '1.*' -PublicConfiguration "{'workspaceId':  '$workspaceId'}" -PrivateConfiguration "{'workspaceKey': '$workspaceKey' }" | Update-AzureVM -Verbose
```


En el caso de las máquinas virtuales de Azure Resource Manager, use el siguiente ejemplo de PowerShell:

```
Login-AzureRMAccount
Select-AzureSubscription -SubscriptionId "**"

$workspaceName = "your workspace name"
$VMresourcegroup = "**"
$VMresourcename = "**"

$workspace = (Get-AzureRmOperationalInsightsWorkspace).Where({$_.Name -eq $workspaceName})

if ($workspace.Name -ne $workspaceName) 
{
    Write-Error "Unable to find OMS Workspace $workspaceName. Do you need to run Select-AzureRMSubscription?"
}

$workspaceId = $workspace.CustomerId 
$workspaceKey = (Get-AzureRmOperationalInsightsWorkspaceSharedKeys -ResourceGroupName $workspace.ResourceGroupName -Name $workspace.Name).PrimarySharedKey

$vm = Get-AzureRMVM -ResourceGroupName $VMresourcegroup -Name $VMresourcename
$location = $vm.Location

Set-AzureRMVMExtension -ResourceGroupName $VMresourcegroup -VMName $VMresourcename -Name 'MicrosoftMonitoringAgent' -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionType 'MicrosoftMonitoringAgent' -TypeHandlerVersion '1.0' -Location $location -SettingString "{'workspaceId':  '$workspaceId'}" -ProtectedSettingString "{'workspaceKey': '$workspaceKey' }"


```
Al realizar la configuración con PowerShell, es necesario proporcionar el Id. del área de trabajo y la clave principal. Puede encontrar la clave principal y el identificador de área de trabajo en la página **Configuración** del portal de OMS. También puede usar PowerShell como se ilustra en el siguiente ejemplo.

![id. de área de trabajo y clave principal](./media/log-analytics-azure-storage/oms-analyze-azure-sources.png)

## Recopilación de datos mediante Diagnósticos de Azure

OMS puede analizar los datos escritos en almacenamiento de Azure por Diagnósticos de Azure. Existen dos pasos principales que realizar:

- Configurar la recopilación de datos de diagnóstico para Almacenamiento de Azure
- Configurar OMS para analizar los datos de la cuenta de almacenamiento

Los temas siguientes describen cómo habilitar la recopilación de datos de diagnóstico para Almacenamiento de Azure y, a continuación, cómo configurar OMS para analizar los datos en Almacenamiento de Azure.

Diagnósticos de Azure es una extensión de Azure que le permite recopilar datos de diagnóstico de un rol de trabajo, un rol web o una máquina virtual en ejecución en Azure. Los datos se almacenan en una cuenta de almacenamiento de Azure y, luego, se pueden usar en OMS.

>[AZURE.NOTE] Si tiene una suscripción de pago, se le cobrarán tarifas normales por el almacenamiento y las transacciones de datos cuando envíe diagnósticos a una cuenta de almacenamiento y cuando OMS lea los datos desde la cuenta de almacenamiento.

Diagnósticos de Azure puede recopilar los tipos de telemetría siguientes:

Origen de datos|Descripción
 ---|---
Registros IIS|Información sobre los sitios web de IIS.
Registros de infraestructura de diagnóstico de Azure|Información sobre los propios Diagnósticos.
Registros de solicitudes con error de IIS |Información sobre las solicitudes erróneas a un sitio o aplicación de IIS.
Registros de eventos de Windows|Información enviada al sistema de registro de eventos de Windows.
Contadores de rendimiento|Sistema operativo y contadores de rendimiento personalizados.
Volcados de memoria|Información sobre el estado del proceso en caso de bloqueo de una aplicación.
Registros de errores personalizados|Archivos creados por su aplicación o servicio.
NET EventSource|Eventos generados por su código mediante la clase [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource%28v=vs.110%29.aspx) de .NET
ETW basado en manifiesto|Eventos de ETW generados por cualquier proceso
Syslog|Eventos enviados a los demonios Syslog o Rsyslog


Actualmente, OMS puede analizar lo siguiente:

- Registros IIS de roles web y máquinas virtuales
- Registros de eventos de Windows y registros de ETW correspondientes a roles web, roles de trabajo y máquinas virtuales de Azure que ejecutan un sistema operativo Windows
- Syslog desde máquinas virtuales de Azure que ejecutan un sistema operativo Linux
- Diagnósticos registrados en Almacenamiento de blobs en formato json sobre recursos de grupo de seguridad de red, de puerta de enlace de aplicaciones y de KeyVault

Los registros deben estar en las siguientes ubicaciones:

- WADWindowsEventLogsTable (Almacenamiento de tablas): contiene información de los registros de eventos de Windows.
- WADETWEventTable (Almacenamiento de tablas): contiene información de los registros de eventos de Windows.
- WADServiceFabricSystemEventTable, WADServiceFabricReliableActorEventTable, WADServiceFabricReliableServiceEventTable (Almacenamiento de tablas): contienen información sobre los eventos de operación, actor y servicio de Service Fabric.
- wad-iis-logfiles (Almacenamiento de blobs): contiene información acerca de los registros de IIS.
- LinuxsyslogVer2v0 (Almacenamiento de tablas): contiene eventos syslog de Linux.

    >[AZURE.NOTE] Los registros de IIS de Sitios web Azure no son compatibles actualmente.

Para máquinas virtuales, también tiene la opción de instalar el [Microsoft Monitoring Agent](http://go.microsoft.com/fwlink/?LinkId=517269) en la máquina virtual para permitir información adicional. Además de poder analizar registros de IIS y registros de eventos, también podrá realizar análisis adicionales, como seguimiento de cambios de configuración, evaluación de SQL y evaluación de actualizaciones.

Puede ayudarnos a dar prioridad a otros registros para que OMS los analice; para ello, vote en nuestra [página de comentarios](http://feedback.azure.com/forums/267889-azure-log-analytics/category/88086-log-management-and-log-collection-policy).

## Activación de Diagnósticos de Azure en un rol web para la recopilación de eventos y registros de IIS

Consulte [Habilitar diagnósticos en un servicio en la nube](https://msdn.microsoft.com/library/azure/dn482131.aspx). Usará la información básica de ahí y personalizará los pasos para usarlos con OMS.

Con el diagnóstico de Azure habilitado:

- Los registros de IIS se almacenan de forma predeterminada, con los datos transferidos en el intervalo de transferencia de scheduledTransferPeriod.

- Los registros de eventos de Windows no se transfieren de forma predeterminada.

### Para habilitar diagnósticos

Para habilitar los registros de eventos de Windows o para cambiar el valor de scheduledTransferPeriod, configure Diagnósticos de Azure con el archivo de configuración XML (diagnostics.wadcfg), tal y como se explica en [Paso 2: Agregar el archivo diagnostics.wadcfg a la solución de Visual Studio](https://msdn.microsoft.com/library/azure/dn482131.aspx#BKMK_step2) y en [Paso 3: Configurar los diagnósticos para la aplicación](https://msdn.microsoft.com/library/azure/dn482131.aspx#BKMK_step3) en el tema Habilitación de diagnósticos de Azure en servicios en la nube de Azure. El siguiente archivo de configuración de ejemplo recopila los registros de IIS y todos los eventos desde los registros de aplicación y sistema:

```
    <?xml version="1.0" encoding="utf-8" ?>
    <DiagnosticMonitorConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"
          configurationChangePollInterval="PT1M"
          overallQuotaInMB="4096">

      <Directories bufferQuotaInMB="0"
         scheduledTransferPeriod="PT10M">  
        <!-- IISLogs are only relevant to Web roles -->
        <IISLogs container="wad-iis" directoryQuotaInMB="0" />
      </Directories>

      <WindowsEventLog bufferQuotaInMB="0"
         scheduledTransferLogLevelFilter="Verbose"
         scheduledTransferPeriod="PT10M">
        <DataSource name="Application!*" />
        <DataSource name="System!*" />
      </WindowsEventLog>

    </DiagnosticMonitorConfiguration>
```

En [Paso 4: Configuración del almacenamiento de los datos de diagnóstico](https://msdn.microsoft.com/library/azure/dn482131.aspx#BKMK_step4) del tema Cómo habilitar diagnósticos en un Servicio en la nube, asegúrese de que ConfigurationSettings especifica una cuenta de almacenamiento, como en el ejemplo siguiente:

```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"/>
    </ConfigurationSettings>
```

Los valores de **AccountName** y **AccountKey** se encuentran en el panel de cuentas de almacenamiento del Portal de administración de Microsoft Azure, debajo de Administrar claves de acceso. El protocolo de la cadena de conexión debe ser **https**.

Una vez que se aplica la configuración de diagnóstico actualizada al servicio en la nube y se escribe el diagnóstico en Almacenamiento de Azure, está preparado para configurar OMS.

## Habilitación de Diagnósticos de Azure en una máquina virtual para la recopilación de registros de IIS y de eventos

Use el siguiente procedimiento para habilitar Diagnósticos de Azure en una máquina virtual para la recopilación de registros de IIS y de eventos mediante el Portal de administración de Microsoft Azure.

### Para habilitar Diagnósticos de Azure en una máquina virtual con el Portal de administración de Azure

1. Instale al agente de máquina virtual cuando cree una máquina virtual. Si la máquina virtual ya existe, compruebe que el agente de máquina virtual ya está instalado.
	- Si usa el Portal de administración de Azure predeterminado para crear la máquina virtual, lleve a cabo una **Creación personalizada** y seleccione **Instalar el agente de máquina virtual**.
	- Si usa el nuevo Portal de administración de Azure para crear una máquina virtual, seleccione **Configuración opcional**, luego **Diagnósticos** y establezca **Estado** en **Activado**.

	Tras la finalización, la máquina virtual tendrá automáticamente la extensión de Diagnósticos de Azure instalada y en ejecución que será la responsable de la recopilación de datos de diagnóstico.

2. Habilite la supervisión y configure el registro de eventos en una máquina virtual existente. Puede habilitar el diagnóstico en el nivel de máquina virtual. Para activar el diagnóstico y, a continuación, configurar el registro de eventos, realice los siguientes pasos:
	1. Seleccione la máquina virtual.
	2. Haga clic en **Supervisión**.
	3. Haga clic en **Diagnósticos**.
	4. Establezca el **Estado** en **Activado**.
	5. Seleccione las métricas de diagnóstico que desee utilizar. OMS puede analizar los registros de sistema de eventos de Windows, los registros de aplicaciones de eventos de Windows y los registros de IIS.
	7. Haga clic en **Aceptar**.

Con PowerShell de Azure puede especificar con mayor precisión los eventos que se escriben en Almacenamiento de Azure. Consulte el esquema de configuración de Diagnósticos de Azure 1.2 para obtener un archivo de configuración de ejemplo y documentación detallada sobre su esquema. Asegúrese de instalar y configurar la versión 0.8.7 o posterior de Azure PowerShell siguiendo los pasos que se indica en [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md) Si tiene instalada una versión de Diagnósticos de Microsoft Azure que es anterior a la versión 1.2, no puede usar el nuevo portal para habilitar o configurar diagnósticos.

Puede habilitar y actualizar el agente mediante el siguiente script de PowerShell. También puede utilizar este script con la configuración de registro personalizada. Necesitará modificar el script para establecer la cuenta de almacenamiento, el nombre del servicio y el nombre de máquina virtual.

### Para habilitar diagnósticos en una máquina virtual con PowerShell de Azure

Revise el siguiente ejemplo de script, cópielo, modifíquelo según sea necesario, guarde el ejemplo como un archivo de script de PowerShell y, a continuación, ejecute el script.


	#Connect to Azure
	Add-AzureAccount

	# settings to change:
	$wad_storage_account_name = "myStorageAccount"
	$service_name = "myService"
	$vm_name = "myVM"

	#Construct Azure Diagnostics public config and convert to config format

	# Collect just system error events:
	$wad_xml_config = "<WadCfg><DiagnosticMonitorConfiguration><WindowsEventLog scheduledTransferPeriod=""PT1M""><DataSource name=""System!* "" /></WindowsEventLog></DiagnosticMonitorConfiguration></WadCfg>"

	$wad_b64_config = [System.Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes($wad_xml_config))
	$wad_public_config = [string]::Format("{{""xmlCfg"":""{0}""}}",$wad_b64_config)

	#Construct Azure diagnostics private config

	$wad_storage_account_key = (Get-AzureStorageKey $wad_storage_account_name).Primary
	$wad_private_config = [string]::Format("{{""storageAccountName"":""{0}"",""storageAccountKey"":""{1}""}}",$wad_storage_account_name,$wad_storage_account_key)

	#Enable Diagnostics Extension for Virtual Machine

	$wad_extension_name = "IaaSDiagnostics"
	$wad_publisher = "Microsoft.Azure.Diagnostics"
	$wad_version = (Get-AzureVMAvailableExtension -Publisher $wad_publisher -ExtensionName $wad_extension_name).Version # Gets latest version of the extension

	(Get-AzureVM -ServiceName $service_name -Name $vm_name) | Set-AzureVMExtension -ExtensionName $wad_extension_name -Publisher $wad_publisher -PublicConfiguration $wad_public_config -PrivateConfiguration $wad_private_config -Version $wad_version | Update-AzureVM


## Habilitación del análisis de Almacenamiento de Azure por parte de OMS

Para habilitar el análisis de almacenamiento y configurar OMS para que lea desde la cuenta de almacenamiento de Azure con Diagnósticos de Azure, use la información que se encuentra en [Orígenes de datos en Log Analytics](log-analytics-data-sources.md#collect-data-from-azure-diagnostics).

En aproximadamente 1 hora, comenzará a ver los datos desde la cuenta de almacenamiento disponible para el análisis dentro de OMS.


## Pasos siguientes

- Consulte [Configuración del proxy y del firewall (opcional) en Log Analytics](log-analytics-proxy-firewall.md) si la organización usa un servidor proxy o un firewall para que los agentes puedan comunicarse con el servicio Log Analytics.

<!---HONumber=AcomDC_0518_2016-->