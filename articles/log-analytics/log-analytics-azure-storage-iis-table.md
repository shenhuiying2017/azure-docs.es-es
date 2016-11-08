---
title: Uso de Blob Storage para IIS y Table Storage para eventos | Microsoft Docs
description: Log Analytics puede leer los registros de los servicios de Azure que escriben los diagnósticos en Table Storage o los registros de IIS Blob Storage.
services: log-analytics
documentationcenter: ''
author: bandersmsft
manager: jwhit
editor: ''

ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: banders

---
# <a name="using-blob-storage-for-iis-and-table-storage-for-events"></a>Uso de Blob Storage para IIS y Table Storage para eventos
Log Analytics puede leer los registros de los siguientes servicios que escriben los diagnósticos Table Storage o los registros de IIS en Blob Storage:

* Clústeres de Service Fabric (versión preliminar)
* Máquinas virtuales
* Roles web y de trabajo

Antes de que Log Analytics pueda recopilar los datos de estos recursos, es necesario habilitar Diagnósticos de Azure.

Una vez que se habilitan los diagnósticos, puede usar Azure Portal o PowerShell para configurar Log Analytics para recopilar los registros.

Diagnósticos de Azure es una extensión de Azure que le permite recopilar datos de diagnóstico de un rol de trabajo, un rol web o una máquina virtual en ejecución en Azure. Los datos se almacenan en una cuenta de Azure Storage para que se puedan recopilar con Log Analytics.

Para que Log Analytics recopile estos registros de Diagnóstico de Azure, deben estar en las siguientes ubicaciones:

| Tipo de registro | Tipo de recurso | La ubicación |
| --- | --- | --- |
| Registros IIS |Máquinas virtuales <br> Roles web <br> Roles de trabajo |wad-iis-logfiles (Blob Storage) |
| syslog |Máquinas virtuales |LinuxsyslogVer2v0 (Table Storage) |
| Eventos operativos de Service Fabric |Nodos de Service Fabric |WADServiceFabricSystemEventTable |
| Service Fabric Reliable Actor Events |Nodos de Service Fabric |WADServiceFabricReliableActorEventTable |
| Service Fabric Reliable Service Events |Nodos de Service Fabric |WADServiceFabricReliableServiceEventTable |
| Registros de eventos de Windows |Nodos de Service Fabric <br> Máquinas virtuales <br> Roles web <br> Roles de trabajo |WADWindowsEventLogsTable (Table Storage) |
| Registros de ETW de Windows |Nodos de Service Fabric <br> Máquinas virtuales <br> Roles web <br> Roles de trabajo |WADETWEventTable (Table Storage) |

> [!NOTE]
> Los registros de IIS de Sitios web Azure no son compatibles actualmente.
> 
> 

Para máquinas virtuales, también tiene la opción de instalar el [Microsoft Monitoring Agent](http://go.microsoft.com/fwlink/?LinkId=517269) en la máquina virtual para permitir información adicional. Además de poder analizar registros de IIS y registros de eventos, también podrá realizar análisis adicionales, como seguimiento de cambios de configuración, evaluación de SQL y evaluación de actualizaciones.

## <a name="enable-azure-diagnostics-in-a-virtual-machine-for-event-log-and-iis-log-collection"></a>Habilitación de Diagnósticos de Azure en una máquina virtual para la recopilación de registros de IIS y de eventos
Use el siguiente procedimiento para habilitar Diagnósticos de Azure en una máquina virtual para la recopilación de registros de IIS y de eventos mediante el Portal de administración de Microsoft Azure.

### <a name="to-enable-azure-diagnostics-in-a-virtual-machine-with-the-azure-management-portal"></a>Para habilitar Diagnósticos de Azure en una máquina virtual con el Portal de administración de Azure
1. Instale al agente de máquina virtual cuando cree una máquina virtual. Si la máquina virtual ya existe, compruebe que el agente de máquina virtual ya está instalado.
   
   * Si usa el nuevo Portal de administración de Azure para crear una máquina virtual, seleccione **Configuración opcional**, luego **Diagnósticos** y establezca **Estado** en **Activado**.
     
     Tras la finalización, la máquina virtual tendrá automáticamente la extensión de Diagnósticos de Azure instalada y en ejecución que será la responsable de la recopilación de datos de diagnóstico.
2. Habilite la supervisión y configure el registro de eventos en una máquina virtual existente. Puede habilitar el diagnóstico en el nivel de máquina virtual. Para activar el diagnóstico y, a continuación, configurar el registro de eventos, realice los siguientes pasos:
   
   1. Seleccione la máquina virtual.
   2. Haga clic en **Supervisión**.
   3. Haga clic en **Diagnósticos**.
   4. Establezca el **Estado** en **Activado**.
   5. Seleccione los registros de diagnóstico que desee recopilar.
   6. Haga clic en **Aceptar**.

Con PowerShell de Azure puede especificar con mayor precisión los eventos que se escriben en Almacenamiento de Azure. Consulte el artículo sobre la [recopilación de datos mediante Diagnóstico de Azure que se escriben en Table Storage o registros IIS en Blob Storage](log-analytics-azure-storage-json.md).

## <a name="enable-azure-diagnostics-in-a-web-role-for-iis-log-and-event-collection"></a>Activación de Diagnósticos de Azure en un rol web para la recopilación de eventos y registros de IIS
Consulte el artículo sobre la [Habilitación de diagnósticos en un servicio en la nube](../cloud-services/cloud-services-dotnet-diagnostics.md). Usará la información básica de ahí y personalizará los pasos para usarlos con Log Analytics.

Con el diagnóstico de Azure habilitado:

* Los registros de IIS se almacenan de forma predeterminada, con los datos transferidos en el intervalo de transferencia de scheduledTransferPeriod.
* Los registros de eventos de Windows no se transfieren de forma predeterminada.

### <a name="to-enable-diagnostics"></a>Para habilitar diagnósticos
Para habilitar los registros de eventos de Windows, o para cambiar scheduledTransferPeriod, configure Diagnósticos de Azure con el archivo de configuración XML (diagnostics.wadcfg) como se muestra en el [Paso 4: crear el archivo de configuración de Diagnósticos e instalar la extensión](../cloud-services/cloud-services-dotnet-diagnostics.md).

El siguiente archivo de configuración de ejemplo recopila los registros de IIS y todos los eventos desde los registros de aplicación y sistema:

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

Asegúrese de que ConfigurationSettings especifica una cuenta de almacenamiento, como en el ejemplo siguiente:

```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"/>
    </ConfigurationSettings>
```

Los valores de **AccountName** y **AccountKey** se encuentran en el panel de cuentas de almacenamiento de Azure Management Portal, en Administrar claves de acceso. El protocolo de la cadena de conexión debe ser **https**.

Una vez que se aplica la configuración de diagnóstico actualizada al servicio en la nube y se escribe el diagnóstico en Azure Storage, está preparado para configurar Log Analytics.

## <a name="use-the-azure-portal-to-collect-logs-from-azure-storage"></a>Uso de Azure Portal para recopilar registros de Azure Storage
Con Azure Portal puede configurar Log Analytics para recopilar los registros para los siguientes servicios de Azure:

* Clústeres de Service Fabric
* Máquinas virtuales
* Roles web y de trabajo

En Azure Portal, vaya hasta el área de trabajo de Log Analytics y realice las siguientes tareas:

1. Haga clic en *Storage accounts logs* (Registros de las cuentas de almacenamiento)
2. Haga clic en la tarea *Agregar*
3. Seleccione la cuenta de almacenamiento que contenga los registros de diagnóstico
   * Puede ser una cuenta de almacenamiento clásico o una cuenta de almacenamiento de Azure Resource Manager
4. Seleccione el tipo de datos de los cuales desea recopilar registros
   * Serán registros de IIS; eventos; Syslog (Linux); registros de ETW; eventos de Service Fabric
5. El valor de origen se rellenará automáticamente según el tipo de datos y no se puede cambiar
6. Haga clic en Aceptar para guardar la configuración

Repita los pasos 2 a 6 para los tipos de datos y cuentas de almacenamiento adicionales que quiera que recopile Log Analytics.

En aproximadamente 30 minutos podrá ver los datos de la cuenta de almacenamiento en Log Analytics. Solo verá los datos que se escriban en el almacenamiento de una vez aplicada la configuración. Log Analytics no lee los datos preexistentes de la cuenta de almacenamiento.

> [!NOTE]
> El portal no valida la existencia del origen en la cuenta de almacenamiento o si se escriben nuevos datos.
> 
> 

## <a name="enable-azure-diagnostics-in-a-virtual-machine-for-event-log-and-iis-log-collection-using-powershell"></a>Habilitación de Diagnósticos de Azure en una máquina virtual para la recopilación de registros de IIS y de eventos con PowerShell
Con PowerShell de Azure puede especificar con mayor precisión los eventos que se escriben en Almacenamiento de Azure.
Para más información, consulte [Habilitación de diagnósticos en máquinas virtuales de Azure](../virtual-machines-dotnet-diagnostics.md) .

Puede habilitar y actualizar el agente mediante el siguiente script de PowerShell.
También puede utilizar este script con una configuración de registro personalizada.
Necesitará modificar el script para establecer la cuenta de almacenamiento, el nombre del servicio y el nombre de máquina virtual.
El script usa cmdlets para máquinas virtuales clásicas.

Revise el siguiente ejemplo de script, cópielo, modifíquelo según sea necesario, guarde el ejemplo como un archivo de script de PowerShell y, a continuación, ejecute el script.

```
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
```

## <a name="next-steps"></a>Pasos siguientes
* [Use archivos JSON de Blob Storage](log-analytics-azure-storage-json.md) para leer los registros de los servicios de Azure que escriban diagnósticos en Blob Storage en formato JSON.
* [Incorporación de soluciones de Log Analytics desde la galería de soluciones](log-analytics-add-solutions.md) para más información sobre los datos.
* [Búsquedas de registros en Log Analytics](log-analytics-log-searches.md) para analizar los datos.

<!--HONumber=Oct16_HO2-->


