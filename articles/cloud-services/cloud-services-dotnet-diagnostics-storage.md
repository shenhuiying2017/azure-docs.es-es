---
title: "Almacenamiento y visualización de los datos de diagnóstico en Azure Storage | Microsoft Docs"
description: "Obtención de datos de diagnóstico de Azure en Almacenamiento de Azure y su visualización"
services: cloud-services
documentationcenter: .net
author: rboucher
manager: jwhit
editor: tysonn
ms.assetid: 18e0780d-43e7-41e4-b8e9-f1fb9a36eb03
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/01/2016
ms.author: robb
ms.openlocfilehash: 374cc179e13c00e439415e3df16e0c6d5ccba5e3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="store-and-view-diagnostic-data-in-azure-storage"></a>Almacenamiento y visualización de los datos de diagnóstico en Almacenamiento de Azure
Los datos de diagnóstico no se almacenan de forma permanente a menos que se transfieran al emulador de almacenamiento de Microsoft Azure o a Almacenamiento de Azure. Una vez que se encuentren almacenados, los datos se pueden ver con una de las diversas herramientas disponibles.

## <a name="specify-a-storage-account"></a>Especificación de una cuenta de almacenamiento
Especifique la cuenta de almacenamiento que desea usar en el archivo ServiceConfiguration.cscfg. La información de cuenta se define como una cadena de conexión en un valor de configuración. En el ejemplo siguiente se muestra la cadena de conexión predeterminada creada para un nuevo proyecto de servicio en la nube en Visual Studio:

```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
    </ConfigurationSettings>
```

Puede cambiar esta cadena de conexión para proporcionar información de cuenta para una cuenta de Almacenamiento de Azure.

Dependiendo del tipo de datos de diagnóstico que se recopila, Diagnósticos de Azure usa el servicio BLOB o el servicio Tabla. La tabla siguiente muestra los orígenes de datos que se conservan y su formato.

| Origen de datos | Formato de almacenamiento |
| --- | --- |
| Registros de Azure |Tabla |
| Registros de IIS 7.0 |Blob |
| Registros de infraestructura de diagnóstico de Azure |Tabla |
| Registros de seguimiento de solicitudes con error |Blob |
| Registros de eventos de Windows |Tabla |
| Contadores de rendimiento |Tabla |
| Volcados de memoria |Blob |
| Registros de errores personalizados |Blob |

## <a name="transfer-diagnostic-data"></a>Transferencia de datos de diagnóstico
Para la versión 2.5 y posteriores de SDK, la solicitud de transferencia de datos de diagnóstico puede producirse a través del archivo de configuración. Puede transferir datos de diagnóstico a intervalos programados como se especifica en la configuración.

Para la versión 2.4 y anteriores de SDK, se puede solicitar la transferencia de los datos de diagnóstico a través del archivo de configuración y mediante programación. El método mediante programación también permite realizar transferencias a petición.

> [!IMPORTANT]
> Cuando se realiza una transferencia de datos de diagnóstico a una cuenta de Almacenamiento de Azure, se incurre en costos por los recursos de almacenamiento que usan los datos de diagnóstico.
> 
> 

## <a name="store-diagnostic-data"></a>Almacenamiento de datos de diagnóstico
Los datos de registro se almacenan en almacenamiento BLOB o Tabla con los nombres siguientes:

**Tablas**

* **WadLogsTable** : registros escritos en código mediante la escucha de seguimiento.
* **WADDiagnosticInfrastructureLogsTable** : monitor de diagnóstico y cambios de configuración.
* **WADDirectoriesTable** : directorios que el monitor de diagnóstico está supervisando.  Esto incluye los registros de IIS, los registros de solicitudes con error de IIS y los directorios personalizados.  La ubicación del archivo de registro de blob se especifica en el campo de contenedor y el nombre del blob está en el campo RelativePath.  El campo AbsolutePath indica la ubicación y el nombre del archivo tal como existía en la máquina virtual de Azure.
* **WADPerformanceCountersTable** : contadores de rendimiento.
* **WADWindowsEventLogsTable** : registros de eventos de Windows.

**Blobs**

* **wad-control-container** : (solo para SDK 2.4 y anteriores) contiene los archivos de configuración XML que controlan el diagnóstico de Azure.
* **wad-iis-failedreqlogfiles** : contiene información de registros de solicitudes con error de IIS.
* **wad-iis-logfiles** : contiene información acerca de los registros de IIS.
* **"custom"** : un contenedor personalizado basado en la configuración de directorios supervisados por el monitor de diagnóstico.  El nombre de este contenedor de blob se especificará en WADDirectoriesTable.

## <a name="tools-to-view-diagnostic-data"></a>Herramientas para ver los datos de diagnóstico
Existen varias herramientas para ver los datos una vez que se transfieren al almacenamiento. Por ejemplo:

* El Explorador de servidores en Visual Studio: si instaló Azure Tools para Microsoft Visual Studio, puede usar el nodo de Almacenamiento de Azure en el Explorador de servidores para ver los datos de tabla y blob de solo lectura de las cuentas de Almacenamiento de Azure. Puede mostrar datos de la cuenta del emulador de almacenamiento local y también desde cuentas de almacenamiento que haya creado para Azure. Para obtener más información, consulte [Exploración y administración de recursos de almacenamiento con el Explorador de servidores](../vs-azure-tools-storage-resources-server-explorer-browse-manage.md).
* [Explorador de almacenamiento de Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) es una aplicación independiente que permite trabajar fácilmente con los datos de Almacenamiento de Azure en Windows, OSX y Linux.
* [Azure Management Studio](http://www.cerebrata.com/products/azure-management-studio/introduction) incluye Azure Diagnostics Manager, que permite ver, descargar y administrar los datos de diagnóstico recopilados por las aplicaciones que se ejecutan en Azure.

## <a name="next-steps"></a>Pasos siguientes
[Seguimiento del flujo en una aplicación de servicios en la nube con Diagnósticos de Azure](cloud-services-dotnet-diagnostics-trace-flow.md)

