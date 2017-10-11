---
title: "Esquema de configuración de Diagnósticos de Azure 1.0 | Microsoft Docs"
description: "SOLO es pertinente si utiliza Azure SDK 2.4 y versiones anteriores con Azure Virtual Machines, conjuntos de escalado de máquinas virtuales, Service Fabric o Cloud Services."
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
ms.openlocfilehash: a8fdfb52d5091d3fc9779657737c7430fcfada51
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="azure-diagnostics-10-configuration-schema"></a>Esquema de configuración de Diagnósticos de Azure 1.0
> [!NOTE]
> Diagnósticos de Azure es el componente que se usa para recopilar contadores de rendimiento y otras estadísticas de Azure Virtual Machines, conjuntos de escalado de máquinas virtuales, Service Fabric y Cloud Services.  Esta página solo es pertinente si está usando uno de estos servicios.
>

Diagnósticos de Azure se usa con otros productos de diagnósticos de Microsoft, como Azure Monitor, Application Insights y Log Analytics.

El archivo de configuración de Diagnósticos de Azure define los valores que se usan para inicializar el monitor de diagnóstico. Este se usa para inicializar la configuración de diagnóstico al iniciar el monitor de diagnóstico.  

 De forma predeterminada, el archivo de esquema de configuración de Diagnósticos de Azure se instala en el directorio `C:\Program Files\Microsoft SDKs\Azure\.NET SDK\<version>\schemas`. Reemplace `<version>` por la versión instalada del [SDK de Azure](http://www.windowsazure.com/develop/downloads/).  

> [!NOTE]
>  El archivo de configuración de diagnóstico se suele utilizar con las tareas de inicio que requieren los datos de diagnóstico que se van a recopilar antes en el proceso de inicio. Para más información sobre Diagnósticos de Azure, consulte [Recopilación de datos de registro mediante Diagnósticos de Azure](assetId:///83a91c23-5ca2-4fc9-8df3-62036c37a3d7).  

## <a name="example-of-the-diagnostics-configuration-file"></a>Ejemplo del archivo de configuración de diagnóstico  
 En el ejemplo siguiente se muestra un archivo de configuración de diagnóstico típico:  

```xml  
<?xml version="1.0" encoding="utf-8"?>
<DiagnosticMonitorConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"  
      configurationChangePollInterval="PT1M"  
      overallQuotaInMB="4096">  
   <DiagnosticInfrastructureLogs bufferQuotaInMB="1024"  
      scheduledTransferLogLevelFilter="Verbose"  
      scheduledTransferPeriod="PT1M" />  
   <Logs bufferQuotaInMB="1024"  
      scheduledTransferLogLevelFilter="Verbose"  
      scheduledTransferPeriod="PT1M" />  

   <Directories bufferQuotaInMB="1024"   
      scheduledTransferPeriod="PT1M">  

      <!-- These three elements specify the special directories   
           that are set up for the log types -->  
      <CrashDumps container="wad-crash-dumps" directoryQuotaInMB="256" />  
      <FailedRequestLogs container="wad-frq" directoryQuotaInMB="256" />  
      <IISLogs container="wad-iis" directoryQuotaInMB="256" />  

      <!-- For regular directories the DataSources element is used -->  
      <DataSources>  
         <DirectoryConfiguration container="wad-panther" directoryQuotaInMB="128">  
            <!-- Absolute specifies an absolute path with optional environment expansion -->  
            <Absolute expandEnvironment="true" path="%SystemRoot%\system32\sysprep\Panther" />  
         </DirectoryConfiguration>  
         <DirectoryConfiguration container="wad-custom" directoryQuotaInMB="128">  
            <!-- LocalResource specifies a path relative to a local   
                 resource defined in the service definition -->  
            <LocalResource name="MyLoggingLocalResource" relativePath="logs" />  
         </DirectoryConfiguration>  
      </DataSources>  
   </Directories>  

   <PerformanceCounters bufferQuotaInMB="512" scheduledTransferPeriod="PT1M">  
      <!-- The counter specifier is in the same format as the imperative   
           diagnostics configuration API -->  
      <PerformanceCounterConfiguration   
         counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT5S" />  
   </PerformanceCounters>  

   <WindowsEventLog bufferQuotaInMB="512"  
      scheduledTransferLogLevelFilter="Verbose"  
      scheduledTransferPeriod="PT1M">  
      <!-- The event log name is in the same format as the imperative   
           diagnostics configuration API -->  
      <DataSource name="System!*" />  
   </WindowsEventLog>  
</DiagnosticMonitorConfiguration>  
```  

## <a name="diagnosticsconfiguration-namespace"></a>Espacio de nombres DiagnosticsConfiguration  
 El espacio de nombres XML del archivo de configuración de diagnóstico es:  

```  
http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration  
```  

## <a name="schema-elements"></a>Elementos de esquema  
 El archivo de configuración de diagnóstico incluye los siguientes elementos.


## <a name="diagnosticmonitorconfiguration-element"></a>Elemento DiagnosticMonitorConfiguration  
Elemento de nivel superior del archivo de configuración de diagnóstico.  

Atributos:

|Atributo  |Escriba   |Obligatorio| Valor predeterminado | Descripción|  
|-----------|-------|--------|---------|------------|  
|**configurationChangePollInterval**|duration|Opcional | PT1M| Especifica el intervalo en el que el monitor de diagnóstico sondea los cambios de configuración de diagnóstico.|  
|**overallQuotaInMB**|unsignedInt|Opcional| 4000 MB. Si proporciona un valor, no debe superar esta cantidad. |La cantidad total de almacenamiento del sistema de archivos asignada para todos los búferes de registro.|  

## <a name="diagnosticinfrastructurelogs-element"></a>Elemento DiagnosticInfrastructureLogs  
Define la configuración de búfer para los registros generados por la infraestructura de diagnóstico subyacente.

Elemento principal: [elemento DiagnosticMonitorConfiguration](#DiagnosticMonitorConfiguration).  

Atributos:

|Atributo|Tipo|Descripción|  
|---------|----|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Opcional. Especifica la cantidad máxima de almacenamiento del sistema de archivos que está disponible para los datos especificados.<br /><br /> El valor predeterminado es 0.|  
|**scheduledTransferLogLevelFilter**|cadena|Opcional. Especifica el nivel de gravedad mínimo para las entradas de registro que se van a transferir. El valor predeterminado es **Undefined**. Otros valores posibles son **Verbose**, **Information**, **Warning**, **Error** y **Critical**.|  
|**scheduledTransferPeriod**|duration|Opcional. Especifica el intervalo existente entre las transferencias programadas de datos, redondeado al minuto más cercano.<br /><br /> El valor predeterminado es PT0S.|  

## <a name="logs-element"></a>Elemento Logs  
 Define la configuración del búfer para los registros básicos de Azure.

 Elemento principal: [elemento DiagnosticMonitorConfiguration](#DiagnosticMonitorConfiguration).  

Atributos:  

|Atributo|Tipo|Descripción|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Opcional. Especifica la cantidad máxima de almacenamiento del sistema de archivos que está disponible para los datos especificados.<br /><br /> El valor predeterminado es 0.|  
|**scheduledTransferLogLevelFilter**|cadena|Opcional. Especifica el nivel de gravedad mínimo para las entradas de registro que se van a transferir. El valor predeterminado es **Undefined**. Otros valores posibles son **Verbose**, **Information**, **Warning**, **Error** y **Critical**.|  
|**scheduledTransferPeriod**|duration|Opcional. Especifica el intervalo existente entre las transferencias programadas de datos, redondeado al minuto más cercano.<br /><br /> El valor predeterminado es PT0S.|  

## <a name="directories-element"></a>Elemento Directories  
Define la configuración de búfer para los registros basados en archivos que se pueden definir.

Elemento principal: [elemento DiagnosticMonitorConfiguration](#DiagnosticMonitorConfiguration).  


Atributos:  

|Atributo|Tipo|Descripción|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Opcional. Especifica la cantidad máxima de almacenamiento del sistema de archivos que está disponible para los datos especificados.<br /><br /> El valor predeterminado es 0.|  
|**scheduledTransferPeriod**|duration|Opcional. Especifica el intervalo existente entre las transferencias programadas de datos, redondeado al minuto más cercano.<br /><br /> El valor predeterminado es PT0S.|  

## <a name="crashdumps-element"></a>Elemento CrashDumps  
 Define el directorio de archivos de volcado de memoria.

 Elemento principal: [elemento Directories](#Directories).  

Atributos:  

|Atributo|Tipo|Descripción|  
|---------------|----------|-----------------|  
|**container**|cadena|El nombre del contenedor donde es se va a transferir el contenido del directorio.|  
|**directoryQuotaInMB**|unsignedInt|Opcional. Especifica el tamaño máximo del directorio en megabytes.<br /><br /> El valor predeterminado es 0.|  

## <a name="failedrequestlogs-element"></a>Elemento FailedRequestLogs  
 Define el directorio de registro de solicitudes con error.

 Elemento principal: [elemento Directories](#Directories).  

Atributos:  

|Atributo|Tipo|Descripción|  
|---------------|----------|-----------------|  
|**container**|cadena|El nombre del contenedor donde es se va a transferir el contenido del directorio.|  
|**directoryQuotaInMB**|unsignedInt|Opcional. Especifica el tamaño máximo del directorio en megabytes.<br /><br /> El valor predeterminado es 0.|  

##  <a name="iislogs-element"></a>Elemento IISLogs  
 Define el directorio de registro de IIS.

 Elemento principal: [elemento Directories](#Directories).  

Atributos:  

|Atributo|Tipo|Descripción|  
|---------------|----------|-----------------|  
|**container**|cadena|El nombre del contenedor donde es se va a transferir el contenido del directorio.|  
|**directoryQuotaInMB**|unsignedInt|Opcional. Especifica el tamaño máximo del directorio en megabytes.<br /><br /> El valor predeterminado es 0.|  

## <a name="datasources-element"></a>Elemento DataSources  
 Define cero o más directorios de registro adicionales.

 Elemento principal: [elemento Directories](#Directories).

## <a name="directoryconfiguration-element"></a>Elemento DirectoryConfiguration  
 Define el directorio de archivos de registro que se va a supervisar.

 Elemento principal: [elemento DataSources](#DataSources).

Atributos:

|Atributo|Tipo|Descripción|  
|---------------|----------|-----------------|  
|**container**|cadena|El nombre del contenedor donde es se va a transferir el contenido del directorio.|  
|**directoryQuotaInMB**|unsignedInt|Opcional. Especifica el tamaño máximo del directorio en megabytes.<br /><br /> El valor predeterminado es 0.|  

## <a name="absolute-element"></a>Elemento Absolute  
 Define una ruta de acceso absoluta del directorio que se va a supervisar con expansión de entorno opcional.

 Elemento principal: [elemento DirectoryConfiguration](#DirectoryConfiguration).  

Atributos:  

|Atributo|Tipo|Descripción|  
|---------------|----------|-----------------|  
|**path**|string|Obligatorio. La ruta de acceso absoluta al directorio que se va a supervisar.|  
|**expandEnvironment**|boolean|Obligatorio. Si establece en **true**, las variables de entorno de la ruta de acceso se expanden.|  

## <a name="localresource-element"></a>Elemento LocalResource  
 Define una ruta de acceso relativa a un recurso local definido en la definición del servicio.

 Elemento principal: [elemento DirectoryConfiguration](#DirectoryConfiguration).  

Atributos:  

|Atributo|Tipo|Descripción|  
|---------------|----------|-----------------|  
|**name**|string|Obligatorio. El nombre del recurso local que contiene el directorio que se va a supervisar.|  
|**relativePath**|string|Obligatorio. La ruta de acceso relativa al recurso local que se va a supervisar.|  

## <a name="performancecounters-element"></a>Elemento PerformanceCounters  
 Define la ruta de acceso al contador de rendimiento que se va a recopilar.

 Elemento principal: [elemento DiagnosticMonitorConfiguration](#DiagnosticMonitorConfiguration).


 Atributos:  

|Atributo|Tipo|Descripción|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Opcional. Especifica la cantidad máxima de almacenamiento del sistema de archivos que está disponible para los datos especificados.<br /><br /> El valor predeterminado es 0.|  
|**scheduledTransferPeriod**|duration|Opcional. Especifica el intervalo existente entre las transferencias programadas de datos, redondeado al minuto más cercano.<br /><br /> El valor predeterminado es PT0S.|  

## <a name="performancecounterconfiguration-element"></a>Elemento PerformanceCounterConfiguration  
 Define el contador de rendimiento que se va a recopilar.

 Elemento principal: [elemento PerformanceCounters](#PerformanceCounters)  

 Atributos:  

|Atributo|Tipo|Descripción|  
|---------------|----------|-----------------|  
|**counterSpecifier**|string|Obligatorio. La ruta de acceso al contador de rendimiento que se va a recopilar.|  
|**sampleRate**|duration|Obligatorio. La velocidad a la que se debe recopilar el contador de rendimiento.|  

## <a name="windowseventlog-element"></a>Elemento WindowsEventLog  
 Define los registros de eventos que se van a supervisar.

 Elemento principal: [elemento DiagnosticMonitorConfiguration](#DiagnosticMonitorConfiguration).

  Atributos:

|Atributo|Tipo|Descripción|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Opcional. Especifica la cantidad máxima de almacenamiento del sistema de archivos que está disponible para los datos especificados.<br /><br /> El valor predeterminado es 0.|  
|**scheduledTransferLogLevelFilter**|cadena|Opcional. Especifica el nivel de gravedad mínimo para las entradas de registro que se van a transferir. El valor predeterminado es **Undefined**. Otros valores posibles son **Verbose**, **Information**, **Warning**, **Error** y **Critical**.|  
|**scheduledTransferPeriod**|duration|Opcional. Especifica el intervalo existente entre las transferencias programadas de datos, redondeado al minuto más cercano.<br /><br /> El valor predeterminado es PT0S.|  

## <a name="datasource-element"></a>Elemento DataSource  
 Define el registro de eventos que se va a supervisar.

 Elemento principal: [elemento WindowsEventLog](#windowsEventLog).  

 Atributos:

|Atributo|Tipo|Descripción|  
|---------------|----------|-----------------|  
|**name**|string|Obligatorio. Expresión XPath que especifica el registro que se va a recopilar.|  
