---
title: Habilitar Azure Application Insights Profiler en recursos de Azure Compute | Microsoft Docs
description: "Aprenda a configurar el generador de perfiles en una aplicación ASP.NET hospedada por un recurso de Azure Cloud Services."
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: bwren
ms.openlocfilehash: 65ba755f35df7bd09dd652ac6fccf96a878c6ca9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="enable-application-insights-profiler-on-an-azure-cloud-services-resource"></a>Habilitación de Application Insights Profiler para un recurso de Azure Cloud Services

En este tutorial se muestra cómo habilitar Azure Application Insights Profiler en una aplicación de ASP.NET hospedada por un recurso de Azure Cloud Services. Los ejemplos incluyen compatibilidad con Azure Virtual Machines, conjuntos de escalado de máquinas virtuales y Azure Service Fabric. Todos los ejemplos se basan en plantillas que admiten el modelo de implementación de Azure Resource Manager. Para más información sobre el modelo de implementación, consulte [La implementación de Azure Resource Manager frente a la implementación clásica: los modelos de implementación y el estado de los recursos](/azure-resource-manager/resource-manager-deployment-model).

## <a name="overview"></a>Información general

En el diagrama siguiente se ilustra cómo funciona el generador de perfiles en los recursos de Azure Cloud Services. Se usa una máquina virtual de Azure como ejemplo.

![Información general](./media/enable-profiler-compute/overview.png) Debe instalar el componente agente de Diagnósticos en los recursos de Azure Cloud Services para recopilar información a fin de procesarla y visualizarla en Azure Portal. En el resto del tutorial se proporcionan instrucciones sobre cómo instalar y configurar el agente de Diagnósticos para habilitar Application Insights Profiler.

## <a name="prerequisites-for-the-walkthrough"></a>Requisitos previos del tutorial

* Una plantilla de implementación de Resource Manager que instala los agentes del generador de perfiles en las máquinas virtuales ([WindowsVirtualMachine.json](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)) o en los conjuntos de escalado ([WindowsVirtualMachineScaleSet.json](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)).

* Una instancia de Application Insights habilitada para la generación de perfiles. Para obtener instrucciones, consulte [Habilitación del perfil](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-profiler#enable-the-profiler).

* .NET framework 4.6.1 o posterior instalado en el recurso de Azure Cloud Services de destino.

## <a name="create-a-resource-group-in-your-azure-subscription"></a>Creación de un grupo de recursos en la suscripción de Azure
En el ejemplo siguiente se muestra cómo crear un grupo de recursos mediante un script de PowerShell:

```
New-AzureRmResourceGroup -Name "Replace_With_Resource_Group_Name" -Location "Replace_With_Resource_Group_Location"
```

## <a name="create-an-application-insights-resource-in-the-resource-group"></a>Creación de un recurso de Application Insights en el grupo de recursos
En la hoja **Application Insights**, escriba la información del recurso, como se muestra en este ejemplo: 

![Hoja Application Insights](./media/enable-profiler-compute/createai.png)

## <a name="apply-an-application-insights-instrumentation-key-in-the-azure-resource-manager-template"></a>Aplicación de la clave de instrumentación de Application Insights en la plantilla de Azure Resource Manager

1. Si no ha descargado la plantilla todavía, hágalo desde [GitHub](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json).

2. Busque la clave de Application Insights.
   
   ![Ubicación de la clave](./media/enable-profiler-compute/copyaikey.png)

3. Sustituya el valor de la plantilla.
   
   ![Valor reemplazado en la plantilla](./media/enable-profiler-compute/copyaikeytotemplate.png)

## <a name="create-an-azure-vm-to-host-the-web-application"></a>Creación de una máquina virtual de Azure para hospedar la aplicación web
1. Cree una cadena segura para guardar la contraseña.

   ```
   $password = ConvertTo-SecureString -String "Replace_With_Your_Password" -AsPlainText -Force
   ```

2. Implemente la plantilla de Azure Resource Manager.

   Cambie el directorio de la consola de PowerShell a la carpeta que contiene la plantilla de Resource Manager. Para implementar la plantilla, ejecute el siguiente comando:

   ```
   New-AzureRmResourceGroupDeployment -ResourceGroupName "Replace_With_Resource_Group_Name" -TemplateFile .\WindowsVirtualMachine.json -adminUsername "Replace_With_your_user_name" -adminPassword $password -dnsNameForPublicIP "Replace_WIth_your_DNS_Name" -Verbose
   ```

Una vez que se ejecuta el script correctamente, debe buscar una máquina virtual llamada **MyWindowsVM** en el grupo de recursos.

## <a name="configure-web-deploy-on-the-vm"></a>Configuración de Web Deploy en la máquina virtual
Asegúrese de que Web Deploy esté habilitado en la máquina virtual de forma que pueda publicar la aplicación web desde Visual Studio.

Para instalar Web Deploy en una máquina virtual manualmente mediante WebPI, consulte [Installing and Configuring Web Deploy on IIS 8.0 or Later](https://docs.microsoft.com/en-us/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later) (Instalación y configuración de Web Deploy en IIS 8.0 o superior). Si quiere ver un ejemplo de cómo automatizar la instalación de Web Deploy mediante una plantilla de Azure Resource Manager, consulte [Create, configure, and deploy Web Application to an Azure VM](https://azure.microsoft.com/en-us/resources/templates/201-web-app-vm-dsc/) (Creación, configuración e implementación de una aplicación web en una máquina virtual de Azure).

Si va a implementar una aplicación de ASP.NET MVC, vaya al Administrador del servidor, seleccione **Agregar roles y características** > **Servidor web (IIS)** > **Servidor web** > **Desarrollo de aplicaciones** y habilitar ASP.NET 4.5 en su servidor.

![Adición de ASP.NET](./media/enable-profiler-compute/addaspnet45.png)

## <a name="install-the-azure-application-insights-sdk-for-your-project"></a>Instalación del SDK de Azure Application Insights en el proyecto
1. Abra la aplicación web ASP.NET en Visual Studio.

2. Haga clic con el botón derecho en el proyecto y seleccione **Agregar** > **Servicios conectados**.

3. Seleccione **Application Insights**.

4. Siga las instrucciones de la página. Seleccione el recurso de Application Insights que creó anteriormente.

5. Seleccione el botón **Registrar**.


## <a name="publish-the-project-to-an-azure-vm"></a>Publicación del proyecto en una máquina virtual de Azure
Existen varias maneras de publicar una aplicación en una máquina virtual de Azure. Una forma consiste en usar Visual Studio 2017.

1. Haga clic con el botón derecho en el proyecto y seleccione **Publicar**.

2. Seleccione **Microsoft Azure Virtual Machines** como destino de publicación y siga los pasos.

   ![Publish-FromVS](./media/enable-profiler-compute/publishtoVM.png)

3. Ejecute una prueba de carga con la aplicación. Debería ver los resultados en la página web del portal de la instancia de Application Insights.


## <a name="enable-the-profiler"></a>Habilitar el generador de perfiles

1. Vaya a la hoja **Rendimiento** de Application Insights y haga clic en **Generador de perfiles** en la esquina superior derecha para configurarlo.

   ![Configurar el botón Profiler](./media/enable-profiler-compute/PerformanceTriageViewPofilerButton.png)

2. Seleccione **Habilitar el generador de perfiles**.

   ![Habilitar el botón Profiler](./media/enable-profiler-compute/enableprofiler2.png)


## <a name="add-a-performance-test-to-your-application"></a>Adición de una prueba de rendimiento a la aplicación
Siga estos pasos para que podamos recopilar algunos datos de ejemplo que se mostrarán en Application Insights Profiler:

1. Busque el recurso de Application Insights que creó anteriormente. 

2. Vaya a la hoja **Disponibilidad** y agregue una prueba de rendimiento que envíe solicitudes web a la dirección URL de la aplicación. 

   ![Adición de una prueba de rendimiento](./media/enable-profiler-compute/AvailabilityTest.png)

## <a name="view-your-performance-data"></a>Visualización de los datos de rendimiento

1. Espere unos 10 o 15 minutos a que el generador de perfiles recopile y analice los datos. 

2. Vaya a la hoja **Rendimiento** del recurso de Application Insights y observe cuál era el rendimiento de la aplicación en estado de carga. Para centrarse en el funcionamiento lento de interés con suficientes datos de uso, ordene la cuadrícula operativa por la columna Recuento. Observe qué intervalos de duración tienen los seguimientos de Profiler examinando el canal de Profiler por encima de la distribución de duración. Tenga en cuenta que, cuanto más tiempo supervise la aplicación, más seguimientos recopilará Profiler y, por tanto, se cubrirá una mayor parte de la distribución en los ejemplos de nivel de código enriquecido compatibles con los seguimientos del generador de perfiles. 

   ![Seguimientos de Profiler en la vista de evaluación de prioridades de rendimiento](./media/enable-profiler-compute/PerformanceTriageViewProfilerTraces.png)

    Puede acercarse al intervalo de duración de su interés, como el tercer pico alrededor del percentil 95. Esto restringirá el número de ejemplos y seguimientos de Profiler en los botones para Realizar acción. 

    ![Acercar el intervalo de duración](./media/enable-profiler-compute/DurationRangeZoomedTo95th.png)

    Ahora, haga clic en el botón **Seguimientos de Profiler** para abrir Profiler con el seguimiento adecuado.

3. Seleccione **Ejemplos**, para abrir la hoja de **visualización de seguimientos**.

   ![Abrir la hoja de visualización de seguimientos](./media/enable-profiler-compute/traceview.png)


## <a name="work-with-an-existing-template"></a>Uso de una plantilla existente

1. Busque la declaración de recursos de Azure Diagnostics en la plantilla de implementación.
   
   Si no tiene una declaración, puede crear una que sea similar a la declaración del ejemplo siguiente. Puede actualizar la plantilla desde el [sitio web de Azure Resource Explorer](https://resources.azure.com).

2. Cambie el publicador de `Microsoft.Azure.Diagnostics` a `AIP.Diagnostics.Test`.

3. Para `typeHandlerVersion`, utilice `0.0`.

4. Asegúrese de que `autoUpgradeMinorVersion` está establecido en `true`.

5. Agregue la nueva instancia de receptor de `ApplicationInsightsProfiler` del objeto de configuración `WadCfg`, como se muestra en el ejemplo siguiente:

```
"resources": [
        {
          "type": "extensions",
          "name": "Microsoft.Insights.VMDiagnosticsSettings",
          "apiVersion": "2016-03-30",
          "properties": {
            "publisher": "AIP.Diagnostics.Test",
            "type": "IaaSDiagnostics",
            "typeHandlerVersion": "0.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
              "WadCfg": {
                "SinksConfig": {
                  "Sink": [
                    {
                      "name": "Give a descriptive short name. E.g.: MyApplicationInsightsProfilerSink",
                      "ApplicationInsightsProfiler": "Enter the Application Insights instance instrumentation key guid here"
                    }
                  ]
                },
                "DiagnosticMonitorConfiguration": {
                    ...
                }
                ...
              }
              ...
            }
            ...
          }
          ...
]
```

## <a name="enable-the-profiler-on-virtual-machine-scale-sets"></a>Habilitación de Profiler en los conjuntos de escalado de máquinas virtuales
Descargue la plantilla [WindowsVirtualMachineScaleSet.json](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json) para ver cómo habilitar Profiler. Aplique los mismos cambios de una plantilla de máquina virtual al recurso de extensión de diagnósticos del conjunto de escalado de máquinas virtuales.

Asegúrese de que cada instancia del conjunto de escalado tiene acceso a Internet. El agente de Profiler puede enviar los ejemplos recopilados a Application Insights para su presentación y análisis.

## <a name="enable-the-profiler-on-service-fabric-applications"></a>Habilitación de Profiler en las aplicaciones de Service Fabric
1. Aprovisione el clúster de Service Fabric para que tenga la extensión de Azure Diagnostics que instala el agente de Profiler.

2. Instale el SDK de Application Insights en el proyecto y configure la clave de Application Insights.

3. Agregue código de la aplicación para instrumentar la telemetría.

### <a name="provision-the-service-fabric-cluster-to-have-the-azure-diagnostics-extension-that-installs-the-profiler-agent"></a>Aprovisionamiento del clúster de Service Fabric para que tenga la extensión de Azure Diagnostics que instala el agente de Profiler
Un clúster de Service Fabric puede ser seguro o no seguro. Puede establecer un clúster de puerta de enlace como no seguro para que no se necesite un certificado para su acceso. Los clústeres que hospedan lógica y datos empresariales deben ser seguros. Puede habilitar Profiler en clústeres de Service Fabric tanto seguros como no seguros. En este tutorial se usa un clúster no seguro como ejemplo para explicar los cambios que son necesarios para habilitar Profiler. Puede aprovisionar un clúster seguro de la misma manera.

1. Descargue [ServiceFabricCluster.json](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json). Al igual que hizo con las máquinas virtuales y los conjuntos de escalado de máquinas virtuales, sustituya `Application_Insights_Key` por la clave de Application Insights:

   ```
   "publisher": "AIP.Diagnostics.Test",
                 "settings": {
                   "WadCfg": {
                     "SinksConfig": {
                       "Sink": [
                         {
                           "name": "MyApplicationInsightsProfilerSinkVMSS",
                           "ApplicationInsightsProfiler": "[Application_Insights_Key]"
                         }
                       ]
                     },
   ```

2. Implemente la plantilla mediante un script de PowerShell:

   ```
   Login-AzureRmAccount
   New-AzureRmResourceGroup -Name [Your_Resource_Group_Name] -Location [Your_Resource_Group_Location] -Verbose -Force
   New-AzureRmResourceGroupDeployment -Name [Choose_An_Arbitrary_Name] -ResourceGroupName [Your_Resource_Group_Name] -TemplateFile [Path_To_Your_Template]

   ```

### <a name="install-the-application-insights-sdk-in-the-project-and-configure-the-application-insights-key"></a>Instalación del SDK de Application Insights en el proyecto y configuración de la clave de Application Insights
Instale el SDK de Application Insights desde el [paquete NuGet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/). Asegúrese de instalar una versión estable, la 2.3 o posterior. 

Para más información sobre la configuración de Application Insights en los proyectos, consulte [Using Service Fabric with Application Insights](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/blob/dev/appinsights/ApplicationInsights.md) (Uso de Service Fabric con Application Insights).

### <a name="add-application-code-to-instrument-telemetry"></a>Adición de código de la aplicación para instrumentar la telemetría
1. Si quiere instrumentar algún fragmento de código, agregue una instrucción using alrededor. 

   En el ejemplo siguiente, el método `RunAsync` realiza parte del trabajo y la clase `telemetryClient` captura la telemetría una vez que se inicia. El evento necesita un nombre único en toda la aplicación.

   ```
   protected override async Task RunAsync(CancellationToken cancellationToken)
       {
           // TODO: Replace the following sample code with your own logic
           //       or remove this RunAsync override if it's not needed in your service.

           while (true)
           {
               using( var operation = telemetryClient.StartOperation<RequestTelemetry>("[Insert_Event_Unique_Name]"))
               {
                   cancellationToken.ThrowIfCancellationRequested();

                   ++this.iterations;

                   ServiceEventSource.Current.ServiceMessage(this.Context, "Working-{0}", this.iterations);

                   await Task.Delay(TimeSpan.FromSeconds(1), cancellationToken);
               }

           }
       }
   ```

2. Implemente la aplicación en el clúster de Service Fabric. Espere a que la aplicación se ejecute durante 10 minutos. Para obtener un mejor efecto, puede ejecutar una prueba de carga en la aplicación. Vaya a la hoja de **Rendimiento** del portal de Application Insights; verá que aparecen ejemplos de seguimiento de generación de perfiles.

<!---
Commenting out these sections for now
## Enable the Profiler on Cloud Services applications
[TODO]
## Enable the Profiler on classic Azure Virtual Machines
[TODO]
## Enable the Profiler on on-premise servers
[TODO]
--->

## <a name="next-steps"></a>Pasos siguientes

- Encuentre ayuda con los problemas de Profiler en la sección de [solución de problemas](app-insights-profiler.md#troubleshooting).

- Lea más sobre Profiler en [Application Insights Profiler](app-insights-profiler.md).
