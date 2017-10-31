---
title: "Habilitación de Application Insights Profiler para aplicaciones de Azure Compute | Microsoft Docs"
description: Aprenda a configurar Profiler en aplicaciones que se ejecutan en Azure Compute.
services: application-insights
documentationcenter: 
author: ramach
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/16/2017
ms.author: ramach
ms.openlocfilehash: b39b3b234112647ebeb531262d3b3876aee0b63b
ms.sourcegitcommit: a7c01dbb03870adcb04ca34745ef256414dfc0b3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/17/2017
---
# <a name="enable-application-insights-profiler-on-azure-virtual-machines-service-fabric-and-cloud-services"></a>Habilitación de Application Insights Profiler en Azure Virtual Machines, Service Fabric y Cloud Services

En este tutorial se muestra cómo habilitar Azure Application Insights Profiler en una aplicación ASP.NET hospedada por recursos de Azure Compute.  
Los ejemplos incluyen compatibilidad con Azure Virtual Machines, conjuntos de escalado de máquinas virtuales de Azure, Azure Service Fabric y Azure Cloud Services.  
Todos los ejemplos se basan en plantillas que admiten el modelo de implementación de [Azure Resource Manager](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview).  


## <a name="overview"></a>Información general

En el diagrama siguiente se ilustra cómo funciona Profiler en recursos de Azure. Se usa una máquina virtual de Azure como ejemplo.

![Información general](./media/enable-profiler-compute/overview.png)


Para habilitar completamente Profiler, es necesario configurar tres elementos:

1. La hoja del portal de la instancia de Application Insights
2. El código fuente de la aplicación (por ejemplo, una aplicación web ASP.NET)
3. El código fuente de definición de implementación del entorno (por ejemplo, un archivo json de plantilla de implementación de máquina virtual)


## <a name="configure-the-application-insights-instance"></a>Configuración de la instancia de Application Insights

Cree o visite la hoja de Azure Portal de la instancia de Application Insights que quiera usar y anote su clave de instrumentación. La necesitará en otros pasos de configuración:

  ![Ubicación de la clave](./media/enable-profiler-compute/CopyAIKey.png)

Esta instancia debe ser la misma que la que está configurada en su aplicación para enviar datos de telemetría a cada solicitud.
Además, los resultados de Profiler van a estar disponibles en esta instancia.  

Todavía en Azure Portal, siga los pasos que se indican en [Habilitar Profiler](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-profiler#enable-the-profiler) para terminar de configurar la instancia AI de Profiler. Para los fines de este tutorial, no es necesario vincular las aplicaciones web; simplemente, asegúrese de que Profiler esté habilitado en el portal.


## <a name="configure-the-application-source-code"></a>Configuración del código fuente de la aplicación

La aplicación debe estar configurada para enviar datos de telemetría a una instancia de Application Insights en cada operación `Request`.  

1. Agregue el [SDK de Application Insights](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-overview#get-started) al proyecto de aplicación. Asegúrese de que las versiones del paquete NuGet sean las siguientes:  
  - Para aplicaciones ASP.NET: [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) de la versión 2.3.0 o posterior.
  - Para aplicaciones ASP.NET Core: [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/) 2.1.0 o una más reciente.
  - Para otras aplicaciones .NET y .NET Core (por ejemplo, servicio sin estado de Service Fabric, rol de trabajo de servicio en la nube): [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) o [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) 2.3.0 o una más reciente.  

2. Si la aplicación *no* es una aplicación ASP.NET o ASP.NET Core (por ejemplo, roles de trabajo de Cloud Services, API sin estado de Service Fabric), es necesaria la siguiente configuración de instrumentación adicional:  

  2.1. Agregue el código siguiente en un punto anterior de la duración de la aplicación.  

  ```csharp
  using Microsoft.ApplicationInsights.Extensibility;
  ...
  // Replace with your own Application Insights instrumentation key.
  TelemetryConfiguration.Active.InstrumentationKey = "00000000-0000-0000-0000-000000000000";
  ```
  Para más información sobre esta configuración de la clave de instrumentación global, consulte [Using Service Fabric with Application Insights](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/blob/dev/appinsights/ApplicationInsights.md) (Uso de Service Fabric con Application Insights).  

  2.2. Para cualquier parte del código que se vaya a instrumentar, agregue una instrucción `StartOperation<RequestTelemetry>` `using` alrededor, como en el ejemplo siguiente:

  ```csharp
  using Microsoft.ApplicationInsights;
  using Microsoft.ApplicationInsights.DataContracts;
  ...
  var client = new TelemetryClient();
  ...
  using (var operation = client.StartOperation<RequestTelemetry>("Insert_Your_Custom_Event_Unique_Name"))
  {
    // ... Code I want to profile.
  }
  ```

> [!NOTE]  
> No se permite llamar a `StartOperation<RequestTelemetry>` dentro de otro ámbito de `StartOperation<RequestTelemetry>`. En su lugar, puede usar `StartOperation<DependencyTelemetry>` en el ámbito anidado. Ejemplo:  

```csharp
using (var getDetailsOperation = client.StartOperation<RequestTelemetry>("GetProductDetails"))
{
  try
  {
    ProductDetail details = new ProductDetail() { Id = productId };
    getDetailsOperation.Telemetry.Properties["ProductId"] = productId.ToString();

    // By using DependencyTelemetry, 'GetProductPrice' is correctly linked as part of the 'GetProductDetails' request.
    using (var getPriceOperation = client.StartOperation<DependencyTelemetry>("GetProductPrice"))
    {
        double price = await _priceDataBase.GetAsync(productId);
        if (IsTooCheap(price))
        {
            throw new PriceTooLowException(productId);
        }
        details.Price = price;
    }

    // Similarly, note how 'GetProductReviews' doesn't establish another RequestTelemetry.
    using (var getReviewsOperation = client.StartOperation<DependencyTelemetry>("GetProductReviews"))
    {
        details.Reviews = await _reviewDataBase.GetAsync(productId);
    }

    getDetailsOperation.Telemetry.Success = true;
    return details;
  }
  catch(Exception ex)
  {
    getDetailsOperation.Telemetry.Success = false;

    // This exception gets linked to the 'GetProductDetails' request telemetry.
    client.TrackException(ex);
    throw;
  }
}
```


## <a name="configure-the-environment-deployment-definition"></a>Configuración de la definición de implementación del entorno

El entorno donde se ejecutan Profiler y su aplicación puede ser una máquina virtual, un conjunto de escalado de máquinas virtuales, un clúster de Service Fabric o una instancia de Cloud Services.  

### <a name="virtual-machine-virtual-machine-scale-sets-or-service-fabric"></a>Máquina virtual, conjuntos de escalado de máquinas virtuales o Service Fabric

Ejemplos completos:  
  * [Máquina virtual](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)
  * [Conjunto de escalado de máquinas virtuales](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)
  * [Clúster de Service Fabric](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json)

1. Para asegurarse de que se está usando [.NET Framework 4.6.1](https://docs.microsoft.com/en-us/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) o una versión más reciente, es suficiente con confirmar que el sistema operativo implementado es `Windows Server 2012 R2` o más reciente.

2. Busque la extensión [Azure Diagnostics](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/azure-diagnostics) en el archivo de plantilla de implementación, agregue la siguiente sección `SinksConfig` como elemento secundario de `WadCfg` y sustituya el valor de la propiedad `ApplicationInsightsProfiler` por su propia clave de instrumentación de AI:  
```json
"SinksConfig": {
  "Sink": [
    {
      "name": "MyApplicationInsightsProfilerSink",
      "ApplicationInsightsProfiler": "00000000-0000-0000-0000-000000000000"
    }
  ]
}
```

Consulte los ejemplos y [esta guía](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para saber cómo agregar la extensión Diagnostics a la plantilla de implementación.


### <a name="cloud-services"></a>Cloud Services

1. Para asegurarse de que se está usando [.NET Framework 4.6.1](https://docs.microsoft.com/en-us/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) o una versión más reciente, es suficiente con confirmar que los archivos `ServiceConfiguration.*.cscfg` contienen en `osFamily` el valor `"5"` o más reciente.

2. Busque el archivo [Azure Diagnostics](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/azure-diagnostics) `diagnostics.wadcfgx` para su rol de aplicación:  
![Ubicación del archivo de configuración de diagnósticos](./media/enable-profiler-compute/cloudservice-solutionexplorer.png)  
Si no encuentra el archivo, consulte [esta guía](https://docs.microsoft.com/en-us/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#enable-diagnostics-in-cloud-service-projects-before-deploying-them) para saber cómo habilitar la extensión Diagnostics en el proyecto de Azure Cloud Services.

3. Agregue la siguiente sección `SinksConfig` como elemento secundario de `WadCfg`:  
  ```xml
  <WadCfg>
    <DiagnosticMonitorConfiguration>...</DiagnosticMonitorConfiguration>
    <SinksConfig>
      <Sink name="MyApplicationInsightsProfiler">
        <!-- Replace with your own Application Insights instrumentation key. -->
        <ApplicationInsightsProfiler>00000000-0000-0000-0000-000000000000</ApplicationInsightsProfiler>
      </Sink>
    </SinksConfig>
  </WadCfg>
  ```

> [!NOTE]  
> Si el archivo `diagnostics.wadcfgx` también contiene otro receptor de tipo `ApplicationInsights`, estas tres claves de instrumentación deben coincidir:  
>  * La clave de instrumentación usada por la aplicación.  
>  * La clave de instrumentación usada por el receptor `ApplicationInsights`.  
>  * La clave de instrumentación usada por el receptor `ApplicationInsightsProfiler`.  
>
>El valor de la clave de instrumentación real utilizada por el receptor `ApplicationInsights` puede encontrarse en los archivos `ServiceConfiguration.*.cscfg`.  
>Después del lanzamiento de Azure SDK para Visual Studio 15.5, solo las claves de instrumentación que usen la aplicación y el receptor `ApplicationInsightsProfiler` deben coincidir entre sí.


## <a name="environment-deployment-and-runtime-configurations"></a>Configuraciones de tiempo de ejecución y de implementación del entorno

1. Implemente la definición de implementación de entorno modificada.  
Normalmente, para aplicar las modificaciones, se necesita una implementación de plantilla completa o una publicación de Cloud Services mediante cmdlets de PowerShell o Visual Studio.  
El siguiente es un enfoque alternativo para el servicio `Virtual Machines` existente que solo afecta a su extensión Diagnostics:  
```powershell
$ConfigFilePath = [IO.Path]::GetTempFileName()
# After exporting the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
(Get-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
# Set-AzureRmVMDiagnosticsExtension might require the -StorageAccountName argument
# if your original diagnostics configuration had the storageAccountName property in the protectedSettings section
# (which is not downloadable). Make sure to pass the same original value you had in this cmdlet call.
Set-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
```

2. Si la aplicación deseada se ejecuta mediante [IIS](https://www.microsoft.com/web/platform/server.aspx), será necesario habilitar la característica `IIS Http Tracing` de Windows:  
  Establezca el acceso remoto al entorno y, a continuación, use la ventana [Agregar características de Windows]( https://docs.microsoft.com/en-us/iis/configuration/system.webserver/tracing/) o ejecute lo siguiente en PowerShell (como administrador):  
  ```powershell
  Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All
  ```  
  Si establecer el acceso remoto supone un problema, puede usar la [CLI de Azure](https://docs.microsoft.com/en-us/cli/azure/get-started-with-azure-cli) para ejecutar lo siguiente:  
  ```powershell
  az vm run-command invoke -g MyResourceGroupName -n MyVirtualMachineName --command-id RunPowerShellScript --scripts "Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All"
  ```


## <a name="how-to-enable-profiler-on-on-premises-servers"></a>Habilitación de Profiler en servidores locales

También se le conoce como ejecutar Profiler AI en modo independiente (no vinculado a modificaciones de la extensión Diagnostics).  
No está previsto proporcionar compatibilidad con Profiler oficialmente en servidores locales.
Si está interesado en probar este escenario, puede descargar el código de compatibilidad desde [aquí](https://github.com/ramach-msft/AIProfiler-Standalone).  
*No* somos responsables de mantener ese código ni de responder a problemas y solicitudes de características en relación con él.


## <a name="next-steps"></a>Pasos siguientes

- Genere tráfico a la aplicación (por ejemplo, inicie una [prueba de disponibilidad](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-monitor-web-app-availability)) y luego deje pasar de 10 a 15 minutos hasta que comiencen a enviarse seguimientos a la instancia de Application Insights.

- Consulte [Seguimientos de Profiler](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-profiler#enable-the-profiler) en Azure Portal.

- Busque ayuda con la solución de problemas de Profiler en la sección [Solución de problemas](app-insights-profiler.md#troubleshooting) de Profiler.

- Lea más sobre Profiler en [Application Insights Profiler](app-insights-profiler.md).
