---
title: "Habilitación de Application Insights Profiler para aplicaciones de Azure Compute | Microsoft Docs"
description: Aprenda a configurar Application Insights Profiler en aplicaciones que se ejecutan en Azure Compute.
services: application-insights
documentationcenter: 
author: ramach-msft
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/16/2017
ms.author: ramach
ms.openlocfilehash: 57a4cb560825e0c05ac49df26ac12ee52da52c3c
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
# <a name="enable-application-insights-profiler-for-azure-vms-service-fabric-and-cloud-services"></a>Habilitación de Application Insights Profiler en Azure Virtual Machines, Service Fabric y Cloud Services

En este artículo se muestra cómo habilitar Azure Application Insights Profiler en una aplicación ASP.NET hospedada por recursos de Azure Compute. 

Los ejemplos de este artículo incluyen compatibilidad con Azure Virtual Machines, conjuntos de escalado de máquinas virtuales, Azure Service Fabric y Azure Cloud Services. Todos los ejemplos se basan en plantillas que admiten el modelo de implementación de [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).  


## <a name="overview"></a>Información general

La siguiente imagen muestra cómo funciona Application Insights Profiler con los recursos de Azure. La imagen usa una máquina virtual de Azure como ejemplo.

  ![Información general](./media/enable-profiler-compute/overview.png)

Para habilitar completamente el generador de perfiles, debe cambiar la configuración en tres ubicaciones:

* El panel de la instancia de Application Insights en Azure Portal.
* El código fuente de la aplicación (por ejemplo, una aplicación web ASP.NET).
* El código fuente de la definición de la implementación del entorno (por ejemplo, un archivo .json de la plantilla de implementación de máquina virtual).


## <a name="set-up-the-application-insights-instance"></a>Configuración de la instancia de Application Insights

En Azure Portal, cree la instancia de Application Insights que desea usar, o bien vaya a ella si ya está creada. Anote la clave de instrumentación de la instancia. Use la clave de instrumentación en otros pasos de la configuración.

  ![Ubicación de la clave de instrumentación](./media/enable-profiler-compute/CopyAIKey.png)

Esta instancia debe ser la misma que la aplicación. Está configurada para enviar datos de telemetría en cada solicitud.
Los resultados del generador de perfiles también están disponibles en esta instancia.  

En Azure Portal, complete los pasos que se describen en [Habilitar el generador de perfiles](https://docs.microsoft.com/azure/application-insights/app-insights-profiler#enable-the-profiler) para finalizar la configuración de la instancia de Application Insights para el generador de perfiles. En el ejemplo de este artículo no es preciso vincular las aplicaciones web. Sólo hay que asegurarse de que el generador de perfiles está habilitado en el portal.


## <a name="set-up-the-application-source-code"></a>Configuración del código fuente de la aplicación

Configure la aplicación para que envíe datos de telemetría a una instancia de Application Insights en cada operación `Request`:  

1. Agregue el [SDK de Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview#get-started) al proyecto de aplicación. Asegúrese de que las versiones del paquete NuGet sean las siguientes:  
  - Para aplicaciones ASP.NET: [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) 2.3.0 o cualquier versión posterior.
  - Para aplicaciones ASP.NET Core: [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/) 2.1.0 o cualquier versión posterior.
  - Para otras aplicaciones .NET y .NET Core (por ejemplo, un servicio sin estado de Service Fabric o un rol de trabajo de Cloud Services): [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) o [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) 2.3.0, o cualquier versión posterior.  

2. Si la aplicación *no* es una aplicación ASP.NET o ASP.NET Core (por ejemplo, si es un rol de trabajo de Cloud Services, o API sin estado de Service Fabric), se requiere la siguiente configuración de instrumentación adicional:  

  1. Agregue el código siguiente al principio de la duración de la aplicación:  

    ```csharp
    using Microsoft.ApplicationInsights.Extensibility;
    ...
    // Replace with your own Application Insights instrumentation key.
    TelemetryConfiguration.Active.InstrumentationKey = "00000000-0000-0000-0000-000000000000";
    ```
  Para más información acerca de la configuración de esta clave de instrumentación global, consulte [Using Service Fabric with Application Insights](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/blob/dev/appinsights/ApplicationInsights.md) (Uso de Service Fabric con Application Insights).  

  2. Con respecto a cualquier parte del código que desee instrumentar, agregue una instrucción `StartOperation<RequestTelemetry>` **USING** a su alrededor, como en el ejemplo siguiente:

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

  No se permite llamar a `StartOperation<RequestTelemetry>` dentro de otro ámbito de `StartOperation<RequestTelemetry>`. En su lugar, puede usar `StartOperation<DependencyTelemetry>` en el ámbito anidado. Por ejemplo:  

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


## <a name="set-up-the-environment-deployment-definition"></a>Configuración de la definición de implementación del entorno

El entorno en que se ejecutan el generador de perfiles y su aplicación puede ser una máquina virtual, un conjunto de escalado de máquinas virtuales, un clúster de Service Fabric o una instancia de Cloud Services.  

### <a name="virtual-machines-virtual-machine-scale-sets-or-service-fabric"></a>Máquinas virtuales, conjuntos de escalado de máquinas virtuales o Service Fabric

Ejemplos completos:  
  * [Máquina virtual](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)
  * [Conjunto de escalado de máquinas virtuales](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)
  * [Clúster de Service Fabric](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json)

1. Para asegurarse de que se está usando [.NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) o una versión más reciente, es suficiente con confirmar que el sistema operativo implementado es `Windows Server 2012 R2`, u otro posterior.

2. Busque la extensión [Azure Diagnostics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) en el archivo de plantilla de implementación y agregue la siguiente sección `SinksConfig` como elemento secundario de `WadCfg`. Reemplace el valor de la propiedad `ApplicationInsightsProfiler` por su propia clave de instrumentación de Application Insights:  
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

  Para obtener información acerca de cómo agregar la extensión de Diagnostics a una plantilla de implementación, consulte [Uso de la supervisión y el diagnóstico con una máquina virtual Windows y plantillas de Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


### <a name="cloud-services"></a>Cloud Services

1. Para asegurarse de que [.NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed), o cualquier versión posterior, están en uso, basta con confirmar que los archivos ServiceConfiguration.\*.cscfg tienen un valor de `osFamily` de **"5"**, o posterior.

2. Busque el archivo diagnostics.wadcfgx de [Azure Diagnostics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) para su rol de aplicación:  
  ![Ubicación del archivo de configuración de diagnósticos](./media/enable-profiler-compute/cloudservice-solutionexplorer.png)  
  Si no encuentra el archivo, para aprender a habilitar la extensión de Diagnostics en el proyecto de Cloud Services, consulte [Configuración de diagnósticos para Azure Cloud Services y máquinas virtuales](https://docs.microsoft.com/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#enable-diagnostics-in-cloud-service-projects-before-deploying-them).

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
> Si el archivo `diagnostics.wadcfgx` también contiene otro receptor del tipo `ApplicationInsights`, estas tres claves de instrumentación deben coincidir:  
>  * La clave de instrumentación que usa la aplicación.  
>  * La clave de instrumentación que usa el receptor `ApplicationInsights`.  
>  * La clave de instrumentación que usa el receptor `ApplicationInsightsProfiler`.  
>
> El valor real de la clave de instrumentación real que usa el receptor `ApplicationInsights` se puede encontrar en los archivos ServiceConfiguration.\*.cscfg.  
> Después del lanzamiento de Azure SDK para Visual Studio 15.5, solo es preciso que coincidan las claves de instrumentación que usen la aplicación y el receptor `ApplicationInsightsProfiler`.


## <a name="environment-deployment-and-runtime-configurations"></a>Configuraciones de tiempo de ejecución y de implementación del entorno

1. Implemente la definición de implementación de entorno modificada.  

  Para aplicar las modificaciones, lo habitual es que haya una implementación de plantilla completa o una publicación de Cloud Services mediante cmdlets de PowerShell o Visual Studio implicadas.  

  El siguiente es un enfoque alternativo para las máquinas virtuales existentes que solo afecta a su extensión de Azure Diagnostics:  
  ```powershell
  $ConfigFilePath = [IO.Path]::GetTempFileName()
  # After you export the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
  (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
  # Set-AzureRmVMDiagnosticsExtension might require the -StorageAccountName argument
  # if your original diagnostics configuration had the storageAccountName property in the protectedSettings section
  # (which is not downloadable). Make sure to pass the same original value you had in this cmdlet call.
  Set-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
  ```

2. Si la aplicación deseada se ejecuta mediante [IIS](https://www.microsoft.com/web/platform/server.aspx), habilite la característica `IIS Http Tracing` de Windows:  
  
  1. Establezca un acceso remoto al entorno y, después, use la ventana [Agregar características de Windows]( https://docs.microsoft.com/iis/configuration/system.webserver/tracing/) o ejecute el siguiente comando en PowerShell (como administrador):  
    ```powershell
    Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All
    ```  
  2. Si el establecimiento del acceso remoto supone un problema, puede usar la [CLI de Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) para ejecutar el siguiente comando:  
    ```powershell
    az vm run-command invoke -g MyResourceGroupName -n MyVirtualMachineName --command-id RunPowerShellScript --scripts "Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All"
    ```


## <a name="enable-the-profiler-on-on-premises-servers"></a>Habilitación del generador de perfiles en servidores locales

La habilitación del generador de perfiles en un servidor local también es conocida como Application Insights Profiler en modo independiente (no está vinculada a las modificaciones de la extensión Azure Diagnostics). 

No está previsto proporcionar compatibilidad con el generador de perfiles oficialmente en servidores locales. Si está interesado en probar este escenario, puede [descargar el código de compatibilidad](https://github.com/ramach-msft/AIProfiler-Standalone). *No* somos responsables de mantener ese código ni de responder a problemas y solicitudes de características en relación con él.

## <a name="next-steps"></a>Pasos siguientes

- Genere tráfico para su aplicación (por ejemplo, inicie una [prueba de disponibilidad](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability)). A continuación, espere de 10 a 15 minutos para que se empiecen a enviar seguimientos a la instancia de Application Insights.
- Consulte [Seguimientos de Profiler](https://docs.microsoft.com/azure/application-insights/app-insights-profiler#enable-the-profiler) en Azure Portal.
- Obtenga ayuda para solucionar problemas de Profiler en la sección [Solución de problemas](app-insights-profiler.md#troubleshooting) de Profiler.
- Lea más sobre Profiler en [Application Insights Profiler](app-insights-profiler.md).
