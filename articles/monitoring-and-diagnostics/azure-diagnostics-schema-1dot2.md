---
title: "Esquema de configuración de Diagnósticos de Azure 1.2 | Microsoft Docs"
description: "SOLO es pertinente si utiliza Azure SDK 2.5 con Azure Virtual Machines, conjuntos de escalado de máquinas virtuales, Service Fabric o Cloud Services."
services: monitoring-and-diagnostics
documentationcenter: .net
author: rboucher
manager: carmonm
editor: 
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/15/2017
ms.author: robb
ms.openlocfilehash: 1e9cc6d0950945df8c4fba74d8e1f6196be224f0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-diagnostics-12-configuration-schema"></a>Esquema de configuración de Diagnósticos de Azure 1.2
> [!NOTE]
> Diagnósticos de Azure es el componente que se usa para recopilar contadores de rendimiento y otras estadísticas de Azure Virtual Machines, conjuntos de escalado de máquinas virtuales, Service Fabric y Cloud Services.  Esta página solo es pertinente si está usando uno de estos servicios.
>

Diagnósticos de Azure se usa con otros productos de diagnósticos de Microsoft, como Azure Monitor, Application Insights y Log Analytics.

Este esquema define los valores posibles que puede usar para inicializar valores de configuración de diagnóstico cuando se inicia el monitor de diagnóstico.  


 Descargue la definición del esquema del archivo de configuración público ejecutando el comando de PowerShell siguiente:  

```PowerShell  
(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File –Encoding utf8 -FilePath 'C:\temp\WadConfig.xsd'  
```  

 Para más información sobre Diagnósticos de Azure, consulte [Habilitación de Diagnósticos en Azure Cloud Services y Azure Virtual Machines](http://azure.microsoft.com/documentation/articles/cloud-services-dotnet-diagnostics/).  

## <a name="example-of-the-diagnostics-configuration-file"></a>Ejemplo del archivo de configuración de diagnóstico  
 En el ejemplo siguiente se muestra un archivo de configuración de diagnóstico típico:  

```xml
<?xml version="1.0" encoding="utf-8"?>  
<PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">  
  <WadCfg>  
    <DiagnosticMonitorConfiguration overallQuotaInMB="10000">  
      <PerformanceCounters scheduledTransferPeriod="PT1M">  
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT1M" unit="percent" />  
      </PerformanceCounters>  
      <Directories scheduledTransferPeriod="PT5M">  
        <IISLogs containerName="iislogs" />  
        <FailedRequestLogs containerName="iisfailed" />  
        <DataSources>  
          <DirectoryConfiguration containerName="mynewprocess">  
            <Absolute path="C:\MyNewProcess" expandEnvironment="false" />  
          </DirectoryConfiguration>  
          <DirectoryConfiguration containerName="badapp">  
            <Absolute path="%SYSTEMDRIVE%\BadApp" expandEnvironment="true" />  
          </DirectoryConfiguration>  
          <DirectoryConfiguration containerName="goodapp">  
            <LocalResource name="Skippy" relativePath="..\PeanutButter"/>  
          </DirectoryConfiguration>  
        </DataSources>  
      </Directories>  
      <EtwProviders>  
        <EtwEventSourceProviderConfiguration provider="MyProviderClass" scheduledTransferPeriod="PT5M">  
          <Event id="0"/>  
          <Event id="1" eventDestination="errorTable"/>  
          <DefaultEvents />  
        </EtwEventSourceProviderConfiguration>  
        <EtwManifestProviderConfiguration provider="5974b00b-84c2-44bc-9e58-3a2451b4e3ad" scheduledTransferLogLevelFilter="Information" scheduledTransferPeriod="PT2M">  
          <Event id="0"/>  
          <DefaultEvents eventDestination="defaultTable"/>  
        </EtwManifestProviderConfiguration>  
      </EtwProviders>  
      <WindowsEventLog scheduledTransferPeriod="PT5M">  
        <DataSource name="System!*[System[Provider[@Name='Microsoft Antimalware']]]"/>  
        <DataSource name="System!*[System[Provider[@Name='NTFS'] and (EventID=55)]]" />  
        <DataSource name="System!*[System[Provider[@Name='disk'] and (EventID=7 or EventID=52 or EventID=55)]]" />  
      </WindowsEventLog>  
      <CrashDumps containerName="wad-crashdumps" directoryQuotaPercentage="30" dumpType="Mini">  
        <CrashDumpConfiguration processName="mynewprocess.exe" />  
        <CrashDumpConfiguration processName="badapp.exe"/>  
      </CrashDumps>  
    </DiagnosticMonitorConfiguration>  
  </WadCfg>  
</PublicConfig>  

```  

## <a name="diagnostics-configuration-namespace"></a>Espacio de nombres de configuración de Diagnósticos  
 El espacio de nombres XML del archivo de configuración de diagnóstico es:  

```  
http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration  
```  

## <a name="publicconfig-element"></a>Elemento PublicConfig  
 Elemento de nivel superior del archivo de configuración de diagnósticos En la siguiente tabla se describen los elementos del archivo de configuración.  

|Nombre del elemento|Descripción|  
|------------------|-----------------|  
|**WadCfg**|Obligatorio. Opciones de configuración para los datos de telemetría que se van a recopilar.|  
|**StorageAccount**|El nombre de la cuenta de Azure Storage en la que se van a almacenar los datos. También se puede especificar como un parámetro al ejecutar el cmdlet Set-AzureServiceDiagnosticsExtension.|  
|**LocalResourceDirectory**|El directorio en la máquina virtual que va a utilizar Monitoring Agent para almacenar los datos del evento. Si no se establece, se usa el directorio predeterminado:<br /><br /> Para un rol de trabajo o web: `C:\Resources\<guid>\directory\<guid>.<RoleName.DiagnosticStore\`<br /><br /> Para una máquina virtual: `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<WADVersion>\WAD<WADVersion>`<br /><br /> Los atributos necesarios son:<br /><br /> -                      **path**: el directorio del sistema que va a usar Diagnósticos de Azure.<br /><br /> -                      **expandEnvironment**: controla si se expanden las variables de entorno en el nombre de ruta de acceso.|  

## <a name="wadcfg-element"></a>Elemento WadCFG  
Define las opciones de configuración para los datos de telemetría que se van a recopilar. En la tabla siguiente se describen los elementos secundarios:  

|Nombre del elemento|Descripción|  
|------------------|-----------------|  
|**DiagnosticMonitorConfiguration**|Obligatorio. Los atributos opcionales son:<br /><br /> -                     **overallQuotaInMB**: la cantidad máxima de espacio en disco local que se puede utilizar en los distintos tipos de datos de diagnóstico recopilados por Diagnósticos de Azure. La configuración predeterminada es 5120 MB.<br /><br /> -                     **useProxyServer**: configura Diagnósticos de Azure para utilizar la configuración del servidor proxy tal como se estableció en la configuración de Internet Explorer.|  
|**CrashDumps**|Habilite la recopilación de volcados de memoria. Los atributos opcionales son:<br /><br /> -                     **containerName**: el nombre del contenedor de blobs en la cuenta de Azure Storage que se usará para almacenar los volcados de memoria.<br /><br /> -                     **crashDumpType**: configura Diagnósticos de Azure para recopilar volcados de memoria parciales o completos.<br /><br /> -                     **directoryQuotaPercentage**: configura el porcentaje de **overallQuotaInMB** que se va a reservar para los volcados de memoria en la máquina virtual.|  
|**DiagnosticInfrastructureLogs**|Habilite la recopilación de registros generados por Diagnósticos de Azure. Los registros de infraestructura de diagnóstico son útiles para solucionar problemas del mismo sistema de diagnóstico. Los atributos opcionales son:<br /><br /> -                     **scheduledTransferLogLevelFilter**: configura el nivel de gravedad mínimo de los registros recopilados.<br /><br /> -                     **scheduledTransferPeriod**: el intervalo existente entre las transferencias programadas en el almacenamiento, redondeado al minuto más cercano. El valor es un [“tipo de datos de duración” XML](http://www.w3schools.com/schema/schema_dtypes_date.asp).|  
|**Directorios**|Habilita la recopilación del contenido de un directorio, los registros de solicitud de acceso de error de IIS o los registros de IIS. Atributo opcional:<br /><br /> **scheduledTransferPeriod**: el intervalo existente entre las transferencias programadas en el almacenamiento, redondeado al minuto más cercano. El valor es un [“tipo de datos de duración” XML](http://www.w3schools.com/schema/schema_dtypes_date.asp).|  
|**EtwProviders**|Configura la recopilación de eventos ETW en EventSource o el manifiesto de ETW en función de los proveedores.|  
|**Métricas**|Este elemento le permite generar una tabla de contadores de rendimiento optimizada para las consultas rápidas. Cada contador de rendimiento que se define en el elemento **PerformanceCounters** se almacena en la tabla de métricas además de la tabla de contadores de rendimiento. Atributo necesario:<br /><br /> **resourceId**: es el identificador de recurso de la máquina virtual en donde se va a implementar Diagnósticos de Azure. Obtenga el valor de **resourceID** en [Azure Portal](https://portal.azure.com). Seleccione **Examinar** -> **Grupos de recursos** -> **<Nombre\>**. Haga clic en el icono **Propiedades** y copie el valor del campo **ID**.|  
|**PerformanceCounters**|Habilita la recopilación de contadores de rendimiento. Atributo opcional:<br /><br /> **scheduledTransferPeriod**: el intervalo existente entre las transferencias programadas en el almacenamiento, redondeado al minuto más cercano. El valor es un ["tipo de datos de duración" XML](http://www.w3schools.com/schema/schema_dtypes_date.asp).|  
|**WindowsEventLog**|Habilita la recopilación de registros de eventos de Windows. Atributo opcional:<br /><br /> **scheduledTransferPeriod**: el intervalo existente entre las transferencias programadas en el almacenamiento, redondeado al minuto más cercano. El valor es un ["tipo de datos de duración" XML](http://www.w3schools.com/schema/schema_dtypes_date.asp).|  

## <a name="crashdumps-element"></a>Elemento CrashDumps  
 Habilita la recopilación de volcados de memoria. En la tabla siguiente se describen los elementos secundarios:  

|Nombre del elemento|Descripción|  
|------------------|-----------------|  
|**CrashDumpConfiguration**|Obligatorio. Atributo necesario:<br /><br /> **processName**: el nombre del proceso para el que desea que Diagnósticos de Azure recopile un volcado de memoria.|  
|**crashDumpType**|Configura Diagnósticos de Azure para recopilar volcados de memoria parciales o completos.|  
|**directoryQuotaPercentage**|Configura el porcentaje de **overallQuotaInMB** que se va a reservar para los volcados de memoria en la máquina virtual.|  

## <a name="directories-element"></a>Elemento Directories  
 Habilita la recopilación del contenido de un directorio, los registros de solicitud de acceso de error de IIS o los registros de IIS. En la tabla siguiente se describen los elementos secundarios:  

|Nombre del elemento|Descripción|  
|------------------|-----------------|  
|**DataSources**|Una lista de directorios que se van a supervisar.|  
|**FailedRequestLogs**|La inclusión de este elemento en la configuración habilita la recopilación de registros sobre las solicitudes erróneas en un sitio o aplicación de IIS. También debe habilitar las opciones de seguimiento en **system.WebServer** de **Web.config**.|  
|**IISLogs**|La inclusión de este elemento en la configuración habilita la recopilación de registros de IIS:<br /><br /> **containerName**: el nombre del contenedor de blobs en la cuenta de Azure Storage que se usará para almacenar los registros de IIS.|  

## <a name="datasources-element"></a>Elemento DataSources  
 Una lista de directorios que se van a supervisar. En la tabla siguiente se describen los elementos secundarios:  

|Nombre del elemento|Descripción|  
|------------------|-----------------|  
|**DirectoryConfiguration**|Obligatorio. Atributo necesario:<br /><br /> **containerName**: el nombre del contenedor de blobs en la cuenta de Azure Storage que se usará para almacenar los archivos de registro.|  

## <a name="directoryconfiguration-element"></a>Elemento DirectoryConfiguration  
 El elemento **DirectoryConfiguration** puede incluir el elemento **Absolute** o el elemento **LocalResource**, pero no ambos. En la tabla siguiente se describen los elementos secundarios:  

|Nombre del elemento|Descripción|  
|------------------|-----------------|  
|**Absolute**|La ruta de acceso absoluta al directorio que se va a supervisar. Los atributos siguientes son necesarios:<br /><br /> -                     **Path**: la ruta de acceso absoluta al directorio que se va a supervisar.<br /><br /> -                      **expandEnvironment**: configura si se expanden las variables de entorno en Path.|  
|**LocalResource**|La ruta de acceso relativa a un recurso local que se va a supervisar. Los atributos necesarios son:<br /><br /> -                     **Name**: el recurso local que contiene el directorio que se va a supervisar<br /><br /> -                     **relativePath**: la ruta de acceso relativa al nombre que contiene el directorio que se va a supervisar|  

## <a name="etwproviders-element"></a>Elemento EtwProviders  
 Configura la recopilación de eventos ETW en EventSource o el manifiesto de ETW en función de los proveedores. En la tabla siguiente se describen los elementos secundarios:  

|Nombre del elemento|Descripción|  
|------------------|-----------------|  
|**EtwEventSourceProviderConfiguration**|Configura la recopilación de eventos generados a partir de la [clase EventSource](http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx). Atributo necesario:<br /><br /> **provider**: el nombre de clase del evento EventSource.<br /><br /> Los atributos opcionales son:<br /><br /> -                     **scheduledTransferLogLevelFilter**: el nivel de gravedad mínimo para transferir a la cuenta de almacenamiento.<br /><br /> -                     **scheduledTransferPeriod**: el intervalo existente entre las transferencias programadas en el almacenamiento, redondeado al minuto más cercano. El valor es un [tipo de datos de duración XML](http://www.w3schools.com/schema/schema_dtypes_date.asp).|  
|**EtwManifestProviderConfiguration**|Atributo necesario:<br /><br /> **provider**: el GUID del proveedor de eventos<br /><br /> Los atributos opcionales son:<br /><br /> - **scheduledTransferLogLevelFilter**: el nivel de gravedad mínimo para transferir a la cuenta de almacenamiento.<br /><br /> -                     **scheduledTransferPeriod**: el intervalo existente entre las transferencias programadas en el almacenamiento, redondeado al minuto más cercano. El valor es un [tipo de datos de duración XML](http://www.w3schools.com/schema/schema_dtypes_date.asp).|  

## <a name="etweventsourceproviderconfiguration-element"></a>Elemento EtwEventSourceProviderConfiguration  
 Configura la recopilación de eventos generados a partir de la [clase EventSource](http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx). En la tabla siguiente se describen los elementos secundarios:  

|Nombre del elemento|Descripción|  
|------------------|-----------------|  
|**DefaultEvents**|Atributo opcional:<br /><br /> **eventDestination**: el nombre de la tabla en la que se van a almacenar los eventos|  
|**Evento**|Atributo necesario:<br /><br /> **id**: el identificador del evento.<br /><br /> Atributo opcional:<br /><br /> **eventDestination**: el nombre de la tabla en la que se van a almacenar los eventos|  

## <a name="etwmanifestproviderconfiguration-element"></a>Elemento EtwManifestProviderConfiguration  
 En la tabla siguiente se describen los elementos secundarios:  

|Nombre del elemento|Descripción|  
|------------------|-----------------|  
|**DefaultEvents**|Atributo opcional:<br /><br /> **eventDestination**: el nombre de la tabla en la que se van a almacenar los eventos|  
|**Evento**|Atributo necesario:<br /><br /> **id**: el identificador del evento.<br /><br /> Atributo opcional:<br /><br /> **eventDestination**: el nombre de la tabla en la que se van a almacenar los eventos|  

## <a name="metrics-element"></a>Elemento Metrics  
 Le permite generar una tabla de contadores de rendimiento optimizada para las consultas rápidas. En la tabla siguiente se describen los elementos secundarios:  

|Nombre del elemento|Descripción|  
|------------------|-----------------|  
|**MetricAggregation**|Atributo necesario:<br /><br /> **scheduledTransferPeriod**: el intervalo existente entre las transferencias programadas en el almacenamiento, redondeado al minuto más cercano. El valor es un [tipo de datos de duración XML](http://www.w3schools.com/schema/schema_dtypes_date.asp).|  

## <a name="performancecounters-element"></a>Elemento PerformanceCounters  
 Habilita la recopilación de contadores de rendimiento. En la tabla siguiente se describen los elementos secundarios:  

|Nombre del elemento|Descripción|  
|------------------|-----------------|  
|**PerformanceCounterConfiguration**|Los atributos siguientes son necesarios:<br /><br /> -                     **counterSpecifier**: el nombre del contador de rendimiento. Por ejemplo: `\Processor(_Total)\% Processor Time`. Para obtener una lista de contadores de rendimiento en el host, ejecute el comando `typeperf`.<br /><br /> -                     **sampleRate**: la frecuencia de muestreo del contador.<br /><br /> Atributo opcional:<br /><br /> **unit**: la unidad de medida del contador.|  

## <a name="performancecounterconfiguration-element"></a>Elemento PerformanceCounterConfiguration  
 En la tabla siguiente se describen los elementos secundarios:  

|Nombre del elemento|Descripción|  
|------------------|-----------------|  
|**annotation**|Atributo necesario:<br /><br /> **displayName**: el nombre para mostrar para el contador<br /><br /> Atributo opcional:<br /><br /> **locale**: la configuración regional que se usará al mostrar el nombre del contador|  

## <a name="windowseventlog-element"></a>Elemento WindowsEventLog  
 En la tabla siguiente se describen los elementos secundarios:  

|Nombre del elemento|Descripción|  
|------------------|-----------------|  
|**DataSource**|Los registros de eventos de Windows que se van a recopilar. Atributo necesario:<br /><br /> **name**: la consulta de XPath que describe los eventos de Windows que se van a recopilar. Por ejemplo:<br /><br /> `Application!*[System[(Level >= 3)]], System!*[System[(Level <=3)]], System!*[System[Provider[@Name='Microsoft Antimalware']]], Security!*[System[(Level >= 3]]`<br /><br /> Para recopilar todos los eventos, especifique "*".|
