---
title: "Supervisión de un servicio en la nube de Azure | Microsoft Docs"
description: "Se describe qué conlleva la supervisión de un servicio en la nube de Azure y cuáles son algunas de las opciones."
services: cloud-services
documentationcenter: 
author: thraka
manager: timlt
editor: 
ms.assetid: 
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/22/2017
ms.author: adegeo
ms.openlocfilehash: c63a49c65f2d8261caa534308477888c752a89da
ms.sourcegitcommit: 6fb44d6fbce161b26328f863479ef09c5303090f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/10/2018
---
# <a name="introduction-to-cloud-service-monitoring"></a>Introducción a la supervisión de servicios en la nube

Puede supervisar las métricas de rendimiento claves de cualquier servicio en la nube. Cada rol de servicio en la nube recopila datos mínimos: uso de CPU, uso de red y uso de disco. Si el servicio en la nube tiene la extensión `Microsoft.Azure.Diagnostics` aplicada a un rol, este último puede recopilar puntos de datos adicionales. En este artículo se ofrece una introducción a Azure Diagnostics para Cloud Services.

Con la supervisión básica, los datos del contador de rendimiento de las instancias de rol se muestrean y recopilan en intervalos de tres minutos. Los datos de la supervisión básica no se almacenan en la cuenta de almacenamiento y, por tanto, no tienen ningún costo adicional asociado.

Con la supervisión avanzada, las métricas adicionales se muestrean y recopilan en intervalos de cinco minutos, una hora y doce horas. Los datos agregados se almacenan en una cuenta de almacenamiento, en tablas, y se purgan después de diez días. La cuenta de almacenamiento utilizada se configura en función del rol; puede usar diferentes cuentas de almacenamiento para distintos roles. La configuración se realiza con una cadena de conexión en los archivos [.csdef](cloud-services-model-and-package.md#servicedefinitioncsdef) y [.cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg).


## <a name="basic-monitoring"></a>Supervisión básica

Como se mencionó en la introducción, un servicio en la nube recopila automáticamente los datos de la supervisión básica de la máquina virtual del host. Estos datos incluyen el porcentaje de CPU, la entrada y salida de red y la escritura/lectura de disco. Los datos de supervisión recopilados se muestran automáticamente en las páginas de información general y métricas del servicio en la nube, en Azure Portal. 

La supervisión básica no requiere una cuenta de almacenamiento. 

![Iconos de supervisión básica de un servicio en la nube](media/cloud-services-how-to-monitor/basic-tiles.png)

## <a name="advanced-monitoring"></a>Supervisión avanzada

La supervisión avanzada conlleva el uso de la extensión de Azure Diagnostics y, de forma opcional, del SDK de Application Insights en el rol que se desea supervisar. La extensión de Diagnostics usa un archivo de configuración (por rol) denominado **diagnostics.wadcfgx** para configurar las métricas de diagnóstico supervisadas. Los datos que la extensión de Azure Diagnostics recopila se almacenan en una cuenta de almacenamiento de Azure, que se configura en los archivos **.wadcfgx**, [.csdef](cloud-services-model-and-package.md#servicedefinitioncsdef) y [.cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg). Esto significa que existe un costo adicional asociado con la supervisión avanzada.

A medida que se crea cada rol, Visual Studio le agrega la extensión de Azure Diagnostics. Esta extensión puede recopilar los siguientes tipos de información:

* Contadores de rendimiento personalizados
* Registros de aplicación
* Registros de eventos de Windows
* Origen de eventos de .NET
* Registros IIS
* ETW basado en manifiesto
* Volcados de memoria
* Registros de errores personalizados

Aunque todos estos datos se agregan a la cuenta de almacenamiento, el portal no ofrece una forma nativa de crear gráficos de los datos. Puede usar otro servicio, como Application Insights, para correlacionar y mostrar los datos.

### <a name="use-application-insights"></a>Uso de Application Insights

Si publica el servicio en la nube desde Visual Studio, se le ofrece la opción de enviar datos de diagnóstico a Application Insights. Puede crear el recurso de Application Insights en cualquier momento o enviar los datos a un recursos existente. Application Insights puede supervisar la disponibilidad, el rendimiento, los errores y el uso del servicio en la nube. Se pueden agregar gráficos personalizados a Application Insights, para poder ver los datos que más interesan. Los datos de instancias de rol pueden recopilarse con el SDK de Application Insights en el proyecto del servicio en la nube. Para más información sobre cómo integrar Application Insights, vea [Application Insights para Azure Cloud Services](../application-insights/app-insights-cloudservices.md).

Tenga en cuenta que, aunque puede usar Application Insights para mostrar los contadores de rendimiento y otra configuración especificados mediante la extensión de Microsoft Azure Diagnostics, solo podrá disfrutar de una experiencia mejorada con la integración del SDK de Application Insights en los roles web y de trabajo.


## <a name="add-advanced-monitoring"></a>Adición de la supervisión avanzada

En primer lugar, si aún no tiene una cuenta de almacenamiento **clásica**, [cree una](../storage/common/storage-create-storage-account.md#create-a-storage-account). Asegúrese de que la cuenta de almacenamiento se crea con el **modelo de implementación clásica** especificado.

Después, vaya al recurso **Cuenta de almacenamiento (clásico)**. Seleccione **Configuración** > **Claves de acceso** y copie el valor de **Cadena de conexión principal**. Necesita este valor para el servicio en la nube. 

Debe modificar dos archivos de configuración para habilitar el diagnóstico avanzado, **ServiceDefinition.csdef** y **ServiceConfiguration.cscfg**. Lo más probable es que tenga dos archivos de configuración **.cscfg**, uno denominado **ServiceConfiguration.cloud.cscfg**, para implementar en Azure, y otro llamado **ServiceConfiguration.local.cscfg**, que se usa para las implementaciones de depuración locales. Modifique los dos.

En el archivo **ServiceDefinition.csdef**, agregue una configuración nueva llamada `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` para cada rol que usa el diagnóstico avanzado. Visual Studio agrega este valor al archivo al crear un proyecto. Si no existe, puede agregarlo ahora. 

```xml
<ServiceDefinition name="AnsurCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRoleWithSBQueue1" vmsize="Small">
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
```

De esta forma se define una configuración nueva que debe agregarse a cada archivo **ServiceConfiguration.cscfg**. Abra y modifique cada archivo **.cscfg**. Agregue una configuración llamada `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString`. Establezca el valor en la **Cadena de conexión principal** de la cuenta de almacenamiento clásica o en `UseDevelopmentStorage=true`, si desea usar el almacenamiento local en el equipo de desarrollo.

```xml
<ServiceConfiguration serviceName="AnsurCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2015-04.2.6">
  <Role name="WorkerRoleWithSBQueue1">
    <Instances count="1" />
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=mystorage;AccountKey=KWwkdfmskOIS240jnBOeeXVGHT9QgKS4kIQ3wWVKzOYkfjdsjfkjdsaf+sddfwwfw+sdffsdafda/w==" />

<!-- or use the local development machine for storage
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
-->
```

## <a name="next-steps"></a>pasos siguientes

- [Información sobre Application Insights con Cloud Services](../application-insights/app-insights-cloudservices.md)

