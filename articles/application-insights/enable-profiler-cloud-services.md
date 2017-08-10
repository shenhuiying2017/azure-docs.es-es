---
title: "Habilitación de Azure Application Insights Profiler para un recurso de Compute | Microsoft Docs"
description: Aprenda a configurar Profiler
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
ms.author: sewhee
ms.translationtype: HT
ms.sourcegitcommit: 137671152878e6e1ee5ba398dd5267feefc435b7
ms.openlocfilehash: cde0b0bce2e332243e555a72088b8e9eeb680bdb
ms.contentlocale: es-es
ms.lasthandoff: 07/28/2017

---

# <a name="how-to-enable-application-insights-profiler-on-azure-compute-resources"></a>Habilitación de Application Insights Profiler en recursos de Azure Compute

En este tutorial se muestra cómo habilitar Application Insights Profiler en una aplicación de ASP.NET hospedada por recursos de Azure Compute. Los ejemplos incluyen compatibilidad con máquinas virtuales, conjuntos de escalado de máquinas virtuales y Service Fabric. Todos los ejemplos se basan en plantillas que admiten el modelo de implementación de Azure Resource Manager. Para más información sobre el modelo de implementación, consulte [Azure Resource Manager vs. classic deployment: Understand deployment models and the state of your resources](/azure-resource-manager/resource-manager-deployment-model) (Implementación clásica frente a implementación con Azure Resource Manager: los modelos de implementación y el estado de los recursos).

## <a name="overview"></a>Información general

En el diagrama siguiente se ilustra cómo funciona Profiler en recursos de Azure Compute. Se usa una máquina virtual de Azure como ejemplo.

![Información general](./media/enable-profiler-compute/overview.png) Debe instalar el componente agente de Diagnósticos en los recursos de Azure Compute para recopilar información a fin de procesarla y visualizarla en Azure Portal. En el resto del tutorial se proporcionan instrucciones sobre cómo instalar y configurar el agente de Diagnósticos para habilitar Application Insights Profiler.

## <a name="prerequisites-for-the-walkthrough"></a>Requisitos previos del tutorial

* Descargue las plantillas de implementación de Resource Manager que instalan los agentes de Profiler en las máquinas virtuales o los conjuntos de escalado de máquinas virtuales.

    [WindowsVirtualMachine.json](https://github.com/CFreemanwa/samples/blob/master/WindowsVirtualMachine.json) | [WindowsVirtualMachineScaleSet.json](https://github.com/CFreemanwa/samples/blob/master/WindowsVirtualMachineScaleSet.json)
* Una instancia de Application Insights habilitada para la generación de perfiles. Consulte https://docs.microsoft.com/en-us/azure/application-insights/app-insights-profiler#enable-the-profiler para ver cómo hacerlo.
* .NET Framework >= 4.6.1 instalado en el recurso de destino de Azure Compute.

## <a name="create-a-resource-group-in-your-azure-subscription"></a>Creación de un grupo de recursos en la suscripción de Azure
En el ejemplo siguiente se muestra cómo crear un grupo de recursos mediante un script de PowerShell:

```
New-AzureRmResourceGroup -Name "Replace_With_Resource_Group_Name" -Location "Replace_With_Resource_Group_Location"
```

## <a name="create-an-application-insights-resource-in-the-resource-group"></a>Creación de un recurso de Application Insights en el grupo de recursos

![Creación de recursos de Application Insights](./media/enable-profiler-compute/createai.png)

## <a name="apply-application-insights-instrumentation-key-in-the-azure-resource-manager-template"></a>Aplicación de la clave de instrumentación de Application Insights en la plantilla de Azure Resource Manager
Si no ha descargado la plantilla todavía, hágalo desde los lugares siguientes. [WindowsVirtualMachine.json](https://github.com/CFreemanwa/samples/blob/master/WindowsVirtualMachine.json)

![Búsqueda de la clave de AI](./media/enable-profiler-compute/copyaikey.png)

![Sustitución del valor de plantilla](./media/enable-profiler-compute/copyaikeytotemplate.png)

## <a name="create-azure-vm-to-host-the-web-application"></a>Creación de una máquina virtual de Azure para hospedar la aplicación web
* Creación de una cadena segura para guardar la contraseña
```
$password = ConvertTo-SecureString -String "Replace_With_Your_Password" -AsPlainText -Force
```

* Implementación de la plantilla de Azure Resource Manager

Cambie el directorio en la consola de PowerShell a la carpeta que contiene la plantilla de Resource Manager. Para implementar la plantilla, ejecute el siguiente comando:

```
New-AzureRmResourceGroupDeployment -ResourceGroupName "Replace_With_Resource_Group_Name" -TemplateFile .\WindowsVirtualMachine.json -adminUsername "Replace_With_your_user_name" -adminPassword $password -dnsNameForPublicIP "Replace_WIth_your_DNS_Name" -Verbose
```

Una vez que se ejecuta el script correctamente, debe buscar una máquina virtual llamada *MyWindowsVM* en el grupo de recursos.

## <a name="configure-web-deploy-on-the-vm"></a>Configuración de Web Deploy en la máquina virtual
Asegúrese de que **Web Deploy** esté habilitado en la máquina virtual de forma que pueda publicar la aplicación web desde Visual Studio.

Puede instalar Web Deploy en una máquina virtual manualmente mediante WebPI:[Installing and Configuring Web Deploy on IIS 8.0 or Later](https://docs.microsoft.com/en-us/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later) (Instalación y configuración de Web Deploy en IIS 8.0 o superior).

Este es un ejemplo de cómo automatizar la instalación de Web Deploy mediante una plantilla de Azure Resource Manager: [Create, configure, and deploy Web Application to an Azure VM](https://azure.microsoft.com/en-us/resources/templates/201-web-app-vm-dsc/) (Creación, configuración e implementación de una aplicación web en una máquina virtual de Azure).

Si va a implementar una aplicación de ASP.NET MVC, deberá ir al Administrador del servidor, **Agregar roles y características | Servidor web (IIS) | Servidor web | Desarrollo de aplicaciones** y habilitar ASP.NET 4.5 en su servidor.
![Adición de ASP.NET](./media/enable-profiler-compute/addaspnet45.png)

## <a name="install-azure-application-insights-sdk-to-your-project"></a>Instalación del SDK de Azure Application Insights en el proyecto
* Abra la aplicación web ASP.NET en Visual Studio.
* Haga clic con el botón derecho en el proyecto y seleccione **Agregar | Servicios conectados**.
* Elija Application Insights.
* Siga las instrucciones de la página. Seleccione el recurso de Application Insights que creó anteriormente.
* Haga clic en el botón **Registrar**.


## <a name="publish-the-project-to-azure-vm"></a>Publicación del proyecto en una máquina virtual de Azure
Existen varias maneras de publicar una aplicación en una máquina virtual de Azure. Una de ellas es en Visual Studio 2017.
Para finalizar el proceso de publicación, haga clic con el botón derecho en el proyecto y seleccione "Publicar …". Seleccione la máquina virtual de Azure como destino de publicación y siga los pasos.

![Publish-FromVS](./media/enable-profiler-compute/publishtoVM.png)

Ejecute algunas pruebas de carga con la aplicación. Debería poder ver los resultados en la página web del portal de la instancia de Application Insights.


## <a name="enable-the-profiler-in-application-insights"></a>Habilitación de Profiler en Application Insights
Vaya a la hoja Rendimiento de Application Insights. Haga clic en el icono de configuración y habilite la aplicación Profiler.

![Habilitación de Profiler paso 1](./media/enable-profiler-compute/enableprofiler1.png)

![Habilitación de Profiler paso 2](./media/enable-profiler-compute/enableprofiler2.png)

## <a name="add-an-availability-test-to-your-application"></a>Adición de una prueba de disponibilidad a la aplicación
Busque el recurso de Application Insights que configuró anteriormente. Vaya a la hoja Disponibilidad y agregue una prueba de rendimiento que envíe solicitudes web a la dirección URL de la aplicación. De esta forma, puede recopilar algunos datos de ejemplo que se mostrarán en Application Insights Profiler.

![Adición de prueba de rendimiento][./media/enable-profiler-compute/add-test.png]

## <a name="view-your-performance-data"></a>Visualización de los datos de rendimiento

Espere unos 10 o 15 minutos a que Profiler recopile y analice los datos. A continuación, vaya a la hoja Rendimiento del recurso de AI y observe cuál era el rendimiento de la aplicación en estado de carga.

![Visualización del rendimiento][./media/enable-profiler-compute/view-aiperformance.png]

Al hacer clic en el icono, en Ejemplos, se abre la hoja de visualización de seguimientos.

![Visualización de seguimientos][./media/enable-profiler-compute/traceview.png]


## <a name="work-with-an-existing-template"></a>Uso de una plantilla existente

1. Busque la declaración de recursos de Windows Azure Diagnostics (WAD) en la plantilla de implementación.
  * Si aún no tiene una, puede crearla (vea cómo hacerlo en el ejemplo completo).
  * Puede actualizar la plantilla desde el sitio web de recursos de Azure (https://resources.azure.com).
2. Modifique el publicador de "Microsoft.Azure.Diagnostics" a "AIP.Diagnostics.Test".
3. Use typeHandlerVersion como "0.0".
4. Asegúrese de que autoUpgradeMinorVersion está establecido en true.
5. Agregue la nueva instancia de receptor de ApplicationInsightsProfiler del objeto de configuración WadCfg, como se muestra en el ejemplo siguiente.

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
Descargue la plantilla [WindowsVirtualMachineScaleSet.json](https://github.com/CFreemanwa/samples/blob/master/WindowsVirtualMachineScaleSet.json) para ver cómo habilitar Profiler. Aplique los mismos cambios en una plantilla de máquina virtual al recurso de extensión de diagnósticos del conjunto de escalado de máquinas virtuales.
Asegúrese de que cada instancia del conjunto de escalado tenga acceso a Internet, de forma que el agente de generador de perfiles pueda enviar los ejemplos recopilados a Application Insights para que los analice y muestre.

## <a name="enable-the-profiler-on-service-fabric-applications"></a>Habilitación de Profiler en las aplicaciones de Service Fabric
Actualmente, la habilitación de Profiler en las aplicaciones de Service Fabric requiere los pasos siguientes:
1. Aprovisionar el clúster de Service Fabric para que tenga la extensión WAD que instala el agente de Profiler.
2. Instalación del SDK de Application Insights en el proyecto y configuración de la clave de AI
3. Adición de código de la aplicación para instrumentar la telemetría

## <a name="provision-the-service-fabric-cluster-have-the-wad-extension-that-installs-the-profiler-agent"></a>Aprovisionar el clúster de Service Fabric para que tenga la extensión WAD que instala el agente de Profiler.
Un clúster de Service Fabric puede ser seguro o no seguro. Puede establecer un clúster de puerta de enlace como no seguro para que no se necesite un certificado para su acceso. Los clústeres que hospedan lógica y datos empresariales deben ser seguros. Puede habilitar Profiler en clústeres de Service Fabric tanto seguros como no seguros. En este tutorial se usa un clúster no seguro como ejemplo para explicar los cambios que son necesarios para habilitar Profiler. Puede aprovisionar un clúster seguro de la misma manera.

Descargue [ServiceFabricCluster.json](https://github.com/CFreemanwa/samples/blob/master/ServiceFabricCluster.json). Igual que para las máquinas virtuales y los conjuntos de escalado de máquinas virtuales, sustituya la clave de Application Insights por su clave de AI:

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

Implemente la plantilla mediante PowerShell:
```
Login-AzureRmAccount
New-AzureRmResourceGroup -Name [Your_Resource_Group_Name] -Location [Your_Resource_Group_Location] -Verbose -Force
New-AzureRmResourceGroupDeployment -Name [Choose_An_Arbitrary_Name] -ResourceGroupName [Your_Resource_Group_Name] -TemplateFile [Path_To_Your_Template]

```

## <a name="install-application-insights-sdk-in-the-project-and-configure-ai-key"></a>Instalación del SDK de Application Insights en el proyecto y configuración de la clave de AI
Instale el SDK de Application Insights desde el paquete de NuGet. Asegúrese de instalar una versión estable 2.3 o posterior. [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) Consulte [Using Service Fabric with Application Insights](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/blob/dev/appinsights/ApplicationInsights.md) (Uso de Service Fabric con Application Insights) para configurar Application Insights en sus proyectos.

## <a name="add-application-code-to-instrument-telemetry"></a>Adición de código de la aplicación para instrumentar la telemetría
Si quiere instrumentar algún fragmento de código, agregue una instrucción using alrededor. En el ejemplo siguiente, el método RunAsync realiza parte del trabajo y la clase telemetryClient captura la telemetría una vez que se inicia. El evento necesita un nombre único en toda la aplicación.

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

Implemente la aplicación en el clúster de Service Fabric. Espere a que la aplicación se ejecute durante 10 minutos. Para obtener un mejor efecto, puede ejecutar una prueba de carga en la aplicación. Vaya a la hoja de Rendimiento del portal de Application Insights; verá que aparecen ejemplos de seguimiento de generación de perfiles.

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

