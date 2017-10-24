---
title: "Esquema de configuración de la versión 1.3 y posterior de la extensión Azure Diagnostics | Microsoft Docs"
description: "La versión 1.3 y posterior del esquema de Azure Diagnostics se incluye como parte de Microsoft Azure SDK 2.4 y posterior."
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
ms.openlocfilehash: 2ee66e0f41868d7d5411605596a22c00b5712896
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-diagnostics-13-and-later-configuration-schema"></a>Esquema de configuración de Azure Diagnostics 1.3 y posterior
> [!NOTE]
> La extensión Azure Diagnostics es el componente que se usa para recopilar los contadores de rendimiento y otras estadísticas de los siguientes recursos:
> - Máquinas virtuales de Azure 
> - Conjuntos de escalado de máquina virtual
> - Service Fabric 
> - Cloud Services 
> - Grupos de seguridad de red
> 
> Esta página solo es pertinente si está usando uno de estos servicios.

Esta página es válida para las versiones 1.3 y posterior (Azure SDK 2.4 y posterior). Las secciones de configuración más recientes incluyen comentarios para mostrar en qué versión se agregaron.  

El archivo de configuración descrito en este artículo se usa para establecer la configuración de diagnóstico al iniciar el monitor de diagnóstico.  

La extensión se usa junto con otros productos de diagnósticos de Microsoft, como Azure Monitor, Application Insights y Log Analytics.



Descargue la definición del esquema del archivo de configuración público ejecutando el comando de PowerShell siguiente:  

```powershell  
(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File –Encoding utf8 -FilePath 'C:\temp\WadConfig.xsd'  
```  

Para obtener información sobre Azure Diagnostics, consulte [este artículo sobre dicha extensión](azure-diagnostics.md).  

## <a name="example-of-the-diagnostics-configuration-file"></a>Ejemplo del archivo de configuración de diagnóstico  
 En el ejemplo siguiente se muestra un archivo de configuración de diagnóstico típico:  

```xml  
<?xml version="1.0" encoding="utf-8"?>  
<DiagnosticsConfiguration  xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">   
  <PublicConfig>  
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
          <EtwEventSourceProviderConfiguration   
                       provider="MyProviderClass"   
                       scheduledTransferPeriod="PT5M">  
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

        <Logs  bufferQuotaInMB="1024"   
             scheduledTransferPeriod="PT1M"   
             scheduledTransferLogLevelFilter="Verbose"   
             sinks="ApplicationInsights.AppLogs"/>  <!-- sinks attribute added in 1.5 -->  

        <CrashDumps containerName="wad-crashdumps" directoryQuotaPercentage="30" dumpType="Mini">  
          <CrashDumpConfiguration processName="mynewprocess.exe" />  
          <CrashDumpConfiguration processName="badapp.exe"/>  
        </CrashDumps>  

        <DockerSources> <!-- Added in 1.9 --> 
          <Stats enabled="true" sampleRate="PT1M" scheduledTransferPeriod="PT1M" />
        </DockerSources>

      </DiagnosticMonitorConfiguration>  

      <SinksConfig>   <!-- Added in 1.5 -->  
        <Sink name="ApplicationInsights">   
          <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>   
          <Channels>   
            <Channel logLevel="Error" name="Errors"  />   
            <Channel logLevel="Verbose" name="AppLogs"  />   
          </Channels>   
        </Sink>   
        <Sink name="EventHub"> <!-- Added in 1.7 -->
          <EventHub Url="https://myeventhub-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" usePublisherId="false" />
        </Sink>
        <Sink name="secondaryEventHub"> <!-- Added in 1.7 -->
          <EventHub Url="https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub" SharedAccessKeyName="SendRule" usePublisherId="false" />
        </Sink>
        <Sink name="secondaryStorageAccount"> <!-- Added in 1.7 -->
          <StorageAccount name="secondarydiagstorageaccount" endpoint="https://core.windows.net" />
        </Sink>
   </SinksConfig>

  </WadCfg>  

  <StorageAccount>diagstorageaccount</StorageAccount>
  <StorageType>TableAndBlob</StorageType> <!-- Added in 1.8 -->  
  </PublicConfig>  

  <PrivateConfig>  <!-- Added in 1.3 -->  
    <StorageAccount name="" key="" endpoint="" sasToken="{sas token}"  />  <!-- sasToken in Private config added in 1.8.1 -->  
    <EventHub Url="https://myeventhub-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" SharedAccessKey="{base64 encoded key}" />
   
    <SecondaryStorageAccounts>
       <StorageAccount name="secondarydiagstorageaccount" key="{base64 encoded key}" endpoint="https://core.windows.net" sasToken="{sas token}" />
    </SecondaryStorageAccounts>
   
    <SecondaryEventHubs>
       <EventHub Url="https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub" SharedAccessKeyName="SendRule" SharedAccessKey="{base64 encoded key}" />
    </SecondaryEventHubs>

  </PrivateConfig>  
  <IsEnabled>true</IsEnabled>  
</DiagnosticsConfiguration>  

```  

Este es el equivalente JSON del archivo de configuración XML anterior. 

PublicConfig y PrivateConfig están separados porque en la mayoría de los casos de uso de JSON se pasan como variables distintas. En estos casos se incluyen plantillas de Resource Manager, conjuntos de escalado de máquinas virtuales, PowerShell y Visual Studio. 

```json
"PublicConfig" {
    "WadCfg": {
        "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": 10000,
            "DiagnosticInfrastructureLogs": {
                "scheduledTransferLogLevelFilter": "Error"
            },
            "PerformanceCounters": {
                "scheduledTransferPeriod": "PT1M",
                "PerformanceCounterConfiguration": [
                    {
                        "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                        "sampleRate": "PT1M",
                        "unit": "percent"
                    }
                ]
            },
            "Directories": {
                "scheduledTransferPeriod": "PT5M",
                "IISLogs": {
                    "containerName": "iislogs"
                },
                "FailedRequestLogs": {
                    "containerName": "iisfailed"
                },
                "DataSources": [
                    {
                        "containerName": "mynewprocess",
                        "Absolute": {
                            "path": "C:\\MyNewProcess",
                            "expandEnvironment": false
                        }
                    },
                    {
                        "containerName": "badapp",
                        "Absolute": {
                            "path": "%SYSTEMDRIVE%\\BadApp",
                            "expandEnvironment": true
                        }
                    },
                    {
                        "containerName": "goodapp",
                        "LocalResource": {
                            "relativePath": "..\\PeanutButter",
                            "name": "Skippy"
                        }
                    }
                ]
            },
            "EtwProviders": {
                "sinks": "",
                "EtwEventSourceProviderConfiguration": [
                    {
                        "scheduledTransferPeriod": "PT5M",
                        "provider": "MyProviderClass",
                        "Event": [
                            {
                                "id": 0
                            },
                            {
                                "id": 1,
                                "eventDestination": "errorTable"
                            }
                        ],
                        "DefaultEvents": {
                        }
                    }
                ],
                "EtwManifestProviderConfiguration": [
                    {
                        "scheduledTransferPeriod": "PT2M",
                        "scheduledTransferLogLevelFilter": "Information",
                        "provider": "5974b00b-84c2-44bc-9e58-3a2451b4e3ad",
                        "Event": [
                            {
                                "id": 0
                            }
                        ],
                        "DefaultEvents": {
                        }
                    }
                ]
            },
            "WindowsEventLog": {
                "scheduledTransferPeriod": "PT5M",
                "DataSource": [
                    {
                        "name": "System!*[System[Provider[@Name='Microsoft Antimalware']]]"
                    },
                    {
                        "name": "System!*[System[Provider[@Name='NTFS'] and (EventID=55)]]"
                    },
                    {
                        "name": "System!*[System[Provider[@Name='disk'] and (EventID=7 or EventID=52 or EventID=55)]]"
                    }
                ]
            },
            "Logs": {
                "scheduledTransferPeriod": "PT1M",
                "scheduledTransferLogLevelFilter": "Verbose",
                "sinks": "ApplicationInsights.AppLogs"
            },
            "CrashDumps": {
                "directoryQuotaPercentage": 30,
                "dumpType": "Mini",
                "containerName": "wad-crashdumps",
                "CrashDumpConfiguration": [
                    {
                        "processName": "mynewprocess.exe"
                    },
                    {
                        "processName": "badapp.exe"
                    }
                ]
            }
        },
        "SinksConfig": {
            "Sink": [
                {
                    "name": "ApplicationInsights",
                    "ApplicationInsights": "{Insert InstrumentationKey}",
                    "Channels": {
                        "Channel": [
                            {
                                "logLevel": "Error",
                                "name": "Errors"
                            },
                            {
                                "logLevel": "Verbose",
                                "name": "AppLogs"
                            }
                        ]
                    }
                },
                {
                    "name": "EventHub",
                    "EventHub": {
                        "Url": "https://myeventhub-ns.servicebus.windows.net/diageventhub",
                        "SharedAccessKeyName": "SendRule",
                        "usePublisherId": false
                    }
                },
                {
                    "name": "secondaryEventHub",
                    "EventHub": {
                        "Url": "https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub",
                        "SharedAccessKeyName": "SendRule",
                        "usePublisherId": false
                    }
                },
                {
                    "name": "secondaryStorageAccount",
                    "StorageAccount": {
                        "name": "secondarydiagstorageaccount",
                        "endpoint": "https://core.windows.net"
                    }
                }
            ]
        }
    },
    "StorageAccount": "diagstorageaccount",
    "StorageType": "TableAndBlob"
}
```

```json
"PrivateConfig" {
    "storageAccountName": "diagstorageaccount",
    "storageAccountKey": "{base64 encoded key}",
    "storageAccountEndPoint": "https://core.windows.net",
    "storageAccountSasToken": "{sas token}",
    "EventHub": {
        "Url": "https://myeventhub-ns.servicebus.windows.net/diageventhub",
        "SharedAccessKeyName": "SendRule",
        "SharedAccessKey": "{base64 encoded key}"
    },
    "SecondaryStorageAccounts": {
        "StorageAccount": [
            {
                "name": "secondarydiagstorageaccount",
                "key": "{base64 encoded key}",
                "endpoint": "https://core.windows.net",
                "sasToken": "{sas token}"
            }
        ]
    },
    "SecondaryEventHubs": {
        "EventHub": [
            {
                "Url": "https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub",
                "SharedAccessKeyName": "SendRule",
                "SharedAccessKey": "{base64 encoded key}"
            }
        ]
    }
}

```

## <a name="reading-this-page"></a>Lectura de esta página  
 Las etiquetas siguientes se encuentran aproximadamente en el orden mostrado en el ejemplo anterior.  Si no ve una descripción completa donde la espera, busque en la página el elemento o atributo.  

## <a name="common-attribute-types"></a>Tipos de atributos comunes  
 El atributo **scheduledTransferPeriod** aparece en varios elementos. Es el intervalo entre las transferencias programadas en el almacenamiento, redondeado al minuto más cercano. El valor es un [“tipo de datos de duración” XML](http://www.w3schools.com/schema/schema_dtypes_date.asp).


## <a name="diagnosticsconfiguration-element"></a>Elemento DiagnosticsConfiguration  
 *Árbol: Raíz - DiagnosticsConfiguration*

Agregado en la versión 1.3.  

Elemento de nivel superior del archivo de configuración de diagnóstico.  

**Atributo**: xmlns - El espacio de nombres XML del archivo de configuración de diagnóstico es el siguiente:  
http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration  


|Elementos secundarios|Descripción|  
|--------------------|-----------------|  
|**PublicConfig**|Obligatorio. Consulte la descripción en cualquier parte de esta página.|  
|**PrivateConfig**|Opcional. Consulte la descripción en cualquier parte de esta página.|  
|**IsEnabled**|Booleano. Consulte la descripción en cualquier parte de esta página.|  

## <a name="publicconfig-element"></a>Elemento PublicConfig  
 *Árbol: Raíz - DiagnosticsConfiguration - PublicConfig*

 Describe la configuración de diagnóstico pública.  

|Elementos secundarios|Descripción|  
|--------------------|-----------------|  
|**WadCfg**|Obligatorio. Consulte la descripción en cualquier parte de esta página.|  
|**StorageAccount**|El nombre de la cuenta de Azure Storage en la que se van a almacenar los datos. También se puede especificar como un parámetro al ejecutar el cmdlet Set-AzureServiceDiagnosticsExtension.|  
|**StorageType**|Puede ser *Table*, *Blob* o *TableAndBlob*. Table es el valor predeterminado. Cuando se elige TableAndBlob, los datos de diagnóstico se escriben dos veces: una vez en cada tipo.|  
|**LocalResourceDirectory**|El directorio en la máquina virtual donde Monitoring Agent almacena los datos del evento. Si no se establece, se usa el directorio predeterminado:<br /><br /> Para un rol de trabajo o web: `C:\Resources\<guid>\directory\<guid>.<RoleName.DiagnosticStore\`<br /><br /> Para una máquina virtual: `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<WADVersion>\WAD<WADVersion>`<br /><br /> Los atributos necesarios son:<br /><br /> - **path**: el directorio del sistema que va a usar Diagnósticos de Azure.<br /><br /> - **expandEnvironment**: controla si se expanden las variables de entorno en el nombre de ruta de acceso.|  

## <a name="wadcfg-element"></a>Elemento WadCFG  
 *Árbol: Raíz - DiagnosticsConfiguration - PublicConfig - WadCFG*
 
 Identifica y configura los datos de telemetría que se van a recopilar.  


## <a name="diagnosticmonitorconfiguration-element"></a>Elemento DiagnosticMonitorConfiguration 
 *Árbol: Raíz - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration*

 Obligatorio 

|Attributes|Descripción|  
|----------------|-----------------|  
| **overallQuotaInMB** | La cantidad máxima de espacio en disco local que se puede utilizar en los distintos tipos de datos de diagnóstico que recopila Azure Diagnostics. La configuración predeterminada es 5120 MB.<br />
|**useProxyServer** | Configure Azure Diagnostics para utilizar la configuración del servidor proxy tal como se estableció en la configuración de Internet Explorer.|  

<br /> <br />

|Elementos secundarios|Descripción|  
|--------------------|-----------------|  
|**CrashDumps**|Consulte la descripción en cualquier parte de esta página.|  
|**DiagnosticInfrastructureLogs**|Habilite la recopilación de registros generados por Diagnósticos de Azure. Los registros de infraestructura de diagnóstico son útiles para solucionar problemas del mismo sistema de diagnóstico. Los atributos opcionales son:<br /><br /> - **scheduledTransferLogLevelFilter**: configura el nivel de gravedad mínimo de los registros recopilados.<br /><br /> - **scheduledTransferPeriod**: el intervalo existente entre las transferencias programadas en el almacenamiento, redondeado al minuto más cercano. El valor es un [“tipo de datos de duración” XML](http://www.w3schools.com/schema/schema_dtypes_date.asp). |  
|**Directorios**|Consulte la descripción en cualquier parte de esta página.|  
|**EtwProviders**|Consulte la descripción en cualquier parte de esta página.|  
|**Métricas**|Consulte la descripción en cualquier parte de esta página.|  
|**PerformanceCounters**|Consulte la descripción en cualquier parte de esta página.|  
|**WindowsEventLog**|Consulte la descripción en cualquier parte de esta página.| 
|**DockerSources**|Consulte la descripción en cualquier parte de esta página. | 



## <a name="crashdumps-element"></a>Elemento CrashDumps  
 *Árbol: Raíz - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - CrashDumps*
 
 Habilita la recopilación de volcados de memoria.  

|Atributos|Descripción|  
|----------------|-----------------|  
|**containerName**|Opcional. El nombre del contenedor de blobs en la cuenta de Azure Storage que se usará para almacenar los volcados de memoria.|  
|**crashDumpType**|Opcional.  Configura Diagnósticos de Azure para recopilar volcados de memoria parciales o completos.|  
|**directoryQuotaPercentage**|Opcional.  Configura el porcentaje de **overallQuotaInMB** que se va a reservar para los volcados de memoria en la máquina virtual.|  

|Elementos secundarios|Descripción|  
|--------------------|-----------------|  
|**CrashDumpConfiguration**|Obligatorio. Define los valores de configuración para cada proceso.<br /><br /> También se necesita el atributo siguiente:<br /><br /> **processName**: el nombre del proceso para el que desea que Diagnósticos de Azure recopile un volcado de memoria.|  

## <a name="directories-element"></a>Elemento Directories 
 *Árbol: Raíz - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration -  Directories*

 Habilita la recopilación del contenido de un directorio, los registros de solicitud de acceso de error de IIS o los registros de IIS.  

 Atributo **scheduledTransferPeriod** opcional. Consulte la explicación anterior.  

|Elementos secundarios|Descripción|  
|--------------------|-----------------|  
|**IISLogs**|La inclusión de este elemento en la configuración habilita la recopilación de registros de IIS:<br /><br /> **containerName**: el nombre del contenedor de blobs en la cuenta de Azure Storage que se usará para almacenar los registros de IIS.|   
|**FailedRequestLogs**|La inclusión de este elemento en la configuración habilita la recopilación de registros sobre las solicitudes erróneas en un sitio o aplicación de IIS. También debe habilitar las opciones de seguimiento en **system.WebServer** de **Web.config**.|  
|**DataSources**|Una lista de directorios que se van a supervisar.| 




## <a name="datasources-element"></a>Elemento DataSources  
 *Árbol: Raíz - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - Directories - DataSources*

 Una lista de directorios que se van a supervisar.  

|Elementos secundarios|Descripción|  
|--------------------|-----------------|  
|**DirectoryConfiguration**|Obligatorio. Atributo necesario:<br /><br /> **containerName**: el nombre del contenedor de blobs en la cuenta de Azure Storage que se usará para almacenar los archivos de registro.|  





## <a name="directoryconfiguration-element"></a>Elemento DirectoryConfiguration  
 *Árbol: Raíz - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - Directories - DataSources - DirectoryConfiguration*

 Puede incluir el elemento **Absolute** o el elemento **LocalResource**, pero no ambos.  

|Elementos secundarios|Descripción|  
|--------------------|-----------------|  
|**Absolute**|La ruta de acceso absoluta al directorio que se va a supervisar. Los atributos siguientes son necesarios:<br /><br /> - **Path**: la ruta de acceso absoluta al directorio que se va a supervisar.<br /><br /> - **expandEnvironment**: configura si se expanden las variables de entorno en Path.|  
|**LocalResource**|La ruta de acceso relativa a un recurso local que se va a supervisar. Los atributos necesarios son:<br /><br /> - **Name**: el recurso local que contiene el directorio que se va a supervisar<br /><br /> - **relativePath**: la ruta de acceso relativa al nombre que contiene el directorio que se va a supervisar|  



## <a name="etwproviders-element"></a>Elemento EtwProviders  
 *Árbol: Raíz - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - EtwProviders*

 Configura la recopilación de eventos ETW en EventSource o el manifiesto de ETW en función de los proveedores.  

|Elementos secundarios|Descripción|  
|--------------------|-----------------|  
|**EtwEventSourceProviderConfiguration**|Configura la recopilación de eventos generados a partir de la [clase EventSource](http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx). Atributo necesario:<br /><br /> **provider**: el nombre de clase del evento EventSource.<br /><br /> Los atributos opcionales son:<br /><br /> - **scheduledTransferLogLevelFilter**: el nivel de gravedad mínimo para transferir a la cuenta de almacenamiento.<br /><br /> - **scheduledTransferPeriod**: el intervalo existente entre las transferencias programadas en el almacenamiento, redondeado al minuto más cercano. El valor es un [“tipo de datos de duración” XML](http://www.w3schools.com/schema/schema_dtypes_date.asp). |  
|**EtwManifestProviderConfiguration**|Atributo necesario:<br /><br /> **provider**: el GUID del proveedor de eventos<br /><br /> Los atributos opcionales son:<br /><br /> - **scheduledTransferLogLevelFilter**: el nivel de gravedad mínimo para transferir a la cuenta de almacenamiento.<br /><br /> - **scheduledTransferPeriod**: el intervalo existente entre las transferencias programadas en el almacenamiento, redondeado al minuto más cercano. El valor es un [“tipo de datos de duración” XML](http://www.w3schools.com/schema/schema_dtypes_date.asp). |  



## <a name="etweventsourceproviderconfiguration-element"></a>Elemento EtwEventSourceProviderConfiguration  
 *Árbol: Raíz - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - EtwProviders- EtwEventSourceProviderConfiguration*

 Configura la recopilación de eventos generados a partir de la [clase EventSource](http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx).  

|Elementos secundarios|Descripción|  
|--------------------|-----------------|  
|**DefaultEvents**|Atributo opcional:<br/><br/> **eventDestination**: el nombre de la tabla en la que se van a almacenar los eventos|  
|**Evento**|Atributo necesario:<br /><br /> **id**: el identificador del evento.<br /><br /> Atributo opcional:<br /><br /> **eventDestination**: el nombre de la tabla en la que se van a almacenar los eventos|  



## <a name="etwmanifestproviderconfiguration-element"></a>Elemento EtwManifestProviderConfiguration  
 *Árbol: Raíz - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - EtwProviders - EtwManifestProviderConfiguration*

|Elementos secundarios|Descripción|  
|--------------------|-----------------|  
|**DefaultEvents**|Atributo opcional:<br /><br /> **eventDestination**: el nombre de la tabla en la que se van a almacenar los eventos|  
|**Evento**|Atributo necesario:<br /><br /> **id**: el identificador del evento.<br /><br /> Atributo opcional:<br /><br /> **eventDestination**: el nombre de la tabla en la que se van a almacenar los eventos|  



## <a name="metrics-element"></a>Elemento Metrics  
 *Árbol: Raíz - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - Metrics*

 Le permite generar una tabla de contadores de rendimiento optimizada para las consultas rápidas. Cada contador de rendimiento que se define en el elemento **PerformanceCounters** se almacena en la tabla de métricas además de la tabla de contadores de rendimiento.  

 El atributo **resourceId** es necesario.  El identificador de recurso de la máquina virtual o conjunto de escalado de máquinas virtuales en donde se va a implementar Azure Diagnostics. Obtenga el valor de **resourceID** en [Azure Portal](https://portal.azure.com). Seleccione **Examinar** -> **Grupos de recursos** -> **<Nombre\>**. Haga clic en el icono **Propiedades** y copie el valor del campo **ID**.  

|Elementos secundarios|Descripción|  
|--------------------|-----------------|  
|**MetricAggregation**|Atributo necesario:<br /><br /> **scheduledTransferPeriod**: el intervalo existente entre las transferencias programadas en el almacenamiento, redondeado al minuto más cercano. El valor es un [“tipo de datos de duración” XML](http://www.w3schools.com/schema/schema_dtypes_date.asp). |  



## <a name="performancecounters-element"></a>Elemento PerformanceCounters  
 *Árbol: Raíz - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - PerformanceCounters*

 Habilita la recopilación de contadores de rendimiento.  

 Atributo opcional:  

 Atributo **scheduledTransferPeriod** opcional. Consulte la explicación anterior.

|Elemento secundario|Descripción|  
|-------------------|-----------------|  
|**PerformanceCounterConfiguration**|Los atributos siguientes son necesarios:<br /><br /> - **counterSpecifier**: el nombre del contador de rendimiento. Por ejemplo: `\Processor(_Total)\% Processor Time`. Para obtener una lista de contadores de rendimiento en el host, ejecute el comando `typeperf`.<br /><br /> - **sampleRate**: la frecuencia de muestreo del contador.<br /><br /> Atributo opcional:<br /><br /> **unit**: la unidad de medida del contador.|  




## <a name="windowseventlog-element"></a>Elemento WindowsEventLog
 *Árbol: Raíz - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - WindowsEventLog*
 
 Habilita la recopilación de registros de eventos de Windows.  

 Atributo **scheduledTransferPeriod** opcional. Consulte la explicación anterior.  

|Elemento secundario|Descripción|  
|-------------------|-----------------|  
|**DataSource**|Los registros de eventos de Windows que se van a recopilar. Atributo necesario:<br /><br /> **name**: la consulta de XPath que describe los eventos de Windows que se van a recopilar. Por ejemplo:<br /><br /> `Application!*[System[(Level <=3)]], System!*[System[(Level <=3)]], System!*[System[Provider[@Name='Microsoft Antimalware']]], Security!*[System[(Level <= 3)]`<br /><br /> Para recopilar todos los eventos, especifique "*".|  




## <a name="logs-element"></a>Elemento Logs  
 *Árbol: Raíz - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - Logs*

 Se presenta en la versión 1.0 y 1.1. Falta en 1.2. Se ha agregado en 1.3.  

 Define la configuración del búfer para los registros básicos de Azure.  

|Atributo|Tipo|Descripción|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|**unsignedInt**|Opcional. Especifica la cantidad máxima de almacenamiento del sistema de archivos que está disponible para los datos especificados.<br /><br /> El valor predeterminado es 0.|  
|**scheduledTransferLogLevelFilterr**|**cadena**|Opcional. Especifica el nivel de gravedad mínimo para las entradas de registro que se van a transferir. El valor predeterminado es **Undefined**, que transfiere todos los registros. Otros valores posibles (en orden de mayor a menor información) son **Verbose**, **Information**, **Warning**, **Error** y **Critical**.|  
|**scheduledTransferPeriod**|**duration**|Opcional. Especifica el intervalo existente entre las transferencias programadas de datos, redondeado al minuto más cercano.<br /><br /> El valor predeterminado es PT0S.|  
|**sinks**: agregado en 1.5|**cadena**|Opcional. Apunta a una ubicación de receptor para enviar datos de diagnóstico. Por ejemplo, Application Insights.|  

## <a name="dockersources"></a>DockerSources
 *Árbol: Raíz - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - DockerSources*

 Se agregó en la versión 1.9.

|Nombre del elemento|Descripción|  
|------------------|-----------------|  
|**Stats**|Indica al sistema para recopilar estadísticas de los contenedores de Docker.|  

## <a name="sinksconfig-element"></a>Elemento SinksConfig  
 *Árbol: Raíz - DiagnosticsConfiguration - PublicConfig - WadCFG - SinksConfig*

 Una lista de ubicaciones donde enviar datos de diagnóstico y la configuración asociada a estas ubicaciones.  

|Nombre del elemento|Descripción|  
|------------------|-----------------|  
|**Sink**|Consulte la descripción en cualquier parte de esta página.|  

## <a name="sink-element"></a>Elemento Sink
 *Árbol: Raíz - DiagnosticsConfiguration - PublicConfig - WadCFG - SinksConfig - Sink*

 Agregado en la versión 1.5.  

 Define las ubicaciones donde se van a enviar datos de diagnóstico. Por ejemplo, el servicio Application Insights.  

|Atributo|Tipo|Descripción|  
|---------------|----------|-----------------|  
|**name**|string|Cadena que identifica el nombre de receptor.|  

|Elemento|Escriba|Descripción|  
|-------------|----------|-----------------|  
|**Application Insights**|cadena|Se usa solo al enviar datos a Application Insights. Contiene la clave de instrumentación para una cuenta activa de Application Insights a la que tiene acceso.|  
|**Channels**|cadena|Uno para cada filtrado adicional transmitido|  

## <a name="channels-element"></a>Elemento Channels  
 *Árbol: Raíz - DiagnosticsConfiguration - PublicConfig - WadCFG - SinksConfig - Sink - Channels*

 Agregado en la versión 1.5.  

 Define los filtros para los flujos de datos de registro que se pasan a través de un receptor.  

|Elemento|Escriba|Descripción|  
|-------------|----------|-----------------|  
|**Channel**|string|Consulte la descripción en cualquier parte de esta página.|  

## <a name="channel-element"></a>Elemento Channel
 *Árbol: Raíz - DiagnosticsConfiguration - PublicConfig - WadCFG - SinksConfig - Sink - Channels - Channel*

 Agregado en la versión 1.5.  

 Define las ubicaciones donde se van a enviar datos de diagnóstico. Por ejemplo, el servicio Application Insights.  

|Atributos|Escriba|Descripción|  
|----------------|----------|-----------------|  
|**logLevel**|**cadena**|Especifica el nivel de gravedad mínimo para las entradas de registro que se van a transferir. El valor predeterminado es **Undefined**, que transfiere todos los registros. Otros valores posibles (en orden de mayor a menor información) son **Verbose**, **Information**, **Warning**, **Error** y **Critical**.|  
|**name**|**cadena**|Un nombre único del canal al que se hace referencia|  


## <a name="privateconfig-element"></a>Elemento PrivateConfig 
 *Árbol: Raíz - DiagnosticsConfiguration - PrivateConfig*

 Agregado en la versión 1.3.  

 Opcional  

 Almacena los detalles privados de la cuenta de almacenamiento (nombre, clave y punto de conexión). Esta información se envía a la máquina virtual, pero no se puede recuperar de ella.  

|Elementos secundarios|Descripción|  
|--------------------|-----------------|  
|**StorageAccount**|La cuenta de almacenamiento que se va a usar. Los atributos siguientes son necesarios<br /><br /> - **name**: el nombre de la cuenta de almacenamiento.<br /><br /> - **key**: la clave de la cuenta de almacenamiento.<br /><br /> - **endpoint**: el punto de conexión para acceder a la cuenta de almacenamiento. <br /><br /> -**sasToken** (se agregó en la versión 1.8.1): puede especificar un token de SAS en lugar de una clave de cuenta de almacenamiento en la configuración privada. Si se proporciona, se omite la clave de cuenta de almacenamiento. <br />Requisitos del token de SAS: <br />- Solo admite el token de SAS de la cuenta. <br />Se requieren los tipos de servicio - *b* y *t*. <br /> Se requieren los permisos - *a*, *c*, *u* y *w*. <br /> Se requieren los tipos de recursos - *c* y *o*. <br /> - Solo admite únicamente el protocolo HTTPS. <br /> - La hora de inicio y de expiración debe ser válida.|  


## <a name="isenabled-element"></a>Elemento IsEnabled  
 *Árbol: Raíz - DiagnosticsConfiguration - IsEnabled*

 Booleano. Use `true` para habilitar los diagnósticos o `false` para deshabilitarlos.
